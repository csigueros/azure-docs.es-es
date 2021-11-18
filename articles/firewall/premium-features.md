---
title: Características de Azure Firewall Prémium
description: Azure Firewall Prémium es un servicio administrado de seguridad de red basado en la nube que protege los recursos de Microsoft Azure Virtual Network.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: d00bfe9e9ed43ed1eafc65d61718de6cb4ecf04e
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179844"
---
# <a name="azure-firewall-premium-features"></a>Características de Azure Firewall Prémium

:::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="Logotipo de certificación ICSA" border="false"::: :::image type="content" source="media/premium-features/pci-logo.png" alt-text="Logotipo de certificación PCI" border="false":::

Azure Firewall Premium proporciona una protección contra amenazas avanzada que satisface las necesidades de entornos altamente confidenciales y regulados, como los sectores de pagos y atención sanitaria. 

Las organizaciones pueden aprovechar las características de la referencia de almacén (SKU) Premium, como la inspección de IDPS y TLS para evitar que el malware y los virus se propaguen entre redes en las direcciones lateral y horizontal. Para satisfacer las mayores demandas de rendimiento de la inspección de IDPS y TLS, Azure Firewall Premium usa una SKU de máquina virtual más eficaz. Al igual que la SKU Estándar, la SKU Premium se puede escalar verticalmente sin problemas hasta 30 Gbps e integrarse con zonas de disponibilidad para posibilitar un contrato de nivel de servicio (SLA) del 99,99 %. La SKU Premium cumple con las necesidades del entorno Estándar de seguridad de datos (PCI DSS) del sector de tarjetas de pago.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Diagrama de información general de Azure Firewall Prémium":::

Azure Firewall Premium incluye las siguientes características:

- **Inspección de TLS**: descifra el tráfico saliente, procesa los datos y, luego, los cifra y los envía al destino.
- **IDPS**: sistema de detección y prevención de intrusiones en la red (IDPS) que permite supervisar la actividad malintencionada de la red, registrar información sobre esta actividad, notificarla y, opcionalmente, intentar bloquearla.
- **Filtrado de direcciones URL**: extiende la funcionalidad de filtrado de nombres de dominio completos de Azure Firewall para tener en cuenta una dirección URL completa. Por ejemplo, `www.contoso.com/a/c` en lugar de `www.contoso.com`.
- **Categorías web**: los administradores pueden permitir o denegar el acceso de los usuarios a categorías de sitios web como, por ejemplo, sitios web de apuestas, de redes sociales u otros.

## <a name="tls-inspection"></a>La inspección de TLS

Azure Firewall Prémium termina las conexiones TLS de salida y horizontales de derecha a izquierda. La inspección de TLS entrante es compatible con [Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) lo cual permite el cifrado completo. Azure Firewall realiza las funciones de seguridad de valor agregado necesarias y vuelve a cifrar el tráfico que se envía al destino original.

> [!TIP]
> TLS 1.0 y 1.1 están en desuso y no tendrán soporte técnico. Se ha detectado que las versiones TLS 1.0 y 1.1 de TLS/Capa de sockets seguros (SSL) son vulnerables y, aunque todavía funcionan para permitir la compatibilidad con versiones anteriores, no se recomiendan. Migre a la versión TLS 1.2 lo antes posible.

Para más información sobre los requisitos de los certificados de la entidad de certificación intermedia de Azure Firewall Premium, consulte [Certificados de Azure Firewall Premium](premium-certificates.md).

## <a name="idps"></a>IDPS

Sistema de detección y prevención de intrusiones en la red (IDPS) que permite supervisar la actividad malintencionada de la red, registrar información sobre esta actividad, notificarla y, opcionalmente, intentar bloquearla. 

