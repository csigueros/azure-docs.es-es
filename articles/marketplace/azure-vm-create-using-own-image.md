---
title: Creación de una oferta de máquina virtual de Azure en Azure Marketplace mediante su propia imagen
description: Publique una oferta de máquina virtual de Azure en Azure Marketplace mediante su propia imagen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 11/10/2021
ms.openlocfilehash: 9aa76bf3c26043a93407ee1543225028e795bb9f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315376"
---
# <a name="create-a-virtual-machine-using-your-own-image"></a>Creación de una máquina virtual mediante su propia imagen

En este artículo se describe cómo publicar una imagen de máquina virtual que se creó en el entorno local.

## <a name="bring-your-image-into-azure"></a>Incorporación de su imagen en Azure

Cargue el VHD en una instancia de Azure Compute Gallery (anteriormente denominada Shared Image Gallery)

1. En Azure Portal, busque **Instancias de Azure Compute Gallery**.
2. Cree o use una instancia de Azure Compute Gallery existente. Se recomienda crear una instancia de Azure Compute Gallery independiente para las imágenes que se publican en Marketplace.
3. Cree o use una definición de imagen existente.
4. Seleccione **Crear una versión**.
5. Elija la región y la versión de la imagen.
6. Si el disco duro virtual aún no se ha cargado en Azure Portal, elija **Blobs de almacenamiento (VHD)** como **Origen** y, a continuación, **Examinar**. Puede crear una **cuenta de almacenamiento** y un **contenedor de almacenamiento** si no los ha creado anteriormente. Cargue el disco duro virtual.
7. Seleccione **Revisar + crear**. Una vez que finalice la validación, seleccione **Crear**.

> [!TIP]
> La cuenta del editor debe tener acceso de "Propietario" para publicar la imagen de Azure Compute Gallery. Si es necesario, siga los pasos de la sección siguiente, **Definición de los permisos correctos** para conceder acceso.

## <a name="set-the-right-permissions"></a>Definición de los permisos correctos

Si la cuenta del Centro de partners es el propietario de la suscripción que hospeda Azure Compute Gallery, no se necesita nada más para los permisos.

Si solo tiene acceso de lectura a la suscripción, use una de las dos opciones siguientes.

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>Opción uno: pedir al propietario que conceda permiso de propietario

Pasos para que el propietario conceda permiso de propietario:

1. Vaya a Azure Compute Gallery
2. Seleccione **Control de acceso** (IAM) en el panel de la izquierda.
3. Seleccione **Agregar** y, a continuación, **Agregar asignación de roles**.<br>
    :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Se muestra la ventana Agregar asignación de roles.":::
1. En **Rol**, seleccione **Propietario**.
1. En **Asignar acceso a**, seleccione **User, group, or service principle** (Usuario, grupo o entidad de servicio).
1. En **Seleccionar**, escriba el correo electrónico de Azure de la persona que publicará la imagen.
1. Seleccione **Guardar**.

### <a name="option-two--run-a-command"></a>Opción dos: ejecutar un comando

Pida al propietario que ejecute cualquiera de estos comandos (en cualquier caso, use el valor de SusbscriptionId de la suscripción en la que creó la instancia de Azure Compute Gallery).

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> No es necesario generar URI de SAS, ya que ahora puede publicar una imagen de Azure Compute Gallery en el Centro de partners sin usar API. <br/> <br/>Si *va* a publicar mediante API, deberá generar URI de SAS en lugar de usar una instancia de Azure Compute Gallery. Consulte [Generación de un URI de SAS para una imagen de máquina virtual](azure-vm-get-sas-uri.md).

## <a name="next-steps"></a>Pasos siguientes

- [Pruebe la imagen de la máquina virtual](azure-vm-image-test.md) para asegurarse de que cumple los requisitos de publicación de Azure Marketplace (opcional).
- Si no quiere probar la imagen de la VM, inicie sesión en el [Centro de partners](https://go.microsoft.com/fwlink/?linkid=2165935) y publique la imagen de Azure Compute Gallery.
- Si tiene dificultades para crear un disco duro virtual basado en Azure, consulte las [preguntas frecuentes que pueden surgir al crear una máquina virtual en Azure Marketplace](azure-vm-create-faq.yml).
