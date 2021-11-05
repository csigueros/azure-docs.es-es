---
title: Data Lake Storage y WANdisco LiveData Platform para Azure
description: Aprenda a migrar petabytes de datos de Hadoop locales a sistemas de archivos de Azure Data Lake Storage Gen2 sin interrumpir las operaciones de los datos ni requerir tiempo de inactividad.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 10/26/2021
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b0b78d1233d6e605691e06c69c9d2167c7ecb592
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019227"
---
# <a name="migrate-on-premises-hadoop-data-to-azure-data-lake-storage-gen2-with-wandisco-livedata-platform-for-azure"></a>Migración de datos de Hadoop locales a Azure Data Lake Storage Gen2 con WANdisco LiveData Platform para Azure

[WANdisco LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) migra petabytes de datos de Hadoop locales a sistemas de archivos de Azure Data Lake Storage Gen2 sin interrumpir las operaciones de los datos ni requerir tiempo de inactividad. Las comprobaciones continuas de la plataforma impiden que los datos se pierdan y, al mismo tiempo, mantienen la coherencia en ambos extremos de la transferencia, aunque se someta a modificaciones.

La plataforma consta de dos servicios. [LiveData Migrator para Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) migra los datos que se usan activamente desde entornos locales a Azure Storage y [LiveData Plane para Azure](https://www.wandisco.com/products/livedata-plane-for-azure) garantiza que todos los datos modificados o los datos de ingesta se replique de manera coherente.

> [!div class="mx-imgBorder"]
> ![Ilustración de información general de Live Data Platform](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Administre ambos servicios mediante Azure Portal y la CLI de Azure. Cada servicio sigue el mismo modelo de facturación de pago por uso medido que todos los demás servicios de Azure: el consumo de datos en LiveData Platform para Azure aparecerá en la factura mensual de Azure, en la que se proporcionarán métricas de uso.

A diferencia de la migración de datos *sin conexión* mediante la [copia de información estática en Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md) o mediante herramientas de Hadoop como [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), puede mantener el funcionamiento completo de los sistemas de negocios durante la migración *en línea* con WANdisco LiveData para Azure. Mantenga sus entornos de macrodatos funcionando incluso mientras mueve sus datos a Azure.

## <a name="key-benefits-of-wandisco-livedata-platform-for-azure"></a>Ventajas clave de WANdisco LiveData Platform para Azure

El motor de consenso compatible con redes de áreas extensas de [WANdisco LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) logra la coherencia de los datos y realiza una replicación de datos en tiempo real a gran escala. Para más información, vea este vídeo:<br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho]

Entre las ventajas calve de la plataforma se incluyen las siguientes:

- **Precisión de los datos**: la validación de los datos de un extremo a otro evita que se pierdan datos y garantiza que los datos transferidos sean adecuados para su uso.

- **Coherencia de los datos**: mantenga los volúmenes de datos coherentes de manera automática entre los distintos entornos, incluso mientras están sometidos a cambios continuos.

- **Eficiencia de los datos**: transfiera volúmenes de datos de gran tamaño de manera continua con control total del consumo de ancho de banda.

- **Eliminación del tiempo de inactividad**: cree, modifique, lea y elimine datos libremente con otras aplicaciones durante la migración, sin necesidad de interrumpir las operaciones empresariales mientras se realiza la transferencia de datos a Azure. Puede continuar ejecutando las aplicaciones, la infraestructura de análisis, los trabajos de ingesta y otros tipos de procesamiento.

- **Uso sencillo**: utilice la integración con Azure de Platform para crear, configurar y programar el progreso de las migraciones automatizadas, además de hacer un seguimiento de ellas. Además, configure la replicación selectiva de datos, los metadatos de Hive, la seguridad de los datos y la confidencialidad según sea necesario.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Características clave de WANdisco LiveData Platform para Azure

Entre las principales características de la plataforma se incluyen las siguientes:

- **Migración de metadatos**: además de los datos de HDFS, migre metadatos (de Hive y otros almacenamientos) con LiveData Migrator para Azure.

- **Transferencia de datos**: utilice LiveData Migrator para Azure para controlar y automatizar cuándo se iniciará la transferencia de datos, lo que elimina la necesidad de migrar manualmente los cambios en los datos.

- **Kerberos**: LiveData Migrator para Azure admite clústeres de Kerberos.

- **Plantillas de exclusión**: cree reglas en LiveData Migrator para Azure a fin de evitar que se migren determinados tamaños o nombres de archivo (definidos mediante patrones globales) al almacenamiento de destino. Cree plantillas de exclusión en Azure Portal o con la CLI y aplíquelas a la cantidad de migraciones que desee.

- **Asignaciones de ruta de acceso**: defina rutas de acceso de destino alternativas para sistemas de archivo de destino determinados, los que trasladarán automáticamente los datos transferidos a los directorios que especifique.

- **Administración del ancho de banda**: configure la cantidad máxima de ancho de banda de red que LiveData Migrator para Azure puede utilizar para evitar el consumo excesivo de este ancho de banda.

- **Exclusiones**: defina las consultas de plantilla que impidan migran cualquier archivo y directorio que cumpla con los criterios, lo que le permite migrar datos de manera selectiva desde su sistema de origen.

- **Métricas**: consulte los detalles sobre la transferencia de datos en LiveData Migrator para Azure, por ejemplo, los archivos transferidos en el tiempo, las rutas de acceso excluidas, los elementos que no se pudieron transferir y mucho más.


   > [!div class="mx-imgBorder"]
   > ![LiveData](./media/migrate-gen2-wandisco-live-data-platform/metrics-1.png)

## <a name="migrate-big-data-faster-without-risk"></a>Migración de macrodatos con mayor rapidez sin riesgo

El primer servicio incluido en WANDISCO LiveData Platform para Azure es [LiveData Migrator para Azure](https://www.wandisco.com/products/livedata-migrator-for-azure), que migra los datos desde entornos locales a Azure Storage. Una vez que implemente LiveData Migrator en el clúster de Hadoop local, se creará automáticamente la mejor configuración para su sistema de archivos. Desde allí, proporcione los detalles de Kerberos para el sistema. LiveData Migrator para Azure estará listo para migrar datos a Azure Storage.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator para la arquitectura de Azure ](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture-1.png)

Antes de empezar a trabajar con LiveData Migrator para Azure, revise estos [requisitos previos](https://docs.wandisco.com/live-data-platform/docs/prereq/).

Para realizar una migración:

1. En la CLI de Azure: 

   - Ejecute `az provider register --namespace Wandisco.Fusion --consent-to-permissions` en la CLI de Azure para registrarse en el proveedor de recursos de WANdisco.
   - Ejecute `az vm image terms accept --offer ldma --plan metered-v1 --publisher Wandisco --subscription <subscriptionID>` para aceptar los términos y condiciones de la facturación de uso medido de LiveData Platform.

2. Implemente una instancia de LiveData Migrator de Azure Portal en el clúster de Hadoop local. (No es necesario que realice ningún cambio en el clúster ni que lo reinicie).

   > [!div class="mx-imgBorder"]
   > ![Creación de una instancia de LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

   > [!NOTE]
   > WANdisco LiveData Migrator para Azure ofrece la opción de crear un clúster de prueba de Hadoop.

3. Si corresponde, configure los detalles de Kerberos.

4. Defina la cuenta de almacenamiento de destino habilitada para Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   > ![Creación de una instancia de destino de LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

5. Defina la ubicación de los datos que quiere migrar; por ejemplo, `/user/hive/warehouse`.

   > [!div class="mx-imgBorder"]
   > ![Creación de una migración de LiveData Migrator](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

6. Inicio de la migración

Supervise el progreso de la migración a través de herramientas estándar de Azure, como la CLI de Azure y Azure Portal.

Si desea instrucciones más detalladas, vea la [serie de vídeos de procedimientos sobre LiveData Migrator para Azure](https://fast.wistia.com/embed/channel/qg51p8erky).

## <a name="bidirectionally-replicate-data-under-active-change-with-livedata-plane-for-azure"></a>Replicación bidireccional de datos bajo cambio activo con LiveData Plane para Azure

El segundo servicio incluido en LiveData Platform es [LiveData Plane para Azure](https://www.wandisco.com/products/livedata-plane-for-azure). LiveData Plane utiliza el motor de coordinación de WANdisco para mantener la coherencia de los datos en muchos clústeres de Hadoop locales y Azure Storage mediante la aplicación inteligente de cambios a los datos en todos los sistemas, lo que quita el riesgo de conflictos de datos en distintos puntos de uso.

> [!div class="mx-imgBorder"]
> ![LiveData Plane para la arquitectura de Azure ](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Después de la migración inicial, mantenga la coherencia de los datos con LiveData Plane para Azure:

1. Implemente LiveData Plane para Azure en el entorno local y en Azure, empezando por Azure Portal. No es necesario que haga ningún cambio en la aplicación.

2. Configure las reglas de replicación que cubren las ubicaciones de datos de las cuales quiera mantener la coherencia; por ejemplo, `/user/contoso/sales/region/WA`.

3. Ejecute aplicaciones que accedan a los datos y los modifiquen en cualquier ubicación según sea necesario.

LiveData Plane para Azure replica de manera coherente los cambios de datos en todos los entornos sin afectar significativamente el rendimiento de las aplicaciones o el funcionamiento del clúster.

## <a name="test-drive-or-trial"></a>Versión de prueba o evaluación

En la [página de LiveData Platform para Azure en Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview), tiene dos opciones:

- El botón **Obténgalo ahora** inicia el servicio en su suscripción. Desde allí, puede utilizar su propio clúster de Hadoop o el clúster de evaluación de WANdisco.

- Haga clic en **Versión de prueba** para probar LiveData Migrator para Azure en un entorno preconfigurado y hospedado. Esto le permite probar LiveData Migrator para Azure antes de agregarlo a la suscripción, sin ningún costo ni riesgo para los datos.

Mire el [vídeo de demostración de la versión de prueba](https://fast.wistia.net/embed/channel/qg51p8erky?wchannelid=qg51p8erky&wmediaid=ute6gsc60w) para ver la versión de prueba en acción.

## <a name="next-steps"></a>Pasos siguientes

- [Planee y cree una migración en LiveData Migrator para Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma).

## <a name="see-also"></a>Consulte también

- [LiveData Migrator para Azure en Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview)

- [Planes y precios de LiveData Migrator para Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=PlansAndPricee)

- [Preguntas más frecuentes sobre LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Problemas conocidos de LiveData Platform para Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)
