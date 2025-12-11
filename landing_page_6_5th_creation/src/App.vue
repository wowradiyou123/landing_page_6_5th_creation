<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue'

const isScrolled = ref(false)
const palette = ref([
  '#2E294E',
  '#541388',
  '#FFD400',
  '#D90368',
  '#04A777',
])

const handlePointerMove = (event) => {
  if (!window.innerWidth || !window.innerHeight) return
  const xRatio = event.clientX / window.innerWidth
  const yRatio = event.clientY / window.innerHeight
  const root = document.documentElement
  root.style.setProperty('--cursor-x', `${(xRatio * 100).toFixed(2)}%`)
  root.style.setProperty('--cursor-y', `${(yRatio * 100).toFixed(2)}%`)
}

const randomBrightColor = () => {
  const h = Math.floor(Math.random() * 360)
  const s = 70 + Math.random() * 20
  const l = 55 + Math.random() * 10
  return `hsl(${h}deg, ${s}%, ${l}%)`
}

const randomHexColor = () => {
  const n = Math.floor(Math.random() * 0xffffff)
  return `#${n.toString(16).padStart(6, '0').toUpperCase()}`
}

const shufflePalette = () => {
  palette.value = palette.value.map(() => randomHexColor())
}

const handleAccentChunkHover = (event) => {
  const target = event?.target
  if (!(target instanceof HTMLElement)) return
  target.style.color = randomBrightColor()
}

let io

const setupScrollAnimations = () => {
  const elements = document.querySelectorAll('.reveal')
  if (!('IntersectionObserver' in window) || !elements.length) return

  io = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add('is-visible')
          io.unobserve(entry.target)
        }
      })
    },
    {
      threshold: 0.1,
    },
  )

  elements.forEach((el) => io.observe(el))
}

const handleScroll = () => {
  isScrolled.value = window.scrollY > 24
}

onMounted(() => {
  window.addEventListener('pointermove', handlePointerMove)
  window.addEventListener('scroll', handleScroll, { passive: true })
  handleScroll()
  setupScrollAnimations()
})

onBeforeUnmount(() => {
  window.removeEventListener('pointermove', handlePointerMove)
  window.removeEventListener('scroll', handleScroll)
  if (io) io.disconnect()
})
</script>

