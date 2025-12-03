# 🎓 Interaktives Lernskript: 3D-Modellierung (Modeling)

Dieses Dokument führt dich durch die theoretischen Grundlagen der geometrischen Modellierung, von Kurven über Polygone bis hin zu Volumenmodellen.

---

## Teil 1: Parametrische Modellierung (Kurven & Splines)

### 1.1 Grundlagen & Rasterisierung
Computergrafik unterscheidet oft zwischen dem idealen mathematischen Modell (z.B. einer Linie von A nach B) und der Darstellung auf einem Raster-Bildschirm (Pixel).

* **Bresenham-Algorithmus:** Ein fundamentaler Algorithmus, um Linien (und Kreise) effizient auf einem Raster zu zeichnen.
    * *Der Trick:* Er nutzt nur Ganzzahl-Addition, Subtraktion und Bit-Shifting (keine teuren Divisionen oder Fließkommazahlen).
    * Er entscheidet pro Schritt, ob das nächste Pixel "geradeaus" oder "diagonal" gesetzt werden muss, basierend auf einem Fehlerwert.

### 1.2 Bézier-Kurven
Bézier-Kurven sind die Basis vieler Vektorgrafiken.

* **Definition:** Sie werden durch **Kontrollpunkte** definiert. Eine kubische Bézier-Kurve (Grad 3) hat 4 Kontrollpunkte ($P_0, P_1, P_2, P_3$).
* **Eigenschaften:** Die Kurve verläuft durch den ersten und letzten Punkt. Die Punkte dazwischen ($P_1, P_2$) definieren die Tangenten und ziehen die Kurve in ihre Richtung, werden aber meist nicht berührt.
* **De Casteljau Algorithmus:** Ein numerisch stabiler Algorithmus, um Punkte auf einer Bézier-Kurve zu berechnen. Er funktioniert durch wiederholte lineare Interpolation zwischen den Kontrollpunkten.

### 1.3 B-Splines & NURBS
Bézier-Kurven haben einen Nachteil: Wenn man einen Kontrollpunkt bewegt, verändert sich die *gesamte* Kurve (globaler Einfluss).

* **B-Splines:** Lösen das Problem durch stückweise Polynome. Ein Kontrollpunkt beeinflusst nur einen lokalen Abschnitt der Kurve (**lokale Kontrolle**).
* **NURBS (Non-Uniform Rational B-Splines):** Die "Königsklasse". Sie führen **Gewichte** für jeden Kontrollpunkt ein.
    * *Warum wichtig?* Nur mit NURBS lassen sich Kegelschnitte (wie perfekte Kreise) exakt darstellen. Normale B-Splines können Kreise nur annähern.

### 🧠 Quiz: Parametrische Kurven

**Frage 1: Was ist der Hauptvorteil des Bresenham-Algorithmus gegenüber dem einfachen Zeichnen einer Linieformel ($y=mx+c$)?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

Der Bresenham-Algorithmus ist deutlich effizienter, da er auf teure Fließkomma-Berechnungen und Divisionen verzichtet und stattdessen nur Ganzzahl-Operationen (Addition, Subtraktion, Bit-Shifting) verwendet. Zudem vermeidet er Lücken bei steilen Linien.

</details>

**Frage 2: Du hast eine kubische Bézier-Kurve. Wie viele Kontrollpunkte benötigst du dafür und durch wie viele dieser Punkte verläuft die Kurve garantiert?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

Man benötigt **4 Kontrollpunkte** ($P_0$ bis $P_3$). Die Kurve verläuft garantiert durch **2 Punkte**: den Startpunkt ($P_0$) und den Endpunkt ($P_3$).

</details>

**Frage 3: Was ist der entscheidende Unterschied zwischen einer normalen B-Spline und einer NURBS-Kurve, insbesondere wenn es um Formen wie Kreise geht?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

