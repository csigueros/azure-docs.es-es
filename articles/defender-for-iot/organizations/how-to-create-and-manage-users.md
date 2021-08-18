---
title: Creación y administración de usuarios
description: Cree y administre usuarios de los sensores y de la consola de administración local. A los usuarios se les puede asignar el rol Administrador, Analista de seguridad o Usuario de solo lectura.
ms.date: 05/20/2021
ms.topic: article
ms.openlocfilehash: 256b97f08f687b444375135d0b809423b8a73796
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020754"
---
# <a name="about-defender-for-iot-console-users"></a>Acerca de los usuarios de la consola de Defender para IoT

En este artículo se describe cómo crear y administrar usuarios de los sensores y de la consola de administración local. Los roles de usuario incluyen Administrador, Analista de seguridad o Usuario de solo lectura. Cada rol está asociado a un intervalo de permisos para las herramientas del sensor o la consola de administración local. Los roles están diseñados para facilitar un acceso detallado y seguro a Azure Defender para IoT.

También hay características disponibles para realizar un seguimiento de la actividad del usuario y habilitar el inicio de sesión de Active Directory.

De forma predeterminada, los sensores y la consola de administración local se instalan con un usuario *cyberx y de soporte técnico*. Estos usuarios tienen acceso a herramientas avanzadas para la solución de problemas y la configuración. Los usuarios administradores deben iniciar sesión con estas credenciales de usuario, crear un usuario administrador y, luego, crear usuarios adicionales para los analistas de seguridad y los usuarios de solo lectura.

## <a name="role-based-permissions"></a>Permisos basados en roles
Están disponibles los siguientes roles de usuario:

- **Solo lectura**: los usuarios de solo lectura realizan tareas como la visualización de alertas y dispositivos en el mapa del dispositivo. Estos usuarios tienen acceso a las opciones que se muestran en **Navigation** (Navegación).

- **Analista de seguridad**: Los analistas de seguridad tienen permisos de usuario de solo lectura. También pueden realizar acciones en los dispositivos, confirmar alertas y usar las herramientas de investigación. Estos usuarios tienen acceso a las opciones que se muestran en **Navigation** (Navegación) y **Analysis** (Análisis).

- **Administrator**: los administradores tienen acceso a todas las herramientas, incluida la definición de configuraciones del sistema, la creación y administración de usuarios, etc. Estos usuarios tienen acceso a las opciones que se muestran en **Navigation** (Navegación), **Analysis** (Análisis) y **Administration** (Administración).

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Permisos basados en roles para las herramientas de la consola de administración local

En esta sección se describen los permisos disponibles para los administradores, los analistas de seguridad y los usuarios de solo lectura para la consola de administración local.  

| Permiso | Solo lectura | Analista de seguridad | Administrador |
|--|--|--|--|
| Ver y filtrar el mapa de la empresa | ✓ | ✓ | ✓ |
| Crear un sitio |  |  | ✓ |
| Administrar un sitio (agregar y editar zonas) |  |  | ✓ |
| Ver y filtrar el inventario de dispositivos | ✓ | ✓ | ✓ |
| Ver y administrar alertas: confirmación, aprendizaje y anclaje | ✓ | ✓ | ✓ |
| Generación de informes |  | ✓ | ✓ |
| Ver los informes de evaluación de riesgos |  | ✓ | ✓ |
| Establecer exclusiones de alertas |  | ✓ | ✓ |
| Ver o definir grupos de acceso |  |  | ✓ |
| Administrar la configuración del sistema |  |  | ✓ |
| Administrar usuarios |  |  | ✓ |
| Enviar datos de alertas a los asociados |  |  | ✓ |
| Administración de certificados |  |  | ✓ |
| Tiempo de espera de la sesión cuando los usuarios no están activos | 30 minutos | 30 minutos  | 30 minutos  |

#### <a name="assign-users-to-access-groups"></a>Asignación de usuarios a grupos de acceso

Los administradores pueden mejorar el control de acceso de los usuarios en Defender para IoT mediante la asignación de usuarios a *grupos de acceso* específicos. Los grupos de acceso se asignan a las zonas, los sitios, las regiones y las unidades de negocio donde se encuentra un sensor. Mediante la asignación de usuarios a grupos de acceso, los administradores logran un control específico sobre el lugar en el que los usuarios administran y analizan las detecciones de dispositivos. 