Azure Firewall Premium proporciona un IDPS basado en firma que permite detectar ataques rápidamente mediante la búsqueda de patrones específicos, como secuencias de bytes en el tráfico de red o las secuencias de instrucciones malintencionadas conocidas que utiliza el malware. Las firmas IDPS son aplicables al tráfico de nivel de aplicación y de red (capas 4-7), están totalmente administradas y se actualizan continuamente. IDPS se puede aplicar al tráfico entrante, de radio a radio (horizontal de derecha a izquierda) y saliente.

Las firmas y los conjuntos de reglas de Azure Firewall incluyen:
- Énfasis en la aplicación de la huella digital al malware real, comando y control, kits de vulnerabilidad de seguridad y en la actividad malintencionada descontrolada que se omiten en los métodos de prevención tradicionales.
- Más de 58 000 reglas en más de 50 categorías.
    - Las categorías incluyen comando y control de malware, suplantación de identidad (phishing), troyanos, redes de robots (botnets), eventos informativos, vulnerabilidades de seguridad, protocolos de red SCADA, actividad del kit de vulnerabilidades de seguridad, etc.
- Se publican entre 20 y 40 reglas nuevas o más cada día.
- Baja puntuación de falsos positivos mediante el uso de un espacio aislado de malware de última generación y el bucle de comentarios de red del sensor global.

IDPS permite detectar ataques en todos los puertos y protocolos para el tráfico no cifrado. Sin embargo, cuando hay que inspeccionar el tráfico HTTPS, Azure Firewall puede usar su funcionalidad de inspección de TLS para descifrar el tráfico y detectar mejor las actividades malintencionadas.  

La lista de omisión de IDPS le permite no filtrar el tráfico a ninguna de las direcciones IP, los intervalos y las subredes especificados en la lista de omisión.

También puede usar reglas de firma cuando el modo IDPS está establecido en **Alert**, pero hay una o más firmas específicas que desea bloquear, incluido su tráfico asociado. En este caso, puede agregar nuevas reglas de firma estableciendo el modo de inspección de TLS en **Deny**.


## <a name="url-filtering"></a>Filtrado para direcciones URL

El filtrado de direcciones URL extiende la funcionalidad de filtrado de nombres de dominio completos de Azure Firewall para que tenga en cuenta direcciones URL completas. Por ejemplo, `www.contoso.com/a/c` en lugar de `www.contoso.com`.  

El filtrado de direcciones URL se puede aplicar al tráfico HTTP y HTTPS. Cuando se inspecciona el tráfico HTTPS, Azure Firewall Premium puede usar su funcionalidad de inspección de TLS para descifrar el tráfico y extraer la dirección URL de destino para validar si se permite el acceso. La inspección de TLS requiere participación en el nivel de reglas de la aplicación. Una vez habilitada, puede usar las direcciones URL para filtrar con HTTPS. 

## <a name="web-categories"></a>Categorías web

Las categorías web hacen que los administradores puedan permitir o denegar el acceso de los usuarios a categorías de sitios web como, por ejemplo, sitios web de apuestas, de redes sociales u otros. Las categorías web también se incluirán en Azure Firewall Estándar, pero estarán más perfeccionadas en Azure Firewall Premium. Frente a la funcionalidad de categorías web de la SKU Estándar que coincide con la categoría basada en un nombre de dominio completo, la SKU Prémium coincide con la categoría según la dirección URL completa tanto para el tráfico HTTP como para el HTTPS. 

Por ejemplo, si Azure Firewall intercepta una solicitud HTTPS para `www.google.com/news`, se espera la siguiente categorización: 

- Firewall Estándar: solo se examinará la parte del nombre de dominio completo, por lo que `www.google.com` se clasificará como *Motor de búsqueda*. 

- Firewall Prémium: se examinará la dirección URL completa, por lo que se `www.google.com/news` clasificará como *Noticias*.

Las categorías se organizan en función de la gravedad en **Responsabilidad**, **Ancho de banda alto**, **Uso empresarial**, **Pérdida de productividad**, **Navegación general** y **Sin categoría**. Para obtener una descripción detallada de las categorías web, vea [Categorías web de Azure Firewall](web-categories.md).

