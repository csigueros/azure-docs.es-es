---
title: Administración de áreas de trabajo de Microsoft Sentinel a gran escala
description: Azure Lighthouse ayuda a administrar Microsoft Sentinel de manera eficaz en recursos delegados del cliente.
ms.date: 11/05/2021
ms.topic: how-to
ms.openlocfilehash: 042a7d376a82ed70782db252e3ef17274b7cf7c7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289933"
---
# <a name="manage-microsoft-sentinel-workspaces-at-scale"></a>Administración de áreas de trabajo de Microsoft Sentinel a gran escala

Como proveedor de servicios, es posible que haya incorporado varios inquilinos de cliente para [Azure Lighthouse](../overview.md). Azure Lighthouse permite a los proveedores de servicios realizar operaciones a escala a través de varios inquilinos de Azure Active Directory (Azure AD) a la vez, lo que hace que las tareas de administración sean más eficaces.

Microsoft Sentinel ofrece análisis de seguridad e inteligencia sobre amenazas, con lo que proporciona una única solución para la detección de alertas, la visibilidad de amenazas, la búsqueda proactiva y la respuesta a amenazas. Con Azure Lighthouse puede administrar varias áreas de trabajo de Microsoft Sentinel en inquilinos a gran escala. Esto permite escenarios como la ejecución de consultas en varias áreas de trabajo o la creación de libros para visualizar y supervisar los datos de los orígenes de datos conectados para obtener información. La dirección IP, como las consultas y los cuadernos de estrategias, permanecen en el inquilino de administración, pero se puede usar para realizar la administración de seguridad en los inquilinos del cliente.

Este tema proporciona información general sobre cómo usar [Microsoft Sentinel](../../sentinel/overview.md) de forma escalable para la visibilidad entre inquilinos y los servicios de seguridad administrada.

> [!TIP]
> Aunque en este tema hacemos referencia a proveedores de servicios y clientes, esta guía es aplicable también a [empresas que usan Azure Lighthouse para administrar varios inquilinos](../concepts/enterprise.md).

