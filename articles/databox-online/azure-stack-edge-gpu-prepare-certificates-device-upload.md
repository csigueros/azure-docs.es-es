---
title: Preparación de certificados para cargarlos en dispositivos Azure Stack Edge Pro con GPU/Pro R/Mini R
description: Describe cómo preparar los certificados para cargarlos en dispositivos Azure Stack Edge Pro con GPU/Pro R/Mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/30/2021
ms.author: alkohli
ms.openlocfilehash: fa908a75a09beefe00babe0e710f43c4d74c3aaf
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360627"
---
# <a name="prepare-certificates-to-upload-on-your-azure-stack-edge-pro-gpu"></a>Preparación de certificados para cargarlos en dispositivos Azure Stack Edge Pro con GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo convertir los certificados al formato adecuado para que estén listos para cargarse en el dispositivo Azure Stack Edge. Este procedimiento suele ser necesario cuando los usuarios traen sus propios certificados.

Para más información sobre cómo crear estos certificados, consulte [Uso de certificados con el dispositivo Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-create-certificates-powershell.md).


## <a name="prepare-certificates"></a>Preparar certificados

Si trae sus propios certificados, los que ha creado para el dispositivo de manera predeterminada residen en el **almacén Personal** del cliente. Estos certificados tienen que exportarse en el cliente en archivos de formato adecuado que se puedan cargar en el dispositivo.

- **Preparar certificados raíz**: el certificado raíz tiene que exportarse como archivo de formato DER con la extensión `.cer`. Para pasos detallados, consulte [Exportación de certificados con formato DER](#export-certificates-as-der-format).

- **Preparar certificados de punto de conexión**: los certificados de punto de conexión se tienen que exportar como archivos *.pfx* con claves privadas. Para pasos detallados, consulte [ Exportación de certificados como un archivo *.pfx* con claves privadas](#export-certificates-as-pfx-format-with-private-key). 


## <a name="export-certificates-as-der-format"></a>Exportación de certificados con formato DER

1. Ejecute *certlm.msc* para iniciar el almacén de certificados de la máquina local.

1. En el almacén de certificados Personal, seleccione el certificado raíz. Haga clic con el botón derecho y seleccione **Todas las tareas -> Exportar...**

    ![Exportación de certificados DER 1](media/azure-stack-edge-gpu-manage-certificates/export-cert-cer-1.png)

2. Se abrirá el asistente de certificados. Seleccione el formato **DER binario codificado X.509 (.cer)** . Seleccione **Next** (Siguiente).

    ![Exportación de certificados DER 2](media/azure-stack-edge-gpu-manage-certificates/export-cert-cer-2.png)

3. Busque y seleccione la ubicación en la que desea exportar el archivo con formato .cer.

    ![Exportación de certificados DER 3](media/azure-stack-edge-gpu-manage-certificates/export-cert-cer-3.png)

4. Seleccione **Finalizar**.

    ![Exportación de certificados DER 4](media/azure-stack-edge-gpu-manage-certificates/export-cert-cer-4.png)


## <a name="export-certificates-as-pfx-format-with-private-key"></a>Exportación de certificados con formato .pfx con clave privada

Siga estos pasos para exportar un certificado SSL con clave privada en una máquina Windows. 

> [!IMPORTANT]
> Siga estos pasos en la misma máquina que usó para crear el certificado. 

1. Ejecute *certlm.msc* para iniciar el almacén de certificados de la máquina local.

1. Haga doble clic en la carpeta **Personal** y, a continuación, en **Certificados**.

    ![Exportación del certificado 1](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-1.png)
 
2. Haga clic con el botón derecho en el certificado del que desea realizar una copia de seguridad y elija **Todas las tareas > Exportar...**

    ![Exportación del certificado 2](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-2.png)

3. Siga el Asistente para exportación de certificados para realizar una copia de seguridad del certificado en un archivo .pfx.

    ![Exportación del certificado 3](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-3.png)

4. Elija **Exportar la clave privada**.

    ![Exportación del certificado 4](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-4.png)

5. Elija **Incluir todos los certificados en la ruta de certificación (si es posible)** , **Exportar todas las propiedades extendidas** y **Habilitar privacidad de certificado**. 

    > [!IMPORTANT]
    > NO seleccione la opción **Delete Private Key option if export is successful** (Eliminar la opción de clave privada si la exportación es correcta).

    ![Exportación del certificado 5](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-5.png)

6. Escriba una contraseña que pueda recordar. Confirme la contraseña. La contraseña protege la clave privada.

    ![Exportación del certificado 6](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-6.png)

7. Elija guardar el archivo en una ubicación establecida.

    ![Exportación del certificado 7](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-7.png)
  
8. Seleccione **Finalizar**.

    ![Exportación del certificado 8](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-8.png)

9. Recibirá un mensaje que indica que la exportación se ha realizado correctamente. Seleccione **Aceptar**.

    ![Exportación del certificado 9](media/azure-stack-edge-gpu-manage-certificates/export-cert-pfx-9.png)

La copia de seguridad del archivo .pfx se guarda en la ubicación seleccionada y está lista para moverse o almacenarse para mantener la seguridad.


## <a name="next-steps"></a>Pasos siguientes

[Más información sobre la carga de certificados en el dispositivo](azure-stack-edge-gpu-manage-certificates.md)
