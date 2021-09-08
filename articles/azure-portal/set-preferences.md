---
title: Administración de las preferencias y la configuración de Azure Portal
description: Cambie la configuración de Azure Portal, como la suscripción o el directorio predeterminados, los tiempos de espera, el modo de menú, el contraste, el tema, las notificaciones, el idioma o la región, etc.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 285afaaf51f28b0fa53a9df2a9fa232b01d3fe24
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741278"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Administración de las preferencias y la configuración de Azure Portal

Puede cambiar la configuración predeterminada del Azure Portal para que se adapte a sus propias preferencias.

La mayoría de los valores están disponibles en el menú **Configuración** situado en la sección superior derecha del encabezado de página global.

:::image type="content" source="media/set-preferences/settings-top-header.png" alt-text="Captura de pantalla en la que se muestra el icono de configuración en el encabezado de página global.":::

> [!NOTE]
> Nos encontramos en proceso de trasladar todos los usuarios a la experiencia de configuración más reciente que se describe en este tema. Para obtener información sobre la experiencia anterior, vea [Administración de la configuración y las preferencias de Azure Portal (versión anterior)](original-preferences.md).

## <a name="directories--subscriptions"></a>Directorios y suscripciones

La página **Directorios y suscripciones** permite administrar directorios y establecer filtros de suscripción.

### <a name="switch-and-manage-directories"></a>Cambio y administración de directorios

En la sección **Directorios**, verá el **Directorio actual** (en el que ha iniciado sesión actualmente).

