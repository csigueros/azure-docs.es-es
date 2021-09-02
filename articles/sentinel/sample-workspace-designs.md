---
title: Diseños de ejemplo de áreas de trabajo de Azure Sentinel | Microsoft Docs
description: Aprenda de los ejemplos de diseños de arquitecturas de Azure Sentinel con varios inquilinos, nubes o regiones.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/18/2021
ms.openlocfilehash: dca6beedfe8fa3d57674323490c2d79cf5aa048f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179354"
---
# <a name="azure-sentinel-sample-workspace-designs"></a>Diseños de ejemplo de áreas de trabajo de Azure Sentinel

En este artículo se describen los diseños de áreas de trabajo sugeridos para las organizaciones con los siguientes requisitos de ejemplo:

- Varios inquilinos y regiones, con requisitos de soberanía de los datos europea
- Inquilino único con varias nubes
- Varios inquilinos, con varias regiones y seguridad centralizada

En los ejemplos de este artículo se usa el [árbol de decisión sobre el diseño de áreas de trabajo de Azure Sentinel](design-your-workspace-architecture.md) para determinar el mejor diseño posible para cada organización. Para más información, consulte [Procedimientos recomendados de arquitectura de áreas de trabajo de Azure Sentinel](best-practices-workspace-architecture.md).

## <a name="sample-1-multiple-tenants-and-regions"></a>Ejemplo 1: Varios inquilinos y regiones

Contoso Corporation es una empresa multinacional con sede en Londres. Contoso tiene oficinas en todo el mundo, con centros importantes en la ciudad de Nueva York y Tokio. Recientemente, Contoso ha migrado su conjunto de productividad a Office 365, con muchas cargas de trabajo migradas a Azure.

### <a name="contoso-tenants"></a>Inquilinos de Contoso

Debido a una adquisición hace varios años, Contoso tiene dos inquilinos de Azure AD: `contoso.onmicrosoft.com` y `wingtip.onmicrosoft.com`. Cada inquilino tiene su propia instancia de Office 365 y varias suscripciones de Azure, como se muestra en la imagen siguiente:

:::image type="content" source="media/best-practices/contoso-tenants.png" alt-text="Diagrama de inquilinos de Contoso, cada uno con conjuntos de suscripciones independientes." border="false":::

### <a name="contoso-compliance-and-regional-deployment"></a>Cumplimiento de Contoso e implementación regional

Contoso tiene actualmente recursos de Azure hospedados en tres regiones diferentes: Este de EE. UU., Norte de Europa y Oeste de Japón, y un requisito estricto de mantener todos los datos generados en Europa dentro de las regiones de Europa.

Los dos inquilinos de Azure AD de Contoso tienen recursos en las tres regiones: Este de EE. UU., Norte de Europa y Oeste de Japón

### <a name="contoso-resource-types-and-collection-requirements"></a>Tipos de recursos y requisitos de recopilación de Contoso

Contoso tiene que recopilar eventos de los siguientes orígenes de datos:

-   Office 365
-   Registros de inicio de sesión y de auditoría de Azure AD
-   Actividad de Azure
-   Eventos de seguridad de Windows, de orígenes locales y de máquinas virtuales de Azure
-   Syslog, de orígenes locales y de máquinas virtuales de Azure
-   CEF, desde varios dispositivos de red locales, como Palo Alto, Cisco ASA y Cisco Meraki
-   Varios recursos de PaaS de Azure, como Azure Firewall, AKS, Key Vault, Azure Storage y Azure SQL
-   Cisco Umbrella

Las máquinas virtuales de Azure se encuentran principalmente en la región Norte de Europa, con solo algunas en las regiones Este de EE. UU. y Oeste de Japón. Contoso usa Azure Defender para servidores en todas sus máquinas virtuales de Azure.

Contoso espera ingerir aproximadamente 300 GB/día de todos sus orígenes de datos.

### <a name="contoso-access-requirements"></a>Requisitos de acceso de Contoso

