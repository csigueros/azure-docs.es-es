---
title: Creación de una reserva de capacidad en Azure (versión preliminar)
description: Aprenda a reservar capacidad de proceso en una región de Azure o en una zona de disponibilidad al crear una reserva de capacidad.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: 360a634104b7735a90cbae4b41eae9723b723e2e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532631"
---
# <a name="create-a-capacity-reservation-preview"></a>Creación de una reserva de capacidad (versión preliminar)

La reserva de capacidad siempre se crea como parte de un grupo de reserva de capacidad. El primer paso consiste en crear un grupo si aún no existe uno adecuado y, luego, crear reservas. Una vez que las reservas se hayan creado correctamente, estarán disponibles de inmediato para su uso con máquinas virtuales. La capacidad se reserva para su uso, siempre y cuando no se elimine la reserva.     

Una solicitud bien formada para el grupo de reserva de capacidad siempre debe completarse correctamente, ya que no reserva ninguna capacidad. Simplemente sirve como contenedor para las reservas. Sin embargo, una solicitud de reserva de capacidad podría producir un error si no tiene la cuota necesaria para la serie de VM o si Azure no tiene capacidad suficiente para satisfacer la solicitud. Por esto, solicite más cuota o pruebe otra combinación de tamaño, ubicación o zona de VM. 

La creación de una reserva de capacidad se completa correctamente o se produce un error en su totalidad. Para una solicitud para reservar 10 instancias, solo se devuelve un estado correcto si se pudieron asignar las 10. De lo contrario, no se podrá crear la reserva de capacidad. 

> [!IMPORTANT]
> La reserva de capacidad está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="considerations"></a>Consideraciones

La reserva de capacidad debe cumplir las siguientes reglas: 
- El parámetro location debe coincidir con la propiedad location del grupo de reserva de capacidad primario. Una falta de coincidencia producirá un error. 
- El tamaño de VM debe estar disponible en la región de destino. De lo contrario, no se podrá crear la reserva. 
- La suscripción debe tener una cuota aprobada suficiente igual o superior a la cantidad de VM que se reservan para la serie de VM y para la región en general. Si fuera necesario, [solicite más cuota](../azure-portal/supportability/per-vm-quota-requests.md).
- Cada grupo de reserva de capacidad puede tener exactamente una reserva para un determinado tamaño de VM. Por ejemplo, solo se puede crear una reserva de capacidad para el tamaño de VM `Standard_D2s_v3`. Si intenta crear una segunda reserva para `Standard_D2s_v3` en el mismo grupo de reserva de capacidad, se producirá un error. Sin embargo, se puede crear otra reserva en el mismo grupo para otros tamaños de VM, como `Standard_D4s_v3`, `Standard_D8s_v3`, y así sucesivamente.  
- Para un grupo de reserva de capacidad que admite zonas, cada tipo de reserva se define mediante la combinación del **tamaño de VM** y la **zona**. Por ejemplo, se admite una reserva de capacidad para `Standard_D2s_v3` en `Zone 1`, otra reserva de capacidad para `Standard_D2s_v3` en `Zone 2`, y una tercera reserva de capacidad para `Standard_D2s_v3` en `Zone 3`.


## <a name="create-a-capacity-reservation"></a>Creación de una reserva de capacidad 

### <a name="api"></a>[API](#tab/api1)

1. Creación de un grupo de reserva de capacidad 

    Para crear un grupo de reserva de capacidad, cree la siguiente solicitud PUT en el proveedor *Microsoft.Compute*: 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}&api-version=2021-04-01
    ``` 
    
    En el cuerpo de la solicitud, incluya lo siguiente: 
    
    ```json
    { 
      "location":"eastus"
    } 
    ```
    
    Este grupo se crea para contener reservas para la ubicación Este de EE. UU. 
    
    En este ejemplo, el grupo solo admitirá reservas regionales, ya que no se especificaron zonas en el momento de la creación. Para crear un grupo zonal, pase un parámetro adicional *zones* en el cuerpo de la solicitud, como se muestra a continuación: 
    
    ```json
    { 
      "location":"eastus",
      "zones": ["1", "2", "3"] 
    } 
    ```
 
1. Creación de una reserva de capacidad 

    Para crear un grupo de reserva, cree la siguiente solicitud PUT en el proveedor *Microsoft.Compute*: 
    
    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01 
    ```
    
    En el cuerpo de la solicitud, incluya lo siguiente: 
    
    ```json
    { 
      "location": "eastus", 
      "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
      }, 
     "tags": { 
            "environment": "testing" 
    } 
    ```
    
    La solicitud anterior crea una reserva en la ubicación Este de EE. UU. para 5 cantidades del tamaño de VM D2s_v3. 


### <a name="portal"></a>[Portal](#tab/portal1)

<!-- no images necessary if steps are straightforward --> 