<template>
  <div class="page">
    <!-- Top notification bar -->
    <div class="top-banner">
      <span>✨ 초고속 컬러 팔레트 생성기!</span>
      <button class="top-banner-btn">프리미엄 1개월 무료 체험하기!</button>
    </div>

    <!-- Large decorative side text -->
    <div class="side-text side-text--left">
      <span class="side-text-chunk" @mousemove="handleAccentChunkHover">가나다</span>
      <span class="side-text-chunk" @mousemove="handleAccentChunkHover">라마바</span>
      <span class="side-text-chunk" @mousemove="handleAccentChunkHover">사</span>
    </div>
    <div class="side-text side-text--right">
      <span class="side-text-chunk" @mousemove="handleAccentChunkHover">ABC</span>
      <span class="side-text-chunk" @mousemove="handleAccentChunkHover">DEF</span>
      <span class="side-text-chunk" @mousemove="handleAccentChunkHover">G</span>
    </div>

    <!-- Navbar -->
    <header class="navbar" :class="{ 'navbar--scrolled': isScrolled }">
      <div class="navbar-shell">
        <div class="nav-left">
          <div class="logo">ChromaSense</div>
          <nav class="nav-links desktop-only" aria-label="Main navigation">
            <a href="#">Tools</a>
            <a href="#">Discover</a>
            <a href="#">Download</a>
            <a href="#">Company</a>
          </nav>
        </div>
        <div class="nav-right">
          <button class="link-btn desktop-only">Premium</button>
          <button class="link-btn desktop-only">Sign in</button>
          <button class="primary-btn desktop-only">Sign up</button>
          <button class="nav-menu mobile-only" aria-label="Open menu">
            ☰
          </button>
        </div>
      </div>
    </header>

    <!-- Hero section -->
    <main class="main">
      <section class="hero" aria-labelledby="hero-heading">
        <div class="hero-text reveal">
          <p class="eyebrow">Palette Generator</p>
          <h1 id="hero-heading" class="hero-heading">
            <span class="hero-heading-chunk" @mouseenter="handleAccentChunkHover">초고속</span><br />
            <span class="hero-heading-chunk" @mouseenter="handleAccentChunkHover">색상 팔레트</span
            ><br />
            <span class="hero-heading-chunk" @mouseenter="handleAccentChunkHover">생성기!</span>
          </h1>
          <p class="subtitle">
            완벽한 팔레트를 만들거나 수많은 아름다운 색상 조합에서 영감을 받아보세요.
          </p>
          <div class="hero-actions">
            <button class="primary-btn hero-main-btn">Start the Generator</button>
            <button class="ghost-btn">Explore 10M+ Palettes</button>
          </div>
        </div>

        <div class="hero-preview reveal" aria-hidden="true">
          <div class="hero-card">
            <div class="hero-card-header">
              <span class="hero-card-title">Color palette</span>
              <button class="mini-btn" @click="shufflePalette">
                <span class="mini-btn-icon" aria-hidden="true">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    fill="none"
                    viewBox="0 0 24 24"
                    stroke-width="1.5"
                    stroke="currentColor"
                    class="mini-btn-svg"
                  >
                    <path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      d="M16.023 9.348h4.992v-.001M2.985 19.644v-4.992m0 0h4.992m-4.993 0 3.181 3.183a8.25 8.25 0 0 0 13.803-3.7M4.031 9.865a8.25 8.25 0 0 1 13.803-3.7l3.181 3.182m0-4.991v4.99"
                    />
                  </svg>
                </span>
                <span>새로고침</span>
              </button>
            </div>
            <div class="palette-strip">
              <div
                v-for="(color, index) in palette"
                :key="index"
                class="swatch"
                :style="{ '--swatch-color': color }"
              >
                <span>{{ color }}</span>
              </div>
            </div>
            <div class="hero-card-footer">
              <span>Hit spacebar to generate</span>
            </div>
          </div>
        </div>
      </section>

      <!-- Tools section -->
      <section class="section section-tools">
        <div class="section-header reveal">
          <p class="eyebrow eyebrow-muted">Our tools</p>
          <h2>Our tools, loved by millions</h2>
        </div>
        <p class="section-subtitle reveal">
          Generate palettes, extract colors from images, check accessibility and more.
        </p>
        <div class="cards-grid">
          <article class="tool-card reveal">
            <h3>Palette Generator</h3>
            <p>
              Create beautiful color schemes in seconds with the worldwide loved palette tool.
              Just hit the spacebar!
            </p>
            <button class="text-link">Start the Generator →</button>
          </article>
          <article class="tool-card reveal">
            <h3>Explore Palettes</h3>
            <p>
              Discover millions of color palettes by topic, style and color. Save your favorites.
            </p>
            <button class="text-link">Explore 10M+ palettes →</button>
          </article>
          <article class="tool-card reveal">
            <h3>Image Picker</h3>
            <p>
              Extract beautiful colors from any image and turn them into ready-to-use palettes.
            </p>
            <button class="text-link">Launch the image picker →</button>
          </article>
          <article class="tool-card reveal">
            <h3>Contrast Checker</h3>
            <p>
              Calculate contrast ratios and ensure your designs meet accessibility standards.
            </p>
            <button class="text-link">Try the contrast checker →</button>
          </article>
          <article class="tool-card reveal">
            <h3>Palette Visualizer</h3>
            <p>
              Preview your colors on real designs in real-time before using them in your projects.
            </p>
            <button class="text-link">Open the visualizer →</button>
          </article>
          <article class="tool-card reveal">
            <h3>Color Bot</h3>
            <p>
              Chat with our AI-powered Color Bot and get color suggestions tailored to your brief.
            </p>
            <button class="text-link">Chat with Color Bot →</button>
          </article>
          <article class="tool-card reveal">
            <h3>색상 선택기</h3>
            <p>
              색상의 의미, 용법, 변형, 접근성 및 변환과 같은 유용한 색상 정보를 빠르게
              확인해 보세요.
            </p>
            <button class="text-link">색상 선택 도구 실행하기 →</button>
          </article>
          <article class="tool-card reveal">
            <h3>테일윈드 컬러</h3>
            <p>
              Tailwind CSS 색상을 실제 UI에 미리 입혀 보고, 프로젝트에 사용할 팔레트를
              빠르게 결정해 보세요.
            </p>
            <button class="text-link">Tailwind 색상 미리보기 →</button>
          </article>
        </div>
      </section>

      <!-- Korean tools section (separate cards) -->
      <section class="section section-tools">
        <div class="section-header reveal">
          <p class="eyebrow eyebrow-muted">도구</p>
          <h2>ChromaSense 도구를 한국어로 만나보세요</h2>
        </div>
        <div class="cards-grid">
          <article class="tool-card reveal">
            <h3>팔레트 생성기</h3>
            <p>
              전 세계적으로 사랑받는 팔레트 도구를 사용하여 몇 초 만에 아름다운 색상 조합을
              만들어 보세요. 프리미엄만 이용할 수 있어요.
            </p>
            <button class="primary-btn">프리미엄 1개월 무료체험을 이용해보세요!</button>
          </article>
          <article class="tool-card reveal">
            <h3>팔레트 탐색하기</h3>
            <p>
              수천 가지의 아름다운 색상 조합에서 영감을 얻으세요. 색상, 스타일, 주제 또는 헥스
              값으로 검색할 수 있습니다.
            </p>
            <button class="text-link">1천만 개 이상의 팔레트를 살펴보세요</button>
          </article>
          <article class="tool-card reveal">
            <h3>팔레트 시각화 도구</h3>
            <p>
              실제 디자인에서 색상을 미리 보고 프로젝트에 사용하기 전에 어떻게 보이는지
              확인하세요.
            </p>
            <button class="text-link">시각화 도구를 엽니다</button>
          </article>
          <article class="tool-card reveal">
            <h3>HTML 색상표</h3>
            <p>
              HTML에서 사용할 수 있는 다양한 색상 이름과 HEX 코드를 한눈에 확인하고,
              프로젝트에 맞는 색을 빠르게 찾아보세요.
            </p>
            <button class="text-link">HTML 색상표 보기</button>
          </article>
          <article class="tool-card reveal">
            <h3>무료 폰트</h3>
            <p>
              프로젝트에 사용할 수 있는 아름다운 무료 폰트를 탐색하고, 분위기에 맞는
              타이포그래피를 골라보세요.
            </p>
            <div class="tool-actions">
              <button class="text-link">무료 폰트 살펴보기</button>
              <button class="text-link">그라디언트 찾아보기</button>
              <button class="text-link">그라디언트 만들기</button>
            </div>
          </article>
        </div>
        <div class="apps-block reveal">
          <h2 class="apps-title">앱</h2>
          <div class="apps-buttons">
            <button class="apps-button">iOS 앱</button>
            <button class="apps-button">Figma 플러그인</button>
            <button class="apps-button">어도비 확장 프로그램</button>
            <button class="apps-button">크롬 확장 프로그램</button>
          </div>
        </div>
      </section>

      <!-- Callout section -->
      <section class="section section-accent section-pro">
        <div class="section-inner reveal">
          <div>
            <h2>Make something coolorful!</h2>
            <p class="section-subtitle">
              Organize your palettes into projects, export them in multiple formats, and access
              them anywhere.
            </p>
          </div>
          <div class="section-actions">
            <button class="primary-btn">프리미엄</button>
            <button class="ghost-btn">View plans</button>
          </div>
        </div>
      </section>
    </main>

    <!-- Footer -->
    <footer class="footer">
      <div class="footer-inner">
        <div class="footer-top">
          <div class="footer-col reveal">
            <h4>Tools</h4>
            <a href="#">Generate your palettes</a>
            <a href="#">Explore popular palettes</a>
            <a href="#">Extract palette from image</a>
            <a href="#">Contrast checker</a>
            <a href="#">Palette visualizer</a>
          </div>
          <div class="footer-col reveal">
            <h4>Discover</h4>
            <a href="#">List of colors</a>
            <a href="#">Browse gradients</a>
            <a href="#">Create a gradient</a>
            <a href="#">Free fonts</a>
          </div>
          <div class="footer-col reveal">
            <h4>Apps</h4>
            <a href="#">iOS App</a>
            <a href="#">Figma Plugin</a>
            <a href="#">Adobe Extension</a>
            <a href="#">Chrome Extension</a>
          </div>
          <div class="footer-col reveal">
            <h4>Company</h4>
            <a href="#">Pricing</a>
            <a href="#">License</a>
            <a href="#">Terms of service</a>
            <a href="#">Privacy policy</a>
            <a href="#">Help center</a>
          </div>
        </div>
        <div class="footer-bottom">
          <span>© ChromaSense. Let's make something colorful!</span>
          <div class="footer-lang">
            <button class="footer-lang-btn">한국어</button>
            <button class="footer-lang-btn">English</button>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  position: relative;
  display: flex;
  flex-direction: column;
  color: var(--color-text-primary);
  background:
    radial-gradient(circle at var(--cursor-x) var(--cursor-y), rgba(56, 189, 248, 0.55), transparent 55%),
    radial-gradient(circle at 0% 0%, rgba(88, 28, 135, 0.5), transparent 55%),
    radial-gradient(circle at bottom, rgba(15, 23, 42, 0.9), #020617);
  transition: background 0.35s ease-out;
}

