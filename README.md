# Lueckendetektion

## Fragen:
- Wenn eine Lücke an einem Ort enger wird und die Traufen näher als 10m beieinander liegen, zählt die Lücke dann als zwei Lücken? sie wird also geschnitten und nicht gelöscht?

## Momentaner Workflow:
1. LiDAR import
2. DSM ableiten
3. DTM ableiten
4. DTM no_data-Lücken mit interpelation füllen, damit später keine no_data-Lücken als Waldlücken erkannt werden.
5. CHM (Vegetationshöhenmodell) aus DSM und DTM ableiten.
6. Vegetationsmaske -> Raster mit Vegetation/ nicht Vegetation
7. Baummaske -> Raster mit Vegetation über 3 Meter.

## Geplant:
8. Euklidische Distanztransformation -> Distanz von einem Pixel zum nächsten Baum-Pixel.
9. Alle Pixel, die mindestens 5 Meter entfernt sind vom nächsten Baum-Pixel auswählen.
10. 5 Meter Buffer von den ausgewählten Pixel.
11. Flächen bilden und Polygonisierung.
