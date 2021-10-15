---
title: Transición de la administración del nivel de ámbito de la Ventaja híbrida de Azure
description: En este artículo se describen los cambios y varios escenarios de transición para ilustrar la transición a la administración del nivel de ámbito de la Ventaja híbrida de Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.openlocfilehash: a813a8934ff66b10e0f3c7adce65887acebba6f8
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547224"
---
# <a name="transition-to-scope-level-management-of-azure-hybrid-benefit"></a>Transición de la administración del nivel de ámbito de la Ventaja híbrida de Azure

Al realizar la transición a la administración del nivel de ámbito de la Ventaja híbrida de Azure, se elimina la necesidad de configurar la ventaja en el nivel de recurso. En este artículo se describen los cambios y varios escenarios de transición para ilustrar el resultado. Para comprender mejor cómo la nueva experiencia de administración de licencias del nivel de ámbito aplica licencias y descuentos a los recursos, vea [¿Qué es la administración del nivel de ámbito de Ventaja híbrida de Azure?](overview-azure-hybrid-benefit-scope.md)

## <a name="changes-to-individual-resource-configuration"></a>Cambios en la configuración de los recursos individuales

Al asignar licencias a una suscripción mediante la nueva experiencia, los cambios se muestran en Azure Portal. Después, no podrá administrar la ventaja en el nivel de recurso. Los cambios que realice en un nivel de ámbito invalidan la configuración en el nivel de recurso individual.

:::image type="content" source="./media/transition-existing/sql-db-configure.png" alt-text="Captura de pantalla que muestra la información sobre la invalidación de licencias de SQL" lightbox="./media/transition-existing/sql-db-configure.png" :::.

> [!NOTE]
> Si cambia la configuración de la Ventaja híbrida de Azure mediante PowerShell, una API o fuera de Azure Portal en un recurso, se guarda el cambio de configuración. Sin embargo, no tendrá ningún efecto mientras la suscripción o la cuenta de facturación de ese recurso esté cubierta por licencias asignadas a un ámbito. Si alguna vez opta por no participar en la administración del nivel de ámbito de la Ventaja híbrida de Azure quitando todas las asignaciones de licencias, los descuentos de licencia vuelven a determinarse según la configuración de la Ventaja híbrida de Azure en cada recurso.

## <a name="check-how-many-sql-licenses-you-use-before-transition"></a>Comprobación de cuántas licencias de SQL se usan antes de la transición

Al inscribirse en la administración del nivel de ámbito de la experiencia Ventaja híbrida de Azure, verá el uso de la Ventaja híbrida de Azure actual que está habilitado para recursos individuales. Para obtener más información sobre la experiencia global, vea [Creación de asignaciones de licencia de SQL Server para Ventaja híbrida de Azure](create-sql-license-assignments.md). Si es colaborador de la suscripción y no tiene el rol de administrador de facturación necesario, puede analizar el uso de diferentes tipos de licencias de SQL Server en Azure mediante un script de PowerShell. El script genera una instantánea del uso en varias suscripciones o en toda la cuenta. Para obtener detalles y ejemplos del uso del script, consulte el [script de PowerShell sql-license-usage](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit) de ejemplo. Una vez que haya ejecutado el script, identifique al administrador de facturación y hable con él sobre la oportunidad de cambiar la administración de la Ventaja híbrida de Azure al nivel de ámbito de la suscripción o la cuenta de facturación.

> [!NOTE]
> El script incluye compatibilidad con licencias principales normalizadas (NCL). 

## <a name="hadr-benefit-for-sql-server-vms"></a>Ventaja de alta disponibilidad y recuperación ante desastres para VM con SQL Server

La nueva experiencia de Azure Portal es totalmente compatible con la ventaja de alta disponibilidad y recuperación ante desastres (HADR) para VM con SQL Server. Si la VM con SQL Server está configurada como una réplica de HADR, no se requiere ninguna otra acción. Para más información sobre cómo funciona la ventaja de HADR para VM con SQL Server, vea [Coexistencia de SQL Server HADR y la Ventaja híbrida de Azure de nivel de ámbito](sql-server-hadr-licenses.md).

## <a name="transition-scenario-examples"></a>Ejemplos de escenarios de transición

Revise los siguientes ejemplos de escenarios de transición que más se ajusten a su situación.

### <a name="migrate-sql-workloads-from-on-premises-to-the-cloud-using-sql-database-with-azure-hybrid-benefit"></a>Migración de las cargas de trabajo de SQL del entorno local a la nube con SQL Database mediante la Ventaja híbrida de Azure

- **Cargas de trabajo locales de SQL Server**: dos máquinas de 16 núcleos que hospedan bases de datos críticas de SQL Server Enterprise Edition se migrarán a la nube.
- **Destino de Azure para migrar cargas de trabajo**: después del análisis, se decide que las cargas de trabajo se ejecutarán en dos instancias de Azure SQL Managed Instance de 16 núcleos.
- **Licencias que se asignarán a Azure**: teniendo en cuenta los puntos anteriores, se asignarán a Azure licencias de 32 núcleos de SQL Server Enterprise Edition con Software Assurance.
- **Acción recomendada**: use la nueva experiencia de Azure Portal para asignar licencias de 32 núcleos de SQL Server Enterprise Edition a la suscripción apropiada o a la cuenta de facturación general.
- **Resultado**: migración fácil de administrar y optimizada para costos de cargas de trabajo de SQL Server desde el entorno local a la nube.

