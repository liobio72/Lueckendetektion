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
- Vegetationshöhenmodell und Waldmaske importieren.
  - vegetationshoehenmodell.tif
  - Waldmaske.shp
2. Waldmaske filtern und rasterisieren
- Nur Polygone mit 'forest_typ = 1' (Deckungsgrad der Vegetation über 80%) behalten. Diese zu einem binären Raster ableiten.
  - forest_tree_mask.tif
3. Baummaske ableiten
- Im Vegetationshoehenmodell nur Punkte mit Höhe >= 3 Meter nehmen und rasterisieren.
  - tree_mask.tif
4. Raster für die Suche der Waldlücken
- Kombinieren der beiden vorherigen Raster mit 3 verschiedenen Values; 0 = Nicht Wald-Fläche, 1 = Waldfläche, 2 = potenzielle Lücken.
  - waldlueckendetektion_mask.tif

### Lückendetektion Variante a:  Euklidische Distanzanalyse
5a. Euklidische Distanzanalyse
- Distanz von jedem Pixel mit Value = 2 (potenzielle Lücke) zu einem Pixel mit Value = 1 (Waldfläche).
  - gap_distance.tif
6a. Pixel mit mindestens 5 Metern Distanz filtern.
- gap_pixels.tif
7a. Polygonisierung der gap_pixels
- gap_polygons.gpkg
8a. 5 Meter Buffer von den gap_polygons
- gap_buffered_polygons.gpkg
9a. Polygone dissolven (überlappende Polygone mergen)
- gap_dissolved_polygons.gpkg
10a. Polygone rausnehmen, die Rand von 'forest_typ = 1' schneiden.
- gap_polygons_final.gpkg

### Lückendetektion Variante b: Moving Window
5b. Moving Window
- Ein Kreis mit 10 Meter Durchmesser (* Korrektur-Faktor: 0.98) über die Waldfläche ziehen lassen und jede Lücke, in die der Kreis passt, als Lücke schreiben.
  - gap_moving_window.tif
6b. Polygonisierung der passenden Lücken
- gap_polygons_moving_window.gpkg
7b. Überscheidende Polygone mit forest_typ1
- gap_polygons_final_moving_window.gpkg

### Kontrolle:
99. Vergleich 'Lueckendetektion-Polygone' zu 'Luecken interpretiert Luftbild-Polygone'
