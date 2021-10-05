---
title: 'Azure Front Door: preguntas frecuentes'
description: En esta página se proporcionan respuestas a las preguntas frecuentes sobre Azure Front Door Estándar/Prémium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/18/2021
ms.author: duau
ms.openlocfilehash: 378ba28eaa4b68cb1d05fa0af5c11ca95bd52a8b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210333"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Preguntas frecuentes sobre Azure Front Door Estándar/Prémium (versión preliminar)

En este artículo se responden preguntas comunes sobre la funcionalidad y las características de Azure Front Door.

## <a name="general"></a>General

### <a name="what-is-azure-front-door"></a>¿Qué es Azure Front Door?

Azure Front Door es una red CDN de nube moderna, rápida, confiable y segura con protección de amenazas inteligente. Proporciona aceleración de contenido estático y dinámico, equilibrio de carga global y seguridad mejorada para las aplicaciones globales a gran escala, las API, los sitios web y los servicios en la nube gracias a la protección contra amenazas inteligente.

### <a name="what-features-does-azure-front-door-support"></a>¿Qué características admite Azure Front Door?

Azure Front Door admite:

* Aceleración tanto de las aplicaciones dinámicas como del contenido estático
* Descarga de TLS/SSL y TLS de extremo a extremo
* Firewall de aplicaciones web
* Afinidad de sesión basada en cookies
* Enrutamiento basado en URL.
* Certificados gratuitos y varias administraciones de dominios

Para obtener una lista completa de las características admitidas, consulte [Overview of Azure Front Door](overview.md) (Información general de Azure Front Door).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>¿Cuál es la diferencia entre Azure Front Door y Azure Application Gateway?

Si bien tanto Front Door como Application Gateway son equilibradores de carga de capa 7 (HTTP/HTTPS), la principal diferencia es que Front Door es un servicio global. Application Gateway es un servicio regional. Mientras que Front Door puede equilibrar la carga entre las diferentes unidades de escalado, los clústeres y las unidades de marca entre regiones, Application Gateway le permite equilibrar la carga entre las máquinas virtuales y contenedores que se encuentran dentro de la unidad de escalado.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>¿Cuándo se debe implementar una instancia de Application Gateway detrás de Front Door?

Los principales escenarios en que se debería usar Application Gateway detrás de Front Door son los siguientes:

* Front Door puede realizar el equilibrio de carga basado en rutas solo a nivel global, pero si se desea equilibrar la carga del tráfico incluso a nivel de la red virtual, debería utilizarse Application Gateway.
* Puesto que Front Door no funciona en una máquina virtual o un contenedor, no puede hacer el drenaje de conexiones. Por contra, Application Gateway sí le permite hacer purga de conexiones. 
* Con una instancia de Application Gateway detrás de Front Door, es posible lograr el 100 % de la descarga de TLS/SSL y enrutar solo solicitudes HTTP dentro de su red virtual (VNET).
* Tanto Front Door como Application Gateway admiten la afinidad de la sesión. Front Door puede dirigir el tráfico subsiguiente desde una sesión de usuario hasta el mismo clúster o servidor back-end de una región dada. Application Gateway puede establecer la afinidad del tráfico con el mismo servidor dentro del clúster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>¿Es posible implementar Azure Load Balancer detrás de Front Door?

Azure Front Door necesita una VIP pública o un nombre DNS disponible públicamente al que enrutar el tráfico. La implementación de Azure Load Balancer detrás de Front Door es un caso de uso común.

### <a name="what-protocols-does-azure-front-door-support"></a>¿Qué protocolos admite Azure Front Door?

Azure Front Door admite HTTP, HTTPS y HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>¿Cómo admite HTTP/2 el servicio Azure Front Door?

La compatibilidad con el protocolo HTTP/2 está disponible únicamente para los clientes que se conectan a Azure Front Door. La comunicación con los back-end en el grupo de back-end se produce a través de HTTP/1.1. La compatibilidad con HTTP/2 está habilitada de forma predeterminada.

### <a name="what-resources-are-supported-today-as-part-of-an-origin-group"></a>¿Qué recursos se admiten actualmente como parte del grupo de origen?

Los grupos de origen pueden estar compuestos por dos tipos de orígenes:

