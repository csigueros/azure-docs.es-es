---
title: Integración con Application Gateway
description: Obtenga más información sobre la integración de una aplicación de una instancia de App Service Environment con ILB con Azure Application Gateway.
author: madsd
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 10/12/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: f52f984b1e72d685dae46002e9ae4bac543c068a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447703"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integración de ILB App Service Environment con Azure Application Gateway #

[App Service Environment][AppServiceEnvironmentoverview] es una implementación de Azure App Service en la subred de una red virtual de Azure de cliente. Se puede implementar con un punto de conexión externo o interno para el acceso a la aplicación. La implementación del entorno de App Service con un punto de conexión privado se denomina App Service Environment (ASE) de equilibrador de carga interno (ILB).

Los firewalls de aplicaciones web ayudan a proteger las aplicaciones web, ya que inspeccionan el tráfico web entrante y bloquean las inyecciones de código SQL, un posible ataque de scripts de sitios, cargas de malware y DDoS de aplicaciones, y otros ataques. Puede obtener un dispositivo WAF en Azure Marketplace o puede usar [Azure Application Gateway][appgw].

Azure Application Gateway es una aplicación virtual que ofrece un equilibrio de carga de nivel 7, descarga de TLS/SSL y protección mediante firewall de aplicaciones web (WAF). Puede escuchar en una dirección IP pública y enrutar el tráfico hacia el punto de conexión de la aplicación. En la información siguiente se describe cómo integrar una puerta de enlace de aplicación configurada con firewall de aplicaciones web con una aplicación en un entorno de App Service con ILB.  

La integración de la puerta de enlace de aplicación con el entorno de App Service con ILB se produce en el nivel de la aplicación. Al configurar la puerta de enlace de aplicación con el entorno de App Service con ILB, lo hace para aplicaciones específicas de este último. Esta técnica permite hospedar aplicaciones para varios inquilinos seguras en un único entorno de App Service con ILB.  

:::image type="content" source="./media/integrate-with-application-gateway/appgw-highlevel.png" alt-text="Captura de pantalla del diagrama de integración general":::

En este tutorial realizará lo siguiente:

* Crear una instancia de Azure Application Gateway.
* Configurar la puerta de enlace de aplicación para que apunte a una aplicación en el entorno de App Service con ILB.
* Editar el nombre de host público del DNS que apunta a la puerta de enlace de aplicaciones.

## <a name="prerequisites"></a>Prerrequisitos

Para integrar la puerta de enlace de aplicación con el entorno de App Service con ILB necesita lo siguiente:

* Un entorno de App Service con ILB.
* Una zona DNS privada para el entorno de App Service con ILB.
* Una aplicación que se ejecute en el entorno de App Service con ILB.
* Un nombre DNS público que se utilizará posteriormente para que apunte a la puerta de enlace de aplicación.
* Si tiene que usar el cifrado TLS/SSL en la puerta de enlace de aplicación, se necesita un certificado público válido que se use para enlazar a la puerta de enlace de aplicación.

### <a name="ilb-app-service-environment"></a>Entorno de App Service con ILB

Para más información sobre cómo crear un entorno de App Service con ILB, vea [Creación de una instancia de App Service Environment en Azure Portal][creation] y [Creación de una instancia de ASE mediante el uso de una plantilla de Azure Resource Manager][createfromtemplate].

* Después de crear el ASE con ILB, el dominio predeterminado es `<YourAseName>.appserviceenvironment.net`.

    :::image type="content" source="./media/integrate-with-application-gateway/ilb-ase.png" alt-text="Captura de pantalla de la información general de ASE con ILB":::

* Se aprovisiona un equilibrador de carga interno para el acceso entrante. Puede comprobar la dirección de entrada en las direcciones IP de la configuración de ASE. Más adelante puede crear una zona DNS privada asignada a esta dirección IP.

    :::image type="content" source="./media/integrate-with-application-gateway/ip-addresses.png" alt-text="Captura de pantalla de la obtención de la dirección de entrada de la configuración de direcciones IP de ASE con ILB.":::

### <a name="a-private-dns-zone"></a>Zona DNS privada

Necesita una [zona DNS privada][privatednszone] para la resolución de nombres interna. Puede crearla con el nombre de ASE mediante los conjuntos de registros que se muestran en la tabla siguiente (para obtener instrucciones, vea [Inicio rápido: Creación de una zona DNS privada de Azure mediante Azure Portal][createprivatednszone]).

