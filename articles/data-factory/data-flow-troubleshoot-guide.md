---
title: Solución de problemas de los flujos de datos de asignación
description: Obtenga información sobre la solución de problemas relacionados con flujos de datos en Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: f41d53207eb00e4044e523e481de444a615a5d88
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389406"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Solución de problemas de los flujos de datos de asignación en Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se exploran métodos comunes de solución de problemas de flujos de datos de asignación en Azure Data Factory.  Vea [Mensajes y códigos de error comunes de los flujos de datos](data-flow-troubleshoot-errors.md) para conocer mensajes de error específicos y sus causas y recomendaciones asociadas.

## <a name="miscellaneous-troubleshooting-tips"></a>Consejos de solución de problemas varios
- **Problema**: Se produjo una excepción inesperada y un error en la ejecución.
    - **Mensaje**: Durante la ejecución de una actividad de Data Flow: Se obtuvo una excepción inesperada y se produjo un error de ejecución.
    - **Causa**: Se trata de un error del servicio de back-end. Vuelva a intentar la operación y reinicie la sesión de depuración.
    - **Recomendación**: Si reintenta y reinicia y no se resuelve el problema, póngase en contacto con el servicio de soporte al cliente.

-  **Problema**: No hay datos de salida en la combinación durante la vista previa de los datos de depuración.
    - **Mensaje**: hay un gran número de valores NULL o valores que faltan, lo que puede deberse a que no se han muestreado suficientes filas. Pruebe a actualizar el límite de filas de depuración y los datos.
    - **Causa**: La condición de combinación no ha coincidido con ninguna fila o dio como resultado un gran número de valores NULL durante la vista previa de los datos.
    - **Recomendación**: En **Configuración de depuración**, aumente el número de filas del límite de filas de origen. Asegúrese de seleccionar una instancia de Azure IR que tenga un clúster de flujo de datos lo suficientemente grande como para controlar más datos.
    
- **Problema**: Error de validación en el origen con archivos CSV de varias líneas. 
    - **Mensaje**: Puede ver uno de estos mensajes de error:
        - Falta la última columna o es NULL.
        - Error en la validación del esquema en el origen.
        - La importación de esquema no se muestra correctamente en la experiencia de usuario y la última columna tiene un carácter de nueva línea en el nombre.
    - **Causa**: En el flujo de datos de asignación, los archivos de origen CSV de varias líneas no funcionan actualmente cuando se usa \r\n como delimitador de filas. A veces, las líneas adicionales de los retornos de carro pueden producir errores. 
    - **Recomendación** Genere el archivo en el origen usando el carácter \n como delimitador de filas, en lugar de \r\n. O bien, use la actividad de copia para convertir el archivo CSV para que use \n como delimitador de filas.

## <a name="general-troubleshooting-guidance"></a>Guía de solución de problemas generales
1. Compruebe el estado de las conexiones del conjunto de datos. En cada transformación de origen y de receptor, vaya al servicio vinculado para cada conjunto de datos que use y pruebe las conexiones.
2. Compruebe el estado de las conexiones de archivos y tablas en el diseñador de flujo de datos. En el modo de depuración, seleccione **Vista previa de datos** en las transformaciones de origen para asegurarse de que puede tener acceso a los datos.
3. Si todo parece correcto en la vista previa de los datos, vaya al diseñador de canalizaciones y coloque el flujo de datos en una actividad de canalización. Depure la canalización para realizar una prueba de un extremo a otro.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>Mejora en el formato CSV/CDM en Data Flow 

Si usa el **formato de texto delimitado o CDM para asignar el flujo de datos en Azure Data Factory V2**, puede que se enfrente a cambios de comportamiento en las canalizaciones existentes debido a la mejora del texto delimitado o CDM en el flujo de datos a partir del **1 de mayo de 2021**. 

