<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Robot Trading XAUUSD + BTC</title>
  <meta name="description" content="Dashboard monitoring robot trading XAUUSD dan BTC">
  <style>
    :root{
      --bg:#07111f;
      --panel:#0d1b2a;
      --panel2:#102235;
      --line:#1d344b;
      --text:#edf5ff;
      --muted:#8fa6bd;
      --green:#21d07a;
      --red:#ff5b68;
      --yellow:#ffc857;
      --blue:#4da3ff;
      --shadow:0 12px 35px rgba(0,0,0,.25);
      --radius:18px;
    }
    *{box-sizing:border-box}
    body{
      margin:0;
      font-family:Inter,system-ui,-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;
      background:linear-gradient(135deg,#06101c 0%,#09192a 55%,#07111f 100%);
      color:var(--text);
      min-height:100vh;
    }
    button,input,select{font:inherit}
    .app{display:flex;min-height:100vh}
    .sidebar{
      width:250px;background:rgba(7,17,31,.92);border-right:1px solid var(--line);
      padding:22px 16px;position:fixed;inset:0 auto 0 0;z-index:10;
      backdrop-filter:blur(14px);
    }
    .brand{display:flex;align-items:center;gap:12px;padding:6px 8px 24px}
    .logo{
      width:43px;height:43px;border-radius:13px;display:grid;place-items:center;
      background:linear-gradient(135deg,#1d8fff,#6a5cff);font-size:22px;
      box-shadow:0 8px 24px rgba(50,120,255,.25)
    }
    .brand strong{display:block;font-size:15px}
    .brand span{display:block;color:var(--muted);font-size:11px;margin-top:3px}
    nav{display:grid;gap:7px}
    nav button{
      width:100%;border:0;background:transparent;color:#9fb2c5;text-align:left;
      padding:12px 13px;border-radius:12px;cursor:pointer;font-weight:600;
    }
    nav button:hover,nav button.active{background:#11283e;color:white}
    .side-bottom{position:absolute;left:16px;right:16px;bottom:20px}
    .mode{
      border:1px solid var(--line);background:#0b1a2a;padding:12px;border-radius:14px;
      color:var(--muted);font-size:12px
    }
    .mode b{color:var(--yellow)}
    main{margin-left:250px;width:calc(100% - 250px);padding:28px}
    .topbar{display:flex;justify-content:space-between;gap:16px;align-items:center;margin-bottom:24px}
    .topbar h1{font-size:26px;margin:0 0 5px}
    .topbar p{margin:0;color:var(--muted);font-size:13px}
    .actions{display:flex;gap:10px;align-items:center}
    .status-pill{
      display:flex;align-items:center;gap:8px;padding:10px 13px;border-radius:999px;
      border:1px solid rgba(33,208,122,.25);background:rgba(33,208,122,.08);
      color:#b9f7d7;font-size:12px;font-weight:700
    }
    .dot{width:8px;height:8px;background:var(--green);border-radius:50%;box-shadow:0 0 10px var(--green)}
    .btn{
      border:1px solid var(--line);background:#102235;color:white;padding:10px 14px;
      border-radius:11px;cursor:pointer;font-weight:700
    }
    .btn:hover{background:#17304a}
    .btn.primary{background:#1778db;border-color:#268df2}
    .grid{display:grid;gap:16px}
    .stats{grid-template-columns:repeat(4,minmax(0,1fr));margin-bottom:16px}
    .card{
      background:linear-gradient(145deg,rgba(16,34,53,.94),rgba(10,25,40,.94));
      border:1px solid var(--line);border-radius:var(--radius);box-shadow:var(--shadow)
    }
    .stat{padding:18px}
    .label{color:var(--muted);font-size:12px}
    .value{font-size:25px;font-weight:800;margin-top:8px}
    .sub{font-size:11px;color:var(--muted);margin-top:7px}
    .green{color:var(--green)!important}.red{color:var(--red)!important}
    .yellow{color:var(--yellow)!important}.blue{color:var(--blue)!important}
    .two{grid-template-columns:1.55fr 1fr;margin-bottom:16px}
    .card-head{padding:17px 18px;border-bottom:1px solid var(--line);display:flex;justify-content:space-between;align-items:center}
    .card-head h2{font-size:15px;margin:0}
    .card-body{padding:18px}
    .assets{grid-template-columns:repeat(2,minmax(0,1fr))}
    .asset{padding:18px}
    .asset-head{display:flex;justify-content:space-between;align-items:center}
    .asset-name{font-size:17px;font-weight:800}
    .tag{font-size:10px;padding:5px 8px;border-radius:999px;background:#16304a;color:#a9c5dd}
    .price{font-size:27px;font-weight:800;margin:17px 0 4px}
    .change{font-size:12px}
    .asset-grid{display:grid;grid-template-columns:1fr 1fr;gap:9px;margin-top:18px}
    .mini{background:#091827;border:1px solid #173047;border-radius:11px;padding:10px}
    .mini span{display:block;color:var(--muted);font-size:10px}
    .mini b{display:block;margin-top:4px;font-size:13px}
    .signal{
      margin-top:14px;padding:11px;border-radius:12px;text-align:center;font-weight:800;
      background:rgba(255,200,87,.08);border:1px solid rgba(255,200,87,.2);color:var(--yellow)
    }
    .chart-wrap{height:300px;position:relative}
    canvas{width:100%;height:100%;display:block}
    .legend{display:flex;gap:18px;color:var(--muted);font-size:11px;margin-top:10px}
    .legend i{width:8px;height:8px;border-radius:50%;display:inline-block;margin-right:5px}
    .table-wrap{overflow:auto}
    table{width:100%;border-collapse:collapse;min-width:650px}
    th,td{text-align:left;padding:13px 12px;border-bottom:1px solid var(--line);font-size:12px}
    th{color:var(--muted);font-weight:600}
    td{color:#dbe8f5}
    .badge{padding:5px 8px;border-radius:7px;font-size:10px;font-weight:800}
    .badge.buy{background:rgba(33,208,122,.12);color:var(--green)}
    .badge.sell{background:rgba(255,91,104,.12);color:var(--red)}
    .badge.close{background:rgba(77,163,255,.12);color:var(--blue)}
    .risk{display:grid;gap:12px}
    .risk-row{display:flex;justify-content:space-between;font-size:12px}
    .progress{height:8px;background:#071421;border-radius:99px;overflow:hidden;margin-top:8px}
    .bar{height:100%;border-radius:99px;background:linear-gradient(90deg,#21d07a,#ffc857)}
    .settings{display:grid;grid-template-columns:1fr 1fr;gap:14px}
    .field{display:grid;gap:6px}
    .field label{font-size:11px;color:var(--muted)}
    .field input,.field select{
      width:100%;padding:10px 11px;border-radius:10px;border:1px solid var(--line);
      background:#081726;color:white;outline:none
    }
    .field input:focus,.field select:focus{border-color:#2b8ce8}
    .notice{
      padding:13px;border-radius:12px;background:rgba(255,200,87,.07);
      border:1px solid rgba(255,200,87,.18);color:#e8d7a6;font-size:11px;line-height:1.5
    }
    .setting-summary{
      margin-top:15px;padding:13px 14px;border-radius:12px;
      background:rgba(77,163,255,.07);border:1px solid rgba(77,163,255,.18);
      font-size:11px;line-height:1.6;color:var(--muted)
    }
    .setting-summary b{color:var(--text)}
    .hidden{display:none}
    .toast{
      position:fixed;right:22px;bottom:22px;background:#10263a;border:1px solid #254764;
      color:white;padding:13px 16px;border-radius:12px;box-shadow:var(--shadow);
      opacity:0;transform:translateY(12px);pointer-events:none;transition:.25s;z-index:99;font-size:12px
    }
    .toast.show{opacity:1;transform:none}
    @media(max-width:1050px){
      .stats{grid-template-columns:1fr 1fr}.two{grid-template-columns:1fr}
    }
    @media(max-width:760px){
      .sidebar{width:70px;padding:16px 9px}.brand{justify-content:center;padding-bottom:18px}.brand div:not(.logo),
      nav button span,.side-bottom{display:none}.logo{width:42px}
      nav button{text-align:center;padding:12px 5px;font-size:18px}
      main{margin-left:70px;width:calc(100% - 70px);padding:17px}
      .topbar{align-items:flex-start}.topbar h1{font-size:21px}.status-pill{display:none}
      .stats,.assets,.settings{grid-template-columns:1fr}
      .actions .btn{padding:9px}
    }
  </style>
</head>
<body>
<div class="app">
  <aside class="sidebar">
    <div class="brand">
      <div class="logo">🤖</div>
      <div><strong>TRADING BOT</strong><span>XAUUSD + BTC</span></div>
    </div>
    <nav>
      <button class="active" data-page="dashboard"><span>📊</span> Dashboard</button>
      <button data-page="positions"><span>📈</span> Posisi</button>
      <button data-page="history"><span>🧾</span> Riwayat</button>
      <button data-page="settings"><span>⚙️</span> Pengaturan</button>
    </nav>
    <div class="side-bottom">
      <div class="mode">Mode: <b id="modeText">DEMO</b><br><span>Data contoh / simulasi</span></div>
    </div>
  </aside>

  <main>
    <section id="page-dashboard">
      <div class="topbar">
        <div>
          <h1>Dashboard Robot Trading</h1>
          <p id="clock">Memuat waktu...</p>
        </div>
        <div class="actions">
          <div class="status-pill"><span class="dot"></span> ROBOT ONLINE</div>
          <button class="btn primary" id="toggleBot">⏸ Matikan Robot</button>
        </div>
      </div>

      <div class="grid stats">
        <div class="card stat"><div class="label">Balance</div><div class="value" id="balance">$10,000.00</div><div class="sub">Saldo akun</div></div>
        <div class="card stat"><div class="label">Equity</div><div class="value" id="equity">$10,245.30</div><div class="sub" id="equitySub">Floating P/L +$245.30</div></div>
        <div class="card stat"><div class="label">Profit Hari Ini</div><div class="value green" id="todayProfit">+$145.80</div><div class="sub">Dari 3 transaksi</div></div>
        <div class="card stat"><div class="label">Win Rate</div><div class="value blue" id="winRate">66.7%</div><div class="sub">20 transaksi terakhir</div></div>
      </div>

      <div class="grid two">
        <div class="card">
          <div class="card-head"><h2>Pergerakan Harga</h2><select id="chartAsset" class="btn"><option>XAUUSD</option><option>BTCUSD</option></select></div>
          <div class="card-body">
            <div class="chart-wrap"><canvas id="chart"></canvas></div>
            <div class="legend"><span><i style="background:#4da3ff"></i>Harga simulasi</span><span id="chartInfo">M15</span></div>
          </div>
        </div>

        <div class="card">
          <div class="card-head"><h2>Risk Management</h2><span class="tag">AKTIF</span></div>
          <div class="card-body risk">
            <div><div class="risk-row"><span>Risiko / transaksi</span><b id="riskValue">1%</b></div><div class="progress"><div class="bar" style="width:33%"></div></div></div>
            <div><div class="risk-row"><span>Loss hari ini</span><b id="lossValue">0.0%</b></div><div class="progress"><div class="bar" style="width:5%"></div></div></div>
            <div><div class="risk-row"><span>Trade hari ini</span><b id="tradeValue">3 / 5</b></div><div class="progress"><div class="bar" style="width:60%"></div></div></div>
            <div class="notice">Proteksi harian aktif. Batas loss contoh: 3%. Parameter ini hanya tampilan dashboard dan belum mengendalikan akun broker.</div>
          </div>
        </div>
      </div>

      <div class="card">
        <div class="card-head"><h2>Market Monitor</h2><span class="tag">AUTO REFRESH</span></div>
        <div class="card-body">
          <div class="grid assets">
            <div class="asset">
              <div class="asset-head"><div class="asset-name">🪙 XAUUSD</div><span class="tag">GOLD</span></div>
              <div class="price" id="goldPrice">2,650.40</div><div class="change green" id="goldChange">▲ +0.42%</div>
              <div class="asset-grid">
                <div class="mini"><span>EMA 9</span><b id="goldEma9">2,649.82</b></div>
                <div class="mini"><span>EMA 21</span><b id="goldEma21">2,647.95</b></div>
                <div class="mini"><span>RSI 14</span><b id="goldRsi">57.8</b></div>
                <div class="mini"><span>ATR 14</span><b id="goldAtr">4.21</b></div>
              </div>
              <div class="signal" id="goldSignal">WAIT — Menunggu konfirmasi</div>
            </div>
            <div class="asset">
              <div class="asset-head"><div class="asset-name">₿ BTCUSD</div><span class="tag">BITCOIN</span></div>
              <div class="price" id="btcPrice">106,420.00</div><div class="change green" id="btcChange">▲ +1.12%</div>
              <div class="asset-grid">
                <div class="mini"><span>EMA 9</span><b id="btcEma9">106,210</b></div>
                <div class="mini"><span>EMA 21</span><b id="btcEma21">105,870</b></div>
                <div class="mini"><span>RSI 14</span><b id="btcRsi">59.4</b></div>
                <div class="mini"><span>ATR 14</span><b id="btcAtr">1,280</b></div>
              </div>
              <div class="signal" id="btcSignal">WAIT — Menunggu konfirmasi</div>
            </div>
          </div>
        </div>
      </div>
    </section>

    <section id="page-positions" class="hidden">
      <div class="topbar"><div><h1>Posisi Terbuka</h1><p>Posisi yang sedang dipantau robot.</p></div></div>
      <div class="card"><div class="card-head"><h2>Open Positions</h2><span class="tag" id="positionCount">2 posisi</span></div>
        <div class="table-wrap"><table><thead><tr><th>Symbol</th><th>Type</th><th>Lot</th><th>Entry</th><th>SL</th><th>TP</th><th>Floating P/L</th></tr></thead>
        <tbody id="positionTable"></tbody></table></div>
      </div>
    </section>

    <section id="page-history" class="hidden">
      <div class="topbar"><div><h1>Riwayat Transaksi</h1><p>Contoh histori transaksi robot.</p></div><button class="btn" id="clearHistory">Bersihkan Tampilan</button></div>
      <div class="card"><div class="card-head"><h2>Trade History</h2><span class="tag">DEMO</span></div>
        <div class="table-wrap"><table><thead><tr><th>Waktu</th><th>Symbol</th><th>Type</th><th>Entry</th><th>Exit</th><th>Hasil</th><th>Status</th></tr></thead>
        <tbody id="historyTable"></tbody></table></div>
      </div>
    </section>

    <section id="page-settings" class="hidden">
      <div class="topbar"><div><h1>Pengaturan Robot</h1><p>Atur tampilan, instrumen, strategi, timeframe, dan batas posisi.</p></div></div>
      <div class="card"><div class="card-head"><h2>Risk & Strategy</h2></div><div class="card-body">
        <div class="settings">
          <div class="field" style="grid-column:1/-1">
            <label>Akun Trading</label>
            <input id="tradingLogin" type="text" inputmode="numeric" placeholder="Masukkan nomor akun trading">
          </div>
          <div class="field">
            <label>Password Trading</label>
            <input id="tradingPassword" type="password" placeholder="Masukkan password trading">
          </div>
          <div class="field">
            <label>Server Broker</label>
            <input id="tradingServer" type="text" placeholder="Contoh: Broker-Live / Broker-Demo">
          </div>
          <div class="field">
            <label>Tampilan</label>
            <select id="themeSelect">
              <option value="dark">🌙 Mode Gelap</option>
              <option value="light">☀️ Mode Terang</option>
            </select>
          </div>
          <div class="field">
            <label>Mode Akun</label>
            <select id="modeSelect"><option>DEMO</option><option>LIVE</option></select>
          </div>

          <div class="field">
            <label>Instrumen</label>
            <select id="assetSelect">
              <option value="XAUUSD">🪙 XAUUSD</option>
              <option value="BTCUSD">₿ BTCUSD</option>
              <option value="BOTH">🪙 XAUUSD + ₿ BTCUSD</option>
            </select>
          </div>
          <div class="field">
            <label>Mode Trading</label>
            <select id="strategySelect">
              <option>Normal</option>
              <option>Scalping</option>
            </select>
          </div>

          <div class="field">
            <label>Timeframe Entry</label>
            <select id="timeframeSelect">
              <option>M1</option><option>M5</option><option>M15</option><option>M30</option><option>H1</option>
            </select>
          </div>
          <div class="field">
            <label>Maksimal Posisi Terbuka</label>
            <select id="maxPositions">
              <option>1</option><option>2</option><option selected>3</option><option>5</option><option>10</option>
            </select>
          </div>

          <div class="field">
            <label>Maks. Posisi XAUUSD</label>
            <select id="maxGoldPositions">
              <option>1</option><option selected>2</option><option>3</option><option>5</option><option>10</option>
            </select>
          </div>
          <div class="field">
            <label>Maks. Posisi BTCUSD</label>
            <select id="maxBtcPositions">
              <option selected>1</option><option>2</option><option>3</option><option>5</option><option>10</option>
            </select>
          </div>

          <div class="field"><label>EMA Cepat</label><input id="emaFast" value="9" type="number"></div>
          <div class="field"><label>EMA Lambat</label><input id="emaSlow" value="21" type="number"></div>
          <div class="field"><label>RSI Period</label><input id="rsiPeriod" value="14" type="number"></div>
          <div class="field"><label>Risiko / Transaksi (%)</label><input id="riskInput" value="1" type="number" min="0.1" step="0.1"></div>
          <div class="field"><label>SL ATR Multiplier</label><input id="slAtr" value="1.5" type="number" step="0.1"></div>
          <div class="field"><label>TP ATR Multiplier</label><input id="tpAtr" value="3" type="number" step="0.1"></div>
        </div>
        <br>
        <div class="notice">Akun, server, dan password di bawah hanya disimpan untuk kebutuhan tampilan lokal. File HTML ini belum dapat terhubung langsung ke MT5/broker atau menjalankan order nyata tanpa backend/API. Untuk keamanan, password tidak disimpan ke localStorage.</div>
        <div class="setting-summary" id="accountSummary">Belum ada akun trading yang dimasukkan.</div>
        <br><button class="btn primary" id="saveSettings">Simpan Pengaturan</button>
        <button class="btn" id="clearTradingAccount" type="button">Hapus Akun</button>
      </div></div>
    </section>
  </main>
</div>
<div class="toast" id="toast"></div>

<script>
  // ================================================================
  // DASHBOARD DEMO
  // ================================================================
  // Data di bawah sengaja berupa SIMULASI.
  // Untuk data live, ganti fungsi getMarketData() dengan API/backend
  // milik Anda. Jangan taruh API secret/password broker di frontend.
  // ================================================================

  let botRunning = true;
  let selectedAsset = "XAUUSD";

  const state = {
    balance: 10000,
    equity: 10245.30,
    todayProfit: 145.80,
    gold: {price:2650.40, change:0.42, ema9:2649.82, ema21:2647.95, rsi:57.8, atr:4.21},
    btc: {price:106420, change:1.12, ema9:106210, ema21:105870, rsi:59.4, atr:1280}
  };

  const positions = [
    {symbol:"XAUUSD",type:"BUY",lot:"0.10",entry:"2646.80",sl:"2640.49",tp:"2659.42",pl:"+$36.00"},
    {symbol:"BTCUSD",type:"BUY",lot:"0.01",entry:"105850",sl:"103290",tp:"110970",pl:"+$82.40"}
  ];

  let history = [
    {time:"08:21",symbol:"XAUUSD",type:"BUY",entry:"2638.20",exit:"2645.90",result:"+$77.00",status:"CLOSED"},
    {time:"07:42",symbol:"BTCUSD",type:"SELL",entry:"106180",exit:"105920",result:"+$26.00",status:"CLOSED"},
    {time:"06:55",symbol:"XAUUSD",type:"SELL",entry:"2649.80",exit:"2645.20",result:"+$46.00",status:"CLOSED"},
    {time:"Kemarin",symbol:"BTCUSD",type:"BUY",entry:"104200",exit:"103650",result:"-$55.00",status:"CLOSED"}
  ];

  function money(n){ return (n>=0?"+$":"-$")+Math.abs(n).toLocaleString("en-US",{minimumFractionDigits:2,maximumFractionDigits:2}); }
  function num(n,d=2){ return Number(n).toLocaleString("en-US",{minimumFractionDigits:d,maximumFractionDigits:d}); }
  function toast(msg){
    const el=document.getElementById("toast");
    el.textContent=msg; el.classList.add("show");
    clearTimeout(window._toast); window._toast=setTimeout(()=>el.classList.remove("show"),2200);
  }

  function renderPositions(){
    const tb=document.getElementById("positionTable");
    tb.innerHTML=positions.map(p=>`
      <tr>
        <td><b>${p.symbol}</b></td>
        <td><span class="badge ${p.type==="BUY"?"buy":"sell"}">${p.type}</span></td>
        <td>${p.lot}</td><td>${p.entry}</td><td>${p.sl}</td><td>${p.tp}</td>
        <td class="green"><b>${p.pl}</b></td>
      </tr>`).join("");
    document.getElementById("positionCount").textContent=positions.length+" posisi";
  }

  function renderHistory(){
    const tb=document.getElementById("historyTable");
    tb.innerHTML=history.map(h=>`
      <tr>
        <td>${h.time}</td><td><b>${h.symbol}</b></td>
        <td><span class="badge ${h.type==="BUY"?"buy":"sell"}">${h.type}</span></td>
        <td>${h.entry}</td><td>${h.exit}</td>
        <td class="${h.result.startsWith("+")?"green":"red"}"><b>${h.result}</b></td>
        <td><span class="badge close">${h.status}</span></td>
      </tr>`).join("");
  }

  function updateMarket(){
    const g=state.gold, b=state.btc;
    g.price += (Math.random()-.47)*1.8;
    b.price += (Math.random()-.47)*420;
    g.ema9 += (g.price-g.ema9)*.08;
    g.ema21 += (g.price-g.ema21)*.035;
    b.ema9 += (b.price-b.ema9)*.08;
    b.ema21 += (b.price-b.ema21)*.035;
    g.rsi=Math.max(30,Math.min(70,g.rsi+(Math.random()-.5)*1.3));
    b.rsi=Math.max(30,Math.min(70,b.rsi+(Math.random()-.5)*1.3));

    document.getElementById("goldPrice").textContent=num(g.price,2);
    document.getElementById("goldEma9").textContent=num(g.ema9,2);
    document.getElementById("goldEma21").textContent=num(g.ema21,2);
    document.getElementById("goldRsi").textContent=num(g.rsi,1);

    document.getElementById("btcPrice").textContent=num(b.price,2);
    document.getElementById("btcEma9").textContent=num(b.ema9,0);
    document.getElementById("btcEma21").textContent=num(b.ema21,0);
    document.getElementById("btcRsi").textContent=num(b.rsi,1);

    const gs=g.ema9>g.ema21 && g.rsi>50 ? "BUY — Konfirmasi tren" :
             g.ema9<g.ema21 && g.rsi<50 ? "SELL — Konfirmasi tren" :
             "WAIT — Menunggu konfirmasi";
    const bs=b.ema9>b.ema21 && b.rsi>50 ? "BUY — Konfirmasi tren" :
             b.ema9<b.ema21 && b.rsi<50 ? "SELL — Konfirmasi tren" :
             "WAIT — Menunggu konfirmasi";
    document.getElementById("goldSignal").textContent=gs;
    document.getElementById("btcSignal").textContent=bs;

    drawChart();
  }

  function drawChart(){
    const canvas=document.getElementById("chart");
    const rect=canvas.getBoundingClientRect();
    const dpr=window.devicePixelRatio||1;
    canvas.width=rect.width*dpr; canvas.height=rect.height*dpr;
    const ctx=canvas.getContext("2d"); ctx.scale(dpr,dpr);
    const w=rect.width,h=rect.height;
    ctx.clearRect(0,0,w,h);

    // grid
    ctx.strokeStyle="rgba(143,166,189,.10)"; ctx.lineWidth=1;
    for(let i=1;i<6;i++){let y=i*h/6;ctx.beginPath();ctx.moveTo(0,y);ctx.lineTo(w,y);ctx.stroke();}
    for(let i=1;i<8;i++){let x=i*w/8;ctx.beginPath();ctx.moveTo(x,0);ctx.lineTo(x,h);ctx.stroke();}

    const base=selectedAsset==="XAUUSD"?state.gold.price:state.btc.price;
    const amp=selectedAsset==="XAUUSD"?3.2:1200;
    const pts=[];
    let v=base;
    for(let i=0;i<70;i++){
      v += (Math.random()-.49)*amp;
      pts.push(v);
    }
    const min=Math.min(...pts), max=Math.max(...pts);
    ctx.beginPath();
    pts.forEach((p,i)=>{
      const x=i*(w-10)/(pts.length-1)+5;
      const y=h-((p-min)/(max-min||1))*(h-24)-12;
      i?ctx.lineTo(x,y):ctx.moveTo(x,y);
    });
    ctx.strokeStyle="#4da3ff";ctx.lineWidth=2.2;ctx.stroke();
  }

  function updateClock(){
    const d=new Date();
    document.getElementById("clock").textContent =
      d.toLocaleDateString("id-ID",{weekday:"long",day:"numeric",month:"long",year:"numeric"})+
      " • "+d.toLocaleTimeString("id-ID");
  }

  document.querySelectorAll("nav button").forEach(btn=>{
    btn.addEventListener("click",()=>{
      document.querySelectorAll("nav button").forEach(b=>b.classList.remove("active"));
      btn.classList.add("active");
      document.querySelectorAll("main > section").forEach(s=>s.classList.add("hidden"));
      document.getElementById("page-"+btn.dataset.page).classList.remove("hidden");
      if(btn.dataset.page==="positions") renderPositions();
      if(btn.dataset.page==="history") renderHistory();
      if(btn.dataset.page==="dashboard") setTimeout(drawChart,50);
    });
  });

  document.getElementById("toggleBot").addEventListener("click",e=>{
    botRunning=!botRunning;
    e.target.textContent=botRunning?"⏸ Matikan Robot":"▶ Aktifkan Robot";
    document.querySelector(".status-pill").innerHTML=botRunning
      ? '<span class="dot"></span> ROBOT ONLINE'
      : '<span class="dot" style="background:#ff5b68;box-shadow:0 0 10px #ff5b68"></span> ROBOT OFFLINE';
    toast(botRunning?"Robot diaktifkan (tampilan demo).":"Robot dimatikan (tampilan demo).");
  });

  document.getElementById("chartAsset").addEventListener("change",e=>{
    selectedAsset=e.target.value; drawChart();
  });

  const settingIds = [
    "themeSelect","modeSelect","assetSelect","strategySelect","timeframeSelect",
    "maxPositions","maxGoldPositions","maxBtcPositions",
    "emaFast","emaSlow","rsiPeriod","riskInput","slAtr","tpAtr",
    "tradingLogin","tradingServer"
  ];

  function loadSettings(){
    settingIds.forEach(id=>{
      const el=document.getElementById(id);
      const saved=localStorage.getItem("robot_"+id);
      if(el && saved!==null) el.value=saved;
    });
    applyTheme();
    document.getElementById("modeText").textContent=document.getElementById("modeSelect").value;
    const tf=document.getElementById("timeframeSelect").value;
    document.getElementById("chartInfo").textContent=tf;
    updateAccountSummary();
  }

  function updateAccountSummary(){
    const login=document.getElementById("tradingLogin").value.trim();
    const server=document.getElementById("tradingServer").value.trim();
    const mode=document.getElementById("modeSelect").value;
    const summary=document.getElementById("accountSummary");
    if(login){
      summary.innerHTML="Akun: <b>"+login+"</b> • Server: <b>"+(server||"Belum diisi")+"</b> • Mode: <b>"+mode+"</b><br>Status koneksi: <b class="yellow">Belum terhubung ke broker</b>";
    }else{
      summary.textContent="Belum ada akun trading yang dimasukkan.";
    }
  }

  function applyTheme(){
    const theme=document.getElementById("themeSelect").value;
    document.body.classList.toggle("light",theme==="light");
  }

  document.getElementById("themeSelect").addEventListener("change",()=>{
    applyTheme();
    localStorage.setItem("robot_themeSelect",document.getElementById("themeSelect").value);
    toast(document.getElementById("themeSelect").value==="light"?"Mode terang aktif.":"Mode gelap aktif.");
  });

  document.getElementById("modeSelect").addEventListener("change",e=>{
    document.getElementById("modeText").textContent=e.target.value;
    localStorage.setItem("robot_modeSelect",e.target.value);
    toast("Mode akun diubah ke "+e.target.value);
  });

  document.getElementById("timeframeSelect").addEventListener("change",e=>{
    document.getElementById("chartInfo").textContent=e.target.value;
  });

  document.getElementById("tradingLogin").addEventListener("input",updateAccountSummary);
  document.getElementById("tradingServer").addEventListener("input",updateAccountSummary);

  document.getElementById("clearTradingAccount").addEventListener("click",()=>{
    document.getElementById("tradingLogin").value="";
    document.getElementById("tradingServer").value="";
    document.getElementById("tradingPassword").value="";
    localStorage.removeItem("robot_tradingLogin");
    localStorage.removeItem("robot_tradingServer");
    updateAccountSummary();
    toast("Data akun trading dihapus.");
  });

  document.getElementById("saveSettings").addEventListener("click",()=>{
    settingIds.forEach(id=>{
      const el=document.getElementById(id);
      if(el) localStorage.setItem("robot_"+id,el.value);
    });
    const v=parseFloat(document.getElementById("riskInput").value)||1;
    document.getElementById("riskValue").textContent=v+"%";
    document.querySelector(".risk .bar").style.width=Math.min(100,v*33)+"%";
    document.getElementById("chartInfo").textContent=document.getElementById("timeframeSelect").value;
    toast("Pengaturan robot berhasil disimpan.");
  });

  document.getElementById("clearHistory").addEventListener("click",()=>{
    history=[];
    renderHistory();
    toast("Riwayat tampilan dibersihkan.");
  });

  window.addEventListener("resize",drawChart);
  loadSettings();
  updateClock(); setInterval(updateClock,1000);
  renderPositions(); renderHistory(); updateMarket();
  setInterval(()=>{ if(botRunning) updateMarket(); },3000);
</script>
</body>
</html>
