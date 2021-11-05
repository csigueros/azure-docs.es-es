---
title: Introducción a la compatibilidad del nivel de acceso de archivo
description: Conozca más sobre la compatibilidad del nivel de acceso de archivo con Azure Backup.
ms.topic: overview
ms.date: 10/23/2021
ms.custom: references_regions
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 3c28d99c066bf71ea3970ce8a01eb68989e11123
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080904"
---
# <a name="about-archive-tier-support"></a>Compatibilidad del nivel de acceso de archivo

Los clientes confían en Azure Backup para almacenar los datos de copia de seguridad, incluidos los de retención a largo plazo (LTR), donde las necesidades de retención están definidas por las reglas de cumplimiento de la organización. En la mayoría de los casos, pocas veces se accede a los datos de copia de seguridad antiguos, que solo se almacenan para satisfacer las necesidades de cumplimiento.

Azure Backup admite la copia de seguridad de puntos de retención a largo plazo en el nivel de acceso de archivo, junto con instantáneas y el nivel Estándar.

## <a name="supported-workloads"></a>Cargas de trabajo compatibles

El nivel de acceso de archivo admite las cargas de trabajo siguientes:

| Cargas de trabajo | Operations |
| --- | --- |
| Azure Virtual Machines | <ul><li>Solo puntos de recuperación mensuales y anuales. No se admiten los puntos de recuperación diarios y semanales.  </li><li>Antigüedad >= 3 meses en el nivel estándar del almacén </li><li>Retención restante >= 6 meses </li><li>Sin dependencias diarias ni semanales activas. </li></ul> |
| SQL Server en máquinas virtuales de Azure o SAP HANA en máquinas virtuales de Azure | <ul><li>Solo puntos de recuperación completos. No se admiten registros ni diferenciales. </li><li>Antigüedad >= 45 días en el nivel Estándar del almacén. </li><li>Retención restante >= 6 meses </li><li>Ninguna dependencia </li></ul> |

