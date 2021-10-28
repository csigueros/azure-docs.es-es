---
title: Ejecución de diagnósticos, recopilación de registros para solucionar problemas de dispositivos de Azure Stack Edge | Microsoft Docs
description: Describe cómo ejecutar diagnósticos, usar registros para solucionar problemas de dispositivos GPU de Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/18/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 4d66d325d3484f61ebee70d430e9a81b2da3a974
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235418"
---
# <a name="run-diagnostics-collect-logs-to-troubleshoot-azure-stack-edge-device-issues"></a>Ejecución de diagnósticos, recopilación de registros para solucionar problemas de dispositivos de Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo ejecutar diagnósticos, recopilar un paquete de soporte técnico, recopilar registros de seguridad avanzados y revisar los registros para solucionar problemas de carga y actualización en un dispositivo de Azure Stack Edge.


## <a name="run-diagnostics"></a>Ejecución de diagnósticos

Para diagnosticar y solucionar los errores de cualquier dispositivo, puede ejecutar las pruebas de diagnóstico. Realice los pasos siguientes en la interfaz de usuario web local del dispositivo para ejecutar las pruebas de diagnóstico.

1. En la interfaz de usuario de web local, vaya a **Solución de problemas > Pruebas de diagnóstico en**. Seleccione la prueba que quiere ejecutar y elija **Ejecutar prueba**. Aparece una notificación que indica que hay algunas pruebas en ejecución en el dispositivo. 

    ![Seleccionar pruebas ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)

    Esta tabla describe cada una de las pruebas de diagnóstico que se ejecutan en el dispositivo de Azure Stack Edge.

    | Nombre de la prueba                        | Descripción        |
    |----------------------------------|---------------------------------------------------------------------------------------------------------|
    | Conectividad de Azure Portal        | La prueba valida la conectividad del dispositivo de Azure Stack Edge a Azure Portal.      |
    | Servicios de mantenimiento compatibles con Azure | Varios servicios, como Azure Resource Manager, un proveedor de recursos de proceso, un proveedor de recursos de red y un de almacenamiento de blobs, se ejecutan en el dispositivo. La unión de estos servicios proporciona una pila compatible con Azure. La comprobación de estado garantiza que estos servicios compatibles con Azure están funcionando. |
    | Certificados                     | La prueba valida la fecha de expiración y el impacto del cambio de dispositivo y dominio DNS en los certificados. La comprobación de estado ha verificado que todos los certificados se importan y se aplican en todos los nodos del dispositivo.                                                                                      |
    | Entorno de ejecución del proceso perimetral de Azure       | La prueba valida que el servicio de kubernetes de Azure Stack Edge funciona según lo previsto. También se comprueba el estado de la máquina virtual de Kubernetes, así como el estado de los servicios de Kubernetes implementados por el dispositivo.  |
    | Discos                            | La prueba valida que todos los discos del dispositivo están conectados y son funcionales. También comprueba que los discos tienen instalado el firmware correcto y que Bitlocker está configurado correctamente. |
    | Unidades de fuente de alimentación (PSU)                             | La prueba valida que todas las fuentes de alimentación estén conectadas y funcionen.  |
    | Interfaces de red               | La prueba valida que todas las interfaces de red están conectadas al dispositivo y que la topología de red para ese sistema es la que se esperaba.    |
    | Unidades centrales de procesamiento (CPU)                             | La prueba valida que las CPU del sistema están configuradas correctamente y que están activas y funcionan.    |
    | Aceleración de proceso             | La prueba valida que la aceleración de proceso funciona según lo previsto en términos de hardware y software. En función del modelo de dispositivo, la aceleración de proceso podría ser una unidad gráfica de procesamiento (GPU), una unidad de procesamiento de visión (VPU) o una matriz de puertas programables de campo (FPGA).   |
    | Configuración de red                 | Esta prueba valida la configuración de red del dispositivo.    |
    | Conectividad de Internet            | Esta prueba valida la conectividad a Internet del dispositivo.   |
    | Software del sistema                  | Esta prueba valida que el almacenamiento del sistema y la pila de software funcionan según lo previsto.   |
    | Sincronización de hora                        | Esta prueba valida la configuración de hora del dispositivo y comprueba que el servidor de hora configurado en el dispositivo es válido y accesible.     |
    | Preparación de actualizaciones de software        | Esta prueba valida que el servidor de actualizaciones configurado es válido y accesible.   |
 
