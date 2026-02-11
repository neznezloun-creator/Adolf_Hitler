<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Чарлист — Тьма</title>
<style>
body{
  margin:0;
  background:#000;
  color:#e5e5e5;
  font-family: "Segoe UI", sans-serif;
}
h1{
  text-align:center;
  letter-spacing:3px;
  text-transform:uppercase;
}
.wrap{
  max-width:1200px;
  margin:auto;
  padding:20px;
}
.card{
  border:1px solid #333;
  border-radius:14px;
  padding:14px;
  margin:10px 0;
  background:#050505;
  box-shadow:0 0 20px rgba(255,255,255,0.05);
}
.grid{
  display:grid;
  grid-template-columns: repeat(auto-fit,minmax(280px,1fr));
  gap:16px;
}
.label{
  font-size:12px;
  text-transform:uppercase;
  letter-spacing:2px;
  color:#888;
  margin-bottom:6px;
}
input, textarea{
  width:100%;
  background:#0b0b0b;
  border:1px solid #333;
  color:#eee;
  border-radius:8px;
  padding:8px;
  margin:4px 0;
}
button{
  background:#111;
  border:1px solid #444;
  color:#ddd;
  padding:8px;
  border-radius:8px;
  cursor:pointer;
}
button:hover{ border-color:#aaa; color:#fff; }

.dots{ display:flex; gap:6px; flex-wrap:wrap; }
.dot{
  width:18px; height:18px;
  border-radius:50%;
  border:2px solid #666;
  background:#111;
  cursor:pointer;
}
.dot.active{
  background:#fff;
  border-color:#fff;
}

.item{
  position:relative;
  border:1px solid #333;
  border-radius:10px;
  padding:10px;
  margin-top:8px;
  background:#0a0a0a;
}
.del{
  position:absolute;
  top:6px; right:8px;
  cursor:pointer;
  color:#666;
  font-size:14px;
}
.del:hover{ color:#fff; }

.moveBtn{
  margin-top:6px;
  font-size:12px;
}

img{
  width:100%;
  border-radius:12px;
  border:1px solid #333;
  object-fit:cover;
  max-height:300px;
}
</style>
</head>
<body>
<div class="wrap">
<h1>Чарлист — Тьма</h1>

<div class="grid">

<div class="card">
<div class="label">Персонаж</div>
<input placeholder="Имя">
<input type="file" accept="image/*" onchange="loadImg(event)">
<img id="portrait">
</div>

<div class="card">
<div class="label">Характеристики</div>
<div id="stats"></div>
</div>

<div class="card">
<div class="label">Таланты</div>
<input id="talName" placeholder="Название">
<textarea id="talDesc" placeholder="Описание"></textarea>
<button onclick="addTalent()">Добавить талант</button>
<div id="talents"></div>
</div>

</div>

<div class="grid">

<div class="card"><div class="label">Телосложение — навыки</div><div id="s_body"></div></div>
<div class="card"><div class="label">Ловкость — навыки</div><div id="s_agl"></div></div>
<div class="card"><div class="label">Смекалка — навыки</div><div id="s_mind"></div></div>
<div class="card"><div class="label">Эмпатия — навыки</div><div id="s_emp"></div></div>

</div>

<div class="grid">
<div class="card">
<div class="label">Инвентарь</div>
<div id="invAdd"></div>
<div id="inventory"></div>
</div>

<div class="card">
<div class="label">Склад</div>
<div id="stashAdd"></div>
<div id="stash"></div>
</div>
</div>

</div>

<script>
function dotRow(container, max){
  let val=0;
  const wrap=document.createElement("div");
  wrap.className="dots";
  for(let i=1;i<=max;i++){
    const d=document.createElement("div");
    d.className="dot";
    d.onclick=()=>{
      val = (val===i)? i-1 : i;
      [...wrap.children].forEach((c,idx)=>{
        c.classList.toggle("active", idx < val);
      });
      label.textContent = val+"/"+max;
    };
    wrap.appendChild(d);
  }
  const label=document.createElement("div");
  label.style.fontSize="12px";
  label.style.color="#777";
  label.textContent="0/"+max;
  container.appendChild(wrap);
  container.appendChild(label);
}

function statBlock(name,max){
  const c=document.createElement("div");
  c.className="card";
  const l=document.createElement("div");
  l.className="label";
  l.textContent=name;
  c.appendChild(l);
  dotRow(c,max);
  return c;
}

const statsDiv=document.getElementById("stats");
[
["Телосложение",4],
["Смекалка",5],
["Ловкость",2],
["Эмпатия",3]
].forEach(s=>statsDiv.appendChild(statBlock(s[0],s[1])));

function skillSet(id,names){
  const root=document.getElementById(id);
  names.forEach(n=>{
    const c=document.createElement("div");
    const l=document.createElement("div");
    l.className="label";
    l.textContent=n;
    c.appendChild(l);
    dotRow(c,5);
    root.appendChild(c);
  });
}

skillSet("s_body",["Сила","Выносливость","Драка"]);
skillSet("s_agl",["Скрытность","Проворность","Стрельба"]);
skillSet("s_mind",["Наблюдательность","Анализ","Знания"]);
skillSet("s_emp",["Проницательность","Влияние","Лечение"]);

function loadImg(e){
  const f=e.target.files[0];
  if(f) document.getElementById("portrait").src=URL.createObjectURL(f);
}

function addListControls(targetAdd, targetList, otherList){
  const wrap=document.getElementById(targetAdd);
  const name=document.createElement("input");
  name.placeholder="Название";
  const desc=document.createElement("textarea");
  desc.placeholder="Описание";
  const btn=document.createElement("button");
  btn.textContent="Добавить";
  wrap.appendChild(name);
  wrap.appendChild(desc);
  wrap.appendChild(btn);

  btn.onclick=()=>{
    if(!name.value) return;
    addItem(targetList, otherList, name.value, desc.value);
    name.value=""; desc.value="";
  };
}

function addItem(listId, otherId, name, desc){
  const list=document.getElementById(listId);
  const div=document.createElement("div");
  div.className="item";
  div.innerHTML="<div class='del'>✕</div><b>"+name+"</b><div>"+desc+"</div>";
  div.querySelector(".del").onclick=()=>div.remove();
  if(otherId){
    const mv=document.createElement("button");
    mv.textContent="Переместить";
    mv.className="moveBtn";
    mv.onclick=()=>{
      div.remove();
      document.getElementById(otherId).appendChild(div);
    };
    div.appendChild(mv);
  }
  list.appendChild(div);
}

addListControls("invAdd","inventory","stash");
addListControls("stashAdd","stash","inventory");

function addTalent(){
  addItem("talents",null,
    talName.value,
    talDesc.value
  );
  talName.value=""; talDesc.value="";
}
</script>
</body>
</html>