El entorno de Azure de Contoso ya tiene un área de trabajo de Log Analytics existente que usa el equipo de operaciones para supervisar la infraestructura. Esta área de trabajo se encuentra en el inquilino de Contoso AAD, en la región Norte de Europa, y se usa para recopilar registros de máquinas virtuales de Azure de todas las regiones. Actualmente ingiere aproximadamente 50 GB/día.

El equipo de operaciones de Contoso debe tener acceso a todos los registros que tienen actualmente en el área de trabajo, entre los cuales se incluyen varios tipos de datos que no necesita el centro de operaciones de seguridad (SOC) como **Perf**, **InsightsMetrics**, **ContainerLog**, etc. El equipo de operaciones *no* debe tener acceso a los nuevos registros que se recopilarán en Azure Sentinel.

### <a name="contosos-solution"></a>Solución de Contoso

En los pasos siguientes se aplica el [árbol de decisión sobre el diseño de áreas de trabajo de Azure Sentinel](design-your-workspace-architecture.md) para determinar el mejor diseño posible para Contoso:

1. Contoso ya tiene un área de trabajo existente, por lo que podemos explorar la habilitación de Azure Sentinel en ella.

    La ingesta de datos que no son del SOC es inferior a 100 GB/día, por lo que podemos continuar con el [paso 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies) y asegurarnos de seleccionar la opción pertinente en el [paso 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data).

1.  Contoso tiene requisitos normativos, por lo que necesitaremos al menos un área de trabajo de Azure Sentinel en Europa.

1.  Contoso tiene dos inquilinos de Azure AD diferentes y recopila datos de orígenes de datos en el nivel de inquilino como, por ejemplo, registros de inicio de sesión y auditoría de Office 365 y Azure AD, por lo que se necesitan al menos un área de trabajo por inquilino.

1.  Contoso no necesita [contracargos](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back), por lo que podemos continuar en el [paso 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data).

1.  Contoso necesita recopilar datos que no son del SOC, aunque no hay ninguna superposición entre los datos de SOC y los que no lo son. Además, los datos de SOC suponen aproximadamente 250 GB/día, por lo que deben usar áreas de trabajo independientes para mejorar la rentabilidad.

1.  La mayoría de las máquinas virtuales de Contoso se encuentran en la región Norte de Europa, donde ya tienen un área de trabajo. Por lo tanto, en este caso, los costos de ancho de banda no son un problema.

1.  Contoso tiene un único equipo de SOC que va a usar Azure Sentinel, por lo que no es necesaria ninguna separación adicional.

1.  Todos los miembros del equipo de SOC de Contoso tendrán acceso a todos los datos, por lo que no se necesita ninguna separación adicional.

El diseño del área de trabajo de Azure Sentinel resultante para Contoso se muestra en la siguiente imagen:

:::image type="content" source="media/best-practices/contoso-solution.png" alt-text="Diagrama de la solución de Contoso, con un área de trabajo independiente para el equipo de operaciones." border="false":::

La solución sugerida incluye:

- Un área de trabajo de Log Analytics independiente para el equipo de operaciones de Contoso. Esta área de trabajo solo contendrá datos que no necesite el equipo de SOC de Contoso, como las tablas **Perf**, **InsightsMetrics** o **ContainerLog**.

- Dos áreas de trabajo de Azure Sentinel, una en cada inquilino de Azure AD, para ingerir datos de Office 365, actividades de Azure, Azure AD y todos los servicios PaaS de Azure.

- Todos los demás datos, procedentes de orígenes de datos locales, se pueden enrutar a una de las dos áreas de trabajo de Azure Sentinel.


## <a name="sample-2-single-tenant-with-multiple-clouds"></a>Ejemplo 2: Inquilino único con varias nubes

Fabrikam es una organización con sede principal en la ciudad de Nueva York y oficinas distribuidas por todo Estados Unidos. Fabrikam está al comienzo de su recorrido en la nube y todavía necesita implementar su primera zona de aterrizaje de Azure y migrar sus primeras cargas de trabajo. Fabrikam ya tiene algunas cargas de trabajo en AWS, las cuales pretenden supervisar mediante Azure Sentinel.

