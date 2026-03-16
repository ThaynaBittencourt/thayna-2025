<!-- eslint-disable vue/block-lang -->
<script setup>
import { onMounted, onUnmounted, ref } from 'vue';
import * as THREE from 'three';
import { gsap } from 'gsap';

// ── State ──
const canvasRef = ref(null);
const activeSection = ref(null);
const tooltip = ref({ visible: false, text: '', x: 0, y: 0 });
const loadingPct = ref(0);
const isLoading = ref(true);
const quality = ref(true);

// ── Three.js globals ──
let renderer, scene, camera, animFrameId;
let nodes = []; // { mesh, label, section, lines[] }
let gridDots = [];
let clock;
let raycaster, mouse;
let hoveredNode = null;
let cameraTarget = { x: 0, y: 0 };

const SECTIONS = [
  { id: 'works',   label: 'WORKS',    pos: [0, 0, -3],    color: 0xd97706 },
  { id: 'about',   label: 'ABOUT ME', pos: [-4, 0, 1],    color: 0x4ade80 },
  { id: 'skills',  label: 'SKILLS',   pos: [4, 0, 1],     color: 0x60a5fa },
  { id: 'contact', label: 'CONTACT',  pos: [0, 0, 4],     color: 0xf472b6 },
];

// ── Build Scene ──
function buildScene() {
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0x080a0c);
  scene.fog = new THREE.FogExp2(0x080a0c, 0.035);

  clock = new THREE.Clock();
  raycaster = new THREE.Raycaster();
  mouse = new THREE.Vector2(-10, -10);

  // CAMERA — isometric-ish perspective
  camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 200);
  camera.position.set(0, 14, 16);
  camera.lookAt(0, 0, 0);

  // RENDERER
  renderer = new THREE.WebGLRenderer({
    canvas: canvasRef.value,
    antialias: quality.value,
    alpha: false,
  });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, quality.value ? 2 : 1));
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  renderer.toneMapping = THREE.ACESFilmicToneMapping;
  renderer.toneMappingExposure = 1.2;

  buildLights();
  buildGrid();
  buildCenterPlatform();
  buildNodes();
  buildConnections();
  buildParticles();
}

function buildLights() {
  const ambient = new THREE.AmbientLight(0x1a1a2e, 3);
  scene.add(ambient);

  const dirLight = new THREE.DirectionalLight(0xffffff, 2);
  dirLight.position.set(10, 20, 10);
  dirLight.castShadow = true;
  dirLight.shadow.mapSize.set(2048, 2048);
  dirLight.shadow.camera.near = 0.1;
  dirLight.shadow.camera.far = 80;
  dirLight.shadow.camera.left = -20;
  dirLight.shadow.camera.right = 20;
  dirLight.shadow.camera.top = 20;
  dirLight.shadow.camera.bottom = -20;
  scene.add(dirLight);

  // Accent point lights per section
  SECTIONS.forEach(s => {
    const pl = new THREE.PointLight(s.color, 1.5, 12);
    pl.position.set(s.pos[0], 3, s.pos[2]);
    scene.add(pl);
  });

  // Center glow
  const centerLight = new THREE.PointLight(0xd97706, 2, 8);
  centerLight.position.set(0, 2, 0);
  scene.add(centerLight);
}

function buildGrid() {
  // Dot grid
  const dotGeo = new THREE.SphereGeometry(0.04, 4, 4);
  const dotMat = new THREE.MeshBasicMaterial({ color: 0x1a2a1a });
  const spacing = 1.5;
  const count = 18;

  for (let x = -count; x <= count; x++) {
    for (let z = -count; z <= count; z++) {
      const dot = new THREE.Mesh(dotGeo, dotMat.clone());
      dot.position.set(x * spacing, 0, z * spacing);
      scene.add(dot);
      gridDots.push(dot);
    }
  }

  // Ground plane (invisible, for shadows)
  const groundGeo = new THREE.PlaneGeometry(80, 80);
  const groundMat = new THREE.ShadowMaterial({ opacity: 0.3 });
  const ground = new THREE.Mesh(groundGeo, groundMat);
  ground.rotation.x = -Math.PI / 2;
  ground.receiveShadow = true;
  scene.add(ground);
}

