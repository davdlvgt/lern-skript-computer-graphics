---
layout: default
title: Transformations
nav_order: 2
parent: Home
---

# 🎓 Interaktives Lernskript: Transformations (Computer Graphics)

Dieses Dokument führt dich durch die Grundlagen der geometrischen Transformationen, Vektoren und Matrizen.

---

## Einheit 1: Koordinatensysteme & Vektoren
*(Basierend auf Folien 1-23)*

### Die Theorie
In der Computergrafik arbeiten wir ständig mit verschiedenen Koordinatensystemen. Das wichtigste Unterscheidungsmerkmal ist die "Händigkeit":

* **Rechtshändiges System (RHS):** Daumen = X, Zeigefinger = Y, Mittelfinger = Z (zeigt auf dich zu). Standard in der Mathematik und OpenGL.
* **Linkshändiges System (LHS):** Z zeigt in den Bildschirm hinein (DirectX, Unity).

Vektoren sind das Herzstück. Wichtige Operationen sind:

1.  **Skalarprodukt (Dot Product):** Ergebnis ist eine Zahl (Skalar). Es sagt uns, wie sehr zwei Vektoren in die gleiche Richtung zeigen ($\cos \theta$). Ist es 0, stehen sie senkrecht aufeinander.
2.  **Kreuzprodukt (Cross Product):** Ergebnis ist ein neuer Vektor, der senkrecht auf den beiden anderen steht. Wichtig für die Berechnung von Normalen (Flächenausrichtung).

### 🧠 Dein Selbsttest

**Frage 1:** Du hast zwei Vektoren, die die Kanten eines Dreiecks im 3D-Raum darstellen. Du möchtest wissen, in welche Richtung die Fläche des Dreiecks zeigt (die "Normale"). Welche Operation verwendest du?

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Das Kreuzprodukt (Cross Product).**
Es erzeugt einen Vektor, der senkrecht auf der Ebene steht, die von den beiden Kantenvektoren aufgespannt wird.
</details>

<br>

**Frage 2:** Warum ist die Unterscheidung zwischen links- und rechtshändigen Koordinatensystemen wichtig, wenn man 3D-Modelle importiert?

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Weil das Modell sonst gespiegelt sein könnte oder in die falsche Richtung schaut.**
Wenn eine Software Z als "Tiefe in den Bildschirm" (LHS) interpretiert, die Daten aber als "Tiefe aus dem Bildschirm" (RHS) gespeichert wurden, ist die Z-Achse invertiert.
</details>

---

## Einheit 2: Matrizen & Homogene Koordinaten
*(Basierend auf Folien 25-36)*

### Die Theorie
Um Bewegungen (Translation), Drehungen (Rotation) und Skalierungen effizient zu berechnen, nutzen wir Matrizen.
Das Problem: Eine $3 \times 3$ Matrix kann rotieren und skalieren, aber **nicht verschieben** (Translation).

Die Lösung: **Homogene Koordinaten**. Wir fügen eine 4. Dimension hinzu ($w$). Ein 3D-Vektor $(x, y, z)$ wird zu $(x, y, z, 1)$. Dadurch können wir $4 \times 4$ Matrizen verwenden, die auch Translationen durch Multiplikation abbilden können.

Die Reihenfolge der Multiplikation ist entscheidend! $A \cdot B$ ist nicht dasselbe wie $B \cdot A$.

### 🧠 Dein Selbsttest

**Frage 3:** Warum verwenden wir in der Computergrafik oft $4 \times 4$ Matrizen für 3D-Vektoren statt $3 \times 3$?

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Um Translationen (Verschiebungen) als Matrixmultiplikation darstellen zu können.**
Mit einer $3 \times 3$ Matrix sind nur lineare Transformationen (Skalierung, Rotation) möglich, aber keine affinen Verschiebungen. Durch die 4. Komponente wird dies möglich.
</details>

<br>

**Frage 4:** Was bedeutet es mathematisch, wenn wir sagen "Matrix-Multiplikation ist nicht kommutativ"? Was heißt das für die Anwendung (z.B. erst Rotieren, dann Verschieben vs. erst Verschieben, dann Rotieren)?

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Die Reihenfolge ändert das Ergebnis.**
Wenn du dich erst drehst und dann einen Schritt nach vorne machst, landest du woanders, als wenn du erst einen Schritt nach vorne machst und dich dann drehst. In der Grafikpipeline muss man daher strikt auf die Reihenfolge der Matrizen achten.
</details>

---

## Einheit 3: Transformationen & Quaternions
*(Basierend auf Folien 37-60)*

### Die Theorie
* **Translation, Skalierung, Rotation:** Die Standard-Operationen.
* **Gimbal Lock:** Bei der Verwendung von Euler-Winkeln (Rotation um X, dann Y, dann Z) kann es passieren, dass zwei Rotationsachsen übereinander liegen. Man verliert einen Freiheitsgrad – das Objekt kann sich in eine bestimmte Richtung nicht mehr drehen.
* **Quaternions:** Eine Methode mit 4 Werten $(w, x, y, z)$, um Rotationen zu speichern. Sie vermeiden den Gimbal Lock und ermöglichen eine viel glattere Interpolation (weiche Kamerafahrten) zwischen zwei Orientierungen.