.page::before {
  content: '';
  pointer-events: none;
  position: absolute;
  inset: 0;
  opacity: 0.16;
  mix-blend-mode: soft-light;
  background-image:
    radial-gradient(circle at 10% 20%, rgba(148, 163, 184, 0.18) 0, transparent 45%),
    radial-gradient(circle at 80% 0%, rgba(15, 23, 42, 0.2) 0, transparent 55%);
}

.top-banner {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-2) var(--space-6);
  background: linear-gradient(
    to right,
    rgba(15, 23, 42, 0.96),
    rgba(15, 23, 42, 0.92)
  );
  border-bottom: 1px solid var(--color-border-soft);
  font-size: 0.875rem;
  position: relative;
  z-index: 1;
}

.top-banner-btn {
  padding: 0.3rem 0.9rem;
  border-radius: var(--radius-pill);
  border: 1px solid rgba(56, 189, 248, 0.8);
  background: linear-gradient(135deg, #38bdf8, #6366f1);
  color: #ffffff;
  font-size: 0.8rem;
  font-weight: 600;
  cursor: pointer;
  box-shadow: 0 10px 24px rgba(37, 99, 235, 0.45);
  transition: transform 0.15s ease-out, box-shadow 0.15s ease-out,
    filter 0.15s ease-out;
}

.top-banner-btn:hover {
  transform: translateY(-1px);
  filter: brightness(1.02);
  box-shadow: 0 14px 32px rgba(234, 179, 8, 0.5);
}

.navbar {
  position: sticky;
  top: 0;
  z-index: 20;
  border-bottom: 1px solid var(--color-border-soft);
  backdrop-filter: blur(26px);
  background: linear-gradient(
    to bottom,
    rgba(15, 23, 42, 0.96),
    rgba(15, 23, 42, 0.85),
    transparent
  );
}

.navbar--scrolled {
  background: linear-gradient(
    to bottom,
    rgba(15, 23, 42, 0.98),
    rgba(15, 23, 42, 0.94),
    rgba(15, 23, 42, 0.9)
  );
  box-shadow: 0 8px 32px rgba(15, 23, 42, 0.9);
}

.navbar--scrolled .navbar-shell {
  padding-block: var(--space-2);
}

.navbar-shell {
  max-width: 1280px;
  margin: 0 auto;
  padding: var(--space-4) var(--space-6);
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.nav-left {
  display: flex;
  align-items: center;
  gap: var(--space-6);
}

.logo {
  font-family: 'Bitcount Prop Double Ink', 'Chiron GoRound TC', 'Nanum Gothic Coding',
    var(--font-sans);
  font-weight: 800;
  font-size: 1.35rem;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  background: linear-gradient(
    90deg,
    #f97316,
    #ec4899,
    #6366f1,
    #22c55e,
    #eab308,
    #f97316
  );
  background-size: 200% auto;
  -webkit-background-clip: text;
  color: transparent;
  animation: logo-rainbow 6s linear infinite;
}

.nav-links {
  display: flex;
  gap: var(--space-4);
  font-size: 0.95rem;
}

.nav-links a {
  color: var(--color-text-soft);
  text-decoration: none;
  position: relative;
  padding-bottom: 0.1rem;
}

.nav-links a::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: -0.25rem;
  width: 0;
  height: 2px;
  border-radius: 999px;
  background: linear-gradient(
    90deg,
    var(--color-accent-primary),
    var(--color-accent-secondary)
  );
  transition: width 0.18s ease-out;
}

.nav-links a:hover::after {
  width: 100%;
}

.nav-right {
  display: flex;
  align-items: center;
  gap: var(--space-3);
}

.link-btn {
  background: transparent;
  border-radius: var(--radius-pill);
  border: none;
  color: var(--color-text-soft);
  font-size: 0.9rem;
  font-weight: 500;
  padding: 0.45rem 0.75rem;
  cursor: pointer;
}

.primary-btn {
  padding: 0.6rem 1.5rem;
  border-radius: var(--radius-pill);
  border: 1px solid rgba(56, 189, 248, 0.4);
  background: linear-gradient(
    135deg,
    var(--color-accent-primary),
    var(--color-accent-secondary)
  );
  color: white;
  font-weight: 600;
  font-size: 0.9rem;
  cursor: pointer;
  box-shadow: var(--shadow-soft);
  transition: transform 0.15s ease-out, box-shadow 0.15s ease-out,
    filter 0.15s ease-out;
}

.primary-btn:hover {
  transform: translateY(-1px);
  filter: brightness(1.06);
  box-shadow: var(--shadow-strong);
  animation: primary-glow 0.6s ease-out;
}

.ghost-btn {
  padding: 0.6rem 1.4rem;
  border-radius: var(--radius-pill);
  border: 1px solid rgba(148, 163, 184, 0.6);
  background: rgba(15, 23, 42, 0.7);
  color: var(--color-text-soft);
  font-size: 0.9rem;
  font-weight: 500;
  cursor: pointer;
  box-shadow: 0 0 0 1px rgba(15, 23, 42, 0.9);
  transition: background 0.15s ease-out, border-color 0.15s ease-out,
    transform 0.15s ease-out;
}

.ghost-btn:hover {
  background: rgba(15, 23, 42, 0.9);
  border-color: rgba(148, 163, 184, 0.9);
  transform: translateY(-1px);
}

.nav-menu {
  background: rgba(15, 23, 42, 0.8);
  border-radius: 999px;
  border: 1px solid rgba(148, 163, 184, 0.5);
  color: var(--color-text-primary);
  font-size: 1.25rem;
  padding: 0.25rem 0.75rem;
  cursor: pointer;
}

.main {
  padding: var(--space-10) var(--space-6) var(--space-12);
  flex: 1;
  max-width: 1280px;
  margin-inline: auto;
  position: relative;
  z-index: 1;
}

.hero {
  display: grid;
  grid-template-columns: minmax(0, 1.4fr) minmax(0, 1fr);
  align-items: center;
  gap: var(--space-10);
  padding-bottom: var(--space-10);
}

.hero-text {
  max-width: 640px;
}

.eyebrow {
  font-size: 0.78rem;
  text-transform: uppercase;
  letter-spacing: 0.22em;
  color: #a5b4fc;
  margin-bottom: 0.5rem;
}

.eyebrow-muted {
  color: var(--color-text-muted);
}

.hero h1 {
  font-size: clamp(2.4rem, 3vw + 1.8rem, 3.6rem);
  line-height: 1.02;
  margin: 0 0 var(--space-3);
}

.hero-heading-chunk {
  color: var(--color-text-primary);
  transition: color 0.3s ease-out;
}

.subtitle {
  font-size: 1.02rem;
  color: var(--color-text-soft);
  max-width: 32rem;
}

.hero-actions {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-3);
  margin: var(--space-7) 0 var(--space-4);
}

