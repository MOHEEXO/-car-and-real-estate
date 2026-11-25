<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AutoLoc · Voitures & Immobilier</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
<style>
  * { margin:0; padding:0; box-sizing:border-box; font-family:'Inter',system-ui,sans-serif; }
  :root {
    --black:#1a1512; --brown-dark:#3d2c1f; --brown:#6b4a2f;
    --brown-light:#a67c52; --brown-pale:#e8ddd0; --brown-bg:#f6f1ea;
    --white:#ffffff; --text-soft:#6e5f52; --border-light:#e3d9cc;
    --shadow-sm:0 8px 20px rgba(61,44,31,.06), 0 2px 6px rgba(61,44,31,.04);
    --shadow-hover:0 20px 30px -10px rgba(61,44,31,.18);
    --radius:20px; --gold:#c9a227;
  }
  body[data-theme="dark"] {
    --black:#f5efe8; --brown-dark:#d4b896; --brown:#c9a678;
    --brown-light:#a67c52; --brown-pale:#2a2019; --brown-bg:#1a1410;
    --white:#241d17; --text-soft:#a89078; --border-light:#3a2e24;
    --shadow-sm:0 8px 20px rgba(0,0,0,.4), 0 2px 6px rgba(0,0,0,.25);
    --shadow-hover:0 20px 30px -10px rgba(0,0,0,.6);
    --gold:#d4b048;
  }
  body { background:var(--brown-bg); color:var(--black); line-height:1.5; transition:background .3s, color .3s; }
  body.modal-open { overflow:hidden; }
  body[data-lang="ar"] { font-family:'Segoe UI','Tahoma',system-ui,sans-serif; }
  .container { max-width:1280px; margin:0 auto; padding:0 24px; }
  a { text-decoration:none; }

  @keyframes fadeIn { from{opacity:0} to{opacity:1} }
  @keyframes fadeInUp { from{opacity:0; transform:translateY(20px)} to{opacity:1; transform:none} }
  @keyframes slideUp { from{opacity:0; transform:translateY(30px)} to{opacity:1; transform:none} }
  @keyframes slideDown { from{opacity:0; transform:translateY(-10px)} to{opacity:1; transform:none} }
  @keyframes popIn { 0%{transform:scale(0)} 70%{transform:scale(1.15)} 100%{transform:scale(1)} }
  @keyframes pulse { 0%,100%{transform:scale(1)} 50%{transform:scale(1.08)} }
  @keyframes shake { 0%,100%{transform:translateX(0)} 25%{transform:translateX(-8px)} 75%{transform:translateX(8px)} }
  @keyframes ripple { 0%{transform:scale(0); opacity:.6} 100%{transform:scale(2.5); opacity:0} }
  @keyframes glow { 0%,100%{box-shadow:0 0 0 0 rgba(201,162,39,0)} 50%{box-shadow:0 0 0 8px rgba(201,162,39,.15)} }
  @keyframes float { 0%,100%{transform:translateY(0)} 50%{transform:translateY(-8px)} }
  @keyframes slideInFromLeft { from{opacity:0; transform:translateX(-20px)} to{opacity:1; transform:none} }

  .navbar {
    background:var(--white); backdrop-filter:blur(6px);
    position:sticky; top:0; z-index:100;
    border-bottom:1px solid var(--border-light);
    box-shadow:0 2px 20px rgba(61,44,31,.05);
    transition:background .3s;
  }
  .nav-container {
    display:flex; align-items:center; justify-content:space-between;
    padding:16px 24px; max-width:1280px; margin:0 auto; gap:16px;
  }
  .logo {
    display:flex; align-items:center; gap:10px;
    font-size:1.6rem; font-weight:800;
    color:var(--black); letter-spacing:-.5px; flex-shrink:0;
    transition:transform .3s;
  }
  .logo:hover { transform:scale(1.03); }
  .logo i { color:var(--brown); transition:transform .3s; }
  .logo:hover i { transform:rotate(-15deg); }
  .logo span { color:var(--brown); }

  .main-tabs {
    display:flex; gap:6px; background:var(--brown-pale);
    padding:6px; border-radius:40px; flex-wrap:wrap; justify-content:center;
  }
  .main-tab {
    padding:10px 18px; border-radius:40px; border:none;
    cursor:pointer; font-weight:700; font-size:.88rem;
    background:transparent; color:var(--brown-dark);
    transition:all .3s cubic-bezier(.2,0,0,1);
    display:flex; align-items:center; gap:8px; white-space:nowrap;
  }
  .main-tab:hover { background:rgba(255,255,255,.6); color:var(--brown); transform:translateY(-1px); }
  .main-tab.active {
    background:var(--brown); color:var(--white);
    box-shadow:0 4px 12px rgba(107,74,47,.35);
    animation:popIn .35s cubic-bezier(.2,0,0,1);
  }
  body[data-theme="dark"] .main-tab.active { color:#1a1410; }

  .nav-controls { display:flex; align-items:center; gap:10px; flex-shrink:0; }

  .theme-toggle {
    width:48px; height:48px; border-radius:50%;
    border:2px solid var(--border-light);
    background:var(--white); color:var(--brown-dark);
    cursor:pointer; display:flex; align-items:center; justify-content:center;
    font-size:1.1rem; transition:all .4s cubic-bezier(.68,-.55,.27,1.55);
    box-shadow:0 2px 6px rgba(61,44,31,.08);
    position:relative; overflow:hidden;
  }
  .theme-toggle:hover { transform:rotate(20deg) scale(1.08); }
  .theme-toggle.clicked { animation:ripple .5s ease-out; }
  .theme-toggle i { transition:transform .5s; position:relative; z-index:1; }
  .theme-toggle:hover i { transform:rotate(360deg); }
  .theme-toggle .icon-moon { display:block; }
  .theme-toggle .icon-sun { display:none; }
  body[data-theme="dark"] .theme-toggle .icon-moon { display:none; }
  body[data-theme="dark"] .theme-toggle .icon-sun {
    display:block; animation:float 2s ease-in-out infinite; color:var(--gold);
  }

  .lang-switch {
    display:flex; gap:4px; background:var(--brown-pale);
    padding:4px; border-radius:40px; flex-shrink:0;
  }
  .lang-btn {
    padding:8px 14px; border-radius:40px; border:none;
    cursor:pointer; font-weight:800; font-size:.8rem;
    background:transparent; color:var(--brown-dark);
    transition:all .35s cubic-bezier(.68,-.55,.27,1.55);
    display:flex; align-items:center; gap:6px; white-space:nowrap;
  }
  .lang-btn:hover { background:rgba(255,255,255,.7); transform:scale(1.05); }
  .lang-btn.active {
    background:var(--brown); color:var(--white);
    box-shadow:0 3px 8px rgba(107,74,47,.3);
    animation:popIn .4s cubic-bezier(.2,0,0,1);
  }
  body[data-theme="dark"] .lang-btn.active { color:#1a1410; }
  .lang-btn .flag { font-size:1rem; transition:transform .3s; }
  .lang-btn:hover .flag { transform:scale(1.2) rotate(-8deg); }

  .view { display:none; }
  .view.active { display:block; animation:fadeInUp .45s cubic-bezier(.2,0,0,1); }

  .hero {
    background:linear-gradient(135deg, var(--brown-dark) 0%, var(--brown) 60%, var(--brown-light) 100%);
    padding:80px 0 100px; color:#fff;
    position:relative; overflow:hidden;
  }
  body[data-theme="dark"] .hero {
    background:linear-gradient(135deg, #241d17 0%, #3a2e24 60%, #4a3a2a 100%);
  }
  .hero::after {
    content:''; position:absolute; right:-100px; top:-100px;
    width:400px; height:400px; border-radius:50%;
    background:rgba(255,255,255,.05); animation:float 6s ease-in-out infinite;
  }
  .hero-content { position:relative; z-index:2; max-width:720px; animation:fadeInUp .6s cubic-bezier(.2,0,0,1); }
  .hero-badge {
    display:inline-block; background:rgba(255,255,255,.15);
    border:1px solid rgba(255,255,255,.3);
    padding:6px 18px; border-radius:40px;
    font-size:.85rem; font-weight:600; margin-bottom:24px;
    backdrop-filter:blur(4px);
  }
  .hero h1 { font-size:3rem; font-weight:800; letter-spacing:-.03em; line-height:1.15; margin-bottom:20px; }
  .hero h1 span { color:var(--gold); }
  .hero p { font-size:1.15rem; opacity:.9; margin-bottom:36px; max-width:560px; }

  .cat-buttons { display:grid; grid-template-columns:repeat(3, 1fr); gap:20px; margin-top:20px; }
  .cat-btn {
    background:rgba(255,255,255,.12);
    border:2px solid rgba(255,255,255,.25);
    border-radius:var(--radius);
    padding:24px 20px; cursor:pointer;
    color:#fff; text-align:left;
    transition:all .35s cubic-bezier(.2,0,0,1);
    display:flex; flex-direction:column; gap:6px;
    backdrop-filter:blur(6px);
    animation:fadeInUp .6s cubic-bezier(.2,0,0,1) both;
  }
  .cat-btn:nth-child(1) { animation-delay:.1s; }
  .cat-btn:nth-child(2) { animation-delay:.2s; }
  .cat-btn:nth-child(3) { animation-delay:.3s; }
  .cat-btn:hover {
    background:var(--white); color:var(--brown-dark);
    transform:translateY(-6px) scale(1.02);
    box-shadow:0 20px 40px rgba(0,0,0,.25);
    border-color:var(--white);
  }
  .cat-btn-icon { font-size:1.8rem; margin-bottom:6px; transition:transform .4s cubic-bezier(.68,-.55,.27,1.55); }
  .cat-btn:hover .cat-btn-icon { color:var(--brown); transform:scale(1.15) rotate(-8deg); }
  .cat-btn-title { font-size:1.15rem; font-weight:800; }
  .cat-btn-desc { font-size:.85rem; opacity:.8; }

  .home-section { padding:60px 0 20px; }
  .home-section-header {
    display:flex; align-items:flex-end; justify-content:space-between;
    margin-bottom:32px; flex-wrap:wrap; gap:16px;
  }
  .home-section-header h2 { font-size:1.9rem; font-weight:800; letter-spacing:-.02em; }
  .home-section-header h2 i { color:var(--brown); margin-right:8px; }
  .home-section-header p { color:var(--text-soft); font-size:.95rem; margin-top:4px; }

  .pill-badge {
    background:var(--brown-pale); color:var(--brown-dark);
    padding:8px 18px; border-radius:40px; font-weight:700;
    font-size:.8rem; text-transform:uppercase; letter-spacing:.5px;
  }
  .pill-badge.gold { background:rgba(201,162,39,.15); color:#8a6d0f; animation:glow 2s ease-in-out infinite; }
  body[data-theme="dark"] .pill-badge.gold { background:rgba(212,176,72,.15); color:var(--gold); }

  .card-grid { display:grid; grid-template-columns:repeat(auto-fill, minmax(260px,1fr)); gap:24px; }
  .card {
    background:var(--white); border-radius:var(--radius); padding:22px;
    box-shadow:var(--shadow-sm); display:flex; flex-direction:column;
    transition:all .35s cubic-bezier(.2,0,0,1);
    animation:fadeInUp .4s cubic-bezier(.2,0,0,1) both;
    border:1px solid var(--border-light);
    cursor:pointer;
  }
  .card:nth-child(1) { animation-delay:.05s; }
  .card:nth-child(2) { animation-delay:.1s; }
  .card:nth-child(3) { animation-delay:.15s; }
  .card:nth-child(4) { animation-delay:.2s; }
  .card:nth-child(5) { animation-delay:.25s; }
  .card:nth-child(6) { animation-delay:.3s; }
  .card:hover { transform:translateY(-6px); box-shadow:var(--shadow-hover); border-color:var(--brown-light); }

  .card-image {
    height:160px; border-radius:14px; margin-bottom:16px;
    display:flex; align-items:center; justify-content:center;
    font-size:3.5rem; color:var(--brown-dark);
    background:linear-gradient(145deg, var(--brown-pale), var(--border-light));
    position:relative; overflow:hidden;
  }
  .card-image img {
    width:100%; height:100%;
    object-fit:cover; object-position:center;
    display:block;
    transition:transform .5s cubic-bezier(.2,0,0,1);
  }
  .card:hover .card-image img { transform:scale(1.08); }
  .card-image > i { transition:transform .4s cubic-bezier(.68,-.55,.27,1.55); }
  .card:hover .card-image > i { transform:scale(1.12) rotate(-5deg); }

  .photo-count-badge {
    position:absolute; bottom:10px; left:10px;
    background:rgba(0,0,0,.7); color:#fff;
    font-size:.7rem; font-weight:700;
    padding:4px 10px; border-radius:20px;
    display:flex; align-items:center; gap:5px;
    backdrop-filter:blur(4px);
    z-index:2;
  }
  .photo-count-badge i { font-size:.75rem; }

  .card-badge {
    position:absolute; top:10px; right:10px;
    background:var(--brown); color:var(--white);
    font-size:.7rem; font-weight:800; letter-spacing:.5px;
    padding:4px 10px; border-radius:20px; text-transform:uppercase;
    z-index:2;
  }
  body[data-theme="dark"] .card-badge { color:#1a1410; }
  .card-badge.sale { background:var(--black); color:var(--white); }
  body[data-theme="dark"] .card-badge.sale { background:#d4b896; color:#1a1410; }
  .card-badge.rank { background:var(--gold); color:#1a1410; left:10px; right:auto; animation:pulse 2s ease-in-out infinite; }

  .card-brand {
    font-size:.78rem; font-weight:700; text-transform:uppercase;
    letter-spacing:1px; color:var(--brown); margin-bottom:4px;
  }
  .card-name { font-size:1.25rem; font-weight:700; margin-bottom:6px; color:var(--black); }
  .card-cat-tag { font-size:.76rem; color:var(--text-soft); margin-bottom:12px; }
  .card-cat-tag i { color:var(--brown-light); margin-right:4px; }

  .card-specs {
    display:flex; flex-wrap:wrap; gap:10px;
    color:var(--text-soft); font-size:.87rem;
    border-top:1px solid var(--border-light);
    padding-top:14px; margin-bottom:14px;
  }
  .card-specs i { color:var(--brown); margin-right:4px; }

  .rating {
    display:flex; align-items:center; gap:4px;
    font-size:.85rem; color:var(--text-soft); margin-bottom:10px;
  }
  .rating .stars { color:var(--gold); letter-spacing:1px; }
  .rating strong { color:var(--black); font-size:.95rem; margin-right:4px; }

  .card-footer {
    display:flex; align-items:center; justify-content:space-between;
    margin-top:auto; gap:8px;
  }
  .price { font-size:1.3rem; font-weight:800; color:var(--black); display:flex; align-items:baseline; gap:2px; }
  .price small { font-size:.75rem; color:var(--text-soft); font-weight:500; margin-left:4px; }

  .btn-book {
    background:var(--white); border:2px solid var(--brown-pale);
    padding:8px 16px; border-radius:40px;
    font-weight:700; cursor:pointer; color:var(--brown-dark);
    transition:all .3s cubic-bezier(.2,0,0,1);
    font-size:.85rem; white-space:nowrap;
  }
  .btn-book:hover {
    background:var(--brown); border-color:var(--brown); color:var(--white);
    box-shadow:0 8px 16px rgba(107,74,47,.3);
    transform:translateY(-2px) scale(1.03);
  }
  body[data-theme="dark"] .btn-book:hover { color:#1a1410; }
  .btn-book.sale-btn { background:var(--black); color:var(--white); border-color:var(--black); }
  body[data-theme="dark"] .btn-book.sale-btn { background:var(--brown-dark); color:#1a1410; border-color:var(--brown-dark); }
  .btn-book.sale-btn:hover { background:var(--brown-dark); border-color:var(--brown-dark); }

  .section-title { font-size:2rem; font-weight:800; letter-spacing:-.02em; margin-bottom:8px; }
  .section-subtitle { color:var(--text-soft); margin-bottom:32px; max-width:600px; }
  .shop-layout { display:grid; grid-template-columns:280px 1fr; gap:32px; padding:40px 0 80px; }
  .tree-sidebar {
    background:var(--white); border-radius:var(--radius);
    padding:24px 16px; box-shadow:var(--shadow-sm);
    height:fit-content; position:sticky; top:90px;
    border:1px solid var(--border-light);
    animation:slideInFromLeft .5s cubic-bezier(.2,0,0,1);
  }
  .tree-sidebar h3 {
    font-size:1.1rem; font-weight:800; margin-bottom:16px;
    padding:0 8px; display:flex; align-items:center; gap:8px;
  }
  .tree-sidebar h3 i { color:var(--brown); }
  .tree { list-style:none; padding:0; }
  .tree-node { margin:2px 0; animation:slideInFromLeft .4s ease both; }
  .tree-node:nth-child(1) { animation-delay:.1s; }
  .tree-node:nth-child(2) { animation-delay:.15s; }
  .tree-node:nth-child(3) { animation-delay:.2s; }
  .tree-node-label {
    display:flex; align-items:center; gap:8px;
    padding:10px 12px; border-radius:10px;
    cursor:pointer; font-weight:500; font-size:.92rem;
    transition:all .25s cubic-bezier(.2,0,0,1);
    user-select:none; color:var(--black);
  }
  .tree-node-label:hover { background:var(--brown-pale); color:var(--brown-dark); transform:translateX(3px); }
  .tree-node-label.active {
    background:var(--brown); color:var(--white); font-weight:700;
    box-shadow:0 4px 12px rgba(107,74,47,.35);
    animation:popIn .3s cubic-bezier(.2,0,0,1);
  }
  body[data-theme="dark"] .tree-node-label.active { color:#1a1410; }
  .tree-node-label.active .arrow,
  .tree-node-label.active .count { color:var(--white); }
  body[data-theme="dark"] .tree-node-label.active .arrow,
  body[data-theme="dark"] .tree-node-label.active .count { color:#1a1410; }
  .tree-node-label.active .count { background:rgba(255,255,255,.22); }
  .tree-node-label .arrow { font-size:.7rem; width:14px; transition:transform .3s; color:var(--brown-light); }
  .tree-node.open > .tree-node-label .arrow { transform:rotate(90deg); }
  .tree-node-label .count {
    margin-left:auto; font-size:.75rem; font-weight:700;
    background:var(--brown-pale); color:var(--brown-dark);
    padding:2px 8px; border-radius:20px; min-width:22px; text-align:center;
  }
  .tree-children { list-style:none; padding-left:20px; display:none; }
  .tree-node.open > .tree-children { display:block; animation:slideDown .3s ease; }

  .empty-state {
    grid-column:1/-1; text-align:center; padding:60px 20px;
    color:var(--text-soft); background:var(--white);
    border-radius:var(--radius); border:1px dashed var(--border-light);
  }
  .empty-state i { font-size:3rem; color:var(--brown-pale); margin-bottom:16px; display:block; }

  .back-home {
    display:inline-flex; align-items:center; gap:8px;
    background:var(--white); color:var(--brown-dark);
    border:1px solid var(--border-light);
    padding:10px 20px; border-radius:40px;
    font-weight:600; font-size:.9rem; margin-top:24px;
    cursor:pointer; transition:all .3s;
  }
  .back-home:hover { background:var(--brown); color:var(--white); border-color:var(--brown); transform:translateX(-4px); }
  body[data-theme="dark"] .back-home:hover { color:#1a1410; }

  .about-section {
    padding:80px 0; background:var(--white);
    border-top:1px solid var(--border-light);
    border-bottom:1px solid var(--border-light);
  }
  .about-grid { display:grid; grid-template-columns:1fr 1fr; gap:60px; align-items:center; }
  .about-content h2 { font-size:2.2rem; font-weight:800; letter-spacing:-.02em; margin-bottom:16px; line-height:1.2; }
  .about-content h2 span { color:var(--brown); }
  .about-content > p { color:var(--text-soft); font-size:1.05rem; margin-bottom:28px; line-height:1.7; }
  .about-values { display:flex; flex-direction:column; gap:16px; margin-bottom:28px; }
  .about-value-item {
    display:flex; align-items:flex-start; gap:14px;
    animation:fadeInUp .5s cubic-bezier(.2,0,0,1) both;
  }
  .about-value-item:nth-child(1) { animation-delay:.1s; }
  .about-value-item:nth-child(2) { animation-delay:.2s; }
  .about-value-item:nth-child(3) { animation-delay:.3s; }
  .about-value-icon {
    width:44px; height:44px; border-radius:12px;
    background:var(--brown-pale); color:var(--brown);
    display:flex; align-items:center; justify-content:center;
    font-size:1.1rem; flex-shrink:0; transition:all .3s;
  }
  .about-value-item:hover .about-value-icon { background:var(--brown); color:var(--white); transform:rotate(-8deg) scale(1.05); }
  body[data-theme="dark"] .about-value-item:hover .about-value-icon { color:#1a1410; }
  .about-value-text h4 { font-size:1rem; font-weight:800; margin-bottom:2px; color:var(--black); }
  .about-value-text p { font-size:.88rem; color:var(--text-soft); line-height:1.5; }
  .about-stats {
    display:grid; grid-template-columns:repeat(3, 1fr);
    gap:16px; margin-top:24px; padding-top:24px;
    border-top:1px solid var(--border-light);
  }
  .about-stat { text-align:center; animation:fadeInUp .6s cubic-bezier(.2,0,0,1) both; }
  .about-stat:nth-child(1) { animation-delay:.2s; }
  .about-stat:nth-child(2) { animation-delay:.3s; }
  .about-stat:nth-child(3) { animation-delay:.4s; }
  .about-stat-num { font-size:2rem; font-weight:800; color:var(--brown); line-height:1; }
  .about-stat-label { font-size:.82rem; color:var(--text-soft); margin-top:6px; }
  .about-visual {
    background:linear-gradient(135deg, var(--brown-dark), var(--brown-light));
    border-radius:32px; padding:48px 32px;
    color:#fff; text-align:center;
    position:relative; overflow:hidden;
    box-shadow:0 20px 50px rgba(61,44,31,.25);
    animation:float 5s ease-in-out infinite;
  }
  .about-visual-icon { font-size:5rem; margin-bottom:16px; }
  .about-visual h3 { font-size:1.5rem; font-weight:800; margin-bottom:8px; }
  .about-visual p { opacity:.9; font-size:.95rem; }

  .contact-section {
    padding:80px 0;
    background:linear-gradient(180deg, var(--brown-bg), var(--white));
  }
  body[data-theme="dark"] .contact-section {
    background:linear-gradient(180deg, var(--brown-bg), #1e1814);
  }
  .contact-header { text-align:center; margin-bottom:48px; }
  .contact-header h2 { font-size:2.2rem; font-weight:800; letter-spacing:-.02em; margin-bottom:12px; }
  .contact-header h2 i { color:var(--brown); margin-right:8px; }
  .contact-header p { color:var(--text-soft); font-size:1.05rem; max-width:600px; margin:0 auto; }
  .contact-grid { display:grid; grid-template-columns:1fr 1.2fr; gap:40px; }
  .contact-info { display:flex; flex-direction:column; gap:20px; }
  .contact-card {
    background:var(--white); border-radius:var(--radius);
    padding:24px; border:1px solid var(--border-light);
    box-shadow:var(--shadow-sm);
    display:flex; align-items:flex-start; gap:16px;
    transition:all .3s cubic-bezier(.2,0,0,1);
    animation:fadeInUp .5s cubic-bezier(.2,0,0,1) both;
  }
  .contact-card:nth-child(1) { animation-delay:.1s; }
  .contact-card:nth-child(2) { animation-delay:.2s; }
  .contact-card:nth-child(3) { animation-delay:.3s; }
  .contact-card:hover { transform:translateY(-4px) translateX(4px); box-shadow:var(--shadow-hover); border-color:var(--brown-light); }
  .contact-card-icon {
    width:52px; height:52px; border-radius:14px;
    background:linear-gradient(135deg, var(--brown), var(--brown-light));
    color:#fff; display:flex; align-items:center; justify-content:center;
    font-size:1.3rem; flex-shrink:0;
    transition:transform .4s cubic-bezier(.68,-.55,.27,1.55);
  }
  .contact-card:hover .contact-card-icon { transform:rotate(-10deg) scale(1.1); }
  .contact-card-body h4 { font-size:.82rem; text-transform:uppercase; letter-spacing:1px; color:var(--brown); margin-bottom:4px; font-weight:700; }
  .contact-card-body p { font-size:1.05rem; font-weight:700; color:var(--black); margin-bottom:2px; }
  .contact-card-body small { font-size:.85rem; color:var(--text-soft); }

  .contact-form-wrap {
    background:var(--white); border-radius:var(--radius);
    padding:32px; border:1px solid var(--border-light);
    box-shadow:var(--shadow-sm);
    animation:fadeInUp .6s cubic-bezier(.2,0,0,1) both;
    animation-delay:.2s;
  }
  .contact-form-wrap h3 { font-size:1.3rem; font-weight:800; margin-bottom:8px; color:var(--black); }
  .contact-form-wrap > p { color:var(--text-soft); font-size:.9rem; margin-bottom:24px; }
  .contact-form-row { display:grid; grid-template-columns:1fr 1fr; gap:16px; }
  .contact-form-group { margin-bottom:16px; }
  .contact-form-group label { display:block; font-weight:700; font-size:.85rem; margin-bottom:6px; color:var(--black); }
  .contact-form-group label .req { color:#c0392b; margin-left:3px; }
  .contact-form-group input,
  .contact-form-group textarea,
  .contact-form-group select {
    width:100%; padding:12px 14px;
    border:2px solid var(--border-light); border-radius:10px;
    font-size:.92rem; font-family:inherit;
    background:var(--white); color:var(--black);
    transition:all .2s;
  }
  .contact-form-group input:focus,
  .contact-form-group textarea:focus,
  .contact-form-group select:focus {
    outline:none; border-color:var(--brown);
    box-shadow:0 0 0 4px rgba(107,74,47,.12);
    transform:translateY(-1px);
  }
  .contact-form-group textarea { resize:vertical; min-height:100px; }
  .contact-form-group.error input,
  .contact-form-group.error textarea,
  .contact-form-group.error select { border-color:#c0392b; background:rgba(192,57,43,.04); animation:shake .4s; }
  .contact-form-group .err-msg { display:none; color:#c0392b; font-size:.76rem; font-weight:600; margin-top:4px; }
  .contact-form-group.error .err-msg { display:block; }
  .contact-submit {
    width:100%; background:var(--brown); color:var(--white);
    border:none; padding:14px; border-radius:40px;
    font-weight:800; font-size:1rem; cursor:pointer;
    transition:all .3s cubic-bezier(.2,0,0,1);
    box-shadow:0 8px 20px rgba(107,74,47,.3);
    display:flex; align-items:center; justify-content:center; gap:10px;
    margin-top:8px;
  }
  body[data-theme="dark"] .contact-submit { color:#1a1410; }
  .contact-submit:hover { background:var(--brown-dark); transform:translateY(-2px); box-shadow:0 12px 26px rgba(107,74,47,.4); }
  .contact-submit:disabled { opacity:.7; cursor:not-allowed; transform:none; }
  .contact-success { text-align:center; padding:32px 16px; animation:popIn .5s cubic-bezier(.2,0,0,1); }
  .contact-success i { font-size:3.5rem; color:#4caf50; margin-bottom:16px; animation:pulse 1s ease-in-out; }
  .contact-success h4 { font-size:1.3rem; font-weight:800; margin-bottom:8px; color:var(--black); }
  .contact-success p { color:var(--text-soft); font-size:.92rem; }

  .modal-overlay {
    position:fixed; inset:0; background:rgba(26,21,18,.65);
    backdrop-filter:blur(6px);
    display:none; align-items:center; justify-content:center;
    z-index:1000; padding:24px;
    animation:fadeIn .25s ease;
  }
  .modal-overlay.active { display:flex; }
  .modal {
    background:var(--white); border-radius:28px;
    max-width:820px; width:100%; max-height:90vh;
    overflow-y:auto; position:relative;
    box-shadow:0 30px 60px rgba(0,0,0,.4);
    animation:slideUp .35s cubic-bezier(.2,0,0,1);
    transition:background .3s;
  }
  .modal-close {
    position:absolute; top:18px; right:18px;
    width:42px; height:42px; border-radius:50%;
    border:none; background:var(--white);
    color:var(--brown-dark); font-size:1.2rem;
    cursor:pointer; z-index:10;
    display:flex; align-items:center; justify-content:center;
    box-shadow:0 6px 16px rgba(0,0,0,.15);
    transition:all .35s cubic-bezier(.68,-.55,.27,1.55);
  }
  .modal-close:hover { background:var(--brown); color:var(--white); transform:rotate(90deg) scale(1.1); }

  /* ⭐ MODAL HERO avec galerie */
  .modal-hero {
    height:340px; position:relative; overflow:hidden;
    display:flex; align-items:center; justify-content:center;
    background:linear-gradient(135deg, var(--brown-dark), var(--brown));
    border-radius:28px 28px 0 0;
  }
  .modal-hero > i.fas { font-size:6rem; color:#fff; }
  .modal-hero img#galleryMainImg {
    width:100%; height:100%;
    object-fit:cover; object-position:center;
    display:block;
    animation:fadeIn .3s ease;
  }
  .modal-hero .fallback-icon {
    display:none; font-size:6rem; color:#fff;
  }

  .gallery-nav {
    position:absolute; top:50%; transform:translateY(-50%);
    width:44px; height:44px; border-radius:50%;
    background:rgba(255,255,255,.92);
    border:none; cursor:pointer;
    color:var(--brown-dark); font-size:1.1rem;
    display:flex; align-items:center; justify-content:center;
    box-shadow:0 4px 12px rgba(0,0,0,.2);
    transition:all .25s;
    z-index:3;
  }
  .gallery-nav:hover {
    background:var(--brown); color:#fff;
    transform:translateY(-50%) scale(1.1);
  }
  .gallery-nav.prev { left:16px; }
  .gallery-nav.next { right:16px; }

  .gallery-counter {
    position:absolute; top:16px; right:16px;
    background:rgba(0,0,0,.65); color:#fff;
    padding:6px 14px; border-radius:20px;
    font-size:.85rem; font-weight:700;
    backdrop-filter:blur(4px);
    z-index:3;
  }

  .gallery-thumbs {
    position:absolute; bottom:16px; left:50%;
    transform:translateX(-50%);
    display:flex; gap:8px; padding:8px 12px;
    background:rgba(0,0,0,.5);
    border-radius:16px;
    backdrop-filter:blur(8px);
    z-index:3;
    max-width:calc(100% - 32px);
    overflow-x:auto;
  }
  .gallery-thumb {
    width:56px; height:44px; border-radius:8px;
    object-fit:cover; cursor:pointer;
    border:2px solid transparent;
    transition:all .2s;
    opacity:.7;
    flex-shrink:0;
  }
  .gallery-thumb:hover { opacity:1; transform:scale(1.05); }
  .gallery-thumb.active {
    border-color:var(--gold);
    opacity:1;
    transform:scale(1.05);
  }

  .modal-badge {
    position:absolute; top:16px; left:16px;
    background:rgba(255,255,255,.95); color:var(--brown-dark);
    padding:6px 16px; border-radius:20px;
    font-size:.8rem; font-weight:800; text-transform:uppercase; letter-spacing:.5px;
    z-index:3;
  }

  .modal-body { padding:32px; }
  .modal-title-row {
    display:flex; justify-content:space-between; align-items:flex-start;
    flex-wrap:wrap; gap:16px; margin-bottom:24px;
  }
  .modal-title { font-size:1.9rem; font-weight:800; letter-spacing:-.02em; }
  .modal-brand { color:var(--brown); font-weight:700; font-size:.9rem; text-transform:uppercase; letter-spacing:1px; margin-bottom:6px; }
  .modal-price { font-size:1.7rem; font-weight:800; color:var(--black); white-space:nowrap; }
  .modal-price small { font-size:.9rem; color:var(--text-soft); font-weight:500; }

  .modal-rating {
    display:flex; align-items:center; gap:8px;
    margin-bottom:24px; padding-bottom:24px;
    border-bottom:1px solid var(--border-light);
    color:var(--text-soft); font-size:.95rem;
  }
  .modal-rating .stars { color:var(--gold); font-size:1.1rem; letter-spacing:2px; }
  .modal-rating strong { color:var(--black); font-size:1.1rem; }

  .modal-section { margin-bottom:28px; }
  .modal-section h4 {
    font-size:1.15rem; font-weight:800;
    margin-bottom:16px; color:var(--black);
    display:flex; align-items:center; gap:8px;
  }
  .modal-section h4 i { color:var(--brown); }

  .features-grid { display:grid; grid-template-columns:repeat(auto-fill, minmax(160px,1fr)); gap:12px; }
  .feature-chip {
    background:var(--brown-pale); color:var(--brown-dark);
    padding:12px 16px; border-radius:12px;
    display:flex; align-items:center; gap:10px;
    font-size:.9rem; font-weight:600;
    transition:all .3s;
  }
  .feature-chip:hover { transform:translateY(-2px); box-shadow:0 4px 12px rgba(107,74,47,.15); }
  .feature-chip i { color:var(--brown); font-size:1rem; }

  .poi-list { display:flex; flex-direction:column; gap:10px; }
  .poi-item {
    display:flex; align-items:center; gap:14px;
    padding:14px 18px; border-radius:12px;
    background:var(--brown-pale); border:1px solid var(--border-light);
    transition:all .3s;
    animation:slideInFromLeft .4s ease both;
  }
  .poi-item:nth-child(1) { animation-delay:.05s; }
  .poi-item:nth-child(2) { animation-delay:.1s; }
  .poi-item:nth-child(3) { animation-delay:.15s; }
  .poi-item:nth-child(4) { animation-delay:.2s; }
  .poi-item:nth-child(5) { animation-delay:.25s; }
  .poi-item:hover { background:var(--border-light); border-color:var(--brown-light); transform:translateX(4px); }
  .poi-icon {
    width:44px; height:44px; border-radius:12px;
    background:var(--white); color:var(--brown);
    display:flex; align-items:center; justify-content:center;
    font-size:1.15rem; flex-shrink:0;
    box-shadow:0 2px 6px rgba(61,44,31,.06);
  }
  .poi-info { flex:1; }
  .poi-name { font-weight:700; font-size:.95rem; color:var(--black); }
  .poi-sub { font-size:.8rem; color:var(--text-soft); }
  .poi-distance {
    font-weight:800; font-size:1rem; color:var(--brown);
    white-space:nowrap;
    background:var(--white); padding:6px 14px; border-radius:20px;
    border:1px solid var(--border-light);
  }
  .poi-distance.close { color:#2d7a2d; border-color:#c5e0c5; background:#f0f8f0; }
  .poi-distance.medium { color:#b08200; border-color:#e8d9a8; background:#fdf8e8; }
  .poi-distance.far { color:#a04242; border-color:#e8c5c5; background:#fdf0f0; }
  body[data-theme="dark"] .poi-distance.close { color:#7fd47f; border-color:#2d5a2d; background:#1a2e1a; }
  body[data-theme="dark"] .poi-distance.medium { color:#e8c26a; border-color:#5a4a1a; background:#2e2a1a; }
  body[data-theme="dark"] .poi-distance.far { color:#e88a8a; border-color:#5a2d2d; background:#2e1a1a; }

  .modal-description {
    color:var(--text-soft); line-height:1.7;
    background:var(--brown-pale); padding:18px; border-radius:12px;
    border-left:4px solid var(--brown);
    font-size:.95rem;
  }

  .modal-actions {
    display:flex; gap:12px; margin-top:32px;
    padding-top:24px; border-top:1px solid var(--border-light);
  }
  .modal-actions .btn-primary {
    flex:1; background:var(--brown); color:var(--white);
    border:none; padding:16px; border-radius:40px;
    font-weight:800; font-size:1rem; cursor:pointer;
    transition:all .3s cubic-bezier(.2,0,0,1);
    box-shadow:0 8px 20px rgba(107,74,47,.3);
  }
  body[data-theme="dark"] .modal-actions .btn-primary { color:#1a1410; }
  .modal-actions .btn-primary:hover { background:var(--brown-dark); transform:translateY(-2px); box-shadow:0 12px 26px rgba(107,74,47,.4); }
  .modal-actions .btn-secondary {
    background:var(--white); color:var(--brown-dark);
    border:2px solid var(--border-light); padding:16px 24px;
    border-radius:40px; font-weight:700; cursor:pointer;
    transition:all .3s;
  }
  .modal-actions .btn-secondary:hover { border-color:var(--brown); color:var(--brown); transform:translateY(-2px); }

  .order-modal { max-width:560px; }
  .order-hero {
    padding:32px 32px 24px;
    background:linear-gradient(135deg, var(--brown-dark), var(--brown));
    color:#fff; border-radius:28px 28px 0 0;
    position:relative;
  }
  body[data-theme="dark"] .order-hero { background:linear-gradient(135deg, #3a2e24, #4a3a2a); }
  .order-hero h2 { font-size:1.6rem; font-weight:800; margin-bottom:6px; }
  .order-hero p { opacity:.85; font-size:.92rem; }
  .order-hero .order-item-name {
    display:inline-flex; align-items:center; gap:8px;
    background:rgba(255,255,255,.15); padding:6px 14px;
    border-radius:20px; font-weight:700; font-size:.85rem;
    margin-top:10px; backdrop-filter:blur(4px);
  }
  .order-body { padding:32px; }
  .form-group { margin-bottom:20px; }
  .form-group label { display:block; font-weight:700; font-size:.88rem; margin-bottom:8px; color:var(--black); }
  .form-group label .req { color:#c0392b; margin-left:3px; }
  .form-group input,
  .form-group select {
    width:100%; padding:13px 16px;
    border:2px solid var(--border-light); border-radius:12px;
    font-size:.95rem; font-family:inherit;
    background:var(--white); color:var(--black);
    transition:all .2s;
  }
  .form-group input:focus,
  .form-group select:focus {
    outline:none; border-color:var(--brown);
    box-shadow:0 0 0 4px rgba(107,74,47,.12);
  }
  .form-group input::placeholder { color:#b8a894; }
  body[data-theme="dark"] .form-group input::placeholder { color:#7a6a5a; }
  .form-group select { cursor:pointer; }
  .form-group.error input,
  .form-group.error select { border-color:#c0392b; background:rgba(192,57,43,.04); animation:shake .4s; }
  .form-group .error-msg { display:none; color:#c0392b; font-size:.78rem; font-weight:600; margin-top:6px; }
  .form-group.error .error-msg { display:block; }
  .form-hint { font-size:.78rem; color:var(--text-soft); margin-top:6px; display:flex; align-items:center; gap:6px; }
  .form-hint i { color:var(--brown); }

  .order-submit {
    width:100%; background:var(--brown); color:var(--white);
    border:none; padding:16px; border-radius:40px;
    font-weight:800; font-size:1rem; cursor:pointer;
    transition:all .3s cubic-bezier(.2,0,0,1);
    box-shadow:0 8px 20px rgba(107,74,47,.3);
    display:flex; align-items:center; justify-content:center; gap:10px;
    margin-top:8px;
  }
  body[data-theme="dark"] .order-submit { color:#1a1410; }
  .order-submit:hover { background:var(--brown-dark); transform:translateY(-2px); box-shadow:0 12px 26px rgba(107,74,47,.4); }
  .order-submit:disabled { opacity:.6; cursor:not-allowed; transform:none; }

  .order-success { text-align:center; padding:48px 32px; animation:fadeIn .4s ease; }
  .order-success-icon {
    width:90px; height:90px; border-radius:50%;
    background:linear-gradient(135deg, #4caf50, #388e3c);
    color:#fff; font-size:2.5rem;
    display:flex; align-items:center; justify-content:center;
    margin:0 auto 24px;
    animation:popIn .5s cubic-bezier(.2,0,0,1);
    box-shadow:0 12px 30px rgba(76,175,80,.35);
  }
  .order-success h3 { font-size:1.6rem; font-weight:800; margin-bottom:12px; }
  .order-success p { color:var(--text-soft); margin-bottom:24px; line-height:1.6; }
  .order-success .order-ref {
    display:inline-block; background:var(--brown-pale);
    color:var(--brown-dark); padding:10px 24px;
    border-radius:20px; font-weight:800; font-size:1.1rem;
    letter-spacing:1px; margin-bottom:24px;
  }
  .order-success .btn-primary {
    background:var(--brown); color:var(--white);
    border:none; padding:14px 32px; border-radius:40px;
    font-weight:800; cursor:pointer; transition:.2s;
  }
  body[data-theme="dark"] .order-success .btn-primary { color:#1a1410; }
  .order-success .btn-primary:hover { background:var(--brown-dark); }

  .footer { background:var(--white); padding:48px 0 24px; border-top:1px solid var(--border-light); }
  .footer-grid { display:grid; grid-template-columns:2fr 1fr 1fr 1fr; gap:40px; margin-bottom:32px; }
  .footer-brand .logo { font-size:1.4rem; margin-bottom:12px; }
  .footer-brand p { color:var(--text-soft); font-size:.88rem; max-width:260px; margin-bottom:16px; }
  .footer-social { display:flex; gap:10px; }
  .footer-social a {
    width:38px; height:38px; border-radius:50%;
    background:var(--brown-pale); color:var(--brown-dark);
    display:flex; align-items:center; justify-content:center;
    transition:all .3s;
  }
  .footer-social a:hover { background:var(--brown); color:var(--white); transform:translateY(-3px) rotate(-8deg); }
  body[data-theme="dark"] .footer-social a:hover { color:#1a1410; }
  .footer-col h5 { font-size:.95rem; font-weight:800; margin-bottom:14px; color:var(--black); }
  .footer-col ul { list-style:none; }
  .footer-col ul li { margin-bottom:10px; }
  .footer-col ul li a {
    color:var(--text-soft); font-size:.88rem;
    transition:all .2s; display:inline-flex; align-items:center; gap:6px;
  }
  .footer-col ul li a:hover { color:var(--brown); transform:translateX(3px); }
  .footer-bottom { text-align:center; padding-top:24px; border-top:1px solid var(--border-light); color:var(--text-soft); font-size:.85rem; }

  @media (max-width:1060px) {
    .nav-container { flex-wrap:wrap; }
    .main-tabs { order:4; width:100%; }
    .nav-controls { order:3; }
  }
  @media (max-width:960px) {
    .cat-buttons { grid-template-columns:1fr; }
    .hero h1 { font-size:2.2rem; }
    .about-grid, .contact-grid { grid-template-columns:1fr; gap:32px; }
    .footer-grid { grid-template-columns:1fr 1fr; }
  }
  @media (max-width:860px) {
    .shop-layout { grid-template-columns:1fr; }
    .tree-sidebar { position:static; }
  }
  @media (max-width:600px) {
    .modal-body { padding:22px; }
    .modal-title { font-size:1.5rem; }
    .modal-price { font-size:1.3rem; }
    .modal-hero { height:240px; }
    .gallery-nav { width:36px; height:36px; font-size:.9rem; }
    .gallery-thumb { width:44px; height:36px; }
    .features-grid { grid-template-columns:1fr 1fr; }
    .modal-actions { flex-direction:column; }
    .lang-btn span.lang-label { display:none; }
    .order-body { padding:22px; }
    .order-hero { padding:24px 22px 20px; }
    .theme-toggle { width:42px; height:42px; }
    .contact-form-row { grid-template-columns:1fr; }
    .footer-grid { grid-template-columns:1fr; gap:28px; }
    .about-stats { grid-template-columns:1fr; }
    .contact-grid { gap:24px; }
  }
</style>
</head>
<body data-lang="fr" data-theme="light">

<!-- NAVBAR -->
<header class="navbar">
  <div class="nav-container">
    <a href="#" class="logo" data-goto="home"><i class="fas fa-home"></i> Auto<span>Loc</span></a>
    <div class="main-tabs" id="mainTabs">
      <button class="main-tab active" data-goto="home"><i class="fas fa-house"></i> <span data-i18n="nav.home">Accueil</span></button>
      <button class="main-tab" data-goto="cars"><i class="fas fa-car"></i> <span data-i18n="nav.cars">Voitures</span></button>
      <button class="main-tab" data-goto="rent"><i class="fas fa-key"></i> <span data-i18n="nav.rent">Immo Location</span></button>
      <button class="main-tab" data-goto="sale"><i class="fas fa-tag"></i> <span data-i18n="nav.sale">Immo Vente</span></button>
      <button class="main-tab" data-goto="contact"><i class="fas fa-envelope"></i> <span data-i18n="nav.contact">Contact</span></button>
    </div>
    <div class="nav-controls">
      <button class="theme-toggle" id="themeToggle" aria-label="Changer thème">
        <i class="fas fa-moon icon-moon"></i>
        <i class="fas fa-sun icon-sun"></i>
      </button>
      <div class="lang-switch" id="langSwitch">
        <button class="lang-btn active" data-lang="fr"><span class="flag">🇫🇷</span><span class="lang-label">FR</span></button>
        <button class="lang-btn" data-lang="ar"><span class="flag">🇩🇿</span><span class="lang-label">دارجة</span></button>
        <button class="lang-btn" data-lang="en"><span class="flag">🇬🇧</span><span class="lang-label">EN</span></button>
      </div>
    </div>
  </div>
</header>

<!-- HOME -->
<div class="view active" id="view-home">
  <section class="hero">
    <div class="container">
      <div class="hero-content">
        <span class="hero-badge"><i class="fas fa-star" style="color:var(--gold);margin-right:6px;"></i> <span data-i18n="hero.badge">+ de 500 biens & véhicules vérifiés</span></span>
        <h1><span data-i18n="hero.title1">Louez une voiture ou trouvez</span> <span data-i18n="hero.title2">votre futur chez-vous</span></h1>
        <p data-i18n="hero.desc">Trois univers, un seul endroit.</p>
        <div class="cat-buttons">
          <button class="cat-btn" data-goto="cars">
            <i class="fas fa-car cat-btn-icon"></i>
            <div class="cat-btn-title" data-i18n="cat.cars">Location Voitures</div>
            <div class="cat-btn-desc" data-i18n="cat.cars.desc">Citadines, SUV, électriques…</div>
          </button>
          <button class="cat-btn" data-goto="rent">
            <i class="fas fa-key cat-btn-icon"></i>
            <div class="cat-btn-title" data-i18n="cat.rent">Immo · Location</div>
            <div class="cat-btn-desc" data-i18n="cat.rent.desc">Apparts, villas, bureaux…</div>
          </button>
          <button class="cat-btn" data-goto="sale">
            <i class="fas fa-tag cat-btn-icon"></i>
            <div class="cat-btn-title" data-i18n="cat.sale">Immo · Vente</div>
            <div class="cat-btn-desc" data-i18n="cat.sale.desc">Maisons, terrains, studios…</div>
          </button>
        </div>
      </div>
    </div>
  </section>

  <section class="home-section">
    <div class="container">
      <div class="home-section-header">
        <div>
          <h2><i class="fas fa-trophy"></i> <span data-i18n="home.topRated">Les mieux notés</span></h2>
          <p data-i18n="home.topRated.desc">Cliquez sur une carte pour voir les détails.</p>
        </div>
        <span class="pill-badge gold"><i class="fas fa-star"></i> <span data-i18n="home.topRated.badge">Top 3</span></span>
      </div>
      <div class="card-grid" id="topRatedGrid"></div>
    </div>
  </section>

  <section class="home-section" style="padding-bottom:80px;">
    <div class="container">
      <div class="home-section-header">
        <div>
          <h2><i class="fas fa-fire"></i> <span data-i18n="home.best">Les plus populaires</span></h2>
          <p data-i18n="home.best.desc">Les offres les plus réservées ce mois-ci.</p>
        </div>
        <span class="pill-badge"><i class="fas fa-arrow-trend-up"></i> <span data-i18n="home.best.badge">Best-sellers</span></span>
      </div>
      <div class="card-grid" id="bestSellerGrid"></div>
    </div>
  </section>

  <section class="about-section">
    <div class="container">
      <div class="about-grid">
        <div class="about-content">
          <h2><span data-i18n="about.title1">À propos de</span> <span>AutoLoc</span></h2>
          <p data-i18n="about.desc">Depuis plus de 10 ans, nous accompagnons les Algériens dans leurs projets de mobilité et d'immobilier.</p>
          <div class="about-values">
            <div class="about-value-item">
              <div class="about-value-icon"><i class="fas fa-shield-halved"></i></div>
              <div class="about-value-text">
                <h4 data-i18n="about.v1.title">Confiance & transparence</h4>
                <p data-i18n="about.v1.desc">Des prix clairs, des contrats simples, zéro frais caché.</p>
              </div>
            </div>
            <div class="about-value-item">
              <div class="about-value-icon"><i class="fas fa-bolt"></i></div>
              <div class="about-value-text">
                <h4 data-i18n="about.v2.title">Rapidité & efficacité</h4>
                <p data-i18n="about.v2.desc">Réservation en ligne en moins de 3 minutes, 7j/7.</p>
              </div>
            </div>
            <div class="about-value-item">
              <div class="about-value-icon"><i class="fas fa-headset"></i></div>
              <div class="about-value-text">
                <h4 data-i18n="about.v3.title">Support 24/7</h4>
                <p data-i18n="about.v3.desc">Une équipe dédiée, joignable à tout moment.</p>
              </div>
            </div>
          </div>
          <div class="about-stats">
            <div class="about-stat">
              <div class="about-stat-num">+500</div>
              <div class="about-stat-label" data-i18n="about.stat1">Biens & véhicules</div>
            </div>
            <div class="about-stat">
              <div class="about-stat-num">98%</div>
              <div class="about-stat-label" data-i18n="about.stat2">Clients satisfaits</div>
            </div>
            <div class="about-stat">
              <div class="about-stat-num">10+</div>
              <div class="about-stat-label" data-i18n="about.stat3">Années d'expérience</div>
            </div>
          </div>
        </div>
        <div class="about-visual">
          <i class="fas fa-handshake about-visual-icon"></i>
          <h3 data-i18n="about.visual.title">Notre engagement</h3>
          <p data-i18n="about.visual.desc">Votre satisfaction est notre priorité absolue.</p>
        </div>
      </div>
    </div>
  </section>

  <section class="contact-section">
    <div class="container">
      <div class="contact-header">
        <h2><i class="fas fa-envelope"></i> <span data-i18n="contact.title">Contactez-nous</span></h2>
        <p data-i18n="contact.desc">Une question, un projet ? Notre équipe vous répond en moins de 24h.</p>
      </div>
      <div class="contact-grid">
        <div class="contact-info">
          <div class="contact-card">
            <div class="contact-card-icon"><i class="fas fa-phone"></i></div>
            <div class="contact-card-body">
              <h4 data-i18n="contact.phone">Téléphone</h4>
              <p>+213 555 12 34 56</p>
              <small data-i18n="contact.phone.sub">Lun-Sam · 8h - 20h</small>
            </div>
          </div>
          <div class="contact-card">
            <div class="contact-card-icon"><i class="fas fa-envelope"></i></div>
            <div class="contact-card-body">
              <h4 data-i18n="contact.email">Email</h4>
              <p>contact@autoloc.dz</p>
              <small data-i18n="contact.email.sub">Réponse sous 24h</small>
            </div>
          </div>
          <div class="contact-card">
            <div class="contact-card-icon"><i class="fas fa-location-dot"></i></div>
            <div class="contact-card-body">
              <h4 data-i18n="contact.address">Adresse</h4>
              <p data-i18n="contact.address.val">12 Rue Didouche Mourad, Alger</p>
              <small data-i18n="contact.address.sub">Algérie</small>
            </div>
          </div>
        </div>
        <div class="contact-form-wrap" id="contactFormWrap"></div>
      </div>
    </div>
  </section>
</div>

<!-- CARS -->
<div class="view" id="view-cars">
  <div class="container">
    <div class="shop-layout">
      <aside class="tree-sidebar">
        <h3><i class="fas fa-sitemap"></i> <span data-i18n="tree.title">Catégories</span></h3>
        <ul class="tree" id="carsTree"></ul>
      </aside>
      <main>
        <h1 class="section-title" data-i18n="cars.title">Location de Voitures</h1>
        <p class="section-subtitle" id="carsInfo"></p>
        <div class="card-grid" id="carsGrid"></div>
        <button class="back-home" data-goto="home"><i class="fas fa-arrow-left"></i> <span data-i18n="back.home">Retour à l'accueil</span></button>
      </main>
    </div>
  </div>
</div>

<!-- RENT -->
<div class="view" id="view-rent">
  <div class="container">
    <div class="shop-layout">
      <aside class="tree-sidebar">
        <h3><i class="fas fa-sitemap"></i> <span data-i18n="tree.title">Catégories</span></h3>
        <ul class="tree" id="rentTree"></ul>
      </aside>
      <main>
        <h1 class="section-title" data-i18n="rent.title">Immobilier · À Louer</h1>
        <p class="section-subtitle" id="rentInfo"></p>
        <div class="card-grid" id="rentGrid"></div>
        <button class="back-home" data-goto="home"><i class="fas fa-arrow-left"></i> <span data-i18n="back.home">Retour à l'accueil</span></button>
      </main>
    </div>
  </div>
</div>

<!-- SALE -->
<div class="view" id="view-sale">
  <div class="container">
    <div class="shop-layout">
      <aside class="tree-sidebar">
        <h3><i class="fas fa-sitemap"></i> <span data-i18n="tree.title">Catégories</span></h3>
        <ul class="tree" id="saleTree"></ul>
      </aside>
      <main>
        <h1 class="section-title" data-i18n="sale.title">Immobilier · À Vendre</h1>
        <p class="section-subtitle" id="saleInfo"></p>
        <div class="card-grid" id="saleGrid"></div>
        <button class="back-home" data-goto="home"><i class="fas fa-arrow-left"></i> <span data-i18n="back.home">Retour à l'accueil</span></button>
      </main>
    </div>
  </div>
</div>

<!-- CONTACT -->
<div class="view" id="view-contact">
  <div class="container" style="padding-top:40px;">
    <div class="contact-header">
      <h2><i class="fas fa-envelope"></i> <span data-i18n="contact.title">Contactez-nous</span></h2>
      <p data-i18n="contact.desc">Une question, un projet ? Notre équipe vous répond en moins de 24h.</p>
    </div>
    <div class="contact-grid" style="padding-bottom:60px;">
      <div class="contact-info">
        <div class="contact-card">
          <div class="contact-card-icon"><i class="fas fa-phone"></i></div>
          <div class="contact-card-body">
            <h4 data-i18n="contact.phone">Téléphone</h4>
            <p>+213 555 12 34 56</p>
            <small data-i18n="contact.phone.sub">Lun-Sam · 8h - 20h</small>
          </div>
        </div>
        <div class="contact-card">
          <div class="contact-card-icon"><i class="fas fa-envelope"></i></div>
          <div class="contact-card-body">
            <h4 data-i18n="contact.email">Email</h4>
            <p>contact@autoloc.dz</p>
            <small data-i18n="contact.email.sub">Réponse sous 24h</small>
          </div>
        </div>
        <div class="contact-card">
          <div class="contact-card-icon"><i class="fas fa-location-dot"></i></div>
          <div class="contact-card-body">
            <h4 data-i18n="contact.address">Adresse</h4>
            <p data-i18n="contact.address.val">12 Rue Didouche Mourad, Alger</p>
            <small data-i18n="contact.address.sub">Algérie</small>
          </div>
        </div>
      </div>
      <div class="contact-form-wrap" id="contactFormWrap2"></div>
    </div>
    <button class="back-home" data-goto="home"><i class="fas fa-arrow-left"></i> <span data-i18n="back.home">Retour à l'accueil</span></button>
  </div>
</div>

<!-- MODALS -->
<div class="modal-overlay" id="detailModal">
  <div class="modal" id="detailModalContent"></div>
</div>
<div class="modal-overlay" id="orderModal">
  <div class="modal order-modal" id="orderModalContent"></div>
</div>

<!-- FOOTER -->
<footer class="footer">
  <div class="container">
    <div class="footer-grid">
      <div class="footer-brand">
        <a href="#" class="logo" data-goto="home"><i class="fas fa-home"></i> Auto<span>Loc</span></a>
        <p data-i18n="footer.desc">Location de voitures, immobilier à louer et à vendre — partout en Algérie.</p>
        <div class="footer-social">
          <a href="#"><i class="fab fa-facebook-f"></i></a>
          <a href="#"><i class="fab fa-instagram"></i></a>
          <a href="#"><i class="fab fa-twitter"></i></a>
          <a href="#"><i class="fab fa-linkedin-in"></i></a>
        </div>
      </div>
      <div class="footer-col">
        <h5 data-i18n="footer.explore">Explorer</h5>
        <ul>
          <li><a href="#" data-goto="cars"><i class="fas fa-chevron-right" style="font-size:.6rem;"></i> <span data-i18n="nav.cars">Voitures</span></a></li>
          <li><a href="#" data-goto="rent"><i class="fas fa-chevron-right" style="font-size:.6rem;"></i> <span data-i18n="nav.rent">Immo Location</span></a></li>
          <li><a href="#" data-goto="sale"><i class="fas fa-chevron-right" style="font-size:.6rem;"></i> <span data-i18n="nav.sale">Immo Vente</span></a></li>
        </ul>
      </div>
      <div class="footer-col">
        <h5 data-i18n="footer.company">Entreprise</h5>
        <ul>
          <li><a href="#" data-goto="home"><i class="fas fa-chevron-right" style="font-size:.6rem;"></i> <span data-i18n="footer.about">À propos</span></a></li>
          <li><a href="#" data-goto="contact"><i class="fas fa-chevron-right" style="font-size:.6rem;"></i> <span data-i18n="nav.contact">Contact</span></a></li>
        </ul>
      </div>
      <div class="footer-col">
        <h5 data-i18n="footer.contact">Contact</h5>
        <ul>
          <li><a href="#"><i class="fas fa-phone" style="font-size:.7rem;"></i> +213 555 12 34 56</a></li>
          <li><a href="#"><i class="fas fa-envelope" style="font-size:.7rem;"></i> contact@autoloc.dz</a></li>
        </ul>
      </div>
    </div>
    <div class="footer-bottom">
      <p>© 2025 AutoLoc · <span data-i18n="footer.rights">Tous droits réservés</span></p>
    </div>
  </div>
</footer>

<script>
/* ============================================================
   0. THEME
   ============================================================ */
let currentTheme = localStorage.getItem('autoloc_theme') || 'light';
document.body.dataset.theme = currentTheme;
const themeToggle = document.getElementById('themeToggle');
themeToggle.addEventListener('click', () => {
  currentTheme = currentTheme === 'light' ? 'dark' : 'light';
  document.body.dataset.theme = currentTheme;
  localStorage.setItem('autoloc_theme', currentTheme);
  themeToggle.classList.remove('clicked');
  void themeToggle.offsetWidth;
  themeToggle.classList.add('clicked');
  setTimeout(() => themeToggle.classList.remove('clicked'), 500);
});

/* ============================================================
   1. TRANSLATIONS
   ============================================================ */
const translations = {
  fr: {
    'nav.home':'Accueil','nav.cars':'Voitures','nav.rent':'Immo Location','nav.sale':'Immo Vente','nav.contact':'Contact',
    'hero.badge':'+ de 500 biens & véhicules vérifiés',
    'hero.title1':'Louez une voiture ou trouvez','hero.title2':'votre futur chez-vous',
    'hero.desc':"Trois univers, un seul endroit : location de véhicules, immobilier à louer et immobilier à vendre.",
    'cat.cars':'Location Voitures','cat.cars.desc':'Citadines, SUV, électriques…',
    'cat.rent':'Immo · Location','cat.rent.desc':'Apparts, villas, bureaux…',
    'cat.sale':'Immo · Vente','cat.sale.desc':'Maisons, terrains, studios…',
    'home.topRated':'Les mieux notés','home.topRated.desc':"Cliquez sur une carte pour voir les détails.",
    'home.topRated.badge':'Top 3 · Toutes catégories',
    'home.best':'Les plus populaires','home.best.desc':'Les offres les plus réservées ce mois-ci.',
    'home.best.badge':'Best-sellers',
    'tree.title':'Catégories','cars.title':'Location de Voitures',
    'rent.title':'Immobilier · À Louer','sale.title':'Immobilier · À Vendre',
    'back.home':"Retour à l'accueil",
    'info.all':'Tous les biens','info.results':'résultat(s)',
    'badge.location':'Location','badge.sale':'Vente',
    'btn.reserve':'Réserver','btn.visit':'Visiter','btn.buy':'Acheter',
    'unit.day':'/ jour','unit.month':'/ mois',
    'spec.places':'places','spec.rooms':'pièce(s)','spec.terrain':'Terrain',
    'rating.reviews':'avis',
    'modal.features':'Équipements & avantages','modal.pois':"Points d'intérêt à proximité",
    'modal.description':'Description','modal.close':'Fermer',
    'modal.reviews':'avis clients','modal.specs':'Caractéristiques',
    'desc.car':'Véhicule récent et parfaitement entretenu. Idéal pour les trajets urbains comme pour les longs voyages.',
    'desc.rent':'Bien lumineux et confortable, situé dans un quartier calme et bien desservi.',
    'desc.sale':'Belle opportunité à saisir ! Bien en excellent état, dans un secteur recherché.',
    'order.title':'Formulaire de commande','order.subtitle':'Remplissez vos informations.',
    'order.fullname':'Nom complet','order.fullname.ph':'Ex: Ahmed Benali',
    'order.phone':'Numéro de téléphone','order.phone.ph':'Ex: 0555 12 34 56',
    'order.wilaya':'Wilaya','order.commune':'Commune',
    'order.select.wilaya':'Sélectionner une wilaya...','order.select.commune':'Sélectionner une commune...','order.select.placeholder':'Sélectionner...',
    'order.commune.ph':'Saisir votre commune',
    'order.commune.hint':'Communes disponibles pour Alger et Blida uniquement.',
    'order.commune.notListed':'Saisissez votre commune manuellement.',
    'order.submit':'Confirmer la demande','order.submitting':'Envoi en cours...',
    'order.success.title':'Demande envoyée !',
    'order.success.desc':'Merci pour votre confiance. Notre équipe vous contactera dans les plus brefs délais.',
    'order.success.ref':'Référence','order.success.close':'Fermer',
    'order.err.required':'Ce champ est obligatoire','order.err.phone':'Numéro de téléphone invalide',
    'order.wilaya.count':'58 wilayas',
    'about.title1':'À propos de',
    'about.desc':"Depuis plus de 10 ans, nous accompagnons les Algériens dans leurs projets de mobilité et d'immobilier.",
    'about.v1.title':'Confiance & transparence','about.v1.desc':'Des prix clairs, des contrats simples, zéro frais caché.',
    'about.v2.title':'Rapidité & efficacité','about.v2.desc':'Réservation en ligne en moins de 3 minutes, 7j/7.',
    'about.v3.title':'Support 24/7','about.v3.desc':'Une équipe dédiée, joignable à tout moment.',
    'about.stat1':'Biens & véhicules','about.stat2':'Clients satisfaits','about.stat3':"Années d'expérience",
    'about.visual.title':'Notre engagement','about.visual.desc':'Votre satisfaction est notre priorité absolue.',
    'contact.title':'Contactez-nous','contact.desc':"Une question, un projet ? Notre équipe vous répond en moins de 24h.",
    'contact.phone':'Téléphone','contact.phone.sub':'Lun-Sam · 8h - 20h',
    'contact.email':'Email','contact.email.sub':'Réponse sous 24h',
    'contact.address':'Adresse','contact.address.val':'12 Rue Didouche Mourad, Alger','contact.address.sub':'Algérie',
    'contact.form.title':'Envoyez-nous un message','contact.form.sub':'Remplissez le formulaire ci-dessous.',
    'contact.form.name':'Nom complet','contact.form.phone':'Téléphone','contact.form.email':'Email',
    'contact.form.email.err':'Email invalide','contact.form.subject':'Sujet','contact.form.message':'Message',
    'contact.form.submit':'Envoyer le message','contact.form.submitting':'Envoi...',
    'contact.subject.rent':'Location voiture','contact.subject.immoRent':'Immobilier - Location',
    'contact.subject.immoSale':'Immobilier - Achat','contact.subject.other':'Autre',
    'contact.success.title':'Message envoyé !','contact.success.desc':'Merci, nous vous répondrons très vite.',
    'footer.desc':'Location de voitures, immobilier à louer et à vendre — partout en Algérie.',
    'footer.explore':'Explorer','footer.company':'Entreprise','footer.about':'À propos','footer.contact':'Contact',
    'footer.rights':'Tous droits réservés'
  },
  ar: {
    'nav.home':'الرئيسية','nav.cars':'كراء السيارات','nav.rent':'عقارات للكراء','nav.sale':'عقارات للبيع','nav.contact':'اتصل بنا',
    'hero.badge':'+500 عقار وسيارة موثوقة',
    'hero.title1':'اكتري سيارة ولا لقى','hero.title2':'دار أحلامك',
    'hero.desc':'ثلاثة عوالم في بلاصة وحدة: كراء السيارات، عقارات للكراء، وعقارات للبيع.',
    'cat.cars':'كراء السيارات','cat.cars.desc':'سيارات صغيرة، SUV، كهربائية…',
    'cat.rent':'عقارات · كراء','cat.rent.desc':'شقق، فيلات، مكاتب…',
    'cat.sale':'عقارات · بيع','cat.sale.desc':'ديور، أراضي، ستوديوهات…',
    'home.topRated':'الأعلى تقييماً','home.topRated.desc':'كليكي على أي بطاقة باش تشوف التفاصيل.',
    'home.topRated.badge':'أفضل 3 · كل الفئات',
    'home.best':'الأكثر شعبية','home.best.desc':'العروض الأكثر حجزاً هاد الشهر.',
    'home.best.badge':'الأكثر مبيعاً',
    'tree.title':'الفئات','cars.title':'كراء السيارات',
    'rent.title':'عقارات · للكراء','sale.title':'عقارات · للبيع',
    'back.home':'رجع للرئيسية',
    'info.all':'كل العقارات','info.results':'نتيجة',
    'badge.location':'كراء','badge.sale':'بيع',
    'btn.reserve':'احجز','btn.visit':'زور','btn.buy':'اشري',
    'unit.day':'/ نهار','unit.month':'/ شهر',
    'spec.places':'بلايص','spec.rooms':'غرف','spec.terrain':'أرض',
    'rating.reviews':'تقييم',
    'modal.features':'المميزات والفوائد','modal.pois':'المعالم القريبة',
    'modal.description':'الوصف','modal.close':'غلق',
    'modal.reviews':'تقييم الزبائن','modal.specs':'الخصائص',
    'desc.car':'سيارة جديدة ومصانة مزيان. مناسبة للتنقل في المدينة ولا للسفر الطويل.',
    'desc.rent':'عقار مضيء ومريح، في حي هادي وقريب من كلش. متوفر دوكا.',
    'desc.sale':'فرصة زوينة! عقار في حالة ممتازة، في منطقة مطلوبة.',
    'order.title':'استمارة الطلب','order.subtitle':'عمّر معلوماتك باش نكملو طلبك.',
    'order.fullname':'الاسم الكامل','order.fullname.ph':'مثلا: أحمد بن علي',
    'order.phone':'رقم الهاتف','order.phone.ph':'مثلا: 0555 12 34 56',
    'order.wilaya':'الولاية','order.commune':'البلدية',
    'order.select.wilaya':'اختر ولاية...','order.select.commune':'اختر بلدية...','order.select.placeholder':'اختر...',
    'order.commune.ph':'اكتب البلدية تاعك',
    'order.commune.hint':'البلديات متوفرة غير للجزائر العاصمة والبليدة.',
    'order.commune.notListed':'اكتب البلدية تاعك بيدك.',
    'order.submit':'أكد الطلب','order.submitting':'جاري الإرسال...',
    'order.success.title':'تم إرسال الطلب!',
    'order.success.desc':'شكرا على الثقة. فريقنا غيتواصل معاك في أقرب وقت.',
    'order.success.ref':'المرجع','order.success.close':'غلق',
    'order.err.required':'هاد الخانة إلزامية','order.err.phone':'رقم الهاتف ماشي صحيح',
    'order.wilaya.count':'58 ولاية',
    'about.title1':'على',
    'about.desc':'من أكثر من 10 سنين، نرافقو الجزائريين في مشاريعهم تاع التنقل والعقارات.',
    'about.v1.title':'الثقة والشفافية','about.v1.desc':'أسعار واضحة، عقود ساهلة، بلا مصاريف خفية.',
    'about.v2.title':'السرعة والفعالية','about.v2.desc':'حجز أونلاين في أقل من 3 دقايق، 7 أيام في الأسبوع.',
    'about.v3.title':'دعم 24/7','about.v3.desc':'فريق مخصص، متاح في أي وقت.',
    'about.stat1':'عقار وسيارة','about.stat2':'زبون راضي','about.stat3':'سنة خبرة',
    'about.visual.title':'التزامنا','about.visual.desc':'رضاك هو أولويتنا.',
    'contact.title':'اتصل بنا','contact.desc':'عندك سؤال، مشروع، ولا طلب خاص؟ فريقنا يجاوبك في أقل من 24 ساعة.',
    'contact.phone':'الهاتف','contact.phone.sub':'الاثنين-السبت · 8ص - 8م',
    'contact.email':'الإيميل','contact.email.sub':'الرد في 24 ساعة',
    'contact.address':'العنوان','contact.address.val':'12 شارع ديدوش مراد، الجزائر','contact.address.sub':'الجزائر',
    'contact.form.title':'ابعتلنا رسالة','contact.form.sub':'عمّر الاستمارة من تحت.',
    'contact.form.name':'الاسم الكامل','contact.form.phone':'الهاتف','contact.form.email':'الإيميل',
    'contact.form.email.err':'إيميل ماشي صحيح','contact.form.subject':'الموضوع','contact.form.message':'الرسالة',
    'contact.form.submit':'ابعت الرسالة','contact.form.submitting':'جاري الإرسال...',
    'contact.subject.rent':'كراء سيارة','contact.subject.immoRent':'عقار - كراء',
    'contact.subject.immoSale':'عقار - شراء','contact.subject.other':'آخر',
    'contact.success.title':'تم إرسال الرسالة!','contact.success.desc':'شكرا، غنجاوبوك قريب.',
    'footer.desc':'كراء السيارات، عقارات للكراء وللبيع — في كل الجزائر.',
    'footer.explore':'استكشف','footer.company':'الشركة','footer.about':'من نحن','footer.contact':'اتصال',
    'footer.rights':'كل الحقوق محفوظة'
  },
  en: {
    'nav.home':'Home','nav.cars':'Cars','nav.rent':'Real Estate Rent','nav.sale':'Real Estate Sale','nav.contact':'Contact',
    'hero.badge':'+500 verified properties & vehicles',
    'hero.title1':'Rent a car or find','hero.title2':'your future home',
    'hero.desc':'Three worlds, one place: car rental, real estate for rent, and real estate for sale.',
    'cat.cars':'Car Rental','cat.cars.desc':'City cars, SUVs, electric…',
    'cat.rent':'Real Estate · Rent','cat.rent.desc':'Apartments, villas, offices…',
    'cat.sale':'Real Estate · Sale','cat.sale.desc':'Houses, land, studios…',
    'home.topRated':'Top rated','home.topRated.desc':'Click on a card to see details.',
    'home.topRated.badge':'Top 3 · All categories',
    'home.best':'Most popular','home.best.desc':'The most booked offers this month.',
    'home.best.badge':'Best-sellers',
    'tree.title':'Categories','cars.title':'Car Rental',
    'rent.title':'Real Estate · For Rent','sale.title':'Real Estate · For Sale',
    'back.home':'Back to home',
    'info.all':'All properties','info.results':'result(s)',
    'badge.location':'Rental','badge.sale':'Sale',
    'btn.reserve':'Book','btn.visit':'Visit','btn.buy':'Buy',
    'unit.day':'/ day','unit.month':'/ month',
    'spec.places':'seats','spec.rooms':'room(s)','spec.terrain':'Land',
    'rating.reviews':'reviews',
    'modal.features':'Features & amenities','modal.pois':'Nearby points of interest',
    'modal.description':'Description','modal.close':'Close',
    'modal.reviews':'customer reviews','modal.specs':'Characteristics',
    'desc.car':'Recent, well-maintained vehicle. Perfect for city trips and long journeys.',
    'desc.rent':'Bright and comfortable property in a quiet, well-connected area.',
    'desc.sale':'Great opportunity! Property in excellent condition in a sought-after area.',
    'order.title':'Order Form','order.subtitle':'Fill in your information.',
    'order.fullname':'Full name','order.fullname.ph':'Ex: Ahmed Benali',
    'order.phone':'Phone number','order.phone.ph':'Ex: 0555 12 34 56',
    'order.wilaya':'Wilaya (Province)','order.commune':'Commune (Municipality)',
    'order.select.wilaya':'Select a wilaya...','order.select.commune':'Select a commune...','order.select.placeholder':'Select...',
    'order.commune.ph':'Enter your commune',
    'order.commune.hint':'Communes available for Alger and Blida only.',
    'order.commune.notListed':'Enter your commune manually.',
    'order.submit':'Confirm request','order.submitting':'Sending...',
    'order.success.title':'Request sent!',
    'order.success.desc':'Thank you for your trust. Our team will contact you shortly.',
    'order.success.ref':'Reference','order.success.close':'Close',
    'order.err.required':'This field is required','order.err.phone':'Invalid phone number',
    'order.wilaya.count':'58 wilayas',
    'about.title1':'About',
    'about.desc':"For over 10 years, we have been supporting Algerians in their mobility and real estate projects.",
    'about.v1.title':'Trust & transparency','about.v1.desc':'Clear prices, simple contracts, zero hidden fees.',
    'about.v2.title':'Speed & efficiency','about.v2.desc':'Book online in under 3 minutes, 7 days a week.',
    'about.v3.title':'24/7 support','about.v3.desc':'A dedicated team, reachable at any time.',
    'about.stat1':'Properties & vehicles','about.stat2':'Satisfied clients','about.stat3':'Years of experience',
    'about.visual.title':'Our commitment','about.visual.desc':'Your satisfaction is our absolute priority.',
    'contact.title':'Contact us','contact.desc':'A question, a project? Our team responds within 24h.',
    'contact.phone':'Phone','contact.phone.sub':'Mon-Sat · 8am - 8pm',
    'contact.email':'Email','contact.email.sub':'Reply within 24h',
    'contact.address':'Address','contact.address.val':'12 Rue Didouche Mourad, Alger','contact.address.sub':'Algeria',
    'contact.form.title':'Send us a message','contact.form.sub':'Fill in the form below.',
    'contact.form.name':'Full name','contact.form.phone':'Phone','contact.form.email':'Email',
    'contact.form.email.err':'Invalid email','contact.form.subject':'Subject','contact.form.message':'Message',
    'contact.form.submit':'Send message','contact.form.submitting':'Sending...',
    'contact.subject.rent':'Car rental','contact.subject.immoRent':'Real Estate - Rent',
    'contact.subject.immoSale':'Real Estate - Buy','contact.subject.other':'Other',
    'contact.success.title':'Message sent!','contact.success.desc':'Thank you, we will reply very soon.',
    'footer.desc':'Car rental, real estate for rent and sale — across Algeria.',
    'footer.explore':'Explore','footer.company':'Company','footer.about':'About','footer.contact':'Contact',
    'footer.rights':'All rights reserved'
  }
};

let currentLang = localStorage.getItem('autoloc_lang') || 'fr';
function t(key) { return translations[currentLang]?.[key] ?? translations.fr[key] ?? key; }

function applyTranslations() {
  document.querySelectorAll('[data-i18n]').forEach(el => {
    const key = el.dataset.i18n;
    const val = t(key);
    if (val) el.innerHTML = val;
  });
  document.documentElement.lang = currentLang;
  document.body.dataset.lang = currentLang;
  document.body.style.direction = currentLang === 'ar' ? 'rtl' : 'ltr';
  document.querySelectorAll('.lang-btn').forEach(b => {
    b.classList.toggle('active', b.dataset.lang === currentLang);
  });
}

document.getElementById('langSwitch').addEventListener('click', (e) => {
  const btn = e.target.closest('.lang-btn');
  if (!btn) return;
  if (currentLang === btn.dataset.lang) return;
  currentLang = btn.dataset.lang;
  localStorage.setItem('autoloc_lang', currentLang);
  applyTranslations();
  refreshAllDynamic();
  rewireContactForms();
});

/* ============================================================
   2. DZD FORMATTER
   ============================================================ */
function formatDZD(amount) {
  return amount.toLocaleString('fr-FR').replace(/\u202f/g, ' ') + ' دج';
}

/* ============================================================
   3. ALGERIA
   ============================================================ */
const WILAYAS = [
  { code:'01', name:'Adrar', ar:'أدرار' },{ code:'02', name:'Chlef', ar:'الشلف' },
  { code:'03', name:'Laghouat', ar:'الأغواط' },{ code:'04', name:'Oum El Bouaghi', ar:'أم البواقي' },
  { code:'05', name:'Batna', ar:'باتنة' },{ code:'06', name:'Béjaïa', ar:'بجاية' },
  { code:'07', name:'Biskra', ar:'بسكرة' },{ code:'08', name:'Béchar', ar:'بشار' },
  { code:'09', name:'Blida', ar:'البليدة' },{ code:'10', name:'Bouira', ar:'البويرة' },
  { code:'11', name:'Tamanrasset', ar:'تمنراست' },{ code:'12', name:'Tébessa', ar:'تبسة' },
  { code:'13', name:'Tlemcen', ar:'تلمسان' },{ code:'14', name:'Tiaret', ar:'تيارت' },
  { code:'15', name:'Tizi Ouzou', ar:'تيزي وزو' },{ code:'16', name:'Alger', ar:'الجزائر' },
  { code:'17', name:'Djelfa', ar:'الجلفة' },{ code:'18', name:'Jijel', ar:'جيجل' },
  { code:'19', name:'Sétif', ar:'سطيف' },{ code:'20', name:'Saïda', ar:'سعيدة' },
  { code:'21', name:'Skikda', ar:'سكيكدة' },{ code:'22', name:'Sidi Bel Abbès', ar:'سيدي بلعباس' },
  { code:'23', name:'Annaba', ar:'عنابة' },{ code:'24', name:'Guelma', ar:'قالمة' },
  { code:'25', name:'Constantine', ar:'قسنطينة' },{ code:'26', name:'Médéa', ar:'المدية' },
  { code:'27', name:'Mostaganem', ar:'مستغانم' },{ code:'28', name:"M'Sila", ar:'المسيلة' },
  { code:'29', name:'Mascara', ar:'معسكر' },{ code:'30', name:'Ouargla', ar:'ورقلة' },
  { code:'31', name:'Oran', ar:'وهران' },{ code:'32', name:'El Bayadh', ar:'البيض' },
  { code:'33', name:'Illizi', ar:'إليزي' },{ code:'34', name:'Bordj Bou Arreridj', ar:'برج بوعريريج' },
  { code:'35', name:'Boumerdès', ar:'بومرداس' },{ code:'36', name:'El Tarf', ar:'الطارف' },
  { code:'37', name:'Tindouf', ar:'تندوف' },{ code:'38', name:'Tissemsilt', ar:'تيسمسيلت' },
  { code:'39', name:'El Oued', ar:'الوادي' },{ code:'40', name:'Khenchela', ar:'خنشلة' },
  { code:'41', name:'Souk Ahras', ar:'سوق أهراس' },{ code:'42', name:'Tipaza', ar:'تيبازة' },
  { code:'43', name:'Mila', ar:'ميلة' },{ code:'44', name:'Aïn Defla', ar:'عين الدفلى' },
  { code:'45', name:'Naâma', ar:'النعامة' },{ code:'46', name:'Aïn Témouchent', ar:'عين تموشنت' },
  { code:'47', name:'Ghardaïa', ar:'غرداية' },{ code:'48', name:'Relizane', ar:'غليزان' },
  { code:'49', name:'Timimoun', ar:'تيميمون' },{ code:'50', name:'Bordj Badji Mokhtar', ar:'برج باجي مختار' },
  { code:'51', name:'Ouled Djellal', ar:'أولاد جلال' },{ code:'52', name:'Béni Abbès', ar:'بني عباس' },
  { code:'53', name:'In Salah', ar:'عين صالح' },{ code:'54', name:'In Guezzam', ar:'عين قزام' },
  { code:'55', name:'Touggourt', ar:'تقرت' },{ code:'56', name:'Djanet', ar:'جانت' },
  { code:'57', name:"El M'Ghair", ar:'المغير' },{ code:'58', name:'El Meniaa', ar:'المنيعة' }
];
const COMMUNES = {
  '16': ['Alger-Centre','Bab El Oued','Bologhine','Casbah','Oued Koriche','Bir Mourad Raïs','Birkhadem','El Biar','Bouzareah','El Madania','Belouizdad','Hussein Dey','Kouba','Bachdjerrah','Dar El Beïda','Bab Ezzouar','Ben Aknoun','Dely Ibrahim','El Hammamet','Raïs Hamidou','Djasr Kasentina','El Mouradia','Hydra','Mohammadia','Bordj El Kiffan','El Magharia','Beni Messous','Bordj El Bahri','Aïn Taya','El Marsa',"H'raoua",'Rouiba','Reghaïa','Aïn Benian','Baraki','Les Eucalyptus','Sidi Moussa','Oued Smar','Bouroubah','El Harrach','Ouled Fayet','El Achour','Draria','Douera','Baba Hassen','Khraicia','Saoula','Sidi Mhamed'],
  '09': ['Blida','Boufarik','Larbaâ','Bougara','Bouinan','Bouarfa','Chréa','Chiffa','Djebabra','El Affroun','Guerrouaou','Hammam Melouane','Meftah','Mouzaïa','Oued Alleug','Ouled Slama','Ouled Yaïch','Soumaa','Souhane','Beni Mered','Beni Tamou','Aïn Romana','Ben Khéllil']
};
function getWilayaName(w) { return currentLang === 'ar' ? w.ar : w.name; }

/* ============================================================
   4. NAVIGATION
   ============================================================ */
function goTo(viewName) {
  document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
  document.getElementById('view-' + viewName).classList.add('active');
  document.querySelectorAll('.main-tab').forEach(t => {
    t.classList.toggle('active', t.dataset.goto === viewName);
  });
  if (viewName === 'contact') rewireContactForms();
  window.scrollTo({ top: 0, behavior: 'smooth' });
}
document.addEventListener('click', (e) => {
  const el = e.target.closest('[data-goto]');
  if (el) { e.preventDefault(); goTo(el.dataset.goto); }
});

/* ============================================================
   5. TREE DATA
   ============================================================ */
const carsTree = { id:'root', nameKey:'tree.carsRoot', children:[
  { id:'citadine', nameKey:'tree.citadine', children:[{id:'eco',nameKey:'tree.eco'},{id:'compact',nameKey:'tree.compact'}] },
  { id:'suv', nameKey:'tree.suv', children:[{id:'suv-compact',nameKey:'tree.suvC'},{id:'suv-large',nameKey:'tree.suvL'}] },
  { id:'electric', nameKey:'tree.elec', children:[{id:'ev-berline',nameKey:'tree.evB'},{id:'ev-suv',nameKey:'tree.evS'}] }
]};
const rentTree = { id:'root', nameKey:'tree.rentRoot', children:[
  { id:'appart', nameKey:'tree.appart', children:[{id:'studio',nameKey:'tree.studio'},{id:'t2',nameKey:'tree.t2'},{id:'t4plus',nameKey:'tree.t4'}] },
  { id:'maison', nameKey:'tree.maison', children:[{id:'villa',nameKey:'tree.villa'},{id:'duplex',nameKey:'tree.duplex'}] },
  { id:'commerce', nameKey:'tree.commerce', children:[{id:'bureau',nameKey:'tree.bureau'},{id:'boutique',nameKey:'tree.boutique'}] }
]};
const saleTree = { id:'root', nameKey:'tree.saleRoot', children:[
  { id:'appart-sale', nameKey:'tree.appartS', children:[{id:'studio-sale',nameKey:'tree.studio'},{id:'t3-sale',nameKey:'tree.t3'}] },
  { id:'maison-sale', nameKey:'tree.maisonS', children:[{id:'villa-sale',nameKey:'tree.villa'},{id:'ferme',nameKey:'tree.ferme'}] },
  { id:'terrain', nameKey:'tree.terrain', children:[{id:'constructible',nameKey:'tree.construct'},{id:'agricole',nameKey:'tree.agri'}] }
]};
const treeNames = {
  'tree.carsRoot':{fr:'Tous les véhicules',ar:'كل السيارات',en:'All vehicles'},
  'tree.citadine':{fr:'Citadines',ar:'سيارات المدينة',en:'City cars'},
  'tree.eco':{fr:'Économique',ar:'اقتصادية',en:'Economy'},
  'tree.compact':{fr:'Compacte',ar:'مدمجة',en:'Compact'},
  'tree.suv':{fr:'SUV',ar:'SUV',en:'SUV'},
  'tree.suvC':{fr:'SUV Compact',ar:'SUV مدمج',en:'Compact SUV'},
  'tree.suvL':{fr:'Grand SUV',ar:'SUV كبير',en:'Large SUV'},
  'tree.elec':{fr:'Électriques',ar:'كهربائية',en:'Electric'},
  'tree.evB':{fr:'Berline Électrique',ar:'سيدان كهربائية',en:'Electric Sedan'},
  'tree.evS':{fr:'SUV Électrique',ar:'SUV كهربائية',en:'Electric SUV'},
  'tree.rentRoot':{fr:'Tous les biens',ar:'كل العقارات',en:'All properties'},
  'tree.appart':{fr:'Appartements',ar:'شقق',en:'Apartments'},
  'tree.studio':{fr:'Studio',ar:'ستوديو',en:'Studio'},
  'tree.t2':{fr:'T2 / T3',ar:'T2 / T3',en:'T2 / T3'},
  'tree.t4':{fr:'T4 et plus',ar:'T4 وأكثر',en:'T4 and more'},
  'tree.maison':{fr:'Maisons',ar:'ديور',en:'Houses'},
  'tree.villa':{fr:'Villa',ar:'فيلا',en:'Villa'},
  'tree.duplex':{fr:'Duplex',ar:'دوبلكس',en:'Duplex'},
  'tree.commerce':{fr:'Locaux commerciaux',ar:'محلات تجارية',en:'Commercial'},
  'tree.bureau':{fr:'Bureau',ar:'مكتب',en:'Office'},
  'tree.boutique':{fr:'Boutique',ar:'بوتيك',en:'Shop'},
  'tree.saleRoot':{fr:'Tous les biens',ar:'كل العقارات',en:'All properties'},
  'tree.appartS':{fr:'Appartements',ar:'شقق',en:'Apartments'},
  'tree.t3':{fr:'T3 / T4',ar:'T3 / T4',en:'T3 / T4'},
  'tree.maisonS':{fr:'Maisons',ar:'ديور',en:'Houses'},
  'tree.ferme':{fr:'Ferme / Mas',ar:'فيرمة',en:'Farm'},
  'tree.terrain':{fr:'Terrains',ar:'أراضي',en:'Land'},
  'tree.construct':{fr:'Constructible',ar:'قابلة للبناء',en:'Buildable'},
  'tree.agri':{fr:'Agricole',ar:'زراعية',en:'Agricultural'}
};
function treeName(node){ const e=treeNames[node.nameKey]; return e?e[currentLang]:(node.nameKey||node.id); }

function randomMeta(){ return { rating:(Math.random()*1.5+3.5).toFixed(1), sales:Math.floor(Math.random()*200)+20 }; }
function randomPOIs(){
  const P=[{icon:'fa-mosque',nameKey:'poi.mosque'},{icon:'fa-shopping-bag',nameKey:'poi.mall'},{icon:'fa-school',nameKey:'poi.school'},{icon:'fa-hospital',nameKey:'poi.hospital'},{icon:'fa-bus',nameKey:'poi.bus'},{icon:'fa-tree',nameKey:'poi.park'},{icon:'fa-utensils',nameKey:'poi.resto'},{icon:'fa-gas-pump',nameKey:'poi.gas'},{icon:'fa-graduation-cap',nameKey:'poi.uni'},{icon:'fa-dumbbell',nameKey:'poi.gym'},{icon:'fa-train',nameKey:'poi.train'},{icon:'fa-pharmacy',nameKey:'poi.pharm'}];
  return [...P].sort(()=>Math.random()-0.5).slice(0,5).map(p=>({...p,distance:Math.floor(Math.random()*1950)+50})).sort((a,b)=>a.distance-b.distance);
}
function randomFeatures(type){
  if(type==='car'){return [{icon:'fa-snowflake',key:'feat.ac'},{icon:'fa-bluetooth-b',key:'feat.bt'},{icon:'fa-gas-pump',key:'feat.full'},{icon:'fa-shield',key:'feat.insurance'},{icon:'fa-gps',key:'feat.gps'},{icon:'fa-camera',key:'feat.cam'},{icon:'fa-usb',key:'feat.usb'},{icon:'fa-chair',key:'feat.seat'}].sort(()=>Math.random()-0.5).slice(0,4);}
  return [{icon:'fa-wifi',key:'feat.wifi'},{icon:'fa-car',key:'feat.parking'},{icon:'fa-snowflake',key:'feat.ac'},{icon:'fa-utensils',key:'feat.kitchen'},{icon:'fa-couch',key:'feat.furnished'},{icon:'fa-water',key:'feat.water'},{icon:'fa-elevator',key:'feat.elevator'},{icon:'fa-sun',key:'feat.balcony'},{icon:'fa-paw',key:'feat.pets'},{icon:'fa-shield',key:'feat.security'},{icon:'fa-swimming-pool',key:'feat.pool'},{icon:'fa-fire',key:'feat.heating'}].sort(()=>Math.random()-0.5).slice(0,5);
}
const poiNames={
  'poi.mosque':{fr:'Mosquée',ar:'مسجد',en:'Mosque',sub_fr:'Lieu de culte',sub_ar:'مكان عبادة',sub_en:'Place of worship'},
  'poi.mall':{fr:'Centre commercial',ar:'مركز تجاري',en:'Shopping mall',sub_fr:'Mall / Galerie',sub_ar:'مول / غاليري',sub_en:'Mall / Gallery'},
  'poi.school':{fr:'École primaire',ar:'مدرسة ابتدائية',en:'Primary school',sub_fr:'Établissement scolaire',sub_ar:'مؤسسة تعليمية',sub_en:'School'},
  'poi.hospital':{fr:'Hôpital / Clinique',ar:'مستشفى / عيادة',en:'Hospital',sub_fr:'Santé',sub_ar:'صحة',sub_en:'Health'},
  'poi.bus':{fr:'Arrêt de bus',ar:'محطة حافلات',en:'Bus stop',sub_fr:'Transport en commun',sub_ar:'نقل عمومي',sub_en:'Public transport'},
  'poi.park':{fr:'Parc / Jardin',ar:'حديقة',en:'Park',sub_fr:'Espace vert',sub_ar:'مساحة خضراء',sub_en:'Green space'},
  'poi.resto':{fr:'Restaurants',ar:'مطاعم',en:'Restaurants',sub_fr:'Restauration',sub_ar:'مطاعم',sub_en:'Dining'},
  'poi.gas':{fr:'Station-service',ar:'محطة بنزين',en:'Gas station',sub_fr:'Carburant',sub_ar:'وقود',sub_en:'Fuel'},
  'poi.uni':{fr:'Université',ar:'جامعة',en:'University',sub_fr:'Enseignement supérieur',sub_ar:'تعليم عالي',sub_en:'Higher education'},
  'poi.gym':{fr:'Salle de sport',ar:'قاعة رياضة',en:'Gym',sub_fr:'Fitness',sub_ar:'رياضة',sub_en:'Fitness'},
  'poi.train':{fr:'Gare / Métro',ar:'محطة قطار / مترو',en:'Train / Metro',sub_fr:'Transport',sub_ar:'نقل',sub_en:'Transport'},
  'poi.pharm':{fr:'Pharmacie',ar:'صيدلية',en:'Pharmacy',sub_fr:'Santé',sub_ar:'صحة',sub_en:'Health'}
};
const featNames={
  'feat.ac':{fr:'Climatisation',ar:'تكييف',en:'Air conditioning'},
  'feat.bt':{fr:'Bluetooth',ar:'بلوتوث',en:'Bluetooth'},
  'feat.full':{fr:'Plein inclus',ar:'بنزين مشمول',en:'Full tank'},
  'feat.insurance':{fr:'Assurance tous risques',ar:'تأمين شامل',en:'Full insurance'},
  'feat.gps':{fr:'GPS intégré',ar:'GPS مدمج',en:'GPS'},
  'feat.cam':{fr:'Caméra de recul',ar:'كاميرا خلفية',en:'Reversing camera'},
  'feat.usb':{fr:'Port USB',ar:'منفذ USB',en:'USB port'},
  'feat.seat':{fr:'Sièges chauffants',ar:'مقاعد مسخنة',en:'Heated seats'},
  'feat.wifi':{fr:'Wi-Fi fibre',ar:'واي فاي',en:'Fiber Wi-Fi'},
  'feat.parking':{fr:'Parking privé',ar:'باركينغ خاص',en:'Private parking'},
  'feat.kitchen':{fr:'Cuisine équipée',ar:'مطبخ مجهز',en:'Equipped kitchen'},
  'feat.furnished':{fr:'Meublé',ar:'مفروش',en:'Furnished'},
  'feat.water':{fr:'Eau chaude',ar:'ماء سخون',en:'Hot water'},
  'feat.elevator':{fr:'Ascenseur',ar:'أسانسير',en:'Elevator'},
  'feat.balcony':{fr:'Balcon / Terrasse',ar:'بلكون / طراس',en:'Balcony'},
  'feat.pets':{fr:'Animaux acceptés',ar:'يقبل الحيوانات',en:'Pets allowed'},
  'feat.security':{fr:'Sécurité 24/7',ar:'أمن 24/7',en:'24/7 security'},
  'feat.pool':{fr:'Piscine',ar:'مسبح',en:'Pool'},
  'feat.heating':{fr:'Chauffage central',ar:'تدفئة مركزية',en:'Central heating'}
};
function poiName(p){return poiNames[p.nameKey]?.[currentLang]||p.nameKey;}
function poiSub(p){return poiNames[p.nameKey]?.['sub_'+currentLang]||'';}
function featName(f){return featNames[f.key]?.[currentLang]||f.key;}
function randomDescription(item){ return item.type==='car'?t('desc.car'):item.type==='rent'?t('desc.rent'):t('desc.sale'); }

/* ⭐ DATA AVEC IMAGES RENAULT CLIO */
const carItems = [
  { 
    type:'car', brand:'Renault', name:'Clio V', cat:'eco',
    icon:'fa-car',
    images: [
      'images/clio-1.jpg',
      'images/clio-2.jpg',
      'images/clio-3.jpg'
    ],
    fuel:'Essence', gear:'Manuelle', seats:5, price:3900, unit:'unit.day',
    badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs()
  },
  { type:'car', brand:'Peugeot', name:'208 GT', cat:'compact', icon:'fa-car-side', fuel:'Essence', gear:'Auto', seats:5, price:4500, unit:'unit.day', badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs() },
  { type:'car', brand:'Citroën', name:'C3 Aircross', cat:'suv-compact', icon:'fa-bus', fuel:'Diesel', gear:'Auto', seats:5, price:5800, unit:'unit.day', badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs() },
  { type:'car', brand:'Peugeot', name:'3008', cat:'suv-large', icon:'fa-truck-pickup', fuel:'Hybride', gear:'Auto', seats:5, price:7200, unit:'unit.day', badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs() },
  { type:'car', brand:'Tesla', name:'Model 3', cat:'ev-berline', icon:'fa-bolt', fuel:'Électrique', gear:'Auto', seats:5, price:8900, unit:'unit.day', badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs() },
  { type:'car', brand:'Tesla', name:'Model Y', cat:'ev-suv', icon:'fa-bolt', fuel:'Électrique', gear:'Auto', seats:5, price:10500, unit:'unit.day', badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs() },
  { type:'car', brand:'Dacia', name:'Sandero', cat:'eco', icon:'fa-car', fuel:'Essence', gear:'Manuelle', seats:5, price:3200, unit:'unit.day', badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs() },
  { type:'car', brand:'Renault', name:'Captur', cat:'suv-compact', icon:'fa-truck-pickup', fuel:'Essence', gear:'Auto', seats:5, price:5400, unit:'unit.day', badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs() },
  { type:'car', brand:'Volkswagen', name:'ID.3', cat:'ev-berline', icon:'fa-bolt', fuel:'Électrique', gear:'Auto', seats:5, price:7800, unit:'unit.day', badge:'badge.location', ...randomMeta(), features:randomFeatures('car'), pois:randomPOIs() }
];
const rentItems = [
  { type:'rent', title:'Studio Cosy · Alger', cat:'studio', icon:'fa-door-open', surface:'24 m²', rooms:1, city:'Alger', price:85000, unit:'unit.month', badge:'badge.location', ...randomMeta(), features:randomFeatures('rent'), pois:randomPOIs() },
  { type:'rent', title:'T2 Lumineux · Blida', cat:'t2', icon:'fa-building', surface:'45 m²', rooms:2, city:'Blida', price:92000, unit:'unit.month', badge:'badge.location', ...randomMeta(), features:randomFeatures('rent'), pois:randomPOIs() },
  { type:'rent', title:'T3 Familial · Oran', cat:'t2', icon:'fa-building', surface:'68 m²', rooms:3, city:'Oran', price:115000, unit:'unit.month', badge:'badge.location', ...randomMeta(), features:randomFeatures('rent'), pois:randomPOIs() },
  { type:'rent', title:'T4 Standing · Constantine', cat:'t4plus', icon:'fa-city', surface:'95 m²', rooms:4, city:'Constantine', price:145000, unit:'unit.month', badge:'badge.location', ...randomMeta(), features:randomFeatures('rent'), pois:randomPOIs() },
  { type:'rent', title:'Villa Piscine · Tipaza', cat:'villa', icon:'fa-house-chimney', surface:'160 m²', rooms:5, city:'Tipaza', price:240000, unit:'unit.month', badge:'badge.location', ...randomMeta(), features:randomFeatures('rent'), pois:randomPOIs() },
  { type:'rent', title:'Duplex Loft · Annaba', cat:'duplex', icon:'fa-house', surface:'110 m²', rooms:4, city:'Annaba', price:185000, unit:'unit.month', badge:'badge.location', ...randomMeta(), features:randomFeatures('rent'), pois:randomPOIs() },
  { type:'rent', title:'Bureau 40m² · Alger', cat:'bureau', icon:'fa-briefcase', surface:'40 m²', rooms:2, city:'Alger', price:160000, unit:'unit.month', badge:'badge.location', ...randomMeta(), features:randomFeatures('rent'), pois:randomPOIs() },
  { type:'rent', title:'Boutique · Blida', cat:'boutique', icon:'fa-store', surface:'55 m²', rooms:1, city:'Blida', price:190000, unit:'unit.month', badge:'badge.location', ...randomMeta(), features:randomFeatures('rent'), pois:randomPOIs() }
];
const saleItems = [
  { type:'sale', title:'Studio · Alger', cat:'studio-sale', icon:'fa-door-open', surface:'22 m²', rooms:1, city:'Alger', price:18900000, badge:'badge.sale', ...randomMeta(), features:randomFeatures('sale'), pois:randomPOIs() },
  { type:'sale', title:'T3 Vue Mer · Oran', cat:'t3-sale', icon:'fa-building', surface:'72 m²', rooms:3, city:'Oran', price:42000000, badge:'badge.sale', ...randomMeta(), features:randomFeatures('sale'), pois:randomPOIs() },
  { type:'sale', title:'T4 Rénové · Blida', cat:'t3-sale', icon:'fa-city', surface:'88 m²', rooms:4, city:'Blida', price:34500000, badge:'badge.sale', ...randomMeta(), features:randomFeatures('sale'), pois:randomPOIs() },
  { type:'sale', title:'Villa Moderne · Alger', cat:'villa-sale', icon:'fa-house-chimney', surface:'180 m²', rooms:6, city:'Alger', price:68500000, badge:'badge.sale', ...randomMeta(), features:randomFeatures('sale'), pois:randomPOIs() },
  { type:'sale', title:'Ferme · Blida', cat:'ferme', icon:'fa-house', surface:'240 m²', rooms:7, city:'Blida', price:89000000, badge:'badge.sale', ...randomMeta(), features:randomFeatures('sale'), pois:randomPOIs() },
  { type:'sale', title:'Terrain 500m² · Alger', cat:'constructible', icon:'fa-mountain', surface:'500 m²', rooms:0, city:'Alger', price:21000000, badge:'badge.sale', ...randomMeta(), features:randomFeatures('sale'), pois:randomPOIs() },
  { type:'sale', title:'Terrain Agricole · Blida', cat:'agricole', icon:'fa-wheat-awn', surface:'2 ha', rooms:0, city:'Blida', price:12000000, badge:'badge.sale', ...randomMeta(), features:randomFeatures('sale'), pois:randomPOIs() }
];

/* ============================================================
   6. CARD RENDERER
   ============================================================ */
function renderStars(r){const f=Math.floor(r),h=r-f>=.5?1:0;let s='';for(let i=0;i<f;i++)s+='★';if(h)s+='½';for(let i=f+h;i<5;i++)s+='☆';return s;}

function getFirstImage(item) {
  if (item.images && item.images.length > 0) return item.images[0];
  if (item.image) return item.image;
  return null;
}

function createCard(item, path, opts={}) {
  const el = document.createElement('div');
  el.className = 'card';
  const badgeText = t(item.badge);
  const badgeClass = item.badge==='badge.sale' ? 'card-badge sale' : 'card-badge';
  const rankBadge = opts.rank ? `<span class="card-badge rank">#${opts.rank}</span>` : '';

  let specs = '';
  if (item.type==='car') {
    specs = `<span><i class="fas fa-gas-pump"></i>${item.fuel}</span>
             <span><i class="fas fa-cogs"></i>${item.gear}</span>
             <span><i class="fas fa-user-friends"></i>${item.seats} ${t('spec.places')}</span>`;
  } else {
    specs = `<span><i class="fas fa-ruler-combined"></i>${item.surface}</span>
             <span><i class="fas fa-door-open"></i>${item.rooms?item.rooms+' '+t('spec.rooms'):t('spec.terrain')}</span>`;
  }
  const priceText = item.type==='sale'
    ? formatDZD(item.price)
    : formatDZD(item.price) + ' <small>' + t(item.unit||'unit.month') + '</small>';
  const btnLabel = item.type==='car'?t('btn.reserve'):item.type==='rent'?t('btn.visit'):t('btn.buy');
  const btnClass = item.type==='sale'?'btn-book sale-btn':'btn-book';
  const brandLine = item.brand||item.city||'';
  const firstImg = getFirstImage(item);
  const photoCount = item.images ? item.images.length : (item.image ? 1 : 0);

  el.innerHTML = `
    <div class="card-image">
      ${firstImg 
        ? `<img src="${firstImg}" alt="${item.name||item.title}" 
             onerror="this.style.display='none'; this.nextElementSibling.style.display='flex';">
           <i class="fas ${item.icon}" style="display:none;"></i>`
        : `<i class="fas ${item.icon}"></i>`
      }
      ${photoCount > 1 ? `<span class="photo-count-badge"><i class="fas fa-images"></i> ${photoCount}</span>` : ''}
      <span class="${badgeClass}">${badgeText}</span>
      ${rankBadge}
    </div>
    <div class="card-brand">${brandLine}</div>
    <div class="card-name">${item.name||item.title}</div>
    ${path?`<div class="card-cat-tag"><i class="fas fa-tag"></i>${path}</div>`:''}
    <div class="rating">
      <span class="stars">${renderStars(parseFloat(item.rating))}</span>
      <strong>${item.rating}</strong> · ${item.sales} ${t('rating.reviews')}
    </div>
    <div class="card-specs">${specs}</div>
    <div class="card-footer">
      <div class="price">${priceText}</div>
      <button class="${btnClass}">${btnLabel}</button>
    </div>
  `;

  el.addEventListener('click', (e) => {
    if (e.target.closest('.btn-book')) return;
    openDetailModal(item, path);
  });
  const actionBtn = el.querySelector('.btn-book');
  actionBtn.addEventListener('click', (e) => {
    e.stopPropagation();
    openOrderForm(item);
  });
  return el;
}

/* ============================================================
   7. DETAIL MODAL — GALERIE CORRIGÉE
   ============================================================ */
const detailModal = document.getElementById('detailModal');
const detailModalContent = document.getElementById('detailModalContent');
function distanceClass(m){if(m<=300)return'close';if(m<=800)return'medium';return'far';}
function formatDistance(m){return m<1000?m+' m':(m/1000).toFixed(1).replace('.0','')+' km';}

function openDetailModal(item, path) {
  const brandLine = item.brand||item.city||'';
  const title = item.name||item.title;
  const priceText = item.type==='sale'
    ? formatDZD(item.price)
    : formatDZD(item.price) + ' <small>' + t(item.unit||'unit.month') + '</small>';
  const btnLabel = item.type==='car'?t('btn.reserve'):item.type==='rent'?t('btn.visit'):t('btn.buy');
  const badgeText = t(item.badge);
  const badgeClass = item.badge==='badge.sale'?'sale':'';

  // ⭐ IMAGES (array)
  const images = item.images && item.images.length > 0 
    ? item.images 
    : (item.image ? [item.image] : []);
  const hasGallery = images.length > 1;
  const hasImage = images.length > 0;

  let specsHTML = '';
  if (item.type==='car') {
    specsHTML = `<span><i class="fas fa-gas-pump"></i>${item.fuel}</span>
      <span><i class="fas fa-cogs"></i>${item.gear}</span>
      <span><i class="fas fa-user-friends"></i>${item.seats} ${t('spec.places')}</span>`;
  } else {
    specsHTML = `<span><i class="fas fa-ruler-combined"></i>${item.surface}</span>
      <span><i class="fas fa-door-open"></i>${item.rooms?item.rooms+' '+t('spec.rooms'):t('spec.terrain')}</span>
      <span><i class="fas fa-map-marker-alt"></i>${item.city}</span>`;
  }

  const featuresHTML = (item.features||[]).map(f=>`<div class="feature-chip"><i class="fas ${f.icon}"></i>${featName(f)}</div>`).join('');
  const poisHTML = (item.pois||[]).map(p=>`
    <div class="poi-item">
      <div class="poi-icon"><i class="fas ${p.icon}"></i></div>
      <div class="poi-info"><div class="poi-name">${poiName(p)}</div><div class="poi-sub">${poiSub(p)}</div></div>
      <div class="poi-distance ${distanceClass(p.distance)}">${formatDistance(p.distance)}</div>
    </div>`).join('');

  // ⭐ Build hero HTML
  let heroHTML = '';
  if (hasImage) {
    heroHTML = `
      <img id="galleryMainImg" src="${images[0]}" alt="${title}" 
           onerror="this.style.display='none'; document.getElementById('galleryFallback').style.display='flex';">
      <i id="galleryFallback" class="fas ${item.icon}" style="display:none; font-size:6rem; color:#fff;"></i>
      ${hasGallery ? `
        <button class="gallery-nav prev" id="galleryPrev" type="button"><i class="fas fa-chevron-left"></i></button>
        <button class="gallery-nav next" id="galleryNext" type="button"><i class="fas fa-chevron-right"></i></button>
        <div class="gallery-counter"><span id="galleryCounter">1 / ${images.length}</span></div>
        <div class="gallery-thumbs" id="galleryThumbs">
          ${images.map((img, i) => `
            <img src="${img}" alt="Photo ${i+1}" 
                 class="gallery-thumb ${i===0?'active':''}" 
                 data-idx="${i}"
                 onerror="this.style.display='none';">
          `).join('')}
        </div>
      ` : ''}
    `;
  } else {
    heroHTML = `<i class="fas ${item.icon}" style="font-size:6rem; color:#fff;"></i>`;
  }

  detailModalContent.innerHTML = `
    <button class="modal-close" id="modalCloseBtn" type="button"><i class="fas fa-times"></i></button>
    <div class="modal-hero" id="modalHero">
      ${heroHTML}
      <span class="modal-badge" style="background:${badgeClass==='sale'?'#1a1512':'#fff'};color:${badgeClass==='sale'?'#fff':'#3d2c1f'};">${badgeText}</span>
    </div>
    <div class="modal-body">
      <div class="modal-title-row">
        <div>
          <div class="modal-brand">${brandLine}</div>
          <div class="modal-title">${title}</div>
          ${path?`<div class="card-cat-tag" style="margin-top:6px;"><i class="fas fa-tag"></i>${path}</div>`:''}
        </div>
        <div class="modal-price">${priceText}</div>
      </div>
      <div class="modal-rating">
        <span class="stars">${renderStars(parseFloat(item.rating))}</span>
        <strong>${item.rating}</strong><span>· ${item.sales} ${t('modal.reviews')}</span>
      </div>
      <div class="modal-section">
        <h4><i class="fas fa-info-circle"></i> ${t('modal.specs')}</h4>
        <div class="card-specs" style="border-top:none;padding-top:0;margin-bottom:0;font-size:.95rem;">${specsHTML}</div>
      </div>
      <div class="modal-section">
        <h4><i class="fas fa-list-check"></i> ${t('modal.features')}</h4>
        <div class="features-grid">${featuresHTML}</div>
      </div>
      <div class="modal-section">
        <h4><i class="fas fa-location-dot"></i> ${t('modal.pois')}</h4>
        <div class="poi-list">${poisHTML}</div>
      </div>
      <div class="modal-section">
        <h4><i class="fas fa-align-left"></i> ${t('modal.description')}</h4>
        <div class="modal-description">${randomDescription(item)}</div>
      </div>
      <div class="modal-actions">
        <button class="btn-primary" id="modalOrderBtn" type="button"><i class="fas fa-check"></i> ${btnLabel}</button>
        <button class="btn-secondary" id="modalCancelBtn" type="button">${t('modal.close')}</button>
      </div>
    </div>
  `;

  detailModal.classList.add('active');
  document.body.classList.add('modal-open');

  // Wire close
  document.getElementById('modalCloseBtn').addEventListener('click', closeDetailModal);
  document.getElementById('modalCancelBtn').addEventListener('click', closeDetailModal);
  document.getElementById('modalOrderBtn').addEventListener('click', () => {
    closeDetailModal();
    openOrderForm(item);
  });

  // ⭐ GALERIE — logique corrigée (récupération APRÈS injection DOM)
  if (hasGallery) {
    let currentIdx = 0;
    const mainImg = document.getElementById('galleryMainImg');
    const counter = document.getElementById('galleryCounter');
    const thumbs = document.querySelectorAll('.gallery-thumb');
    const prevBtn = document.getElementById('galleryPrev');
    const nextBtn = document.getElementById('galleryNext');

    function showImage(idx) {
      if (!mainImg) return;
      if (idx < 0) idx = images.length - 1;
      if (idx >= images.length) idx = 0;
      currentIdx = idx;
      mainImg.src = images[idx];
      // Reset error display if needed
      mainImg.style.display = 'block';
      const fallback = document.getElementById('galleryFallback');
      if (fallback) fallback.style.display = 'none';
      // Animation
      mainImg.style.animation = 'none';
      void mainImg.offsetWidth;
      mainImg.style.animation = 'fadeIn .3s ease';
      // Update counter
      if (counter) counter.textContent = `${idx+1} / ${images.length}`;
      // Update thumbs
      thumbs.forEach((th, i) => th.classList.toggle('active', i === idx));
    }

    if (prevBtn) prevBtn.addEventListener('click', (e) => {
      e.preventDefault();
      e.stopPropagation();
      showImage(currentIdx - 1);
    });
    if (nextBtn) nextBtn.addEventListener('click', (e) => {
      e.preventDefault();
      e.stopPropagation();
      showImage(currentIdx + 1);
    });
    thumbs.forEach(th => {
      th.addEventListener('click', (e) => {
        e.preventDefault();
        e.stopPropagation();
        showImage(parseInt(th.dataset.idx));
      });
    });

    // Support clavier
    document.addEventListener('keydown', function keyboardNav(e) {
      if (!detailModal.classList.contains('active')) {
        document.removeEventListener('keydown', keyboardNav);
        return;
      }
      if (e.key === 'ArrowLeft') showImage(currentIdx - 1);
      if (e.key === 'ArrowRight') showImage(currentIdx + 1);
    });
  }
}
function closeDetailModal(){ detailModal.classList.remove('active'); document.body.classList.remove('modal-open'); }
detailModal.addEventListener('click', (e)=>{ if(e.target===detailModal) closeDetailModal(); });

/* ============================================================
   8. ORDER FORM
   ============================================================ */
const orderModal = document.getElementById('orderModal');
const orderModalContent = document.getElementById('orderModalContent');

function openOrderForm(item) {
  const title = item.name||item.title;
  const brandLine = item.brand||item.city||'';
  const priceText = item.type==='sale'
    ? formatDZD(item.price)
    : formatDZD(item.price) + ' <small>' + t(item.unit||'unit.month') + '</small>';
  const isSale = item.type==='sale';

  const wilayaOptions = WILAYAS.map(w =>
    `<option value="${w.code}">${w.code} — ${getWilayaName(w)}</option>`
  ).join('');

  orderModalContent.innerHTML = `
    <button class="modal-close" id="orderCloseBtn" type="button"><i class="fas fa-times"></i></button>
    <div class="order-hero">
      <h2><i class="fas fa-${isSale?'file-signature':'clipboard-check'}"></i> ${t('order.title')}</h2>
      <p>${t('order.subtitle')}</p>
      <div class="order-item-name">
        <i class="fas ${item.icon}"></i>
        ${brandLine} · ${title} — ${priceText}
      </div>
    </div>
    <div class="order-body">
      <form id="orderForm" novalidate>
        <div class="form-group" id="fgFullname">
          <label>${t('order.fullname')} <span class="req">*</span></label>
          <input type="text" name="fullname" placeholder="${t('order.fullname.ph')}" required>
          <span class="error-msg">${t('order.err.required')}</span>
        </div>
        <div class="form-group" id="fgPhone">
          <label>${t('order.phone')} <span class="req">*</span></label>
          <input type="tel" name="phone" placeholder="${t('order.phone.ph')}" required>
          <span class="error-msg">${t('order.err.phone')}</span>
        </div>
        <div class="form-group" id="fgWilaya">
          <label>${t('order.wilaya')} <span class="req">*</span>
            <span style="font-weight:500;color:var(--text-soft);font-size:.78rem;">(${t('order.wilaya.count')})</span>
          </label>
          <select name="wilaya" id="wilayaSelect" required>
            <option value="">${t('order.select.wilaya')}</option>
            ${wilayaOptions}
          </select>
          <span class="error-msg">${t('order.err.required')}</span>
        </div>
        <div class="form-group" id="fgCommune">
          <label>${t('order.commune')} <span class="req">*</span></label>
          <div id="communeWrapper">
            <select name="commune" id="communeField" disabled>
              <option value="">${t('order.select.commune')}</option>
            </select>
          </div>
          <span class="error-msg">${t('order.err.required')}</span>
          <div class="form-hint" id="communeHint">
            <i class="fas fa-info-circle"></i>
            <span>${t('order.commune.hint')}</span>
          </div>
        </div>
        <button type="submit" class="order-submit" id="orderSubmitBtn">
          <i class="fas fa-paper-plane"></i> <span>${t('order.submit')}</span>
        </button>
      </form>
    </div>
  `;

  orderModal.classList.add('active');
  document.body.classList.add('modal-open');

  const form = document.getElementById('orderForm');
  const wilayaSelect = document.getElementById('wilayaSelect');
  const communeWrapper = document.getElementById('communeWrapper');
  const communeHint = document.getElementById('communeHint');
  const fgCommune = document.getElementById('fgCommune');

  wilayaSelect.addEventListener('change', () => {
    const code = wilayaSelect.value;
    fgCommune.classList.remove('error');
    communeWrapper.innerHTML = '';
    if (!code) {
      communeWrapper.innerHTML = `<select name="commune" id="communeField" disabled>
        <option value="">${t('order.select.commune')}</option></select>`;
      communeHint.innerHTML = `<i class="fas fa-info-circle"></i><span>${t('order.commune.hint')}</span>`;
      return;
    }
    if (COMMUNES[code] && COMMUNES[code].length > 0) {
      const opts = COMMUNES[code].map(c => `<option value="${c}">${c}</option>`).join('');
      communeWrapper.innerHTML = `<select name="commune" id="communeField" required>
        <option value="">${t('order.select.commune')}</option>${opts}</select>`;
      communeHint.innerHTML = `<i class="fas fa-list"></i><span>${t('order.commune.hint')}</span>`;
    } else {
      communeWrapper.innerHTML = `<input type="text" name="commune" id="communeField"
        placeholder="${t('order.commune.ph')}" required>`;
      communeHint.innerHTML = `<i class="fas fa-pen"></i><span>${t('order.commune.notListed')}</span>`;
    }
  });

  document.getElementById('orderCloseBtn').addEventListener('click', closeOrderForm);
  form.addEventListener('submit', (e) => { e.preventDefault(); handleOrderSubmit(item, form); });
}
function closeOrderForm(){ orderModal.classList.remove('active'); document.body.classList.remove('modal-open'); }
orderModal.addEventListener('click', (e)=>{ if(e.target===orderModal) closeOrderForm(); });
document.addEventListener('keydown', (e)=>{
  if(e.key==='Escape'){
    if(detailModal.classList.contains('active')) closeDetailModal();
    if(orderModal.classList.contains('active')) closeOrderForm();
  }
});

function handleOrderSubmit(item, form) {
  let valid = true;
  form.querySelectorAll('.form-group').forEach(g => g.classList.remove('error'));
  const fullname = form.querySelector('input[name="fullname"]');
  const phone = form.querySelector('input[name="phone"]');
  const wilaya = form.querySelector('select[name="wilaya"]');
  const commune = form.querySelector('#communeField');

  if (!fullname.value.trim()) { document.getElementById('fgFullname').classList.add('error'); valid = false; }
  if (!phone.value.trim()) { document.getElementById('fgPhone').classList.add('error'); valid = false; }
  else if (!/^[\d\s\+\-\(\)]{8,}$/.test(phone.value.trim())) { document.getElementById('fgPhone').classList.add('error'); valid = false; }
  if (!wilaya.value) { document.getElementById('fgWilaya').classList.add('error'); valid = false; }
  if (!commune || !commune.value.trim()) { document.getElementById('fgCommune').classList.add('error'); valid = false; }

  if (!valid) {
    form.style.animation = 'none';
    setTimeout(() => form.style.animation = 'shake .4s', 10);
    return;
  }

  const submitBtn = document.getElementById('orderSubmitBtn');
  submitBtn.disabled = true;
  submitBtn.innerHTML = `<i class="fas fa-spinner fa-spin"></i> <span>${t('order.submitting')}</span>`;

  setTimeout(() => {
    const ref = 'AL-' + Date.now().toString().slice(-8);
    const brandLine = item.brand||item.city||'';
    const title = item.name||item.title;
    orderModalContent.innerHTML = `
      <button class="modal-close" id="successCloseBtn" type="button"><i class="fas fa-times"></i></button>
      <div class="order-success">
        <div class="order-success-icon"><i class="fas fa-check"></i></div>
        <h3>${t('order.success.title')}</h3>
        <p>${t('order.success.desc')}</p>
        <div class="order-ref">${t('order.success.ref')}: ${ref}</div>
        <div style="margin-bottom:24px;color:var(--text-soft);font-size:.92rem;">
          <strong style="color:var(--black);">${brandLine} · ${title}</strong>
        </div>
        <button class="btn-primary" id="successCloseBtn2" type="button">${t('order.success.close')}</button>
      </div>
    `;
    document.getElementById('successCloseBtn').addEventListener('click', closeOrderForm);
    document.getElementById('successCloseBtn2').addEventListener('click', closeOrderForm);
  }, 1200);
}

/* ============================================================
   9. CONTACT FORM
   ============================================================ */
function renderContactFormHTML() {
  return `
    <h3>${t('contact.form.title')}</h3>
    <p>${t('contact.form.sub')}</p>
    <form class="contact-form-inner" novalidate>
      <div class="contact-form-row">
        <div class="contact-form-group" data-fg="name">
          <label><span>${t('contact.form.name')}</span> <span class="req">*</span></label>
          <input type="text" name="name" placeholder="Ahmed Benali" required>
          <span class="err-msg">${t('order.err.required')}</span>
        </div>
        <div class="contact-form-group" data-fg="phone">
          <label><span>${t('contact.form.phone')}</span> <span class="req">*</span></label>
          <input type="tel" name="phone" placeholder="0555 12 34 56" required>
          <span class="err-msg">${t('order.err.phone')}</span>
        </div>
      </div>
      <div class="contact-form-group" data-fg="email">
        <label><span>${t('contact.form.email')}</span> <span class="req">*</span></label>
        <input type="email" name="email" placeholder="ahmed@email.com" required>
        <span class="err-msg">${t('contact.form.email.err')}</span>
      </div>
      <div class="contact-form-group" data-fg="subject">
        <label><span>${t('contact.form.subject')}</span> <span class="req">*</span></label>
        <select name="subject" required>
          <option value="">${t('order.select.placeholder')}</option>
          <option value="location">${t('contact.subject.rent')}</option>
          <option value="immo-rent">${t('contact.subject.immoRent')}</option>
          <option value="immo-sale">${t('contact.subject.immoSale')}</option>
          <option value="other">${t('contact.subject.other')}</option>
        </select>
        <span class="err-msg">${t('order.err.required')}</span>
      </div>
      <div class="contact-form-group" data-fg="message">
        <label><span>${t('contact.form.message')}</span> <span class="req">*</span></label>
        <textarea name="message" placeholder="..." required></textarea>
        <span class="err-msg">${t('order.err.required')}</span>
      </div>
      <button type="submit" class="contact-submit">
        <i class="fas fa-paper-plane"></i> <span>${t('contact.form.submit')}</span>
      </button>
    </form>
  `;
}

function wireContactForm(wrapper) {
  if (!wrapper) return;
  const form = wrapper.querySelector('.contact-form-inner');
  if (!form || form.dataset.wired === 'true') return;
  form.dataset.wired = 'true';

  form.addEventListener('submit', (e) => {
    e.preventDefault();
    let valid = true;
    form.querySelectorAll('.contact-form-group').forEach(g => g.classList.remove('error'));

    const name = form.querySelector('input[name="name"]');
    const phone = form.querySelector('input[name="phone"]');
    const email = form.querySelector('input[name="email"]');
    const subject = form.querySelector('select[name="subject"]');
    const message = form.querySelector('textarea[name="message"]');

    if (!name.value.trim()) { name.closest('.contact-form-group').classList.add('error'); valid = false; }
    if (!phone.value.trim() || !/^[\d\s\+\-\(\)]{8,}$/.test(phone.value.trim())) { phone.closest('.contact-form-group').classList.add('error'); valid = false; }
    if (!email.value.trim() || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value.trim())) { email.closest('.contact-form-group').classList.add('error'); valid = false; }
    if (!subject.value) { subject.closest('.contact-form-group').classList.add('error'); valid = false; }
    if (!message.value.trim()) { message.closest('.contact-form-group').classList.add('error'); valid = false; }

    if (!valid) {
      form.style.animation = 'none';
      setTimeout(() => form.style.animation = 'shake .4s', 10);
      return;
    }

    const btn = form.querySelector('.contact-submit');
    btn.disabled = true;
    btn.innerHTML = `<i class="fas fa-spinner fa-spin"></i> <span>${t('contact.form.submitting')}</span>`;

    setTimeout(() => {
      wrapper.innerHTML = `
        <div class="contact-success">
          <i class="fas fa-check-circle"></i>
          <h4>${t('contact.success.title')}</h4>
          <p>${t('contact.success.desc')}</p>
        </div>
      `;
    }, 1200);
  });
}

function rewireContactForms() {
  const w1 = document.getElementById('contactFormWrap');
  if (w1) { w1.innerHTML = renderContactFormHTML(); wireContactForm(w1); }
  const w2 = document.getElementById('contactFormWrap2');
  if (w2) { w2.innerHTML = renderContactFormHTML(); wireContactForm(w2); }
}

/* ============================================================
   10. TREE ENGINE
   ============================================================ */
function createTreeEngine(config) {
  const state = { activeId: 'root' };
  function collectIds(n){const ids=[n.id];if(n.children)n.children.forEach(c=>ids.push(...collectIds(c)));return ids;}
  function countInSubtree(n){const ids=new Set(collectIds(n));return config.items.filter(it=>ids.has(it[config.catKey])).length;}
  function findNode(n,id){if(n.id===id)return n;if(n.children){for(const c of n.children){const f=findNode(c,id);if(f)return f;}}return null;}
  function getFiltered(catId){const n=findNode(config.tree,catId);if(!n)return config.items;const ids=new Set(collectIds(n));return config.items.filter(it=>ids.has(it[config.catKey]));}
  function getPath(leafId){const p=[];(function walk(n,cur){if(n.id===leafId){p.push(...cur,treeName(n));return true;}if(n.children){for(const c of n.children){if(walk(c,[...cur,treeName(n)]))return true;}}return false;})(config.tree,[]);return p.join(' › ');}

  function buildNode(node) {
    const li=document.createElement('li'); li.className='tree-node'; li.dataset.id=node.id;
    const label=document.createElement('div'); label.className='tree-node-label';
    if(node.id===state.activeId) label.classList.add('active');
    const hasChildren=node.children&&node.children.length>0;
    const arrow=document.createElement('i'); arrow.className='fas fa-chevron-right arrow';
    if(!hasChildren) arrow.style.visibility='hidden';
    label.appendChild(arrow);
    const nameSpan=document.createElement('span'); nameSpan.textContent=treeName(node); label.appendChild(nameSpan);
    const count=document.createElement('span'); count.className='count'; count.textContent=countInSubtree(node); label.appendChild(count);
    li.appendChild(label);
    if(hasChildren){const ul=document.createElement('ul');ul.className='tree-children';node.children.forEach(c=>ul.appendChild(buildNode(c)));li.appendChild(ul);}
    label.addEventListener('click',(e)=>{e.stopPropagation();if(hasChildren)li.classList.toggle('open');state.activeId=node.id;renderTree();renderGrid();updateInfo(node);});
    return li;
  }
  function renderTree() {
    config.treeEl.innerHTML=''; config.treeEl.appendChild(buildNode(config.tree));
    if(state.activeId!=='root'){
      const activeLi=config.treeEl.querySelector(`.tree-node[data-id="${state.activeId}"]`);
      if(activeLi){let parent=activeLi.parentElement.closest('.tree-node');while(parent){parent.classList.add('open');parent=parent.parentElement.closest('.tree-node');}if(activeLi.querySelector('.tree-children'))activeLi.classList.add('open');}
    } else { config.treeEl.querySelectorAll(':scope > .tree-node').forEach(li=>li.classList.add('open')); }
  }
  function renderGrid() {
    const items=getFiltered(state.activeId); config.gridEl.innerHTML='';
    if(items.length===0){config.gridEl.innerHTML=`<div class="empty-state"><i class="fas fa-search"></i><p>—</p></div>`;return;}
    items.forEach(it=>config.gridEl.appendChild(createCard(it,getPath(it[config.catKey]))));
  }
  function updateInfo(node){
    const c=countInSubtree(node);
    config.infoEl.textContent = node.id==='root' ? `${t('info.all')} (${c})` : `${treeName(node)} — ${c} ${t('info.results')}`;
  }
  return { init(){renderTree();renderGrid();updateInfo(findNode(config.tree,'root'));} };
}

const engines = {
  cars: createTreeEngine({ tree:carsTree, items:carItems, treeEl:document.getElementById('carsTree'), gridEl:document.getElementById('carsGrid'), infoEl:document.getElementById('carsInfo'), catKey:'cat' }),
  rent: createTreeEngine({ tree:rentTree, items:rentItems, treeEl:document.getElementById('rentTree'), gridEl:document.getElementById('rentGrid'), infoEl:document.getElementById('rentInfo'), catKey:'cat' }),
  sale: createTreeEngine({ tree:saleTree, items:saleItems, treeEl:document.getElementById('saleTree'), gridEl:document.getElementById('saleGrid'), infoEl:document.getElementById('saleInfo'), catKey:'cat' })
};
Object.values(engines).forEach(e => e.init());

function renderHomeGrids() {
  const all = [...carItems, ...rentItems, ...saleItems];
  const top = [...all].sort((a,b)=>parseFloat(b.rating)-parseFloat(a.rating)).slice(0,3);
  const best = [...all].sort((a,b)=>b.sales-a.sales).slice(0,6);
  const tg = document.getElementById('topRatedGrid');
  const bg = document.getElementById('bestSellerGrid');
  tg.innerHTML=''; bg.innerHTML='';
  top.forEach((it,i)=>tg.appendChild(createCard(it,'',{rank:i+1})));
  best.forEach((it,i)=>bg.appendChild(createCard(it,'',{rank:i+1})));
}
renderHomeGrids();

function refreshAllDynamic() {
  renderHomeGrids();
  Object.values(engines).forEach(e => e.init());
  if (detailModal.classList.contains('active')) closeDetailModal();
  if (orderModal.classList.contains('active')) closeOrderForm();
  rewireContactForms();
}

applyTranslations();
rewireContactForms();
</script>
</body>
</html>atform
