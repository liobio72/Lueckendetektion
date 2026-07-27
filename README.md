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
1. Import der Daten <br>
   Vegetationshöhenmodell und Waldmaske <br>
          -> vegetationshoehenmodell.tif <br>
          -> Waldmaske.shp <br>
2. Waldmaske filtern und rasterisieren <br>
   Nur Polygone mit 'forest_typ = 1' (Deckungsgrad der Vegetation über 80%) behalten. Diese zu einem binären Raster ableiten. <br>
          -> forest_tree_mask.tif <br>
3. Baummaske ableiten <br>
   Im Vegetationshoehenmodell nur Punkte mit Höhe >= 3 Meter nehmen und rasterisieren. <br>
          -> tree_mask.tif <br>
5. Raster für die Suche der Waldlücken <br>
   Kombinieren der beiden vorherigen Raster mit 3 verschiedenen Values; 0 = Nicht Wald-Fläche, 1 = Waldfläche, 2 = potenzielle Lücken. <br>
          -> waldlueckendetektion_mask.tif <br>
6. 
7. Lückendetektion..... <br>
   Die Waldlückendetektion basiert auf einem rasterbasierten Vegetationshöhenmodell. Zunächst werden alle Pixel mit einer Vegetationshöhe von mindestens 3 m als Baumflächen klassifiziert. Die Analyse wird anschliessend auf
   Waldflächen mit forest_typ = 1 beschränkt. Mithilfe einer euklidischen Distanztransformation wird der Abstand jeder potenziellen Lückenfläche zur nächstgelegenen Baumfläche berechnet. Waldlücken werden gemäss dem
   definierten Mindestabstand von 10 m zwischen gegenüberliegenden Kronentraufen identifiziert.

### Geplant:
- Euklidische Distanztransformation -> Distanz von einem Pixel zum nächsten Baum-Pixel.
- Alle Pixel, die mindestens 5 Meter entfernt sind vom nächsten Baum-Pixel auswählen.
- 5 Meter Buffer von den ausgewählten Pixel.
- Flächen bilden und Polygonisierung.

### Kontrolle:
99. Vergleich 'Lueckendetektion-Polygone' zu 'Luecken interpretiert Luftbild-Polygone'
