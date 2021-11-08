---
title: 'Microsoft Defender para App Service: ventajas y características'
description: Obtenga información sobre las funcionalidades de Microsoft Defender para App Service y cómo habilitarlo en una suscripción.
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: be03a03d517701e50b4c2e8bfbc7949273c1200e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428812"
---
# <a name="protect-your-web-apps-and-apis"></a>Protección de las API y las aplicaciones web

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="prerequisites"></a>Prerrequisitos

Defender for Cloud está integrado de forma nativa con App Service, lo que elimina la necesidad de implementación e incorporación; la integración es transparente.

Para proteger el plan de Azure App Service con Microsoft Defender para App Service, necesitará lo siguiente:

- Un plan de App Service admitido asociado a máquinas dedicadas. Los planes admitidos se enumeran en [Disponibilidad](#availability).

- Las protecciones mejoradas de Defender for Cloud habilitadas en su suscripción, como se describe en [Inicio rápido: Habilitación de características de seguridad mejoradas](enable-enhanced-security.md).

    > [!TIP]
    > Opcionalmente, puede habilitar planes individuales de Microsoft Defender, como Microsoft Defender para App Service.

## <a name="availability"></a>Disponibilidad

| Aspecto                       | Detalles                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado de la versión:               | Disponibilidad general (GA)                                                                                                                                                                      |
| Precios:                     | Microsoft Defender para App Service se factura como se muestra en la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).<br>La facturación se realiza en función de las instancias de proceso totales en todos los planes.       |
| Planes de App Service admitidos: | Se admiten [todos los planes de App Service](https://azure.microsoft.com/pricing/details/app-service/plans/) excepto [Azure Functions en el plan de consumo](../azure-functions/functions-scale.md). |
| Nubes:                      | :::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nacionales o soberanas (Azure Government, Azure China 21Vianet)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-microsoft-defender-for-app-service"></a>¿Cuáles son las ventajas de Microsoft Defender para App Service?

Azure App Service es una plataforma totalmente administrada para crear y hospedar aplicaciones web y API. Al ser totalmente administrada, no hay que preocuparse por la infraestructura. Proporciona administración, supervisión y conclusiones operativas para satisfacer requisitos de seguridad, cumplimiento normativo y rendimiento de nivel empresarial. Para más información, consulte [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Microsoft Defender para App Service** usa la escala de la nube para identificar ataques dirigidos a aplicaciones que se ejecutan mediante App Service. Los atacantes sondean las aplicaciones web para buscar y aprovechar los puntos débiles. Antes de enrutarse a entornos específicos, las solicitudes para las aplicaciones que se ejecutan en Azure atraviesan varias puertas de enlace donde se las inspecciona y registra. A continuación, estos datos se usan para identificar vulnerabilidades y atacantes, así como para aprender nuevos patrones que se usarán más adelante.

Al habilitar Microsoft Defender para App Service, se beneficia inmediatamente de los siguientes servicios que ofrece este plan de Defender:

- **Protección**: Defender para App Service evalúa los recursos incluidos en el plan de App Service y genera recomendaciones de seguridad en función de lo que halle. Siga las instrucciones detalladas de estas recomendaciones para proteger los recursos de App Service.

- **Detección**: Defender para App Service detecta un elevado número de amenazas para los recursos de App Service, ya que supervisa:
    - la instancia de la máquina virtual en la que se ejecuta App Service y su interfaz de administración.
    - las solicitudes y respuestas tanto que se envían a las aplicaciones de App Service, como que se envían desde ellas
    - los espacios aislados y las máquinas virtuales subyacentes
    - los registros internos de App Service (disponibles gracias a la visibilidad que tiene Azure como proveedor de nube)

Como solución nativa en la nube, Defender para App Service puede identificar metodologías de ataque que se aplican a varios destinos. Por ejemplo, desde un único host sería difícil identificar un ataque distribuido desde un pequeño subconjunto de direcciones IP que rastrean puntos de conexión similares en varios hosts.

Conjuntamente, los datos del registro y la infraestructura puede indicar lo que ocurre: desde un nuevo ataque que prolifera en la red hasta riesgos concretos en las máquinas de los clientes. Por lo tanto, incluso si se implementa Microsoft Defender para App Service una vez que se han aprovechado las vulnerabilidades de una aplicación web, es posible que pueda detectar los ataques en curso.


## <a name="what-threats-can-defender-for-app-service-detect"></a>¿Qué amenazas puede detectar Defender para App Service?

### <a name="threats-by-mitre-attck-tactics"></a>Amenazas por tácticas de MITRE ATT&CK

Defender for Cloud supervisa muchas amenazas para los recursos de App Service. Las alertas abarcan casi la lista completa de tácticas de MITRE ATT&CK, desde los ataques previos al comando y el control.

- **Amenazas previas a ataques**: Defender for Cloud puede detectar la ejecución de varios tipos de detectores de vulnerabilidades que los atacantes usan con frecuencia para sondear las aplicaciones en busca de puntos débiles.

- **Amenazas en el acceso inicial** - [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) alimenta estas alertas que incluyen el desencadenamiento de una alerta cuando una dirección IP malintencionada conocida se conecta a la interfaz de FTP de Azure App Service.

- **Amenazas a la ejecución**: Defender for Cloud puede detectar intentos de ejecución de comandos con privilegios elevados, comandos de Linux en Windows App Service, comportamiento de ataque sin archivos, herramientas de minería de moneda digital y muchas otras actividades de ejecución de código sospechosas y malintencionadas.

### <a name="dangling-dns-detection"></a>Detección de DNS pendiente

Defender para App Service también identifica las entradas de DNS que queden en el registrador de DNS cuando se retira un sitio web de App Service (se conocen como entradas DNS pendientes). Cuando se quita un sitio web, pero no se quita su dominio personalizado del registrador de DNS, la entrada DNS apunta a un recurso que no existe y el subdominio es vulnerable a una adquisición. Defender for Cloud no examina el registrador de DNS en busca de entradas DNS pendientes *existentes*, sino que le avisa cuando se retira un sitio web de App Service y su dominio personalizado (entrada DNS) no se elimina.

Las adquisiciones de subdominios son una amenaza muy grave que se producen de forma común en las organizaciones. Cuando un actor de amenazas detecta una entrada DNS pendiente, crea su propio sitio en la dirección de destino. A continuación, el tráfico dirigido al dominio de la organización se dirige al sitio del actor de amenazas, que este puede usar ese tráfico para una amplia gama de actividades malintencionadas.

La protección contra DNS pendiente está disponibles tanto si los dominios se administran con Azure DNS como con un registrador de dominios externo y se aplica a App Service en Windows y en Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Ejemplo de una alerta sobre una entrada DNS desenlazándose detectada. Habilite Microsoft Defender para App Service para recibir esta y otras alertas para su entorno." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Para más información sobre DNS pendientes y la amenaza de adquisición de subdominios, consulte [Evitar las entradas DNS pendientes y evitar la adquisición de subdominios](../security/fundamentals/subdomain-takeover.md).

Para obtener una lista completa de las alertas de App Service, consulte la [Tabla de referencia de alertas](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> Es posible que Defender no desencadene alertas de DNS pendientes si el dominio personalizado no apunta directamente a un recurso de App Service o si Defender no ha supervisado el tráfico a su sitio web desde que se habilitó la protección contra DNS pendiente (porque no habrá registros que ayuden a identificar el dominio personalizado).

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre Microsoft Defender para App Service. 

> [!div class="nextstepaction"]
> [Habilitación de las protecciones mejoradas](enable-enhanced-security.md)

Para obtener material relacionado, consulte los siguientes artículos: 

- Para exportar las alertas a Microsoft Sentinel, la SIEM de terceros, o cualquier otra herramienta externa, siga las instrucciones de [Transmisión de alertas a una solución de administración de servicios de TI, SIEM o SOAR](export-to-siem.md).
- Para obtener una lista de las alertas de Microsoft Defender para App Service, consulte la [Tabla de referencia de alertas](alerts-reference.md#alerts-azureappserv).
- Para más información sobre los planes de App Service, consulte [Planes de App Service](https://azure.microsoft.com/pricing/details/app-service/plans/).