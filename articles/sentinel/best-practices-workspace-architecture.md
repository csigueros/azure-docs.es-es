---
title: Procedimientos recomendados de arquitectura de áreas de trabajo de Azure Sentinel
description: Obtenga información sobre los procedimientos recomendados para diseñar el área trabajo de Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/28/2021
ms.openlocfilehash: 085b9a187ce574c5138867f7a2ce929218717c37
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253823"
---
# <a name="azure-sentinel-workspace-architecture-best-practices"></a>Procedimientos recomendados de arquitectura de áreas de trabajo de Azure Sentinel

Al planear la implementación del área de trabajo de Azure Sentinel, también debe diseñar la arquitectura del área de trabajo de Log Analytics. Las decisiones sobre la arquitectura del área de trabajo suelen estar controladas por requisitos técnicos y empresariales. En este artículo se revisan los factores clave de decisión para ayudarle a determinar la arquitectura de área de trabajo adecuada para las organizaciones, incluido:

- Si va a usar un solo inquilino o varios inquilinos
- Cualquier requisito de cumplimiento que tenga para la recopilación y el almacenamiento de datos
- Control del acceso a los datos de Azure Sentinel
- Implicaciones de costes para distintos escenarios

Para obtener más información, consulte [Diseñar la arquitectura del área de trabajo de Azure Sentinel](design-your-workspace-architecture.md) y [Diseños de áreas de trabajo de ejemplo](sample-workspace-designs.md) para escenarios comunes, y [Actividades previas a la implementación y requisitos previos para implementar Azure Sentinel](prerequisites.md).

