

---

## 🎨 Sektion 1: Fundamentet, Cirkler & Trigonometri

## 1. CodePen & Det Basale Setup

For at tegne skal vi bruge tre ting i vores HTML: en container (papiret), et script (hånden der tegner) og en `context`(penslen).

I CodePen opretter du bare en profil og smider følgende i henholdsvis HTML og JS vinduerne:

HTML:

```html
<!-- Canvas er vores hvide papir. standard 0,0 er øverst til venstre -->
<canvas id="urCanvas" width="400" height="400" style="background:#eee;"></canvas>
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

Computeren forstår ikke $360^\circ$. Den bruger radianer.

- En hel cirkel ($360^\circ$) er lig med $2 \cdot \pi$ (ca. `6.28`).
- En halv cirkel ($180^\circ$) er lig med $\pi$ (ca. `3.14`).

Formlen for at lave grader om til radianer:  
$$\text{radianer} = \text{grader} \cdot \left(\frac{\pi}{180}\right)$$ 

---

## 3. Tegn en cirkel med `arc` (Urskiven)

Inden vi tegner med matematik, har canvas en indbygget funktion til cirkler: `ctx.arc(x, y, radius, startVinkel, slutVinkel)`.

Her introducerer vi også Styles (farve og stregtykkelse):

```javascript
ctx.beginPath(); // Start en ny tegning (løft pen)

// Tegn cirkel: arc(center-x, center-y, radius, start-radian, slut-radian)
ctx.arc(cx, cy, radius, 0, 2 * Math.PI); 

// Styles (Vores indstillinger)
ctx.fillStyle = "white";       // Baggrundsfarve inden i cirklen
ctx.strokeStyle = "#333";      // Stregfarve (kant)
ctx.lineWidth = 10;            // Stregtykkelse i pixels

ctx.fill();   // Fyld cirklen ud med hvid
ctx.stroke(); // Tegn selve kanten op
```

---

## 4. Trigonometri: Tegn en cirkel ved hjælp af mindre cirkler (Prikker på urskiven)

Hvis vi vil placere 12 prikker (eller timer) rundt på urskiven, skal vi bruge Sinus (`Math.sin`) og Cosinus (`Math.cos`).

- Cosinus finder $X$-koordinatet ud fra en vinkel.
- Sinus finder $Y$-koordinatet ud fra en vinkel.

Her er princippet, hvor vi looper igennem 12 punkter:

```javascript
// En urskive er opdelt i 12 timer. En hel cirkel er 2 * Math.PI.
// Hver time fylder derfor (2 * Math.PI) / 12 radianer.

for (let i = 0; i < 12; i++) {
    let vinkel = i * ((2 * Math.PI) / 12); // Vinklen for denne specifikke time
    
    // Beregn den præcise (x, y) position på urskiven vha. trigonometri
    let x = cx + radius * Math.cos(vinkel);
    let y = cy + radius * Math.sin(vinkel);
    
    // Tegn en lille prik/cirkel på hver position
    ctx.beginPath();
    ctx.arc(x, y, 5, 0, 2 * Math.PI);
    ctx.fillStyle = "red";
    ctx.fill();
}
```

---

## ⏱️ Sektion 2: Tid, Animation & Visere

## 1. Animation med `setInterval` og `Date()`

For at uret tikker, skal vi køre en funktion hvert sekund (1000 millisekunder). `new Date()` henter computerens ur lige nu.

```javascript
// Kør funktionen 'tick' hvert 1000. millisekund
setInterval(tick, 1000);