Es posible que encuentre los siguientes problemas antes de realizar la mejora, pero después de este proceso se solucionarán los problemas. Lea el siguiente contenido para determinar si esta mejora le afecta. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>Escenario 1: encontrar el problema de delimitador de filas inesperado.

 Se verá afectado si se encuentra en las siguientes condiciones:
 - Uso del texto delimitado con la configuración de varias líneas establecida en "true" o CDM como origen.
 - La primera fila tiene más de 128 caracteres. 
 - El delimitador de fila de los archivos de datos no es `\n`.

 Antes de realizar la mejora, el delimitador de filas predeterminado `\n` se puede usar inesperadamente para analizar archivos de texto delimitados, ya que cuando la configuración de varias líneas está establecida en "true", se invalida la configuración del delimitador de filas y este se detecta automáticamente en función de los primeros 128 caracteres. Si no detecta el delimitador de filas real, se revertiría a `\n`.  

 Después de realizar la mejora, debería funcionar cualquiera de los tres delimitadores de fila: `\r`, `\n` o `\r\n`.
 
 En el ejemplo siguiente se muestra un cambio de comportamiento en la canalización después de realizar la mejora:

 **Ejemplo**:<br/>
   En la siguiente columna:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   Antes de realizar la mejora, `\r` se mantiene en el valor de la columna. El resultado de la columna analizada es:<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   Después de realizar la mejora, el resultado de la columna analizada debe ser:<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>Escenario 2: detecte un problema de lectura incorrecta de valores de columna que contengan "\r\n".

 Se verá afectado si se encuentra en las siguientes condiciones:
 - Uso del texto delimitado con la configuración de varias líneas establecida en "true" o CDM como origen. 
 - El delimitador de filas es `\r\n`.

 Antes de la mejora, al leer el valor de la columna, puede que se reemplace `\r\n` incorrectamente por `\n`. 

 Después de realizar la mejora, `\r\n` en el valor de la columna no se reemplazará por `\n`.

 En el ejemplo siguiente se muestra un cambio de comportamiento en la canalización después de realizar la mejora:
 
 **Ejemplo**:<br/>
  
 En la siguiente columna:<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 Antes de realizar la mejora, el resultado de la columna analizada es:<br/>
  **`A\n`**` B C`<br/>

 Después de realizar la mejora, el resultado de la columna analizada debe ser:<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>Escenario 3: detecte un problema de escritura incorrecta de valores de columna que contengan "\n".

 Se verá afectado si se encuentra en las siguientes condiciones:
 - Uso del texto delimitado como receptor.
 - El valor de la columna contiene `\n`.
 - El delimitador de filas está establecido en `\r\n`.
 
 Antes de realizar la mejora, al escribir el valor de la columna, puede que se reemplace `\n` incorrectamente por `\r\n`. 

 Después de realizar la mejora, `\n` en el valor de la columna no se reemplazará por `\r\n`.
 
 En el ejemplo siguiente se muestra un cambio de comportamiento en la canalización después de realizar la mejora:

 **Ejemplo**:<br/>

 En la siguiente columna:<br/>
 **`A\n`**` B C`<br/>

 Antes de realizar la mejora, el receptor de CSV es:<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 Después de realizar la mejora, el receptor de CSV debe ser:<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>Escenario 4: se detectó un problema al leer incorrectamente una cadena vacía como NULL.
 
 Se verá afectado si se encuentra en las siguientes condiciones:
 - Uso del texto delimitado como origen. 
 - El valor NULL se establece en un valor que no está vacío. 
 - El valor de la columna es una cadena vacía y no tiene comillas. 
 
 Antes de realizar la mejora, el valor de columna de cadena vacía sin comillas se lee como NULL. 

 Después de realizar la mejora, la cadena vacía no se analizará como un valor NULL. 
 
 En el ejemplo siguiente se muestra un cambio de comportamiento en la canalización después de realizar la mejora:

 **Ejemplo**:<br/>

 En la siguiente columna:<br/>
  `A, ,B, `<br/>

 Antes de realizar la mejora, el resultado de la columna analizada es:<br/>
  `A null B null`<br/>

 Después de realizar la mejora, el resultado de la columna analizada debe ser:<br/>
  `A "" (empty string) B "" (empty string)`<br/>

###  <a name="internal-server-errors"></a>Errores internos del servidor

A continuación se muestran los escenarios específicos que pueden provocar errores internos del servidor.

#### <a name="scenario-1-not-choosing-the-appropriate-compute-sizetype-and-other-factors"></a>Escenario 1: No elegir el tamaño o tipo de proceso adecuados y otros factores

  La ejecución correcta de flujos de datos depende de muchos factores, como el tamaño y el tipo de proceso, el número de orígenes o receptores que se van a procesar, la especificación de la partición, las transformaciones implicadas, el tamaño de los conjuntos de datos, la asimetría de los datos, etc.<br/>
  
  Para obtener más información, consulte [Rendimiento de Integration Runtime](concepts-integration-runtime-performance.md).

#### <a name="scenario-2-using-debug-sessions-with-parallel-activities"></a>Escenario 2: Uso de sesiones de depuración con actividades paralelas

  Al desencadenar una ejecución mediante la sesión de depuración de flujo de datos con construcciones como ForEach en la canalización, pueden enviarse varias ejecuciones paralelas al mismo clúster. Esta situación puede provocar errores del clúster durante la ejecución, debido a problemas de recursos, como una memoria insuficiente.<br/>
  
  Para enviar una ejecución con la configuración apropiada de IR definida en la actividad de la canalización tras publicar los cambios, seleccione **Desencadenar ahora** o **Depurar** > **Usar tiempo de ejecución de actividad**.

#### <a name="scenario-3-transient-issues"></a>Escenario 3: Errores transitorios

  Los problemas transitorios con microservicios implicados en la ejecución pueden provocar un error en la ejecución.<br/>
  
  La configuración de reintentos en la actividad de canalización puede contribuir a resolver los problemas provocados por errores transitorios. Para más información, consulte [Directiva de actividad](concepts-pipelines-activities.md#activity-json).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda para solucionar problemas, consulte estos recursos:

- [Mensajes y errores comunes de los flujos de datos de asignación](data-flow-troubleshoot-errors.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
