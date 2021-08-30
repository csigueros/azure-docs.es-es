---
title: Información general de los certificados de Azure Stack Edge Pro con GPU, Pro R y Mini R
description: Se describe información general de todos los certificados que se pueden usar en un dispositivo de Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 558b31262a74a351ef17e42eb79772645f9a4641
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290369"
---
# <a name="what-are-certificates-on-azure-stack-edge-pro-gpu"></a>¿Qué son los certificados en Azure Stack Edge Pro con GPU?

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describen los tipos de certificados que se pueden instalar en el dispositivo Azure Stack Edge Pro con GPU. En este artículo también se incluyen los detalles de cada tipo de certificado.  

## <a name="about-certificates"></a>Acerca de los certificados

Los certificados proporcionan un vínculo entre una **clave pública** y una entidad (como una nombre de dominio) que ha sido **firmada** (verificada) por un tercero de confianza (por ejemplo, una **entidad de certificación**).  Proporcionan una manera cómoda de distribuir claves de cifrado públicas de confianza. Por lo tanto, los certificados aseguran que la comunicación es de confianza y que envía información cifrada al servidor adecuado. 

## <a name="deploying-certificates-on-device"></a>Implementación de certificados en un dispositivo

En el dispositivo de Azure Stack Edge, puede usar los certificados autofirmados o aportar los propios.

- **Certificados generados por el dispositivo**: cuando el dispositivo está configurado desde el principio, se generan automáticamente certificados autofirmados. Si es necesario, puede regenerar estos certificados a través de la interfaz de usuario web local. Una vez que se regeneren los certificados, descargue e importe los certificados en los clientes que se usan para acceder al dispositivo.

- **Aportación de sus propios certificados**: como alternativa, puede aportar sus propios certificados. Hay instrucciones que debe seguir si tiene previsto traer sus propios certificados.

