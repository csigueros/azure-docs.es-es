---
title: Arquitectura de Azure Backup para la copia de seguridad de SAP HANA
description: Obtenga más información sobre la arquitectura de Azure Backup para la copia de seguridad de SAP HANA.
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 7ca16e9990f097a98b8395c97567cabfbcb71eae
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084230"
---
# <a name="azure-backup-architecture-for-sap-hana-backup"></a>Arquitectura de Azure Backup para la copia de seguridad de SAP HANA

[El servicio de Azure Backup](/azure/backup/backup-overview) le permite realizar una copia de seguridad de los datos de las bases de datos de SAP HANA de una aplicación de forma coherente. En este artículo se describen los componentes de la arquitectura y los procesos de Azure Backup.

## <a name="how-does-azure-backup-work-with-sap-hana-databases"></a>¿Cómo funciona Azure Backup con las bases de datos de SAP HANA?

Azure Backup proporciona una solución de copia de seguridad de streaming para realizar copias de seguridad de bases de datos de SAP HANA que se ejecutan en una máquina virtual de Azure. Esta oferta de copia de seguridad no requiere ninguna configuración de infraestructura, lo que elimina la necesidad de implementar y administrar la infraestructura de copia de seguridad.

Azure Backup tiene [certificación Backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) de SAP, proporciona compatibilidad con copias de seguridad nativas usando las API nativas de SAP HANA. Con esta solución, puede realizar copias de seguridad de bases de datos de SAP HANA que se ejecutan en máquinas virtuales de Azure y restaurarlas, y usar las funcionalidades de administración empresarial que proporciona Azure Backup.