El **Directorio de inicio** muestra el directorio predeterminado al iniciar sesión en Azure Portal. Para elegir otro directorio de inicio, seleccione **Cambiar** para ir a la página [Apariencia y vistas de inicio](#appearance--startup-views), donde podrá cambiar esta opción.

Para ver una lista completa de los directorios a los que tiene acceso, seleccione **Todos los directorios**.

Para marcar un directorio como favorito, seleccione el icono de estrella. Esos directorios se mostrarán en la sección **Favoritos**.

Para cambiar a otro directorio, seleccione el directorio en el que quiere trabajar y, después, seleccione el botón **Cambiar** en su fila.

:::image type="content" source="media/set-preferences/settings-directories-subscriptions-default-filter.png" alt-text="Captura de pantalla en la que se muestra el panel de configuración de directorios.":::

### <a name="subscription-filters"></a>Filtros de suscripción

Puede elegir las suscripciones que se filtran de manera predeterminada cuando inicia sesión en Azure Portal. Esto puede resulta útil si tiene una lista de suscripciones principal con la que trabaja, pero utiliza otras de vez en cuando.

Para usar filtros personalizados, seleccione **Filtros avanzados**. Se le pedirá que confirme antes de continuar.

:::image type="content" source="media/set-preferences/settings-advanced-filters-enable.png" alt-text="Captura de pantalla en la que se muestra el cuadro de diálogo de confirmación de Filtros avanzados.":::

Esto habilitará la página **Filtros avanzados**, donde puede crear y administrar varios filtros de suscripción. Las suscripciones seleccionadas actualmente se guardarán como un filtro importado que puede usar otra vez. Si quiere dejar de usar filtros avanzados, vuelva a seleccionar el botón de alternancia para restaurar la vista de suscripción predeterminada. Los filtros personalizados que haya creado se guardarán y estarán disponibles para su uso si habilita **Filtros avanzados** en el futuro.

:::image type="content" source="media/set-preferences/settings-advanced-filters-disable.png" alt-text="Captura de pantalla en la que se muestra el cuadro de diálogo de confirmación para deshabilitar Filtros avanzados.":::

## <a name="advanced-filters"></a>Filtros avanzados

En la página **Filtros avanzados**, puede crear, modificar o eliminar filtros de suscripción.

:::image type="content" source="media/set-preferences/settings-advanced-filters.png" alt-text="Captura de pantalla en la que se muestra la pantalla Filtros avanzados":::.

El filtro **Predeterminado** muestra todas las suscripciones a las que tiene acceso. Este filtro se usa si no hay ningún otro o cuando el filtro activo no puede incluir suscripciones.

También puede ver un filtro denominado **Filtro importado**, que incluye todas las suscripciones que se habían seleccionado anteriormente.

Para cambiar el filtro que está actualmente en uso, selecciónelo en el cuadro desplegable **Filtro avanzado**. También puede seleccionar **Modificar filtros avanzados** para ir a la página **Filtros avanzados**, donde puede crear, modificar y eliminar los filtros.

### <a name="create-a-filter"></a>Crear un filtro

Para crear un filtro, seleccione **Crear un filtro**. Puede crear hasta diez filtros.

Cada filtro debe tener un nombre único que tenga entre 8 y 50 caracteres y contenga solo letras, números y guiones.

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create.png" alt-text="Captura de pantalla en la que se muestra las opciones de Crear un filtro":::.

Después de asignar un nombre al filtro, especifique al menos una condición. En el campo **Tipo de filtro**, seleccione **Nombre de suscripción**, **Id. de suscripción** o **Estado de la suscripción**. Después, seleccione un operador y escriba un valor por el que filtrar.

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create-operators.png" alt-text="Captura de pantalla en la que se muestra la lista de operadores para la creación de filtros.":::

Cuando haya terminado de agregar condiciones, seleccione **Crear**. Después, el filtro aparecerá en la lista en **Filtros activos**.

### <a name="modify-or-delete-a-filter"></a>Modificación o eliminación de un filtro

Puede modificar o cambiar el nombre de un filtro existente seleccionando el icono de lápiz de la fila de ese filtro. Realice los cambios y luego seleccione **Aplicar**.

> [!NOTE]
> Si modifica un filtro que está activo actualmente y los cambios dan como resultado 0 suscripciones, se activará el filtro **Predeterminado** en su lugar. No se puede activar un filtro que no incluya ninguna suscripción.

Para eliminar un filtro, seleccione el icono de papelera de la fila de ese filtro. No se puede eliminar el filtro **Predeterminado** ni ninguno que esté activo actualmente.

## <a name="appearance--startup-views"></a>Apariencia y vistas de inicio

El panel **Apariencia y vistas de inicio** tiene dos secciones. La sección **Apariencia** permite elegir el comportamiento del menú, el tema de color y si se debe usar un tema de contraste alto, y la sección **Startup views** (Vistas de inicio) le permite establecer opciones para lo que ve cuando inicia sesión por primera vez en Azure Portal.

:::image type="content" source="media/set-preferences/azure-portal-settings-appearance.png" alt-text="Captura de pantalla en la que se muestra la sección Apariencia del panel Apariencia y vistas de inicio.":::

### <a name="set-menu-behavior"></a>Establecimiento del comportamiento del menú

La sección **Comportamiento del menú** le permite elegir cómo se comporta el menú predeterminado de Azure Portal.

- **Control flotante**: el menú se ocultará hasta que lo necesite. Puede seleccionar el icono de menú en la esquina superior izquierda para abrirlo o cerrarlo.
- **Acoplado**: el menú siempre estará visible. Puede contraer el menú para proporcionar más espacio de trabajo.

### <a name="choose-a-theme-or-enable-high-contrast"></a>Selección de un tema o habilitación de contraste alto

El tema que elija afectará a los colores de fondo y de fuente que aparecen en el Azure Portal. Puede seleccionar uno de los cuatro temas de color preestablecidos en la sección **Tema**. Seleccione cada miniatura para buscar el tema que mejor se adapte a sus necesidades.

También puede elegir un tema de la sección **Tema de contraste alto**. Estos temas pueden hacer que Azure Portal sea más fácil de leer, especialmente si tiene una discapacidad visual. Si selecciona el tema de contraste alto blanco o negro, se invalidarán las demás selecciones de tema.

### <a name="startup-page"></a>Página de inicio

Elija una de las opciones siguientes para la página que verá cuando inicie sesión por primera vez en Azure Portal.

- **Inicio**: muestra la página principal, con accesos directos a servicios populares de Azure, una lista de recursos que ha usado más recientemente y vínculos útiles a herramientas, documentación y mucho más.
- **Panel**: muestra el panel usado más recientemente. Los paneles se pueden personalizar para crear un área de trabajo diseñada solo para usted. Por ejemplo, puede crear un panel que sea proyecto, tarea o rol centrado. Para obtener más información, consulte [Creación y uso compartido de paneles en Azure Portal](azure-portal-dashboards.md).

### <a name="startup-directory"></a>Directorio de inicio

Elija una de las opciones siguientes para que el directorio funcione cuando inicie sesión por primera vez en Azure Portal.

- **Iniciar sesión en el último directorio visitado**: al iniciar sesión en Azure Portal, comenzará en el directorio en el que haya trabajado la última vez.
- **Seleccionar un directorio**: elija esta opción para seleccionar uno de los directorios. Comenzará en ese directorio cada vez que inicie sesión en Azure Portal, incluso si la última vez ha trabajado en otro directorio.

:::image type="content" source="media/set-preferences/azure-portal-settings-startup-views.png" alt-text="Captura de pantalla en la que se muestra la sección Inicio del panel Apariencia y vistas de inicio.":::

## <a name="language--region"></a>Idioma y región

Elija el idioma y el formato regional que influirán en el modo en que los datos, como las fechas y la moneda, aparecerán en Azure Portal.

:::image type="content" source="media/set-preferences/azure-portal-settings-language-region.png" alt-text="Captura de pantalla en la que se muestra el panel de configuración de Idioma y región":::.

> [!NOTE]
> Esta configuración regional y de idioma solo afecta al Azure Portal. Los vínculos de documentación que se abren en una pestaña o ventana nueva usan la configuración del explorador para determinar el idioma que se va a mostrar.

### <a name="language"></a>Lenguaje

Use la lista desplegable para seleccionar uno de la lista de idiomas disponibles. Esta configuración controla el idioma en el que se ve el texto en Azure Portal.

### <a name="regional-format"></a>Formato regional

Seleccione una opción para controlar la forma en que se muestran las fechas, la hora, los números y la moneda en Azure Portal.

Las opciones que se muestran en la lista desplegable **Formato regional** cambian en función de la opción seleccionada para **Idioma**. Por ejemplo, si selecciona **Inglés** como idioma y luego **Inglés (Estados Unidos)** como el formato regional, la moneda se muestra en dólares estadounidenses. Si selecciona **Inglés** como idioma y luego **Inglés (Europa)** como el formato regional, se muestra la moneda en euros.

Seleccione **Aplicar** para actualizar la configuración de idioma y el formato regional.

## <a name="my-information"></a>Mi información

La página **Mi información** le permite actualizar la dirección de correo electrónico que se usa para las actualizaciones de los servicios, la facturación, el soporte técnico o los problemas de seguridad de Azure. También puede optar por recibir correos electrónicos adicionales sobre Microsoft Azure, y otros productos y servicios, o no recibirlos.

Cerca de la parte superior de la página **Mi información**, verá opciones para exportar, restaurar o eliminar la configuración.

:::image type="content" source="media/set-preferences/settings-my-information.png" alt-text="Captura de pantalla de la página de configuración de Mi información.":::

### <a name="export-user-settings"></a>Exportación de la configuración de usuario

La información sobre la configuración personalizada se almacena en Azure. Puede exportar los siguientes datos de usuario:

- Paneles privados de Azure Portal
- Configuración del usuario, como suscripciones o directorios favoritos
- Temas y otra configuración del portal personalizada

Es una buena idea exportar y revisar la configuración si tiene previsto eliminarla. Volver a generar paneles o rehacer la configuración puede llevar mucho tiempo.

Para exportar la configuración del portal, seleccione **Exportar configuración** en la parte superior del panel **Información general** de la configuración. Esto crea un archivo *.json* que contiene los datos de configuración del usuario.

Debido a la naturaleza dinámica de la configuración de usuario y el riesgo de daños en los datos, no se puede importar la configuración desde el archivo *.json*.

### <a name="restore-default-settings"></a>Restaurar la configuración predeterminada

Si ha realizado cambios en la configuración de Azure Portal y quiere descartarlos, seleccione **Restaurar la configuración predeterminada** en la parte superior del panel **Información general** de la configuración. Se le pedirá que confirme esta acción. Al hacerlo, se perderán los cambios que haya realizado en la configuración de Azure Portal. Esta opción no afecta a las personalizaciones del panel.

### <a name="delete-user-settings-and-dashboards"></a>Eliminación de la configuración de usuario y los paneles

La información sobre la configuración personalizada se almacena en Azure. Puede eliminar los siguientes datos de usuario:

- Paneles privados de Azure Portal
- Configuración del usuario, como suscripciones o directorios favoritos
- Temas y otra configuración del portal personalizada

Es una buena idea exportar y revisar la configuración antes de eliminarla. Volver a generar [paneles](azure-portal-dashboards.md) o rehacer la configuración personalizada puede llevar mucho tiempo.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Para eliminar la configuración del portal, seleccione **Eliminar todas las opciones de configuración y los paneles privados** en la parte superior de la página **Mi información**. Se le pedirá que confirme la eliminación. Al hacerlo, todas las personalizaciones de configuración volverán a los valores predeterminados y se perderán todos los paneles privados.

## <a name="signing-out--notifications"></a>Cierre de sesión y notificaciones

Este panel le permite administrar las notificaciones emergentes y los tiempos de espera de sesión.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-notifications.png" alt-text="Captura de pantalla en la que se muestra el panel Cierre de sesión y notificaciones":::.

### <a name="signing-out"></a>Cierre de sesión

La configuración de tiempo de espera de inactividad ayuda a proteger los recursos frente al acceso no autorizado si se olvida de proteger la estación de trabajo. Una vez que haya estado inactivo durante un tiempo, se cerrará automáticamente la sesión de Azure Portal. Como usuario individual, puede cambiar la configuración de tiempo de espera manualmente. Si es administrador, puede establecerlo en el nivel de directorio de todos los usuarios del directorio.

### <a name="change-your-individual-timeout-setting-user"></a>Cambio de la configuración de tiempo de espera individual (usuario)

En el menú desplegable situado junto a **Cerrar la sesión cuando no haya actividad**, elija la duración después de la cual se cerrará la sesión de Azure Portal si está inactivo.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive.png" alt-text="Captura de pantalla en la que se muestra la opción de configuración de tiempo de espera del usuario":::.

Seleccione **Aplicar** para guardar los cambios. Después de eso, si está inactivo durante la sesión del portal, Azure Portal cerrará la sesión cuando se supere la duración establecida.

Si el administrador ha habilitado una directiva de tiempo de espera de inactividad, puede establecer la suya propia, siempre que sea menor que la configuración de nivel de directorio. Seleccione **Reemplazar directiva de tiempo de espera de inactividad del directorio** y, después, establezca un intervalo de tiempo para el **Valor de invalidación**.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-user.png" alt-text="Captura de pantalla en la que se muestra la configuración de invalidación del tiempo de espera de inactividad del directorio":::.

### <a name="change-the-directory-timeout-setting-admin"></a>Cambio de la configuración de tiempo de espera del directorio (admin)

Los administradores del [rol Administrador global](../active-directory/roles/permissions-reference.md#global-administrator) pueden aplicar el tiempo máximo de inactividad antes de que se haya cerrado una sesión. Esta configuración de tiempo de espera de inactividad se aplica en el nivel de directorio. La configuración surte efecto para las nuevas sesiones. No se aplicará de forma inmediata a ningún usuario que ya haya iniciado sesión. Para más información sobre los directorios, vea [Introducción a Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Si es un Administrador global y quiere aplicar un valor de tiempo de espera de inactividad para todos los usuarios de Azure Portal, seleccione **Habilitar tiempo de espera de inactividad del nivel de directorio** para activar la configuración: Luego, escriba las **Horas** y los **Minutos** de tiempo máximo que un usuario puede estar inactivo antes de que su sesión se cierre de forma automática. Después de seleccionar **Aplicar**, esta configuración se aplicará a todos los usuarios del directorio.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-admin.png" alt-text="Captura de pantalla en la que se muestran las opciones de tiempo de espera de inactividad de nivel de directorio":::.

Para confirmar que la directiva de tiempo de espera de inactividad se ha establecido correctamente, seleccione **Notificaciones** en el encabezado de página global y compruebe que aparece una notificación correcta.

:::image type="content" source="media/set-preferences/confirmation.png" alt-text="Captura de pantalla en la que se muestra una notificación para la directiva de tiempo de espera de inactividad correcta.":::

### <a name="enable-or-disable-pop-up-notifications"></a>Habilitar o deshabilitar las notificaciones emergentes

Las notificaciones son mensajes del sistema relacionados con la sesión actual. Proporcionan información como, por ejemplo, mostrar el saldo de crédito actual, confirmar la última acción o permitirle saber cuándo se convierten los recursos que creó. Cuando se activan las notificaciones emergentes, los mensajes se muestran brevemente en la esquina superior de la pantalla.

Para habilitar las notificaciones emergentes, seleccione **Habilitar notificaciones emergentes** (o anule la selección para deshabilitarlas).

Para leer todas las notificaciones recibidas durante la sesión actual, seleccione **Notificaciones** en el encabezado global.

:::image type="content" source="media/set-preferences/read-notifications.png" alt-text="Captura de pantalla en la que se muestra el icono Notificaciones en el encabezado global.":::

Para ver las notificaciones de sesiones anteriores, busque eventos en el registro de actividad. Para más información, consulte [Visualización del registro de actividad](../azure-monitor/essentials/activity-log.md#view-the-activity-log).

## <a name="next-steps"></a>Pasos siguientes

- [Obtener más información sobre métodos abreviados de teclado de Azure Portal](azure-portal-keyboard-shortcuts.md)
- [Ver exploradores y dispositivos compatibles](azure-portal-supported-browsers-devices.md)
- [Agregar, quitar y reorganizar favoritos](azure-portal-add-remove-sort-favorites.md)
- [Creación y uso compartido de paneles personalizados](azure-portal-dashboards.md)
- [Ver vídeos de procedimientos de Azure Portal](azure-portal-video-series.md)
