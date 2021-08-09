---
title: Importación de inteligencia sobre amenazas en Azure Sentinel | Microsoft Docs
description: Use fuentes de inteligencia sobre amenazas en Azure Sentinel para analizar datos, detectar amenazas y generar alertas e incidentes. Visualice información de inteligencia sobre amenazas con libros.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 4936cf0dd399f0409332dc2dea2efb8dc9cd8299
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "109810235"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>Importación de inteligencia sobre amenazas en Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>Presentación de la inteligencia sobre amenazas

La inteligencia sobre ciberamenazas (CTI) es información que describe posibles amenazas o amenazas existentes conocidas a sistemas y usuarios. Este tipo de información presenta muchas formas, desde informes escritos que detallan las motivaciones, infraestructura y técnicas de un actor de amenaza determinado hasta observaciones específicas de direcciones IP, dominios y códigos hash de archivo asociados a ciberamenazas. Las organizaciones usan CTI para proporcionar contexto esencial a actividades inusuales, de modo que el personal de seguridad pueda tomar medidas rápidamente para proteger a sus usuarios y recursos. CTI puede proceder de muchos lugares, por ejemplo, fuentes de distribución de datos de código abierto, comunidades de uso compartido de inteligencia sobre amenazas e inteligencia local recopilada durante las investigaciones de seguridad realizadas dentro de una organización.

Dentro de una solución de administración de eventos e información de seguridad (SIEM) como Azure Sentinel, la forma de CTI más usada son los indicadores de amenazas, a los que se suele hacer referencia como indicadores de riesgo o IoC. Los indicadores de amenazas son datos que asocian observaciones como direcciones URL, códigos hash de archivo o direcciones IP con actividad de amenazas conocida como suplantación de identidad (phishing), redes de robots (botnet) o malware. Esta forma de inteligencia sobre amenazas suele llamarse inteligencia sobre amenazas táctica, ya que puede aplicarse a productos de seguridad y automatización a gran escala para proteger y detectar posibles amenazas a una organización. En Azure Sentinel, puede usar indicadores de amenazas para ayudar a detectar actividad malintencionada observada en el entorno y proporcionar contexto a investigadores de seguridad para ayudar a tomar decisiones de respuesta.

Puede integrar la inteligencia sobre amenazas (TI) en Azure Sentinel a través de las actividades siguientes:

- Use **conectores de datos** a varias plataformas de TI para [importar inteligencia sobre amenazas](./connect-threat-intelligence.md) en Azure Sentinel.
- Vea y administre la inteligencia sobre amenazas importada en **Registros** y en la nueva área **Inteligencia sobre amenazas** de Azure Sentinel.
- Use las plantillas de reglas de **análisis** integradas para generar alertas e incidentes de seguridad mediante la inteligencia sobre amenazas importada.
- Visualice información clave sobre la inteligencia sobre amenazas en Azure Sentinel con el **libro de inteligencia sobre amenazas**.

Inteligencia sobre amenazas también proporciona contexto útil dentro de otras experiencias de Azure Sentinel como **Búsqueda** y **Notebooks** y, aunque no se trata en este artículo, se hace mención a estas experiencias en [esta excelente entrada de blog de Ian Hellen sobre los cuadernos de Jupyter Notebook en Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239), que trata el uso de CTI en Notebooks.

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>Conectores de datos de Azure Sentinel para la inteligencia sobre amenazas

Al igual que todos los demás datos de eventos en Azure Sentinel, los indicadores de amenazas se importan mediante conectores de datos. Hay dos conectores de datos en Azure Sentinel proporcionados de forma específica para los indicadores de amenazas, **Inteligencia sobre amenazas: TAXII** y **Plataformas de inteligencia sobre amenazas**. Puede usar el conector de datos solo o ambos conectores juntos, dependiendo del origen de los indicadores de amenazas en la organización. 

Consulte este catálogo de [integraciones de la inteligencia sobre amenazas](threat-intelligence-integration.md) disponibles con Azure Sentinel.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>Incorporación de indicadores de amenazas a Azure Sentinel con el conector de datos Plataformas de inteligencia sobre amenazas