### <a name="web-category-logging"></a>Registro de categorías web
Puede ver el tráfico filtrado por **Categorías web** en los registros de aplicaciones. El campo **Categorías web** solo se muestra si se ha configurado explícitamente en las reglas de aplicación de directivas de firewall. Por ejemplo, si no dispone de una regla que deniegue explícitamente los *motores de búsqueda*, y un usuario solicita ir a www.bing.com, solo se muestra un mensaje de denegación predeterminado en lugar de un mensaje de categorías web. Esto se debe a que la categoría web no se configuró explícitamente.

### <a name="category-exceptions"></a>Excepciones de las categorías

Puede crear excepciones a las reglas de la categoría web. Cree una colección de reglas de permiso o denegación independiente con una prioridad más alta dentro del grupo de recopilación de reglas. Por ejemplo, puede configurar una colección de reglas que permita `www.linkedin.com` con prioridad 100, con una colección de reglas que deniegue las **redes sociales** con prioridad 200. Esto crea la excepción para la categoría web **Redes sociales** predefinida.

### <a name="web-category-search"></a>Búsqueda de categorías web

Puede identificar a qué categoría corresponden un FQDN o una dirección URL determinados mediante la característica de **comprobación de categorías web**. Para usarlo, seleccione la pestaña **Categorías web** en **Configuración de directiva de firewall**. Esto es especialmente útil al definir las reglas de aplicación para el tráfico de destino.

:::image type="content" source="media/premium-features/firewall-category-search.png" alt-text="Cuadro de diálogo de búsqueda de categorías de firewall":::

### <a name="category-change"></a>Cambio de categoría

En la pestaña **Categorías web** de **Configuración de directiva de firewall**, puede solicitar un cambio de categorización si: 

- Cree que un FQDN o una dirección URL deben estar en otra categoría 

   o bien 

- Tiene una categoría sugerida para un FQDN o una dirección URL sin categoría 

 Una vez que envíe un informe de cambio de categoría, se le proporcionará un token en las notificaciones que indican que hemos recibido la solicitud de procesamiento. Puede comprobar si la solicitud está en curso, denegada o aprobada si escribe el token en la barra de búsqueda.  Asegúrese de guardar el id. de token para hacerlo.

:::image type="content" source="media/premium-features/firewall-category-change.png" alt-text="Cuadro de diálogo de informe de categoría de firewall":::

 ## <a name="supported-regions"></a>Regiones admitidas

Azure Firewall Premium se admite en las siguientes regiones:

- Centro de Australia (Público/Australia)
- Centro de Australia 2 (Público/Australia)
- Este de Australia (Público/Australia)
- Sudeste de Australia (Público/Australia)
- Sur de Brasil (Público/Brasil)
- Sudeste de Brasil (Público/Brasil)
- Centro de Canadá (Público/Canadá)
- Este de Canadá (Público/Canadá)
- Centro de la India (Público/India)
- Centro de EE. UU. (Público/Estados Unidos)
- Centro de EE. UU. EUAP (Público/Canarias (EE. UU.))
- Norte de China 2 (Mooncake/China)
- Este de China 2 (Mooncake/China)
- Este de Asia (Público/Asia Pacífico)
- Este de EE. UU. (Público/Estados Unidos)
- Este de EE. UU. 2 (Público/Estados Unidos)
- Centro de Francia (Público/Francia)
- Sur de Francia (Público/Francia)
- Centro-oeste de Alemania (Público/Alemania)
- Este de Japón (Público/Japón)
- Oeste de Japón (Público/Japón)
- Centro de Corea del Sur (público/Corea)
- Sur de Corea del Sur (Público/Corea)
- Centro-norte de EE. UU. (Público/Estados Unidos)
- Norte de Europa (Público/Europa)
- Este de Noruega (Público/Noruega)
- Norte de Sudáfrica (Público/Sudáfrica)
- Centro-sur de EE. UU. (Público/Estados Unidos)
- Sur de la India (Público/India)
- Sudeste Asiático (Público/Asia Pacífico)
- Norte de Suiza (Pública/Suiza)
- Centro de Emiratos Árabes Unidos (Público/EAU)
- Norte de Emiratos Árabes Unidos (Público/EAU)
- Sur de Reino Unido (Público/Reino Unido)
- Oeste de Reino Unido (Público/Reino Unido)
- USGov Arizona (Fairfax/USGov)
- USGov Texas (Fairfax/USGov)
- USGov Virginia (Fairfax/USGov)
- Centro-oeste de EE. UU. (Público/Estados Unidos)
- Oeste de Europa (Público/Europa)
- Oeste de la India (Público/India)
- Oeste de EE. UU. (Público/Estados Unidos)
- Oeste de EE. UU. 2 (Público/Estados Unidos)
- Oeste de EE. UU. 3 (Público/Estados Unidos)