.hero-main-btn {
  font-size: 0.95rem;
}

.trusted-text {
  font-size: 0.8rem;
  text-transform: uppercase;
  letter-spacing: 0.16em;
  color: var(--color-text-muted);
  margin-top: var(--space-3);
}

.logo-row {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-4);
  margin-top: var(--space-3);
  font-size: 0.84rem;
  color: var(--color-text-muted);
}

.logo-row span {
  opacity: 0.9;
}

.hero-preview {
  display: flex;
  justify-content: center;
}

.hero-card {
  background: linear-gradient(
      145deg,
      rgba(15, 23, 42, 0.95),
      rgba(15, 23, 42, 0.9)
    ),
    radial-gradient(circle at top left, #1e293b, transparent 60%);
  border-radius: var(--radius-xl);
  padding: var(--space-5);
  width: 100%;
  max-width: 430px;
  box-shadow: var(--shadow-strong);
  border: 1px solid var(--color-border-subtle);
  backdrop-filter: blur(28px);
  transition: transform 0.22s ease-out, box-shadow 0.22s ease-out,
    border-color 0.22s ease-out;
}

.hero-card:hover {
  transform: translateY(-4px) scale(1.01);
  border-color: rgba(56, 189, 248, 0.7);
  box-shadow: var(--shadow-strong);
}

.hero-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--space-3);
}

