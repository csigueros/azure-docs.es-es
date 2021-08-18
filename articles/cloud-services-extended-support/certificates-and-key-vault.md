---
title: Almacenamiento y uso de certificados en Azure Cloud Services (soporte extendido)
description: Procesos para el almacenamiento y uso de certificados en Azure Cloud Services (soporte extendido)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 83c455d6a8c952e8f4258dbb90d1d6726e8fcd65
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114447919"
---
# <a name="use-certificates-with-azure-cloud-services-extended-support"></a>Uso de certificados con Azure Cloud Services (soporte extendido)

Key Vault se usa para almacenar certificados asociados a Cloud Services (soporte extendido). Key Vault se puede crear por medio de [Azure Portal](../key-vault/general/quick-create-portal.md) y [PowerShell](../key-vault/general/quick-create-powershell.md). Agregue los certificados a Key Vault y haga referencia a las huellas digitales del certificado en el archivo de configuración de servicio. También debe habilitar Key Vault para los permisos adecuados, de modo que el recurso de Cloud Services (soporte extendido) pueda recuperar el certificado almacenado como secretos de Key Vault.  

## <a name="upload-a-certificate-to-key-vault"></a>Carga de un certificado en Key Vault 

1.  Inicie sesión en Azure Portal y vaya a Key Vault. Si no tiene una instancia de Key Vault configurada, puede optar por crear una en esta misma ventana.

2. Seleccione **Directivas de acceso**.

    :::image type="content" source="media/certs-and-key-vault-1.png" alt-text="Imagen que muestra la selección de Directivas de acceso en la hoja de Key Vault.":::

3. Asegúrese de que las directivas de acceso incluye la siguiente propiedad:
    - **Habilitar el acceso a Azure Virtual Machines para la implementación**

    :::image type="content" source="media/certs-and-key-vault-2.png" alt-text="Imagen que muestra la ventana de directivas de acceso en Azure Portal.":::
 
4.  Seleccione **Certificados**. 

    :::image type="content" source="media/certs-and-key-vault-3.png" alt-text="Imagen que muestra la selección de la opción Certificados en la ventana de directivas de la hoja de Key Vault en Azure Portal.":::

5. Seleccione **Generar o importar**.

    :::image type="content" source="media/certs-and-key-vault-4.png" alt-text="Imagen que muestra la selección de la opción Generar o importar.":::

4.  Complete la información necesaria para terminar de cargar el certificado. El certificado debe estar en formato **.PFX**.

    :::image type="content" source="media/certs-and-key-vault-5.png" alt-text="Imagen que muestra la ventana de importación en Azure Portal.":::

5.  Agregue los detalles del certificado a su rol en el archivo de configuración de servicio (.cscfg). Asegúrese de que la huella digital del certificado en Azure Portal coincide con la huella digital del archivo de configuración de servicio (.cscfg). 
    
    ```json
    <Certificate name="<your cert name>" thumbprint="<thumbprint in key vault" thumbprintAlgorithm="sha1" /> 
    ```
6.  Para la implementación a través de la plantilla de ARM, se puede encontrar el objeto certificateUrl en el certificado del almacén de claves etiquetado como Identificador secreto

    :::image type="content" source="media/certs-and-key-vault-6.png" alt-text="Imagen muestra el campo Identificador de secreto en el almacén de claves.":::

## <a name="next-steps"></a>Pasos siguientes 
- Revise los [requisitos previos de implementación](deploy-prerequisite.md) de Cloud Services (soporte extendido).
- Vea las [preguntas más frecuentes](faq.yml) sobre Cloud Services (soporte extendido).
- Implemente una instancia de Cloud Services (soporte extendido) mediante [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), una [plantilla](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
