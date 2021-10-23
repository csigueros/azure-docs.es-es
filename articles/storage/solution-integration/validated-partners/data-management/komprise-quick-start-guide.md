---
title: Análisis y migración de los datos de archivo a Azure con Komprise Intelligent Data Manager
titleSuffix: Azure Storage
description: Guía de introducción para implementar Komprise Intelligent Data Manager. En la guía se muestra cómo analizar la infraestructura de archivos y migrar los datos a Azure Files, Azure NetApp Files, Azure Blob Storage o cualquier solución NAS de ISV disponible
author: dukicn
ms.author: nikoduki
ms.date: 05/20/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: a6333ef4385439afa2e2f0000ae3f452357aa958
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856827"
---
# <a name="analyze-and-migrate-to-azure-with-komprise"></a>Análisis y migración a Azure con Komprise

Este artículo facilita la integración de la infraestructura de Komprise Intelligent Data Management con los servicios de almacenamiento de Azure. Incluye consideraciones e instrucciones de implementación sobre cómo analizar y migrar los datos.

Komprise proporciona análisis e información sobre los archivos y los objetos de datos almacenados en sistemas de almacenamiento conectados a la red (NAS), y sobre los almacenes de objetos, tanto locales como en la nube.  Permite la migración de datos a servicios de almacenamiento de Azure como Azure Files, Azure NetApp Files, Azure Blob Storage u otras soluciones NAS de ISV. Obtenga más información sobre [las soluciones de asociados comprobadas para el almacenamiento principal y secundario](../primary-secondary-storage/partner-overview.md).

Entre los casos de uso más habituales de Komprise se incluyen:

- Análisis de datos no estructurados de archivos y objetos para obtener información sobre la administración de datos, el movimiento, el posicionamiento, el archivado, la protección y la contención.
- Migración de datos de archivos a Azure Files, Azure NetApp Files o una solución NAS de ISV.
- Almacenamiento por niveles basado en directivas y el archivado de datos de archivos en Azure Blob Storage al tiempo que se conserva un acceso sencillo desde la solución NAS original y se permite el acceso a objetos nativos en Azure.
- Copia de datos de archivos en Azure Blob Storage basado en programaciones configurables, al tiempo que se conserva el acceso a objetos nativos de Azure.
- Migración de datos de objetos a Azure Blob Storage.
- Administración por niveles y por ciclo de vida de los datos de objetos en los niveles de acceso frecuente, esporádico y de archivo de Azure Blob Storage en función de la hora del último acceso.

## <a name="reference-architecture"></a>Arquitectura de referencia

En el siguiente diagrama se proporciona una arquitectura de referencia para las implementaciones locales a Azure y dentro de Azure.

:::image type="content" source="./media/komprise-quick-start-guide/komprise-architecture.png" alt-text="La arquitectura de referencia describe la configuración básica de Komprise Intelligent Data Manager":::

En el diagrama siguiente se proporciona una arquitectura de referencia para migrar cargas de trabajo de objetos locales y en la nube a Azure Blob Storage.

:::image type="content" source="./media/komprise-quick-start-guide/komprise-architecture-blob.png" alt-text="La arquitectura de referencia describe la configuración para migrar cargas de trabajo de objetos locales y en la nube a Azure Blob Storage":::

Komprise es una solución de software que se implementa fácilmente en un entorno virtual. La solución consta de lo siguiente:
- **Director**: la consola de administración de Komprise Grid. Se usa para configurar el entorno, supervisar actividades, ver informes y gráficos, y establecer directivas.
- **Observer**: administra y analiza recursos compartidos, resumen informes, se comunican con Director y controlan el tráfico de objetos y de datos de NFS.
- **Proxies**: simplifican y aceleran el flujo de datos SMB/CIFS y se escalan con facilidad para satisfacer los requisitos de rendimiento de un entorno en crecimiento.

## <a name="before-you-begin"></a>Antes de empezar

La planificación por adelantado ayudará a migrar los datos con menos riesgo.

### <a name="get-started-with-azure"></a>Introducción a Azure