- Los orígenes públicos incluyen cuentas de almacenamiento, aplicaciones de App Service, instancias de Kubernetes o cualquier otro nombre de host personalizado que tenga conectividad pública. Estos orígenes deben definirse a través de una dirección IP pública o un nombre de host DNS que se pueda resolver públicamente. Los miembros del grupo de origen pueden estar implementados en zonas de disponibilidad, regiones e incluso fuera de Azure, siempre y cuando dispongan de conectividad pública. Los orígenes públicos se admiten en los niveles Estándar y Premium de Azure Front Door.
- Los [orígenes de Private Link](concept-private-link.md) están disponibles cuando se usa Azure Front Door (Premium).

### <a name="what-regions-is-the-service-available-in"></a>¿En qué regiones está disponible el servicio?

Azure Front Door es un servicio global y no está asociado a ninguna región de Azure específica. La única ubicación que debe especificar al crear una instancia de Front Door es la del grupo de recursos. Esa ubicación especifica básicamente dónde se almacenarán los metadatos del grupo de recursos. El propio recurso de Front Door se crea como un recurso global, y la configuración se implementa globalmente en todas las ubicaciones perimetrales. 

### <a name="where-are-the-edge-locations-for-azure-front-door"></a>¿Cuáles son las ubicaciones perimetrales de Azure Front Door?