NURBS (Non-Uniform Rational B-Splines) haben zusätzlich **Gewichte** für die Kontrollpunkte. Dies ermöglicht die mathematisch **exakte** Darstellung von Kegelschnitten (wie Kreisen und Ellipsen). Normale B-Splines können Kreise nur annähern.

</details>

## Teil 2: Polygon-Netze (Polygon Meshes)

### 2.1 Definitionen
Da parametrische Modelle oft schwer direkt zu rendern sind, nutzen wir meist Polygon-Netze.

* **Polygon:** Eine Fläche, die durch mindestens 3 Punkte verbunden ist.
* **Manifold (Mannigfaltigkeit):** Ein "sauberes" Mesh. Jede Kante wird von genau zwei Flächen geteilt (bei geschlossenen Objekten). Es gibt keine sich selbst durchdringenden Geometrien oder Kanten, an denen 3+ Flächen hängen.

### 2.2 Datenstrukturen (Wie speichert der PC das?)
Einfache Listen von Vertices und Faces reichen oft nicht, wenn man wissen will: "Welche Fläche liegt neben Fläche A?". Wir brauchen Nachbarschaftsinformationen (Topologie).

* **Winged Edge:** Eine kanten-basierte Struktur. Jede Kante kennt ihre Start-/Endpunkte, die angrenzenden Flächen und die direkten Nachbar-Kanten.
* **DCEL (Doubly-Connected Edge List):** Auch bekannt als **Half-Edge** Struktur. Jede Kante wird in zwei "Halb-Kanten" mit entgegengesetzter Richtung zerlegt. Dies macht das Navigieren durch das Netz (z.B. "Finde alle Nachbarn eines Vertex") sehr effizient (konstante Zeit für viele Operationen).

### 2.3 Triangulierung & Art Gallery Problem
Viele Algorithmen arbeiten nur auf Dreiecken. Ein komplexes Polygon muss also in Dreiecke zerlegt werden (**Triangulierung**).

* **Art Gallery Problem:** Wie viele Kameras braucht man, um einen verwinkelten Raum komplett zu überwachen?
* **Lösung:** Man zerlegt das Polygon in monotone Teil-Polygone (z.B. mit einem **Sweep-Line Algorithmus**) und trianguliert diese dann. Das geht effizienter als naive Ansätze.

### 🧠 Quiz: Polygone & Datenstrukturen

**Frage 4: Warum reicht eine einfache Liste von Dreiecken (z.B. "Dreieck A besteht aus Punkt 1, 2, 3") oft nicht aus für komplexe Modellierungs-Operationen?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

Weil einfache Listen keine expliziten **Nachbarschaftsinformationen** (Topologie) speichern. Wenn man z.B. wissen will, welches Dreieck *neben* Dreieck A liegt, müsste man die ganze Liste durchsuchen. Strukturen wie DCEL/Half-Edge speichern diese Verbindungen direkt.

</details>

**Frage 5: Was ist der Unterschied zwischen einem "Vertex Split" und einem "Face Split" bei Subdivision-Schemata?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

* **Vertex Split:** Erzeugt neue Punkte für die angrenzenden Flächen eines Punktes (selten bei Dreiecken).
* **Face Split:** Fügt einen neuen Punkt in die Mitte einer Fläche oder Kante ein und unterteilt die Fläche (z.B. wird ein Viereck in vier kleinere Vierecke geteilt). Dies ist die gängigste Methode (z.B. Catmull-Clark).

</details>

**Frage 6: Ein Polygonnetz soll "watertight" (wasserdicht) sein. Was bedeutet das für die Kanten des Netzes?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

Es bedeutet, dass das Netz geschlossen ist (ein Volumen umschließt). Jede Kante im Netz muss an genau **zwei** Flächen grenzen. Es darf keine "offenen" Kanten geben, an denen nur eine Fläche hängt, und keine Kanten, an denen mehr als zwei Flächen hängen (Non-Manifold).

</details>

## Teil 3: Subdivision & CSG

