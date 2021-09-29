---
title: Data Lake Storage y WANdisco LiveData Platform para Azure (versión preliminar)
description: Migre los datos de Hadoop locales a Azure Data Lake Storage Gen2 mediante WANdisco LiveData Platform para Azure.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: cee20d5104ceb09dc0ba3d826b28108e543cc3d1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664910"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Cumplimiento de los requisitos de migración exigidos con WANdisco LiveData Platform para Azure (versión preliminar)

Migre los datos de Hadoop locales a Azure Data Lake Storage Gen2 mediante [WANdisco LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/landing/). Esta plataforma elimina el tiempo de inactividad de la aplicación, la posibilidad de que se pierdan datos y garantiza la coherencia de los datos incluso mientras las operaciones continúan ejecutándose en el entorno local.

> [!NOTE]
> WANdisco LiveData Platform para Azure está en versión preliminar pública. Para obtener disponibilidad regional, consulte las [Regiones admitidas](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions).

La plataforma consta de dos servicios: [LiveData Migrator para Azure](https://www.wandisco.com/products/livedata-migrator-for-azure), que es útil para migrar los datos que se usan activamente desde entornos locales a Azure Storage y [LiveData Plane para Azure](https://www.wandisco.com/products/livedata-plane-for-azure), que garantiza que todos los datos modificados o los datos de ingesta se replican de forma coherente.

> [!div class="mx-imgBorder"]
> ![Ilustración de la información general de Live Data Platform](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Puede administrar ambos servicios mediante Azure Portal y la CLI de Azure; asimismo, ambos siguen el mismo modelo de facturación de uso medido y de pago por uso, igual que el resto de los servicios de Azure. El consumo de LiveData Platform para Azure aparecerá en la misma factura mensual de Azure y le proporcionará una manera coherente y cómoda de realizar un seguimiento y supervisar el uso que haga del servicio.

A diferencia de la migración de datos *sin conexión* mediante la [copia de información estática en Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md) o mediante herramientas de Hadoop como [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), puede mantener el funcionamiento completo de los sistemas de negocios durante la migración *en línea* con WANdisco LiveData para Azure. Mantenga sus entornos de macrodatos funcionando incluso mientras mueve sus datos a Azure.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Características clave de WANdisco LiveData Platform para Azure

[WANdisco LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) usa un motor de consenso único que es compatible con redes de áreas extensas, para así poder lograr la coherencia de los datos y realizar la replicación de los mismos a escala mientras que las aplicaciones pueden continuar modificando esos datos en la replicación. <br><br>

> [!VIDEO https://www.youtube.com/embed/KRrmcYPxEho]

Entre las principales características de la plataforma se incluyen las siguientes:

- **Coherencia de los datos**: solucione el desafío que supone de migrar grandes volúmenes de datos entre entornos y mantenerlos de forma coherente en la migración de rendimiento de los sistemas de almacenamiento, aunque estén cambiando continuamente. Emplee el motor de consenso único compatible con redes de áreas extensas de WANdisco directamente en Azure para lograr la coherencia de los datos y poder migrarlos garantizando su coherencia incluso si se realizan cambios en los mismos.

- **Mantenimiento de las operaciones**: dado que las aplicaciones pueden continuar creando, modificando, leyendo y eliminando datos durante la migración, no es necesario interrumpir las operaciones de su negocio ni introducir un periodo de interrupción solo para migrar los macrodatos a Azure. Puede continuar ejecutando las aplicaciones, la infraestructura de análisis, los trabajos de ingesta y otros tipos de procesamiento.

- **Validación de los resultados**: la validación de un extremo a otro de los datos se puede usar una vez que se hayan migrado a Azure y requiere que se ejecuten las cargas de trabajo de la aplicación de producción en los datos. Solo un servicio de LiveData proporciona esta opción sin el riesgo que supone la divergencia de datos, ya que mantiene la coherencia de los datos, independientemente de si se produce algún cambio en el origen o el destino de la migración. Puede probar y validar el comportamiento de la aplicación sin riesgo y sin que cambien los procesos y sistemas.

- **Complejidad reducida**: elimine la necesidad de crear y administrar trabajos programados para copiar datos mediante la migración de esos datos a través de Automation. Use la integración profunda con Azure como plano de control para administrar y supervisar el progreso de la migración, incluida la replicación selectiva de datos, los metadatos de Hive, la seguridad de los datos y la confidencialidad.

- **Eficacia**: mantenga un alto rendimiento y escale fácilmente a volúmenes de macrodatos. Gracias al control del consumo de ancho de banda, puede satisfacer sus objetivos de migración sin afectar a otras operaciones del sistema.

## <a name="migrate-big-data-faster-without-risk"></a>Migración de macrodatos con mayor rapidez sin riesgo

El primer servicio de WANdisco LiveData Platform para Azure es [LiveData Migrator para Azure](https://www.wandisco.com/products/livedata-migrator-for-azure), una solución para migrar los datos que se usan de forma activa desde entornos locales a Azure Storage. LiveData Migrator para Azure se aprovisiona y administra por completo desde Azure Portal o la CLI de Azure, y funciona junto con el clúster de Hadoop local sin tener que realizar ningún cambio de configuración, modificar aplicaciones o reiniciar el servicio para empezar a migrar los datos inmediatamente.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator para la arquitectura de Azure ](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

Las migraciones de macrodatos pueden ser complejas y desafiantes. Hasta ahora, mover petabytes de información sin interrumpir las operaciones de su negocio no se podía lograr con tecnologías de copia de datos sin conexión. [LiveData Migrator para Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) le ofrece una implementación sencilla, gracias a la cual puede establecer un servicio de LiveData con una migración y replicación continua de datos a la vez que las aplicaciones leen, escriben y modifican los datos que se están migrando.

Para realizar una migración solo debe seguir estos tres sencillos pasos:

1. Aprovisione la instancia de LiveData Migrator de Azure Portal al clúster de Hadoop local. No es necesario que cambie el clúster ni que establezca ningún tiempo de inactividad; las aplicaciones pueden seguir funcionando.

   > [!div class="mx-imgBorder"]
   > ![Creación de una instancia de LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Defina la cuenta de almacenamiento de destino habilitada para Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   > ![Creación de una instancia de destino de LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Defina la ubicación de los datos que quiera migrar (por ejemplo, `/user/hive/warehouse`) e inicie la migración.

   > [!div class="mx-imgBorder"]
   > ![Creación de una migración de LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Supervise el progreso de la migración a través de las herramientas estándar de Azure, incluyendo la CLI de Azure y Azure Portal, y siga usando su entorno local durante el proceso. Antes de empezar, consulte estos [requisitos previos](https://docs.wandisco.com/live-data-platform/docs/prereq/).

## <a name="replicate-data-under-active-change"></a>Replicación de datos bajo cambio activo

Las migraciones a gran escala de los lagos de datos locales a Azure necesitan que las aplicaciones se prueben y se validen. Poder hacer esto sin el riesgo de introducir cambios en los datos que crearán varios orígenes verdaderos que no se pueden conciliar fácilmente es fundamental para eliminar ese riesgo y minimizar el costo de pasar a Azure. [LiveData Plane para Azure](https://www.wandisco.com/products/livedata-plane-for-azure) usa la tecnología del motor de coordinación de WANdisco para solucionar estos problemas.

> [!div class="mx-imgBorder"]
> ![LiveData Plane para la arquitectura de Azure ](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Mantenga la coherencia de los datos en los clústeres de Hadoop locales y en Azure Storage con LiveData Plane para Azure después realizar la migración inicial:

1. Aprovisione LiveData Plane para Azure de forma local y en Azure, empezando por Azure Portal. No es necesario que haga ningún cambio en la aplicación.

2. Configure las reglas de replicación que cubren las ubicaciones de datos de las cuales quiera mantener la coherencia; por ejemplo, `/user/contoso/sales/region/WA`.

3. Ejecute aplicaciones que obtengan acceso y modifiquen los datos de cualquier ubicación como si fueran un sistema de archivos compatible con Hadoop, según sea necesario.

LiveData Plane para Azure mantiene la coherencia de los datos sin sobrecargar de forma significativa el rendimiento de la aplicación o el funcionamiento del clúster. Modifique o ingiera los datos mientras todos los cambios se replican de forma coherente.

## <a name="next-steps"></a>Pasos siguientes

- [LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) se usa como cualquier otro recurso de Azure y está disponible en la versión preliminar.

- Consulte los [requisitos previos](https://docs.wandisco.com/live-data-platform/docs/prereq/), planee la migración y realice rápidamente una migración a gran escala con LiveData Migrator para Azure.

- Pruebe LiveData Migrator sin necesidad de tener un clúster de Hadoop local mediante el [espacio aislado HDFS](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/).

## <a name="see-also"></a>Consulte también

- [LiveData Migrator para Azure en Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [LiveData Plane para Azure en Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [Planes y precios de LiveData Migrator para Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Planes y precios de LiveData Plane para Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice)

- [Preguntas más frecuentes sobre LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Problemas conocidos de LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Introducción a Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
