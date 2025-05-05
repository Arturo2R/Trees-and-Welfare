### Grilla
**Dimension escogida:** 400x400px zoom 19
```python
from math import cos, radians
baqlat = radians(4.57)
zoom = 19
resolution = 156543.03 * abs(cos(baqlat)) / (2 ** zoom)
print(resolution)
```
Eso de una resolución de 30 cm por pixel

```python
resolucion = 0.297
area = 420 * resolution
print(area)
```
Por lo que cada imagen capturara 120 metros de largo, $14,400m^2$ .


Primero debo definir el area de interés ROI extrayendo el mapa de barranquilla, y dividirlo en cuadrados de 120m (Tambien debo añadirle un overlap, y encima de eso para quitar la etiqueta de google maps). 

![[Pasted image 20250501180936.png]]


Para obtener las imágenes usare las imágenes de satelitales de Maxar que se pueden acceder.

Con centroide
![[Pasted image 20250501182817.png]]

[Overview | Maps Static API | Google for Developers](https://developers.google.com/maps/documentation/maps-static/overview)

``` python
import requests

url = "https://maps.googleapis.com/maps/api/staticmap"
params = {
    "center": "10.996295073606044,-74.79629964934422",
    "zoom": "18",
    "size": "640x6400",
    "maptype": "satellite",
    "format": "png"
    "key": "AIzaSyDGXWb1rUdq3d7EseCZX6rM_twB-tzllZs"
}

response = requests.get(url, params=params)
with open("barranquilla_map.png", "wb") as f:
    f.write(response.content)
```

Los mapas de Google Maps tienen un "nivel de zoom" de número entero que define la resolución de la vista actual. Dentro de la vista predeterminada de `roadmap`, se pueden usar niveles de zoom entre `0` (el nivel de zoom más bajo, en el que se puede ver todo el mundo en un mapa) y `21+` (hasta calles y edificios individuales). Los contornos de los edificios, cuando están disponibles, aparecen en el mapa alrededor del nivel de zoom `17`. Este valor difiere de un área a otra y puede cambiar con el tiempo a medida que evolucionan los datos.

- 1: Mundo
- 5: Tierra firme y continente
- 10: Ciudad
- 15: Calles
- 20: Edificios

Naming and function of tiles and zoom levels: https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames#Resolution_and_Scale

![[Pasted image 20250501114756.png]]![[Pasted image 20250501114758.png]]


> [!NOTE] Title
> Contents


https://wiki.openstreetmap.org/wiki/Slippy_map_tilenames#Resolution_and_Scale

[[Slippy map tilenames - OpenStreetMap Wiki]]



## Resolution and Scale

Exact length of the equator (according to [Wikipedia](https://en.wikipedia.org/wiki/en:equator#Exact_length "w:en:equator")) is 40075.016686 km in WGS-84. At zoom 0, one pixel would equal 156543.03 meters (assuming a tile size of 256 px):

40075.016686 * 1000 / 256 ≈ 6378137.0 * 2 * pi / 256 ≈ 156543.03

Which gives us a formula to calculate resolution at any given zoom:

resolution = 156543.03 meters/pixel * cos(latitude) / (2 ^ zoomlevel)

Some applications need to know a map scale, that is, how 1 cm on a screen translates to 1 cm of a map.

scale = 1 : (screen_dpi * 1/0.0254 in/m * resolution)

And here is the table to rid you of those calculations. All values are shown for equator, and you have to multiply them by cos(latitude) to adjust to a given latitude. For example, divide those by 2 for latitude 60 (Oslo, Helsinki, Saint-Petersburg).

|zoom|resolution, m/px|scale 90 dpi|1 screen cm is|scale 96 dpi|scale 120 dpi|
|---|---|---|---|---|---|
|0|156543.03|1 : 554 680 041|5547 km|1 : 591 658 711|1 : 739 573 389|
|1|78271.52|1 : 277 340 021|2773 km|1 : 295 829 355|1 : 369 786 694|
|2|39135.76|1 : 138 670 010|1387 km|1 : 147 914 678|1 : 184 893 347|
|3|19567.88|1 : 69 335 005|693 km|1 : 73 957 339|1 : 92 446 674|
|4|9783.94|1 : 34 667 503|347 km|1 : 36 978 669|1 : 46 223 337|
|5|4891.97|1 : 17 333 751|173 km|1 : 18 489 335|1 : 23 111 668|
|6|2445.98|1 : 8 666 876|86.7 km|1 : 9 244 667|1 : 11 555 834|
|7|1222.99|1 : 4 333 438|43.3 km|1 : 4 622 334|1 : 5 777 917|
|8|611.50|1 : 2 166 719|21.7 km|1 : 2 311 167|1 : 2 888 959|
|9|305.75|1 : 1 083 359|10.8 km|1 : 1 155 583|1 : 1 444 479|
|10|152.87|1 : 541 680|5.4 km|1 : 577 792|1 : 722 240|
|11|76.437|1 : 270 840|2.7 km|1 : 288 896|1 : 361 120|
|12|38.219|1 : 135 420|1.35 km|1 : 144 448|1 : 180 560|
|13|19.109|1 : 67 710|677 m|1 : 72 224|1 : 90 280|
|14|9.5546|1 : 33 855|339 m|1 : 36 112|1 : 45 140|
|15|4.7773|1 : 16 927|169 m|1 : 18 056|1 : 22 570|
|16|2.3887|1 : 8 464|84.6 m|1 : 9 028|1 : 11 285|
|17|1.1943|1 : 4 232|42.3 m|1 : 4 514|1 : 5 642|
|18|0.5972|1 : 2 116|21.2 m|1 : 2 257|1 : 2 821|

[[Funcionamiento de un Mapa moderno]]


138 pixels per inch my computer