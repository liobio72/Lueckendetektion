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
   Vegetationshöhenmodell und Waldmaske importieren. <br>
          -> vegetationshoehenmodell.tif <br>
          -> Waldmaske.shp <br>
2. Waldmaske filtern und rasterisieren <br>
   Nur Polygone mit 'forest_typ = 1' (Deckungsgrad der Vegetation über 80%) behalten. Diese zu einem binären Raster ableiten. <br>
          -> forest_tree_mask.tif <br>
3. Baummaske ableiten <br>
   Im Vegetationshoehenmodell nur Punkte mit Höhe >= 3 Meter nehmen und rasterisieren. <br>
          -> tree_mask.tif <br>
4. Raster für die Suche der Waldlücken <br>
   Kombinieren der beiden vorherigen Raster mit 3 verschiedenen Values; 0 = Nicht Wald-Fläche, 1 = Waldfläche, 2 = potenzielle Lücken. <br>
          -> waldlueckendetektion_mask.tif <br>
<br>
### Lückendetektion Variante a:  Euklidische Distanzanalyse
5a. Euklidische Distanzanalyse <br>
    Distanz von jedem Pixel mit Value = 2 (potenzielle Lücke) zu einem Pixel mit Value = 1 (Waldfläche). <br>
          -> gap_distance.tif
6a. Pixel mit mindestens 5 Metern Distanz filtern. <br>
          -> gap_pixels.tif
7a. Polygonisierung der gap_pixels <br>
          -> gap_polygons.gpkg
8a. 5 Meter Buffer von den gap_polygons <br>
          -> gap_buffered_polygons.gpkg
9a. Polygone dissolven (überlappende Polygone mergen) <br>
          -> gap_dissolved_polygons.gpkg
10a. Polygone rausnehmen, die Rand von 'forest_typ = 1' schneiden. <br>
          -> gap_polygons_final.gpkg
<br>
### Lückendetektion Variante b: Moving Window
5b. Moving Window
    Ein Kreis mit 10 Meter Durchmesser (* Korrektur-Faktor: 0.98) über die Waldfläche ziehen lassen und jede Lücke, in die der Kreis passt, als Lücke schreiben. <br>
          -> gap_moving_window.tif <br>
6b. Polygonisierung der passenden Lücken <br>
          -> gap_polygons_moving_window.gpkg <br>
7b. Überscheidende Polygone mit forest_typ1
          -> gap_polygons_final_moving_window.gpkg <br>

### Geplant:
- Euklidische Distanztransformation -> Distanz von einem Pixel zum nächsten Baum-Pixel.
- Alle Pixel, die mindestens 5 Meter entfernt sind vom nächsten Baum-Pixel auswählen.
- 5 Meter Buffer von den ausgewählten Pixel.
- Flächen bilden und Polygonisierung.

### Kontrolle:
99. Vergleich 'Lueckendetektion-Polygone' zu 'Luecken interpretiert Luftbild-Polygone'
