---
title: Guía de solución de problemas
titleSuffix: Microsoft Genomics
description: Obtenga información sobre las estrategias de solución de problemas para usar Microsoft Genomics, incluidos los mensajes de error y cómo resolverlos.
keywords: solución de problemas, error, depuración
services: genomics
author: vigunase
ms.author: vigunase
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: 580acb1db02530d1c9ef40d9c116ea5a661bd570
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259815"
---
# <a name="troubleshooting-guide"></a>Guía de solución de problemas

Estas son algunas sugerencias de solución para algunos de los problemas comunes que puede encontrarse al usar el servicio Microsoft Genomics (MSGEN).

 Para ver las preguntas más frecuentes no relacionadas con la solución de problemas, consulte [Preguntas frecuentes](frequently-asked-questions-genomics.yml).
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Paso 1: Buscar códigos de error asociados con el flujo de trabajo

Puede encontrar los mensajes de error asociados con el flujo de trabajo:

1. Usando la línea de comandos y escribiendo `msgen status`
2. Examinando el contenido de standardoutput.txt

### <a name="1-using-the-command-line-msgen-status"></a>1. Usando la línea de comandos `msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Hay tres argumentos obligatorios:

* URL: el URI base de la API.
* KEY: la clave de acceso de la cuenta de Genomics.
    * Vaya a Azure Portal y abra la página de la cuenta de Genomics para buscar los valores de URL y KEY. En el encabezado **Administración**, elija **Claves de acceso**. Allí encontrará la URL de la API y las claves de acceso.

  
* ID: el identificador del flujo de trabajo.
    * Sirve para encontrar el tipo de identificador de flujo de trabajo en el comando `msgen list`. Suponiendo que el archivo de configuración contiene la dirección URL y las claves de acceso, y que se encuentra en la misma ubicación que el archivo ejecutable msgen, el comando tendrá un aspecto similar al siguiente: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        La salida de este comando debe ser así:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  Como alternativa, puede incluir la ruta de acceso al archivo de configuración en lugar de escribir directamente la URL y la clave. Tenga en cuenta que si incluye estos argumentos en la línea de comandos, así como el archivo de configuración, los argumentos de la línea de comandos tendrán prioridad.  

Para el identificador 1001 del flujo de trabajo y el archivo config.txt colocado en la misma ruta que el ejecutable msgen, el comando tendrá este aspecto:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.  Examinando el contenido de standardoutput.txt 
Busque el contenedor de salida del flujo de trabajo en cuestión. MSGEN crea una carpeta `[workflowfilename].logs.zip` después de ejecutar cada flujo de trabajo. Abra la carpeta para ver el contenido:

* outputFileList.txt: una lista de los archivos de salida generados durante el flujo de trabajo
* StandardError.txt: este archivo está en blanco.
* standardoutput.txt: registra todos los mensajes de estado de nivel superior, incluidos los errores, que se produjeron mientras se ejecutaba el flujo de trabajo.
* Archivos de registro GATK: el resto de los archivos de la carpeta `logs`.

Para solucionar el problema, examine el contenido de standardoutput.txt y anote los mensajes de error que aparecen.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Paso 2: Probar los pasos recomendados para los errores comunes

En esta sección se explica brevemente la salida de errores comunes del servicio Microsoft Genomics (msgen) y las estrategias para resolverlos. 

El servicio Microsoft Genomics (msgen) puede producir los siguientes dos tipos de errores:

1. Errores internos del servicio: errores internos del servicio que no se pueden resolver corrigiendo los parámetros o archivos de entrada. A veces, estos errores podrían resolverse volviendo a enviar el flujo de trabajo.
2. Errores de entrada: los errores que se pueden resolver mediante los argumentos correctos o corrigiendo los formatos de archivo.

### <a name="1-internal-service-errors"></a>1. Errores internos del servicio

Los usuarios no pueden corregir errores internos del servicio. Puede volver a enviar el flujo de trabajo, pero si esto no funciona, póngase en contacto con el equipo de soporte técnico de Microsoft Genomics.

| Mensaje de error                                                                                                                            | Pasos recomendados para la solución de problemas                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Se ha producido un error interno. Pruebe a volver a enviar el flujo de trabajo. Si vuelve a ver este error, póngase en contacto con el equipo de soporte técnico de Microsoft Genomics para obtener ayuda. | Envíe el flujo de trabajo de nuevo. Póngase en contacto con el equipo de soporte técnico de Microsoft Genomics para obtener ayuda si persiste el problema creando una [incidencia de soporte técnico](file-support-ticket-genomics.md ). |

### <a name="2-input-errors"></a>2. Errores de entrada

Estos errores sí pueden corregirlos los usuarios. Según el tipo de archivo y el código de error, el servicio Microsoft Genomics genera códigos de error distintos. Siga los pasos de solución de problemas recomendados que se enumeran a continuación.

