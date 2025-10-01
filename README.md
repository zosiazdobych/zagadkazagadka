<!doctype html>
<html lang="pl">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Si – N – Ca = Fe</title>
  <meta name="description" content="Zagadkowa strona: znajdź datę ukrytą w równaniu pierwiastków" />
  <style>
    :root{
      --bg:#0f1c2e;          /* granat */
      --card:#162535;        /* ciemny granat */
      --accent:#e8decf;      /* beż */
      --muted:#b9c3cf;
      --ok:#4bd37b;
      --err:#ff6b6b;
      --shadow:0 10px 30px rgba(0,0,0,.35);
      --radius:18px;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Helvetica Neue", Arial, "Noto Sans", "Liberation Sans", sans-serif;
      color:var(--accent);
      background:
        radial-gradient(1200px 600px at 20% -10%, #19324d 0%, transparent 60%),
        radial-gradient(1000px 700px at 120% 10%, #1b2d45 0%, transparent 60%),
        var(--bg);
      min-height:100svh;
      display:flex;align-items:center;justify-content:center;
      padding:24px;
      overflow-x:hidden;
    }
    .wrap{
      width:min(980px,100%);
      background:linear-gradient(180deg, #182536, #111a28);
      border:1px solid rgba(255,255,255,.06);
      box-shadow:var(--shadow);
      border-radius:28px;
      overflow:hidden;
      position:relative;
    }
    /* ledwo widoczny "watermark" króliczka w tle */
    .wrap::after{
      content:"";
      position:absolute; right:18px; top:18px;
      width:42px; height:42px; opacity:.12;
      background-repeat:no-repeat; background-size:contain;
      filter:grayscale(1);
      background-image:url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="64" height="64" viewBox="0 0 64 64"><g fill="%23e8decf"><circle cx="40" cy="18" r="8"/><circle cx="28" cy="20" r="7"/><ellipse cx="36" cy="36" rx="16" ry="12"/><circle cx="44" cy="36" r="3"/><circle cx="32" cy="36" r="3"/><path d="M25 10c3 0 6 8 6 12s-3 4-6 4-6-2-6-4 3-12 6-12z"/><path d="M44 8c3 0 8 10 8 14s-3 4-6 4-6-2-6-4 1-14 4-14z"/></g></svg>');
      pointer-events:none;
    }
    header{ padding:28px 28px 8px; }
    h1{margin:0;font-weight:800;letter-spacing:.2px}
    p.sub{margin:.25rem 0 0;color:var(--muted)}

    .card{
      background:var(--card);
      margin:20px;
      border-radius:var(--radius);
      padding:22px;
      box-shadow:inset 0 0 0 1px rgba(255,255,255,.04);
    }

    /* EKSPOZYCJA RÓWNANIA */
    .equation{
      display:flex; align-items:center; justify-content:center;
      gap:18px; padding:22px;
      border:1px solid rgba(255,255,255,.08);
      border-radius:18px;
      background:linear-gradient(180deg,#122033,#0f1a29);
    }
    .el, .op{
      display:inline-flex; align-items:center; justify-content:center;
      min-width:72px; height:80px; padding:12px 16px;
      border-radius:14px;
      background:#1b2c40;
      border:1px solid rgba(255,255,255,.08);
      font-weight:800; font-size:28px; letter-spacing:.5px;
      user-select:none;
      box-shadow:inset 0 -6px 14px rgba(0,0,0,.15);
    }
    .op{ opacity:.9 }

    .status{ margin-top:12px; font-size:14px; color:var(--muted); }
    .status.ok{ color:var(--ok) }
    .status.err{ color:var(--err) }

    /* data */
    .step-date{margin-top:18px}
    .inputs{ display:flex; gap:10px; flex-wrap:wrap; align-items:center }
    input[type="text"]{
      padding:12px 14px;
      background:#0f1b2a;
      border:1px solid rgba(255,255,255,.08);
      border-radius:12px;
      color:var(--accent);
      outline:none;
      min-width:220px;
      font-size:16px;
    }
    button{
      padding:12px 16px;border:0;border-radius:12px;
      background:linear-gradient(180deg,#1f3b5b,#1b314d);
      color:var(--accent);font-weight:700;cursor:pointer;
      box-shadow:0 6px 16px rgba(0,0,0,.25);
    }
    button:hover{filter:brightness(1.05)}
    small.hint{color:var(--muted)}
    .tip{color:#9fb3c6;font-size:13px;margin-top:6px}

    /* QR */
    .qr{ display:none; margin-top:12px; text-align:center }
    .qr.active{ display:block; animation:pop .3s ease }
    .qr img{ width:220px; height:220px; image-rendering:pixelated; border-radius:12px; box-shadow:var(--shadow); background:white }
    .qr .placeholder{
      width:220px;height:220px;border-radius:12px;
      display:flex;align-items:center;justify-content:center;
      border:2px dashed rgba(232,222,207,.35); color:var(--muted);
      margin:0 auto; background:#0f1b2a;
    }
    .qr p{ color:var(--muted); margin:.5rem 0 0 }

    /* duży króliczek po sukcesie */
    .bunny-pop{
      position:fixed; inset:auto 16px 16px auto; width:120px; height:120px; display:none;
      animation:pop .35s ease forwards;
      filter: drop-shadow(0 10px 25px rgba(0,0,0,.35));
      z-index:50;
    }
    .bunny-pop.active{ display:block }
    .bunny-pop img{ width:100%; height:100%; }

    @keyframes pop{from{opacity:0;transform:scale(.96)}to{opacity:1;transform:scale(1)}}

    footer{padding:8px 28px 28px;color:var(--muted);font-size:12px}
    .badge{display:inline-block;padding:6px 10px;border:1px solid rgba(255,255,255,.08);border-radius:10px}

    /* mobile */
    @media (max-width:720px){
      .el,.op{ height:64px; min-width:60px; font-size:22px }
    }
  </style>
</head>
<body>
  <div class="wrap" role="application" aria-label="Zagadka chemiczna z kodem QR">
    <header>
      <h1 title="kliknij w nagłówek 3× — króliczek pomacha 🐇">Si – N – Ca = Fe</h1>
      <p class="sub">Masz wszystko, czego potrzebujesz. Wpisz właściwą <em>datę</em> i odsłoń nagrodę.</p>
    </header>

    <div class="card" aria-live="polite">
      <div class="equation" aria-label="Równanie z symbolami pierwiastków">
        <div class="el">Si</div>
        <div class="op">–</div>
        <div class="el">N</div>
        <div class="op">–</div>
        <div class="el">Ca</div>
        <div class="op">=</div>
        <div class="el">Fe</div>
      </div>

      <div class="status" id="status">Podpowiedź subtelna: pomyśl, co „kryje się” za każdym symbolem…</div>

      <div class="step-date" id="stepDate">
        <div class="inputs" style="margin-top:12px">
          <input id="dateInput" type="text" inputmode="numeric" autocomplete="off"
                 placeholder="dd.mm.rrrr" aria-label="Wpisz datę" />
          <button id="checkBtn">Sprawdź</button>
          <!-- Dyskretna podpowiedź rozwijana -->
          <details style="margin-left:4px">
            <summary>?</summary>
            <div class="tip">Jeśli utkniesz: zamień symbole na ich <em>liczby atomowe</em>, a zapis ułóż jak datę.</div>
          </details>
        </div>
        <small class="hint">Akceptowane formaty: 14.07.2026, 14-07-2026, 14/07/2026</small>
        <div id="dateMsg" class="status" role="status"></div>
      </div>

      <!-- QR -->
      <div class="qr" id="qrBox" aria-hidden="true">
        <div id="qrHolder" class="placeholder">Tu pojawi się kod QR</div>
        <p id="qrCaption">Dodaj swój link w kodzie, aby wygenerować prawdziwy QR.</p>
      </div>
    </div>

    <footer>
      <span class="badge">Design: granat &amp; beż • mobile ready • ukryty króliczek 🐇</span>
    </footer>
  </div>

  <!-- duży króliczek po sukcesie -->
  <div class="bunny-pop" id="bunnyPop" aria-hidden="true">
    <img alt="Króliczek" src='data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="180" height="180" viewBox="0 0 64 64"><defs><linearGradient id="g" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="%23e8decf"/><stop offset="1" stop-color="%23cfc4b3"/></linearGradient></defs><g fill="url(%23g)"><circle cx="42" cy="20" r="10"/><circle cx="26" cy="22" r="9"/><ellipse cx="34" cy="40" rx="18" ry="13"/><circle cx="44" cy="38" r="3"/><circle cx="30" cy="38" r="3"/><path d="M22 8c3 0 7 10 7 15s-3 4-7 4-7-2-7-4 4-15 7-15z"/><path d="M44 6c4 0 9 12 9 17s-3 4-7 4-7-2-7-4 1-17 5-17z"/></g></svg>'/>
  </div>

  <script>
    /* ====== USTAWIENIA DO PODMIANY PRZEZ CIEBIE ======
       1) Gdy będziesz mieć docelowy link/tekst do QR – wpisz go tu:
    */
    const QR_DATA = ""; // np. "https://twoj-link.pl/sekret"
    /*  2) Jeśli chcesz inną datę niż ta wynikająca z Si(14) – N(7) – Ca(20) = Fe(26),
          podmień expected poniżej. */
    const expected = { dd:14, mm:7, yyyy:2026 }; // 14-07-2026

    const status = document.getElementById('status');
    const dateInput = document.getElementById('dateInput');
    const dateMsg = document.getElementById('dateMsg');
    const qrBox = document.getElementById('qrBox');
    const qrHolder = document.getElementById('qrHolder');
    const qrCaption = document.getElementById('qrCaption');
    const bunnyPop = document.getElementById('bunnyPop');

    document.getElementById('checkBtn').addEventListener('click', verifyDate);
    dateInput.addEventListener('keydown', e=>{ if(e.key==='Enter') verifyDate(); });
    dateInput.addEventListener('input', ()=>{ dateInput.value = dateInput.value.replace(/\s+/g,'.'); });

    function normalizeDate(str){
      if(!str) return null;
      const cleaned = str.trim().replace(/[–—]/g,'-');
      const parts = cleaned.split(/[.\-\/]/).map(s=>s.trim());
      if(parts.length!==3) return null;
      const [d,m,y] = parts.map(p=>parseInt(p,10));
      if(Number.isNaN(d)||Number.isNaN(m)||Number.isNaN(y)) return null;
      return {dd:d, mm:m, yyyy:y};
    }

    function verifyDate(){
      const got = normalizeDate(dateInput.value);
      if(!got){
        dateMsg.textContent = 'Wpisz datę w formacie dd.mm.rrrr.';
        dateMsg.className = 'status err';
        qrBox.classList.remove('active');
        return;
      }
      if(got.dd===expected.dd && got.mm===expected.mm && got.yyyy===expected.yyyy){
        dateMsg.textContent = 'Dobrze! Odsłaniam kod QR.';
        dateMsg.className = 'status ok';
        showQR();
        showBunny();
      }else{
        dateMsg.textContent = 'Nie ta data. Spróbuj spojrzeć na znaki w inny sposób…';
        dateMsg.className = 'status err';
        qrBox.classList.remove('active');
        bunnyPop.classList.remove('active');
      }
    }

    function showQR(){
      qrBox.classList.add('active');
      qrBox.setAttribute('aria-hidden','false');

      if (QR_DATA && QR_DATA.trim().length){
        const url = "https://api.qrserver.com/v1/create-qr-code/?size=220x220&data=" + encodeURIComponent(QR_DATA.trim());
        const img = new Image();
        img.width = 220; img.height = 220; img.alt = "Kod QR";
        img.onload = ()=>{
          qrHolder.replaceWith(img);
          qrCaption.textContent = 'Zeskanuj kod QR.';
        };
        img.onerror = ()=>{
          qrCaption.textContent = 'Nie udało się pobrać QR. Wstaw link później lub sprawdź połączenie.';
        };
        img.src = url;
      } else {
        qrCaption.innerHTML = 'Ustaw najpierw docelowy link w pliku (stała <code>QR_DATA</code>).';
      }
    }

    function showBunny(){
      bunnyPop.classList.add('active');
      bunnyPop.setAttribute('aria-hidden','false');
      setTimeout(()=>{ bunnyPop.classList.remove('active'); }, 6000);
    }

    // Easter egg: 3× klik w nagłówek → królik
    (function(){
      const h1 = document.querySelector('h1');
      let clicks = 0, timer = null;
      h1.addEventListener('click', ()=>{
        clicks++; clearTimeout(timer);
        timer = setTimeout(()=>{ clicks=0; }, 800);
        if(clicks>=3){ showBunny(); clicks=0; }
      });
    })();
  </script>
</body>
</html>
