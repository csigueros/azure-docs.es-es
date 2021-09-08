---
title: Uso de certificados con el dispositivo Azure Stack Edge Pro con GPU | Microsoft Docs
description: Descripción del uso de certificados con el dispositivo Azure Stack Edge Pro con GPU, por ejemplo, por qué usarlos, qué tipo usar y cómo cargarlos en el dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: d39b1f1b4220c0899cb649f0544bc7da94f20c09
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113361610"
---
# <a name="upload-import-and-export-certificates-on-azure-stack-edge-pro-gpu"></a>Carga, importación y exportación de certificados en Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Para garantizar una comunicación segura y de confianza entre un dispositivo Azure Stack Edge y los clientes que se conectan a él, puede usar certificados autofirmados o traer sus propios certificados. En este artículo se describe cómo administrar estos certificados, lo que incluye cómo cargar, importar y exportar estos certificados, o ver su fecha de expiración.

Para más información sobre cómo crear estos certificados, consulte [Creación de certificados mediante Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md).


## <a name="upload-certificates-on-your-device"></a>Carga de certificados en el dispositivo

Si trae sus propios certificados, los que ha creado para el dispositivo de manera predeterminada residen en el **almacén Personal** del cliente. Estos certificados tienen que exportarse en el cliente en archivos de formato adecuado que se puedan cargar en el dispositivo.


### <a name="prerequisites"></a>Requisitos previos

Antes de cargar los certificados raíz y los certificados de punto de conexión en el dispositivo, asegúrese de que los certificados se exportan en el formato adecuado.

- El certificado raíz tiene que exportarse como archivo de formato DER con la extensión `.cer`. Para pasos detallados, consulte [Exportación de certificados con formato DER](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format).
- Los certificados de punto de conexión se tienen que exportar como archivos *.pfx* con claves privadas. Para pasos detallados, consulte [ Exportación de certificados como un archivo *.pfx* con claves privadas](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key). 

### <a name="upload-certificates"></a>Carga de certificados 

Para cargar los certificados raíz y de punto de conexión en el dispositivo, use la opción **+ Agregar certificado** de la página **Certificados** de la interfaz de usuario web local. Siga estos pasos:

1. Cargue primero los certificados raíz. En la interfaz de usuario web local, vaya a **Certificados > + Agregar certificado**.

    ![Incorporación del certificado de cadena de firma 1](media/azure-stack-edge-series-manage-certificates/add-cert-1.png)

2. A continuación, cargue los certificados de punto de conexión. 

    ![Incorporación del certificado de cadena de firma 2](media/azure-stack-edge-series-manage-certificates/add-cert-2.png)

    Elija los archivos de certificado en formato *.pfx* y escriba la contraseña que proporcionó al exportar el certificado. El certificado de Azure Resource Manager puede tardar unos minutos en aplicarse.

    Si la cadena de firma no se actualiza primero e intenta cargar los certificados de punto de conexión, se producirá un error.

    ![Error al aplicar el certificado](media/azure-stack-edge-series-manage-certificates/apply-cert-error-1.png)

    Vuelva y cargue el certificado de cadena de firma; después, cargue y aplique los certificados de punto de conexión.

> [!IMPORTANT]
> Si cambian el nombre del dispositivo o el dominio DNS cambian, deben crearse certificados nuevos. Los certificados de cliente y los certificados de dispositivo deben actualizarse con el nombre de dispositivo y dominio DNS nuevos. 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>Importación de certificados en el cliente que accede al dispositivo

Puede usar los certificados generados por el dispositivo o aportar sus propios certificados. Si se usan los certificados generados por el dispositivo, es preciso descargar los certificados en el cliente antes de poder importarlos en el almacén de certificados adecuado. Consulte [Descarga de certificados en el cliente que accede al dispositivo](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates).

En ambos casos, los certificados que ha creado y cargado en el dispositivo deben importarse en el cliente de Windows (que accede al dispositivo) en el almacén de certificados adecuado. 

- El certificado raíz que exportó con formato DER debe importarse ahora en las **entidades de certificación raíz de confianza** en el sistema cliente. Para pasos detallados, consulte [Importación de certificados en el almacén de entidades de certificación raíz de confianza](#import-certificates-as-der-format).

- Los certificados de punto de conexión que exportó como `.pfx` tienen que exportarse como DER con la extensión `.cer`. Este archivo `.cer` se importa entonces en el **almacén de certificados Personal** en el sistema. Para pasos detallados, consulte [Importación de certificados en el almacén de certificados Personal](#import-certificates-as-der-format).

### <a name="import-certificates-as-der-format"></a>Importación de certificados con formato DER 

Para importar certificados en un cliente de Windows, realice los pasos siguientes:

1. Haga clic con el botón derecho en el archivo y seleccione **Instalar certificado**. Esta acción inicia el Asistente para importar certificados.

    ![Importación del certificado 1](media/azure-stack-edge-series-manage-certificates/import-cert-1.png)

2. Para **Ubicación de almacén**, seleccione **Máquina local** y, después, **Siguiente**.

    ![Importación del certificado 2](media/azure-stack-edge-series-manage-certificates/import-cert-2.png)

3. Seleccione **Colocar todos los certificados en el siguiente almacén** y, después, **Examinar**. 

    - Para importar en el almacén personal, vaya al almacén Personal del host remoto y seleccione **Siguiente**.

        ![Importación del certificado 4](media/azure-stack-edge-series-manage-certificates/import-cert-4.png)


    - Para importar en el almacén de confianza, vaya a la entidad de certificación raíz de confianza y seleccione **Siguiente**.

        ![Importación del certificado 3](media/azure-stack-edge-series-manage-certificates/import-cert-3.png)

 
4. Seleccione **Finalizar**. Aparece un mensaje que indica que la importación se ha realizado correctamente.


## <a name="view-certificate-expiry"></a>Visualización de la expiración del certificado

Si incorpora sus propios certificados, estos expirarán normalmente en 1 año o 6 meses. Para ver la fecha de expiración del certificado, vaya a la página **Certificados** en la interfaz de usuario web local del dispositivo. Si selecciona un certificado específico, verá en él la fecha de expiración.


## <a name="next-steps"></a>Pasos siguientes

Aprenda a [solucionar problemas de certificados](azure-stack-edge-gpu-certificate-troubleshooting.md)