---
title: Conexión a Azure Percept DK a través de Ethernet
description: En esta guía se muestra a los usuarios cómo conectarse a la experiencia de instalación de Azure Percept DK cuando se conectan a través de una conexión Ethernet.
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 06/01/2021
ms.custom: template-how-to
ms.openlocfilehash: 6ffc2a2d55bdad670c8323dfc57efad17dec1930
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227094"
---
# <a name="connect-to-azure-percept-dk-over-ethernet"></a>Conexión a Azure Percept DK a través de Ethernet

En esta guía paso a paso aprenderá a iniciar la experiencia de instalación de Azure Percept DK a través de una conexión Ethernet. Es un complemento de guía [Inicio rápido: Instalación de Azure Percept DK e implementación del primer modelo de inteligencia artificial](./quickstart-percept-dk-set-up.md). Consulte cada opción que se describe a continuación y elija cuál es la más adecuada para su entorno.

## <a name="prerequisites"></a>Prerrequisitos

- Una instancia de Azure Percept DK ([obtener una aquí](https://go.microsoft.com/fwlink/?linkid=2155270))
- Un equipo host basado en Windows, Linux u OS X con funcionalidad Wi-Fi o Ethernet y un explorador web
- Cable de red

## <a name="identify-your-dev-kits-ip-address"></a>Identificación de la dirección IP del kit de desarrollo

La clave para ejecutar la experiencia de configuración de Azure Percept DK a través de una conexión Ethernet es encontrar la dirección IP del kit de desarrollo. En este artículo se tratan tres opciones:
1. Desde el enrutador de red
1. A través de SSH
1. A través de la herramienta Nmap

### <a name="from-your-network-router"></a>Desde el enrutador de red
La manera más rápida de identificar la dirección IP de los kits de desarrollo es buscarla en el enrutador de red.
1. Enchufe el cable Ethernet al kit de desarrollo y el otro extremo al enrutador.
1. Encendido de Azure Percept DK.
1. Busque una etiqueta en el enrutador de red que especifique las instrucciones de acceso.

    **Estos son ejemplos de etiquetas de enrutador**

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-01.png" alt-text="Etiqueta de ejemplo de un enrutador de red":::

    :::image type="content" source="media/how-to-connect-over-ethernet/router-sticker-02.png" alt-text="Otra etiqueta de ejemplo de un enrutador de red":::

1. En el equipo que está conectado a Ethernet o Wi-Fi, abra un explorador web.
1. Escriba la dirección del explorador para el enrutador tal como se encuentra en la etiqueta.
1. Cuando se le solicite, escriba el nombre y la contraseña del enrutador, tal como se encuentra en la etiqueta.
1. Una vez en la interfaz del enrutador, seleccione Mis dispositivos (o algo similar, según el enrutador).
1. Busque el kit de desarrollo de Azure Percept en la lista de dispositivos.
1. Copie la dirección IP del kit de desarrollo de Azure Percept.

### <a name="via-ssh"></a>A través de SSH
Es posible encontrar la dirección IP de los kits de desarrollo mediante la conexión al kit de desarrollo a través de SSH.

> [!NOTE]
> El uso del método SSH para identificar la dirección IP del kit de desarrollo requiere que pueda conectarse al punto de acceso Wi-Fi de dicho kit. Si esto no es posible para usted, utilice uno de los otros métodos.

1. Enchufe el cable Ethernet al kit de desarrollo y el otro extremo al enrutador.
1. Encienda el kit de desarrollo de Azure Percept.
1. Conéctese al kit de desarrollo a través de SSH. Consulte [Conexión a Azure Percept DK a través de SSH](./how-to-ssh-into-percept-dk.md) para obtener instrucciones detalladas sobre cómo conectarse al kit de desarrollo a través de SSH.
1. Para enumerar la dirección IP de la red local Ethernet, escriba el comando de envío en la ventana del terminal SSH:

    ```bash
    ip a | grep eth1
    ```

    :::image type="content" source="media/how-to-connect-over-ethernet/ssh-local-network-address.png" alt-text="Ejemplo de identificación de la dirección IP de red local en el terminal SSH":::


1. La dirección IP del kit de desarrollo se muestra después de "inet". Copie la dirección IP.

### <a name="using-the-nmap-tool"></a>Uso de la herramienta Nmap
También puede usar las herramientas gratuitas que se encuentran en la Web para identificar la dirección IP del kit de desarrollo. En estas instrucciones, se trata una herramienta denominada Nmap.
1. Enchufe el cable Ethernet al kit de desarrollo y el otro extremo al enrutador.
1. Encienda el kit de desarrollo de Azure Percept.
1. En el equipo host, descargue e instale el [analizador de seguridad de Nmap gratuito](https://nmap.org/download.html) que se necesita para su plataforma (Windows/Mac/Linux).
1. Obtenga la "puerta de enlace predeterminada" del equipo: [búsqueda de la puerta de enlace predeterminada](https://www.noip.com/support/knowledgebase/finding-your-default-gateway/).
1. Abra la aplicación Nmap 
1. Escriba la puerta de enlace predeterminada en el cuadro *Destino* y anexe **/24** al final. Cambie *Perfil* a **Examen rápido** y seleccione el botón **Examinar**.
    
    :::image type="content" source="media/how-to-connect-over-ethernet/nmap-tool.png" alt-text="Ejemplo de la entrada de la herramienta Nmap":::
 
1. En los resultados, busque el kit de desarrollo de Azure Percept en la lista de dispositivos (similar **a apd-xxxxxxxx**).
1. Copie la dirección IP del kit de desarrollo de Azure Percept. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Inicio de la experiencia de instalación de Azure Percept DK
1. Enchufe el cable Ethernet al kit de desarrollo y el otro extremo al enrutador.
1. Encienda el kit de desarrollo de Azure Percept.
1. Abra un explorador web y pegue la dirección IP del kit de desarrollo. La experiencia de instalación debe iniciarse en el explorador.

## <a name="next-steps"></a>Pasos siguientes
- [Finalización de la experiencia de instalación](./quickstart-percept-dk-set-up.md)