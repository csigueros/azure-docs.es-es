---
title: Administración de acceso seguro a recursos en redes virtuales de radio para clientes de P2S
titleSuffix: Azure Virtual WAN
description: En este artículo se le ayuda a usar las reglas de Azure Firewall y Azure Virtual WAN para administrar el acceso seguro a las redes virtuales para clientes de VPN de usuario (punto a sitio).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: e46f9c5e3d59c4f30d0a9a0a9dddcf98cd636e40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734624"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Administración de acceso seguro a recursos en redes virtuales de radio para clientes de VPN de usuario

En este artículo se le muestra cómo usar las reglas y los filtros de Azure Firewall y Virtual WAN para administrar el acceso seguro para las conexiones de los recursos en Azure a través de conexiones VPN abiertas o IKEv2 de punto a sitio. Esta configuración es útil si tiene usuarios remotos a los que desea restringir el acceso a los recursos de Azure, o bien para proteger sus recursos en Azure.

Los pasos descritos en este artículo le ayudarán a crear la arquitectura en el siguiente diagrama para permitir que los clientes de VPN de usuario tengan acceso a un recurso específico (VM1) en una red virtual de radio conectada al centro virtual, pero no a otros recursos (VM2). Use este ejemplo de arquitectura como directriz básica.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagrama: centro virtual protegido" :::

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Tiene los valores disponibles para la configuración de autenticación que desea usar. Por ejemplo, un servidor RADIUS, la autenticación con Azure Active Directory o la [generación y exportación de certificados](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Crear una instancia de Virtual WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Definición de parámetros de configuración de P2S

La configuración de punto a sitio (P2S) define los parámetros para conectar clientes remotos. En esta sección se le ayuda a definir los parámetros de configuración de P2S y, a continuación, crear la configuración que se usará para el perfil de cliente de VPN. Las instrucciones que siga dependerán del método de autenticación que desee usar.

### <a name="authentication-methods"></a>Métodos de autenticación

Al seleccionar el método de autenticación, tiene tres opciones. Cada método tiene requisitos específicos. Seleccione uno de los métodos siguientes y, a continuación, complete los pasos.

* **Autenticación con Azure Active Directory:** Obtenga lo siguiente:

   * El **id. de la aplicación** de la aplicación empresarial de VPN de Azure registrada en el inquilino de Azure AD.
   * El **emisor**. Ejemplo: `https://sts.windows.net/your-Directory-ID`.
   * El **inquilino de Azure AD**. Ejemplo: `https://login.microsoftonline.com/your-Directory-ID`.

* **Autenticación basada en Radius:** obtenga la dirección IP Radius, el secreto del servidor Radius y la información sobre el certificado.

* **Certificados de Azure:** para esta configuración, se requieren certificados. Debe generar u obtener certificados. Se requiere un certificado de cliente para cada cliente. Además, es necesario cargar la información sobre el certificado raíz (clave pública). Para obtener más información sobre los certificados necesarios, consulte [Generación y exportación de certificados](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Creación del centro y la puerta de enlace

En esta sección, creará el centro virtual con una puerta de enlace de punto a sitio. Para la configuración, puede usar los siguientes valores de ejemplo:

* **Espacio de direcciones IP privadas del centro:** 10.0.0.0/24
* **Grupo de direcciones de clientes:** 10.5.0.0/16
* **Servidores DNS personalizados:** puede enumerar hasta cinco servidores DNS

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generación de los archivos de configuración de cliente VPN

En esta sección, generará y descargará los archivos del perfil de configuración. Estos archivos se usan para configurar el cliente VPN nativo en el equipo cliente. Para obtener información sobre el contenido de los archivos del perfil de cliente, consulte [Configuración de punto a sitio: certificados](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>Configuración de clientes VPN

Use el perfil descargado para configurar los clientes de acceso remoto. El procedimiento de cada sistema operativo es diferente, siga las instrucciones apropiadas para su sistema.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Conexión de la red virtual de radio

En esta sección, creará una conexión entre el centro de conectividad y la red virtual radial.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Creación de máquinas virtuales

En esta sección, creará dos máquinas virtuales en la red virtual, VM1 y VM2. En el diagrama de red, usamos 10.18.0.4 y 10.18.0.5. Al configurar las máquinas virtuales, asegúrese de seleccionar la red virtual que ha creado (se encuentra en la pestaña Redes). Para conocer los pasos para crear una máquina virtual, consulte [Inicio rápido: creación de una máquina virtual](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Protección del centro virtual

Un centro virtual estándar no tiene ninguna directiva de seguridad integrada para proteger los recursos en redes virtuales de radio. Un centro virtual protegido usa Azure Firewall o un proveedor externo para administrar el tráfico entrante y saliente con el fin de proteger los recursos en Azure.

Convierta el centro en un centro protegido con el siguiente artículo: [Configuración de Azure Firewall en un centro de Virtual WAN](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Creación de reglas para administrar y filtrar el tráfico

Cree reglas que determinen el comportamiento de Azure Firewall. Al proteger el centro, garantizamos que todos los paquetes que entran en el centro virtual estén sujetos al procesamiento de firewall antes de obtener acceso a los recursos de Azure.

Una vez que complete estos pasos, habrá creado una arquitectura que permite a los usuarios de VPN tener acceso a la máquina virtual con la dirección IP privada 10.18.0.4, pero **NO** tener acceso a la máquina virtual con la dirección IP privada 10.18.0.5

1. En Azure Portal, vaya a **Firewall Manager**.
1. En Seguridad, seleccione **Directivas de Azure Firewall**.
1. Seleccione **Crear una directiva de Azure Firewall**.
1. En **Detalles de la directiva**, escriba un nombre y seleccione la región en la que se implementa su el centro virtual.
1. Seleccione **Siguiente: Configuración DNS (versión preliminar)** .
1. Seleccione **Siguiente: Las reglas**.
1. En la pestaña **Reglas**, seleccione **Agregar una colección de reglas**.
1. Proporcione un nombre para la colección. Establezca el tipo como **Red**. Agregue un valor de prioridad **100**.
1. Rellene el nombre de la regla, el tipo de origen, el origen, el protocolo, los puertos de destino y el tipo de destino, como se muestra en el ejemplo siguiente. A continuación, seleccione **Agregar**. Esta regla permite que cualquier dirección IP del grupo de clientes de VPN tenga acceso a la máquina virtual con la dirección IP privada 10.18.04, pero no a ningún otro recurso conectado al centro virtual. Cree las reglas que quiera que se ajusten a la arquitectura y las reglas de permisos deseadas.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Reglas de firewall" :::

1. Seleccione **Siguiente: Inteligencia sobre amenazas**.
1. Seleccione **Siguiente: Centros de conectividad**.
1. En la pestaña **Centros de conectividad**, seleccione **Asociar centros virtuales**.
1. Seleccione el centro virtual que creó anteriormente y, a continuación, seleccione **Agregar**.
1. Seleccione **Revisar + crear**.
1. Seleccione **Crear**.

Este proceso puede tardar 5 minutos o más en completarse.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Enrutamiento del tráfico a través de Azure Firewall

En esta sección, debe asegurarse de que el tráfico se enruta a través de Azure Firewall.

1. En el portal, en **Firewall Manager**, seleccione **Centros virtuales protegidos**.
1. Seleccione el centro virtual que creó.
1. En **Ajustes**, seleccione **Configuración de seguridad**.
1. En **Private traffic** (Tráfico privado), seleccione **Send via Azure Firewall** (Enviar a través de Azure Firewall).
1. Compruebe que la conexión de red virtual y el tráfico privado de conexión de rama están protegidos por Azure Firewall.
1. Seleccione **Guardar**.

## <a name="validate"></a><a name="validate"></a>Validación

Compruebe la configuración del centro protegido.

1. Conéctese al **centro virtual protegido** a través de VPN desde el dispositivo de cliente.
1. Haga ping en la dirección IP **10.18.0.4** desde el cliente. Debería obtener una respuesta.
1. Haga ping en la dirección IP **10.18.0.5** desde el cliente. No tendría que ver ninguna respuesta.

### <a name="considerations"></a>Consideraciones

* Asegúrese de que la **tabla de rutas eficaces** del centro virtual protegido tiene el próximo salto para el tráfico privado por parte del firewall. Para tener acceso a la tabla de rutas efectivas, vaya al recurso **Centro virtual**. En **Conectividad**, seleccione **Enrutamiento** y, a continuación, **Rutas eficaces**. Allí, seleccione la tabla de rutas **predeterminada**.
* Compruebe que ha creado reglas en la sección [Creación de reglas](#create-rules). Si se omiten estos pasos, las reglas que ha creado no se asociarán realmente al centro y a la tabla de rutas, y el flujo de paquetes no usará Azure Firewall.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre Virtual WAN, consulte [Preguntas más frecuentes sobre Virtual WAN](virtual-wan-faq.md).
* Para más información acerca de Azure Firewall, consulte [Preguntas más frecuentes sobre Azure Firewall](../firewall/firewall-faq.yml).