### 3.1 Subdivision Surfaces (Unterteilungsflächen)
Wie macht man aus einem eckigen Polygon-Modell ein glattes organisches Objekt?

* Man unterteilt die Polygone rekursiv in kleinere Polygone und verschiebt die Punkte, um die Form abzurunden.
* **Catmull-Clark:** Der Standard für Viereck-Netze (Quads). Erzeugt sehr schöne, glatte Oberflächen.
* **Loop Subdivision:** Ein Schema speziell für Dreiecks-Netze.

### 3.2 CSG (Constructive Solid Geometry)
Hier wird nicht mit Punkten und Kanten modelliert, sondern mit **Volumen-Primitiven** (Kugel, Würfel, Zylinder).

* Man kombiniert diese mit Booleschen Operatoren:
    * **Union ($\cup$):** Vereinigung (A plus B).
    * **Difference ($-$):** Abzug (A ohne B, z.B. um ein Loch zu bohren).
    * **Intersection ($\cap$):** Schnittmenge (Nur das, wo A und B sich überlappen).
* Wird oft im CAD-Bereich (Ingenieurwesen) verwendet.

### 🧠 Quiz: Fortgeschrittene Modellierung

**Frage 7: Welches Subdivision-Schema eignet sich am besten, wenn dein Modell hauptsächlich aus Vierecken (Quads) besteht?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

**Catmull-Clark Subdivision.** Es ist der Industriestandard für Quad-Meshes und erzeugt reine Quad-Topologie (außer an sehr speziellen Stellen). Loop Subdivision hingegen ist für Dreiecke gedacht.

</details>

**Frage 8: Du möchtest ein Modell erstellen, das exakt wie ein Schweizer Käse aussieht (ein Block mit vielen Löchern). Welches Modellierungsverfahren (CSG oder Splines) ist hierfür intuitiver und warum?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

**CSG (Constructive Solid Geometry).**
Man kann einfach einen Würfel nehmen und Kugeln an den entsprechenden Stellen mittels der **Differenz (Difference)**-Operation abziehen ("subtrahieren"). Mit Splines wäre das Loch-Schneiden viel aufwendiger.

</details>

## Teil 4: Volumetrische Modellierung

### 4.1 Voxel
Statt nur die Hülle (Boundary Rep) zu speichern, speichern wir das Volumen.

* **Voxel:** Ein "Volumen-Pixel". Denk an Minecraft-Blöcke oder medizinische CT-Scans.
* Vorteil: Man kann leicht ins Innere schauen oder komplexe Schnitte machen.
* Nachteil: Hoher Speicherbedarf.

### 4.2 Marching Cubes
Wie macht man aus Voxeln (z.B. einem MRT-Scan) wieder ein 3D-Modell aus Dreiecken?

* Der **Marching Cubes** Algorithmus geht durch das Voxel-Gitter. Er schaut sich 8 benachbarte Voxel an (einen virtuellen Würfel).
* Je nachdem, welche Ecken des Würfels "innerhalb" und welche "außerhalb" des Objekts liegen, wählt er passende Dreiecke aus einer Tabelle, um die Oberfläche durch diesen Würfel zu ziehen.

### 🧠 Quiz: Volumen

**Frage 9: Woher kennt man Voxel-Daten typischerweise aus der realen Welt (außerhalb von Spielen)?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

Aus der **Medizin**, speziell aus der Computertomographie (CT) oder Magnetresonanztomographie (MRT). Diese Geräte erzeugen Schichtbilder, die gestapelt ein Voxel-Gitter ergeben.

</details>

**Frage 10: Was ist das Ziel des "Marching Cubes"-Algorithmus?**

<details markdown="1">
<summary>Lösung anzeigen</summary>

Er konvertiert **volumetrische Daten (Voxel)** in ein **Oberflächen-Modell (Polygone/Dreiecke)**. Er extrahiert also eine Iso-Oberfläche aus einem Datenfeld, damit sie von Standard-Grafikkarten gerendert werden kann.

</details>