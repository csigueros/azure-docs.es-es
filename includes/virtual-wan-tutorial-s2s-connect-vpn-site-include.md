---
ms.author: cherylmc
author: cherylmc
ms.date: 08/18/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 53c6980ea6e5d868a842b1e14031f27ff51f655a
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122638273"
---
1. Vaya al **Centro de conectividad virtual -> VPN (Sitio a sitio)** .

1. Es posible que tenga que hacer clic en **Hub association: Connected to this hub** (Asociación del centro de conectividad: conectado a este centro de conectividad) para borrar los filtros y ver los sitios.

1. Seleccione la casilla de verificación del sitio al que quiere conectarse y, a continuación, haga clic en **Conectar sitios de VPN**.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect-site.png" alt-text="Captura de pantalla en la que se muestra el botón Conectar.":::

1. En la página **Conectar sitios**, establezca la configuración necesaria.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png" alt-text="Captura de pantalla que muestra el panel de Sitios conectados del centro de conectividad virtual listo para una clave precompartida y la configuración asociada.":::

   * **Clave precompartida (PSK)** : escriba la clave compartida previamente que usa el dispositivo VPN. Si no especifica una clave, Azure generará una automáticamente. A continuación, usará esa clave al configurar el dispositivo VPN.
   * **Protocolo e IPsec**: puede dejar la configuración predeterminada para el protocolo (IKEv2) e IPsec (valor predeterminado), o bien establecer una configuración personalizada. Para obtener más información, vea [default/custom IPsec](../articles/virtual-wan/virtual-wan-ipsec.md).
   * **Propagate Default Route** (Propagar ruta predeterminada): solo cambie este valor a **Habilitar** si sabe que quiere propagar la ruta predeterminada. De lo contrario, déjelo como **Deshabilitar**. Puede modificar el título en cualquier momento más tarde. 
   
     La opción **Habilitar** permite que el centro de conectividad propague una ruta predeterminada aprendida a esta conexión. Esta marca habilita la propagación de la ruta predeterminada a una conexión, solamente si el centro de Virtual WAN ya ha aprendido la ruta predeterminada como resultado de la implementación de un firewall en el centro, o en caso de que otro sitio conectado tenga habilitada la tunelización forzada. La ruta predeterminada no se origina en el centro de conectividad de Virtual WAN. 
   * **Use policy based traffic selector** (Usar el selector de tráfico basado en directivas): deje este valor en **Deshabilitar** a menos que esté estableciendo una conexión a un dispositivo que utilice esta configuración.

1. En la parte inferior de la página, haga clic en **Conectar**.

1. Al hacer clic en **Conectar**, el estado de conectividad pasa a ser **Actualizando**. Una vez completada la actualización, en el sitio aparecerá la conexión y el estado de conectividad.

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png" alt-text="Instantánea en la que se muestra una conexión entre sitios y el estado de conectividad." lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/connectivity-status.png":::

   **Estado de aprovisionamiento de la conexión**: es el estado del recurso de Azure para la conexión que conecta el sitio VPN con la puerta de enlace de VPN del centro de conectividad de Azure. Una vez que esta operación de plano de control se realiza correctamente, la puerta de enlace de VPN de Azure y el dispositivo VPN local procederán a establecer la conectividad.

   **Estado de conectividad**: es el estado de conectividad real (ruta de acceso a datos) entre la puerta de enlace de VPN de Azure en el centro de conectividad y el sitio VPN. Una vez completada la actualización, esta puede mostrar cualquiera de los siguientes estados:

    * **Desconocido**: Este estado suele observarse si los sistemas de back-end están trabajando en una transición a otro estado.
    * **Conectando**: la puerta de enlace de VPN está intentando ponerse en contacto con el sitio VPN local.
    * **Conectado**: se ha establecido la conectividad entre la puerta de enlace de VPN y el sitio VPN local.
   * **No conectado**: no se ha establecido la conectividad.
    * **Desconectado**: Este estado aparece si, por cualquier motivo (local o en Azure), se desconectó la conexión.
1. Si quiere realizar cambios en el sitio, selecciónelo y, a continuación, haga clic en el menú contextual **...** .

   :::image type="content" source="./media/virtual-wan-tutorial-connect-vpn-site-include/edit.png" alt-text="Captura de pantalla que muestra las opciones de edición, eliminación y descarga." lightbox="./media/virtual-wan-tutorial-connect-vpn-site-include/expand/edit.png":::

   En esta página, puede hacer lo siguiente: 

   * Editar o eliminar la conexión VPN.
   * Eliminar la conexión de VPN a este centro de conectividad.
   * Descargar una configuración específica de rama para obtener información sobre el sitio de Azure. Si quiere descargar el archivo de configuración que pertenece a todos los sitios conectados del centro de conectividad, seleccione **Descargar la configuración de VPN** en el menú de la parte superior de la página.