### 🧠 Dein Selbsttest

**Frage 5:** Was ist der "Gimbal Lock" (kardinale Blockade) und bei welcher Rotationsmethode tritt er auf?

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Er tritt bei Euler-Winkeln auf.**
Er beschreibt den Zustand, wenn zwei der drei Rotationsachsen parallel zueinander ausgerichtet werden, wodurch das System einen Freiheitsgrad verliert. Eine Rotation um eine der Achsen hat dann keinen Effekt mehr bzw. den gleichen wie die andere.
</details>

<br>

**Frage 6:** Nenne einen großen Vorteil von Quaternions gegenüber Rotationsmatrizen oder Euler-Winkeln, wenn es um Animationen geht.

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Die glatte Interpolation (SLERP).**
Es ist mathematisch einfacher und stabiler, den "kürzesten Weg" zwischen zwei Rotationen mittels Quaternions zu berechnen, als zwischen zwei Matrizen oder Euler-Winkeln zu interpolieren.
</details>

---

## Einheit 4: Projektionen
*(Basierend auf Folien 62-77)*

### Die Theorie
Wie kommt die 3D-Welt auf den 2D-Bildschirm? Durch Projektion.

1.  **Orthographische Projektion:** Parallelprojektion. Linien bleiben parallel. Größe ändert sich nicht mit der Distanz. (Gut für technische Zeichnungen oder 2D-Spiele).
2.  **Perspektivische Projektion:** Entspricht dem menschlichen Sehen. Dinge, die weiter weg sind, erscheinen kleiner. Parallele Linien treffen sich am Fluchtpunkt. Dies geschieht durch die **Perspective Division** (Teilen durch die Tiefe $z$ bzw. $w$).

### 🧠 Dein Selbsttest

**Frage 7:** Du renderst ein Haus. In der einen Ansicht sind die Wände hinten genauso hoch wie vorne. In der anderen Ansicht werden die Wände nach hinten kleiner. Welche Projektionsart ist welche?

<details markdown="1">
<summary>Lösung anzeigen</summary>

* **Gleich hoch:** Orthographische (Parallele) Projektion.
* **Nach hinten kleiner:** Perspektivische Projektion.
</details>

<br>

**Frage 8:** Was ist das "Viewing Frustum" (Sichtpyramide)?

<details markdown="1">
<summary>Lösung anzeigen</summary>

Das ist der Bereich im 3D-Raum, den die Kamera "sieht". Alles außerhalb dieses Volumens wird abgeschnitten (Clipping) und nicht berechnet. Bei einer perspektivischen Projektion hat es die Form einer Pyramidestumpfes (Frustum), bei orthographischer Projektion ist es ein Quader.
</details>

---

## Einheit 5: Die Grafik-Pipeline
*(Basierend auf Folien 82-96)*

### Die Theorie
Um ein Bild zu berechnen, durchlaufen die Daten die Pipeline auf der GPU:

1.  **Vertex Shader:** Verarbeitet jeden Eckpunkt (Vertex). Hier passiert die Transformation von 3D-Welt-Koordinaten in Bildschirm-Koordinaten (Model-View-Projection).
2.  **Rasterization:** Die Vektorgrafik (Dreiecke) wird in Pixel (Fragmente) umgewandelt.
3.  **Fragment Shader (Pixel Shader):** Berechnet die Farbe jedes einzelnen Pixels (z.B. durch Licht und Texturen).

**Die "Modelview"-Matrix:**
Oft fasst man die Transformation des Objekts (Model) und die der Kamera (View) zusammen.
$v' = Projection \cdot View \cdot Model \cdot v$

### 🧠 Dein Selbsttest

**Frage 9:** Wenn du die Position aller Eckpunkte eines Objekts verändern willst (z.B. eine Welle, die durch ein Gitter läuft), welchen Shader bearbeitest du?

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Den Vertex Shader.**
Er ist für die Manipulation der Geometrie (Position der Vertices) zuständig.
</details>

<br>

**Frage 10:** In welcher Reihenfolge werden die Matrizen üblicherweise auf einen Vertex $v$ angewendet, um ihn auf den Bildschirm zu bringen? (Denk an die mathematische Schreibweise von rechts nach links).

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Projektion $\cdot$ View $\cdot$ Model $\cdot$ Vertex**

1. **Model-Matrix:** Bringt den Vertex vom lokalen Objektraum in die Welt.
2. **View-Matrix:** Bringt die Welt in den Kameraraum.
3. **Projection-Matrix:** Projiziert den Kameraraum auf den 2D-Bildschirm (Clip Space).
</details>