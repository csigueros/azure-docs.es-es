---
title: Coexistencia de HADR de SQL Server y Ventaja híbrida de Azure administrada centralmente
description: En este artículo se explica cómo coexisten la ventaja de Software Assurance de HADR de SQL Server y Ventaja híbrida de Azure administrada centralmente.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: f475fa19c06eb2ecc8ed863db06f5a647308ee00
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229434"
---
# <a name="sql-server-hadr-and-centrally-managed-azure-hybrid-benefit-coexistence"></a>Coexistencia de HADR de SQL Server y Ventaja híbrida de Azure administrada centralmente

Una de las ventajas de Software Assurance (SA) es que permite a los clientes de Azure instalar y ejecutar instancias pasivas de SQL Server para la recuperación ante desastres en previsión de un evento de conmutación por error. La administración de nivel de ámbito de la Ventaja híbrida de Azure admite la ventaja de HADR de SQL Server al garantizar que las réplicas de recuperación ante desastres calificadas no consuman las licencias de SQL Server asignadas. Como resultado, no tiene que administrar las réplicas por separado. 

## <a name="hadr-benefit-selection"></a>Selección de la ventaja HADR

Para usar la ventaja HADR, seleccione **HADR** mediante el panel **Configurar** del recurso **SQL Virtual Machine**, tal como se muestra en la siguiente imagen.

:::image type="content" source="./media/sql-server-hadr-licenses/select-hadr-benefit.png" alt-text="Captura de pantalla que muestra la opción HADR en la configuración de la máquina virtual de SQL." lightbox="./media/sql-server-hadr-licenses/select-hadr-benefit.png" :::

Si se selecciona la propiedad HADR, las licencias asignadas centralmente a un ámbito no se usan para descontar el costo del software SQL de ese recurso. En cambio, el medidor de pago por uso cambia automáticamente a un medidor HADR de 0 USD. Este método garantiza que las licencias de SQL asignadas solo se utilicen para los recursos que requieren licencia. No es necesario aumentar las asignaciones de licencias de SQL para notar los beneficios de HADR. En el siguiente diagrama, se ilustra este concepto.

Los precios que se muestran en la siguiente imagen son solo a modo de ejemplo.

:::image type="content" source="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg" alt-text="Diagrama que muestra el consumo total de núcleos virtuales con descuento con HADR seleccionado." border="false" lightbox="./media/sql-server-hadr-licenses/fully-discounted-consumption-hadr.svg":::

> [!NOTE]
> La opción HADR refleja el rol específico de esta instancia de SQL Server en el grupo de disponibilidad AlwaysOn. Seleccionarlo es responsabilidad del propietario del servicio o DBA, y requiere al menos un rol de [Colaborador de SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor). Esta tarea no está relacionada con las asignaciones de licencias de nivel de ámbito.

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Preguntas frecuentes sobre la administración centralizada de Ventaja híbrida de Azure](faq-azure-hybrid-benefit-scope.yml).
- Descubra cómo se aplican los descuentos en [¿Qué es la administración centralizada de Ventaja híbrida de Azure?](sql-server-hadr-licenses.md)
- Obtenga información sobre cómo realizar la [transición desde la experiencia de la Ventaja híbrida de Azure existente](transition-existing.md).