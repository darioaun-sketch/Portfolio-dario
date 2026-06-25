<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Darío — Editor de Video</title>
  <meta name="description" content="Editor de Reels, Shorts y TikToks. Corte rápido, retención y storytelling visual." />
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;0,700;1,300;1,400;1,600&family=Inter:wght@300;400;500;600&display=swap" rel="stylesheet" />
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --cream:   #f3efe6;
      --paper:   #ebe5d8;
      --warm:    #e2d9cb;
      --mid:     #96897a;
      --muted:   #b5a898;
      --dark:    #28201a;
      --ink:     #3a3028;
      --accent:  #a05c28;
      --accent2: #c8844a;
      --glow:    rgba(160, 92, 40, 0.13);
      --border:  rgba(170, 158, 138, 0.4);
    }

    html { scroll-behavior: smooth; }

    body {
      background: var(--cream);
      color: var(--ink);
      font-family: 'Inter', sans-serif;
      font-weight: 300;
      line-height: 1.6;
      overflow-x: hidden;
    }

    /* ── LOADER ── */
    #loader {
      position: fixed; inset: 0;
      background: var(--dark);
      z-index: 9999;
      display: flex; flex-direction: column;
      align-items: center; justify-content: center; gap: 28px;
      transition: opacity 1.4s ease, visibility 1.4s ease;
    }
    #loader.done { opacity: 0; visibility: hidden; }
    .loader-logo {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: 2.2rem;
      letter-spacing: 0.36em;
      color: var(--cream);
      opacity: 0;
      animation: loaderIn 1s 0.3s forwards;
    }
    .loader-logo span { color: var(--accent2); }
    .loader-line {
      width: 0;
      height: 1px;
      background: var(--accent2);
      animation: lineGrow 1s 0.6s cubic-bezier(0.22,1,0.36,1) forwards;
      box-shadow: 0 0 12px rgba(200,132,74,0.6);
    }
    @keyframes loaderIn {
      from { opacity: 0; transform: translateY(10px); }
      to   { opacity: 1; transform: translateY(0); }
    }
    @keyframes lineGrow { from { width: 0; } to { width: 80px; } }

    /* ── CURSOR GLOW ── */
    .cursor-glow {
      position: fixed;
      width: 440px; height: 440px;
      border-radius: 50%;
      background: radial-gradient(circle, rgba(160,92,40,0.08) 0%, transparent 68%);
      pointer-events: none;
      transform: translate(-50%, -50%);
      z-index: 1;
    }

    /* ── NAV ── */
    nav {
      position: fixed; top: 0; left: 0; right: 0; z-index: 200;
      display: flex; justify-content: space-between; align-items: center;
      padding: 0 56px;
      height: 68px;
      background: rgba(243,239,230,0.82);
      backdrop-filter: blur(20px);
      border-bottom: 1px solid var(--border);
      transition: background 0.6s ease, box-shadow 0.6s ease;
    }
    nav.scrolled {
      background: rgba(243,239,230,0.97);
      box-shadow: 0 2px 40px rgba(160,92,40,0.07);
    }
    .nav-logo {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 400;
      font-size: 1.25rem;
      letter-spacing: 0.2em;
      color: var(--dark);
      text-decoration: none;
      transition: opacity 0.3s;
    }
    .nav-logo:hover { opacity: 0.55; }
    .nav-logo span { color: var(--accent); }
    .nav-center {
      position: absolute; left: 50%; transform: translateX(-50%);
      font-family: 'Inter', sans-serif;
      font-size: 0.62rem;
      letter-spacing: 0.22em;
      text-transform: uppercase;
      color: var(--muted);
    }
    .nav-links { display: flex; gap: 36px; list-style: none; align-items: center; }
    .nav-links a {
      font-family: 'Inter', sans-serif;
      color: var(--mid);
      text-decoration: none;
      font-size: 0.7rem;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      transition: color 0.3s ease;
      position: relative;
    }
    .nav-links a::after {
      content: '';
      position: absolute;
      bottom: -2px; left: 0; right: 0;
      height: 1px;
      background: var(--accent);
      transform: scaleX(0); transform-origin: left;
      transition: transform 0.35s ease;
    }
    .nav-links a:hover { color: var(--dark); }
    .nav-links a:hover::after { transform: scaleX(1); }
    .nav-cta {
      background: var(--accent) !important;
      color: var(--cream) !important;
      padding: 8px 20px !important;
      font-weight: 500 !important;
      transition: box-shadow 0.4s ease, transform 0.3s ease !important;
    }
    .nav-cta::after { display: none !important; }
    .nav-cta:hover {
      box-shadow: 0 0 24px rgba(160,92,40,0.45) !important;
      transform: translateY(-1px) !important;
      color: var(--cream) !important;
    }

    /* ── HERO ── */
    .hero {
      min-height: 100vh;
      display: grid;
      grid-template-columns: 1fr 1fr;
      position: relative;
      overflow: hidden;
    }

    /* left panel */
    .hero-left {
      background: var(--dark);
      padding: 140px 64px 80px;
      display: flex; flex-direction: column; justify-content: flex-end;
      position: relative; overflow: hidden;
    }
    .hero-left-blob {
      position: absolute;
      width: 500px; height: 500px;
      border-radius: 50%;
      top: -100px; left: -100px;
      background: radial-gradient(circle, rgba(160,92,40,0.18) 0%, transparent 65%);
      filter: blur(80px);
      animation: blobFloat 9s ease-in-out infinite;
      pointer-events: none;
    }
    .hero-left-blob-2 {
      position: absolute;
      width: 300px; height: 300px;
      border-radius: 50%;
      bottom: 60px; right: -60px;
      background: radial-gradient(circle, rgba(160,92,40,0.12) 0%, transparent 65%);
      filter: blur(60px);
      animation: blobFloat 13s ease-in-out infinite reverse;
      pointer-events: none;
    }
    @keyframes blobFloat {
      0%,100% { transform: translateY(0) scale(1); }
      50%      { transform: translateY(-20px) scale(1.03); }
    }

    .hero-eyebrow {
      display: inline-flex; align-items: center; gap: 10px;
      font-family: 'Inter', sans-serif;
      font-size: 0.62rem; letter-spacing: 0.24em; text-transform: uppercase;
      color: var(--accent2); margin-bottom: 32px; position: relative;
    }
    .hero-eyebrow::before {
      content: ''; display: block; width: 20px; height: 1px;
      background: var(--accent2);
    }
    .hero-name {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: clamp(5.5rem, 9vw, 9rem);
      line-height: 0.88;
      letter-spacing: 0.01em;
      color: var(--cream);
      margin-bottom: 28px;
      position: relative;
    }
    .hero-role {
      font-family: 'Cormorant Garamond', serif;
      font-style: italic;
      font-size: clamp(1.1rem, 1.8vw, 1.5rem);
      color: rgba(243,239,230,0.45);
      margin-bottom: 40px;
      position: relative;
    }
    .hero-desc {
      font-family: 'Inter', sans-serif;
      color: rgba(243,239,230,0.5);
      font-size: 0.84rem;
      line-height: 1.9;
      max-width: 340px;
      margin-bottom: 52px;
      position: relative;
    }
    .hero-actions { display: flex; gap: 12px; flex-wrap: wrap; position: relative; }
    .btn-primary {
      display: inline-flex; align-items: center; gap: 10px;
      background: var(--accent);
      color: var(--cream);
      font-family: 'Inter', sans-serif; font-weight: 500;
      font-size: 0.68rem; letter-spacing: 0.14em; text-transform: uppercase;
      text-decoration: none; padding: 14px 28px;
      transition: box-shadow 0.45s ease, transform 0.3s ease;
    }
    .btn-primary:hover {
      box-shadow: 0 0 32px rgba(160,92,40,0.55), 0 6px 20px rgba(160,92,40,0.22);
      transform: translateY(-2px);
    }
    .btn-ghost-light {
      display: inline-flex; align-items: center; gap: 10px;
      border: 1px solid rgba(243,239,230,0.18);
      color: rgba(243,239,230,0.45);
      font-family: 'Inter', sans-serif;
      font-size: 0.68rem; letter-spacing: 0.12em; text-transform: uppercase;
      text-decoration: none; padding: 14px 28px;
      transition: border-color 0.4s ease, color 0.4s ease;
    }
    .btn-ghost-light:hover {
      border-color: rgba(243,239,230,0.5);
      color: var(--cream);
    }

    /* right panel */
    .hero-right {
      background: var(--cream);
      padding: 140px 64px 80px;
      display: flex; flex-direction: column; justify-content: flex-end;
      border-left: 1px solid var(--border);
      position: relative;
    }

    /* large background number */
    .hero-bg-num {
      position: absolute;
      top: 60px; right: 40px;
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: 18rem;
      line-height: 1;
      color: var(--paper);
      pointer-events: none;
      user-select: none;
      letter-spacing: -0.04em;
    }

    .stat-row {
      display: flex; align-items: flex-end; gap: 0;
      border-bottom: 1px solid var(--border);
      padding: 28px 0;
      position: relative;
      transition: padding-left 0.4s ease;
    }
    .stat-row:first-child { border-top: 1px solid var(--border); }
    .stat-row:hover { padding-left: 10px; }
    .stat-num {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300; font-size: 3.2rem;
      color: var(--dark); line-height: 1;
      min-width: 100px;
    }
    .stat-num span { color: var(--accent); font-size: 1.8rem; }
    .stat-info { padding-bottom: 4px; }
    .stat-label {
      font-family: 'Inter', sans-serif;
      font-size: 0.65rem; letter-spacing: 0.16em;
      text-transform: uppercase; color: var(--mid);
      display: block; margin-bottom: 3px;
    }
    .stat-desc { font-family: 'Inter', sans-serif; font-size: 0.76rem; color: var(--muted); }

    /* availability badge */
    .avail-badge {
      display: inline-flex; align-items: center; gap: 8px;
      margin-top: 36px;
      font-family: 'Inter', sans-serif;
      font-size: 0.68rem; letter-spacing: 0.14em; text-transform: uppercase;
      color: var(--mid);
    }
    .avail-dot {
      width: 7px; height: 7px; border-radius: 50%;
      background: #7ab87a;
      box-shadow: 0 0 8px rgba(122,184,122,0.7);
      animation: pulse 2.5s ease-in-out infinite;
    }
    @keyframes pulse {
      0%,100% { box-shadow: 0 0 8px rgba(122,184,122,0.7); }
      50%      { box-shadow: 0 0 16px rgba(122,184,122,0.9); }
    }

    /* ── MARQUEE ── */
    .marquee-wrap {
      overflow: hidden;
      border-top: 1px solid var(--border);
      border-bottom: 1px solid var(--border);
      padding: 13px 0;
      background: var(--dark);
      position: relative;
    }
    .marquee-track {
      display: inline-flex;
      animation: marquee 28s linear infinite;
    }
    .marquee-track span {
      font-family: 'Cormorant Garamond', serif;
      font-style: italic; font-size: 0.95rem;
      letter-spacing: 0.1em;
      color: rgba(243,239,230,0.28);
      padding: 0 28px;
    }
    .marquee-track span.dot { font-style: normal; color: var(--accent2); font-size: 0.6rem; padding: 0 4px; vertical-align: middle; }
    @keyframes marquee {
      from { transform: translateX(0); }
      to   { transform: translateX(-50%); }
    }

    /* ── SECTION SHARED ── */
    .section { padding: 100px 56px; border-top: 1px solid var(--border); position: relative; }
    .section-eyebrow {
      font-family: 'Inter', sans-serif;
      font-size: 0.6rem; letter-spacing: 0.28em; text-transform: uppercase;
      color: var(--accent); margin-bottom: 12px;
    }
    .section-title {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: clamp(2.2rem, 3.6vw, 3.1rem);
      line-height: 1.1; color: var(--dark); margin-bottom: 56px;
    }
    .section-title em { font-style: italic; }

    /* ── INTRO BAND ── */
    #intro {
      background: var(--paper);
      padding: 72px 56px;
      display: grid;
      grid-template-columns: 1fr 2px 1fr 2px 1fr;
      gap: 0;
      align-items: center;
    }
    .intro-divider { background: var(--border); height: 80px; }
    .intro-block { padding: 0 48px; }
    .intro-block:first-child { padding-left: 0; }
    .intro-block:last-child { padding-right: 0; }
    .intro-label {
      font-family: 'Inter', sans-serif;
      font-size: 0.58rem; letter-spacing: 0.28em; text-transform: uppercase;
      color: var(--accent); margin-bottom: 10px;
    }
    .intro-value {
      font-family: 'Cormorant Garamond', serif;
      font-size: 1.15rem; font-weight: 400;
      color: var(--dark); line-height: 1.5;
    }

    /* ── SERVICES ── */
    #services { background: var(--cream); }
    .services-grid {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 1px; background: var(--border);
    }
    .service-cell {
      background: var(--cream);
      padding: 36px 28px;
      position: relative; overflow: hidden;
      transition: background 0.5s ease;
      cursor: default;
    }
    .service-cell::before {
      content: '';
      position: absolute; inset: 0;
      background: radial-gradient(circle at 20% 90%, var(--glow), transparent 58%);
      opacity: 0; transition: opacity 0.6s ease;
    }
    .service-cell:hover { background: var(--paper); }
    .service-cell:hover::before { opacity: 1; }
    .service-num {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300; font-size: 2.4rem;
      color: var(--border); line-height: 1;
      margin-bottom: 18px; position: relative;
      transition: color 0.4s ease;
    }
    .service-cell:hover .service-num { color: rgba(160,92,40,0.18); }
    .service-name {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 600; font-size: 1.08rem;
      color: var(--dark); margin-bottom: 10px; position: relative;
    }
    .service-desc {
      font-family: 'Inter', sans-serif;
      font-size: 0.75rem; color: var(--mid); line-height: 1.72; position: relative;
    }

    /* ── PORTFOLIO ── */
    #portfolio {
      background: var(--dark);
      padding-bottom: 0;
    }
    #portfolio .section-eyebrow { color: var(--accent2); }
    #portfolio .section-title { color: var(--cream); }
    .portfolio-intro {
      display: grid; grid-template-columns: 1fr 1fr;
      gap: 56px; margin-bottom: 64px; align-items: end;
    }
    .portfolio-note {
      font-family: 'Inter', sans-serif;
      font-size: 0.82rem; color: rgba(243,239,230,0.38); line-height: 1.9;
      padding-top: 8px; border-left: 1px solid rgba(243,239,230,0.1); padding-left: 28px;
    }
    .portfolio-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 2px;
    }
    .video-card {
      background: #1e1710; overflow: hidden; position: relative;
      transition: transform 0.65s cubic-bezier(0.22,1,0.36,1),
                  box-shadow 0.65s cubic-bezier(0.22,1,0.36,1);
    }
    .video-card::after {
      content: ''; position: absolute; inset: 0;
      box-shadow: inset 0 0 0 1px transparent;
      transition: box-shadow 0.5s ease;
      pointer-events: none; z-index: 2;
    }
    .video-card:hover {
      transform: translateY(-6px);
      box-shadow: 0 24px 70px rgba(160,92,40,0.28), 0 6px 24px rgba(160,92,40,0.14);
    }
    .video-card:hover::after { box-shadow: inset 0 0 0 1px rgba(200,132,74,0.4); }
    .video-shell {
      position: relative; width: 100%; padding-top: 177.78%;
      background: #1a1410;
    }
    .video-shell iframe {
      position: absolute; inset: 0; width: 100%; height: 100%; border: none;
    }
    .tiktok-fallback {
      position: absolute; inset: 0;
      display: flex; flex-direction: column;
      align-items: center; justify-content: center; gap: 24px;
      background: #1a1410; padding: 36px;
    }
    .tiktok-fallback p {
      font-family: 'Cormorant Garamond', serif;
      font-style: italic; font-size: 1.05rem;
      color: rgba(243,239,230,0.35); text-align: center; line-height: 1.7;
    }
    .tiktok-fallback a {
      display: inline-flex; align-items: center; gap: 8px;
      background: var(--accent); color: var(--cream);
      font-family: 'Inter', sans-serif; font-weight: 500;
      font-size: 0.68rem; letter-spacing: 0.12em; text-transform: uppercase;
      text-decoration: none; padding: 12px 24px;
      transition: box-shadow 0.4s ease, transform 0.3s ease;
    }
    .tiktok-fallback a:hover {
      box-shadow: 0 0 28px rgba(160,92,40,0.55); transform: translateY(-1px);
    }
    .video-meta {
      padding: 22px 24px 28px;
      border-top: 1px solid rgba(243,239,230,0.07);
      background: rgba(243,239,230,0.03);
      transition: background 0.4s ease;
    }
    .video-card:hover .video-meta { background: rgba(243,239,230,0.06); }
    .video-tag {
      font-family: 'Inter', sans-serif;
      font-size: 0.58rem; letter-spacing: 0.22em; text-transform: uppercase;
      color: var(--accent2); margin-bottom: 7px;
    }
    .video-title {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 600; font-size: 1.1rem;
      color: rgba(243,239,230,0.85); margin-bottom: 4px;
    }
    .video-desc {
      font-family: 'Inter', sans-serif;
      font-size: 0.73rem; color: rgba(243,239,230,0.3);
    }

    /* ── PROCESS ── */
    #process { background: var(--dark); }
    #process .section-eyebrow { color: var(--accent2); }
    #process .section-title { color: var(--cream); }
    .process-grid {
      display: grid; grid-template-columns: repeat(4, 1fr);
      gap: 1px; background: rgba(243,239,230,0.08);
    }
    .process-step {
      padding: 40px 28px;
      background: var(--dark);
      position: relative; overflow: hidden;
      transition: background 0.45s ease;
    }
    .process-step::before {
      content: '';
      position: absolute; bottom: 0; left: 0; right: 0;
      height: 1px;
      background: var(--accent);
      transform: scaleX(0); transform-origin: left;
      transition: transform 0.55s cubic-bezier(0.22,1,0.36,1);
      box-shadow: 0 0 12px rgba(160,92,40,0.55);
    }
    .process-step:hover { background: rgba(243,239,230,0.03); }
    .process-step:hover::before { transform: scaleX(1); }
    .process-step-num {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300; font-size: 3rem;
      color: rgba(243,239,230,0.08); margin-bottom: 24px; line-height: 1;
      transition: color 0.4s ease;
    }
    .process-step:hover .process-step-num { color: rgba(160,92,40,0.25); }
    .process-step-title {
      font-family: 'Cormorant Garamond', serif;
      font-size: 1.1rem; font-weight: 600;
      color: rgba(243,239,230,0.85); margin-bottom: 12px;
    }
    .process-step-desc {
      font-family: 'Inter', sans-serif;
      font-size: 0.75rem; color: rgba(243,239,230,0.35); line-height: 1.75;
    }

    /* ── PRICING ── */
    #pricing { background: var(--cream); }
    .pricing-intro {
      display: grid; grid-template-columns: 1fr 1fr;
      gap: 56px; margin-bottom: 52px; align-items: end;
    }
    .pricing-note {
      font-family: 'Inter', sans-serif;
      font-size: 0.8rem; color: var(--mid); line-height: 1.9;
      border-left: 1px solid var(--border); padding-left: 24px;
    }
    .pricing-grid {
      display: grid; grid-template-columns: repeat(3, 1fr);
      gap: 2px; background: var(--border); margin-bottom: 2px;
    }
    .plan-card {
      background: var(--cream); padding: 44px 34px;
      position: relative; overflow: hidden;
      display: flex; flex-direction: column;
      transition: transform 0.55s cubic-bezier(0.22,1,0.36,1), box-shadow 0.55s ease;
    }
    .plan-card::after {
      content: '';
      position: absolute; bottom: -120px; right: -120px;
      width: 240px; height: 240px; border-radius: 50%;
      background: radial-gradient(circle, var(--glow), transparent 70%);
      opacity: 0; transition: opacity 0.6s ease, transform 0.6s ease;
      transform: scale(0.5);
    }
    .plan-card:hover { transform: translateY(-4px); box-shadow: 0 14px 55px rgba(160,92,40,0.11); }
    .plan-card:hover::after { opacity: 1; transform: scale(1); }

    /* featured plan: dark background */
    .plan-card.featured {
      background: var(--dark);
    }
    .plan-card.featured .plan-name { color: rgba(243,239,230,0.4); }
    .plan-card.featured .plan-price { color: var(--cream); }
    .plan-card.featured .plan-price sup { color: var(--accent2); }
    .plan-card.featured .plan-period { color: rgba(243,239,230,0.3); }
    .plan-card.featured .plan-divider { background: rgba(243,239,230,0.1); }
    .plan-card.featured .plan-features li { color: rgba(243,239,230,0.35); }
    .plan-card.featured .plan-features li.strong { color: rgba(243,239,230,0.85); }
    .plan-card.featured .plan-features li::before { color: var(--accent2); }
    .plan-card.featured .plan-cta {
      background: var(--accent);
      color: var(--cream);
      box-shadow: 0 0 0 rgba(160,92,40,0);
    }
    .plan-card.featured .plan-cta:hover {
      box-shadow: 0 0 28px rgba(160,92,40,0.55);
    }

    .plan-badge {
      position: absolute; top: 20px; right: 20px;
      background: var(--accent2); color: var(--dark);
      font-family: 'Inter', sans-serif; font-weight: 600;
      font-size: 0.52rem; letter-spacing: 0.16em; text-transform: uppercase;
      padding: 4px 10px;
    }
    .plan-name {
      font-family: 'Inter', sans-serif;
      font-size: 0.6rem; letter-spacing: 0.26em; text-transform: uppercase;
      color: var(--mid); margin-bottom: 20px;
    }
    .plan-price {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300; font-size: 4.4rem;
      line-height: 1; color: var(--dark); margin-bottom: 4px; position: relative;
    }
    .plan-price sup { font-size: 1.4rem; vertical-align: super; color: var(--muted); }
    .plan-period { font-family: 'Inter', sans-serif; font-size: 0.68rem; color: var(--mid); margin-bottom: 28px; }
    .plan-divider { height: 1px; background: var(--border); margin-bottom: 24px; }
    .plan-features {
      list-style: none; display: flex; flex-direction: column; gap: 11px;
      position: relative; flex: 1;
    }
    .plan-features li {
      font-family: 'Inter', sans-serif; font-size: 0.76rem; color: var(--mid);
      display: flex; align-items: flex-start; gap: 10px; line-height: 1.55;
    }
    .plan-features li::before { content: '—'; color: var(--accent); flex-shrink: 0; }
    .plan-features li.strong { color: var(--ink); }
    .plan-cta {
      display: inline-flex; align-items: center; justify-content: center; gap: 8px;
      margin-top: 28px;
      border: 1px solid var(--border);
      color: var(--mid);
      font-family: 'Inter', sans-serif; font-weight: 500;
      font-size: 0.68rem; letter-spacing: 0.14em; text-transform: uppercase;
      text-decoration: none; padding: 13px 20px;
      transition: border-color 0.35s ease, color 0.35s ease, box-shadow 0.4s ease;
    }
    .plan-cta:hover { border-color: var(--accent); color: var(--accent); }

    /* packs */
    .packs-label {
      font-family: 'Inter', sans-serif;
      font-size: 0.6rem; letter-spacing: 0.26em; text-transform: uppercase;
      color: var(--mid); margin: 40px 0 14px;
    }
    .packs-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 1px; background: var(--border); }
    .pack-card {
      background: var(--paper); padding: 32px 36px;
      display: flex; justify-content: space-between; align-items: center; gap: 24px;
      transition: background 0.4s ease, box-shadow 0.4s ease;
    }
    .pack-card:hover { background: var(--cream); box-shadow: inset 0 0 40px rgba(160,92,40,0.05); }
    .pack-label { font-family: 'Cormorant Garamond', serif; font-weight: 600; font-size: 1.1rem; color: var(--dark); }
    .pack-sub { font-family: 'Inter', sans-serif; font-size: 0.7rem; color: var(--mid); margin-top: 5px; }
    .pack-price { font-family: 'Cormorant Garamond', serif; font-weight: 300; font-size: 2.3rem; color: var(--accent); white-space: nowrap; flex-shrink: 0; }
    .pack-price span { font-family: 'Inter', sans-serif; font-size: 0.74rem; color: var(--mid); font-weight: 300; }

    /* ── ABOUT ── */
    #about { background: var(--paper); display: grid; grid-template-columns: 1fr 1fr; gap: 100px; align-items: start; }
    .about-body p { font-family: 'Inter', sans-serif; color: var(--mid); font-size: 0.86rem; line-height: 1.95; margin-bottom: 18px; }
    .about-body p strong { color: var(--ink); font-weight: 500; }
    .about-body blockquote {
      border-left: 1px solid var(--accent); padding-left: 24px;
      margin: 36px 0;
      font-family: 'Cormorant Garamond', serif; font-style: italic; font-weight: 300;
      font-size: 1.55rem; color: var(--dark); line-height: 1.5; position: relative;
    }
    .about-body blockquote::before {
      content: ''; position: absolute; left: -1px; top: 0; bottom: 0; width: 1px;
      background: var(--accent); box-shadow: 0 0 16px rgba(160,92,40,0.6);
    }
    .skills-table { width: 100%; border-collapse: collapse; }
    .skills-table tr { border-bottom: 1px solid var(--border); transition: background 0.3s ease; }
    .skills-table tr:last-child { border-bottom: none; }
    .skills-table tr:hover { background: rgba(160,92,40,0.04); }
    .skills-table td { padding: 15px 8px; font-size: 0.78rem; vertical-align: top; }
    .skills-table td:first-child { font-family: 'Inter', sans-serif; color: var(--mid); width: 40%; padding-right: 16px; letter-spacing: 0.04em; }
    .skills-table td:last-child { font-family: 'Cormorant Garamond', serif; font-size: 1rem; color: var(--ink); font-weight: 400; }

    /* ── CONTACT ── */
    #contact { background: var(--dark); text-align: center; padding: 130px 56px; position: relative; overflow: hidden; }
    .contact-glow {
      position: absolute; width: 700px; height: 500px; border-radius: 50%;
      background: radial-gradient(ellipse, rgba(160,92,40,0.15) 0%, transparent 68%);
      top: 50%; left: 50%; transform: translate(-50%, -50%);
      filter: blur(60px); pointer-events: none;
      animation: pulseGlow 6s ease-in-out infinite;
    }
    @keyframes pulseGlow {
      0%,100% { opacity: 0.6; transform: translate(-50%, -50%) scale(1); }
      50%      { opacity: 1; transform: translate(-50%, -50%) scale(1.08); }
    }
    #contact .section-eyebrow { color: var(--accent2); position: relative; }
    .contact-title {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300; font-size: clamp(3rem, 6.5vw, 6rem);
      line-height: 1.02; color: var(--cream); margin-bottom: 18px; position: relative;
    }
    .contact-title em { font-style: italic; color: var(--accent2); }
    .contact-sub {
      font-family: 'Inter', sans-serif;
      color: rgba(243,239,230,0.38); font-size: 0.84rem;
      margin-bottom: 60px; max-width: 280px; margin-left: auto; margin-right: auto; position: relative;
    }
    .contact-channels {
      display: flex; justify-content: center; gap: 1px;
      background: rgba(243,239,230,0.08); max-width: 700px;
      margin: 0 auto 42px; position: relative;
    }
    .channel-item {
      background: rgba(243,239,230,0.03);
      padding: 30px 36px; flex: 1; min-width: 140px;
      text-decoration: none;
      display: flex; flex-direction: column; align-items: center; gap: 9px;
      transition: background 0.4s ease, box-shadow 0.45s ease;
    }
    .channel-item:hover {
      background: rgba(243,239,230,0.06);
      box-shadow: 0 0 40px rgba(160,92,40,0.12);
    }
    .channel-platform { font-family: 'Inter', sans-serif; font-size: 0.55rem; letter-spacing: 0.22em; text-transform: uppercase; color: rgba(243,239,230,0.3); }
    .channel-handle {
      font-family: 'Cormorant Garamond', serif;
      font-size: 1.05rem; font-weight: 400; color: rgba(243,239,230,0.75);
      transition: color 0.35s ease;
    }
    .channel-item:hover .channel-handle { color: var(--accent2); }
    .contact-cta {
      display: inline-flex; align-items: center; gap: 10px;
      background: var(--accent); color: var(--cream);
      font-family: 'Inter', sans-serif; font-weight: 500;
      font-size: 0.7rem; letter-spacing: 0.16em; text-transform: uppercase;
      text-decoration: none; padding: 18px 44px; position: relative;
      transition: box-shadow 0.5s ease, transform 0.35s ease;
    }
    .contact-cta:hover {
      box-shadow: 0 0 44px rgba(160,92,40,0.55), 0 8px 24px rgba(160,92,40,0.22);
      transform: translateY(-2px);
    }

    /* ── FOOTER ── */
    footer {
      background: var(--dark);
      border-top: 1px solid rgba(243,239,230,0.08);
      padding: 28px 56px;
      display: grid; grid-template-columns: 1fr 1fr 1fr;
      align-items: center;
    }
    footer p { font-family: 'Inter', sans-serif; font-size: 0.68rem; color: rgba(243,239,230,0.25); }
    .footer-center {
      font-family: 'Cormorant Garamond', serif;
      font-size: 0.9rem; letter-spacing: 0.18em;
      color: rgba(243,239,230,0.15); text-align: center;
    }
    .footer-right { text-align: right; }

    /* ── REVEAL 2s ── */
    .reveal {
      opacity: 0; transform: translateY(16px);
      transition: opacity 2s cubic-bezier(0.22,1,0.36,1),
                  transform 2s cubic-bezier(0.22,1,0.36,1);
    }
    .reveal.visible { opacity: 1; transform: translateY(0); }

    .stagger > * {
      opacity: 0; transform: translateY(14px);
      transition: opacity 1.8s cubic-bezier(0.22,1,0.36,1),
                  transform 1.8s cubic-bezier(0.22,1,0.36,1);
    }
    .stagger.visible > *:nth-child(1)  { opacity:1; transform:none; transition-delay:0.00s; }
    .stagger.visible > *:nth-child(2)  { opacity:1; transform:none; transition-delay:0.09s; }
    .stagger.visible > *:nth-child(3)  { opacity:1; transform:none; transition-delay:0.18s; }
    .stagger.visible > *:nth-child(4)  { opacity:1; transform:none; transition-delay:0.27s; }
    .stagger.visible > *:nth-child(5)  { opacity:1; transform:none; transition-delay:0.36s; }
    .stagger.visible > *:nth-child(6)  { opacity:1; transform:none; transition-delay:0.45s; }
    .stagger.visible > *:nth-child(7)  { opacity:1; transform:none; transition-delay:0.54s; }
    .stagger.visible > *:nth-child(8)  { opacity:1; transform:none; transition-delay:0.63s; }

    .h-fade { opacity: 0; }
    .h-fade.go { animation: heroFade 2s cubic-bezier(0.22,1,0.36,1) forwards; }
    @keyframes heroFade {
      from { opacity: 0; transform: translateY(14px); }
      to   { opacity: 1; transform: translateY(0); }
    }

    /* ── RESPONSIVE ── */
    @media (max-width: 1024px) {
      .services-grid { grid-template-columns: repeat(2, 1fr); }
      .portfolio-grid { grid-template-columns: repeat(2, 1fr); }
      .pricing-grid { grid-template-columns: 1fr; }
      .process-grid { grid-template-columns: repeat(2, 1fr); }
      #intro { grid-template-columns: 1fr; gap: 28px; }
      .intro-divider { display: none; }
      .intro-block { padding: 0; }
    }
    @media (max-width: 768px) {
      nav { padding: 0 24px; }
      .nav-center, .nav-links { display: none; }
      .hero { grid-template-columns: 1fr; }
      .hero-left { padding: 120px 28px 60px; min-height: 100vh; }
      .hero-right { padding: 48px 28px 60px; border-left: none; border-top: 1px solid var(--border); }
      .hero-bg-num { display: none; }
      .section { padding: 72px 24px; }
      #about { grid-template-columns: 1fr; gap: 52px; }
      .portfolio-grid { grid-template-columns: 1fr; }
      .pricing-intro { grid-template-columns: 1fr; }
      .pricing-note { border-left: none; padding-left: 0; }
      .portfolio-intro { grid-template-columns: 1fr; }
      .portfolio-note { border-left: none; padding-left: 0; }
      .services-grid { grid-template-columns: 1fr; }
      .process-grid { grid-template-columns: 1fr; }
      .packs-grid { grid-template-columns: 1fr; }
      #contact { padding: 80px 24px; }
      footer { grid-template-columns: 1fr; gap: 8px; text-align: center; padding: 24px; }
      .footer-center, .footer-right { text-align: center; }
    }
    @media (prefers-reduced-motion: reduce) {
      .marquee-track, .hero-left-blob, .hero-left-blob-2, .contact-glow, .avail-dot { animation: none; }
      .reveal, .stagger > *, .h-fade { opacity: 1; transform: none; transition: none; animation: none; }
    }
  </style>
