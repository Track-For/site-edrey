<script setup lang="ts">
import { onBeforeUnmount, onMounted, ref } from 'vue'
import {
  Activity,
  ArrowDownRight,
  ArrowRight,
  CalendarDays,
  Camera,
  Check,
  ClipboardCheck,
  HeartHandshake,
  Menu,
  MessageCircle,
  ShieldCheck,
  Smile,
  Sparkles,
  SunMedium,
  WandSparkles,
  X,
} from '@lucide/vue'

const menuOpen = ref(false)
const activeTreatment = ref<number | null>(null)
const formStatus = ref('')
const showIntro = ref(true)
const whatsappNumber = '559492211681'
const whatsappLink = `https://wa.me/${whatsappNumber}`
const instagramLink = 'https://www.instagram.com/dr.edreymundoco/'
let revealObserver: IntersectionObserver | null = null
let scrollAnimationFrame: number | null = null
let removeScrollListener: (() => void) | null = null
let introTimer: number | null = null
let introUnlockTimer: number | null = null

const treatments = [
  {
    title: 'Avaliação e prevenção',
    description: 'Acompanhamento completo da saúde bucal para prevenir problemas e cuidar de dentes e gengivas ao longo do tempo',
    icon: SunMedium,
  },
  {
    title: 'Restaurações estéticas',
    description: 'Recuperação de forma, função e harmonia com materiais que se integram ao seu sorriso',
    icon: WandSparkles,
  },
  {
    title: 'Clareamento dental',
    description: 'Um plano supervisionado para devolver luminosidade ao sorriso com segurança e naturalidade',
    icon: Sparkles,
  },
  {
    title: 'Prótese dentária',
    description: 'Soluções planejadas para restaurar conforto, confiança e qualidade na mastigação',
    icon: ShieldCheck,
  },
  {
    title: 'Tratamento de canal',
    description: 'Cuidado criterioso para preservar o dente e aliviar o desconforto, respeitando cada etapa clínica',
    icon: Activity,
  },
  {
    title: 'Ortodontia e aparelhos',
    description: 'Planejamento individualizado para alinhar o sorriso, melhorar a função e acompanhar cada fase do tratamento ortodôntico',
    icon: Smile,
  },
  {
    title: 'Extrações',
    description: 'Avaliação responsável e procedimento conduzido com atenção ao conforto e à recuperação',
    icon: Check,
  },
]

const faqs = [
  {
    question: 'Como funciona a primeira avaliação?',
    answer:
      'A consulta começa com uma conversa sobre suas necessidades e expectativas. Depois da avaliação clínica, você recebe uma explicação clara sobre os possíveis caminhos de cuidado',
  },
  {
    question: 'Qual tratamento é o mais indicado para mim?',
    answer:
      'A indicação depende da avaliação individual. O objetivo é compreender sua saúde bucal, suas prioridades e construir um plano coerente com o seu momento',
  },
  {
    question: 'Posso tirar dúvidas antes de decidir?',
    answer:
      'Sim. Decisões bem informadas fazem parte do atendimento. Benefícios, etapas e cuidados são explicados antes do início de qualquer tratamento.',
  },
  {
    question: 'Como devo agir em uma urgência odontológica?',
    answer:
      'Dor intensa, trauma, sangramento persistente ou inchaço importante pedem avaliação rápida. Procure um serviço odontológico de urgência disponível na sua região.',
  },
]

function closeMenu() {
  menuOpen.value = false
}

function toggleTreatment(index: number) {
  activeTreatment.value = index
}

function finishIntro() {
  if (!showIntro.value) return
  showIntro.value = false
  document.documentElement.classList.add('cinematic-ready')
  introUnlockTimer = window.setTimeout(() => {
    document.body.classList.remove('intro-playing')
    introUnlockTimer = null
  }, 900)
  if (introTimer !== null) {
    window.clearTimeout(introTimer)
    introTimer = null
  }
}

