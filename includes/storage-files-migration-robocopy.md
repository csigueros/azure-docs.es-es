---
title: Archivo de inclusión
description: archivo de inclusión
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 52e1accfb5f5bb762cc2833a19e1caa3daa4a03d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462203"
---
```console
robocopy /MT:128 /R:1 /W:1 /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Conmutador                | Significado |
|-----------------------|---------|
| `/MT:n`               | Permite que Robocopy se ejecute en modo multiproceso. El valor predeterminado para `n` es 8. La cantidad máxima es de 128 subprocesos. Comience con un número elevado de subprocesos para una ejecución inicial. Un número elevado de subprocesos ayuda a saturar el ancho de banda disponible. Las ejecuciones subsiguientes de `/MIR` se ven afectadas progresivamente por el proceso disponible en comparación con el ancho de banda de red disponible. Para las ejecuciones posteriores, haga coincidir más estrechamente el valor del número de subprocesos con el número de núcleos del procesador y el número de subprocesos por núcleo. Considere si es necesario reservar los núcleos para otras tareas que quizá tenga un servidor de producción. |
| `/R:n`                | Número máximo de reintentos para un archivo que no se puede copiar en el primer intento. Puede mejorar la velocidad de una ejecución de Robocopy especificando un número máximo (`n`) de reintentos antes de que el archivo no se pueda copiar permanentemente en la ejecución. Este conmutador funciona cuando ya está claro que habrá más ejecuciones de Robocopy. Si el archivo no se puede copiar en la ejecución actual, se volverá a intentar en el siguiente trabajo de Robocopy. Los archivos que generan errores porque estaban en uso o debido a problemas de tiempo de espera pueden copiarse correctamente con esta estrategia. |
| `/W:n`                | Especifica el tiempo que espera Robocopy antes de intentar copiar un archivo que no se ha copiado correctamente en el último intento. `n` es el número de segundos de espera entre reintentos. `/W:n` a menudo se usa junto con `/R:n`. |
| `/B`                  | Ejecuta Robocopy en el mismo modo que usaría una aplicación de copia de seguridad. Este conmutador permite que Robocopy mueva los archivos para los que el usuario actual no tiene permisos. |
| `/MIR`                | (Reflejar origen en destino). Permite que Robocopy solo tenga que copiar las diferencias entre el origen y el destino. Se copiarán los subdirectorios vacíos. Se copiarán los elementos (archivos o carpetas) que hayan cambiado o no existan en el destino. Los elementos que existan en el destino, pero no en el origen, se purgarán (se eliminarán) del destino. Cuando use este conmutador, haga coincidir exactamente con las estructuras de carpetas de origen y de destino. *Coincidencia* significa que se copia desde el nivel de carpeta y origen correctos en el nivel de carpeta del destino coincidente. Solo entonces se puede realizar correctamente una copia de "puesta al día". Cuando el origen y el destino no coinciden, el uso de `/MIR` dará lugar a eliminaciones y nuevas copias a gran escala. |
| `/IT`                 | Garantiza que se conserve la fidelidad en ciertos escenarios de reflejo. </br>Por ejemplo, si un archivo experimenta un cambio de ACL y una actualización de atributo entre dos ejecuciones de Robocopy, se marca como oculto. Sin `/IT`, Robocopy podría omitir el cambio de ACL y no se transferiría a la ubicación de destino. |
|`/COPY:[copyflags]`    | Fidelidad de la copia del archivo. Predeterminado: `/COPY:DAT`. Marcas de copia: `D` = datos, `A` = atributos, `T` = marcas de tiempo, `S` = seguridad = ACL de NTFS, `O` = información del propietario, `U` = información de a<u>u</u>ditoría. No se puede almacenar la información de auditoría en un recurso compartido de archivos de Azure. |
| `/DCOPY:[copyflags]`  | Fidelidad de la copia de directorios. Predeterminado: `/DCOPY:DA`. Marcas de copia: `D` = Datos, `A` = Atributos, `T` = Marcas de tiempo. |
| `/NP`                 | Especifica que no se mostrará el progreso de la copia de cada archivo y carpeta. Mostrar el progreso reduce significativamente el rendimiento de la copia. |
| `/NFL`                | Especifica que los nombres de archivo no se han registrado. Mejora el rendimiento de la copia. |
| `/NDL`                | Especifica que los nombres de directorio no se han registrado. Mejora el rendimiento de la copia. |
| `/UNILOG:<file name>` | Escribe el estado en el archivo de registro como Unicode. (Sobrescribe el registro existente). |
| `/L`                  | **Solo para una serie de pruebas** </br> Los archivos solo se mostrarán en la lista. No se copiarán, no se eliminarán y no tendrán marca de tiempo. Por lo general, se usa con `/TEE` para la salida de la consola. Es posible que las marcas del script de ejemplo, como `/NP`, `/NFL` y `/NDL`, se tengan que quitar para lograr los resultados de la prueba documentados correctamente. |
| `/LFSM`               | **Solo para destinos con almacenamiento en capas** </br>Especifica que Robocopy funciona en "modo de espacio libre bajo". Este conmutador solo es útil para destinos con almacenamiento en capas que pueden quedarse sin capacidad local antes de que Robocopy finalice. Se agregó específicamente para su uso con un destino habilitado de nube por niveles de Azure File Sync. Se puede usar con independencia de Azure File Sync. En este modo, Robocopy se pondrá en pausa siempre que una copia de archivo haga que el espacio disponible del volumen de destino se sitúe por debajo de un valor de "suelo". El formato `/LFSM:n` de la marca puede especificar este valor. El parámetro `n` se especifica en la base 2: `nKB`, `nMB` o `nGB`. Si `/LFSM` se especifica sin ningún valor floor explícito, floor se establece en el 10 por ciento del tamaño del volumen de destino. El modo de espacio libre bajo no es compatible con `/MT`, `/EFSRAW`, `/B` o `/ZB`. |
| `/Z`                  | **Usar con cautela** </br>Copia los archivos en modo de reinicio. Este conmutador solo se recomienda en un entorno de red inestable. Reduce significativamente el rendimiento de la copia debido al registro adicional. |
| `/ZB`                 | **Usar con cautela** </br>Usa el modo de reinicio. Si se deniega el acceso, esta opción utiliza el modo de copia de seguridad. Esta opción reduce significativamente el rendimiento de la copia debido a los puntos de control. |
