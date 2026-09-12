
---

## Tal på urskiven


Når vi skal skrive tekst med `rotate()`, bruger vi en smart "stempel-teknik":

1. Vi drejer papiret til timens vinkel.
2. Vi går op i toppen og skriver tallet.
3. Vigtig detalje: Vi roterer papiret _omvendt_ (`-vinkel`) lige inden vi skriver teksten. Gør vi ikke det, vil tallene ligge ned eller stå på hovedet langs kanten! bagefter roterer vi tilbage.

---

For at forstå, hvordan vi skriver på en canvas, skal vi kigge på `ctx.fillText()`, samt hvordan vi styler og placerer teksten med `font`, `textAlign` og `textBaseline`.

---

## 1. Værktøjet: `ctx.fillText()`

Dette er selve stemplet. Funktionen tager imod tre obligatoriske parametre:

```javascript
ctx.fillText(tekst, x, y);
```

- `tekst`: Den streng (eller det tal), du vil skrive (f.eks. `"XII"` eller `num.toString()`).
- `x`: Den vandrette koordinat på canvasen, hvor stemplet skal trykkes ned.
- `y`: Den lodrette koordinat på canvasen, hvor stemplet skal trykkes ned.

---

## 2. Styling: `ctx.font`

Inden du kalder `fillText()`, skal du fortælle canvasen, hvordan teksten skal se ud. Det gør du med en enkelt tekststreng via `ctx.font`. Formatet følger den klassiske CSS-syntaks (Størrelse + Skrifttype):

```javascript
// Princip-eksempel:
ctx.font = "20px Arial";                  // Standard sans-serif font
ctx.font = "bold 24px Georgia";           // Fed skrift, serif-font (god til Romertal)
ctx.font = "italic bold 16px Courier";    // Kursiv, fed og monospace
```

_Hvis du ikke definerer en font, falder canvas automatisk tilbage til standarden, som er `10px sans-serif`._

---

## 3. Justering (Alignment)

Dette er den absolut vigtigste detalje, når du laver et ur. Når du siger, at teksten skal stå på koordinatet `(x, y)`, hvor på teksten skal det punkt så røre? Er det øverst til venstre, eller lige i midten?

Vi styrer dette med to egenskaber: `textAlign` (vandret) og `textBaseline` (lodret).

## Vandret justering: `ctx.textAlign`

Fortæller canvas, hvordan teksten skal placere sig vandret i forhold til dit `x`-koordinat.

- `"left"` (Standard): Teksten _starter_ ved `x` og bevæger sig mod højre.
- `"right"`: Teksten _ender_ ved `x`.
- `"center"`: Teksten bliver centreret perfekt hen over dit `x`-koordinat. (Dette bruger vi til uret!)

## Lodret justering: `ctx.textBaseline`

Fortæller canvas, hvordan teksten skal placere sig lodret i forhold til dit `y`-koordinat. Default er `"alphabetic"`, hvilket betyder, at `y`-punktet flugter med bunden af bogstaverne (som en usynlig skrivelinje i et hæfte).

- `"top"`: Teksten hænger _under_ dit `y`-koordinat.
- `"bottom"`: Teksten står _oven på_ dit `y`-koordinat.
- `"middle"`: Teksten bliver centreret perfekt vertikalt på dit `y`-koordinat. (Dette bruger vi til uret!)

---

## Visuelt princip-eksempel

Her er et lille isoleret eksempel, du kan teste. Det tegner et rødt kryds i et koordinat, og placerer derefter tekst på tre forskellige måder, så du kan se forskellen:

```javascript
const canvas = document.getElementById("urCanvas");
const ctx = canvas.getContext("2d");

// Vi definerer et punkt midt på skærmen
let x = 200;
let y = 100;

// Sæt font og farve én gang
ctx.font = "20px Arial";
ctx.fillStyle = "black";

// --- EKSEMPEL A: Standard (Ingen justering) ---
// Teksten vil starte ved x, og bunden af teksten vil røre y.
ctx.fillText("Standard", x, y); 

// --- EKSEMPEL B: Perfekt Centreret (Urets teknik) ---
// Teksten vil blive placeret med sit absolutte centrum lige oven i (x, y)
ctx.textAlign = "center";
ctx.textBaseline = "middle";
ctx.fillText("Centreret", x, y + 50); // Tegnet 50px længere nede

// --- EKSEMPEL C: Højrejusteret i toppen ---
ctx.textAlign = "right";
ctx.textBaseline = "top";
ctx.fillText("Højre/Top", x, y + 100); // Tegnet 100px længere nede
```

## Hvorfor er dette kritisk for uret?

Når vi bruger `rotate()`-teknikken i vores ur-loop, flytter vi koordinatsystemets `(0,0)` ud til kanten af uret, hvor tallet skal stå.

Hvis vi ikke brugte `ctx.textAlign = "center"` og `ctx.textBaseline = "middle"`, ville tallet `"XII"` (12) flyve en lille smule over mod højre, og tallet `"VI"` (6) ville ikke sidde centreret i bunden. Ved at sætte begge til midten, sikrer vi, at uanset om tallet er bredt (som `VIII`) eller smalt (som `I`), så sidder det præcis i centrum af det koordinat, vi har udregnet.

