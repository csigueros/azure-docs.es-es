---
title: Creación de asignaciones de licencia de SQL Server para Ventaja híbrida de Azure
description: En este artículo se explica cómo crear asignaciones de licencia de SQL Server para Ventaja híbrida de Azure.
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisri
ms.openlocfilehash: 48b68260e07d6e69e41daeacae631a9415f48a65
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547221"
---
# <a name="create-sql-server-license-assignments-for-azure-hybrid-benefit"></a>Creación de asignaciones de licencia de SQL Server para Ventaja híbrida de Azure

La nueva experiencia de Ventaja híbrida de Azure de Azure Portal permite las asignaciones de licencia de SQL Server en el nivel de cuenta o en un nivel de suscripción determinado. Cuando se crea la asignación en el nivel de cuenta, los descuentos de Ventaja híbrida de Azure se aplican automáticamente a los recursos de SQL de todas las suscripciones de la cuenta hasta el número de licencias especificado en la asignación.

En cada asignación de licencia se selecciona un ámbito y luego se asignan licencias al ámbito. Cada ámbito puede tener varias entradas de licencia.

## <a name="prerequisites"></a>Requisitos previos

Se deben cumplir los siguientes requisitos previos para crear asignaciones de licencia de SQL Server.

- La organización debe tener un tipo de contrato y una oferta admitidos.
- Debe ser miembro de un rol que tenga permisos para asignar licencias de SQL.
- La organización debe tener licencias principales de SQL Server con Software Assurance o licencias de suscripción principales disponibles para asignar a Azure.
- La organización debe estar inscrita en el registro automático de las máquinas virtuales de Azure SQL con la extensión IaaS. Para obtener más información, vea [Registro automático con la extensión Agente de IaaS de SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-automatic-registration-all-vms.md).
  > [!IMPORTANT]
  > El no cumplimiento de este requisito previo hace que Azure genere datos incompletos sobre el uso actual de Ventaja híbrida de Azure. Esta situación podría dar lugar a asignaciones de licencia incorrectas y traducirse en cargos innecesarios de pago por uso por licencias de SQL Server.

Los roles necesarios difieren en función del tipo de contrato.