function handleIntroKeydown(event: KeyboardEvent) {
  if (event.key === 'Escape') finishIntro()
}

onMounted(() => {
  const revealElements = document.querySelectorAll<HTMLElement>('[data-reveal], [data-cinematic-section]')
  const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches

  const updateScrollMotion = () => {
    const scrollableHeight = Math.max(document.documentElement.scrollHeight - window.innerHeight, 1)
    const progress = Math.min(window.scrollY / scrollableHeight, 1)
    const heroParallax = Math.min(window.scrollY * 0.075, 44)

    document.documentElement.style.setProperty('--page-progress', String(progress))
    document.documentElement.style.setProperty('--hero-parallax', `${heroParallax}px`)
    scrollAnimationFrame = null
  }

  const scheduleScrollMotion = () => {
    if (scrollAnimationFrame !== null) return
    scrollAnimationFrame = window.requestAnimationFrame(updateScrollMotion)
  }

  document.documentElement.classList.add('motion-ready')
  if (prefersReducedMotion) {
    showIntro.value = false
    document.documentElement.classList.add('cinematic-ready')
  } else {
    document.body.classList.add('intro-playing')
    window.addEventListener('keydown', handleIntroKeydown)
    introTimer = window.setTimeout(finishIntro, 1950)
  }
  updateScrollMotion()
  window.addEventListener('scroll', scheduleScrollMotion, { passive: true })
  removeScrollListener = () => window.removeEventListener('scroll', scheduleScrollMotion)

  if (prefersReducedMotion || !('IntersectionObserver' in window)) {
    revealElements.forEach((element) => {
      element.dataset.revealed = 'true'
    })
    return
  }

  revealObserver = new IntersectionObserver(
    (entries, observer) => {
      entries.forEach((entry) => {
        if (!entry.isIntersecting) return
        const element = entry.target as HTMLElement
        element.dataset.revealed = 'true'
        observer.unobserve(entry.target)
      })
    },
    { threshold: 0.14, rootMargin: '0px 0px -8% 0px' },
  )

  revealElements.forEach((element) => revealObserver?.observe(element))

})

onBeforeUnmount(() => {
  revealObserver?.disconnect()
  removeScrollListener?.()
  if (scrollAnimationFrame !== null) window.cancelAnimationFrame(scrollAnimationFrame)
  if (introTimer !== null) window.clearTimeout(introTimer)
  if (introUnlockTimer !== null) window.clearTimeout(introUnlockTimer)
  window.removeEventListener('keydown', handleIntroKeydown)
  document.body.classList.remove('intro-playing')
  document.documentElement.classList.remove('motion-ready', 'cinematic-ready')
})

function sendToWhatsApp(event: Event) {
  const form = event.currentTarget as HTMLFormElement
  const data = new FormData(form)
  const name = String(data.get('name') || '').trim()
  const careNeed = String(data.get('careNeed') || '').trim()
  const period = String(data.get('period') || '').trim()
  const details = String(data.get('details') || '').trim()
  const message = [
    'Olá! Gostaria de solicitar um atendimento com o Dr. Edrey Mundoco.',
    '',
    'Profissional desejado: Dr. Edrey Mundoco',
    `Nome: ${name}`,
    `O que preciso: ${careNeed}`,
    `Melhor período: ${period}`,
    `Detalhes: ${details}`,
  ].join('\n')
  const whatsappUrl = `${whatsappLink}?text=${encodeURIComponent(message)}`

  formStatus.value = 'Abrindo sua conversa no WhatsApp…'
  const whatsappWindow = window.open(whatsappUrl, '_blank')
  if (whatsappWindow) {
    whatsappWindow.opener = null
  } else {
    window.location.href = whatsappUrl
  }
}
</script>

