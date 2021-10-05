---
title: Administración de organizaciones de IoT Central | Microsoft Docs
description: En este artículo se explica cómo crear y administrar la jerarquía de una organización para controlar qué usuarios pueden acceder a qué dispositivos de la organización de IoT Central. Use organizaciones para crear aplicaciones multiinquilino de IoT Central.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.openlocfilehash: e15e2b095cd6b6876728ff773a7b4617e325d77d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656766"
---
# <a name="manage-iot-central-organizations"></a>Administración de organizaciones de IoT Central

Las organizaciones permiten definir una jerarquía que se usa para administrar qué usuarios pueden ver qué dispositivos de la aplicación de IoT Central. El rol del usuario determina sus permisos en los dispositivos que ven y las experiencias a las que pueden acceder.

Las organizaciones son jerárquicas:

:::image type="content" source="media/howto-create-organization/organizations-hierarchy.png" alt-text="Diagrama que muestra una jerarquía de ejemplo de organizaciones." border="false":::

Al crear la primera jerarquía de una organización, se ve que el nombre de la aplicación está en la raíz. Cada organización que se agrega se convierte en una organización secundaria u hoja. Las jerarquías de una organización usan la herencia. Por ejemplo, en la jerarquía del diagrama anterior:

- Los usuarios de *Adatum Solar* tienen acceso a los dispositivos de *Estados Unidos*, *Canadá*, *Europa* y *América Latina*.
- Los usuarios de *Contoso* tienen acceso a todos los dispositivos de todas las organizaciones de la jerarquía.

Los usuarios de organizaciones del mismo nivel no pueden ver los dispositivos de los demás.

Cuando se proporciona a los usuarios acceso a la aplicación, cuanto más arriba en la jerarquía se les asigne acceso, más pueden ver y administrar. La organización controla los dispositivos que el usuario puede ver. El rol define lo que un usuario puede hacer, como crear, leer y eliminar dispositivos.

En la captura de pantalla siguiente se muestra la definición de jerarquía de una organización en IoT Central:

:::image type="content" source="media/howto-create-organization/organizations-definition.png" alt-text="Captura de pantalla de la definición de jerarquía de organizaciones.":::

## <a name="create-a-hierarchy"></a>Creación de una jerarquía

Para empezar a usar organizaciones, debe definir la jerarquía de la organización. Cada organización de la jerarquía actúa como un contenedor lógico donde se colocan dispositivos, se guardan paneles y grupos de dispositivos y se invita a usuarios. Para crear las organizaciones, vaya a la sección **Administración** de la aplicación de IoT Central, seleccione la pestaña **Organizaciones** y luego seleccione **+ Nuevo** o use el menú contextual de una organización existente. Para crear una o varias organizaciones a la vez, seleccione **+ Agregar otra organización**:

:::image type="content" source="media/howto-create-organization/create-organizations-hierarchy.png" alt-text="Captura de pantalla que muestra las opciones para crear una jerarquía de organización.":::

> [!TIP]
> La configuración inicial de las organizaciones debe realizarla un miembro del rol **Administrador de aplicaciones**.

Para reasignar una organización a un nuevo elemento primario, seleccione **Editar** y elija el nuevo elemento primario.

Para eliminar una organización, debe eliminar o mover a otra organización los elementos asociados, como paneles, dispositivos, usuarios, grupos de dispositivos y trabajos.

> [!TIP]
> También puede usar la API REST para [crear y administrar organizaciones](/rest/api/iotcentral/1.1-previewdataplane/organizations).

## <a name="assign-devices"></a>Asignación de dispositivos

Después de definir la jerarquía de la organización, asigne los dispositivos a organizaciones. Cada dispositivo pertenece únicamente a una sola organización, así que elija la adecuada en la jerarquía.

Cuando cree un nuevo dispositivo en la aplicación, asígnelo a una organización de la jerarquía:

:::image type="content" source="media/howto-create-organization/assign-device.png" alt-text="Captura de pantalla que muestra cómo asignar un dispositivo a una organización.":::

Para asignar o reasignar un dispositivo existente a una organización, seleccione el dispositivo en la lista de dispositivos y luego seleccione **Organización**:

:::image type="content" source="media/howto-create-organization/change-device-organization.png" alt-text="Captura de pantalla que muestra cómo cambiar la organización a la que está asociado un dispositivo.":::

> [!TIP]
> Puede ver a qué organización pertenece un dispositivo en la lista de dispositivos. Use la herramienta de filtro de la lista de dispositivos para mostrar los dispositivos de una organización determinada.

Al reasignar un dispositivo a otra organización, los datos del dispositivo permanecen con la organización en función del tiempo de ingesta de datos. Por ejemplo:

- Un dispositivo forma parte de la organización **Contoso/Customer1** de los días uno a siete, luego se mueve a la organización **Contoso/Customer4** el día ocho.
- El día nueve, los usuarios de **Contoso/Customer4** pueden ver datos de los días ocho y nueve.
- El día nueve, los usuarios de **Contoso/Customer1** pueden ver datos de los días uno a siete.

### <a name="device-first-registration"></a>Registro de un dispositivo por primera vez

Los dispositivos pueden registrarse automáticamente en la aplicación de IoT Central sin necesidad de agregarse primero a la lista de dispositivos. En este caso, IoT Central agrega el dispositivo a la organización raíz de la jerarquía. Luego se puede reasignar el dispositivo a otra organización.

