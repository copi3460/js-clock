## 🎨 Sektion 1: Fundamentet, Cirkler & Trigonometri

## 1. CodePen & Det Basale Setup

For at tegne skal vi bruge tre ting i vores HTML: en container (papiret), et script (hånden der tegner) og en `context`(penslen).

I CodePen opretter du bare en profil og smider følgende i henholdsvis HTML og JS vinduerne:

HTML:

```html
<!-- Canvas er vores hvide papir. standard 0,0 er øverst til venstre -->
<canvas id="urCanvas" width="400" height="400" style="background:#eee;"></canvas>
<script src="script.js"></script>
```

JavaScript (JS):

```javascript
// 1. Hent canvas-elementet (papiret)
const canvas = document.getElementById("urCanvas");

// 2. Hent 'context' (Vores pensel/værktøjskasse til at tegne 2D)
const ctx = canvas.getContext("2d");

// 3. Find midtpunktet (cx, cy) og urets radius
const cx = canvas.width / 2;  // 200
const cy = canvas.height / 2; // 200
const radius = 150;           // Urets størrelse ud fra midten
```

---

## 2. Grader vs. Radianer

Computeren forstår ikke 360º. Den bruger radianer.

- En hel cirkel 360º er lig med 2*pi (ca. `6.28`).
- En halv cirkel 180º er lig med pi (ca. `3.14`).

Formlen for at lave grader om til radianer:  
$$\text{radianer} = \text{grader} \cdot \left(\frac{\pi}{180}\right)$$ 

---

## 3. Tegn en cirkel med `arc` (Urskiven)

Inden vi tegner med matematik, har canvas en indbygget funktion til cirkler: `ctx.arc(x, y, radius, startVinkel, slutVinkel)`.

Her introducerer vi også Styles (farve og stregtykkelse):

```javascript
ctx.fillStyle="pink";         // Baggrundsfarve inden i cirklen
ctx.beginPath();              // Start en ny tegning (løft pen)

// Tegn cirkel: arc(center-x, center-y, radius, start-radian, slut-radian)
ctx.arc(200,200,20,0,2*Math.PI);
ctx.fill();                   // fyld cirklen med farve

ctx.strokeStyle = "#333";      // Stregfarve (kant)
ctx.lineWidth=10;              // Stregtykkelse i pixels
ctx.beginPath();               // Start en ny tegning (løft pen)

// Tegn cirkel: arc(center-x, center-y, radius, start-radian, slut-radian)
ctx.arc(200,200,190,0,2*Math.PI);
ctx.stroke();                  // Træk en linje rund om cirklen
```

---


## rotate

Når vi bruger `rotate()`, slipper vi for svær trigonometri. I stedet for at flytte _pennen_ rundt i en cirkel, lader vi pennen stå stille og drejer hele papiret (koordinatsystemet) som et rat, mens vi stempler tallene eller prikkerne ind.

Her er de vigtige detaljer, du skal kende, før vi ændrer koden:

1. `ctx.translate(cx, cy)`: Canvas roterer altid omkring sit `(0,0)` punkt (øverst til venstre som standard). Derfor _skal_ vi flytte `(0,0)` ind til urets centrum `(cx, cy)` først. Nu roterer papiret perfekt omkring midten.
2. Husk at nulstille rotationen: Hvis vi roterer papiret for at tegne prik 1, vil næste rotation lægges _ovenpå_. For at undgå, at papiret spinder fuldstændig ud af kontrol, nulstiller vi enten rotationen efter hver prik, eller også roterer vi bare en lille smule ad gangen i loopet.

Her er den opdaterede JavaScript-kode til din CodePen, hvor alt er lavet om til at bruge `ctx.translate()` og `ctx.rotate()`:

