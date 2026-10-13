<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no">
<title>WARZONE 3D - Brawl Edition</title>
<style>
*{box-sizing:border-box}
body{margin:0;overflow:hidden;background:#1a1c29;color:white;font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif}
canvas{display:block}

/* Brawl Stars Tarzı Menü ve Paneller */
#menu{position:fixed;inset:0;z-index:5;display:flex;align-items:center;justify-content:center;text-align:center;background:radial-gradient(circle,#2c3e50,#1a1c29);padding:15px}
.panel{width:min(440px,100%);padding:26px;background:#1e2235ee;border:3px solid #f1c40f;border-radius:24px;box-shadow:0 10px 25px rgba(0,0,0,0.6)}
h1{font-size:40px;letter-spacing:2px;color:#f39c12;margin:0 0 10px 0;text-shadow:3px 3px 0px #c0392b}
button,select{padding:12px;border:2px solid #f39c12;border-radius:12px;background:#2c3e50;color:white;font-weight:bold;font-size:16px;margin:6px;transition:0.2s}
button:hover{background:#f39c12;color:#1a1c29;transform:scale(1.03)}
button{cursor:pointer}
#start{background:#2ecc71;border-color:#27ae60;width:90%;font-size:20px;box-shadow:0 5px 0 #1e8449}
#start:hover{background:#27ae60;color:white}

#hud{display:none;position:fixed;inset:0;pointer-events:none;text-shadow:1px 2px 3px #000}
#stats{position:absolute;top:15px;left:15px;background:#1e2235dd;border:2px solid #f39c12;padding:14px;border-radius:14px;line-height:1.6;font-size:15px}
#cross{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);font-size:22px;color:#f1c40f}
#controls{position:absolute;bottom:20px;left:15px;right:15px;display:flex;justify-content:space-between;align-items:end}
#controls button{pointer-events:auto;background:#34495eee;border:2px solid #bdc3c7;touch-action:none;min-width:52px;min-height:52px;border-radius:14px}
#pad{display:grid;grid-template-columns:repeat(3,52px);gap:4px}
#pad button{margin:0;padding:5px}
#fire{background:#e74c3c!important;border-color:#c0392b!important;border-radius:50%!important;min-width:80px!important;min-height:80px!important;font-size:16px}
#rightControls{display:flex;align-items:end;gap:10px}
#msg{position:absolute;top:25%;width:100%;text-align:center;font-size:26px;color:#f1c40f;font-weight:bold;text-shadow:2px 2px 0 #000}

/* Üst Butonlar (Ayarlar ve Menü) */
.top-btn{position:absolute;top:15px;pointer-events:auto;background:#e67e22;border:2px solid #d35400;border-radius:12px;padding:10px 16px;font-weight:bold}
#exit{right:15px}
#settingsBtn{right:95px;background:#3498db;border-color:#2980b9}

/* Ayarlar Menüsü Modalı */
#settingsModal{display:none;position:fixed;inset:0;background:rgba(0,0,0,0.7);z-index:10;align-items:center;justify-content:center}
.modal-content{background:#1e2235;border:3px solid #3498db;padding:25px;border-radius:20px;width:320px;text-align:center}
</style>
</head>
<body>

<!-- ANA MENÜ -->
<div id="menu">
 <div class="panel">
  <h1>WARZONE 3D</h1>
  <p style="color:#bdc3c7;margin-top:0">Brawl Arena Savaşları</p>
  <div id="preview" style="height:80px;color:#2ecc71;font-size:14px;font-weight:bold">
   ⭐ SAVAŞÇINI SEÇ VE ARENAYA GİR!
  </div>
  <p>Silahını Seç:</p>
  <select id="weapon">
   <option value="rifle">Saldırı Tüfeği</option>
   <option value="minigun">Minigun</option>
   <option value="shotgun">Pompalı Tüfek</option>
   <option value="sniper">Keskin Nişancı</option>
  </select>
  <p>Başlangıç Kredisi: <b>1000</b></p>
  <button id="start">OYNA!</button>
  <button id="menuSettingsBtn" style="background:#3498db;border-color:#2980b9;width:90%">AYARLAR</button>
 </div>
</div>

<!-- OYUN İÇİ ARAYÜZ (HUD) -->
<div id="hud">
 <div id="stats">
  ❤️ Can: <span id="hp">100</span><br>
  💰 Kredi: <span id="money">1000</span><br>
  🔫 Silah: <span id="gun">Saldırı Tüfeği</span><br>
  🎯 Skor: <span id="score">0</span><br>
  👾 Rakip: <span id="count">0</span>
 </div>
 <button id="exit" class="top-btn">MENÜ</button>
 <button id="settingsBtn" class="top-btn">⚙️</button>
 <div id="cross">+</div>
 <div id="msg"></div>
 <div id="controls">
  <div id="pad">
   <span></span><button data-key="w">▲</button><span></span>
   <button data-key="a">◀</button><button data-key="s">▼</button><button data-key="d">▶</button>
  </div>
  <div id="rightControls">
   <button id="fire">ATEŞ!</button>
  </div>
 </div>
</div>

<!-- AYARLAR MODALI -->
<div id="settingsModal">
 <div class="modal-content">
  <h2>AYARLAR</h2>
  <p>Grafik Kalitesi:</p>
  <select id="qualitySetting">
   <option value="high">Yüksek (Ultra)</option>
   <option value="medium" selected>Orta</option>
   <option value="low">Düşük (Performans)</option>
  </select>
  <br><br>
  <button id="closeSettings" style="background:#e74c3c;border-color:#c0392b">KAPAT</button>
 </div>
</div>

<script type="module">
import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js';

const $=id=>document.getElementById(id);
const names={rifle:'Saldırı Tüfeği',minigun:'Minigun',shotgun:'Pompalı Tüfek',sniper:'Keskin Nişancı'};
let scene,camera,renderer,player,gun;
let enemies=[],obstacles=[],shots=[];
let keys={},touch={},running=false,credits=1000,score=0,hp=100;
let weapon='rifle',yaw=0,lastShot=0,fireHeld=false;
let gameOver=false,lastTime=0;

// Ayarlar Paneli Kontrolleri
$('settingsBtn').onclick=()=>$('settingsModal').style.display='flex';
$('menuSettingsBtn').onclick=()=>$('settingsModal').style.display='flex';
$('closeSettings').onclick=()=>$('settingsModal').style.display='none';
$('qualitySetting').onchange=(e)=>{
 if(!renderer)return;
 if(e.target.value==='low') renderer.setPixelRatio(1);
 else if(e.target.value==='medium') renderer.setPixelRatio(Math.min(devicePixelRatio,1.2));
 else renderer.setPixelRatio(Math.min(devicePixelRatio,2));
};

function material(color,roughness=0.7){
 return new THREE.MeshStandardMaterial({color,roughness,metalness:0.2});
}
function addBox(x,y,z,w,h,d,color){
 const m=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),material(color));
 m.position.set(x,y,z);m.castShadow=true;m.receiveShadow=true;
 scene.add(m);
 return m;
}
function blocked(x,z,r=.65){
 return obstacles.some(o=>Math.abs(x-o.x)<o.w/2+r && Math.abs(z-o.z)<o.d/2+r);
}

function makeWorld(){
 scene=new THREE.Scene();
 scene.background=new THREE.Color(0x2c3e50);
 scene.fog=new THREE.Fog(0x2c3e50,30,120);

 camera=new THREE.PerspectiveCamera(60,innerWidth/innerHeight,.1,200);
 renderer=new THREE.WebGLRenderer({antialias:true});
 renderer.setSize(innerWidth,innerHeight);
 renderer.setPixelRatio(Math.min(devicePixelRatio,1.2));
 renderer.shadowMap.enabled=true;
 renderer.shadowMap.type=THREE.PCFSoftShadowMap;
 renderer.outputColorSpace=THREE.SRGBColorSpace;
 document.body.appendChild(renderer.domElement);

 scene.add(new THREE.HemisphereLight(0xffffff,0x34495e,1.5));
 const sun=new THREE.DirectionalLight(0xf1c40f,2);
 sun.position.set(-30,50,30);
 sun.castShadow=true;
 sun.shadow.mapSize.set(1024,1024);
 scene.add(sun);

 // Zemin
 const ground=new THREE.Mesh(new THREE.PlaneGeometry(90,90),material(0x27ae60));
 ground.rotation.x=-Math.PI/2;
 ground.position.y=-.06;
 ground.receiveShadow=true;
 scene.add(ground);

 const grid=new THREE.GridHelper(90,45,0x2ecc71,0x2ecc71);
 grid.position.y=0;
 scene.add(grid);

 // Sınır Duvarları
 addBox(0,1,-45,90,2,1,0x34495e);
 addBox(0,1,45,90,2,1,0x34495e);
 addBox(-45,1,0,1,2,90,0x34495e);
 addBox(45,1,0,1,2,90,0x34495e);

 // Rastgele Engel ve Bloklar
 for(let i=0;i<35;i++){
  let x,z,w,d,h;
  do{
   x=(Math.random()-.5)*75;
   z=(Math.random()-.5)*75;
   w=2+Math.random()*4;
   d=2+Math.random()*4;
   h=1.5+Math.random()*3;
  }while((Math.abs(x)<8&&Math.abs(z)<8)||blocked(x,z));
  obstacles.push({x,z,w,d});
  addBox(x,h/2,z,w,h,d,[0x7f8c8d,0x95a5a6,0xd35400,0x8e44ad][i%4]);
 }
 createPlayer();
}

function createPlayer(){
 player=new THREE.Group();
 const body=new THREE.Mesh(new THREE.BoxGeometry(.7,1,.42),material(0x3498db));
 body.position.y=1.2;body.castShadow=true;player.add(body);
 const head=new THREE.Mesh(new THREE.SphereGeometry(.24,12,10),material(0xf5cd79));
 head.position.y=1.92;player.add(head);
 player.position.set(0,0,0);
 scene.add(player);

 gun=new THREE.Mesh(new THREE.BoxGeometry(.15,.16,.8),material(0x333333));
 gun.position.set(.35,1.5,-.5);
 player.add(gun);
}

function makeEnemy(){
 let x,z,tries=0;
 do{
  x=(Math.random()-.5)*75;
  z=(Math.random()-.5)*75;
  tries++;
 }while(tries<40&&(Math.hypot(x-player.position.x,z-player.position.z)<15||blocked(x,z,.8)));
 
 const e=new THREE.Group();
 const body=new THREE.Mesh(new THREE.BoxGeometry(.7,1,.45),material(0xe74c3c));
 body.position.y=1.1;e.add(body);
 const head=new THREE.Mesh(new THREE.SphereGeometry(.23,10,8),material(0xf5cd79));
 head.position.y=1.8;e.add(head);
 e.position.set(x,0,z);
 e.userData={hp:100,attack:0};
 scene.add(e);enemies.push(e);
}

function updateUI(){
 $('hp').textContent=Math.max(0,Math.ceil(hp));
 $('money').textContent=credits;
 $('score').textContent=score;
 $('count').textContent=enemies.length;
 $('gun').textContent=names[weapon];
}

function setWeapon(value){
 weapon=value;
 $('gun').textContent=names[weapon];
 const widths={rifle:.8,minigun:1.05,shotgun:.65,sniper:1.2};
 gun.scale.z=widths[weapon];
}

function startGame(){
 if(!renderer)makeWorld();
 credits=1000;score=0;hp=100;gameOver=false;
 enemies.forEach(e=>scene.remove(e));
 enemies=[];shots.forEach(s=>scene.remove(s));shots=[];
 player.position.set(0,0,0);yaw=0;
 setWeapon($('weapon').value);
 for(let i=0;i<6;i++)makeEnemy();
 $('menu').style.display='none';
 $('hud').style.display='block';
 $('msg').textContent='';
 running=true;
 lastTime=performance.now();
 updateUI();
}

function shoot(){
 if(!running||gameOver)return;
 const now=performance.now();
 const delay={rifle:250,minigun:80,shotgun:600,sniper:800}[weapon];
 if(now-lastShot<delay)return;
 lastShot=now;

 const origin=player.position.clone().add(new THREE.Vector3(0,1.5,0));
 const direction=new THREE.Vector3(Math.sin(yaw),0,Math.cos(yaw));

 let ray=new THREE.Raycaster(origin,direction,0,50);
 const hits=ray.intersectObjects(enemies,true);
 let hit=null;
 if(hits.length){
  for(const h of hits){
   let e=h.object;
   while(e.parent&&!enemies.includes(e))e=e.parent;
   if(enemies.includes(e)){hit=e;break}
  }
 }

 const end=origin.clone().add(direction.clone().multiplyScalar(40));
 const line=new THREE.Line(
  new THREE.BufferGeometry().setFromPoints([origin,end]),
  new THREE.LineBasicMaterial({color:0xf1c40f,transparent:true,opacity:.8}));
 scene.add(line);shots.push(line);

 if(hit){
  hit.userData.hp-=weapon==='sniper'?100:weapon==='shotgun'?35:40;
  if(hit.userData.hp<=0){
   scene.remove(hit);enemies=enemies.filter(e=>e!==hit);
   score++;credits+=100;
   $('msg').textContent='+100 KREDİ!';
   setTimeout(()=>{if($('msg').textContent==='+100 KREDİ!')$('msg').textContent=''},600);
   makeEnemy();
  }
 }
 updateUI();
}

function endGame(){
 gameOver=true;running=false;
 $('msg').innerHTML='OYUN BİTTİ<br>Skor: '+score+'<br><button id="again" style="background:#2ecc71;border:none;padding:10px 20px;border-radius:10px;color:white;cursor:pointer">TEKRAR OYNA</button>';
 $('again').onclick=startGame;
}

function movePlayer(dt){
 let f=0,s=0;
 if(keys.w||touch.w)f++;
 if(keys.s||touch.s)f--;
 if(keys.a||touch.a)s++;
 if(keys.d||touch.d)s--;
 if(f!==0||s!==0){
  yaw=Math.atan2(s,f);
 }
 const len=Math.hypot(f,s)||1;
 f/=len;s/=len;
 const speed=9*dt;
 const dx=f*speed;
 const dz=s*speed;
 if(!blocked(player.position.x+dx,player.position.z,.5))player.position.x+=dx;
 if(!blocked(player.position.x,player.position.z+dz,.5))player.position.z+=dz;
 player.position.x=THREE.MathUtils.clamp(player.position.x,-42,42);
 player.position.z=THREE.MathUtils.clamp(player.position.z,-42,42);
 player.rotation.y=yaw;
}

function updateEnemies(dt){
 for(const e of enemies){
  const dx=player.position.x-e.position.x;
  const dz=player.position.z-e.position.z;
  const dist=Math.hypot(dx,dz);
  e.rotation.y=Math.atan2(dx,dz);
  if(dist>2.2){
   const step=Math.min(2.2*dt,dist-2);
   const nx=e.position.x+dx/dist*step;
   const nz=e.position.z+dz/dist*step;
   if(!blocked(nx,e.position.z,.4))e.position.x=nx;
   if(!blocked(e.position.x,nz,.4))e.position.z=nz;
  }else{
   e.userData.attack+=dt;
   if(e.userData.attack>1.5){
    hp-=8;e.userData.attack=0;updateUI();
    if(hp<=0)endGame();
   }
  }
 }
}

function animate(now){
 requestAnimationFrame(animate);
 if(!renderer)return;
 const dt=Math.min((now-lastTime)/1000,.05);
 lastTime=now;

 if(running&&!gameOver){
  movePlayer(dt);
  updateEnemies(dt);
  if(fireHeld)shoot();

  // Brawl Stars Tarzı Üstten İzometrik Kamera Takibi
  const targetCamPos=new THREE.Vector3(player.position.x, player.position.y + 16, player.position.z + 12);
  camera.position.lerp(targetCamPos, .1);
  camera.lookAt(player.position.x, player.position.y + 1, player.position.z);

  for(const s of shots){
   scene.remove(s);s.geometry.dispose();s.material.dispose();
  }
  shots=[];
 }
 renderer.render(scene,camera);
}

$('start').onclick=startGame;
$('exit').onclick=()=>{
 running=false;
 $('hud').style.display='none';
 $('menu').style.display='flex';
};
$('weapon').onchange=()=>{if(gun)setWeapon($('weapon').value)};

addEventListener('keydown',e=>{keys[e.key.toLowerCase()]=true});
addEventListener('keyup',e=>{keys[e.key.toLowerCase()]=false});

$('fire').addEventListener('pointerdown',e=>{e.preventDefault();fireHeld=true;shoot()});
addEventListener('pointerup',()=>fireHeld=false);

document.querySelectorAll('[data-key]').forEach(btn=>{
 const k=btn.dataset.key;
 btn.addEventListener('pointerdown',e=>{e.preventDefault();touch[k]=true});
 for(const ev of ['pointerup','pointercancel','pointerleave'])
  btn.addEventListener(ev,()=>touch[k]=false);
});

addEventListener('resize',()=>{
 if(!renderer||!camera)return;
 camera.aspect=innerWidth/innerHeight;
 camera.updateProjectionMatrix();
 renderer.setSize(innerWidth,innerHeight);
});

requestAnimationFrame(animate);
</script>
</body>
</html>
