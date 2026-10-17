<!DOCTYPE html>
<html lang="ka">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>სისტემური ფსონის კალკულატორი</title>
<style>
:root{--bg:#f4f5f7;--card:#fff;--text:#14171c;--muted:#6a7280;--line:#e1e4ea;--acc:#16a34a;--bad:#dc2626;--inp:#f0f2f5;
box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#0f1216;--card:#191d24;--text:#eef0f4;--muted:#98a1b0;--line:#2a303a;--inp:#232932;color-scheme:dark}}
:root[data-theme="dark"]{--bg:#0f1216;--card:#191d24;--text:#eef0f4;--muted:#98a1b0;--line:#2a303a;--inp:#232932;color-scheme:dark}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--text);font-family:system-ui,-apple-system,"Segoe UI",Roboto,sans-serif;line-height:1.4}
main{max-width:560px;margin:0 auto;padding:16px}
h1{font-size:20px;margin:4px 0 14px}
.card{background:var(--card);border:1px solid var(--line);border-radius:14px;padding:14px;margin-bottom:12px}
label{font-size:13px;color:var(--muted);display:block;margin-bottom:6px}
input,select{width:100%;min-width:0;padding:10px;border-radius:10px;border:1px solid var(--line);background:var(--inp);color:var(--text);font-size:16px}
.row2{display:grid;grid-template-columns:1fr 1fr;gap:10px}
.ev{display:grid;grid-template-columns:1fr 66px 74px 34px;gap:6px;align-items:center;margin-bottom:8px}
button{font:inherit;cursor:pointer;border-radius:10px;border:1px solid var(--line);background:var(--inp);color:var(--text);padding:10px 0}
.tog{font-size:13px}
.tog.w{background:var(--acc);color:#fff;border-color:var(--acc)}
.tog.l{background:var(--bad);color:#fff;border-color:var(--bad)}
.add{width:100%;margin-top:4px}
.big{font-size:26px;font-weight:700;color:var(--acc)}
.kv{display:flex;justify-content:space-between;padding:6px 0;border-bottom:1px solid var(--line);font-size:15px}
.kv:last-child{border:0}
.scroll{overflow-x:auto}
table{width:100%;border-collapse:collapse;font-size:14px}
th,td{padding:8px 6px;text-align:right;border-bottom:1px solid var(--line);white-space:nowrap}
th:first-child,td:first-child{text-align:left}
th{color:var(--muted);font-weight:500;font-size:12px}
.note{font-size:12px;color:var(--muted);margin-top:8px}
.cb{display:grid;grid-template-columns:1fr 54px 78px;gap:6px;padding:8px 6px;border-bottom:1px solid var(--line);font-size:14px;opacity:.55}
.cb span:nth-child(2),.cb b{text-align:right}
.cb.ok{opacity:1;background:rgba(22,163,74,.12);border-radius:8px}
.cb.ok b{color:var(--acc)}
.cbh{opacity:1;color:var(--muted);font-size:12px}
#combos{max-height:420px;overflow-y:auto}
</style>
</head>
<body>
<main>
<h1>⚽ სისტემური ფსონის კალკულატორი</h1>

<div class="card">
<div class="row2">
<div><label for="stake">საერთო ფსონი</label><input id="stake" type="text" inputmode="decimal" value="10"></div>
<div><label for="sys">სისტემა</label><select id="sys"></select></div>
</div>
</div>

<div class="card">
<label>გუნდი, კოეფიციენტი და შედეგი</label>
<div id="evs"></div>
<button class="add" id="add">+ გუნდის დამატება</button>
</div>

<div class="card">
<div class="kv"><span>კომბინაციები</span><b id="cnt">–</b></div>
<div class="kv"><span>ფსონი ერთ კომბინაციაზე</span><b id="per">–</b></div>
<div style="margin-top:10px;color:var(--muted);font-size:13px">მოგება არჩეული შედეგებით</div>
<div class="big" id="pay">–</div>
<div class="kv"><span>წმინდა შედეგი</span><b id="net">–</b></div>
</div>

<div class="card">
<div style="font-size:13px;color:var(--muted);margin-bottom:6px">სცენარები: რამდენი გუნდი მოიგო</div>
<div class="scroll"><table>
<thead><tr><th>მოგებული</th><th>მინ. გადახდა</th><th>მაქს. გადახდა</th></tr></thead>
<tbody id="tb"></tbody></table></div>
<div class="note">მინ. — მოგებულია ყველაზე დაბალი კოეფიციენტები, მაქს. — ყველაზე მაღალი.</div>
</div>

<div class="card">
<div style="font-size:13px;color:var(--muted);margin-bottom:6px">ყველა ვარიანტი · მოგებული: <b id="won">–</b></div>
<div class="cb cbh"><span>გუნდები</span><span>კოეფ.</span><b>გადახდა</b></div>
<div id="combos"></div>
<div class="note">მწვანედ ჩანს ის კომბინაციები, რომლებშიც ყველა გუნდი მოგებულია.</div>
</div>
</main>

<script>
const evs=[{t:"რეალი",o:"1.50",w:true},{t:"მან. სიტი",o:"1.80",w:true},{t:"ბაიერნი",o:"2.10",w:false},{t:"იუვენტუსი",o:"1.65",w:true},{t:"პსჟ",o:"2.40",w:true}];
const $=id=>document.getElementById(id);
const num=v=>parseFloat(String(v).replace(",","."));
let k=3;

function sumProd(a,k){let s=0;(function r(st,c,p){if(c===k){s+=p;return}for(let i=st;i<=a.length-(k-c);i++)r(i+1,c+1,p*a[i])})(0,0,1);return s}
const f=x=>x.toLocaleString("en-US",{minimumFractionDigits:2,maximumFractionDigits:2});
const esc=s=>s.replace(/[&<>"]/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;"}[c]));

function draw(){
  $("evs").innerHTML="";
  evs.forEach((e,i)=>{
    const d=document.createElement("div");d.className="ev";
    d.innerHTML=`<input type="text" placeholder="გუნდი"><input type="text" inputmode="decimal" placeholder="კოეფ.">
    <button class="tog"></button><button aria-label="წაშლა">✕</button>`;
    const [t,o,tog,del]=d.children;
    t.value=e.t;o.value=e.o;
    const paint=()=>{tog.className="tog "+(e.w?"w":"l");tog.textContent=e.w?"მოიგო":"წააგო"};paint();
    t.oninput=()=>{e.t=t.value;calc()};
    o.oninput=()=>{e.o=o.value;calc()};
    tog.onclick=()=>{e.w=!e.w;paint();calc()};
    del.onclick=()=>{evs.splice(i,1);draw();calc()};
    $("evs").appendChild(d);
  });
}

function calc(){
  const ev=evs.map((e,i)=>({o:num(e.o),w:e.w,name:(e.t||"").trim()||("გუნდი "+(i+1))})).filter(e=>e.o>=1.01);
  const n=ev.length;
  const sel=$("sys");
  if(sel.options.length!==n){
    sel.innerHTML="";
    for(let i=1;i<=n;i++){const op=document.createElement("option");op.value=i;op.textContent=i+"/"+n;sel.appendChild(op)}
    if(k>n)k=n;if(k<1)k=1;
  }
  if(n)sel.value=k;
  const tb=$("tb");tb.innerHTML="";
  if(n<1){["cnt","per","pay","net","won"].forEach(x=>$(x).textContent="–");$("combos").innerHTML="";return}
  const stake=Math.max(0,num($("stake").value)||0);
  const cnt=Math.round(sumProd(new Array(n).fill(1),k));
  const per=stake/cnt;
  const win=ev.filter(e=>e.w).map(e=>e.o);
  const pay=win.length>=k?per*sumProd(win,k):0;
  $("cnt").textContent=cnt;
  $("per").textContent=f(per);
  $("pay").textContent=f(pay);
  const net=pay-stake;
  $("net").textContent=(net>=0?"+":"")+f(net);
  $("net").style.color=net>=0?"var(--acc)":"var(--bad)";
  const asc=ev.map(e=>e.o).sort((a,b)=>a-b);
  for(let j=k;j<=n;j++){
    const tr=document.createElement("tr");
    tr.innerHTML=`<td>${j} / ${n}</td><td>${f(per*sumProd(asc.slice(0,j),k))}</td><td>${f(per*sumProd(asc.slice(n-j),k))}</td>`;
    tb.appendChild(tr);
  }
  let h="",wc=0;
  (function r(st,cur){
    if(cur.length===k){
      const p=cur.reduce((a,e)=>a*e.o,1),ok=cur.every(e=>e.w);
      if(ok)wc++;
      h+=`<div class="cb ${ok?"ok":""}"><span>${cur.map(e=>esc(e.name)).join(" + ")}</span><span>${p.toFixed(2)}</span><b>${f(per*p)}</b></div>`;
      return;
    }
    for(let i=st;i<=n-(k-cur.length);i++)r(i+1,cur.concat(ev[i]));
  })(0,[]);
  $("combos").innerHTML=h;
  $("won").textContent=wc+" / "+cnt;
}

$("sys").onchange=e=>{k=parseInt(e.target.value);calc()};
$("stake").oninput=calc;
$("add").onclick=()=>{if(evs.length<14){evs.push({t:"",o:"",w:true});draw();calc()}};
draw();calc();
</script>
</body>
</html>
