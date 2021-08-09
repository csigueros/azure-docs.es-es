---
title: Administración de Protección contra DDoS de Azure estándar mediante Azure Portal
description: Aprenda a usar Azure DDoS Protection Estándar para mitigar un ataque.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: ae33d1695188e103c7c56374a5f39e8fc0d27430
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110061415"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Inicio rápido: Creación y configuración de Azure DDoS Protection Estándar

Introducción a Azure DDoS Protection Estándar mediante Azure Portal. 

Un plan de protección contra DDoS define un conjunto de redes virtuales que tiene habilitada la protección contra DDoS estándar en distintas suscripciones. Puede configurar un plan de protección contra DDoS para la organización y vincular redes virtuales de distintas suscripciones al mismo plan. 

En este inicio rápido, creará un plan de protección contra DDoS y lo vinculará a una red virtual. 

## <a name="prerequisites"></a>Prerrequisitos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Inicie sesión en Azure Portal en https://portal.azure.com. Asegúrese de que la cuenta está asignada al rol de [colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [rol personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenga asignadas las acciones adecuadas que se muestran en la guía paso a paso en [Permisos](manage-permissions.md).

## <a name="create-a-ddos-protection-plan"></a>Creación de un plan de protección contra DDoS

1. Seleccione **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Busque el término *DDoS*. Seleccione **Plan de protección contra DDoS** cuando aparezca en los resultados de la búsqueda.
3. Seleccione **Crear**.
4. Especifique o seleccione los siguientes valores y, a continuación, seleccione **Crear**:

    |Configuración        |Valor                                              |
    |---------      |---------                                          |
    |Nombre           | Escriba _MyDdosProtectionPlan_.                     |
    |Subscription   | Seleccione su suscripción.                         |
    |Resource group | Seleccione **Crear nuevo** y escriba _MyResourceGroup_.|
    |Location       | Escriba _Este de EE. UU._                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Habilitación de la protección contra DDoS en una red virtual

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Habilitación de la protección contra DDoS para una red virtual nueva

1. Seleccione **Crear un recurso** en la esquina superior izquierda de Azure Portal.
2. Seleccione **Redes** y **Red virtual**.
3. Escriba o seleccione los siguientes valores, acepte los valores predeterminados restantes y seleccione **Crear**:

    | Configuración         | Valor                                           |
    | ---------       | ---------                                       |
    | Nombre            | Escriba _MyVnet_.                                 |
    | Subscription    | Seleccione su suscripción.                                    |
    | Resource group  | Seleccione **Usar existente** y después seleccione **MyResourceGroup**. |
    | Location        | Escriba _Este de EE. UU._                                                    |
    | DDoS Protection Standard | Seleccione **Habilitar**. El plan que selecciona puede estar en la misma suscripción que la red virtual, o una suscripción distinta, pero ambas suscripciones deben estar asociadas al mismo inquilino de Azure Active Directory.|

No puede mover una red virtual a otro grupo de recursos ni a otra suscripción si DDoS Standard está habilitado para la red virtual. Si tiene que mover una red virtual que tenga habilitado DDoS Standard, primero deshabilítelo, mueva la red virtual y, luego, habilite DDoS Standard. Después de eso, se restablecen los umbrales de directiva ajustados automáticamente para todas las direcciones IP públicas protegidas en la red virtual.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Habilitación de la protección contra DDoS para una red virtual existente

1. Cree un plan de protección contra DDoS con los pasos descritos en [Creación de un plan de protección contra DDoS](#create-a-ddos-protection-plan), si no tiene un plan de protección de DDoS existente.
2. Escriba el nombre de la red virtual en la que quiere habilitar DDoS Protection Standard en el cuadro **Buscar recursos, servicios y documentos** de la parte superior de Azure Portal. Seleccione el nombre de la red virtual cuando aparezca en los resultados de la búsqueda.
3. Seleccione **Protección DDoS** en **CONFIGURACIÓN**.
4. Seleccione **Estándar**. En **Plan de protección contra DDoS**, seleccione un plan de protección contra DDoS existente o el plan que creó en el paso 1 y, luego, seleccione **Guardar**. El plan que selecciona puede estar en la misma suscripción que la red virtual, o una suscripción distinta, pero ambas suscripciones deben estar asociadas al mismo inquilino de Azure Active Directory.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>Habilitación de la protección contra DDoS en todas las redes virtuales

Esta [directiva integrada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d) detectará cualquier red virtual de un ámbito definido que no tenga DDoS Protection Estándar habilitado y, después, creará opcionalmente una tarea de corrección que creará a su vez la asociación para proteger la red virtual. Para obtener una lista completa de directivas integradas, consulte [Definiciones de directivas integradas de Azure Policy para Azure DDoS Protection estándar](policy-reference.md). 

## <a name="validate-and-test"></a>Validación y prueba

En primer lugar, compruebe los detalles del plan de protección contra DDoS:

1. Seleccione **Todos los servicios** en la parte superior izquierda del portal.
2. Escriba *DDoS* en el cuadro **Filtrar**. Seleccione **Planes de protección contra DDoS** cuando aparezca en los resultados.
3. Seleccione el plan de protección contra DDoS en la lista.

Se debe mostrar la red virtual _MyVnet_. 

### <a name="view-protected-resources"></a>Visualización de recursos protegidos
En **Recursos protegidos**, puede ver las redes virtuales protegidas y las direcciones IP públicas, o agregar más redes virtuales al plan de protección contra DDoS:

![Visualización de recursos protegidos](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede mantener los recursos para el tutorial siguiente. Si ya no lo necesita, elimine el grupo de recursos _MyResourceGroup_. Al eliminar el grupo de recursos, también elimina el plan de protección contra DDoS y todos sus recursos relacionados. Si no tiene pensado utilizar este plan de protección contra DDoS, debe eliminar los recursos para evitar cargos innecesarios.

   >[!WARNING]
   >Esta acción es irreversible.

1. En Azure Portal, busque y seleccione **Grupos de recursos** o seleccione **Grupos de recursos** desde el menú de Azure Portal.

2. Filtre o desplácese hacia abajo para buscar el grupo de recursos _MyResourceGroup_.

3. Seleccione el grupo de recursos y, después, seleccione **Eliminar grupo de recursos**.

4. Escriba el nombre del grupo de recursos para confirmar y, a continuación, seleccione **Eliminar**.

Para deshabilitar la protección contra DDoS en una red virtual: 

1. Escriba el nombre de la red virtual para la que desea deshabilitar DDoS Protection Standard en el cuadro **Buscar recursos, servicios y documentos** en la parte superior del portal. Seleccione el nombre de la red virtual cuando aparezca en los resultados de la búsqueda.
2. En **DDoS Protection Standard**, seleccione **Deshabilitar**.

Si quiere eliminar un plan de protección contra DDoS, primero debe desasociar todas las redes virtuales que tenga. 

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo ver y configurar la telemetría del plan de protección contra DDoS, continúe con los tutoriales.

> [!div class="nextstepaction"]
> [Visualización y configuración de la telemetría de protección contra DDoS](telemetry.md)
