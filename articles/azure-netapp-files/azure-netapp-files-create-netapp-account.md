---
title: Creación de una cuenta de NetApp para obtener acceso a Azure NetApp Files | Microsoft Docs
description: Describe cómo tener acceso a Azure NetApp Files y crear una cuenta de NetApp para poder configurar un grupo de capacidad y crear un volumen.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: 994af6291f55e9f1dd44f2f522f9273639531257
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407064"
---
# <a name="create-a-netapp-account"></a>Creación de una cuenta de NetApp
La creación de una cuenta de NetApp le permite configurar un grupo de capacidad y crear después un volumen. Use la hoja de Azure NetApp Files para crear una nueva cuenta de NetApp.

## <a name="before-you-begin"></a>Antes de empezar

También debe haber registrado su suscripción para usar el proveedor de recursos de NetApp. Consulte [Registro del proveedor de recursos de NetApp](azure-netapp-files-register.md).

## <a name="steps"></a>Pasos 

1. Inicie sesión en Azure Portal. 
2. Acceda a la hoja de Azure NetApp Files mediante uno de los métodos siguientes:  
   * Busque **Azure NetApp Files** en el cuadro de búsqueda de Azure Portal.  
   * Haga clic en **Todos los servicios** en la navegación y, a continuación, filtre Azure NetApp Files.  

   Puede incluir en "Favoritos" la hoja de Azure NetApp Files haciendo clic en el icono de estrella. 

3. Haga clic en **+ Agregar** para crear una nueva cuenta de NetApp.  
   Aparecerá la ventana de la nueva cuenta de NetApp.  

4. Especifique la siguiente información acerca de la cuenta de NetApp: 
   * **Nombre de cuenta**  
     Especifique un nombre único para la suscripción.
   * **Suscripción**  
     Seleccione una suscripción de las existentes.
   * **Grupo de recursos**   
     Use un grupo de recursos existente o cree uno.
   * **Ubicación**  
     Seleccione la región donde desea que la cuenta y sus recursos secundarios se encuentren.  

     ![Nueva cuenta de NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Haga clic en **Crear**.     
   La cuenta de NetApp que creó aparece ahora en la hoja de Azure NetApp Files. 

> [!NOTE] 
> Si no se le ha concedido acceso al servicio Azure NetApp Files, recibirá el siguiente error al intentar crear la primera cuenta de NetApp:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Pasos siguientes  

[Creación de un grupo de capacidad](azure-netapp-files-set-up-capacity-pool.md)

