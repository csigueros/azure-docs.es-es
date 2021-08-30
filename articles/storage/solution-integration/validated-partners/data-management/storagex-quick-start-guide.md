---
title: Análisis y migración de los datos de archivo a Azure con Data Dynamics StorageX
titleSuffix: Azure Storage
description: Guía de introducción para implementar Data Dynamics StorageX. En la guía se muestra cómo migrar los datos a Azure Files, Azure NetApp Files, Azure Blob Storage o cualquier solución NAS de ISV disponible
author: dukicn
ms.author: nikoduki
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 8613d641f305f93598ac070c5eaaeee5fed81b8d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290227"
---
# <a name="migrate-data-to-azure-with-data-dynamics-storagex"></a>Migración de datos a Azure con Data Dynamics StorageX

Este artículo lo ayuda a implementar Data Dynamics StorageX en Microsoft Azure. Presentamos conceptos clave sobre el funcionamiento de StorageX, los requisitos previos de la implementación, el proceso de instalación y los procedimientos para obtener guías de operaciones. Para información más detallada, visite el [portal del cliente de Data Dynamics](https://www.datdynsupport.com/).

Data Dynamics StorageX es una plataforma de Administración de datos unificada no estructurada que permite analizar, administrar y mover datos entre entornos de almacenamiento heterogéneos. Las funcionalidades básicas son:
- Funcionalidades de movimiento de datos
    - NAS a NAS (con o sin análisis)
    - NAS a replicación de objetos
    - NAS a archivo de objetos
- Informe de comprobación de archivos duplicados
- Informe de recursos compartidos abiertos
- Análisis de metadatos de archivos con etiquetado personalizado
  
Puede encontrar más funcionalidades en StorageX y una comparación con herramientas similares en la [tabla de comparación](./migration-tools-comparison.md).

En este documento se tratan tres temas principales:
- El proceso de implementación de la oferta de StorageX en el entorno de Azure.
- Los pasos básicos para agregar recursos de almacenamiento de archivos y en la nube a StorageX.
- La creación de directivas de movimiento de datos.

## <a name="reference-architecture"></a>Arquitectura de referencia

En el diagrama siguiente se proporciona una arquitectura de referencia para las implementaciones locales a Azure y dentro de Azure de StorageX.

:::image type="content" source="./media/storagex-quick-start-guide/storagex-architecture.png" alt-text="Diagrama que muestra la arquitectura de referencia para la implementación de StorageX":::

## <a name="storagex-interoperability-matrix"></a>Matriz de interoperabilidad de StorageX

| Característica | SMB | NFS | Notas |
| ------- |--- | --- | --- |
| **Migración** | SMB a SMB | NFSv3 a NFSv3 | Se puede combinar para varios protocolos |
| **Archivo de archivo a objeto** | SMB a Azure Blob Storage | NFSv3 a Azure Blob Storage | Archivado de datos para optimizar el costo |
| **Replicación de archivo a objeto** | SMB a Azure Blob Storage | NFSv3 a Azure Blob Storage | Replicación de datos para aumentar la protección |

## <a name="before-you-begin"></a>Antes de empezar

Un planeamiento por adelantado le facilitará la migración y lo ayudará a disminuir los riesgos. Estas son algunas de las sugerencias para facilitar la migración:

- Recopile datos sobre los servicios de origen y destino.
- Asegúrese de tener una lista de los puntos de conectividad y todos los puertos de red necesarios.
- El servidor StorageX de Azure deberá formar parte de la infraestructura de Active Directory. Asegúrese de interactuar con un administrador con privilegios de Active Directory para agregar el servidor a Active Directory.
- A fin de facilitar la implementación, utilice [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview).

### <a name="get-started-with-azure"></a>Introducción a Azure

Microsoft ofrece un marco para empezar a trabajar con Azure. [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) es un enfoque detallado para la transformación digital empresarial y una guía completa para la planificación de una adopción de la nube de calidad de producción. CAF incluye una [guía paso a paso de configuración de Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) para ayudarle a ponerse en marcha de forma rápida y segura. Puede encontrar una versión interactiva en [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Encontrará arquitecturas de ejemplo, procedimientos recomendados específicos para la implementación de aplicaciones y recursos de aprendizaje gratuitos que le ayudarán a convertirse en un experto en Azure.

### <a name="considerations-for-migrations"></a>Consideraciones sobre las migraciones

Al considerar las migraciones de datos de archivo a Azure, varios aspectos son importantes. Antes de continuar, consulte estos recursos para más información:

- [Información general sobre la migración del almacenamiento](../../../common/storage-migration-overview.md)
- Características admitidas más recientes en Data Dynamics StorageX en la [matriz de comparación de herramientas de migración](./migration-tools-comparison.md).

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

#### <a name="creating-a-storagex-service-account"></a>Creación de una cuenta de servicio de StorageX

En el caso de las migraciones de SMB, se recomienda que la cuenta de servicio ejecute los servicios de StorageX. La cuenta de servicio permite realizar un seguimiento del acceso por motivos de soporte técnico, al tiempo que proporciona los privilegios adecuados para conceder acceso total.

El proceso que ejecuta directivas de movimiento de datos en los motores de datos universales de StorageX usa la cuenta de servicio de StorageX. La cuenta debe tener asignados todos los privilegios necesarios en el dominio de Active Directory.

Si el entorno lo permite, puede crear una cuenta como administrador de dominio. El enfoque recomendado es conceder permisos directamente en el servidor NAS. Para ello, coloque la cuenta de servicio en los grupos Administradores y Operadores de copia de seguridad.

La cuenta de servicio de StorageX debe tener los privilegios siguientes:

- SeServiceLogonRight
- SeSecurityPrivilege 
- SeBackupPrivilege 
- SeRestorePrivilege 
- SeAuditPrivilege
- SeInteractiveLogonRight

El acceso a NFS requiere una regla de exportación para la dirección IP del servidor StorageX y conceder acceso para RW y raíz. Por ejemplo, "raíz=10.2.3.12, rw=10.2.3.12", donde 10.2.3.12 es la dirección IP del servidor StorageX.

## <a name="deployment-guide"></a>Guía de implementación

En esta sección se proporcionan instrucciones sobre cómo implementar un servidor StorageX y otros componentes de StorageX en el entorno de Azure.

Antes de que se inicie la implementación, es importante considerar varios requisitos previos:

- Se debe identificar y crear el servicio de almacenamiento de destino.
- Asegúrese de tener los puertos adecuados abiertos para acceder a los servicios de almacenamiento. Puede usar la herramienta [portqryui](https://www.microsoft.com/download/details.aspx?id=24009) de Microsoft para comprobar que los puertos necesarios están abiertos.

Puede obtener más información aquí:

- Procedimiento para crear un [recurso compartido de archivos de Azure](../../../files/storage-how-to-create-file-share.md)
- Procedimiento para crear un [volumen SMB](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) o una [exportación de NFS](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md) en Azure NetApp Files

Las migraciones de SMB requieren que Active Directory esté configurado correctamente antes de agregar cualquier recurso. Usaremos una implementación de Azure NetApp Files existente con un volumen nuevo para el destino de migración. A fin de crear un volumen de Azure NetApp Files, es necesario:

- [Crear cuenta de Azure NetApp Files](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-netapp-account)
- [Crear grupo de capacidades](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#set-up-a-capacity-pool)
- [Unir una cuenta de Azure NetApp Files a un Dominio de Active Directory](../../../../azure-netapp-files/create-active-directory-connections.md)

Una vez completada la configuración de Azure NetApp Files, podemos crear un recurso compartido que usaremos para la migración. 

1. Vaya a la cuenta de Azure NetApp Files existente y seleccione **Volúmenes**. Seleccione **Agregar volumen**. Asígnele un nombre, seleccione un grupo de capacidad y la configuración de red.

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-1.png" alt-text="Captura de pantalla para agregar un volumen de Azure NetApp Files nuevo":::

1. Seleccione el protocolo **SMB**, una conexión de Active Directory existente y proporcione un **Nombre del recurso compartido**.

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-2.png" alt-text="Captura de pantalla de las propiedades de SMB en el volumen de Azure NetApp Files":::

1. Agregue **Etiquetas** si las usa y **Crear** un recurso compartido.

    Una vez creado el volumen, seguiremos con la implementación de StorageX.

1.  Implemente la oferta de movilidad y movimientos de datos de Data Dynamics en Azure. El enfoque recomendado es usar [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview). Agregue el servidor implementado al dominio de Active Directory.
1.  En el servidor implementado, compruebe que todos los puertos de red necesarios están abiertos.
1.  Ejecute el script de instalación de StorageX. El script de instalación está disponible en el escritorio cuando inicia sesión en el servidor implementado en Azure. 
    Todos los archivos de instalación y los registros de salida se encuentran en la carpeta **C:\ProgramData\data Dynamics\StorageX**. En esa carpeta, la subcarpeta **InstallationFiles** contiene los archivos de instalación de origen comprimidos, mientras que la carpeta **SilentInstaller-DD** contiene los archivos XML de configuración y los registros de instalación.

    De manera predeterminada, el script de instalación implementa estos servicios:

    - StorageX
    - StorageX API
    - Motor de procesamiento de StorageX
    - Motor de datos universal de StorageX
    - Recuperación de archivos de StorageX
    - Elasticsearch
    - Microsoft SQL Express
    - Microsoft SQL Management Studio
    - Kibana
    - Cualquier software compatible (por ejemplo, Java)

    El script de instalación tiene un archivo de configuración XML en el que se pueden personalizar estos archivos distintos, pero no se recomienda modificar manualmente la configuración.

    Para realizar la instalación, haga clic con el botón derecho en **StorageXPOCSilentInstaller - Acceso directo** en el escritorio y seleccione **Ejecutar como administrador**. Una vez que se inicie la instalación, se le pedirá la cuenta de servicio de StorageX y la contraseña de esa cuenta.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-1.jpg" alt-text="Captura de pantalla de la CLI de instalación":::

    El script de instalación seguirá instalando todos los servicios. Cuando finalice el proceso de instalación, verá el mensaje en la interfaz de la línea de comandos.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-2.jpg" alt-text="Captura de pantalla que muestra la instalación finalizada":::

    StorageX se iniciará automáticamente.

## <a name="migration-guide"></a>Guía de migración

Una vez que finalice la instalación y que se inicien todos los servicios, puede empezar el proceso de migración.

1.  Abra la consola de StorageX.
1.  Haga clic en la pestaña **Recursos de almacenamiento**.
1.  En la vista **Mis recursos**, agregue los dispositivos NAS o los servicios de almacenamiento, denominados **recursos de almacenamiento** en StorageX. Puede agregar varios tipos distintos:
    - **Agregar un recurso de almacenamiento de archivos** para dispositivos NAS locales, servidor de archivos de Windows, Azure NetApp Files o servidores de archivos genéricos.
    - **Agregar un recurso de almacenamiento de objetos** para S3 o Azure Blob Storage (incluido el servicio Azure Files).
    - **Agregar un espacio de nombres DFS** para servidores de archivos DFS.

    Seleccione **Agregar un recurso de almacenamiento de archivos** a fin de iniciar el asistente para agregar un destino de migración de Azure NetApp Files.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-1.png" alt-text="Captura de pantalla para agregar un recurso a la implementación de StorageX":::

1. Seleccione **Generic CIFS** (CIFS genérico) para las migraciones de SMB y CIFS.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-2.png" alt-text="Captura de pantalla para agregar un recurso CIFS genérico a la implementación de StorageX":::

1. Busque la cuenta de equipo de Azure NetApp Files. La cuenta de equipo de Azure NetApp Files se creó cuando se agregó al Dominio de Active Directory.
   
    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-3.png" alt-text="Captura de pantalla para agregar una cuenta de equipo de Azure NetApp Files":::

    **Finalice** el asistente y compruebe que se agregó la cuenta y que contiene el recurso compartido que se creó.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-4.png" alt-text="Captura de pantalla de la búsqueda de los recursos agregados":::

    Cada origen y destino se debe agregar como un recurso independiente. En el ejemplo se muestra cómo agregar un recurso compartido de Azure NetApp Files y puede ser ligeramente distinto para otros servicios.

1.  Haga clic en la pestaña **Movimiento de datos**.

1.  En el panel de árbol, haga clic con el botón derecho en la carpeta en la que desea crear la directiva y seleccione **Directiva de migración por fases**.

1.  Siga los pasos del asistente para crear una directiva de migración por fases. Seleccione los objetivos de **Origen** y **Destino** adecuados.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-1.png" alt-text="Captura de pantalla de la selección de los objetivos de origen y destino adecuados":::

1. Proporcione un nombre y una descripción a la directiva y haga clic en **Finalizar** el asistente.

1.  Haga clic con el botón derecho en la directiva nueva y seleccione **Ejecutar** a fin de ejecutar una migración de prueba. Se recomienda revisar la configuración de seguridad en varios archivos en el recurso de origen para comprobar que se proporcionaron los permisos necesarios.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-2.png" alt-text="Captura de pantalla para ejecutar una directiva definida":::

1. Compruebe el estado de la directiva a fin de asegurarse de que la migración inicial se ejecutó correctamente. 

## <a name="support"></a>Soporte técnico

Si se producen problemas, Microsoft y Data Dynamics pueden proporcionar ayuda mediante canales de soporte técnico normales. Si tiene problemas de infraestructura o con cualquier servicio de Azure, póngase en contacto con Microsoft. Si tiene problemas relacionados con la instalación de Data Dynamics StorageX, póngase en contacto con Data Dynamics.

### <a name="how-to-open-a-case-with-azure"></a>Procedimiento para abrir un caso en Azure

En [Azure Portal](https://portal.azure.com), busque soporte técnico en la barra de búsqueda de la parte superior. Seleccione **Ayuda y soporte técnico** -> **Nueva solicitud de soporte técnico**.

### <a name="how-to-open-a-case-with-data-dynamics"></a>Procedimiento para abrir un caso en Data Dynamics

Vaya al [Portal de soporte técnico de Data Dynamics](https://www.datdynsupport.com/). Si no se registró, proporcione su dirección de correo electrónico y el equipo de soporte técnico creará una cuenta para usted. Una vez que inicie sesión, abra una solicitud del usuario. Si ya abrió un caso de soporte técnico de Azure, anote el número de la solicitud de soporte técnico cuando cree la solicitud.

## <a name="next-steps"></a>Pasos siguientes

Hay varios recursos disponibles para obtener más información:

- [Información general sobre la migración del almacenamiento](../../../common/storage-migration-overview.md)
- Características admitidas en Data Dynamics StorageX en la [matriz de comparación de herramientas de migración](./migration-tools-comparison.md)
- [Data Dynamics](https://www.datadynamicsinc.com/)
- El [portal del cliente de Data Dynamics](https://www.datdynsupport.com/) contiene toda la documentación sobre StorageX