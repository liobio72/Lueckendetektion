# Lueckendetektion

## Ziel des Datensatz
Der Datensatz soll Waldlücken im Waldgebiet erkennen. Als eine Waldlücke gilt eine Fläche, auf der die Vegetation unter 3 Meter hoch ist und zwischen zwei gegenüberliegenden Kronentraufen
(bzw. Vegetationspunkten, die über 3 Meter liegen) mindestens 10 Meter Abstand von einander besteht.

Der Datensatz wird zur Beurteilung von Schutzwald verwendet, da dieser eine grössere Schutzleistung hat, wenn er dicht ist und keine Lücken enthält.

## Fragen:
- Wenn eine Lücke an einem Ort enger wird und die Traufen näher als 10m beieinander liegen, zählt die Lücke dann als zwei Lücken? sie wird also geschnitten und nicht gelöscht?
- Wenn eine Lücke umringt ist von einem schmalen Band an Wald/ Bäumen, zählt die Lücke trotzdem als Lücke?

## Workflow:
1. LiDAR import <br>
   swissSURFACE3D Daten im .las format.
2. DSM ableiten <br>
   Mit 'Export to Raster' und dem Attribut "Classification" und "Z".
3. DTM ableiten <br>
   Mit 'Export to Raster' und dem Filter "Classification = 2 (ground)" und dem Attribut "Z".
4. DTM no_data-Lücken schliessen <br>
   Lücken im DTM mit "no_data" mit 'Fill NoData' per interpellation füllen, damit später keine no_data-Lücken als Waldlücken erkannt werden.
5. CHM (Vegetationshöhenmodell) aus DSM und DTM ableiten. <br>
   Mit dem 'Raster Calculator' und der Expression "DSM - DTM" das CHM (Canopy Height Model/ Vegetationshöhenmodell*) erstellen.
6. Vegetationsmaske ableiten <br>
   Mit 'Export to Raster' und dem Filter "Classification = 3" erstellen.
7. Baummaske ableiten <br>
   Mit 'Raster Calculator' und der Expression "Vegetationsmaske = 1 AND CHM >= 3" die Baummaske erstellen.

### Geplant:
8. Euklidische Distanztransformation -> Distanz von einem Pixel zum nächsten Baum-Pixel.
9. Alle Pixel, die mindestens 5 Meter entfernt sind vom nächsten Baum-Pixel auswählen.
10. 5 Meter Buffer von den ausgewählten Pixel.
11. Flächen bilden und Polygonisierung.
