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
