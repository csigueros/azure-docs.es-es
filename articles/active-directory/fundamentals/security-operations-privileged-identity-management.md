---
title: Operaciones de seguridad de Azure Active Directory para Privileged Identity Management
description: Guía para establecer líneas de base y usar Azure Active Directory Privileged Identity Management (PIM) para supervisar posibles problemas con las cuentas que se rigen por PIM, y generar alertas sobre ellos.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f17ff93dde6c4bbb7df57d177bc7d1e3d746b347
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814611"
---
# <a name="azure-active-directory-security-operations-for-privileged-identity-management-pim"></a>Operaciones de seguridad de Azure Active Directory para Privileged Identity Management (PIM)

La seguridad de los recursos empresariales depende de la integridad de las cuentas con privilegios que administran los sistemas de TI. Los atacantes cibernéticos usan ataques de robo de credenciales dirigidos a las cuentas de administrador y otras cuentas de acceso con privilegios para intentar acceder a datos confidenciales.

En el caso de los servicios en la nube, la prevención y la respuesta son responsabilidades conjuntas del proveedor de los servicios en la nube y del cliente. 

Tradicionalmente, la seguridad de la organización se ha centrado en los puntos de entrada y salida de una red, como el perímetro de seguridad. Sin embargo, las aplicaciones SaaS y los dispositivos personales han hecho que este enfoque sea menos eficaz. En Azure Active Directory (Azure AD), se ha reemplazado el perímetro de seguridad de la red por la autenticación en la capa de identidad de la organización. A medida que se asignan roles administrativos con privilegios a los usuarios, su acceso debe protegerse en los entornos locales, de nube e híbridos. 

Usted es totalmente responsable de todas las capas de seguridad de su entorno de TI local. Cuando se usan servicios en la nube de Azure, la prevención y la respuesta son responsabilidades conjuntas de Microsoft como proveedor de servicios en la nube y de usted como cliente. 

* Para más información sobre el modelo de responsabilidad compartida, consulte [Responsabilidad compartida en la nube](../../security/fundamentals/shared-responsibility.md).

* Para más información sobre cómo proteger el acceso de los usuarios con privilegios, consulte [Protección del acceso con privilegios para implementaciones híbridas y en la nube en Azure AD](../roles/security-planning.md).

* Puede encontrar una amplia variedad de vídeos, guías paso a paso y contenido de conceptos clave sobre la identidad con privilegios en [Documentación sobre Privileged Identity Management](../privileged-identity-management/index.yml). 

Privileged Identity Management (PIM) es un servicio de Azure AD que permite administrar, controlar y supervisar el acceso a recursos importantes de la organización. Estos recursos incluyen los de Azure AD, Azure y los de otros servicios de Microsoft Online Services, como Microsoft 365 o Microsoft Intune. Puede usar PIM para ayudar a mitigar los siguientes riesgos:

* Identificar y minimizar el número de personas que tienen acceso a información y recursos seguros.

* Detectar permisos de acceso excesivos, innecesarios o mal usados en recursos confidenciales.

* Reducir las posibilidades de que actores malintencionados obtengan acceso a información o recursos protegidos.

* Reducir la posibilidad de que usuarios no autorizados alteren por accidente recursos confidenciales.

En este artículo se proporcionan instrucciones sobre la configuración de líneas de base, la auditoría de inicios de sesión y el uso de cuentas con privilegios, y el origen de los registros de auditoría que puede usar para ayudar a mantener la integridad de las cuentas con privilegios. 

## <a name="where-to-look"></a>Dónde mirar

Los archivos de registro que usa para la investigación y supervisión son: 

* [Registros de auditoría de Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Registros de inicio de sesión](../reports-monitoring/concept-all-sign-ins.md)

* [Registros de auditoría de Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview) 

* [Registros de Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)

En Azure Portal puede ver los registros de auditoría de Azure AD y descargarlos como archivos de valores separados por comas (CSV) o notación de objetos JavaScript (JSON). Azure Portal tiene varias maneras de integrar los registros de Azure AD con otras herramientas que permiten una mayor automatización de la supervisión y las alertas:

* [**Azure Sentinel**](../../sentinel/overview.md) : permite el análisis de seguridad inteligente en el nivel empresarial al ofrecer funcionalidades de administración de eventos e información de seguridad (SIEM). 

* [**Azure Monitor**](../../azure-monitor/overview.md) : permite la supervisión y la generación de alertas automatizadas de diversas condiciones. Puede crear o usar libros para combinar datos de orígenes diferentes.

* [**Azure Event Hubs**](../../event-hubs/event-hubs-about.md) **integrado con una instancia de SIEM**[: los registros de Azure AD se pueden integrar con otras instancias de SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), como Splunk, ArcSight, QRadar y Sumo Logic a través de la integración de Azure Event Hubs.

* [**Microsoft Cloud App Security**](/cloud-app-security/what-is-cloud-app-security) (MCAS): le permite detectar y administrar aplicaciones, controlar las aplicaciones y los recursos y comprobar el cumplimiento de las aplicaciones en la nube. 

En el resto de este artículo se proporcionan recomendaciones para establecer una línea de base sobre la que supervisar y generar alertas, organizada mediante un modelo de niveles. Después de la tabla se enumeran vínculos a soluciones precompiladas. También puede crear alertas mediante las herramientas anteriores. El contenido se organiza en las siguientes áreas de temas de PIM:

* Líneas de base

* Asignación de roles de Azure AD 

* Configuración de alertas de roles de Azure AD

* Asignación de roles de recursos de Azure

* Administración del acceso a los recursos de Azure

* Acceso con privilegios elevados para administrar suscripciones de Azure

## <a name="baselines"></a>Líneas de base

Esta es la configuración de línea de base recomendada:

| Elementos para supervisar| Nivel de riesgo| Recomendación| Roles| Notas |
| - |- |- |- |- |
| Asignación de roles de Azure AD| Alto| <li>Exigir una justificación para la activación.<li>Exigir la aprobación para la activación.<li>Establecer un proceso de aprobación de dos niveles.<li>Durante la activación, exigir Multi-Factor Authentication (MFA) de Azure Active Directory.<li>Establecer la duración máxima de elevación en 8 horas.| <li>Administración de roles con privilegios<li>Administrador global| Un administrador de roles con privilegios puede personalizar PIM en su organización de Azure AD, por ejemplo, cambiar la experiencia de los usuarios que activan una asignación de roles válida. |
| Configuración del rol de recursos de Azure| Alto| <li>Exigir una justificación para la activación.<li>Exigir la aprobación para la activación.<li>Establecer un proceso de aprobación de dos niveles.<li>Durante la activación, exigir Azure MFA.<li>Establecer la duración máxima de elevación en 8 horas.| <li>Propietario<li>Administrador de recursos<li>Acceso de usuario <li>Administrador<li>Administrador global<li>Administrador de seguridad| Investigue inmediatamente si no se trata de un cambio planeado. Esta configuración podría permitir que un atacante accediera a las suscripciones de Azure de su entorno. |


## <a name="azure-ad-roles-assignment"></a>Asignación de roles de Azure AD

Un administrador de roles con privilegios puede personalizar PIM en su organización de Azure AD. Así, por ejemplo, puede cambiar la experiencia de un usuario que activa una asignación de roles válida, como se muestra a continuación:

* Evitar que un actor no autorizado quite los requisitos de Azure MFA para activar el acceso con privilegios.

