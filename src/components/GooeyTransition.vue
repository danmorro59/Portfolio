<script setup>
import { onMounted, onUnmounted, ref } from 'vue'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'
import { ScrollToPlugin } from 'gsap/ScrollToPlugin'

gsap.registerPlugin(ScrollTrigger, ScrollToPlugin)

const props = defineProps({
  triggerEl: { type: Object, required: true },
  snapTarget: { type: Object, default: null },
})

const emit = defineEmits(['progress'])

const canvasRef = ref(null)
const welcomeRef = ref(null)
let gl = null
let uniforms = {}
let animFrameId = null
let scrollProgress = 0

const VERT_SHADER = `
  precision mediump float;
  varying vec2 vUv;
  attribute vec2 a_position;
  void main() {
    vUv = a_position;
    gl_Position = vec4(a_position, 0.0, 1.0);
  }
`

// Modified shader: at progress=0 the screen is fully covered,
// at progress=1 the goop has melted away downward
const FRAG_SHADER = `
  precision mediump float;

  varying vec2 vUv;
  uniform vec2 u_resolution;
  uniform float u_progress;
  uniform float u_col_width;
  uniform float u_seed;
  uniform float u_scale;
  uniform float u_time;
  uniform float u_speed;
  uniform vec3 u_color;

  vec3 mod289(vec3 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
  vec2 mod289(vec2 x) { return x - floor(x * (1.0 / 289.0)) * 289.0; }
  vec3 permute(vec3 x) { return mod289(((x * 34.0) + 1.0) * x); }

  float snoise(vec2 v) {
    const vec4 C = vec4(0.211324865405187, 0.366025403784439, -0.577350269189626, 0.024390243902439);
    vec2 i = floor(v + dot(v, C.yy));
    vec2 x0 = v - i + dot(i, C.xx);
    vec2 i1 = (x0.x > x0.y) ? vec2(1.0, 0.0) : vec2(0.0, 1.0);
    vec4 x12 = x0.xyxy + C.xxzz;
    x12.xy -= i1;
    i = mod289(i);
    vec3 p = permute(permute(i.y + vec3(0.0, i1.y, 1.0)) + i.x + vec3(0.0, i1.x, 1.0));
    vec3 m = max(0.5 - vec3(dot(x0, x0), dot(x12.xy, x12.xy), dot(x12.zw, x12.zw)), 0.0);
    m = m * m;
    m = m * m;
    vec3 x = 2.0 * fract(p * C.www) - 1.0;
    vec3 h = abs(x) - 0.5;
    vec3 ox = floor(x + 0.5);
    vec3 a0 = x - ox;
    m *= 1.79284291400159 - 0.85373472095314 * (a0 * a0 + h * h);
    vec3 g;
    g.x = a0.x * x0.x + h.x * x0.y;
    g.yz = a0.yz * x12.xz + h.yz * x12.yw;
    return 130.0 * dot(m, g);
  }

  float get_l(vec2 v) {
    return 1.0 - clamp(length(v), 0.0, 1.0);
  }

  float rand(float n) {
    return fract(sin(n) * 43758.5453123);
  }

  void main() {
    float p = 1.0 - u_progress; // invert: 1 at start, 0 at end

    float scale = 0.001 * u_scale;
    float speed = 0.001 * u_speed;

    vec2 uv = vUv;

    // Flip Y so goop hangs from the top
    float flippedY = 1.0 - vUv.y;

    uv.x *= (scale * u_resolution.x);

    vec2 noise_uv = uv;
    noise_uv *= 5.0;
    noise_uv.y *= (0.25 * scale * u_resolution.y);
    noise_uv += vec2(0.0, u_time * 1.5 * speed);

    float shape = 0.0;
    shape += snoise(noise_uv);
    shape = clamp(0.5 + 0.5 * shape, 0.0, 1.0);

    // Coverage: fully covered at p=1, peeling away at p=0
    // Use flippedY so the goop clings to the top and drips from the bottom edge
    float coverage = p * 1.8 - flippedY * 0.8;
    coverage += 0.3 * p * snoise(vec2(vUv.x * 8.0, u_time * speed * 0.5));
    shape *= pow(clamp(coverage + 0.3, 0.0, 1.0), 6.0);
    shape = clamp(shape, 0.0, 1.0);

    float dots = 0.0;
    float bars = 0.0;
    float light = 0.0;

    const int num_col = 9;
    for (int i = 0; i < num_col; i++) {
      vec2 col_uv = vUv;

      float start_time_offset = rand(100.0 * (float(i) + u_seed));
      float c_t = fract(u_time * speed + start_time_offset);
      float drop_time = 0.2 + 0.6 * rand(10.0 * (float(i) + u_seed));

      float before_drop_normal = c_t / drop_time;
      float before_drop_t = pow(before_drop_normal, 0.4) * drop_time;
      float after_drop_normal = max(0.0, c_t - drop_time) / (1.0 - drop_time);
      float after_drop_t_dot = 3.0 * pow(after_drop_normal, 2.0) * (1.0 - drop_time);
      float after_drop_t_bar = pow(after_drop_normal, 2.0) * drop_time;

      float eased_drop_t = step(c_t, drop_time) * before_drop_t;
      eased_drop_t += step(drop_time, c_t) * (drop_time + after_drop_t_dot);

      col_uv.y -= (1.0 + 3.0 * rand(15.0 * float(i))) * (1.0 - p);
      col_uv.x *= (u_resolution.x / u_resolution.y);
      col_uv *= (7.0 * scale * u_resolution.y);
      col_uv.x += (u_col_width * (0.5 * float(num_col) - float(i)));

      vec2 dot_uv = col_uv;
      dot_uv.y += 4.0 * (eased_drop_t - 0.5);

      float d = get_l(dot_uv);
      d = pow(d, 4.0);

      float drop_grow = step(c_t, drop_time) * pow(before_drop_normal, 0.4);
      drop_grow += step(drop_time, c_t) * mix(1.0, 0.8, clamp(7.0 * after_drop_normal, 0.0, 1.0));
      d *= drop_grow;
      d *= step(0.5, drop_time);
      dots += d * p;

      vec2 bar_uv = col_uv;
      bar_uv.y += step(c_t, drop_time) * 4.0 * (before_drop_t - 0.5);
      bar_uv.y += step(drop_time, c_t) * 4.0 * (drop_time - after_drop_t_bar - 0.5);

      float bar = smoothstep(-0.5, 0.0, bar_uv.x) * (1.0 - smoothstep(0.0, 0.5, bar_uv.x));
      bar = pow(bar, 4.0);
      light += bar * smoothstep(0.0, 0.1, -bar_uv.x);
      float bar_mask = smoothstep(-0.2, 0.2, bar_uv.y);
      bar *= bar_mask;

      bars += bar * p;
    }

    shape += bars;
    shape = clamp(shape, 0.0, 1.0);
    shape += dots;

    float gooey = smoothstep(0.48, 0.5, shape);
    gooey -= 0.1 * smoothstep(0.5, 0.6, shape);

    vec3 color = u_color;
    color *= gooey;
    color = mix(color, color + vec3(0.08), max(0.0, 1.0 - 2.0 * flippedY) * light * smoothstep(0.1, 0.7, snoise(0.5 * uv)) * (smoothstep(0.49, 0.6, shape) - smoothstep(0.6, 1.0, shape)));

    gl_FragColor = vec4(color, gooey);
  }
`

