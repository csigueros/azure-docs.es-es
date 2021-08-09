---
title: Aplicación de la extensión Key Vault VM en Azure Cloud Services (soporte extendido)
description: Habilitación de la extensión Key Vault VM en Cloud Services (soporte extendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: shisriva
ms.author: shisriva
ms.reviewer: gachandw
ms.date: 05/12/2021
ms.custom: ''
ms.openlocfilehash: 27a25cbff54acda871530daca455045e5519c936
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110105432"
---
# <a name="apply-the-key-vault-vm-extension-to-azure-cloud-services-extended-support"></a>Aplicación de la extensión Key Vault VM en Azure Cloud Services (soporte extendido)

## <a name="what-is-the-key-vault-vm-extension"></a>¿Cuál es la extensión Key Vault VM?
La extensión Key Vault VM proporciona una actualización automática de los certificados almacenados en Azure Key Vault. En concreto, la extensión supervisa una lista de certificados observados que se encuentran en almacenes de claves y, cuando detecta un cambio, recupera e instala los certificados correspondientes. Para más información, consulte [Extensión Key Vault VM para Windows](../virtual-machines/extensions/key-vault-windows.md).

## <a name="whats-new-in-the-key-vault-vm-extension"></a>Novedades de la extensión Key Vault VM
La extensión Key Vault VM ahora se admite en la plataforma Azure Cloud Services (soporte extendido) para habilitar la administración de certificados de un extremo a otro. Ahora, la extensión puede extraer certificados de una instancia de Key Vault configurada en un intervalo de sondeo predefinido e instalarlos para que los use el servicio. 

## <a name="how-can-i-leverage-the-key-vault-vm-extension"></a>¿Cómo puedo aprovechar la extensión Key Vault VM?
En el siguiente tutorial se muestra cómo instalar la extensión Key Vault VM en los servicios PaaSV1 mediante la creación previa de un certificado de arranque en un almacén para obtener de AAD un token que le ayudará en la autenticación de la extensión con el almacén. Una vez que se haya configurado el proceso de autenticación y se haya instalado la extensión, los certificados más recientes se retirarán automáticamente a intervalos de sondeo normales. 


## <a name="prerequisites"></a>Requisitos previos 
Para usar la extensión Azure Key Vault VM, es preciso tener un inquilino de Azure Active Directory. Para más información sobre cómo configurar un nuevo inquilino de Active Directory, consulte el artículo sobre la [configuración del inquilino de AAD](../active-directory/develop/quickstart-create-new-tenant.md)

## <a name="enable-the-azure-key-vault-vm-extension"></a>Habilitación de la extensión Azure Key Vault VM

1. [Genere un certificado](../key-vault/certificates/create-certificate-signing-request.md) en el almacén y descargue el archivo .cer para dicho certificado.

2. En [Azure Portal](https://portal.azure.com), vaya a **Registros de aplicaciones**.
    
    :::image type="content" source="media/app-registration-0.jpg" alt-text="Muestra la selección de un registro de aplicaciones en el portal.":::
    

3. En la página Registros de aplicaciones, seleccione **Nuevo registro** en la esquina superior izquierda de la página
    
    :::image type="content" source="media/app-registration-1.png" alt-text="Muestra Registros de aplicaciones en Azure Portal.":::

4. En la página siguiente puede rellenar el formulario y completar la creación de la aplicación.

5. Cargue el archivo .cer del certificado en el portal de aplicaciones de Azure Active Directory.

    - Opcionalmente, también puede aprovechar la [característica de notificación de Event Grid en Key Vault](https://azure.microsoft.com/updates/azure-key-vault-event-grid-integration-is-now-available/) para cargar el certificado.  

6. Conceda a Azure Active Directory permisos de enumeración u obtención de secretos de la aplicación de Azure Active Directory:
    - Si usa la versión preliminar de RBAC, busque el nombre de la aplicación de AAD que ha creado y asígnele el rol de Usuario de secretos de almacén de claves (versión preliminar).
    - Si usa directivas de acceso al almacén, asigne los permisos **Secret-Get** a la aplicación de AAD que ha creado. Para más información, consulte [Asignación de directivas de acceso](../key-vault/general/assign-access-policy-portal.md).

7. Instale la primera versión de los certificados creados en el primer paso y la extensión Key Vault VM mediante la plantilla de Azure Resource Manager, como se muestra a continuación:

    ```json
        {
       "osProfile":{
          "secrets":[
             {
                "sourceVault":{
                   "id":"[parameters('sourceVaultValue')]"
                },
                "vaultCertificates":[
                   {
                      "certificateUrl":"[parameters('bootstrpCertificateUrlValue')]"
                   }
                ]
             }
          ]
       }{
          "name":"KVVMExtensionForPaaS",
          "properties":{
             "type":"KeyVaultForPaaS",
             "autoUpgradeMinorVersion":true,
             "typeHandlerVersion":"1.0",
             "publisher":"Microsoft.Azure.KeyVault",
             "settings":{
                "secretsManagementSettings":{
                   "pollingIntervalInS":"3600",
                   "certificateStoreName":"My",
                   "certificateStoreLocation":"LocalMachine",
                   "linkOnRenewal":false,
                   "requireInitialSync":false, 
                   "observedCertificates":"[parameters('keyVaultObservedCertificates']"
                },
                "authenticationSettings":{
                   "clientId":"Your AAD app ID",
                   "clientCertificateSubjectName":"Your boot strap certificate subject name [Do not include the 'CN=' in the subject name]"
                }
             }
          }
       }
    ```
    Es posible que tenga que especificar el almacén de certificados para el certificado de arranque en ServiceDefinition.csdef como se indica a continuación:
    
    ```xml
        <Certificates>
                 <Certificate name="bootstrapcert" storeLocation="LocalMachine" storeName="My" />
        </Certificates> 
    ```

## <a name="next-steps"></a>Pasos siguientes
Mejore la implementación mediante la [habilitación de la supervisión en Cloud Services (soporte extendido)](enable-alerts.md)