Muchas organizaciones usan soluciones de la plataforma de inteligencia sobre amenazas (TIP) para agregar fuentes de indicadores de amenazas desde diferentes orígenes, mantener los datos dentro de la plataforma y, a continuación, elegir qué indicadores de amenazas se aplicarán a diferentes soluciones de seguridad como dispositivos de red, soluciones de protección contra amenazas avanzada o SIEM como Azure Sentinel. Si la organización usa una solución TIP integrada, como MISP, Anomali ThreatStream, ThreatConnect, EclecticIQ Platform, ThreatQ Threat Intelligence Platform o MineMeld de Palo Alto Networks, el **conector de datos Plataformas de inteligencia contra amenazas** le permite usar TIP para importar indicadores de amenazas a Azure Sentinel. Como el conector trabaja con la [API tiIndicators de Microsoft Graph Security](/graph/api/resources/tiindicator) para lograrlo, cualquier plataforma de inteligencia sobre amenazas personalizada puede usar el conector para aprovechar las ventajas de la API tiIndicators para enviar indicadores a Azure Sentinel (y a otras soluciones de seguridad de Microsoft como ATP de Defender).

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="Ruta de acceso de importación de inteligencia sobre amenazas":::

Siga estos pasos para importar indicadores de amenazas a Azure Sentinel desde su TIP integrada o solución de inteligencia sobre amenazas personalizada:

1. Obtenga un id. de la aplicación y un secreto de cliente en Azure Active Directory

1. Escriba esta información en la solución TIP o en la aplicación personalizada

1. Habilite el conector de datos Plataformas de inteligencia sobre amenazas en Azure Sentinel

Aquí puede ver de forma detallada cada uno de estos pasos.

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Obtenga un id. de la aplicación y un secreto de cliente en Azure Active Directory

Tanto si trabaja con una TIP o con una solución personalizada, la API tiIndicators requiere cierta información básica para conectarse y enviar indicadores de amenazas. Los tres fragmentos de información que deberá obtener son:
- Id. de aplicación (cliente)
- Id. de directorio (inquilino)
- Secreto del cliente

Esta información siempre procede de su instancia de Azure Active Directory a través de un proceso llamado **Registro de aplicaciones** que incluye los tres pasos siguientes:
- Registrar una aplicación con Azure Active Directory
- Especifique los permisos que necesita la aplicación para conectarse a la API tiIndicators de Microsoft Graph y enviar indicadores de amenazas
- Obtenga el consentimiento de la organización para conceder estos permisos a esta aplicación.  

**Registro de una aplicación con Azure Active Directory**

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Active Directory**.

1. Seleccione **Registros de aplicaciones** en el menú y **Nuevo registro**.

1. Elija un nombre para el registro de aplicación, seleccione el botón de radio **Un solo inquilino** y **Registrar**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="Registro de una aplicación":::

1. En la pantalla resultante, copie los valores **Id. de la aplicación (cliente)** e **Id. de directorio (inquilino)** . Estos son los dos primeros fragmentos de información que necesitará posteriormente para configurar su TIP o solución personalizada para enviar indicadores de amenazas a Azure Sentinel.

**Especifique los permisos que requiere la aplicación**

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Active Directory**.

1. Seleccione **Registros de aplicaciones** en el menú y la aplicación recién registrada.

1. Seleccione **Permisos de API** en el menú y haga clic en el botón **Agregar un permiso**.

1. En la página **Seleccionar una API**, seleccione **Microsoft Graph** para elegir en una lista de permisos de Microsoft Graph.

1. Cuando se le pregunte **¿Qué tipo de permiso necesita la aplicación?** , seleccione **Permisos de aplicación**. Este es el tipo de permisos usados por aplicaciones que se autentican con el id. de la aplicación y secretos de aplicación (claves de API).

1. Seleccione **ThreatIndicators.ReadWrite.OwnedBy** y **Agregar permisos** para agregar este permiso a la lista de permisos de la aplicación.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="Especifique los permisos":::

**Obtenga el consentimiento de la organización para conceder estos permisos**

1. Para dar su consentimiento, necesita un administrador global de Azure Active Directory para seleccionar el botón **Conceder consentimiento de administrador para el inquilino** en la página de permisos de API de la aplicación. Si no tiene el rol Administrador global en su cuenta, este botón no estará disponible y tendrá que pedir a un administrador global de la organización que realice este paso.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="Otorgar consentimiento":::

