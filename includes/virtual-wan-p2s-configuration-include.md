---
author: cherylmc
ms.author: cherylmc
ms.date: 02/23/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: b5c0bdbb29af7b8894d86233520ff09854faa201
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732502"
---
1. Vaya a **Todos los recursos** y seleccione la WAN virtual que ha creado y, luego, en el menú de la izquierda, elija **Configuraciones de VPN de usuario**.
1. En la página **Configuraciones de VPN de usuario**, seleccione **+Crear una configuración de VPN de usuario** en la parte superior de la página para abrir la página **Crear nueva configuración de VPN de usuario**.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Captura de pantalla de la página Configuraciones de VPN de usuario.":::

1. En la pestaña **Aspectos básicos**, en **Detalles de instancia**, rellene el campo **Nombre** con el nombre que quiere asignar a la configuración de VPN.
1. En **Tipo de túnel**, en la lista desplegable, seleccione el tipo de túnel que prefiera. Las opciones de tipo de túnel son: **VPN IKEV2**, **OpenVPN** y **OpenVpn e Ikev2**.
1. Siga los pasos correspondientes al tipo de túnel que ha seleccionado. Una vez especificados todos los valores, haga clic en **Revisar y crear** y, luego, en **Crear** para crear la configuración.

   **VPN IKEv2**

   * **Requisitos:** al seleccionar el tipo de túnel **IKEv2**, verá un mensaje que le invita a seleccionar un método de autenticación. En el caso de IKEv2, solo puede especificar un método de autenticación. Puede elegir la autenticación basada en Azure Active Directory, RADIUS o certificados de Azure.
 
   * **Parámetros personalizados de IPSec:** para personalizar los parámetros de IKE fase 1 e IKE fase 2, cambie el conmutador IPsec a **Personalizado** y seleccione los valores de los parámetros. Para más información sobre los parámetros personalizables, consulte el artículo [IPSec personalizado](../articles/virtual-wan/point-to-site-ipsec.md).

     :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Captura de pantalla del modificador de IPsec para personalizar.":::

   * **Autenticación:** : vaya al mecanismo de autenticación que quiere usar; para ello, haga clic en **Siguiente** en la parte inferior de la página para avanzar hasta el método de autenticación, o bien haga clic en la pestaña correspondiente en la parte superior de la página. Para seleccionar el método, cambie el conmutador a **Sí**.

     En este ejemplo, se selecciona la autenticación RADIUS. En el caso de la autenticación basada en RADIUS, puede proporcionar una dirección IP y un secreto de servidor RADIUS secundarios.

     :::image type="content" source="media/virtual-wan-p2s-configuration/ike-radius.png" alt-text="Captura de pantalla de IKE.":::

   **OpenVPN**

   * **Requisitos:** al seleccionar el tipo de túnel **OpenVPN**, verá un mensaje que le invita a seleccionar un mecanismo de autenticación. Si se selecciona OpenVPN como tipo de túnel, se pueden especificar varios métodos de autenticación. Puede elegir cualquier subconjunto de autenticación basada en Azure Active Directory, RADIUS o certificados de Azure. En el caso de la autenticación basada en RADIUS, puede proporcionar una dirección IP y un secreto de servidor RADIUS secundarios.

   * **Autenticación:** : vaya a los métodos de autenticación que quiere usar; para ello, haga clic en **Siguiente** en la parte inferior de la página para avanzar hasta el método de autenticación, o bien haga clic en la pestaña correspondiente en la parte superior de la página.
   En cada método que quiera seleccionar, cambie el conmutador a **Sí** y escriba los valores adecuados.

     En este ejemplo, se selecciona Azure Active Directory.

     :::image type="content" source="media/virtual-wan-p2s-configuration/openvpn.png" alt-text="Captura de pantalla de la página OpenVPN.":::