> [!NOTE]
> Puede administrar recursos delegados que estén ubicados en diferentes [regiones](../../availability-zones/az-overview.md#regions). Sin embargo, no se admite la delegación de suscripciones entre una [nube nacional](../../active-directory/develop/authentication-national-cloud.md) y la nube pública de Azure o entre dos nubes nacionales independientes.

## <a name="architectural-considerations"></a>Consideraciones arquitectónicas

En el caso de un proveedor de servicios de seguridad administrada (MSSP) que quiera compilar una oferta de seguridad como servicio con Microsoft Sentinel, es posible que se necesite un solo centro de operaciones de seguridad (SOC) para supervisar, administrar y configurar de forma centralizada varias áreas de trabajo de Microsoft Sentinel implementadas en inquilinos individuales del cliente. Del mismo modo, es posible que empresas con varios inquilinos de Azure AD quieran administrar de forma centralizada varias áreas de trabajo de Microsoft Sentinel implementadas en sus inquilinos.

Este modelo de implementación presenta las siguientes ventajas:

- La propiedad de los datos sigue siendo de cada inquilino administrado.
- Admite requisitos para almacenar datos dentro de los límites geográficos.
- Garantiza el aislamiento de datos, ya que los datos de varios clientes no se almacenan en la misma área de trabajo.
- Evita la filtración de datos de los inquilinos administrados, lo que ayuda a garantizar el cumplimiento de los datos.
- Los costos relacionados se cargan a cada inquilino administrado, en lugar de al inquilino administrador.
- Los datos de todos los orígenes de datos y conectores de datos que se integran con Microsoft Sentinel (como los registros de actividad de Azure AD, los registros de Office 365 o las alertas de Protección contra amenazas de Microsoft) permanecen dentro de cada inquilino del cliente.
- Reduce la latencia de red.
- Facilidad para agregar o quitar nuevas subsidiarias o clientes.
- Permite usar una vista de varias áreas de trabajo al trabajar con Azure Lighthouse.
- Para proteger su propiedad intelectual, puede usar cuadernos de estrategias y libros para trabajar entre inquilinos sin compartir código directamente con los clientes. Solo las reglas analíticas y de búsqueda se deben guardar directamente en el inquilino de cada cliente.

> [!IMPORTANT]
> Si todas las áreas de trabajo se crean en los inquilinos del cliente, los proveedores de recursos Microsoft.SecurityInsights y Microsoft.OperationalInsights también deben [registrarse](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) en una suscripción del inquilino de administración.

Un modelo de implementación alternativo es crear un área de trabajo de Microsoft Sentinel en el inquilino de administración. En este modelo, Azure Lighthouse permite recopilar registros de orígenes de datos entre inquilinos administrados. Pero hay algunos orígenes de datos que no se pueden conectar entre inquilinos, como Microsoft Defender for Cloud. Debido a esta limitación, este modelo no es adecuado para muchos escenarios de proveedores de servicios.

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>Control de acceso basado en rol de Azure (RBAC de Azure) detallado

Cada suscripción de cliente que administrará un MSSP debe [incorporarse a Azure Lighthouse](onboard-customer.md). Esto permite a los usuarios designados del inquilino de administración acceder y realizar operaciones de administración en áreas de trabajo de Microsoft Sentinel implementadas en inquilinos del cliente.

Al crear las autorizaciones, puede asignar los roles integrados de Microsoft Sentinel a usuarios, grupos o entidades de servicio del inquilino de administración:

- [Lector de Microsoft Sentinel](../../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)
- [Respondedor de Microsoft Sentinel](../../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder)
- [Colaborador de Microsoft Sentinel](../../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)

También puede asignar roles integrados adicionales para realizar funciones adicionales. Para obtener información sobre roles concretos que se pueden usar con Microsoft Sentinel, vea [Permisos de Microsoft Sentinel](../../sentinel/roles.md).

Una vez que haya incorporado a los clientes, los usuarios designados pueden iniciar sesión en el inquilino de administración y [acceder directamente al área de trabajo de Microsoft Sentinel del cliente](../../sentinel/multiple-tenants-service-providers.md) con los roles asignados.

## <a name="view-and-manage-incidents-across-workspaces"></a>Ver y administrar incidentes en áreas de trabajo

Si va a administrar recursos de Microsoft Sentinel de varios clientes, puede ver y administrar incidentes en varias áreas de trabajo en varios inquilinos a la vez. Para obtener más información, vea [Procesamiento de incidentes en varias áreas de trabajo a la vez](../../sentinel/multiple-workspace-view.md) y [Extender Microsoft Sentinel por áreas de trabajo e inquilinos](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Asegúrese de que se asignaron permisos de lectura y escritura a los usuarios del inquilino de administración en todas las áreas de trabajo que se administran. Si un usuario solo tiene permisos de lectura en algunas áreas de trabajo, pueden aparecer mensajes de advertencia al seleccionar incidentes en esas áreas de trabajo, y el usuario no podrá modificar esos incidentes ni ningún otro que haya seleccionado con ellos (aunque tenga permisos para los demás).

## <a name="configure-playbooks-for-mitigation"></a>Configurar los cuadernos de estrategias para la mitigación

[Los cuadernos de estrategias](../../sentinel/tutorial-respond-threats-playbook.md) pueden usarse para la mitigación automática cuando se desencadena una alerta. Estos cuadernos de estrategias se pueden ejecutar de forma manual o automática cuando se desencadenan alertas específicas. Los cuadernos de estrategias se pueden implementar en el inquilino de administración o en el inquilino del cliente, con los procedimientos de respuesta configurados en función de los usuarios del inquilino que deberán tomar medidas en respuesta a una amenaza de seguridad.

## <a name="create-cross-tenant-workbooks"></a>Crear libros entre inquilinos

Los [libros de Azure Monitor de Microsoft Sentinel](../../sentinel/overview.md#workbooks) ayudan a visualizar y supervisar datos de los orígenes de datos conectados para obtener información. Puede usar las plantillas de libro integradas en Microsoft Sentinel o crear libros personalizados para los escenarios.

Puede implementar libros en el inquilino de administración y crear paneles a escala para supervisar y consultar datos en los inquilinos de los clientes. Para obtener más información consulte [Supervisión entre áreas de trabajo](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks).

También puede implementar libros directamente en un inquilino individual que administra para escenarios específicos de ese cliente.

## <a name="run-log-analytics-and-hunting-queries-across-microsoft-sentinel-workspaces"></a>Ejecución de Log Analytics y consultas de búsqueda en áreas de trabajo de Microsoft Sentinel

Cree y guarde consultas de Log Analytics para la detección de amenazas de forma centralizada en el inquilino de administración, incluidas las [consultas de búsqueda](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting). Luego, estas consultas se pueden ejecutar en las áreas de trabajo de Microsoft Sentinel de todos los clientes mediante el operador de unión y la expresión () de área de trabajo. Para obtener más información consulte [Consultas entre áreas de trabajo](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Usar la automatización para la administración entre áreas de trabajo

Puede usar automatización para administrar varias áreas de trabajo de Microsoft Sentinel y configurar [consultas de búsqueda](../../sentinel/hunting.md), cuadernos de estrategias y libros. Para más información consulte [Administración entre áreas de trabajo con automatización](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

## <a name="monitor-security-of-office-365-environments"></a>Supervisión de la seguridad de los entornos de Office 365

Use Azure Lighthouse junto con Microsoft Sentinel para supervisar la seguridad de los entornos de Office 365 entre inquilinos. En primer lugar, los [conectores de datos de serie de Office 365 deben estar habilitados en el inquilino administrado](../../sentinel/data-connectors-reference.md#microsoft-office-365) para que la información sobre las actividades de usuarios y administradores en Exchange y SharePoint (incluido OneDrive) se pueda ingerir en un área de trabajo de Microsoft Sentinel en el inquilino administrado. Esto incluye detalles sobre acciones como descargas de archivos, solicitudes de acceso enviadas, cambios en eventos de grupo y operaciones de buzón de correo, junto con información sobre los usuarios que realizaron dichas acciones. Las [alertas de DLP de Office 365](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) también se admiten como parte del conector integrado de Office 365.

[Conector de Microsoft Defender for Cloud Apps](../../sentinel/data-connectors-reference.md#microsoft-cloud-app-security-mcas) para transmitir alertas y registros de Cloud Discovery a Microsoft Sentinel. Esto ofrece visibilidad sobre las aplicaciones en la nube, proporciona sofisticados análisis para identificar y combatir ciberamenazas y ayuda a controlar cómo viajan los datos. Los registros de actividad de Defender for Cloud Apps se pueden [consumir mediante el formato de evento común (CEF)](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849).

Después de configurar los conectores de datos de Office 365, puede usar capacidades de Microsoft Sentinel entre inquilinos, como la visualización y el análisis de los datos de los libros, el uso de consultas para crear alertas personalizadas y la configuración de cuadernos de estrategias para responder a amenazas.

## <a name="protect-intellectual-property"></a>Protección de la propiedad intelectual

Al trabajar con clientes, se recomienda proteger la propiedad intelectual desarrollada en Microsoft Sentinel, como reglas de análisis de Microsoft Sentinel, consultas de búsqueda, cuadernos de estrategias y libros. Hay distintos métodos que puede usar para asegurarse de que los clientes no tienen acceso completo al código utilizado en estos recursos.

Para obtener más información, vea [Protección de la propiedad intelectual del MSSP en Microsoft Sentinel](../../sentinel/mssp-protect-intellectual-property.md).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Microsoft Sentinel](../../sentinel/overview.md).
- Revise la [página de precios de Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Explore [`Sentinel All-in-One`](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/Sentinel-All-In-One), un proyecto para acelerar las tareas de implementación y configuración inicial de un entorno de Microsoft Sentinel.
- Más información sobre las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md).
