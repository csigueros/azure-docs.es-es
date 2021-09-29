---
title: Reserva de capacidad a petición en Azure (versión preliminar)
description: Aprenda a reservar capacidad de proceso en una región de Azure o una zona de disponibilidad con reserva de capacidad.
author: vargupt
ms.author: vargupt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/09/2021
ms.reviewer: cynthn, jushiman
ms.custom: template-how-to
ms.openlocfilehash: fe50e8db24f0f280365e435d8a205e9b45ac6ccb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774567"
---
# <a name="on-demand-capacity-reservation-preview"></a>Reserva de capacidad a petición (versión preliminar)

La reserva de capacidad a petición permite reservar capacidad de proceso en una región de Azure o una zona de disponibilidad durante cualquier período de tiempo. A diferencia de [Instancias reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/), no es necesario que se registre para un compromiso de 1 o 3 años. Cree y elimine reservas en cualquier momento y tenga control total sobre cómo quiere administrarlas.  

Una vez que se crea la reserva de capacidad, la capacidad está disponible de forma inmediata y se reserva exclusivamente para su uso hasta que se elimina la reserva.  


> [!IMPORTANT]
> La reserva de capacidad está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin un Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


La reserva de capacidad tiene algunas propiedades básicas que siempre se definen en el momento de la creación: 
- **Tamaño de máquina virtual** : cada reserva es para un tamaño de máquina virtual. Por ejemplo, `Standard_D2s_v3`. 
- **Ubicación** : cada reserva es para una ubicación (región). Si esa ubicación tiene zonas de disponibilidad, la reserva también puede especificar una de las zonas. 
- **Cantidad** : cada reserva tiene una cantidad de instancias que se deben reservar. 

Para crear una reserva de capacidad, estos parámetros se pasan a Azure como una solicitud de capacidad. Si la suscripción no tiene la cuota necesaria o Azure no tiene capacidad disponible que cumpla la especificación, la reserva no se implementará. Para evitar errores de implementación, solicite más cuota o pruebe otra combinación de tamaño, ubicación o zona de máquina virtual. 

Una vez que Azure acepta una solicitud de reserva, está disponible para que la consuman las máquinas virtuales de configuraciones que coincidan. Para consumir la reserva de capacidad, la máquina virtual tendrá que especificar la reserva como una de sus propiedades. De lo contrario, la reserva de capacidad permanecerá sin usar. Una ventaja de este diseño es que solo puede destinar cargas de trabajo críticas a las reservas y otras cargas de trabajo no críticas se pueden ejecutar sin capacidad reservada.   

> [!NOTE]
> La reserva de capacidad también incluye el Acuerdo de Nivel de Servicio de disponibilidad de Azure para su uso con máquinas virtuales. El Acuerdo de Nivel de Servicio no se aplicará durante la versión preliminar pública y se definirá cuando la reserva de capacidad esté disponible con carácter general.


## <a name="register-for-capacity-reservation"></a>Registro para la reserva de capacidad 

Antes de poder usar la característica Reserva de capacidad, debe registrar la suscripción para la versión preliminar. El registro puede tardar varios minutos en terminar. Puede usar la CLI de Azure o PowerShell para completar el registro de la característica.

### <a name="cli"></a>[CLI](#tab/cli1)