> [!NOTE] 
> La Ventaja híbrida de Azure permite seguir utilizando también las licencias asignadas a Azure en el entorno local durante un máximo de 180 días, a medida que las cargas de trabajo se migran, prueban e implementan.

### <a name="simplify-license-management-by-transitioning-to-scope-level-management-of-azure-hybrid-benefit"></a>Simplificación de la administración de licencias mediante la transición a la administración del nivel de ámbito de la Ventaja híbrida de Azure

- **Recursos de SQL Server en ejecución**: se está ejecutando una licencia de 64 núcleos de Azure SQL Database crítico para la empresa, con la Ventaja híbrida de Azure seleccionada.
- **Licencias disponibles para asignar a Azure**: el equipo de adquisiciones confirma que hay más de 64 licencias principales de SQL Server Enterprise Edition con Software Assurance que no están en uso en el entorno local.
- **Acción recomendada**: use la nueva administración del nivel de ámbito de la Ventaja híbrida de Azure para asignar 64 licencias principales de SQL Server Enterprise Edition a Azure. O bien, si se espera que el uso aumente pronto, asigne más licencias según sea necesario.
- **Resultado**: la transición cubrirá el costo de software de SQL Server, por lo que no debería haber ningún cambio en la facturación asociada.

### <a name="save-more-by-assigning-more-sql-server-licenses-to-cover-more-azure-sql-resources"></a>Más ahorro de dinero mediante la asignación de más licencias de SQL Server para cubrir más recursos de Azure SQL

- **Recursos de SQL Server en ejecución**: se están ejecutando dos licencias de 16 núcleos de Azure SQL Database crítico para la empresa, con la Ventaja híbrida de Azure seleccionada solo en una de ellas.
- **Licencias disponibles para asignar a Azure**: según su equipo de adquisiciones, hay 48 licencias principales de SQL Server Enterprise Edition con Software Assurance que no se están utilizando en el entorno local o en Azure.
- **Acción recomendada**: use la administración del nivel de ámbito de la experiencia de la Ventaja híbrida de Azure para asignar las 48 licencias principales de SQL Server Enterprise Edition. Esto significa un aumento de 16 en comparación con la selección de la Ventaja híbrida de Azure a nivel de recurso.
- **Resultado**: dado que se usan más licencias adquiridas fuera de Azure, se deben reducir los importes facturados en Azure. Además, dado que el costo por año de Software Assurance es menor que el costo por año de SQL Server con cargos de pago por uso, se reduce el costo total por el uso de SQL Server.

### <a name="restore-compliance-when-excessive-azure-hybrid-benefit-usage-is-found"></a>Restauración del cumplimiento cuando se detecta un uso excesivo de la Ventaja híbrida de Azure

- **Recursos de SQL Server en ejecución**: se están ejecutando tres licencias de 8 núcleos de la base de datos SQL de uso general y una instancia de 16 núcleos de VM con SQL Server Enterprise, con la Ventaja híbrida de Azure seleccionada en todo. El número de licencias de la Ventaja híbrida de Azure necesarias para la cobertura, es decir, 24 núcleos de Standard Edition más 16 núcleos de SQL Server Enterprise. O bien, 88 de SQL Server Standard Edition (+ 0 de SQL Server Enterprise Edition), o 22 de SQL Server Enterprise (+ 0 de SQL Server Standard Edition) también podrían ofrecer la cobertura. Esto se debe a que una licencia principal de SQL Server Enterprise Edition y cuatro licencias principales de SQL Server Standard Edition pueden cubrir el mismo uso de la Ventaja híbrida de Azure en todos los tipos de recursos de Azure SQL. Revise las reglas de la Ventaja híbrida de Azure en el artículo [¿Qué es la administración del nivel de ámbito de Ventaja híbrida de Azure?](overview-azure-hybrid-benefit-scope.md) para obtener más información sobre esta flexibilidad.
- **Licencias disponibles para asignar a Azure**: según su equipo de adquisiciones, hay 64 licencias principales de SQL Server Standard Edition con Software Assurance que no se están utilizando en el entorno local o en Azure. Esto es menor que la cantidad necesaria de 22 licencias principales de SQL Server Enterprise o 88 licencias principales de SQL Server Standard Edition.
- **Acción recomendada**: para restaurar el cumplimiento, identifique 6 licencias principales de SQL Server Enterprise o 24 licencias principales de SQL Server Standard Edition con Software Assurance y asigne estas licencias y las 64 licencias principales de SQL Server Standard ya confirmadas a Azure mediante la administración del nivel de ámbito de la experiencia de la Ventaja híbrida de Azure.
- **Resultado**: el incumplimiento se elimina y la Ventaja híbrida de Azure se usa de forma óptima para minimizar los costos.
- **Acción alternativa**: asigne a Azure solo las 64 licencias principales de SQL Server Standard Edition. Se garantiza el cumplimiento, pero como esas licencias son insuficientes para cubrir todo el uso de Azure SQL, experimentará algunos cargos de pago por uso.
## <a name="next-steps"></a>Pasos siguientes

- Consulte el tutorial [Administración y optimización de la Ventaja híbrida de Azure para SQL Server](tutorial-azure-hybrid-benefits-sql.md).
- Pase a la administración de licencias del nivel de ámbito mediante la [creación de asignaciones de licencias de SQL Server](create-sql-license-assignments.md).
- Vea [Preguntas frecuentes sobre la administración del nivel de ámbito de la Ventaja híbrida de Azure](faq-azure-hybrid-benefit-scope.yml).