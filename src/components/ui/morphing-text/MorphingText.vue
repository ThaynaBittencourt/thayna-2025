<template>
  <div
    :class="
      cn(
        'relative mx-auto h-16 w-full max-w-screen-md text-center font-sans text-[30pt] font-bold leading-none [filter:url(#threshold)_blur(0.6px)] md:h-24 lg:text-[3.0rem]',
        props.class
      )
    "
  >
    <span ref="text1Ref" :class="cn(TEXT_CLASSES)" />
    <span ref="text2Ref" :class="cn(TEXT_CLASSES)" />

    <svg id="filters" class="fixed size-0" preserveAspectRatio="xMidYMid slice">
      <defs>
        <filter id="threshold">
          <feColorMatrix
            in="SourceGraphic"
            type="matrix"
            values="1 0 0 0 0
                  0 1 0 0 0
                  0 0 1 0 0
                  0 0 0 255 -140"
          />
        </filter>
      </defs>
    </svg>
  </div>
</template>

<script setup>
import { cn } from "@/lib/utils";
import { ref, onMounted, onUnmounted } from "vue";

const TEXT_CLASSES = "absolute inset-x-0 top-0 m-auto inline-block w-full";

const props = defineProps({
  class: { type: String, required: false },
  texts: { type: Array, required: true },
  morphTime: { type: Number, required: false, default: 3.5 },
  coolDownTime: { type: Number, required: false, default: 2.5 },
});

const textIndex = ref(0);
const morph = ref(0);
const coolDown = ref(0);
const time = ref(new Date());

const text1Ref = ref();
const text2Ref = ref();

function setStyles(fraction) {
  if (!text1Ref.value || !text2Ref.value) return;

  text2Ref.value.style.filter = `blur(${Math.min(8 / fraction - 8, 100)}px)`;
  text2Ref.value.style.opacity = `${Math.pow(fraction, 0.4) * 100}%`;

  const invertedFraction = 1 - fraction;
  text1Ref.value.style.filter = `blur(${Math.min(
    8 / invertedFraction - 8,
    100
  )}px)`;
  text1Ref.value.style.opacity = `${Math.pow(invertedFraction, 0.4) * 100}%`;

  text1Ref.value.textContent =
    props.texts[textIndex.value % props.texts.length];
  text2Ref.value.textContent =
    props.texts[(textIndex.value + 1) % props.texts.length];
}

function doMorph() {
  morph.value -= coolDown.value;
  coolDown.value = 0;

  let fraction = morph.value / props.morphTime;

  if (fraction > 1) {
    coolDown.value = props.coolDownTime;
    fraction = 1;
  }

  setStyles(fraction);

  if (fraction === 1) {
    textIndex.value++;
  }
}

function doCoolDown() {
  morph.value = 0;

  if (text1Ref.value && text2Ref.value) {
    text2Ref.value.style.filter = "none";
    text2Ref.value.style.opacity = "100%";
    text1Ref.value.style.filter = "none";
    text1Ref.value.style.opacity = "0%";
  }
}

let animationFrameId = 0;
function animate() {
  animationFrameId = requestAnimationFrame(animate);

  const newTime = new Date();
  const dt = (newTime.getTime() - time.value.getTime()) / 1000;
  time.value = newTime;

  coolDown.value -= dt;

  if (coolDown.value <= 0) {
    doMorph();
  } else {
    doCoolDown();
  }
}

onMounted(() => {
  animate();
});

onUnmounted(() => {
  cancelAnimationFrame(animationFrameId);
});
</script>