function initShader(canvas) {
  const context = canvas.getContext('webgl', { alpha: true, premultipliedAlpha: false })
  if (!context) return null

  context.enable(context.BLEND)
  context.blendFunc(context.SRC_ALPHA, context.ONE_MINUS_SRC_ALPHA)
  context.clearColor(0, 0, 0, 0)

  function createShader(src, type) {
    const shader = context.createShader(type)
    context.shaderSource(shader, src)
    context.compileShader(shader)
    if (!context.getShaderParameter(shader, context.COMPILE_STATUS)) {
      console.error('Shader compile error:', context.getShaderInfoLog(shader))
      context.deleteShader(shader)
      return null
    }
    return shader
  }

  const vs = createShader(VERT_SHADER, context.VERTEX_SHADER)
  const fs = createShader(FRAG_SHADER, context.FRAGMENT_SHADER)

  const program = context.createProgram()
  context.attachShader(program, vs)
  context.attachShader(program, fs)
  context.linkProgram(program)

  if (!context.getProgramParameter(program, context.LINK_STATUS)) {
    console.error('Program link error:', context.getProgramInfoLog(program))
    return null
  }

  const uCount = context.getProgramParameter(program, context.ACTIVE_UNIFORMS)
  for (let i = 0; i < uCount; i++) {
    const name = context.getActiveUniform(program, i).name
    uniforms[name] = context.getUniformLocation(program, name)
  }

  const vertices = new Float32Array([-1, -1, 1, -1, -1, 1, 1, 1])
  const buffer = context.createBuffer()
  context.bindBuffer(context.ARRAY_BUFFER, buffer)
  context.bufferData(context.ARRAY_BUFFER, vertices, context.STATIC_DRAW)

  context.useProgram(program)

  const posLoc = context.getAttribLocation(program, 'a_position')
  context.enableVertexAttribArray(posLoc)
  context.bindBuffer(context.ARRAY_BUFFER, buffer)
  context.vertexAttribPointer(posLoc, 2, context.FLOAT, false, 0, 0)

  // Dark charcoal goop — blends with the site's dark theme
  // Slightly lighter than pure black so the gooey edges are visible
  context.uniform1f(uniforms.u_col_width, 0.7)
  context.uniform1f(uniforms.u_speed, 0.2)
  context.uniform1f(uniforms.u_scale, 0.25)
  context.uniform1f(uniforms.u_seed, 0.231)
  context.uniform3f(uniforms.u_color, 0.12, 0.12, 0.12)

  return context
}