### <a name="fabrikam-tenancy-requirements"></a>Requisitos de inquilinos de Fabrikam

Fabrikam tiene un único inquilino de Azure AD.

### <a name="fabrikam-compliance-and-regional-deployment"></a>Cumplimiento de Fabrikam e implementación regional

Fabrikam no tiene requisitos de cumplimiento. Fabrikam tiene recursos en varias regiones de Azure ubicadas en Estados Unidos, pero los costos de ancho de banda entre regiones no son un problema importante.

### <a name="fabrikam-resource-types-and-collection-requirements"></a>Tipos de recursos y requisitos de recopilación de Fabrikam

Fabrikam tiene que recopilar eventos de los siguientes orígenes de datos:

-   Registros de inicio de sesión y de auditoría de Azure AD
-   Actividad de Azure
-   Eventos de seguridad, de orígenes locales y de máquinas virtuales de Azure
-   Eventos de Windows, de orígenes locales y de máquinas virtuales de Azure
-   Datos de rendimiento, de orígenes locales y de máquinas virtuales de Azure
-   AWS CloudTrail
-   Registros de rendimiento y auditoría de AKS

### <a name="fabrikam-access-requirements"></a>Requisitos de acceso de Fabrikam

El equipo de operaciones de Fabrikam debe tener acceso a:

-   Eventos de seguridad y eventos de Windows, de orígenes locales y de máquinas virtuales de Azure
-   Datos de rendimiento, de orígenes locales y de máquinas virtuales de Azure
-   Rendimiento de AKS (Container Insights) y registros de auditoría
-   Todos los datos de actividad de Azure

El equipo de SOC de Fabrikam debe tener acceso a:
-   Registros de inicio de sesión y de auditoría de Azure AD
-   Todos los datos de actividad de Azure
-   Eventos de seguridad, de orígenes locales y de máquinas virtuales de Azure
-   Registros de AWS CloudTrail
-   Registros de auditoría de AKS
-   El portal de Azure Sentinel completo

### <a name="fabrikams-solution"></a>Solución de Fabrikam

En los pasos siguientes se aplica el [árbol de decisión sobre el diseño de áreas de trabajo de Azure Sentinel](design-your-workspace-architecture.md) para determinar el mejor diseño posible para Fabrikam:

1.  Fabrikam no tiene ningún área de trabajo existente, por lo que puede continuar en el [paso 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies).

1.  Fabrikam no tiene requisitos normativos, por lo que puede continuar con el [paso 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants).

1.  Fabrikam tiene un entorno de un solo inquilino, por lo que puede continuar en el [paso 4](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back).

1.  Fabrikam no tiene necesidad de dividir los cargos, por lo que puede continuar en el [paso 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data).

