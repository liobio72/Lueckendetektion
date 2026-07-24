# Lueckendetektion

## Ziel des Datensatz
Der Datensatz soll Waldlücken im Waldgebiet erkennen. Als eine Waldlücke gilt eine Fläche, auf der die Vegetation unter 3 Meter hoch ist und zwischen zwei gegenüberliegenden Kronentraufen
(bzw. Vegetationspunkten, die über 3 Meter liegen) mindestens 10 Meter Abstand voneinander besteht.

Der Datensatz wird zur Beurteilung von Schutzwald verwendet. Schutzwälder können ihrer Funktion besser dienen, wenn sie dicht sind und keine Lücken enthalten.

## Vorgehen
### Input-Daten
- Vegetationshöhenmodell von der WSL
  Erstellt durch LiDAR-Daten von der swisstopo, aus dem Produkt swissSURFACE3D.
- Waldmaske von der WSL
  Polygon-Layer der Waldfläche. Aufgeteilt auf Deckungsgrad der Vegetation im Attribut 'forest_typ'.
  'forest_typ = 1': Deckungsgrad > 80%, 'forest_typ = ... AUSFÜLLEN

## Workflow:
1. Import der Daten
   Vegetationshöhenmodell und Waldmaske
2. Waldmaske filtern
   Nur Polygone mit 'forest_typ = 1' (Deckungsgrad der Vegetation über 80%) werden verwendet.
3. Waldmaske rasterisieren
   Binärer Raster aus der Waldmaske. 1, wenn 'forest_typ=1'. 0, wenn 'forest_typ NOT 1'.
4. Binäre Baummaske ableiten
   Binärer Raster aus dem Vegetationshöhenmodell. 1, wenn 'Vegetationshöhe >= 3m'. 0, wenn 'Vegetationshöhe < 3m'.
5. Kombinieren der zwei Raster
6. Lückendetektion.....

### Geplant:
- Euklidische Distanztransformation -> Distanz von einem Pixel zum nächsten Baum-Pixel.
- Alle Pixel, die mindestens 5 Meter entfernt sind vom nächsten Baum-Pixel auswählen.
- 5 Meter Buffer von den ausgewählten Pixel.
- Flächen bilden und Polygonisierung.

### Kontrolle:
99. Vergleich 'Lueckendetektion-Polygone' zu 'Luecken interpretiert Luftbild-Polygone'