## <a name="known-issues"></a>Problemas conocidos

Azure Firewall Premium presenta los siguientes problemas conocidos:


|Problema  |Descripción  |Mitigación  |
|---------|---------|---------|
|Compatibilidad de ESNI con la resolución de nombres de dominio completo en HTTPS|Indicación de nombre de servidor cifrada no se admite en el protocolo de enlace HTTPS.|Actualmente, solo Firefox admite ESNI a través de la configuración personalizada. La solución que se recomienda es deshabilitar esta característica.|
|Certificados de cliente (TLS)|Los certificados de cliente se usan para crear una confianza de identidad mutua entre el cliente y el servidor. Los certificados de cliente se usan durante una negociación de TLS. Azure Firewall vuelve a negociar una conexión con el servidor y no tiene acceso a la clave privada de los certificados de cliente.|None|
|QUIC/HTTP3|QUIC es la nueva versión principal de HTTP. Es un protocolo basado en UDP a través de los puertos 80 (PLAN) y 443 (SSL). No se admitirá la inspección de FQDN/URL/TLS.|Configure el uso de UDP 80/443 como reglas de red.|
Certificados firmados por clientes que no son de confianza|El firewall no confía en los certificados firmados por el cliente una vez que se han recibido de un servidor web basado en intranet.|Se está investigando una solución.
|Dirección IP de origen incorrecta en las alertas con IDPS para HTTP (sin inspección de TLS).|Cuando el tráfico HTTP de texto sin formato está en uso e IDPS emite una nueva alerta y el destino es una dirección IP pública, la dirección IP de origen que se muestra es incorrecta (se muestra la dirección IP interna, en lugar de la dirección IP original).|Se está investigando una solución.|
|Propagación de certificados|Cuando se aplica un certificado de entidad de certificación en el firewall, puede tardar entre 5 y 10 minutos en surtir efecto.|Se está investigando una solución.|
|Compatibilidad con TLS 1.3|TLS 1.3 se admite de forma parcial. El túnel TLS del cliente al firewall se basa en TLS 1.2 y desde el firewall al servidor web externo en TLS 1.3.|Se están investigando actualizaciones.|
|Punto de conexión privado KeyVault|KeyVault admite el acceso de punto de conexión privado para limitar la exposición de la red. Los Servicios de Azure de confianza pueden omitir esta limitación si se configura una excepción como se describe en la [documentación de KeyVault](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). Azure Firewall no aparece actualmente como un servicio de confianza y no puede tener acceso a Key Vault.|Se está investigando una solución.|

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre los certificados de Azure Firewall Prémium](premium-certificates.md)
- [Implementación y configuración de Azure Firewall Prémium](premium-deploy.md)
- [Migración a Azure Firewall Prémium](premium-migrate.md)
