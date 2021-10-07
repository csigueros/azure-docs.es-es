---
title: Funciones de expresiones en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre las funciones de expresiones de Asignación de Data Flow.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/23/2021
ms.openlocfilehash: dc0bdf8f8d40b23f4f6e8338ab6b608278428149
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060278"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Expresiones de transformación de datos en Asignación de Data Flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

En este artículo se proporcionan detalles sobre las expresiones y funciones compatibles con Azure Data Factory y Azure Synapse Analytics en los flujos de datos de asignación.


## <a name="expression-functions"></a>Funciones de expresiones

En las canalizaciones de Data Factory y Synapse, use el lenguaje de expresiones de la característica de flujo de datos de asignación para configurar transformaciones de datos.

| Función de expresión | Tarea |
|-----|-----|
| [abs](data-flow-expression-functions.md#abs) | El valor absoluto de un número.  |
| [acos](data-flow-expression-functions.md#acos) | Calcula un valor inverso de coseno.  |
| [add](data-flow-expression-functions.md#add) | Agrega un par de cadenas o números. Agrega una fecha a un número de días. Agrega una vigencia a una marca de tiempo. Anexa una matriz de tipo similar a otra. Igual que el operador +.  |
| [addDays](data-flow-expression-functions.md#addDays) | Agrega días a una fecha o marca de tiempo. Igual que el operador + para la fecha.  |
| [addMonths](data-flow-expression-functions.md#addMonths) | Suma meses a una fecha o marca de tiempo. Opcionalmente, puede pasar una zona horaria.  |
| [and](data-flow-expression-functions.md#and) | Operador lógico AND. Igual que &&.  |
| [asin](data-flow-expression-functions.md#asin) | Calcula un valor inverso de seno.  |
| [atan](data-flow-expression-functions.md#atan) | Calcula un valor inverso de tangente.  |
| [atan2](data-flow-expression-functions.md#atan2) | Devuelve el ángulo en radianes entre el eje X positivo de un plano y el punto que especificaron las coordenadas.  |
| [between](data-flow-expression-functions.md#between) | Comprueba si el primer valor se encuentra entre otros dos valores, ambos inclusive. Se pueden comparar valores numéricos, de cadena y de fecha y hora   |
| [bitwiseAnd](data-flow-expression-functions.md#bitwiseAnd) | Operador AND bit a bit entre tipos enteros. Igual que el operador &   |
| [bitwiseOr](data-flow-expression-functions.md#bitwiseOr) | Operador OR bit a bit entre tipos enteros. Igual que el operador \|  |
| [bitwiseXor](data-flow-expression-functions.md#bitwiseXor) | Operador OR bit a bit entre tipos enteros. Igual que el operador \|  |
| [blake2b](data-flow-expression-functions.md#blake2b) | Calcula la síntesis del mensaje Blake2 del conjunto de columnas de diferentes tipos de datos primitivos dada una longitud en bits que solo pueden ser múltiplos de 8 entre 8 y 512. Se puede usar para calcular una huella digital de una fila.  |
| [blake2bBinary](data-flow-expression-functions.md#blake2bBinary) | Calcula la síntesis del mensaje Blake2 del conjunto de columnas de diferentes tipos de datos primitivos dada una longitud en bits que solo pueden ser múltiplos de 8 entre 8 y 512. Se puede usar para calcular una huella digital de una fila.  |
| [case](data-flow-expression-functions.md#case) | Basándose en una condición alternativa, aplica un valor o el otro. Si el número de entradas es par, el otro se establece de manera predeterminada en NULL para la última condición.  |
| [cbrt](data-flow-expression-functions.md#cbrt) | Calcula la raíz cúbica de un número.  |
| [ceil](data-flow-expression-functions.md#ceil) | Devuelve el entero más pequeño no menor que el número.  |
| [coalesce](data-flow-expression-functions.md#coalesce) | Devuelve el primer valor no NULL de un conjunto de entradas. Todas las entradas deben ser del mismo tipo.  |
| [columnNames](data-flow-expression-functions.md#columnNames) | Obtiene los nombres de todas las columnas de salida de una secuencia. Puede pasar un nombre de secuencia opcional como segundo argumento.  |
| [columns](data-flow-expression-functions.md#columns) | Obtiene los valores de todas las columnas de salida de una secuencia. Puede pasar un nombre de secuencia opcional como segundo argumento.   |
| [compare](data-flow-expression-functions.md#compare) | Compara dos valores del mismo tipo. Devuelve un entero negativo si value1 < value2, 0 si value1 == value2, un valor positivo si value1 > value2.  |
| [concat](data-flow-expression-functions.md#concat) | Concatena un número variable de cadenas. Igual que el operador + con cadenas.  |
| [concatWS](data-flow-expression-functions.md#concatWS) | Concatena un número variable de cadenas con un separador. El primer parámetro es el separador.  |
| [cos](data-flow-expression-functions.md#cos) | Calcula un valor de coseno.  |
| [cosh](data-flow-expression-functions.md#cosh) | Calcula un coseno hiperbólico de un valor.  |
| [crc32](data-flow-expression-functions.md#crc32) | Calcula la síntesis del mensaje CRC32 del conjunto de columnas de diferentes tipos de datos primitivos dada una longitud en bits, que solo puede tener los valores 0(256), 224, 256, 384 y 512. Se puede usar para calcular una huella digital de una fila.  |
| [currentDate](data-flow-expression-functions.md#currentDate) | Obtiene la fecha actual cuando este trabajo empieza a ejecutarse. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). |
| [currentTimestamp](data-flow-expression-functions.md#currentTimestamp) | Obtiene la marca de tiempo actual cuando se inicia el trabajo para ejecutarse con la zona horaria local.  |
| [currentUTC](data-flow-expression-functions.md#currentUTC) | Obtiene la marca de tiempo actual como hora UTC. Si quiere que la hora actual se interprete en una zona horaria distinta a la del clúster, puede pasar una zona horaria opcional con el formato "GMT", "PST", "UTC ", "America/Caimán". La zona horaria actual se establece como predeterminada. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Para convertir la zona horaria UTC a una distinta, use `fromUTC()`.  |
| [dayOfMonth](data-flow-expression-functions.md#dayOfMonth) | Obtiene el día del mes dada una fecha.  |
| [dayOfWeek](data-flow-expression-functions.md#dayOfWeek) | Obtiene el día de la semana dada una fecha. 1 corresponde al domingo, 2 al lunes... y 7 al sábado.  |
| [dayOfYear](data-flow-expression-functions.md#dayOfYear) | Obtiene el día del año dada una fecha.  |
| [days](data-flow-expression-functions.md#days) | Duración en milisegundos para el número de días.  |
| [degrees](data-flow-expression-functions.md#degrees) | Convierte radianes en grados.  |
| [divide](data-flow-expression-functions.md#divide) | Divide dos números. Igual que el operador `/`.  |
| [dropLeft](data-flow-expression-functions.md#dropLeft) | Quita los mismos caracteres de la izquierda de la cadena. Si la colocación solicitada supera la longitud de la cadena, se devuelve una cadena vacía.|
| [dropRight](data-flow-expression-functions.md#dropRight) | Quita los mismos caracteres de la derecha de la cadena. Si la colocación solicitada supera la longitud de la cadena, se devuelve una cadena vacía.|
| [endsWith](data-flow-expression-functions.md#endsWith) | Comprueba si la cadena finaliza con la cadena proporcionada.  |
| [equals](data-flow-expression-functions.md#equals) | Operador de comparación igual que. Igual que el operador ==.  |
| [equalsIgnoreCase](data-flow-expression-functions.md#equalsIgnoreCase) | Operador de comparación igual que, sin distinción entre mayúsculas y minúsculas. Igual que el operador <=>.  |
| [escape](data-flow-expression-functions.md#escape) | Escapa una cadena según un formato. Los valores literales para el formato aceptable son "json", "xml", "ecmascript", "html", "java".|
| [expr](data-flow-expression-functions.md#expr) | Da como resultado una expresión de una cadena. Esto es lo mismo que escribir esta expresión de forma no literal. Se puede usar para pasar parámetros como representaciones de cadena.|
| [factorial](data-flow-expression-functions.md#factorial) | Calcula el valor factorial de un número.  |
| [false](data-flow-expression-functions.md#false) | Siempre devuelve un valor false. Utilice la función `syntax(false())` si hay una columna denominada "false".  |
| [floor](data-flow-expression-functions.md#floor) | Devuelve el entero más grande no mayor que el número.  |
| [fromBase64](data-flow-expression-functions.md#fromBase64) | Descodifica la cadena con codificación base64 especificada.|
| [fromUTC](data-flow-expression-functions.md#fromUTC) | Convierte a la marca de tiempo de UTC. Si lo desea, puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria actual se establece como predeterminada. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [greater](data-flow-expression-functions.md#greater) | Operador de comparación mayor que. Igual que el operador >.  |
| [greaterOrEqual](data-flow-expression-functions.md#greaterOrEqual) | Operador de comparación mayor que o igual que. Igual que el operador >=.  |
| [greatest](data-flow-expression-functions.md#greatest) | Devuelve el valor mayor entre la lista de valores como entrada, omitiendo los valores NULL. Devuelve NULL si todas las entradas son NULL.  |
| [hasColumn](data-flow-expression-functions.md#hasColumn) | Comprueba un valor de columna por el nombre en la secuencia. Puede pasar un nombre de flujo opcional como segundo argumento. Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  |
| [hour](data-flow-expression-functions.md#hour) | Obtiene el valor de la hora de una marca de tiempo. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [hours](data-flow-expression-functions.md#hours) | Duración en milisegundos para el número de horas.  |
| [iif](data-flow-expression-functions.md#iif) | Basándose en una condición, aplica un valor o el otro. Si "Otro" si no se especifica, se considera NULL. Ambos valores deben ser compatibles (numéricos, cadena...).   |
| [iifNull](data-flow-expression-functions.md#iifNull) | Comprueba si el primer parámetro es NULL. Si no es NULL, se devuelve el primer parámetro. Si es NULL, se devuelve el segundo parámetro. Si se especifican tres parámetros, el comportamiento es el mismo que iif(isNull(value1), value2, value3) y se devuelve el tercer parámetro si el primer valor no es NULL.  |
| [initCap](data-flow-expression-functions.md#initCap) | Convierte la primera letra de cada palabra en mayúsculas. Las palabras se identifican como separadas por espacios en blanco.  |
| [instr](data-flow-expression-functions.md#instr) | Busca la posición (basada en 1) de la subcadena dentro de una cadena. Se devuelve 0 si no se encuentra.  |
| [isDelete](data-flow-expression-functions.md#isDelete) | Comprueba si la fila está marcada para eliminar. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  |
| [isError](data-flow-expression-functions.md#isError) | Comprueba si la fila se marca como error. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  |
| [isIgnore](data-flow-expression-functions.md#isIgnore) | Comprueba si la fila se marca para pasarse por alto. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  |
| [isInsert](data-flow-expression-functions.md#isInsert) | Comprueba si la fila está marcada para insertar. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  |
| [isMatch](data-flow-expression-functions.md#isMatch) | Comprueba si la fila cumplía los criterios de coincidencia en la búsqueda. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  |
| [isNull](data-flow-expression-functions.md#isNull) | Comprueba si el valor es NULL.  |
| [isUpdate](data-flow-expression-functions.md#isUpdate) | Comprueba si la fila está marcada para actualizar. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  |
| [isUpsert](data-flow-expression-functions.md#isUpsert) | Comprueba si la fila está marcada para insertar. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  |
| [jaroWinkler](data-flow-expression-functions.md#jaroWinkler) | Obtiene la distancia de JaroWrinkler entre dos cadenas. |
| [lastDayOfMonth](data-flow-expression-functions.md#lastDayOfMonth) | Obtiene el último día del mes dada una fecha.  |
| [least](data-flow-expression-functions.md#least) | Operador de comparación menor que o igual que. Igual que el operador <=.  |
| [left](data-flow-expression-functions.md#left) | Extrae un inicio de la subcadena en el índice 1 con el número de caracteres. Igual que SUBSTRING(str, 1, n).  |
| [length](data-flow-expression-functions.md#length) | Devuelve la longitud de la cadena.  |
| [lesser](data-flow-expression-functions.md#lesser) | Operador de comparación menor que. Igual que el operador <.  |
| [lesserOrEqual](data-flow-expression-functions.md#lesserOrEqual) | Operador de comparación menor que o igual que. Igual que el operador <=.  |
| [levenshtein](data-flow-expression-functions.md#levenshtein) | Obtiene la distancia de edición entre dos cadenas.  |
| [like](data-flow-expression-functions.md#like) | El patrón es una cadena que se hace coincidir literalmente. La excepción son los siguientes símbolos especiales: _ coincide con cualquier carácter en la entrada (similar a . en expresiones regulares ```posix```).|
| [locate](data-flow-expression-functions.md#locate) | Busca la posición (basada en 1) de la subcadena dentro de una cadena a partir de una posición determinada. Si la posición se omite se considera desde el principio de la cadena. Se devuelve 0 si no se encuentra.  |
| [log](data-flow-expression-functions.md#log) | Calcula el valor del logaritmo. Se puede suministrar una base opcional o un número de Euler si se usa.  |
| [log10](data-flow-expression-functions.md#log10) | Calcula el valor del logaritmo en base 10.  |
| [lower](data-flow-expression-functions.md#lower) | Pone en minúsculas una cadena.  |
| [lpad](data-flow-expression-functions.md#lpad) | Left rellena la cadena mediante el carácter de relleno proporcionado hasta que se alcanza una determinada longitud. Si la cadena es igual o mayor que la longitud, se recorta para cumplir con la longitud.  |
| [ltrim](data-flow-expression-functions.md#ltrim) | Left recorta una cadena de caracteres iniciales. Si no se especifica el segundo parámetro, recorta el espacio en blanco. Si no, recorta cualquier carácter especificado en el segundo parámetro.  |
| [md5](data-flow-expression-functions.md#md5) | Calcula la síntesis del mensaje MD5 del conjunto de columnas de diferentes tipos de datos primitivos y devuelve una cadena hexadecimal de 32 caracteres. Se puede usar para calcular una huella digital de una fila.  |
| [millisecond](data-flow-expression-functions.md#millisecond) | Obtiene el valor de los milisegundos de una fecha. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [milliseconds](data-flow-expression-functions.md#milliseconds) | Duración en milisegundos para el número de milisegundos.  |
| [minus](data-flow-expression-functions.md#minus) | Resta números. Restar el número de días a partir de una fecha. Resta la vigencia de una marca de tiempo. Resta dos marcas de tiempo para obtener la diferencia en milisegundos. Igual que el operador -.  |
| [minute](data-flow-expression-functions.md#minute) | Obtiene el valor de los minutos de una marca de tiempo. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [minutes](data-flow-expression-functions.md#minutes) | Duración en milisegundos para el número de minutos.  |
| [mod](data-flow-expression-functions.md#mod) | Módulo de par de números. Igual que el operador %.  |
| [month](data-flow-expression-functions.md#month) | Obtiene el valor del mes de una fecha o una marca de tiempo.  |
| [monthsBetween](data-flow-expression-functions.md#monthsBetween) | Obtiene el número de meses entre dos fechas. Puede redondear el cálculo. Puede pasar una zona horaria opcional con el formato "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [multiply](data-flow-expression-functions.md#multiply) | Multiplica dos números. Igual que el operador *.  |
| [negate](data-flow-expression-functions.md#negate) | Niega un número. Convierte los números positivos en negativos y viceversa.  |
| [nextSequence](data-flow-expression-functions.md#nextSequence) | Devuelve la siguiente secuencia única. El número es consecutivo solo dentro de una partición y viene precedido por el identificador de la partición.  |
| [normalize](data-flow-expression-functions.md#normalize) | Normaliza el valor de la cadena para separar caracteres Unicode acentuados.  |
| [not](data-flow-expression-functions.md#not) | Operador de negación lógica.  |
| [notEquals](data-flow-expression-functions.md#notEquals) | Operador de comparación no igual que. Igual que el operador !=.  |
| [notNull](data-flow-expression-functions.md#notNull) | Comprueba si el valor no es NULL.  |
| [null](data-flow-expression-functions.md#null) | Devuelve un valor NULL. Use la función `syntax(null())` si hay una columna denominada "null". Cualquier operación que use dará como resultado un valor NULL.  |
| [or](data-flow-expression-functions.md#or) | Operador lógico OR. Igual que \|\|.  |
| [pMod](data-flow-expression-functions.md#pMod) | Módulo positivo de par de números.  |
| [partitionId](data-flow-expression-functions.md#partitionId) | Devuelve el identificador de la partición actual en donde se encuentra la fila de entrada.  |
| [power](data-flow-expression-functions.md#power) | Eleva un número a la potencia de otro.  |
| [radians](data-flow-expression-functions.md#radians) | Convierte grados en radianes.|
| [random](data-flow-expression-functions.md#random) | Devuelve un número aleatorio dado un valor de inicialización opcional dentro de una partición. El valor de inicialización debe ser un valor fijo y se usa junto con partitionId para generar valores aleatorios.   |
| [regexExtract](data-flow-expression-functions.md#regexExtract) | Extrae una subcadena coincidente para un patrón de expresión regular especificado. El último parámetro identifica el grupo de coincidencia y, si se omite, se usa el valor predeterminado 1. Use `<regex>` (comilla inversa) para que coincida con una cadena sin escape.  |
| [regexMatch](data-flow-expression-functions.md#regexMatch) | Comprueba si la cadena coincide con el patrón de expresión regular especificado. Use `<regex>` (comilla inversa) para que coincida con una cadena sin escape.  |
| [regexReplace](data-flow-expression-functions.md#regexReplace) | Reemplaza todas las repeticiones de un patrón de expresión regular por otra subcadena en la cadena dada. Use `<regex>` (comilla inversa) para que coincida con una cadena sin escape.  |
| [regexSplit](data-flow-expression-functions.md#regexSplit) | Divide una cadena según un delimitador basándose en la expresión regular y devuelve una matriz de cadenas.  |
| [replace](data-flow-expression-functions.md#replace) | Reemplaza todas las repeticiones de una subcadena por otra subcadena en la cadena dada. Si se omite el último parámetro, el valor predeterminado es una cadena vacía.  |
| [reverse](data-flow-expression-functions.md#reverse) | Invierte una cadena.  |
| [right](data-flow-expression-functions.md#right) | Extrae una subcadena con el número de caracteres de la derecha. Igual que SUBSTRING(str, LENGTH(str) - n, n).  |
| [rlike](data-flow-expression-functions.md#rlike) | Comprueba si la cadena coincide con el patrón de expresión regular especificado.  |
| [round](data-flow-expression-functions.md#round) | Redondea un número con una escala opcional y un modo de redondeo opcional dados. Si la escala se omite, se establece en 0 de forma predeterminada. Si el modo se omite, se establece en ROUND_HALF_UP(5) de forma predeterminada. Los valores de redondeo incluyen|
| [rpad](data-flow-expression-functions.md#rpad) | Right rellena la cadena mediante el carácter de relleno proporcionado hasta que se alcanza una determinada longitud. Si la cadena es igual o mayor que la longitud, se recorta para cumplir con la longitud.  |
| [rtrim](data-flow-expression-functions.md#rtrim) | Right recorta una cadena de caracteres finales. Si no se especifica el segundo parámetro, recorta el espacio en blanco. Si no, recorta cualquier carácter especificado en el segundo parámetro.  |
| [second](data-flow-expression-functions.md#second) | Obtiene el valor de los segundos de una fecha. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [segundos](data-flow-expression-functions.md#seconds) | Duración en milisegundos para el número de segundos.  |
| [sha1](data-flow-expression-functions.md#sha1) | Calcula la síntesis del mensaje SHA-1 del conjunto de columnas de diferentes tipos de datos primitivos y devuelve una cadena hexadecimal de 40 caracteres. Se puede usar para calcular una huella digital de una fila.  |
| [sha2](data-flow-expression-functions.md#sha2) | Calcula la síntesis del mensaje SHA-2 del conjunto de columnas de diferentes tipos de datos primitivos dada una longitud en bits, que solo puede tener los valores 0(256), 224, 256, 384 y 512. Se puede usar para calcular una huella digital de una fila.  |
| [sin](data-flow-expression-functions.md#sin) | Calcula un valor de seno.  |
| [sinh](data-flow-expression-functions.md#sinh) | Calcula un valor de seno hiperbólico.  |
| [soundex](data-flow-expression-functions.md#soundex) | Obtiene el código ```soundex``` de la cadena.  |
| [split](data-flow-expression-functions.md#split) | Divide una cadena según un delimitador y devuelve una matriz de cadenas.  |
| [sqrt](data-flow-expression-functions.md#sqrt) | Calcula la raíz cuadrada de un número.  |
| [startsWith](data-flow-expression-functions.md#startsWith) | Comprueba si la cadena comienza por la cadena proporcionada.  |
| [subDays](data-flow-expression-functions.md#subDays) | Resta días de una fecha o una marca de tiempo. Igual que el operador - para la fecha.  |
| [subMonths](data-flow-expression-functions.md#subMonths) | Resta meses a una fecha o marca de tiempo.  |
| [substring](data-flow-expression-functions.md#substring) | Extrae una subcadena de una determinada longitud de una posición. La posición se basa en 1. Si la longitud se omite, se establece el final de la cadena como valor predeterminado.  |
| [tan](data-flow-expression-functions.md#tan) | Calcula un valor de tangente.  |
| [tanh](data-flow-expression-functions.md#tanh) | Calcula un valor de tangente hiperbólica.  |
| [translate](data-flow-expression-functions.md#translate) | Reemplaza un conjunto de caracteres por otro conjunto de caracteres de la cadena. Los caracteres tienen un reemplazo de 1 a 1.  |
| [trim](data-flow-expression-functions.md#trim) | Recorta una cadena de caracteres iniciales y finales. Si no se especifica el segundo parámetro, recorta el espacio en blanco. Si no, recorta cualquier carácter especificado en el segundo parámetro.  |
| [true](data-flow-expression-functions.md#true) | Siempre devuelve un valor true. Use la función `syntax(true())` si hay una columna denominada "true".  |
| [typeMatch](data-flow-expression-functions.md#typeMatch) | Coincide con el tipo de la columna. Solo se puede usar en expresiones de patrón. "number" coincide con los tipos short, entero, long, double, float o decimal; "integral" coincide con los tipos short, entero y long; "fractional" coincide con los tipos double, float y decimal; "datetime" coincide con los tipos de date o timestamp.  |
| [unescape](data-flow-expression-functions.md#unescape) | Elimina el escape de una cadena según un formato. Los valores literales para el formato aceptable son "json", "xml", "ecmascript", "html", "java".|
| [upper](data-flow-expression-functions.md#upper) | Pone en mayúsculas una cadena.  |
| [uuid](data-flow-expression-functions.md#uuid) | Devuelve el UUID generado.  |
| [weekOfYear](data-flow-expression-functions.md#weekOfYear) | Obtiene la semana del año dada una fecha.  |
| [weeks](data-flow-expression-functions.md#weeks) | Duración en milisegundos para el número de semanas.  |
| [xor](data-flow-expression-functions.md#xor) | Operador XOR lógico. Igual que el operador ^.  |
| [year](data-flow-expression-functions.md#year) | Obtiene el valor de año de una fecha.  |
|||

## <a name="aggregate-functions"></a>Funciones de agregado
Las siguientes funciones solo están disponibles para las transformaciones de agregado, dinamización, anulación de dinamización y ventana.

| Aggregate, función | Tarea |
|----|----|
| [approxDistinctCount](data-flow-expression-functions.md#approxDistinctCount) | Obtiene el recuento agregado aproximado de valores distintos para una columna. El segundo parámetro opcional es controlar el error de estimación.|
| [avg](data-flow-expression-functions.md#avg) | Obtiene el promedio de valores de una columna.  |
| [avgIf](data-flow-expression-functions.md#avgIf) | En función de los criterios, obtiene el promedio de valores de una columna.  |
| [collect](data-flow-expression-functions.md#collect) | Recopila todos los valores de la expresión del grupo agregado en una matriz. Las estructuras se pueden recopilar y transformar en estructuras alternativas durante este proceso. El número de elementos será igual al número de filas de ese grupo y puede contener valores NULL. El número de elementos recopilados debe ser pequeño.  |
| [count](data-flow-expression-functions.md#count) | Obtiene el recuento agregado de valores. Si se especifican las columnas opcionales, omite los valores NULL en el recuento.  |
| [countDistinct](data-flow-expression-functions.md#countDistinct) | Obtiene el recuento agregado de valores distintos de un conjunto de columnas.  |
| [countIf](data-flow-expression-functions.md#countIf) | En función de los criterios, obtiene el recuento agregado de valores. Si se especifica la columna opcional, omite los valores NULL en el recuento.  |
| [covariancePopulation](data-flow-expression-functions.md#covariancePopulation) | Obtiene la covarianza de la población entre dos columnas.  |
| [covariancePopulationIf](data-flow-expression-functions.md#covariancePopulationIf) | En función de los criterios, obtiene la covarianza de la población de dos columnas.  |
| [covarianceSample](data-flow-expression-functions.md#covarianceSample) | Obtiene la covarianza de muestra de dos columnas.  |
| [covarianceSampleIf](data-flow-expression-functions.md#covarianceSampleIf) | En función de los criterios, obtiene la covarianza de muestra de dos columnas.  |
| [first](data-flow-expression-functions.md#first) | Obtiene el primer valor de un grupo de columnas. Si el segundo parámetro ignoreNulls se omite, se supone que es falso.  |
| [isDistinct](data-flow-expression-functions.md#isDistinct) | Busca si una columna o un conjunto de columnas es distinto. No cuenta NULL como valor distinto |
| [kurtosis](data-flow-expression-functions.md#kurtosis) | Obtiene la curtosis de una columna.  |
| [kurtosisIf](data-flow-expression-functions.md#kurtosisIf) | En función de los criterios, obtiene la curtosis de una columna.  |
| [last](data-flow-expression-functions.md#last) | Obtiene el último valor de un grupo de columnas. Si el segundo parámetro ignoreNulls se omite, se supone que es falso.  |
| [max](data-flow-expression-functions.md#max) | Obtiene el valor máximo de una columna.  |
| [maxIf](data-flow-expression-functions.md#maxIf) | En función de los criterios, obtiene el valor máximo de una columna.  |
| [mean](data-flow-expression-functions.md#mean) | Obtiene la media de valores de una columna. Igual que AVG.  |
| [meanIf](data-flow-expression-functions.md#meanIf) | En función de los criterios, obtiene la media de valores de una columna. Igual que avgIf.  |
| [min](data-flow-expression-functions.md#min) | Obtiene el valor mínimo de una columna.  |
| [minIf](data-flow-expression-functions.md#minIf) | En función de los criterios, obtiene el valor mínimo de una columna.  |
| [skewness](data-flow-expression-functions.md#skewness) | Obtiene la asimetría de una columna.  |
| [skewnessIf](data-flow-expression-functions.md#skewnessIf) | En función de los criterios, obtiene la asimetría de una columna.  |
| [stddev](data-flow-expression-functions.md#stddev) | Obtiene la desviación estándar de una columna.  |
| [stddevIf](data-flow-expression-functions.md#stddevIf) | En función de los criterios, obtiene la desviación estándar de una columna.  |
| [stddevPopulation](data-flow-expression-functions.md#stddevPopulation) | Obtiene la desviación estándar de población de una columna.  |
| [stddevPopulationIf](data-flow-expression-functions.md#stddevPopulationIf) | En función de los criterios, obtiene la desviación estándar de población de una columna.  |
| [stddevSample](data-flow-expression-functions.md#stddevSample) | Obtiene la desviación estándar de muestra de una columna.  |
| [stddevSampleIf](data-flow-expression-functions.md#stddevSampleIf) | En función de los criterios, obtiene la desviación estándar de muestra de una columna.  |
| [sum](data-flow-expression-functions.md#sum) | Obtiene la suma de agregados de una columna numérica.  |
| [sumDistinct](data-flow-expression-functions.md#sumDistinct) | Obtiene la suma de agregados de valores distintos de una columna numérica.  |
| [sumDistinctIf](data-flow-expression-functions.md#sumDistinctIf) | En función de los criterios, obtiene la suma de agregados de una columna numérica. La condición se puede basar en cualquier columna.  |
| [sumIf](data-flow-expression-functions.md#sumIf) | En función de los criterios, obtiene la suma de agregados de una columna numérica. La condición se puede basar en cualquier columna.  |
| [variance](data-flow-expression-functions.md#variance) | Obtiene la varianza de una columna.  |
| [varianceIf](data-flow-expression-functions.md#varianceIf) | En función de los criterios, obtiene la varianza de una columna.  |
| [variancePopulation](data-flow-expression-functions.md#variancePopulation) | Obtiene la varianza de población de una columna.  |
| [variancePopulationIf](data-flow-expression-functions.md#variancePopulationIf) | En función de los criterios, obtiene la varianza de población de una columna.  |
| [varianceSample](data-flow-expression-functions.md#varianceSample) | Obtiene la varianza no sesgada de una columna.  |
| [varianceSampleIf](data-flow-expression-functions.md#varianceSampleIf) | En función de los criterios, obtiene la varianza no sesgada de una columna.  |
|||

## <a name="array-functions"></a>Funciones de matriz
Las funciones de matriz realizan transformaciones sobre estructuras de datos que son matrices. Estas incluyen palabras clave especiales para direccionar elementos de matriz e índices:

* ```#acc``` representa un valor que se desea incluir en la salida única al reducir una matriz.
* ```#index``` representa el índice de la matriz actual, junto con los números de índice de la matriz ```#index2, #index3 ...```.
* ```#item``` representa el valor del elemento actual de la matriz.

| Función de matriz | Tarea |
|----|----|
| [array](data-flow-expression-functions.md#array) | Crea una matriz de elementos. Todos los elementos deben ser del mismo tipo. Si no se especifica ningún elemento, una matriz de cadenas vacías es el valor predeterminado. Igual que un operador de creación [].  |
| [at](data-flow-expression-functions.md#at) | Busca el elemento en un índice de matrices. El índice es de base 1. El índice fuera de los límites da como resultado un valor NULL. Busca un valor en una asignación dada una clave. Si no se encuentra la clave, devuelve NULL.|
| [contains](data-flow-expression-functions.md#contains) | Devuelve true si algún elemento de la matriz proporcionada se evalúa como true en el predicado proporcionado. "Contains" espera una referencia a un elemento de la función de predicado como #item.  |
| [distinct](data-flow-expression-functions.md#distinct) | Devuelve un conjunto distinto de elementos de una matriz.|
| [except](data-flow-expression-functions.md#except) | Devuelve un conjunto de diferencias de una matriz desde otros duplicados eliminados.|
| [filter](data-flow-expression-functions.md#filter) | Filtra los elementos de la matriz que no cumplen el predicado proporcionado. "Filter" espera una referencia a un elemento de la función de predicado como #item.  |
| [find](data-flow-expression-functions.md#find) | Find the first item from an array that match the condition. It takes a filter function where you can address the item in the array as #item. For deeply nested maps you can refer to the parent maps using the #item_n(#item_1, #item_2...) notation.  |
| [flatten](data-flow-expression-functions.md#flatten) | Acopla una o varias matrices en una sola. Las matrices de elementos atómicos se devuelven sin modificar. El último argumento es opcional y su valor predeterminado es false para acoplar de manera recursiva más de un nivel de profundidad.|
| [in](data-flow-expression-functions.md#in) | Comprueba si un elemento está en la matriz.  |
| [intersect](data-flow-expression-functions.md#intersect) | Devuelve un conjunto de intersección de elementos distintos de dos matrices.|
| [map](data-flow-expression-functions.md#map) | Asigna cada elemento de la matriz a un nuevo elemento mediante la expresión proporcionada. La asignación espera una referencia a un elemento de la función de expresión como #item.  |
| [mapIf](data-flow-expression-functions.md#mapIf) | Asigna de manera condicional una matriz a otra de la misma longitud o más corta. The values can be of any datatype including structTypes. It takes a mapping function where you can address the item in the array as #item and current index as #index. En el caso de asignaciones con anidaciones muy profundas, puede consultar las asignaciones primarias mediante la notación ``#item_[n](#item_1, #index_1...)``.|
| [mapIndex](data-flow-expression-functions.md#mapIndex) | Asigna cada elemento de la matriz a un nuevo elemento mediante la expresión proporcionada. Map espera una referencia a un elemento en la función de expresión como #item y una referencia al índice del elemento como #index.  |
| [mapLoop](data-flow-expression-functions.md#mapLoop) | Produce un bucle desde 1 hasta la longitud para crear una matriz de esa longitud. It takes a mapping function where you can address the index in the array as #index. En el caso de asignaciones con anidaciones muy profundas, puede consultar las asignaciones primarias mediante la notación #index_n(#index_1, #index_2...).|
| [reduce](data-flow-expression-functions.md#reduce) | Acumula los elementos de una matriz. "Reduce" espera una referencia a un acumulador y a un elemento en la primera función de expresión, como #acc y #item, y espera que el valor resultante sea #result para poder usarlo en la segunda función de expresión.  |
| [size](data-flow-expression-functions.md#size) | Busca el tamaño de una matriz o un tipo de asignación.  |
| [slice](data-flow-expression-functions.md#slice) | Extrae un subconjunto de una matriz de una posición. La posición se basa en 1. Si la longitud se omite, se establece el final de la cadena como valor predeterminado.  |
| [sort](data-flow-expression-functions.md#sort) | Ordena la matriz mediante la función de predicado proporcionada. "Sort" espera una referencia a dos elementos consecutivos de la función de expresión, como #item1 y #item2.  |
| [unfold](data-flow-expression-functions.md#unfold) | Despliega una matriz en un conjunto de filas y repite los valores de las columnas restantes en cada fila.|
| [union](data-flow-expression-functions.md#union) | Devuelve un conjunto de uniones de elementos distintos de dos matrices.|
|||

## <a name="cached-lookup-functions"></a>Funciones de búsqueda almacenadas en caché
Las siguientes funciones solo están disponibles cuando se usa una búsqueda almacenada en caché y ha incluido un receptor almacenado en caché.

| Función de búsqueda almacenada en caché | Tarea |
|----|----|
| [lookup](data-flow-expression-functions.md#lookup) | Busca la primera fila del receptor almacenado en caché con las claves especificadas, que coinciden con las claves del receptor almacenado en caché.|
| [mlookup](data-flow-expression-functions.md#mlookup) | Busca todas las filas coincidentes del receptor almacenado en caché con las claves especificadas, que coinciden con las claves del receptor almacenado en caché.|
| [output](data-flow-expression-functions.md#output) | Devuelve la primera fila de los resultados del receptor almacenado en caché .|
| [outputs](data-flow-expression-functions.md#outputs) | Devuelve el conjunto completo de filas de salida de los resultados del receptor almacenado en caché .|
|||

## <a name="conversion-functions"></a>Funciones de conversión

Las funciones de conversión se usan para convertir datos y probar tipos de datos.

| Función de conversión | Tarea |
|----|----|
| [isBitSet](data-flow-expression-functions.md#isBitSet) | Comprueba si se establece una posición de bit en este conjunto de bits .|
| [setBitSet](data-flow-expression-functions.md#setBitSet) | Establece las posiciones de bit en este conjunto de bits .|
| [isBoolean](data-flow-expression-functions.md#isBoolean) | Permite comprobar si el valor de la cadena es booleano según las reglas de ``toBoolean()``.|
| [isByte](data-flow-expression-functions.md#isByte) | Permite comprobar si el valor de la cadena es un byte con un formato opcional según las reglas de ``toByte()``.|
| [isDate](data-flow-expression-functions.md#isDate) | Permite comprobar si la cadena de fecha de entrada es una fecha con un formato de fecha de entrada opcional. Consulte SimpleDateFormat de Java para conocer los formatos disponibles. Si se omite el formato de fecha de entrada, el formato predeterminado es ``yyyy-[M]M-[d]d``. Los formatos aceptados son ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``.|
| [isShort](data-flow-expression-functions.md#isShort) | Permite comprobar si el valor de la cadena es un elemento corto con un formato opcional según las reglas de ``toShort()``.|
| [isInteger](data-flow-expression-functions.md#isInteger) | Permite comprobar si el valor de la cadena es un elemento entero con un formato opcional según las reglas de ``toInteger()``.|
| [isLong](data-flow-expression-functions.md#isLong) | Permite comprobar si el valor de la cadena es un elemento largo con un formato opcional según las reglas de ``toLong()``.|
| [isNan](data-flow-expression-functions.md#isNan) | Comprueba si no es un número.|
| [isFloat](data-flow-expression-functions.md#isFloat) | Permite comprobar si el valor de la cadena es un elemento flotante con un formato opcional según las reglas de ``toFloat()``.|
| [isDouble](data-flow-expression-functions.md#isDouble) | Permite comprobar si el valor de la cadena es un elemento doble con un formato opcional según las reglas de ``toDouble()``.|
| [isDecimal](data-flow-expression-functions.md#isDecimal) | Permite comprobar si el valor de la cadena es un elemento decimal con un formato opcional según las reglas de ``toDecimal()``.|
| [isTimestamp](data-flow-expression-functions.md#isTimestamp) | Permite comprobar si la cadena de fecha de entrada es una marca de tiempo con un formato de marca de tiempo de entrada opcional. Consulte la clase SimpleDateFormat de Java para conocer los formatos disponibles. Si la marca de tiempo se omite, se usa el patrón predeterminado ``yyyy-[M]M-[d]d hh:mm:ss[.f...]``. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La marca de tiempo admite una precisión de hasta milisegundos con un valor de 999. Consulte la clase SimpleDateFormat de Java para conocer los formatos disponibles.|
| [toBase64](data-flow-expression-functions.md#toBase64) | Codifica la cadena especificada en Base64.  |
| [toBinary](data-flow-expression-functions.md#toBinary) | Convierte cualquier valor numérico/fecha/marca de tiempo/cadena en una representación binaria.  |
| [toBoolean](data-flow-expression-functions.md#toBoolean) | Convierte un valor de (" t", "true", "y", "yes", "1") en true y ("f", "false", "n", "no", "0") en false y NULL para cualquier otro valor.  |
| [toByte](data-flow-expression-functions.md#toByte) | Convierte cualquier valor numérico o cadena en un valor de tipo byte. Se puede utilizar un formato decimal de Java opcional para la conversión.  |
| [toDate](data-flow-expression-functions.md#toDate) | Convierte la cadena de fecha de entrada en fecha con un formato de fecha de entrada opcional. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. Si se omite el formato de fecha de entrada, el formato predeterminado es aaaa-[M]M-[d]d. Los formatos aceptados son :[ aaaa, aaaa-[M]M, aaaa-[M]M-[d]d, aaaa-[M]M-[d]dH* ].  |
| [toDecimal](data-flow-expression-functions.md#toDecimal) | Convierte cualquier valor numérico o cadena en un valor decimal. Si no se especifican la precisión y la escala, se utiliza el valor predeterminado (10,2). Se puede utilizar un formato decimal de Java opcional para la conversión. Formato de configuración regional opcional en el formulario del lenguaje BCP47, como en-US, de, zh-CN.  |
| [toDouble](data-flow-expression-functions.md#toDouble) | Convierte cualquier valor numérico o cadena en un valor doble. Se puede utilizar un formato decimal de Java opcional para la conversión. Formato de configuración regional opcional en el formulario del lenguaje BCP47, como en-US, de, zh-CN.  |
| [toFloat](data-flow-expression-functions.md#toFloat) | Convierte cualquier valor numérico o cadena en un valor flotante. Se puede utilizar un formato decimal de Java opcional para la conversión. Trunca cualquier valor doble.  |
| [toInteger](data-flow-expression-functions.md#toInteger) | Convierte cualquier valor numérico o cadena en un valor entero. Se puede utilizar un formato decimal de Java opcional para la conversión. Trunca cualquier valor long, float o double.  |
| [toLong](data-flow-expression-functions.md#toLong) | Convierte cualquier valor numérico o cadena en un valor largo. Se puede utilizar un formato decimal de Java opcional para la conversión. Trunca cualquier valor float o double.  |
| [toShort](data-flow-expression-functions.md#toShort) | Convierte cualquier valor numérico o cadena en un valor corto. Se puede utilizar un formato decimal de Java opcional para la conversión. Trunca cualquier valor entero, long, float o double.  |
| [toString](data-flow-expression-functions.md#toString) | Convierte un tipo de datos primitivo en una cadena. Para los números y la fecha se puede especificar un formato. Si no se especifica, se elige el valor predeterminado del sistema. Para los números, se usa el formato decimal de Java. Consulte el formato SimpleDateFormat de Java para ver todos los formatos de fecha posibles; el formato predeterminado es aaaa-MM-dd.  |
| [toTimestamp](data-flow-expression-functions.md#toTimestamp) | Convierte una cadena en una marca de tiempo dado un formato de marca de tiempo opcional. Si la marca de tiempo se omite, se usa el patrón predeterminado aaaa-[M]M-[d]d hh:mm:ss[.f...]. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La marca de tiempo admite una precisión de hasta milisegundos, con el valor de 999. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
| [toUTC](data-flow-expression-functions.md#toUTC) | Convierte la marca de tiempo en UTC. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria actual se establece como predeterminada. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  |
|||

## <a name="map-functions"></a>Funciones de asignación

  Las funciones de asignación realizan operaciones en tipos de datos de asignación

| Función de asignación | Tarea |
|----|----|
| [associate](data-flow-expression-functions.md#associate) | Crea una asignación de clave/valores. Todas las claves y valores deben ser del mismo tipo. Si no se especifica ningún elemento, su valor predeterminado es una asignación de cadena a tipo de cadena. Igual que un operador de creación ```[ -> ]```. Las claves y los valores deben alternarse.|
| [keyValues](data-flow-expression-functions.md#keyValues) | Crea una asignación de clave/valores. El primer parámetro es una matriz de claves y el segundo es la matriz de valores. Ambas matrices deben tener la misma longitud.|
| [mapAssociation](data-flow-expression-functions.md#mapAssociation) | Transforma una asignación al asociar las claves a nuevos valores. Devuelve una matriz. Toma una función de asignación donde se puede hacer referencia al elemento como #key y al índice actual como #value. |
| [reassociate](data-flow-expression-functions.md#reassociate) | Transforma una asignación al asociar las claves a nuevos valores. Toma una función de asignación donde se puede hacer referencia al elemento como #key y al índice actual como #value.  |
|||

## <a name="metafunctions"></a>Metafunciones

Las metafunciones aparecen principalmente en los metadatos del flujo de datos.

| Metafunción  | Tarea |
|----|----|
| [byItem](data-flow-expression-functions.md#byItem) | Busque un subelemento en una estructura o matriz de estructuras; si hay varias coincidencias, se devuelve la primera coincidencia. Si no hay coincidencias, se devuelve un valor NULL. El tipo del valor devuelto debe haberse convertido mediante una de las acciones de conversión de tipo (? fecha, ? cadena ...).  Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  |
| [byOrigin](data-flow-expression-functions.md#byOrigin) | Selecciona un valor de columna en función del nombre de la secuencia de origen. El segundo argumento es el nombre de la secuencia de origen. Si hay varias coincidencias, se devuelve la primera. Si no hay coincidencias, se devuelve un valor NULL. El tipo del valor devuelto debe haberse convertido mediante una de las funciones de conversión de tipo (TO_DATE, TO_STRING...). Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  |
| [byOrigins](data-flow-expression-functions.md#byOrigins) | Selecciona una matriz de columnas en función del nombre en la secuencia. El segundo argumento es la secuencia desde la que se originó. Si hay varias coincidencias, se devuelve la primera. Si no hay coincidencias, se devuelve un valor NULL. El tipo del valor devuelto debe haberse convertido mediante una de las funciones de conversión de tipo (TO_DATE, TO_STRING...). Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.|
| [byName](data-flow-expression-functions.md#byName) | Selecciona un valor de columna por el nombre en la secuencia. Puede pasar un nombre de flujo opcional como segundo argumento. Si hay varias coincidencias, se devuelve la primera. Si no hay coincidencias, se devuelve un valor NULL. El tipo del valor devuelto debe haberse convertido mediante una de las funciones de conversión de tipo (TO_DATE, TO_STRING...).  Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  |
| [byNames](data-flow-expression-functions.md#byNames) | Seleccione una matriz de columnas por nombre en la secuencia. Puede pasar un nombre de flujo opcional como segundo argumento. Si hay varias coincidencias, se devuelve la primera. Si no hay ninguna coincidencia para una columna, la salida completa es un valor NULL. El valor devuelto requiere una función de conversión de tipos (toDate, toString, etc.).  Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.|
| [byPath](data-flow-expression-functions.md#byPath) | Busca una ruta de acceso jerárquica por nombre en la secuencia. Puede pasar un nombre de secuencia opcional como segundo argumento. Si no se encuentra ninguna ruta de acceso, devuelve el valor NULL. Los nombres de columna o las rutas de acceso que se conocen en tiempo de diseño deben usarse solo mediante su nombre o mediante la ruta de acceso de la notación de puntos. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  |
| [byPosition](data-flow-expression-functions.md#byPosition) | Selecciona un valor de columna por su posición relativa (de base 1) en la secuencia. Si la posición está fuera de los límites, devuelve un valor Null. El tipo del valor devuelto debe haberse convertido mediante una de las funciones de conversión de tipo (TO_DATE, TO_STRING...). No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  |
| [hasPath](data-flow-expression-functions.md#hasPath) | Comprueba si existe una determinada ruta de acceso jerárquica por nombre en la secuencia. Puede pasar un nombre de secuencia opcional como segundo argumento. Los nombres de columna o las rutas de acceso que se conocen en tiempo de diseño deben usarse solo mediante su nombre o mediante la ruta de acceso de la notación de puntos. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  |
| [originColumns](data-flow-expression-functions.md#originColumns) | Obtiene todas las columnas de salida de un flujo de origen donde se crearon columnas. Debe incluirse en otra función.|
| [hex](data-flow-expression-functions.md#hex) | Devuelve una representación de cadena hexa de un valor binario .|
| [unhex](data-flow-expression-functions.md#unhex) | Deshace la representación hexa de un valor binario de su representación de cadena. Se puede usar junto con SHA2, MD5 para convertir de cadena a representación binaria .|
|||

## <a name="window-functions"></a>Funciones de ventana

Las siguientes funciones solo están disponibles en las transformaciones de ventana.

| Función de ventana | Tarea |
|----|----|
| [cumeDist](data-flow-expression-functions.md#cumeDist) | La función CumeDist calcula la posición de un valor respecto a todos los valores de la partición. El resultado es el número de filas anteriores o iguales a la fila actual en la ordenación de la partición dividido por el número total de filas de la partición de la ventana. Cualquier valor de empate en la ordenación se evaluará en la misma posición.  |
| [denseRank](data-flow-expression-functions.md#denseRank) | Calcula el rango de un valor en un grupo de valores especificado en la cláusula order by de una ventana. El resultado es uno más el número de filas anteriores o iguales a la fila actual en la ordenación de la partición. Los valores no generarán intervalos en la secuencia. La clasificación densa funciona incluso cuando los datos no están ordenados y busca cambios en los valores.  |
| [lag](data-flow-expression-functions.md#lag) | Obtiene el valor del primer parámetro evaluado n filas antes de la fila actual. El segundo parámetro es el número de filas que se debe mirar hacia atrás, y el valor predeterminado es 1. Si no hay tantas filas, se devuelve un valor NULL, a menos que se especifique un valor predeterminado.  |
| [lead](data-flow-expression-functions.md#lead) | Obtiene el valor del primer parámetro evaluado n filas después de la fila actual. El segundo parámetro es el número de filas que se debe mirar hacia adelante, y el valor predeterminado es 1. Si no hay tantas filas, se devuelve un valor NULL, a menos que se especifique un valor predeterminado.  |
| [nTile](data-flow-expression-functions.md#nTile) | La función ```NTile``` divide las filas para cada partición de ventana en `n` cubos comprendidos entre los valores 1 y `n` como máximo. Los valores de cubo variarán en 1 como máximo. Si el número de filas de la partición no se divide uniformemente en el número de cubos, los valores del resto se distribuyen uno por cubo, empezando por el primer cubo. La función ```NTile``` es útil para el cálculo de ```tertiles```, cuartiles, deciles y otras estadísticas de resumen comunes. La función calcula dos variables durante la inicialización: El tamaño de un cubo normal tendrá una fila extra agregada. Ambas variables se basan en el tamaño de la partición actual. Durante el proceso de cálculo, la función realiza un seguimiento del número de fila actual, el número de cubo actual y el número de fila en el que el cubo cambiará (bucketThreshold). Cuando el número de la fila actual alcance el umbral del cubo, el valor del cubo se incrementa en uno y el umbral aumentará el tamaño del cubo (más uno adicional si el cubo actual se rellena).  |
| [rank](data-flow-expression-functions.md#rank) | Calcula el rango de un valor en un grupo de valores especificado en la cláusula order by de una ventana. El resultado es uno más el número de filas anteriores o iguales a la fila actual en la ordenación de la partición. Los valores generarán intervalos en la secuencia. La clasificación funciona incluso cuando no está ordenada y busca cambios en los valores.  |
| [rowNumber](data-flow-expression-functions.md#rowNumber) | Asigna una numeración secuencial de filas para las filas en una ventana que comienzan con 1.  |
|||

## <a name="alphabetical-listing-of-all-functions"></a>Lista alfabética de todas las funciones

A continuación, se muestra una lista alfabética de todas las funciones disponibles en los flujos de datos de asignación.

<a name="abs" ></a>

### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
El valor absoluto de un número.  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   


<a name="acos" ></a>

### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un valor inverso de coseno.  
* ``acos(1) -> 0.0``  
___


<a name="add" ></a>

### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Agrega un par de cadenas o números. Agrega una fecha a un número de días. Agrega una vigencia a una marca de tiempo. Anexa una matriz de tipo similar a otra. Igual que el operador +.  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="addDays" ></a>

### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Agrega días a una fecha o marca de tiempo. Igual que el operador + para la fecha.  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___


<a name="addMonths" ></a>

### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Suma meses a una fecha o marca de tiempo. Opcionalmente, puede pasar una zona horaria.  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___


<a name="and" ></a>

### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador lógico AND. Igual que &&.  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___


<a name="approxDistinctCount" ></a>

### <code>approxDistinctCount</code>
<code><b>approxDistinctCount(<i>&lt;value1&gt;</i> : any, [ <i>&lt;value2&gt;</i> : double ]) => long</b></code><br/><br/>
Obtiene el recuento agregado aproximado de valores distintos para una columna. El segundo parámetro opcional es controlar el error de estimación.
* ``approxDistinctCount(ProductID, .05) => long``  
___


<a name="array" ></a>

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
Crea una matriz de elementos. Todos los elementos deben ser del mismo tipo. Si no se especifica ningún elemento, una matriz de cadenas vacías es el valor predeterminado. Igual que un operador de creación [].  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___


<a name="asin" ></a>

### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un valor inverso de seno.  
* ``asin(0) -> 0.0``  
___


<a name="associate" ></a>

### <code>associate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
Crea una asignación de clave/valores. Todas las claves y valores deben ser del mismo tipo. Si no se especifica ningún elemento, su valor predeterminado es una asignación de cadena a tipo de cadena. Igual que un operador de creación ```[ -> ]```. Las claves y los valores deben alternarse.
*   ``associate('fruit', 'apple', 'vegetable', 'carrot' )=> ['fruit' -> 'apple', 'vegetable' -> 'carrot']``
___


<a name="at" ></a>

### <code>at</code>
<code><b>at(<i>&lt;value1&gt;</i> : array/map, <i>&lt;value2&gt;</i> : integer/key type) => array</b></code><br/><br/>
Busca el elemento en un índice de matrices. El índice es de base 1. El índice fuera de los límites da como resultado un valor NULL. Busca un valor en una asignación dada una clave. Si no se encuentra la clave, devuelve NULL.
*   ``at(['apples', 'pears'], 1) => 'apples'``
*   ``at(['fruit' -> 'apples', 'vegetable' -> 'carrot'], 'fruit') => 'apples'``
___


<a name="atan" ></a>

### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un valor inverso de tangente.  
* ``atan(0) -> 0.0``  
___


<a name="atan2" ></a>

### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Devuelve el ángulo en radianes entre el eje X positivo de un plano y el punto que especificaron las coordenadas.  
* ``atan2(0, 0) -> 0.0``  
___


<a name="avg" ></a>

### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtiene el promedio de valores de una columna.  
* ``avg(sales)``  
___


<a name="avgIf" ></a>

### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
En función de los criterios, obtiene el promedio de valores de una columna.  
* ``avgIf(region == 'West', sales)``  
___


<a name="between" ></a>

### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
Comprueba si el primer valor se encuentra entre otros dos valores, ambos inclusive. Se pueden comparar valores numéricos, de cadena y de fecha y hora * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___


<a name="bitwiseAnd" ></a>

### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Operador AND bit a bit entre tipos enteros. Igual que el operador & * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___


<a name="bitwiseOr" ></a>

### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Operador OR bit a bit entre tipos enteros. Igual que el operador | * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___


<a name="bitwiseXor" ></a>

### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Operador OR bit a bit entre tipos enteros. Igual que el operador | * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___


<a name="blake2b" ></a>

### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula la síntesis del mensaje Blake2 del conjunto de columnas de diferentes tipos de datos primitivos dada una longitud en bits que solo pueden ser múltiplos de 8 entre 8 y 512. Se puede usar para calcular una huella digital de una fila. * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___


<a name="blake2bBinary" ></a>

### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
Calcula la síntesis del mensaje Blake2 del conjunto de columnas de diferentes tipos de datos primitivos dada una longitud en bits que solo pueden ser múltiplos de 8 entre 8 y 512. Se puede usar para calcular una huella digital de una fila. * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___


<a name="byItem" ></a>

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Busque un subelemento en una estructura o matriz de estructuras; si hay varias coincidencias, se devuelve la primera coincidencia. Si no hay coincidencias, se devuelve un valor NULL. El tipo del valor devuelto debe haberse convertido mediante una de las acciones de conversión de tipo (? fecha, ? cadena ...).  Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros. * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
___


<a name="byName" ></a>

### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Selecciona un valor de columna por el nombre en la secuencia. Puede pasar un nombre de flujo opcional como segundo argumento. Si hay varias coincidencias, se devuelve la primera. Si no hay coincidencias, se devuelve un valor NULL. El tipo del valor devuelto debe haberse convertido mediante una de las funciones de conversión de tipo (TO_DATE, TO_STRING...).  Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___


<a name="byNames" ></a>

### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Seleccione una matriz de columnas por nombre en la secuencia. Puede pasar un nombre de flujo opcional como segundo argumento. Si hay varias coincidencias, se devuelve la primera. Si no hay ninguna coincidencia para una columna, la salida completa es un valor NULL. El valor devuelto requiere una función de conversión de tipos (toDate, toString, etc.).  Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___


<a name="byOrigin" ></a>

### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Selecciona un valor de columna en función del nombre de la secuencia de origen. El segundo argumento es el nombre de la secuencia de origen. Si hay varias coincidencias, se devuelve la primera. Si no hay coincidencias, se devuelve un valor NULL. El tipo del valor devuelto debe haberse convertido mediante una de las funciones de conversión de tipo (TO_DATE, TO_STRING...). Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___


<a name="byOrigins" ></a>

### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Selecciona una matriz de columnas en función del nombre en la secuencia. El segundo argumento es la secuencia desde la que se originó. Si hay varias coincidencias, se devuelve la primera. Si no hay coincidencias, se devuelve un valor NULL. El tipo del valor devuelto debe haberse convertido mediante una de las funciones de conversión de tipo (TO_DATE, TO_STRING...). Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___


<a name="byPath" ></a>

### <code>byPath</code>
<code><b>byPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => any</b></code><br/><br/>
Busca una ruta de acceso jerárquica por nombre en la secuencia. Puede pasar un nombre de secuencia opcional como segundo argumento. Si no se encuentra ninguna ruta de acceso, devuelve el valor NULL. Los nombres de columna o las rutas de acceso que se conocen en tiempo de diseño deben usarse solo mediante su nombre o mediante la ruta de acceso de la notación de puntos. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  
* ``byPath('grandpa.parent.child') => column`` 
___


<a name="byPosition" ></a>

### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Selecciona un valor de columna por su posición relativa (de base 1) en la secuencia. Si la posición está fuera de los límites, devuelve un valor Null. El tipo del valor devuelto debe haberse convertido mediante una de las funciones de conversión de tipo (TO_DATE, TO_STRING...). No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  
___


<a name="case" ></a>

### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Basándose en una condición alternativa, aplica un valor o el otro. Si el número de entradas es par, el otro se establece de manera predeterminada en NULL para la última condición.  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___


<a name="cbrt" ></a>

### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula la raíz cúbica de un número.  
* ``cbrt(8) -> 2.0``  
___


<a name="ceil" ></a>

### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Devuelve el entero más pequeño no menor que el número.  
* ``ceil(-0.1) -> 0``  
___


<a name="coalesce" ></a>

### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Devuelve el primer valor no NULL de un conjunto de entradas. Todas las entradas deben ser del mismo tipo.  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___


<a name="collect" ></a>

### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
Recopila todos los valores de la expresión del grupo agregado en una matriz. Las estructuras se pueden recopilar y transformar en estructuras alternativas durante este proceso. El número de elementos será igual al número de filas de ese grupo y puede contener valores NULL. El número de elementos recopilados debe ser pequeño.  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___


<a name="columnNames" ></a>

### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
Obtiene los nombres de todas las columnas de salida de una secuencia. Puede pasar un nombre de secuencia opcional como segundo argumento.  
* ``columnNames()``
* ``columnNames('DeriveStream')``
___


<a name="columns" ></a>

### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Obtiene los valores de todas las columnas de salida de una secuencia. Puede pasar un nombre de secuencia opcional como segundo argumento.   
* ``columns()``
* ``columns('DeriveStream')``
___


<a name="compare" ></a>

### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Compara dos valores del mismo tipo. Devuelve un entero negativo si value1 < value2, 0 si value1 == value2, un valor positivo si value1 > value2.  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___


<a name="concat" ></a>

### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena un número variable de cadenas. Igual que el operador + con cadenas.  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___


<a name="concatWS" ></a>

### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Concatena un número variable de cadenas con un separador. El primer parámetro es el separador.  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___


<a name="contains" ></a>

### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Devuelve true si algún elemento de la matriz proporcionada se evalúa como true en el predicado proporcionado. "Contains" espera una referencia a un elemento de la función de predicado como #item.  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___


<a name="cos" ></a>

### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un valor de coseno.  
* ``cos(10) -> -0.8390715290764524``  
___


<a name="cosh" ></a>

### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un coseno hiperbólico de un valor.  
* ``cosh(0) -> 1.0``  
___


<a name="count" ></a>

### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Obtiene el recuento agregado de valores. Si se especifican las columnas opcionales, omite los valores NULL en el recuento.  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___


<a name="countDistinct" ></a>

### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Obtiene el recuento agregado de valores distintos de un conjunto de columnas.  
* ``countDistinct(custId, custName)``  
___


<a name="countIf" ></a>

### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
En función de los criterios, obtiene el recuento agregado de valores. Si se especifica la columna opcional, omite los valores NULL en el recuento.  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___


<a name="covariancePopulation" ></a>

### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la covarianza de la población entre dos columnas.  
* ``covariancePopulation(sales, profit)``  
___


<a name="covariancePopulationIf" ></a>

### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la covarianza de la población de dos columnas.  
* ``covariancePopulationIf(region == 'West', sales)``  
___


<a name="covarianceSample" ></a>

### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la covarianza de muestra de dos columnas.  
* ``covarianceSample(sales, profit)``  
___


<a name="covarianceSampleIf" ></a>

### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la covarianza de muestra de dos columnas.  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___



<a name="crc32" ></a>

### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Calcula la síntesis del mensaje CRC32 del conjunto de columnas de diferentes tipos de datos primitivos dada una longitud en bits, que solo puede tener los valores 0(256), 224, 256, 384 y 512. Se puede usar para calcular una huella digital de una fila.  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___


<a name="cumeDist" ></a>

### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
La función CumeDist calcula la posición de un valor respecto a todos los valores de la partición. El resultado es el número de filas anteriores o iguales a la fila actual en la ordenación de la partición dividido por el número total de filas de la partición de la ventana. Cualquier valor de empate en la ordenación se evaluará en la misma posición.  
* ``cumeDist()``  
___


<a name="currentDate" ></a>

### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Obtiene la fecha actual cuando este trabajo empieza a ejecutarse. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___


<a name="currentTimestamp" ></a>

### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Obtiene la marca de tiempo actual cuando se inicia el trabajo para ejecutarse con la zona horaria local.  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___


<a name="currentUTC" ></a>

### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Obtiene la marca de tiempo actual como hora UTC. Si quiere que la hora actual se interprete en una zona horaria distinta a la del clúster, puede pasar una zona horaria opcional con el formato "GMT", "PST", "UTC ", "America/Caimán". La zona horaria actual se establece como predeterminada. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Para convertir la zona horaria UTC a una distinta, use `fromUTC()`.  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="dayOfMonth" ></a>

### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtiene el día del mes dada una fecha.  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___


<a name="dayOfWeek" ></a>

### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtiene el día de la semana dada una fecha. 1 corresponde al domingo, 2 al lunes... y 7 al sábado.  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___


<a name="dayOfYear" ></a>

### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtiene el día del año dada una fecha.  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___


<a name="days" ></a>

### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duración en milisegundos para el número de días.  
* ``days(2) -> 172800000L``  
___


<a name="degrees" ></a>

### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Convierte radianes en grados.  
* ``degrees(3.141592653589793) -> 180``  
___


<a name="denseRank" ></a>

### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Calcula el rango de un valor en un grupo de valores especificado en la cláusula order by de una ventana. El resultado es uno más el número de filas anteriores o iguales a la fila actual en la ordenación de la partición. Los valores no generarán intervalos en la secuencia. La clasificación densa funciona incluso cuando los datos no están ordenados y busca cambios en los valores.  
* ``denseRank()``  
___


<a name="distinct" ></a>

### <code>distinct</code>
<code><b>distinct(<i>&lt;value1&gt;</i> : array) => array</b></code><br/><br/>
Devuelve un conjunto distinto de elementos de una matriz.
* ``distinct([10, 20, 30, 10]) => [10, 20, 30]``
___


<a name="divide" ></a>

### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Divide dos números. Igual que el operador `/`.  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``
___


<a name="dropLeft" ></a>

### <code>dropLeft</code>
<code><b>dropLeft(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
Quita los mismos caracteres de la izquierda de la cadena. Si la colocación solicitada supera la longitud de la cadena, se devuelve una cadena vacía.
*   dropLeft('bojjus', 2) => 'jjus' *   dropLeft('cake', 10) => '' ___


<a name="dropRight" ></a>

### <code>dropRight</code>
<code><b>dropRight(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : integer) => string</b></code><br/><br/>
Quita los mismos caracteres de la derecha de la cadena. Si la colocación solicitada supera la longitud de la cadena, se devuelve una cadena vacía.
*   dropRight('bojjus', 2) => 'bojj' *   dropRight('cake', 10) => '' ___


<a name="endsWith" ></a>

### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Comprueba si la cadena finaliza con la cadena proporcionada.  
* ``endsWith('dumbo', 'mbo') -> true``  
___


<a name="equals" ></a>

### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparación igual que. Igual que el operador ==.  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___


<a name="equalsIgnoreCase" ></a>

### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Operador de comparación igual que, sin distinción entre mayúsculas y minúsculas. Igual que el operador <=>.  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___


<a name="escape" ></a>

### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Escapa una cadena según un formato. Los valores literales para el formato aceptable son "json", "xml", "ecmascript", "html", "java".
___


<a name="except" ></a>

### <code>except</code>
<code><b>except(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
Devuelve un conjunto de diferencias de una matriz desde otros duplicados eliminados.
* ``except([10, 20, 30], [20, 40]) => [10, 30]``  
___


<a name="expr" ></a>

### <code>expr</code>
<code><b>expr(<i>&lt;expr&gt;</i> : string) => any</b></code><br/><br/>
Da como resultado una expresión de una cadena. Esto es lo mismo que escribir esta expresión de forma no literal. Se puede usar para pasar parámetros como representaciones de cadena.
*   expr(‘price * discount’) => any ___


<a name="factorial" ></a>

### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Calcula el valor factorial de un número.  
* ``factorial(5) -> 120``  
___


<a name="false" ></a>

### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Siempre devuelve un valor false. Utilice la función `syntax(false())` si hay una columna denominada "false".  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___


<a name="filter" ></a>

### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Filtra los elementos de la matriz que no cumplen el predicado proporcionado. "Filter" espera una referencia a un elemento de la función de predicado como #item.  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___


<a name="find" ></a>

### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Find the first item from an array that match the condition. It takes a filter function where you can address the item in the array as #item. For deeply nested maps you can refer to the parent maps using the #item_n(#item_1, #item_2...) notation.  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___


<a name="first" ></a>* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___


<a name="first" ></a>

### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtiene el primer valor de un grupo de columnas. Si el segundo parámetro ignoreNulls se omite, se supone que es falso.  
* ``first(sales)``  
* ``first(sales, false)``  
___



<a name="flatten" ></a>

### <code>flatten</code>
<code><b>flatten(<i>&lt;array&gt;</i> : array, <i>&lt;value2&gt;</i> : array ..., <i>&lt;value2&gt;</i> : boolean) => array</b></code><br/><br/>
Acopla una o varias matrices en una sola. Las matrices de elementos atómicos se devuelven sin modificar. El último argumento es opcional y su valor predeterminado es false para acoplar de manera recursiva más de un nivel de profundidad.
*   ``flatten([['bojjus', 'girl'], ['gunchus', 'boy']]) => ['bojjus', 'girl', 'gunchus', 'boy']``
*   ``flatten([[['bojjus', 'gunchus']]] , true) => ['bojjus', 'gunchus']``
___


<a name="floor" ></a>

### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Devuelve el entero más grande no mayor que el número.  
* ``floor(-0.1) -> -1``  
___


<a name="fromBase64" ></a>

### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Descodifica la cadena con codificación base64 especificada.
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___


<a name="fromUTC" ></a>

### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convierte a la marca de tiempo de UTC. Si lo desea, puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria actual se establece como predeterminada. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___


<a name="greater" ></a>

### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparación mayor que. Igual que el operador >.  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="greaterOrEqual" ></a>

### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparación mayor que o igual que. Igual que el operador >=.  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___


<a name="greatest" ></a>

### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Devuelve el valor mayor entre la lista de valores como entrada, omitiendo los valores NULL. Devuelve NULL si todas las entradas son NULL.  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___


<a name="hasColumn" ></a>

### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Comprueba un valor de columna por el nombre en la secuencia. Puede pasar un nombre de flujo opcional como segundo argumento. Los nombres de columna que se conocen en tiempo de diseño deben tratarse simplemente por su nombre. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  
* ``hasColumn('parent')``  
___


<a name="hasPath" ></a>

### <code>hasPath</code>
<code><b>hasPath(<i>&lt;value1&gt;</i> : string, [<i>&lt;streamName&gt;</i> : string]) => boolean</b></code><br/><br/>
Comprueba si existe una determinada ruta de acceso jerárquica por nombre en la secuencia. Puede pasar un nombre de secuencia opcional como segundo argumento. Los nombres de columna o las rutas de acceso que se conocen en tiempo de diseño deben usarse solo mediante su nombre o mediante la ruta de acceso de la notación de puntos. No se admiten entradas calculadas, pero se pueden usar sustituciones de parámetros.  
* ``hasPath('grandpa.parent.child') => boolean``
___  


<a name="hex" ></a>

### <code>hex</code>
<code><b>hex(<i>\<value1\></i>: binary) => string</b></code><br/><br/>
Devuelve una representación de cadena hexa de un valor binario * ``hex(toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])) -> '1fadbe'``
___


<a name="hour" ></a>.

### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtiene el valor de la hora de una marca de tiempo. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___


<a name="hours" ></a>

### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duración en milisegundos para el número de horas.  
* ``hours(2) -> 7200000L``  
___


<a name="iif" ></a>

### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Basándose en una condición, aplica un valor o el otro. Si "Otro" si no se especifica, se considera NULL. Ambos valores deben ser compatibles (numéricos, cadena...). * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___


<a name="iifNull" ></a>

### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Comprueba si el primer parámetro es NULL. Si no es NULL, se devuelve el primer parámetro. Si es NULL, se devuelve el segundo parámetro. Si se especifican tres parámetros, el comportamiento es el mismo que iif(isNull(value1), value2, value3) y se devuelve el tercer parámetro si el primer valor no es NULL.  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___


<a name="in" ></a>

### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Comprueba si un elemento está en la matriz.  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___


<a name="initCap" ></a>

### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Convierte la primera letra de cada palabra en mayúsculas. Las palabras se identifican como separadas por espacios en blanco.  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___


<a name="instr" ></a>

### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Busca la posición (basada en 1) de la subcadena dentro de una cadena. Se devuelve 0 si no se encuentra.  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___


<a name="intersect" ></a>

### <code>intersect</code>
<code><b>intersect(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
Devuelve un conjunto de intersección de elementos distintos de dos matrices.
* ``intersect([10, 20, 30], [20, 40]) => [20]``  
___


<a name="isBitSet" ></a>

### <code>isBitSet</code>
<code><b>isBitSet (<i><i>\<value1\></i></i> : array, <i>\<value2\></i>:integer ) => boolean</b></code><br/><br/>
Comprueba si se establece una posición de bit en este conjunto de bits * ``isBitSet(toBitSet([10, 32, 98]), 10) => true``
___


<a name="isBoolean" ></a>.

### <code>isBoolean</code>
<code><b>isBoolean(<i>\<value1\></i>: string) => boolean</b></code><br/><br/>
Permite comprobar si el valor de la cadena es booleano según las reglas de ``toBoolean()``
* ``isBoolean('true') -> true``
* ``isBoolean('no') -> true``
* ``isBoolean('microsoft') -> false``
___


<a name="isByte" ></a>.

### <code>isByte</code>
<code><b>isByte(<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
Permite comprobar si el valor de la cadena es un byte con un formato opcional según las reglas de ``toByte()``
* ``isByte('123') -> true``
* ``isByte('chocolate') -> false``
___


<a name="isDate" ></a>.

### <code>isDate</code>
<code><b>isDate (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Permite comprobar si la cadena de fecha de entrada es una fecha con un formato de fecha de entrada opcional. Consulte SimpleDateFormat de Java para conocer los formatos disponibles. Si se omite el formato de fecha de entrada, el formato predeterminado es ``yyyy-[M]M-[d]d``. Los formatos aceptados son ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``
* ``isDate('2012-8-18') -> true``
* ``isDate('12/18--234234' -> 'MM/dd/yyyy') -> false``
___


<a name="isDecimal" ></a>.

### <code>isDecimal</code>
<code><b>isDecimal (<i>\<value1\></i> : string) => boolean</b></code><br/><br/>
Permite comprobar si el valor de la cadena es un elemento decimal con un formato opcional según las reglas de ``toDecimal()``
* ``isDecimal('123.45') -> true``
* ``isDecimal('12/12/2000') -> false``
___


<a name="isDelete" ></a>.

### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Comprueba si la fila está marcada para eliminar. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  
* ``isDelete()``  
* ``isDelete(1)``  
___


<a name="isDistinct" ></a>

### <code>isDistinct</code>
<code><b>isDistinct(<i>&lt;value1&gt;</i> : any , <i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Busca si una columna o un conjunto de columnas es distinto. No cuenta NULL como valor distinto *    ``isDistinct(custId, custName) => boolean``
___



<a name="isDouble" ></a>

### <code>isDouble</code>
<code><b>isDouble (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Permite comprobar si el valor de la cadena es un elemento doble con un formato opcional según las reglas de ``toDouble()``
* ``isDouble('123') -> true``
* ``isDouble('$123.45' -> '$###.00') -> true``
* ``isDouble('icecream') -> false``
___


<a name="isError" ></a>.

### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Comprueba si la fila se marca como error. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  
* ``isError()``  
* ``isError(1)``  
___


<a name="isFloat" ></a>

### <code>isFloat</code>
<code><b>isFloat (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Permite comprobar si el valor de la cadena es un elemento flotante con un formato opcional según las reglas de ``toFloat()``
* ``isFloat('123') -> true``
* ``isFloat('$123.45' -> '$###.00') -> true``
* ``isFloat('icecream') -> false``
___


<a name="isIgnore" ></a>.

### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Comprueba si la fila se marca para pasarse por alto. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  
* ``isIgnore()``  
* ``isIgnore(1)``  
___


<a name="isInsert" ></a>

### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Comprueba si la fila está marcada para insertar. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  
* ``isInsert()``  
* ``isInsert(1)``  
___


<a name="isInteger" ></a>

### <code>isInteger</code>
<code><b>isInteger (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Permite comprobar si el valor de la cadena es un elemento entero con un formato opcional según las reglas de ``toInteger()``
* ``isInteger('123') -> true``
* ``isInteger('$123' -> '$###') -> true``
* ``isInteger('microsoft') -> false``
___


<a name="isLong" ></a>.

### <code>isLong</code>
<code><b>isLong (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Permite comprobar si el valor de la cadena es un elemento largo con un formato opcional según las reglas de ``toLong()``
* ``isLong('123') -> true``
* ``isLong('$123' -> '$###') -> true``
* ``isLong('gunchus') -> false``
___


<a name="isMatch" ></a>.

### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Comprueba si la fila cumplía los criterios de coincidencia en la búsqueda. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  
* ``isMatch()``  
* ``isMatch(1)``  
___


<a name="isNan" ></a>

### <code>isNan</code>
<code><b>isNan (<i>\<value1\></i> : integral) => boolean</b></code><br/><br/>
Comprueba si no es un número.
* ``isNan(10.2) => false``
___


<a name="isNull" ></a>

### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Comprueba si el valor es NULL.  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___


<a name="isShort" ></a>

### <code>isShort</code>
<code><b>isShort (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Permite comprobar si el valor de la cadena es un elemento corto con un formato opcional según las reglas de ``toShort()``
* ``isShort('123') -> true``
* ``isShort('$123' -> '$###') -> true``
* ``isShort('microsoft') -> false``
___


<a name="isTimestamp" ></a>.

### <code>isTimestamp</code>
<code><b>isTimestamp (<i>\<value1\></i> : string, [&lt;format&gt;: string]) => boolean</b></code><br/><br/>
Permite comprobar si la cadena de fecha de entrada es una marca de tiempo con un formato de marca de tiempo de entrada opcional. Consulte la clase SimpleDateFormat de Java para conocer los formatos disponibles. Si la marca de tiempo se omite, se usa el patrón predeterminado ``yyyy-[M]M-[d]d hh:mm:ss[.f...]``. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La marca de tiempo admite una precisión de hasta milisegundos con un valor de 999. Consulte la clase SimpleDateFormat de Java para conocer los formatos disponibles.
* ``isTimestamp('2016-12-31 00:12:00') -> true``
* ``isTimestamp('2016-12-31T00:12:00' -> 'yyyy-MM-dd\\'T\\'HH:mm:ss' -> 'PST') -> true``
* ``isTimestamp('2012-8222.18') -> false``
___


<a name="isUpdate" ></a>

### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Comprueba si la fila está marcada para actualizar. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  
* ``isUpdate()``  
* ``isUpdate(1)``  
___


<a name="isUpsert" ></a>

### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Comprueba si la fila está marcada para insertar. En las transformaciones que toman más de un flujo de entrada, se puede pasar el índice (basado en 1) del flujo. El índice de flujo debe ser 1 o 2 y el valor predeterminado es 1.  
* ``isUpsert()``  
* ``isUpsert(1)``  
___


<a name="jaroWinkler" ></a>

### <code>jaroWinkler</code>
<code><b>jaroWinkler(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => double</b></code><br/><br/>
Obtiene la distancia de JaroWrinkler entre dos cadenas. 
* ``jaroWinkler('frog', 'frog') => 1.0``  
___


<a name="keyValues" ></a>

### <code>keyValues</code>
<code><b>keyValues(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : array) => map</b></code><br/><br/>
Crea una asignación de clave/valores. El primer parámetro es una matriz de claves y el segundo es la matriz de valores. Ambas matrices deben tener la misma longitud.
*   ``keyValues(['bojjus', 'appa'], ['gunchus', 'ammi']) => ['bojjus' -> 'gunchus', 'appa' -> 'ammi']``
___ 


<a name="kurtosis" ></a>

### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la curtosis de una columna.  
* ``kurtosis(sales)``  
___


<a name="kurtosisIf" ></a>

### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la curtosis de una columna.  
* ``kurtosisIf(region == 'West', sales)``  
___


<a name="lag" ></a>

### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtiene el valor del primer parámetro evaluado n filas antes de la fila actual. El segundo parámetro es el número de filas que se debe mirar hacia atrás, y el valor predeterminado es 1. Si no hay tantas filas, se devuelve un valor NULL, a menos que se especifique un valor predeterminado.  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___


<a name="last" ></a>

### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Obtiene el último valor de un grupo de columnas. Si el segundo parámetro ignoreNulls se omite, se supone que es falso.  
* ``last(sales)``  
* ``last(sales, false)``  
___


<a name="lastDayOfMonth" ></a>

### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Obtiene el último día del mes dada una fecha.  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___


<a name="lead" ></a>

### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Obtiene el valor del primer parámetro evaluado n filas después de la fila actual. El segundo parámetro es el número de filas que se debe mirar hacia adelante, y el valor predeterminado es 1. Si no hay tantas filas, se devuelve un valor NULL, a menos que se especifique un valor predeterminado.  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___


<a name="least" ></a>

### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Operador de comparación menor que o igual que. Igual que el operador <=.  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___


<a name="left" ></a>

### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrae un inicio de la subcadena en el índice 1 con el número de caracteres. Igual que SUBSTRING(str, 1, n).  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___


<a name="length" ></a>

### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Devuelve la longitud de la cadena.  
* ``length('dumbo') -> 5``  
___


<a name="lesser" ></a>

### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparación menor que. Igual que el operador <.  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___


<a name="lesserOrEqual" ></a>

### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparación menor que o igual que. Igual que el operador <=.  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___


<a name="levenshtein" ></a>

### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Obtiene la distancia de edición entre dos cadenas.  
* ``levenshtein('boys', 'girls') -> 4``  
___


<a name="like" ></a>

### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
El patrón es una cadena que se hace coincidir literalmente. La excepción son los siguientes símbolos especiales: _ coincide con cualquier carácter en la entrada (similar a . en expresiones regulares ```posix```) y % coincide con cero o más caracteres en la entrada (similar a .* en expresiones regulares ```posix```).
El carácter de escape es ". Si un carácter de escape precede a un símbolo especial u otro carácter de escape, el carácter siguiente se hace coincidir literalmente. No es válido escapar cualquier otro carácter.  
* ``like('icecream', 'ice%') -> true``  
___


<a name="locate" ></a>

### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Busca la posición (basada en 1) de la subcadena dentro de una cadena a partir de una posición determinada. Si la posición se omite se considera desde el principio de la cadena. Se devuelve 0 si no se encuentra.  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___


<a name="log" ></a>

### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Calcula el valor del logaritmo. Se puede suministrar una base opcional o un número de Euler si se usa.  
* ``log(100, 10) -> 2``  
___


<a name="log10" ></a>

### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula el valor del logaritmo en base 10.  
* ``log10(100) -> 2``  
___


<a name="lookup" ></a>

### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
Busca la primera fila del receptor almacenado en caché con las claves especificadas, que coinciden con las claves del receptor almacenado en caché.
* ``cacheSink#lookup(movieId)``  
___


<a name="lower" ></a>

### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Pone en minúsculas una cadena.  
* ``lower('GunChus') -> 'gunchus'``  
___


<a name="lpad" ></a>

### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Left rellena la cadena mediante el carácter de relleno proporcionado hasta que se alcanza una determinada longitud. Si la cadena es igual o mayor que la longitud, se recorta para cumplir con la longitud.  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``  
___


<a name="ltrim" ></a>

### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Left recorta una cadena de caracteres iniciales. Si no se especifica el segundo parámetro, recorta el espacio en blanco. Si no, recorta cualquier carácter especificado en el segundo parámetro.  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___


<a name="map" ></a>

### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Asigna cada elemento de la matriz a un nuevo elemento mediante la expresión proporcionada. La asignación espera una referencia a un elemento de la función de expresión como #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___


<a name="mapAssociation" ></a>

### <code>mapAssociation</code>
<code><b>mapAssociation(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => array</b></code><br/><br/>
Transforma una asignación al asociar las claves a nuevos valores. Devuelve una matriz. Toma una función de asignación donde se puede hacer referencia al elemento como #key y al índice actual como #value. 
*   ``mapAssociation(['bojjus' -> 'gunchus', 'appa' -> 'ammi'], @(key = #key, value = #value)) => [@(key = 'bojjus', value = 'gunchus'), @(key = 'appa', value = 'ammi')]``
___ 


<a name="mapIf" ></a>

### <code>mapIf</code>
<code><b>mapIf (<i>\<value1\></i> : array, <i>\<value2\></i> : binaryfunction, \<value3\>: binaryFunction) => any</b></code><br/><br/>
Asigna de manera condicional una matriz a otra de la misma longitud o más corta. The values can be of any datatype including structTypes. It takes a mapping function where you can address the item in the array as #item and current index as #index. En el caso de asignaciones con anidaciones muy profundas, puede consultar las asignaciones primarias mediante la notación ``#item_[n](#item_1, #index_1...)``.
*    ``mapIf([10, 20, 30], #item > 10, #item + 5) -> [25, 35]``
* ``mapIf(['icecream', 'cake', 'soda'], length(#item) > 4, upper(#item)) -> ['ICECREAM', 'CAKE']``
___


<a name="mapIndex" ></a>

### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Asigna cada elemento de la matriz a un nuevo elemento mediante la expresión proporcionada. Map espera una referencia a un elemento en la función de expresión como #item y una referencia al índice del elemento como #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___


<a name="mapLoop" ></a>

### <code>mapLoop</code>
<code><b>mapLoop(<i>\<value1\></i> : integer, <i>\<value2\></i> : unaryfunction) => any</b></code><br/><br/>
Produce un bucle desde 1 hasta la longitud para crear una matriz de esa longitud. It takes a mapping function where you can address the index in the array as #index. En el caso de asignaciones con anidaciones muy profundas, puede consultar las asignaciones primarias mediante la notación #index_n(#index_1, #index_2...).
*    ``mapLoop(3, #index * 10) -> [10, 20, 30]``
___


<a name="max" ></a>

### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtiene el valor máximo de una columna.  
* ``max(sales)``  
___


<a name="maxIf" ></a>

### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
En función de los criterios, obtiene el valor máximo de una columna.  
* ``maxIf(region == 'West', sales)``  
___


<a name="md5" ></a>

### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula la síntesis del mensaje MD5 del conjunto de columnas de diferentes tipos de datos primitivos y devuelve una cadena hexadecimal de 32 caracteres. Se puede usar para calcular una huella digital de una fila.  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___


<a name="mean" ></a>

### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtiene la media de valores de una columna. Igual que AVG.  
* ``mean(sales)``  
___


<a name="meanIf" ></a>

### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
En función de los criterios, obtiene la media de valores de una columna. Igual que avgIf.  
* ``meanIf(region == 'West', sales)``  
___


<a name="millisecond" ></a>

### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtiene el valor de los milisegundos de una fecha. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="milliseconds" ></a>

### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duración en milisegundos para el número de milisegundos.  
* ``milliseconds(2) -> 2L``  
___


<a name="min" ></a>

### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Obtiene el valor mínimo de una columna.  
* ``min(sales)``  
___


<a name="minIf" ></a>

### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
En función de los criterios, obtiene el valor mínimo de una columna.  
* ``minIf(region == 'West', sales)``  
___


<a name="minus" ></a>

### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Resta números. Restar el número de días a partir de una fecha. Resta la vigencia de una marca de tiempo. Resta dos marcas de tiempo para obtener la diferencia en milisegundos. Igual que el operador -.  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___


<a name="minute" ></a>

### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtiene el valor de los minutos de una marca de tiempo. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___


<a name="minutes" ></a>

### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duración en milisegundos para el número de minutos.  
* ``minutes(2) -> 120000L``  
___


<a name="mlookup" ></a>

### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
Busca todas las filas coincidentes del receptor almacenado en caché con las claves especificadas, que coinciden con las claves del receptor almacenado en caché.
* ``cacheSink#mlookup(movieId)``  
___


<a name="mod" ></a>

### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Módulo de par de números. Igual que el operador %.  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___


<a name="month" ></a>

### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtiene el valor del mes de una fecha o una marca de tiempo.  
* ``month(toDate('2012-8-8')) -> 8``  
___


<a name="monthsBetween" ></a>

### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Obtiene el número de meses entre dos fechas. Puede redondear el cálculo. Puede pasar una zona horaria opcional con el formato "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___


<a name="multiply" ></a>

### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multiplica dos números. Igual que el operador *.  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___


<a name="negate" ></a>

### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Niega un número. Convierte los números positivos en negativos y viceversa.  
* ``negate(13) -> -13``  
___


<a name="nextSequence" ></a>

### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Devuelve la siguiente secuencia única. El número es consecutivo solo dentro de una partición y viene precedido por el identificador de la partición.  
* ``nextSequence() == 12313112 -> false``  
___


<a name="normalize" ></a>

### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normaliza el valor de la cadena para separar caracteres Unicode acentuados.  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___


<a name="not" ></a>

### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador de negación lógica.  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___


<a name="notEquals" ></a>

### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operador de comparación no igual que. Igual que el operador !=.  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___


<a name="notNull" ></a>

### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Comprueba si el valor no es NULL.  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___


<a name="nTile" ></a>

### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
La función ```NTile``` divide las filas para cada partición de ventana en `n` cubos comprendidos entre los valores 1 y `n` como máximo. Los valores de cubo variarán en 1 como máximo. Si el número de filas de la partición no se divide uniformemente en el número de cubos, los valores del resto se distribuyen uno por cubo, empezando por el primer cubo. La función ```NTile``` es útil para el cálculo de ```tertiles```, cuartiles, deciles y otras estadísticas de resumen comunes. La función calcula dos variables durante la inicialización: El tamaño de un cubo normal tendrá una fila extra agregada. Ambas variables se basan en el tamaño de la partición actual. Durante el proceso de cálculo, la función realiza un seguimiento del número de fila actual, el número de cubo actual y el número de fila en el que el cubo cambiará (bucketThreshold). Cuando el número de la fila actual alcance el umbral del cubo, el valor del cubo se incrementa en uno y el umbral aumentará el tamaño del cubo (más uno adicional si el cubo actual se rellena).  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___


<a name="null" ></a>

### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Devuelve un valor NULL. Use la función `syntax(null())` si hay una columna denominada "null". Cualquier operación que use dará como resultado un valor NULL.  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___


<a name="or" ></a>

### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador lógico OR. Igual que ||.  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___


<a name="originColumns" ></a>

### <code>originColumns</code>
<code><b>originColumns(<i>&lt;streamName&gt;</i> : string) => any</b></code><br/><br/>
Obtiene todas las columnas de salida de un flujo de origen donde se crearon columnas. Debe incluirse en otra función.
* ``array(toString(originColumns('source1')))``
___  


<a name="output" ></a>

### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
Devuelve la primera fila de los resultados del receptor almacenado en caché * ``cacheSink#output()``.  
___


<a name="outputs" ></a>

### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
Devuelve el conjunto completo de filas de salida de los resultados del receptor almacenado en caché * ``cacheSink#outputs()``
___



<a name="partitionId" ></a>.

### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Devuelve el identificador de la partición actual en donde se encuentra la fila de entrada.  
* ``partitionId()``  
___


<a name="pMod" ></a>

### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Módulo positivo de par de números.  
* ``pmod(-20, 8) -> 4``  
___


<a name="power" ></a>

### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Eleva un número a la potencia de otro.  
* ``power(10, 2) -> 100``  
___


<a name="radians" ></a>

### <code>radians</code>
<code><b>radians(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Convierte los grados en radianes * ``radians(180) => 3.141592653589793``  
___


<a name="random" ></a>

### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
Devuelve un número aleatorio dado un valor de inicialización opcional dentro de una partición. El valor de inicialización debe ser un valor fijo y se usa junto con partitionId para generar valores aleatorios. * ``random(1) == 1 -> false``
___


<a name="rank" ></a>

### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Calcula el rango de un valor en un grupo de valores especificado en la cláusula order by de una ventana. El resultado es uno más el número de filas anteriores o iguales a la fila actual en la ordenación de la partición. Los valores generarán intervalos en la secuencia. La clasificación funciona incluso cuando no está ordenada y busca cambios en los valores.  
* ``rank()``  
___


<a name="reassociate" ></a>

### <code>reassociate</code>
<code><b>reassociate(<i>&lt;value1&gt;</i> : map, <i>&lt;value2&gt;</i> : binaryFunction) => map</b></code><br/><br/>
Transforma una asignación al asociar las claves a nuevos valores. Toma una función de asignación donde se puede hacer referencia al elemento como #key y al índice actual como #value.  
* ``reassociate(['fruit' -> 'apple', 'vegetable' -> 'tomato'], substring(#key, 1, 1) + substring(#value, 1, 1)) => ['fruit' -> 'fa', 'vegetable' -> 'vt']``
___
  


<a name="reduce" ></a>

### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Acumula los elementos de una matriz. "Reduce" espera una referencia a un acumulador y a un elemento en la primera función de expresión, como #acc y #item, y espera que el valor resultante sea #result para poder usarlo en la segunda función de expresión.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___


<a name="regexExtract" ></a>

### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrae una subcadena coincidente para un patrón de expresión regular especificado. El último parámetro identifica el grupo de coincidencia y, si se omite, se usa el valor predeterminado 1. Use `<regex>` (comilla inversa) para que coincida con una cadena sin escape.  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___


<a name="regexMatch" ></a>

### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Comprueba si la cadena coincide con el patrón de expresión regular especificado. Use `<regex>` (comilla inversa) para que coincida con una cadena sin escape.  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___


<a name="regexReplace" ></a>

### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Reemplaza todas las repeticiones de un patrón de expresión regular por otra subcadena en la cadena dada. Use `<regex>` (comilla inversa) para que coincida con una cadena sin escape.  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___


<a name="regexSplit" ></a>

### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Divide una cadena según un delimitador basándose en la expresión regular y devuelve una matriz de cadenas.  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___


<a name="replace" ></a>

### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Reemplaza todas las repeticiones de una subcadena por otra subcadena en la cadena dada. Si se omite el último parámetro, el valor predeterminado es una cadena vacía.  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___


<a name="reverse" ></a>

### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Invierte una cadena.  
* ``reverse('gunchus') -> 'suhcnug'``  
___


<a name="right" ></a>

### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrae una subcadena con el número de caracteres de la derecha. Igual que SUBSTRING(str, LENGTH(str) - n, n).  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___


<a name="rlike" ></a>

### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Comprueba si la cadena coincide con el patrón de expresión regular especificado.  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___


<a name="round" ></a>

### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Redondea un número con una escala opcional y un modo de redondeo opcional dados. Si la escala se omite, se establece en 0 de forma predeterminada. Si el modo se omite, se establece en ROUND_HALF_UP(5) de forma predeterminada. Los valores de redondeo incluyen 1 - ROUND_UP 2 - ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY.  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___


<a name="rowNumber" ></a>

### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Asigna una numeración secuencial de filas para las filas en una ventana que comienzan con 1.  
* ``rowNumber()``  



<a name="rpad" ></a>

### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Right rellena la cadena mediante el carácter de relleno proporcionado hasta que se alcanza una determinada longitud. Si la cadena es igual o mayor que la longitud, se recorta para cumplir con la longitud.  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___


<a name="rtrim" ></a>

### <code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Right recorta una cadena de caracteres finales. Si no se especifica el segundo parámetro, recorta el espacio en blanco. Si no, recorta cualquier carácter especificado en el segundo parámetro.  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___


<a name="second" ></a>

### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Obtiene el valor de los segundos de una fecha. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria local se utiliza como el valor predeterminado. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___


<a name="seconds" ></a>

### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duración en milisegundos para el número de segundos.  
* ``seconds(2) -> 2000L``  
___


<a name="setBitSet" ></a>

### <code>setBitSet</code>
<code><b>setBitSet (<i>\<value1\></i>: array, <i>\<value2\></i>:array) => array</b></code><br/><br/>
Establece las posiciones de bit en este conjunto de bits * ``setBitSet(toBitSet([10, 32]), [98]) => [4294968320L, 17179869184L]``
___  


<a name="sha1" ></a>.

### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula la síntesis del mensaje SHA-1 del conjunto de columnas de diferentes tipos de datos primitivos y devuelve una cadena hexadecimal de 40 caracteres. Se puede usar para calcular una huella digital de una fila.  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___


<a name="sha2" ></a>

### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Calcula la síntesis del mensaje SHA-2 del conjunto de columnas de diferentes tipos de datos primitivos dada una longitud en bits, que solo puede tener los valores 0(256), 224, 256, 384 y 512. Se puede usar para calcular una huella digital de una fila.  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___


<a name="sin" ></a>

### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un valor de seno.  
* ``sin(2) -> 0.9092974268256817``  
___


<a name="sinh" ></a>

### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un valor de seno hiperbólico.  
* ``sinh(0) -> 0.0``  
___


<a name="size" ></a>

### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
Busca el tamaño de una matriz o un tipo de asignación * ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___


<a name="skewness" ></a>

### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la asimetría de una columna.  
* ``skewness(sales)``  
___


<a name="skewnessIf" ></a>

### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la asimetría de una columna.  
* ``skewnessIf(region == 'West', sales)``  
___


<a name="slice" ></a>

### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrae un subconjunto de una matriz de una posición. La posición se basa en 1. Si la longitud se omite, se establece el final de la cadena como valor predeterminado.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___


<a name="sort" ></a>

### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Ordena la matriz mediante la función de predicado proporcionada. "Sort" espera una referencia a dos elementos consecutivos de la función de expresión, como #item1 y #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  
___


<a name="soundex" ></a>

### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Obtiene el código ```soundex``` de la cadena.  
* ``soundex('genius') -> 'G520'``  
___


<a name="split" ></a>

### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Divide una cadena según un delimitador y devuelve una matriz de cadenas.  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___


<a name="sqrt" ></a>

### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula la raíz cuadrada de un número.  
* ``sqrt(9) -> 3``  
___


<a name="startsWith" ></a>

### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Comprueba si la cadena comienza por la cadena proporcionada.  
* ``startsWith('dumbo', 'du') -> true``  
___


<a name="stddev" ></a>

### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la desviación estándar de una columna.  
* ``stdDev(sales)``  
___


<a name="stddevIf" ></a>

### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la desviación estándar de una columna.  
* ``stddevIf(region == 'West', sales)``  
___


<a name="stddevPopulation" ></a>

### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la desviación estándar de población de una columna.  
* ``stddevPopulation(sales)``  
___


<a name="stddevPopulationIf" ></a>

### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la desviación estándar de población de una columna.  
* ``stddevPopulationIf(region == 'West', sales)``  
___


<a name="stddevSample" ></a>

### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la desviación estándar de muestra de una columna.  
* ``stddevSample(sales)``  
___


<a name="stddevSampleIf" ></a>

### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la desviación estándar de muestra de una columna.  
* ``stddevSampleIf(region == 'West', sales)``  
___


<a name="subDays" ></a>

### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Resta días de una fecha o una marca de tiempo. Igual que el operador - para la fecha.  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___


<a name="subMonths" ></a>

### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Resta meses a una fecha o marca de tiempo.  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___


<a name="substring" ></a>

### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrae una subcadena de una determinada longitud de una posición. La posición se basa en 1. Si la longitud se omite, se establece el final de la cadena como valor predeterminado.  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___


<a name="sum" ></a>

### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtiene la suma de agregados de una columna numérica.  
* ``sum(col)``  
___


<a name="sumDistinct" ></a>

### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Obtiene la suma de agregados de valores distintos de una columna numérica.  
* ``sumDistinct(col)``  
___


<a name="sumDistinctIf" ></a>

### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
En función de los criterios, obtiene la suma de agregados de una columna numérica. La condición se puede basar en cualquier columna.  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___


<a name="sumIf" ></a>

### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
En función de los criterios, obtiene la suma de agregados de una columna numérica. La condición se puede basar en cualquier columna.  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___


<a name="tan" ></a>

### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un valor de tangente.  
* ``tan(0) -> 0.0``  
___


<a name="tanh" ></a>

### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Calcula un valor de tangente hiperbólica.  
* ``tanh(0) -> 0.0``  
___


<a name="toBase64" ></a>

### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Codifica la cadena especificada en Base64.  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___


<a name="toBinary" ></a>

### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Convierte cualquier valor numérico/fecha/marca de tiempo/cadena en una representación binaria.  
* ``toBinary(3) -> [0x11]``  
___


<a name="toBoolean" ></a>

### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Convierte un valor de (" t", "true", "y", "yes", "1") en true y ("f", "false", "n", "no", "0") en false y NULL para cualquier otro valor.  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___


<a name="toByte" ></a>

### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
Convierte cualquier valor numérico o cadena en un valor de tipo byte. Se puede utilizar un formato decimal de Java opcional para la conversión.  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___


<a name="toDate" ></a>

### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Convierte la cadena de fecha de entrada en fecha con un formato de fecha de entrada opcional. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. Si se omite el formato de fecha de entrada, el formato predeterminado es aaaa-[M]M-[d]d. Los formatos aceptados son :[ aaaa, aaaa-[M]M, aaaa-[M]M-[d]d, aaaa-[M]M-[d]dH* ].  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___


<a name="toDecimal" ></a>

### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Convierte cualquier valor numérico o cadena en un valor decimal. Si no se especifican la precisión y la escala, se utiliza el valor predeterminado (10,2). Se puede utilizar un formato decimal de Java opcional para la conversión. Formato de configuración regional opcional en el formulario del lenguaje BCP47, como en-US, de, zh-CN.  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___


<a name="toDouble" ></a>

### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Convierte cualquier valor numérico o cadena en un valor doble. Se puede utilizar un formato decimal de Java opcional para la conversión. Formato de configuración regional opcional en el formulario del lenguaje BCP47, como en-US, de, zh-CN.  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___


<a name="toFloat" ></a>

### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Convierte cualquier valor numérico o cadena en un valor flotante. Se puede utilizar un formato decimal de Java opcional para la conversión. Trunca cualquier valor doble.  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___


<a name="toInteger" ></a>

### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Convierte cualquier valor numérico o cadena en un valor entero. Se puede utilizar un formato decimal de Java opcional para la conversión. Trunca cualquier valor long, float o double.  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___


<a name="toLong" ></a>

### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Convierte cualquier valor numérico o cadena en un valor largo. Se puede utilizar un formato decimal de Java opcional para la conversión. Trunca cualquier valor float o double.  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___


<a name="toShort" ></a>

### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Convierte cualquier valor numérico o cadena en un valor corto. Se puede utilizar un formato decimal de Java opcional para la conversión. Trunca cualquier valor entero, long, float o double.  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___


<a name="toString" ></a>

### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Convierte un tipo de datos primitivo en una cadena. Para los números y la fecha se puede especificar un formato. Si no se especifica, se elige el valor predeterminado del sistema. Para los números, se usa el formato decimal de Java. Consulte el formato SimpleDateFormat de Java para ver todos los formatos de fecha posibles; el formato predeterminado es aaaa-MM-dd.  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___


<a name="toTimestamp" ></a>

### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convierte una cadena en una marca de tiempo dado un formato de marca de tiempo opcional. Si la marca de tiempo se omite, se usa el patrón predeterminado aaaa-[M]M-[d]d hh:mm:ss[.f...]. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La marca de tiempo admite una precisión de hasta milisegundos, con el valor de 999. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___


<a name="toUTC" ></a>

### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Convierte la marca de tiempo en UTC. Puede pasar una zona horaria opcional en forma de "GMT", "PST", "UTC", "America/Cayman". La zona horaria actual se establece como predeterminada. Consulte la clase `SimpleDateFormat` de Java para obtener los formatos disponibles. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  



<a name="translate" ></a>

### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Reemplaza un conjunto de caracteres por otro conjunto de caracteres de la cadena. Los caracteres tienen un reemplazo de 1 a 1.  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___


<a name="trim" ></a>

### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Recorta una cadena de caracteres iniciales y finales. Si no se especifica el segundo parámetro, recorta el espacio en blanco. Si no, recorta cualquier carácter especificado en el segundo parámetro.  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___


<a name="true" ></a>

### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Siempre devuelve un valor true. Use la función `syntax(true())` si hay una columna denominada "true".  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___


<a name="typeMatch" ></a>

### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Coincide con el tipo de la columna. Solo se puede usar en expresiones de patrón. "number" coincide con los tipos short, entero, long, double, float o decimal; "integral" coincide con los tipos short, entero y long; "fractional" coincide con los tipos double, float y decimal; "datetime" coincide con los tipos de date o timestamp.  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___


<a name="unescape" ></a>

### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Elimina el escape de una cadena según un formato. Los valores literales para el formato aceptable son "json", "xml", "ecmascript", "html", "java".
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___


<a name="unfold" ></a>

### <code>unfold</code>
<code><b>unfold (<i>&lt;value1&gt;</i>: array) => any</b></code><br/><br/>
Despliega una matriz en un conjunto de filas y repite los valores de las columnas restantes en cada fila.
*   ``unfold(addresses) => any``
*   ``unfold( @(name = salesPerson, sales = salesAmount) ) => any``
___  


<a name="unhex" ></a>

### <code>unhex</code>
<code><b>unhex(<i>\<value1\></i>: string) => binary</b></code><br/><br/>
Deshace la representación hexa de un valor binario de su representación de cadena. Se puede usar junto con SHA2, MD5 para convertir de cadena a representación binaria *    ``unhex('1fadbe') -> toBinary([toByte(0x1f), toByte(0xad), toByte(0xbe)])``
*    ``unhex(md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))) -> toBinary([toByte(0x4c),toByte(0xe8),toByte(0xa8),toByte(0x80),toByte(0xbd),toByte(0x62),toByte(0x1a),toByte(0x1f),toByte(0xfa),toByte(0xd0),toByte(0xbc),toByte(0xa9),toByte(0x05),toByte(0xe1),toByte(0xbc),toByte(0x5a)])``



<a name="union" ></a>.

### <code>union</code>
<code><b>union(<i>&lt;value1&gt;</i>: array, <i>&lt;value2&gt;</i> : array) => array</b></code><br/><br/>
Devuelve un conjunto de uniones de elementos distintos de dos matrices.
* ``union([10, 20, 30], [20, 40]) => [10, 20, 30, 40]``
___  
  


<a name="upper" ></a>

### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Pone en mayúsculas una cadena.  
* ``upper('bojjus') -> 'BOJJUS'``  
___


<a name="uuid" ></a>

### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Devuelve el UUID generado.  
* ``uuid()``  
___


<a name="variance" ></a>

### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la varianza de una columna.  
* ``variance(sales)``  
___


<a name="varianceIf" ></a>

### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la varianza de una columna.  
* ``varianceIf(region == 'West', sales)``  
___


<a name="variancePopulation" ></a>

### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la varianza de población de una columna.  
* ``variancePopulation(sales)``  
___


<a name="variancePopulationIf" ></a>

### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la varianza de población de una columna.  
* ``variancePopulationIf(region == 'West', sales)``  
___


<a name="varianceSample" ></a>

### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Obtiene la varianza no sesgada de una columna.  
* ``varianceSample(sales)``  
___


<a name="varianceSampleIf" ></a>

### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
En función de los criterios, obtiene la varianza no sesgada de una columna.  
* ``varianceSampleIf(region == 'West', sales)``  



<a name="weekOfYear" ></a>

### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtiene la semana del año dada una fecha.  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___


<a name="weeks" ></a>

### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Duración en milisegundos para el número de semanas.  
* ``weeks(2) -> 1209600000L``  
___


<a name="xor" ></a>

### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Operador XOR lógico. Igual que el operador ^.  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___


<a name="year" ></a>

### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Obtiene el valor de año de una fecha.  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="next-steps"></a>Pasos siguientes

[Aprenda a usar el Generador de expresiones](concepts-data-flow-expression-builder.md).