Vea nuestro vídeo: [Architecting SecOps for Success: Best Practices for Deploying Azure Sentinel](https://youtu.be/DyL9MEMhqmI) (Arquitectura de SecOps para el éxito: procedimientos recomendados de implementación de Azure Sentinel)


## <a name="tenancy-considerations"></a>Consideraciones sobre el alquiler

Aunque es más fácil de administrar menos áreas de trabajo, es posible que tenga necesidades específicas para varios inquilinos y áreas de trabajo. Por ejemplo, muchas organizaciones tienen un entorno en la nube que contiene varios [inquilinos Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md), resultantes de fusiones y adquisiciones o debido a los requisitos de separación de identidades.

Al determinar cuántos inquilinos y áreas de trabajo usar, tenga en cuenta que la mayoría de las características de Azure Sentinel funcionan mediante una sola área de trabajo o una instancia de Azure Sentinel, y Azure Sentinel ingiere todos los registros que se encuentran dentro del área de trabajo.

> [!IMPORTANT]
> Los costes son una de las consideraciones principales a la hora de determinar la arquitectura de Azure Sentinel. Para obtener más información, consulte [Costes y facturación de Azure Sentinel](azure-sentinel-billing.md).
>
### <a name="working-with-multiple-tenants"></a>Trabajo con varios inquilinos

Si tiene varios inquilinos, por ejemplo, si es un proveedor de servicios de seguridad administrado (MSSP), se recomienda crear al menos un área de trabajo para cada inquilino de Azure AD para admitir [conectores de datos de servicio a servicio](connect-data-sources.md#service-to-service-integration) integrados que solo funcionan dentro de su propio inquilino de Azure AD.

Todos los conectores basados en la configuración de diagnóstico no se pueden conectar a un área de trabajo que no se encuentra en el mismo inquilino donde reside el recurso. Esto se aplica a conectores como [Azure Firewall](connect-azure-firewall.md), [Azure Storage](connect-azure-storage-account.md), [Azure Activity](connect-azure-activity.md) o [Azure Active Directory](connect-azure-active-directory.md).

Utilice [Azure Lighthouse](../lighthouse/how-to/onboard-customer.md) para ayudar a administrar varias instancias de Azure Sentinel en diferentes inquilinos.

> [!NOTE]
> Los [conectores de datos de asociados](data-connectors-reference.md) normalmente se basan en colecciones de agentes o API y, por tanto, no se adjuntan a un inquilino de Azure AD concreto.
>



## <a name="compliance-considerations"></a>Consideraciones de cumplimiento normativo

Una vez recopilados, almacenados y procesados los datos, el cumplimiento puede convertirse en un requisito de diseño importante, con un impacto significativo en la arquitectura de Azure Sentinel. Tener la capacidad de validar y demostrar quién tiene acceso a los datos bajo todas las condiciones es un requisito crítico de soberanía de datos en muchos países y regiones, y evaluar los riesgos y obtener información detallada en los flujos de trabajo de Azure Sentinel es una prioridad para muchos clientes.

En Azure Sentinel, los datos se almacenan y procesan principalmente en la misma zona geográfica o región, con algunas excepciones, como cuando se usan reglas de detección que aprovechan el aprendizaje automático de Microsoft. En tales casos, los datos se pueden copiar fuera de la zona geográfica del área de trabajo para su procesamiento.

Para más información, consulte:

- [Disponibilidad geográfica y residencia de datos](quickstart-onboard.md#geographical-availability-and-data-residency)
- [Residencia de datos en Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)
- [Almacenamiento y procesamiento de datos de la UE en Europa: blog de directivas de la UE](https://blogs.microsoft.com/eupolicy/2021/05/06/eu-data-boundary/)

Para empezar a validar el cumplimiento, evalúe los orígenes de datos y cómo y dónde envían los datos.

> [!NOTE]
> El [agente de Log Analytics](connect-windows-security-events.md) admite TLS 1.2 para garantizar la seguridad de los datos en tránsito entre el agente y el servicio Log Analytics, así como el estándar FIPS 140. 
>
> Si va a enviar datos a una ubicación geográfica o región distinta de la del área de trabajo de Azure Sentinel, independientemente de si el recurso de envío reside o no en Azure, considere la posibilidad de usar un área de trabajo en la misma región o zona geográfica.
>

## <a name="region-considerations"></a>Consideraciones sobre las regiones

Use instancias de Azure Sentinel independientes para cada región. Aunque Azure Sentinel se puede usar en varias regiones, es posible que tenga requisitos para separar los datos por equipo, región o sitio, o regulaciones y controles que hacen que los modelos de varias regiones sean imposibles o más complejos de lo necesario. El uso de instancias y áreas de trabajo independientes para cada región ayuda a evitar costes de ancho de banda o salida para mover datos entre regiones.

Tenga en cuenta lo siguiente al trabajar con varias regiones:

- Suelen aplicarse los costes de salida cuando se requiere el agente de [Log Analytics o Azure Monitor](connect-windows-security-events.md) para recopilar registros, como en máquinas virtuales.

- También se cobra la salida de Internet, lo que puede no afectarle a menos que exporte datos fuera del área de trabajo de Log Analytics. Por ejemplo, puede incurrir en cargos de salida de Internet si exporta los datos de Log Analytics a un servidor local.

- Los costes de ancho de banda varían en función de la región de origen y destino y el método de recopilación. Para más información, consulte:

    - [Precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/)
    - [Cargos por transferencias de datos mediante Log Analytics](../azure-monitor/logs/manage-cost-storage.md).

- Use plantillas para las reglas de análisis, consultas personalizadas, libros y otros recursos para que las implementaciones sean más eficientes. Implemente las plantillas en lugar de implementar manualmente cada recurso en cada región.

- Los conectores basados en la configuración de diagnóstico no incurren en costes de ancho de banda. Para obtener más información, consulte [Administración del uso y los costes con los registros de Azure Monitor](../azure-monitor/logs/manage-cost-storage.md#data-transfer-charges-using-log-analytics).

Por ejemplo, si decide recopilar registros de máquinas virtuales en el este de EE. UU. y enviarlos a un área de trabajo de Azure Sentinel en el oeste de EE. UU., se le cobrarán los costes de entrada por la transferencia de datos. Puesto que el agente de Log Analytics comprime los datos en tránsito, el tamaño que se cobra por el ancho de banda puede ser menor que el tamaño de los registros en Azure Sentinel.

Si va a recopilar registros de Syslog y CEF de varios orígenes de todo el mundo, puede que desee configurar un recopilador de Syslog en la misma región que el área de trabajo de Azure Sentinel para evitar costes de ancho de banda, siempre que el cumplimiento no sea un problema.

Comprender si los costes de ancho de banda justifican áreas de trabajo independientes de Azure Sentinel depende del volumen de datos que necesite transferir entre regiones. Puede usar la [calculadora de precios de Azure](https://azure.microsoft.com/pricing/details/bandwidth/) para calcular los costes.

Para obtener más información, consulte [Residencia de datos en Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

## <a name="access-considerations"></a>Consideraciones acerca del acceso

Es posible que tenga situaciones planeadas en las que distintos equipos necesitarán tener acceso a los mismos datos. Por ejemplo, el equipo de SOC debe tener acceso a todos los datos Azure Sentinel, mientras que los equipos de operaciones y aplicaciones solo necesitarán acceso a partes específicas. Es posible que los equipos de seguridad independientes también necesiten acceder a características de Azure Sentinel, pero con distintos conjuntos de datos.

Combine [RBAC de contexto de recursos](resource-context-rbac.md) y [RBAC de nivel de tabla](../azure-monitor/logs/manage-access.md#table-level-azure-rbac) para proporcionar a sus equipos una amplia gama de opciones de acceso que deberían permitir la mayoría de los casos de uso.

Para obtener más información, vea [Permisos de Azure Sentinel](roles.md).

### <a name="resource-context-rbac"></a>RBAC de contexto del recurso.

En la imagen siguiente se muestra una versión simplificada de una arquitectura de área de trabajo en la que los equipos de seguridad y operaciones necesitan tener acceso a diferentes conjuntos de datos y se usa RBAC de contexto de recurso para proporcionar los permisos necesarios.


[ ![Diagrama de una arquitectura de ejemplo para RBAC de contexto de recursos.](media/resource-context-rbac/resource-context-rbac-sample.png) ](media/resource-context-rbac/resource-context-rbac-sample.png#lightbox)

En esta imagen, el área de trabajo de Azure Sentinel se coloca en una suscripción independiente para aislar mejor los permisos.

> [!NOTE]
> Otra opción sería colocar Azure Sentinel en un grupo de administración independiente dedicado a la seguridad, lo que garantizaría que solo se hereden las asignaciones de permisos mínimas. Dentro del equipo de seguridad, a varios grupos se les asignan permisos según sus funciones. Dado que estos equipos tienen acceso a todo el área de trabajo, tendrán acceso a la experiencia Azure Sentinel completa, restringida solo por los roles de Azure Sentinel que estén asignados. Para obtener más información, vea [Permisos de Azure Sentinel](roles.md).
>

Además de la suscripción de seguridad, se usa una suscripción independiente para que los equipos de aplicaciones hospeden sus cargas de trabajo. A los equipos de aplicaciones se les concede acceso a sus respectivos grupos de recursos, donde pueden administrar los recursos. Este RBAC de contexto de recurso y suscripción independiente permite a estos equipos ver los registros generados por los recursos a los que tienen acceso, incluso cuando los registros se almacenan en un área de trabajo donde *no* tienen acceso directo. Los equipos de aplicaciones pueden acceder a sus registros a través del área **Registros** del Azure Portal para mostrar los registros de un recurso específico, o a través de Azure Monitor, para mostrar todos los registros a los que pueden acceder al mismo tiempo.

Los recursos de Azure tienen compatibilidad integrada con RBAC de contexto de recursos, pero pueden requerir un ajuste adicional al trabajar con recursos que no son de Azure. Para más información, consulte [Configuración explícita de RBAC de contexto de recursos](resource-context-rbac.md#explicitly-configure-resource-context-rbac).

### <a name="table-level-rbac"></a>RBAC de nivel de tabla

RBAC de nivel de tabla permite definir tipos de datos específicos (tablas) para que solo sean accesibles para un conjunto especificado de usuarios.

Por ejemplo, considere si la organización cuya arquitectura se describe en la imagen anterior también debe conceder acceso a los registros de Office 365 a un equipo de auditoría interno. En este caso, podrían usar RBAC de nivel de tabla para conceder al equipo de auditoría acceso a toda la tabla **OfficeActivity**, sin conceder permisos a ninguna otra tabla.

### <a name="access-considerations-with-multiple-workspaces"></a>Consideraciones de acceso con varias áreas de trabajo

Si tiene diferentes entidades, subsidiarias o zonas geográficas dentro de su organización, cada una con sus propios equipos de seguridad que necesitan acceso a Azure Sentinel, use áreas de trabajo independientes para cada entidad o subsidiaria. Implemente las áreas de trabajo independientes dentro de un único inquilino de Azure AD o entre varios inquilinos mediante Azure Lighthouse. 

El equipo central de SOC también puede usar un área de trabajo adicional de Azure Sentinel para administrar artefactos centralizados, como reglas de análisis o libros.

Para obtener más información, consulte [Simplificación del trabajo con varias áreas de trabajo](#simplify-working-with-multiple-workspaces).


## <a name="technical-best-practices-for-creating-your-workspace"></a>Procedimientos recomendados técnicos para crear el área de trabajo

Use las siguientes instrucciones de procedimientos recomendados al crear el área de trabajo de Log Analytics que usará para Azure Sentinel:

- **Al nombrar su área de trabajo**, incluya *Azure Sentinel* o algún otro indicador en el nombre para que sea fácilmente identificable entre sus otras áreas de trabajo.

- **Use la misma área de trabajo para Azure Sentinel y Azure Security Center**, de modo que todos los registros recopilados por Azure Security Center también puedan ingerir y usar Azure Sentinel. El área de trabajo predeterminada creada por Azure Security Center no aparecerá como un área de trabajo disponible para Azure Sentinel.

- **Use un clúster de área de trabajo dedicado si la ingesta de datos proyectada es de aproximadamente o más de 1 TB al día**. Un [clúster dedicado](../azure-monitor/logs/logs-dedicated-clusters.md) permite proteger los recursos de los datos de Azure Sentinel, lo que permite un mejor rendimiento de las consultas para grandes conjuntos de datos. Los clústeres dedicados también proporcionan la opción de más cifrado y control de las claves de su organización.

## <a name="simplify-working-with-multiple-workspaces"></a>Simplificación del trabajo con varias áreas de trabajo

Si necesita trabajar con varias áreas de trabajo, simplifique la administración e investigación de incidentes al [condensar y enumerar todos los incidentes de cada instancia de Azure Sentinel en una sola ubicación](multiple-workspace-view.md).

Para hacer referencia a datos que se encuentran en otros espacios de trabajo de Azure Sentinel, como en [libros entre áreas de trabajo](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks), utilice [consultas entre áreas de trabajo](extend-sentinel-across-workspaces-tenants.md).

El mejor momento para usar consultas entre áreas de trabajo es cuando se almacena información valiosa en un área de trabajo, una suscripción o un inquilino diferentes, y puede proporcionar valor a la acción actual. Por ejemplo, el código siguiente muestra una consulta de ejemplo entre áreas de trabajo:

```Kusto
union Update, workspace("contosoretail-it").Update, workspace("WORKSPACE ID").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

Para obtener más información, consulte [Extender Azure Sentinel por áreas de trabajo e inquilinos](extend-sentinel-across-workspaces-tenants.md).
## <a name="next-steps"></a>Pasos siguientes


> [!div class="nextstepaction"]
>[Diseño de una arquitectura de áreas de trabajo de Azure Sentinel](design-your-workspace-architecture.md)

> [!div class="nextstepaction"]
>[Diseños de ejemplo de áreas de trabajo de Azure Sentinel](sample-workspace-designs.md)

> [!div class="nextstepaction"]
>[Incorporación de Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Obtención de visibilidad sobre las alertas](get-visibility.md)