1. Abra [Azure Portal](https://portal.azure.com)
1. En la barra de búsqueda, escriba **Grupos de reserva de capacidad**.
1. Seleccione **Grupos de reserva de capacidad** en las opciones.
1. Seleccione **Crear**
1. En la pestaña *Aspectos básicos*, cree un grupo de reserva de capacidad:
    1. Seleccione una **suscripción**.
    1. Seleccione un **grupo de recursos** o cree uno.
    1. Asigne un **nombre** al grupo. 
    1. Seleccione una **región**. 
    1. De manera opcional, seleccione **Zonas de disponibilidad** u opte por no especificar ninguna zona y permitir que Azure la elija automáticamente.
1. Seleccione **Siguiente**.
1. En la pestaña *Reservas*, cree al menos una reserva de capacidad:
    1. Asigne a cada reserva un **Nombre de reserva**, la cantidad de **Instancias** de VM y seleccione un **Tamaño de VM**.
    1. La columna *Costo/mes* mostrará información de facturación en función de las selecciones.
1. Seleccione **Siguiente**.
1. En la pestaña *Etiquetas*, tiene la opción de crear etiquetas.
1. Seleccione **Siguiente**. 
1. En la pestaña *Revisar y crear*, revise la información del grupo de reserva de capacidad.
1. Seleccione **Crear**


### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Para poder crear una reserva de capacidad, cree un grupo de recursos con `New-AzResourceGroup`. En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación Este de EE. UU.

    ```powershell-interactive
    New-AzResourceGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    ```

1. Ahora cree un grupo de reserva de capacidad con `New-AzCapacityReservationGroup`. En el ejemplo siguiente se crea un grupo *myCapacityReservationGroup* en la ubicación Este de EE. UU. para las 3 zonas de disponibilidad.

    ```powershell-interactive
    New-AzCapacityReservationGroup
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1","2","3"
    -Name "myCapacityReservationGroup"
    ```

1. Una vez creado el grupo de reserva de capacidad, cree una nueva reserva de capacidad con `New-AzCapacityReservation`. En el ejemplo siguiente se crea *myCapacityReservation* para 5 cantidades de tamaño de VM Standard_D2s_v3 en Zona 1 de la ubicación Este de EE. UU.

    ```powershell-interactive
    New-AzCapacityReservation
    -ResourceGroupName "myResourceGroup"
    -Location "eastus"
    -Zone "1"
    -ReservationGroupName "myCapacityReservationGroup"
    -Name "myCapacityReservation"
    -Sku "Standard_D2s_v3"
    -CapacityToReserve 5
    ```

Para más información, consulte los comandos de Azure PowerShell [New-AzResourceGroup](/powershell/module/az.Resources/new-azresourcegroup), [New-AzCapacityReservationGroup](/powershell/module/az.compute/new-azcapacityreservationgroup) y [New-AzCapacityReservation](/powershell/module/az.compute/new-azcapacityreservation).


### <a name="arm-template"></a>[Plantilla ARM](#tab/arm1)

Una  [plantilla de ARM](../azure-resource-manager/templates/overview.md) es un archivo de notación de objetos JavaScript (JSON) que define la infraestructura y la configuración del proyecto. La plantilla usa sintaxis declarativa. En la sintaxis declarativa, se describe la implementación deseada sin escribir la secuencia de comandos de programación para crearla. 

Las plantillas de Resource Manager permiten implementar grupos de recursos relacionados. En una sola plantilla, puede crear grupos de reserva de capacidad y reservas de capacidad. Puede implementar plantillas mediante Azure Portal, la CLI de Azure o Azure PowerShell, o bien desde las canalizaciones de integración continua o entrega continua (CI/CD). 

Si el entorno cumple los requisitos previos y ya está familiarizado con el uso de plantillas de ARM, use cualquiera de las plantillas siguientes: 

- [Creación de una reserva de capacidad zonal](https://github.com/Azure/on-demand-capacity-reservation/blob/main/ZonalCapacityReservation.json)
- [Creación de una VM con reserva de capacidad](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineWithReservation.json)
- [Creación de conjuntos de escalado de máquinas virtuales con reserva de capacidad](https://github.com/Azure/on-demand-capacity-reservation/blob/main/VirtualMachineScaleSetWithReservation.json)


--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="check-on-your-capacity-reservation"></a>Comprobación de la reserva de capacidad 

Una vez que la reserva de capacidad de haya creado correctamente, estará disponible de inmediato para su uso con las VM. 

### <a name="api"></a>[API](#tab/api2)

```rest
GET  
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/CapacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{capacityReservationName}?api-version=2021-04-01
```
 
```json
{ 
    "name": "<CapacityReservationName>", 
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/capacityReservationGroups/{CapacityReservationGroupName}/capacityReservations/{CapacityReservationName}", 
    "type": "Microsoft.Compute/capacityReservationGroups/capacityReservations", 
    "location": "eastus", 
    "tags": { 
        "environment": "testing" 
    }, 
    "sku": { 
        "name": "Standard_D2s_v3", 
        "capacity": 5 
    }, 
    "properties": { 
        "reservationId": "<reservationId>", 
         "provisioningTime": "<provisioningTime>", 
         "provisioningState": "Updating" 
    } 
} 
```

### <a name="powershell"></a>[PowerShell](#tab/powershell2)

Compruebe la reserva de capacidad:

```powershell-interactive
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">
```

Para buscar el tamaño de VM y la cantidad reservada, use lo siguiente: 

```powershell-interactive
$CapRes =
Get-AzCapacityReservation
-ResourceGroupName <"ResourceGroupName">
-ReservationGroupName <"CapacityReservationGroupName">
-Name <"CapacityReservationName">

$CapRes.sku
```

Para más información, vaya al comando de Azure PowerShell [Get-AzCapacityReservation](/powershell/module/az.compute/get-azcapacityreservation).

### <a name="portal"></a>[Portal](#tab/portal3)

1. Abra [Azure Portal](https://portal.azure.com)
1. En la barra de búsqueda, escriba **Grupos de reserva de capacidad**.
1. Seleccione **Grupos de reserva de capacidad** en las opciones.
1. En la lista, seleccione el nombre del grupo de reserva de capacidad que acaba de crear.
1. Seleccione **Información general** a la izquierda.
1. Seleccione **Reservas**.
1. En esta vista, podrá ver todas las reservas del grupo, junto con el tamaño de VM y la cantidad reservada.

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a modificar la reserva de capacidad](capacity-reservation-modify.md)
