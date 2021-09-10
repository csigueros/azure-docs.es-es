---
title: Conexión de sensores con un proxy
description: Obtenga información sobre cómo configurar Azure Defender para IoT para comunicarse con un sensor a través de un proxy sin acceso directo a Internet.
ms.topic: how-to
ms.date: 07/04/2021
ms.openlocfilehash: f16ec5c45d78237e256dcd5936ac0612d175022b
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297930"
---
# <a name="connect-azure-defender-for-iot-sensors-without-direct-internet-access-by-using-a-proxy"></a>Conexión de sensores de Azure Defender para IoT sensores sin acceso directo a Internet mediante un proxy 

En este artículo se describe cómo configurar Azure Defender para IoT para comunicarse con un sensor a través de un proxy sin acceso directo a Internet. Conecte el sensor con un proxy de reenvío que tenga tunelización HTTP y use el comando HTTP CONNECT para la conectividad. Las instrucciones que se proporcionan aquí usan el proxy Squid de código abierto; se puede usar cualquier otro proxy que admita CONNECT. 

El proxy usa un túnel SSL cifrado para transferir datos de los sensores al servicio. El proxy no inspecciona, analiza ni almacena en caché ningún dato. 

En el diagrama siguiente se muestran los datos que van del sensor de Azure Defender para IoT en el segmento de OT a la nube a través de un proxy ubicado en la red de IT y la red perimetral industrial.

:::image type="content" source="media/how-to-connect-sensor-by-proxy/cloud-access.png" alt-text="Conectar el sensor a un proxy a través de la nube.":::

## <a name="set-up-your-system"></a>Configuración del sistema

En este escenario, instalaremos y configuraremos la versión más reciente de [Squid](http://www.squid-cache.org/) en un servidor Ubuntu 18.

> [!Note]
> Azure Defender para IoT no ofrece compatibilidad con Squid ni con ningún otro servicio de proxy.

**Para instalar el proxy de Squid en un servidor Ubuntu 18**:

1. Inicie sesión en la máquina Ubuntu de proxy designada.

1. Inicie una ventana del terminal.
 
1. Actualice el software a la versión más reciente con el comando siguiente.

    ```bash
    sudo apt-get update 
    ```

1. Instale el paquete de Squid con el comando siguiente.

    ```bash
    sudo apt-get install squid 
    ```

1. Busque el archivo de configuración de Squid que se encuentra en `/etc/squid/squid.conf` y `/etc/squid/conf.d/`.

1. Realice una copia de seguridad del archivo original con el siguiente comando.

    ```bash
    sudo cp -v /etc/squid/squid.conf{,.factory}'/etc/squid/squid.conf' -> '/etc/squid/squid.conf.factory sudo nano /etc/squid/squid.conf
    ```

1. Abra `/etc/squid/squid.conf` en un editor de texto.

1. Busque `# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS`.

1. Agregue `acl sensor1 src <sensor-ip>` y `http_access allow sensor1` en el archivo.

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/add-lines.png" alt-text="Agregar las dos líneas siguientes al texto y guardar el archivo.":::

1. (Opcional) Agregue más sensores agregando una línea adicional para cada sensor.

1. Habilite el servicio Squid para empiece en el inicio con el siguiente comando.

    ```bash
    sudo systemctl enable squid 
    ```

## <a name="set-up-a-sensor-to-use-squid"></a>Configuración de un sensor para usar Squid

**Para configurar un sensor para usar Squid**:

1. Inicie sesión en el sensor.

1. Vaya a **Configuración del sistema** > **Red**.

1. Seleccione **Habilitar proxy**.

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/enable-proxy.png" alt-text="Seleccionar Habilitar proxy en la ventana Configuración de red del sensor.":::

1. Escriba la dirección del proxy.

1. Escriba un puerto. El puerto predeterminado es `3128`.

1. (Opcional) Escriba un usuario para el proxy y la contraseña.

1. Seleccione **Guardar**.

## <a name="see-also"></a>Consulte también

[Administración de suscripciones](how-to-manage-subscriptions.md).