| Tipo de acuerdo | Rol necesario | Ofertas admitidas |
| --- | --- | --- |
| Contrato Enterprise | _Administrador de empresa_<p> Para obtener más información sobre cómo convertirse en miembro del rol, vea [Incorporación de otro administrador de empresa](../manage/ea-portal-administration.md#add-a-department-administrator). | - MS-AZR-0017P (Microsoft Azure Enterprise)<br>- MS-AZR-USGOV-0017P (Azure Government Enterprise) |
| Contrato de cliente de Microsoft | *Propietario de la cuenta de facturación*<br> *Colaborador de la cuenta de facturación* <br> *Propietario del perfil de facturación*<br> *Colaborador del perfil de facturación*<br> Para obtener más información sobre cómo convertirse en miembro de los roles, vea [Administración de roles de facturación](../manage/understand-mca-roles.md#manage-billing-roles-in-the-azure-portal). | MS-AZR-0017G (Plan de Microsoft Azure)|
| WebDirect/pago por uso | No disponible | None |
| CSP/clientes dirigidos por asociados | No disponible | None |

> [!NOTE]
> La experiencia de licencia de nivel de ámbito no está disponible para suscripciones de crédito a MSDN, patrocinadas o suscripciones a MPN. El uso de software de SQL es gratuito para las suscripciones de desarrollo/pruebas (tipos de oferta MS-AZR-0148P o MS-AZR-0023P).

## <a name="create-a-sql-license-assignment"></a>Creación de una asignación de licencia de SQL

En el procedimiento siguiente, vaya de **Cost Management + Billing** a **Reservas + Ventaja híbrida**. No vaya a **Reservas** desde la página principal de Azure. Si lo hace, no tendrá el ámbito necesario para ver la experiencia de asignación de licencia. 

1. Inicie sesión en Azure Portal y vaya a **Cost Management + Billing**.  
    :::image type="content" source="./media/create-sql-license-assignments/select-cost-management.png" alt-text="Captura de pantalla que muestra la navegación de Azure Portal a Cost Management + Billing." lightbox="./media/create-sql-license-assignments/select-cost-management.png" :::
 2. Si tiene un Contrato Enterprise, seleccione un ámbito de facturación.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-scope.png" alt-text="Captura de pantalla que muestra la selección del ámbito de facturación de EA." lightbox="./media/create-sql-license-assignments/select-billing-scope.png" :::
 3. Si tiene un Contrato de cliente de Microsoft, seleccione un perfil de facturación.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-profile.png" alt-text="Captura de pantalla que muestra la selección del perfil de facturación." lightbox="./media/create-sql-license-assignments/select-billing-profile.png" :::
 4. En el menú izquierdo seleccione **Reservas + Ventaja híbrida**.  
    :::image type="content" source="./media/create-sql-license-assignments/select-reservations.png" alt-text="Captura de pantalla que muestra la selección de Reservas + Ventaja híbrida."  :::
 5. Seleccione **Agregar** y, en la lista, seleccione **Ventaja híbrida de Azure (versión preliminar)** .  
    :::image type="content" source="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" alt-text="Captura de pantalla que muestra la selección de Ventaja híbrida de Azure." lightbox="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" :::
 6. En la siguiente pantalla seleccione **Begin to assign licenses** (Comenzar a asignar licencias).  
    :::image type="content" source="./media/create-sql-license-assignments/get-started-centralized.png" alt-text="Captura de pantalla que muestra la selección de Ventaja híbrida de SQL." lightbox="./media/create-sql-license-assignments/get-started-centralized.png" :::
 7. Seleccione un ámbito y escriba el número de licencias que se va a usar para cada edición de SQL Server. Si no tiene licencias que asignar a una edición concreta, escriba cero.  
    > [!NOTE]
    > Es responsable de determinar que las entradas que realiza en la experiencia de licencia administrada de nivel de ámbito sean precisas y cumplan las obligaciones de licencia. Se muestra información de uso de licencias para ayudarle a realizar las asignaciones de licencia. Pero la información mostrada podría ser incompleta o inexacta debido a varios factores.
    >
    > Si el número de licencias que especifica es menor que el que está usando actualmente, se ve un mensaje de advertencia que indica que _ha especificado menos licencias de las que usa actualmente para Ventaja híbrida de Azure en este ámbito y la factura de este ámbito va a aumentar_.  
    
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition.png" alt-text="Captura de pantalla que muestra la selección del ámbito y el número de licencias." lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition.png" :::
 8. Opcionalmente, seleccione la pestaña **Detalles de uso** para ver el uso de Ventaja híbrida de Azure actual habilitado en el ámbito del recurso.  
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" alt-text="Captura de pantalla que muestra la pestaña Detalles de uso." lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" :::
 9. Seleccione **Agregar**.
10. Opcionalmente, cambie el nombre predeterminado de asignación de licencia. La fecha de revisión se establece automáticamente en un año y no se puede cambiar. Su finalidad es recordarle que revise periódicamente las asignaciones de licencia.  
    :::image type="content" source="./media/create-sql-license-assignments/license-assignment-commit.png" alt-text="Captura de pantalla que muestra el nombre de asignación de licencia predeterminado." lightbox="./media/create-sql-license-assignments/license-assignment-commit.png" :::
11. Después de revisar las selecciones, seleccione **Siguiente: Revisar y aplicar**.
12. Seleccione la opción de atestación **Al seleccionar &quot;Aplicar&quot;** para confirmar que tiene autoridad para aplicar Ventaja híbrida de Azure, suficientes licencias de SQL Server y que va a mantener las licencias mientras estén asignadas.  
    :::image type="content" source="./media/create-sql-license-assignments/confirm-apply-attestation.png" alt-text="Captura de pantalla que muestra la opción de atestación." lightbox="./media/create-sql-license-assignments/confirm-apply-attestation.png" :::
13. Seleccione **Aplicar** y luego **Sí**.
14. Se muestra la lista de licencias asignadas.  
    :::image type="content" source="./media/create-sql-license-assignments/assigned-licenses.png" alt-text="Captura de pantalla que muestra la lista de licencias asignadas." lightbox="./media/create-sql-license-assignments/assigned-licenses.png" ::: 

## <a name="track-assigned-license-use"></a>Seguimiento del uso de licencias asignadas

Vaya a **Cost Management + Billing** y seleccione **Reservas + Ventaja híbrida**.

Se muestra una lista de todas las reservas y asignaciones de licencia. Si quiere filtrar los resultados a solo las asignaciones de licencia, establezca un filtro para **Ventaja híbrida de SQL**.

:::image type="content" source="./media/create-sql-license-assignments/view-the-assignments.png" alt-text="Captura de pantalla que muestra la lista de reservas y licencias." lightbox="./media/create-sql-license-assignments/view-the-assignments.png" :::

En **Last Day Utilization** (Uso del último día) o **7-day Utilization** (Uso de 7 días), seleccione un porcentaje, que podría estar en blanco o ser cero, para ver el historial de uso de asignaciones en detalle.

:::image type="content" source="./media/create-sql-license-assignments/assignment-utilization-view.png" alt-text="Captura de pantalla que muestra los detalles de uso de asignaciones." lightbox="./media/create-sql-license-assignments/assignment-utilization-view.png" :::

Si el uso de una asignación de licencia es del 100 %, es probable que algunos recursos del ámbito incurran en cargos de pago por uso por SQL Server. Se recomienda usar de nuevo la experiencia de asignación de licencia para revisar cuánto uso cubren o no las licencias asignadas. Después, realice el mismo proceso que antes, incluida la consulta al departamento de adquisición o administración de recursos de software, la confirmación de que hay más licencias disponibles y la asignación de las licencias.

## <a name="changes-after-license-assignment"></a>Cambios tras la asignación de licencia

Después de crear asignaciones de licencia de SQL, la experiencia con Ventaja híbrida de Azure cambia en Azure Portal.

- Las elecciones existentes de Ventaja híbrida de Azure configuradas para recursos de SQL individuales ya no se aplican. Se reemplazan por la asignación de licencia de SQL creada en el nivel de suscripción o cuenta.
- La opción de ventaja híbrida no se muestra como en la configuración de recursos de SQL.
- Las aplicaciones o scripts que configuran la ventaja híbrida mediante programación siguen funcionando, pero el valor no tiene ningún efecto.
- Los descuentos de software de SQL se aplican a los recursos de SQL del ámbito. El ámbito se basa en el número de licencias de las asignaciones de licencia creadas para la suscripción de la cuenta donde se ha creado el recurso.
- Es posible que un recurso específico configurado para la ventaja híbrida no obtenga el descuento si otros recursos consumen todas las licencias. Pero se aplica el descuento máximo al ámbito, en función del número de recuentos de licencias. Para obtener más información sobre cómo se aplican los descuentos, vea [¿Qué es la administración del nivel de ámbito de Ventaja híbrida de Azure?](overview-azure-hybrid-benefit-scope.md)

## <a name="cancel-a-license-assignment"></a>Cancelación de una asignación de licencia

Revise la situación de la licencia antes de cancelar las asignaciones de licencia. Cuando se cancela una asignación de licencia, ya no se reciben descuentos por ellas. Por lo tanto, la factura de Azure puede aumentar. Si cancela la última asignación de licencia restante, la administración de Ventaja híbrida de Azure se revierte al nivel de recurso individual.

### <a name="to-cancel-a-license-assignment"></a>Para cancelar una asignación de licencia

1. En la lista de reservas y asignaciones de licencia seleccione una asignación de licencia.
1. En la página de detalles de la asignación de licencia seleccione **Cancelar**.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment-symbol.png" alt-text="Captura de pantalla que muestra la opción Cancelar." :::
1. En la página Cancelar revise la notificación y seleccione **Sí, cancelar**.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment.png" alt-text="Captura de pantalla que muestra la página Cancelar." lightbox="./media/create-sql-license-assignments/cancel-assignment.png" :::

## <a name="next-steps"></a>Pasos siguientes

- Vea [Preguntas frecuentes sobre la administración del nivel de ámbito de Ventaja híbrida de Azure](faq-azure-hybrid-benefit-scope.yml).
- Obtenga información sobre cómo se aplican los descuentos en [¿Qué es la administración del nivel de ámbito de Ventaja híbrida de Azure?](overview-azure-hybrid-benefit-scope.md)