</head>
<body>

  <!-- LOADER -->
  <div id="loader">
    <p class="loader-logo">DARÍO<span>.</span></p>
    <div class="loader-line"></div>
  </div>

  <!-- CURSOR GLOW -->
  <div class="cursor-glow" id="cursorGlow"></div>

  <!-- NAV -->
  <nav id="mainNav">
    <a href="#" class="nav-logo">DARÍO<span>.</span></a>
    <p class="nav-center">Editor de Video</p>
    <ul class="nav-links">
      <li><a href="#portfolio">Portafolio</a></li>
      <li><a href="#pricing">Precios</a></li>
      <li><a href="#about">Enfoque</a></li>
      <li><a href="#contact" class="nav-cta">Trabajemos</a></li>
    </ul>
  </nav>

  <!-- HERO -->
  <section class="hero">
    <div class="hero-left">
      <div class="hero-left-blob"></div>
      <div class="hero-left-blob-2"></div>
      <p class="hero-eyebrow h-fade">Editor de video — Disponible</p>
      <h1 class="hero-name h-fade" style="animation-delay:.15s">DARÍO</h1>
      <p class="hero-role h-fade" style="animation-delay:.28s">Reels · Shorts · TikToks</p>
      <p class="hero-desc h-fade" style="animation-delay:.42s">Transformo ideas en videos que captan atención, generan emoción y mantienen a la audiencia viendo hasta el final.</p>
      <div class="hero-actions h-fade" style="animation-delay:.56s">
        <a href="#portfolio" class="btn-primary">
          Ver portafolio
          <svg width="12" height="12" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 8h10M9 4l4 4-4 4"/></svg>
        </a>
        <a href="#pricing" class="btn-ghost-light">Ver precios</a>
      </div>
    </div>
    <div class="hero-right">
      <div class="hero-bg-num">3</div>
      <div class="stat-row h-fade" style="animation-delay:.3s">
        <div class="stat-num">3<span>+</span></div>
        <div class="stat-info">
          <span class="stat-label">Estilos</span>
          <span class="stat-desc">Cinematic, storytelling y alta retención</span>
        </div>
      </div>
      <div class="stat-row h-fade" style="animation-delay:.44s">
        <div class="stat-num">100<span>%</span></div>
        <div class="stat-info">
          <span class="stat-label">Retención</span>
          <span class="stat-desc">Cada segundo tiene una función</span>
        </div>
      </div>
      <div class="stat-row h-fade" style="animation-delay:.58s">
        <div class="stat-num">2</div>
        <div class="stat-info">
          <span class="stat-label">Rondas de cambios</span>
          <span class="stat-desc">Incluidas en todos los planes</span>
        </div>
      </div>
      <div class="avail-badge h-fade" style="animation-delay:.72s">
        <span class="avail-dot"></span>
        Disponible para nuevos proyectos
      </div>
    </div>
  </section>

  <!-- MARQUEE -->
  <div class="marquee-wrap">
    <div class="marquee-track">
      <span>Edición profesional</span><span class="dot">·</span>
      <span>Subtítulos personalizados</span><span class="dot">·</span>
      <span>Sound design</span><span class="dot">·</span>
      <span>Sincronización musical</span><span class="dot">·</span>
      <span>Optimización de hooks</span><span class="dot">·</span>
      <span>Storytelling visual</span><span class="dot">·</span>
      <span>Retención de audiencia</span><span class="dot">·</span>
      <span>Edición profesional</span><span class="dot">·</span>
      <span>Subtítulos personalizados</span><span class="dot">·</span>
      <span>Sound design</span><span class="dot">·</span>
      <span>Sincronización musical</span><span class="dot">·</span>
      <span>Optimización de hooks</span><span class="dot">·</span>
      <span>Storytelling visual</span><span class="dot">·</span>
      <span>Retención de audiencia</span><span class="dot">·</span>
    </div>
  </div>

  <!-- INTRO BAND -->
  <section id="intro">
    <div class="intro-block reveal">
      <p class="intro-label">Especialidad</p>
      <p class="intro-value">Corte rápido, movimiento y ritmo en formato vertical</p>
    </div>
    <div class="intro-divider"></div>
    <div class="intro-block reveal">
      <p class="intro-label">Formatos</p>
      <p class="intro-value">Reels · Shorts · TikTok · Videoclips · Corporativo</p>
    </div>
    <div class="intro-divider"></div>
    <div class="intro-block reveal">
      <p class="intro-label">Entrega</p>
      <p class="intro-value">Drive organizado · Flujo claro · Sin confusiones</p>
    </div>
  </section>

  <!-- SERVICES -->
  <section class="section" id="services">
    <p class="section-eyebrow reveal">Qué hago</p>
    <h2 class="section-title reveal">Todo lo que tu video<br><em>necesita para funcionar.</em></h2>
    <div class="services-grid stagger">
      <div class="service-cell">
        <p class="service-num">01</p>
        <p class="service-name">Edición completa</p>
        <p class="service-desc">Reels, Shorts y TikToks con cortes precisos y ritmo que retiene.</p>
      </div>
      <div class="service-cell">
        <p class="service-num">02</p>
        <p class="service-name">Subtítulos personalizados</p>
        <p class="service-desc">Tipografía y animación adaptada al estilo de cada video.</p>
      </div>
      <div class="service-cell">
        <p class="service-num">03</p>
        <p class="service-name">Sincronización musical</p>
        <p class="service-desc">Cortes y transiciones que respiran con la música.</p>
      </div>
      <div class="service-cell">
        <p class="service-num">04</p>
        <p class="service-name">Hook y retención</p>
        <p class="service-desc">Apertura pensada para que nadie se vaya en los primeros 3 segundos.</p>
      </div>
      <div class="service-cell">
        <p class="service-num">05</p>
        <p class="service-name">Storytelling visual</p>
        <p class="service-desc">Cada segundo tiene una función. Cada corte avanza la historia.</p>
      </div>
      <div class="service-cell">
        <p class="service-num">06</p>
        <p class="service-name">Apoyo en guiones</p>
        <p class="service-desc">Estructura narrativa y propuesta de guion enfocada en retención.</p>
      </div>
      <div class="service-cell">
        <p class="service-num">07</p>
        <p class="service-name">Formato vertical nativo</p>
        <p class="service-desc">Optimizado para 9:16. Pensado para celular desde el inicio.</p>
      </div>
      <div class="service-cell">
        <p class="service-num">08</p>
        <p class="service-name">Sound design</p>
        <p class="service-desc">Audio diseñado para impacto. Disponible en planes Premium y Guion.</p>
      </div>
    </div>
  </section>

  <!-- PORTFOLIO -->
  <section class="section" id="portfolio">
    <div class="portfolio-intro">
      <div>
        <p class="section-eyebrow reveal">Portafolio</p>
        <h2 class="section-title reveal" style="margin-bottom:0;color:var(--cream)">Tres estilos.<br><em>Un mismo criterio.</em></h2>
      </div>
      <p class="portfolio-note reveal">Cada video es propio. Tres aproximaciones distintas al mismo oficio: narrar con imágenes, cortar con propósito y mantener la atención de quien mira.</p>
    </div>
    <div class="portfolio-grid stagger">
      <div class="video-card">
        <div class="video-shell">
          <iframe src="https://www.instagram.com/reel/DUcL9fhEf16/embed"
            allowfullscreen allow="autoplay; clipboard-write; encrypted-media; picture-in-picture; web-share" scrolling="no"></iframe>
        </div>
        <div class="video-meta">
          <p class="video-tag">Reel · Cinematográfico</p>
          <p class="video-title">Narrativa visual</p>
          <p class="video-desc">Color, ritmo y emoción en cada corte.</p>
        </div>
      </div>
      <div class="video-card">
        <div class="video-shell">
          <iframe src="https://www.instagram.com/reel/DJF8kU5xddQ/embed"
            allowfullscreen allow="autoplay; clipboard-write; encrypted-media; picture-in-picture; web-share" scrolling="no"></iframe>
        </div>
        <div class="video-meta">
          <p class="video-tag">Reel · Storytelling</p>
          <p class="video-title">Historia que engancha</p>
          <p class="video-desc">Estructura pensada para retener.</p>
        </div>
      </div>
      <div class="video-card">
        <div class="video-shell">
          <div class="tiktok-fallback">
            <p>Alta retención · Corte rápido<br>Abrí el video en TikTok para verlo.</p>
            <a href="https://vt.tiktok.com/ZSCRP1MTK/" target="_blank" rel="noopener">
              Ver en TikTok
              <svg width="11" height="11" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 8h10M9 4l4 4-4 4"/></svg>
            </a>
          </div>
        </div>
        <div class="video-meta">
          <p class="video-tag">TikTok · Alta Retención</p>
          <p class="video-title">Corte rápido y preciso</p>
          <p class="video-desc">Hook poderoso. Sin un segundo desperdiciado.</p>
        </div>
      </div>
    </div>
  </section>

  <!-- PROCESS -->
  <section class="section" id="process">
    <p class="section-eyebrow reveal">Cómo trabajo</p>
    <h2 class="section-title reveal">Un proceso claro<br><em>de inicio a entrega.</em></h2>
    <div class="process-grid stagger">
      <div class="process-step">
        <p class="process-step-num">01</p>
        <p class="process-step-title">Recibo el material</p>
        <p class="process-step-desc">El cliente sube los clips a una carpeta de Drive compartida. Sin complicaciones.</p>
      </div>
      <div class="process-step">
        <p class="process-step-num">02</p>
        <p class="process-step-title">Analizo y estructuro</p>
        <p class="process-step-desc">Estudio el contenido, defino el ritmo y armo la estructura narrativa antes de cortar.</p>
      </div>
      <div class="process-step">
        <p class="process-step-num">03</p>
        <p class="process-step-title">Edito y refino</p>
        <p class="process-step-desc">Corte, subtítulos, música, sound design y optimización de retención en un solo pase.</p>
      </div>
      <div class="process-step">
        <p class="process-step-num">04</p>
        <p class="process-step-title">Entrego y ajusto</p>
        <p class="process-step-desc">Primera versión lista. Dos rondas de cambios incluidas hasta que quede perfecto.</p>
      </div>
    </div>
  </section>

  <!-- PRICING -->
  <section class="section" id="pricing">
    <div class="pricing-intro">
      <div>
        <p class="section-eyebrow reveal">Precios</p>
        <h2 class="section-title reveal" style="margin-bottom:0">Invertís en video.<br><em>Recibís resultados.</em></h2>
      </div>
      <p class="pricing-note reveal">Todos los planes incluyen 2 rondas de cambios. El cliente provee el material; yo me encargo del resto. Entrega por Google Drive.</p>
    </div>
    <div class="pricing-grid stagger">
      <div class="plan-card">
        <p class="plan-name">Edición Estándar</p>
        <p class="plan-price"><sup>$</sup>15</p>
        <p class="plan-period">por video · USD</p>
        <div class="plan-divider"></div>
        <ul class="plan-features">
          <li class="strong">Edición completa</li>
          <li>Subtítulos animados</li>
          <li>Música</li>
          <li>Formato vertical 9:16</li>
          <li>2 rondas de cambios</li>
        </ul>
        <a href="https://wa.me/593995167885" target="_blank" rel="noopener" class="plan-cta">
          Consultar
          <svg width="11" height="11" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 8h10M9 4l4 4-4 4"/></svg>
        </a>
      </div>
      <div class="plan-card featured">
        <span class="plan-badge">Más elegido</span>
        <p class="plan-name">Edición Premium</p>
        <p class="plan-price"><sup>$</sup>25</p>
        <p class="plan-period">por video · USD</p>
        <div class="plan-divider"></div>
        <ul class="plan-features">
          <li class="strong">Todo lo del Estándar</li>
          <li>Sound design</li>
          <li>Sincronización avanzada al ritmo</li>
          <li>Optimización de ritmo y retención</li>
          <li>2 rondas de cambios</li>
        </ul>
        <a href="https://wa.me/593995167885" target="_blank" rel="noopener" class="plan-cta">
          Consultar
          <svg width="11" height="11" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 8h10M9 4l4 4-4 4"/></svg>
        </a>
      </div>
      <div class="plan-card">
        <p class="plan-name">Edición + Guion</p>
        <p class="plan-price"><sup>$</sup>35</p>
        <p class="plan-period">por video · USD</p>
        <div class="plan-divider"></div>
        <ul class="plan-features">
          <li class="strong">Todo lo del Premium</li>
          <li>Revisión de idea</li>
          <li>Optimización de hook</li>
          <li>Estructura narrativa</li>
          <li>Propuesta de guion para retención</li>
          <li>2 rondas de cambios</li>
        </ul>
        <a href="https://wa.me/593995167885" target="_blank" rel="noopener" class="plan-cta">
          Consultar
          <svg width="11" height="11" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 8h10M9 4l4 4-4 4"/></svg>
        </a>
      </div>
    </div>
    <p class="packs-label reveal">Paquetes mensuales — para creadores que publican constantemente</p>
    <div class="packs-grid stagger">
      <div class="pack-card">
        <div>
          <p class="pack-label">4 Videos Premium</p>
          <p class="pack-sub">Publicás una vez por semana</p>
        </div>
        <p class="pack-price">$90 <span>/ mes</span></p>
      </div>
      <div class="pack-card">
        <div>
          <p class="pack-label">8 Videos Premium</p>
          <p class="pack-sub">Publicás dos veces por semana</p>
        </div>
        <p class="pack-price">$170 <span>/ mes</span></p>
      </div>
    </div>
  </section>

  <!-- ABOUT -->
  <section class="section" id="about">
    <div class="about-body">
      <p class="section-eyebrow reveal">Mi enfoque</p>
      <h2 class="section-title reveal">No corto clips.<br><em>Construyo videos.</em></h2>
      <p class="reveal">Estudio constantemente <strong>retención, storytelling y comportamiento de audiencia</strong> para crear videos que mantengan la atención y comuniquen mejor el mensaje.</p>
      <blockquote class="reveal">"Busco que cada segundo tenga una función."</blockquote>
      <p class="reveal">Me especializo en <strong>corte rápido y movimiento preciso</strong>, pero me adapto al estilo que el proyecto necesite sin perder criterio.</p>
    </div>
    <div>
      <p class="section-eyebrow reveal" style="margin-bottom:22px">Lo que traigo a cada proyecto</p>
      <table class="skills-table reveal">
        <tr><td>Edición</td><td>Corte rápido, sincronización, ritmo</td></tr>
        <tr><td>Subtítulos</td><td>Animados y personalizados</td></tr>
        <tr><td>Sound design</td><td>Audio pensado para impacto</td></tr>
        <tr><td>Storytelling</td><td>Estructura narrativa por retención</td></tr>
        <tr><td>Hook</td><td>Optimización de apertura</td></tr>
        <tr><td>Guiones</td><td>Propuesta y revisión de estructura</td></tr>
        <tr><td>Formatos</td><td>Reels, Shorts, TikTok nativo</td></tr>
        <tr><td>Entrega</td><td>Organizada por Drive</td></tr>
      </table>
    </div>
  </section>

  <!-- CONTACT -->
  <section class="section" id="contact">
    <div class="contact-glow"></div>
    <p class="section-eyebrow reveal">Contacto</p>
    <h2 class="contact-title reveal">¿Tenés un<br><em>proyecto?</em></h2>
    <p class="contact-sub reveal">Escribime por el canal que prefieras. Respondo rápido.</p>
    <div class="contact-channels reveal">
      <a href="https://wa.me/593995167885" target="_blank" rel="noopener" class="channel-item">
        <span class="channel-platform">WhatsApp</span>
        <span class="channel-handle">+593 99 516 7885</span>
      </a>
      <a href="https://instagram.com/dariohopes" target="_blank" rel="noopener" class="channel-item">
        <span class="channel-platform">Instagram</span>
        <span class="channel-handle">@dariohopes</span>
      </a>
      <a href="mailto:darioaun@gmail.com" class="channel-item">
        <span class="channel-platform">Correo</span>
        <span class="channel-handle">darioaun@gmail.com</span>
      </a>
    </div>
    <a href="https://wa.me/593995167885" target="_blank" rel="noopener" class="contact-cta reveal">
      Empezar ahora
      <svg width="13" height="13" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 8h10M9 4l4 4-4 4"/></svg>
    </a>
  </section>

  <!-- FOOTER -->
  <footer>
    <p>© 2026 Darío</p>
    <p class="footer-center">DARÍO<span style="color:var(--accent2)">.</span></p>
    <p class="footer-right">darioaun@gmail.com</p>
  </footer>

  <script>
    // Loader
    window.addEventListener('load', () => {
      setTimeout(() => {
        document.getElementById('loader').classList.add('done');
        document.querySelectorAll('.h-fade').forEach(el => el.classList.add('go'));
      }, 1000);
    });

    // Cursor glow
    const glow = document.getElementById('cursorGlow');
    let mx = window.innerWidth/2, my = window.innerHeight/2, cx = mx, cy = my;
    document.addEventListener('mousemove', e => { mx = e.clientX; my = e.clientY; });
    (function tick() {
      cx += (mx-cx)*0.06; cy += (my-cy)*0.06;
      glow.style.left = cx+'px'; glow.style.top = cy+'px';
      requestAnimationFrame(tick);
    })();

    // Nav scroll
    const nav = document.getElementById('mainNav');
    window.addEventListener('scroll', () => {
      nav.classList.toggle('scrolled', window.scrollY > 50);
    }, { passive: true });

    // Scroll reveal
    const obs = new IntersectionObserver(entries => {
      entries.forEach(e => {
        if (e.isIntersecting) { e.target.classList.add('visible'); obs.unobserve(e.target); }
      });
    }, { threshold: 0.07 });
    document.querySelectorAll('.reveal, .stagger').forEach(el => obs.observe(el));
  </script>

</body>
</html>
