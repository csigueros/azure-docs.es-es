---
title: Azure Bastion | Microsoft Docs
description: Obtenga información sobre las preguntas más frecuentes sobre Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 8ff219e326f7c8a2bbc075594ff8ffb30e9b04f5
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289161"
---
# <a name="azure-bastion-faq"></a>Preguntas más frecuentes sobre Azure Bastion

## <a name="faqs"></a>Preguntas más frecuentes

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>¿Necesito una dirección IP pública en mi máquina virtual para conectarme mediante Azure Bastion?

No. Si va a conectarse a una máquina virtual mediante Azure Bastion, no necesita una dirección IP pública en la máquina virtual de Azure a la que se va a conectar. El servicio Bastion abrirá la sesión o conexión RDP/SSH a la máquina virtual a través de la dirección IP privada de su máquina virtual dentro de su red virtual.

### <a name="is-ipv6-supported"></a>¿Se admite IPv6?

Actualmente, IPv6 no se admite. Azure Bastion solo admite IPv4.

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>¿Puedo usar Azure Bastion con zonas de DNS privado de Azure?

Azure Bastion debe poder comunicarse con determinados puntos de conexión internos para conectarse correctamente a los recursos de destino. Por lo tanto, *puede* usar Azure Bastion con Azure DNS Private Zones siempre que el nombre de zona que seleccione no se superponga con la nomenclatura de estos puntos de conexión internos. Antes de implementar el recurso de Azure Bastion, asegúrese de que la red virtual del host no esté vinculada a una zona de DNS privado que incluya lo siguiente en el nombre:
* core.windows.net
* azure.com

