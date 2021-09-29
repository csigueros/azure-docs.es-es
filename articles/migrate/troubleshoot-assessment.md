---
title: Solución de problemas de las evaluaciones en Azure Migrate
description: Obtenga ayuda con la evaluación en Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/28/2021
ms.openlocfilehash: eca62eccca6f53935f29a16bca0afc382896212c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124810358"
---
# <a name="troubleshoot-assessment"></a>Solución de problemas de evaluación

Este artículo incluye ayuda para la solución de problemas de evaluación y visualización de dependencias con [Azure Migrate: Detección y evaluación](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="assessment-readiness-issues"></a>Problemas de preparación de la evaluación

En esta tabla se muestra la ayuda para corregir los siguientes problemas de preparación de la valoración.

**Problema** | **Revisión**
--- | ---
Tipo de arranque no compatible | Azure no admite máquinas virtuales con el tipo de arranque EFI. Convierta el tipo de arranque a BIOS antes de ejecutar una migración. <br/><br/>Puede usar Azure Migrate Server Migration para controlar la migración de estas máquinas virtuales. Durante la migración, el tipo de arranque de la máquina virtual se convertirá a BIOS.
Sistemas operativos Windows condicionalmente compatibles | El sistema operativo ha superado la fecha de finalización del soporte técnico y necesita un contrato de soporte técnico personalizado para [recibir soporte técnico en Azure](/troubleshoot/azure/virtual-machines/server-software-support). Considere la posibilidad de actualizar antes de migrar a Azure. Revise la información sobre la [preparación de los servidores que ejecutan Windows Server 2003](prepare-windows-server-2003-migration.md) para la migración a Azure.
Sistemas operativos Windows no compatibles | Azure solo admite [versiones del sistema operativo Windows seleccionadas](/troubleshoot/azure/virtual-machines/server-software-support). Considere la posibilidad de actualizar el servidor antes de migrar a Azure.
Sistema operativo Linux aprobado con condiciones | Azure aprueba solo [versiones del sistema operativo Linux seleccionadas](../virtual-machines/linux/endorsed-distros.md). Considere la posibilidad de actualizar el servidor antes de migrar a Azure. Para más información, consulte [este sitio web](#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment).
Sistema operativo Linux no aprobado | El servidor puede iniciarse en Azure, pero Azure no proporciona compatibilidad con el sistema operativo. Considere la actualización a una [versión de Linux aprobada](../virtual-machines/linux/endorsed-distros.md) antes de migrar a Azure.
Sistema operativo desconocido | El sistema operativo de la máquina virtual se especificó como "Otros" en vCenter Server. Este comportamiento impide que Azure Migrate compruebe la preparación para Azure de la máquina virtual. Asegúrese de que Azure [admite](./migrate-support-matrix-vmware-migration.md#azure-vm-requirements) el sistema operativo antes de migrar el servidor.
Versión de bits no admitida | Es posible que las máquinas virtuales con un sistema operativo de 32 bits arranquen en Azure, pero se recomienda actualizar a 64 bits antes de migrar a Azure.
Requiere una suscripción de Microsoft Visual Studio | En el servidor se ejecuta un sistema operativo cliente Windows, que solo se admite mediante una suscripción de Visual Studio.
No se encontró ninguna máquina virtual para el rendimiento de almacenamiento requerido | El rendimiento del almacenamiento (operaciones de entrada/salida por segundo [IOPS] y rendimiento) necesario para el servidor supera la compatibilidad con máquinas virtuales de Azure. Reduzca los requisitos de almacenamiento del servidor antes de realizar la migración.
No se encontró ninguna máquina virtual para el rendimiento de red requerido | El rendimiento de red (entrada/salida) requerido para el servidor excede el soporte técnico de máquina virtual de Azure. Reduzca los requisitos de red del servidor.
No se encontró ninguna máquina virtual en la ubicación especificada | Utilice una ubicación de destino diferente antes de la migración.
Uno o varios discos no son adecuados | Uno o varios discos conectados a la máquina virtual no cumplen los requisitos de Azure.<br/><br/> Azure Migrate: Detección y evaluación evalúa los discos en función de los límites de disco de los discos Ultra (64 TB).<br/><br/> Para cada disco conectado a la máquina virtual, asegúrese de que el tamaño del disco es menor que 64 TB (compatible con discos SSD Ultra).<br/><br/> Si no lo es, reduzca el tamaño de los discos antes de la migración o bien use varios discos en Azure y [colóquelos juntos](../virtual-machines/premium-storage-performance.md#disk-striping) para obtener mayores límites de almacenamiento. Asegúrese de que el rendimiento (IOPS y rendimiento) necesario para cada disco sea compatible con los [discos de máquinas virtuales administradas](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) de Azure.
Uno o varios adaptadores de red no son adecuados | Quite los adaptadores de red no utilizados del servidor antes de realizar la migración.
El recuento del disco supera el límite | Quite los discos no utilizados del servidor antes de realizar la migración.
El tamaño del disco supera el límite | Azure Migrate: Detección y evaluación admite discos con un tamaño de hasta 64 TB (discos Ultra). Reduzca los discos a menos de 64 TB antes de la migración o use varios discos en Azure y [colóquelos juntos](../virtual-machines/premium-storage-performance.md#disk-striping) para obtener mayores límites de almacenamiento.
El disco no está disponible en la ubicación especificada | Asegúrese de que el disco está en la ubicación de destino antes de realizar la migración.
El disco no está disponible para la redundancia especificada | El disco debe usar el tipo de almacenamiento de redundancia definido en la configuración de evaluación (LRS de forma predeterminada).
No se ha podido determinar la idoneidad del disco debido a un error interno | Intente crear una evaluación para el grupo.
No se encontró ninguna máquina virtual con los núcleos y memoria requeridos | Azure no pudo encontrar un tipo de máquina virtual adecuado. Reduzca la memoria y el número de núcleos del servidor local antes de realizar la migración.
No se ha podido determinar la idoneidad de la máquina virtual debido a un error interno | Intente crear una evaluación para el grupo.
No se ha podido determinar la idoneidad de uno o varios discos debido a un error interno | Intente crear una evaluación para el grupo.
No se ha podido determinar la idoneidad de uno o varios adaptadores de red debido a un error interno | Intente crear una evaluación para el grupo.
No se ha encontrado ningún tamaño de máquina virtual para la instancia reservada (RI) de moneda de oferta | Servidor marcado como "no válido" porque no se ha encontrado el tamaño de la máquina virtual para la combinación seleccionada de RI, oferta y moneda. Edite las propiedades de valoración para elegir las combinaciones válidas y recalcular la valoración. 
Protocolo de Internet preparado condicionalmente | Solo se aplica a las valoraciones de Azure VMware Solution. Azure VMware Solution no admite direcciones de Internet IPv6. Póngase en contacto con el equipo de Azure VMware Solution para obtener instrucciones de corrección si el servidor se detecta con IPv6.

## <a name="suggested-migration-tool-in-an-import-based-azure-vmware-solution-assessment-is-unknown"></a>Se desconoce la herramienta de migración sugerida en una valoración de Azure VMware Solution basada en importación

En el caso de los servidores importados mediante un archivo CSV, se desconoce la herramienta de migración predeterminada en una valoración de Azure VMware Solution. Para los servidores de un entorno de VMware, use la solución Hybrid Cloud Extension (HCX) de VMware. [Más información](../azure-vmware/configure-vmware-hcx.md).

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Las máquinas virtuales Linux están "condicionalmente preparadas" en una valoración de máquinas virtuales de Azure.

En el caso de las máquinas virtuales de VMware e Hyper-V, una valoración de máquinas virtuales de Azure marca las máquinas virtuales Linux como "condicionalmente preparadas" debido a una brecha conocida. 

- La brecha evita que detecte la versión secundaria del sistema operativo Linux instalada en las máquinas virtuales locales.
- Por ejemplo, para RHEL 6.10, actualmente una valoración de máquinas virtuales de Azure solo detecta RHEL 6 como la versión del sistema operativo. Este comportamiento se debe a que vCenter Server y el host de Hyper-V no proporcionan la versión de kernel para los sistemas operativos de máquinas virtuales Linux.
- Como Azure solo aprueba versiones específicas de Linux, las máquinas virtuales Linux actualmente se marcan como "condicionalmente preparadas" en una valoración de máquinas virtuales de Azure.
- Puede determinar si el sistema operativo Linux en ejecución en la máquina virtual local está aprobado en Azure; para ello, consulte [Soporte técnico de Linux en Azure](../virtual-machines/linux/endorsed-distros.md).
- Una vez haya comprobado que la distribución está aprobada, puede omitir esta advertencia.

Este intervalo se puede solucionar habilitando [la detección de aplicaciones](./how-to-discover-applications.md) en las máquinas virtuales de VMware. Una valoración de máquinas virtuales de Azure usa el sistema operativo detectado de la máquina virtual con las credenciales de invitado proporcionadas. Estos datos del sistema operativo identifican la información del sistema operativo adecuada en el caso de las máquinas virtuales Windows y Linux.

## <a name="operating-system-version-not-available"></a>La versión del sistema operativo no está disponible

En el caso de los servidores físicos, la información de la versión secundaria del sistema operativo debe estar disponible. Si no está disponible, póngase en contacto con el Soporte técnico de Microsoft. Para los servidores en un entorno de VMware, Azure Migrate usa la información del sistema operativo especificada para la máquina virtual en vCenter Server. Pero vCenter Server no proporciona la versión secundaria para los sistemas operativos. Para detectar la versión secundaria, configure la [detección de aplicaciones](./how-to-discover-applications.md). En el caso de las máquinas virtuales de Hyper-V, no se admite la detección de versiones secundarias del sistema operativo. 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Las SKU de Azure son más grandes que las locales en una valoración de máquinas virtuales de Azure.

Una valoración de máquinas virtuales de Azure podría recomendar SKU de máquinas virtuales de Azure con más núcleos y memoria que la asignación local actual según el tipo de valoración:

- La recomendación de las SKU de máquinas virtuales depende de las propiedades de la evaluación.
- La recomendación se ve afectada por el tipo de valoración que realiza en una valoración de máquinas virtuales de Azure. Los dos tipos son **Basado en el rendimiento** o **Como local**.
- Para las valoraciones basadas en el rendimiento, la valoración de máquinas virtuales de Azure tiene en cuenta los datos de uso de las máquinas virtuales en el entorno local (uso de CPU, memoria, disco y red) para determinar la SKU de máquina virtual de destino correcta para las máquinas virtuales en el entorno local. También agrega un factor de confort al determinar el uso efectivo.
- Para el dimensionamiento local, no se tienen en cuenta los datos de rendimiento y la SKU de destino se recomienda en función de la asignación local.

Ahora se verá una recomendación de ejemplo:

Hay una máquina virtual local con cuatro núcleos y 8 GB de memoria, con un uso de CPU del 50 %, un uso de memoria del 50 % y un factor de confort específico de 1,3.

- Si la evaluación es **Como local**, se recomienda una SKU de VM de Azure con 4 núcleos y 8 GB de memoria.
- Si la valoración está **basada en el rendimiento**, en función del uso de memoria y CPU efectiva (el 50 % de 4 núcleos * 1,3 = 2,6 núcleos y el 50 % de 8 GB de memoria * 1,3 = 5,3 GB de memoria), se recomienda la SKU de máquina virtual más barata de cuatro núcleos (número de núcleos admitidos más cercano) y 8 GB de memoria (tamaño de memoria admitido más cercano).
- [Más información](concepts-assessment-calculation.md#types-of-assessments) sobre el ajuste de tamaño de evaluación.

## <a name="why-is-the-recommended-azure-disk-sku-bigger-than-on-premises-in-an-azure-vm-assessment"></a>¿Por qué las SKU de discos de Azure recomendadas son más grandes que las locales en una valoración de máquinas virtuales de Azure?

Es posible que la valoración de máquinas virtuales de Azure recomiende un disco más grande según el tipo de valoración:

- El ajuste de tamaño de disco depende de dos propiedades de evaluación: criterio de ajuste de tamaño y tipo de almacenamiento.
- Si el criterio de dimensionamiento está **basado en el rendimiento** y el tipo de almacenamiento es **Automático**, se tendrán en cuenta los valores de rendimiento y de IOPS del disco para identificar el tipo de disco de destino (discos HDD estándar, SSD estándar, Premium o Ultra). Se recomienda una SKU de disco del tipo de disco y esta recomendación tiene en cuenta los requisitos de tamaño del disco en el entorno local.
- Si el criterio de dimensionamiento está **basado en el rendimiento** y el tipo de almacenamiento es **Premium**, se recomienda una SKU de disco Premium de Azure en función de los requisitos de IOPS, rendimiento y tamaño del disco en el entorno local. Se usa la misma lógica para ajustar el tamaño del disco cuando el criterio de ajuste de tamaño es **Como local** y el tipo de almacenamiento es **HDD estándar**, **SSD estándar**, **Prémium** o **Ultra**.

Por ejemplo, imagine que tiene un disco local con 32 GB de memoria, pero la IOPS agregada de lectura y escritura del disco es de 800. La valoración de máquinas virtuales de Azure recomienda un disco Premium debido a los mayores requisitos de IOPS. También recomienda una SKU de disco que pueda admitir las IOPS y el tamaño necesarios. La coincidencia más cercana en este ejemplo sería P15 (256 GB, 1100 IOPS). Aunque el tamaño que necesita el disco local era de 32 GB, la valoración de máquinas virtuales de Azure recomienda un disco con un tamaño mayor debido a los altos requisitos de IOPS del disco en el entorno local.

## <a name="why-is-performance-data-missing-for-some-or-all-vms-in-my-assessment-report"></a>¿Por qué faltan datos de rendimiento de algunas o todas las máquinas virtuales en el informe de valoración?

En el caso de la valoración **basada en el rendimiento**, la exportación del informe de valoración indica "PercentageOfCoresUtilizedMissing" o "PercentageOfMemoryUtilizedMissing" cuando el dispositivo de Azure Migrate no puede recopilar datos de rendimiento de las máquinas virtuales locales. Asegúrese de comprobar lo siguiente:

- Si las máquinas virtuales están encendidas durante el tiempo para el que crea la valoración.
- Si solo faltan contadores de memoria y está intentando evaluar las máquinas virtuales de Hyper-V, compruebe si tiene habilitada la memoria dinámica en estas máquinas virtuales. Debido a problema conocido, actualmente el dispositivo de Azure Migrate no puede recopilar el uso de memoria de esas máquinas virtuales.
- Si faltan todos los contadores de rendimiento, asegúrese de que se cumplen los requisitos de acceso a puertos para la evaluación. Obtenga más información sobre los requisitos de acceso a puertos para la valoración de [VMware](./migrate-support-matrix-vmware.md#port-access-requirements), [Hyper-V](./migrate-support-matrix-hyper-v.md#port-access) y los servidores [físicos](./migrate-support-matrix-physical.md#port-access).
Si falta alguno de los contadores de rendimiento, Azure Migrate: Detección y evaluación vuelve a la memoria y los núcleos locales asignados, y recomienda un tamaño de máquina virtual acorde.

## <a name="why-is-performance-data-missing-for-some-or-all-servers-in-my-azure-vm-or-azure-vmware-solution-assessment-report"></a>¿Por qué faltan datos de rendimiento para algunos o todos los servidores de la máquina virtual de Azure o el informe de valoración de Azure VMware Solution?

En el caso de la valoración **basada en el rendimiento**, la exportación del informe de valoración indica "PercentageOfCoresUtilizedMissing" o "PercentageOfMemoryUtilizedMissing" cuando el dispositivo de Azure Migrate no puede recopilar datos de rendimiento de los servidores locales. Asegúrese de comprobar lo siguiente:

- Si los servidores están encendidos durante el tiempo de creación de la valoración.
- Si solo faltan contadores de memoria e intenta evaluar servidores en un entorno Hyper-V. En este caso, habilite la memoria dinámica en los servidores y vuelva a calcular la valoración para reflejar los últimos cambios. El dispositivo solo puede recopilar los valores de uso para servidores en un entorno Hyper-V si el servidor tiene habilitada la memoria dinámica.
- Si faltan todos los contadores de rendimiento, asegúrese de que se permiten las conexiones de salida en los puertos 443 (HTTPS).

    > [!Note]
    > Si falta alguno de los contadores de rendimiento, Azure Migrate: Detección y evaluación vuelve a la memoria y los núcleos locales asignados, y recomienda un tamaño de máquina virtual acorde.

## <a name="why-is-performance-data-missing-for-some-or-all-sql-instances-or-databases-in-my-azure-sql-assessment"></a>¿Por qué faltan datos de rendimiento en algunas o todas las instancias o bases de datos de SQL en la valoración de Azure SQL?

Para asegurarse de que se recopilan los datos de rendimiento, compruebe lo siguiente:

- Si los servidores SQL están encendidos durante el tiempo de creación de la valoración.
- Si el estado de conexión del Agente SQL en Azure Migrate es **Conectado** y compruebe también el último latido. 
- Si el estado de conexión de Azure Migrate para todas las instancias de SQL es **Conectado** en el panel de la instancia de SQL detectada.
- Si faltan todos los contadores de rendimiento, asegúrese de que se permiten las conexiones de salida en los puertos 443 (HTTPS).

Si falta alguno de los contadores de rendimiento, la valoración de Azure SQL recomienda la configuración de Azure SQL más pequeña para esa instancia o base de datos.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>¿Por qué la clasificación de confianza de mi valoración es baja?

La calificación de confianza se calcula para las valoraciones **basadas en el rendimiento** en función del porcentaje de [puntos de datos disponibles](./concepts-assessment-calculation.md#ratings) necesarios para calcular la valoración. Una valoración podría obtener una clasificación de confianza baja por los motivos siguientes:

- No ha generado un perfil de su entorno durante el tiempo que ha estado creando la evaluación. Por ejemplo, si va a crear una valoración con la duración de rendimiento establecida en una semana, debe esperar al menos una semana después de iniciar la detección para que se recopilen todos los puntos de datos. Si no puede esperar tanto tiempo, cambie la duración del rendimiento a un período más reducido y vuelva a calcular la valoración.
- La valoración no puede recopilar los datos de rendimiento de algunos o todos los servidores en el período de valoración. Para obtener una clasificación de confianza alta, asegúrese de que: 
    - Los servidores estén encendidos mientras dure la valoración.
    - Se permiten las conexiones salientes en los puertos 443.
    - La memoria dinámica está habilitada en los servidores de Hyper-V.
    - El estado de conexión de los agentes de Azure Migrate es "Conectado". Compruebe también el último latido.
    - Para las valoraciones de Azure SQL, el estado de conexión de Azure Migrate para todas las instancias de SQL es "Conectado" en el panel de la instancia de SQL detectada.

    Recalcule la evaluación para reflejar los cambios más recientes en la clasificación de confianza.

- En el caso de las valoraciones de máquinas virtuales de Azure y Azure VMware Solution, se han creado pocos servidores después de iniciar la detección. Por ejemplo, imagine que va a crear una valoración para el historial de rendimiento del último mes, pero en el entorno se han creado varios servidores hace solo una semana. En este caso, los datos de rendimiento de los nuevos servidores no estarán disponibles para todo el período y la clasificación de confianza será baja. [Más información](./concepts-assessment-calculation.md#confidence-ratings-performance-based).
- En el caso de las evaluaciones de Azure SQL, se crearon algunas instancias de SQL o bases de datos después de que se iniciara la detección. Por ejemplo, imagine que va a crear una valoración para el historial de rendimiento del último mes, pero en el entorno se han creado varias instancias de SQL o bases de datos hace solo una semana. En este caso, los datos de rendimiento de los nuevos servidores no estarán disponibles para todo el período y la clasificación de confianza será baja. [Más información](./concepts-azure-sql-assessment-calculation.md#confidence-ratings).

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>¿Se incluye la licencia del sistema operativo en una valoración de máquinas virtuales de Azure?

Una valoración de máquinas virtuales de Azure solo tiene en cuenta el costo de la licencia del sistema operativo para los servidores Windows. Los costos de las licencias para los servidores Linux no se tienen en cuenta actualmente.

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>¿Cómo funciona el dimensionamiento basado en el rendimiento en una valoración de máquinas virtuales de Azure?

Una valoración de máquinas virtuales de Azure recopila continuamente datos de rendimiento de los servidores en el entorno local y los usa para recomendar las SKU de máquina virtual y disco de Azure. [Obtenga información](concepts-assessment-calculation.md#calculate-sizing-performance-based) sobre cómo se recopilan datos basados en el rendimiento.

## <a name="can-i-migrate-my-disks-to-an-ultra-disk-by-using-azure-migrate"></a>¿Puedo migrar mis discos a un disco Ultra mediante Azure Migrate?

No. Actualmente, Azure Migrate y Azure Site Recovery no admiten la migración a discos Ultra. Busque los pasos para implementar un disco Ultra en [este sitio web](../virtual-machines/disks-enable-ultra-ssd.md?tabs=azure-portal#deploy-an-ultra-disk).

## <a name="why-are-the-provisioned-iops-and-throughput-in-my-ultra-disk-more-than-my-on-premises-iops-and-throughput"></a>¿Por qué los valores de IOPS y rendimiento aprovisionados de mi disco Ultra son mayores que mis valores de IOPS y rendimiento locales?

Según la [página oficial de precios](https://azure.microsoft.com/pricing/details/managed-disks/), el disco Ultra se factura según los valores de tamaño, IOPS y rendimiento aprovisionados. Por ejemplo, si ha aprovisionado un disco Ultra de 200 GiB con 20 000 IOPS y 1000 MB/segundo y lo ha eliminado después de 20 horas, se asignará a la oferta de tamaño de disco de 256 GiB. Se le facturarán 256 GiB, 20 000 IOPS y 1000 MB/segundo durante 20 horas.

IOPS que se van a aprovisionar = (rendimiento detectado) *1024/256

## <a name="does-the-ultra-disk-recommendation-consider-latency"></a>¿La recomendación de disco Ultra tiene en cuenta la latencia?

No, actualmente solo se usa el tamaño del disco, el rendimiento total y la cantidad de IOPS total para el dimensionamiento y el costo.

## <a name="i-can-see-m-series-supports-ultra-disk-but-in-my-assessment-where-ultra-disk-was-recommended-it-says-no-vm-found-for-this-location"></a>Veo que la serie M admite discos Ultra, pero en mi valoración, donde se ha recomendado el disco Ultra, se indica "No se encontró ninguna VM para esta ubicación"

Este resultado es posible, ya que no todos los tamaños de VM que admiten el disco Ultra están presentes en todas las regiones que admiten discos Ultra. Cambie la región de evaluación de destino para obtener el tamaño de VM de este servidor.

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>¿Por qué en la valoración se muestra una advertencia de que se ha creado con una combinación no válida de instancias reservadas, tiempo de actividad de máquina virtual y descuento (%)?

Al seleccionar **Instancias reservadas**, las propiedades **Descuento (%)** y **Tiempo de actividad de VM** no son aplicables. Como la valoración se ha creado con una combinación no válida de estas propiedades, se han deshabilitado los botones **editar** y **Volver a calcular**. Cree una valoración. [Más información](./concepts-assessment-calculation.md#whats-an-assessment).

## <a name="i-dont-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>No veo los datos de rendimiento de algunos adaptadores de red en los servidores físicos

Este problema puede ocurrir si el servidor físico tiene habilitada la virtualización de Hyper-V. En estos servidores, debido a una brecha en el producto, Azure Migrate detecta actualmente los adaptadores de red físicos y virtuales. El rendimiento de la red solo se captura en los adaptadores de red virtuales descubiertos.

## <a name="the-recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>La SKU de máquina virtual de Azure recomendada para el servidor físico es demasiado grande

Este problema puede ocurrir si el servidor físico tiene habilitada la virtualización de Hyper-V. En estos servidores, Azure Migrate detecta actualmente los adaptadores de red físicos y virtuales. Como resultado, el número de adaptadores de red detectados es mayor que el número real. La valoración de máquinas virtuales de Azure elige una que pueda admitir el número necesario de adaptadores de red, lo que puede dar lugar a una máquina virtual demasiado grande. [Obtenga más información](./concepts-assessment-calculation.md#calculating-sizing) sobre el impacto del número de adaptadores de red en el dimensionamiento. Esta brecha del producto se solucionará más adelante.

## <a name="the-readiness-category-is-marked-not-ready-for-my-physical-server"></a>La categoría de preparación está marcada como "No preparado" para el servidor físico

Es posible que la categoría de preparación esté marcada incorrectamente como "No preparado" en el caso de un servidor físico que tenga habilitada la virtualización de Hyper-V. En estos servidores, debido a una brecha en el producto, Azure Migrate detecta actualmente los adaptadores físicos y virtuales. Como resultado, el número de adaptadores de red detectados es mayor que el número real. En las valoraciones **Como local** y **Basada en el rendimiento**, la valoración de máquinas virtuales de Azure elige una máquina virtual de Azure que pueda admitir el número necesario de adaptadores de red. Si se detecta que el número de adaptadores de red es superior a 32, el número máximo de NIC admitidas en máquinas virtuales de Azure, el servidor se marcará como "No preparado". [Obtenga más información](./concepts-assessment-calculation.md#calculating-sizing) sobre el impacto del número de NIC en el dimensionamiento.

## <a name="the-number-of-discovered-nics-is-higher-than-actual-for-physical-servers"></a>El número de NIC detectado es mayor que el valor real para los servidores físicos

Este problema puede ocurrir si el servidor físico tiene habilitada la virtualización de Hyper-V. Actualmente, Azure Migrate detecta los adaptadores físicos y virtuales en estos servidores. Como resultado, el número de NIC detectadas es mayor que el número real.

## <a name="capture-network-traffic"></a>Captura del tráfico de red

Para recopilar registros del tráfico de red:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione F12 para iniciar las Herramientas de desarrollo. Si es necesario, desactive la opción **Borrar entradas de navegación**.
1. Seleccione la pestaña **Red** para empezar a capturar el tráfico de red:
   - En Chrome, seleccione **Preserve log** (Conservar registro). La grabación debe iniciarse automáticamente. Un círculo rojo indica que la captura del tráfico está en curso. Si no aparece el círculo rojo, seleccione el círculo negro para comenzar.
   - En Microsoft Edge e Internet Explorer, la grabación debe iniciarse automáticamente. Si no es así, seleccione el botón de reproducción de color verde.
1. Pruebe a reproducir el error.
1. Una vez que haya encontrado el error durante la grabación, detenga la grabación y guarde una copia de la actividad grabada:
   - En Chrome, haga clic con el botón derecho y seleccione **Guardar como HAR con contenido**. Esta acción comprime y exporta los registros como un archivo .har.
   - En Microsoft Edge o Internet Explorer, seleccione la opción **Exportar tráfico capturado**. Esta acción comprime y exporta el registro.
1. Seleccione la pestaña **Consola** para ver si hay alguna advertencia o error. Para guardar el registro de la consola:
   - En Chrome, haga clic con el botón derecho en cualquier lugar en el registro de la consola. Seleccione **Guardar como** para exportar y comprimir el registro.
   - En Microsoft Edge o Internet Explorer, haga clic con el botón derecho sobre los errores y seleccione **Copiar todo**.
1. Cierre las Herramientas de desarrollo.

## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>¿Dónde se detectan los datos del sistema operativo en mi valoración?

- En el caso de las máquinas virtuales de VMware, de forma predeterminada, son los datos del sistema operativo que proporciona vCenter Server.
   - En el caso de las máquinas virtuales Linux de VMware, si está habilitada la detección de aplicaciones, los detalles del sistema operativo se capturan desde la máquina virtual invitada. Para comprobar qué detalles del sistema operativo se incluyen en la valoración, vaya a la vista **Servidores detectados** y mantenga el mouse sobre el valor de la columna **Sistema operativo**. En el texto que aparece, podrá ver si los datos del sistema operativo que ve se han recopilado de vCenter Server o de la máquina virtual invitada con las credenciales de la máquina virtual.
   - En el caso de las máquinas virtuales Windows, los detalles del sistema operativo siempre se capturan desde el vCenter Server.
- En el caso de las máquinas virtuales de Hyper-V, los datos del sistema operativo se recopilan del host de Hyper-V.
- En el caso de los servidores físicos, se capturan desde el servidor.

## <a name="common-web-apps-discovery-errors"></a>Errores comunes de detección de aplicaciones web

Azure Migrate ofrece opciones para valorar las aplicaciones web ASP.NET detectadas para la migración a Azure App Service mediante la herramienta Azure Migrate: Detección y evaluación. Consulte el tutorial de [evaluación](tutorial-assess-webapps.md) para comenzar.

En la tabla se resumen los errores de valoración típicos de App Service.

| **Error** | **Causa** | **Acción recomendada** |
|--|--|--|
|**Comprobación del grupo de aplicaciones**|El sitio de IIS usa los siguientes grupos de aplicaciones: {0}.|Azure App Service no admite más de una configuración de grupo de aplicaciones por aplicación de App Service. Mueva las cargas de trabajo a un único grupo de aplicaciones y quite los demás grupos de aplicaciones.|
|**Comprobación de la identidad del grupo de aplicaciones**|El grupo de aplicaciones del sitio se ejecuta como un tipo de identidad de usuario no admitido: {0}.|App Service no admite el uso de los tipos de identidad del grupo de aplicaciones LocalSystem o SpecificUser. Establezca el grupo de aplicaciones para que se ejecute como ApplicationPoolIdentity.|
|**Comprobación de autorización**|Se han encontrado los siguientes tipos de autenticación no admitidos: {0}.|La configuración y los tipos de autenticación admitidos de App Service son diferentes de los de IIS local. Deshabilite los tipos de autenticación no admitidos en el sitio. Una vez que haya completado la migración, será posible configurar el sitio mediante uno de los tipos de autenticación admitidos de App Service.|
|**Se desconoce la comprobación de autorización**|No se pueden determinar los tipos de autenticación habilitados para toda la configuración del sitio.|No se pueden determinar los tipos de autenticación. Corrija todos los errores de configuración y confirme que todas las ubicaciones de contenido del sitio son accesibles para el grupo de administradores.|
|**Comprobación del error de configuración**|Se han encontrado los siguientes errores de configuración: {0}.|No se puede determinar la preparación de la migración sin leer toda la configuración aplicable. Corrija todos los errores de configuración. Asegúrese de que la configuración es válida y accesible.|
|**Comprobación del tamaño del contenido**|El contenido del sitio parece ser mayor que el máximo permitido de 2 GB para una migración correcta.|Para que la migración se realice correctamente, el tamaño del contenido del sitio debe ser inferior a 2 GB. Evalúe si el sitio podría usar opciones de almacenamiento no basadas en el sistema de archivos para contenido estático, como Azure Storage.|
|**Se desconoce la comprobación de tamaño del contenido**|No se ha podido determinar el tamaño del contenido del archivo, lo que suele indicar un problema de acceso.|El contenido debe ser accesible para poder migrar el sitio. Confirme que el sitio no usa recursos compartidos de UNC para el contenido y que todas las ubicaciones de contenido del sitio son accesibles para el grupo Administradores.|
|**Comprobación del módulo global**|Se han detectado los siguientes módulos globales no admitidos: {0}.|App Service admite módulos globales limitados. Quite los módulos no admitidos de la sección GlobalModules junto con toda la configuración asociada.|
|**Comprobación de filtro ISAPI**|Se han detectado los siguientes filtros ISAPI no admitidos: {0}.|No se admite la configuración automática de filtros ISAPI personalizados. Quite los filtros ISAPI no admitidos.|
|**Se desconoce la comprobación de filtro de ISAPI**|No se pueden determinar los filtros ISAPI presentes para toda la configuración del sitio.|No se admite la configuración automática de filtros ISAPI personalizados. Corrija todos los errores de configuración y confirme que todas las ubicaciones de contenido del sitio son accesibles para el grupo de administradores.|
|**Comprobación de etiquetas de ubicación**|Se han encontrado las siguientes rutas de ubicación en el archivo applicationHost.config: {0}.|El método de migración no admite el traslado de la configuración de rutas de ubicación en applicationHost.config. Mueva la configuración de rutas de ubicación al archivo web.config raíz del sitio o a un archivo web.config asociado a la aplicación específica a la que se aplica.|
|**Comprobación del protocolo**|Se han encontrado enlaces con los siguientes protocolos no admitidos: {0}.|App Service solo admite los protocolos HTTP y HTTPS. Quite los enlaces con protocolos que no sean HTTP o HTTPS.|
|**Comprobación del directorio virtual**|Los siguientes directorios virtuales se hospedan en recursos compartidos de UNC: {0}.|La migración no permite migrar contenido del sitio hospedado en recursos compartidos de UNC. Mueva el contenido a una ruta de archivo local o considere la posibilidad de cambiar a una opción de almacenamiento no basada en el sistema de archivos, como Azure Storage. Si usa la configuración compartida, deshabilítela para el servidor antes de modificar las rutas de contenido.|
|**Comprobación de enlaces HTTPS**|La aplicación usa HTTPS.|Se necesitan más pasos manuales adicionales para la configuración de HTTPS en App Service. Se necesitan otros pasos posteriores a la migración para asociar certificados al sitio de App Service.|
|**Comprobación del puerto TCP.**|Se han encontrado enlaces en los siguientes puertos no admitidos: {0}.|App Service solo admite los puertos 80 y 443. Los clientes que realizan solicitudes al sitio deben actualizar el puerto en sus solicitudes para usar 80 o 443.|
|**Comprobación del marco**|Se ha detectado que este sitio puede estar usando los siguientes marcos que no son de .NET o versiones de marcos de .NET no admitidas: {0}.|La migración no valida el marco para sitios que no son de .NET. App Service admite varios marcos, pero estos tienen otras opciones de migración. Confirme que el sitio no use marcos que no son de .NET o considere la posibilidad de usar una opción de migración alternativa.|

## <a name="next-steps"></a>Pasos siguientes

[Crear](how-to-create-assessment.md) o [personalizar](how-to-modify-assessment.md) una evaluación.