[Obtenga más información](/azure/backup/sap-hana-db-about#added-value) sobre los valores agregados que Azure Backup proporciona para SAP HANA.

## <a name="where-is-the-data-backed-up"></a>¿Dónde se efectúa la copia de seguridad de los datos?

Azure Backup almacena los datos de copia de seguridad en almacenes de Recovery Services. Un almacén es una entidad de almacenamiento en línea de Azure que se usa para almacenar datos, como copias de seguridad, puntos de recuperación y directivas de copia de seguridad.

[Obtenga más información](/azure/backup/backup-azure-backup-faq#recovery-services-vault) sobre los almacenes de Recovery Services.

## <a name="backup-agents"></a>Agentes de copia de seguridad

Para hacer una copia de seguridad de las bases de datos de SAP HANA que se ejecutan en una máquina virtual de Azure, debe permitir la instalación del complemento (agente de copia de seguridad de SAP HANA) en la máquina virtual de Azure. Este complemento se conecta con HANA Backint y ayuda al servicio de Azure Backup a trasladar datos al almacén. También permite a Azure Backup realizar restauraciones.

## <a name="backup-types"></a>Tipos de copia de seguridad

[Más información](/azure/backup/backup-architecture#sap-hana-backup-types) sobre tipos de copia de seguridad de SAP HANA.

## <a name="about-architecture"></a>Acerca de la arquitectura

Consulte la [arquitectura de alto nivel de Azure Backup para las bases de datos de SAP HANA](/azure/backup/sap-hana-db-about#backup-architecture). Para obtener una descripción detallada del proceso de copia de seguridad, consulte el siguiente proceso:

:::image type="content" source="./media/sap-hana-db-about/backup-architecture.png" alt-text="Diagrama que muestra el proceso de copia de seguridad de la base de datos de SAP HANA.":::

1. Para empezar el proceso de copia de seguridad, [cree un almacén de Recovery Services](/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-services-vault) en Azure. Este almacén se usará para almacenar las copias de seguridad y los puntos de recuperación creados con el tiempo.

1. La máquina virtual de Azure que ejecuta un servidor SAP HANA se registra con el almacén y se [detectan](/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases) las bases de datos de las que se va a realizar la copia de seguridad. A fin de habilitar el servicio Azure Backup para que detecte las bases de datos, debe ejecutar este [script de prerregistro](https://go.microsoft.com/fwlink/?linkid=2173610) en el servidor HANA como usuario raíz. 
   >[!Note]
   >Asegúrese de que la instancia de HANA esté en funcionamiento durante el descubrimiento de las bases de datos de esta instancia.

1. Además, asegúrese de que se cumplen los [demás requisitos previos](/azure/backup/tutorial-backup-sap-hana-db#prerequisites).

   >[!Important]
   >Asegúrese de que se cumplen los requisitos previos para configurar la conectividad de red adecuada. Consulte la recomendación sobre [cómo configurar máquinas virtuales de Azure que se ejecutan en SAP HANA con componentes de red adicionales para usar la oferta de copia de seguridad](/azure/backup/tutorial-backup-sap-hana-db#set-up-network-connectivity).

1. Consulte también [lo que hace el script de registro previo](/azure/backup/tutorial-backup-sap-hana-db#what-the-pre-registration-script-does). Si intenta configurar la copia de seguridad de las bases de datos de SAP HANA sin ejecutar este script, es posible que reciba el error _UserErrorHanaScriptNotRun_.

1. Ahora el servicio Azure Backup instala el complemento de Azure Backup para HANA en el servidor SAP HANA registrado. Este complemento usa el usuario de Backup creado por el script de registro previo para realizar todas las operaciones de copia de seguridad y restauración.

1. Para [configurar la copia de seguridad](/azure/backup/tutorial-backup-sap-hana-db#configure-backup) en las bases de datos que se han detectado, elija la directiva de copia de seguridad necesaria y habilite las copias de seguridad.

1. Azure Backup para SAP HANA (solución con certificación Backint), no depende de los tipos de máquina virtual o disco subyacente. La copia de seguridad se realiza mediante secuencias generadas por SAP HANA.

## <a name="backup-flow"></a>Flujo de copia de seguridad

1. Las copias de seguridad programadas se administran mediante entradas crontab creadas en la máquina virtual de HANA, mientras que las copias de seguridad a petición se desencadenan directamente por el servicio de Azure Backup.

1. Una vez que SAP HANA Backup Engine/Backint recibe la solicitud de copia de seguridad, prepara la base de datos de SAP HANA para una copia de seguridad mediante la creación de un punto de guardado y el movimiento de los datos a los volúmenes de almacenamiento subyacentes.

1. A continuación, Backint ejecuta la operación de lectura desde los volúmenes de datos subyacentes: el servidor de índice y el motor XS para la base de datos del inquilino y el servidor de nombres para SYSTEMDB. Los discos SSD prémium pueden proporcionar un rendimiento de E/S óptimo para la operación de streaming de copia de seguridad. Sin embargo, el uso de discos sin almacenar en caché con M64Is puede proporcionar mayores velocidades.

1. Para transmitir los datos de copia de seguridad, Backint crea hasta tres canalizaciones, que escriben directamente en el almacén de Recovery Services de Azure Backup.

   Si no usa firewall o NVA en la configuración, la secuencia de copia de seguridad se transfiere a través de la red de Azure al almacén de Recovery Services. Además, puede configurar [punto de conexión de servicio de red virtual](/azure/virtual-network/virtual-network-service-endpoints-overview) o un [punto de conexión privado](/azure/private-link/private-endpoint-overview) para permitir que SAP HANA envíe tráfico de copia de seguridad directamente a Azure Storage, omitiendo NVA o Azure Firewall. Además, cuando se usa firewall o NVA, el tráfico a Azure Active Directory y al almacén de Recovery Services pasará a través del firewall o NVA y no afectará al rendimiento general de la copia de seguridad. 

1. Azure Backup intenta lograr velocidades de hasta 420 MB/s para las copias de seguridad que no son para registros y de hasta 100 MB/s para las copias de seguridad de registros. [Obtenga más información](/azure/backup/tutorial-backup-sap-hana-db#understanding-backup-and-restore-throughput-performance) sobre el rendimiento de las copias de seguridad y la restauración.

1. Los registros detallados se escriben en los archivos _backup.log_ y _backint.log_ de la instancia de SAP HANA.

1. Una vez completada la transmisión de la copia de seguridad, el catálogo se transmite al almacén de Recovery Services. Si la copia de seguridad (completa/diferencial/incremental/de registro) y el catálogo de esta copia de seguridad se transmiten correctamente y se guardan en el almacén de Recovery Services, Azure Backup considera que la operación de copia de seguridad es correcta.

Consulte las siguientes configuraciones de SAP HANA y vea la ejecución de la operación de copia de seguridad mencionada anteriormente:

**Escenario de configuración de SAP HANA: red de Azure, sin NVA o Azure Firewall**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-without-nva-or-azure-firewall.png" alt-text="Diagrama que muestra la configuración de SAP HANA si la red de Azure no tiene NVA o Azure Firewall.":::

**Escenario de configuración de SAP HANA: red de Azure, con UDR y NVA o Azure Firewall**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall.png" alt-text="Diagrama que muestra la configuración de SAP HANA si la red de Azure tiene UDR y NVA o Azure Firewall.":::

>[!Note]
>NVA o Azure Firewall pueden agregar una sobrecarga cuando SAP HANA transmite la copia de seguridad a Azure Storage o al almacén de Recovery Services (plano de datos). Consulte el _punto 6_ en el diagrama anterior.

**Escenario de configuración de SAP HANA: red de Azure que tiene UDR y NVA o Azure Firewall y punto de conexión privado o punto de conexión de servicio**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall-and-private-endpoint-or-service-endpoint.png" alt-text="Diagrama que muestra la configuración de SAP HANA si la red de Azure tiene UDR y NVA o Azure Firewall y punto de conexión privado o punto de conexión de servicio.":::

## <a name="next-steps"></a>Pasos siguientes

[Hacer una copia de seguridad de bases de datos de SAP HANA en máquinas virtuales de Azure](/azure/backup/backup-azure-sap-hana-database).

