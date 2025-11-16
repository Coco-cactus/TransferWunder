<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Transfer Wunder</title>
<style>
body { font-family: Arial, sans-serif; background: #111; color: #fff; text-align: center; margin:0; padding:0; }
h1 { margin-top: 20px; }
.select-btn { margin: 5px; padding: 8px 12px; border-radius: 8px; cursor: pointer; background: #222; color: #fff; border: 1px solid #555; }
.select-btn.active { background: #00f; color: #fff; }
.pack { width: 220px; height: 320px; border-radius: 20px; margin: 20px auto; display: flex; align-items: center; justify-content: center; font-size: 22px; font-weight: bold; color: #000; transition: transform 0.5s; position: relative; cursor: pointer; background:#444; }
.card { width: 220px; height: 320px; border-radius: 20px; padding: 10px; margin: 20px auto; position: relative; background-size: cover; background-position: center; box-shadow: 0 0 15px rgba(255,255,255,0.3); transition: transform 0.5s, opacity 0.5s; opacity:0; }
.card.show { opacity:1; transform: scale(1.1); }
.rating { font-size: 40px; font-weight: bold; position: absolute; top: 10px; left: 10px; }
.player-img { width: 120px; height: 120px; border-radius: 50%; position: absolute; top: 90px; left: 50%; transform: translateX(-50%); background: #000; }
.startelf { width: 80%; margin: 20px auto; background: #222; padding: 10px; border-radius: 10px; }
.startelf div { display:flex; justify-content: space-between; padding: 5px; border-bottom:1px solid #555; cursor:pointer; }
.result-screen { margin-top: 20px; font-size: 24px; }
</style>
</head>
<body>
<h1>Transfer Wunder</h1>
<h3>Form & Farbe wählen</h3>
<div id="shape-container"></div>
<div id="color-container"></div>
<div id="pack" class="pack">Klick zum Öffnen</div>
<div id="result"></div>
<h3>Startelf (max. 11 Spieler)</h3>
<div id="startelf" class="startelf"></div>
<div id="ges-sum"></div>
<div id="final-result" class="result-screen"></div>
<script>
const SHAPES=["Dreieck","Kreis","Quadrat","Stern","Hexagon","Pfeil","Herz","Raute","Trapez","Halbkreis"];
const COLORS=["Grün","Blau","Rot","Gelb","Orange","Lila","Schwarz","Weiß","Pink","Türkis"];
const RARITIES=[
  {name:"Gold",chance:50,bg:"linear-gradient(gold,#b8860b)",color:"gold"},
  {name:"Diamant",chance:30,bg:"linear-gradient(white,lightblue)",color:"lightblue"},
  {name:"Regenbogen",chance:15,bg:"linear-gradient(45deg,red,orange,yellow,green,blue,purple)",color:"violet"},
  {name:"Ballon d'Or",chance:5,bg:"linear-gradient(black,gold)",color:"orange"}
];

const PLAYER_STATS=[
  {name:"Messi",rating:93},{name:"Ronaldo",rating:92},{name:"Neymar",rating:91},{name:"Mbappe",rating:91},{name:"Lewandowski",rating:91},
  {name:"De Bruyne",rating:91},{name:"Haaland",rating:90},{name:"Modric",rating:89},{name:"Kante",rating:88},{name:"Salah",rating:90},
  {name:"Griezmann",rating:87},{name:"Dybala",rating:87},{name:"Hazard",rating:88},{name:"Kane",rating:88},{name:"Aubameyang",rating:87},
  {name:"Sterling",rating:87},{name:"Sancho",rating:87},{name:"Verratti",rating:86},{name:"Goretzka",rating:86},{name:"Firmino",rating:87},
  {name:"Alisson",rating:89},{name:"Buffon",rating:88},{name:"Ter Stegen",rating:89},{name:"Courtois",rating:90},{name:"Oblak",rating:91},
  {name:"Pique",rating:88},{name:"Ramos",rating:89},{name:"Varane",rating:88},{name:"Chiellini",rating:87},{name:"Bonucci",rating:87},
  {name:"Thiago Silva",rating:87},{name:"Gundogan",rating:86},{name:"Fabinho",rating:86},{name:"Rodri",rating:86},{name:"Kimmich",rating:89},
  {name:"Jorginho",rating:87},{name:"Casemiro",rating:88},{name:"Busquets",rating:86},{name:"Fabregas",rating:85},{name:"Vidal",rating:85},
  {name:"Muller",rating:88},{name:"Sane",rating:87},{name:"Mount",rating:86},{name:"Phil Foden",rating:86},{name:"De Ligt",rating:88},
  {name:"Sergio Ramos",rating:89},{name:"Dani Carvajal",rating:87},{name:"Hakimi",rating:87},{name:"Alexander-Arnold",rating:87},{name:"Walker",rating:86},
  {name:"Cancelo",rating:87},{name:"Robertson",rating:86},{name:"Gnabry",rating:87},{name:"Insigne",rating:86},{name:"Zaniolo",rating:85},
  {name:"Vinicius Jr.",rating:87},{name:"Rodrygo",rating:86},{name:"Ferran Torres",rating:86},{name:"Joao Felix",rating:87},{name:"Dembele",rating:87},
  {name:"Coutinho",rating:86},{name:"Pulisic",rating:86},{name:"Raphinha",rating:85},{name:"Richarlison",rating:85},{name:"Odegaard",rating:87},
  {name:"Kroos",rating:88},{name:"Haaland",rating:90},{name:"Lewandowski",rating:91},{name:"Messi",rating:93},{name:"Ronaldo",rating:92},
  {name:"Neymar",rating:91},{name:"Salah",rating:90},{name:"Sterling",rating:87},{name:"Kane",rating:88},{name:"Aubameyang",rating:87},
  {name:"Griezmann",rating:87},{name:"Dybala",rating:87},{name:"Hazard",rating:88},{name:"Firmino",rating:87},{name:"Verratti",rating:86},
  {name:"Goretzka",rating:86},{name:"Sancho",rating:87},{name:"Rodri",rating:86},{name:"Kimmich",rating:89},{name:"Alisson",rating:89},
  {name:"Oblak",rating:91},{name:"Courtois",rating:90},{name:"Messi",rating:93},{name:"Ronaldo",rating:92},{name:"Mbappe",rating:91},
  {name:"Neymar",rating:91},{name:"Lewandowski",rating:91},{name:"Salah",rating:90},{name:"Sterling",rating:87},{name:"Kane",rating:88},
  {name:"Aubameyang",rating:87},{name:"Griezmann",rating:87},{name:"Dybala",rating:87},{name:"Hazard",rating:88},{name:"Firmino",rating:87}
];

const players=[];
for(let i=0;i<100;i++){
  let shape=SHAPES[Math.floor(i/10)];
  let color=COLORS[i%10];
  let rarityRoll=Math.random()*100,sum=0,rarity=RARITIES[0];
  for(let r of RARITIES){sum+=r.chance;if(rarityRoll<=sum){rarity=r;break;}}
  let stat=PLAYER_STATS[i];
  if(!stat){continue;} // Skip undefined players
  players.push({id:i+1,name:stat.name,shape:shape,color:color,rating:stat.rating,rarity:rarity.name,rarityBg:rarity.bg,rarityColor:rarity.color});
}

let selectedShape=SHAPES[0];
let selectedColor=COLORS[0];
const shapeContainer=document.getElementById('shape-container');
const colorContainer=document.getElementById('color-container');
SHAPES.forEach(s=>{let btn=document.createElement('button'); btn.innerText=s; btn.className='select-btn'; btn.onclick=()=>{selectedShape=s; updateSelection();}; shapeContainer.appendChild(btn);});
COLORS.forEach(c=>{let btn=document.createElement('button'); btn.innerText=c; btn.className='select-btn'; btn.onclick=()=>{selectedColor=c; updateSelection();}; colorContainer.appendChild(btn);});
function updateSelection(){
  Array.from(shapeContainer.children).forEach(b=>b.classList.remove('active'));
  Array.from(colorContainer.children).forEach(b=>b.classList.remove('active'));
  Array.from(shapeContainer.children).find(b=>b.innerText==selectedShape).classList.add('active');
  Array.from(colorContainer.children).find(b=>b.innerText==selectedColor).classList.add('active');
}
updateSelection();

let startelf=JSON.parse(localStorage.getItem('startelf'))||[];
function updateStartelf(){
  const container=document.getElementById('startelf'); container.innerHTML='';
  startelf.forEach((p,i)=>{
    let div=document.createElement('div');
    div.innerHTML=`<span style='color:${p.rarityColor}'>${p.name} (${p.rarity})</span><span>${p.rating}</span>`;
    div.onclick=()=>{startelf.splice(i,1); saveStartelf(); updateStartelf(); updateGES();};
    container.appendChild(div);
  });
}
function updateGES(){
  const sum=startelf.reduce((a,b)=>a+b.rating,0);
  document.getElementById('ges-sum').innerText='GES-Summe: '+sum;
  if(startelf.length>=11){
    document.getElementById('final-result').innerText=`Startelf vollständig! Endbewertung: ${sum}`;
    startelf=[];
    saveStartelf();
    updateStartelf();
  }
}
function saveStartelf(){localStorage.setItem('startelf',JSON.stringify(startelf));}
updateStartelf(); updateGES();

const packDiv=document.getElementById('pack');
packDiv.onclick=function(){
  const filtered=players.filter(p=>p.shape==selectedShape && p.color==selectedColor);
  if(filtered.length==0){alert('Keine Spieler mit dieser Form/Farbe!'); return;}
  let rarityRoll=Math.random()*100,sum=0,rarity=RARITIES[0];
  for(let r of RARITIES){sum+=r.chance;if(rarityRoll<=sum){rarity=r; break;}}
  const candidates=filtered.filter(p=>p.rarity==rarity.name); if(candidates.length==0)candidates.push(...filtered);
  if(candidates.length===0){alert('Keine Spieler verfügbar!'); return;}
  const player=candidates[Math.floor(Math.random()*candidates.length)];
  if(!player){alert('Fehler beim Laden des Spielers.'); return;}
  const result=document.getElementById('result');
  result.innerHTML=`<div class='card show' style='background:${player.rarityBg}'><div class='rating'>${player.rating}</div><div class='player-name' style='position:absolute;bottom:20px;width:100%;text-align:center;font-weight:bold;color:${player.rarityColor}'>${player.name}</div></div>`;
  result.querySelector('.card').onclick=()=>{
    if(startelf.length<11 && !startelf.includes(player)){
      startelf.push(player); saveStartelf(); updateStartelf(); updateGES();
    }
  };
}
</script>
</body>
</html>