| Tipo de archivo | Código de error | Mensaje de error                                                                           | Pasos recomendados para la solución de problemas                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Any          | 701        | La lectura [identificadorLectura] tiene [númeroDeBases], pero el límite es [longitudLectura Máxima]           | La razón más común de este error es un daño en los archivos que provoca la concatenación de dos lecturas. Compruebe los archivos de entrada. |
| BAM          | 200        |   No se puede leer el archivo "[nombreArchivo]".                                                                                       | Compruebe el formato del archivo BAM. Envíe de nuevo el flujo de trabajo con un archivo que tenga el formato correcto.                                                                           |
| BAM          | 201        |  No se puede leer el archivo BAM [nombre_archivo].                                                                                      |Compruebe el formato del archivo BAM.  Envíe el flujo de trabajo con un archivo que tenga el formato correcto.                                                                            |
| BAM          | 202        | No se puede leer el archivo BAM [nombre_archivo]. El archivo es demasiado pequeño y faltan el encabezado.                                                                                        | Compruebe el formato del archivo BAM.  Envíe el flujo de trabajo con un archivo que tenga el formato correcto.                                                                            |
| BAM          | 203        |   No se puede leer el archivo BAM [nombre_archivo]. El encabezado o el archivo estaban dañados.                                                                                      |Compruebe el formato del archivo BAM.  Envíe el flujo de trabajo con un archivo que tenga el formato correcto.                                                                           |
| BAM          | 204        |    No se puede leer el archivo BAM [nombre_archivo]. El encabezado o el archivo estaban dañados.                                                                                     | Compruebe el formato del archivo BAM.  Envíe el flujo de trabajo con un archivo que tenga el formato correcto.                                                                           |
| BAM          | 205        |    No se puede leer el archivo BAM [nombre_archivo]. El encabezado o el archivo estaban dañados.                                                                                     | Compruebe el formato del archivo BAM.  Envíe el flujo de trabajo con un archivo que tenga el formato correcto.                                                                            |
| BAM          | 206        |   No se puede leer el archivo BAM [nombre_archivo]. El encabezado o el archivo estaban dañados.                                                                                      | Compruebe el formato del archivo BAM.  Envíe el flujo de trabajo con un archivo que tenga el formato correcto.                                                                            |
| BAM          | 207        |  No se puede leer el archivo BAM [nombre_archivo]. Archivo truncado cerca del desplazamiento [desplazamiento].                                                                                       | Compruebe el formato del archivo BAM.  Envíe el flujo de trabajo con un archivo que tenga el formato correcto.                                                                            |
| BAM          | 208        |   Archivo BAM no válido. El valor de ReadID [Identificador_lectura] no tiene ninguna secuencia en el archivo [nombre_archivo].                                                                                      | Compruebe el formato del archivo BAM.  Envíe el flujo de trabajo con un archivo que tenga el formato correcto.                                                                             |
| FASTQ        | 300        |  No se puede leer el archivo FASTQ. [Nombre_archivo] no termina con una nueva línea.                                                                                     | Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                           |
| FASTQ        | 301        |   No se puede leer el archivo FASTQ [nombre_archivo]. El registro de archivos FASTQ es mayor que el tamaño de búfer en el desplazamiento: [_desplazamiento]                                                                                      | Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                         |
| FASTQ        | 302        |     Error de sintaxis de FASTQ. El archivo [nombre_archivo] tiene una línea en blanco.                                                                                    | Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                         |
| FASTQ        | 303        |       Error de sintaxis de FASTQ. El archivo [nombre_ archivo] tiene un carácter inicial no válido en el desplazamiento [_desplazamiento], tipo de línea [tipo_línea] y carácter [_carácter].                                                                                  | Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                         |
| FASTQ        | 304      |  Error de sintaxis de FASTQ en readID [_IdentificadorLectura].  La primera lectura del lote no tiene un valor de readID terminado en /1 en el archivo [nombre_archivo]                                                                                       | Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                         |
| FASTQ        | 305        |  Error de sintaxis de FASTQ en readID [_identificadorLectura]. La segunda lectura del lote no tiene un valor de readID terminado en /2 en el archivo [nombre_archivo]                                                                                      | Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                          |
| FASTQ        | 306        |  Error de sintaxis de FASTQ en readID [_IdentificadorLectura]. La primera lectura del par no tiene un identificador que termina en /1 en el archivo [nombre_archivo]                                                                                       | Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                          |
| FASTQ        | 307        |   Error de sintaxis de FASTQ en readID [_IdentificadorLectura]. El valor de ReadID no termina con /1 o /2. El archivo [nombre_ archivo] no se puede usar como un archivo FASTQ emparejado.                                                                                      |Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                          |
| FASTQ        | 308        |  Error de lectura de FASTQ. Las lecturas de ambos extremos respondieron de forma diferente. ¿Ha elegido los archivos FASTQ correctos?                                                                                       | Corrija el formato del archivo FASTQ y envíe el flujo de trabajo de nuevo.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Paso 3: Ponerse en contacto con el equipo de soporte técnico de Microsoft Genomics

Si sigue obteniendo errores en el trabajo, o si tiene alguna otra pregunta, póngase en contacto con el equipo de soporte técnico de Microsoft Genomics desde Azure Portal. Encontrará información adicional sobre cómo enviar una solicitud de soporte en [aquí](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió a solucionar errores y problemas comunes con el servicio Microsoft Genomics. Para obtener más información y preguntas frecuentes, vea [Preguntas frecuentes](frequently-asked-questions-genomics.yml). 