function buildCenterPlatform() {
  // Main platform
  const geo = new THREE.BoxGeometry(2.2, 0.15, 2.2);
  const mat = new THREE.MeshStandardMaterial({
    color: 0x1a1a1a,
    metalness: 0.8,
    roughness: 0.2,
    emissive: 0xd97706,
    emissiveIntensity: 0.08,
  });
  const platform = new THREE.Mesh(geo, mat);
  platform.position.y = 0.075;
  platform.castShadow = true;
  platform.receiveShadow = true;
  scene.add(platform);

  // Glowing border ring
  const ringGeo = new THREE.TorusGeometry(1.4, 0.03, 8, 60);
  const ringMat = new THREE.MeshBasicMaterial({ color: 0xd97706 });
  const ring = new THREE.Mesh(ringGeo, ringMat);
  ring.rotation.x = Math.PI / 2;
  ring.position.y = 0.16;
  scene.add(ring);

  // Center cube (hero element)
  const cubeGeo = new THREE.BoxGeometry(0.6, 0.6, 0.6);
  const cubeMat = new THREE.MeshStandardMaterial({
    color: 0xd97706,
    metalness: 0.9,
    roughness: 0.1,
    emissive: 0xd97706,
    emissiveIntensity: 0.4,
  });
  const cube = new THREE.Mesh(cubeGeo, cubeMat);
  cube.position.set(0, 0.75, 0);
  cube.castShadow = true;
  scene.add(cube);

  // Animate center cube
  gsap.to(cube.rotation, { y: Math.PI * 2, duration: 8, repeat: -1, ease: 'none' });
  gsap.to(cube.position, { y: 1.1, duration: 2, yoyo: true, repeat: -1, ease: 'power1.inOut' });
}

function buildNodeMesh(section) {
  const group = new THREE.Group();
  group.position.set(...section.pos);

  // Platform base
  const baseGeo = new THREE.BoxGeometry(2.8, 0.12, 2.8);
  const baseMat = new THREE.MeshStandardMaterial({
    color: 0x111111,
    metalness: 0.6,
    roughness: 0.4,
    emissive: section.color,
    emissiveIntensity: 0.05,
  });
  const base = new THREE.Mesh(baseGeo, baseMat);
  base.position.y = 0.06;
  base.castShadow = true;
  base.receiveShadow = true;
  group.add(base);

  // Corner brackets
  const bracketPositions = [[-1.2, 0.15, -1.2], [1.2, 0.15, -1.2], [-1.2, 0.15, 1.2], [1.2, 0.15, 1.2]];
  bracketPositions.forEach(([bx, by, bz]) => {
    const bGeo = new THREE.BoxGeometry(0.06, 0.25, 0.06);
    const bMat = new THREE.MeshBasicMaterial({ color: section.color });
    const b = new THREE.Mesh(bGeo, bMat);
    b.position.set(bx, by, bz);
    group.add(b);
  });

  // Main object per section
  const obj = buildSectionObject(section);
  obj.position.y = 0.15;
  group.add(obj);

  // Hover glow ring
  const glowGeo = new THREE.TorusGeometry(1.6, 0.025, 6, 48);
  const glowMat = new THREE.MeshBasicMaterial({ color: section.color, transparent: true, opacity: 0 });
  const glow = new THREE.Mesh(glowGeo, glowMat);
  glow.rotation.x = Math.PI / 2;
  glow.position.y = 0.13;
  group.add(glow);
  group.userData.glowMesh = glow;

  scene.add(group);
  return group;
}

