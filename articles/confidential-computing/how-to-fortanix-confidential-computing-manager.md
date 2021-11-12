---
title: Fortanix Confidential Computing Manager en una aplicación administrada de Azure
description: Obtenga información sobre cómo implementar Fortanix Confidential Computing Manager (CCM) en una aplicación administrada en Azure Portal.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: fa811a3f528fb0d1bf20e053910540a6204eb724
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315924"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix Confidential Computing Manager en una aplicación administrada de Azure

En este artículo se muestra cómo implementar una aplicación administrada por Fortanix Confidential Computing Manager en Azure Portal.

Fortanix es un proveedor de software de terceros con productos y servicios basados en la infraestructura de Azure. Hay otros proveedores de terceros que ofrecen servicios de computación confidencial similares en Azure.

> [!NOTE]
>Los productos a los que se hace referencia en este documento no están bajo el control de Microsoft. Microsoft le proporciona esta información solo para su comodidad y las referencias a estos productos que no son de Microsoft no implica que tengan la aprobación de Microsoft.

## <a name="prerequisites"></a>Prerrequisitos

- Un registro de Docker privado al que se enviarán las imágenes de aplicaciones convertidas.
- Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de empezar.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Implementación de una instancia de Confidencial Computing Manager a través de una aplicación administrada de Azure

1. Vaya a [Azure Portal](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/Azure-portal.png" alt-text="Azure Portal":::.

2. En la barra de búsqueda, busque "Fortanix Confidential Computing Manager" y encontrará la lista de Marketplace de Fortanix CCM. Seleccione **Fortanix Confidential Computing Manager on Azure**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Lista de Marketplace.":::

3. Se abre la página en la que se crea la aplicación administrada por CCM. Seleccione **Crear**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Crear la aplicación.":::

4. Rellene todos los campos obligatorios.
   1. En la sección Detalles de la aplicación administrada, el campo **Grupo de recursos administrado** tendrá un valor predeterminado que el usuario puede modificar si es necesario.
   2. En el campo **Región**, seleccione **Este de Australia**, **Sudeste de Australia**, **Este de EE. UU.** , **Oeste de EE. UU. 2**, **Oeste de Europa**, **Norte de Europa**, **Centro de Canadá**, **Este de Canadá** o **Este de EE. UU. 2 EUAP**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Campos obligatorios":::

   Seleccione **Revisar y crear** para crear la aplicación administrada por Fortanix CCM.

5. Revise los detalles y, una vez superada la validación, active la casilla **Acepto los términos y condiciones anteriores** y, después, seleccione **Crear** para crear la aplicación administrada.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Revisar los detalles.":::

6. Se iniciará la implementación de Fortanix CCM y se notificará que la implementación está en curso.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Progreso de la implementación.":::

7. Una vez finalizada la implementación, seleccione el botón **Ir al recurso** para ir a la página "Información general" de la aplicación administrada por CCM implementada para inscribir el nodo de proceso.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Captura de pantalla que muestra una implementación correcta en Azure Portal.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Captura de pantalla que muestra información general sobre el recurso informático confidencial en Azure Portal.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Inscripción del nodo de proceso en Fortanix CCM

1. Seleccione **Confidential Computing Manager** en el menú de navegación izquierdo. Inicie sesión en Fortanix CCM y cree una cuenta como se ve en la **Figura 9**.

    Para obtener más información sobre cómo registrarse, iniciar sesión y crear una cuenta en CCM, consulte [Introducción a CCM](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Captura de pantalla que muestra el inicio de sesión de Fortanix Confidential Computing Manager.":::
    
2. Para obtener el token de unión de la consola de administración de CCM, seleccione primero el botón **ENROLL NODE** (INSCRIBIR NODO). A continuación, en la ventana ENROLL NODE (INSCRIBIR NODO), seleccione el botón **COPIAR** para copiar el token de unión.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Captura de pantalla que muestra cómo obtener el token de unión.":::

3. Ahora, para inscribir un agente de nodo, seleccione la pestaña **Confidencial Computing Node Agent** (Agente de nodo de computación confidencial) y seleccione **Agregar** para agregar un agente de nodo de CCM.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Captura de pantalla que muestra cómo agregar el agente de nodo.":::

4.  En el formulario del agente del nodo de CCM, rellene todos los campos obligatorios. Pegue el token de unión que copió en el paso 2 en **Token de unión**. Seleccione **Revisar y enviar** para confirmar la operación.

    Para obtener más información sobre cómo inscribir un nodo de proceso de CCM, consulte el tema sobre la [inscripción del nodo de proceso](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Captura de pantalla que muestra la inscripción del nodo de proceso.":::
    
5. Una vez superada la validación, seleccione **Enviar** para completar la creación del agente de nodo.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Captura de pantalla que muestra que el agente de nodo se ha creado.":::

6. Para comprobar el estado de la implementación, vaya a la pestaña **Información general** y seleccione el vínculo **Grupo de recursos administrado**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Captura de pantalla que muestra que el nodo se ha inscrito.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Captura de pantalla que muestra la comprobación del estado de implementación.":::

7. Ahora observará que el estado de la implementación aún está en curso y el agente de nodo tardará unos minutos en inscribirse correctamente.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Captura de pantalla que muestra la implementación en curso.":::

8. Cuando la inscripción del agente de nodo finaliza correctamente, el estado cambia a "Correcto".

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Captura de pantalla que muestra que la implementación se ha realizado correctamente.":::

9. Ahora, en la aplicación administrada por CCM, vaya a las páginas Nodos de proceso y observará que el nodo se encuentra en un estado **Activo** y se ha inscrito correctamente.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Captura de pantalla que muestra que el nodo se ha inscrito correctamente.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

El usuario también puede eliminar un agente de nodo de CCM de la página Confidencial Computing Node Agent (Agente de nodo de computación confidencial). Para eliminar el agente de nodo, seleccione el agente de nodo y haga clic en el botón **Eliminar** en la barra superior.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Captura de pantalla que muestra la eliminación del agente de nodo.":::

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, inscribió un nodo con una aplicación administrada de Azure en Fortanix Confidential Computing Manager. La inscripción del nodo le permite convertir la imagen de la aplicación para la ejecución en una máquina virtual de computación confidencial. Para más información sobre las máquinas virtuales de computación confidencial en Azure, consulte [Soluciones en Virtual Machines](virtual-machine-solutions-sgx.md).

Para más información sobre las ofertas de computación confidencial de Azure, consulte [Computación confidencial de Azure](overview.md).

Más información sobre cómo completar tareas similares con otras ofertas de terceros en Azure, como [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) y [Scone](https://sconedocs.github.io).
