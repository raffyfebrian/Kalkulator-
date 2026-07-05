# Kalkulator-
Tampilan Depan Kalkulator Tugas Besar 
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Precision Calculator — Shunting-Yard Engine</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600;700&family=IBM+Plex+Sans:wght@400;500;600;700&family=IBM+Plex+Sans+Condensed:wght@500;600;700&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#0b1113;
    --panel:#121a1d;
    --panel-2:#182226;
    --line:#26333688;
    --line-strong:#324146;
    --text:#e9e6da;
    --text-dim:#8ea1a3;
    --text-faint:#516366;
    --amber:#ffb000;
    --amber-dim:#5c4213;
    --rail:#3fd7b0;
    --rail-dim:#123b32;
    --danger:#ff6b5e;
    --radius:3px;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    background:
      radial-gradient(1200px 600px at 15% -10%, #16211f 0%, transparent 60%),
      radial-gradient(900px 500px at 100% 0%, #14191f 0%, transparent 55%),
      var(--bg);
    color:var(--text);
    font-family:'IBM Plex Sans', sans-serif;
    -webkit-font-smoothing:antialiased;
    min-height:100vh;
  }
  ::selection{background:var(--amber); color:#1a1300;}

  /* ---------- Nav ---------- */
  .topbar{
    display:flex; align-items:center; justify-content:space-between;
    padding:18px 28px;
    border-bottom:1px solid var(--line);
    position:sticky; top:0; z-index:50;
    background:rgba(11,17,19,0.88);
    backdrop-filter: blur(8px);
  }
  .brand{display:flex; align-items:center; gap:12px;}
  .brand-mark{
    width:30px;height:30px;border:1.5px solid var(--amber); border-radius:3px;
    display:flex;align-items:center;justify-content:center;
    font-family:'IBM Plex Mono';font-weight:700;color:var(--amber);font-size:13px;
    box-shadow:0 0 12px -2px var(--amber);
  }
  .brand-text{font-family:'IBM Plex Mono'; font-weight:600; letter-spacing:0.08em; font-size:14px;}
  .brand-text small{display:block; font-family:'IBM Plex Sans'; font-weight:400; color:var(--text-dim); letter-spacing:0.12em; font-size:9.5px; margin-top:2px; text-transform:uppercase;}
  .navlinks{display:flex; gap:26px; font-size:12.5px; letter-spacing:0.06em; color:var(--text-dim); text-transform:uppercase; font-weight:500;}
  .navlinks span{cursor:default; padding-bottom:3px; border-bottom:1px solid transparent;}
  .navlinks span.active{color:var(--amber); border-color:var(--amber);}

  .wrap{max-width:1180px; margin:0 auto; padding:36px 28px 80px;}

  .lede{margin-bottom:30px;}
  .eyebrow{font-family:'IBM Plex Mono'; font-size:11px; letter-spacing:0.18em; color:var(--rail); text-transform:uppercase; margin-bottom:10px;}
  h1{font-family:'IBM Plex Sans Condensed'; font-weight:700; font-size:30px; margin:0 0 8px; letter-spacing:-0.01em;}
  .lede p{color:var(--text-dim); font-size:14px; max-width:640px; line-height:1.6; margin:0;}

  /* ---------- Main grid ---------- */
  .console{
    display:grid;
    grid-template-columns: 320px 1fr;
    gap:20px;
  }
  @media (max-width: 880px){ .console{grid-template-columns: 1fr;} }

  .panel{
    background:var(--panel);
    border:1px solid var(--line);
    border-radius:var(--radius);
    position:relative;
  }
  .panel-head{
    display:flex; align-items:center; justify-content:space-between;
    padding:12px 16px; border-bottom:1px solid var(--line);
  }
  .panel-title{
    font-family:'IBM Plex Mono'; font-size:11px; letter-spacing:0.14em; text-transform:uppercase; color:var(--text-dim);
  }
  .panel-title b{color:var(--text); font-weight:600;}
  .tag{
    font-family:'IBM Plex Mono'; font-size:9.5px; color:var(--text-faint); border:1px solid var(--line-strong); padding:2px 6px; border-radius:2px; letter-spacing:0.05em;
  }

  /* ---------- Calculator unit ---------- */
  .display{
    margin:16px; padding:18px 16px 14px;
    background:#080d0e; border:1px solid var(--line-strong); border-radius:2px;
    min-height:96px;
    box-shadow: inset 0 0 24px #00000080;
  }
  .display-infix{
    font-family:'IBM Plex Mono'; font-size:20px; color:var(--amber);
    min-height:28px; word-break:break-all; text-shadow:0 0 10px #ffb00055;
    letter-spacing:0.01em;
  }
  .display-sub{
    margin-top:8px; font-family:'IBM Plex Mono'; font-size:11.5px; color:var(--text-faint);
    display:flex; justify-content:space-between;
  }
  .display-sub .err{color:var(--danger);}

  .keypad{ padding:0 16px 16px; display:grid; grid-template-columns:repeat(4,1fr); gap:8px; }
  .key{
    background:var(--panel-2); border:1px solid var(--line-strong); color:var(--text);
    font-family:'IBM Plex Mono'; font-size:15px; padding:13px 0; border-radius:2px;
    cursor:pointer; transition:background .12s, transform .05s;
    user-select:none;
  }
  .key:hover{background:#20302f;}
  .key:active{transform:scale(0.96);}
  .key.op{color:var(--rail);}
  .key.fn{color:var(--text-dim); font-size:11px; letter-spacing:0.04em;}
  .key.eq{
    grid-column:span 4; background:var(--amber); color:#1a1300; font-weight:700;
    letter-spacing:0.06em; font-size:13px;
  }
  .key.eq:hover{background:#ffc233;}

  /* ---------- Transformation strip ---------- */
  .transform{
    margin:0 16px 16px; padding:14px 14px;
    border:1px dashed var(--line-strong); border-radius:2px;
    display:flex; flex-direction:column; gap:10px;
  }
  .transform-row{display:flex; align-items:center; gap:10px; flex-wrap:wrap;}
  .transform-label{font-family:'IBM Plex Mono'; font-size:9.5px; letter-spacing:0.14em; color:var(--text-faint); width:56px; flex-shrink:0; text-transform:uppercase;}
  .chip{
    font-family:'IBM Plex Mono'; font-size:13px; padding:4px 9px; border-radius:2px;
    background:var(--panel-2); border:1px solid var(--line-strong); color:var(--text);
  }
  .chip.num{color:var(--text);}
  .chip.op{color:var(--rail); border-color:var(--rail-dim);}
  .arrow-sep{color:var(--text-faint); font-family:'IBM Plex Mono';}

  /* ---------- Track diagram (signature element) ---------- */
  .track-wrap{ padding:20px 20px 18px; }
  .track{ position:relative; }
  .siding-zone{
    position:relative; min-height:118px; padding-left:44px; margin-bottom:6px;
  }
  .siding-line-v{
    position:absolute; left:18px; top:0; bottom:-10px; width:2px; background:var(--amber-dim);
  }
  .siding-label{
    font-family:'IBM Plex Mono'; font-size:9.5px; color:var(--amber); letter-spacing:0.12em; margin-bottom:10px; display:block;
  }
  .siding-cars{
    display:flex; flex-direction:column-reverse; gap:5px; align-items:flex-start;
  }
  .car{
    font-family:'IBM Plex Mono'; font-weight:600; font-size:12.5px;
    min-width:34px; padding:4px 7px; height:22px; border-radius:2px; display:flex; align-items:center; justify-content:center;
    background:var(--amber-dim); border:1px solid var(--amber); color:var(--amber);
    box-shadow:0 0 8px -2px #ffb00070;
  }
  .car.top{background:var(--amber); color:#1a1300; box-shadow:0 0 12px -1px #ffb000;}
  .mainline-zone{
    padding-left:44px; padding-top:14px; border-top:2px solid var(--line-strong); position:relative;
  }
  .mainline-zone::before{
    content:""; position:absolute; left:0; right:0; top:-1px; height:1px; background:var(--line);
  }
  .mainline-label{
    font-family:'IBM Plex Mono'; font-size:9.5px; color:var(--rail); letter-spacing:0.12em; display:block; margin-bottom:10px;
  }
  .mainline-cars{ display:flex; gap:5px; flex-wrap:wrap; }
  .car2{
    font-family:'IBM Plex Mono'; font-weight:600; font-size:12px;
    padding:3px 8px; border-radius:2px;
    background:var(--rail-dim); border:1px solid var(--rail); color:var(--rail);
  }
  .track-empty{ padding:30px 10px; text-align:center; color:var(--text-faint); font-family:'IBM Plex Mono'; font-size:12px; letter-spacing:0.03em; line-height:1.6;}

  /* ---------- Breakdown table ---------- */
  .breakdown-scroll{ max-height:280px; overflow:auto; }
  table.breakdown{ width:100%; border-collapse:collapse; font-family:'IBM Plex Mono'; font-size:12px; }
  table.breakdown th{
    text-align:left; padding:8px 12px; color:var(--text-faint); font-weight:500;
    font-size:10px; letter-spacing:0.1em; text-transform:uppercase;
    border-bottom:1px solid var(--line-strong); position:sticky; top:0; background:var(--panel);
  }
  table.breakdown td{ padding:7px 12px; border-bottom:1px solid var(--line); color:var(--text-dim); vertical-align:top;}
  table.breakdown tr:last-child td{border-bottom:none;}
  table.breakdown td.tok{color:var(--amber); font-weight:600;}
  table.breakdown td.act{color:var(--text);}
  .mini-chip{display:inline-block; background:var(--panel-2); border:1px solid var(--line-strong); padding:1px 5px; border-radius:2px; margin:1px; font-size:11px;}

  /* ---------- Two-column result row ---------- */
  .result-grid{ display:grid; grid-template-columns: 1fr 1fr; gap:20px; margin-top:20px;}
  @media (max-width: 880px){ .result-grid{grid-template-columns:1fr;} }

  /* Stack visualizer */
  .stackviz{ padding:18px 18px 20px; display:flex; gap:20px; align-items:flex-start;}
  .stackviz-col{flex:1;}
  .stackviz-title{font-family:'IBM Plex Mono'; font-size:9.5px; color:var(--text-faint); letter-spacing:0.12em; text-transform:uppercase; margin-bottom:10px;}
  .stack-tower{ display:flex; flex-direction:column-reverse; gap:6px; min-height:160px; justify-content:flex-start; border:1px dashed var(--line-strong); border-top:none; padding:10px 8px 0; position:relative;}
  .stack-base{ position:absolute; bottom:-1px; left:0; right:0; height:3px; background:var(--line-strong);}
  .stack-item{
    font-family:'IBM Plex Mono'; font-weight:600; font-size:14px; text-align:center;
    padding:8px 0; border-radius:2px; background:var(--panel-2); border:1px solid var(--line-strong); color:var(--text);
  }
  .stack-item.top{ background:var(--amber); color:#1a1300; border-color:var(--amber); box-shadow:0 0 14px -3px #ffb000;}
  .stack-item .lbl{font-size:8px; letter-spacing:0.1em; opacity:0.7; display:block; margin-top:2px;}
  .stack-empty-msg{color:var(--text-faint); font-family:'IBM Plex Mono'; font-size:11px; text-align:center; padding:14px 0;}

  .result-box{padding:18px;}
  .result-final{
    font-family:'IBM Plex Mono'; font-size:38px; font-weight:700; color:var(--rail);
    text-shadow:0 0 16px #3fd7b055; word-break:break-all; line-height:1.1;
  }
  .result-meta{ margin-top:12px; display:flex; flex-direction:column; gap:6px; font-family:'IBM Plex Mono'; font-size:11.5px; color:var(--text-dim);}
  .result-meta .k{color:var(--text-faint);}

  /* ---------- History ---------- */
  .history-list{ padding:6px 8px 12px; display:flex; flex-direction:column; max-height:230px; overflow:auto;}
  .history-item{
    display:flex; justify-content:space-between; align-items:center; gap:10px;
    padding:10px 10px; border-radius:2px; cursor:pointer; font-family:'IBM Plex Mono'; font-size:12.5px;
  }
  .history-item:hover{background:var(--panel-2);}
  .history-item .hi-expr{color:var(--text-dim); overflow:hidden; text-overflow:ellipsis; white-space:nowrap;}
  .history-item .hi-res{color:var(--rail); flex-shrink:0;}
  .history-empty{color:var(--text-faint); font-family:'IBM Plex Mono'; font-size:11.5px; padding:16px 12px;}
  .clear-hist{
    font-family:'IBM Plex Mono'; font-size:9.5px; color:var(--text-faint); background:none; border:1px solid var(--line-strong);
    padding:3px 8px; border-radius:2px; cursor:pointer; letter-spacing:0.06em;
  }
  .clear-hist:hover{color:var(--danger); border-color:var(--danger);}

  .section-gap{margin-top:22px;}
  footer{ text-align:center; padding:30px 0 10px; color:var(--text-faint); font-family:'IBM Plex Mono'; font-size:10.5px; letter-spacing:0.08em;}
</style>
</head>
<body>

  <div class="topbar">
    <div class="brand">
      <div class="brand-mark">λ</div>
      <div class="brand-text">PRECISION CALC
        <small>Shunting-Yard Engine</small>
      </div>
    </div>
    <div class="navlinks">
      <span class="active">Kalkulator</span>
      <span>Logic Breakdown</span>
      <span>Riwayat</span>
    </div>
  </div>

  <div class="wrap">
    <div class="lede">
      <div class="eyebrow">/ Precision Calculator</div>
      <h1>Evaluasi ekspresi matematika, langkah demi langkah.</h1>
      <p>Masukkan ekspresi infix biasa. Mesin ini mengubahnya menjadi notasi postfix (RPN) memakai Algoritma Shunting-Yard milik Edsger Dijkstra, lalu mengevaluasinya memakai struktur data Stack — dan menampilkan setiap langkahnya secara terbuka.</p>
    </div>

    <!-- ===== MAIN CONSOLE: calculator + track ===== -->
    <div class="console">

      <!-- Calculator unit -->
      <div class="panel">
        <div class="panel-head">
          <div class="panel-title">01 — <b>Input Ekspresi</b></div>
          <div class="tag">INFIX</div>
        </div>
        <div class="display">
          <div class="display-infix" id="infixDisplay">0</div>
          <div class="display-sub">
            <span id="tokenCount">0 token</span>
            <span id="errMsg" class="err"></span>
          </div>
        </div>
        <div class="keypad" id="keypad">
          <button class="key fn" data-k="C">C</button>
          <button class="key fn" data-k="CE">CE</button>
          <button class="key fn" data-k="⌫">⌫</button>
          <button class="key op" data-k="/">÷</button>

          <button class="key" data-k="7">7</button>
          <button class="key" data-k="8">8</button>
          <button class="key" data-k="9">9</button>
          <button class="key op" data-k="*">×</button>

          <button class="key" data-k="4">4</button>
          <button class="key" data-k="5">5</button>
          <button class="key" data-k="6">6</button>
          <button class="key op" data-k="-">−</button>

          <button class="key" data-k="1">1</button>
          <button class="key" data-k="2">2</button>
          <button class="key" data-k="3">3</button>
          <button class="key op" data-k="+">+</button>

          <button class="key" data-k="(">(</button>
          <button class="key" data-k="0">0</button>
          <button class="key" data-k=")">)</button>
          <button class="key op" data-k="%">%</button>

          <button class="key" data-k=".">.</button>
          <button class="key op" data-k="^">^</button>
          <button class="key fn" data-k="ans" style="grid-column:span 2;">ANS</button>

          <button class="key eq" id="evalBtn">EVALUATE ▸</button>
        </div>

        <!-- Transformation documentation strip -->
        <div class="transform">
          <div class="transform-row">
            <div class="transform-label">Infix</div>
            <div id="infixChips" style="display:flex; gap:6px; flex-wrap:wrap;"><span class="chip" style="color:var(--text-faint)">—</span></div>
          </div>
          <div class="transform-row">
            <div class="transform-label">Postfix</div>
            <div id="postfixChips" style="display:flex; gap:6px; flex-wrap:wrap;"><span class="chip" style="color:var(--text-faint)">—</span></div>
          </div>
        </div>
      </div>

      <!-- Track diagram + breakdown -->
      <div style="display:flex; flex-direction:column; gap:20px;">

        <div class="panel">
          <div class="panel-head">
            <div class="panel-title">02 — <b>Diagram Alur</b> (Operator Stack &amp; Output Queue)</div>
            <div class="tag">LIVE</div>
          </div>
          <div class="track-wrap">
            <div class="track" id="trackViz">
              <div class="track-empty" id="trackEmptyMsg">Masukkan ekspresi lalu tekan EVALUATE untuk melihat token bergerak dari infix menuju stack, lalu ke antrean postfix.</div>
              <div id="trackContent" style="display:none;">
                <div class="siding-zone">
                  <div class="siding-line-v"></div>
                  <span class="siding-label">SIDING · OPERATOR STACK</span>
                  <div class="siding-cars" id="sidingCars"></div>
                </div>
                <div class="mainline-zone">
                  <span class="mainline-label">MAIN LINE · POSTFIX OUTPUT</span>
                  <div class="mainline-cars" id="mainCars"></div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="panel">
          <div class="panel-head">
            <div class="panel-title">03 — <b>Logic Breakdown</b> — Algoritma Shunting-Yard</div>
            <div class="tag" id="stepTag">0 langkah</div>
          </div>
          <div class="breakdown-scroll">
            <table class="breakdown">
              <thead><tr><th>#</th><th>Token</th><th>Aksi</th><th>Stack (Top→)</th><th>Output</th></tr></thead>
              <tbody id="breakdownBody">
                <tr><td colspan="5" style="color:var(--text-faint); text-align:center; padding:20px;">Belum ada proses. Evaluasi ekspresi untuk mengisi tabel ini.</td></tr>
              </tbody>
            </table>
          </div>
        </div>

      </div>
    </div>

    <!-- ===== Stack visualizer + Result ===== -->
    <div class="result-grid">
      <div class="panel">
        <div class="panel-head">
          <div class="panel-title">04 — <b>Visualisasi Stack</b> Saat Evaluasi Postfix</div>
          <div class="tag">LIFO</div>
        </div>
        <div class="stackviz">
          <div class="stackviz-col">
            <div class="stackviz-title">Operand Stack (akhir evaluasi)</div>
            <div class="stack-tower" id="stackTower">
              <div class="stack-base"></div>
            </div>
          </div>
        </div>
      </div>

      <div class="panel">
        <div class="panel-head">
          <div class="panel-title">05 — <b>Hasil Akhir</b> Komputasi</div>
          <div class="tag" id="complexityTag">O(n)</div>
        </div>
        <div class="result-box">
          <div class="result-final" id="finalResult">0</div>
          <div class="result-meta">
            <div><span class="k">Ekspresi&nbsp;:</span> <span id="metaExpr">—</span></div>
            <div><span class="k">Langkah Shunting-Yard&nbsp;:</span> <span id="metaSteps">0</span></div>
            <div><span class="k">Waktu proses&nbsp;:</span> <span id="metaTime">0.00 ms</span></div>
          </div>
        </div>
      </div>
    </div>

    <!-- ===== History ===== -->
    <div class="panel section-gap">
      <div class="panel-head">
        <div class="panel-title">06 — <b>Riwayat Perhitungan</b></div>
        <button class="clear-hist" id="clearHistBtn">HAPUS SEMUA</button>
      </div>
      <div class="history-list" id="historyList">
        <div class="history-empty">Belum ada riwayat perhitungan pada sesi ini.</div>
      </div>
    </div>

    <footer>PRECISION CALCULATOR — STACK · SHUNTING-YARD · REVERSE POLISH NOTATION</footer>
  </div>

<script>
(function(){
  "use strict";

  const PREC = { 'u-': 4, '^': 4, '*': 3, '/': 3, '%': 3, '+': 2, '-': 2 };
  const RIGHT_ASSOC = new Set(['^','u-']);
  const OPS = new Set(['+','-','*','/','%','^']);

  let expr = "";
  let lastAnswer = null;
  let history = [];

  try{
    const saved = localStorage.getItem('pc_history');
    if(saved) history = JSON.parse(saved);
  }catch(e){ history = []; }

  const infixDisplay = document.getElementById('infixDisplay');
  const tokenCount = document.getElementById('tokenCount');
  const errMsg = document.getElementById('errMsg');
  const infixChips = document.getElementById('infixChips');
  const postfixChips = document.getElementById('postfixChips');
  const breakdownBody = document.getElementById('breakdownBody');
  const stepTag = document.getElementById('stepTag');
  const stackTower = document.getElementById('stackTower');
  const finalResult = document.getElementById('finalResult');
  const metaExpr = document.getElementById('metaExpr');
  const metaSteps = document.getElementById('metaSteps');
  const metaTime = document.getElementById('metaTime');
  const historyList = document.getElementById('historyList');
  const trackEmptyMsg = document.getElementById('trackEmptyMsg');
  const sidingLine = document.getElementById('sidingLine');
  const sidingLabel = document.getElementById('sidingLabel');
  const sidingCars = document.getElementById('sidingCars');
  const mainLine = document.getElementById('mainLine');
  const mainLabel = document.getElementById('mainLabel');
  const mainCars = document.getElementById('mainCars');

  function updateInfixDisplay(){
    infixDisplay.textContent = expr.length ? expr : "0";
  }

  // ---------- Tokenizer ----------
  function tokenize(str){
    const raw = str.replace(/\s+/g,'');
    if(raw.length === 0) throw new Error("Ekspresi kosong.");
    const tokens = [];
    let i = 0;
    while(i < raw.length){
      const c = raw[i];
      if(/[0-9.]/.test(c)){
        let j = i;
        let num = "";
        while(j < raw.length && /[0-9.]/.test(raw[j])){ num += raw[j]; j++; }
        if((num.match(/\./g)||[]).length > 1) throw new Error("Format desimal tidak valid pada '" + num + "'.");
        tokens.push({type:'num', value:num});
        i = j;
        continue;
      }
      if(c === '('){ tokens.push({type:'lparen', value:'('}); i++; continue; }
      if(c === ')'){ tokens.push({type:'rparen', value:')'}); i++; continue; }
      if(OPS.has(c)){
        const prevTok = tokens[tokens.length-1];
        const isUnaryContext = (c === '-') && (!prevTok || prevTok.type === 'op' || prevTok.type === 'lparen');
        if(isUnaryContext){
          tokens.push({type:'op', value:'u-'});
        } else {
          tokens.push({type:'op', value:c});
        }
        i++;
        continue;
      }
      throw new Error("Karakter tidak dikenali: '" + c + "'.");
    }

    // Auto-multiplication insertion: number/) followed by ( ; and ) followed by number
    const withMul = [];
    for(let k = 0; k < tokens.length; k++){
      const t = tokens[k];
      withMul.push(t);
      const nxt = tokens[k+1];
      if(!nxt) continue;
      const curClose = (t.type === 'num' || t.type === 'rparen');
      const nxtOpen = (nxt.type === 'lparen') || (nxt.type === 'num' && t.type === 'rparen');
      if(curClose && nxtOpen){
        withMul.push({type:'op', value:'*'});
      }
    }
    return withMul;
  }

  // ---------- Shunting-Yard ----------
  function shuntingYard(tokens){
    const outQueue = [];
    const opStack = [];
    const steps = [];

    function snap(){
      return {
        stack: opStack.map(t => t.value === 'u-' ? '(-)' : t.value).slice().reverse(),
        output: outQueue.map(t => t.type === 'num' ? t.value : (t.value === 'u-' ? '(-)' : t.value)).slice()
      };
    }

    for(const tok of tokens){
      if(tok.type === 'num'){
        outQueue.push(tok);
        const s = snap();
        steps.push({token: tok.value, action:'Operand → langsung ke output', stack:s.stack, output:s.output});
      } else if(tok.type === 'op'){
        while(opStack.length){
          const top = opStack[opStack.length-1];
          if(top.type !== 'op') break;
          const topPrec = PREC[top.value], curPrec = PREC[tok.value];
          const rightAssoc = RIGHT_ASSOC.has(tok.value);
          if(topPrec > curPrec || (topPrec === curPrec && !rightAssoc)){
            outQueue.push(opStack.pop());
          } else break;
        }
        opStack.push(tok);
        const s = snap();
        const label = tok.value === 'u-' ? '(-) unary' : tok.value;
        steps.push({token: label, action:'Operator di-push ke stack (setelah pop prioritas lebih tinggi)', stack:s.stack, output:s.output});
      } else if(tok.type === 'lparen'){
        opStack.push(tok);
        const s = snap();
        steps.push({token:'(', action:'Kurung buka di-push sebagai penanda batas', stack:s.stack, output:s.output});
      } else if(tok.type === 'rparen'){
        let found = false;
        while(opStack.length){
          const top = opStack[opStack.length-1];
          if(top.type === 'lparen'){ opStack.pop(); found = true; break; }
          outQueue.push(opStack.pop());
        }
        if(!found) throw new Error("Tanda kurung tidak seimbang.");
        const s = snap();
        steps.push({token:')', action:'Pop stack sampai ( ditemukan, lalu dibuang', stack:s.stack, output:s.output});
      }
    }
    while(opStack.length){
      const top = opStack[opStack.length-1];
      if(top.type === 'lparen') throw new Error("Tanda kurung tidak seimbang.");
      outQueue.push(opStack.pop());
      const s = snap();
      steps.push({token: top.value === 'u-' ? '(-)' : top.value, action:'Flush sisa stack ke output', stack:s.stack, output:s.output});
    }
    return {postfix: outQueue, steps};
  }

  // ---------- RPN Evaluator ----------
  function evaluatePostfix(postfix){
    const stack = [];
    for(const tok of postfix){
      if(tok.type === 'num'){
        stack.push(parseFloat(tok.value));
      } else if(tok.value === 'u-'){
        if(stack.length < 1) throw new Error("Ekspresi tidak valid (operand kurang untuk unary minus).");
        stack.push(-stack.pop());
      } else {
        if(stack.length < 2) throw new Error("Ekspresi tidak valid (operand kurang untuk operator '" + tok.value + "').");
        const b = stack.pop();
        const a = stack.pop();
        let r;
        switch(tok.value){
          case '+': r = a + b; break;
          case '-': r = a - b; break;
          case '*': r = a * b; break;
          case '/': if(b === 0) throw new Error("Pembagian dengan nol tidak diperbolehkan."); r = a / b; break;
          case '%': if(b === 0) throw new Error("Modulus dengan nol tidak diperbolehkan."); r = a % b; break;
          case '^': r = Math.pow(a,b); break;
          default: throw new Error("Operator tidak dikenali.");
        }
        stack.push(r);
      }
    }
    if(stack.length !== 1) throw new Error("Ekspresi tidak lengkap atau tidak valid.");
    return stack[0];
  }

  function formatNumber(n){
    if(!isFinite(n)) return "Error";
    let r = Math.round(n * 1e8) / 1e8;
    return r.toString();
  }

  // ---------- Rendering ----------
  function renderChips(container, items, kindFn){
    container.innerHTML = "";
    if(!items || !items.length){
      container.innerHTML = '<span class="chip" style="color:var(--text-faint)">—</span>';
      return;
    }
    items.forEach(v => {
      const el = document.createElement('span');
      el.className = 'chip ' + (kindFn(v) === 'op' ? 'op' : 'num');
      el.textContent = v;
      container.appendChild(el);
    });
  }

  function renderTrack(steps){
    if(!steps.length){
      trackEmptyMsg.style.display = 'flex';
      sidingLine.style.display = 'none';
      sidingLabel.style.display = 'none';
      mainLine.style.display = 'none';
      mainLabel.style.display = 'none';
      sidingCars.innerHTML = ""; mainCars.innerHTML = "";
      return;
    }
    trackEmptyMsg.style.display = 'none';
    sidingLine.style.display = 'block';
    sidingLabel.style.display = 'block';
    mainLine.style.display = 'block';
    mainLabel.style.display = 'block';
    const last = steps[steps.length-1];
    sidingCars.innerHTML = "";
    last.stack.forEach((v, idx) => {
      const d = document.createElement('div');
      d.className = 'car' + (idx === 0 ? ' top' : '');
      d.textContent = v;
      sidingCars.appendChild(d);
    });
    mainCars.innerHTML = "";
    last.output.forEach(v => {
      const d = document.createElement('div');
      d.className = 'car2';
      d.textContent = v;
      mainCars.appendChild(d);
    });
  }

  function renderBreakdown(steps){
    breakdownBody.innerHTML = "";
    if(!steps.length){
      breakdownBody.innerHTML = '<tr><td colspan="5" style="color:var(--text-faint); text-align:center; padding:20px;">Belum ada proses. Evaluasi ekspresi untuk mengisi tabel ini.</td></tr>';
      stepTag.textContent = "0 langkah";
      return;
    }
    steps.forEach((s, idx) => {
      const tr = document.createElement('tr');
      const stackStr = s.stack.length ? s.stack.map(x=>'<span class="mini-chip">'+x+'</span>').join('') : '<span style="color:var(--text-faint)">kosong</span>';
      const outStr = s.output.length ? s.output.map(x=>'<span class="mini-chip">'+x+'</span>').join('') : '<span style="color:var(--text-faint)">kosong</span>';
      tr.innerHTML = '<td>'+(idx+1)+'</td><td class="tok">'+s.token+'</td><td class="act">'+s.action+'</td><td>'+stackStr+'</td><td>'+outStr+'</td>';
      breakdownBody.appendChild(tr);
    });
    stepTag.textContent = steps.length + " langkah";
  }

  function renderStackTower(finalStackValues){
    stackTower.innerHTML = '<div class="stack-base"></div>';
    if(!finalStackValues || !finalStackValues.length){
      const msg = document.createElement('div');
      msg.className = 'stack-empty-msg';
      msg.textContent = 'Stack kosong — hasil sudah final.';
      stackTower.appendChild(msg);
      return;
    }
    finalStackValues.forEach((v, idx) => {
      const d = document.createElement('div');
      d.className = 'stack-item' + (idx === finalStackValues.length-1 ? ' top' : '');
      d.innerHTML = formatNumber(v) + (idx === finalStackValues.length-1 ? '<span class="lbl">TOP OF STACK</span>' : '');
      stackTower.appendChild(d);
    });
  }

  function renderHistory(){
    historyList.innerHTML = "";
    if(!history.length){
      historyList.innerHTML = '<div class="history-empty">Belum ada riwayat perhitungan pada sesi ini.</div>';
      return;
    }
    history.slice().reverse().forEach(item => {
      const row = document.createElement('div');
      row.className = 'history-item';
      row.innerHTML = '<span class="hi-expr">'+item.expr+'</span><span class="hi-res">= '+item.result+'</span>';
      row.addEventListener('click', () => {
        expr = item.expr;
        updateInfixDisplay();
        errMsg.textContent = "";
      });
      historyList.appendChild(row);
    });
  }

  function saveHistory(){
    try{ localStorage.setItem('pc_history', JSON.stringify(history)); }catch(e){}
  }

  // ---------- Main evaluate flow ----------
  function runEvaluate(){
    errMsg.textContent = "";
    const t0 = performance.now();
    try{
      const tokens = tokenize(expr);
      tokenCount.textContent = tokens.length + " token";
      const infixShown = tokens.map(t => t.type==='num' ? t.value : (t.value==='u-' ? '(-)' : t.value));
      renderChips(infixChips, infixShown, v => OPS.has(v) || v==='(-)' ? 'op' : 'num');

      const {postfix, steps} = shuntingYard(tokens);
      const postfixShown = postfix.map(t => t.type==='num' ? t.value : (t.value==='u-' ? '(-)' : t.value));
      renderChips(postfixChips, postfixShown, v => OPS.has(v) || v==='(-)' ? 'op' : 'num');

      renderTrack(steps);
      renderBreakdown(steps);

      const result = evaluatePostfix(postfix);
      const t1 = performance.now();

      finalResult.textContent = formatNumber(result);
      metaExpr.textContent = expr;
      metaSteps.textContent = steps.length;
      metaTime.textContent = (t1-t0).toFixed(3) + " ms";
      renderStackTower([result]);

      lastAnswer = result;
      history.push({expr: expr, result: formatNumber(result)});
      if(history.length > 30) history.shift();
      saveHistory();
      renderHistory();

    }catch(ex){
      errMsg.textContent = ex.message || "Terjadi kesalahan.";
      finalResult.textContent = "Error";
      renderStackTower([]);
    }
  }

  // ---------- Keypad wiring ----------
  document.getElementById('keypad').addEventListener('click', (e) => {
    const btn = e.target.closest('.key');
    if(!btn) return;
    const k = btn.dataset.k;
    if(k === undefined) return;
    if(k === 'C'){ expr = ""; errMsg.textContent=""; }
    else if(k === 'CE'){
      const m = expr.match(/(\d+\.?\d*|\.\d+)$/);
      if(m) expr = expr.slice(0, expr.length - m[0].length);
      else expr = expr.slice(0, -1);
    }
    else if(k === '⌫'){ expr = expr.slice(0,-1); }
    else if(k === 'ans'){ expr += (lastAnswer !== null ? formatNumber(lastAnswer) : "0"); }
    else { expr += k; }
    updateInfixDisplay();
  });

  document.getElementById('evalBtn').addEventListener('click', runEvaluate);

  document.getElementById('clearHistBtn').addEventListener('click', () => {
    history = [];
    saveHistory();
    renderHistory();
  });

  window.addEventListener('keydown', (e) => {
    if(document.activeElement && document.activeElement.tagName === 'INPUT') return;
    if(/[0-9.+\-*/%^()]/.test(e.key)){ expr += e.key; updateInfixDisplay(); e.preventDefault(); }
    else if(e.key === 'Enter' || e.key === '='){ runEvaluate(); e.preventDefault(); }
    else if(e.key === 'Backspace'){ expr = expr.slice(0,-1); updateInfixDisplay(); }
    else if(e.key.toLowerCase() === 'c'){ expr = ""; updateInfixDisplay(); }
  });

  // init
  expr = "3+4*2/(1-5)^2";
  updateInfixDisplay();
  renderHistory();
  renderStackTower(null);
})();
</script>
</body>
</html>