function buildSectionObject(section) {
  const group = new THREE.Group();

  if (section.id === 'works') {
    // Server rack towers
    const positions = [[-0.5, 0, 0], [0, 0, 0], [0.5, 0, 0]];
    positions.forEach(([px, py, pz], i) => {
      const h = 0.8 + i * 0.2;
      const geo = new THREE.BoxGeometry(0.25, h, 0.35);
      const mat = new THREE.MeshStandardMaterial({ color: 0x1a1a1a, metalness: 0.9, roughness: 0.1, emissive: section.color, emissiveIntensity: 0.15 });
      const mesh = new THREE.Mesh(geo, mat);
      mesh.position.set(px, h / 2, pz);
      mesh.castShadow = true;
      group.add(mesh);
      // LED strips
      for (let j = 0; j < 4; j++) {
        const ledGeo = new THREE.BoxGeometry(0.22, 0.03, 0.02);
        const ledMat = new THREE.MeshBasicMaterial({ color: section.color });
        const led = new THREE.Mesh(ledGeo, ledMat);
        led.position.set(px, j * 0.2 + 0.1, pz + 0.18);
        group.add(led);
      }
    });
    // Base plate
    const plateGeo = new THREE.BoxGeometry(1.8, 0.05, 1.2);
    const plateMat = new THREE.MeshStandardMaterial({ color: 0xeeeeee, roughness: 0.8 });
    const plate = new THREE.Mesh(plateGeo, plateMat);
    plate.position.y = 0;
    group.add(plate);

  } else if (section.id === 'about') {
    // Human figure (simplified)
    const bodyGeo = new THREE.CylinderGeometry(0.12, 0.15, 0.6, 8);
    const bodyMat = new THREE.MeshStandardMaterial({ color: 0xcccccc, metalness: 0.3 });
    const body = new THREE.Mesh(bodyGeo, bodyMat);
    body.position.y = 0.55;
    group.add(body);
    // Head
    const headGeo = new THREE.SphereGeometry(0.15, 10, 10);
    const head = new THREE.Mesh(headGeo, bodyMat);
    head.position.y = 1.05;
    group.add(head);
    // Platform disc
    const discGeo = new THREE.CylinderGeometry(0.6, 0.65, 0.08, 32);
    const discMat = new THREE.MeshStandardMaterial({ color: 0x111111, metalness: 0.9, emissive: section.color, emissiveIntensity: 0.2 });
    const disc = new THREE.Mesh(discGeo, discMat);
    disc.position.y = 0.04;
    group.add(disc);
    // Orbit ring
    const orbitGeo = new THREE.TorusGeometry(0.5, 0.02, 6, 40);
    const orbitMat = new THREE.MeshBasicMaterial({ color: section.color });
    const orbit = new THREE.Mesh(orbitGeo, orbitMat);
    orbit.position.y = 0.6;
    group.add(orbit);
    gsap.to(orbit.rotation, { y: Math.PI * 2, duration: 4, repeat: -1, ease: 'none' });
    gsap.to(body.position, { y: 0.7, duration: 1.8, yoyo: true, repeat: -1, ease: 'power1.inOut' });
    gsap.to(head.position, { y: 1.2, duration: 1.8, yoyo: true, repeat: -1, ease: 'power1.inOut' });

  } else if (section.id === 'skills') {
    // Satellite dish
    // Arm
    const armGeo = new THREE.CylinderGeometry(0.04, 0.04, 0.8, 8);
    const armMat = new THREE.MeshStandardMaterial({ color: 0x888888, metalness: 0.8 });
    const arm = new THREE.Mesh(armGeo, armMat);
    arm.position.y = 0.4;
    group.add(arm);
    // Dish
    const dishGeo = new THREE.SphereGeometry(0.5, 12, 12, 0, Math.PI * 2, 0, Math.PI / 2);
    const dishMat = new THREE.MeshStandardMaterial({ color: 0xaaaaaa, metalness: 0.7, roughness: 0.3, side: THREE.DoubleSide });
    const dish = new THREE.Mesh(dishGeo, dishMat);
    dish.position.y = 0.85;
    dish.rotation.x = Math.PI / 3;
    group.add(dish);
    // Signal rings
    for (let i = 1; i <= 3; i++) {
      const sGeo = new THREE.TorusGeometry(i * 0.25, 0.015, 6, 32);
      const sMat = new THREE.MeshBasicMaterial({ color: section.color, transparent: true, opacity: 0.6 / i });
      const s = new THREE.Mesh(sGeo, sMat);
      s.position.set(0.4, 0.85 + i * 0.1, 0);
      s.rotation.y = Math.PI / 4;
      group.add(s);
    }
    gsap.to(dish.rotation, { z: 0.3, duration: 3, yoyo: true, repeat: -1, ease: 'power1.inOut' });

  } else if (section.id === 'contact') {
    // Robotic arm
    const seg1Geo = new THREE.BoxGeometry(0.1, 0.5, 0.1);
    const armMat = new THREE.MeshStandardMaterial({ color: 0x333333, metalness: 0.9 });
    const seg1 = new THREE.Mesh(seg1Geo, armMat);
    seg1.position.y = 0.35;
    group.add(seg1);
    const joint1Geo = new THREE.SphereGeometry(0.1, 8, 8);
    const joint1 = new THREE.Mesh(joint1Geo, armMat);
    joint1.position.y = 0.65;
    group.add(joint1);
    const seg2Geo = new THREE.BoxGeometry(0.08, 0.45, 0.08);
    const seg2 = new THREE.Mesh(seg2Geo, armMat);
    seg2.position.set(0.2, 0.9, 0);
    seg2.rotation.z = -0.5;
    group.add(seg2);
    // Envelope
    const envGeo = new THREE.BoxGeometry(0.3, 0.2, 0.02);
    const envMat = new THREE.MeshStandardMaterial({ color: 0xffffff, emissive: section.color, emissiveIntensity: 0.4 });
    const env = new THREE.Mesh(envGeo, envMat);
    env.position.set(0.42, 1.1, 0);
    group.add(env);
    // Base
    const baseGeo2 = new THREE.CylinderGeometry(0.3, 0.35, 0.12, 12);
    const baseMat2 = new THREE.MeshStandardMaterial({ color: 0x1a1a1a, metalness: 0.9, emissive: section.color, emissiveIntensity: 0.15 });
    const base2 = new THREE.Mesh(baseGeo2, baseMat2);
    base2.position.y = 0.06;
    group.add(base2);
    gsap.to(seg2.rotation, { z: 0.3, duration: 2, yoyo: true, repeat: -1, ease: 'power1.inOut' });
    gsap.to(env.position, { y: 1.3, duration: 2, yoyo: true, repeat: -1, ease: 'power1.inOut' });
  }

  return group;
}

