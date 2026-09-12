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
