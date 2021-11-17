---
title: Procedimiento para la captura manual del volcado de memoria del montón, el volcado de memoria de subprocesos y JFR en Azure Spring Cloud
description: Aprenda a capturar manualmente un volcado de memoria del montón, un volcado de subprocesos o a iniciar JFR.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5f34dce5e177650b11858cabf770b7d63dc9b593
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000591"
---
# <a name="how-to-manually-capture-heap-dump-thread-dump-and-jfr-in-azure-spring-cloud"></a>Procedimiento para la captura manual del volcado de memoria del montón, el volcado de memoria de subprocesos y JFR en Azure Spring Cloud

**Este artículo se aplica a:** ✔️ Java ❌ C#

La solución de problemas eficaz, que es fundamental para que nuestros clientes corrijan a tiempo los problemas que aparecen en su entorno de producción, garantiza que su negocio siempre esté en línea. Hoy, en Azure Spring Cloud, ya hemos proporcionado streaming y consulta de registros de aplicaciones, emisión de gran cantidad de métricas y alertas, seguimiento distribuido, etc. para ayudar a nuestros clientes en este campo. Sin embargo, cuando los clientes reciben alertas sobre la solicitud de una latencia elevada, una fuga en el montón de JVM o un uso elevado de la CPU, no hay ninguna solución "in extremis" para ellos. Por lo tanto, hemos habilitado la generación manual de un volcado de memoria del montón, la generación de un volcado de subprocesos o el inicio de un JFR.

## <a name="prerequisites"></a>Requisitos previos
Para completar este ejercicio, necesitará lo siguiente:

* Una instancia del servicio Azure Spring Cloud implementada. Para comenzar, siga nuestro [inicio rápido sobre la implementación de una aplicación mediante la CLI de Azure](./quickstart.md).
* Al menos una aplicación ya creada en su instancia de servicio.
* Al menos un [almacenamiento persistente ya enlaza en la aplicación](how-to-built-in-persistent-storage.md) para guardar los archivos de diagnóstico generados.

**Si los clientes desean usar la ruta de acceso en la ruta de acceso de montaje, asegúrese de que ya se ha creado la ruta de acceso secundaria.**

## <a name="generate-a-heap-dump"></a>Generación de un volcado de memoria del montón
Genere un volcado de memoria del montón de nuestra aplicación en Azure Spring Cloud.
```heap dump command
   az spring-cloud app deployment generate-heap-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="generate-a-thread-dump"></a>Generación de un volcado de memoria de subprocesos
Genere un volcado de memoria de subprocesos de nuestra aplicación en Azure Spring Cloud.
```thread dump command
   az spring-cloud app deployment generate-thread-dump -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path>
```

## <a name="start-jfr"></a>Inicio de JFR
Inicie un JFR de nuestra aplicación en Azure Spring Cloud.
```JFR command
   az spring-cloud app deployment start-JFR -g <resource-group-name> -s <service-instance-name> --app <app-name> --deployment <deployment-name> --app-instance <app-instance name> --file-path <your-target-file-path-in-your-persistent-storage-mount-path> --duration <duration-of-JFR>
```
El valor predeterminado de duration es 60 segundos.
## <a name="get-your-diagnostic-files"></a>Obtención de archivos de diagnóstico
Vaya a la ruta de acceso del archivo de destino en el almacenamiento persistente y busque el volcado o JFR. Puede descargarlos en la máquina local. El nombre del archivo generado será "{appInstance} \_heapdump\_ {timeStamp}.hprof" en el caso del volcado de memoria del montón, "{appInstance} \_threaddump\_ {timeStamp}.txt" en el caso del volcado de subprocesos y "{appInstance} \_JFR\_ {timeStamp}.jfr" en el caso de JFR.