function buildConnections() {
  // Lines from center to each node
  SECTIONS.forEach(section => {
    const points = [
      new THREE.Vector3(0, 0.15, 0),
      new THREE.Vector3(section.pos[0], 0.15, section.pos[2]),
    ];
    const geo = new THREE.BufferGeometry().setFromPoints(points);
    const mat = new THREE.LineBasicMaterial({
      color: section.color,
      transparent: true,
      opacity: 0.25,
    });
    const line = new THREE.Line(geo, mat);
    scene.add(line);
  });
}

function buildNodes() {
  SECTIONS.forEach(section => {
    const group = buildNodeMesh(section);
    nodes.push({ group, section });
  });
}

function buildParticles() {
  const count = 300;
  const geo = new THREE.BufferGeometry();
  const positions = new Float32Array(count * 3);
  for (let i = 0; i < count; i++) {
    positions[i * 3] = (Math.random() - 0.5) * 60;
    positions[i * 3 + 1] = Math.random() * 20 + 1;
    positions[i * 3 + 2] = (Math.random() - 0.5) * 60;
  }
  geo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
  const mat = new THREE.PointsMaterial({
    color: 0xd97706,
    size: 0.06,
    transparent: true,
    opacity: 0.4,
  });
  const particles = new THREE.Points(geo, mat);
  scene.add(particles);
  gsap.to(particles.rotation, { y: Math.PI * 2, duration: 80, repeat: -1, ease: 'none' });
}

// ── Animate ──
function animate() {
  animFrameId = requestAnimationFrame(animate);
  const t = clock.getElapsedTime();

  // Subtle camera drift
  camera.position.x += (cameraTarget.x * 0.5 - camera.position.x) * 0.02;

  // Grid dot breathing
  if (quality.value) {
    gridDots.forEach((dot, i) => {
      const wave = Math.sin(t * 0.8 + i * 0.05) * 0.5 + 0.5;
      dot.material.opacity = 0.1 + wave * 0.15;
      dot.material.transparent = true;
    });
  }

  // Node hover glow pulse
  nodes.forEach(({ group, section }) => {
    const glow = group.userData.glowMesh;
    if (glow) {
      glow.material.opacity = hoveredNode === group ? 0.5 + Math.sin(t * 4) * 0.2 : 0;
    }
    // Subtle float
    group.position.y = Math.sin(t * 0.6 + SECTIONS.indexOf(section) * 1.5) * 0.04;
  });

  // Raycaster hover detection
  raycaster.setFromCamera(mouse, camera);
  const meshes = nodes.map(n => n.group.children[0]); // base mesh
  const intersects = raycaster.intersectObjects(meshes);
  if (intersects.length > 0) {
    const hit = intersects[0].object.parent;
    if (hoveredNode !== hit) {
      hoveredNode = hit;
      gsap.to(hit.scale, { x: 1.05, y: 1.05, z: 1.05, duration: 0.3, ease: 'power2.out' });
      document.body.style.cursor = 'pointer';
    }
  } else {
    if (hoveredNode) {
      gsap.to(hoveredNode.scale, { x: 1, y: 1, z: 1, duration: 0.3, ease: 'power2.out' });
      hoveredNode = null;
      document.body.style.cursor = 'none';
    }
  }

  renderer.render(scene, camera);
}

// ── Events ──
function onMouseMove(e) {
  mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
  mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;
  cameraTarget.x = (e.clientX / window.innerWidth - 0.5) * 2;

  // Tooltip follow
  if (hoveredNode) {
    const node = nodes.find(n => n.group === hoveredNode);
    if (node) {
      tooltip.value = { visible: true, text: node.section.label, x: e.clientX, y: e.clientY - 40 };
    }
  } else {
    tooltip.value.visible = false;
  }
}

function getAllNodeMeshes() {
  const meshes = [];
  nodes.forEach(({ group }) => {
    group.traverse((child) => {
      if (child.isMesh) {
        child.userData.parentGroup = group;
        meshes.push(child);
      }
    });
  });
  return meshes;
}

