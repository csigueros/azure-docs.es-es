---
title: Entornos de prueba, limitación y límites de servicio
titleSuffix: Microsoft identity platform
description: Obtenga información sobre la limitación y los límites de servicio que se deben tener en cuenta al implementar un entorno de prueba de Azure Active Directory y probar una aplicación integrada con la Plataforma de identidad de Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2021
ms.author: ryanwi
ms.reviewer: arcrowe
ms.openlocfilehash: 35f7694dbdf3d7c4f3065f19dc89bac7d7b1915e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367622"
---
# <a name="throttling-and-service-limits-to-consider-for-testing"></a>Limitación y límites de servicio que se deben tener en cuenta para las pruebas
Como desarrollador, quiere probar la aplicación antes de publicarla en producción. Al probar aplicaciones protegidas por la Plataforma de identidad de Microsoft, debe configurar un entorno de Azure Active Directory (Azure AD) y un inquilino que se usará para las pruebas.  

Las aplicaciones que se integran con la Plataforma de identidad de Microsoft requieren que los objetos de directorio (como registros de aplicaciones, entidades de servicio, grupos y usuarios) se puedan crear y administrar en un inquilino de Azure AD.  Cualquier configuración de inquilino de producción que afecte al comportamiento de la aplicación se debe replicar en el inquilino de prueba. Rellene el inquilino de prueba con las directivas de acceso condicional, concesión de permisos, asignación de notificaciones, duración del token y emisión de tokens necesarias. La aplicación también puede usar recursos de Azure, como proceso o almacenamiento, que deben agregarse al entorno de prueba. El entorno de prueba puede requerir muchos recursos, dependiendo de la aplicación que se va a probar.

Para garantizar el uso confiable de los servicios por parte de todos los clientes, Azure AD y otros servicios limitan el número de recursos que se pueden crear por cliente y por inquilino. Al configurar un entorno de prueba e implementar objetos de directorio y recursos de Azure, puede alcanzar algunos de estos límites y cuotas de servicio.

Azure AD, Microsoft Graph y otros servicios de Azure también limitan el número de llamadas simultáneas a un servicio o limitan la cantidad de carga de proceso por cliente para evitar el uso excesivo de recursos. Se trata de una práctica conocida como limitación y garantiza que los servicios de Azure puedan controlar el uso y las solicitudes entrantes sin interrupciones del servicio. La limitación puede producirse en el nivel de aplicación, inquilino o servicio completo. La limitación se produce normalmente cuando una aplicación tiene un gran número de solicitudes dentro o entre inquilinos.  En el entorno de ejecución, la aplicación puede leer o actualizar objetos de directorio de Azure AD mediante Microsoft Graph como parte de su lógica de negocios. Por ejemplo, leer o establecer atributos de usuario, actualizar el calendario de un usuario o enviar correos electrónicos en nombre del usuario.  Mientras se ejecuta, la aplicación también podría implementar, actualizar y eliminar recursos de Azure, así como acceder a ellos. Durante las pruebas, la aplicación podría alcanzar estos límites de tiempo de ejecución y los límites de servicio mencionados anteriormente al implementar recursos u objetos de directorio.

## <a name="azure-ad-service-limits-relevant-to-testing"></a>Límites de servicio de Azure AD pertinentes para las pruebas
Puede encontrar las restricciones de uso general y los límites de servicio de Azure AD [aquí](/azure/active-directory/enterprise-users/directory-service-limits-restrictions).  Puede encontrar los límites, cuotas y restricciones de suscripción y servicios de Azure generales [aquí](/azure/azure-resource-manager/management/azure-subscription-service-limits).

En la tabla siguiente se incluyen los límites de servicio de Azure AD que se deben tener en cuenta al configurar un entorno de prueba o ejecutar pruebas. 

