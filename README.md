<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Darío — Editor de Video</title>
  <meta name="description" content="Transformo ideas en videos que captan atención, generan emoción y mantienen a la audiencia viendo hasta el final." />
  <link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;0,700;1,300;1,400&family=Inter:wght@300;400;500&display=swap" rel="stylesheet" />
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    :root {
      --cream:    #f5f1ea;
      --paper:    #ede8de;
      --warm:     #e6ddd1;
      --mid:      #9c9285;
      --dark:     #2a2218;
      --ink:      #3e3428;
      --accent:   #a8622e;
      --glow:     rgba(168, 98, 46, 0.14);
      --border:   rgba(180, 168, 148, 0.45);
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

    /* ── PAGE LOADER ── */
    #loader {
      position: fixed;
      inset: 0;
      background: var(--cream);
      z-index: 9999;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: opacity 1.2s ease, visibility 1.2s ease;
    }
    #loader.done { opacity: 0; visibility: hidden; }
    .loader-name {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: 2rem;
      letter-spacing: 0.3em;
      color: var(--dark);
      opacity: 0;
      animation: loaderFade 1s 0.3s forwards;
    }
    .loader-name span { color: var(--accent); }
    @keyframes loaderFade {
      from { opacity: 0; transform: translateY(8px); }
      to   { opacity: 1; transform: translateY(0); }
    }

    /* ── CURSOR GLOW ── */
    .cursor-glow {
      position: fixed;
      width: 420px; height: 420px;
      border-radius: 50%;
      background: radial-gradient(circle, rgba(168,98,46,0.09) 0%, transparent 68%);
      pointer-events: none;
      transform: translate(-50%, -50%);
      z-index: 1;
      transition: opacity 0.5s ease;
    }

    /* ── NAV ── */
    nav {
      position: fixed;
      top: 0; left: 0; right: 0;
      z-index: 200;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 20px 56px;
      background: rgba(245, 241, 234, 0.78);
      backdrop-filter: blur(18px);
      -webkit-backdrop-filter: blur(18px);
      border-bottom: 1px solid var(--border);
      transition: background 0.6s ease, box-shadow 0.6s ease;
    }
    nav.scrolled {
      background: rgba(245, 241, 234, 0.96);
      box-shadow: 0 2px 40px rgba(168,98,46,0.07);
    }
    .nav-logo {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 400;
      font-size: 1.3rem;
      letter-spacing: 0.18em;
      color: var(--dark);
      text-decoration: none;
      transition: opacity 0.3s ease;
    }
    .nav-logo:hover { opacity: 0.6; }
    .nav-logo span { color: var(--accent); }
    .nav-links { display: flex; gap: 40px; list-style: none; }
    .nav-links a {
      font-family: 'Inter', sans-serif;
      color: var(--mid);
      text-decoration: none;
      font-size: 0.72rem;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      transition: color 0.35s ease;
      position: relative;
    }
    .nav-links a::after {
      content: '';
      position: absolute;
      bottom: -3px; left: 0; right: 0;
      height: 1px;
      background: var(--accent);
      transform: scaleX(0);
      transform-origin: left;
      transition: transform 0.35s ease;
    }
    .nav-links a:hover { color: var(--dark); }
    .nav-links a:hover::after { transform: scaleX(1); }
    .nav-cta {
      background: var(--accent) !important;
      color: var(--cream) !important;
      padding: 9px 24px !important;
      transition: box-shadow 0.4s ease, transform 0.3s ease !important;
    }
    .nav-cta::after { display: none !important; }
    .nav-cta:hover {
      box-shadow: 0 0 26px rgba(168,98,46,0.45) !important;
      transform: translateY(-1px) !important;
      color: var(--cream) !important;
    }

    /* ── HERO ── */
    .hero {
      min-height: 100vh;
      display: grid;
      grid-template-columns: 1.15fr 0.85fr;
      align-items: end;
      padding: 148px 56px 88px;
      position: relative;
      overflow: hidden;
    }
    .hero-blob {
      position: absolute;
      border-radius: 50%;
      filter: blur(90px);
      pointer-events: none;
      z-index: 0;
    }
    .hero-blob-1 {
      width: 520px; height: 520px;
      top: -60px; right: 4%;
      background: radial-gradient(circle, rgba(168,98,46,0.11) 0%, transparent 68%);
      animation: blobFloat 9s ease-in-out infinite;
    }
    .hero-blob-2 {
      width: 300px; height: 300px;
      bottom: 8%; left: -40px;
      background: radial-gradient(circle, rgba(168,98,46,0.07) 0%, transparent 68%);
      animation: blobFloat 13s ease-in-out infinite reverse;
    }
    @keyframes blobFloat {
      0%,100% { transform: translateY(0) scale(1); }
      50%      { transform: translateY(-22px) scale(1.03); }
    }
    .hero::after {
      content: '';
      position: absolute;
      top: 0; right: 0;
      width: 40%;
      height: 100%;
      background: var(--paper);
      z-index: 0;
    }
    .hero-left, .hero-right { position: relative; z-index: 1; }
    .hero-left { padding-right: 56px; }
    .hero-right { padding-left: 56px; border-left: 1px solid var(--border); }

    .hero-eyebrow {
      display: inline-flex;
      align-items: center;
      gap: 12px;
      font-size: 0.68rem;
      letter-spacing: 0.22em;
      text-transform: uppercase;
      color: var(--accent);
      margin-bottom: 30px;
    }
    .hero-eyebrow::before {
      content: '';
      display: block;
      width: 22px; height: 1px;
      background: var(--accent);
    }
    .hero-name {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: clamp(5rem, 10vw, 9.5rem);
      line-height: 0.88;
      letter-spacing: -0.01em;
      color: var(--dark);
      margin-bottom: 22px;
    }
    .hero-role {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 400;
      font-style: italic;
      font-size: clamp(1.1rem, 2.2vw, 1.6rem);
      color: var(--mid);
      letter-spacing: 0.04em;
      margin-bottom: 38px;
    }
    .hero-role strong {
      font-style: normal;
      font-weight: 600;
      color: var(--ink);
    }
    .hero-desc {
      color: var(--mid);
      font-size: 0.9rem;
      line-height: 1.9;
      max-width: 370px;
      margin-bottom: 52px;
    }
    .hero-actions { display: flex; gap: 14px; align-items: center; flex-wrap: wrap; }

    .btn-primary {
      display: inline-flex; align-items: center; gap: 10px;
      background: var(--accent);
      color: var(--cream);
      font-family: 'Inter', sans-serif;
      font-weight: 500;
      font-size: 0.72rem;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      text-decoration: none;
      padding: 15px 30px;
      transition: box-shadow 0.45s ease, transform 0.35s ease;
    }
    .btn-primary:hover {
      box-shadow: 0 0 32px rgba(168,98,46,0.5), 0 6px 20px rgba(168,98,46,0.22);
      transform: translateY(-2px);
    }
    .btn-ghost {
      display: inline-flex; align-items: center; gap: 10px;
      border: 1px solid var(--border);
      color: var(--mid);
      font-family: 'Inter', sans-serif;
      font-size: 0.72rem;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      text-decoration: none;
      padding: 15px 30px;
      transition: border-color 0.4s ease, color 0.4s ease, box-shadow 0.4s ease;
    }
    .btn-ghost:hover {
      border-color: rgba(168,98,46,0.5);
      color: var(--ink);
      box-shadow: 0 0 18px rgba(168,98,46,0.12);
    }

    .stat-block {
      padding: 28px 0;
      border-bottom: 1px solid var(--border);
      transition: padding-left 0.4s ease;
    }
    .stat-block:last-child { border-bottom: none; }
    .stat-block:hover { padding-left: 10px; }
    .stat-num {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: 2.8rem;
      color: var(--dark);
      line-height: 1;
      margin-bottom: 5px;
    }
    .stat-num span { color: var(--accent); }
    .stat-label {
      font-size: 0.68rem;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      color: var(--mid);
    }

    /* ── MARQUEE ── */
    .marquee-wrap {
      overflow: hidden;
      border-top: 1px solid var(--border);
      border-bottom: 1px solid var(--border);
      padding: 13px 0;
      background: var(--paper);
      position: relative;
    }
    .marquee-wrap::before,
    .marquee-wrap::after {
      content: '';
      position: absolute;
      top: 0; bottom: 0;
      width: 100px; z-index: 2;
    }
    .marquee-wrap::before { left: 0; background: linear-gradient(to right, var(--paper), transparent); }
    .marquee-wrap::after  { right: 0; background: linear-gradient(to left, var(--paper), transparent); }
    .marquee-track {
      display: inline-flex;
      animation: marquee 26s linear infinite;
    }
    .marquee-track span {
      font-family: 'Cormorant Garamond', serif;
      font-style: italic;
      font-size: 0.95rem;
      letter-spacing: 0.12em;
      color: var(--mid);
      padding: 0 30px;
    }
    .marquee-track span.dot {
      font-style: normal;
      color: var(--accent);
      padding: 0 2px;
      font-size: 0.7rem;
    }
    @keyframes marquee {
      from { transform: translateX(0); }
      to   { transform: translateX(-50%); }
    }

    /* ── SECTION SHARED ── */
    .section { padding: 100px 56px; border-top: 1px solid var(--border); position: relative; }
    .section-eyebrow {
      font-family: 'Inter', sans-serif;
      font-size: 0.65rem;
      letter-spacing: 0.25em;
      text-transform: uppercase;
      color: var(--accent);
      margin-bottom: 14px;
    }
    .section-title {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: clamp(2.2rem, 3.8vw, 3.2rem);
      line-height: 1.1;
      color: var(--dark);
      margin-bottom: 56px;
    }
    .section-title em {
      font-style: italic;
      font-weight: 300;
    }

    /* ── SERVICES ── */
    .services-grid {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 1px;
      background: var(--border);
    }
    .service-cell {
      background: var(--cream);
      padding: 32px 28px;
      position: relative;
      overflow: hidden;
      transition: background 0.5s ease;
    }
    .service-cell::before {
      content: '';
      position: absolute;
      inset: 0;
      background: radial-gradient(circle at 20% 90%, var(--glow), transparent 60%);
      opacity: 0;
      transition: opacity 0.6s ease;
    }
    .service-cell:hover { background: var(--paper); }
    .service-cell:hover::before { opacity: 1; }
    .service-name {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 600;
      font-size: 1.05rem;
      color: var(--dark);
      margin-bottom: 9px;
      position: relative;
    }
    .service-desc {
      font-family: 'Inter', sans-serif;
      font-size: 0.76rem;
      color: var(--mid);
      line-height: 1.7;
      position: relative;
    }

    /* ── PORTFOLIO ── */
    #portfolio { background: var(--paper); }
    .portfolio-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 2px;
    }
    .video-card {
      background: var(--warm);
      overflow: hidden;
      position: relative;
      transition: transform 0.6s cubic-bezier(0.22,1,0.36,1), box-shadow 0.6s cubic-bezier(0.22,1,0.36,1);
    }
    .video-card::after {
      content: '';
      position: absolute;
      inset: 0;
      box-shadow: inset 0 0 0 1px transparent;
      transition: box-shadow 0.5s ease;
      pointer-events: none;
      z-index: 2;
    }
    .video-card:hover {
      transform: translateY(-5px);
      box-shadow: 0 16px 56px rgba(168,98,46,0.16), 0 4px 20px rgba(168,98,46,0.09);
    }
    .video-card:hover::after { box-shadow: inset 0 0 0 1px rgba(168,98,46,0.3); }
    .video-shell {
      position: relative;
      width: 100%;
      padding-top: 177.78%;
      background: var(--warm);
    }
    .video-shell iframe {
      position: absolute;
      inset: 0;
      width: 100%; height: 100%;
      border: none;
    }
    .tiktok-fallback {
      position: absolute;
      inset: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      gap: 22px;
      background: var(--warm);
      padding: 36px;
    }
    .tiktok-fallback p {
      font-family: 'Cormorant Garamond', serif;
      font-style: italic;
      font-size: 1rem;
      color: var(--mid);
      text-align: center;
      line-height: 1.7;
    }
    .tiktok-fallback a {
      display: inline-flex; align-items: center; gap: 8px;
      background: var(--accent);
      color: var(--cream);
      font-family: 'Inter', sans-serif;
      font-weight: 500;
      font-size: 0.7rem;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      text-decoration: none;
      padding: 12px 24px;
      transition: box-shadow 0.4s ease, transform 0.3s ease;
    }
    .tiktok-fallback a:hover {
      box-shadow: 0 0 24px rgba(168,98,46,0.5);
      transform: translateY(-1px);
    }
    .video-meta {
      padding: 22px 24px 28px;
      border-top: 1px solid var(--border);
      background: var(--paper);
      transition: background 0.4s ease;
    }
    .video-card:hover .video-meta { background: var(--cream); }
    .video-tag {
      font-family: 'Inter', sans-serif;
      font-size: 0.6rem;
      letter-spacing: 0.2em;
      text-transform: uppercase;
      color: var(--accent);
      margin-bottom: 7px;
    }
    .video-title {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 600;
      font-size: 1.1rem;
      color: var(--dark);
    }
    .video-desc {
      font-family: 'Inter', sans-serif;
      font-size: 0.74rem;
      color: var(--mid);
      margin-top: 5px;
    }

    /* ── PRICING ── */
    #pricing { background: var(--cream); }
    .pricing-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 1px;
      background: var(--border);
      margin-bottom: 52px;
    }
    .plan-card {
      background: var(--cream);
      padding: 42px 32px;
      position: relative;
      overflow: hidden;
      transition: transform 0.5s cubic-bezier(0.22,1,0.36,1), box-shadow 0.5s ease;
    }
    .plan-card::before {
      content: '';
      position: absolute;
      bottom: -80px; right: -80px;
      width: 200px; height: 200px;
      border-radius: 50%;
      background: radial-gradient(circle, var(--glow), transparent 70%);
      opacity: 0;
      transition: opacity 0.6s ease, transform 0.6s ease;
      transform: scale(0.6);
    }
    .plan-card:hover { transform: translateY(-4px); box-shadow: 0 10px 50px rgba(168,98,46,0.10); }
    .plan-card:hover::before { opacity: 1; transform: scale(1); }
    .plan-card.featured { background: var(--paper); }
    .plan-badge {
      position: absolute;
      top: 20px; right: 20px;
      background: var(--accent);
      color: var(--cream);
      font-family: 'Inter', sans-serif;
      font-weight: 500;
      font-size: 0.56rem;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      padding: 4px 10px;
    }
    .plan-name {
      font-family: 'Inter', sans-serif;
      font-size: 0.65rem;
      letter-spacing: 0.22em;
      text-transform: uppercase;
      color: var(--mid);
      margin-bottom: 18px;
    }
    .plan-price {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: 3.8rem;
      line-height: 1;
      color: var(--dark);
      margin-bottom: 5px;
      position: relative;
    }
    .plan-price sup { font-size: 1.2rem; vertical-align: super; color: var(--mid); }
    .plan-period { font-family: 'Inter', sans-serif; font-size: 0.72rem; color: var(--mid); margin-bottom: 28px; }
    .plan-divider { height: 1px; background: var(--border); margin-bottom: 24px; }
    .plan-features { list-style: none; display: flex; flex-direction: column; gap: 11px; position: relative; }
    .plan-features li {
      font-family: 'Inter', sans-serif;
      font-size: 0.78rem;
      color: var(--mid);
      display: flex; align-items: flex-start; gap: 10px; line-height: 1.5;
    }
    .plan-features li::before { content: '—'; color: var(--accent); flex-shrink: 0; }
    .plan-features li.strong { color: var(--ink); }

    .packs-label {
      font-family: 'Inter', sans-serif;
      font-size: 0.65rem;
      letter-spacing: 0.22em;
      text-transform: uppercase;
      color: var(--mid);
      margin-bottom: 16px;
    }
    .packs-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 1px; background: var(--border); }
    .pack-card {
      background: var(--paper);
      padding: 32px 34px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      transition: background 0.4s ease, box-shadow 0.45s ease;
    }
    .pack-card:hover { background: var(--cream); box-shadow: inset 0 0 40px rgba(168,98,46,0.05); }
    .pack-label { font-family: 'Cormorant Garamond', serif; font-weight: 600; font-size: 1.1rem; color: var(--dark); }
    .pack-sub { font-family: 'Inter', sans-serif; font-size: 0.73rem; color: var(--mid); margin-top: 5px; }
    .pack-price { font-family: 'Cormorant Garamond', serif; font-weight: 300; font-size: 2.2rem; color: var(--accent); white-space: nowrap; }
    .pack-price span { font-family: 'Inter', sans-serif; font-size: 0.78rem; color: var(--mid); font-weight: 300; }

    /* ── ABOUT ── */
    #about {
      background: var(--paper);
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 88px;
      align-items: start;
    }
    .about-body p { font-family: 'Inter', sans-serif; color: var(--mid); font-size: 0.88rem; line-height: 1.95; margin-bottom: 18px; }
    .about-body p strong { color: var(--ink); font-weight: 500; }
    .about-body blockquote {
      border-left: 1px solid var(--accent);
      padding-left: 22px;
      margin: 34px 0;
      font-family: 'Cormorant Garamond', serif;
      font-style: italic;
      font-weight: 300;
      font-size: 1.45rem;
      color: var(--dark);
      line-height: 1.5;
      position: relative;
    }
    .about-body blockquote::before {
      content: '';
      position: absolute;
      left: -1px; top: 0; bottom: 0;
      width: 1px;
      background: var(--accent);
      box-shadow: 0 0 14px rgba(168,98,46,0.6);
    }
    .skills-table { width: 100%; border-collapse: collapse; }
    .skills-table tr { border-bottom: 1px solid var(--border); transition: background 0.35s ease; }
    .skills-table tr:last-child { border-bottom: none; }
    .skills-table tr:hover { background: rgba(168,98,46,0.04); }
    .skills-table td { padding: 14px 8px; font-size: 0.78rem; vertical-align: top; }
    .skills-table td:first-child {
      font-family: 'Inter', sans-serif;
      color: var(--mid); width: 40%; padding-right: 18px; letter-spacing: 0.04em;
    }
    .skills-table td:last-child {
      font-family: 'Cormorant Garamond', serif;
      font-size: 0.95rem;
      color: var(--ink);
      font-weight: 400;
    }

    /* ── CONDITIONS ── */
    #conditions { background: var(--warm); }
    .conditions-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 1px; background: var(--border); }
    .condition-item {
      background: var(--warm);
      padding: 30px 26px;
      position: relative;
      overflow: hidden;
      transition: background 0.5s ease;
    }
    .condition-item::after {
      content: '';
      position: absolute;
      bottom: 0; left: 0; right: 0;
      height: 1px;
      background: var(--accent);
      transform: scaleX(0);
      transform-origin: left;
      transition: transform 0.55s cubic-bezier(0.22,1,0.36,1);
      box-shadow: 0 0 12px rgba(168,98,46,0.55);
    }
    .condition-item:hover { background: var(--paper); }
    .condition-item:hover::after { transform: scaleX(1); }
    .condition-num {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: 2.4rem;
      color: var(--border);
      margin-bottom: 14px;
      transition: color 0.4s ease;
    }
    .condition-item:hover .condition-num { color: rgba(168,98,46,0.22); }
    .condition-text { font-family: 'Inter', sans-serif; font-size: 0.78rem; color: var(--mid); line-height: 1.7; }
    .condition-text strong { color: var(--ink); font-weight: 500; }

    /* ── CONTACT ── */
    #contact { background: var(--cream); text-align: center; padding: 120px 56px; position: relative; overflow: hidden; }
    .contact-glow {
      position: absolute;
      width: 700px; height: 500px;
      border-radius: 50%;
      background: radial-gradient(ellipse, rgba(168,98,46,0.09) 0%, transparent 68%);
      top: 50%; left: 50%;
      transform: translate(-50%, -50%);
      filter: blur(50px);
      pointer-events: none;
      animation: pulseGlow 6s ease-in-out infinite;
    }
    @keyframes pulseGlow {
      0%,100% { opacity: 0.6; transform: translate(-50%, -50%) scale(1); }
      50%      { opacity: 1; transform: translate(-50%, -50%) scale(1.07); }
    }
    .contact-title {
      font-family: 'Cormorant Garamond', serif;
      font-weight: 300;
      font-size: clamp(2.8rem, 6vw, 5.5rem);
      line-height: 1.05;
      color: var(--dark);
      margin-bottom: 18px;
      position: relative;
    }
    .contact-title em { font-style: italic; color: var(--accent); }
    .contact-sub {
      font-family: 'Inter', sans-serif;
      color: var(--mid);
      font-size: 0.86rem;
      margin-bottom: 52px;
      max-width: 300px;
      margin-left: auto; margin-right: auto;
      position: relative;
    }
    .contact-channels {
      display: flex;
      justify-content: center;
      gap: 1px;
      background: var(--border);
      max-width: 680px;
      margin: 0 auto 38px;
      position: relative;
    }
    .channel-item {
      background: var(--cream);
      padding: 28px 34px;
      flex: 1; min-width: 140px;
      text-decoration: none;
      display: flex; flex-direction: column; align-items: center; gap: 8px;
      transition: background 0.4s ease, box-shadow 0.45s ease;
    }
    .channel-item:hover { background: var(--paper); box-shadow: 0 0 30px rgba(168,98,46,0.10); }
    .channel-platform { font-family: 'Inter', sans-serif; font-size: 0.58rem; letter-spacing: 0.2em; text-transform: uppercase; color: var(--mid); }
    .channel-handle {
      font-family: 'Cormorant Garamond', serif;
      font-size: 1rem;
      font-weight: 400;
      color: var(--dark);
      transition: color 0.35s ease;
    }
    .channel-item:hover .channel-handle { color: var(--accent); }
    .contact-cta {
      display: inline-flex; align-items: center; gap: 10px;
      background: var(--accent);
      color: var(--cream);
      font-family: 'Inter', sans-serif;
      font-weight: 500;
      font-size: 0.72rem;
      letter-spacing: 0.14em;
      text-transform: uppercase;
      text-decoration: none;
      padding: 17px 42px;
      position: relative;
      transition: box-shadow 0.5s ease, transform 0.35s ease;
    }
    .contact-cta:hover {
      box-shadow: 0 0 40px rgba(168,98,46,0.52), 0 8px 24px rgba(168,98,46,0.22);
      transform: translateY(-2px);
    }

    /* ── FOOTER ── */
    footer {
      border-top: 1px solid var(--border);
      padding: 24px 56px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: var(--paper);
    }
    footer p { font-family: 'Inter', sans-serif; font-size: 0.72rem; color: var(--mid); }

    /* ── REVEAL — 2s fade, super smooth ── */
    .reveal {
      opacity: 0;
      transform: translateY(16px);
      transition: opacity 2s cubic-bezier(0.22, 1, 0.36, 1),
                  transform 2s cubic-bezier(0.22, 1, 0.36, 1);
    }
    .reveal.visible { opacity: 1; transform: translateY(0); }

    /* stagger grids */
    .stagger > * {
      opacity: 0;
      transform: translateY(14px);
      transition: opacity 1.8s cubic-bezier(0.22,1,0.36,1),
                  transform 1.8s cubic-bezier(0.22,1,0.36,1);
    }
    .stagger.visible > *:nth-child(1)  { opacity:1; transform:none; transition-delay:0.00s; }
    .stagger.visible > *:nth-child(2)  { opacity:1; transform:none; transition-delay:0.10s; }
    .stagger.visible > *:nth-child(3)  { opacity:1; transform:none; transition-delay:0.20s; }
    .stagger.visible > *:nth-child(4)  { opacity:1; transform:none; transition-delay:0.30s; }
    .stagger.visible > *:nth-child(5)  { opacity:1; transform:none; transition-delay:0.40s; }
    .stagger.visible > *:nth-child(6)  { opacity:1; transform:none; transition-delay:0.50s; }
    .stagger.visible > *:nth-child(7)  { opacity:1; transform:none; transition-delay:0.60s; }
    .stagger.visible > *:nth-child(8)  { opacity:1; transform:none; transition-delay:0.70s; }

    /* hero sequential fade */
    .h-fade { opacity: 0; }
    .h-fade.go {
      animation: heroFade 2s cubic-bezier(0.22,1,0.36,1) forwards;
    }
    .h-fade:nth-child(1).go  { animation-delay: 0.0s; }
    .h-fade:nth-child(2).go  { animation-delay: 0.18s; }
    .h-fade:nth-child(3).go  { animation-delay: 0.36s; }
    .h-fade:nth-child(4).go  { animation-delay: 0.54s; }
    .h-fade:nth-child(5).go  { animation-delay: 0.72s; }
    @keyframes heroFade {
      from { opacity: 0; transform: translateY(16px); }
      to   { opacity: 1; transform: translateY(0); }
    }

    /* ── RESPONSIVE ── */
    @media (max-width: 1024px) {
      .services-grid { grid-template-columns: repeat(2, 1fr); }
      .portfolio-grid { grid-template-columns: repeat(2, 1fr); }
      .pricing-grid { grid-template-columns: 1fr; }
      .conditions-grid { grid-template-columns: repeat(2, 1fr); }
    }
    @media (max-width: 768px) {
      nav { padding: 16px 24px; }
      .nav-links { display: none; }
      .hero { grid-template-columns: 1fr; padding: 120px 24px 68px; }
      .hero::after { display: none; }
      .hero-left { padding-right: 0; }
      .hero-right { border-left: none; border-top: 1px solid var(--border); padding-left: 0; padding-top: 38px; margin-top: 38px; }
      .section { padding: 72px 24px; }
      #about { grid-template-columns: 1fr; gap: 52px; }
      .portfolio-grid { grid-template-columns: 1fr; }
      .services-grid { grid-template-columns: 1fr; }
      .conditions-grid { grid-template-columns: 1fr; }
      .packs-grid { grid-template-columns: 1fr; }
      #contact { padding: 80px 24px; }
      footer { flex-direction: column; gap: 10px; text-align: center; padding: 20px 24px; }
    }
    @media (prefers-reduced-motion: reduce) {
      .marquee-track, .hero-blob-1, .hero-blob-2, .contact-glow { animation: none; }
      .reveal, .stagger > *, .h-fade { opacity: 1; transform: none; transition: none; animation: none; }
    }
  </style>