function onClick() {
  raycaster.setFromCamera(mouse, camera);
  const meshes = getAllNodeMeshes();
  const intersects = raycaster.intersectObjects(meshes, false);
  
  if (intersects.length > 0) {
    const hit = intersects[0].object.userData.parentGroup;
    if (hit) {
      const node = nodes.find(n => n.group === hit);
      if (node) openSection(node.section);
    }
  }
}

function openSection(section) {
  activeSection.value = section.id;
  // Camera fly-in to section
  gsap.to(camera.position, {
    x: section.pos[0] * 0.4,
    y: 10,
    z: section.pos[2] * 0.4 + 10,
    duration: 1.2,
    ease: 'power3.inOut',
  });
}

function closeSection() {
  activeSection.value = null;
  gsap.to(camera.position, {
    x: 0, y: 14, z: 16,
    duration: 1.2, ease: 'power3.inOut',
  });
}

function onResize() {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
}

// ── Lifecycle ──
onMounted(() => {
  // Fake loading progress
  const interval = setInterval(() => {
    loadingPct.value += Math.random() * 15;
    if (loadingPct.value >= 100) {
      loadingPct.value = 100;
      clearInterval(interval);
      setTimeout(() => {
        isLoading.value = false;
        buildScene();
        animate();
      }, 400);
    }
  }, 120);

  window.addEventListener('mousemove', onMouseMove);
  window.addEventListener('click', onClick);
  window.addEventListener('resize', onResize);
});

onUnmounted(() => {
  cancelAnimationFrame(animFrameId);
  renderer?.dispose();
  window.removeEventListener('mousemove', onMouseMove);
  window.removeEventListener('click', onClick);
  window.removeEventListener('resize', onResize);
});

const sectionContent = {
  works: {
    title: 'Works',
    items: [
      { title: 'INTEGRAÇÃO COM IA', cat: 'Inteligência Artificial', year: '2026' },
      { title: 'AUTOMAÇÃO n8n', cat: 'Workflow', year: '2026' },
      { title: 'PROJETO LARAVEL', cat: 'Full Stack', year: '2025' },
    ],
  },
  about: {
    title: 'About Me',
    bio: 'Desenvolvedor Full Stack baseado em Natal, RN. Especializado em Laravel, Vue.js e automações com n8n. Apaixonado por criar experiências digitais modernas e funcionais.',
    stats: [{ label: 'Anos exp.', val: '3+' }, { label: 'Projetos', val: '20+' }, { label: 'Tecnologias', val: '15+' }],
  },
  skills: {
    title: 'Skills',
    items: [
      { name: 'Laravel', level: 90 },
      { name: 'Vue.js', level: 85 },
      { name: 'n8n / Automações', level: 80 },
      { name: 'MySQL', level: 80 },
      { name: 'Docker', level: 65 },
      { name: 'Three.js', level: 55 },
    ],
  },
  contact: {
    title: 'Contact',
    links: [
      { label: 'Email', val: 'professornegreco@gmail.com', href: 'mailto:professornegreco@gmail.com' },
      { label: 'GitHub', val: 'ThaynaBittencourt', href: 'https://github.com/ThaynaBittencourt/' },
      { label: 'LinkedIn', val: 'thaynã-bittencourt', href: 'https://www.linkedin.com/in/thayn%C3%A3-bittencourt-470571274/' },
    ],
  },
};
</script>

