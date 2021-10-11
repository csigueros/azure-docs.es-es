---
title: ¿Qué es la administración del nivel de ámbito de Ventaja híbrida de Azure?
description: Ventaja híbrida de Azure es una ventaja de licencia que le permite traer sus licencias de Windows Server y SQL Server basadas en núcleos locales con Software Assurance (o suscripción) activo a Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 795c8c3509a25abc8244150f129eb9dfbf997557
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547445"
---
# <a name="what-is-scope-level-management-of-azure-hybrid-benefit"></a>¿Qué es la administración del nivel de ámbito de Ventaja híbrida de Azure?

La Ventaja híbrida de Azure es una ventaja de licencia que ayuda a reducir considerablemente los costos que conlleva la ejecución de cargas de trabajo en la nube. Permite usar licencias locales de Windows Server y SQL Server habilitadas para Software Assurance en Azure. Para más información, consulte [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

Ventaja híbrida de Azure para SQL Server se puede administrar en el ámbito de una toda una suscripción de Azure o de una cuenta de facturación general. A alto nivel, así es cómo funciona:

1. En primer lugar, confirme que tanto usted como Azure pueden ver todas las máquinas virtuales con SQL Server habilitando el registro automático de las imágenes de SQL Server autoinstaladas con la extensión IaaS. Para más información, consulte [Registro de varias máquinas virtuales con SQL en Azure con la extensión Agente de IaaS de SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md).
1. En **Cost Management + Billing** en Azure Portal, usted (el administrador de facturación) elige el ámbito y el número de licencias aptas que se desea asignar para cubrir los recursos del ámbito.  
    :::image type="content" source="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" alt-text="Captura de pantalla que muestra cómo establecer un ámbito y asignar licencias." lightbox="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" :::

- Cada hora, cuando se ejecutan los recursos del ámbito, Azure les asigna automáticamente las licencias y descuenta los costos correctamente. Cada hora se pueden cubrir distintos recursos.
- Cualquier uso por encima del número de licencias asignadas se factura a los precios normales de pago por uso.
- En el momento en que decida administrar la ventaja mediante la asignación de licencias en un nivel de ámbito, dejará de poder administrar recursos individuales en el ámbito.

La manera original de habilitar Ventaja híbrida de Azure a nivel de recurso aún está disponible para SQL Server y actualmente es la única opción para Windows Server. Implica que un rol DevOps selecciona la ventaja para cada recurso individual (como una máquina virtual con SQL Database o Windows Server) al crearlo o administrarlo. Si lo hace, se descuenta el costo por hora de ese recurso. Para obtener más información, vea [Ventaja para uso híbrido de Azure para Windows Server](../../azure-sql/azure-hybrid-benefit.md).

La habilitación de Ventaja híbrida de Azure para SQL Server en un nivel de ámbito de cuenta o de suscripción se encuentra actualmente en versión preliminar. Solo está disponible para clientes empresariales. Estamos trabajando en ampliar la funcionalidad no solo a Windows Server, sino a más clientes.

## <a name="qualifying-sql-server-licenses"></a>Licencias de SQL Server aptas

Las licencias básicas Enterprise y Estándar de SQL Server con Software Assurance activo pueden optar a esta ventaja. Además, las suscripciones a las licencias básicas de SQL Server.

## <a name="qualifying-azure-resources"></a>Recursos de Azure aptos

La administración de Ventaja híbrida de Azure a nivel de ámbito abarca los siguientes recursos comunes de Azure SQL:

- Instancias de SQL Database
- Instancias administradas de SQL
- Grupos elásticos de SQL
- SQL Server en máquinas virtuales de Azure

La administración de Ventaja híbrida de Azure a nivel de recurso también puede abarcar todo lo anterior. Actualmente es la única opción para abarcar los siguientes recursos:

- Hosts dedicados de Azure
- SQL Server Integration Services (SSIS) de Azure Data Factory

## <a name="scope-level-management-advantages"></a>Ventajas de la administración a nivel de ámbito

Estas son las principales ventajas:

- **Un enfoque más sencillo y con mayor control**: el administrador de facturación asigna directamente las licencias disponibles a uno o varios ámbitos de Azure. El enfoque original, a gran escala, implica coordinar el uso de Ventaja híbrida de Azure entre muchos recursos y propietarios de DevOps.
- **Una forma fácil de usar para optimizar costos**: un administrador puede supervisar el uso de Ventaja híbrida de Azure y ajustar directamente las licencias asignadas a Azure. Por ejemplo, un administrador podría ver la oportunidad de ahorrar más dinero mediante la asignación de más licencias a Azure. Luego, habla con su departamento de adquisiciones para confirmar la disponibilidad de las licencias. Por último, pueden asignar fácilmente las licencias a Azure y empezar a ahorrar.
- **Un método mejor para administrar los costos durante los picos de uso**: puede escalar verticalmente fácilmente el mismo recurso o agregar más recursos durante los picos temporales. No necesita asignar más licencias de SQL Server (por ejemplo, períodos de cierre o aumento de las compras de vacaciones). En el caso de los picos de cargas de trabajo de corta duración, los cargos de pago por uso por la capacidad adicional pueden suponer un costo menos que adquirir más licencias para usar Ventaja híbrida de Azure para la capacidad. La administración de la ventaja en un nivel ámbito, en lugar de en un nivel de recurso, le ayuda a decidir en función del uso agregado.
- **Separación clara de las obligaciones para mantener el cumplimiento**: en el modelo Ventaja híbrida de Azure de nivel de recurso, los propietarios de los recursos pueden tomar decisiones de selección de Ventaja híbrida de Azure sin reconocimiento de disponibilidad de licencias (a través de la adquisición) o el uso general de licencias de SQL (incluido el entorno local). La administración de nivel de ámbito de Ventaja híbrida de Azure elimina el riesgo de que los propietarios de recursos seleccionen Ventaja híbrida de Azure cuando no haya licencias disponibles. Los administradores de facturación que administran Ventaja híbrida de Azure centralmente se posicionan para confirmar con los departamentos de adquisiciones y administración de recursos de software el número de licencias que hay disponibles para asignar a Azure. Este punto se ilustra en el diagrama siguiente.

:::image type="content" source="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg" alt-text="Diagrama que muestra la separación de obligaciones." border="false" lightbox="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg":::

## <a name="how-licenses-apply-to-azure-resources"></a>Aplicación de licencias a los recursos de Azure

Tanto la licencia de SQL Server Enterprise (básica) como la de SQL Server Enterprise (básica) con Software Assurance son válidas, pero, como se describe en los [términos y condiciones de los productos de Microsoft](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS), se aplican diferentes relaciones de conversión al traerlas a Azure con Ventaja híbrida de Azure.

Una regla que se debe conocer: una licencia de SQL Server Enterprise Edition tiene la misma cobertura que _cuatro_ licencias de SQL Server Standard Edition, en todos los tipos de recursos de Azure SQL aptos.

Para explicar mejor cómo funciona, se usa el término _licencia básica normalizada_, o NCL. En alineación con la regla anterior, una licencia básica de SQL Server Standard genera una NCL. Una licencia básica de SQL Server Enterprise genera cuatro NCL. Por ejemplo, si asigna cuatro licencias básicas de SQL Server Enterprise y siete licencias básicas de SQL Server Standard, la cobertura total y la potencia de descuento de Ventaja híbrida de Azure equivale a 23 NCL (4\*4+7\*1).

En la tabla siguiente se resume el número de NCL necesarias para descontar por completo el costo de la licencia de SQL Server para los distintos tipos de recursos. La administración de nivel de ámbito Ventaja híbrida de Azure aplica estrictamente las reglas de los términos y condiciones del producto, que se resumen a continuación.

| **Servicio de datos de Azure** | **Nivel de servicio** | **Número necesario de NCL** |
| --- | --- | --- |
| SQL Managed Instance o grupo de instancias | Crítico para la empresa | 4 por núcleo virtual |
| SQL Managed Instance o grupo de instancias | Uso general | 1 por núcleo virtual |
| SQL Database o grupo de bases de datos elásticas<sup>1</sup> | Crítico para la empresa | 4 por núcleo virtual |
| SQL Database o grupo de bases de datos elásticas<sup>1</sup> | Uso general | 1 por núcleo virtual |
| SQL Database o grupo de bases de datos elásticas<sup>1</sup> | Hiperescala | 1 por núcleo virtual |
| SQL Server Integration Services de Azure Data Factory | Enterprise | 4 por núcleo virtual |
| SQL Server Integration Services de Azure Data Factory | Estándar | 1 por núcleo virtual |
| Máquinas virtuales de SQL Server<sup>2</sup> | Enterprise | 4 por CPU virtual |
| Máquinas virtuales de SQL Server<sup>2</sup> | Estándar | 1 por CPU virtual |

<sup>1</sup> *Ventaja híbrida de Azure no está disponible en el nivel de proceso sin servidor de Azure SQL Database.*

<sup>2</sup> *Sujeto a un mínimo de cuatro licencias de núcleo virtual por máquina virtual.*

## <a name="ongoing-scope-level-management"></a>Administración de nivel de ámbito en curso

Este es nuestro ritmo periódico recomendado de administración de Ventaja híbrida de Azure en un nivel de ámbito, que puede funcionar en su caso:

- Interactúe con su organización para saber cuántos recursos y núcleos virtuales de Azure SQL se usarán durante el próximo mes, trimestre o año.
- Trabaje con los departamentos de administración de recursos de software y de adquisiciones para determinar si hay suficientes licencias básicas de SQL con Software Assurance disponibles. La ventaja permite que las licencias que admiten la migración de cargas de trabajo que se van a usar tanto en el entorno local como en Azure durante un máximo de 180 días. Por tanto, esas licencias se pueden contar como disponibles.
- Asigne las licencias disponibles para cubrir el uso actual _y_ el uso planeado durante el próximo período.
- Supervise el uso de licencias asignadas.
  - Si se aproxima al 100 %, consulte a otros usuarios de su organización para conocer el crecimiento de uso esperado. Si es necesario, confirme la disponibilidad de las licencias y, después, asigne más licencias al ámbito.
  - Si el uso es del 100 %, es posible que el número de recursos que usa supere el número de licencias asignadas. Vuelva al artículo sobre [creación de asignaciones de licencias](create-sql-license-assignments.md) y examine el uso que muestra Azure. Revise también los detalles de uso de la licencia. Luego, hable con sus compañeros para confirmar u obtener licencias antes de asignarlas al ámbito para obtener más cobertura.
- Repita el proceso proactivo periódicamente.

## <a name="next-steps"></a>Pasos siguientes

- Consulte el tutorial [Administración y optimización de Ventaja híbrida de Azure para SQL Server](tutorial-azure-hybrid-benefits-sql.md).
- Aprenda a [realizar la transición a la administración de nivel de ámbito de Ventaja híbrida de Azure](transition-existing.md).
- Consulte [Preguntas frecuentes sobre la administración del nivel de ámbito de Ventaja híbrida de Azure](faq-azure-hybrid-benefit-scope.yml).