.hero-card-title {
  font-size: 0.85rem;
  color: var(--color-text-soft);
}

.mini-btn {
  padding: 0.2rem 0.7rem;
  border-radius: var(--radius-pill);
  border: 1px solid rgba(148, 163, 184, 0.85);
  background: rgba(15, 23, 42, 0.9);
  color: var(--color-text-soft);
  font-size: 0.75rem;
  cursor: pointer;
  display: inline-flex;
  align-items: center;
  gap: 0.35rem;
}

.mini-btn-svg {
  width: 14px;
  height: 14px;
}

.palette-strip {
  display: grid;
  grid-template-columns: repeat(5, minmax(0, 1fr));
  border-radius: 1rem;
  overflow: hidden;
  box-shadow: 0 16px 40px rgba(15, 23, 42, 0.9);
}

.swatch {
  background: var(--swatch-color);
  min-height: 185px;
  display: flex;
  align-items: flex-end;
  padding: 0.6rem;
  font-size: 0.7rem;
  font-weight: 600;
  color: #f9fafb;
  transition: transform 0.22s ease-out, box-shadow 0.22s ease-out;
}

.swatch span {
  background: rgba(15, 23, 42, 0.78);
  padding: 0.2rem 0.45rem;
  border-radius: var(--radius-pill);
}