<template>
  <div class="world-root">

    <!-- LOADER -->
    <Transition name="loader-fade">
      <div v-if="isLoading" class="loader">
        <div class="loader-inner">
          <div class="loader-logo">TB<span class="acc">.</span></div>
          <div class="loader-pct">{{ Math.floor(loadingPct) }}<sup>%</sup></div>
          <div class="loader-bar-track">
            <div class="loader-bar" :style="{ width: loadingPct + '%' }"></div>
          </div>
          <p class="loader-label">Inicializando mundo 3D</p>
        </div>
      </div>
    </Transition>

    <!-- THREE.JS CANVAS -->
    <canvas ref="canvasRef" class="world-canvas" />

    <!-- HUD — top left -->
    <div class="hud-tl" v-show="!isLoading">
      <div class="hud-name">THAYNÃ BITTENCOURT</div>
      <div class="hud-role">FULL STACK WEB DEVELOPER</div>
    </div>

    <!-- HUD — top right (FPS-style badge) -->
    <div class="hud-tr" v-show="!isLoading">
      <div class="hud-badge">
        <span class="badge-label">PORTFOLIO</span>
        <span class="badge-year">2026</span>
      </div>
    </div>

    <!-- HUD — bottom left -->
    <div class="hud-bl" v-show="!isLoading">
      <label class="hud-check">
        <input type="checkbox" v-model="quality" @change="() => { renderer?.setPixelRatio(Math.min(window.devicePixelRatio, quality ? 2 : 1)) }" />
        <span>HIGH QUALITY</span>
      </label>
      <div class="hud-hint">[ CLIQUE NOS OBJETOS PARA EXPLORAR ]</div>
    </div>

    <!-- HUD — bottom right -->
    <div class="hud-br" v-show="!isLoading">
      <a href="https://www.linkedin.com/in/thayn%C3%A3-bittencourt-470571274/" target="_blank" class="hud-link">LINKEDIN</a>
      <a href="https://github.com/ThaynaBittencourt/" target="_blank" class="hud-link">GITHUB</a>
      <a href="mailto:professornegreco@gmail.com" class="hud-link hud-cta">OPEN TO WORK ↗</a>
    </div>

    <!-- CURSOR DOT -->
    <div class="cursor-dot" :style="{ left: tooltip.x + 'px', top: tooltip.y + 60 + 'px' }" />

    <!-- TOOLTIP -->
    <Transition name="tt">
      <div
        v-if="tooltip.visible"
        class="tooltip"
        :style="{ left: tooltip.x + 'px', top: tooltip.y + 'px' }"
      >[ {{ tooltip.text }} ]</div>
    </Transition>

    <!-- SECTION PANEL -->
    <Transition name="panel">
      <div v-if="activeSection" class="panel-overlay" @click.self="closeSection">
        <div class="panel">
          <button class="panel-close" @click="closeSection">✕ FECHAR</button>

          <!-- WORKS -->
          <template v-if="activeSection === 'works'">
            <div class="panel-eyebrow">Selected Works</div>
            <h2 class="panel-title">{{ sectionContent.works.title }}</h2>
            <div class="panel-divider"></div>
            <div class="works-list">
              <div v-for="(item, i) in sectionContent.works.items" :key="i" class="work-item">
                <span class="work-idx">0{{ i+1 }}</span>
                <div class="work-info">
                  <p class="work-cat">{{ item.cat }}</p>
                  <h3 class="work-title">{{ item.title }}</h3>
                </div>
                <span class="work-year">{{ item.year }}</span>
              </div>
            </div>
          </template>

          <!-- ABOUT -->
          <template v-if="activeSection === 'about'">
            <div class="panel-eyebrow">About Me</div>
            <h2 class="panel-title">Thaynã<br/><span class="panel-muted">Bittencourt</span></h2>
            <div class="panel-divider"></div>
            <p class="panel-bio">{{ sectionContent.about.bio }}</p>
            <div class="about-stats">
              <div v-for="stat in sectionContent.about.stats" :key="stat.label" class="stat">
                <div class="stat-val">{{ stat.val }}</div>
                <div class="stat-label">{{ stat.label }}</div>
              </div>
            </div>
          </template>

          <!-- SKILLS -->
          <template v-if="activeSection === 'skills'">
            <div class="panel-eyebrow">Tech Stack</div>
            <h2 class="panel-title">{{ sectionContent.skills.title }}</h2>
            <div class="panel-divider"></div>
            <div class="skills-list">
              <div v-for="skill in sectionContent.skills.items" :key="skill.name" class="skill-item">
                <div class="skill-header">
                  <span class="skill-name">{{ skill.name }}</span>
                  <span class="skill-pct">{{ skill.level }}%</span>
                </div>
                <div class="skill-bar-track">
                  <div class="skill-bar" :style="{ width: skill.level + '%' }"></div>
                </div>
              </div>
            </div>
          </template>

          <!-- CONTACT -->
          <template v-if="activeSection === 'contact'">
            <div class="panel-eyebrow">Get In Touch</div>
            <h2 class="panel-title">{{ sectionContent.contact.title }}</h2>
            <div class="panel-divider"></div>
            <div class="contact-list">
              <a
                v-for="link in sectionContent.contact.links"
                :key="link.label"
                :href="link.href"
                target="_blank"
                class="contact-item"
              >
                <span class="contact-label">{{ link.label }}</span>
                <span class="contact-val">{{ link.val }} ↗</span>
              </a>
            </div>
          </template>

        </div>
      </div>
    </Transition>

  </div>
</template>

<style scoped>
:global(html, body) {
  margin: 0; padding: 0;
  overflow: hidden;
  background: #080a0c;
  cursor: none;
}
:global(*) { box-sizing: border-box; }

@font-face {
  font-family: 'UberMove';
  src: url('/fonts/UberMoveMedium.otf') format('opentype');
  font-weight: 400; font-display: swap;
}
@font-face {
  font-family: 'UberMove';
  src: url('/fonts/UberMoveBold.otf') format('opentype');
  font-weight: 700; font-display: swap;
}