- Para empezar, comprenda los tipos de certificados que se pueden usar con un dispositivo de Azure Stack Edge en este artículo.
- A continuación, revise los [requisitos de certificado para cada tipo de certificado](azure-stack-edge-gpu-certificate-requirements.md).  
- Luego puede [crear los certificados mediante Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md) o [crear los certificados mediante la herramienta Readiness Checker](azure-stack-edge-gpu-create-certificates-tool.md).
- Por último, [convierta los certificados al formato adecuado](azure-stack-edge-gpu-prepare-certificates-device-upload.md) para que estén listos para cargarlos en el dispositivo.
- [Cargue los certificados](azure-stack-edge-gpu-manage-certificates.md#upload-certificates-on-your-device) en el dispositivo.
- [Importe los certificados en los clientes](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device) que acceden al dispositivo.

## <a name="types-of-certificates"></a>Tipos de certificados

Los distintos tipos de certificados que puede aportar para el dispositivo son los siguientes: 
- Certificados de firma
    - CA raíz
    - Intermedio

- Certificados de nodo

- Certificados de punto de conexión   
   
    - Certificados de Azure Resource Manager
    - Certificados de Blob Storage

- Certificados de interfaz de usuario local
- Certificados de dispositivo IoT
    
- Certificados de Kubernetes
    
- Certificados de Wi-Fi
- Certificados de VPN  

- Certificados de cifrado
    - Certificados de sesión de soporte

Estos certificados se describen minuciosamente en las secciones siguientes.

## <a name="signing-chain-certificates"></a>Certificados de cadena de firma

Estos son los certificados para la autoridad que firma los certificados o la entidad del certificado de firma. 

### <a name="types"></a>Tipos

Estos certificados podrían ser certificados raíz o intermedios. Los certificados raíz siempre son autofirmados (o se firman por sí mismos). Los certificados intermedios no son autofirmados, sino que los firma la autoridad de firma.

#### <a name="caveats"></a>Advertencias

- Los certificados raíz deben ser certificados de cadena de firma.
- Los certificados raíz se pueden cargar en el dispositivo con el siguiente formato: 
    - **DER**: están disponibles como extensión de archivo `.cer`.
    - **Codificado con Base 64**: están disponibles como extensión de archivo `.cer`.
    - **P7b**: este formato se usa solo para certificados de cadena de firma que incluyen los certificados raíz e intermedio.
- Los certificados de cadena de firma siempre se cargan antes que cualquier otro.


## <a name="node-certificates"></a>Certificados de nodo

<!--Your  device could be a 1-node device or a 4-node device.--> Todos los nodos del dispositivo se comunican constantemente entre sí y, por lo tanto, deben tener una relación de confianza. Los certificados de nodo proporcionan una manera de establecer esa confianza. También entran en juego cuando se conecta al nodo de dispositivo mediante una sesión remota de PowerShell mediante HTTPS.

#### <a name="caveats"></a>Advertencias

- El certificado de nodo debe proporcionarse en formato `.pfx` con una clave privada que se pueda exportar. 
- Puede crear y cargar un certificado de nodo comodín o cuatro certificados de nodo individuales. 
- Los certificados de nodo deben cambiarse si el dominio DNS cambia pero el nombre del dispositivo no. Si va a traer su propio certificado de nodo, no podrá cambiar el número de serie del dispositivo, solo podrá cambiar el nombre de dominio.
- Utilice la tabla siguiente como guía para crear un certificado de nodo.
   
    |Tipo |Nombre del firmante (SN)  |Nombre alternativo del firmante (SAN)  |Ejemplo de nombre de firmante |
    |---------|---------|---------|---------|
    |Nodo|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Certificados de punto de conexión

En el caso de los puntos de conexión que expone el dispositivo se requiere un certificado para la comunicación de confianza. Los certificados de punto de conexión incluyen los necesarios al acceder a Azure Resource Manager y al almacenamiento de blobs mediante las API REST. 

Cuando incorpore un certificado firmado de su propiedad, necesitará también la cadena de firma correspondiente. En el caso de los certificados de cadena de firma, Azure Resource Manager y blob en el dispositivo, necesitará también los certificados correspondientes en la máquina cliente para autenticarse y comunicarse con el dispositivo.

#### <a name="caveats"></a>Advertencias

- Los certificados de punto de conexión deben estar en formato `.pfx` con una clave privada. La cadena de firma debe estar en formato DER (extensión de archivo `.cer`). 
- Sus certificados de punto de conexión propios pueden ser individuales o de varios dominios. 
- Si lo que trae es una cadena de firma, se debe cargar el certificado de la cadena de firma antes de cargar un certificado de punto de conexión.
- Estos certificados deben cambiarse si cambian el nombre del dispositivo o los nombres de dominio DNS.
- Se puede usar un certificado de punto de conexión comodín.
- Las propiedades de los certificados de punto de conexión son similares a las de un certificado SSL típico. 
- Al crear un certificado de punto de conexión, use la tabla siguiente:

    |Tipo |Nombre del firmante (SN)  |Nombre alternativo del firmante (SAN)  |Ejemplo de nombre de firmante |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Certificado único de varios SAN para ambos puntos de conexión|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Certificados de interfaz de usuario local

Puede acceder a la interfaz de usuario web local del dispositivo desde un explorador. Para asegurarse de que esta comunicación es segura, puede cargar su propio certificado. 

#### <a name="caveats"></a>Advertencias

- El certificado de la interfaz de usuario local también se carga en formato `.pfx` con una clave privada que se puede exportar.
- Después de cargar este certificado de interfaz de usuario local, deberá reiniciar el explorador y borrar la memoria caché. Consulte las instrucciones específicas para el explorador.

    |Tipo |Nombre del firmante (SN)  |Nombre alternativo del firmante (SAN)  |Ejemplo de nombre de firmante |
    |---------|---------|---------|---------|
    |Interfaz de usuario local| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>Certificados de dispositivo IoT Edge

El dispositivo también es un dispositivo IoT con el proceso habilitado por un dispositivo IoT Edge conectado a él. También puede cargar certificados de IoT Edge para las conexiones seguras entre este dispositivo IoT Edge y los dispositivos de nivel inferior que puedan conectarse a él. 

El dispositivo tiene certificados autofirmados por si desea utilizar solo el escenario de proceso con el dispositivo. En cambio, si el dispositivo está conectado a dispositivos de nivel inferior, deberá traer sus propios certificados.

Hay tres certificados de IoT Edge que necesita instalar para habilitar esta relación de confianza:

- **Entidad de certificación raíz o entidad de certificación del propietario**
- **Entidad de certificación de dispositivo** 
- **Certificado clave del dispositivo**


#### <a name="caveats"></a>Advertencias

- Los certificados de IoT Edge se cargan en formato `.pem`. 

Para más información sobre los certificados de IoT Edge, consulte [Detalles de los certificados de Azure IoT Edge](../iot-edge/iot-edge-certs.md#iot-edge-certificates) y [Creación de certificados de producción de IoT Edge](../iot-edge/how-to-manage-device-certificates.md?preserve-view=true&view=iotedge-2020-11#create-production-certificates).

## <a name="kubernetes-certificates"></a>Certificados de Kubernetes

Si el dispositivo tiene un registro de contenedor de Edge, necesitará un certificado del registro de contenedor de Edge para comunicarse de manera segura con el cliente que accede al registro en el dispositivo.

#### <a name="caveats"></a>Advertencias

- El certificado del registro de contenedor de Edge se debe cargar en formato *.pfx* con una clave privada.

## <a name="vpn-certificates"></a>Certificados de VPN

Si hay una VPN (punto a sitio) configurada en el dispositivo, puede aportar su propio certificado VPN para garantizar que la comunicación es de confianza. El certificado raíz se instala en Azure VPN Gateway y los certificados de cliente se instalan en cada equipo cliente que se conecta a una red virtual mediante conexión de punto a sitio.

#### <a name="caveats"></a>Advertencias

- El certificado de VPN se debe cargar en formato *.pfx* con una clave privada.
- El certificado de VPN no depende del nombre del dispositivo, del número de serie del dispositivo ni de la configuración del dispositivo. Solo requiere el nombre de dominio completo externo.
- Asegúrese de que el OID de cliente esté definido.

Para más información, consulte [Generación y exportación de certificados para conexiones de punto a sitio con PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md#generate-and-export-certificates-for-point-to-site-using-powershell).

## <a name="wi-fi-certificates"></a>Certificados de Wi-Fi

Si el dispositivo está configurado para funcionar en una red inalámbrica WPA2-Enterprise, también necesitará un certificado de Wi-Fi para cualquier comunicación que se produzca a través de la red inalámbrica. 

#### <a name="caveats"></a>Advertencias

- El certificado de Wi-Fi se debe cargar en formato *.pfx* con una clave privada.
- Asegúrese de que el OID de cliente esté definido.

## <a name="support-session-certificates"></a>Certificados de sesión de soporte

Si el dispositivo tiene problemas, para solucionarlos, puede abrir una sesión remota de soporte técnico de PowerShell en el dispositivo. Para permitir una comunicación segura cifrada con esta sesión de soporte técnico, puede cargar un certificado.

#### <a name="caveats"></a>Advertencias

- Asegúrese de que el certificado `.pfx` correspondiente con clave privada está instalado en la máquina cliente mediante la herramienta de descifrado.
- Compruebe que el campo **Uso de la clave** del certificado no sea **Firma de certificados**. Para comprobarlo, haga clic con el botón derecho en el certificado, elija **Abrir** y, en la pestaña **Detalles**, busque **Uso de la clave**. 

- El certificado de la sesión de soporte técnico se debe proporcionar con formato DER y extensión `.cer`.


## <a name="next-steps"></a>Pasos siguientes

[Revise los requisitos de los certificados](azure-stack-edge-gpu-certificate-requirements.md).