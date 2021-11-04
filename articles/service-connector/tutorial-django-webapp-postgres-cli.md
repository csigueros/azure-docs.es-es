---
title: 'Tutorial: Uso de Service Connector para compilar una aplicación de Django con Postgres en Azure App Service'
description: Cree una aplicación web de Python con una base de datos de PostgreSQL e impleméntela en Azure. En el tutorial se usa el marco Django, la aplicación se hospeda en Azure App Service en Linux y el servicio y la base de datos de aplicaciones se conectan con Service Connector.
ms.devlang: python
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
zone_pivot_groups: postgres-server-options
ms.custom: ignite-fall-2021
ms.openlocfilehash: a14dc7e27c49d9f4e253e3a3a51c0526a56cadf9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091597"
---
# <a name="tutorial-using-service-connector-preview-to-build-a-django-app-with-postgres-on-azure-app-service"></a>Tutorial: Uso de Service Connector (versión preliminar) para compilar una aplicación de Django con Postgres en Azure App Service

> [!NOTE]
> En este tutorial, usará Service Connector (versión preliminar) que facilita la conexión de la aplicación web al servicio de base de datos. Este tutorial es una modificación del [tutorial de App Service](../app-service/tutorial-python-postgresql-app.md) para usar esta característica en versión preliminar de forma que vea las similitudes. Consulte la sección [4.2 Configuración de variables de entorno para conectar la base de datos](#42-configure-environment-variables-to-connect-the-database) en este tutorial para ver dónde entra en juego el conector de servicio y simplifica el proceso de conexión que se proporciona en el tutorial de App Service.

::: zone pivot="postgres-single-server"

En este tutorial se muestra cómo implementar una aplicación web de [Django](https://www.djangoproject.com/) de Python en [Azure App Service](overview.md) y cómo conectarla a una base de datos de Azure Database for Postgres. También puede seleccionar la opción anterior para probar la opción de servidor flexible de PostgresSQL (versión preliminar). La opción de servidor flexible proporciona un mecanismo de implementación más sencillo y menores costos continuos.

En este tutorial, se usa la CLI de Azure para completar las siguientes tareas:

> [!div class="checklist"]
> * Configurar el entorno inicial con Python y la CLI de Azure
> * Crear una base de datos de Azure Database for PostgreSQL
> * Implementar código en Azure App Service y conectarse a PostgreSQL
> * Actualizar el código y volver a implementarlo
> * Visualización de los registros de diagnóstico
> * Administrar la aplicación web en Azure Portal

:::zone-end

::: zone pivot="postgres-flexible-server"

En este tutorial se muestra cómo implementar una aplicación web Python [Django](https://www.djangoproject.com/) controlada por datos en [Azure App Service](overview.md) y cómo conectarla a una base de datos de [Azure Database for PostgreSQL: servidor flexible (versión preliminar)](../postgresql/flexible-server/index.yml). Si no puede usar la opción de servidor flexible de PostgreSQL (versión preliminar), seleccione la opción de servidor único anterior. 

En este tutorial, se usa la CLI de Azure para completar las siguientes tareas:

> [!div class="checklist"]
> * Configurar el entorno inicial con Python y la CLI de Azure
> * Creación de una base de datos de Azure Database for PostgreSQL: servidor flexible
> * Implementación de código en Azure App Service y conexión al servidor flexible de PostgreSQL
> * Actualizar el código y volver a implementarlo
> * Visualización de los registros de diagnóstico
> * Administrar la aplicación web en Azure Portal


:::zone-end

## <a name="1-set-up-your-initial-environment"></a>1. Configuración del entorno inicial

1. Disponga de una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Instale <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 o una versión posterior</a>.
1. Instale la <a href="/cli/azure/install-azure-cli" target="_blank">CLI de Azure</a> 2.18.0 o posterior, con la que se ejecutan comandos en cualquier shell para aprovisionar y configurar los recursos de Azure.

Abra una ventana del terminal y compruebe que la versión de Python es la 3.6 o superior:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Compruebe que la versión de la CLI de Azure es la 2.18.0 o posterior:

```azurecli
az --version
```

Si necesita actualizar, pruebe el comando `az upgrade` (requiere la versión 2.11 o superior), o consulte <a href="/cli/azure/install-azure-cli" target="_blank">Instalación de la CLI de Azure</a>.

Después, inicie sesión en Azure mediante la CLI:

```azurecli
az login
```

Este comando abre un explorador que recopila las credenciales. Cuando el comando finaliza, muestra una salida JSON que contiene información sobre las suscripciones.

Una vez que haya iniciado sesión, puede ejecutar comandos de Azure con la CLI de Azure para trabajar con los recursos de su suscripción.

¿Tiene problemas? [Póngase en contacto con nosotros](https://aka.ms/DjangoCLITutorialHelp).

## <a name="2-clone-or-download-the-sample-app"></a>2. Clonación o descarga de la aplicación de ejemplo

# <a name="git-clone"></a>[Clonación de Git](#tab/clone)

Clone el repositorio de ejemplo:

```terminal
git clone https://github.com/Azure-Samples/serviceconnector-webapp-postgresql-django.git
```

Después, vaya a esa carpeta:

```terminal
cd serviceconnector-webapp-postgresql-django
```

::: zone pivot="postgres-flexible-server"

Para el servidor flexible (versión preliminar), use la rama de servidor flexible del ejemplo, que contiene algunos cambios necesarios, como cómo se establece la dirección URL del servidor de bases de datos y se agrega `'OPTIONS': {'sslmode': 'require'}` a la configuración de la base de datos de Django según lo requiera el servidor flexible de Azure PostgreSQL.

```terminal
git checkout flexible-server
```

::: zone-end

# <a name="download"></a>[Descargar](#tab/download)

Visite [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp).

::: zone pivot="postgres-flexible-server"
Para el servidor flexible (versión preliminar), seleccione el control de ramas que dice "maestro" y seleccione la rama de servidor flexible en su lugar.
::: zone-end

Seleccione **Clonar** y **Descargar archivo ZIP**. 

Descomprima el archivo ZIP en una carpeta denominada *djangoapp*. 

A continuación, abra una ventana del terminal en la carpeta *djangoapp*.

---

El ejemplo djangoapp contiene la aplicación en Django de sondeos controlada por datos que se obtiene al seguir [Creación de la primera aplicación Django](https://docs.djangoproject.com/en/3.1/intro/tutorial01/) de la documentación de Django. La aplicación completa se proporciona aquí para su comodidad.

El ejemplo también se ha modificado para ejecutarse en un entorno de producción, como App Service:

- La configuración para producción está en el archivo *azuresite/production.py*. Los valores del desarrollo están en el archivo *azuresite/settings.py*.
- La aplicación usa la configuración de producción cuando la variable de entorno `WEBSITE_HOSTNAME` está establecida. Azure App Service establece automáticamente esta variable en la dirección URL de la aplicación web, como `msdocs-django.azurewebsites.net`.

Los valores de producción son específicos de la configuración de Django para ejecutarse en cualquier entorno de producción y no son específicos de App Service. Para obtener más información, consulte la [lista de implementación de Django](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/). Consulte también [Configuración de producción para Django en Azure](../app-service/configure-language-python.md#production-settings-for-django-apps) para los detalles de algunos de los cambios.

¿Tiene problemas? [Póngase en contacto con nosotros](https://aka.ms/DjangoCLITutorialHelp).

## <a name="3-create-postgres-database-in-azure"></a>3. Creación de una base de datos de Postgres en Azure

::: zone pivot="postgres-single-server"
<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Habilite el almacenamiento en caché de parámetros con la CLI de Azure para que no sea necesario proporcionar esos parámetros con cada comando. (Los valores almacenados en caché se guardan en la carpeta *.azure*).

```azurecli
az config param-persist on 
```

Instale la extensión `db-up` para la CLI de Azure:

```azurecli
az extension add --name db-up
```

Si no se reconoce el comando `az`, asegúrese de que tiene instalada la CLI de Azure tal y como se describe en [Configuración del entorno inicial](#1-set-up-your-initial-environment).

A continuación, cree la base de datos de Postgres en Azure con el comando [`az postgres up`](/cli/azure/postgres#az_postgres_up):

```azurecli
az postgres up --resource-group ServiceConnector-tutorial-rg --location eastus --sku-name B_Gen5_1 --server-name <postgres-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- **Reemplace** *\<postgres-server-name>* por un nombre que sea **único en Azure** (el punto de conexión de servidor será `https://<postgres-server-name>.postgres.database.azure.com`). Un buen patrón es usar una combinación del nombre de la empresa y otro valor exclusivo.
- Para *\<admin-username>* y *\<admin-password>* , especifique las credenciales para crear un usuario administrador de este servidor Postgres. El nombre de inicio de sesión del administrador no puede ser *azure_superuser*, *azure_pg_admin*, *admin*, *administrator*, *root*, *guest* ni *public*. No puede empezar por *pg_* . La contraseña debe contener entre **8 y 128 caracteres** de tres de las categorías siguientes: letras del alfabeto inglés mayúsculas, letras del alfabeto inglés minúsculas, números (0 a 9) y caracteres no alfanuméricos (por ejemplo, !, $, #, %). La contraseña no puede contener el nombre de usuario.
- No use el carácter `$` en el nombre de usuario ni la contraseña. Más adelante creará variables de entorno con estos valores, donde el carácter `$` tiene un significado especial dentro del contenedor de Linux que se usa para ejecutar aplicaciones de Python.
- El [plan de tarifa](../postgresql/concepts-pricing-tiers.md) B_Gen5_1 (Básico, Gen5 y 1 núcleo) que se usa aquí es el menos costoso. En el caso de las bases de datos de producción, omita el argumento `--sku-name` para usar el nivel GP_Gen5_2 (De uso general, gen. 5, 2 núcleos) en su lugar.

Este comando realiza las siguientes acciones, que pueden tardar algunos minutos:

- Crea un [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) denominado `ServiceConnector-tutorial-rg`, si aún no existe.
- Cree un servidor Postgres y asigne el nombre con el argumento `--server-name`.
- Cree una cuenta de administrador con los argumentos `--admin-user` y `--admin-password`. Puede omitir estos argumentos para permitir que el comando genere automáticamente unas credenciales únicas.
- Cree la base de datos `pollsdb` y asigne el nombre con el argumento `--database-name`.
- Habilita el acceso desde la dirección IP local.
- Habilita el acceso desde servicios de Azure.
- Crea un usuario de base de datos con acceso a la base de datos `pollsdb`.

Puede realizar todos los pasos por separado con otros comandos `az postgres` y `psql`, pero `az postgres up` realiza todos los pasos juntos.

Cuando el comando se completa, genera un objeto JSON que contiene cadenas de conexión diferentes para la base de datos, junto con la dirección URL del servidor, un nombre de usuario generado (como "joyfulKoala@msdocs-djangodb-12345") y una contraseña de GUID. **Copie el nombre de usuario y la contraseña en un archivo de texto temporal**, ya que los necesitará más adelante en este tutorial.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, se puede establecer en cualquiera de las [regiones de Azure](https://azure.microsoft.com/global-infrastructure/regions/). Puede obtener las regiones disponibles para su suscripción con el comando [`az account list-locations`](/cli/azure/account#az_account_list_locations). En el caso de las aplicaciones de producción, coloque la base de datos y la aplicación en la misma ubicación.

::: zone-end

::: zone pivot="postgres-flexible-server"

1. Habilite el almacenamiento en caché de parámetros con la CLI de Azure para que no sea necesario proporcionar esos parámetros con cada comando. (Los valores almacenados en caché se guardan en la carpeta *.azure*).

    ```azurecli
    az config param-persist on 
    ```

1. Cree un [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) (puede cambiarle el nombre, si lo desea). El nombre del grupo de recursos se almacena en caché y se aplica automáticamente a los comandos posteriores.

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg --location eastus
    ```

1. Cree el servidor de bases de datos (el proceso tarda unos minutos):

    ```azurecli
    az postgres flexible-server create --sku-name Standard_B1ms --public-access all
    ```
    
    Si no se reconoce el comando `az`, asegúrese de que tiene instalada la CLI de Azure tal y como se describe en [Configuración del entorno inicial](#1-set-up-your-initial-environment).
    
    El comando [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) realiza las siguientes acciones, que pueden tardar unos minutos:
    
    - Cree un grupo de recursos predeterminado si aún no hay un nombre almacenado en caché.
    - Cree un servidor flexible de PostgreSQL:
        - De forma predeterminada, el comando usa un nombre generado tipo `server383813186`. Puede especificar su propio nombre con el parámetro `--name`. El nombre debe ser único en todo Azure.
        - El comando usa el plan de tarifa `Standard_B1ms` de menor costo. Omita el argumento `--sku-name` para usar el nivel `Standard_D2s_v3` predeterminado.
        - El comando usa el grupo de recursos y la ubicación almacenados en caché del comando `az group create` anterior, que en este ejemplo es el grupo de recursos `ServiceConnector-tutorial-rg` de la región `eastus`.
    - Cree una cuenta de administrador con nombre de usuario y contraseña. Puede especificar estos valores directamente con los parámetros `--admin-user` y `--admin-password`.
    - Cree una base de datos que se llama `flexibleserverdb` de manera predeterminada. Puede especificar otro nombre de base de datos con el parámetro `--database-name`.
    - Habilite el acceso público completo, que puede controlar mediante el parámetro `--public-access`.
    
1. Cuando se complete el comando, **copie la salida JSON del comando en un archivo**, ya que necesita valores de la salida más adelante en este tutorial, específicamente el host, el nombre de usuario y la contraseña, junto con el nombre de la base de datos.

::: zone-end

¿Tiene problemas? [Póngase en contacto con nosotros](https://aka.ms/DjangoCLITutorialHelp).

## <a name="4-deploy-the-code-to-azure-app-service"></a>4. Implementación del código en Azure App Service

En esta sección, creará un host de aplicación en la aplicación de App Service, conectará esta aplicación a la base de datos de Postgres y, a continuación, implementará el código en ese host.

### <a name="41-create-the-app-service-app"></a>4.1 Creación de la aplicación de App Service

::: zone pivot="postgres-single-server"

En el terminal, asegúrese de estar en la carpeta del repositorio *djangoapp* que contiene el código de la aplicación.

Cree una aplicación de App Service (el proceso de host) con el comando [`az webapp up`](/cli/azure/webapp#az_webapp_up):

```azurecli
az webapp up --resource-group ServiceConnector-tutorial-rg --location eastus --plan ServiceConnector-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- Para el argumento `--location`, asegúrese de usar la ubicación que [admite Service Connector](concept-region-support.md).
- **Reemplace** *\<app-name>* por un nombre que sea único en Azure (el punto de conexión del servidor es `https://<app-name>.azurewebsites.net`). Los caracteres permitidos para *\<app-name>* son `A`-`Z`, `0`-`9` y `-`. Un buen patrón es usar una combinación del nombre de la empresa y un identificador de la aplicación.

Este comando realiza las siguientes acciones, que pueden tardar algunos minutos:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Cree el [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) si todavía no existe. (En este comando se usa el mismo grupo de recursos en el que se creó la base de datos anterior).
- Cree el [plan de App Service](../app-service/overview-hosting-plans.md) *DjangoPostgres-tutorial-plan* en el plan de tarifa Básico (B1), si no existe. `--plan` y `--sku` son opcionales.
- Cree la aplicación de App Service, si no existe.
- Habilite el registro predeterminado de la aplicación, si aún no está habilitado.
- Cargue el repositorio mediante la implementación del archivo ZIP con la automatización de compilación habilitada.
- Almacenar en caché los parámetros comunes, como el nombre del grupo de recursos y el plan de App Service, mediante el archivo *.azure/config*. Como consecuencia, no es necesario especificar el mismo parámetro con comandos posteriores. Por ejemplo, para volver a implementar la aplicación después de realizar cambios, puede ejecutar `az webapp up` de nuevo sin ningún parámetro. Sin embargo, los comandos que proceden de extensiones de la CLI, como `az postgres up`, no usan la memoria caché en este momento, por lo que es necesario especificar aquí el grupo de recursos y la ubicación, con el uso inicial de `az webapp up`.

::: zone-end

::: zone pivot="postgres-flexible-server"

1. En el terminal, asegúrese de estar en la carpeta del repositorio *djangoapp* que contiene el código de la aplicación.

1. Cambie a la rama `flexible-server` de la aplicación de ejemplo. Esta rama contiene la configuración específica necesaria para el servidor flexible de PostgreSQL:

    ```cmd
    git checkout flexible-server
    ```

1. Ejecute el siguiente comando [`az webapp up`](/cli/azure/webapp#az_webapp_up) para crear el host de App Service para la aplicación:

    ```azurecli
    az webapp up --name <app-name> --sku B1 
    ```
    <!-- without --sku creates PremiumV2 plan -->
        
    Este comando realiza las siguientes acciones, que pueden tardar unos minutos, para lo que usa el grupo de recursos y la ubicación almacenados en caché del comando `az group create` anterior (el grupo `Python-Django-PGFlex-rg` de la región `eastus` en este ejemplo).
    
    <!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
    <!-- No it doesn't. az webapp up doesn't respect --resource-group -->
    - Cree un [plan de App Service](../app-service/overview-hosting-plans.md) en el plan de tarifa Básico (B1). Puede omitir `--sku` para usar los valores predeterminados.
    - Cree la aplicación de App Service.
    - Habilite el registro predeterminado para la aplicación.
    - Cargue el repositorio mediante la implementación del archivo ZIP con la automatización de compilación habilitada.

::: zone-end

Tras una implementación correcta, el comando genera la salida JSON como en el ejemplo siguiente:

![Ejemplo de la salida del comando az webapp up](../app-service/media/tutorial-python-postgresql-app/az-webapp-up-output.png)

¿Tiene problemas? Consulte primero la [Guía de solución de problemas](../app-service/configure-language-python.md#troubleshooting) y, si eso no funciona, [háganoslo saber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="42-configure-environment-variables-to-connect-the-database"></a>4.2 Configuración de variables de entorno para conectar la base de datos

Con el código ahora implementado en App Service, el paso siguiente consiste en conectar la aplicación a la base de datos de Postgres en Azure.

El código de la aplicación espera encontrar la información de la base de datos en cuatro variables de entorno llamadas `AZURE_POSTGRESQL_HOST`, `AZURE_POSTGRESQL_NAME`, `AZURE_POSTGRESQL_USER` y `AZURE_POSTGRESQL_PASS`.

Para establecer variables de entorno en App Service, cree "valores de aplicación" mediante el comando [az connection create]().

::: zone pivot="postgres-single-server"

```azurecli
az webapp connection create postgres --client-type django
```

El grupo de recursos, el nombre de la aplicación y el nombre de la base de datos se extraen de los valores almacenados en caché. Durante la ejecución de este comando, debe proporcionar la contraseña de administrador de la base de datos de Postgres.

- El comando crea los valores denominados "AZURE_POSTGRESQL_HOST", "AZURE_POSTGRESQL_NAME", "AZURE_POSTGRESQL_USER" y "AZURE_POSTGRESQL_PASS", según lo esperado por el código de la aplicación.
- Si olvidó sus credenciales de administrador, el comando le guiará para restablecerla.

::: zone-end

::: zone pivot="postgres-flexible-server"
```azurecli
az webapp connection create postgres --client-type django
```

El grupo de recursos, el nombre de la aplicación y el nombre de la base de datos se extraen de los valores almacenados en caché. Durante la ejecución de este comando, debe proporcionar la contraseña de administrador de la base de datos de Postgres.

- El comando crea los valores denominados "AZURE_POSTGRESQL_HOST", "AZURE_POSTGRESQL_NAME", "AZURE_POSTGRESQL_USER" y "AZURE_POSTGRESQL_PASS", según lo esperado por el código de la aplicación.
- Si olvidó sus credenciales de administrador, el comando le guiará para restablecerla.

::: zone-end

En el código de Python, tiene acceso a esta configuración como variables de entorno con instrucciones como `os.environ.get('AZURE_POSTGRESQL_HOST')`. Para obtener más información, consulte [Acceso a variables de entorno](../app-service/configure-language-python.md#access-environment-variables).

¿Tiene problemas? Consulte primero la [Guía de solución de problemas](../app-service/configure-language-python.md#troubleshooting) y, si eso no funciona, [háganoslo saber](https://aka.ms/DjangoCLITutorialHelp).

### <a name="43-run-django-database-migrations"></a>4.3 Ejecución de migraciones de la base de datos de Django

Las migraciones de bases de datos de Django aseguran que el esquema de la base de datos de PostgreSQL en Azure coincida con las descritas en el código.

1. Ejecute `az webpp ssh` para abrir una sesión SSH para la aplicación web en el explorador:

    ```azurecli
    az webapp ssh
    ```


1. En la sesión de SSH, ejecute los siguientes comandos (puede pegar los comandos con **CTRL**+**Mayús**+**V**):

    ```bash
    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```

    Si encuentra algún error relacionado con la conexión a la base de datos, compruebe los valores de la configuración de la aplicación creada en la sección anterior.

1. El comando `createsuperuser` le pide las credenciales de superusuario. A los efectos de este tutorial, use el nombre de usuario predeterminado `root`, presione **ENTRAR** en la dirección de correo electrónico para que quede en blanco y escriba `Pollsdb1` como contraseña.

1. Si aparece un error que indica que la base de datos está bloqueada, asegúrese de haber ejecutado el comando `az webapp settings` en la sección anterior. Sin esa configuración, el comando Migrate no se puede comunicar con la base de datos, y esto provoca el error.

¿Tiene problemas? Consulte primero la [Guía de solución de problemas](../app-service/configure-language-python.md#troubleshooting) y, si eso no funciona, [háganoslo saber](https://aka.ms/DjangoCLITutorialHelp).
    
### <a name="44-create-a-poll-question-in-the-app"></a>4.4 Creación de una pregunta de sondeo en la aplicación

1. Abra el sitio web de la aplicación. La aplicación debe mostrar los mensajes "Polls app" (Aplicación de sondeos) y "No polls are available" (No hay sondeos disponibles) porque todavía no hay sondeos específicos en la base de datos.

    ```azurecli
    az webapp browse
    ```

    Si ve el mensaje "Application Error" (Error de aplicación), es probable que no haya creado la configuración necesaria en el paso anterior, [Configuración de las variables de entorno para conectarse a la base de datos](#42-configure-environment-variables-to-connect-the-database), o que dichos valores contengan errores. Ejecute el comando `az webapp config appsettings list` para comprobar la configuración.

    Después de actualizar la configuración para corregir los errores, espere un minuto para que la aplicación se reinicie y, después, actualice el explorador.

1. Vaya a la página de administración de la aplicación web; para ello, anexe `/admin` a la dirección URL, como en `http://<app-name>.azurewebsites.net/admin`. Inicie sesión con las credenciales de superusuario de Django de la sección anterior (`root` y `Pollsdb1`). En **Polls** (Sondeos), seleccione **Add** (Agregar) junto a **Questions** (Preguntas) y cree una pregunta de sondeo con algunas opciones.

1. Vuelva al sitio web principal (`http://<app-name>.azurewebsites.net`) para confirmar que las preguntas se presentan ahora al usuario. Responda a las preguntas como desee para generar algunos datos en la base de datos.

**¡Enhorabuena!** Está ejecutando una aplicación web de Django de Python en Azure App Service para Linux, con una base de datos de Postgres activa.

> [!NOTE]
> App Service detecta un proyecto de Django mediante la búsqueda de un archivo *wsgi.py* en cada subcarpeta, que `manage.py startproject` crea de forma predeterminada. Cuando App Service encuentra ese archivo, carga la aplicación web de Django. Para obtener más información, consulte [Configuración de una imagen de Python integrada](../app-service/configure-language-python.md).


## <a name="5-clean-up-resources"></a>5. Limpieza de recursos

Si desea mantener la aplicación o continuar con los tutoriales adicionales, vaya a [Pasos siguientes](#next-steps). De lo contrario, para evitar incurrir en cargos adicionales, puede eliminar la creación del grupo de recursos para este tutorial:

```azurecli
az group delete --name ServiceConnector-tutorial-rg --no-wait
```

Al eliminar el grupo de recursos, también se desasignan y eliminan todos los recursos que contiene. Asegúrese de que ya no necesita los recursos del grupo antes de usar el comando.

La eliminación de todos los recursos puede llevar tiempo. El argumento `--no-wait` permite que el comando devuelva resultados inmediatamente.

¿Tiene problemas? [Háganoslo saber](https://aka.ms/DjangoCLITutorialHelp).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
