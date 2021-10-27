---
title: 'Acceso público: Hiperescala (Citus) de Azure Database for PostgreSQL'
description: En este artículo se describe el acceso público para Hiperescala (Citus) de Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: cb695514fe4fd1b3d0ed72dd70aeb8b5d6ca4253
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072199"
---
# <a name="public-access-in-azure-database-for-postgresql---hyperscale-citus"></a>Acceso público en Hiperescala (Citus) de Azure Database for PostgreSQL

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

En esta página se describe la opción de acceso público. Para obtener información sobre el acceso privado, consulte [este documento](concepts-hyperscale-private-access.md).

## <a name="firewall-overview"></a>Información general de firewalls

El firewall del servidor Azure Database for PostgreSQL impide todo acceso al nodo de coordinación de Hiperescala (Citus) hasta que se especifica qué equipos tienen permiso. Asimismo, otorgan acceso al servidor según la dirección IP de origen de cada solicitud.
Para configurar su firewall, cree reglas de firewall que especifiquen intervalos de direcciones IP aceptables. Puede crear reglas de firewall a nivel de servidor.

**Regas de firewall:** Estas reglas permiten a los clientes acceder al nodo de coordinación de Hiperescala (Citus), es decir, a todas las bases de datos que se encuentren en el mismo servidor lógico. Las reglas de firewall de nivel de servidor pueden configurarse mediante Azure Portal. Para crear reglas de firewall en el nivel del servidor, debe ser propietario o colaborador de la suscripción.

El firewall bloquea de forma predeterminada el acceso de todas las bases de datos al nodo de coordinación. Para comenzar a usar el servidor desde otro equipo, debe especificar una o varias reglas de firewall a nivel de servidor para habilitar el acceso a su servidor. Use las reglas de firewall para especificar los intervalos de direcciones IP de Internet que se permitirán. La acción de las reglas de firewall no repercute sobre el acceso al propio sitio web de Azure Portal.
Los intentos de conexión desde Internet y Azure deben atravesar primero el firewall antes de poder alcanzar PostgreSQL Database, tal y como se muestra en el siguiente diagrama:

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="Ejemplo de flujo de cómo funciona el firewall":::

## <a name="connecting-from-the-internet-and-from-azure"></a>Conexión desde Internet y desde Azure

Un firewall del grupo de servidores de Hiperescala (Citus) controla quién puede conectarse al nodo de coordinación del grupo. El firewall determina el acceso mediante la consulta de una lista configurable de reglas. Cada regla es una dirección IP o un intervalo de direcciones que se permiten.

Cuando el firewall bloquea las conexiones, puede provocar errores de la aplicación. Cuando se usa el controlador JDBC de PostgreSQL, por ejemplo, se genera un error similar al siguiente:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

Consulte [Creación y administración de reglas de firewall](howto-hyperscale-manage-firewall-using-portal.md) para saber cómo se definen las reglas.

## <a name="troubleshooting-the-database-server-firewall"></a>Solución de problemas del firewall del servidor de la base de datos
Si al acceder a Microsoft Azure Database for PostgreSQL: Hiperescala (Citus), el servicio no se comporta según lo esperado, considere estos puntos:

* **Los cambios realizados en la lista de permitidos no han surtido efecto todavía:** puede haber un retraso de hasta cinco minutos hasta que los cambios en la configuración del firewall de Hyperscale (Citus) surtan efecto.

* **El usuario no está autorizado o se ha usado una contraseña incorrecta:** si un usuario no tiene permisos en el servidor o la contraseña usada es incorrecta, se denegará la conexión al servidor. La creación de una configuración de firewall solo ofrece a los clientes una oportunidad de intentar conectarse al servidor; cada cliente debe continuar ofreciendo las credenciales de seguridad necesarias.

Por ejemplo, mediante un cliente JDBC, puede aparecer el siguiente error.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: Error grave: error de autenticación de contraseña para el usuario "suNombreDeUsuario"

* **Dirección IP dinámica:** Si tiene una conexión a Internet con un direccionamiento IP dinámico y tiene problemas al acceder al firewall, pruebe una de las soluciones siguientes:

* Pida a su proveedor de acceso a Internet (ISP) el intervalo de direcciones IP asignado a los equipos cliente que acceden al nodo de coordinación de Hiperescala (Citus) y, luego, agréguelo como regla de firewall.

* Obtenga el direccionamiento IP estático en su lugar para los equipos cliente y luego agregue la dirección IP estática como regla de firewall.

## <a name="next-steps"></a>Pasos siguientes
Para leer artículos sobre cómo crear reglas de firewall de nivel de servidor y de base de datos, consulte:
* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](howto-hyperscale-manage-firewall-using-portal.md) (Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal)