* Evitar que los usuarios malintencionados omitan la justificación y la aprobación de la activación del acceso con privilegios.

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Alerta sobre la incorporación de cambios a permisos de cuenta con privilegios| Alto| Registros de auditoría de Azure AD| Categoría = Administración de roles<br>- y -<br>Tipo de actividad: agregar un miembro válido (permanente) <br>- y -<br>Tipo de actividad: agregar un miembro válido (opcional) <br>- y -<br>Estado = correcto/error<br>- y -<br>Propiedades modificadas = Role.DisplayName| Supervise siempre los cambios en el administrador de roles con privilegios y en el administrador global, y genere alertas al respecto. <li>Podrían indicar que un atacante intenta obtener privilegios para modificar la configuración de asignación de roles.<li> Si no tiene un umbral definido, genere una alerta cuando haya 4 en 60 minutos para los usuarios y cuando haya 2 en 60 minutos para las cuentas con privilegios. |
| Alerta sobre los cambios de eliminación masiva en los permisos de cuentas con privilegios| Alto| Registros de auditoría de Azure AD| Categoría = Administración de roles<br>- y -<br>Tipo de actividad: quitar un miembro válido (permanente) <br>- y -<br>Tipo de actividad: quitar un miembro válido (opcional) <br>- y -<br>Estado = correcto/error<br>- y -<br>Propiedades modificadas = Role.DisplayName| Investigue inmediatamente si no se trata de un cambio planeado. Esta configuración podría permitir que un atacante accediera a las suscripciones de Azure de su entorno. |
| Cambios en la configuración de PIM| Alto| Registro de auditoría de Azure AD| Servicio = PIM<br>- y -<br>Categoría = Administración de roles<br>- y -<br>Tipo de actividad = Actualización de la configuración de roles en PIM<br>- y -<br>Motivo de estado = MFA durante la activación deshabilitada (ejemplo)| Supervise siempre los cambios en el administrador de roles con privilegios y en el administrador global, y genere alertas al respecto. <li>Pueden indicar que un atacante ya ha obtenido acceso capaz de modificar la configuración de asignación de roles.<li>Una de estas acciones podría reducir la seguridad de la elevación de PIM y facilitar a los atacantes la adquisición de una cuenta con privilegios. |
| Elevación de aprobaciones y denegaciones| Alto| Registro de auditoría de Azure AD| Servicio = Revisión de acceso<br>- y -<br>Categoría = UserManagement<br>- y -<br>Tipo de actividad = Solicitud aprobada o denegada<br>- y -<br>Actor iniciado = UPN| Se deben supervisar todas las elevaciones. Registre todas las elevaciones, ya que podrían ser un indicio claro de la escala de tiempo de un ataque. |
| La configuración de alerta cambia a deshabilitado.| Alto| Registros de auditoría de Azure AD| Servicio =PIM<br>- y -<br>Categoría = Administración de roles<br>- y -<br>Tipo de actividad = Deshabilitar alerta de PIM<br>- y -<br>Estado = correcto/error| Genere una alerta siempre. <li>Ayuda a detectar actores no autorizados que quitan alertas asociadas a los requisitos de Azure MFA para activar el acceso con privilegios.<li>Ayuda a detectar actividades sospechosas o poco seguras. |


Para más información sobre cómo identificar cambios en la configuración de roles en el registro de auditoría de Azure AD, consulte [Visualización del historial de auditoría para los roles de Azure AD en Privileged Identity Management](../privileged-identity-management/pim-how-to-use-audit-log.md). 

## <a name="azure-resource-role-assignment"></a>Asignación de roles de recursos de Azure

La supervisión de las asignaciones de roles de recursos de Azure proporciona visibilidad sobre la actividad y las activaciones de los roles de recursos. Es posible que se utilicen incorrectamente para crear una superficie de ataque a un recurso. Cuando supervise este tipo de actividad, intente detectar:

* Asignaciones de roles de consulta en recursos específicos

* Asignaciones de roles para todos los recursos secundarios

