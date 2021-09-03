---
title: Compartir imágenes de máquina virtual con Galerías compartidas
description: Aprenda a usar galerías de imágenes compartidas para compartir imágenes de máquinas virtuales Linux en su organización.
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 6/8/2021
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: f00f98ac9e6350aa09d3fa67c1c87677fef2db6f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738972"
---
# <a name="shared-image-galleries-overview"></a>Información general de Shared Image Galleries

Shared Image Gallery es un servicio que ayuda a crear la estructura y la organización en torno a las imágenes. Las galerías de imágenes compartidas proporcionan:

- Replicación global de imágenes.
- Control de versiones y agrupación de las imágenes para facilitar la administración.
- Imágenes de alta disponibilidad con cuentas de almacenamiento con redundancia de zona (ZRS) en las regiones donde esté disponible Availability Zones. ZRS ofrece mejor resistencia a errores de zona.
- Se admite Premium Storage (Premium_LRS).
- Uso compartido entre suscripciones e, incluso entre inquilinos de Active Directory (AD), mediante Azure RBAC.
- Escalado de las implementaciones con réplicas de imagen en cada región.

Con Shared Image Gallery, puede compartir imágenes con diferentes usuarios, entidades de servicio o grupos de AD dentro y fuera de su organización. Las imágenes compartidas se pueden replicar en varias regiones, para un escalado más rápido de las implementaciones.

Una imagen es una copia de una VM completa (incluidos los discos de datos asociados) o, simplemente, el disco del sistema operativo, según cómo se cree. Cuando crea una VM desde la imagen, la copia de los VHD de la imagen se usa para crear los discos para la nueva VM. La imagen permanece en el almacenamiento y puede usarse una y otra vez para crear nuevas VM.

Si tiene un gran número de imágenes que se deben mantener y quiere que estén disponibles en toda la empresa, puede usar Shared Image Gallery como repositorio. 

La característica de galería de imágenes compartidas tiene varios tipos de recursos:

