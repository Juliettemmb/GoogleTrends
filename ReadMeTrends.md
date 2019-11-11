# Descargar series de Google Trends

Se descargan series de combinaciones de palabras, según la data de interés.

## Librerías

Se usará pandas para el manejo de datos y "pytrends" una API no oficial de Google trends.

```
import pandas as pd
from pytrends.request import TrendReq
```

### Información preliminar requerida

Se eligen las palabras de interés, llenando entre las comillas las palabras principales y disponiendo de una archivo Excel que corresponde a las palabras que le seguirán a las principales en la búsqueda.

Para nuestro criterio se decidió de una base de datos únicamento usar las palabras que como frecuencia se repetieran más de 7 veces.

```
candidate_firstname = "carlos"
candidate_lastname = "Galan"

df = pd.read_excel("C:/Users/EJEMPLO/Downloads/"+ candidate_lastname +".xlsx")

df = df[['Palabras','Frequencia']]
df = df[df['Frequencia']>7]
```

### Conexión a Google

Se realiza mediante la siguiente línea

```
pytrends = TrendReq(hl = 'en-US', tz = 360)
```
### Combinación palabras principales y secundarias de búsqueda

Con la función se crean dos columnas en el data frame, una que contiene, para este caso, el "primer nombre del candidato" + las palabras secundarias elegidas por frecuencia y en la segunda columna, la misma información pero esta vez con el "segundo nombre del candidato"

```
df['Palabras1'] = df['Palabras'].apply(lambda x:x + " " + candidate_firstname)
df['Palabras2'] = df['Palabras'].apply(lambda x:x + " " + candidate_lastname)
```

Se crea una lista de palabras usando ambos data frame, posteriormente se crean listas de hasta 5 elementos y se almacena cada grupo en una posición de grupos_palabras[]

```
palabras = list(df['Palabras1']) + list(df['Palabras2'])

grupos_palabras = list(zip(*[iter(palabras)]*5))
grupos_palabras = [list(x) for x in grupos_palabras]
```

## Obtención Series

Para cada grupo de palabras se especifican los parámetros de búsqueda.
En este caso, la lista de palabras a buscar, el tiempo: entre hoy y 5 años atrás, y en geografía 'CO' de Colombia.

Usando "pytrends.interest_over_time()" obtenemos el puntaje que ha recibido la búsqueda del elemento de la lista indicado.

```
dicti = {}
i = 1
for palabras in grupos_palabras:
    pytrends.build_payload(palabras, timeframe = 'today 5-y', geo = 'CO')
    dicti[i] = pytrends.interest_over_time()
    i+=1
```

### Guardar data

El archivo se exporta .xlsx

```
prueba.to_excel("CarlosGalanGoogleTrends.xlsx")
```


## Construido con

* [pytrends](https://github.com/GeneralMills/pytrends) 

## Autores

Semillero Data Science.