function tick() {
    // 1. Rens canvasen, så gamle visere slettes
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    // Redraw urskiven her (koden fra Sektion 1)
    
    // 2. Hent den aktuelle tid
    const nu = new Date();
    let sekunder = nu.getSeconds();
    let minutter = nu.getMinutes();
    let timer = nu.getHours();
    let millisekunder = nu.getMilliseconds();
    
    // Nu kan vi bruge disse tal til at styre viserne!
}
```

---

## 2. Opdeling af urskiven: Hvor mange radianer er et sekund?

Der er 60 sekunder på et minut.

- En hel omgang er $2\pi$.
- Ét sekund (eller ét minut) svarer til: $(2 \cdot \pi) / 60$ radianer.

_Vigtigt:_ I matematik starter $0$ radianer til højre (kl. 3). For at uret starter i toppen (kl. 12), trækker vi altid en kvart omgang fra vinklen: `-(Math.PI / 2)`.

---

## 3. Tegne en viser med `moveTo` og `lineTo`

Når vi tegner en viser (en lige linje), bruger vi `moveTo(fraX, fraY)` til at sætte pennen på midten `(cx, cy)`, og `lineTo(tilX, tilY)`til at trække stregen ud til urets kant baseret på vinklen ($v$).

Her er eksemplet for sekundviseren:

```javascript
// Beregn vinklen for sekunderne (og juster så 0 er klokken 12)
let v = sekunder * ((2 * Math.PI) / 60) - (Math.PI / 2);

// Hvor lang skal sekundviseren være?
let laengde = radius * 0.85; 

// Find slutpunktet for viseren med cos og sin
let x = cx + laengde * Math.cos(v);
let y = cy + laengde * Math.sin(v);

// Tegn viseren
ctx.beginPath();
ctx.moveTo(cx, cy); // Start i centrum
ctx.lineTo(x, y);   // Gå ud til spidsen af viseren
ctx.strokeStyle = "red";
ctx.lineWidth = 2;   // Tynd viser til sekunder
ctx.stroke();
```

_For Minutter og Timer gør du nøjagtig det samme, men ændrer `laengde` (f.eks. `radius * 0.7` for minutter) og `lineWidth`(gør dem tykkere)._  
_For at få Timer + Minutter til at glide glat, lader du timens vinkel påvirkes en lille smule af, hvor mange minutter der er gået._

---

## 4. Tekst på canvas: fillText() og Romertal

Vi kan udskrive tekst som f.eks. tal direkte på canvas. Vi kan bruge et Array (en liste) til at holde Romertallene.

```javascript
ctx.font = "20px Georgia"; // Sæt skrifttype og størrelse
ctx.fillStyle = "#333";
ctx.textAlign = "center";
ctx.textBaseline = "middle";