| Resource | Descripción|
|----------|------------|
| **Origen de imagen** | Se trata de un recurso que se puede usar para crear una **versión de imagen** en una galería de imágenes. Un origen de imagen puede ser una máquina virtual de Azure existente, ya sea [generalizada o especializada](#generalized-and-specialized-images); una imagen administrada; una instantánea; un disco duro virtual o una versión de imagen de otra galería de imágenes. |
| **Galería de imágenes** | Al igual que Azure Marketplace, una **galería de imágenes** es un repositorio para administrar y compartir imágenes, pero usted puede controlar quién tiene acceso. |
| **Definición de la imagen** | Las definiciones de imagen se crean dentro de una galería y contienen información sobre la imagen y los requisitos para usarla internamente. Esto incluye si la imagen es Windows o Linux, notas de la versión y los requisitos de memoria mínima y máxima. Es una definición de un tipo de imagen. |
| **Versión de la imagen** | Una **versión de la imagen** es lo que se usa para crear una VM cuando se usa una galería. Puede tener varias versiones de una imagen según sea necesario para su entorno. Al igual que una imagen administrada, cuando se usa una **versión de la imagen** para crear una VM, la versión de la imagen se usa para crear nuevos discos para la VM. Las versiones de las imágenes pueden usarse varias veces. |

<br>

![Gráfico que muestra cómo puede tener varias versiones de una imagen en la galería](./media/shared-image-galleries/shared-image-gallery.png)

## <a name="image-definitions"></a>Definiciones de imagen

Las definiciones de la imagen son una agrupación lógica de las versiones de una imagen. La definición de la imagen contiene información acerca de por qué se creó la imagen, para qué sistema operativo sirve y otra información acerca del uso de la imagen. Una definición de imagen es como un plan para todos los detalles que rodean la creación de una imagen específica. No se implementa una VM desde una definición de imagen, sino desde las versiones de imágenes creadas a partir de la definición.

Hay tres parámetros para cada definición de imagen que se usan en combinación: **Publisher**, **Offer** y **SKU**. Se utilizan para buscar una definición de imagen específica. Puede tener versiones de imágenes que comparten uno o dos valores, pero no los tres.  Por ejemplo, estas son las tres definiciones de imágenes y sus valores:

|Definición de imágenes|Publicador|Oferta|SKU|
|---|---|---|---|
|myImage1|Contoso|Finance|Back-end|
|myImage2|Contoso|Finance|Front-end|
|myImage3|Prueba|Finance|Front-end|

Los tres tienen conjuntos de valores únicos. El formato es similar a cómo puede especificar actualmente el editor, la oferta y la SKU para las [imágenes de Azure Marketplace](./windows/cli-ps-findimage.md) en Azure PowerShell para obtener la última versión de una imagen de Marketplace. Cada definición de imagen debe tener un conjunto único de estos valores.

Los siguientes parámetros determinan qué tipos de versiones de imagen pueden contener:

- Estado del sistema operativo: puede establecer el estado del sistema operativo en [generalizado o especializado](#generalized-and-specialized-images). Este campo es obligatorio.
- Sistema operativo: puede ser Windows o Linux. Este campo es obligatorio.
- Generación de Hyper-V: especifique si la imagen se creó a partir de un disco duro virtual de Hyper-V de generación 1 o [generación 2](generation-2.md). El valor predeterminado es la generación 1.


Los siguientes son otros parámetros que se pueden establecer en la definición de la imagen para que pueda realizar un seguimiento más sencillo de sus recursos:

- Descripción: la descripción de uso para proporcionar información más detallada sobre por qué existe la definición de la imagen. Por ejemplo, podría tener una definición de la imagen para el servidor front-end que tenga la aplicación preinstalada.
- CLUF: puede utilizarse para señalar un contrato de licencia de usuario final específico para la definición de la imagen.
- Declaración de privacidad y Notas de la versión: almacene las notas de la versión y las declaraciones de privacidad en el almacenamiento Azure y proporcione un identificador URI para acceder a ellas como parte de la definición de imagen.
- Fecha final del ciclo de vida: establezca una fecha final del ciclo de vida predeterminada para todas las versiones de la imagen en la definición de la imagen. Las fechas finales del ciclo de vida son informativas; los usuarios seguirán teniendo la posibilidad de crear máquinas virtuales a partir de imágenes y versiones tras alcanzarse la fecha final del ciclo de vida.
- Etiqueta: puede agregar etiquetas al crear la definición de imagen. Para más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos](../azure-resource-manager/management/tag-resources.md).
- Número mínimo y máximo de vCPU y recomendaciones de memoria: si la imagen tiene vCPU y recomendaciones de memoria, puede adjuntar esa información a la definición de imagen.
- Tipos de disco no permitidos: puede proporcionar información acerca de las necesidades de almacenamiento para la máquina virtual. Por ejemplo, si la imagen no es adecuada para los discos HDD estándar, agréguelos a la lista de no permitidos.
- Información del plan de compra para imágenes de Marketplace: `-PurchasePlanPublisher`, `-PurchasePlanName`y `-PurchasePlanProduct`. Para más información sobre el plan de compra, consulte [Buscar imágenes en Azure Marketplace](./windows/cli-ps-findimage.md) e [Indicación de la información del plan de compra de Azure Marketplace al crear imágenes](marketplace-images.md).


## <a name="image-versions"></a>Versiones de la imagen

Una **versión de la imagen** es lo que se usa para crear una VM. Puede tener varias versiones de una imagen según sea necesario para su entorno. Cuando se usa una **versión de la imagen** para crear una VM, la versión de la imagen se usa para crear nuevos discos para la VM. Las versiones de las imágenes pueden usarse varias veces.

Las propiedades de una versión de imagen son las siguientes:

- Número de versión. Se usa como nombre de la versión de la imagen. Siempre se plasma con el siguiente formato: VersiónPrincipal.VersiónSecundaria.Revisión. Cuando se especifica que se use la versión **más reciente** al crear una VM, la imagen más reciente se elige en función del valor de VersiónPrincipal más alto, seguido del valor de VersiónSecundaria y de Revisión. 
- Origen. El origen puede ser una VM, un disco administrado, una instantánea, una imagen administrada u otra versión de la imagen. 
- Excluir de la versión más reciente. Puede evitar que se use una versión como la versión más reciente de la imagen. 
- Fecha final del ciclo de vida. Indique la fecha final del ciclo de vida de la versión de la imagen. Las fechas finales del ciclo de vida son informativas; los usuarios seguirán teniendo la posibilidad de crear máquinas virtuales a partir de versiones tras alcanzarse la fecha final del ciclo de vida.


## <a name="generalized-and-specialized-images"></a>Imágenes generalizadas y especializadas

Hay dos estados del sistema operativo compatibles con Shared Image Gallery. Normalmente, las imágenes requieren que la máquina virtual usada para crear la imagen se haya generalizado antes de tomar la imagen. La generalización es un proceso que quita la información específica del equipo y del usuario de la máquina virtual. En Windows, se usa la herramienta Sysprep. En el caso de Linux, puede usar [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` o `-deprovision+user` parámetros.

Las máquinas virtuales especializadas no han pasado por un proceso para quitar la información y las cuentas específicas de la máquina. Además, las máquinas virtuales creadas a partir de imágenes especializadas no tienen un `osProfile` asociado a ellas. Esto significa que las imágenes especializadas tendrán algunas limitaciones, además de algunas ventajas.

- Las VM y los conjuntos de escalado creados a partir de imágenes especializadas pueden estar en funcionamiento más rápido. Dado que se crean a partir de un origen que ya ha pasado a través del primer arranque, las VM creadas a partir de estas imágenes se inician más rápido.
- Las cuentas que se podrían usar para iniciar sesión en la máquina virtual también se pueden usar en cualquier máquina virtual creada mediante la imagen especializada que se crea a partir de esa máquina virtual.
- Las máquinas virtuales tendrán el **nombre de equipo** de la máquina virtual de la que se tomó la imagen. Debe cambiar el nombre de equipo para evitar colisiones.
- `osProfile` es la forma en que se pasa información confidencial a la máquina virtual, mediante `secrets`. Esto puede producir problemas al usar KeyVault, WinRM y otras funciones que usan `secrets` en `osProfile`. En algunos casos, puede usar identidades Managed Service Identity (MSI) para solucionar estas limitaciones.

## <a name="regional-support"></a>Compatibilidad regional

Todas las regiones públicas pueden ser regiones de destino, pero ciertas regiones requieren que los clientes sigan un proceso de solicitud para obtener acceso. Para solicitar que se agregue una suscripción a la lista de permitidos para una región, como Centro de Australia y Centro de Australia 2, envíe [una solicitud de acceso](/troubleshoot/azure/general/region-access-request-process).

## <a name="limits"></a>Límites 

Hay límites por suscripción, para implementar los recursos con las galerías de imágenes compartidas:
- 100 galerías de imágenes compartidas por suscripción, por región
- 1000 definiciones de imágenes por suscripción, por región
- 10 000 versiones de imágenes por suscripción, por región
- 10 réplicas de versiones de imágenes por suscripción, por región
- Cualquier disco asociado a la imagen debe tener un tamaño inferior o igual a 1 TB

Para más información, consulte [Comparación del uso de recursos con los límites](../networking/check-usage-against-limits.md) para obtener ejemplos sobre cómo comprobar el uso actual.
 
## <a name="scaling"></a>Ampliación
La galería de imágenes compartidas le permite especificar el número de réplicas de las imágenes que quiere que Azure mantenga. Esto ayuda en los escenarios de implementación de varias VM, ya que las implementaciones de VM se pueden distribuir a las distintas réplicas, lo que reduce la probabilidad de que el proceso de creación de instancias quede limitado por la sobrecarga de una única réplica.

Con la galería de imágenes compartidas, ahora puede implementar hasta 1000 instancias de máquinas virtuales en una conjunto de escalado de máquinas virtuales (a partir de 600 con imágenes administradas). Las réplicas de imágenes proporcionan un mejor rendimiento de implementación, confiabilidad y coherencia.   Puede establecer un número de réplicas diferente en cada región de destino, en función de las necesidades de escala de la región. Dado que cada réplica es una copia en profundidad de la imagen, esto ayuda a escalar las implementaciones linealmente con cada réplica adicional. Aunque entendemos que no hay dos imágenes o regiones iguales, he aquí nuestra guía general sobre cómo usar réplicas en una región:

- En el caso de las implementaciones de conjunto de escalado de máquinas virtuales, se recomienda mantener una réplica por cada 20 máquinas virtuales que cree simultáneamente. Por ejemplo, si va a crear 120 máquinas virtuales simultáneamente mediante la misma imagen en una región, se recomienda conservar al menos seis réplicas de la imagen. 
- Para las implementaciones de un conjunto de escalado de máquinas virtuales, para cada conjunto con hasta 600 instancias, se recomienda conservar al menos una réplica. Por ejemplo, si va a crear cinco conjuntos de escalado de forma simultánea, cada uno con 600 instancias de máquinas virtuales con la misma imagen en una única región, se recomienda conservar al menos cinco réplicas de la imagen. 

Siempre se recomienda aprovisionar en exceso el número de réplicas debido a factores como el tamaño de la imagen, el contenido y el tipo de sistema operativo.

![Gráfico que muestra cómo puede escalar imágenes](./media/shared-image-galleries/scaling.png)

## <a name="make-your-images-highly-available"></a>Hacer que las imágenes tengan alta disponibilidad

[El almacenamiento con redundancia de zona (ZRS) de Azure](https://azure.microsoft.com/blog/azure-zone-redundant-storage-in-public-preview/) proporciona resistencia frente a un error de la zona de disponibilidad en la región. Con la disponibilidad general de la galería de imágenes compartidas, puede elegir almacenar sus imágenes en cuentas de ZRS en regiones con zonas de disponibilidad. 

También puede elegir el tipo de cuenta para cada una de las regiones de destino. El tipo de cuenta de almacenamiento predeterminado es Standard_LRS, pero puede elegir Standard_ZRS para regiones con zonas de disponibilidad. Para más información sobre la disponibilidad regional de ZRS, consulte [Redundancia de datos](../storage/common/storage-redundancy.md).

![Gráfico que muestra ZRS](./media/shared-image-galleries/zrs.png)

## <a name="replication"></a>Replicación
La galería de imágenes compartidas también permite replicar las imágenes en otras regiones de Azure automáticamente. Cada versión de una imagen compartida se puede replicar en distintas regiones en función de lo que sea más conveniente para su organización. Un ejemplo es replicar siempre la imagen más reciente en varias regiones, mientras que todas las versiones anteriores solo están disponibles en una región. Esto puede ayudar a ahorrar costos de almacenamiento para las versiones de imágenes compartidas. 

Las regiones donde puede replicarse la versión de una imagen compartida se pueden actualizar después de su creación. El tiempo necesario para replicar en diferentes regiones depende de la cantidad de datos que se copien y del número de regiones en las que se replicará la versión. En algunos casos, esto puede tardar varias horas. Mientras se produce la replicación, puede ver el estado de replicación por región. Una vez que se complete la replicación de la imagen en una región, podrá implementar una máquina virtual o conjunto de escalado con esa versión de la imagen en la región.

![Gráfico que muestra cómo puede replicar imágenes](./media/shared-image-galleries/replication.png)

## <a name="access"></a>Acceso

Dado que la galería de imágenes compartidas, la definición de la imagen y la versión de la imagen son recursos, pueden compartirse con los controles integrados nativos de Azure RBAC. Con Azure RBAC puede compartir estos recursos con otros usuarios, entidades de servicio y grupos. Incluso se puede compartir el acceso a personas ajenas al inquilino en el que se crearon. Cuando el usuario tiene acceso a la versión de las imágenes compartidas, puede implementar una máquina virtual o un conjunto de escalado de máquinas virtuales.  Aquí está la matriz de uso compartido que ayuda a entender a lo que el usuario tiene acceso:

| Compartido con el usuario     | Galería de imágenes compartidas | Definición de imágenes | Versión de la imagen |
|----------------------|----------------------|--------------|----------------------|
| Galería de imágenes compartidas | Sí                  | Sí          | Sí                  |
| Definición de imágenes     | No                   | Sí          | Sí                  |

Se recomienda el uso compartido en el nivel de la galería para una mejor experiencia. No se recomienda compartir las versiones individuales de la imagen. Para obtener más información sobre Azure RBAC, consulte [Asignación de roles de Azure](../role-based-access-control/role-assignments-portal.md).

Las imágenes también se pueden compartir, a escala, incluso entre inquilinos mediante un registro de aplicación de varios inquilinos. Para obtener más información sobre cómo compartir imágenes entre los inquilinos, consulte "Compartir imágenes de máquina virtual de la galería en inquilinos de Azure" usando la [CLI de Azure](./linux/share-images-across-tenants.md) o [PowerShell](./windows/share-images-across-tenants.md).

## <a name="billing"></a>Facturación
No hay ningún cargo adicional por usar el servicio de la galería de imágenes compartidas. Se le cobrará por los siguientes recursos:
- Costos del almacenamiento de cada réplica. El costo de almacenamiento se cobra como una instantánea y se basa en el tamaño ocupado de la versión de la imagen, el número de réplicas de la versión de la imagen y el número de regiones en que se replica la versión. 
- Cargos de salida de red para la replicación de la primera versión de la imagen desde la región de origen a las regiones replicadas. Las réplicas subsiguientes se tratan dentro de la región, por lo que no habrá ningún cargo adicional. 

Por ejemplo, supongamos que tiene una imagen de un disco de sistema operativo de 127 GB, que solo ocupa 10 GB de almacenamiento, y un disco de datos vacío de 32 GB. El tamaño ocupado de cada imagen solo sería de 10 GB. La imagen se replica en tres regiones y cada una de ellas tiene dos réplicas. Habrá un total de seis instantáneas, y cada una de ellas usará 10 GB. Se le cobrará el costo de almacenamiento de cada instantánea en función del tamaño ocupado de 10 GB. Pagará los cargos de salida de red por la primera réplica que se copie en las dos regiones adicionales. Para más información sobre los precios de las instantáneas de cada región, consulte [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Para más información sobre la salida de la red, consulte [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="updating-resources"></a>Actualización de recursos

Una vez creados, puede realizar algunos cambios en los recursos de la galería de imágenes. Estos se limitan a:
 
Galería de imágenes compartidas:
- Descripción

Definición de la imagen:
- vCPU recomendadas:
- Memoria recomendada
- Descripción
- Fecha final del ciclo de vida

Versión de la imagen:
- Recuento de réplicas regionales
- Regiones de destino
- Excluir de la versión más reciente
- Fecha final del ciclo de vida

## <a name="sdk-support"></a>Compatibilidad con SDK

Los siguientes SDK admiten la creación de galerías de imágenes compartidas:

- [.NET](/dotnet/api/overview/azure/virtualmachines/management)
- [Java](/java/azure/)
- [Node.js](/javascript/api/overview/azure/arm-compute-readme)
- [Python](/python/api/overview/azure/virtualmachines)
- [Go](/azure/go/)

## <a name="templates"></a>Plantillas

Puede crear recursos de galería de imágenes compartidas con plantillas. Hay varias plantillas de Inicio rápido de Azure disponibles: 

- [Creación de una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-create/)
- [Creación de una definición de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Creación de una versión de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes 

* [¿Cómo puedo mostrar todos los recursos de Shared Image Gallery en las suscripciones?](#how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions) 
* [¿Puedo mover una imagen existente a Shared Image Gallery?](#can-i-move-my-existing-image-to-the-shared-image-gallery)
* [¿Puedo crear una versión de la imagen a partir de un disco especializado?](#can-i-create-an-image-version-from-a-specialized-disk)
* [¿Puedo mover el recurso de Shared Image Gallery a otra suscripción después de crearlo?](#can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created)
* [¿Puedo replicar las versiones de mis imágenes en nubes como Azure China 21Vianet, Azure Alemania o la nube de Azure Government?](#can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud)
* [¿Puedo replicar las versiones de mis imágenes en las distintas suscripciones?](#can-i-replicate-my-image-versions-across-subscriptions)
* [¿Puedo compartir versiones de imágenes entre los inquilinos de Azure AD?](#can-i-share-image-versions-across-azure-ad-tenants)
* [¿Cuánto tardan en replicarse las versiones de las imágenes en las regiones de destino?](#how-long-does-it-take-to-replicate-image-versions-across-the-target-regions)
* [¿Cuál es la diferencia entre región de origen y región de destino?](#what-is-the-difference-between-source-region-and-target-region)
* [¿Cómo especifico la región de origen al crear la versión de la imagen?](#how-do-i-specify-the-source-region-while-creating-the-image-version)
* [¿Cómo especifico el número de réplicas de la versión de la imagen que se crearán en cada región?](#how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region)
* [¿Puedo crear la instancia de Shared Image Gallery en una ubicación que no sea en la que se encuentran la definición de la imagen y la versión de la imagen?](#can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version)
* [¿Cuánto cuesta usar Shared Image Gallery?](#what-are-the-charges-for-using-the-shared-image-gallery)
* [¿Qué versión de la API debo usar para crear Shared Image Gallery, así como la definición y la versión de la imagen?](#what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version)
* [¿Qué versión de la API debo usar para crear una máquina virtual compartida o un conjunto de escalado de máquinas virtuales a partir de la versión de la imagen?](#what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version)
* [¿Puedo actualizar mi conjunto de escalado de máquinas virtuales creado mediante una imagen administrada para usar imágenes de Shared Image Gallery?](#can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images)

### <a name="how-can-i-list-all-the-shared-image-gallery-resources-across-subscriptions"></a>¿Cómo puedo mostrar todos los recursos de la galería de imágenes compartidas en las suscripciones?

Para enumerar todos los recursos de Shared Image Gallery en las suscripciones a las que tiene acceso en Azure Portal, siga estos pasos:

1. Abra [Azure Portal](https://portal.azure.com).
1. Desplácese hacia abajo en la página y seleccione **Todos los recursos**.
1. Seleccione todas las suscripciones en las que quiera enumerar todos los recursos.
1. Busque los recursos de tipo **Shared Image Gallery**.
  
Para enumerar todos los recursos de la galería de imágenes compartidas mediante las suscripciones para las que tiene permiso, use el comando siguiente en la CLI de Azure:

```azurecli
   az account list -otsv --query "[].id" | xargs -n 1 az sig list --subscription
```

Para obtener más información, vea **Administrar los recursos de la galería** mediante la [CLI de Azure](update-image-resources-cli.md) o [PowerShell](update-image-resources-powershell.md).

### <a name="can-i-move-my-existing-image-to-the-shared-image-gallery"></a>¿Puedo mover una imagen existente a la galería de imágenes compartidas?
 
Sí. Hay tres escenarios basados en los tipos de imagen que pueda tener.

 Escenario 1: Si tiene una imagen administrada, puede crear una definición de la imagen y versión de la imagen a partir de ella. Para obtener más información, consulte **Migración de una imagen administrada a una versión de la imagen** con la [CLI de Azure](image-version-managed-image-cli.md) o [PowerShell](image-version-managed-image-powershell.md).

 Escenario 2: Si tiene una imagen no administrada, puede crear una imagen administrada a partir de ella y, luego, crear una definición de la imagen y una versión de la imagen a partir de ella. 

 Escenario 3: Si tiene un VHD en el sistema de archivos local, deberá cargar el VHD en una imagen administrada y, luego, crear una definición de la imagen y una versión de la imagen a partir de ella.

- Si el VHD es de una VM Windows, consulte [Carga de un VHD](./windows/upload-generalized-managed.md).
- Si el VHD es para una VM Linux, consulte [Cargar un disco duro virtual](./linux/upload-vhd.md#option-1-upload-a-vhd).

### <a name="can-i-create-an-image-version-from-a-specialized-disk"></a>¿Puedo crear una versión de la imagen desde un disco especializado?

Sí, puede crear una máquina virtual a partir de una imagen especializada mediante la [CLI](vm-specialized-image-version-cli.md), [PowerShell](vm-specialized-image-version-powershell.md) o la API. 

### <a name="can-i-move-the-shared-image-gallery-resource-to-a-different-subscription-after-it-has-been-created"></a>¿Puedo mover el recurso de Shared Image Gallery a otra suscripción después de crearlo?

No, no puede mover el recurso de Shared Image Gallery a otra suscripción. Puede replicar las versiones de la imagen de la galería en otras regiones o copiar una imagen de otra galería mediante la [CLI de Azure](image-version-another-gallery-cli.md) o [PowerShell](image-version-another-gallery-powershell.md).

### <a name="can-i-replicate-my-image-versions-across-clouds-such-as-azure-china-21vianet-or-azure-germany-or-azure-government-cloud"></a>¿Puedo replicar las versiones de mis imágenes en nubes como Azure China 21Vianet, Azure Alemania o la nube de Azure Government?

No, no puede replicar las versiones de la imagen en las nubes.

### <a name="can-i-replicate-my-image-versions-across-subscriptions"></a>¿Puedo replicar las versiones de mis imágenes entre suscripciones?

No, puede replicar las versiones de la imagen en varias regiones de una suscripción y usarla en otras suscripciones a través de RBAC.

### <a name="can-i-share-image-versions-across-azure-ad-tenants"></a>¿Puedo compartir versiones de imágenes entre los inquilinos de Azure AD? 

Sí, puede utilizar RBAC para compartirlo con las personas entre los inquilinos. Pero, para compartir a escala, consulte "Uso compartido de imágenes de la galería entre inquilinos de Azure" con [PowerShell](./windows/share-images-across-tenants.md) o [CLI](./linux/share-images-across-tenants.md).

### <a name="how-long-does-it-take-to-replicate-image-versions-across-the-target-regions"></a>¿Cuánto tarda la replicación de versiones de imágenes en las regiones de destino?

El tiempo de replicación de la versión de una imagen depende totalmente del tamaño de la imagen y del número de regiones en las que se va a replicar. Sin embargo, como práctica recomendada para obtener los mejores resultados, se recomienda mantener una imagen pequeña y las regiones de origen y de destino cerca. Puede comprobar el estado de la replicación mediante la marca -ReplicationStatus.

### <a name="what-is-the-difference-between-source-region-and-target-region"></a>¿Cuál es la diferencia entre la región de origen y la región de destino?

La región de origen es la región en la que se creará la versión de la imagen, y las regiones de destino son las regiones donde se almacenará una copia de la versión de la imagen. Para cada versión de una imagen, solo puede tener una región de origen. Además, asegúrese de pasar la ubicación de la región de origen como una de las regiones de destino cuando cree una versión de la imagen.

### <a name="how-do-i-specify-the-source-region-while-creating-the-image-version"></a>¿Cómo puedo especificar la región de origen al crear la versión de la imagen?

Al crear una versión de la imagen, puede usar la etiqueta **--location** en la CLI y la etiqueta **-Location** en PowerShell para especificar la región de origen. Asegúrese de que la imagen administrada que usa como imagen de base para crear la versión de la imagen esté en la misma ubicación que la ubicación en la que va a crear la versión de la imagen. Además, asegúrese de pasar la ubicación de la región de origen como una de las regiones de destino cuando cree una versión de la imagen.  

### <a name="how-do-i-specify-the-number-of-image-version-replicas-to-be-created-in-each-region"></a>¿Cómo puedo especificar el número de réplicas de la versión de la imagen que se creará en cada región?

Hay dos maneras de especificar el número de réplicas de la versión de la imagen que se creará en cada región:
 
1. El recuento de réplicas regionales que especifica el número de réplicas que desea crear para cada región. 
2. El recuento de réplicas comunes que sea el recuento predeterminado por región en caso de que no se especifique el recuento de réplicas regionales. 

Para especificar el recuento de réplicas regionales, pase la ubicación junto con el número de réplicas que quiere crear en esa región: "Centro-sur de EE. UU. = 2". 

Si el recuento de réplicas regionales no se especifica con cada ubicación, el número predeterminado de réplicas será el recuento de réplicas comunes que especificó. 

Para especificar el recuento de réplicas comunes en la CLI, use el argumento **--replica-count** en el comando `az sig image-version create`.

### <a name="can-i-create-the-shared-image-gallery-in-a-different-location-than-the-one-for-the-image-definition-and-image-version"></a>¿Puedo crear la instancia de Shared Image Gallery en una ubicación que no sea en la que se encuentran la definición de la imagen y la versión de la imagen?

Sí, es posible. Pero como práctica recomendada, le animamos a mantener el grupo de recursos, la galería de imágenes compartidas, la definición de la imagen y la versión de la imagen en la misma ubicación.

### <a name="what-are-the-charges-for-using-the-shared-image-gallery"></a>¿Cuáles son los cargos por uso de la galería de imágenes compartidas?

No hay ningún cargo por usar el servicio de la galería de imágenes compartidas, excepto los cargos de almacenamiento de las versiones de las imágenes y los cargos de salida de red para la replicación de las versiones de las imágenes de la región de origen a las regiones de destino.

### <a name="what-api-version-should-i-use-to-create-shared-image-gallery-and-image-definition-and-image-version"></a>¿Qué versión de la API debo usar para crear Shared Image Gallery, así como la definición y la versión de la imagen?

Para trabajar con galerías de imágenes compartidas, definiciones de imágenes y versiones de imágenes, se recomienda usar la API versión 2018-06-01. El almacenamiento con redundancia de zona (ZRS) requiere la versión de 2019-03-01 o posterior.

### <a name="what-api-version-should-i-use-to-create-shared-vm-or-virtual-machine-scale-set-out-of-the-image-version"></a>¿Qué versión de la API debo usar para crear una máquina virtual compartida o un conjunto de escalado de máquinas virtuales a partir de la versión de la imagen?

Para implementaciones de VM y conjuntos de escalado de máquinas virtuales que usan una versión de la imagen, se recomienda usar la API versión 2018-04-01 o posterior.

### <a name="can-i-update-my-virtual-machine-scale-set-created-using-managed-image-to-use-shared-image-gallery-images"></a>¿Puedo actualizar mi conjunto de escalado de máquinas virtuales creado mediante una imagen administrada para usar imágenes de Shared Image Gallery?

Sí, puede actualizar la referencia de imagen del conjunto de escalado desde una imagen administrada a una imagen de Shared Image Gallery, siempre que el tipo de sistema operativo, la generación de Hyper-V y el diseño del disco de datos coincidan entre las imágenes.

## <a name="troubleshoot-shared-image-gallery-issues"></a>Solución de problemas de Shared Image Gallery
Si tiene problemas para realizar operaciones en los recursos de la galería de imágenes compartidas, consulte la lista de errores comunes en la [guía de solución de problemas](troubleshooting-shared-images.md).

Además, puede publicar y etiquetar la pregunta con `azure-virtual-machines-images` en [Preguntas y respuestas](/answers/topics/azure-virtual-machines-images.html).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo implementar imágenes compartidas mediante la [CLI de Azure](shared-images-cli.md) y [PowerShell](shared-images-powershell.md).