Microsoft ofrece un marco para empezar a trabajar con Azure. [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) es un enfoque detallado para la transformación digital empresarial y una guía completa para la planificación de una adopción de la nube de calidad de producción. CAF incluye una [guía paso a paso de configuración de Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) para ayudarle a ponerse en marcha de forma rápida y segura. Puede encontrar una versión interactiva en [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Encontrará arquitecturas de ejemplo, procedimientos recomendados específicos para la implementación de aplicaciones y recursos de aprendizaje gratuitos que le ayudarán a convertirse en un experto en Azure.

### <a name="considerations-for-migrations"></a>Consideraciones sobre las migraciones

Al considerar las migraciones de datos de archivo a Azure, varios aspectos son importantes. Antes de continuar, consulte estos recursos para más información:

- [Información general sobre la migración del almacenamiento](../../../common/storage-migration-overview.md)
- Características más recientes admitidas por Komprise Intelligent Data Management en la [matriz de comparación de herramientas de migración](./migration-tools-comparison.md).

Recuerde que necesitará suficiente capacidad de red para respaldar las migraciones sin que las aplicaciones de producción se vean afectadas. En esta sección se describen las herramientas y técnicas disponibles para evaluar las necesidades de la red.

#### <a name="determine-unutilized-internet-bandwidth"></a>Cálculo del ancho de banda de Internet no utilizado

Es importante conocer cuánto ancho de banda tiene disponible a diario que no se suela utilizar (o su *capacidad de aumento*). Puede resultarle útil evaluar si puede cumplir los siguientes objetivos:

- Tiempo inicial de las migraciones cuando no se usa Azure Data Box como el método sin conexión.
- Tiempo necesario para realizar la resincronización incremental antes de la conmutación final al servicio de archivos de destino.

Use los siguientes métodos para identificar la capacidad de aumento de ancho de banda que Azure puede consumir.

- Si ya es cliente de Azure ExpressRoute, consulte su [uso del circuito](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) en Azure Portal.
- Póngase en contacto con el ISP y solicite informes para mostrar el uso diario y mensual existente.
- Hay varias herramientas que pueden medir el uso mediante la supervisión del tráfico de red en el nivel de enrutador o conmutador:
  - [SolarWinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [PRTG de Paessler](https://www.paessler.com/bandwidth_monitoring)
  - [Network Assistant de Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

## <a name="migration-planning-guide"></a>Guía de planificación de la migración

Komprise es fácil de configurar y permite ejecutar varias migraciones simultáneamente en tres pasos:

1.  Análisis de los datos para identificar los archivos y objetos que se van a migrar o archivar.
1.  Definición de directivas para migrar, mover o copiar datos no estructurados en Azure Storage.
1.  Activar directivas que muevan automáticamente los datos.

El primer paso es fundamental para buscar y priorizar los datos adecuados para la migración. El análisis de Komprise proporciona lo siguiente:

- Información sobre el tiempo de acceso para identificar lo siguiente:
  - Archivos a los que se accede con menos frecuencia que se pueden almacenar en caché en el entorno local o en un servicio de archivos rápido
  - Datos en reposo que se pueden archivar en Blob Storage
- Información sobre los usuarios, grupos o recursos compartidos principales para determinar el orden de la migración y el grupo más afectado dentro de la organización para evaluar el impacto empresarial
- Número de archivos o capacidad por tipo de archivo para determinar el tipo de archivos almacenados y si hay posibilidades de limpiar el contenido. La limpieza reducirá el esfuerzo de migración y el costo del almacenamiento de destino. Hay análisis similares disponibles para los datos de objeto.
- Número de archivos o capacidad por tamaño de archivo para determinar la duración de la migración. Se tarda más en realizar la migración de un gran número de archivos pequeños que de un número reducido de archivos grandes. Hay análisis similares disponibles para los datos de objeto.
- Costo de los objetos por nivel de almacenamiento para determinar si los datos de acceso esporádico se colocan incorrectamente en niveles caros o si los datos de acceso frecuente se han colocado incorrectamente en niveles más baratos con costos de acceso elevados. La colocación correcta de los datos en función de los patrones de acceso permite optimizar los costos generales de almacenamiento en la nube.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-1.png" alt-text="Análisis por tipo de archivo y tiempo de acceso":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-shares.png" alt-text="Ejemplo de análisis de recurso compartido":::

- Filtro de funcionalidad de consulta personalizado para filtrar el conjunto exacto de archivos y objetos para sus necesidades específicas

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-custom.png" alt-text="Análisis de consultas personalizadas":::

## <a name="deployment-guide"></a>Guía de implementación

Antes de implementar Komprise, se debe implementar el servicio de destino. Aquí puede obtener más información:

- Procedimiento para crear un [recurso compartido de archivos de Azure](../../../files/storage-how-to-create-file-share.md)
- Procedimiento para crear un [volumen SMB](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) o una [exportación de NFS](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md) en Azure NetApp Files

Komprise Grid se implementa en un entorno virtual (Hyper-V, VMware, KVM) para ofrecer velocidad, escalabilidad y resistencia. Como alternativa, puede configurar el entorno en la suscripción de Azure mediante [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management).

1. Abra Azure Portal y busque **Cuentas de almacenamiento**. 

    :::image type="content" source="./media/komprise-quick-start-guide/azure-locate-storage-account.png" alt-text="Muestra dónde ha escrito &quot;Storage&quot; en el cuadro de búsqueda de Azure Portal.":::

    También puede hacer clic en el icono predeterminado **Cuentas de almacenamiento**.

    :::image type="content" source="./media/komprise-quick-start-guide/azure-portal.png" alt-text="Se muestra cómo agregar una cuenta de almacenamiento en Azure Portal.":::

2. Seleccione **Crear** para agregar una cuenta.
   1. Seleccione un grupo de recursos existente o haga clic en **Crear nuevo** para crear uno.
   2. Especifique un nombre único para la cuenta de almacenamiento.
   3. Elija la región.
   4. Seleccione rendimiento **Estándar** o **Prémium**, en función de sus necesidades. Si selecciona **Prémium**, elija **Recursos compartidos de archivos** en **Premium account type** (Tipo de cuenta prémium).
   5. Elija el valor de **[Redundancia](../../../common/storage-redundancy.md)** que satisfaga los sus requisitos de protección de datos.
   
   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-1.png" alt-text="Se muestra la configuración de la cuenta de almacenamiento en el portal.":::

3. A continuación, se recomienda usar la configuración predeterminada de la pantalla **Opciones avanzadas**. Si va a migrar a Azure Files, se recomienda habilitar **Large file shares** (Recursos compartidos de archivos grandes) si está disponible.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-2.png" alt-text="Muestra la pestaña Opciones avanzadas en el portal.":::

4. Por ahora, no cambie las opciones de redes predeterminadas y continúe con **Protección de datos**. Puede elegir habilitar la eliminación temporal, que le permite recuperar datos eliminados por accidente dentro del período de retención definido. La eliminación temporal ofrece protección contra la eliminación accidental o malintencionada.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-3.png" alt-text="Muestra la configuración de la protección de datos en el portal.":::

5. Agregue etiquetas para la organización si usa el etiquetado y haga clic en **Crear** para crear su cuenta.
 
6. Ahora solo debe completar dos pasos rápidos para poder agregar la cuenta al entorno de Komprise. Vaya a la cuenta que creó en Azure Portal y seleccione Recursos compartidos de archivos en el menú de servicio de archivo. Agregue un recurso compartido de archivos y elija un nombre descriptivo. A continuación, navegue hasta el elemento Claves de acceso en Configuración y copie el nombre de la cuenta de Storage y una de las dos claves de acceso. Si no se muestran las claves, haga clic en **Mostrar claves**.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-access-key.png" alt-text="Muestra la configuración de la clave de acceso en el portal.":::

7. Vaya a las **Propiedades** del recurso compartido de archivos de Azure. Anote la dirección URL; será necesario agregar la conexión de Azure al recurso compartido de archivos de destino de Komprise:

   :::image type="content" source="./media/komprise-quick-start-guide/azure-files-endpoint.png" alt-text="Búsqueda del punto de conexión de Azure Files.":::

8. (_Opcional_) Puede agregar capas de seguridad adicionales a la implementación.
 
   1. Configure el acceso basado en roles para limitar quién puede hacer cambios en la cuenta de Storage. Para obtener más información, consulte [Roles integrados en las operaciones de administración](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
 
   2.  Restrinja el acceso a la cuenta a segmentos de red específicos con la [configuración del firewall de almacenamiento](../../../common/storage-network-security.md). Configure las opciones del firewall para evitar el acceso desde fuera de la red corporativa.

       :::image type="content" source="./media/komprise-quick-start-guide/azure-storage-firewall.png" alt-text="Muestra la configuración del firewall de Storage en el portal.":::

   3.  [Bloquee la eliminación](../../../../azure-resource-manager/management/lock-resources.md) de la cuenta para evitar eliminar accidentalmente la cuenta de Storage.

       :::image type="content" source="./media/komprise-quick-start-guide/azure-resource-lock.png" alt-text="Muestra el bloqueo de eliminación de la cuenta en el portal.":::

   4.  Configure [procedimientos recomendados de seguridad](../../../blobs/security-recommendations.md) adicionales.

### <a name="deployment-instructions-for-managing-file-data"></a>Instrucciones de implementación para administrar datos de archivos

1.  **Descargue** la aplicación virtual Komprise Observer desde Director, impleméntela en el hipervisor y configúrela con la red y el dominio. Director se proporciona como un servicio en la nube administrado por Komprise. La información necesaria para acceder a Director se envía con el correo electrónico de bienvenida después de comprar la solución.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-1.png" alt-text="Descarga de la imagen adecuada para Komprise Observer desde Director":::

1. Para agregar los recursos compartidos que se van a analizar y migrar, tiene dos opciones:
   1. Para **detectar** todos los recursos compartidos del entorno de almacenamiento, escriba lo siguiente:
       - Plataforma para el NAS de origen
       - Nombre de host o dirección IP
       - Nombre para mostrar
       - Credenciales (para recursos compartidos de archivos SMB)

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-2.png" alt-text="Especificación del sistema NAS que se va a detectar":::

    1. Para **especificar** un recurso compartido de archivos, escriba lo siguiente:
       - Información de almacenamiento
       - Protocolo
       - Ruta de acceso
       - Display Name (Nombre para mostrar)
       - Credenciales (para recursos compartidos de archivos SMB)
  
        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-3.png" alt-text="Especificación de soluciones NAS para detectar":::

    Este paso se debe repetir para agregar otros recursos compartidos de origen y destino. Para agregar Azure Files como destino, debe proporcionar los detalles de la cuenta de almacenamiento de Azure y del recurso compartido de archivos:

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-1.png" alt-text="Selección de Azure Files como servicio de destino":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-2.png" alt-text="Escritura de los detalles de Azure Files":::

### <a name="deployment-instructions-for-managing-object-data"></a>Instrucciones de implementación para administrar datos de objetos

La administración de objetos proporciona una experiencia diferente. Los módulos Director y Observer se proporcionan como servicios en la nube, administrados por Komprise. Si solo necesita analizar y archivar datos en Azure Blob Storage, no se requiere ninguna implementación adicional. Si necesita realizar migraciones a Azure Blob Storage, envíe la aplicación virtual de Komprise Observer con el correo electrónico de bienvenida e impleméntela en una máquina virtual Linux en la infraestructura en la nube de Azure. Después de la implementación, siga los pasos descritos en Komprise Director.

1. Vaya a **Almacenes de datos** y a **Agregar nuevo almacén de objetos**. Seleccione **Microsoft Azure** como proveedor.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-object-store.png" alt-text="Captura de pantalla en la que se muestra la adición de un nuevo almacén de objetos":::

1. Agregue recursos compartidos para analizar y migrar. Estos pasos deben repetirse para cada recurso compartido de origen y de destino, o contenedor. Hay dos opciones para realizar la misma acción:
    1. **Detectar** todos los contenedores mediante la especificación de:
        - Nombre de la cuenta de almacenamiento
        - Clave de acceso principal
        - Nombre para mostrar
    
        :::image type="content" source="./media/komprise-quick-start-guide/komprise-discover-storage-account.png" alt-text="Captura de pantalla que muestra cómo detectar contenedores en la cuenta de almacenamiento":::

        Para encontrar la información necesaria en **[Azure Portal](https://portal.azure.com/)** , vaya al elemento **Claves de acceso** en **Configuración** de la cuenta de almacenamiento. Si no se muestran las claves, haga clic en **Mostrar claves**.

    1. Para **especificar** un contenedor, especifique:
        - Nombre del contenedor
        - Nombre de la cuenta de almacenamiento
        - Clave de acceso principal
        - Nombre para mostrar

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-container.png" alt-text="Captura de pantalla que muestra cómo agregar contenedores en la cuenta de almacenamiento":::

        El nombre del contenedor representa el contenedor de destino de la migración y debe crearse antes de la migración. Para encontrar información necesaria adicional en **[Azure Portal](https://portal.azure.com/)** , vaya al elemento **Claves de acceso** en **Configuración** de la cuenta de almacenamiento. Si no se muestran las claves, haga clic en **Mostrar claves**.

## <a name="migration-guide"></a>Guía de migración

Komprise proporciona migración en vivo, donde los usuarios finales y las aplicaciones no se interrumpen y pueden seguir accediendo a los datos durante la migración. El proceso de migración automatiza la migración de directorios, archivos y vínculos desde un origen a un destino. En cada paso se comprueba la integridad de los datos. Se aplican todos los atributos, permisos y controles de acceso del origen. En una migración de objetos, se migran objetos, prefijos y metadatos de cada objeto.

Para configurar y ejecutar una migración, siga estos pasos:

1. Inicie sesión en la consola Komprise. La información necesaria para acceder a la consola se envía con el correo electrónico de bienvenida después de comprar la solución.
1. Vaya a **Migrate** (Migrar) y haga clic en **Add Migration** (Agregar migración).

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-new-migrate.png" alt-text="Adición de un nuevo trabajo de migración":::

1. Para agregar la tarea de migración, seleccione el recurso compartido de origen y de destino adecuados. Proporcione un nombre de migración. Una vez que lo configure, haga clic en **Start Migration** (Iniciar migración). Este paso es ligeramente diferente para las migraciones de datos de archivos y objetos.
   
    1. Migración de archivos

       :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-migration.png" alt-text="Especificación de los detalles del trabajo de migración":::

       La migración de archivos proporciona opciones para conservar la hora de acceso y las ACL de SMB en el destino. Esta opción depende del protocolo y el servicio de archivos de origen y destino seleccionados.

    1. Migración de objeto

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-object-migration.png" alt-text="Captura de pantalla que muestra cómo agregar la migración de objetos":::

        La migración de objetos proporciona opciones para elegir el nivel de acceso de Azure de destino (acceso frecuente, esporádico, de archivo). También puede optar por comprobar cada transferencia de datos mediante la suma de comprobación MD5. Los costos de salida se pueden producir en las sumas de comprobación MD5, ya que los objetos en la nube deben recuperarse para calcular la suma de comprobación MD5.

2. Una vez que se inicie la migración, puede ir a **Migrate** (Migrar) para supervisar el progreso.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-monitor-migrations.png" alt-text="Supervisión de todos los trabajos de migración":::

3. Una vez que se hayan migrado todos los cambios, haga clic en **Actions** (Acciones) y seleccione **Start final iteration** (Iniciar iteración final) para ejecutar una última migración. Antes de la última migración, se recomienda detener el acceso a los recursos compartidos de archivos de origen o moverlos al modo de solo lectura (para usuarios y aplicaciones). Este paso garantizará que no se realice ningún cambio en el origen.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-final-migration.png" alt-text="Última migración antes de cambiar":::

    Una vez que finalice la última migración, realice la transición de todos los usuarios y aplicaciones al recurso compartido de destino. Para cambiar al nuevo servicio de archivos normalmente es necesario modificar la configuración de los servidores DNS, los servidores DFS o cambiar los puntos de montaje al nuevo destino. 

4. Como último paso, marque la migración como completada.

## <a name="support"></a>Soporte técnico

Para abrir un caso con Komprise, inicie sesión en el [sitio de soporte técnico de Komprise](https://komprise.freshdesk.com/).

## <a name="marketplace"></a>Marketplace

Obtenga Komprise en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview).

## <a name="next-steps"></a>Pasos siguientes

Hay varios recursos disponibles para obtener más información:

- [Información general sobre la migración del almacenamiento](../../../common/storage-migration-overview.md)
- Características admitidas por Komprise Intelligent Data Management en la [matriz de comparación de herramientas de migración](./migration-tools-comparison.md)
- [Matriz de compatibilidad de Komprise](https://www.komprise.com/partners/microsoft-azure/)
