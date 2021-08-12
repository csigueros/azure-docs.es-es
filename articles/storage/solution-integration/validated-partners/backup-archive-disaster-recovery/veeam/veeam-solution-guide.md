---
title: Copia de seguridad de los datos en Azure con Veeam
titleSuffix: Azure Storage
description: Proporciona información general de los factores que se deben tener en cuenta y los pasos que deben seguirse para usar Azure como destino de almacenamiento y ubicación de recuperación para Veeam Backup & Replication.
author: karauten
ms.author: karauten
ms.date: 05/12/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 334ae28c160a01032d5403e06f40846e8b9d9ed5
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "109839192"
---
# <a name="backup-to-azure-with-veeam"></a>Copia de seguridad en Azure con Veeam

Este artículo le ayuda a integrar una infraestructura de Veeam en Azure Blob Storage. Incluye requisitos previos, consideraciones, implementación y una guía de operaciones. En este artículo se aborda el uso de Azure como destino de copia de seguridad fuera del sitio y como sitio de recuperación en caso de desastre, lo que impide el funcionamiento normal en el sitio principal.

> [!NOTE]
> Veeam también ofrece una solución de objetivo de tiempo de recuperación (RTO) inferior, Veeam Backup & Replication, que admite las cargas de trabajo de Azure VMware Solution. Esta solución le permite tener una máquina virtual en espera que puede ayudarle a recuperarse más rápidamente en caso de que se produzca un desastre en un entorno de producción de Azure. Veeam también ofrece restauración directa a Microsoft Azure y otras herramientas dedicadas para hacer copias de seguridad de recursos de Azure y Office 365. Estas funcionalidades están fuera del ámbito de este documento.

## <a name="reference-architecture"></a>Arquitectura de referencia

En el siguiente diagrama se proporciona una arquitectura de referencia para las implementaciones locales a Azure y dentro de Azure.

![Diagrama de la arquitectura de referencia de Veeam a Azure.](../media/veeam-architecture.png)

La implementación de Veeam existente se puede integrar fácilmente en Azure mediante la adición de una o varias cuentas de Azure Storage como repositorio de copia de seguridad en la nube. Veeam también le permite recuperar copias de seguridad desde el entorno local en Azure, lo que le ofrece un sitio de recuperación a petición en Azure.

## <a name="veeam-interoperability-matrix"></a>Matriz de interoperabilidad de Veeam

| Carga de trabajo | GPv2 y Blob Storage | Compatibilidad con el nivel de acceso esporádico | Compatibilidad con el nivel de archivo | Compatibilidad con la familia de Data Box |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Datos y máquinas virtuales locales | v10a | v10a | N/D | 10a<sup>*</sup> |
| Máquinas virtuales de Azure | v10a | v10a | N/D | 10a<sup>*</sup> |
| Blob de Azure | v10a | v10a | N/D | 10a<sup>*</sup> |
| Azure Files | v10a | v10a | N/D | 10a<sup>*</sup> |

Veeam también ha ofrecido compatibilidad con las características anteriores de Azure en las versiones anteriores de su producto, por lo que se recomienda encarecidamente aprovechar la versión más reciente del producto para disfrutar de una experiencia óptima.

<sup>*</sup>Veeam Backup & Replication solo admite la API REST para Azure Data Box. Por lo tanto, no admite Azure Data Box Disk. Consulte [aquí](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=110) los detalles del soporte técnico de Data Box.


## <a name="before-you-begin"></a>Antes de empezar

Un poco de planificación inicial le permitirá usar Azure como destino de copia de seguridad fuera del sitio y como sitio de recuperación.

### <a name="get-started-with-azure"></a>Introducción a Azure