* Todos los cambios de asignación de roles activos y válidos

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Auditoría de recursos de alerta Auditoría de registro de actividades de cuentas con privilegios| Alto| En PIM, en recursos de Azure, Auditoría de recursos| Acción: Agregar un miembro válido al rol en PIM finalizado (límite de tiempo) <br>- y -<br>Destino principal <br>- y -<br>Tipo = Usuario<br>- y -<br>Estado = Correcto<br>| Genere una alerta siempre. Ayuda a detectar actores no autorizados que agregan roles válidos para administrar todos los recursos de Azure. |
| Auditoría de recurso de alerta Auditoría para deshabilitar alerta| Media| En PIM, en recursos de Azure, Auditoría de recursos| Acción: Deshabilitar alerta<br>- y -<br>Destino principal: Demasiados propietarios asignados a un recurso<br>- y -<br>Estado = Correcto| Ayuda a detectar actores no autorizados que deshabilitan las alertas desde el panel Alertas, lo que puede pasar por alto la actividad malintencionada que se investiga. |
| Auditoría de recursos de alerta Auditoría para deshabilitar alerta| Media| En PIM, en recursos de Azure, Auditoría de recursos| Acción: Deshabilitar alerta<br>- y -<br>Destino principal: Demasiados propietarios permanentes asignados a un recurso<br>- y -<br>Estado = Correcto| Impedir que un actor no autorizado deshabilite las alertas del panel Alertas, lo que puede pasar por alto la actividad malintencionada que se investiga. |
| Auditoría de recursos de alerta Auditoría para deshabilitar alerta| Media| En PIM, en recursos de Azure, Auditoría de recursos| Acción: Deshabilitar alerta<br>- y -<br>Se ha creado un rol duplicado de destino principal<br>- y -<br>Estado = Correcto| Impedir que un actor no autorizado deshabilite las alertas del panel Alertas, lo que puede pasar por alto la actividad malintencionada que se investiga. |


Para más información sobre la configuración de alertas y la auditoría de roles de recursos de Azure, consulte:

* [Configuración de alertas de seguridad para roles de recursos de Azure en Privileged Identity Management](../privileged-identity-management/pim-resource-roles-configure-alerts.md)

* [Visualización del informe de auditoría para los roles de recursos de Azure en Privileged Identity Management (PIM)](../privileged-identity-management/azure-pim-resource-rbac.md)

## <a name="access-management-for-azure-resources-and-subscriptions"></a>Administración del acceso para recursos y suscripciones de Azure

Los usuarios o miembros de un grupo que tienen asignados los roles de suscripciones de propietario o de administrador de acceso de usuario, y los administradores globales de Azure AD que permiten la administración de suscripciones en Azure AD tienen, de forma predeterminada, permisos de administrador de recursos. Estos administradores pueden asignar roles, configurar opciones de rol y revisar el acceso con Privileged Identity Management (PIM) a los recursos de Azure. 

Un usuario que tenga permisos de administrador de recursos puede administrar PIM para los recursos. El riesgo que esto supone, y que se debe supervisar y mitigar, es que la funcionalidad se puede usar para permitir que actores no autorizados tengan acceso con privilegios a los recursos de la suscripción de Azure, como máquinas virtuales o cuentas de almacenamiento.

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Elevaciones| Alto| Azure AD, en Administrar, Propiedades| Revise periódicamente la configuración.<br>Administración del acceso a los recursos de Azure| Los administradores globales pueden elevar sus privilegios habilitando la administración del acceso a los recursos de Azure.<br>Compruebe que actores no autorizados no hayan obtenido permisos para asignar roles en todas las suscripciones y grupos de administración de Azure asociados a Active Directory. |


Para más información, consulte [Asignación de roles de recursos de Azure en Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md).

## <a name="next-steps"></a>Pasos siguientes
Consulte estos artículos de la guía de operaciones de seguridad:

[Introducción a las operaciones de seguridad de Azure AD](security-operations-introduction.md)

[Operaciones de seguridad para cuentas de usuario](security-operations-user-accounts.md)

[Operaciones de seguridad para cuentas con privilegios](security-operations-privileged-accounts.md)

[Operaciones de seguridad para Privileged Identity Management](security-operations-privileged-identity-management.md)

[Operaciones de seguridad para aplicaciones](security-operations-applications.md)

[Operaciones de seguridad para dispositivos](security-operations-devices.md)
 
[Operaciones de seguridad para infraestructura](security-operations-infrastructure.md)