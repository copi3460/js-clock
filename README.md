---

## Det færdige ur

```javascript
const ctx = klokken.getContext("2d");
//tick();
setInterval(tick, 1000);


function tick(){
  // Tegn cirkel: arc(center-x, center-y, radius, start-radian, slut-radian)
  ctx.arc(200,200,20,0,2*Math.PI);
  ctx.fill();                   // fyld cirklen med farve
  
  ctx.strokeStyle = "#333";      // Stregfarve (kant)
  ctx.lineWidth=10;              // Stregtykkelse i pixels
  ctx.beginPath();               // Start en ny tegning (løft pen)
  
  // Tegn cirkel: arc(center-x, center-y, radius, start-radian, slut-radian)
  ctx.arc(200,200,190,0,2*Math.PI);
  ctx.stroke();                  // Træk en linje rund om cirklen


// Her bestemmer vi, hvor stort uret skal være. Radius er afstanden fra midten og ud til kanten.
const radius = 200; 

// Her finder vi det præcise midtpunkt på urets skærm, både vandret (cx) og lodret (cy).
const cx = klokken.width/2; 
const cy = klokken.height/2; 

// Der er 12 timer på et ur. Hvis man skal hele vejen rundt i en cirkel (360 grader), 
// kalder computeren det for "2 * Math.PI". 
// Vi deler den fulde cirkel med 12, så vi ved præcis, hvor meget vi skal dreje for hver time.
const timeVinkel = (2 * Math.PI) / 12;  

// Nu flytter vi vores usynlige tegne-hånd hen til midten af uret. 
// Det gør det meget nemmere at tegne i en cirkel bagefter!
ctx.translate(cx,cy); 

  
// --- 2. INDSTIL TEKST-STYLES ---
  ctx.font = "20px 'Georgia', serif"; // Romertal ser flotte ud med en 'serif' font
  ctx.textAlign = "center";      
  ctx.textBaseline = "middle";   

// --- 3. ARRAY MED ROMERTAL ---
// Indeks 0 = XII (Kl 12), Indeks 1 = I (Kl 1) osv.
  const romertal = ["XII", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX", "X", "XI"];
  

  // Nu laver vi en løkke (en "for-loop"), der gør det samme 12 gange – én gang for hver time.
  for (let i = 0; i < 12; i++) { 
    // Beregn vinklen baseret på indeks (i)
    let vinkel = i * ((2 * Math.PI) / 12);
    
    ctx.rotate(vinkel);             // 1. Drej papiret
    ctx.translate(0, -radius * 0.73); // 2. Gå op mod kanten (lidt tættere på end før, da romertal fylder mere)
    
    // Her tegner vi selve prikken (en lille cirkel). 
    // Fordi vi bruger et minus-tal (-170), hopper vi direkte OP i toppen af uret. 
    // Så den allerførste prik lander helt automatisk på klokken 12!
    ctx.beginPath();
    ctx.arc(0, -27, 6, 0, 2 * Math.PI); 
    
    // Vi vælger en flot rød farve til prikken.
    ctx.fillStyle = "red";  
    
    // Farv prikken rød!
    // ctx.fill();  
    ctx.fillRect(0, -27, 6, 16);
    
    ctx.rotate(-vinkel);            // 3. RET DETALJE: Drej papiret tilbage så teksten står lige [1]
    ctx.fillStyle = "#222";     
    ctx.fillText(romertal[i], 0, 0); // 4. Hent teksten fra vores array og skriv den
    
    // 5. Nulstil positioner
    ctx.rotate(vinkel);             
    ctx.translate(0, radius * 0.73); 
    ctx.rotate(-vinkel);            
  }
  visere(radius);

  ctx.translate(-cx,-cy);
  
}

function visere(radius){
  // Hent det aktuelle tidspunkt, så viserne kan tegnes ud fra virkelige data.
  const nu = new Date();
  const timer = nu.getHours();
  const minutter = nu.getMinutes();
  const sekunder = nu.getSeconds();

    // Timerviseren: omregn timer til en vinkel, tegn en streg, og gendan bagefter.
  const timeVinkelTimer = (2 * Math.PI) / 12;
  const vinkelTimer = timer * timeVinkelTimer;

  ctx.lineWidth = 5;
  ctx.strokeStyle = "black";
  ctx.rotate(vinkelTimer);
  // Tegn selve viseren fra centrum og op mod kanten.
  ctx.beginPath();
  ctx.moveTo(0,0);
  ctx.lineTo(0,-radius*0.5);
  ctx.lineWidth = 8;
  ctx.stroke();
  // Gendan den oprindelige rotation og position.
  ctx.rotate(-vinkelTimer);

  // Minutviseren: samme idé, men med 60 delinger af en hel cirkel.
  const timeVinkelMinutter = (2 * Math.PI) / 60;
  const vinkelMinutter = minutter * timeVinkelMinutter;
  // Gem tilstand før vi roterer, så næste tegnede element starter rent.
  ctx.rotate(vinkelMinutter);
  // Tegn minutviseren, som er længere end timerviseren.
  ctx.beginPath();
  ctx.moveTo(0,0);
  ctx.lineTo(0,-radius*0.7);
  ctx.lineWidth = 5;
  ctx.stroke();
  // Tilbage til udgangspunktet efter minutviseren.
  ctx.rotate(-vinkelMinutter);

// Sekundviseren: den opdateres hvert sekund og får en tydelig rød farve.
  const timeVinkelSekunder = (2 * Math.PI) / 60;
  const vinkelSekunder = sekunder * timeVinkelSekunder;
  // Gem og drej igen, så sekundviseren kan tegnes uafhængigt af de andre.
  ctx.rotate(vinkelSekunder);
  // Tegn den tynde, lange sekundviser.
  ctx.beginPath();
  ctx.moveTo(0,0);
  ctx.lineTo(0,-radius*0.9);
  ctx.lineWidth = 2;
  ctx.strokeStyle = "red";
  ctx.stroke();
  // Gendan standardtilstanden, så uret kan afsluttes korrekt.
  ctx.rotate(-vinkelSekunder);
  

}
```