---


## Romertal fra et Array

Her opretter vi et Array (en liste) med Romertallene. Da Arrays i JavaScript starter ved indeks `0`, lader vi loopet tælle fra `0` til `11`. For at "0" lander klokken 12, trækker vi `XII` ud som det første element i vores liste.

Erstat JavaScript-delen i din CodePen med dette:

```javascript
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
    ctx.fill();  

    ctx.rotate(-vinkel);            // 3. RET DETALJE: Drej papiret tilbage så teksten står lige [1]
    ctx.fillStyle = "#222";     
    ctx.fillText(romertal[i], 0, 0); // 4. Hent teksten fra vores array og skriv den
    
    // 5. Nulstil positioner
    ctx.rotate(vinkel);             
    ctx.translate(0, radius * 0.73); 
    ctx.rotate(-vinkel);            
  }
}
```

## De vigtige detaljer i denne teknik:

- Hvorfor gå frem og tilbage? Hver gang vi kalder `ctx.translate(0, -radius * 0.75)`, flytter vi det midlertidige `(0,0)` punkt derop, hvor tallet skal stå. For at det næste tal beregnes rigtigt fra midten af uret, er vi nødt til at gå den stik modsat vej tilbage bagefter: `ctx.translate(0, radius * 0.75)`.
- Uden `ctx.rotate(-vinkel)`: Hvis du prøver at fjerne den linje (og dens modpart i bunden), vil du se, at 6-tallet (eller VI) står helt på hovedet i bunden af uret, og 3-tallet ligger ned. Ved at mod-rotere lige inden vi skriver, sikrer vi, at alle tal står pænt og opret som på et rigtigt ur.

# Hurtig reference

| Funktion | Betydning |
|---|---|
| `getContext("2d")` | få adgang til tegning |
| `fillStyle` | fyldfarve |
| `strokeStyle` | kantfarve |
| `lineWidth` | linjetykkelse |
| `beginPath()` | ny tegne-rute |
| `arc()` | cirkel/bue |
| `fill()` | udfyld |
| `stroke()` | tegn kant |
| `translate()` | flyt koordinatsystem |
| `rotate()` | drej koordinatsystem |
| `fillRect()` | fyldt rektangel |
| `font` | tekststil |
| `fillText()` | tekst på canvas |

---
# Tiden 



```javascript
  // Hent det aktuelle tidspunkt, så viserne kan tegnes ud fra virkelige data.
  const nu = new Date();
  const timer = nu.getHours();
  const minutter = nu.getMinutes();
  const sekunder = nu.getSeconds();
```


### Time viseren
```javascript
  // Timerviseren: omregn timer til en vinkel, tegn en streg, og gendan bagefter.
  const timeVinkelTimer = (2 * Math.PI) / 12;
  const vinkelTimer = timer * timeVinkelTimer;
  // Gem koordinatsystemet, så vi kan dreje kun denne viser uden at påvirke resten.
  ctx.save();
  ctx.rotate(vinkelTimer);
  // Tegn selve viseren fra centrum og op mod kanten.
  ctx.beginPath();
  ctx.moveTo(0,0);
  ctx.lineTo(0,-radius*0.5);
  ctx.lineWidth = 8;
  ctx.stroke();
  // Gendan den oprindelige rotation og position.
  ctx.restore();
```


### Minut viseren
```javascript
  // Minutviseren: samme idé, men med 60 delinger af en hel cirkel.
  const timeVinkelMinutter = (2 * Math.PI) / 60;
  const vinkelMinutter = minutter * timeVinkelMinutter;
  // Gem tilstand før vi roterer, så næste tegnede element starter rent.
  ctx.save();
  ctx.rotate(vinkelMinutter);
  // Tegn minutviseren, som er længere end timerviseren.
  ctx.beginPath();
  ctx.moveTo(0,0);
  ctx.lineTo(0,-radius*0.7);
  ctx.lineWidth = 5;
  ctx.stroke();
  // Tilbage til udgangspunktet efter minutviseren.
  ctx.restore();
```


### Sekund viseren
```javascript
  // Sekundviseren: den opdateres hvert sekund og får en tydelig rød farve.
  const timeVinkelSekunder = (2 * Math.PI) / 60;
  const vinkelSekunder = sekunder * timeVinkelSekunder;
  // Gem og drej igen, så sekundviseren kan tegnes uafhængigt af de andre.
  ctx.save();
  ctx.rotate(vinkelSekunder);
  // Tegn den tynde, lange sekundviser.
  ctx.beginPath();
  ctx.moveTo(0,0);
  ctx.lineTo(0,-radius*0.9);
  ctx.lineWidth = 2;
  ctx.strokeStyle = "red";
  ctx.stroke();
  // Gendan standardtilstanden, så uret kan afsluttes korrekt.
  ctx.restore();

  // Flyt koordinatsystemet tilbage til udgangspunktet efter tegningen.
  ctx.translate(-cx,-cy); 
```
