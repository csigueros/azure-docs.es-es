---
title: Autenticación del agente de seguridad (versión preliminar)
description: Existen dos métodos para realizar la autenticación de microagentes.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: 8dd8abaedaaababf4d84330c5bf8cb030bac55bd
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018226"
---
# <a name="micro-agent-authentication-methods-preview"></a>Métodos de autenticación de microagentes (versión preliminar)

Hay dos opciones para la autenticación con el microagente de Defender para IoT: 

- Cadena de conexión 

- Certificado 

## <a name="authentication-using-a-connection-string"></a>Autenticación mediante una cadena de conexión 

Para usar una cadena de conexión, es preciso agregar un archivo que utilice la cadena de conexión codificada en UTF-8 en el directorio del agente de Defender en un archivo denominado `connection_string.txt`. Por ejemplo,

```azurecli
echo “<connection string>” > connection_string.txt 
```

A continuación, debe reiniciar el servicio mediante este comando.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Autenticación mediante un certificado 


Para realizar la autenticación mediante un certificado: 

1. Coloque la parte pública con codificación PEM de un certificado en el directorio del agente de Defender, en un archivo denominado `certificate_public.pem`.
1. Coloque la clave privada con codificación PEM en el directorio del agente de Defender, en un archivo denominado `certificate_private.pem`.
1. Coloque la cadena de conexión adecuada en un archivo denominado `connection_string.txt`. Por ejemplo,

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Esta acción indica que el agente de Defender esperará que se proporcione un certificado para la autenticación. 

1. Utilice el siguiente código para reiniciar el servicio: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Comprobación de que el microagente se ejecuta correctamente 

1. Ejecute el siguiente comando: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Compruebe que el servicio es estable. Para ello, debe asegurarse de que está **activo** y de que el tiempo de actividad del proceso es el adecuado: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Asegúrese de que el servicio es estable. Para ello, debe asegurase de que está activo.":::

## <a name="next-steps"></a>Pasos siguientes

Consulte su [Posición de seguridad: punto de referencia de CIS](concept-security-posture.md).
