---
title: Administración de usuarios y roles en una aplicación de Azure IoT Central | Microsoft Docs
description: Como administrador, aquí se indica la forma de administrar usuarios y roles en su aplicación de Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 08/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 4398ab5ed46276c397e812cb4ffbcd1e6e1296e6
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997578"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Administrar usuarios y roles en la aplicación de IoT Central

En este artículo se describe cómo puede agregar, editar y eliminar usuarios en la aplicación de Azure IoT Central. También se describe cómo administrar roles en la aplicación.

Para acceder a la sección **Administración** y poder usarla, debe disponer del rol **Administrador de aplicación** para una aplicación de Azure IoT Central o de un rol personalizado que incluye permisos de administración. Si crea una aplicación de Azure IoT Central, se le asigna automáticamente el rol **Administrator de aplicación** para esa aplicación.

## <a name="add-users"></a>Agregar usuarios

Cada usuario debe tener una cuenta de usuario antes de poder iniciar sesión y acceder a la aplicación. IoT Central admite actualmente cuentas de Microsoft y cuentas de Azure Active Directory, pero no grupos de Azure Active Directory.

Para más información, consulte la [ayuda de la cuenta de Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) y la [Guía de inicio rápido: Adición de nuevos usuarios a Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Para agregar un usuario a una aplicación de IoT Central, vaya a la página **Usuarios** de la sección **Administración**.

    :::image type="content" source="media/howto-manage-users-roles/manage-users-pnp.png" alt-text="Captura de pantalla de Administrar usuarios.":::

1. Para agregar un usuario, en la página **Usuarios**, seleccione **+ Asignar usuario**.

1. Si la aplicación usa [organizaciones](howto-create-organizations.md), elija una organización para asignarla al usuario en el menú desplegable **Organización**.

1. Seleccione un rol para el usuario en el menú desplegable **Rol**. Más información sobre los roles en la sección [Administración de roles](#manage-roles) de este artículo.

    :::image type="content" source="media/howto-manage-users-roles/add-user-pnp.png" alt-text="Captura de pantalla para agregar un usuario y seleccionar un rol.":::

    Los roles disponibles dependen de la organización a la que está asociado el usuario. Puede asignar roles de **aplicación** a usuarios asociados a la organización raíz y roles de **organización** a usuarios asociados a cualquier otra organización de la jerarquía.

    > [!NOTE]
    > Un usuario que tenga un rol personalizado que le conceda permisos para agregar otros usuarios solo puede agregar usuarios a un rol que tenga los mismos permisos que el suyo o unos inferiores.
  
    > [!NOTE]
    > Si se elimina un usuario de Azure Active Directory y, después, se vuelve a agregar, el usuario no podrá iniciar sesión en la aplicación IoT Central. Para volver a habilitar el acceso, el administrador de la aplicación debe eliminar al usuario y en la aplicación y volver a agregarlo también.

### <a name="edit-the-roles-and-organizations-that-are-assigned-to-users"></a>Edición de los roles y las organizaciones que se asignan a los usuarios

Los roles y las organizaciones no se pueden cambiar una vez asignados. Para cambiar el rol o la organización asignada a un usuario, elimine el usuario y agréguelo de nuevo con un rol u organización diferente.

> [!NOTE]
> Los roles asignados son específicos de la aplicación de IoT Central y no se pueden administrar desde Azure Portal.

## <a name="delete-users"></a>Eliminación de usuarios

Para eliminar usuarios, seleccione una o más casillas en la página **Usuarios**. A continuación, seleccione **Eliminar**.

## <a name="manage-roles"></a>Administrar roles

Los roles le permiten controlar qué usuarios de la organización tienen permiso para realizar varias tareas en IoT Central. Hay tres roles integrados que se pueden asignar a los usuarios de la aplicación. También puede [crear roles personalizados](#create-a-custom-role) si necesita un control más preciso.

:::image type="content" source="media/howto-manage-users-roles/manage-roles-pnp.png" alt-text="Captura de pantalla para administrar la selección de roles.":::

### <a name="app-administrator"></a>Administrador de aplicaciones

Los usuarios del rol **Administrador de aplicación** pueden administrar y controlar todas las partes de la aplicación, incluida la facturación.

El usuario que crea una aplicación se asigna automáticamente al rol **Administrador de aplicación**. Siempre debe haber al menos un usuario en el rol **Administrador de aplicación**.

### <a name="app-builder"></a>Generador de aplicaciones

Los usuarios del rol **Generador de aplicaciones** pueden administrar todas las partes de la aplicación, pero no pueden realizar cambios en las pestañas Administración o Exportación de datos continua.

### <a name="app-operator"></a>Operador de aplicaciones

Los usuarios de rol **Operador de aplicaciones** pueden supervisar el estado y el mantenimiento del dispositivo. No se les permite realizar cambios en las plantillas de dispositivo ni administrar la aplicación. Esto significa que los operadores pueden agregar y eliminar dispositivos, administrar conjuntos de dispositivos y ejecutar análisis y trabajos.

### <a name="org-administrator"></a>Administrador de organización

IoT Central agrega este rol automáticamente al agregar una organización a la aplicación. Este rol restringe a los administradores de la organización el acceso a algunas funcionalidades de toda la aplicación, como facturación, personalización de marca, colores, tokens de API e información del grupo de inscripción.

Los usuarios con el rol de **Administrador de la organización** pueden invitar usuarios a la aplicación, crear suborganizaciones dentro de su jerarquía organizativa y administrar los dispositivos dentro de su organización.

### <a name="org-operator"></a>Operador de organización

IoT Central agrega este rol automáticamente al agregar una organización a la aplicación. Este rol impide que los operadores de la organización accedan a algunas funcionalidades de toda la aplicación.

Los usuarios del rol **Operador de organización** pueden completar tareas como agregar dispositivos, ejecutar comandos, ver datos del dispositivo, crear paneles y crear grupos de dispositivos.

### <a name="org-viewer"></a>Visor de organización

IoT Central agrega este rol automáticamente al agregar una organización a la aplicación.

Los usuarios con el rol **Visor de organización** pueden ver elementos como dispositivos y sus datos, paneles de la organización, grupos de dispositivos y plantillas de dispositivo.

## <a name="create-a-custom-role"></a>Crear un rol personalizado

Si la solución requiere controles de acceso más precisos, puede crear roles con conjuntos de permisos personalizados. Para crear un rol personalizado, vaya a la página **Roles** de la sección **Administración** de la aplicación y elija una de estas opciones:

- Seleccione **+ Nuevo**, agregue un nombre y una descripción para el rol y seleccione **Aplicación** u **Organización** como tipo de rol. Esta opción le permite crear una definición de rol desde cero.
- Vaya a un rol existente y seleccione **Copiar**. Esta opción le permite empezar con una definición de rol existente que puede personalizar.

:::image type="content" source="media/howto-manage-users-roles/create-custom-role-pnp.png" alt-text="Captura de pantalla para crear un rol personalizado.":::

> [!WARNING]
> No se puede cambiar el tipo de rol después de crearlo.

Cuando invita a un usuario a la aplicación, si asocia el usuario a:

- la organización raíz, entonces, solo están disponibles los roles de **Aplicación**.
- cualquier otra organización, entonces, solo están disponibles los roles de **Aplicación**.

Puede agregar usuarios al rol personalizado de la misma manera que agrega usuarios a un rol integrado

### <a name="custom-role-options"></a>Opciones de roles personalizados

Al definir un rol personalizado, elige el conjunto de permisos que se concede a un usuario si es miembro del rol. Algunos permisos dependen de otros. Por ejemplo, si agrega a un rol el permiso **Update personal dashboards** (Actualizar paneles personales), se agrega automáticamente el permiso **View personal dashboards** (Ver paneles personales). En las tablas siguientes se resumen los permisos disponibles (y sus dependencias), que puede usar al crear roles personalizados.

#### <a name="managing-devices"></a>Administración de dispositivos

**Permisos de la plantilla de dispositivo**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Administrar | Ver <br/> Otras dependencias: Ver instancias de dispositivo  |
| Control total | Ver, administrar <br/> Otras dependencias: Ver instancias de dispositivo |

**Permisos de instancia de dispositivo**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None <br/> Otras dependencias: Ver plantillas de dispositivo y grupos de dispositivos |
| Actualizar | Ver <br/> Otras dependencias: Ver plantillas de dispositivo y grupos de dispositivos  |
| Crear | Ver <br/> Otras dependencias:  Ver plantillas de dispositivo y grupos de dispositivos  |
| Eliminar | Ver <br/> Otras dependencias: Ver plantillas de dispositivo y grupos de dispositivos  |
| Ejecutar comandos | Actualizar, ver <br/> Otras dependencias: Ver plantillas de dispositivo y grupos de dispositivos  |
| Visualización de datos sin procesar | Ver <br/> Otras dependencias: Ver plantillas de dispositivo y grupos de dispositivos  |
| Control total | Ver, actualizar, crear, eliminar, ejecutar comandos, ver datos sin procesar <br/> Otras dependencias: Ver plantillas de dispositivo y grupos de dispositivos  |

**Permisos de grupos de dispositivos**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None <br/> Otras dependencias: Ver plantillas de dispositivo e instancias de dispositivo |
| Actualizar | Ver <br/> Otras dependencias: Ver plantillas de dispositivo e instancias de dispositivo   |
| Crear | Ver, actualizar <br/> Otras dependencias:  Ver plantillas de dispositivo e instancias de dispositivo   |
| Eliminar | Ver <br/> Otras dependencias:  Ver plantillas de dispositivo e instancias de dispositivo  |
| Control total | Ver, actualizar, crear, eliminar <br/> Otras dependencias: Ver plantillas de dispositivo e instancias de dispositivo |

**Permisos de administración de conectividad de dispositivos**

| Nombre | Dependencias |
| ---- | -------- |
| Leer instancias | None <br/> Otras dependencias: Ver plantillas de dispositivo, grupos de dispositivos e instancias de dispositivo |
| Administrar instancia | Leer instancias <br /> Otras dependencias: Ver plantillas de dispositivo, grupos de dispositivos e instancias de dispositivo |
| Lectura global | None   |
| Administración global | Lectura global |
| Control total | Leer instancias, administrar instancias, lectura global, administración global <br/> Otras dependencias: Ver plantillas de dispositivo, grupos de dispositivos e instancias de dispositivo |

**Permisos de trabajos**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None <br/> Otras dependencias: Ver plantillas de dispositivo, instancias de dispositivo y grupos de dispositivos |
| Actualizar | Ver <br/> Otras dependencias: Ver plantillas de dispositivo, instancias de dispositivo y grupos de dispositivos |
| Crear | Ver, actualizar <br/> Otras dependencias:  Ver plantillas de dispositivo, instancias de dispositivo y grupos de dispositivos |
| Eliminar | Ver <br/> Otras dependencias:  Ver plantillas de dispositivo, instancias de dispositivo y grupos de dispositivos |
| Execute | Ver <br/> Otras dependencias: Ver plantillas de dispositivo, instancias de dispositivo y los grupos de dispositivos; actualizar instancias de dispositivo; ejecutar comandos en instancias de dispositivo |
| Control total | Ver, actualizar, crear, eliminar, ejecutar <br/> Otras dependencias:  Ver plantillas de dispositivo, instancias de dispositivo y los grupos de dispositivos; actualizar instancias de dispositivo; ejecutar comandos en instancias de dispositivo |

**Permisos de reglas**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None <br/> Otras dependencias: Ver plantillas de dispositivo |
| Actualizar | Ver <br/> Otras dependencias: Ver plantillas de dispositivo |
| Crear | Ver, actualizar <br/> Otras dependencias:  Ver plantillas de dispositivo |
| Eliminar | Ver <br/> Otras dependencias: Ver plantillas de dispositivo |
| Control total | Ver, actualizar, crear, eliminar <br/> Otras dependencias: Ver plantillas de dispositivo |

#### <a name="managing-the-app"></a>Administración de la aplicación

**Permisos de configuración de la aplicación**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Actualizar | Ver   |
| Copiar | Ver <br/> Otras dependencias: Ver plantillas de dispositivo, instancias de dispositivo, grupos de dispositivos, paneles, exportación de datos, personalización de marca, vínculos de ayuda, roles personalizados, reglas |
| Eliminar | Ver   |
| Control total | Ver, actualizar, copiar, eliminar <br/> Otras dependencias: Ver plantillas de dispositivo, grupos de dispositivos, paneles de la aplicación, exportación de datos, personalización de marca, vínculos de ayuda, roles personalizados, reglas |

**Permisos de exportación de plantillas de aplicación**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Exportación | Ver <br/> Otras dependencias:  Ver plantillas de dispositivo, instancias de dispositivo, grupos de dispositivos, paneles, exportación de datos, personalización de marca, vínculos de ayuda, roles personalizados, reglas |
| Control total | Ver, exportar <br/> Otras dependencias:  Ver plantillas de dispositivo, grupos de dispositivos, paneles de la aplicación, exportación de datos, personalización de marca, vínculos de ayuda, roles personalizados, reglas |

**Permisos de carga de archivos de dispositivo**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Administrar | Ver   |
| Control total | Ver, administrar |

**Permisos de facturación**

| Nombre | Dependencias |
| ---- | -------- |
| Administrar | None     |
| Control total | Administrar |

#### <a name="managing-users-and-roles"></a>Administración de usuarios y roles

**Permisos de roles personalizados**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None |
| Actualizar | Ver |
| Crear | Ver, actualizar |
| Eliminar | Ver |
| Control total | Ver, actualizar, crear, eliminar |

**Permisos de administración de usuarios**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None <br/> Otras dependencias: Ver roles personalizados |
| Sumar | Ver <br/> Otras dependencias:  Ver roles personalizados |
| Eliminar | Ver <br/> Otras dependencias:  Ver roles personalizados |
| Control total | Ver, agregar, eliminar <br/> Otras dependencias:  Ver roles personalizados |

**Permisos de administración de la organización**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None |
| Actualizar | Ver |
| Crear | Ver, actualizar |
| Eliminar | Ver |
| Control total | Ver, actualizar, crear, eliminar |

> [!NOTE]
> Un usuario que tenga un rol personalizado que le conceda permisos para agregar otros usuarios solo puede agregar usuarios a un rol que tenga los mismos permisos que el suyo o unos inferiores.

#### <a name="customizing-the-app"></a>Personalización de la aplicación

**Permisos de paneles de la aplicación**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Actualizar | Ver   |
| Crear | Ver, actualizar |
| Eliminar | Ver   |
| Control total | Ver, actualizar, crear, eliminar |

**Permisos de paneles personales**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Actualizar | Ver   |
| Crear | Ver, actualizar   |
| Eliminar | Ver   |
| Control total | Ver, actualizar, crear, eliminar |

**Permisos de personalización de marca, iconos de favoritos y colores**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Actualizar | Ver   |
| Control total | Ver, actualizar |

**Permisos de vínculos de ayuda**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Actualizar | Ver   |
| Control total | Ver, actualizar |

#### <a name="extending-the-app"></a>Extensión de la aplicación

**Permisos de exportación de datos**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None     |
| Actualizar | Ver   |
| Crear | Ver, actualizar  |
| Eliminar | Ver   |
| Control total | Ver, actualizar, crear, eliminar |

**Permisos de token de API**

| Nombre | Dependencias |
| ---- | -------- |
| Ver | None  <br/> Otras dependencias: Ver roles personalizados |
| Crear | Ver <br/> Otras dependencias: Ver roles personalizados |
| Eliminar | Ver <br/> Otras dependencias: Ver roles personalizados |
| Control total | Ver, crear, eliminar <br/> Otras dependencias: Ver roles personalizados |

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar usuarios y roles en la aplicación de Azure IoT Central, el siguiente paso sugerido es aprender a [personalizar la interfaz de usuario de la aplicación](howto-customize-ui.md).