<template>
  <Transition name="cinematic-intro" appear>
    <div v-if="showIntro" class="cinematic-intro" aria-hidden="true" @click="finishIntro">
      <div class="intro-light"></div>
      <div class="intro-content">
        <p class="intro-overline">Uma experiência de cuidado</p>
        <img src="/logo-edrey-mark.png" alt="" />
        <p class="intro-name">Dr. Edrey <em>Mundoco</em></p>
        <div class="intro-meta">
          <span>01</span><i></i><span>03</span>
          <small>Odontologia · precisão · presença</small>
        </div>
      </div>
    </div>
  </Transition>

  <div class="film-grain" aria-hidden="true"></div>
  <a class="skip-link" href="#conteudo">Pular para o conteúdo</a>

  <header class="site-header" @keydown.esc="closeMenu">
    <span class="scroll-progress" aria-hidden="true"></span>
    <div class="container header-inner">
      <a class="brand" href="#inicio" aria-label="Dr. Edrey Mundoco — início" @click="closeMenu">
        <img class="brand-logo" src="/logo-edrey-transparent.png" alt="Dr. Edrey Mundoco, cirurgião-dentista" />
      </a>

      <button
        class="menu-toggle"
        type="button"
        :aria-expanded="menuOpen"
        aria-controls="main-navigation"
        :aria-label="menuOpen ? 'Fechar menu' : 'Abrir menu'"
        @click="menuOpen = !menuOpen"
      >
        <X v-if="menuOpen" :size="22" aria-hidden="true" />
        <Menu v-else :size="22" aria-hidden="true" />
      </button>

      <nav id="main-navigation" class="main-nav" :class="{ 'is-open': menuOpen }" aria-label="Principal">
        <a href="#inicio" @click="closeMenu">Início</a>
        <a href="#tratamentos" @click="closeMenu">Tratamentos</a>
        <a href="#sobre" @click="closeMenu">Quem sou</a>
        <a href="#duvidas" @click="closeMenu">Dúvidas</a>
        <a class="button button-small" href="#agendamento" @click="closeMenu">Agendar avaliação</a>
      </nav>
    </div>
  </header>

  <main id="conteudo">
    <section id="inicio" class="hero" aria-labelledby="hero-title">
      <div class="hero-aurora" aria-hidden="true"></div>
      <div class="hero-light-beam" aria-hidden="true"></div>
      <div class="hero-orbit hero-orbit-one" aria-hidden="true"></div>
      <div class="hero-orbit hero-orbit-two" aria-hidden="true"></div>
      <div class="container hero-grid">
        <div class="hero-copy" data-reveal="left">
          <p class="eyebrow"><span></span> Odontologia com cuidado e precisão</p>
          <h1 id="hero-title">
            <span class="headline-line"><span>Seu sorriso merece</span></span>
            <span class="headline-line"><span>um cuidado à altura</span></span>
            <span class="headline-line"><span><em>da sua história</em></span></span>
          </h1>
          <p class="hero-lead">
            Um atendimento próximo, transparente e planejado para você se sentir seguro em cada decisão
          </p>
          <div class="hero-actions">
            <a class="button" href="#agendamento">
              <CalendarDays :size="18" aria-hidden="true" />
              Agendar avaliação
            </a>
            <a class="text-link" href="#tratamentos">
              Conhecer tratamentos
              <ArrowDownRight :size="18" aria-hidden="true" />
            </a>
          </div>
          <div class="hero-note" aria-label="Diferenciais do atendimento">
            <span>Escuta atenta</span>
            <span>Plano individual</span>
            <span>Acompanhamento</span>
          </div>
        </div>

        <div class="hero-portrait-wrap" data-reveal="right">
          <div class="portrait-glow" aria-hidden="true"></div>
          <span class="portrait-spark portrait-spark-one" aria-hidden="true"></span>
          <span class="portrait-spark portrait-spark-two" aria-hidden="true"></span>
          <div class="hero-portrait">
            <img
              src="/edrey-retrato.png"
              alt="Dr. Edrey Mundoco em retrato profissional"
              width="1080"
              height="1610"
              decoding="async"
            />
          </div>
          <div class="portrait-caption">
            <img class="caption-logo-mark" src="/logo-edrey-mark.png" alt="" aria-hidden="true" />
            <p><strong>Dr. Edrey Mundoco</strong><small>Cirurgião-dentista</small></p>
          </div>
        </div>
        <a class="hero-scroll-cue" href="#tratamentos" aria-label="Rolar para conhecer os tratamentos">
          <span aria-hidden="true"></span>
          Role para descobrir
        </a>
      </div>
    </section>

    <section class="trust-strip" aria-label="Compromissos do atendimento">
      <div class="container trust-grid" data-reveal>
        <p><span>01</span> Atendimento individualizado</p>
        <p><span>02</span> Planejamento transparente</p>
        <p><span>03</span> Cuidado em cada etapa</p>
      </div>
    </section>

    <section id="tratamentos" class="section treatments-section" aria-labelledby="treatments-title" data-cinematic-section>
      <div class="container">
        <div class="section-heading" data-reveal>
          <div>
            <p class="eyebrow"><span></span> Tratamentos</p>
            <h2 id="treatments-title">Cuidado completo para a saúde e a <em>estética do sorriso</em></h2>
          </div>
          <p>
            Da prevenção à reabilitação, cada cuidado é pensado a partir das necessidades do seu sorriso, com planejamento individual e acompanhamento próximo
          </p>
        </div>

        <div class="treatments-grid">
          <article
            v-for="(treatment, index) in treatments"
            :key="treatment.title"
            class="treatment-card"
            :class="{ 'is-active': activeTreatment === index }"
            data-reveal
          >
            <button
              type="button"
              :aria-expanded="activeTreatment === index"
              :aria-controls="`treatment-${index}`"
              @click="toggleTreatment(index)"
            >
              <span class="treatment-icon"><component :is="treatment.icon" :size="22" aria-hidden="true" /></span>
              <span class="treatment-number">0{{ index + 1 }}</span>
              <strong>{{ treatment.title }}</strong>
              <ArrowRight class="treatment-arrow" :size="20" aria-hidden="true" />
            </button>
            <div
              :id="`treatment-${index}`"
              class="treatment-detail"
              :class="{ 'is-open': activeTreatment === index }"
              :aria-hidden="activeTreatment !== index"
            >
              <div class="treatment-detail-inner">
                <p>{{ treatment.description }}</p>
                <a href="#agendamento" :tabindex="activeTreatment === index ? 0 : -1">
                  Conversar sobre este cuidado <ArrowRight :size="15" aria-hidden="true" />
                </a>
              </div>
            </div>
          </article>
        </div>

        <div class="section-cta" data-reveal>
          <p><strong>Não sabe por onde começar?</strong> <span>A avaliação é o primeiro passo para um plano realmente seu</span></p>
          <a class="button button-outline" href="#agendamento">Quero cuidar do meu sorriso</a>
        </div>
      </div>
    </section>

    <section id="sobre" class="section about-section" aria-labelledby="about-title" data-cinematic-section>
      <div class="container about-grid">
        <div class="about-visual" data-reveal="left">
          <div class="about-image">
            <img
              src="/edrey-retrato.png"
              alt="Retrato profissional do Dr. Edrey Mundoco"
              width="1080"
              height="1610"
              loading="lazy"
              decoding="async"
            />
          </div>
          <p class="image-label">
            <img class="caption-logo-mark" src="/logo-edrey-mark.png" alt="" aria-hidden="true" loading="lazy" />
            Presença, técnica e atenção aos detalhes
          </p>
        </div>

        <div class="about-copy" data-reveal="right">
          <p class="eyebrow"><span></span> Quem sou</p>
          <h2 id="about-title">Sou o Dr. Edrey Mundoco e acredito em um cuidado <em>próximo e bem explicado</em></h2>
          <p class="about-lead">
            Sou cirurgião-dentista, formado pela Faculdade Integrada Carajás (FIC), e meu atendimento começa pela escuta. Cuido de diferentes necessidades de saúde e estética bucal, sempre com avaliação individual, orientação transparente e um plano claro para cada etapa
          </p>
          <div class="professional-register">
            <ShieldCheck :size="24" aria-hidden="true" />
            <p><small>Registro profissional</small><strong>CRO/PA 13457</strong></p>
          </div>
          <div class="about-points">
            <div>
              <HeartHandshake :size="22" aria-hidden="true" />
              <p><strong>Acolhimento de verdade</strong><span>Espaço para falar sobre expectativas, dúvidas e receios</span></p>
            </div>
            <div>
              <ClipboardCheck :size="22" aria-hidden="true" />
              <p><strong>Decisões bem explicadas</strong><span>Orientação simples para você participar do próprio tratamento</span></p>
            </div>
            <div>
              <ShieldCheck :size="22" aria-hidden="true" />
              <p><strong>Cuidado responsável</strong><span>Planejamento atento à função, à saúde e à naturalidade do sorriso</span></p>
            </div>
          </div>
          <a class="text-link text-link-gold" href="#agendamento">
            Agendar uma conversa <ArrowRight :size="18" aria-hidden="true" />
          </a>
        </div>
      </div>
    </section>

    <section class="section journey-section" aria-labelledby="journey-title" data-cinematic-section>
      <div class="container">
        <div class="journey-heading" data-reveal>
          <p class="eyebrow"><span></span> Sua jornada de cuidado</p>
          <h2 id="journey-title">Um caminho simples, <em>construído com você</em></h2>
        </div>
        <ol class="journey-list" data-reveal>
          <li>
            <span>01</span>
            <div><strong>Avaliação e escuta</strong><p>Entendemos seu momento, suas necessidades e o que você busca para o sorriso</p></div>
          </li>
          <li>
            <span>02</span>
            <div><strong>Planejamento claro</strong><p>As possibilidades são apresentadas com linguagem simples, etapas e cuidados envolvidos</p></div>
          </li>
          <li>
            <span>03</span>
            <div><strong>Cuidado e acompanhamento</strong><p>O tratamento segue o plano definido, com atenção ao conforto e à evolução clínica</p></div>
          </li>
        </ol>
      </div>
    </section>

    <section id="duvidas" class="section faq-section" aria-labelledby="faq-title" data-cinematic-section>
      <div class="container faq-grid">
        <div class="faq-intro" data-reveal="left">
          <p class="eyebrow"><span></span> Dúvidas frequentes</p>
          <h2 id="faq-title">Informação também faz parte do <em>cuidado</em></h2>
          <p>Respostas iniciais para você chegar à avaliação com mais tranquilidade</p>
          <a class="text-link text-link-gold" href="#agendamento">
            Quero fazer uma pergunta <ArrowRight :size="18" aria-hidden="true" />
          </a>
        </div>
        <div class="faq-list" data-reveal="right">
          <details v-for="(faq, index) in faqs" :key="faq.question" :open="index === 0">
            <summary><span>0{{ index + 1 }}</span>{{ faq.question }}<i aria-hidden="true"></i></summary>
            <p>{{ faq.answer }}</p>
          </details>
        </div>
      </div>
    </section>

    <section id="agendamento" class="section booking-section" aria-labelledby="booking-title" data-cinematic-section>
      <div class="booking-orbit" aria-hidden="true"></div>
      <div class="container booking-grid">
        <div class="booking-copy" data-reveal="left">
          <p class="eyebrow"><span></span> Atendimento pelo WhatsApp</p>
          <h2 id="booking-title">Conte o que você precisa e fale <em>com a recepção</em></h2>
          <p>
            Conte o que você busca para sua saúde bucal. A recepção receberá suas informações e poderá orientar o primeiro passo para o atendimento com o Dr. Edrey
          </p>
          <div class="booking-assurance">
            <MessageCircle :size="22" aria-hidden="true" />
            <span>
              <strong>Recepção da clínica pelo WhatsApp</strong>
              <a :href="whatsappLink" target="_blank" rel="noopener noreferrer">+55 94 9221-1681</a>
            </span>
          </div>
        </div>

        <form class="booking-form" data-reveal="right" @submit.prevent="sendToWhatsApp">
          <label for="name">Como podemos chamar você?</label>
          <input id="name" name="name" type="text" autocomplete="name" placeholder="Seu nome" required />

          <label for="care-need">Como podemos ajudar?</label>
          <select id="care-need" name="careNeed" required>
            <option value="" disabled selected>Selecione uma opção</option>
            <option value="Quero fazer uma avaliação ou limpeza preventiva">Avaliação ou limpeza preventiva</option>
            <option value="Quero cuidar da estética do meu sorriso">Clareamento ou restauração estética</option>
            <option value="Preciso tratar dor ou desconforto">Dor, desconforto ou tratamento de canal</option>
            <option value="Quero avaliar prótese ou reabilitação dentária">Prótese ou reabilitação dentária</option>
            <option value="Quero iniciar, continuar ou retomar um tratamento ortodôntico">Ortodontia e aparelhos</option>
            <option value="Quero conversar sobre outra necessidade odontológica">Outra necessidade odontológica</option>
          </select>

          <label for="period">Qual período costuma ser melhor?</label>
          <select id="period" name="period" required>
            <option value="" disabled selected>Selecione um período</option>
            <option value="da manhã">Manhã</option>
            <option value="da tarde">Tarde</option>
            <option value="da noite">Noite</option>
          </select>

          <label for="details">Conte um pouco mais sobre o que você precisa</label>
          <textarea
            id="details"
            name="details"
            rows="4"
            placeholder="Ex.: quero fazer uma avaliação e entender qual cuidado é mais indicado para mim…"
            required
          ></textarea>

          <button class="button form-submit" type="submit">
            Enviar informações no WhatsApp <MessageCircle :size="18" aria-hidden="true" />
          </button>
          <p v-if="formStatus" class="form-status" role="status" aria-live="polite">{{ formStatus }}</p>
          <p class="form-note">
            O WhatsApp abrirá com a mensagem preenchida. Revise as informações e toque em enviar. Em caso de urgência odontológica, procure atendimento imediato.
          </p>
        </form>
      </div>
    </section>
  </main>

  <footer class="site-footer">
    <div class="container footer-top" data-reveal>
      <a class="brand footer-brand" href="#inicio" aria-label="Voltar ao início">
        <img class="brand-logo" src="/logo-edrey-transparent.png" alt="Dr. Edrey Mundoco, cirurgião-dentista" loading="lazy" />
      </a>
      <p>Cuidado que começa na escuta e se revela em cada sorriso · CRO/PA 13457</p>
      <div class="footer-actions">
        <a class="button button-small" :href="whatsappLink" target="_blank" rel="noopener noreferrer">
          <MessageCircle :size="16" aria-hidden="true" />
          WhatsApp
        </a>
        <a class="button button-small instagram-button" :href="instagramLink" target="_blank" rel="noopener noreferrer">
          <Camera :size="16" aria-hidden="true" />
          Instagram
        </a>
      </div>
    </div>
    <div class="container footer-bottom">
      <p>© 2026 Dr. Edrey Mundoco.</p>
      <p>Conteúdo informativo. Avaliação profissional é indispensável.</p>
      <a href="#inicio">Voltar ao topo ↑</a>
    </div>
  </footer>

  <a
    class="whatsapp-float"
    :href="whatsappLink"
    target="_blank"
    rel="noopener noreferrer"
    aria-label="Solicitar à recepção da clínica um atendimento com o Dr. Edrey"
  >
    <MessageCircle :size="21" aria-hidden="true" />
    <span>WhatsApp</span>
  </a>
</template>