| Category         | Límite   |
|-------------------|----------------|
| Inquilinos | Un usuario único puede crear un máximo de 200 directorios.|
| Recursos | <ul><li>De forma predeterminada, los usuarios de la edición Gratis de Azure Active Directory pueden crear un máximo de 50 000 recursos de Azure AD en un solo inquilino. Si tiene al menos un dominio comprobado, la cuota de servicio predeterminada de Azure AD se amplía a 300 000 recursos de Azure AD. La cuota de servicio de Azure AD para las organizaciones creadas mediante el registro en modalidad de autoservicio sigue siendo de 50 000 recursos de Azure AD, incluso después de que se haya tomado el control de la administración interna y la organización se haya convertido en un inquilino administrado con al menos un dominio verificado. Este límite de servicio no está relacionado con el límite del plan de tarifa de 500 000 recursos de la página de precios de Azure AD. Para superar la cuota predeterminada, debe ponerse en contacto con el servicio de soporte técnico de Microsoft.</li><li>Un usuario que no es administrador puede crear hasta 250 recursos de Azure AD. Tanto los recursos activos como los recursos eliminados que están disponibles para restaurar se contabilizan para esta cuota. Solo están disponibles para restaurar los recursos de Azure AD que se han eliminado hace menos de 30 días. Los recursos de Azure AD eliminados que ya no están disponibles para restaurar se contabilizan para esta cuota en un valor de un cuarto durante 30 días. Si tiene desarrolladores que probablemente superen repetidamente esta cuota en el transcurso de sus tareas normales, puede crear y asignar un rol personalizado con permiso para crear un número ilimitado de registros de aplicaciones.</li></ul>|
| APLICACIONES| <ul><li>Un usuario, grupo o entidad de servicio puede tener como máximo 1500 asignaciones de roles de aplicación.</li><li>Un usuario solo puede tener un máximo de 48 aplicaciones en las que tenga configuradas credenciales de nombre de usuario y contraseña.</li></ul>|
| Manifiesto de aplicación| Se puede agregar un máximo de 1200 entradas en el manifiesto de aplicación. |
| Grupos |  <ul><li>Un usuario no administrador puede crear un máximo de 250 grupos en una organización de Azure AD. Cualquier administrador de Azure AD que pueda administrar grupos en la organización también puede crear un número ilimitado de grupos (hasta el límite de objetos de Azure AD). Si asigna un rol para quitar el límite de un usuario, asígnelos a un rol integrado con menos privilegios, como Administrador de usuarios o Administrador de grupos.</li><li>Una organización de Azure AD puede tener un máximo de 5000 grupos dinámicos.</li><li>Se puede crear un máximo de 300 grupos a los que se puedan asignar roles en una sola organización de Azure AD (inquilino).</li><li>Cualquier número de recursos de Azure AD puede ser miembro de un solo grupo.</li><li>Un usuario puede ser un miembro de cualquier número de grupos.</li></ul>|
| Roles y permisos de Azure AD | <ul><li>En una organización de Azure AD se puede crear un máximo de 30 roles personalizados de Azure AD.</li><li>Un máximo de 100 asignaciones de roles personalizadas de Azure AD para una sola entidad de seguridad en el ámbito del inquilino.</li><li>Un máximo de 100 asignaciones de roles integradas de Azure AD para una sola entidad de seguridad en el ámbito no de inquilino (como unidad administrativa u objeto de Azure AD). No hay ningún límite para las asignaciones de roles integradas de Azure AD en el ámbito del inquilino.</li></ul>|


## <a name="throttling-limits-relevant-to-testing"></a>Límites pertinentes para las pruebas

Se aplican los siguientes límites de Microsoft Graph globales:

| Tipo de solicitud | Por aplicación en todos los inquilinos |
|-------------------|----------------|
| Tipo de solicitud | Por aplicación en todos los inquilinos |
| Any | 2000 solicitudes por segundo| 

En la tabla siguiente se incluyen los límites de Azure AD que se deben tener en cuenta al ejecutar pruebas. La limitación se basa en un algoritmo de depósito de tokens, que funciona agregando costos individuales de solicitudes. A continuación, la suma de los costos de solicitud se compara con los límites determinados previamente. Solo se limitarán las solicitudes que superen los límites. Para obtener información más detallada sobre los costos de solicitud, consulte [Límites de servicio de identidad y acceso](/graph/throttling#pattern). Puede encontrar otros límites específicos del servicio de Microsoft Graph [aquí](/graph/throttling#service-specific-limits).

| Tipo de límite | Cuota de unidad de recursos | Cuota de escritura |
|-------------------|----------------|----------------|
| par de aplicación+inquilino | S:3500, M:5000, L:8000 por cada 10 segundos | 3000 por cada 2 minutos y 30 segundos |
| application | 150 000 por cada 20 segundos | 70 000 por cada 5 minutos |
| tenant | No aplicable | 18 000 por cada 5 minutos |

El límite del par aplicación+inquilino varía en función del número de usuarios de las solicitudes de inquilino en las que se ejecutan. Los tamaños de inquilino se definen de la siguiente manera: S (menos de 50 usuarios), M (entre 50 y 500 usuarios) y L (más de 500 usuarios).

## <a name="what-happens-when-a-throttling-limit-is-exceeded"></a>¿Qué ocurre cuando se supera un límite? 
El comportamiento de la limitación puede depender del tipo y número de solicitudes. Por ejemplo, si tiene un volumen de solicitudes elevado, se limitan todos los tipos de solicitudes. Los límites varían en función del tipo de solicitud. Por tanto, podría encontrar un escenario en el que las operaciones de escritura están limitadas, pero todavía se permiten las de lectura.

Cuando se supera un límite, se recibe el código de estado HTTP `429 Too many requests` y se produce un error en la solicitud. En la respuesta se incluye el valor de encabezado `Retry-After`, que especifica el número de segundos que debe esperar (o estar en estado de suspensión) la aplicación antes de enviar la siguiente solicitud. Vuelva a intentarlo.  Si envía una solicitud antes de que haya transcurrido el tiempo especificado en el valor de reintento, la solicitud no se procesará y se devolverá un nuevo valor de reintento. Si se produce un error con la solicitud con un código de error 429, significa que todavía está limitado. Siga utilizando el retraso de `Retry-After` recomendado y vuelva a intentar la solicitud hasta que se realice correctamente.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la [Configuración de un entorno de prueba](test-setup-environment.md).

