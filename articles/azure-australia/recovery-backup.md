---
title: Copia de seguridad y recuperación ante desastres de Azure en Australia
description: Copia de seguridad y recuperación ante desastres en Microsoft Azure para las agencias de la administración pública de Australia en lo que se refiere a ASD Essential 8
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 22f6e970a0d5f73d975223b184979615aaa6d5ae
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2020
ms.locfileid: "117029012"
---
# <a name="backup-and-disaster-recovery-in-azure-australia"></a>Copia de seguridad y recuperación ante desastres de Azure en Australia

Tener planes de recuperación ante desastres y de copia de seguridad con la infraestructura de apoyo en contexto es fundamental para todas las organizaciones. La importancia de tener una solución de copia de seguridad se resalta por su inclusión en [Essential 8 del Australian Cyber Security Center](https://acsc.gov.au/publications/protect/essential-eight-explained.htm).

Microsoft Azure proporciona dos servicios que habilitan la resistencia: Azure Backup y Azure Site Recovery. Estos servicios le permiten proteger los datos tanto locales como en la nube en variados escenarios de diseño. Azure Backup y Azure Site Recovery usan un recurso de administración y almacenamiento común: Azure Recovery Services Vault. Este almacén se utiliza para administrar, supervisar y separar los datos de Azure Backup y Azure Site Recovery.

En este artículo se detallan los elementos de diseño clave para implementar Azure Backup y Azure Site Recovery en línea con los [controles del manual de seguridad de la información (ISM) de la Australian Signals Directorate (ASD)](https://acsc.gov.au/infosec/ism/index.htm).

## <a name="azure-backup"></a>Azure Backup

![Azure Backup](media/backup-overview.png)

Azure Backup se parece a una solución de copia de seguridad local tradicional y proporciona la capacidad de realizar copias de seguridad de los datos locales y los hospedados en Azure. Azure Backup se utiliza para realizar copias de seguridad de los siguientes tipos de datos en Azure:

* Archivos y carpetas
* Sistemas operativos Windows y Linux admitidos hospedados en:
  * Hipervisores de Hyper-V y VMWare
  * Hardware
* Aplicaciones de Microsoft admitidas

### <a name="azure-site-recovery"></a>Azure Site Recovery

![Azure Site Recovery](media/asr-overview.png)

Azure Site Recovery replica las cargas de trabajo que tienen una sola máquina virtual o aplicaciones de varios niveles. La replicación se admite desde el entorno local a Azure, entre regiones de Azure o entre ubicaciones locales organizadas por Azure Site Recovery. Las máquinas virtuales locales se pueden replicar en Azure o en un hipervisor local compatible. Una vez configurado, Azure Site Recovery organiza la replicación, la conmutación por error y la conmutación por recuperación.

## <a name="key-design-considerations"></a>Consideraciones clave sobre el diseño

Al implementar una solución de copia de seguridad o de recuperación ante desastres, la solución propuesta debe tener en cuenta lo siguiente:

* El ámbito y el volumen de datos que se van a capturar
* Cuánto tiempo se conservarán los datos
* Cómo se almacenan y administran de forma segura estos datos
* Las ubicaciones geográficas donde se almacenarán los datos
* Los procedimientos de prueba rutinarios del sistema

ISM proporciona orientación sobre las consideraciones de seguridad que se deben realizar al diseñar una solución. Microsoft Azure proporciona medios para abordar estas consideraciones de seguridad.

### <a name="data-sovereignty"></a>Soberanía de los datos

Las organizaciones deben asegurarse de que se mantenga la soberanía de los datos al utilizar ubicaciones de almacenamiento basadas en la nube. Azure Policy proporciona los medios para restringir las ubicaciones permitidas en las que se puede crear un recurso de Azure. La directiva "Ubicaciones permitidas" de Azure Policy integrada se usa para garantizar que los recursos de Azure creados en el ámbito de una directiva de Azure asignada solo se pueden crear en las ubicaciones geográficas designadas.

Los elementos de Azure Policy para la restricción geográfica de los recursos de Azure son:

* allowedLocations
* allowedSingleLocation

Estas directivas permiten a los administradores de Azure restringir la creación a una lista de ubicaciones designadas o incluso a una sola.

### <a name="redundant-and-geographically-dispersed-storage"></a>Almacenamiento redundante y geográficamente disperso

Los datos en Azure Recovery Services Vault siempre se guardan en un almacenamiento redundante. De forma predeterminada, Recovery Services Vault usa el almacenamiento con redundancia geográfica (GRS) de Azure. Los datos almacenados mediante GRS se replican en otros centros de datos de Azure en la [región secundaria emparejada](../best-practices-availability-paired-regions.md) de Recovery Services Vault. Los datos replicados se almacenan como de solo lectura y solo permiten la escritura si se produce un evento de conmutación por error de Azure. Dentro del centro de datos de Azure, los datos se replican entre dominios de error y dominios de actualización independientes para minimizar la posibilidad de que se produzca una interrupción por mantenimiento o en el hardware. GRS proporciona al menos una disponibilidad de al menos el 99,99999999999999 % anualmente.

Azure Recovery Services Vault puede configurarse para usar el almacenamiento con redundancia local (LRS). LRS es una opción de almacenamiento de menor costo con la desventaja de una disponibilidad reducida. Este modelo de redundancia emplea la misma replicación entre dominios de error y dominios de actualización independientes, pero no se replica entre regiones geográficas. Los datos que se encuentran en el almacenamiento LRS, aunque no son tan resistentes como GRS, proporcionan al menos un 99,999999999 % de disponibilidad anualmente.

A diferencia de las tecnologías de almacenamiento externo tradicionales, como los medios de cinta, las copias adicionales de los datos se crean automáticamente y no requieren ninguna sobrecarga administrativa adicional.

### <a name="restricted-access-and-activity-monitoring"></a>Supervisión de la actividad y del acceso restringido

Los datos de copia de seguridad deben estar protegidos de posibles daños, modificaciones y eliminaciones no autorizadas. Tanto Azure Backup como Azure Site Recovery usan el tejido de administración de Azure común. Este tejido proporciona auditorías detalladas, registros y control de acceso basado en rol de Azure (RBAC de Azure) a los recursos ubicados en Azure. Se puede restringir el acceso a los datos de la copia de seguridad para seleccionar personal administrativo y todas las acciones relacionadas con los datos de copia de seguridad se pueden registrar y auditar.

Tanto Azure Backup como Azure Site Recovery tienen características integradas de registro y creación de informes. Los problemas que se produzcan durante la copia de seguridad o la replicación se envían a los administradores mediante el tejido de administración de Azure.

Azure Recovery Services Vault también tiene las siguientes medidas de seguridad de datos adicionales:

* Los datos de la copia de seguridad se conservan durante 14 días después de que se haya producido una operación de eliminación
* Alertas y notificaciones de operaciones críticas como "Detener copia de seguridad y eliminar datos"
* Requisitos de PIN de seguridad para las operaciones críticas
* Se han implementado comprobaciones de tiempo de retención mínimo

Estas comprobaciones de tiempo de retención mínimo incluyen:

* Para la retención diaria, siete días como mínimo
* Para la retención semanal, cuatro semanas como mínimo
* Para la retención mensual, tres meses como mínimo
* Para la retención anual, un año como mínimo

Todos los datos de copia de seguridad almacenados en Azure se cifran en reposo mediante Storage Service Encryption (SSE) de Azure. Está habilitado para todas las cuentas de almacenamiento nuevas y existentes de forma predeterminada y no se puede deshabilitar. Los datos cifrados se descifran automáticamente durante la recuperación. De forma predeterminada, los datos cifrados mediante SSE se cifran mediante una clave proporcionada y administrada por Microsoft. Las organizaciones pueden elegir proporcionar y administrar su propia clave de cifrado para su uso con SSE. Esto proporciona un nivel de seguridad adicional opcional para los datos cifrados. Esta clave puede almacenarla el cliente en el entorno local o de forma segura en el almacén de claves de Azure.

### <a name="secure-data-transport"></a>Transporte de datos seguro

Azure Backup cifra los datos en tránsito mediante AES 256. Estos datos se protegen con una frase de contraseña creada por el personal administrativo cuando configuró la copia de seguridad por primera vez. Microsoft no tiene acceso a esta frase de contraseña, lo que significa que el cliente debe asegurarse de que esta se almacena de forma segura. La transferencia de datos tiene lugar entre el entorno local y Azure Recovery Services Vault mediante una conexión HTTPS segura.  Los datos de Recovery Services Vault se cifran en reposo con Azure SSE.

Los datos de Azure Site Recovery también se cifran siempre en tránsito. Todos los datos replicados se transportan de forma segura a Azure mediante HTTPS y TLS. Cuando un cliente de Azure se conecta a Azure mediante una conexión de ExpressRoute, los datos de Azure Site Recovery se envían mediante esta conexión privada.  Cuando un cliente de Azure se conecta a Azure mediante una conexión VPN, los datos se replican entre el entorno local y Recovery Services Vault de manera segura por internet.

Esta transferencia de datos en red segura elimina los requisitos de mitigación y el riesgo de seguridad al administrar las soluciones tradicionales de almacenamiento de copias de seguridad externas, como los medios de cinta.

### <a name="data-retention-periods"></a>Períodos de retención de datos

Se recomienda un período de retención de copia de seguridad mínimo de tres meses; sin embargo, normalmente se requieren períodos más prolongados. Azure Backup puede proporcionar hasta 9999 copias de una copia de seguridad. Si solo se realizó una copia de seguridad de Azure de una instancia protegida a diario, esto permitiría la retención de copias de seguridad diarias durante 27 años. Las copias de seguridad mensuales individuales de una instancia protegida permiten 833 años de retención. Como los datos de copia de seguridad están anticuados y se conservan copias de seguridad menos pormenorizadas a lo largo del tiempo, el período de retención total de los datos de copia de seguridad aumenta.  Azure no limita la cantidad de tiempo que los datos pueden permanecer en Azure Recovery Services Vault, solo el total de copias de seguridad por instancia. Tampoco hay diferencia de rendimiento entre la restauración de copias de seguridad antiguas o nuevas y cada restauración tarda la misma cantidad de tiempo en realizarse.

Azure Recovery Services Vault tiene varias directivas de retención y copia de seguridad predeterminadas.  El personal administrativo también puede crear directivas de retención y copia de seguridad personalizadas.

![Directiva de Azure Backup](media/create-policy.png)

Es necesario encontrar un equilibrio entre la frecuencia de copia de seguridad y los requisitos de retención a largo plazo al configurar las directivas de retención y de Azure Backup.

### <a name="backup-and-restore-testing"></a>Prueba de la copia de seguridad y la restauración

ISM recomienda probar los datos de copia de seguridad para asegurarse de que los datos protegidos son válidos cuando se requiera una restauración o una conmutación por error. Azure Backup y Azure Site Recovery también proporcionan la capacidad de probar los datos protegidos una vez que se ha realizado una copia de seguridad o una réplica. Los datos administrados por Azure Backup se pueden restaurar en una ubicación designada para poder validar la coherencia de los datos.

Azure Site Recovery tiene una funcionalidad incorporada para realizar pruebas de conmutación por error. Las cargas de trabajo replicadas en Recovery Services Vault se pueden restaurar en un entorno de Azure designado. El entorno de restauración de destino se puede aislar completamente de cualquier entorno de producción para garantizar que los sistemas de producción no resulten afectados durante las pruebas. Una vez completada la prueba, el entorno de prueba y todos los recursos se pueden eliminar inmediatamente para reducir los costos operativos.

Las pruebas de conmutación por error y la validación se pueden automatizar mediante el servicio Azure Automation integrado en la plataforma Azure. Esto permite programar las pruebas de conmutación por error para que se realicen automáticamente y así garantizar que los datos se replican correctamente en Azure.

## <a name="next-steps"></a>Pasos siguientes

Revise el artículo sobre [cómo garantizar la seguridad con Azure Policy](azure-policy.md).