Tenga en cuenta que si usa una instancia de Azure DNS Private Zones integrada en un punto de conexión privado, el [nombre de la zona DNS recomendado](../private-link/private-endpoint-dns.md#azure-services-dns-zone-configuration) para varios servicios de Azure se superpone con los nombres mencionados anteriormente. El uso de Azure Bastion *no* se admite con estas configuraciones.

El uso de Azure Bastion tampoco se admite con Azure DNS Private Zones en nubes nacionales.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>¿Necesito un cliente RDP o SSH?

No. No es necesario un cliente RDP o SSH para acceder mediante el protocolo de escritorio remoto o Secure Shell a su máquina virtual de Azure en Azure Portal. Use [Azure Portal](https://portal.azure.com) para que RDP/SSH acceda a su máquina virtual directamente en el explorador.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>¿Es necesario que un agente se ejecute en la máquina virtual de Azure?

No. No es preciso instalar un agente ni ningún otro software en el explorador ni en la máquina virtual de Azure. El servicio Bastion no utiliza agentes y no requiere software adicional para RDP y SSH.

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>¿Qué características se admiten en una sesión de RDP?

En este momento, solo se admiten las operaciones de copiado y pegado de texto. No se admiten características como la copia de archivos. No dude en compartir sus comentarios sobre nuevas características en la [página de comentarios de Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>¿Funciona la protección de Bastion con las máquinas virtuales unidas mediante extensión a una máquina virtual AADJ?

Esta característica no funciona con las máquinas unidas mediante extensión que utilizan usuarios de Azure AD. Para más información, consulte la sección sobre [máquinas virtuales Azure con Windows y Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>¿Qué exploradores se admiten?

El explorador debe admitir HTML 5. Utilice el explorador Microsoft Edge o Google Chrome en Windows. Para Apple Mac, use Google Chrome. Microsoft Edge Chromium también es compatible tanto con Windows como con Mac.

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>¿Cuáles son los precios?

Consulte la [página de precios](https://aka.ms/BastionHostPricing)para obtener más información.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>¿Dónde se almacenan los datos de los clientes en Azure Bastion?

Azure Bastion no mueve ni almacena los datos de los clientes fuera de la región en la que se implementa.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>¿Se necesitan roles para acceder a una máquina virtual?

Para crear una conexión, se requieren los siguientes roles:

* Rol Lector en la máquina virtual.
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual.
* Rol Lector en el recurso de Azure Bastion.
* Rol Lector en la red virtual (no es necesario si no hay ninguna red virtual emparejada).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>¿Requiere Azure Bastion una CAL de RDS con fines administrativos en máquinas virtuales hospedadas en Azure?

No, el acceso a las máquinas virtuales Windows Server con Azure Bastion no requiere una [CAL de RDS](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) cuando se utiliza únicamente con fines administrativos.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>¿Qué distribuciones de teclado se admiten durante la sesión remota de Bastion?

Actualmente, Azure Bastion admite la distribución de teclado en-US-QWERTY dentro de la máquina virtual.  La compatibilidad con otras configuraciones regionales para la distribución del teclado está en curso.

### <a name="does-azure-bastion-support-timezone-configuration-or-timezone-redirection-for-target-vms"></a><a name="timezone"></a>¿Admite Azure Bastion la configuración o redireccionamiento de zona horaria para las máquinas virtuales de destino?

Azure Bastion actualmente no admite el redireccionamiento de zona horaria y, además, esta no se puede configurar.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>¿Se admite el enrutamiento definido por el usuario (UDR) en las subredes de Azure Bastion?

No. UDR no es compatible con las subredes de Azure Bastion.

En los escenarios que incluyen Azure Bastion y Azure Firewall/Aplicación virtual de red (NVA) en la misma red virtual, no es preciso forzar el tráfico de una subred de Azure Bastion a Azure Firewall, ya que la comunicación entre Azure Bastion y las máquinas virtuales es privada. Para más información, consulte [Acceso a las máquinas virtuales que están detrás de Azure Firewall con Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="can-i-upgrade-from-a-basic-sku-to-a-standard-sku"></a><a name="upgradesku"></a> ¿Puedo actualizar de una SKU básica a una estándar?

Sí. Para ver los pasos, consulte [Actualización de una SKU](upgrade-sku.md). Para más información sobre SKU, consulte el artículo [Parámetros de configuración](configuration-settings.md#skus).

### <a name="can-i-downgrade-from-a-standard-sku-to-a-basic-sku"></a><a name="downgradesku"></a> ¿Puedo cambiar de una SKU estándar a una SKU básica?

No. No se admite cambiar una SKU estándar por una SKU básica. Para más información sobre SKU, consulte el artículo [Parámetros de configuración](configuration-settings.md#skus).

### <a name="can-i-deploy-multiple-azure-resources-in-my-azure-bastion-subnet"></a><a name="subnet"></a> ¿Puedo implementar varios recursos de Azure en mi subred de Azure Bastion?

No. La subred de Azure Bastion (*AzureBastionSubnet*) solo está reservada para la implementación del recurso de Azure Bastion.

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>¿Por qué obtengo el error "Su sesión ha expirado" antes de iniciar la sesión de Bastion?

Las sesiones deben iniciarse solo en Azure Portal. Inicie sesión en Azure Portal y vuelva a iniciar una sesión. Si va a la dirección URL directamente desde otra sesión del explorador o pestaña, este error es previsible. Ayuda a garantizar que la sesión sea más segura y que se solo se pueda acceder a la sesión desde Azure Portal.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>¿Cómo controlo los errores de implementación?

Examine los mensajes de error y [cree una solicitud de soporte técnico en Azure Portal](../azure-portal/supportability/how-to-create-azure-support-request.md) si fuera necesario. Los errores de implementación pueden estar motivados por [límites, cuotas y restricciones de la suscripción de Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Más concretamente, los clientes pueden encontrar un límite en cuanto al número de direcciones IP públicas que se permiten por suscripción y que provoca un error en la implementación de Azure Bastion.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>¿Cómo puedo incorporar Azure Bastion en mi plan de recuperación ante desastres?

Azure Bastion se implementa en redes virtuales o redes virtuales emparejadas y está asociado a una región de Azure. Usted es responsable de implementar Azure Bastion en una red virtual del sitio de recuperación ante desastres (DR). En caso de que se produzca un error en la región de Azure, realice una operación de conmutación por error de las máquinas virtuales a la región de DR. A continuación, use el host de Azure Bastion implementado en la región de DR para conectarse a las máquinas virtuales que ahora están implementadas en ella.

## <a name="vnet-peering"></a><a name="peering"></a>Emparejamiento de VNET

### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>¿Puedo seguir implementando varios hosts bastión en redes virtuales emparejadas?

Sí. De forma predeterminada, un usuario ve el host bastión que se implementa en la misma red virtual en la que reside la máquina virtual. Sin embargo, en el menú **Conectar**, el usuario puede ver varios hosts bastión detectados en redes emparejadas. Pueden seleccionar el host bastión que prefieren usar para conectarse a la máquina virtual implementada en la red virtual.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Si las redes virtuales emparejadas se implementan en distintas suscripciones, ¿se realizará la conectividad mediante el trabajo de Bastion?

Sí, la conectividad a través de Bastion continuará funcionando para redes virtuales emparejadas en distintas suscripciones para un solo inquilino. No se admiten suscripciones en dos inquilinos diferentes. Para ver Bastion en el menú desplegable **Conectar**, el usuario debe seleccionar las suscripciones a las que tiene acceso en **Suscripción > Suscripción global**.

:::image type="content" source="./media/bastion-faq/global-subscriptions.png" alt-text="Filtro de suscripciones globales." lightbox="./media/bastion-faq/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Tengo acceso a la red virtual emparejada, pero no veo que la máquina virtual se haya implementado allí.

Asegúrese de que el usuario tenga acceso de **lectura** tanto a la máquina virtual como a la red virtual emparejada. Además, compruebe en IAM que el usuario tiene acceso de **lectura** a los siguientes recursos:

* Rol Lector en la máquina virtual.
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual.
* Rol Lector en el recurso de Azure Bastion.
* Rol Lector en la red virtual (no es necesario si no hay ninguna red virtual emparejada).

|Permisos|Descripción|Tipo de permiso|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |Obtiene un host de tipo bastión.|Acción|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Obtiene las referencias del host de tipo bastión en una red virtual.|Acción|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|Obtiene las referencias del host de tipo bastión en una red virtual.|Acción|
|Microsoft.Network/networkInterfaces/read|Obtiene una definición de interfaz de red.|Acción|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|Obtiene una definición de configuración de dirección IP de la interfaz de red.|Acción|
|Microsoft.Network/virtualNetworks/read|Obtiene la definición de red virtual|Acción|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|Obtiene referencias a todas las máquinas virtuales de una subred de red virtual|Acción|
|Microsoft.Network/virtualNetworks/virtualMachines/read|Obtiene referencias a todas las máquinas virtuales de una red virtual|Acción|