:root {
  --acc: #d97706;
  --acc2: #4ade80;
  --bg: #080a0c;
  --surface: #0f1214;
  --border: rgba(255,255,255,0.07);
  --text: #e0d8cc;
  --muted: #556;
  --font: 'UberMove', 'Courier New', monospace;
}

.world-root {
  width: 100vw; height: 100vh;
  overflow: hidden;
  font-family: var(--font);
  color: var(--text);
  position: relative;
}

.world-canvas {
  display: block;
  width: 100%; height: 100%;
  position: absolute; inset: 0;
}

/* ── LOADER ── */
.loader {
  position: fixed; inset: 0; z-index: 9999;
  background: #000;
  display: flex; align-items: center; justify-content: center;
}
.loader-inner { text-align: center; }
.loader-logo {
  font-size: 1.2rem; font-weight: 700;
  letter-spacing: 0.2em; margin-bottom: 2rem; color: var(--text);
}
.acc { color: var(--acc); }
.loader-pct {
  font-size: clamp(4rem, 15vw, 10rem);
  font-weight: 700; line-height: 1;
  letter-spacing: -0.04em; color: var(--text);
  margin-bottom: 2rem;
}
.loader-pct sup { font-size: 0.28em; color: var(--acc); vertical-align: super; }
.loader-bar-track {
  width: 200px; height: 1px;
  background: rgba(255,255,255,0.1);
  margin: 0 auto 1rem;
}
.loader-bar {
  height: 100%; background: var(--acc);
  transition: width 0.15s linear;
}
.loader-label {
  font-size: 0.6rem; letter-spacing: 0.2em;
  text-transform: uppercase; color: var(--muted);
}
.loader-fade-leave-active { transition: opacity 0.6s ease; }
.loader-fade-leave-to { opacity: 0; }

/* ── HUD ── */
.hud-tl, .hud-tr, .hud-bl, .hud-br {
  position: fixed; z-index: 100;
  pointer-events: none;
}
.hud-tl { top: 1.5rem; left: 1.8rem; }
.hud-tr { top: 1.5rem; right: 1.8rem; }
.hud-bl { bottom: 1.5rem; left: 1.8rem; pointer-events: all; }
.hud-br {
  bottom: 1.5rem; right: 1.8rem;
  display: flex; align-items: center; gap: 1.2rem;
  pointer-events: all;
}

.hud-name {
  font-size: 0.85rem; font-weight: 700;
  letter-spacing: 0.15em; color: var(--text);
}
.hud-role {
  font-size: 0.6rem; letter-spacing: 0.15em;
  text-transform: uppercase; color: var(--muted); margin-top: 0.2rem;
}

.hud-badge {
  display: flex; flex-direction: column; align-items: flex-end; gap: 0.2rem;
  border: 1px solid var(--border); padding: 0.5rem 0.8rem; border-radius: 4px;
}
.badge-label { font-size: 0.55rem; letter-spacing: 0.2em; color: var(--muted); }
.badge-year { font-size: 0.9rem; font-weight: 700; color: var(--acc); }

.hud-check {
  display: flex; align-items: center; gap: 0.5rem;
  font-size: 0.6rem; letter-spacing: 0.15em; text-transform: uppercase;
  color: var(--muted); cursor: pointer; margin-bottom: 0.5rem;
}
.hud-check input { accent-color: var(--acc); cursor: pointer; }
.hud-hint {
  font-size: 0.55rem; letter-spacing: 0.12em;
  text-transform: uppercase; color: var(--muted);
  opacity: 0.6;
}