1. Use [az feature register](/cli/azure/feature#az_feature_register) para habilitar la versión preliminar para la suscripción:

    ```azurecli-interactive
    az feature register --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. El registro de la característica puede tardar hasta 15 minutos. Compruebe el estado del registro:

    ```azurecli-interactive
    az feature show --namespace Microsoft.Compute --name CapacityReservationPreview
    ```

1. Una vez que la característica se ha registrado para la suscripción, complete el proceso de participación mediante la propagación del cambio en el proveedor de recursos de Compute:

    ```azurecli-interactive
    az provider register --namespace Microsoft.Compute
    ``` 

### <a name="powershell"></a>[PowerShell](#tab/powershell1)

1. Use el cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para habilitar la versión preliminar para la suscripción:

    ```powershell-interactive
    Register-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. El registro de la característica puede tardar hasta 15 minutos. Compruebe el estado del registro:

    ```powershell-interactive
    Get-AzProviderFeature -FeatureName CapacityReservationPreview -ProviderNamespace Microsoft.Compute
    ``` 

1. Una vez que la característica se ha registrado para la suscripción, complete el proceso de participación mediante la propagación del cambio en el proveedor de recursos de Compute:

    ```powershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ``` 

--- 
<!-- The three dashes above show that your section of tabbed content is complete. Don't remove them :) -->

## <a name="benefits-of-capacity-reservation"></a>Ventajas de la reserva de capacidad 

- Una vez que se implementa, la capacidad se reserva para su uso y siempre está disponible dentro del ámbito de los Acuerdos de Nivel de Servicio aplicables  
- Se puede implementar y eliminar en cualquier momento sin compromiso de permanencia 
- Se puede combinar automáticamente con instancias reservadas para aprovechar los descuentos de los compromisos de permanencia  

## <a name="sla-for-capacity-reservation"></a>Acuerdo de Nivel de Servicio para la reserva de capacidad 

El Acuerdo de Nivel de Servicio para la reserva de capacidad se definirá más adelante cuando la característica esté disponible con carácter general.  


## <a name="limitations-and-restrictions"></a>Limitaciones y restricciones 

- Para la creación de reservas de capacidad se necesita cuota de la misma manera que para la creación de máquinas virtuales. 
- Las máquinas virtuales de acceso puntual y los nodos de Azure Dedicated Host no se admiten con la reserva de capacidad. 
- No se admiten algunas restricciones de implementación: 
    - Grupo con ubicación por proximidad 
    - Dominios de actualización 
    - Almacenamiento UltraSSD  
- Durante la versión preliminar pública solo se admiten las máquinas virtuales de las series Av2, B, D, E y F. 
- En el caso de las series de máquinas virtuales admitidas durante la versión preliminar pública, se admiten hasta tres dominios de error (DE). Una implementación con más de tres DE no se implementará en la reserva de capacidad. 
- Los conjuntos de disponibilidad no se admiten con la reserva de capacidad. 
- Durante esta versión preliminar, solo la suscripción que ha creado la reserva puede usarla. 
- Las reservas solo están disponibles para los clientes de Azure de pago. Las cuentas patrocinadoras, como Evaluación gratuita y Azure for Students, no son aptas para usar esta característica. 


## <a name="pricing-and-billing"></a>Precios y facturación 

Las reservas de capacidad tienen el mismo precio que el tamaño de máquina virtual subyacente. Por ejemplo, si crea una reserva para 10 cantidades de máquina virtual D2s_v3, en cuanto se cree la reserva, se le empezará a facturar por 10 máquinas virtuales D2s_v3, incluso si la reserva no se usa.  

Si después implementa una máquina virtual D2s_v3 y especifica la reserva como su propiedad, la reserva de capacidad se usa. Una vez que esté en uso, solo pagará por la máquina virtual y nada más por la reserva de capacidad. Imagine que implementa cinco máquinas virtuales D2s_v3 en la reserva de capacidad mencionada anteriormente. Verá una factura de cinco máquinas virtuales D2s_v3 y cinco reservas de capacidad sin usar, ambas facturadas con la misma tarifa que una máquina virtual D2s_v3.    

Tanto la reserva de capacidad usada como la no usada son aptas para los descuentos de compromiso de permanencia de instancias reservadas. En el ejemplo anterior, si tiene instancias reservadas para dos máquinas virtuales D2s_v3 en la misma región de Azure, la facturación de dos recursos (ya sea la máquina virtual o la reserva de capacidad sin usar) se pondrá a cero y solo pagará por el resto de los ocho recursos (es decir, cinco reservas de capacidad sin usar y tres máquinas virtuales D2s_v3). En este caso, los descuentos de compromiso de permanencia se podrían aplicar en la máquina virtual o en la reserva de capacidad sin usar, a las que se aplica la misma tarifa de pago por uso. 


## <a name="work-with-capacity-reservation"></a>Trabajo con la reserva de capacidad 

La reserva de capacidad se puede crear para un tamaño de máquina virtual específico en una región de Azure o una zona de disponibilidad. Todas las reservas se crean y administran como parte de un grupo de reservas de capacidad, lo que permite la creación de un grupo para administrar diferentes tamaños de máquina virtual en una sola aplicación de varios niveles. Cada reserva es para un tamaño de máquina virtual y un grupo solo puede tener una reserva por tamaño de máquina virtual.  

Para consumir la reserva de capacidad, especifique el grupo de reservas de capacidad como una de las propiedades de la máquina virtual. Si el grupo no tiene una reserva correspondiente, Azure devolverá un mensaje de error. 

La cantidad reservada para la reserva se puede ajustar después de la implementación inicial si se cambia la propiedad de la capacidad. No se permiten otros cambios en la reserva de capacidad, como los del tamaño o la ubicación de la máquina virtual. El enfoque recomendado consiste en eliminar la reserva existente y crear otra con los nuevos requisitos. 

La reserva de capacidad no crea límites en el número de implementaciones de máquina virtual. Azure admite la asignación de tantas máquinas virtuales como quiera en la reserva. Como para la propia reserva se necesita cuota, se omiten las comprobaciones de cuota para la implementación de máquinas virtuales hasta la cantidad reservada. La asignación de más máquinas virtuales a la reserva está sujeta a comprobaciones de cuota y al cumplimiento de la capacidad adicional por parte de Azure. Una vez que se implementan, estas instancias de máquina virtual adicionales pueden hacer que la cantidad de máquinas virtuales asignadas a la reserva supere la cantidad reservada. Este estado se denomina sobreasignación. Para obtener más información, vaya a [Sobreasignación de la reserva de capacidad](capacity-reservation-overallocate.md). 


## <a name="capacity-reservation-lifecycle"></a>Ciclo de vida de la reserva de capacidad

Cuando se crea una reserva, Azure reserva el número solicitado de instancias de capacidad en la ubicación especificada: 

![Imagen 1 de la reserva de capacidad.](./media/capacity-reservation-overview/capacity-reservation-1.jpg) 

Realice el seguimiento del estado de la reserva general por medio de las siguientes propiedades:  
- `capacity` = cantidad total de instancias reservadas por el cliente 
- `virtualMachinesAllocated` = lista de máquinas virtuales asignadas en la reserva de capacidad y recuento para el consumo de la capacidad. Estas máquinas virtuales tiene el estado *En ejecución* o *Detenida* (*Asignada*), o bien pueden estar en un estado transitorio como *Iniciándose* o *Deteniéndose*. En esta lista no se incluyen las máquinas virtuales que se encuentran en el estado desasignado, que se denomina *Detenida* (*desasignada*). 
- `virtualMachinesAssociated` = lista de máquinas virtuales asociadas a la reserva de capacidad. Esta lista tiene todas las máquinas virtuales que se han configurado para usar la reserva, incluidas las que están en estado desasignado.  

El ejemplo anterior empezará con `capacity` como 2 y una longitud de `virutalMachinesAllocated` y `virtualMachinesAssociated` como 0.  

Cuando después se asigna una máquina virtual a la reserva de capacidad, consumirá lógicamente una de las instancias de capacidad reservada: 

![Imagen 2 de la reserva de capacidad.](./media/capacity-reservation-overview/capacity-reservation-2.jpg) 

Ahora el estado de la reserva de capacidad mostrará `capacity` como 2 y una longitud de `virutalMachinesAllocated`, y `virtualMachinesAssociated` como 1.  

Las asignaciones sobre la reserva de capacidad se realizarán correctamente siempre que las máquinas virtuales tengan propiedades que coincidan y haya al menos una instancia de capacidad vacía.  

En el ejemplo, cuando se asigna una tercera máquina virtual a la reserva de capacidad, la reserva entra en el estado [sobreasignado](capacity-reservation-overallocate.md). Para esta tercera máquina virtual se necesitará la cuota sin usar y el suministro de capacidad adicional de Azure. Después de asignar la tercera máquina virtual, la reserva de capacidad tiene este aspecto: 

![Imagen 3 de la reserva de capacidad.](./media/capacity-reservation-overview/capacity-reservation-3.jpg) 

`capacity` es 2 y la longitud de `virutalMachinesAllocated`, y `virtualMachinesAssociated` es 3. 

Ahora imagine que la aplicación se reduce verticalmente al mínimo de dos máquinas virtuales. Como la máquina virtual 0 necesita una actualización, se elige para la desasignación. La reserva cambia automáticamente a este estado: 

![Imagen 4 de la reserva de capacidad.](./media/capacity-reservation-overview/capacity-reservation-4.jpg) 

`capacity` y la longitud `virtualMachinesAllocated` son 2. Pero la longitud de `virtualMachinesAssociated` sigue siendo 3 ya que la máquina virtual 0, aunque está desasignada, todavía está asociada a la reserva de capacidad.  

La reserva de capacidad existirá hasta que se elimine de forma explícita. Para eliminar una reserva de capacidad, el primer paso consiste en desasociar todas las máquinas virtuales de la propiedad `virtualMachinesAssociated`. Una vez que se complete la desasociación, la reserva de capacidad debe tener este aspecto: 

![Imagen 5 de la reserva de capacidad.](./media/capacity-reservation-overview/capacity-reservation-5.jpg) 

Ahora el estado de la reserva de capacidad mostrará `capacity` como 2 y una longitud de `virtualMachinesAssociated`, y `virtualMachinesAllocated` como 0. Desde este estado, se puede eliminar la reserva de capacidad. Una vez que se haya eliminado ya no pagará por la reserva.  

![Imagen 6 de la reserva de capacidad.](./media/capacity-reservation-overview/capacity-reservation-6.jpg)


## <a name="usage-and-billing"></a>Uso y facturación 

Cuando una reserva de capacidad está vacía, se notifica el uso de la máquina virtual para el tamaño de máquina virtual correspondiente y la ubicación. Las [instancias reservadas de máquina virtual](https://azure.microsoft.com/pricing/reserved-vm-instances/) pueden cubrir parte o todo el uso de la reserva de capacidad incluso cuando las máquinas virtuales no están implementadas. 

### <a name="example"></a>Ejemplo

Por ejemplo, imagine que se ha creado una reserva de capacidad con la cantidad reservada 2. La suscripción tiene acceso a una instancia reservada de máquina virtual correspondiente del mismo tamaño. El resultado son dos flujos de uso para la reserva de capacidad, uno de los cuales está cubierto por la instancia reservada: 

![Imagen 7 de la reserva de capacidad.](./media/capacity-reservation-overview/capacity-reservation-7.jpg)

En la imagen anterior, se aplica un descuento de instancia reservada de máquina virtual a una de las instancias sin usar y el costo de esa instancia se pondrá a cero. En el caso de la otra instancia, se cobrará la tarifa de pago por uso para el tamaño de máquina virtual reservado.  

Cuando se asigna una máquina virtual a la reserva de capacidad, también se deben asignar los demás componentes de máquina virtual, como discos, redes, extensiones y cualquier otro componente solicitado. En este estado, el uso de la máquina virtual reflejará una máquina virtual asignada y una instancia de capacidad sin usar. La instancia reservada de máquina virtual pondrá a cero el costo de la máquina virtual o la instancia de capacidad sin usar. Los demás cargos para discos, redes y otros componentes asociados a la máquina virtual asignada también aparecerán en la factura. 

![Imagen 8 de la reserva de capacidad.](./media/capacity-reservation-overview/capacity-reservation-8.jpg)

En la imagen anterior, el descuento de instancia reservada de máquina virtual se aplica a la máquina virtual 0, que solo se cobrará por otros componentes, como el disco y las redes. La otra instancia sin usar se cobra según la tarifa de pago por uso por el tamaño de máquina virtual reservado.


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 

- **¿Cuál es el precio de la reserva de capacidad a petición?**

    El precio de la reserva de capacidad a petición es el mismo que el del tamaño de máquina virtual subyacente asociado a la reserva. Al usar la reserva de capacidad, se le cobrará por el tamaño de máquina virtual seleccionado según las tarifas de pago por uso, independientemente de si la máquina virtual se ha aprovisionado o no.  Visite las páginas de precios de máquinas virtuales [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) y [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) para obtener más información.

- **¿Se me cobrará dos veces por el costo de la reserva de capacidad a petición y por la máquina virtual real cuando finalmente la aprovisione?**

    No, solo se le cobrará una vez por la reserva de capacidad a petición.   

- **¿Puedo aplicar la instancia reservada de máquina virtual (RI) a la reserva de capacidad a petición para reducir los costos?**

    Sí, puede aplicar instancias reservadas existentes o futuras a reservas de capacidad a petición y recibir descuentos de RI. Las RI disponibles se aplican automáticamente a la reserva de capacidad de la misma manera que a las máquinas virtuales.

- **¿Cuál es la diferencia entre la instancia reservada de máquina virtual (RI) y la reserva de capacidad a petición?**

    Tanto las RI como las reservas de capacidad a petición son aplicables a las máquinas virtuales de Azure. Pero las RI proporcionan tarifas de reserva con descuento para las máquinas virtuales en comparación con las tarifas de pago por uso como resultado de un compromiso de permanencia, de 1 o 3 años. Por el contrario, para las reservas de capacidad a petición no se necesita un compromiso. Puede crear o cancelar una reserva de capacidad en cualquier momento. Pero no se aplicará ningún descuento y se incurrirá en cargos por tarifas de pago por uso después de que la reserva de capacidad se haya aprovisionado correctamente. A diferencia de las RI, que dan prioridad a la capacidad pero no la garantizan, al comprar una reserva de capacidad a petición, Azure reserva la capacidad de proceso de la máquina virtual y proporciona una garantía de Acuerdo de Nivel de Servicio.  

- **¿Qué escenarios se beneficiarían más de las reservas de capacidad a petición?**

    Entre los escenarios típicos se incluyen la continuidad empresarial, la recuperación ante desastres y la escalabilidad horizontal de aplicaciones críticas.  


## <a name="next-steps"></a>Pasos siguientes

Cree una reserva de capacidad y comience a reservar capacidad de proceso en una región de Azure o una zona de disponibilidad. 

> [!div class="nextstepaction"]
> [Creación de una reserva de capacidad](capacity-reservation-create.md)
