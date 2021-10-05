---
title: Control del acceso a IoT Hub mediante Azure Active Directory
description: Guía del desarrollador En este artículo se explica cómo controlar el acceso a IoT Hub para aplicaciones de back-end mediante Azure AD y RBAC de Azure.
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: a8387a5732ab51add02495b03236b42f9cd4e671
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609448"
---
# <a name="control-access-to-iot-hub-by-using-azure-active-directory"></a>Control del acceso a IoT Hub mediante Azure Active Directory

Puede usar Azure Active Directory (Azure AD) para autenticar las solicitudes a las API del servicio Azure IoT Hub, como crear una identidad de dispositivo o invocar un método directo. También puede usar el control de acceso basado en roles de Azure (RBAC de Azure) para autorizar esas mismas API del servicio. Al usar conjuntamente estas tecnologías, puede conceder permisos para acceder a API del servicio IoT Hub a una entidad de seguridad de Azure AD. Esta entidad de seguridad podría ser un usuario, grupo o entidad de servicio de aplicación.

La autenticación del acceso con Azure AD y el control de permisos con RBAC de Azure proporcionan una mayor seguridad y facilidad de uso respecto a los [tokens de seguridad.](iot-hub-dev-guide-sas.md) Para minimizar posibles problemas de seguridad inherentes a los tokens de seguridad, recomendamos [usar Azure AD con su instancia de IoT Hub siempre que sea posible](#azure-ad-access-and-shared-access-policies). 

> [!NOTE]
> La autenticación con Azure AD no es compatible con las *API de dispositivo* de IoT Hub (como mensajes del dispositivo a la nube y la actualización de las propiedades notificadas). Use [claves simétricas](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry) o [X.509](iot-hub-x509ca-overview.md) para autenticar dispositivos en IoT Hub.

## <a name="authentication-and-authorization"></a>Autenticación y autorización

Cuando una entidad de seguridad de Azure AD solicita el acceso a una API del servicio IoT Hub, la identidad de la entidad de seguridad primero *se autentica*. Para realizar la autenticación, la solicitud debe contener un token de acceso de OAuth 2.0 en el entorno de ejecución. El nombre del recurso para solicitar el token es `https://iothubs.azure.net`. Si la aplicación se ejecuta en un recurso de Azure, como una máquina virtual de Azure, una aplicación de función de Azure Functions o una aplicación de Azure App Service, se puede representar como una [identidad administrada](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md). 

Una vez autenticada la entidad de Azure AD, el siguiente paso es la *autorización*. En este paso, IoT Hub usa el servicio de asignación de roles de Azure AD para determinar que permisos tiene la entidad de seguridad. Si los permisos de la entidad de seguridad coinciden con el recurso o la API solicitados, IoT Hub autoriza la solicitud. Así, este paso exige que se asignen uno o varios roles de Azure a la entidad de seguridad. IoT Hub proporciona algunos roles integrados que tienen grupos comunes de permisos.

## <a name="manage-access-to-iot-hub-by-using-azure-rbac-role-assignment"></a>Administración del acceso a IoT Hub mediante la asignación de roles de RBAC de Azure

Con Azure AD y RBAC, IoT Hub requiere que la entidad de seguridad que solicita la API tenga el nivel de permiso adecuado para la autorización. Para conceder permiso a la entidad de seguridad, asígnele también una asignación de roles. 

- Si la entidad de seguridad es un usuario, grupo o entidad de servicio de aplicación, siga las instrucciones de [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).
- Si la entidad de seguridad es una identidad administrada, siga las instrucciones de [Asignación de acceso de una identidad administrada a un recurso mediante Azure Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

Para garantizar los privilegios mínimos, asigne siempre el rol adecuado en el [ámbito de recursos](#resource-scope) más bajo posible,que es probable que sea el ámbito de IoT Hub.

Azure proporciona los siguientes roles integrados de Azure para autorizar el acceso a la API del servicio IoT Hub mediante Azure AD y RBAC:

| Rol | Descripción | 
| ---- | ----------- | 
| [Colaborador de datos de IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | Permite el acceso total a las operaciones del plano de datos de IoT Hub. |
| [Lector de datos de IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | Permite el acceso de lectura total a las propiedades del plano de datos de IoT Hub. |
| [Colaborador del registro de IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | Permite el acceso completo al registro de dispositivos de IoT Hub. |
| [Colaborador de gemelos de IoT Hub](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | Permite el acceso de lectura y escritura a todos los dispositivos y módulos gemelos de IoT Hub. |

También puede definir roles personalizados para su uso con IoT Hub mediante la combinación de los [permisos](#permissions-for-iot-hub-service-apis) que necesita. Para más información, consulte [Creación de roles personalizados para el control de acceso basado en roles de Azure](../role-based-access-control/custom-roles.md).

### <a name="resource-scope"></a>Ámbito de recursos

Antes de asignar un rol de Azure RBAC a una entidad de seguridad, determine el ámbito de acceso que debería tener la entidad de seguridad. Siempre es mejor conceder únicamente el ámbito más restringido posible. Los roles de Azure RBAC definidos en un ámbito más amplio los heredan los recursos que están debajo de ellos.

En la lista se describen los niveles en los que puede definir el ámbito de acceso a IoT Hub, empezando por el ámbito más restringido:

- **IoT Hub.** En este ámbito, se aplica una asignación de roles a IoT Hub. No hay ningún ámbito menor que un centro de IoT individual. No se admite la asignación de roles en ámbitos más pequeños, como la sección de la identidad del dispositivo individual o del gemelo.
- **El grupo de recursos.** En este ámbito, se aplica una asignación de roles a todos los centros de IoT del grupo de recursos.
- **La suscripción.** En este ámbito, se aplica una asignación de roles a todos los centros de IoT de todos los grupos de recursos de la suscripción.
- **Un grupo de administración.** En este ámbito, se aplica una asignación de roles a todos los centros de IoT de todos los grupos de recursos de todas las suscripciones del grupo de administración.

## <a name="permissions-for-iot-hub-service-apis"></a>Permisos para las API del servicio IoT Hub

En las tablas siguientes se describen los permisos disponibles para las API del servicio IoT Hub. Para permitir que un cliente llame a una operación determinada, asegúrese de que el rol RBAC asignado del cliente ofrezca permisos suficientes para esa operación.

| Acción de RBAC | Descripción |
|-|-|
| `Microsoft.Devices/IotHubs/devices/read` | Lee cualquier identidad de dispositivo o módulo. |
| `Microsoft.Devices/IotHubs/devices/write`  | Crea o actualiza cualquier identidad de dispositivo o módulo.  |
| `Microsoft.Devices/IotHubs/devices/delete` | Elimina cualquier identidad del dispositivo o módulo. |
| `Microsoft.Devices/IotHubs/twins/read` | Lee cualquier dispositivo o módulo gemelo. |
| `Microsoft.Devices/IotHubs/twins/write` | Escribe en cualquier dispositivo o módulo gemelo. |
| `Microsoft.Devices/IotHubs/jobs/read` | Obtiene una lista de trabajos. |
| `Microsoft.Devices/IotHubs/jobs/write` | Crea o actualiza cualquier trabajo. |
| `Microsoft.Devices/IotHubs/jobs/delete` | Elimina cualquier trabajo. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action` | Envía un mensaje de la nube al dispositivo a cualquier dispositivo.  |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action` | Recibe, completa o abandona la notificación de comentarios de mensajes de la nube al dispositivo. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action` | Elimina todos los comandos pendientes de un dispositivo.  |
| `Microsoft.Devices/IotHubs/directMethods/invoke/action` | Invoca un método directo en cualquier dispositivo o módulo. |
| `Microsoft.Devices/IotHubs/fileUpload/notifications/action`  | Recibe, completa o abandona notificaciones de carga de archivos. |
| `Microsoft.Devices/IotHubs/statistics/read` | Lee las estadísticas de dispositivos y servicios. |
| `Microsoft.Devices/IotHubs/configurations/read` | Lee las configuraciones de administración de dispositivos. |
| `Microsoft.Devices/IotHubs/configurations/write` | Crea o actualiza las configuraciones de administración de dispositivos. |
| `Microsoft.Devices/IotHubs/configurations/delete` | Elimina cualquier configuración de administración de dispositivos. |
| `Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action`  | Aplica el contenido de configuración a un dispositivo perimetral. |
| `Microsoft.Devices/IotHubs/configurations/testQueries/action` | Valida la condición de destino y las consultas de métricas personalizadas para una configuración. |

> [!TIP]
> - La operación de [actualización del Registro en masa](/rest/api/iothub/service/bulkregistry/updateregistry) requiere `Microsoft.Devices/IotHubs/devices/write` y `Microsoft.Devices/IotHubs/devices/delete`.
> - La operación de [consulta de gemelo](/rest/api/iothub/service/query/gettwins) requiere `Microsoft.Devices/IotHubs/twins/read`.
> - La operación de [obtención del gemelo digital](/rest/api/iothub/service/digitaltwin/getdigitaltwin) requiere `Microsoft.Devices/IotHubs/twins/read`. La operación de [actualización del gemelo digital](/rest/api/iothub/service/digitaltwin/updatedigitaltwin) requiere `Microsoft.Devices/IotHubs/twins/write`.
> - Para el [comando de invocar componente](/rest/api/iothub/service/digitaltwin/invokecomponentcommand) como el [comando invocar nivel de raíz](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand) requieren `Microsoft.Devices/IotHubs/directMethods/invoke/action`.

> [!NOTE]
> Para obtener datos de IoT Hub mediante Azure AD, [configure el enrutamiento a un centro de eventos independiente](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint). Para acceder al [punto de conexión compatible integrado de Event Hubs](iot-hub-devguide-messages-read-builtin.md), use el método de la cadena de conexión (clave de acceso compartido) como antes. 

## <a name="azure-ad-access-and-shared-access-policies"></a>Acceso de Azure AD y directivas de acceso compartido

De forma predeterminada, IoT Hub permite el acceso a la API de servicio a través de Azure AD, además de mediante [directivas de acceso compartido y tokens de seguridad](iot-hub-dev-guide-sas.md). Para minimizar posibles vulnerabilidades de seguridad asociadas a los tokens de seguridad, deshabilite el acceso con directivas de acceso compartido: 

1. Asegúrese de que los clientes y usuarios del servicio tengan el nivel de [acceso suficiente](#manage-access-to-iot-hub-by-using-azure-rbac-role-assignment) a IoT Hub. Para ello, aplique el [principio de acceso con privilegios mínimos](../security/fundamentals/identity-management-best-practices.md).
1. En [Azure Portal](https://portal.azure.com), vaya hasta su instancia de IoT Hub.
1. En el panel izquierdo, seleccione **Directivas de acceso compartido**.
1. En **Connect using shared access policies** (Conectar mediante directivas de acceso compartido),seleccione **Denegar**.
    :::image type="content" source="media/iot-hub-dev-guide-azure-ad-rbac/disable-local-auth.png" alt-text="Captura de pantalla que muestra cómo desactivar las directivas de acceso compartido de IoT Hub.":::
1. Revise la advertencia y seleccione **Guardar**.

Ahora solo se puede acceder a las API del servicio IoT Hub mediante Azure AD y RBAC.

## <a name="azure-ad-access-from-the-azure-portal"></a>Acceso de Azure AD desde Azure Portal

Cuando intenta acceder a IoT Hub, Azure Portal primero comprueba si se le ha asignado un rol de Azure con `Microsoft.Devices/iotHubs/listkeys/action`. Si es así, Azure Portal usará las claves de las directivas de acceso compartido para acceder a IoT Hub. En caso contrario, Azure Portal intentará acceder a los datos mediante su cuenta de Azure AD. 

Para acceder a IoT Hub desde Azure Portal con su cuenta de Azure AD, necesita permisos para acceder a los recursos de datos de IoT Hub (como los dispositivos y gemelos). También necesita permisos para acceder al recurso de IoT Hub en Azure Portal. Los roles integrados proporcionados por IoT Hub conceden acceso a recursos como dispositivos y gemelos, pero no conceden acceso al recurso de IoT Hub. Por lo tanto, el acceso al portal también requiere la asignación de un rol de Azure Resource Manager, como [Lector](../role-based-access-control/built-in-roles.md#reader). El rol Lector es una buena opción porque es el rol más restringido que le permite navegar por el portal. y no incluye el permiso `Microsoft.Devices/iotHubs/listkeys/action` (que proporciona acceso a todos los recursos de datos de IoT Hub a través de directivas de acceso compartido). 

Para asegurarse de que una cuenta no tiene acceso fuera de los permisos asignados, no incluya el permiso `Microsoft.Devices/iotHubs/listkeys/action` al crear un rol personalizado. Por ejemplo, para crear un rol personalizado que pueda leer identidades de dispositivo, pero que no pueda crear ni eliminar dispositivos, cree un rol personalizado que:
- Tenga la acción de datos `Microsoft.Devices/IotHubs/devices/read`.
- No tenga la acción de datos `Microsoft.Devices/IotHubs/devices/write`.
- No tenga la acción de datos `Microsoft.Devices/IotHubs/devices/delete`.
- No tenga la acción `Microsoft.Devices/iotHubs/listkeys/action`.

A continuación, asegúrese de que la cuenta no tiene ningún otro rol que tenga el permiso `Microsoft.Devices/iotHubs/listkeys/action`, como [Propietario](../role-based-access-control/built-in-roles.md#owner) o [Colaborador](../role-based-access-control/built-in-roles.md#contributor). Para permitir que la cuenta tenga acceso a los recursos y pueda navegar por el portal, asigne [Lector](../role-based-access-control/built-in-roles.md#reader).

## <a name="azure-iot-extension-for-azure-cli"></a>Extensión de Azure IoT para la CLI de Azure

La mayoría de comandos de IoT Hub admiten autenticación de Azure AD. Puede controlar el tipo de autenticación que se usa para ejecutar comandos mediante el parámetro `--auth-type`, que acepta los valores `key` o `login`. El valor predeterminado es `key`.

- Cuando `--auth-type` tiene el valor de `key`, como antes, la CLI detecta automáticamente una directiva adecuada al interactuar con IoT Hub.

- Cuando `--auth-type` tiene el valor `login`, se utiliza un token de acceso de la entidad de seguridad que ha iniciado sesión en la CLI de Azure para llevar a cabo la operación.

Para obtener más información, consulte la [página de versiones de la extensión de Azure IoT para la CLI de Azure](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12).

## <a name="sdk-samples"></a>Ejemplos del SDK

- [Ejemplo del SDK de .NET Microsoft.Azure.Devices](https://aka.ms/iothubaadcsharpsample)
- [Ejemplo de SDK de Java](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre las ventajas de usar Azure AD en la aplicación, vea [Integración con Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md).
- Para obtener más información sobre la solicitud de tokens de acceso de Azure AD para usuarios y entidades de servicio, consulte el artículo sobre [escenarios de autenticación de Azure AD](../active-directory/develop/authentication-vs-authorization.md).
