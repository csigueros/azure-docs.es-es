---
author: cherylmc
ms.author: cherylmc
ms.date: 07/30/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 21bda32ddb1c87bb07b6679499e648e0b2f7a809
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734682"
---
1. Vaya a la red de WAN virtual que ha creado. 

1. Seleccione **Configuraciones de VPN de usuario** en el menú de la izquierda.

1. En la página **Configuraciones de VPN de usuario**, seleccione **+Crear una configuración de VPN de usuario**.

   :::image type="content" source="media/virtual-wan-p2s-configuration/user-vpn.png" alt-text="Captura de pantalla de la página configuraciones de VPN de usuario.":::

1. En la página **Crear nueva configuración de VPN de usuario**, en la pestaña **Aspectos básicos**, en **Detalles de instancia**, rellene el campo **Nombre** con el nombre que quiere asignar a la configuración de VPN. 

   :::image type="content" source="media/virtual-wan-p2s-configuration/custom.png" alt-text="Captura de pantalla del modificador de IPsec a personalizado.":::

1. En **Tipo de túnel**, seleccione el tipo de túnel que prefiera de la lista desplegable. Las opciones de tipo de túnel son: **IKEv2 VPN, OpenVPN,** and **OpenVpn e IKEv2**. Cada tipo de túnel tiene diferentes configuraciones necesarias.

   **Requisitos y parámetros**:

     **VPN IKEv2.**

     * **Requisitos:** Al seleccionar el tipo de túnel **IKEv2** , verá un mensaje que le indica que seleccione un método de autenticación. En el caso de IKEv2, solo puede especificar un método de autenticación. Puede elegir la autenticación basada en certificados, Azure Active Directory o RADIUS de Azure.

     * **Parámetros personalizados de IPSec:** Para personalizar los parámetros de IKE fase 1 e IKE fase 2, cambie el modificador IPsec a **personalizado** y seleccione los valores de los parámetros. Para obtener más información sobre los parámetros personalizables, consulte el artículo sobre [IPSec personalizado](../articles/virtual-wan/point-to-site-ipsec.md) .

     **OpenVPN**

     * **Requisitos:** Al seleccionar el tipo de túnel **OpenVPN** , verá un mensaje que le indica que seleccione un mecanismo de autenticación. Si se selecciona OpenVPN como el tipo de túnel, se pueden especificar varios métodos de autenticación. Puede elegir cualquier subconjunto de certificados de Azure, Azure Active Directory o autenticación basada en RADIUS. En el caso de la autenticación basada en RADIUS, puede proporcionar una dirección IP y un secreto de servidor RADIUS secundarios.

1. Configure los métodos de **Autenticación** que desea utilizar. Cada método de autenticación está en una pestaña independiente: **Certificado de Azure**, **Autenticación RADIUS** y **Azure Active Directory**. Algunos métodos de autenticación solo están disponibles en determinados tipos de túnel.

   En la pestaña del método de autenticación que desea configurar, seleccione **Sí** para mostrar los valores de configuración disponibles.

   :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-no.png" alt-text="Captura de pantalla de No hay pantalla: la opción Sí está resaltada.":::

   * **Ejemplo: Autenticación de certificado**

      :::image type="content" source="media/virtual-wan-p2s-configuration/certificate-authentication.png" alt-text="Captura de pantalla de la opción Sí seleccionada.":::

   * **Ejemplo: Autenticación RADIUS**

      :::image type="content" source="media/virtual-wan-p2s-configuration/radius-authentication.png" alt-text="Captura de pantalla de la página de autenticación RADIUS.":::

   * **Ejemplo: Autenticación de Azure Active Directory**

      :::image type="content" source="media/virtual-wan-p2s-configuration/azure-active-directory.png" alt-text="Página de autenticación de Azure Active Directory.":::

1. Cuando haya terminado de configurar los valores, haga clic en **Revisar y crear** en la parte inferior de la página.

1. Haga clic en **Crear** para crear la configuración de VPN de usuario.