1. Una vez que se haya dado el consentimiento a la aplicación, debería ver una marca de verificación verde en **Estado**.
 
Ahora que se ha registrado la aplicación y que se han concedido permisos, puede obtener lo último de la lista: un secreto de cliente para la aplicación.

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Active Directory**.

1. Seleccione **Registros de aplicaciones** en el menú y la aplicación recién registrada.

1. Seleccione **Certificados y secretos** en el menú y haga clic en el botón **Nuevo secreto de cliente** para obtener un secreto (clave de API) para la aplicación.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="Obtenga el secreto de cliente":::

1. Haga clic en el botón **Agregar** y **asegúrese de copiar el secreto de cliente**, ya que no puede volver a recuperar este secreto si sale de esta página. Necesitará este valor al configurar su TIP o solución personalizada.

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>Escriba esta información en la solución TIP o en la aplicación personalizada

Ahora tiene los tres fragmentos de información que necesita para configurar su TIP o solución personalizada para enviar indicadores de amenazas a Azure Sentinel. 
- Id. de aplicación (cliente)
- Id. de directorio (inquilino)
- Secreto del cliente

Escriba estos valores en la configuración de su TIP integrada o solución personalizada cuando sea necesario y los indicadores de amenazas se enviarán a través de la API tiIndicators de Microsoft Graph dirigida a Azure Sentinel.

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>Habilite el conector de datos Plataformas de inteligencia sobre amenazas en Azure Sentinel

El último paso del proceso de integración es habilitar el conector **Plataformas de inteligencia sobre amenazas** en Azure Sentinel. Este es el paso que importa los indicadores de amenazas enviados desde su TIP o solución personalizada a través de la API tiIndicators de Microsoft Graph en Azure Sentinel. Estos indicadores estarán disponibles para todas las áreas de trabajo de Azure Sentinel de la organización. Siga estos pasos para habilitar el conector de datos Plataformas de inteligencia sobre amenazas para cada área de trabajo:

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Sentinel**.

1. Elija el **área de trabajo** al que desea importar los indicadores de amenazas enviados desde su TIP o solución personalizada.

1. Seleccione **Conectores de datos** en el menú, seleccione **Plataformas de inteligencia sobre amenazas** en la galería de conectores y haga clic en el botón **Open connector page** (Abrir página del conector).

1. Como ya ha completado el registro de aplicaciones y configurado su TIP o solución personalizada para enviar indicadores de amenazas, el único paso que queda es hacer clic en el botón **Conectar**.

En cuestión de minutos, los indicadores de amenazas deberían empezar a fluir en esta área de trabajo de Azure Sentinel.

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>Incorporación de indicadores de amenazas a Azure Sentinel con el conector de datos Inteligencia sobre amenazas: TAXII