En lugar de esto, puede usar la característica de importación de CSV para registrar dispositivos de forma masiva en la aplicación y asignarlos a organizaciones. Para obtener más información, vea [Importación de dispositivos](howto-manage-devices.md#import-devices).

### <a name="gateways"></a>Puertas de enlace

Asigne puertas de enlace y dispositivos hoja a las organizaciones. No tiene que asignar una puerta de enlace y sus dispositivos hoja asociados a la misma organización. Si los asigna a diferentes organizaciones, es posible que un usuario pueda ver la puerta de enlace pero no los dispositivos hoja, o los dispositivos hoja pero no la puerta de enlace.

## <a name="roles"></a>Roles

Al crear la primera organización de la aplicación, IoT Central agrega tres nuevos roles a la aplicación: **Administrador de organización**,**Operador de organización** y **Visor de organización**. Estos roles son necesarios, ya que un usuario de la organización no puede acceder a determinadas capacidades de toda la aplicación, como planes de precios, personalización de marca y colores, tokens de API e información de grupos de inscripción de toda la aplicación.

:::image type="content" source="media/howto-create-organization/organization-roles.png" alt-text="Captura de pantalla que muestra los tres roles de la organización.":::

Puede usar estos roles cuando invite a usuarios a una organización de la aplicación.

### <a name="custom-organization-roles"></a>Roles de organización personalizados

Para crear un rol personalizado para los usuarios de la organización, cree un nuevo rol y elija el tipo de rol **Organización**:

:::image type="content" source="media/howto-create-organization/custom-organization-role.png" alt-text="Captura de pantalla que muestra cómo crear un rol de organización personalizado.":::

Luego seleccione los permisos del rol:

:::image type="content" source="media/howto-create-organization/organization-role-permissions.png" alt-text="Captura de pantalla que muestra cómo seleccionar permisos personalizados para un rol organizativo.":::

## <a name="invite-users"></a>Invitar a usuarios

Después de crear la jerarquía de la organización y de asignar dispositivos a organizaciones, invite a usuarios a la aplicación y asígnelos a organizaciones.

Para invitar a un usuario, vaya a  **Administración > Usuarios**. Escriba la dirección de correo electrónico, la organización a la que está asignado y los roles de los que es miembro el usuario. La organización que seleccione filtra la lista de roles disponibles para asegurarse de que se asigne al usuario a un rol válido:

:::image type="content" source="media/howto-create-organization/assign-user-organization.png" alt-text="Captura de pantalla que muestra cómo asignar un usuario a una organización y un rol.":::

Puede asignar el mismo usuario a varias organizaciones. El usuario puede tener un rol diferente en cada organización a la que esté asignado:

| Nombre | Role | Organización |
| ---- | ---- | ------------ |
| user1@contoso.com | Administrador de organización | Contoso Inc/Lamna Health |
| user1@contoso.com | Visor de organización | Contoso Inc/Adatum Solar |

## <a name="use-organizations"></a>Uso de organizaciones

Después de crear la jerarquía de la organización, puede usar organizaciones en áreas de la aplicación, como:

- [Paneles de la organización](howto-manage-dashboards.md) que muestran información a los usuarios sobre los dispositivos de su organización.
- [Grupos de dispositivos](tutorial-use-device-groups.md) de dispositivos de organizaciones concretas.
- [Análisis](howto-create-analytics.md) de dispositivos de organizaciones concretas.
- [Trabajos](howto-manage-devices-in-bulk.md#create-and-run-a-job) que administran de forma masiva dispositivos de organizaciones concretas.

## <a name="default-organization"></a>Organización predeterminada

Puede establecer una organización como predeterminada para usarse en la aplicación. La organización predeterminada se convierte en la opción predeterminada cada vez que se elige una organización, por ejemplo, cuando se agrega un nuevo usuario a la aplicación de IoT Central.

Para establecer la organización predeterminada, seleccione **Configuración** en la barra de menús superior:

:::image type="content" source="media/howto-create-organization/set-default-organization.png" alt-text="Captura de pantalla que muestra cómo establecer la organización predeterminada.":::

> [!TIP]
> Se trata de una preferencia personal que solo se aplica a usted.

## <a name="add-organizations-to-an-existing-application"></a>Incorporación de organizaciones a una aplicación existente

Una aplicación puede contener dispositivos, usuarios y experiencias, como paneles, grupos de dispositivos y trabajos, antes de agregar una jerarquía de organización.

Al empezar a agregar organizaciones, todos los dispositivos, usuarios y experiencias existentes permanecen asociados a la organización raíz de la aplicación:

- Puede reasignar dispositivos a una nueva organización.
- Puede asignar usuarios a una nueva organización y desasignarlos de la raíz.
- Puede volver a crear experiencias, como paneles, grupos de dispositivos y trabajos, y asociarlas a organizaciones de la jerarquía.

## <a name="limits"></a>Límites

Los límites siguientes se aplican a las organizaciones:

- La jerarquía no puede tener más de cinco niveles de profundidad.
- El número total de organizaciones no puede ser superior a 200. Cada nodo de la jerarquía cuenta como una organización.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar organizaciones de Azure IoT Central, el siguiente paso sugerido es aprender a [exportar datos de IoT a destinos en la nube mediante la exportación de datos](howto-export-data.md).
