# Escape-Urban-
Jogo De Polícia, Na Fase De Iniciante Irei Atualizar 
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Urban Escape</title>
<style>
body { margin:0; overflow:hidden; background:#111; }
canvas { background:#2c3e50; display:block; }
#ui {
 position:absolute;
 top:10px;
 left:10px;
 color:white;
 font-family:Arial;
}
button {
 position:absolute;
 bottom:20px;
 width:80px;
 height:80px;
 font-size:20px;
 opacity:0.7;
}
#left { left:20px; }
#right { right:20px; }
</style>
</head>
<body>

<div id="ui">
<div id="money">💰 0</div>
<div id="wanted">⭐ 1</div>
<div id="health">❤️ 100</div>
</div>

<button id="left">◀</button>
<button id="right">▶</button>

<canvas id="game"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

let keys = {};
let money = 0;
let wanted = 1;
let health = 100;

class Car {
 constructor(x,y,color){
   this.x=x;
   this.y=y;
   this.angle=0;
   this.speed=0;
   this.color=color;
 }
 update(){
   this.x += Math.sin(this.angle)*this.speed;
   this.y -= Math.cos(this.angle)*this.speed;
   this.speed*=0.98;
 }
 draw(){
   ctx.save();
   ctx.translate(this.x,this.y);
   ctx.rotate(this.angle);
   ctx.fillStyle=this.color;
   ctx.fillRect(-15,-25,30,50);
   ctx.restore();
 }
}

let player=new Car(canvas.width/2,canvas.height/2,"lime");
let police=[];
let helicopter=null;

function spawnPolice(){
 police.push(new Car(Math.random()*canvas.width,Math.random()*canvas.height,"red"));
}

function spawnHelicopter(){
 helicopter=new Car(player.x,player.y-200,"yellow");
}

function update(){
 player.update();
 money+=0.1;

 if(money>100*wanted){
   wanted++;
   spawnPolice();
 }

 police.forEach(p=>{
   let dx=player.x-p.x;
   let dy=player.y-p.y;
   let angle=Math.atan2(dy,dx);
   p.angle=angle-Math.PI/2;
   p.speed=2+wanted*0.5;
   p.update();

   if(Math.hypot(player.x-p.x,player.y-p.y)<30){
     health-=0.5;
   }
 });

 if(wanted>=5 && !helicopter){
   spawnHelicopter();
 }

 if(helicopter){
   helicopter.x=player.x;
   helicopter.y=player.y-150;
   helicopter.draw();
 }

 if(health<=0){
   alert("Game Over! Dinheiro: "+Math.floor(money));
   location.reload();
 }

 document.getElementById("money").innerText="💰 "+Math.floor(money);
 document.getElementById("wanted").innerText="⭐ "+wanted;
 document.getElementById("health").innerText="❤️ "+Math.floor(health);
}

function draw(){
 ctx.clearRect(0,0,canvas.width,canvas.height);
 player.draw();
 police.forEach(p=>p.draw());
}

function loop(){
 update();
 draw();
 requestAnimationFrame(loop);
}

document.addEventListener("keydown",e=>{
 if(e.key==="ArrowUp") player.speed+=0.5;
 if(e.key==="ArrowLeft") player.angle-=0.1;
 if(e.key==="ArrowRight") player.angle+=0.1;
});

document.getElementById("left").ontouchstart=()=>player.angle-=0.2;
document.getElementById("right").ontouchstart=()=>player.angle+=0.2;

spawnPolice();
loop();
</script>

</body>
</html>
