---
title: Preguntas más frecuentes sobre el dispositivo de Azure Migrate
description: Respuestas a preguntas comunes sobre el dispositivo de Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: a814cce09a134f007fbd136b8c142a60fb099a1b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732656"
---
# <a name="azure-migrate-appliance-common-questions"></a>Dispositivo de Azure Migrate: Preguntas frecuentes

Este artículo contiene respuestas a algunas preguntas frecuentes sobre el dispositivo de Azure Migrate. Si tiene otras preguntas, repase estos recursos:

- [Preguntas generales](resources-faq.md) sobre Azure Migrate
- Preguntas sobre [detección, valoración y visualización de dependencias](common-questions-discovery-assessment.md)
- Preguntas sobre la [migración de servidores](common-questions-server-migration.md)
- Obtenga respuestas a sus preguntas en el [foro de Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>¿Qué es el dispositivo de Azure Migrate?

El dispositivo de Azure Migrate es un dispositivo ligero que Azure Migrate: Detección y evaluación usa para detectar y evaluar servidores físicos o virtuales en el entorno local o cualquier nube. La herramienta Azure Migrate: Evaluación de servidores también usa el dispositivo para realizar la migración sin agentes de los servidores locales que se ejecutan en el entorno VMware.

Aquí encontrará más información sobre el dispositivo Azure Migrate:

- El dispositivo se implementa de forma local como servidor físico o como servidor virtualizado.
- El dispositivo detecta continuamente los servidores locales y envía los metadatos y los datos de rendimiento de los servidores a Azure Migrate.
- La detección del dispositivo es sin agente. No se instala nada en los servidores detectados.

[Obtenga más información](migrate-appliance.md) sobre el dispositivo.

## <a name="how-can-i-deploy-the-appliance"></a>¿Cómo se puede implementar el dispositivo?

Para implementar el dispositivo se pueden usar un par de métodos:

- El dispositivo se puede implementar mediante una plantilla para servidores que se ejecutan en los entornos de VMware o Hyper-V ([plantilla OVA en el caso de VMware](how-to-set-up-appliance-vmware.md) o [VHD en el de Hyper-V](how-to-set-up-appliance-hyper-v.md)).
- Si no quiere usar una plantilla, puede implementar el dispositivo para los entornos de VMware o Hyper-V mediante un [script del instalador de PowerShell](deploy-appliance-script.md).
- En Azure Government, el dispositivo se debe implementar mediante un script del instalador de PowerShell. Consulte los pasos de la implementación [aquí](deploy-appliance-script-government.md).
- En el caso de los servidores físicos o virtualizados locales o de cualquier otra nube, el dispositivo siempre se implementa mediante un script del instalador de PowerShell. Consulte los pasos de la implementación [aquí](how-to-set-up-appliance-physical.md).

## <a name="how-does-the-appliance-connect-to-azure"></a>¿Cómo se conecta el dispositivo a Azure?

El dispositivo puede conectarse a través de Internet, o mediante Azure ExpressRoute. 

- Asegúrese de que el dispositivo se puede conectar a estas [direcciones URL de Azure](./migrate-appliance.md#url-access). 
- Puede usar ExpressRoute con emparejamiento de Microsoft. El emparejamiento público está en desuso y no está disponible para nuevos circuitos de ExpressRoute.
- El emparejamiento privado solo no se admite.

## <a name="does-appliance-analysis-affect-performance"></a>¿Afecta el análisis del dispositivo al rendimiento?

El dispositivo de Azure Migrate genera perfiles de servidores locales continuamente para medir los datos de rendimiento. Esta generación de perfiles apenas afecta al rendimiento de los servidores en los que se realiza.

## <a name="can-i-harden-the-appliance"></a>¿Se puede proteger la aplicación?

Cuando usa la plantilla descargada para crear la aplicación, puede agregar componentes (por ejemplo, un antivirus) a la plantilla, siempre que las reglas de firewall y de comunicación necesarias para el dispositivo de Azure Migrate no se modifiquen.

## <a name="what-network-connectivity-is-required"></a>¿Qué conectividad de red se necesita?

El dispositivo necesita acceso a las direcciones URL de Azure. [Examine](migrate-appliance.md#url-access) la lista de direcciones URL.

## <a name="what-data-does-the-appliance-collect"></a>¿Qué datos recopila el dispositivo?

Consulte los artículos siguientes para obtener información sobre los datos que el dispositivo Azure Migrate recopila en los servidores:

- **Servidores en el entorno de VMware**: [Revise](migrate-appliance.md#collected-data---vmware) los datos recopilados.
- **Servidores en el entorno de Hyper-V**: [Revise](migrate-appliance.md#collected-data---hyper-v) los datos recopilados.
- **Servidores físicos o virtuales**: [Revisión](migrate-appliance.md#collected-data---physical) de los datos recopilados.

## <a name="how-is-data-stored"></a>¿Cómo se almacenan los datos?

Los datos que recopila el dispositivo de Azure Migrate se almacenan en la ubicación de Azure en la que creó el proyecto.

Aquí encontrará más información sobre cómo se almacenan los datos:

- Los datos recopilados se almacenan de forma segura en CosmosDB en una suscripción de Microsoft. Los datos se eliminan cuando se elimina el proyecto. Azure Migrate controla el almacenamiento. No se puede elegir una cuenta de almacenamiento especifica para los datos recopilados.
- Si usa la [visualización de dependencias](concepts-dependency-visualization.md), los datos recopilados se almacenan en un área de trabajo de Azure Log Analytics creada en su suscripción de Azure. Los datos se eliminan al eliminar el área de trabajo de Log Analytics en su suscripción.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>¿Qué cantidad de datos se carga en la generación de perfiles continua?

El volumen de datos que se envía a Azure Migrate depende de varios parámetros. Por ejemplo, un proyecto que tiene 10 servidores (cada una con un disco y una NIC) envía aproximadamente 50 MB de datos al día. Este valor es aproximado; el valor real varía en función del número de puntos de datos para los discos y las NIC. Si aumenta el número de servidores, discos o NIC, el aumento de los datos que se envían es no lineal.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>¿Los datos se cifran en reposo y en tránsito?

Sí, en ambos:

- Los metadatos se envían de manera segura al servicio Azure Migrate por Internet a través de HTTPS.
- Los metadatos se almacenan en una base de datos de [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) y en [Azure Blob Storage](../storage/common/storage-service-encryption.md), en una suscripción de Microsoft. Los metadatos se cifran en reposo en el caso del almacenamiento.
- Los datos para el análisis de dependencia se cifran en tránsito (con HTTPS seguro). Se almacenan en un área de trabajo de Log Analytics de la suscripción. Los datos para el análisis de dependencias se cifran en reposo.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>¿Cómo se conecta el dispositivo a vCenter Server?

En estos pasos se describe cómo se conecta el dispositivo a VMware vCenter Server:

1. El dispositivo se conecta a vCenter Server (puerto 443) mediante las credenciales que proporcionó al configurar el dispositivo.
2. El dispositivo usa VMware PowerCLI para realizar consultas en vCenter Server, para recopilar metadatos sobre las servidores que administra vCenter Server.
3. El dispositivo recopila los datos de configuración sobre servidores (núcleos, memoria, discos, NIC) y el historial de rendimiento de cada servidor del último mes.
4. Los metadatos recopilados se envían a la herramienta de Azure Migrate: Detección y evaluación (mediante Internet a través de HTTPS) para la valoración.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>¿Puede conectarse el dispositivo Azure Migrate a varias instancias de vCenter Server?

No. Hay una asignación unívoca entre un [dispositivo Azure Migrate](migrate-appliance.md) y vCenter Server. Para detectar servidores en varias instancias de vCenter Server, tiene que implementar varias aplicaciones.

## <a name="can-a-project-have-multiple-appliances"></a>¿Puede un proyecto tener varias aplicaciones?

Un proyecto puede tener varios dispositivos registrados. Sin embargo, un dispositivo solo puede registrarse en un solo proyecto.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>¿El dispositivo de Azure Migrate o el dispositivo de replicación puede conectarse al mismo vCenter?

Sí. Puede agregar el dispositivo de Azure Migrate (que se usa para la evaluación y la migración de VMware sin agente) y el dispositivo de replicación (que se usa para la migración basada en agente de servidores que se ejecutan en VMware) al mismo servidor vCenter. Sin embargo, asegúrese de que no está configurando ambos dispositivos en el mismo servidor y que no se admite actualmente.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>¿Cuántos servidores puedo detectar con un dispositivo?

Puede detectar hasta 10 000 servidores del entorno VMware, hasta 5 000 servidores del entorno Hyper-V y hasta 1000 servidores físicos con el uso de una sola aplicación. Si tiene más servidores en su entorno local, lea acerca de [escalar una valoración de Hyper-V](scale-hyper-v-assessment.md), [escalar una valoración de VMware](scale-vmware-assessment.md) y [escalar una evaluación de un servidor físico](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>¿Puedo eliminar un dispositivo?

Actualmente no se admite la eliminación de un dispositivo del proyecto.

La única manera de eliminar el dispositivo es eliminar el grupo de recursos que contiene el proyecto que está asociado con la aplicación.

Si bien, al eliminar el grupo de recursos también se eliminarán otros dispositivos registrados, el inventario detectado, las valoraciones y todos los demás componentes de Azure en el grupo de recursos que estén asociados al proyecto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>¿Puedo usar el dispositivo con una suscripción o un proyecto distintos?

Para usar el dispositivo con una suscripción o un proyecto diferente, debe volver a configurar el dispositivo existente ejecutando el script de instalador de PowerShell para el escenario específico (VMware, Hyper-V o físico) en la aplicación. El script limpiará la configuración y los componentes de dispositivo existentes para implementar un nuevo dispositivo. Asegúrese de borrar la memoria caché del explorador antes de empezar a usar el administrador de configuración del dispositivo recién implementado.

Además, no puede volver a usar una clave de proyecto existente en un dispositivo que se ha reconfigurado. Asegúrese de generar una nueva clave a partir de la suscripción o el proyecto que desee para completar el registro del dispositivo.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>¿Se puede configurar el dispositivo en una máquina virtual de Azure?

No. Actualmente, esta opción no es compatible.

## <a name="can-i-discover-on-an-esxi-host"></a>¿Se puede detectar en un host ESXi?

No. Para descubrir servidores en el entorno VMware, debe tener vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>¿Cómo se actualiza el dispositivo?

De forma predeterminada, el dispositivo y sus agentes instalados se actualizan automáticamente. El dispositivo busca actualizaciones cada 24 horas. Las actualizaciones que resultan en error se vuelven a intentar.

Estas actualizaciones automáticas solo actualizan los agentes del dispositivo y el dispositivo. Las actualizaciones automáticas de Azure Migrate no actualizan el sistema operativo. Use las actualizaciones de Windows para mantener actualizado el sistema operativo.

## <a name="can-i-check-agent-health"></a>¿Puedo comprobar el estado del agente?

Sí. En el portal, vaya a la página **Agent Health** de Azure Migrate: Detección y evaluación o Azure Migrate: Herramienta de migración del servidor. Allí puede comprobar el estado de la conexión de los agentes de detección y valoración del dispositivo con Azure.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>¿Puedo agregar varias credenciales de servidor en el dispositivo de VMware?

Sí, ahora se admiten varias credenciales de servidor para realizar el inventario de software (detección de aplicaciones instaladas), el análisis de dependencias sin agente y la detección de instancias y bases de datos de SQL Server. [Obtenga más información](tutorial-discover-vmware.md#provide-server-credentials) sobre cómo proporcionar las credenciales en el administrador de configuración del dispositivo.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>¿Qué tipo de credenciales de servidor puedo agregar en el dispositivo de VMware?

Puede proporcionar credenciales de autenticación de dominio, Windows (no de dominio), Linux (no de dominio) o SQL Server en el administrador de configuración del dispositivo. [Obtenga más información](add-server-credentials.md) sobre cómo proporcionar credenciales y cómo administrarlas.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>¿Qué tipo de propiedades de conexión de SQL Server es compatible con Azure Migrate para la detección de SQL?

Azure Migrate cifrará la comunicación entre el dispositivo de Azure Migrate y las instancias de origen de SQL Server (con la propiedad Cifrar conexión establecida en TRUE). Estas conexiones se cifran con [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (establecido en TRUE); la capa de transporte usará SSL para cifrar el canal y evitar la cadena de certificados para validar la confianza. El servidor del dispositivo se debe configurar para [confiar en la entidad de certificación raíz del certificado](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Si no se ha proporcionado ningún certificado al servidor, cuando se inicia, SQL Server genera un certificado autofirmado que se utiliza para cifrar los paquetes de inicio de sesión. [Más información](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="next-steps"></a>Pasos siguientes

Consulte la sección [Información general de Azure Migrate](migrate-services-overview.md).