| Nombre  | Tipo | Value               |
| ----- | ---- | ------------------- |
| *     | A    | Dirección de entrada de ASE |
| @     | A    | Dirección de entrada de ASE |
| @     | SOA  | Nombre DNS de ASE        |
| *.scm | A    | Dirección de entrada de ASE |

### <a name="app-service-on-ilb-ase"></a>App Service en ASE con ILB

Debe crear un plan de App Service y una aplicación en el ASE con ILB. Al crear la aplicación en el portal, seleccione el ASE con ILB como **Región**.

### <a name="a-public-dns-name-to-the-application-gateway"></a>Un nombre DNS público para la puerta de enlace de aplicación

Para conectarse a la puerta de enlace de aplicación desde Internet, necesita un nombre de dominio enrutable. En este caso, se ha usado un nombre de dominio enrutable `asabuludemo.com` y el objetivo es la conexión a una instancia de App Service con este nombre de dominio `app.asabuludemo.com`. Las direcciones IP asignadas a este nombre de dominio de aplicación deben establecerse en la dirección IP pública después de crear la puerta de enlace de aplicación.
Con un dominio público asignado a la puerta de enlace de aplicación, no es necesario configurar un dominio personalizado en App Service. Puede comprar un nombre de dominio personalizado a la aplicación con los [dominios de App Service](../manage-custom-dns-buy-domain.md#buy-an-app-service-domain). 

### <a name="a-valid-public-certificate"></a>Un certificado público válido

Para mejorar la seguridad, se recomienda enlazar el certificado TLS/SSL para el cifrado de sesión. Para enlazar el certificado TLS/SSL a la puerta de enlace de aplicación, se necesita un certificado público válido con la siguiente información. Con [App Service Certificate](../configure-ssl-certificate.md#start-certificate-order), puede comprar un certificado TLS/SSL y exportarlo en formato .pfx.

| Name  | Value               | Descripción|
| ----- | ------------------- |------------|
| **Nombre común** |`<yourappname>.<yourdomainname>`, por ejemplo, `app.asabuludemo.com`  <br/> O bien `*.<yourdomainname>`, por ejemplo: `*.asabuludemo.com` | Un certificado estándar o un [certificado comodín](https://wikipedia.org/wiki/Wildcard_certificate) para la puerta de enlace de aplicación|
| **Nombre alternativo del firmante** | `<yourappname>.scm.<yourdomainname>`, por ejemplo, `app.scm.asabuludemo.com`  <br/>O bien `*.scm.<yourdomainname>`, por ejemplo: `*.scm.asabuludemo.com` |La SAN que permite conectarse al servicio kudu de App Service. Es un valor opcional, si no quiere publicar el servicio kudu de App Service en Internet.|

El archivo de certificado debe tener una clave privada y guardarse en formato .pfx; se importará a la puerta de enlace de aplicación más adelante.

## <a name="create-an-application-gateway"></a>Creación de una puerta de enlace de aplicaciones

Para la creación básica de la puerta de enlace de aplicación, consulte [Tutorial: Creación de una puerta de enlace de aplicación con una firewall de aplicaciones web mediante Azure Portal][Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal].

En este tutorial, se usará Azure Portal para crear una puerta de enlace de aplicación con el entorno de App Service con ILB.

En Azure Portal, seleccione **Nuevo** > **Redes** > **Application Gateway** para crear una puerta de enlace de aplicación.

1. Valor Datos básicos

    En la lista desplegable **Nivel**, puede seleccionar **V2 estándar** o **WAF V2** para habilitar la característica **WAF** en la puerta de enlace de aplicación. 

2. Valor Front-end

    Establezca Tipo de dirección IP de front-end en **Público**, **Privado** o **Ambos**. Si lo establece en **Privado** o **Ambos**, tendrá que asignar una dirección IP estática en el intervalo de subredes de la puerta de enlace de aplicación. En este caso, se establece en Dirección IP pública solo para el punto de conexión público.
    
    * Dirección IP pública: debe asociar una dirección IP pública para el acceso público de la puerta de enlace de aplicación. Guarde esta dirección IP; más adelante tendrá que agregar un registro en el servicio DNS.
    
        :::image type="content" source="./media/integrate-with-application-gateway/frontends.png" alt-text="Captura de pantalla de la obtención de la dirección IP pública de la configuración de front-end de la puerta de enlace de aplicación.":::

3. Valor Back-ends

    Escriba un nombre de grupo de back-end y seleccione **App Services** o **Dirección IP o FQDN** en **Tipo de destino**. En este caso, se establece en **App Services** y se selecciona Nombre del Servicio de aplicaciones en la lista desplegable de destino.
    
    :::image type="content" source="./media/integrate-with-application-gateway/add-backend-pool.png" alt-text="Captura de pantalla de la adición de un nombre de grupo de back-end en la configuración de back-end.":::

4. Opción de configuración

    En el valor **Configuración**, haga clic en el icono **Agregar una regla de enrutamiento** para agregar una regla de enrutamiento.
    
    :::image type="content" source="./media/integrate-with-application-gateway/configuration.png" alt-text="Captura de pantalla de la adición de una regla de enrutamiento en el valor Configuración.":::
    
    En una regla de enrutamiento debe configurar un **Cliente de escucha** y **Destinos de back-end**. Puede agregar un cliente de escucha HTTP para la implementación de prueba de concepto, o bien un cliente de escucha HTTPS para mejorar la seguridad.
    
    * Para conectarse a la puerta de enlace de aplicación con el protocolo HTTP, puede crear un cliente de escucha con la siguiente configuración,
    
        | Parámetro      | Value                             | Descripción                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | Nombre de la regla      | Por ejemplo: `http-routingrule`    | Nombre de enrutamiento                                                 |
        | Nombre del cliente de escucha  | Por ejemplo: `http-listener`       | Nombre del cliente de escucha                                                |
        | Dirección IP de front-end    | Público                            | Para el acceso a Internet, establezca en Público                           |
        | Protocolo       | HTTP                             | No use el cifrado TLS/SSL                                       |
        | Port           | 80                               | Puerto HTTP predeterminado                                           |
        | Tipo de cliente de escucha  | Multisitio                        | Permitir escuchar varios sitios en la puerta de enlace de aplicación           |
        | Tipo de host      | Múltiple/Comodín                 | Establézcalo en el nombre de sitio web múltiple o con caracteres comodín si el tipo de cliente de escucha está establecido en varios sitios. |
        | Nombre de host      | Por ejemplo: `app.asabuludemo.com` | Establézcalo en un nombre de dominio enrutable para App Service              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/http-routing-rule.png" alt-text="Captura de pantalla del cliente de escucha HTTP de la regla de enrutamiento de la puerta de enlace de aplicación.":::
    
    * Para conectarse a la puerta de enlace de aplicación con cifrado TLS/SSL, puede crear un cliente de escucha con la configuración siguiente,
    
        | Parámetro      | Value                             | Descripción                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | Nombre de la regla      | Por ejemplo: `https-routingrule`    | Nombre de enrutamiento                                                 |
        | Nombre del cliente de escucha  | Por ejemplo: `https-listener`       | Nombre del cliente de escucha                                                |
        | Dirección IP de front-end    | Público                            | Para el acceso a Internet, establezca en Público                           |
        | Protocolo       | HTTPS                             | Usar cifrado TLS/SSL                                       |
        | Port           | 443                               | Puerto HTTPS predeterminado                                           |
        | Configuración HTTPS | Carga de un certificado              | Carga de un certificado que contiene el CN y la clave privada en formato .pfx. |
        | Tipo de cliente de escucha  | Multisitio                        | Permitir escuchar varios sitios en la puerta de enlace de aplicación           |
        | Tipo de host      | Múltiple/Comodín                 | Establézcalo en el nombre de sitio web múltiple o con caracteres comodín si el tipo de cliente de escucha está establecido en varios sitios. |
        | Nombre de host      | Por ejemplo: `app.asabuludemo.com` | Establézcalo en un nombre de dominio enrutable para App Service              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-routing-rule.png" alt-text="Cliente de escucha HTTPS de la regla de enrutamiento de la puerta de enlace de aplicación.":::
    
    * Debe configurar un **Grupo de back-end** y una **Configuración de HTTP** en **Destinos de back-end**. El grupo de back-end se ha configurado en pasos anteriores. Haga clic en el vínculo **Agregar nuevo** para agregar una configuración de HTTP.
    
        :::image type="content" source="./media/integrate-with-application-gateway/add-new-http-setting.png" alt-text="Captura de pantalla del vínculo Agregar nuevo para agregar una configuración de HTTP.":::
    
    * Los valores de HTTP se muestran a continuación:
    
        | Parámetro                     | Value                                                        | Descripción                                                  |
        | ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
        | Nombre de la configuración de HTTP             | Por ejemplo: `https-setting`                                   | Nombre de la configuración de HTTP                                            |
        | Protocolo de back-end              | HTTPS                                                        | Usar cifrado TLS/SSL                                       |
        | Puerto back-end                  | 443                                                          | Puerto HTTPS predeterminado                                           |
        | Usar certificado de entidad de certificación reconocida | Sí                                                          | El nombre de dominio predeterminado de ASE con ILB es **.appserviceenvironment.net**, el certificado de este dominio lo emite una entidad raíz de confianza pública. Puede establecer la configuración de certificado raíz de confianza para usar el **certificado raíz de confianza de la entidad de certificación conocida**. |
        | Reemplazar por un nuevo nombre de host   | Sí                                                          | El encabezado de nombre de host se sobrescribirá al conectarse a la aplicación en ASE con ILB |
        | Sustitución del nombre de host            | Seleccionar el nombre de host del destino de back-end | Al establecer el grupo de back-end en App Service, puede elegir el host del destino de back-end |
        | Crear sondeos personalizados | No | Usar sondeo de estado predeterminado|
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-setting.png" alt-text="Captura de pantalla de la adición de detalles de una configuración de HTTP.":::


## <a name="configure-an-application-gateway-integration-with-ilb-ase"></a>Configuración de la integración de una puerta de enlace de aplicación con ASE con ILB

Para acceder a ASE con ILB desde la puerta de enlace de aplicación, debe comprobar si hay un vínculo de red virtual a la zona DNS privada. Si no hay ninguna red virtual vinculada a la red virtual de la puerta de enlace de aplicación, agregue un vínculo de red virtual con los pasos siguientes.

### <a name="configure-virtual-network-links-with-a-private-dns-zone"></a>Configuración de vínculos de red virtual con una zona DNS privada

* Para configurar el vínculo de red virtual con la zona DNS privada, vaya al plano de configuración de la zona DNS privada. Seleccione **Vínculos de red virtual** > **Agregar**. 

:::image type="content" source="./media/integrate-with-application-gateway/add-vnet-link.png" alt-text="Adición de un vínculo de red virtual a la zona DNS privada.":::

* Escriba el **nombre del vínculo** y seleccione la suscripción y la red virtual correspondientes en las que reside la puerta de enlace de aplicación.

:::image type="content" source="./media/integrate-with-application-gateway/vnet-link.png" alt-text="Captura de pantalla de la introducción de los detalles del nombre del vínculo en el valor Vínculos de red virtual en la zona DNS privada.":::

* Puede confirmar el estado de mantenimiento del back-end desde **Estado del back-end** en el plano de la puerta de enlace de aplicación.

:::image type="content" source="./media/integrate-with-application-gateway/backend-health.png" alt-text="Captura de pantalla de confirmación del estado de mantenimiento del back-end en Estado del back-end.":::

### <a name="add-a-public-dns-record"></a>Adición de un registro DNS público

Debe configurar una asignación de DNS adecuada al acceder a la puerta de enlace de aplicación desde Internet.

* La dirección IP pública de la puerta de enlace de aplicación se puede encontrar en **Configuraciones de IP de front-end** en el plano de la puerta de enlace de aplicación.

:::image type="content" source="./media/integrate-with-application-gateway/frontend-ip.png" alt-text="La dirección IP de front-end de la puerta de enlace de aplicación se puede encontrar en Configuración de IP de front-end.":::

* Con el servicio Azure DNS como ejemplo, puede agregar un conjunto de registros para asignar el nombre de dominio de la aplicación a la dirección IP pública de la puerta de enlace de aplicación.

:::image type="content" source="./media/integrate-with-application-gateway/dns-service.png" alt-text="Captura de pantalla de la adición de un conjunto de registros para asignar el nombre de dominio de la aplicación a la dirección IP pública de la puerta de enlace de aplicación.":::

### <a name="validate-connection"></a>Validación de la conexión

* En una máquina con acceso desde Internet, puede comprobar la resolución de nombres del nombre de dominio de la aplicación en la dirección IP pública de la puerta de enlace de aplicación.

:::image type="content" source="./media/integrate-with-application-gateway/name-resolution.png" alt-text="Validación de la resolución de nombres desde un símbolo del sistema.":::

* En una máquina con acceso desde Internet, pruebe el acceso web desde un explorador.

:::image type="content" source="./media/integrate-with-application-gateway/access-web.png" alt-text="Captura de pantalla de la apertura de un explorador y el acceso a la web.":::

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[creation]: ./creation.md
[createfromtemplate]: ./create-from-template.md
[createprivatednszone]: ../../dns/private-dns-getstarted-portal.md
[AppServiceEnvironmentoverview]: ./overview.md
[privatednszone]: ../../dns/private-dns-overview.md
[Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal]: ../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md