Para obtener la lista completa de las ubicaciones perimetrales de Azure Front Door, consulte [Ubicaciones perimetrales de Azure Front Door](edge-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>¿Azure Front Door es una implementación dedicada para mi aplicación o se comparte entre clientes?

Azure Front Door es un servicio multiinquilino distribuido globalmente. La infraestructura de Front Door se comparte entre todos sus clientes. Al crear un perfil de Front Door, se define la configuración específica necesaria para su aplicación. Los cambios realizados en Front Door no afectan a otras configuraciones de este servicio.

### <a name="is-http-https-redirection-supported"></a>¿Se admite el redireccionamiento HTTP->HTTPS?

Sí. De hecho, Azure Front Door admite la redirección de cadenas de host, ruta de acceso y consulta, así como de parte de la dirección URL. Obtenga más información sobre la [redirección de URL](concept-rule-set-url-redirect-and-rewrite.md). 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>¿Cómo puedo hacer que Azure Front Door sea el único que tenga acceso a mi back-end?

La mejor manera de bloquear la aplicación para que solo acepte tráfico de la instancia de Front Door específica es publicar la aplicación a través de un punto de conexión privado. El tráfico de red entre Front Door y la aplicación atraviesa la red virtual y un vínculo privado de la red troncal de Microsoft, lo que elimina la exposición desde la red pública de Internet.

Más información sobre el [origen de protección de Front Door con Private Link](concept-private-link.md).  

Una manera alternativa de bloquear la aplicación para que solo acepte el tráfico de la instancia específica de Front Door es configurar las ACL de IP para el servidor back-end. A continuación, restrinja el tráfico del servidor back-end al valor específico del encabezado "X-Azure-FDID" enviado por Front Door. Estos pasos se detallan a continuación:

* Configure la creación de listas de control de acceso de IP de los back-end para que acepten tráfico únicamente del espacio de direcciones IP de back-end de Azure Front Door y de los servicios de infraestructura de Azure. Consulte a continuación los detalles sobre IP para incluir en la lista de control de acceso el servidor back-end:
 
    * Consulte la sección *AzureFrontDoor.Backend* en [Etiquetas de servicio e intervalos de direcciones IP de Azure](https://www.microsoft.com/download/details.aspx?id=56519) para conocer el intervalo de direcciones IP del servidor back-end IPv4 de Front Door. También puede usar la etiqueta de servicio *AzureFrontDoor.Backend* en los [grupos de seguridad de red](../../virtual-network/network-security-groups-overview.md#security-rules).
    * [Servicios de infraestructura básica](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) de Azure mediante las direcciones IP de host virtualizadas `168.63.129.16` y `169.254.169.254`.

    > [!WARNING]
    > El espacio de back-end IP de Front Door puede cambiar más adelante; sin embargo, nos aseguraremos de que antes de que eso suceda nos hayamos integrado con los [intervalos de IP y etiquetas de servicio de Azure](https://www.microsoft.com/download/details.aspx?id=56519). Recomendamos que se suscriba a los [intervalos de IP y etiquetas de servicio de Azure](https://www.microsoft.com/download/details.aspx?id=56519) para conocer los cambios o actualizaciones.

* Realice una operación GET en Front Door con la versión de API `2020-01-01` o superior. En la llamada API, busque el campo `frontdoorID`. Filtre por el encabezado entrante "**X-Azure-FDID**" que envió Front Door al servidor back-end con el valor del campo `frontdoorID`. Encontrará el valor de `Front Door ID` también en la sección de información general de la página del portal de Front Door. 

* Aplique el filtrado de reglas en el servidor web back-end para restringir el tráfico basado en el valor del encabezado "X-Azure-FDID" resultante.

  Este es un ejemplo de [Microsoft Internet Information Services (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

* Azure Front Door también admite la etiqueta de servicio *AzureFrontDoor.Frontend*, que proporciona la lista de direcciones IP que los clientes usan al conectarse a Front Door. Puede usar la etiqueta de servicio *AzureFrontDoor.Frontend* al controlar el tráfico saliente que se debe permitir para conectarse a los servicios implementados detrás de Azure Front Door. Azure Front Door también admite una etiqueta de servicio adicional, *AzureFrontDoor.FirstParty*, para integrarse internamente con otros servicios de Azure. Consulte las [etiquetas de servicio disponibles](../../virtual-network/service-tags-overview.md#available-service-tags) para obtener más información sobre casos de uso de etiquetas de servicio de Azure Front Door.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>¿Puede cambiar la dirección IP de difusión por proximidad durante la vigencia de mi instancia de Front Door?

La dirección IP de difusión por proximidad de su instancia de Front Door normalmente no debería cambiar y es posible que permanezca estática durante la vigencia de Front Door. Sin embargo, esto no se puede **garantizar**. No asuma ninguna dependencia directa de la dirección IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>¿Azure Front Door admite direcciones IP estáticas o dedicadas?

No, Azure Front Door actualmente no admite direcciones IP de difusión por proximidad de front-end estáticas o dedicadas. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>¿Azure Front Door admite encabezados x-forwarded-for?

Sí, Azure Front Door admite los encabezados X-Forwarded-For, X-Forwarded-Host y X-Forwarded-Proto. Para X-Forwarded-For, si el encabezado ya estaba presente, Front Door le agrega la dirección IP del socket del cliente. En caso contrario, agrega el encabezado con la dirección IP del socket del cliente como valor. En el caso de For X-Forwarded-Host y X-Forwarded-Proto, el valor se invalida.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>¿Cuánto tiempo se tarda en implementar una instancia de Azure Front Door? ¿Mi Front Door sigue funcionando mientras se actualiza?

La mayoría de las actualizaciones de la configuración del motor de reglas se completan en menos de 20 minutos. Puede esperar que la regla surta efecto en cuanto se complete la actualización. 

 > [!Note]  
  > La mayoría de las actualizaciones de certificados TLS/SSL personalizados pueden tardar entre varios minutos y una hora en implementarse globalmente.

Las actualizaciones de las rutas o los grupos de back-end se realizan sin problemas y no provocarán tiempo de inactividad (si la nueva configuración es correcta). Las actualizaciones de certificados no provocarán interrupciones, a menos que cambie de la administración de Front Door al uso de su propio certificado y viceversa.


## <a name="configuration"></a>Configuración

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>¿Puede Azure Front Door equilibrar la carga o enrutar el tráfico dentro de una red virtual?

Azure Front Door (Estándar) requiere una dirección IP pública o un nombre DNS que pueda resolverse públicamente para enrutar el tráfico. Azure Front Door no puede realizar el enrutamiento directamente a los recursos de una red virtual. Para resolver este problema, puede usar una puerta de enlace de aplicaciones o un equilibrador de carga de Azure con una dirección IP pública.

Azure Front Door (Premium) admite el enrutamiento del tráfico a [orígenes de Private Link](concept-private-link.md).

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>¿Cuáles son los distintos tiempos de espera y límites del servicio Azure Front Door?

Conozca todos los [tiempos de espera y límites documentados del servicio Azure Front Door](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>¿Cuánto tiempo tarda una regla en surtir efecto después de agregarla al motor de reglas Front Door?

La mayoría de las actualizaciones de la configuración del motor de reglas se completan en menos de 20 minutos. Puede esperar que la regla surta efecto en cuanto se complete la actualización. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>¿Puedo configurar Azure CDN detrás del perfil de Front Door o Front Door detrás de Azure CDN?

Azure Front Door y Azure CDN no se pueden configurar juntos porque ambos servicios usan los mismos sitios perimetrales de Azure para responder a las solicitudes. 

## <a name="performance"></a>Rendimiento

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>¿Cómo admite Azure Front Door la alta disponibilidad y la escalabilidad?

Azure Front Door es una plataforma multiinquilino distribuida globalmente con una enorme capacidad para satisfacer las necesidades de escalabilidad de la aplicación. Ofrecida desde el perímetro de la red global de Microsoft, Front Door proporciona una funcionalidad de equilibrio de carga global que le permite conmutar por error la aplicación entera o incluso microservicios individuales a través de regiones o nubes diferentes.

## <a name="tls-configuration"></a>Configuración de TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>¿Qué versiones de TLS son compatibles con Azure Front Door?

Todos los perfiles de Front Door creados después de septiembre de 2019 usan TLS 1.2 como valor mínimo predeterminado.

Front Door admite las versiones 1.0, 1.1 y 1.2 de TLS. TLS 1.3 no se admite aún.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>¿Qué certificados se admiten en Azure Front Door?

Para habilitar el protocolo HTTPS en un dominio personalizado de Front Door, puede optar por usar un certificado administrado por Azure Front Door o su propio certificado.
La opción administrada de Front Door aprovisiona un certificado TLS/SSL estándar mediante Digicert y se almacena en Key Vault de Front Door. Si decide usar su propio certificado, puede incorporar un certificado de una entidad de certificación compatible y puede ser un TLS estándar, un certificado de validación extendido o incluso un certificado comodín. No se admiten certificados autofirmados.

### <a name="does-front-door-support-autorotation-of-certificates"></a>¿Front Door admite la rotación automática de certificados?

Para la opción de certificados administrados por Front Door, Front Door rota automáticamente los certificados. Si usa un certificado administrado por Front Door y nota que la fecha de expiración del certificado es inferior a 60 días, cree una incidencia de soporte técnico.

La rotación automática no es compatible con su propio certificado TLS/SSL personalizado. Así como lo configuró la primera vez para un dominio personalizado determinado, Front Door deberá apuntar a la versión de certificado correcta en su instancia de Key Vault. Asegúrese de que la entidad de servicio de Front Door siga teniendo acceso a Key Vault. Esta operación de lanzamiento de certificados actualizados de Front Door es atómica y no afecta a la producción siempre que el nombre del firmante o la SAN del certificado no cambie.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>¿Cuáles son los conjuntos de cifrado actuales que admite Azure Front Door?

Para TLS 1.2, se admiten los siguientes conjuntos de cifrado: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Al usar dominios personalizados con TLS 1.0/1.1 habilitado, se admiten los siguientes conjuntos de cifrado:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>¿Puedo configurar la directiva TLS para controlar las versiones del protocolo TLS?

Puede configurar una versión de TLS mínima en Azure Front Door en la configuración HTTPS del dominio personalizado desde Azure Portal o la [API REST de Azure](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Actualmente, puede elegir entre 1.0 y 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>¿Puedo configurar Front Door para admitir únicamente determinados conjuntos de cifrado?

No, no se admite la configuración de determinados conjuntos de cifrado en Front Door. Puede obtener su propio certificado personalizado TLS/SSL de la entidad de certificación (por ejemplo, Verisign, Entrust, o Digicert). Después, tenga los conjuntos de cifrado específicos marcados en el certificado al generarlo. 

### <a name="does-front-door-support-ocsp-stapling"></a>¿Front Door admite la asociación OCSP?

Sí, de forma predeterminada, la asociación OCSP es compatible con Front Door y no se requiere ninguna configuración.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>¿Admite Azure Front Door también volver a cifrar el tráfico dirigido al back-end?

Sí, Azure Front Door admite la descarga de TLS/SSL y TLS de extremo a extremo, lo cual permite volver a cifrar el tráfico dirigido al servidor back-end. De hecho, puesto que las conexiones con el servidor back-end se producen a través de la dirección IP pública, se recomienda que configure Front Door para que use HTTPS como protocolo de reenvío.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>¿Admite Front Door los certificados autofirmados en el back-end para la conexión HTTPS?

No, los certificados autofirmados no se admiten en Front Door y la restricción se aplica a:

* **Servidores back-end**: no se pueden usar certificados autofirmados cuando se reenvía el tráfico como HTTPS o sondeos de estado HTTPS o se rellena la caché de origen de las reglas de enrutamiento con el almacenamiento en caché habilitado.
* **Servidor front-end**: no se pueden usar certificados autofirmados al utilizar su propio certificado TLS/SSL personalizado para habilitar HTTPS en el dominio personalizado.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>¿Por qué se produce un error del tráfico HTTPS al back-end?

Para tener conexiones HTTPS correctas al back-end, ya sea para sondeos de estado o para reenviar solicitudes, hay dos motivos posibles por los que el tráfico HTTPS puede generar un error:

* **Error de coincidencia de nombre del firmante del certificado**: En el caso de las conexiones HTTPS, Front Door espera que el back-end presente el certificado de una entidad de certificación válida con nombres de firmante que coincidan con el nombre de host del back-end. Por ejemplo, si el nombre de host del servidor back-end se establece en `myapp-centralus.contosonews.net` y el certificado que dicho servidor presenta durante el protocolo de enlace TLS no tiene `myapp-centralus.contosonews.net` o `*myapp-centralus*.contosonews.net` en el nombre del firmante, Front Door rechazará la conexión y se producirá un error. 
    * **Solución**: Aunque no se recomienda desde el punto de vista del cumplimiento, una forma de evitar este error es deshabilitar la comprobación del nombre del firmante del certificado para Front Door. Esta opción se puede encontrar en Configuración en Azure Portal y en BackendPoolsSettings en la API.
* **Certificado de hospedaje del servidor back-end de una entidad de certificación no válida**: solo se pueden usar certificados de [entidades de certificación válidas](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) en el servidor back-end con Front Door. No se permiten certificados de entidades de certificación internas ni certificados autofirmados.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>¿Puedo usar la autenticación mutua o de cliente con Azure Front Door?

No. Si bien Azure Front Door admite TLS 1.2, que presentó la autenticación mutua o de cliente en [RFC 5246](https://tools.ietf.org/html/rfc5246), actualmente, Azure Front Door no admite la autenticación mutua ni de cliente.

## <a name="diagnostics-and-logging"></a>Diagnósticos y registro

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>¿Qué tipos de registros y métricas están disponibles con Azure Front Door?

Para obtener información sobre los registros y otras funcionalidades de diagnóstico, consulte Monitoring metrics and logs for Front Door (Supervisión de métricas y registros para Front Door).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>¿Qué es la directiva de retención en los registros de diagnóstico?

Los registros de diagnóstico fluyen hacia la cuenta de almacenamiento de los clientes y estos pueden establecer la directiva de retención según sus preferencias. Los registros de diagnóstico también se pueden enviar a un centro de eventos o a registros de Azure Monitor. Para más información, consulte [Registro de Azure Front Door](how-to-logs.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>¿Cómo puedo obtener registros de auditoría para Azure Front Door?

Los registros de auditoría están disponibles para Azure Front Door. En el portal, haga clic en **Registro de actividad** en la página de menú de su instancia de Front Door para acceder al registro de auditoría. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>¿Puedo establecer alertas con Azure Front Door?

Sí, Azure Front Door admite las alertas. Las alertas se configuran en métricas. 

## <a name="billing"></a>Facturación

### <a name="will-i-be-billed-for-the-azure-front-door-resources-that-are-disabled"></a>¿Se me facturarán los recursos de Azure Front Door que estén deshabilitados?

Los recursos de Azure Front Door, como los perfiles de Front Door, no se facturan si están deshabilitados.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [crear una instancia de Front Door Estándar/Prémium](create-front-door-portal.md).
