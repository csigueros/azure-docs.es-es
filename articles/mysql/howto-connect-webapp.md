---
title: 'Conexión con Azure App Service: Azure Database for MySQL'
description: Instrucciones sobre cómo conectar correctamente un servicio existente de Azure App Service a Azure Database para MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: e2a5f48cabaa118421444cc70e69d60e89de7ff2
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652237"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Conexión de un servicio existente de Azure App Service a un servidor de Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
En este tema se explica cómo conectar un servicio existente de Azure App Service a un servidor de Azure Database for MySQL.

## <a name="before-you-begin"></a>Antes de empezar
Inicie sesión en [Azure Portal](https://portal.azure.com). Cree de un servidor de Azure Database for MySQL. Para obtener más información, consulte [Creación de un servidor de Azure Database for MySQL con Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) o [Creación de un servidor de Azure Database for MySQL con la CLI de Azure](quickstart-create-mysql-server-database-using-azure-cli.md).

Actualmente hay dos soluciones para habilitar el acceso desde un servicio de Azure App Service a un servidor de Azure Database for MySQL. En ambas soluciones hay que configurar las reglas de firewall de nivel de servidor.

## <a name="solution-1---allow-azure-services"></a>Solución 1: permitir los servicios de Azure
Azure Database for MySQL proporciona seguridad de acceso mediante un firewall para proteger los datos. Al conectarse desde un servicio de Azure App Service a un servidor de Azure Database for MySQL server, tenga en cuenta que las direcciones IP de salida de App Service son de naturaleza dinámica. Si elige la opción "Permitir el acceso a servicios de Azure", permite que App Service se conecte al servidor de MySQL.

1. En la hoja del servidor de MySQL, en el encabezado Configuración, haga clic en **Seguridad de conexión** para abrir la hoja Seguridad de conexión para Azure Database for MySQL.

   :::image type="content" source="./media/howto-connect-webapp/1-connection-security.png" alt-text="Azure Portal: haga clic en Seguridad de conexión":::

2. Seleccione **ON** en **Permitir el acceso a servicios de Azure** y luego **Guardar**.
   :::image type="content" source="./media/howto-connect-webapp/allow-azure.png" alt-text="Azure Portal: permitir el acceso a Azure":::

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Solución 2: crear una regla de firewall para permitir explícitamente las direcciones IP de salida
Puede agregar explícitamente todas las IP de salida de su Azure App Service.

1. En la hoja de propiedades de App Service, consulte su **DIRECCIÓN IP DE SALIDA**.

   :::image type="content" source="./media/howto-connect-webapp/2_1-outbound-ip-address.png" alt-text="Azure Portal: ver direcciones IP de salida":::

2. En la hoja de Seguridad de conexión de MySQL, agregue las direcciones IP de salida una por una.

   :::image type="content" source="./media/howto-connect-webapp/2_2-add-explicit-ips.png" alt-text="Azure Portal: agregar direcciones IP explícitas":::

3. No olvide **Guardar** las reglas de firewall.

Aunque el Azure App Service intenta que no se modifiquen las direcciones IP con el tiempo, hay casos en los que pueden cambiar las direcciones IP. Por ejemplo, esto se produce cuando se recicla la aplicación o cuando se realiza una operación de escala, o cuando se añaden nuevos equipos en los centros de datos regionales de Azure para aumentar la capacidad. Cuando cambian las direcciones IP, la aplicación podría experimentar un tiempo de inactividad en caso de que ya no pueda conectarse al servidor MySQL. Tenga esto en cuenta al elegir una de las soluciones anteriores.

## <a name="ssl-configuration"></a>Configuración de SSL
Azure Database for MySQL tiene SSL habilitado de forma predeterminada. Si la aplicación no usa SSL para conectarse a la base de datos, debe deshabilitar SSL en el servidor MySQL. Para más información sobre cómo configurar SSL, consulte [Uso de SSL con Azure Database for MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre las cadenas de conexión, consulte [Cadenas de conexión](howto-connection-string.md).