</head>
<body>

  <!-- LOADER -->
  <div id="loader">
    <p class="loader-name">DARÍO<span>.</span></p>
  </div>

  <!-- CURSOR GLOW -->
  <div class="cursor-glow" id="cursorGlow"></div>

  <!-- NAV -->
  <nav id="mainNav">
    <a href="#" class="nav-logo">DARÍO<span>.</span></a>
    <ul class="nav-links">
      <li><a href="#portfolio">Portafolio</a></li>
      <li><a href="#pricing">Precios</a></li>
      <li><a href="#about">Enfoque</a></li>
      <li><a href="#contact" class="nav-cta">Trabajemos</a></li>
    </ul>
  </nav>

  <!-- HERO -->
  <section class="hero">
    <div class="hero-blob hero-blob-1"></div>
    <div class="hero-blob hero-blob-2"></div>
    <div class="hero-left">
      <p class="hero-eyebrow h-fade">Editor de video — Disponible</p>
      <h1 class="hero-name h-fade">DARÍO</h1>
      <p class="hero-role h-fade"><strong>Reels · Shorts · TikToks</strong></p>
      <p class="hero-desc h-fade">Transformo ideas en videos que captan atención, generan emoción y mantienen a la audiencia viendo hasta el final.</p>
      <div class="hero-actions h-fade">
        <a href="#portfolio" class="btn-primary">
          Ver portafolio
          <svg width="13" height="13" viewBox="0 0 16 16" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M3 8h10M9 4l4 4-4 4"/></svg>
        </a>
        <a href="#pricing" class="btn-ghost">Ver precios</a>
      </div>
    </div>
    <div class="hero-right h-fade" style="animation-delay:0.6s!important">
      <div class="stat-block">
        <p class="stat-num">3<span>+</span></p>
        <p class="stat-label">Estilos dominados</p>
      </div>
      <div class="stat-block">
        <p class="stat-num">100<span>%</span></p>
        <p class="stat-label">Enfocado en retención</p>
      </div>
      <div class="stat-block">
        <p class="stat-num">2</p>
        <p class="stat-label">Rondas de cambios incluidas</p>
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

  <!-- SERVICES -->
  <section class="section" id="services">
    <p class="section-eyebrow reveal">Qué hago</p>
    <h2 class="section-title reveal">Todo lo que tu video<br><em>necesita para funcionar.</em></h2>
    <div class="services-grid stagger">
      <div class="service-cell">
        <p class="service-name">Edición completa</p>
        <p class="service-desc">Reels, Shorts y TikToks con cortes precisos y ritmo que retiene.</p>
      </div>
      <div class="service-cell">
        <p class="service-name">Subtítulos personalizados</p>
        <p class="service-desc">Tipografía y animación adaptada al estilo de cada video.</p>
      </div>
      <div class="service-cell">
        <p class="service-name">Sincronización musical</p>
        <p class="service-desc">Cortes y transiciones que respiran con la música.</p>
      </div>
      <div class="service-cell">
        <p class="service-name">Hook y retención</p>
        <p class="service-desc">Apertura pensada para que nadie se vaya en los primeros 3 segundos.</p>
      </div>
      <div class="service-cell">
        <p class="service-name">Storytelling visual</p>
        <p class="service-desc">Cada segundo tiene una función. Cada corte avanza la historia.</p>
      </div>
      <div class="service-cell">
        <p class="service-name">Apoyo en guiones</p>
        <p class="service-desc">Estructura narrativa y propuesta de guion enfocada en retención.</p>
      </div>
      <div class="service-cell">
        <p class="service-name">Formato vertical nativo</p>
        <p class="service-desc">Optimizado para 9:16. Pensado para celular desde el inicio.</p>
      </div>
      <div class="service-cell">
        <p class="service-name">Entregas por Drive</p>
        <p class="service-desc">Flujo claro y organizado. Sin confusiones.</p>
      </div>
    </div>
  </section>

  <!-- PORTFOLIO -->
  <section class="section" id="portfolio">
    <p class="section-eyebrow reveal">Portafolio</p>
    <h2 class="section-title reveal">Tres estilos.<br><em>Un mismo criterio.</em></h2>
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

  <!-- PRICING -->
  <section class="section" id="pricing">
    <p class="section-eyebrow reveal">Precios</p>
    <h2 class="section-title reveal">Invertís en video.<br><em>Recibís resultados.</em></h2>
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
      </div>
    </div>
    <div>
      <p class="packs-label reveal">Paquetes mensuales</p>
      <div class="packs-grid stagger">
        <div class="pack-card">
          <div>
            <p class="pack-label">4 Videos Premium</p>
            <p class="pack-sub">Para creadores que publican semanalmente</p>
          </div>
          <p class="pack-price">$90 <span>/ mes</span></p>
        </div>
        <div class="pack-card">
          <div>
            <p class="pack-label">8 Videos Premium</p>
            <p class="pack-sub">Para creadores que publican 2 veces por semana</p>
          </div>
          <p class="pack-price">$170 <span>/ mes</span></p>
        </div>
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

  <!-- CONDITIONS -->
  <section class="section" id="conditions">
    <p class="section-eyebrow reveal">Condiciones</p>
    <h2 class="section-title reveal">Claro desde el inicio.<br><em>Sin sorpresas.</em></h2>
    <div class="conditions-grid stagger">
      <div class="condition-item">
        <p class="condition-num">01</p>
        <p class="condition-text"><strong>Máximo 2 rondas de cambios</strong> por video incluidas en el precio.</p>
      </div>
      <div class="condition-item">
        <p class="condition-num">02</p>
        <p class="condition-text"><strong>Cambios adicionales</strong> tienen costo extra acordado.</p>
      </div>
      <div class="condition-item">
        <p class="condition-num">03</p>
        <p class="condition-text"><strong>El cliente provee el material base.</strong> Yo me encargo del resto.</p>
      </div>
      <div class="condition-item">
        <p class="condition-num">04</p>
        <p class="condition-text"><strong>Entregas por Google Drive.</strong> Flujo claro y organizado.</p>
      </div>
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
    <p>© 2026 Darío. Editor de Reels, Shorts y TikToks.</p>
    <p>darioaun@gmail.com</p>
  </footer>

  <script>
    // ── Loader
    window.addEventListener('load', () => {
      setTimeout(() => {
        document.getElementById('loader').classList.add('done');
        document.querySelectorAll('.h-fade').forEach(el => el.classList.add('go'));
      }, 900);
    });

    // ── Cursor glow (lerp)
    const glow = document.getElementById('cursorGlow');
    let mx = window.innerWidth / 2, my = window.innerHeight / 2;
    let cx = mx, cy = my;
    document.addEventListener('mousemove', e => { mx = e.clientX; my = e.clientY; });
    (function animateGlow() {
      cx += (mx - cx) * 0.06;
      cy += (my - cy) * 0.06;
      glow.style.left = cx + 'px';
      glow.style.top  = cy + 'px';
      requestAnimationFrame(animateGlow);
    })();

    // ── Nav scroll
    const nav = document.getElementById('mainNav');
    window.addEventListener('scroll', () => {
      nav.classList.toggle('scrolled', window.scrollY > 50);
    }, { passive: true });

    // ── Scroll reveal (2s fade)
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(e => {
        if (e.isIntersecting) {
          e.target.classList.add('visible');
          observer.unobserve(e.target);
        }
      });
    }, { threshold: 0.07 });

    document.querySelectorAll('.reveal, .stagger').forEach(el => observer.observe(el));
  </script>

</body>
</html>