2. Al terminar las pruebas, se muestran los resultados. 

    ![Ver los resultados de las pruebas](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    Si se produce un error en una prueba, se especifica una dirección URL en la que se muestra la acción recomendada. Seleccione dicha dirección para ver la acción recomendada.
 
    ![Revisión de las advertencias de las pruebas con errores](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>Recopilación de un paquete de soporte

Los paquetes de registros contienen todos los registros pertinentes que pueden ayudar al equipo de soporte técnico de Microsoft a solucionar los problemas de cualquier dispositivo. Los paquetes de registros se pueden generar a través de la interfaz de usuario web local.

Siga estos pasos para recopilar un paquete de soporte. 

1. En la interfaz de usuario web local, vaya a **Solución de problemas > Soporte**. Seleccione **Crear un paquete de soporte técnico**. El sistema empieza a recopilar el paquete de soporte. Dicha recopilación puede tardar varios minutos.

    ![Seleccionar Agregar usuario](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. Una vez que se cree el paquete, haga clic en **Descargar el paquete de soporte técnico**. Se descarga un paquete comprimido en la ruta de acceso que eligió. Puede descomprimir el paquete de registro y ver los archivos de registro del sistema.

    ![Seleccionar Agregar usuario 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>Recopilación de los registros de seguridad avanzada

Los registros de seguridad avanzada pueden ser registros de intrusiones de software o hardware para un dispositivo Azure Stack Edge Pro.

### <a name="software-intrusion-logs"></a>Registros de intrusiones de software

Los registros de intrusiones de software o de firewall predeterminados se recopilan para el tráfico entrante y saliente. 

- Cuando se crea una imagen del dispositivo en la fábrica, el registro de firewall predeterminado está habilitado. Estos registros se agrupan en el paquete de soporte técnico de manera predeterminada cuando se crea un paquete de soporte técnico a través de la interfaz de usuario local o a través de la interfaz de Windows PowerShell del dispositivo.

- Si solo se necesitan los registros del firewall en el paquete de soporte técnico para revisar las intrusiones de software (NW) en el dispositivo, use la opción `-Include FirewallLog` al crear el paquete de soporte. 

- Si no se proporciona ninguna opción de inclusión específica, el registro del firewall se incluye de manera predeterminada en el paquete de soporte técnico.

- En el paquete de soporte, el registro del firewall es `pfirewall.log` y se encuentra en la carpeta raíz. Este es un ejemplo del registro de intrusiones de software para el dispositivo Azure Stack Edge Pro. 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>Registros de intrusiones de hardware

Para detectar cualquier intrusión de hardware en el dispositivo, actualmente se registran todos los eventos de chasis, como la apertura o el cierre del chasis. 

- El registro de eventos del sistema del dispositivo se lee mediante el cmdlet `racadm`. Después, estos eventos se filtran para el evento relacionado con el chasis en un archivo `HWIntrusion.txt`.

- Para obtener solo el registro de intrusiones de hardware en el paquete de soporte técnico, use la opción `-Include HWSelLog` al crear dicho paquete. 

- Si no se proporciona ninguna opción de inclusión específica, el registro de intrusiones de hardware se incluye de manera predeterminada en el paquete de soporte técnico.

- En el paquete de soporte, el registro de intrusiones de hardware es `HWIntrusion.txt` y se encuentra en la carpeta raíz. Este es un ejemplo del registro de intrusiones de hardware para el dispositivo Azure Stack Edge Pro. 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="troubleshoot-device-upload-and-refresh-errors"></a>Solución de errores de carga y actualización de dispositivos

Los errores encontrados durante los procesos de carga y actualización se incluyen en los archivos de error respectivos.

1. Para ver los archivos de error, vaya al recurso compartido y seleccione el recurso compartido para ver el contenido. 


2. Seleccione la _carpeta Microsoft Azure Data Box Edge_. Esta carpeta tiene dos subcarpetas:

    - Cargue la que contiene los archivos de registro de errores de carga.
    - Actualice la carpeta para ver los errores durante la actualización.

    Este es un archivo de registro de ejemplo de actualización.

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. Cuando vea un error en este archivo (resaltado en el ejemplo), anote el código de error, en este caso el 16001. Busque la descripción del código de error en la siguiente referencia de errores.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de activación de dispositivos](azure-stack-edge-gpu-troubleshoot-activation.md).
- [Solución de problemas de Azure Resource Manager](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md).
- [Solución de problemas de almacenamiento en blobs](azure-stack-edge-gpu-troubleshoot-blob-storage.md).
- [Solución de problemas de proceso en IoT Edge](azure-stack-edge-gpu-troubleshoot-iot-edge.md).
