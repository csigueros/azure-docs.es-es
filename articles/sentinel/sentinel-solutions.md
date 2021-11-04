---
title: Acerca de las soluciones y el contenido de Azure Sentinel | Microsoft Docs
description: En este artículo se describen el contenido y las soluciones de Azure Sentinel, que los clientes pueden usar para encontrar herramientas de análisis de datos empaquetadas junto con conectores de datos.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38508b591d79e0dee910468f595fa144353574c9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064076"
---
# <a name="about-azure-sentinel-content-and-solutions"></a>Acerca de las soluciones y el contenido de Azure Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> El **centro de contenido** y las soluciones de Azure Sentinel se encuentran actualmente en **VERSIÓN PRELIMINAR**, igual que todos los paquetes de soluciones individuales. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

El *contenido* de Azure Sentinel es contenido de tipo Administración de eventos e información de seguridad (SIEM), ya que permite a los clientes ingerir datos, supervisar, enviar alertas, buscar, investigar, responder y conectarse con diferentes productos, plataformas y servicios en Azure Sentinel.

El contenido Azure Sentinel incluye cualquiera de los siguientes tipos:

- Los **[Conectores de datos](connect-data-sources.md)** proporcionan la ingesta de registros de diferentes orígenes en Azure Sentinel.
- Los **[Analizadores](normalization-about-parsers.md)** proporcionan el formato o la transformación de registros en formato [ASIM](normalization.md), lo que permite el uso en varios tipos de contenido y escenarios de Azure Sentinel.
- Los **[Libros](get-visibility.md)** proporcionan supervisión, visualización e interactividad con los datos en Azure Sentinel, destacando información valiosa para los usuarios.
- Las **[Reglas analíticas](detect-threats-built-in.md)** proporcionan alertas que apuntan a acciones de SOC pertinentes a través de incidentes.
- Los equipos de SOC utilizan **[consultas de búsqueda](hunting.md)** para buscar amenazas de manera proactiva en Azure Sentinel.
- Los **[Cuadernos](notebooks.md)** ayudan a los equipos de SOC a utilizar características de búsqueda avanzadas en Jupyter y Azure Notebooks.
- Las **[listas de seguimiento](watchlists.md)** admiten la ingesta de datos *específicos* para mejorar la detección de amenazas y reducir la fatiga de las alertas.
- Los **[Conectores personalizados de cuadernos de estrategias y de Azure Logic Apps](automate-responses-with-playbooks.md)** proporcionan características para realizar investigaciones automatizadas, correcciones y escenarios de respuesta en Azure Sentinel.

Las *soluciones* de Azure Sentinel son paquetes de contenido de Azure Sentinel o integraciones de API de Azure Sentinel, que cumplen con un producto de un extremo a otro, un dominio o un escenario vertical de la industria en Azure Sentinel.

