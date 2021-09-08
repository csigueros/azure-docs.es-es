---
title: Solución de problemas de Blob Storage para Azure Stack Edge Pro GPU| Microsoft Docs
description: Describe cómo solucionar problemas con Blob Storage para un dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 2a77d4992d5ee2e505f9e3f29112d45c3776d8d2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438768"
---
# <a name="troubleshoot-blob-storage-issues-for-an-azure-stack-edge-device"></a>Solución de problemas de Blob Storage para un dispositivo Azure Stack Edge 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo solucionar los problemas de un dispositivo Azure Stack Edge Pro con GPU. 

## <a name="errors-for-blob-storage-on-device"></a>Errores de Blob Storage en el dispositivo 

Estos son los errores relacionados con el almacenamiento de blobs en el dispositivo de Azure Stack Edge/Data Box Gateway.

| **Problemas o errores** |  **Resolución** | 
|--------------------|-----------------|
|No se han podido recuperar los recursos secundarios. El valor de uno de los encabezados HTTP no tiene el formato correcto.| En el menú **Editar**, seleccione **API de Azure Stack de destino**. Luego, reinicie el Explorador de Azure Storage.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Compruebe que el nombre del punto de conexión `<accountname>.blob.<serialnumber>.microsoftdatabox.com` se haya agregado al archivo de hosts en esta ruta de acceso: `C:\Windows\System32\drivers\etc\hosts` en Windows o `/etc/hosts` en Linux.|
|No se han podido recuperar los recursos secundarios.<br> Detalles: un certificado autofirmado |Importe el certificado SSL para el dispositivo en el Explorador de Azure Storage: <ol><li>[Genere y descargue el certificado](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>En el menú **Editar**, seleccione Certificados SSL y, después, **Importar certificados**.</li></ol>|
|El comando de AzCopy deja de responder durante un minuto antes de mostrar este error:<br>`Failed to enumerate directory https://… The remote name could not be resolved <accountname>.blob.<serialnumber>.microsoftdatabox.com`|Compruebe que el nombre del punto de conexión `<accountname>.blob.<serialnumber>.microsoftdatabox.com` se haya agregado al archivo de hosts en `C:\Windows\System32\drivers\etc\hosts`.|
|El comando de AzCopy deja de responder durante un minuto antes de mostrar este error:<br>`Error parsing source location. The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel`. |Importe el certificado SSL para el dispositivo en el Explorador de Azure Storage: <ol><li>[Genere y descargue el certificado](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>En el menú **Editar**, seleccione Certificados SSL y, después, **Importar certificados**.</li></ol>|
|El comando de AzCopy deja de responder durante 20 minutos antes de mostrar este error:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`. |Compruebe que el nombre del punto de conexión `<accountname>.blob.<serialnumber>.microsoftdatabox.com` se haya agregado al archivo de hosts en `/etc/hosts`.|
|El comando de AzCopy deja de responder durante 20 minutos antes de mostrar este error:<br>`Error parsing source location… The SSL connection could not be established`. |Importe el certificado SSL para el dispositivo en el Explorador de Azure Storage: <ol><li>[Genere y descargue el certificado](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).</li><li>En el menú **Editar**, seleccione Certificados SSL y, después, **Importar certificados**.</li></ol>|
|El comando de AzCopy deja de responder durante 20 minutos antes de mostrar este error:<br>`Error parsing source location https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>. No such device or address`|Compruebe que el nombre del punto de conexión `<accountname>.blob.<serialnumber>.microsoftdatabox.com` se haya agregado al archivo de hosts en `/etc/hosts`.|
|El comando de AzCopy deja de responder durante 20 minutos antes de mostrar este error: `Error parsing source location… The SSL connection could not be established`.|Importe el certificado SSL para el dispositivo en el almacén de certificados del sistema. Para obtener más información, consulte [Descargar certificado](../databox/data-box-deploy-copy-data-via-rest.md#download-certificate).|
|El valor de uno de los encabezados HTTP no tiene el formato correcto.|Data Box no admite la versión instalada de la biblioteca de Microsoft Azure Storage para Python no es compatible con Azure Stack Edge. Para ver las versiones de biblioteca admitidas, consulte [Bibliotecas cliente de Azure compatibles](azure-stack-edge-gpu-system-requirements-rest.md#supported-azure-client-libraries).|
|… [SSL: CERTIFICATE_VERIFY_FAILED]…| Antes de ejecutar Python, establezca la variable de entorno REQUESTS_CA_BUNDLE a la ruta de acceso del archivo de certificado SSL con codificación Base64 (consulte [Descargar el certificado](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)). Por ejemplo, ejecute:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer`<br>`python`<br>O bien, agregue el certificado al almacén de certificados del sistema y, a continuación, establezca esta variable de entorno a la ruta de acceso de dicho almacén. Por ejemplo, en Ubuntu:<br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt`<br>`python`|
|Se agota el tiempo de espera de conexión.|Inicie sesión en el dispositivo y compruebe si está desbloqueado. Cada vez que se reinicia el dispositivo, permanece bloqueado hasta que alguien inicia sesión.|
|No se pudo crear ni actualizar el valor "%ObjectName;" de storageAccount en el dispositivo "%DeviceName;". Asegúrese de que la clave de acceso para la cuenta de almacenamiento es válida. Si es necesario, actualice la clave en el dispositivo.|Sincronice las claves de la cuenta de almacenamiento. Siga los pasos que se describen [aquí](azure-stack-edge-gpu-manage-storage-accounts.md#sync-storage-keys).|

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de errores de carga y actualización de dispositivos](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-device-upload-and-refresh-errors).
