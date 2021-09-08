---
title: Configuración de los valores avanzados de red en Azure Percept DK
description: En este artículo se le guía al usuario por la Configuración avanzada de red durante la experiencia de configuración de Azure Percept DK.
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 7/19/2021
ms.custom: template-how-to
ms.openlocfilehash: ac06d331bcd587c1294c21388faac75cc6f8cfd6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219631"
---
# <a name="set-up-advanced-network-settings-on-the-azure-percept-dk"></a>Configuración de los valores avanzados de red en Azure Percept DK

Azure Percept DK permite controlar varios componentes de red en el kit de desarrollo. Esto se realiza mediante la configuración avanzada de redes en la experiencia de configuración. Para acceder a esta configuración, debe [iniciar la experiencia de configuración](./quickstart-percept-dk-set-up.md) y seleccionar **Acceder a la configuración de red avanzada** en la página **Conexión de red**.

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-entry.png" alt-text="Inicio de la configuración avanzada de red desde la página Conexión de red":::

## <a name="select-the-security-setting"></a>Selección de la configuración de seguridad
Azure Percept DK admite IPv4 e IPv6 para la conectividad local.

> [!NOTE]
> Azure IoTHub [no admite IPv6](../iot-hub/iot-hub-understand-ip-address.md#support-for-ipv6). IPv4 debe usarse para comunicarse con IoTHub.
1. Seleccione el botón de radio IPv4 y luego un elemento en configuración de red para cambiar los valores de IPv4.
1. Seleccione el botón de radio IPv6 y luego un elemento en configuración de red para cambiar los valores de IPv6.
1. Las opciones de **Configuración de red** pueden variar en función de su selección.

:::image type="content" source="media/how-to-set-up-advanced-network-settings/advanced-ns-security.png" alt-text="Selección de un protocolo de seguridad para ver la lista de opciones de red":::

## <a name="define-a-static-ip-address"></a>Definición de una dirección IP estática

1. En la página **Configuración de red avanzada**, seleccione **Definir una dirección IP estática** en la lista.
1. Seleccione la **Interfaz de red** en el menú desplegable.
1. Desactive **Dirección IP dinámica**.
1. Escriba la dirección IP estática.
1. Escriba la dirección IP de la subred (también conocida como máscara de subred).
1. Escriba la dirección IP de la puerta de enlace (también conocida como puerta de enlace predeterminada).
1. Si procede, escriba la dirección DNS.
1. Seleccione **Guardar**.
1. Seleccione **Atrás** para volver a la página principal de **configuración de red avanzada**.

## <a name="define-dns-server-for-docker"></a>Definición del servidor DNS para Docker
Esta configuración permite modificar o agregar nuevas direcciones IP de DNS para Docker.

> [!NOTE]
> El servicio Docker está configurado para aceptar solo entradas de DNS IPv4.  Las entradas agregadas desde las pantallas IPv6 se omitirán.

1. En la página **Configuración de red avanzada**, seleccione **Definir un servidor DNS para Docker** en la lista.
1. Escriba la dirección DNS IPv4 para Docker.
1. Seleccione **Guardar**.
1. Seleccione **Atrás** para volver a la página principal de **configuración de red avanzada**.

## <a name="define-bridge-internet-protocol-for-docker"></a>Definición del protocolo de Puente de Internet para Docker
Las pantallas Protocolo de Puente de Internet permiten cambiar el espacio de direcciones IPv4 para contenedores de Docker.

Si la dirección IP del dispositivo comparte la misma ruta que el servicio Docker de Azure Percept Devkit (172.17.x.x), deberá cambiar el puente de Docker a otra cosa para permitir las comunicaciones entre los contenedores de Docker y Azure IoTHub.  

1. En la página **Configuración de red avanzada**, seleccione **Definir el protocolo de Internet de puente para Docker** en la lista.
1. Escriba la dirección IPv4 del protocolo de Puente de Internet para Docker (BIP).
1. Seleccione **Guardar**.
1. Seleccione **Atrás** para volver a la página principal de **configuración de red avanzada**.

## <a name="define-an-internet-proxy-server"></a>Definición de un servidor proxy de Internet
Esta opción permite definir un servidor proxy.    

1. En la página **Configuración de red avanzada**, seleccione **Definir un servidor proxy de Internet** en la lista.
1. Active la casilla **Usar un servidor proxy** para habilitar esta opción.
1. Escriba la **Dirección HTTP** del servidor proxy (si procede).
1. Escriba la **Dirección HTTP** del servidor proxy (si procede).
1. Escriba la **Dirección FTP** del servidor proxy (si procede).
1. En el cuadro **No hay direcciones proxy**, escriba las direcciones IP para las que no se debe usar el servidor proxy.
1. Seleccione **Guardar**.
1. Seleccione **Atrás** para volver a la página principal de **configuración de red avanzada**.

## <a name="setup-zero-touch-provisioning"></a>Configuración del aprovisionamiento sin intervención del usuario

> [!IMPORTANT]
> La **Configuración del aprovisionamiento sin intervención del usuario** no es funcional actualmente.

Esta opción le permite convertir Azure Percept DK en un [Configurador masivo de Wi-Fi Easy Connect<sup>TM</sup>](https://techcommunity.microsoft.com/t5/internet-of-things/simplify-wi-fi-iot-device-onboarding-with-zero-touch/ba-p/2161129#:~:text=A%20Wi-Fi%20Easy%20Connect%E2%84%A2%20Configurator%2C%20paired%20with%20the,device%20to%20any%20WPA2-Personal%20or%20WPA3-Personal%20wireless%20LAN.) para incorporar varios dispositivos a la vez a la infraestructura de Wi-Fi.  

## <a name="define-access-point-passphrase"></a>Definición de la frase de contraseña del punto de acceso 
Esta opción permite actualizar la frase de contraseña del punto de acceso Wi-Fi de Azure Percept DK.  

> [!CAUTION]
> Se desconectará inmediatamente del punto de acceso Wi-Fi después de guardar la frase de contraseña nueva.  Vuelva a conectarse con la nueva frase de contraseña para recuperar el acceso.  

Requisitos de la frase de contraseña:
- Debe tener entre 12 y 123 caracteres.
- Debe contener al menos una letra minúscula, una mayúscula, un número y un carácter especial.

1. En la página **Configuración de red avanzada**, seleccione **Definir una frase de contraseña de punto de acceso** en la lista.
1. Escriba una frase de contraseña nueva.
1. Seleccione **Guardar**.
1. Seleccione **Atrás** para volver a la página principal de **configuración de red avanzada**.

## <a name="next-steps"></a>Pasos siguientes
Cuando haya terminado de realizar cambios en **Configuración de red avanzada**, seleccione el botón **Atrás** para [continuar con la experiencia de configuración de Azure Percept DK](./quickstart-percept-dk-set-up.md).