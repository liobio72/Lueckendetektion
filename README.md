# Lueckendetektion

## Ziel des Datensatz
Der Datensatz soll Waldlücken im Waldgebiet erkennen. Als eine Waldlücke gilt eine Fläche, auf der die Vegetation unter 3 Meter hoch ist und zwischen zwei gegenüberliegenden Kronentraufen
(bzw. Vegetationspunkten, die über 3 Meter liegen) mindestens 10 Meter Abstand voneinander besteht.

Der Datensatz wird zur Beurteilung von Schutzwald verwendet. Schutzwälder können ihrer Funktion besser dienen, wenn sie dicht sind und keine Lücken enthalten.

## Vorgehen
### Input-Daten
- Vegetationshöhenmodell von der WSL
  - Erstellt durch LiDAR-Daten von der swisstopo, aus dem Produkt swissSURFACE3D.
- Waldmaske von der WSL
  - Polygon-Layer der Waldfläche. Aufgeteilt auf Deckungsgrad der Vegetation im Attribut 'forest_typ'.
    - forest_typ = 1 => closed forest   (>60% durchschnittlicher Deckungsgrad)
    - forest_typ = 2 => open forest     (<60% & >20% durchschnittlicher Deckungsgrad)
    - forest_typ = 3 => shrub forest    (
  

## Workflow:
#### 0. Import der Daten
  - Vegetationshöhenmodell und Waldmaske importieren. (passiert in Skript 1 und Skript 3.) <br>
`vegetationshoehenmodell.tif` <br>
`Waldmaske.shp`
#### 1. Waldmaske filtern
  - Nur Polygone mit 'forest_typ = 1' (Deckungsgrad der Vegetation über 80%) behalten.

`forest_typ1.gpkg`
#### 2. Waldmaske rasterisieren

`forest_mask.tif`
#### 3. Baummaske ableiten
  - Im Vegetationshoehenmodell nur Punkte mit 'height >= 3' Meter nehmen und rasterisieren.
`tree_mask.tif`
#### 4. Raster für die Suche der Waldlücken
  - Kombinieren der beiden vorherigen Raster mit 3 verschiedenen Values; 0 = Nicht Wald-Fläche, 1 = Waldfläche, 2 = potenzielle Lücken.
`waldlueckendetektion_mask.tif`

### Lückendetektion Variante 1:  Euklidische Distanzanalyse
#### 5. Euklidische Distanzanalyse
  - Distanz von jedem Pixel mit Value = 2 (potenzielle Lücke) zu einem Pixel mit Value = 1 (Waldfläche).
`gap_distance.tif`
#### 6. Pixel mit mindestens 5 Metern Distanz filtern.
`gap_pixels.tif`
#### 7. Polygonisierung der gap_pixels
`gap_polygons.gpkg`
#### 8. 5 Meter Buffer von den gap_polygons
`gap_buffered_polygons.gpkg`
#### 9. Polygone dissolven (überlappende Polygone mergen)
`gap_dissolved_polygons.gpkg`
#### 10. Polygone rausnehmen, die Rand von 'forest_typ = 1' schneiden.
**`gap_polygons_final.gpkg`**

### Lückendetektion Variante 2: Moving Window
#### 5. Moving Window
  - Ein Kreis mit 10 Meter Durchmesser (* Korrektur-Faktor: 0.98) über die Waldfläche ziehen lassen und jede Lücke, in die der Kreis passt, als Lücke schreiben.
`gap_moving_window.tif`
#### 6. Polygonisierung der passenden Lücken
`gap_polygons_moving_window.gpkg`
#### 7. Überscheidende Polygone mit forest_typ1
**`gap_polygons_final_moving_window.gpkg`**

### Kontrolle:
99. Vergleich 'Lueckendetektion-Polygone' zu 'Luecken interpretiert Luftbild-Polygone'