const romertal = ["XII", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX", "X", "XI"];

for (let i = 0; i < 12; i++) {
    // i = 0 svarer til XII (12), i = 1 svarer til I (1) osv.
    // Vi trækker igen Math.PI/2 fra for at starte kl 12 i toppen
    let vinkel = i * ((2 * Math.PI) / 12) - (Math.PI / 2);
    
    // Placer tallene en lille smule inden for kanten (radius * 0.75)
    let x = cx + (radius * 0.75) * Math.cos(vinkel);
    let y = cy + (radius * 0.75) * Math.sin(vinkel);
    
    // Skriv romertallet på koordinatsættet
    ctx.fillText(romertal[i], x, y);
}
```

---

Lad os skille koden ad og kigge på de tre vigtigste detaljer, der manglede en dyb forklaring:

---

## Detalje 1: Hvorfor lige `Math.cos()` til X og `Math.sin()` til Y?

Computeren tegner på et fladt koordinatsystem (et grid). Når vi vil tegne i en cirkel, kender vi en vinkel og en afstand (radius), men canvas skal bruge et præcist `(x, y)` punkt.

- Cosinus (`Math.cos`) måler den vandrette afstand. Den fortæller os, hvor langt vi skal gå til højre eller venstre.
- Sinus (`Math.sin`) måler den lodrette afstand. Den fortæller os, hvor langt vi skal gå op eller ned.

Når vi skriver `radius * Math.cos(vinkel)`, omdanner vi vinklen til et præcist antal pixels på skærmen. Vi lægger `cx` og `cy` til, fordi vi skal starte målingen fra urets centrum (200, 200) i stedet for oppe i hjørnet (0, 0).

---

## Detalje 2: Hvorfor starter computeren "Klokken 3"?

I matematikken og på computeren starter $0$ radianer altid vandret til højre (svarende til klokken 3 på et ur). Vinklen bevæger sig _med uret_ derfra.

Hvis vi lader loopet køre uden justering:

- `i = 0` (første prik) lander klokken 3.
- `i = 3` lander klokken 6 (lige i bunden).
- `i = 6` lander klokken 9.
- `i = 9` lander klokken 12 (lige i toppen).

For at rette op på dette, så `i = 0` rent faktisk bliver til klokken 12 i toppen, skal vi dreje vores vinkel en kvart omgang _mod uret_. En kvart omgang i radianer svarer til $\frac{\pi}{2}$ (eller $90^\circ$). Derfor trækker vi altid `Math.PI / 2` fra vinklen.

---

## Detalje 3: Hvad gør `ctx.beginPath()` og `ctx.fill()` egentlig bag kulisserne?

Hvis du ikke lukker og åbner dine stier korrekt, tror canvas, at alt hvad du tegner, hænger sammen i én lang, usynlig streg.

1. `ctx.beginPath()`: Dette nulstiller "hukommelsen" på penslen. Det fortæller canvas: _"Glem alt om den store urskive, jeg tegnede lige før. Nu starter en helt ny, uafhængig form (en lille prik)."_ Hvis du glemmer denne i loopet, vil canvas forsøge at binde alle de 12 prikker sammen med usynlige streger.
2. Stien vs. Malingen: Når loopet kører `ctx.arc(...)`, tegner det kun en usynlig matematisk cirkel i luften. Der kommer intet på skærmen, før du kalder `ctx.fill()` eller `ctx.stroke()`, som fysisk hælder maling ud på det koordinatsæt, du lige har udregnet.

---

## Opdateret JavaScript til din CodePen (Med rettet "Klokken 12" vinkel)

Her er JavaScript-delen, hvor vinklen nu er korrigeret, så den første prik (`i = 0`) lander præcis i toppen af uret:

```javascript
const canvas = document.getElementById("urCanvas");
const ctx = canvas.getContext("2d");

const cx = canvas.width / 2;  
const cy = canvas.height / 2; 
const radius = 160;           

// 1. TEGN URKSIVEN
ctx.beginPath(); 
ctx.arc(cx, cy, radius, 0, 2 * Math.PI); 
ctx.fillStyle = "#ffffff";     
ctx.strokeStyle = "#34495e";   
ctx.lineWidth = 12;            
ctx.fill();   
ctx.stroke(); 

// 2. TEGN TIMEMARKERINGER (Nu justeret så vi starter i toppen!)
for (let i = 0; i < 12; i++) {
    // Detalje: Vi trækker (Math.PI / 2) fra for at flytte startpunktet fra kl. 3 op til kl. 12
    let vinkel = i * ((2 * Math.PI) / 12) - (Math.PI / 2); 
    
    let x = cx + (radius * 0.85) * Math.cos(vinkel);
    let y = cy + (radius * 0.85) * Math.sin(vinkel);
    
    ctx.beginPath(); // Vigtigt: Nulstil stien for hver eneste prik
    ctx.arc(x, y, 6, 0, 2 * Math.PI);
    ctx.fillStyle = "#e74c3c"; 
    ctx.fill(); // Mal prikken
}

// 3. CENTER-PIN
ctx.beginPath();
ctx.arc(cx, cy, 8, 0, 2 * Math.PI);
ctx.fillStyle = "#34495e";
ctx.fill();
```

Giver logikken bag Sinus/Cosinus og den kvarte omgangs rotation (`- Math.PI / 2`) bedre mening nu?

Når du har testet denne rettede urskive af i CodePen, kan vi dykke ned i de specifikke detaljer omkring viserne og tids-loopet i Sektion 2.

---

## rotate i stedet for sin og cos
At bruge `ctx.rotate()` i stedet for manuelt at udregne koordinater med `sin` og `cos` er en ekstremt elegant løsning.

Når vi bruger `rotate()`, slipper vi for svær trigonometri. I stedet for at flytte _pennen_ rundt i en cirkel, lader vi pennen stå stille og drejer hele papiret (koordinatsystemet) som et rat, mens vi stempler tallene eller prikkerne ind.

Her er de vigtige detaljer, du skal kende, før vi ændrer koden:

1. `ctx.translate(cx, cy)`: Canvas roterer altid omkring sit `(0,0)` punkt (øverst til venstre som standard). Derfor _skal_ vi flytte `(0,0)` ind til urets centrum `(cx, cy)` først. Nu roterer papiret perfekt omkring midten.
2. Husk at nulstille rotationen: Hvis vi roterer papiret for at tegne prik 1, vil næste rotation lægges _ovenpå_. For at undgå, at papiret spinder fuldstændig ud af kontrol, nulstiller vi enten rotationen efter hver prik, eller også roterer vi bare en lille smule ad gangen i loopet.

Her er den opdaterede JavaScript-kode til din CodePen, hvor alt er lavet om til at bruge `ctx.translate()` og `ctx.rotate()`:

```javascript
const canvas = document.getElementById("urCanvas");
const ctx = canvas.getContext("2d");

const cx = canvas.width / 2;  // 200
const cy = canvas.height / 2; // 200
const radius = 160;           

// === 1. FLYT ORIGIN (0,0) TIL CENTRUM ===
// Dette er den vigtigste ændring. Nu er (0,0) lige midt i uret!
ctx.translate(cx, cy);

// === 2. TEGN BAGGRUNDEN (Nu ud fra 0,0) ===
ctx.beginPath(); 
// Da (0,0) er centrum, tegner vi cirklen i 0, 0
ctx.arc(0, 0, radius, 0, 2 * Math.PI); 
ctx.fillStyle = "#ffffff";     
ctx.strokeStyle = "#34495e";   
ctx.lineWidth = 12;            
ctx.fill();   
ctx.stroke(); 

// === 3. TEGN TIMEMARKERINGER MED ROTATE ===
// Der er 12 timer på et ur. En hel omgang er 2 * Math.PI.
// Vinklen mellem hver time er derfor: (2 * Math.PI) / 12
const timeVinkel = (2 * Math.PI) / 12;

for (let i = 0; i < 12; i++) {
    // Vi tegner prikken direkte på Y-aksen (0, -afstand).
    // Fordi Y er negativ, går vi lodret OP. Det betyder, at vi starter KLOKKEN 12 automatisk!
    ctx.beginPath();
    // Vi går 0 ud af X-aksen, og -135 op ad Y-aksen (radius * 0.85)
    ctx.arc(0, -radius * 0.85, 6, 0, 2 * Math.PI);
    ctx.fillStyle = "#e74c3c"; 
    ctx.fill();
    
    // EFTER vi har tegnet prikken, roterer vi hele canvas-papiret
    // svarende til én times vinkel, så den næste prik bliver tegnet det rigtige sted.
    ctx.rotate(timeVinkel);
}

// === 4. TEGN CENTER-PIN (Lige i 0,0) ===
ctx.beginPath();
ctx.arc(0, 0, 8, 0, 2 * Math.PI);
ctx.fillStyle = "#34495e";
ctx.fill();
```

## Hvorfor denne kode er smartere og løser de gamle problemer:

- Ingen `sin` og `cos` overhovedet: Canvas klarer alt det svære grafik-arbejde i baggrunden.
- Slut med "Klokken 3" problemet: Da vi blot tegner vores prik i `(0, -afstand)`, tegner vi den direkte i toppen af aksen. Vi behøver ikke længere at trække `Math.PI / 2` fra for at tvinge uret op i toppen.
- Loopet roterer organisk: For hver omgang i loopet tegner vi en prik og drejer papiret 1/12 omgang. Når loopet er færdigt, har canvas drejet sig præcis en hel omgang ($360^\circ$) og står perfekt klar til næste opgave.

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


## Eksempel 1: Romertal fra et Array

Her opretter vi et Array (en liste) med Romertallene. Da Arrays i JavaScript starter ved indeks `0`, lader vi loopet tælle fra `0` til `11`. For at "0" lander klokken 12, trækker vi `XII` ud som det første element i vores liste.

Erstat JavaScript-delen i din CodePen med dette:

```javascript
const canvas = document.getElementById("urCanvas");
const ctx = canvas.getContext("2d");

const cx = canvas.width / 2;  
const cy = canvas.height / 2; 
const radius = 160;           

ctx.translate(cx, cy); 

// --- 1. TEGN BAGGRUND ---
ctx.beginPath(); 
ctx.arc(0, 0, radius, 0, 2 * Math.PI); 
ctx.fillStyle = "#ffffff";     
ctx.strokeStyle = "#2c3e50";   
ctx.lineWidth = 12;            
ctx.fill();   
ctx.stroke(); 

// --- 2. INDSTIL TEKST-STYLES ---
ctx.font = "20px 'Georgia', serif"; // Romertal ser flotte ud med en 'serif' font
ctx.fillStyle = "#2c3e50";     
ctx.textAlign = "center";      
ctx.textBaseline = "middle";   

// --- 3. ARRAY MED ROMERTAL ---
// Indeks 0 = XII (Kl 12), Indeks 1 = I (Kl 1) osv.
const romertal = ["XII", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX", "X", "XI"];

// --- 4. LOOP IGENNEM ARRAY ---
for (let i = 0; i < 12; i++) {
    // Beregn vinklen baseret på indeks (i)
    let vinkel = i * ((2 * Math.PI) / 12);
    
    ctx.rotate(vinkel);             // 1. Drej papiret
    ctx.translate(0, -radius * 0.73); // 2. Gå op mod kanten (lidt tættere på end før, da romertal fylder mere)
    
    ctx.rotate(-vinkel);            // 3. RET DETALJE: Drej papiret tilbage så teksten står lige [1]
    ctx.fillText(romertal[i], 0, 0); // 4. Hent teksten fra vores array og skriv den
    
    // 5. Nulstil positioner
    ctx.rotate(vinkel);             
    ctx.translate(0, radius * 0.73); 
    ctx.rotate(-vinkel);            
}

// --- 5. CENTER-PIN ---
ctx.beginPath();
ctx.arc(0, 0, 8, 0, 2 * Math.PI);
ctx.fillStyle = "#2c3e50";
ctx.fill();
```

## De vigtige detaljer i denne teknik:

- Hvorfor gå frem og tilbage? Hver gang vi kalder `ctx.translate(0, -radius * 0.75)`, flytter vi det midlertidige `(0,0)` punkt derop, hvor tallet skal stå. For at det næste tal beregnes rigtigt fra midten af uret, er vi nødt til at gå den stik modsat vej tilbage bagefter: `ctx.translate(0, radius * 0.75)`.
- Uden `ctx.rotate(-vinkel)`: Hvis du prøver at fjerne den linje (og dens modpart i bunden), vil du se, at 6-tallet (eller VI) står helt på hovedet i bunden af uret, og 3-tallet ligger ned. Ved at mod-rotere lige inden vi skriver, sikrer vi, at alle tal står pænt og opret som på et rigtigt ur.

---



For at uret tikker, skal vi køre en funktion hvert sekund (1000 millisekunder). `new Date()` henter computerens ur lige nu.

```javascript
// Kør funktionen 'tick' hvert 1000. millisekund
setInterval(tick, 1000);

function tick() {
    // 1. Rens canvasen, så gamle visere slettes
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    // Redraw urskiven her (koden fra Sektion 1)
    
    // 2. Hent den aktuelle tid
    const nu = new Date();
    let sekunder = nu.getSeconds();
    let minutter = nu.getMinutes();
    let timer = nu.getHours();
    let millisekunder = nu.getMilliseconds();
    
    // Nu kan vi bruge disse tal til at styre viserne!
}
```

---

## 2. Opdeling af urskiven: Hvor mange radianer er et sekund?

Der er 60 sekunder på et minut.

- En hel omgang er $2\pi$.
- Ét sekund (eller ét minut) svarer til: $(2 \cdot \pi) / 60$ radianer.

_Vigtigt:_ I matematik starter $0$ radianer til højre (kl. 3). For at uret starter i toppen (kl. 12), trækker vi altid en kvart omgang fra vinklen: `-(Math.PI / 2)`.

---

## 3. Tegne en viser med `moveTo` og `lineTo`

Når vi tegner en viser (en lige linje), bruger vi `moveTo(fraX, fraY)` til at sætte pennen på midten `(cx, cy)`, og `lineTo(tilX, tilY)`til at trække stregen ud til urets kant baseret på vinklen ($v$).

Her er eksemplet for sekundviseren:

```javascript
// Beregn vinklen for sekunderne (og juster så 0 er klokken 12)
let v = sekunder * ((2 * Math.PI) / 60) - (Math.PI / 2);

// Hvor lang skal sekundviseren være?
let laengde = radius * 0.85; 

// Find slutpunktet for viseren med cos og sin
let x = cx + laengde * Math.cos(v);
let y = cy + laengde * Math.sin(v);

// Tegn viseren
ctx.beginPath();
ctx.moveTo(cx, cy); // Start i centrum
ctx.lineTo(x, y);   // Gå ud til spidsen af viseren
ctx.strokeStyle = "red";
ctx.lineWidth = 2;   // Tynd viser til sekunder
ctx.stroke();
```

_For Minutter og Timer gør du nøjagtig det samme, men ændrer `laengde` (f.eks. `radius * 0.7` for minutter) og `lineWidth`(gør dem tykkere)._  
_For at få Timer + Minutter til at glide glat, lader du timens vinkel påvirkes en lille smule af, hvor mange minutter der er gået._

---

## 4. Tekst på canvas: fillText() og Romertal

Vi kan udskrive tekst som f.eks. tal direkte på canvas. Vi kan bruge et Array (en liste) til at holde Romertallene.

```javascript
ctx.font = "20px Georgia"; // Sæt skrifttype og størrelse
ctx.fillStyle = "#333";
ctx.textAlign = "center";
ctx.textBaseline = "middle";

const romertal = ["XII", "I", "II", "III", "IV", "V", "VI", "VII", "VIII", "IX", "X", "XI"];

for (let i = 0; i < 12; i++) {
    // i = 0 svarer til XII (12), i = 1 svarer til I (1) osv.
    // Vi trækker igen Math.PI/2 fra for at starte kl 12 i toppen
    let vinkel = i * ((2 * Math.PI) / 12) - (Math.PI / 2);
    
    // Placer tallene en lille smule inden for kanten (radius * 0.75)
    let x = cx + (radius * 0.75) * Math.cos(vinkel);
    let y = cy + (radius * 0.75) * Math.sin(vinkel);
    
    // Skriv romertallet på koordinatsættet
    ctx.fillText(romertal[i], x, y);
}
```

---

Lad os skille koden ad og kigge på de tre vigtigste detaljer, der manglede en dyb forklaring:

---

## Detalje 1: Hvorfor lige `Math.cos()` til X og `Math.sin()` til Y?

Computeren tegner på et fladt koordinatsystem (et grid). Når vi vil tegne i en cirkel, kender vi en vinkel og en afstand (radius), men canvas skal bruge et præcist `(x, y)` punkt.

- Cosinus (`Math.cos`) måler den vandrette afstand. Den fortæller os, hvor langt vi skal gå til højre eller venstre.
- Sinus (`Math.sin`) måler den lodrette afstand. Den fortæller os, hvor langt vi skal gå op eller ned.

Når vi skriver `radius * Math.cos(vinkel)`, omdanner vi vinklen til et præcist antal pixels på skærmen. Vi lægger `cx` og `cy` til, fordi vi skal starte målingen fra urets centrum (200, 200) i stedet for oppe i hjørnet (0, 0).

---

## Detalje 2: Hvorfor starter computeren "Klokken 3"?

I matematikken og på computeren starter $0$ radianer altid vandret til højre (svarende til klokken 3 på et ur). Vinklen bevæger sig _med uret_ derfra.

Hvis vi lader loopet køre uden justering:

- `i = 0` (første prik) lander klokken 3.
- `i = 3` lander klokken 6 (lige i bunden).
- `i = 6` lander klokken 9.
- `i = 9` lander klokken 12 (lige i toppen).

For at rette op på dette, så `i = 0` rent faktisk bliver til klokken 12 i toppen, skal vi dreje vores vinkel en kvart omgang _mod uret_. En kvart omgang i radianer svarer til $\frac{\pi}{2}$ (eller $90^\circ$). Derfor trækker vi altid `Math.PI / 2` fra vinklen.

---

## Detalje 3: Hvad gør `ctx.beginPath()` og `ctx.fill()` egentlig bag kulisserne?

Hvis du ikke lukker og åbner dine stier korrekt, tror canvas, at alt hvad du tegner, hænger sammen i én lang, usynlig streg.

1. `ctx.beginPath()`: Dette nulstiller "hukommelsen" på penslen. Det fortæller canvas: _"Glem alt om den store urskive, jeg tegnede lige før. Nu starter en helt ny, uafhængig form (en lille prik)."_ Hvis du glemmer denne i loopet, vil canvas forsøge at binde alle de 12 prikker sammen med usynlige streger.
2. Stien vs. Malingen: Når loopet kører `ctx.arc(...)`, tegner det kun en usynlig matematisk cirkel i luften. Der kommer intet på skærmen, før du kalder `ctx.fill()` eller `ctx.stroke()`, som fysisk hælder maling ud på det koordinatsæt, du lige har udregnet.

---

## Opdateret JavaScript til din CodePen (Med rettet "Klokken 12" vinkel)

Her er JavaScript-delen, hvor vinklen nu er korrigeret, så den første prik (`i = 0`) lander præcis i toppen af uret:

```javascript
const canvas = document.getElementById("urCanvas");
const ctx = canvas.getContext("2d");

const cx = canvas.width / 2;  
const cy = canvas.height / 2; 
const radius = 160;           

// 1. TEGN URKSIVEN
ctx.beginPath(); 
ctx.arc(cx, cy, radius, 0, 2 * Math.PI); 
ctx.fillStyle = "#ffffff";     
ctx.strokeStyle = "#34495e";   
ctx.lineWidth = 12;            
ctx.fill();   
ctx.stroke(); 

// 2. TEGN TIMEMARKERINGER (Nu justeret så vi starter i toppen!)
for (let i = 0; i < 12; i++) {
    // Detalje: Vi trækker (Math.PI / 2) fra for at flytte startpunktet fra kl. 3 op til kl. 12
    let vinkel = i * ((2 * Math.PI) / 12) - (Math.PI / 2); 
    
    let x = cx + (radius * 0.85) * Math.cos(vinkel);
    let y = cy + (radius * 0.85) * Math.sin(vinkel);
    
    ctx.beginPath(); // Vigtigt: Nulstil stien for hver eneste prik
    ctx.arc(x, y, 6, 0, 2 * Math.PI);
    ctx.fillStyle = "#e74c3c"; 
    ctx.fill(); // Mal prikken
}

// 3. CENTER-PIN
ctx.beginPath();
ctx.arc(cx, cy, 8, 0, 2 * Math.PI);
ctx.fillStyle = "#34495e";
ctx.fill();
```

Giver logikken bag Sinus/Cosinus og den kvarte omgangs rotation (`- Math.PI / 2`) bedre mening nu?

Når du har testet denne rettede urskive af i CodePen, kan vi dykke ned i de specifikke detaljer omkring viserne og tids-loopet i Sektion 2.
