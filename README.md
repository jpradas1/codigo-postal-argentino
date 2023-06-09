# Challenge Código Postal Argentino

## Introduction
Este proyecto contiene una posible solución al [Challenge expuesto por Palta](https://github.com/palta-app/codigo-postal-argentino). Este consiste en armar un scraper que permita recolectar la información sobre el Código Postal Argentino (CPA de ahora en adelante) puesto que se carece de una API que tenga esta información al alcance.

### ¿Qué es el CPA?

Código Postal Argentina es una nueva versión del código postal argentino comenzado a ser requerido, el mismo se compone de tres partes:

`A1234XYZ`

- A -> Un prefijo de una letra que simboliza el Estado/Provincia donde se encuentra la localidad
- 1234 -> Cuatro números referenciando el código postal de la localidad (el viejo y conocido CP, ejemplo en Mendoza es: 5500)
- XYZ -> Un sufijo de tres letras que refieren: la altura de una calle y cara de una manzana (es decir, si es par o impar).

Ejemplo real de CPA es: `M5500AAA` que pertenece a Avenida Gral San Martin a todos los números impares entre los números 1 a 99 en Mendoza, provincia Mendoza, Argentina

El desafío está en generar un script para poder obtener o consultar los CPAs que existen en la actualidad y guardarlo en documentos CSV.

Para ello hemos encontrado la web: [https://codigo-postal.co/](https://codigo-postal.co/) que permite consultar sobre CPA con distintos endpoints como:

- consultado vía países, provincia, localidad y calles: [https://codigo-postal.co/argentina/mendoza/mendoza/12-de-febrero/](https://codigo-postal.co/argentina/mendoza/mendoza/12-de-febrero/)
- consultando directamente al CPA: [https://codigo-postal.co/argentina/cpa/M5500FHA/](https://codigo-postal.co/argentina/cpa/M5500FHA/)

## Scraper y Spiders
Para la solución de este reto he empleado el framework [SCRAPY](https://scrapy.org/) el cual permite realizar web scraping a alto nivel. 

La estructura de la solución se compone de dos Spiders. La primera ([postal_code](https://github.com/jpradas1/codigo-postal-argentino/tree/main/postal_code)) se encarga the extract los [paises](https://github.com/jpradas1/codigo-postal-argentino/blob/main/data/country.csv) disponibles en la website de [codigo-postal](https://codigo-postal.co/) donde se encuentran 12 paises hasta la fecha. Luego de elegir el país al cual se le quiere realizar webscraping (por ahora funciona completamente para Argentina) se extraen los [estados](https://github.com/jpradas1/codigo-postal-argentino/blob/main/data/state.csv) y posteriormente cada una de las [ciudades](https://github.com/jpradas1/codigo-postal-argentino/blob/main/data/city.csv). Toda esta data es originalmente almacenada en una base de datos SQL tal y como se explica en el script [pipelines.py](https://github.com/jpradas1/codigo-postal-argentino/blob/main/postal_code/postal_code/pipelines.py), aun así en el directorio [data/](https://github.com/jpradas1/codigo-postal-argentino/tree/main/data) se encuetra en formato CSV esta data. Junto a los nombres de las ciudades se extrae el link de cada una de ellas los cuales contienen las localidades.

La segunda Spider [localities](https://github.com/jpradas1/codigo-postal-argentino/tree/main/localities) es la que se encarga verdaderamente de exraer cada uno de los CPA. Con ayuda de los datos extraidos por la primera Spider, esta extrae las [localidades](https://github.com/jpradas1/codigo-postal-argentino/blob/main/data/locality.csv) de cada ciudad como su codigo postal. Sin embargo, no todas las localidades están representadas por un único CPA sino que son las calles de cada localidad que tienen su propio CPA, como se explicó anteriormente. Por lo tanto, para cada una de estas localidades se extrae la [calle, el número, su paridad y su CPA](https://github.com/jpradas1/codigo-postal-argentino/blob/main/data/street.csv). Una vez más la data es almacenada en al misma base de datos SQL alimantada según [pipeline.py](https://github.com/jpradas1/codigo-postal-argentino/blob/main/localities/localities/pipelines.py) de esta Spider. Aun así se ha guardado en formato CSV las data relacionada con el estado o provincia de Buenos Aires cuyas ciudades inicien con "A".

## Ejecución