> [!TIP]
> Puede personalizar el contenido integrado para sus propias necesidades o puede crear su propia solución con contenido que pueda compartir con otros miembros de la comunidad. Para obtener más información, consulte la [Guía de compilación Azure Sentinel](https://aka.ms/sentinelsolutionsbuildguide) para la creación y publicación de soluciones.
>
## <a name="discover-and-manage-azure-sentinel-content"></a>Detectar y administrar el contenido de Azure Sentinel

Utilice el **centro de contenido** de Azure Sentinel para descubrir e instalar contenido listo para usar (integrado) de forma centralizada.

El centro de contenido de Azure Sentinel proporciona la capacidad de detección en el producto, la implementación en un solo paso y la opción de habilitar productos, dominios y soluciones verticales integrados y contenido en Azure Sentinel.

- En el **Centro de contenido**, filtre por [categorías](#azure-sentinel-out-of-the-box-content-and-solution-categories) y otros parámetros, o use la potente búsqueda de texto para buscar el contenido que mejor se adapte a las necesidades de su organización. El **Centro de contenido** también indica el [modelo de soporte técnico](#azure-sentinel-out-of-the-box-content-and-solution-support-models) que se aplica a cada parte del contenido, ya que Microsoft mantiene algunos contenidos y otros los mantienen los asociados o la comunidad.

    Administre las [actualizaciones del contenido integrado](sentinel-solutions-deploy.md#install-or-update-a-solution) a través del **Centro de contenido** de Azure Sentinel y para contenido personalizado a través de la página **Repositorios**.

- Personalice el contenido integrado para sus propias necesidades o cree contenido personalizado, incluidas reglas de análisis, consultas de búsqueda, cuadernos, libros de trabajo y más. Administre su contenido personalizado directamente en el área de trabajo de Azure Sentinel, a través de la [API de Azure Sentinel](/rest/api/securityinsights/), o en su propio repositorio de control de código fuente, a través de la página [Repositorios](ci-cd.md) de Azure Sentinel.

### <a name="why-content-hub-and-solutions"></a>¿Por qué el centro de contenido y las soluciones?

Las *soluciones* de Azure Sentinel son integraciones o contenido empaquetado que proporcionan un valor de producto de un extremo a otro para uno o más escenarios de dominio o verticales.

La experiencia de las soluciones cuenta con la tecnología de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) para la detección e implementación de las soluciones. Para obtener más información, consulte la [Guía de compilación de soluciones de Azure Sentinel](https://aka.ms/sentinelsolutionsbuildguide) para la creación y publicación de soluciones.

Las soluciones de Azure Sentinel proporcionan detectabilidad en el producto, implementación en un solo paso y habilitación de escenarios de productos, dominios o verticales de un extremo a otro en Azure Sentinel. Esta experiencia cuenta con la tecnología de las soluciones para la detectabilidad, implementación y habilitación de soluciones, y para la creación y publicación de soluciones.

- El **contenido empaquetado** son colecciones de uno o varios fragmentos de contenido de Azure Sentinel, como conectores de datos, libros, reglas de análisis, cuadernos de estrategias, consultas de búsqueda, listas de seguimiento, analizadores y otros.

- Las **integraciones** incluyen servicios o herramientas creadas con las API de Azure Sentinel o Azure Log Analytics que admiten integraciones entre Azure y las aplicaciones de clientes existentes, o migran datos y realizan consultas, etc., desde esas aplicaciones a Azure Sentinel.

También puede usar soluciones para instalar paquetes de contenido integrado en un solo paso, donde el contenido a menudo está integrado de inmediato. Los proveedores y asociados pueden usar las soluciones para sacar beneficio de sus inversiones mediante la entrega de un producto, un dominio o un valor vertical combinado.

Use el centro de contenido para descubrir e implementar de manera centralizada soluciones y contenido integrado de una manera basada en escenarios.

Para obtener más información, consulte:

- [Detección e implementación de la soluciones y el contenido integrado de Azure Sentinel](sentinel-solutions-deploy.md)
- [Catálogo del centro de contenido de Azure Sentinel](sentinel-solutions-catalog.md)

## <a name="azure-sentinel-out-of-the-box-content-and-solution-categories"></a>Categorías del contenido y las soluciones integradas de Azure Sentinel

El contenido integrado de Azure Sentinel se puede aplicar con una o más de las siguientes categorías. En el **Centro de contenido**, seleccione las categorías que quiera ver para cambiar el contenido mostrado.

### <a name="domain-categories"></a>Categorías de dominio

| Nombre de la categoría  | Descripción |
| ---------- | ----------------------- |
| **Aplicación**  | Carga de trabajo web, basada en servidor, SaaS, base de datos, comunicaciones o productividad.          |
| **Proveedor de servicios en la nube**  | servicio en la nube|
| **Cumplimiento normativo**   | Productos, servicios y protocolos de cumplimiento.  |
| **DevOps**       | Servicios y herramientas de operaciones de desarrollo.    |
| **Identidad**     | Integraciones y proveedores de servicios de identidad.     |
| **Internet de las cosas (IoT)**    | IoT, dispositivos e infraestructura de OT, servicios de control industrial.                   |
| **Operaciones de TI**| Productos y servicios de administración de TI.   |
| **Migración**    | Productos, servicios y habilitación de la migración.               |
| **Redes**   | Productos, servicios y herramientas de red.    |
| **Plataforma**     | Componentes genéricos o de marco, infraestructura en la nube y plataforma de Azure Sentinel.|
| **Seguridad/Otros**   | Otros productos y servicios de seguridad sin otra categoría clara.           |
| **Seguridad: inteligencia sobre amenazas**  | Plataformas, fuentes, productos y servicios de inteligencia sobre amenazas.        |
| **Seguridad: protección contra amenazas**   | Protección contra amenazas, protección del correo electrónico, productos y servicios de XDR y protección del punto de conexión.     |
| **Seguridad: vulnerabilidad de día 0**   | Soluciones especializadas para ataques de vulnerabilidad de día cero como [Nobelium](/azure/security/fundamentals/recover-from-identity-compromise). |
| **Seguridad: Automatización (SOAR)**   | Automatizaciones de seguridad, SOAR (operaciones de seguridad y respuestas automatizadas), operaciones de seguridad y productos y servicios de respuesta a incidentes.   |
| **Seguridad: seguridad en la nube** | CASB (Agente de seguridad de acceso a la nube), CWPP (Plataformas de protección de cargas de trabajo en la nube), CSPM (Administración de la posición de seguridad en la nube y otros productos y servicios de Cloud Security). |
| **Seguridad: Azure Information Protection**   | Productos y servicios de protección de la información y protección de documentos.|
| **Seguridad: amenaza de Insider**  | Amenazas de Insider y análisis del comportamiento de usuarios y entidades (UEBA) para productos y servicios de seguridad.                |
| **Seguridad: red**    | Dispositivos de red de seguridad, firewall, BAND (detección y respuesta de red), NIDP (prevención de intrusiones y detección de redes) y captura de paquetes de red.   |
| **Seguridad: administración de vulnerabilidades** | Productos y servicios de administración de vulnerabilidades.                    |
| **Storage**      | Almacenes de archivos y servicios y productos de uso compartido de archivos.                 |
| **Entrenamiento y tutoriales**  | Entrenamiento, tutoriales y recursos de incorporación. |
| **Comportamiento del usuario (UEBA)**    | Otros productos y servicios de análisis de comportamiento del usuario.|
| | |

### <a name="industry-vertical-categories"></a>Categorías verticales del sector

| Nombre de la categoría  | Descripción |
| ---------- | ----------------------- |
| **Aeronáutica**  | Productos, servicios y contenido específicos para el sector aeronáutico. |
| **Education**    | Productos, servicios y contenido específicos para el sector educativo.   |
| **Sector financiero**      | Productos, servicios y contenido específicos para el sector financiero.     |
| **Atención sanitaria**   | Productos, servicios y contenido específicos para el sector sanitario.  |
| **Manufacturing** | Productos, servicios y contenido específicos para el sector de manufactura. |
| **Minoristas**       | Productos, servicios y contenido específicos para el sector comercial.       |
| | |

## <a name="azure-sentinel-out-of-the-box-content-and-solution-support-models"></a>Modelos de soporte técnico de soluciones y contenido integrados de Azure Sentinel

Tanto Microsoft como otras organizaciones crean contenido y soluciones integrados en Azure Sentinel. Cada parte de contenido o solución integrado tiene uno de los siguientes tipos de soporte técnico:

| Modelo de soporte técnico  | Descripción |
| ---------- | ----------------------- |
| **Soporte técnico de Microsoft**| Se aplica a: <br>- Contenido o soluciones en las que Microsoft es el proveedor de datos, si procede, y el autor. <br> - Algunos contenidos o soluciones que crea Microsoft para orígenes de datos que no sean de Microsoft. <br><br>    Microsoft admite y mantiene el contenido o las soluciones en este modelo de soporte técnico de acuerdo con los [planes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/options/#overview). <br>Los asociados o el contenido o las soluciones de soporte técnico de la comunidad que haya creado cualquier entidad distinta de Microsoft.|
|**Soporte técnico de asociados** | Se aplica a contenido o soluciones que hayan creado otras partes que no sean de Microsoft.  <br><br>   La empresa asociada proporciona soporte técnico o mantenimiento para estos elementos de contenido o soluciones. La empresa asociada puede ser un fabricante de software independiente, un Proveedor de servicios administrados (MSP/MSSP), un integrador de sistemas (SI) o cualquier organización cuya información de contacto se proporcione en la página de Azure Sentinel para ese conector de datos.<br><br>    Para cualquier problema con una solución compatible con asociados, póngase en contacto con el contacto de soporte técnico especificado.|
|**Soporte técnico de la comunidad** |Se aplica a contenido o soluciones que haya creado Microsoft o los desarrolladores de asociados que no tengan contactos enumerados para soporte técnico y mantenimiento en Azure Sentinel.<br><br>    Si tiene preguntas o problemas relacionados con estos conectores de datos, puede [registrar un problema](https://github.com/Azure/Azure-Sentinel/issues/new/choose) en la [comunidad de GitHub de Azure Sentinel](https://aka.ms/threathunters). |
| | |


## <a name="next-steps"></a>Pasos siguientes

Una vez que haya aprendido a administrar el contenido de Azure Sentinel, empiece a administrar ese contenido y las soluciones en el área de trabajo de Azure Sentinel.

Detecte e instale soluciones desde el **centro de contenido** de Azure Sentinel. Para obtener más información, consulte:

- [Detección e implementación de las soluciones y el contenido integrados de Azure Sentinel (versión preliminar pública)](sentinel-solutions-deploy.md)
- [Catálogo del centro de contenido de Azure Sentinel](sentinel-solutions-catalog.md)
- [Conectores de datos de Azure Sentinel](connect-data-sources.md)
- [Búsqueda del conector de datos de Azure Sentinel](data-connectors-reference.md)
