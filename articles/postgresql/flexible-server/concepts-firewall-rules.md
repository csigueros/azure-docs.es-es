---
title: 'Reglas de firewall en Azure Database for PostgreSQL: servidor flexible'
description: 'En este artículo se describe cómo usar reglas de firewall para conectarse a Azure Database for PostgreSQL: servidor flexible con la opción de implementación de red pública.'
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: bdda8163bacd596eafab2a9b2d10d914012a3efb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866873"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---flexible-server"></a>Reglas de firewall en Azure Database for PostgreSQL: servidor flexible
Cuando se ejecuta Azure Database for PostgreSQL: servidor flexible, tiene dos opciones de red principales. Las opciones son acceso privado (integración con red virtual) y acceso público (direcciones IP permitidas). 

Con el acceso público, se accede al servidor de Azure Database for PostgreSQL desde un punto de conexión público. De forma predeterminada, el firewall bloquea todo el acceso al servidor. Para especificar qué hosts IP pueden acceder al servidor, cree *reglas de firewall* de nivel de servidor. Las reglas de firewall especifican los intervalos de direcciones IP públicas permitidas. Asimismo, otorgan acceso al servidor según la dirección IP de origen de cada solicitud.

Puede crear reglas de firewall mediante Azure Portal o comandos de la CLI de Azure. Debe ser propietario o colaborador de la suscripción.

Las reglas de firewall de nivel de servidor se aplican a todas las bases de datos del mismo servidor de Azure Database for PostgreSQL. Las reglas no afectan al acceso al sitio web de Azure Portal.

En el diagrama siguiente se muestra cómo los intentos de conexión desde Internet y Azure deben atravesar primero el firewall antes de poder llegar a la bases de datos PostgreSQL:

:::image type="content" source="../media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Diagrama en el que se muestra información general sobre cómo funciona el firewall.":::

## <a name="connect-from-the-internet"></a>Conexión desde Internet
Si la dirección IP de origen de la solicitud está comprendida en uno de los intervalos especificados en las reglas de firewall de nivel de servidor, la conexión se concede. De lo contrario, se rechaza. 

Por ejemplo, si la aplicación se conecta con un controlador Java Database Connectivity (JDBC) para PostgreSQL, es posible que vea este error al intentar conectarse cuando el firewall bloquea la conexión:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: Error grave: no hay entrada pg\_hba.conf para el host "123.45.67.890", usuario "adminuser", base de datos "postgresql", SSL

## <a name="connect-from-azure"></a>Conexión desde Azure
Se recomienda encontrar la dirección IP saliente de cualquier aplicación o servicio, y permitir explícitamente el acceso a las direcciones IP individuales o a los rangos. Por ejemplo, puede buscar la dirección IP saliente de una aplicación de Azure App Service, o bien usar una IP pública asociada a una máquina virtual. 

Si no hay ninguna dirección IP saliente fija disponible para el servicio de Azure, considere la posibilidad de habilitar las conexiones desde todas las direcciones IP de los centros de datos de Azure:

1. En Azure Portal, en el panel **Redes**, active la casilla **Allow public access from any Azure service within Azure to this server** (Permitir acceso público a este servidor desde cualquier servicio de Azure dentro de Azure). 
1. Seleccione **Guardar**. 

> [!IMPORTANT]
> La opción **Allow public access from any Azure service within Azure to this server** (Permitir acceso público a este servidor desde cualquier servicio de Azure dentro de Azure) configura el firewall para permitir todas las conexiones desde Azure, incluidas las que provienen de suscripciones de otros clientes. Al usar esta opción, asegúrese de que los permisos de usuario y el inicio de sesión solo dejan acceder a los usuarios autorizados. 

:::image type="content" source="./media/concepts-firewall-rules/allow-public-access.png" alt-text="Captura de pantalla en la que se muestran las selecciones para permitir el acceso a los servicios de Azure en el portal.":::

## <a name="programmatically-manage-firewall-rules"></a>Administración de reglas de firewall mediante programación
Además de usar Azure Portal, puede administrar las reglas de firewall mediante programación con la CLI de Azure. 

Desde la CLI de Azure, una configuración de regla de firewall con una dirección inicial y final igual a 0.0.0.0 hace lo mismo que la opción del portal **Allow public access from any Azure service within Azure to this server** (Permitir el acceso público desde cualquier servicio de Azure dentro de Azure a este servidor). Si las reglas de firewall rechazan el intento de conexión, la aplicación no accederá al servidor de Azure Database for PostgreSQL.

## <a name="troubleshoot-firewall-problems"></a>Solución de problemas de firewall
Tenga en cuenta las posibilidades siguientes cuando el acceso un servidor de Azure Database for PostgreSQL no se comporte según lo previsto:

* **Los cambios en la lista de permitidos aún no han tenido efecto**: los cambios en la configuración del firewall de un servidor de Azure Database for PostgreSQL pueden tardar hasta cinco minutos.

* **El inicio de sesión no está autorizado o se ha usado una contraseña incorrecta**: si un inicio de sesión no tiene permisos en el servidor de Azure Database for PostgreSQL o la contraseña usada es incorrecta, se denegará la conexión al servidor. La creación de una configuración de firewall solo ofrece a los clientes una oportunidad para intentar conectarse al servidor. Cada cliente todavía tiene que proporcionar las credenciales de seguridad necesarias.

  Por ejemplo, es posible que aparezca el siguiente error si se produce un error de autenticación para un cliente JDBC:

  > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: Error grave: error de autenticación de contraseña para el usuario "suNombreDeUsuario"

* **El firewall no permite direcciones IP dinámicas**: si tiene una conexión a Internet con direcciones IP dinámicas y tiene problemas para acceder por el firewall, puede probar una de las siguientes soluciones:

  * Pida a su proveedor de acceso a Internet (ISP) el intervalo de direcciones IP asignado a los equipos cliente que acceden al servidor de Azure Database for PostgreSQL. A continuación, agréguelo como una regla de firewall.

  * Obtenga direcciones IP estáticas en su lugar para los equipos cliente y luego agréguelas como regla de firewall.

* **Las reglas de firewall no están disponibles en formato IPv6**: las reglas de firewall deben estar en formato IPv4. Si especifica reglas de firewall en formato IPv6, recibirá un error de validación.


## <a name="next-steps"></a>Pasos siguientes

* [Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante Azure Portal](how-to-manage-firewall-portal.md)
* [Creación y administración de reglas de firewall de Azure Database for PostgreSQL mediante la CLI de Azure](how-to-manage-firewall-cli.md)