.swatch:hover {
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 12px 26px rgba(15, 23, 42, 0.85);
}

.hero-card-footer {
  margin-top: var(--space-3);
  font-size: 0.78rem;
  color: var(--color-text-muted);
  text-align: right;
}

.section {
  padding-top: var(--space-10);
}

.section-header {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.section h2 {
  font-size: clamp(1.6rem, 1.5vw + 1.2rem, 2rem);
  margin: 0;
}

.section-subtitle {
  font-size: 0.98rem;
  color: var(--color-text-soft);
  max-width: 30rem;
  margin-top: var(--space-2);
}

.cards-grid {
  margin-top: var(--space-6);
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: var(--space-5);
}

.tool-card {
  background: var(--color-bg-elevated-soft);
  border-radius: var(--radius-card);
  padding: var(--space-5);
  border: 1px solid var(--color-border-subtle);
  box-shadow: var(--shadow-soft);
  display: flex;
  flex-direction: column;
  gap: var(--space-3);
  transition: transform 0.22s ease-out, box-shadow 0.22s ease-out,
    border-color 0.22s ease-out, background 0.25s ease-out;
}

.tool-card:hover {
  transform: translateY(-4px) scale(1.01);
  border-color: rgba(56, 189, 248, 0.7);
  background: var(--color-bg-elevated);
  box-shadow: var(--shadow-strong);
}

.tool-card h3 {
  font-size: 1.05rem;
  margin: 0;
}

.tool-card p {
  font-size: 0.9rem;
  color: var(--color-text-soft);
  margin: 0;
}

.tool-actions {
  margin-top: var(--space-3);
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-3);
}

