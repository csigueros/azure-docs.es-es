---
title: Creación de un clúster de Pivotal Cloud Foundry en Azure
description: Aprenda a configurar los parámetros necesarios para aprovisionar un clúster de Pivotal Cloud Foundry (PCF) en Azure.
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 1ed9dbef648eace79807f06f034ba02e8bc67817
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655873"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Creación de un clúster de Pivotal Cloud Foundry en Azure

En este tutorial se proporcionan pasos rápidos para crear y generar los parámetros necesarios para aprovisionar un clúster de Pivotal Cloud Foundry (PCF) en Azure. Para encontrar la solución de Pivotal Cloud Foundry, realice una búsqueda en Azure Marketplace.

![Búsqueda de Pivotal Cloud Foundry en Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Generación de una clave pública SSH

Hay varias maneras de generar una clave pública de Secure Shell (SSH) mediante Windows, Mac o Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Para más información, consulte [Uso de claves SSH con Windows en Azure](../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

> [!NOTE]
>
> Para crear una entidad de servicio, necesita un permiso del propietario de cuenta. Además, puede escribir un script para automatizar la creación de la entidad de servicio. Por ejemplo, puede usar [az ad sp create-for-rbac](/cli/azure/ad/sp) de la CLI de Azure.

1. Inicie sesión en la cuenta de Azure.

    ```azurecli
    az login
    ```

    ![Inicio de sesión de la CLI de Azure](media/deploy/az-login-output.png )
 
    Copie el valor de “id” como **identificador de suscripción** y el valor de "tenantId" para usarlo más adelante.

2. Establezca la suscripción predeterminada de esta configuración.

    ```azurecli
    az account set -s {id}
    ```

3. Cree una aplicación de Azure Active Directory para PCF. Especifique una contraseña alfanumérica única. Almacene la contraseña como **clientSecret** para usarla más adelante.

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    Copie el valor "appId" de la salida como el valor de **ClientID** que usará más adelante.

    > [!NOTE]
    >
    > Elija su propia página principal de la aplicación y el URI de identificador, por ejemplo, http\://www\.contoso.com.

4. Cree una entidad de servicio con su nuevo identificador de la aplicación.

    ```azurecli
    az ad sp create --id {appId}
    ```

5. Establezca el rol de permiso de la entidad de servicio como colaborador.

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    O también puede usar

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![Asignación de rol de la entidad de servicio](media/deploy/svc-princ.png )

6. Compruebe que puede iniciar sesión correctamente a la entidad de servicio utilizando el identificador de la aplicación, la contraseña y el identificador del inquilino.

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. Cree un archivo .json con el formato siguiente. Utilice los valores del **identificador de suscripción**, **tenantID**, **clientID** y **clientSecret** que ha copiado anteriormente. Guarde el archivo.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Obtención del token de Pivotal Network

1. Regístrese o inicie sesión en su cuenta de [Pivotal Network](https://network.pivotal.io).
2. Seleccione el nombre de perfil en la esquina superior derecha de la página. Seleccione **Editar perfil**.
3. Desplácese hasta la parte inferior de la página y copie el valor **LEGACY API TOKEN** (TOKEN DE API HEREDADO). Este valor es el valor del **token de Pivotal Network** que usará más adelante.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Aprovisionamiento de un clúster Cloud Foundry en Azure

Ahora tiene todos los parámetros que necesita para aprovisionar su clúster de Pivotal Cloud Foundry en Azure.
Especifique los parámetros y cree el clúster de Pivotal Cloud Foundry.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Comprobación de la implementación, e inicio de sesión en Pivotal Ops Manager

1. El clúster de Pivotal Cloud Foundry debe mostrar un estado de implementación.

    ![Estado de implementación de Azure](media/deploy/deployment.png )

2. Seleccione el vínculo **Implementaciones** en el panel de navegación de la izquierda para obtener las credenciales de PCF Ops Manager. Seleccione el **nombre de la implementación** en la página siguiente.
3. En el panel de navegación izquierdo, seleccione vínculo **Outputs** (Salidas) para mostrar la dirección URL, el nombre de usuario y la contraseña para PCF Ops Manager. El valor "OPSMAN-FQDN" es la dirección URL.
 
    ![Salida de la implementación de Cloud Foundry](media/deploy/deploy-outputs.png )
 
4. Inicie la dirección URL en un explorador web. Escriba las credenciales del paso anterior para iniciar sesión.

    ![Página de inicio de sesión de Pivotal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Si se produce un error en el Explorador de Internet Explorer debido a un mensaje de advertencia de sitio no seguro, seleccione **Más información** y vaya a la página web. En Firefox, haga clic en **Avanzadas** y agregue la certificación para continuar.

5. La instancia de PCF Ops Manager muestra las instancias de Azure implementadas. Ahora puede implementar y administrar aquí las aplicaciones.
               
    ![Instancia de Azure implementada en Pivotal](media/deploy/ops-mgr.png )