```javascript
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

// Nu laver vi en løkke (en "for-loop"), der gør det samme 12 gange – én gang for hver time.
for (let i = 0; i < 12; i++) { 
  
  // Gør klar med tegneredskaberne! Nu skal vi i gang med en ny prik.
  ctx.beginPath(); 
  
  // Her tegner vi selve prikken (en lille cirkel). 
  // Fordi vi bruger et minus-tal (-170), hopper vi direkte OP i toppen af uret. 
  // Så den allerførste prik lander helt automatisk på klokken 12!
  ctx.arc(0, -170, 6, 0, 2 * Math.PI); 
  
  // Vi vælger en flot rød farve til prikken.
  ctx.fillStyle = "red";  
  
  // Farv prikken rød!
  ctx.fill();  
  
  // NU SKER DET MAGISKE: I stedet for at regne ud, hvor den næste prik skal være, 
  // så drejer vi bare hele papiret en lille smule (svarende til én time). 
  // Næste gang løkken kører, tegner vi bare "opad" igen, men fordi papiret er drejet, 
  // lander prikken det helt rigtige sted (på klokken 1!).
  ctx.rotate(timeVinkel); 
}
```

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

# Canvas Cheatsheet – Clock project

## 1) Grundlæggende

```js
const ctx = klokken.getContext("2d");
```

- `ctx` = canvas-tegningskontekst
- `getContext("2d")` = gør det muligt at tegne i 2D

---

## 2) Farver

```js
ctx.fillStyle = "pink";
ctx.strokeStyle = "red";
ctx.lineWidth = 3;
```

- `fillStyle` = fyldfarve
- `strokeStyle` = kantfarve
- `lineWidth` = linjetykkelse i px

---

## 3) Former

```js
ctx.beginPath();
ctx.arc(x, y, radius, startAngle, endAngle);
ctx.fill();
ctx.stroke();
```

- `beginPath()` = start på en ny tegning
- `arc(x, y, r, start, end)` = tegn cirkel/bue
- `fill()` = udfyld form
- `stroke()` = tegn kant

Eksempel:
```js
ctx.arc(200, 200, 190, 0, 2 * Math.PI);
```

- hele cirkel = `2 * Math.PI`
- 90° ≈ `Math.PI / 2`

---

## 4) Placering og rotation

```js
const cx = klokken.width / 2;
const cy = klokken.height / 2;
ctx.translate(cx, cy);
ctx.rotate(timeVinkel);
```

- `translate(x, y)` = flyt koordinatsystemet
- `rotate(vinkel)` = drej hele tegningen
- `timeVinkel = (2 * Math.PI) / 12;` = én time i cirklen

---

## 5) Rektangler og tekst

```js
ctx.fillRect(x, y, width, height);
ctx.font = "30px Arial";
ctx.fillText("A", x, y);
```

- `fillRect(x, y, w, h)` = fyldt rektangel
- `font` = tekststil
- `fillText(text, x, y)` = tegn tekst

Eksempel:
```js
ctx.fillRect(-7, -160, 14, 28);
ctx.fillText("A", -6, -120);
```

---

## 6) Uret i praksis

```js
const radius = 200;
const cx = klokken.width / 2;
const cy = klokken.height / 2;
const timeVinkel = (2 * Math.PI) / 12;

ctx.translate(cx, cy);

for (let i = 0; i < 12; i++) {
  ctx.beginPath();
  ctx.fillStyle = "red";
  ctx.fillRect(-7, -160, 14, 28);
  ctx.rotate(timeVinkel);
}
```

Dette gør:
- sætter centrum i midten af canvas
- tegner 12 markeringer rundt om uret
- roterer en lille smule for hver gang

---

## 7) Hurtig reference

- `getContext("2d")` → få adgang til tegning
- `fillStyle` → fyldfarve
- `strokeStyle` → kantfarve
- `lineWidth` → linjetykkelse
- `beginPath()` → ny tegne-rute
- `arc()` → cirkel/bue
- `fill()` → udfyld
- `stroke()` → tegn kant
- `translate()` → flyt koordinatsystem
- `rotate()` → drej koordinatsystem
- `fillRect()` → fyldt rektangel
- `font` → tekststil
- `fillText()` → tekst på canvas

---

## 8) Tip til print

- Brug browserens “Print” eller “Udskriv”
- Sæt skala til 100%
- Vis kun denne side
- Format: A4 / liggende eller stående afhængig af visning