.apps-block {
  margin-top: var(--space-10);
  max-width: 640px;
}

.apps-title {
  font-size: 1.4rem;
  margin: 0 0 var(--space-4);
}

.apps-buttons {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-3);
}

.apps-button {
  padding: 0.55rem 1.3rem;
  border-radius: var(--radius-pill);
  border: 1px solid rgba(148, 163, 184, 0.6);
  background: rgba(15, 23, 42, 0.8);
  color: var(--color-text-soft);
  font-size: 0.88rem;
  font-weight: 500;
  cursor: pointer;
  transition: background 0.18s ease-out, border-color 0.18s ease-out,
    transform 0.18s ease-out;
}

.apps-button:hover {
  background: rgba(15, 23, 42, 1);
  border-color: rgba(56, 189, 248, 0.8);
  transform: translateY(-1px);
}

.text-link {
  background: transparent;
  border: none;
  color: var(--color-accent-primary);
  font-size: 0.85rem;
  font-weight: 500;
  cursor: pointer;
  padding: 0;
  display: inline-flex;
  align-items: center;
  gap: 0.25rem;
}

.text-link:hover {
  color: var(--color-accent-secondary);
  transform: translateX(2px);
}

.section-accent {
  margin-top: var(--space-12);
}

.section-inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: var(--space-6);
  padding: var(--space-5) var(--space-6);
  border-radius: var(--radius-xl);
  background: linear-gradient(
    135deg,
    var(--color-accent-primary),
    var(--color-accent-secondary),
    var(--color-accent-tertiary)
  );
  box-shadow: var(--shadow-soft);
}

.section-tools .cards-grid {
  border-top: 1px solid var(--color-border-soft);
  padding-top: var(--space-6);
}

.section-tools {
  border-radius: var(--radius-xl);
}

.section-pro {
  position: relative;
  overflow: hidden;
}


.section-inner h2 {
  margin: 0 0 var(--space-2);
}

.section-inner .section-subtitle {
  color: #e5e7eb;
  margin-top: 0;
}

.section-actions {
  display: flex;
  flex-wrap: wrap;
  gap: var(--space-3);
  justify-content: flex-end;
}