1.  Fabrikam necesitará áreas de trabajo independientes para sus equipos de SOC y de operaciones:

    El equipo de operaciones de Fabrikam necesita recopilar datos de rendimiento, tanto de máquinas virtuales como de AKS. Dado que AKS se basa en la configuración de diagnóstico, se pueden seleccionar registros específicos para enviarlos a áreas de trabajo específicas. Fabrikam puede optar por enviar registros de auditoría de AKS al área de trabajo de Azure Sentinel y todos los registros de AKS a un área de trabajo independiente en la que Azure Sentinel está habilitado. En el área de trabajo en la que Azure Sentinel no está habilitado, Fabrikam habilitará la solución Container Insights.

    En el caso de las máquinas virtuales de Windows, Fabrikam puede usar el [agente de Azure Monitoring](connect-windows-security-events.md#connector-options) para dividir los registros, enviar eventos de seguridad al área de trabajo de Azure Sentinel y eventos de rendimiento y de Windows al área de trabajo sin Azure Sentinel.

    Fabrikam decide considerar sus datos superpuestos como, por ejemplo, eventos de seguridad y eventos de actividad de Azure, como datos de SOC únicamente, y envía estos datos al área de trabajo con Azure Sentinel.

1.  Los costos de ancho de banda no son un problema importante para Fabrikam, por lo que puede continuar en el [paso 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership).

1.  Fabrikam ya ha decidido usar áreas de trabajo independientes para los equipos de SOC y de operaciones. No es necesaria ninguna separación adicional.

1.  Fabrikam necesita controlar el acceso a los datos superpuestos, incluidos los eventos de seguridad y los eventos de actividad de Azure, pero no hay ningún requisito en el nivel de fila.

    Ni los eventos de seguridad ni los eventos de actividad de Azure son registros personalizados, por lo que Fabrikam puede usar RBAC en el nivel de tabla para conceder acceso a estas dos tablas al equipo de operaciones.

El diseño resultante del área de trabajo de Azure Sentinel para Fabrikam se muestra en la imagen siguiente, la cual incluye solo los principales orígenes de registros para simplificar el diseño:

:::image type="content" source="media/best-practices/fabrikam-solution.png" alt-text="Diagrama de la solución de Fabrikam, con un área de trabajo independiente para el equipo de operaciones." border="false" :::

La solución sugerida incluye:

- Dos áreas de trabajo independientes en la región de EE. UU.: una para el equipo de SOC con Azure Sentinel habilitado y otra para el equipo de operaciones, sin Azure Sentinel.

- El [agente de Azure Monitor](connect-windows-security-events.md#connector-options) que se usa para determinar qué registros se envían a cada área de trabajo desde Azure y las máquinas virtuales locales.

- Configuración de diagnóstico, que se usa para determinar qué registros se envían a cada área de trabajo desde recursos de Azure como AKS.

- Datos superpuestos que se envían al área de trabajo de Azure Sentinel, con RBAC en el nivel de tabla para conceder acceso al equipo de operaciones según sea necesario.

## <a name="sample-3-multiple-tenants-and-regions-and-centralized-security"></a>Ejemplo 3: varios inquilinos, con varias regiones y seguridad centralizada

Adventure Works es una empresa multinacional con sede en Tokio. Adventure Works tiene 10 subentidades diferentes, distribuidas por distintos países de todo el mundo.

Adventure Works es cliente de Microsoft 365 E5 y ya tiene cargas de trabajo en Azure.

### <a name="adventure-works-tenancy-requirements"></a>Requisitos de inquilinos de Adventure Works

Adventure Works tiene tres inquilinos de Azure AD diferentes, uno para cada uno de los continentes donde tienen subentidades: Asia, Europa y África. Los distintos países de las subentidades tienen sus identidades en el inquilino del continente al que pertenecen. Por ejemplo, los usuarios japoneses están en el inquilino de *Asia*, los alemanes en el de *Europa* y los egipcios en el de *África*.

### <a name="adventure-works-compliance-and-regional-requirements"></a>Cumplimiento y requisitos regionales de Adventure Works

Adventure Works usa actualmente tres regiones de Azure, cada una alineada con el continente en el que residen las subentidades. Adventure Works no tiene requisitos de cumplimiento estrictos.

### <a name="adventure-works-resource-types-and-collection-requirements"></a>Tipos de recursos y requisitos de recopilación de Adventure Works

Adventure Works debe recopilar los siguientes orígenes de datos para cada subentidad:

-   Registros de inicio de sesión y de auditoría de Azure AD
-   Registros de Office 365
-   Microsoft 365 Defender para registros sin procesar de puntos de conexión
-   Actividad de Azure
-   Azure Defender
-   Recursos de PaaS de Azure, como Azure Firewall, Azure Storage, Azure SQL y el firewall de aplicaciones web de Azure
-   Eventos de seguridad y Windows desde máquinas virtuales de Azure
-   Registros en CEF desde dispositivos de red locales

Las máquinas virtuales de Azure están dispersas por los tres continentes, pero los costos de ancho de banda no son un problema.

### <a name="adventure-works-access-requirements"></a>Requisitos de acceso de Adventure Works

Adventure Works tiene un único equipo de SOC centralizado que supervisa las operaciones de seguridad de todas las distintas subentidades.

Adventure Works también tiene tres equipos de SOC independientes, uno para cada uno de los continentes. El equipo de SOC de cada continente debe poder acceder solo a los datos que se generan en su región, sin ver datos de otros continentes. Por ejemplo, el equipo de SOC de Asia solo debe acceder a los datos de los recursos de Azure implementados en Asia, los inicios de sesión de AAD del inquilino de Asia y los registros de Defender para registros del punto de conexión del inquilino de Asia.

El equipo de SOC de cada continente tiene que acceder a toda la experiencia del portal de Azure Sentinel.

El equipo de operaciones de Adventure Works funciona de forma independiente y tiene sus propias áreas de trabajo sin Azure Sentinel.

### <a name="adventure-works-solution"></a>Solución de Adventure Works

En los pasos siguientes se aplica el [árbol de decisión sobre el diseño de áreas de trabajo de Azure Sentinel](design-your-workspace-architecture.md) para determinar el mejor diseño posible para Adventure Works:

1.  El equipo de operaciones de Adventure Works tiene sus propias áreas de trabajo, por lo que puede continuar en el [paso 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies).

1.  Adventure Works no tiene requisitos normativos, por lo que puede continuar con el [paso 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants).

1.  Adventure Works tiene tres inquilinos de Azure AD y necesita recopilar orígenes de datos en el nivel de inquilino como, por ejemplo, registros de Office 365. Por lo tanto, Adventure Works debe crear varias áreas de trabajo de Azure Sentinel, al menos una para cada inquilino.

1.  Adventure Works no tiene necesidad de dividir los cargos, por lo que puede continuar en el [paso 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data).

1.  Dado que el equipo de operaciones de Adventure Works tiene sus propias áreas de trabajo, el equipo de SOC de Adventure Works usará todos los datos que se han tenido en cuenta en esta decisión.

1.  Los costos de ancho de banda no son un problema importante para Adventure Works, por lo que puede continuar en el [paso 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership).

1.  Adventure Works necesita separar los datos por propiedad, ya que el equipo de SOC de cada contenido solo necesita acceder a los datos pertinentes para ese contenido. No obstante, el equipo de SOC de cada continente también necesita acceso a todo el portal de Azure Sentinel.

1.  Adventure Works no necesita controlar el acceso a los datos por tabla.

El diseño resultante del área de trabajo de Azure Sentinel para Adventure Works se muestra en la imagen siguiente, la cual incluye solo los principales orígenes de registros para simplificar el diseño:

:::image type="content" source="media/best-practices/adventure-works-solution.png" alt-text="Diagrama de la solución de Adventure Works, con áreas de trabajo independientes para cada inquilino de Azure AD." border="false":::

La solución sugerida incluye:

- Un área de trabajo de Azure Sentinel independiente para cada inquilino de Azure AD. Cada área de trabajo recopila datos relacionados con su inquilino para todos los orígenes de datos.

- El equipo de SOC de cada continente solo tiene acceso al área de trabajo de su propio inquilino, lo que garantiza que cada equipo de SOC solo pueda acceder a los registros que se generan dentro del límite del inquilino.

- El equipo central de SOC puede seguir funcionando desde un inquilino de Azure AD independiente, mediante Azure Lighthouse para acceder a cada uno de los diferentes entornos de Azure Sentinel. Si no hay ningún inquilino adicional, el equipo central de SOC puede seguir usando Azure Lighthouse para acceder a las áreas de trabajo remotas.

- El equipo central de SOC también puede crear un área de trabajo adicional si necesita almacenar artefactos que permanecen ocultos en los equipos de SOC del continente o si quiere ingerir otros datos que no sean pertinentes para los equipos de SOC del continente.



## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Incorporación de Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtención de visibilidad sobre las alertas](get-visibility.md)