.hud-link {
  font-size: 0.6rem; font-weight: 700;
  letter-spacing: 0.12em; text-transform: uppercase;
  color: var(--muted); text-decoration: none;
  transition: color 0.2s;
}
.hud-link:hover { color: var(--text); }
.hud-cta {
  color: #000; background: var(--acc);
  padding: 0.5rem 1rem; border-radius: 100px;
  font-size: 0.6rem; font-weight: 700;
  letter-spacing: 0.1em; text-transform: uppercase;
  transition: opacity 0.2s;
}
.hud-cta:hover { opacity: 0.85; color: #000; }

/* ── CURSOR ── */
.cursor-dot {
  position: fixed; z-index: 200;
  width: 8px; height: 8px;
  background: var(--acc); border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
  transition: transform 0.1s;
}

/* ── TOOLTIP ── */
.tooltip {
  position: fixed; z-index: 200;
  font-size: 0.7rem; font-weight: 700;
  letter-spacing: 0.15em; text-transform: uppercase;
  color: var(--acc);
  pointer-events: none;
  transform: translateX(-50%);
  white-space: nowrap;
}
.tt-enter-active, .tt-leave-active { transition: opacity 0.2s; }
.tt-enter-from, .tt-leave-to { opacity: 0; }

/* ── PANEL ── */
.panel-overlay {
  position: fixed; inset: 0; z-index: 500;
  background: rgba(0,0,0,0.6);
  display: flex; align-items: center; justify-content: flex-end;
  backdrop-filter: blur(4px);
}
.panel {
  width: 100%; max-width: 480px; height: 100vh;
  background: var(--surface);
  border-left: 1px solid var(--border);
  padding: 3rem 2.5rem;
  overflow-y: auto;
  position: relative;
}
.panel-close {
  position: absolute; top: 1.5rem; right: 1.5rem;
  background: none; border: 1px solid var(--border);
  color: var(--muted); font-family: var(--font);
  font-size: 0.6rem; letter-spacing: 0.15em;
  text-transform: uppercase; padding: 0.4rem 0.8rem;
  border-radius: 4px; cursor: pointer;
  transition: border-color 0.2s, color 0.2s;
}
.panel-close:hover { border-color: var(--acc); color: var(--acc); }

.panel-eyebrow {
  font-size: 0.65rem; letter-spacing: 0.15em;
  text-transform: uppercase; color: var(--acc); margin-bottom: 0.8rem;
}
.panel-title {
  font-size: clamp(2rem, 5vw, 3rem); font-weight: 700;
  letter-spacing: -0.04em; line-height: 1;
  text-transform: uppercase; margin: 0 0 1.5rem; color: var(--text);
}
.panel-muted { color: #333; }
.panel-divider { width: 100%; height: 1px; background: var(--border); margin-bottom: 2rem; }

/* Works */
.works-list { display: flex; flex-direction: column; gap: 0; }
.work-item {
  display: flex; align-items: flex-start; gap: 1.2rem;
  padding: 1.2rem 0; border-bottom: 1px solid var(--border);
}
.work-idx { font-size: 0.6rem; color: var(--muted); letter-spacing: 0.1em; padding-top: 0.2rem; }
.work-info { flex: 1; }
.work-cat { font-size: 0.62rem; letter-spacing: 0.1em; text-transform: uppercase; color: var(--acc); margin-bottom: 0.3rem; }
.work-title { font-size: 1rem; font-weight: 700; letter-spacing: -0.02em; text-transform: uppercase; color: var(--text); }
.work-year { font-size: 0.62rem; color: var(--muted); font-family: 'Courier New'; }

/* About */
.panel-bio { font-size: 0.9rem; line-height: 1.75; color: #888; margin-bottom: 2.5rem; }
.about-stats { display: flex; gap: 2rem; }
.stat { text-align: center; }
.stat-val { font-size: 2rem; font-weight: 700; color: var(--acc); }
.stat-label { font-size: 0.6rem; letter-spacing: 0.1em; text-transform: uppercase; color: var(--muted); }

/* Skills */
.skills-list { display: flex; flex-direction: column; gap: 1.5rem; }
.skill-header { display: flex; justify-content: space-between; margin-bottom: 0.5rem; }
.skill-name { font-size: 0.8rem; font-weight: 700; letter-spacing: 0.05em; text-transform: uppercase; color: var(--text); }
.skill-pct { font-size: 0.7rem; color: var(--acc); font-family: 'Courier New'; }
.skill-bar-track { width: 100%; height: 2px; background: var(--border); }
.skill-bar { height: 100%; background: var(--acc); transition: width 0.8s ease; }

/* Contact */
.contact-list { display: flex; flex-direction: column; gap: 0; }
.contact-item {
  display: flex; justify-content: space-between; align-items: center;
  padding: 1.2rem 0; border-bottom: 1px solid var(--border);
  text-decoration: none; transition: background 0.2s;
}
.contact-item:hover { background: rgba(217,119,6,0.05); }
.contact-label { font-size: 0.65rem; letter-spacing: 0.12em; text-transform: uppercase; color: var(--muted); }
.contact-val { font-size: 0.8rem; font-weight: 700; color: var(--acc); }

/* Panel transition */
.panel-enter-active, .panel-leave-active { transition: opacity 0.3s; }
.panel-enter-active .panel, .panel-leave-active .panel { transition: transform 0.4s cubic-bezier(0.4,0,0.2,1); }
.panel-enter-from { opacity: 0; }
.panel-enter-from .panel { transform: translateX(100%); }
.panel-leave-to { opacity: 0; }
.panel-leave-to .panel { transform: translateX(100%); }
</style>