<template>
  <div
    v-if="processing && showProcessing"
    class="flex items-center justify-center text-xl text-primary/60"
  >
    <span>Processing Logo...</span>
  </div>
  <canvas
    ref="liquidLogoRef"
    class="w-[300px] h-[300px]"
    :class="{ hidden: processing }"
  ></canvas>
</template>

<script setup lang="ts">
import { onMounted, ref, watch, onUnmounted } from "vue";
import { liquidFragSource, vertexShaderSource } from "./shader";
import { parseLogoImage } from "./parseLogoImage";

interface Props {
  imageUrl: string;
  patternScale?: number;
  refraction?: number;
  edge?: number;
  patternBlur?: number;
  liquid?: number;
  speed?: number;
  showProcessing?: boolean;
}

const props = withDefaults(defineProps<Props>(), {
  refraction: 0.015,
  edge: 0.4,
  patternScale: 2,
  patternBlur: 0.005,
  liquid: 0.07,
  speed: 0.3,
  showProcessing: true,
});

const imageData = ref<ImageData | null>(null);
const glRef = ref<WebGL2RenderingContext | null>(null);
const uniforms = ref<Record<string, WebGLUniformLocation>>({});
const totalAnimationTime = ref(0);
const lastRenderTime = ref(0);
const liquidLogoRef = ref<HTMLCanvasElement | null>(null);
const processing = ref(false);

let renderId: number;
let cleanUpTexture: (() => void) | undefined;

onMounted(async () => {
  processing.value = true;
  await processImage();
  initShader();
  updateUniforms();
  cleanUpTexture = await cleanTexture();
  processing.value = false;

  window.addEventListener("resize", resizeCanvas);
  resizeCanvas();
  animate();
});

onUnmounted(() => {
  window.removeEventListener("resize", resizeCanvas);
  cancelAnimationFrame(renderId);
  if (cleanUpTexture) cleanUpTexture();
});

function updateUniforms() {
  if (!glRef.value || !uniforms.value) return;
  glRef.value.uniform1f(uniforms.value.u_edge, props.edge);
  glRef.value.uniform1f(uniforms.value.u_patternBlur, props.patternBlur);
  glRef.value.uniform1f(uniforms.value.u_patternScale, props.patternScale);
  glRef.value.uniform1f(uniforms.value.u_refraction, props.refraction);
  glRef.value.uniform1f(uniforms.value.u_liquid, props.liquid);
}

function initShader() {
  const canvas = liquidLogoRef.value;
  const gl = canvas?.getContext("webgl2", { antialias: true, alpha: true });
  if (!canvas || !gl) return;

  gl.enable(gl.BLEND);
  gl.blendFunc(gl.SRC_ALPHA, gl.ONE_MINUS_SRC_ALPHA);

  function createShader(
    gl: WebGL2RenderingContext,
    source: string,
    type: number
  ) {
    const shader = gl.createShader(type);
    if (!shader) return null;
    gl.shaderSource(shader, source);
    gl.compileShader(shader);
    if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
      gl.deleteShader(shader);
      return null;
    }
    return shader;
  }

  const vertexShader = createShader(gl, vertexShaderSource, gl.VERTEX_SHADER);
  const fragmentShader = createShader(gl, liquidFragSource, gl.FRAGMENT_SHADER);
  const program = gl.createProgram();
  if (!program || !vertexShader || !fragmentShader) return;

  gl.attachShader(program, vertexShader);
  gl.attachShader(program, fragmentShader);
  gl.linkProgram(program);

  if (!gl.getProgramParameter(program, gl.LINK_STATUS)) return;

  const uniformLocations: Record<string, WebGLUniformLocation> = {};
  const uniformCount = gl.getProgramParameter(program, gl.ACTIVE_UNIFORMS);
  for (let i = 0; i < uniformCount; i++) {
    const uniform = gl.getActiveUniform(program, i);
    if (uniform) {
      uniformLocations[uniform.name] = gl.getUniformLocation(
        program,
        uniform.name
      )!;
    }
  }
  uniforms.value = uniformLocations;

  const vertices = new Float32Array([-1, -1, 1, -1, -1, 1, 1, 1]);
  const vertexBuffer = gl.createBuffer();
  gl.bindBuffer(gl.ARRAY_BUFFER, vertexBuffer);
  gl.bufferData(gl.ARRAY_BUFFER, vertices, gl.STATIC_DRAW);

  gl.useProgram(program);

  const positionLocation = gl.getAttribLocation(program, "a_position");
  gl.enableVertexAttribArray(positionLocation);
  gl.vertexAttribPointer(positionLocation, 2, gl.FLOAT, false, 0, 0);

  glRef.value = gl;
}

function animate() {
  lastRenderTime.value = performance.now();
  renderId = requestAnimationFrame(render);
}

function render(currentTime: number) {
  const deltaTime = currentTime - lastRenderTime.value;
  lastRenderTime.value = currentTime;
  totalAnimationTime.value += deltaTime * props.speed;

  glRef.value?.uniform1f(uniforms.value.u_time, totalAnimationTime.value);
  glRef.value?.drawArrays(glRef.value.TRIANGLE_STRIP, 0, 4);
  renderId = requestAnimationFrame(render);
}

function resizeCanvas() {
  const canvas = liquidLogoRef.value;
  const gl = glRef.value;
  if (!canvas || !gl || !uniforms.value) return;

  const imgRatio = imageData.value
    ? imageData.value.width / imageData.value.height
    : 1;

  const size = 1000;
  canvas.width = size * devicePixelRatio;
  canvas.height = size * devicePixelRatio;
  gl.viewport(0, 0, canvas.width, canvas.height);
  gl.uniform1f(uniforms.value.u_ratio, 1);
  gl.uniform1f(uniforms.value.u_img_ratio, imgRatio);
}

async function processImage() {
  const { imageData: imgData } = await parseLogoImage(props.imageUrl);
  imageData.value = imgData;
}

async function cleanTexture() {
  const gl = glRef.value;
  if (!gl || !uniforms.value || !imageData.value) return;

  const texture = gl.createTexture();
  gl.activeTexture(gl.TEXTURE0);
  gl.bindTexture(gl.TEXTURE_2D, texture);

  gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MIN_FILTER, gl.LINEAR);
  gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_MAG_FILTER, gl.LINEAR);
  gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_S, gl.CLAMP_TO_EDGE);
  gl.texParameteri(gl.TEXTURE_2D, gl.TEXTURE_WRAP_T, gl.CLAMP_TO_EDGE);
  gl.pixelStorei(gl.UNPACK_ALIGNMENT, 1);

  gl.texImage2D(
    gl.TEXTURE_2D,
    0,
    gl.RGBA,
    imageData.value.width,
    imageData.value.height,
    0,
    gl.RGBA,
    gl.UNSIGNED_BYTE,
    imageData.value.data
  );

  gl.uniform1i(uniforms.value.u_image_texture, 0);

  return () => {
    if (texture) gl.deleteTexture(texture);
  };
}

watch(
  () => [
    props.edge,
    props.patternBlur,
    props.patternScale,
    props.refraction,
    props.liquid,
  ],
  updateUniforms
);
</script>
