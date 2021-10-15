---
title: Registro del proveedor de recursos de NetApp para usarlo con Azure NetApp Files | Microsoft Docs
description: Obtenga información sobre cómo registrar el proveedor de recursos de NetApp para Azure NetApp Files.
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
ms.openlocfilehash: b8b70a06a6bda9757f9019a6f753af226235a985
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407748"
---
# <a name="register-for-netapp-resource-provider"></a>Registro del proveedor de recursos de NetApp

Para usar el servicio Azure NetApp Files, debe registrar el proveedor de recursos de NetApp.

1. En Azure Portal, haga clic en el icono de Azure Cloud Shell en la esquina superior derecha:

      ![Icono de Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Si tiene varias suscripciones en su cuenta de Azure, seleccione una que se haya aprobado para Azure NetApp Files:
    
    ```azurecli
    az account set --subscription <subscriptionId>
    ```

3. En la consola de Azure Cloud Shell, escriba el siguiente comando para comprobar que su suscripción se ha aprobado:
    
    ```azurecli
    az feature list | grep NetApp
    ```

   La salida del comando tiene el siguiente aspecto:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` es su identificador de suscripción.



4. En la consola de Azure Cloud Shell, escriba el comando siguiente para registrar el proveedor de recursos de Azure: 
    
    ```azurecli
    az provider register --namespace Microsoft.NetApp --wait
    ```

   El parámetro `--wait` le indica a la consola que espere a que se complete el registro. El proceso de registro puede tardar algún tiempo en completarse.

5. En la consola de Azure Cloud Shell, escriba el comando siguiente para comprobar que se ha registrado el proveedor de recursos de Azure: 
    
    ```azurecli
    az provider show --namespace Microsoft.NetApp
    ```

   La salida del comando tiene el siguiente aspecto:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` es su identificador de suscripción.  El valor del parámetro `state` indica `Registered`.

6. Desde Azure Portal, haga clic en la hoja **Suscripciones**.
7. En la hoja Suscripciones, haga clic en su identificador de suscripción. 
8. En la configuración de la suscripción, haga clic en **Proveedores de recursos** para comprobar que el proveedor de Microsoft.NetApp muestra el estado registrado: 

      ![Microsoft.NetApp registrado](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Pasos siguientes

* [Creación de una cuenta de NetApp](azure-netapp-files-create-netapp-account.md)
* [Creación de una solicitud de soporte técnico de Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