Microsoft ofrece un marco para empezar a trabajar con Azure. [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) es un enfoque detallado para la transformación digital empresarial y una guía completa para la planificación de una adopción de la nube de calidad de producción. CAF incluye una [guía paso a paso de configuración de Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) para ayudarle a empezar de forma rápida y segura. Puede encontrar una versión interactiva en [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Encontrará arquitecturas de ejemplo, procedimientos recomendados específicos para la implementación de aplicaciones y recursos de aprendizaje gratuitos que le ayudarán a convertirse en un experto en Azure.

### <a name="consider-the-network-between-your-location-and-azure"></a>Consideraciones sobre la red entre su ubicación y Azure

Tanto si se usan los recursos en la nube para ejecutar los procesos de producción, pruebas y desarrollo, como si se trata de un destino de copia de seguridad y un sitio de recuperación, es importante comprender las necesidades de ancho de banda para la propagación inicial de copias de seguridad y para las transferencias diarias en curso.

Azure Data Box permite transferir la línea de base de la copia de seguridad inicial a Azure sin requerir más ancho de banda. Esto resulta útil si se estima que la transferencia de línea de base tarda más de lo admisible. Puede usar el estimador de transferencia de datos al crear una cuenta de almacenamiento para calcular el tiempo necesario para transferir la copia de seguridad inicial.

![Muestra estimador de transferencia de datos de Azure Storage en el portal.](../media/az-storage-transfer.png)

Recuerde que necesitará suficiente capacidad de red para admitir las transferencias de datos diarias en la ventana de transferencia necesaria (ventana de copia de seguridad) sin afectar a las aplicaciones de producción. En esta sección se describen las herramientas y técnicas disponibles para evaluar las necesidades de la red.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Cálculo del ancho de banda necesario

Hay disponibles varias opciones de evaluación para determinar la tasa de cambio y el tamaño total del conjunto de copia de seguridad para la transferencia de base de referencia inicial a Azure. A continuación, se muestran algunos ejemplos de herramientas de evaluación y creación de informes:

- [MiTrend](https://mitrend.com/)
- [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
- [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Cálculo del ancho de banda de Internet no utilizado

Es importante conocer cuánto ancho de banda tiene disponible a diario que no se suela utilizar (o su *capacidad de aumento*). Esto le ayudará a evaluar si puede cumplir los siguientes objetivos:

- hora inicial de carga cuando no se usa Azure Data Box para la propagación sin conexión.
- completar copias de seguridad diarias en función de la tasa de cambio identificada anteriormente y la ventana de copia de seguridad.

Use los siguientes métodos para identificar la capacidad de aumento de ancho de banda que sus copias de seguridad de Azure pueden consumir.

- Si ya es cliente de Azure ExpressRoute, consulte su [uso del circuito](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) en Azure Portal.
- Póngase en contacto con su ISP. Debería poder enviarle informes que muestren su uso diario y mensual existente.
- Hay varias herramientas que pueden medir el uso mediante la supervisión del tráfico de red en el nivel de enrutador o conmutador. Entre ellas se incluyen las siguientes:

  - [Bandwidth Analyzer Pack de Solarwinds](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [PRTG de Paessler](https://www.paessler.com/bandwidth_monitoring)
  - [Network Assistant de Cisco](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Elección de las opciones de almacenamiento adecuadas

Al usar Azure como destino de copia de seguridad, hará uso de [Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md). Blob Storage es la solución de almacenamiento de objetos de Microsoft. Blob Storage está optimizado para almacenar grandes cantidades de datos no estructurados, que no cumplen con ninguna definición o modelo de datos. Además, Azure Storage es duradero, seguro y escalable y ofrece una alta disponibilidad. Puede seleccionar el almacenamiento adecuado para la carga de trabajo con el fin de proporcionar el [nivel de resistencia](../../../../common/storage-redundancy.md) para cumplir los contratos de nivel de servicio internos. Blob Storage es un servicio de pago por uso. [Cada mes se le cobra](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) la cantidad de datos almacenados, el acceso a esos datos y, en el caso de los niveles de acceso esporádico y de archivo, un período de retención mínimo necesario. En las tablas siguientes se resumen las opciones de resistencia y de niveles aplicables a los datos de copia de seguridad.

**Opciones de resistencia de Blob Storage:**

|  |Con redundancia local  |Con redundancia de zona  |Con redundancia geográfica  |Con redundancia de zona geográfica  |
|---------|---------|---------|---------|---------|
|**N.º efectivo de copias**     | 3         | 3         | 6         | 6 |
|**N.º de zonas de disponibilidad**     | 1         | 3         | 2         | 4 |
|**N.º de regiones**     | 1         | 1         | 2         | 2 |
|**Conmutación por error manual en la región secundaria**     | N/D         | N/D         | Sí         | Sí |

**Niveles de Blob Storage:**

|  | nivel de acceso frecuente   |nivel de acceso esporádico   | nivel de archivo |
| ----------- | ----------- | -----------  | -----------  |
| **Disponibilidad** | 99,9 %         | 99%         | Sin conexión      |
| **Cargos de uso** | Mayores costos de almacenamiento, menores costos de acceso y transacciones | Menores costos de almacenamiento, mayores costos de acceso y transacciones | Menores costos de almacenamiento, mayores costos de acceso y transacciones |
| **Retención de datos mínima requerida** | N/D | 30 días | 180 días |
| **Latencia (tiempo hasta el primer byte)** | Milisegundos | Milisegundos | Horas |

#### <a name="sample-backup-to-azure-cost-model"></a>Ejemplo de modelo de costes de copia de seguridad en Azure

El pago por uso puede ser intimidatorio para los clientes que no están familiarizados con la nube. Aunque solo paga por la capacidad usada, el precio también incluye las transacciones (lectura o escritura) y la [salida de los datos](https://azure.microsoft.com/pricing/details/bandwidth/) que se vuelven a leer en el entorno local cuando se usa el [plan de datos ilimitados o local directo de Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/), donde se incluye la salida de datos de Azure. Puede usar la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/calculator/) para realizar análisis de tipo "What if". Puede basar el análisis en la lista de precios o en los [precios de capacidad reservada de Azure Storage](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md), que pueden suponer un ahorro de hasta el 38%. Este es un ejercicio de precios de ejemplo para modelar el coste mensual de la copia de seguridad en Azure. Se trata solo de un ejemplo. *Los precios pueden variar debido a actividades que no se capturan aquí.*

|Factor de coste  |Coste mensual  |
|---------|---------|
|100 TB de datos de copia de seguridad en el almacenamiento esporádico     |1556,48 USD         |
|2 TB de datos nuevos escritos al día x 30 días     |42 USD en transacciones          |
|Total mensual estimado     |1598,48 USD         |
|---------|---------|
|Restauración única de 5 TB en un entorno local a través de la red pública de Internet   | 527,26 USD         |

> [!Note]
> Esta estimación se ha generado en la calculadora de precios de Azure con los precios de pago por uso de la región Este de EE. UU. y se basa en el valor predeterminado de Veeam de tamaño de fragmento de 512 KB para las transferencias WAN. Es posible que este ejemplo no sea aplicable a sus requisitos.

## <a name="implementation-guidance"></a>Guía de implementación

En esta sección se proporciona una breve guía sobre cómo agregar Azure Storage a una implementación local de Veeam. Para obtener instrucciones detalladas y consideraciones sobre planeamiento, se recomienda examinar la siguiente guía de Veeam para conocer su [nivel de capacidad](https://helpcenter.veeam.com/docs/backup/vsphere/capacity_tier.html?ver=110).

1. Abra Azure Portal y busque **Cuentas de almacenamiento**. También puede hacer clic en el icono de servicio predeterminado.

      ![Muestra la adición de cuentas de Storage en Azure Portal.](../media/azure-portal.png)

      ![Muestra dónde ha escrito "Storage" en el cuadro de búsqueda de Azure Portal.](../media/locate-storage-account.png)

2. Seleccione **Crear** para agregar una cuenta. Seleccione o cree un grupo de recursos, proporcione un nombre único, elija la región, seleccione el rendimiento **Estándar**, deje siempre el tipo de cuenta como **Storage V2** y elija el nivel de replicación que se ajuste a los SLA y el nivel predeterminado que aplicará el software de copia de seguridad. Una cuenta de Azure Storage hace que los niveles de acceso frecuente, esporádico y de archivo estén disponibles en una sola cuenta, y las directivas de Veeam permiten usar varios niveles para administrar de forma eficaz el ciclo de vida de los datos.

    ![Muestra la configuración de la cuenta de Storage en el portal.](../media/account-create-1.png)

3. Por el momento, no cambie las opciones predeterminadas de protección de datos y de redes. **No** habilite la eliminación temporal de las cuentas de almacenamiento que almacenan los niveles de capacidad de Veeam.

 4. A continuación, le recomendamos usar la configuración predeterminada de la pantalla **Opciones avanzadas** para los casos de uso de copias de seguridad en Azure.

    ![Muestra la pestaña Opciones avanzadas en el portal.](../media/account-create-3.png)

5. Agregue etiquetas para la organización si usa el etiquetado y cree su cuenta.

6. Ahora solo debe completar dos pasos rápidos para poder agregar la cuenta al entorno de Veeam. En Azure Portal, vaya a la cuenta que creó y seleccione **Contenedores** en el menú **Blob service**. Agregue un contenedor y elija un nombre descriptivo. A continuación, navegue hasta el elemento **Claves de acceso** en **Configuración** y copie el **nombre de la cuenta de Storage** y una de las dos claves de acceso. Necesitará el nombre del contenedor, el nombre de la cuenta y la clave de acceso en los pasos siguientes.

    ![Muestra la creación del contenedor en el portal.](../media/container-b.png)

    ![Muestra la configuración de la clave de acceso en el portal.](../media/access-key.png)

    > [!Note]
    > Veeam Backup & Replication ofrece opciones adicionales para conectarse a Azure. En el caso de uso de este artículo, el procedimiento recomendado es usar Microsoft Azure Blob Storage como destino de copia de seguridad.

7. (*Opcional*) Puede agregar capas de seguridad adicionales a la implementación.

     1. Configure el acceso basado en roles para limitar quién puede hacer cambios en la cuenta de Storage. Para obtener más información, consulte [Roles integrados en las operaciones de administración](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Restrinja el acceso a la cuenta a segmentos de red específicos con la [configuración del firewall de Storage](../../../../common/storage-network-security.md) para evitar intentos de acceso desde fuera de la red corporativa.

        ![Muestra la configuración del firewall de Storage en el portal.](../media/storage-firewall.png)

     1. [Bloquee la eliminación](../../../../../azure-resource-manager/management/lock-resources.md) de la cuenta para evitar eliminar accidentalmente la cuenta de Storage.

        ![Bloqueo de recursos](../media/resource-lock.png)

     1. Configure los [procedimientos recomendados de seguridad](../../../../../storage/blobs/security-recommendations.md) adicionales.

8. En la consola de administración de Veaam Backup & Replication, vaya a **Backup Infrastructure** (Infraestructura de Backup) y haga clic con el botón derecho en el panel de información general y seleccione **Add Backup Repository** (Agregar repositorio de copia de seguridad) para abrir el Asistente para la configuración. En el cuadro de diálogo, seleccione **Object storage (Almacenamiento de objetos)**  -> **Microsoft Azure Blob Storage** -> **Azure Blob Storage**.

    ![Muestra cómo seleccionar el almacenamiento de objetos en el Asistente para repositorios de Veeam.](../media/veeam-repo-a.png)

    ![Muestra cómo seleccionar Microsoft Azure Blob Storage en el Asistente para repositorios de Veeam.](../media/veeam-repo-b.png)

    ![Muestra cómo seleccionar Azure Blob Storage en el Asistente para repositorios de Veeam.](../media/veeam-repo-c.png)

9. A continuación, especifique un nombre y una descripción del nuevo repositorio de almacenamiento de blobs.

    ![Muestra cómo escribir un nombre para el repositorio en el Asistente para repositorios de Veeam.](../media/veeam-repo-d.png)

10. En el paso siguiente, agregue las credenciales para acceder a su cuenta de Azure Storage. Seleccione **Microsoft Azure Storage Account** (Cuenta de Microsoft Azure Storage) en el administrador de credenciales en la nube, y escriba el nombre y la clave de acceso de la cuenta de almacenamiento. Seleccione **Azure Global** en el selector de regiones y cualquier servidor de puerta de enlace, si procede.

    ![Muestra cómo especificar una cuenta en el Asistente para repositorios de Veeam.](../media/veeam-repo-e.png)

    > [!Note]
    > Si opta por no usar un servidor de puerta de enlace de Veeam, asegúrese de que todas las extensiones del repositorio de escalabilidad horizontal tienen acceso directo a Internet.

11. En el registro de contenedor, seleccione el contenedor de Azure Storage, y elija o cree una carpeta en la que almacenar las copias de seguridad. También puede definir un límite flexible de la capacidad de almacenamiento general que va a usar Veeam, lo cual se recomienda. Revise la información mostrada en la sección Resumen y complete la herramienta de configuración. Ahora puede seleccionar el nuevo repositorio en la configuración del trabajo de copia de seguridad.

    ![Muestra cómo especificar un contenedor en el Asistente para repositorios de Veeam.](../media/veeam-repo-f.png)

    ![Muestra cómo crear una carpeta en el Asistente para repositorios de Veeam.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Guía de operaciones

### <a name="azure-alerts-and-performance-monitoring"></a>Supervisión de rendimiento y alertas de Azure

Es aconsejable supervisar tanto los recursos de Azure como la capacidad de Veeam para aprovecharlos tal como lo haría con cualquier destino de almacenamiento en el que confíe para almacenar las copias de seguridad. Si combina las funcionalidades de supervisión de Azure Monitor y Veeam (es decir, la pestaña **Estadísticas** del nodo **Trabajos** de la consola de administración de Veeam o las opciones más avanzadas como Veeam One Reporter), le será más fácil mantener el entorno en buen estado.

#### <a name="azure-portal"></a>Azure Portal

Azure proporciona una solución de supervisión sólida mediante [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md). Puede [configurar Azure monitor](../../../../blobs/monitor-blob-storage.md) para realizar un seguimiento de la capacidad, las transacciones, la disponibilidad, la autenticación, etc. de Azure Storage. [Aquí](../../../../blobs/monitor-blob-storage-reference.md) puede encontrar la referencia completa de las métricas de las que se realiza un seguimiento. Algunas métricas útiles para hacer un seguimiento son BlobCapacity (para asegurarse de que permanece por debajo del [límite máximo de capacidad de la cuenta de almacenamiento](../../../../common/scalability-targets-standard-account.md)), Ingress y Egress (para hacer un seguimiento de la cantidad de datos que se escriben y se leen de la cuenta de Azure Storage), y SuccessE2ELatency (para hacer un seguimiento del tiempo de ida y vuelta de las solicitudes entre Azure Storage y MediaAgent).

También puede [crear alertas de registro](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) para hacer un seguimiento del estado del servicio Azure Storage y ver el [Panel de estado de Azure](https://status.azure.com/status) en cualquier momento.

#### <a name="veeam-reporting"></a>Informes de Veeam

- [Configuración de la creación de informes de Veeam One](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Alarmas de Veeam Backup & Replication](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Procedimiento para abrir casos de soporte técnico

Cuando necesite ayuda con la solución de copia de seguridad en Azure, debe abrir un caso tanto con Veeam como con Azure. Esto permitirá que las organizaciones de soporte técnico colaboren si fuera necesario.

#### <a name="to-open-a-case-with-veeam"></a>Procedimiento para abrir un caso con Veeam

En el [sitio web de soporte técnico de Veeam](https://www.veeam.com/support.html), inicie sesión y abra un caso.

Para conocer las opciones de soporte técnico que Veeam pone a su disposición, consulte la [Directiva de asistencia al cliente de Veeam](https://www.veeam.com/veeam_software_support_policy_ds.pdf).

También puede llamar a para abrir un caso, consulte los [números de soporte técnico en todo el mundo](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers).

#### <a name="to-open-a-case-with-azure"></a>Procedimiento para abrir un caso con Azure

En [Azure Portal](https://portal.azure.com), busque **soporte técnico** en la barra de búsqueda de la parte superior. Seleccione **Ayuda y soporte técnico** -> **Nueva solicitud de soporte técnico**.

> [!NOTE]
> Cuando abra un caso, especifique que necesita ayuda con "Azure Storage" o "Redes de Azure". No especifique "Azure Backup". Azure Backup es el nombre de un servicio de Azure, y el caso no se asignaría correctamente.

### <a name="links-to-relevant-veeam-documentation"></a>Vínculos a la documentación pertinente de Veeam

Consulte la siguiente documentación de Veeam para obtener más detalles:

- [Guía de usuario de Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Guía de la arquitectura de Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Ofertas del marketplace

Puede seguir usando la solución Veeam que conoce y en la que confía para proteger las cargas de trabajo que se ejecutan en Azure. Veeam ha facilitado la implementación de su solución en Azure y protege las máquinas virtuales de Azure y muchos otros servicios de Azure.

- [Implementación de Veeam Backup & Replication a través de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [Sitio web de Veeam Backup & Replication sobre Azure](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes recursos en el sitio web de Veeam para obtener información sobre los escenarios de uso especializados:

- [Vídeos de procedimientos de Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Documentación técnica de Veeam](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Base de conocimiento de Veeam y preguntas más frecuentes](https://www.veeam.com/knowledge-base.html?ad=menu-resources)