El estándar del sector más ampliamente adoptado para la transmisión de la inteligencia sobre amenazas es una [combinación del formato de datos STIX y el protocolo TAXII](https://oasis-open.github.io/cti-documentation/). Si la organización obtiene indicadores de amenazas de soluciones que admiten la versión de STIX/TAXII actual (2.0 o 2.1), puede usar el conector de datos **Inteligencia sobre amenazas: TAXII** para traer los indicadores de amenazas a Azure Sentinel. El conector de datos Inteligencia sobre amenazas: TAXII habilita un cliente de TAXII integrado en Azure Sentinel para importar la inteligencia sobre amenazas desde servidores TAXII 2.x.

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="Ruta de acceso de importación de TAXII":::
 
Siga estos pasos para importar indicadores de amenazas con formato STIX a Azure Sentinel desde un servidor TAXII:

1. Obtenga el identificador de colección y raíz de API del servidor TAXII

1. Habilite el conector de datos Inteligencia sobre amenazas: TAXII en Azure Sentinel

Examinemos ahora de forma detallada cada uno de estos pasos.

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>Obtenga el identificador de colección y raíz de API del servidor TAXII

Los servidores TAXII 2.x anuncian raíces de API, que son direcciones URL que hospedan colecciones de inteligencia sobre amenazas. La mayoría de las veces, la raíz de API se puede obtener a través de la página de documentación del proveedor de inteligencia sobre amenazas que hospeda el servidor TAXII. Sin embargo, en ocasiones, la única información anunciada es una dirección URL anunciada como punto de conexión de detección. Si es el caso, es fácil buscar la raíz de API mediante el punto de conexión de detección. Si ya conoce los identificadores de colección y raíz de API del servidor TAXII con los que desea trabajar, no dude en pasar a la sección siguiente, **Habilite el conector de datos Inteligencia sobre amenazas: TAXII en Azure Sentinel**.

Echemos un vistazo a un ejemplo actual de cómo usar una utilidad de línea de comandos llamada [cURL](https://en.wikipedia.org/wiki/CURL), que se proporciona en Windows y la mayoría de las distribuciones de Linux, para detectar la raíz de API y examinar las colecciones de un servidor TAXII cuyo inicio se produce solo desde el punto de conexión de detección. En este ejemplo, usaremos el punto de conexión de detección del servidor [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0.

1.  En un explorador, vaya al [punto de conexión de detección del servidor ThreatStream TAXII 2.0](https://limo.anomali.com/taxii) para recuperar la raíz de API. Autentíquese con el usuario y la contraseña `guest`.

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  Use la utilidad cURL y la raíz de API (https://limo.anomali.com/api/v1/taxii2/feeds/) del paso anterior para examinar la lista de identificadores de colección hospedados en la raíz de API

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

Ahora tiene toda la información que necesita para conectar Azure Sentinel a una o varias colecciones del servidor TAXII proporcionadas por Anomali Limo.

| **Raíz de API** (https://limo.anomali.com/api/v1/taxii2/feeds/) | Id. de colección |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch Ransomware IPs**                                  | 135           |
| **Abuse.ch Ransomware Domains**                              | 136           |
| **DShield Scanning IPs**                                     | 150           |
| **Malware Domain List - Hotlist**                            | 200           |
| **Blutmagie TOR Nodes**                                      | 209           |
| **Emerging Threats C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Emerging Threats - Compromised**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Habilite el conector de datos Inteligencia sobre amenazas: TAXII en Azure Sentinel

Para importar indicadores de amenazas en Azure Sentinel desde un servidor TAXII, siga estos pasos:

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Sentinel**.

1. Elija el **área de trabajo** al que desea importar indicadores de amenazas desde el servidor TAXII.

1. Seleccione **Conectores de datos** en el menú, seleccione **Inteligencia sobre amenazas: TAXII** en la galería de conectores y haga clic en el botón **Open connector page** (Abrir página del conector).

1. Escriba un **nombre** para esta colección de servidores TAXII, una **dirección URL raíz de la API**, un **identificador de colección**, un **nombre de usuario** (si es necesario) y una **contraseña** (si es necesario), y haga clic en el botón **Agregar**.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="Configuración de servidores TAXII":::
 
Debe recibir una confirmación de que se ha establecido correctamente una conexión al servidor TAXII y puede repetir el paso (4) anterior tantas veces como desee para conectarse a varias colecciones desde el mismo servidor TAXII o desde diferentes servidores.

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>Consulta de los indicadores de amenazas en Azure Sentinel

Ahora que ha importado correctamente indicadores de amenazas en Azure Sentinel mediante los conectores de datos **Plataformas de inteligencia sobre amenazas** o **Inteligencia sobre amenazas: TAXII**, puede verlos en la tabla **ThreatIntelligenceIndicator** de **Registros**, que es donde se almacenan todos los datos de eventos de Azure Sentinel. Esta tabla es la base de las consultas realizadas por otras características de Azure Sentinel como Análisis y Libros. A continuación, se muestra cómo buscar y ver los indicadores de amenazas en la tabla **ThreatIntelligenceIndicator**.

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Sentinel**.

1. Elija el **área de trabajo** al que ha importado indicadores de amenazas mediante el conector de datos de inteligencia sobre amenazas.

1. Seleccione **Registros** en la sección **General** del menú de Azure Sentinel.

1. La tabla **ThreatIntelligenceIndicator** se encuentra en el grupo **Azure Sentinel**.

1. Seleccione el icono **Vista previa de los datos** (el ojo) junto al nombre de la tabla y seleccione el botón **Ver en el editor de consultas** para ejecutar una consulta que mostrará los registros de esta tabla.

Los resultados deben tener un aspecto similar al indicador de amenazas de ejemplo que se muestra a continuación:

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="Datos de consulta de ejemplo":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>Administración de los indicadores de amenazas en la nueva área Inteligencia sobre amenazas de Azure Sentinel

Con la nueva área **Inteligencia sobre amenazas**, accesible desde el menú de Azure Sentinel, también puede ver, ordenar, filtrar y buscar los indicadores de amenazas importados sin ni siquiera escribir una consulta de **Registros**. Esta nueva área también permite crear indicadores de amenazas directamente dentro de la interfaz de Azure Sentinel, así como realizar tareas administrativas de inteligencia sobre amenazas comunes como el etiquetado de indicadores y la creación de nuevos indicadores relacionados con investigaciones de seguridad.
Echemos un vistazo a dos de las tareas más comunes, la creación de nuevos indicadores de amenazas y el etiquetado de indicadores para una fácil agrupación y referencia.

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Sentinel**.

1. Elija el **área de trabajo** al que ha importado indicadores de amenazas mediante el conector de datos de inteligencia sobre amenazas.

1. Seleccione **Inteligencia sobre amenazas** en la sección Administración de amenazas del menú de Azure Sentinel.

1. Seleccione el botón **Agregar nuevo** en el menú superior de la página.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="Incorporación de un nuevo indicador de amenazas" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. Elija el tipo de indicador y, a continuación, complete los campos obligatorios marcados con un asterisco rojo (*) en el panel **Nuevo indicador**.

1. Seleccione **Aplicar**. El indicador se agrega a la cuadrícula Indicadores y se envía también a la tabla ThreatIntelligenceIndicator en **Registros**.

El etiquetado de indicadores de amenazas es una forma sencilla de agruparlos para que sean más fáciles de encontrar. Normalmente, podría aplicar una etiqueta a los indicadores relacionados con un incidente concreto, o bien a aquellos que representan amenazas de un actor conocido determinado o una campaña de ataques conocida. Puede etiquetar indicadores de amenazas de forma individual o realizar una selección múltiple de los indicadores y etiquetarlos todos a la vez. A continuación se muestra un ejemplo de etiquetado de varios indicadores con un identificador de incidente. Dado que el etiquetado es de forma libre, una práctica recomendada es crear convenciones de nomenclatura estándar para las etiquetas del indicador de amenazas. Puede aplicar varias etiquetas a cada indicador.

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="Aplicación de etiquetas a indicadores de amenazas" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>Análisis pone en marcha los indicadores de amenazas detectando posibles amenazas

Sus indicadores de amenazas se incluyen en Azure Sentinel y ha visto cómo verlos y administrarlos. Ahora vea lo que pueden hacer por usted. El caso de uso más importante para los indicadores de amenazas en soluciones SIEM como Azure Sentinel es la activación de reglas de análisis.  Estas reglas basadas en indicadores comparan eventos sin formato de los orígenes de datos con sus indicadores de amenazas para detectar amenazas de seguridad en la organización. En **Análisis** de Azure Sentinel, crea reglas de análisis que se ejecutan de forma programada y genera alertas de seguridad. Las reglas están controladas mediante consultas, junto con configuraciones que determinan con qué frecuencia debe ejecutarse la regla, qué clase de resultados de consulta deben generar alertas de seguridad y cualquier respuesta automatizada que se desencadene al generarse alertas.

Aunque siempre puede crear nuevas reglas de análisis desde cero, Azure Sentinel proporciona un conjunto de plantillas de reglas integradas, creadas por ingenieros de seguridad de Microsoft, que puede usar tal cual o modificar para satisfacer sus necesidades. Puede identificar fácilmente las plantillas de reglas que usan indicadores de amenazas, ya que todas tienen un título que empieza por "**TI map**…". Todas estas plantillas de reglas funcionan de forma similar, siendo la única diferencia el tipo de indicadores de amenazas que se usan (dominio, correo electrónico, hash de archivo, dirección IP o dirección URL) y el tipo de evento que se va a comparar. En cada plantilla se muestran los orígenes de datos requeridos necesarios para el funcionamiento de la regla, de modo que puede ver a simple vista si tiene ya importados los eventos necesarios en Azure Sentinel.

Echemos un vistazo a una de estas plantillas de reglas y veamos cómo habilitar y configurar la regla para generar alertas de seguridad mediante los indicadores de amenazas que ha importado en Azure Sentinel. En este ejemplo, usaremos la plantilla de regla llamada **TI map IP entity to AzureActivity**. Esta regla coincidirá con cualquier indicador de amenazas de tipo de dirección IP con todos sus eventos de actividad de Azure. Cuando se encuentre una coincidencia, se generará una **alerta**, así como un **incidente** correspondiente para que lo investigue el equipo de operaciones de seguridad. Esta regla de análisis funcionará correctamente solo si ha habilitado uno de los conectores de datos de **inteligencia sobre amenazas** o ambos (para importar indicadores de amenazas), y el conector de datos **Actividad de Azure** (para importar los eventos de nivel de suscripción de Azure).

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Sentinel**.

1. Elija el **área de trabajo** al que importó indicadores de amenazas mediante los conectores de datos de **inteligencia sobre amenazas** y los datos de actividad de Azure mediante el conector de datos **Actividad de Azure**.

1. Seleccione **Análisis** en la sección **Configuración** del menú de Azure Sentinel.

1. Seleccione la pestaña **Plantillas de reglas** para ver la lista de plantillas de reglas de análisis disponibles.

1. Vaya a la regla titulada **TI map IP entity to AzureActivity** y asegúrese de que ha conectado todos los orígenes de datos necesarios como se muestra a continuación.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="Orígenes de datos necesarios":::

1. Seleccione esta regla y el botón **Crear regla**. Se abrirá un asistente para configurar la regla. Complete la configuración aquí y seleccione el botón **Siguiente: Establecer la lógica de la regla >** .

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="Creación de una regla de análisis":::

1. La parte lógica de la regla del asistente contiene:
    - La consulta que se usará en la regla.

    - Asignaciones de entidades, que indican a Azure Sentinel cómo reconocer entidades como Cuentas, Direcciones IP y Direcciones URL, de modo que tanto **incidentes** como **investigaciones** entiendan cómo trabajar con los datos en cualquier alerta de seguridad generada por esta regla.

    - La programación para ejecutar esta regla.

    - El número de resultados de la consulta necesarios antes de que se genere una alerta de seguridad.

    La configuración predeterminada de la plantilla es:
    - Ejecute una vez por hora.

    - Haga que coincida cualquier indicador de amenazas de direcciones IP de la tabla **ThreatIntelligenceIndicator** con cualquier dirección IP encontrada en la última hora de eventos de la tabla **AzureActivity**.

    - Genere una alerta de seguridad si los resultados de la consulta son mayores que cero, es decir, si se encuentra alguna coincidencia.

Puede dejar la configuración predeterminada o cambiar cualquiera de estas configuraciones para satisfacer sus necesidades. Cuando haya terminado, seleccione el botón **Siguiente: Respuesta automatizada >**

1. Este paso del asistente permite configurar cualquier automatización que desee desencadenar al generarse una alerta de seguridad a partir de esta regla de análisis. La automatización en Azure Sentinel se realiza mediante **Cuadernos de estrategias**, con la tecnología de Azure Logic Apps. Para más información, consulte este [Tutorial: Configuración de respuestas automatizadas frente a amenazas en Azure Sentinel](./tutorial-respond-threats-playbook.md). En este ejemplo, simplemente seleccionamos el botón **Siguiente: Revisar >** para continuar.

1. Este último paso valida la configuración de la regla. Cuando esté listo para habilitar la regla, seleccione el botón **Crear** y habrá terminado.

Ahora que ha habilitado la regla de análisis, encontrará la regla habilitada en la pestaña **Reglas activas** de la sección **Análisis** de Azure Sentinel. Puede editar, habilitar, deshabilitar, duplicar o eliminar la regla activa desde allí. La nueva regla se ejecuta inmediatamente tras la activación y, desde ese momento, se ejecutará en su programación definida.

Según la configuración predeterminada, cada vez que se ejecute la regla en su programación, cualquier resultado encontrado generará una alerta de seguridad. Las alertas de seguridad de Azure Sentinel se pueden ver en la sección **Registros** de Azure Sentinel, en la tabla **SecurityAlert** del grupo **Azure Sentinel**.

En Azure Sentinel, las alertas generadas a partir de las reglas de análisis también generan incidentes de seguridad que se encuentran en **Incidentes** en **Administración de amenazas** en el menú de Azure Sentinel. Los incidentes son aquello que los equipos de operaciones de seguridad investigarán y cuyas prioridades evaluarán para determinar las acciones de respuesta adecuadas. Encontrará información detallada en este [Tutorial: Investigación de incidentes con Azure Sentinel](./tutorial-investigate-cases.md).

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>Los libros proporcionan información sobre la inteligencia sobre amenazas

Por último, puede usar un libro de Azure Sentinel para visualizar información clave sobre la inteligencia sobre amenazas en Azure Sentinel y puede personalizar fácilmente los libros según sus necesidades empresariales.
Veamos cómo buscar el libro de inteligencia sobre amenazas proporcionado en Azure Sentinel y también mostraremos cómo realizar ediciones en el libro para personalizarlo.

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al servicio **Azure Sentinel**.

1. Elija el **área de trabajo** al que ha importado indicadores de amenazas mediante el conector de datos de inteligencia sobre amenazas.

1. Seleccione **Workbooks** en la sección **Administración de amenazas** del menú de Azure Sentinel.

1. Vaya al libro titulado **Inteligencia sobre amenazas** y compruebe que tiene datos en la tabla **ThreatIntelligenceIndicator** como se muestra a continuación.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="Comprobación de datos":::
 
1. Seleccione el botón **Guardar** y elija una ubicación de Azure para almacenar el libro. Este paso es necesario si va a modificar el libro de cualquier manera y guardar los cambios.

1. Ahora, seleccione el botón **Ver libro guardado** para abrir el libro a fin de verlo y editarlo.

1. Ahora debería ver los gráficos predeterminados proporcionados por la plantilla. Ahora realicemos algunos cambios en uno de los gráficos. Seleccione el botón **Editar** en la parte superior de la página para especificar el modo de edición del libro.

1. Agreguemos un nuevo gráfico de indicadores de amenazas por tipo de amenaza. Desplácese a la parte inferior de la página y seleccione Agregar consulta.

1. Agregue el siguiente texto al cuadro de texto **Consulta de registro del área de trabajo de Log Analytics**:
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. En la lista desplegable **Visualización**, seleccione **Gráfico de barras**.

1. Seleccione el botón **Edición finalizada**. Ha creado un nuevo gráfico para el libro.

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="Gráfico de barras":::

Los libros proporcionan paneles interactivos eficaces que proporcionan información sobre todos los aspectos de Azure Sentinel. Hay muchas cosas que puede hacer con los libros y, aunque las plantillas proporcionadas son un buen punto de partida, es probable que quiera profundizar en estas plantillas y personalizarlas, o bien crear nuevos paneles que combinen muchos orígenes de datos diferentes para que pueda visualizar los datos de maneras únicas. Dado que los libros de Azure Sentinel se basan en los libros de Azure Monitor, ya hay una amplia documentación disponible y muchas más plantillas. Un buen punto de partida es este artículo sobre cómo [crear informes interactivos con los libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). 

También hay una completa comunidad de [libros de Azure Monitor en GitHub](https://github.com/microsoft/Application-Insights-Workbooks) donde puede descargar plantillas adicionales y aportar sus propias plantillas.

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido acerca de las funcionalidades de inteligencia sobre amenazas de Azure Sentinel y la nueva hoja Inteligencia sobre amenazas. Para obtener instrucciones prácticas sobre el uso de las funcionalidades de inteligencia sobre amenazas de Azure Sentinel, consulte los siguientes artículos:

- [Conexión de datos de inteligencia sobre amenazas](./connect-threat-intelligence.md) con Azure Sentinel.
- [Integración de plataformas de TIP, fuentes de TAXII y enriquecimientos](threat-intelligence-integration.md) con Azure Sentinel.
- Creación de alertas [integradas](./tutorial-detect-threats-built-in.md) o [personalizadas](./tutorial-detect-threats-custom.md) e [investigación](./tutorial-investigate-cases.md) de incidentes, en Azure Sentinel.