.footer {
  border-top: 1px solid var(--color-border-subtle);
  padding: var(--space-8) var(--space-6) var(--space-8);
  background: radial-gradient(circle at top, rgba(15, 23, 42, 0.9), #020617);
  position: relative;
  z-index: 1;
}

.footer-inner {
  max-width: 1280px;
  margin: 0 auto;
}

.footer-top {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: var(--space-6);
  margin-bottom: var(--space-6);
}

.footer-col h4 {
  font-size: 0.9rem;
  margin: 0 0 var(--space-3);
  color: var(--color-text-soft);
}

.footer-col a {
  display: block;
  font-size: 0.82rem;
  color: var(--color-text-muted);
  margin-bottom: 0.3rem;
  text-decoration: none;
  position: relative;
  padding-bottom: 0.15rem;
}

.footer-col a:hover {
  color: var(--color-text-primary);
}

.footer-col a::after {
  content: '';
  position: absolute;
  left: 0;
  bottom: 0;
  width: 0;
  height: 1px;
  background: linear-gradient(
    90deg,
    var(--color-accent-primary),
    var(--color-accent-secondary)
  );
  transition: width 0.18s ease-out;
}

.footer-col a:hover::after {
  width: 100%;
}

.footer-bottom {
  font-size: 0.78rem;
  color: var(--color-text-muted);
}

.footer-bottom {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: var(--space-4);
}

.footer-lang {
  display: flex;
  gap: 0.4rem;
}

.footer-lang-btn {
  padding: 0.2rem 0.7rem;
  border-radius: var(--radius-pill);
  border: 1px solid rgba(148, 163, 184, 0.6);
  background: rgba(15, 23, 42, 0.9);
  color: var(--color-text-soft);
  font-size: 0.75rem;
  cursor: pointer;
}

.desktop-only {
  display: inline-flex;
}

.mobile-only {
  display: none;
}

/* Focus styles for accessibility */
.top-banner-btn:focus-visible,
.link-btn:focus-visible,
.primary-btn:focus-visible,
.ghost-btn:focus-visible,
.mini-btn:focus-visible,
.nav-menu:focus-visible,
.nav-links a:focus-visible,
.footer-col a:focus-visible,
.text-link:focus-visible {
  outline: none;
  box-shadow: 0 0 0 2px var(--color-focus-ring);
}

@media (max-width: 1024px) {
  .navbar-shell {
    padding-inline: var(--space-4);
  }

  .main {
    padding-inline: var(--space-4);
  }

  .hero {
    grid-template-columns: minmax(0, 1fr);
  }

  .hero-preview {
    order: -1;
  }

  .cards-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .footer-top {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}

@media (max-width: 640px) {
  .top-banner {
    padding-inline: var(--space-3);
    text-align: center;
    flex-direction: column;
  }

  .navbar-shell {
    padding-inline: var(--space-3);
  }

  .main {
    padding-inline: var(--space-3);
  }

  .hero {
    gap: var(--space-8);
  }

  .hero h1 {
    font-size: clamp(2.1rem, 6vw, 2.4rem);
  }

  .hero-actions {
    flex-direction: column;
    align-items: stretch;
  }

  .cards-grid {
    grid-template-columns: minmax(0, 1fr);
  }

  .section-inner {
    flex-direction: column;
    align-items: flex-start;
  }

  .footer-top {
    grid-template-columns: minmax(0, 1fr);
  }

  .desktop-only {
    display: none;
  }

  .mobile-only {
    display: inline-flex;
  }
}

.reveal {
  opacity: 0;
  transform: translateY(16px);
  transition: opacity 0.45s ease-out, transform 0.45s ease-out;
}

.reveal.is-visible {
  opacity: 1;
  transform: translateY(0);
}

.side-text {
  position: fixed;
  top: 50%;
  transform: translateY(-50%);
  font-size: clamp(2.75rem, 3.6vw + 2rem, 4.8rem);
  letter-spacing: 0.16em;
  font-weight: 800;
  opacity: 0.08;
  white-space: nowrap;
  user-select: none;
  pointer-events: auto;
  mix-blend-mode: soft-light;
  transition: color 0.4s ease-out, transform 0.4s ease-out, opacity 0.4s ease-out;
}

.side-text--left {
  left: 2vw;
  text-align: left;
}

.side-text--right {
  right: 2vw;
  text-align: right;
}

.side-text-chunk {
  display: block;
  text-transform: none;
  color: white;
}

.side-text:hover {
  transform: translateY(calc(-50% - 6px)) scale(1.03);
  opacity: 0.18;
}

@media (max-width: 960px) {
  .side-text {
    display: none;
  }
}

@keyframes primary-glow {
  0% {
    box-shadow: var(--shadow-soft);
  }
  50% {
    box-shadow: 0 0 0 1px rgba(56, 189, 248, 0.6), var(--shadow-strong);
  }
  100% {
    box-shadow: var(--shadow-soft);
  }
}

@keyframes logo-rainbow {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}
</style>
