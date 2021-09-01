---
title: 'Novedades de Azure Database for MySQL: servidor único'
description: 'Conozca las actualizaciones recientes de Azure Database for MySQL: servidor único, un servicio en la nube de base de datos relacional de Microsoft basado en MySQL Community Edition.'
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: b324c6b536ae5038ed9eb1d31025d6e88cf21011
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781063"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Novedades de Azure Database for MySQL: servidor único

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL es un servicio en la nube de base de datos relacional de Microsoft. El servicio se basa en el motor de base de datos de [MySQL Community Edition](https://www.mysql.com/products/community/) (disponible con la licencia GPLv2) y admite las versiones 5.6, 5.7 y 8.0. [Azure Database for MySQL: servidor único](./overview.md#azure-database-for-mysql---single-server) es un modo de implementación que proporciona un servicio de base de datos totalmente administrado con unos requisitos mínimos para las personalizaciones de base de datos. La plataforma de servidor único está diseñada para administrar la mayoría de las funciones de administración de bases de datos, como aplicación de revisiones, copias de seguridad, alta disponibilidad y seguridad, todo ello sin que el usuario tenga que intervenir apenas en la configuración y el control.

En este artículo se resumen las nuevas versiones y características de Azure Database for MySQL: servidor único a partir de enero de 2021. Las listas aparecen en orden cronológico inverso, con las actualizaciones más recientes en primer lugar.

## <a name="june-2021"></a>Junio de 2021
  
Esta versión de Azure Database for MySQL: servidor único incluye las siguientes actualizaciones.

- **Se ha habilitado la posibilidad de cambiar el parámetro `activate_all_roles_on_login` del servidor desde el portal o la CLI para MySQL 8.0**

  Los usuarios ahora pueden cambiar el valor del parámetro activate_all_roles_on_login mediante Azure Portal y la CLI. Este parámetro ayuda a configurar si se habilita la activación automática de todos los roles concedidos cuando los usuarios inician sesión en el servidor. Para más información, consulte [Variables del sistema del servidor](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html).

- **Se han solucionado los errores #29596969 y #94668 de MySQL Community**

  Esta versión soluciona un problema con la expresión predeterminada que se omite en una consulta CREATE TABLE si el campo se ha marcado como PRIMARY KEY para MySQL 8.0. (Errores #29596969 y #94668 de MySQL Community). Para más información, consulte [Error de MySQL: #94668: La expresión predeterminada se convierte en NULL durante la consulta CREATE TABLE si el campo es PK](https://bugs.mysql.com/bug.php?id=94668)

- **Se ha corregido un problema con nombres de tabla duplicados en la consulta "SHOW TABLE"** .

  Hemos introducido una nueva función para proporcionar un control preciso de la caché de tablas durante la operación de tabla. Debido a un defecto de código de la nueva característica, la entrada de la caché de directorios podría estar configurada incorrectamente o agregarse y provocar un comportamiento inesperado, como devolver dos tablas con el mismo nombre. La caché de directorios solo funciona para la consulta relacionada "SHOW TABLE"; no afectará a las consultas DML o DDL. Este problema se ha corregido por completo en esta versión.

- **Se ha aumentado el valor predeterminado del parámetro `max_heap_table_size` de servidor para ayudar a reducir los volcados de tablas temporales en el disco**.

  Con esta versión, el valor máximo permitido para el parámetro `max_heap_table_size` se ha cambiado a 8589934592 para De uso general con 64 núcleos virtuales y Optimizado para memoria con 32 núcleos virtuales.

- **Se ha corregido un problema al establecer el valor del parámetro `sql_require_primary_key` en el portal**.

  Los usuarios ahora pueden modificar el valor del parámetro `sql_require_primary_key` directamente desde Azure Portal.

- **Disponibilidad general de la notificación de mantenimiento planeado**

  Esta versión proporciona disponibilidad general de las notificaciones de mantenimiento planeado en Azure Database for MySQL: servidor único. Para más información, consulte el artículo [Notificación de mantenimiento planeado](concepts-planned-maintenance-notification.md).

- **Se ha habilitado el parámetro `redirect_enabled` de forma predeterminada**

  Con esta versión, el parámetro `redirect_enabled` se habilitará de forma predeterminada. El redireccionamiento pretende reducir la latencia de red entre las aplicaciones cliente y los servidores MySQL al permitir que las aplicaciones se conecten directamente a los nodos del servidor back-end. La compatibilidad con el redireccionamiento en aplicaciones PHP está disponible a través de la extensión [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure), desarrollada por Microsoft. Para más información, consulte el artículo [Conexión a Azure Database for MySQL con redireccionamiento](howto-redirection.md).

>[!Note]
> * El redireccionamiento no funciona con la configuración de Private Link. Si usa Private link para Azure Database for MySQL, es posible que se encuentre con un problema de conexión. Para resolver el problema, asegúrese de que el parámetro redirect_enabled esté establecido en "OFF" y de que se reinicie la aplicación cliente.</br>
> * Si tiene una aplicación PHP que usa el controlador de redireccionamiento de mysqlnd_azure para conectarse a Azure Database for MySQL (con el redireccionamiento habilitado de forma predeterminada), podría encontrarse con un problema de codificación de datos que afectara a las transacciones de inserción.</br>
> Para resolver este problema:
>    - En Azure Portal, deshabilite el redireccionamiento estableciendo el parámetro redirect_enabled en "OFF" y reinicie la aplicación PHP para borrar la caché del controlador después del cambio.
>     - Establezca explícitamente los parámetros relacionados con el juego de caracteres en el nivel de sesión, en función de la configuración después de establecer la conexión (por ejemplo, "set names utf8mb4").

## <a name="february-2021"></a>Febrero de 2021

Esta versión de Azure Database for MySQL: servidor único incluye las siguientes actualizaciones.

- Se han agregado nuevos procedimientos almacenados para admitir el identificador de transacción global (GTID) para la entrada de datos de la versión 5.7 y 8.0 del servidor de almacenamiento a gran escala.
- Se ha actualizado para admitir las versiones de MySQL 5.6.50 y 5.7.32.

## <a name="january-2021"></a>Enero de 2021

Esta versión de Azure Database for MySQL: servidor único incluye las siguientes actualizaciones.

- Se ha habilitado el "restablecimiento de contraseña" para corregir automáticamente el primer permiso de administrador.
- Se ha expuesto el parámetro de servidor `auto_increment_increment/auto_increment_offset` y `session_track_gtids`.
- Se han agregado nuevos procedimientos almacenados para controlar el volcado o la restauración del grupo de búferes innodb.
- Se ha expuesto el parámetro de servidor relacionado con la preparación de innodb para un servidor de almacenamiento a gran escala.

## <a name="contacts"></a>Contactos

Si tiene alguna pregunta o sugerencia con respecto al uso de Azure Database for MySQL, póngase en contacto con el equipo de Azure Database for MySQL ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Esta dirección de correo electrónico no es un alias de soporte técnico.

Además, tenga en cuenta los siguientes puntos de contacto según corresponda:

- Para ponerse en contacto con el servicio de soporte técnico de Azure, [presente una incidencia de soporte técnico en Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corregir un problema con la cuenta, envíe una [solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en Azure Portal.
- Para proporcionar comentarios o solicitar nuevas características, cree una entrada mediante [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre los [precios de Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/server/).
- Examine la [documentación pública](./single-server/index.yml) de Azure Database for MySQL: servidor único.
- Revise los detalles sobre la [solución de errores comunes](./howto-troubleshoot-common-errors.md).