function resize() {
  if (!canvasRef.value || !gl) return
  const dpr = Math.min(window.devicePixelRatio, 2)
  canvasRef.value.width = window.innerWidth * dpr
  canvasRef.value.height = window.innerHeight * dpr
  gl.viewport(0, 0, canvasRef.value.width, canvasRef.value.height)
  gl.uniform2f(uniforms.u_resolution, canvasRef.value.width, canvasRef.value.height)
}

function render() {
  if (!gl) return
  gl.clear(gl.COLOR_BUFFER_BIT)
  gl.uniform1f(uniforms.u_time, performance.now())
  gl.uniform1f(uniforms.u_progress, scrollProgress)
  gl.drawArrays(gl.TRIANGLE_STRIP, 0, 4)
  animFrameId = requestAnimationFrame(render)
}

let scrollTriggerInstance = null

onMounted(() => {
  gl = initShader(canvasRef.value)
  if (!gl) return

  resize()
  window.addEventListener('resize', resize)
  render()

  let hasSnapped = false

  const tl = gsap.timeline({
    scrollTrigger: {
      trigger: props.triggerEl,
      start: 'top top',
      end: 'bottom top',
      scrub: 0.5,
      pin: false,
      onUpdate: (self) => {
        // Once user scrolls past 40%, snap to the hero
        if (self.progress > 0.4 && !hasSnapped && props.snapTarget) {
          hasSnapped = true
          gsap.to(window, {
            scrollTo: { y: props.snapTarget, offsetY: 0 },
            duration: 1,
            ease: 'power2.inOut',
          })
        }
      },
      onLeaveBack: () => {
        hasSnapped = false
      },
    },
  })

  // Drive the shader progress and emit to parent
  tl.to({}, {
    duration: 1,
    onUpdate: function () {
      scrollProgress = this.progress()
      emit('progress', scrollProgress)
    },
  }, 0)

  // Fade out welcome text
  tl.to(welcomeRef.value, {
    opacity: 0,
    y: -60,
    duration: 0.4,
    ease: 'power2.in',
  }, 0)

  scrollTriggerInstance = tl.scrollTrigger
})

onUnmounted(() => {
  if (animFrameId) cancelAnimationFrame(animFrameId)
  if (scrollTriggerInstance) scrollTriggerInstance.kill()
  window.removeEventListener('resize', resize)
  if (gl) {
    gl.getExtension('WEBGL_lose_context')?.loseContext()
    gl = null
  }
})
</script>

<template>
  <!-- Welcome text overlay -->
  <div
    ref="welcomeRef"
    class="fixed inset-0 z-[45] flex flex-col items-center justify-center pointer-events-none"
  >
    <p class="font-roboto text-lg md:text-2xl text-gray-3 uppercase tracking-[0.3em] mb-4">Welcome to</p>
    <h2 class="font-roboto text-4xl md:text-[72px] font-bold text-accent leading-tight tracking-wide">
      My Portfolio
    </h2>
    <div class="mt-8 animate-bounce">
      <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 text-gray-3" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
        <path stroke-linecap="round" stroke-linejoin="round" d="M19 14l-7 7m0 0l-7-7m7 7V3" />
      </svg>
    </div>
  </div>

  <!-- WebGL canvas -->
  <canvas
    ref="canvasRef"
    class="fixed top-0 left-0 w-full h-screen z-40 pointer-events-none"
  />
</template>