Al trabajar de esta manera, se admiten organizaciones de gran tamaño en las que los permisos de usuario pueden ser complejos o estar determinados por una directiva de seguridad global de la organización. Para más información, consulte [Definición del control de acceso global](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Permisos basados en roles para las herramientas del sensor

En esta sección se describen los permisos disponibles para los administradores del sensor, los analistas de seguridad y los usuarios de solo lectura.  

| Permiso | Solo lectura | Analista de seguridad | Administrador |
|--|--|--|--|
| Ver el panel | ✓ | ✓ | ✓ |
| Controlar las vistas del zoom del mapa |  |  | ✓ |
| Visualización de alertas | ✓ | ✓ | ✓ |
| Administrar alertas: confirmación, aprendizaje y anclaje |  | ✓ | ✓ |
| Ver los eventos de una escala de tiempo |  | ✓ | ✓ |
| Autorizar dispositivos, dispositivos de examen conocidos y dispositivos de programación |  | ✓ | ✓ |
| Ver datos de la investigación | ✓ | ✓ | ✓ |
| Administrar la configuración del sistema |  |  | ✓ |
| Administrar usuarios |  |  | ✓ |
| Servidores DNS para la búsqueda inversa |  |  | ✓ |
| Enviar datos de alertas a los asociados |  | ✓ | ✓ |
| Crear comentarios de alertas |  | ✓ | ✓ |
| Ver el historial de cambios de programación | ✓ | ✓ | ✓ |
| Crear reglas de alertas personalizadas |  | ✓ | ✓ |
| Administrar varias notificaciones simultáneamente |  | ✓ | ✓ |
| Administración de certificados |  |  | ✓ |
| Tiempo de espera de la sesión cuando los usuarios no están activos | 30 minutos | 30 minutos | 30 minutos |

## <a name="define-users"></a>Definición de usuarios

En esta sección se describe cómo definir usuarios. Los usuarios cyberx, de soporte técnico y administrador pueden agregar, eliminar y actualizar las definiciones de otros usuarios.

**Para definir un usuario**:

1. En el panel izquierdo del sensor o de la consola de administración local, seleccione **Users** (Usuarios).
1. En la ventana **Users** (Usuarios), seleccione **Create User** (Crear usuario).
1. En el panel **Create User** (Crear usuario), defina los parámetros siguientes:

   - **Nombre de usuario**: Especifique un nombre de usuario.
   - **Correo electrónico**: Escriba la dirección de correo electrónico del usuario.
   - **First name** (Nombre): escriba el nombre del usuario.
   - **Last name** (Apellidos): escriba los apellidos del usuario.
   - **Rol**: defina el rol del usuario. Consulte [Permisos basados en roles](#role-based-permissions).
   - **Access Group** (Grupo de acceso): si va a crear un usuario para la consola de administración local, defina el grupo de acceso del usuario. Consulte [Definición del control de acceso global](how-to-define-global-user-access-control.md).
   - **Contraseña**: seleccione el tipo de usuario de la siguiente manera:
     - **Usuario local**: defina una contraseña para el usuario de un sensor o de una consola de administración local. La contraseña debe tener al menos seis caracteres de longitud y debe incluir letras y números.
     - **Usuario de Active Directory**: puede permitir que los usuarios inicien sesión en el sensor o en la consola de administración mediante el uso de credenciales de Active Directory. Los grupos de Active Directory definidos se pueden asociar a niveles de permisos específicos. Por ejemplo, configure un grupo de Active Directory específico y asigne todos los usuarios del grupo al tipo de usuario de solo lectura.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Administrar los usuarios.":::

## <a name="user-session-timeout"></a>Tiempo de espera de la sesión de usuario

Si los usuarios no están activos en el teclado o el ratón durante un tiempo específico, se cerrará su sesión y deberán volver a iniciar sesión.

Cuando los usuarios no han utilizado el ratón o el teclado de la consola durante 30 minutos, se fuerza el cierre de sesión.

Esta característica está habilitada de forma predeterminada y en las actualizaciones, pero se puede deshabilitar. Además, los tiempos de recuento de sesiones se pueden actualizar. Los tiempos de sesión se definen en segundos. Las definiciones se aplican para cada sensor y consola de administración local.

Aparece un mensaje de tiempo de espera de sesión en la consola cuando se ha superado el tiempo de espera de inactividad.

### <a name="control-inactivity-sign-out"></a>Control del cierre de sesión por inactividad

Los usuarios administradores pueden habilitar y deshabilitar el cierre de sesión por inactividad y ajustar los umbrales de inactividad.

**Para acceder al comando**:

1. Inicie sesión en la CLI del sensor o la consola de administración local con las credenciales administrativas de Defender para IoT.

1. Escriba `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Para deshabilitar la característica, cambie `infinity_session_expiration = true` a `infinity_session_expiration = false`.

Para actualizar los períodos de recuento de cierre de sesión, ajuste el valor de `= <number>` al tiempo necesario.

## <a name="track-user-activity"></a>Seguimiento de la actividad del usuario 

Puede realizar un seguimiento de la actividad del usuario en la escala de tiempo de los eventos en cada sensor. La escala de tiempo muestra el evento o el dispositivo afectado y la fecha y la hora en la que el usuario realizó la actividad.

**Para ver la actividad del usuario**:

1. Inicie sesión en el sensor.
1. En la escala de tiempo del evento, habilite la opción **User Operations** (Operaciones del usuario). 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Ver la actividad de un usuario.":::

## <a name="integrate-with-active-directory-servers"></a>Integración con servidores de Active Directory 

Configure el sensor o la consola de administración local para trabajar con Active Directory. Esto permite a los usuarios de Active Directory acceder a las consolas de Defender para IoT con sus credenciales de Active Directory.

Se admiten dos tipos de autenticación basada en LDAP:

- **Autenticación completa**: los detalles del usuario se recuperan del servidor LDAP. Algunos ejemplos son el nombre, los apellidos, el correo electrónico y los permisos del usuario.

- **Usuario de confianza**: solo se recupera la contraseña del usuario. Otros detalles del usuario que se recuperan se basan en los usuarios definidos en el sensor.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Permisos de Active Directory y Defender para IoT

Puede asociar los grupos de Active Directory definidos aquí con niveles de permisos específicos. Por ejemplo, configure un grupo de Active Directory específico y asigne permisos de solo lectura a todos los usuarios del grupo.

**Para configurar Active Directory**:

1. En el panel izquierdo, seleccione **System Settings** (Configuración del sistema).

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Ver la configuración del sistema de Active Directory.":::

2. En el panel **System Settings** (Configuración del sistema), seleccione **Active Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Editar las configuraciones de Active Directory.":::

3. En el cuadro de diálogo **Edit Active Directory Configuration** (Editar configuración de Active Directory), seleccione **Active Directory Integration Enabled** > **Save** (Integración de Active Directory habilitada > Guardar). El cuadro de diálogo **Edit Active Directory Configuration** (Editar configuración de Active Directory) se expande y ahora puede escribir los parámetros para configurar Active Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Escribir los parámetros para configurar Active Directory.":::

    > [!NOTE]
    > - Debe definir aquí los parámetros de LDAP exactamente como aparecen en Active Directory.
    > - Utilice únicamente minúsculas para todos los parámetros de Active Directory. Utilice minúsculas incluso cuando las configuraciones de Active Directory usen mayúsculas.
    > - No puede configurar LDAP y LDAPS para el mismo dominio. Sin embargo, puede usar ambos para dominios diferentes al mismo tiempo.

4. Establezca los parámetros del servidor de Active Directory como se indica a continuación:

   | Parámetros de servidor | Descripción |
   |--|--|
   | Nombre de dominio completo del controlador de dominio | Establezca el nombre de dominio completo (FQDN) exactamente como aparece en el servidor LDAP. Por ejemplo, escriba `host1.subdomain.domain.com`. |
   | Puerto del controlador de dominio | Defina el puerto en el que está configurado el servidor LDAP. |
   | Dominio principal | Establezca el nombre de dominio (por ejemplo, `subdomain.domain.com`) y el tipo de conexión según la configuración de LDAP. |
   | Grupos de Active Directory | Escriba los nombres de grupo que se han definido en la configuración de Active Directory en el servidor LDAP. |
   | Dominios de confianza | Para agregar un dominio de confianza, agregue el nombre de dominio y el tipo de conexión de un dominio de confianza. <br />Solo puede configurar dominios de confianza para los usuarios que se definieron en la sección de usuarios. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>Grupos de Active Directory para la consola de administración local

Si va a crear grupos de Active Directory para los usuarios de la consola de administración local, debe crear una regla de grupo de acceso para cada grupo de Active Directory. Las credenciales de Active Directory de la consola de administración local no funcionarán si no existe una regla de grupo de acceso para el grupo de usuarios de Active Directory. Consulte [Definición del control de acceso global](how-to-define-global-user-access-control.md).

1. Seleccione **Guardar**.

2. Para agregar un servidor de confianza, seleccione **Add Server** (Agregar servidor) y configure otro servidor.

## <a name="change-a-users-password"></a>Cambia la contraseña de un usuario

El administrador puede cambiar la contraseña del rol de analista de seguridad y de solo lectura. El usuario del rol Administrador no puede cambiar su propia contraseña y debe ponerse en contacto con un rol de nivel superior. Los roles de analista de seguridad y de solo lectura no pueden restablecer sus contraseñas ni las de ningún otro rol. Los roles de analista de seguridad y de solo lectura deben ponerse en contacto con un usuario con un nivel de rol superior para restablecer sus contraseñas. El rol CyberX puede cambiar la contraseña de todos los roles de usuario. El rol de soporte técnico puede cambiar la contraseña de un rol de usuario de soporte técnico, administrador, analista de seguridad y de solo lectura.  

**Para restablecer la contraseña de un usuario en el sensor**:

1. Inicie sesión en el sensor con un usuario con el rol de administrador, soporte técnico o CyberX.

1. Seleccione **Usuarios** en el panel izquierdo.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Selección de la opción de usuario en el panel izquierdo.":::

1. Busque el usuario y seleccione **Editar** en el menú desplegable **Acciones**.

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="Selección de Editar en el menú desplegable Acciones.":::

1. Escriba la nueva contraseña en los campos **Nueva contraseña** y **Confirmar la nueva contraseña**.

    > [!NOTE]
    > Las contraseñas deben tener al menos 16 caracteres, contener caracteres alfabéticos en minúsculas y mayúsculas, números y uno de los símbolos siguientes: #%*+,-./:=?@[]^_{}~

1. Seleccione **Actualizar**.

**Para restablecer la contraseña de un usuario en la consola de administración local**:

1. Inicie sesión en la consola de administración local con un usuario con el rol administrador, soporte técnico o CyberX.

1. Seleccione **Usuarios** en el panel izquierdo.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="Selección en el panel izquierdo de la opción del usuario.":::

1. Busque el usuario y seleccione el icono de edición :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false":::.

1. Escriba la nueva contraseña en los campos **Nueva contraseña** y **Confirmar la nueva contraseña**.

    > [!NOTE]
    > Las contraseñas deben tener al menos 16 caracteres, contener caracteres alfabéticos en minúsculas y mayúsculas, números y uno de los símbolos: #%*+,-./:=?@[]^_{}~

1. Seleccione **Actualizar**.

## <a name="recover-the-password-for-the-on-premises-management-console-or-the-sensor"></a>Recuperación de la contraseña de la consola de administración local o el sensor

Puede recuperar la contraseña de la consola de administración local o el sensor con la característica de recuperación de contraseñas. Solo el usuario de CyberX y de soporte técnico tiene acceso a la característica de recuperación de contraseñas.

**Para recuperar la contraseña de la consola de administración local o el sensor**:

1. En la pantalla de inicio de sesión de la consola de administración local o del sensor, seleccione **Recuperación de contraseña**. Se abre la pantalla **Recuperación de contraseña**.

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery.png" alt-text="Seleccione la recuperación de contraseñas en la pantalla de inicio de sesión de la consola de administración local o del sensor.":::

1. Seleccione **CyberX** o **Soporte técnico** en el menú desplegable y copie el código de identificador único.

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery-screen.png" alt-text="Seleccione el usuario de Cyberx o el usuario de soporte técnico en el menú desplegable.":::

1. Vaya a Azure Portal y seleccione **Sites and Sensors** (Sitios y sensores).  

1. Seleccione el icono **Filtro de suscripciones** :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false"::: en la barra de herramientas superior y elija la suscripción a la que está conectado el sensor.

1. Seleccione el menú desplegable **Más acciones** y seleccione **Recover on-premises management console password** (Recuperar contraseña de la consola de administración local).

    :::image type="content" source="media/how-to-create-and-manage-users/recover-password.png" alt-text="Seleccione el sensor y seleccione la opción de recuperación de la contraseña de la consola de administración local.":::   

1. Escriba el identificador único que recibió en la pantalla **Recuperación de contraseña** y seleccione **Recuperar**. Se descarga el archivo `password_recovery.zip`.

    :::image type="content" source="media/how-to-create-and-manage-users/enter-identifier.png" alt-text="Escriba el identificador único y luego seleccione Recuperar.":::

    > [!NOTE]
    > No modifique el archivo de recuperación de contraseña. Es un archivo firmado y no funcionará si se altera.

1. En la pantalla Recuperación de contraseña, seleccione **Cargar**. Se abrirá la ventana **Cargar archivo de recuperación de contraseña**.

1. Seleccione **Examinar** para buscar el archivo `password_recovery.zip`, o arrástrelo a la ventana.

    > [!NOTE]
    > Puede aparecer un mensaje de error que indica que el archivo no es válido. Para corregirlo, asegúrese de que ha seleccionado la suscripción correcta antes de descargar `password_recovery.zip` y vuelva a descargarlo.  

1. Seleccione **Siguiente**; aparecerán el usuario y la contraseña generada por el sistema para la consola de administración.

## <a name="next-steps"></a>Pasos siguientes

[Activación y configuración del sensor](how-to-activate-and-set-up-your-sensor.md)
[Activación y configuración de la consola de administración local](how-to-activate-and-set-up-your-on-premises-management-console.md)
[Seguimiento de la actividad del sensor](how-to-track-sensor-activity.md)
