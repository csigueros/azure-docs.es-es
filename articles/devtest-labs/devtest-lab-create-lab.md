---
title: Creación de un laboratorio
description: Este artículo le guiará por el proceso de creación de un laboratorio mediante Azure Portal y Azure DevTest Labs.
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: 68b560125a4cacceee613b5c7c9bfaafc5c95c06
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602904"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Creación de un laboratorio con Laboratorios de desarrollo y pruebas de Azure

En Azure Dev/Test Labs, un laboratorio es la infraestructura que abarca un grupo de recursos, como máquinas virtuales (VM), que permite una mejor administración de dichos recursos mediante la especificación de límites y cuotas. Este artículo le guiará a través del proceso de creación de un laboratorio mediante Azure Portal.

## <a name="prerequisites"></a>Prerequisites

Para crear un laboratorio necesitará:

* Suscripción a Azure. Para información sobre las opciones de compra de Azure, consulte [Instrucciones para contratar Azure](https://azure.microsoft.com/pricing/purchase-options/) o [Evaluación gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/). Debe ser el propietario de la suscripción para crear el laboratorio.

## <a name="get-started-with-azure-devtest-labs-in-minutes"></a>Introducción a Azure DevTest Labs en cuestión de minutos

Al hacer clic en el vínculo siguiente, se le transferirá a la página de Azure Portal donde podrá empezar a crear un laboratorio nuevo en Azure DevTest Labs.

[Introducción a Azure DevTest Labs en cuestión de minutos](https://go.microsoft.com/fwlink/?LinkID=627034&clcid=0x409)

## <a name="fill-out-settings-for-your-new-account"></a>Configuración de la cuenta nueva

En la página **Creación de una instancia de DevTest Labs**, rellene la configuración siguiente.

> [!TIP]
> En la parte inferior de cada página, encontrará un vínculo en el que podrá **descargar una plantilla para la automatización**.

### <a name="basic-settings"></a>Configuración básica

De manera predeterminada, ve la pestaña **Configuración básica**. 

![configuración básica](./media/devtest-lab-create-lab/basic-settings.png)

Rellene estos valores:

|Nombre|Descripción|
|---|---|
|**Suscripción** | Necesario. Seleccione una **suscripción** para asociar al laboratorio.|
|**Grupos de recursos**| Necesario. Escriba un **nombre para el grupo de recursos** para el laboratorio. Cree uno si no existe.|
|**Nombre del laboratorio**| Necesario. Escriba un **nombre** para el laboratorio.|
|**Ubicación**|Necesario. Seleccione una ubicación en la que se va a almacenar el laboratorio.|
|**Entornos públicos**| Consulte [Configuración y uso de entornos públicos](devtest-lab-configure-use-public-environments.md).

### <a name="auto-shutdown-settings"></a>Configuración de apagado automático

Cambie a la página **Apagado automático** para ver su configuración. El apagado automático permite apagar automáticamente todas las máquinas de un laboratorio a una hora programada cada día.

![Pestaña Apagado automático](./media/devtest-lab-create-lab/auto-shutdown.png)

En la página, puede habilitar **Apagado automático** y definir los parámetros del apagado automático de todas las máquinas virtuales del laboratorio. La característica de apagado automático es principalmente una característica de ahorro de costos por la que puede especificar cuándo desea que la máquina virtual se apague automáticamente. Para cambiar la configuración del apagado automático después de crear el laboratorio, siga los pasos que se describen en el artículo [Administración de todas las directivas para un laboratorio de Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown).

### <a name="networking"></a>Redes

Al crear un laboratorio, se creará automáticamente una red predeterminada. Cambie a la pestaña **Redes** para cambiar o modificar la configuración como desee. Por ejemplo, seleccione una red virtual existente.

![Pestaña Redes ](./media/devtest-lab-create-lab/networking.png)

### <a name="tags"></a>Etiquetas

Escriba la información de **NOMBRE** y **VALOR** en las **Etiquetas**, si desea crear un etiquetado personalizado que se agregue a cada recurso que vaya a crear en el laboratorio. Las etiquetas son útiles para ayudarle a administrar y organizar los recursos del laboratorio por categoría. Para más información acerca de las etiquetas, incluido cómo agregar etiquetas después de crear el laboratorio, consulte [Incorporación de etiquetas a un laboratorio](devtest-lab-add-tag.md).

![Pestaña Etiquetas ](./media/devtest-lab-create-lab/tags.png)

### <a name="review-and-create"></a>Revisar y crear

Cuando haya terminado, seleccione **Crear**. Para supervisar el estado del proceso de creación de un laboratorio, inspeccione el área **Notificaciones** en la parte superior de la página del portal. 

![Pestaña Crear](./media/devtest-lab-create-lab/create-1.png)

## <a name="completed-the-creation"></a>Creación finalizada

Una vez finalizada, el botón **Ir al recurso** aparecerá en la parte inferior de la página y en la ventana de notificación. Como alternativa puede actualizar la página **DevTest Labs** para ver el laboratorio recién creado en la lista de laboratorios.  

![Creación del servicio](./media/devtest-lab-create-lab/create-2.png)

Presione el botón **Ir al recurso** y pasará a la página principal de la cuenta nueva de DevTest Labs.

![Resource](./media/devtest-lab-create-lab/go-to-resource.png)

También puede buscar **DevTest Labs** en Azure Portal. Seleccione la cuenta nueva en la lista y vaya a la página principal. 

![Servicio creado](./media/devtest-lab-create-lab/created.png)

## <a name="next-steps"></a>Pasos siguientes

Una vez creado el laboratorio, le presentamos algunos pasos que se deben tener en cuenta:

* [Acceso seguro a un laboratorio](devtest-lab-add-devtest-user.md)
* [Definición de directivas de laboratorio](devtest-lab-set-lab-policy.md)
* [Creación de una plantilla de laboratorio](devtest-lab-create-template.md)
* [Creación de artefactos personalizados para máquinas virtuales](devtest-lab-artifact-author.md)
* [Adición de una máquina virtual a un laboratorio](devtest-lab-add-vm.md)