>[!Note]
>- La compatibilidad del nivel de acceso de archivo con instancias de SQL Server en máquinas virtuales de Azure y SAP HANA en máquinas virtuales de Azure está disponible con carácter general en varias regiones. Para obtener una lista detallada de las regiones admitidas, consulte la [matriz de compatibilidad](#support-matrix).
>- La compatibilidad con el nivel de archivo en Azure Virtual Machines también está en versión preliminar pública limitada. Si quiere registrarse para obtener una versión preliminar pública limitada, use este [formulario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR463S33c54tEiJLEM6Enqb9UNU5CVTlLVFlGUkNXWVlMNlRPM1lJWUxLRy4u).

## <a name="supported-clients"></a>Clientes compatibles

El nivel de acceso de archivo admite los siguientes clientes:

- [PowerShell](/azure/backup/use-archive-tier-support?pivots=client-powershelltier)
- [CLI](/azure/backup/use-archive-tier-support?pivots=client-clitier)
- [Azure Portal](/azure/backup/use-archive-tier-support?pivots=client-portaltier)

## <a name="how-azure-backup-moves-recovery-points-to-the-vault-archive-tier"></a>¿Cómo mueve Azure Backup los puntos de recuperación al nivel de acceso de archivo de almacén?

> [!VIDEO https://www.youtube.com/embed/nQnH5mpiz60?start=416]

## <a name="archive-recommendations-only-for-azure-virtual-machines"></a>Recomendaciones de archivo (solo para máquinas virtuales de Azure)

Los puntos de recuperación de las máquinas virtuales de Azure son incrementales. Al mover puntos de recuperación al nivel de acceso de archivo, se convierten en puntos de recuperación completos (para tener la seguridad de que todos los puntos de recuperación del nivel de acceso de archivo sean independientes y estén aislados entre sí). Por lo tanto, el almacenamiento de copia de seguridad general (Vault-Standard + Vault-Archive) puede aumentar.

La cantidad de aumento de almacenamiento depende del patrón de renovación de las máquinas virtuales.

- Cuanto mayor sea la renovación, menor será el almacenamiento de copia de seguridad general cuando se mueve un punto de recuperación al nivel de acceso de archivo.
- Si la actividad en la máquina virtual es baja, el cambio al nivel de acceso de archivo puede provocar un aumento en el almacenamiento de copia de seguridad, lo que puede compensar la diferencia de precio entre el nivel de Vault-Standard y el nivel Vault-Archive. Por lo tanto, en esta situación, podría aumentar el costo general.

Para resolver este problema, Azure Backup proporciona un conjunto de recomendaciones. El conjunto de recomendaciones devuelve una lista de puntos de recuperación que, si se mueven juntos al nivel de acceso de archivo, se garantiza un ahorro en los costos.

>[!Note]
>El ahorro en los costos depende de varias razones y puede variar en cada caso.

## <a name="modify-protection"></a>Modificación de la protección

Azure Backup ofrece dos maneras de modificar la protección de un origen de datos:

- Modificación de una directiva existente
- Protección del origen de datos con una nueva directiva

En ambos casos, la nueva directiva se aplica a todos los puntos de recuperación más antiguos, que están en el nivel estándar y en el nivel de acceso de archivo. Por tanto, es posible que se eliminen los puntos de recuperación más antiguos si se produce un cambio en la directiva.

Cuando los puntos de recuperación se mueven al nivel de acceso de archivo, están sujetos a un período de eliminación temprana de 180 días. Los cargos se prorratean. Si se elimina un punto de recuperación que no se ha mantenido en el archivo durante 180 días, se incurrirá en un costo equivalente a 180 menos el número de días que haya estado en el nivel estándar.

Si elimina puntos de recuperación que no se hayan mantenido en archivo durante 180 días como mínimo, incurrirán en los costos de eliminación temprana.

## <a name="stop-protection-and-delete-data"></a>Detener la protección y eliminar los datos

Al detenerse la protección y eliminarse los datos, se eliminan todos los puntos de recuperación. En el caso de los puntos de recuperación archivados que no hayan permanecido durante 180 días en el nivel de acceso de archivo, la eliminación de los puntos de recuperación dará lugar a un costo de eliminación temprana.

## <a name="archive-tier-pricing"></a>Precios del nivel de acceso de archivo

Puede ver los precios del nivel de acceso de archivo en nuestra [página de precios](azure-backup-pricing.md).

## <a name="support-matrix"></a>Matrices compatibles

| Cargas de trabajo | Vista previa | Disponibilidad general |
| --- | --- | --- |
| SQL Server en máquinas virtuales de Azure o SAP HANA en máquinas virtuales de Azure | None | Este de Australia, Centro de la India, Norte de Europa, Sudeste de Asia, Este de Asia, Australia Sudeste, Centro de Canadá, Sur de Brasil, Este de Canadá, Centro de Francia, Sur de Francia, Este de Japón, Oeste de Japón, Centro de Corea, Sur de Corea, Sur de la India, Oeste de Reino Unido, Sur de Reino Unido, Centro de EE. UU., Este de EE. UU. 2, Oeste de EE. UU., Oeste de EE. UU. 2, Centro-oeste de EE. UU., Este de EE. UU., Centro-sur de EE. UU., Centro-norte de EE. UU., Europa occidental, US Gov Virginia, US Gov Texas, US Gov Arizona. |
| Azure Virtual Machines | Este de EE. UU., Este de EE. UU. 2, Centro-sur de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, Centro-oeste de EE. UU., Centro-norte de EE. UU., Sur de Brasil, Este de Canadá, Centro de Canadá, Oeste de Europa, Sur de Reino Unido, Oeste de Reino Unido, Este de Asia, Este de Japón, Sur de la India, Sudeste de Asia, Este de Australia, Centro de la India, Norte de Europa, Sudeste de Australia, Centro de Francia, Sur de Francia, Oeste de Japón, Centro de Corea del Sur, Sur de Corea del Sur. | Ninguno |


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>¿Qué ocurrirá en los puntos de recuperación de archivo si detengo la protección y conservo los datos?

El punto de recuperación permanecerá en el archivo para siempre. Para obtener más información, consulte [Impacto de detener la protección en los puntos de recuperación](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

### <a name="is-cross-region-restore-supported-from-archive-tier"></a>¿Se admite la restauración entre regiones desde el nivel de archivo?

Al trasladar los datos de almacenes GRS del nivel estándar al nivel de archivo, dichos datos se mueven al archivo GRS. Esto es así incluso cuando la restauración entre regiones está habilitada. Una vez que los datos de copia de seguridad se trasladan al nivel de acceso de archivo, no se pueden restaurar en la región asociada. Sin embargo, durante los errores de región, los datos de copia de seguridad de la región secundaria estarán disponibles para la restauración. 

Al restaurar desde un punto de recuperación en el nivel de archivo de la región primaria, el punto de recuperación se copia en el nivel estándar y se conserva según la duración de la rehidratación, tanto en la región primaria como en la secundaria. Puede realizar la restauración entre regiones desde estos puntos de recuperación rehidratados.

### <a name="i-can-see-eligible-recovery-points-for-my-virtual-machine-but-i-cant-seeing-any-recommendation-what-can-be-the-reason"></a>Puedo ver puntos de recuperación aptos para mi máquina virtual, pero no veo ninguna recomendación. ¿Cuál puede ser el motivo?

Los puntos de recuperación de las máquinas virtuales cumplen los criterios de idoneidad. Por lo tanto, hay puntos de recuperación archivables. Sin embargo, la actividad en la máquina virtual puede ser baja, por lo que no hay recomendaciones. En este escenario, aunque puede mover los puntos de recuperación archivables al nivel de acceso de archivo, pueden aumentar los costos generales de almacenamiento de copia de seguridad.

### <a name="i-have-stopped-protection-and-retained-data-for-my-workload-can-i-move-the-recovery-points-to-archive-tier"></a>He detenido la protección y he conservado los datos de mi carga de trabajo. ¿Puedo mover los puntos de recuperación al nivel de acceso de archivo?

No. Una vez detenida la protección para una carga de trabajo determinada, los puntos de recuperación correspondientes no se pueden mover al nivel de acceso de archivo. Para hacerlo, debe reanudar la protección en el origen de datos.

## <a name="next-steps"></a>Pasos siguientes

- [Uso del nivel de acceso de archivo](use-archive-tier-support.md)
- [Precios de Azure Backup](azure-backup-pricing.md)
