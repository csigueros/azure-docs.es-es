---
title: Introducción a los cuadernos de Jupyter y MSTICPy en Azure Sentinel | Microsoft Docs
description: Lea la guía de introducción de Azure Sentinel para cuadernos de ML de Azure Sentinel a fin de obtener información sobre los conceptos básicos de los cuadernos de Azure Sentinel con MSTICPy y consultas.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: e5dc225d96021761fcdf12d5adf0f1fa371ebdb2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594603"
---
# <a name="tutorial-get-started-with-jupyter-notebooks-and-msticpy-in-azure-sentinel"></a>Tutorial: Introducción a los cuadernos de Jupyter y MSTICPy en Azure Sentinel

En este tutorial se describe cómo ejecutar el cuaderno **Getting Started Guide For Azure Sentinel ML Notebooks** (Guía de introducción para cuadernos de ML de Azure Sentinel), que establece configuraciones básicas para ejecutar cuadernos de Jupyter en Azure Sentinel y consultas de datos sencillas.

En el cuaderno **Getting Started Guide for Azure Sentinel ML Notebooks** (Guía de introducción para cuadernos de ML de Azure Sentinel) se usa MSTICPy, una biblioteca de Python de herramientas de ciberseguridad creada por Microsoft, que proporciona la funcionalidad de búsqueda e investigación de amenazas.

MSTICPy reduce la cantidad de código que los clientes necesitan escribir para Azure Sentinel y proporciona lo siguiente:

- Funcionalidades de consulta de datos, tablas de Azure Sentinel, Microsoft Defender para punto de conexión, Splunk y otros orígenes de datos.
- Búsquedas de inteligencia sobre amenazas con proveedores de TI como VirusTotal y AlienVault OTX.
- Funciones de enriquecimiento como la geolocalización de direcciones IP, la extracción del indicador de riesgo (IoC) y búsquedas WhoIs.
- Herramientas de visualización mediante escalas de tiempo de eventos, árboles de proceso y asignación geográfica.
- Análisis avanzados, como la descomposición de series temporales, la detección de anomalías y la agrupación en clústeres.

En los pasos de este tutorial se describe cómo ejecutar el cuaderno **Getting Started Guide for Azure Sentinel ML Notebooks** (Guía de introducción para cuadernos de ML de Azure Sentinel) en el área de trabajo Azure Machine Learning mediante Azure Sentinel. También puede usar este tutorial como guía para realizar pasos similares a fin de ejecutar cuadernos en otros entornos, incluido el local.

Para obtener más información, vea [Uso de cuadernos como tecnología de las investigaciones](hunting.md#use-notebooks-to-power-investigations) y [Uso de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md).

> [!NOTE]
> En varios cuadernos de Azure Sentinel no se usa MSTICPy, como los cuadernos de **Credential Scanner** o los ejemplos de PowerShell y C#. Los cuadernos en los que no se usa MSTICpy no necesitan la configuración de MSTICPy que se describe en este artículo.
>

## <a name="prerequisites"></a>Requisitos previos

- Para usar cuadernos en Azure Sentinel, asegúrese de que tiene los permisos necesarios. Para obtener más información, vea [Administración del acceso a los cuadernos de Azure Sentinel](notebooks.md#manage-access-to-azure-sentinel-notebooks).

- Para realizar los pasos de este tutorial, necesitará Python 3.6 o posterior. En Azure Machine Learning puede usar un kernel de Python 3.8 (recomendado) o uno de Python 3.6.

- En este cuaderno se usa el servicio de búsqueda de geolocalización [MaxMind GeoLite2](https://www.maxmind.com) para direcciones IP. Para usar el servicio MaxMind GeoLite2, necesitará una clave de cuenta. Puede registrarse para obtener una cuenta gratuita y una clave en la [página de registro de Maxmind](https://www.maxmind.com/en/geolite2/signup).

- En este cuaderno se usa [VirusTotal](https://www.virustotal.com) (VT) como origen de inteligencia sobre amenazas. Para usar la búsqueda de inteligencia sobre amenazas de VirusTotal, necesitará una cuenta y una clave de API de VirusTotal.

    Puede registrarse para obtener una cuenta de VT gratuita en la [página de introducción a VirusTotal](https://developers.virustotal.com/v3.0/reference#getting-started). Si ya es un usuario de VirusTotal, puede usar la clave existente.

    > [!WARNING]
    > Si usa una clave de empresa de VT, almacénela en Azure Key Vault en lugar del archivo **msticpyconfig.yaml**. Para obtener más información, vea [Especificación de secretos como secretos de Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets) en la documentación de MSTICPY.
    >
    > Si no quiere configurar una cuenta de Azure Key Vault en este momento, regístrese y use una cuenta gratuita hasta que pueda configurar el almacenamiento de Key Vault.

## <a name="run-and-initialize-the-getting-started-guide-notebook"></a>Ejecución e inicialización del cuaderno Guía de introducción

En este procedimiento se describe cómo iniciar el cuaderno e inicializar MSTICpy.


1. En Azure Sentinel, seleccione **Cuadernos** a la izquierda.

1. En la pestaña **Templates** (Plantillas) seleccione **A Getting Started Guide For Azure Sentinel ML Notebooks** > **Save notebook** (Una guía de introducción para cuadernos de ML de Azure Sentinel > Guardar cuaderno) para guardarlo en el área de trabajo de Azure Machine Learning.

    Seleccione **Launch notebook** (Iniciar cuaderno) para ejecutar el cuaderno. El cuaderno contiene una serie de celdas:

    - Las celdas de *Markdown* contienen texto y gráficos con instrucciones para usar el cuaderno.
    - Las celdas de *código* contienen código ejecutable que realiza las funciones del cuaderno

    **Lectura y ejecución de celdas de código**

    Lea y ejecute las celdas de código en orden. Si omite celdas o las ejecuta en otro orden es posible que produzcan errores más adelante en el cuaderno.

    Para ejecutar cada celda, seleccione el botón de reproducción situado a la izquierda. Según la función que se realice, el código de la celda puede ejecutarse muy rápidamente o puede tardar unos segundos en completarse.

    Cuando se ejecuta, el botón de reproducción cambia a un número de carga y se muestra un estado de `Executing` en la parte inferior de la celda, junto con el tiempo transcurrido.

    > [!TIP]
    > Si el cuaderno no parece funcionar como se describe, reinicie el kernel y ejecute el cuaderno desde el principio. Por ejemplo, si alguna celda del cuaderno **Getting Started Guide** (Guía de introducción) tarda más de un minuto en ejecutarse, intente reiniciar el kernel y volver a ejecutar el cuaderno.
    >
    > En el cuaderno **Getting Started Guide** (Guía de introducción) se incluyen instrucciones para el uso básico de cuadernos de Jupyter, incluido el reinicio del kernel de Jupyter.
    >

    Una vez que haya terminado de leer y ejecutar las celdas de la sección **¿Qué es un cuaderno de Jupyter Notebook?** , estará listo para iniciar las tareas de configuración, empezando por la sección **Configuración del entorno del cuaderno**.

1. Ejecute la primera celda de código de la sección **Configuración del entorno del cuaderno**, que incluye el código siguiente:

    ```python
    # import some modules needed in this cell
    from pathlib import Path
    from IPython.display import display, HTML

    REQ_PYTHON_VER="3.6"
    REQ_MSTICPY_VER="1.2.3"

    display(HTML("Checking upgrade to latest msticpy version"))
    %pip install --upgrade --quiet msticpy[azuresentinel]>=$REQ_MSTICPY_VER

    # intialize msticpy
    from msticpy.nbtools import nbinit
    nbinit.init_notebook(
    namespace=globals(),
    extra_imports=["urllib.request, urlretrieve"]
    )
    pd.set_option("display.html.table_schema", False)
    ```

    El estado de inicialización se muestra en la salida. Se esperan advertencias de configuración sobre valores que faltan en el archivo `Missing msticpyconfig.yaml` porque todavía no ha configurado nada.

> [!NOTE]
> La mayoría de los cuadernos de Azure Sentinel comienzan con una celda de inicialización de MSTICpy que:
>
> - Define las versiones mínimas de Python y MSTICPy que necesita el cuaderno.
> - Garantiza que está instalada la versión más reciente de MSTICPy.
> - Importa y ejecuta la función `init_notebook`.
>

## <a name="create-your-configuration-file"></a>Creación del archivo de configuración

Después de la inicialización básica, está listo para crear el archivo con la configuración básica para trabajar con MSTICPy.

Muchos cuadernos de Azure Sentinel se conectan a servicios externos como [VirusTotal](https://www.virustotal.com) (VT) para recopilar y enriquecer datos. Para conectarse a estos servicios, debe establecer y almacenar los detalles de configuración, como los tokens de autenticación. Almacenar estos datos en el archivo de configuración evita tener que escribir tokens de autenticación y detalles del área de trabajo cada vez que se usa un cuaderno.

MSTICPy usa **msticpyconfig.yaml** para almacenar una amplia gama de detalles de configuración.  De forma predeterminada, la función de inicialización del cuaderno genera un archivo **msticpyconfig.yaml**. Si [ha clonado este cuaderno desde el portal de Azure Sentinel](#run-and-initialize-the-getting-started-guide-notebook), el archivo de configuración se rellenará con datos del área de trabajo de Azure Sentinel. Estos datos se leen desde un archivo **config.json** creado en el área de trabajo de Azure Machine Learning al iniciar el cuaderno. Para obtener más información, vea la [documentación de configuración de paquetes de MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html).

En las secciones siguientes se describe cómo agregar detalles de configuración adicionales al archivo **msticpyconfig.yaml**.

> [!NOTE]
> Si ha vuelto a ejecutar el cuaderno *Getting Started Guide* (Guía de introducción) y ya tiene un archivo **msticpyconfig.yaml** con una configuración mínima, la función `init_notebook` no sobrescribe ni modifica el archivo existente.
>

> [!TIP]
> En cualquier momento, seleccione el menú desplegable **-Help** (Ayuda) de la herramienta de configuración de MSTICPy para obtener más instrucciones y vínculos a documentación detallada.
>

### <a name="display-the-msticpy-settings-editor"></a>Representación del editor de configuraciones de MSTICPy

1. En una celda de código, ejecute el código siguiente para importar la herramienta `MpConfigEdit` y mostrar un editor de configuraciones para el archivo **msticpyconfig.yaml**:

    ```python
    from msticpy.config import MpConfigEdit

    mpedit = MpConfigEdit( "msticpyconfig.yaml")
    mpedit.set_tab("AzureSentinel")
    display(mpedit)
    ```

    Por ejemplo:

    :::image type="content" source="media/notebook-get-started/msticpy-editor.png" alt-text="Captura de pantalla del editor de configuraciones de MSTICPy.":::

    El archivo **msticpyconfig.yaml** creado automáticamente, que se muestra en el editor de configuraciones, contiene dos entradas en la configuración de Azure Sentinel. Las dos se han rellenado con detalles del área de trabajo de Azure Sentinel desde la que se ha clonado el cuaderno. Una entrada tiene el nombre del área de trabajo y la otra se denomina **Default** (Predeterminada).

    MSTICPy permite almacenar configuraciones para varias áreas de trabajo de Azure Sentinel y cambiar entre ellas. La entrada **Default** (Predeterminada) le permite autenticarse en el área de trabajo "principal" de forma predeterminada, sin tener que nombrarla de forma explícita. Si agrega áreas de trabajo adicionales, puede configurar cualquiera de ellas para que sea la entrada **Default** (Predeterminada).

    > [!NOTE]
    > En el entorno de Azure Machine Learning, el editor de configuraciones puede tardar entre 10 y 20 segundos en aparecer.

1. Compruebe la configuración actual y seleccione **Save Settings** (Guardar configuración).

### <a name="add-threat-intelligence-provider-settings"></a>Adición de la configuración del proveedor de inteligencia sobre amenazas

En este procedimiento se describe cómo almacenar la [clave de API de VirusTotal](#prerequisites) en el archivo **msticpyconfig.yaml**. Puede optar por cargar la clave de API en Azure Key Vault, pero primero debe configurar los valores de Key Vault. Para obtener más información, vea [Configuración de los valores de Key Vault](#configure-key-vault-settings).

**Para agregar detalles de VirusTotal en el editor de configuraciones de MSTICPy**:

1. Escriba el código siguiente en una celda de código y ejecútelo:

   ```python
   mpedit.set_tab("TI Providers")
   mpedit
   ```

1. En la pestaña **TI Providers** (Proveedores de TI), seleccione **Add prov** > **VirusTotal** > **Add** (Agregar proveedor > VirusTotal > Agregar).

1. En **Auth Key** (Clave de autenticación), seleccione **Text** (Texto) junto a la opción **Storage** (Almacenamiento).

1. Pegue la clave de API en el campo **Value** (Valor).

1. Seleccione **Update** (Actualizar) y después **Save Settings** (Guardar configuración) en la parte inferior del editor de configuraciones.

> [!TIP]
> Para obtener más información sobre otros proveedores de inteligencia sobre amenazas admitidos, vea [Proveedores de inteligencia sobre amenazas](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html) en la documentación de MSTICPy e [Integración de inteligencia sobre amenazas en Azure Sentinel](threat-intelligence-integration.md).
>
### <a name="add-geoip-provider-settings"></a>Adición de la configuración del proveedor GeoIP

En este procedimiento se describe cómo almacenar una [clave de cuenta de MaxMind GeoLite2](#prerequisites) en el archivo **msticpyconfig.yaml**, lo que permite que el cuaderno use servicios de búsqueda de geolocalización para direcciones IP.

**Para agregar la configuración del proveedor GeoIP en el editor de configuraciones de MSTICPy**:

1. Escriba el código siguiente en una celda de código vacía y ejecútelo:

   ```python
   mpedit.set_tab("GeoIP Providers")
   mpedit
   ```

1. En la pestaña **GeoIP Providers** (Proveedores de GeoIP), seleccione **Add prov** > **GeoIPLite** > **Add** (Agregar proveedor > GeoIPLite > Agregar).

1. En el campo **Value** (Valor), escriba la clave de la cuenta de MaxMind.

1. Si es necesario, actualice la carpeta **~/.msticpy** predeterminada para almacenar la base de datos GeoIP descargada.

    - En Windows, esta carpeta se asigna a **%USERPROFILE%/.msticpy**.
    - En Linux o macOS, esta ruta se asigna a la carpeta **.msticpy** de la carpeta principal.


> [!TIP]
> Para obtener más información sobre otros servicios de búsqueda de geolocalización admitidos, vea la [documentación de proveedores GeoIP de MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html).
>

### <a name="configure-azure-cloud-settings"></a>Configuración de valores de Azure Cloud

Si en la organización no se usa la nube pública de Azure, debe especificarlo en la configuración para autenticarse y usar correctamente los datos de Azure Sentinel y Azure. Para obtener más información, vea [Especificación de la nube de Azure y los métodos de autenticación de Azure predeterminados](#specify-the-azure-cloud-and-azure-authentication-methods).

### <a name="validate-settings"></a>Validación de la configuración

Seleccione **Validate settings** (Validar configuración) en el editor de configuraciones.

Se esperan mensajes de advertencia sobre las configuraciones que faltan, pero no debe tener ninguna para la configuración del proveedor de inteligencia sobre amenazas o del proveedor GeoIP.

En función del entorno, es posible que también necesite [Configurar valores de Key Vault](#configure-key-vault-settings) o [Especificar la nube de Azure](#specify-the-azure-cloud-and-azure-authentication-methods).

Si tiene que realizar cambios debido a la validación, hágalos y, después, seleccione **Save Settings** (Guardar configuración).

Cuando haya terminado, seleccione el botón **Close** (Cerrar) para ocultar la salida de validación.

Para obtener más información, vea: [Configuraciones avanzadas para cuadernos de Jupyter Notebook y MSTICPy en Azure Sentinel](notebooks-msticpy-advanced.md).

## <a name="load-saved-msticpy-settings"></a>Carga de la configuración de MSTICPy guardada

En el procedimiento [Creación del archivo de configuración](#create-your-configuration-file), ha guardado la configuración en el archivo **msticpyconfig.yaml** local.

Pero MSTICPy no vuelve a cargar automáticamente esta configuración hasta que reinicie el kernel o ejecute otro cuaderno. Para forzar la recarga de MSTICPy desde el nuevo archivo de configuración, vaya a la siguiente celda de código, con el código siguiente y ejecútelo:

```python
import msticpy
msticpy.settings.refresh_config()
```

## <a name="test-your-notebook"></a>Prueba del cuaderno

Ahora que ha inicializado el entorno y ha establecido la configuración básica para el área de trabajo, use la clase `QueryProvider` de MSTICPy para probar el cuaderno. `QueryProvider` consulta un origen de datos, en este caso el área de trabajo de Azure Sentinel, y hace que los datos consultados estén disponibles para verlos y analizarlos en el cuaderno.

Use los procedimientos siguientes para crear una instancia de la clase `QueryProvider`, autenticarse en Azure Sentinel desde el cuaderno y ver y ejecutar consultas con una variedad de opciones de parámetros diferentes.

> [!TIP]
> Puede tener varias instancias de `QueryProvider` cargadas para usarlas con varias áreas de trabajo de Azure Sentinel u otros proveedores de datos, como Microsoft Defender para punto de conexión.
>

### <a name="load-the-queryprovider"></a>Carga de QueryProvider

A fin de cargar `QueryProvider` para `AzureSentinel`, continúe a la celda con el código siguiente y ejecútelo:

```python
# Initialize a QueryProvider for Azure Sentinel
qry_prov = QueryProvider("AzureSentinel")
```

> [!NOTE]
> Si ve una advertencia `Runtime dependency of PyGObject is missing` al cargar el controlador de Azure Sentinel, vea el [Error: *Falta la dependencia en tiempo de ejecución de PyGObject*](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/%22Runtime-dependency-of-PyGObject-is-missing%22-error).
Esta advertencia no afecta a la funcionalidad del cuaderno.
>

### <a name="authenticate-to-your-azure-sentinel-workspace-from-your-notebook"></a>Autenticación en el área de trabajo de Azure Sentinel desde el cuaderno

Autentíquese en el área de trabajo de Azure Sentinel mediante la [autorización de dispositivos](/azure/active-directory/develop/v2-oauth2-device-code) con las credenciales de Azure.

La autorización de dispositivos agrega otro factor a la autenticación mediante la generación de un código de dispositivo de un solo uso que se proporciona como parte del proceso de autenticación.

**Para autenticarse mediante la autorización de dispositivos**:

1. Ejecute la siguiente celda de código para generar y mostrar un código de dispositivo:

   ```python
   # Get the Azure Sentinel workspace details from msticpyconfig
   # Loading WorkspaceConfig with no parameters uses the details
   # of your Default workspace
   # If you want to connect to a specific workspace use this syntax:
   #    ws_config = WorkspaceConfig(workspace="WorkspaceName")
   # ('WorkspaceName' should be one of the workspaces defined in msticpyconfig.yaml)
   ws_config = WorkspaceConfig()

   # Connect to Azure Sentinel with your QueryProvider and config details
   qry_prov.connect(ws_config)
   ```

    Por ejemplo:

    :::image type="content" source="media/notebook-get-started/device-authorization.png" alt-text="Captura de pantalla en la que se muestra un código de autorización de dispositivo.":::

1. Seleccione y copie el código indicado en el Portapapeles. Después, vaya a [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) y pegue el código donde se le solicite.

1. Cuando vea el mensaje de confirmación del inicio de sesión, cierre la pestaña del explorador para volver al cuaderno en Azure Sentinel.

   En el cuaderno se muestra una salida similar a la siguiente:

   :::image type="content" source="media/notebook-get-started/authorization-complete.png" alt-text="Captura de pantalla en la que se muestra que el proceso de autorización del dispositivo se ha completado.":::

**Almacenamiento en caché del token de inicio de sesión mediante la CLI de Azure**

Para evitar tener que volver a autenticarse si reinicia el kernel o ejecuta otros cuadernos, puede almacenar en caché el token de inicio de sesión mediante la CLI de Azure.

El componente CLI de Azure en la instancia de Proceso almacena en caché un *token de actualización* que puede volver a usar hasta que se agote el tiempo de espera del token. MSTICPy usa automáticamente las credenciales de la CLI de Azure, si están disponibles.

Para autenticarse mediante la CLI de Azure escriba lo siguiente en una celda vacía y ejecútelo:

```python
!az login
```

> [!NOTE]
> Tendrá que volver a autenticarse si reinicia la instancia de Proceso o cambia a otra instancia. Para obtener más información, vea la sección [Caching credentials with Azure CLI](https://github.com/Azure/Azure-Sentinel-Notebooks/wiki/Caching-credentials-with-Azure-CLI) (Almacenamiento en caché de credenciales con la CLI de Azure) en la wiki del repositorio de GitHub de cuadernos de Azure Sentinel.
>

### <a name="view-the-azure-sentinel-workspace-data-schema-and-built-in-msticpy-queries"></a>Visualización del esquema datos del área de trabajo de Azure Sentinel y las consultas integradas de MSTICPy

Después de conectarse a una instancia de QueryProvider de Azure Sentinel, puede comprender los tipos de datos disponibles para consultar si consulta el esquema de datos del área de trabajo de Azure Sentinel.

QueryProvider de Azure Sentinel tiene una propiedad `schema_tables`, que proporciona una lista de tablas de esquema, y una propiedad `schema`, que también incluye los nombres de columna y los tipos de datos de cada tabla.

**Para ver las 10 primeras tablas del esquema de Azure Sentinel**:

Continúe a la celda siguiente, con el código siguiente, y ejecútelo. Puede omitir `[:10]` para enumerar todas las tablas del área de trabajo.

```python
# Get list of tables in the Workspace with the 'schema_tables' property
qry_prov.schema_tables[:10]  # Output only a sample of tables for brevity
                             # Remove the "[:10]" to see the whole list
```

Se mostrará la siguiente salida:

```output
Sample of first 10 tables in the schema
    ['AACAudit',
     'AACHttpRequest',
     'AADDomainServicesAccountLogon',
     'AADDomainServicesAccountManagement',
     'AADDomainServicesDirectoryServiceAccess',
     'AADDomainServicesLogonLogoff',
     'AADDomainServicesPolicyChange',
     'AADDomainServicesPrivilegeUse',
     'AADDomainServicesSystemSecurity',
     'AADManagedIdentitySignInLogs']
```

MSTICPy también incluye muchas consultas integradas disponibles para su ejecución. Puede enumerar las consultas disponibles con `.list_queries()`, y obtener detalles específicos sobre una consulta si la llama con un signo de interrogación (`?`) incluido como parámetro. Como alternativa, puede ver la lista de consultas y la ayuda asociada en el explorador de consultas.

**Para ver una muestra de las consultas disponibles**:

Continúe a la celda siguiente, con el código siguiente, y ejecútelo. Puede omitir `[::5]` para enumerar todas las consultas.

```python
# Get a sample of available queries
print(qry_prov.list_queries()[::5])  # showing a sample - remove "[::5]" for whole list
```

Se mostrará la siguiente salida:

```output
Sample of queries
=================
['Azure.get_vmcomputer_for_host', 'Azure.list_azure_activity_for_account', 'AzureNetwork.az_net_analytics', 'AzureNetwork.get_heartbeat_for_ip', 'AzureSentinel.get_bookmark_by_id', 'Heartbeatget_heartbeat_for_host', 'LinuxSyslog.all_syslog', 'LinuxSyslog.list_logon_failures', 'LinuxSyslog.sudo_activity', 'MultiDataSource.get_timeseries_decompose', 'Network.get_host_for_ip','Office365.list_activity_for_ip', 'SecurityAlert.list_alerts_for_ip', 'ThreatIntelligence.list_indicators_by_filepath', 'WindowsSecurity.get_parent_process', 'WindowsSecurity.list_host_events','WindowsSecurity.list_hosts_matching_commandline', 'WindowsSecurity.list_other_events']
```

**Para obtener ayuda sobre una consulta pasando `?` como parámetro**:

```python
# Get help about a query by passing "?" as a parameter
qry_prov.Azure.list_all_signins_geo("?")
```

Se mostrará la siguiente salida:

```output
Help for 'list_all_signins_geo' query
=====================================
Query:  list_all_signins_geo
Data source:  AzureSentinel
Gets Signin data used by morph charts

Parameters
----------
add_query_items: str (optional)
    Additional query clauses
end: datetime (optional)
    Query end time
start: datetime (optional)
    Query start time
    (default value is: -5)
table: str (optional)
    Table name
    (default value is: SigninLogs)
Query:
     {table} | where TimeGenerated >= datetime({start}) | where TimeGenerated <= datetime({end}) | extend Result = iif(ResultType==0, "Sucess", "Failed") | extend Latitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).latitude) | extend Longitude = tostring(parse_json(tostring(LocationDetails.geoCoordinates)).longitude)
```

**Para ver tablas y consultas en una lista desplazable y con filtros**:

Continúe a la celda siguiente, con el código siguiente y ejecútelo:

```python
qry_prov.browse_queries()
```

Para la consulta seleccionada, se muestran todos los parámetros obligatorios y opcionales, junto con el texto completo de la consulta. Por ejemplo:

:::image type="content" source="media/notebook-get-started/view-tables-queries-in-list.png" alt-text="Captura de pantalla de las tablas y consultas mostradas en una lista desplazable y con filtros.":::

Para obtener más información, vea [Ejecución de una consulta predefinida](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-a-pre-defined-query) en la documentación de MSTICPy.

> [!NOTE]
> Aunque no puede ejecutar consultas desde el explorador, puede copiar y pegar el ejemplo al final de cada consulta para que se ejecute en otro lugar del cuaderno.
>

### <a name="run-queries-with-time-parameters"></a>Ejecución de consultas con parámetros de hora

La mayoría de las consultas necesitan parámetros de hora. Las cadenas de fecha y hora son tediosas de escribir y modificarlas en varios lugares puede dar lugar a errores.

Cada proveedor de consultas tiene parámetros predeterminados de hora de inicio y finalización para las consultas. Estos parámetros de hora se usan de forma predeterminada, siempre que se necesiten. Puede cambiar el intervalo de tiempo predeterminado si abre el control `query_time`. Los cambios permanecen en vigor hasta que vuelva a cambiarlos.

Continúe a la celda siguiente, con el código siguiente y ejecútelo:


```python
# Open the query time control for your query provider
qry_prov.query_time
```

Establezca las horas `start` y `end` según sea necesario. Por ejemplo:

:::image type="content" source="media/notebook-get-started/set-time-parameters.png" alt-text="Captura de pantalla del establecimiento de parámetros de hora predeterminados para las consultas.":::

### <a name="run-a-query-using-the-built-in-time-range"></a>Ejecución de una consulta mediante el intervalo de tiempo integrado

Los resultados de la consulta se devuelven como un elemento [DataFrame de Pandas](https://pandas.pydata.org), que es una estructura de datos tabular, como una hoja de cálculo o una tabla de base de datos. Puede usar [funciones de Pandas](https://pandas.pydata.org/docs/user_guide/10min.html) para realizar un filtrado y análisis adicionales en los resultados de la consulta.

La celda de código siguiente ejecuta una consulta mediante la configuración de hora predeterminada del proveedor de consultas. Puede cambiar este intervalo y volver a ejecutar la celda de código para consultar el nuevo intervalo de tiempo.

```python
# The time parameters are taken from the qry_prov time settings
# but you can override this by supplying explict "start" and "end" datetimes
signins_df = qry_prov.Azure.list_all_signins_geo()

# display first 5 rows of any results
# If there is no data, just the column headings display
signins_df.head()
```

En la salida se muestran las cinco primeras filas de resultados. Por ejemplo:

:::image type="content" source="media/notebook-get-started/run-query-with-built-in-time-range.png" alt-text="Captura de pantalla de la ejecución de una consulta con el intervalo de tiempo integrado.":::

Si no hay datos, solo se muestran los títulos de columna.

### <a name="run-a-query-using-a-custom-time-range"></a>Ejecución de una consulta mediante un intervalo de tiempo personalizado

También puede crear un objeto de hora de consulta y pasarlo a una consulta como parámetro, lo que le permite ejecutar una consulta única para otro intervalo de tiempo, sin que ello afecte a los valores predeterminados del proveedor de consultas.

```python
# Create and display a QueryTime control.
time_range = nbwidgets.QueryTime()
time_range
```

Después de establecer el intervalo de tiempo deseado, puede pasarlo a la función de consulta, y ejecutar el código siguiente en una celda independiente del código anterior:

```python
signins_df = qry_prov.Azure.list_all_signins_geo(time_range)
signins_df.head()
```

También puede pasar valores datetime como valores datetime de Python o cadenas de fecha y hora mediante los parámetros `start` y `end`:

```python
from datetime import datetime, timedelta
q_end = datetime.utc.now()
q_start = end – timedelta(5)
signins_df = qry_prov.Azure.list_all_signins_geo(start=q_start, end=q_end)
```

### <a name="customize-your-queries"></a>Personalización de las consultas

Puede personalizar las consultas integradas si agrega lógica de consulta adicional, o bien ejecutar consultas completas mediante la función `exec_query`.

Por ejemplo, la mayoría de las consultas integradas admiten el parámetro `add_query_items`, que puede usar para anexar filtros u otras operaciones a las consultas.

1. Ejecute la celda de código siguiente para agregar una trama de datos que resuma el número de alertas por nombre de alerta:

    ```python
    from datetime import datetime, timedelta

    qry_prov.SecurityAlert.list_alerts(
       start=datetime.utcnow() - timedelta(28),
        end=datetime.utcnow(),
        add_query_items="| summarize NumAlerts=count() by AlertName"
    )
    ```

1. Pase una cadena de consulta KQL completa al proveedor de consultas. La consulta se ejecuta en el área de trabajo conectada y los datos se devuelven como objeto DataFrame de Pandas. Ejecute:

    ```python
    # Define your query
    test_query = """
    OfficeActivity
    | where TimeGenerated > ago(1d)
    | take 10
    """

    # Pass the query to your QueryProvider
    office_events_df = qry_prov.exec_query(test_query)
    display(office_events_df.head())

    ```

Para más información, consulte:

- [Referencia de consultas de MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataQueries.html)
- [Ejecución de consultas predefinidas de MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/DataProviders.html#running-an-pre-defined-query)

### <a name="test-virustotal-and-geolite2"></a>Prueba de VirusTotal y GeoLite2

**Para comprobar si hay una dirección IP en los datos de VirusTotal**:

Para usar la inteligencia sobre amenazas a fin de ver si aparece una dirección IP en los datos de VirusTotal, ejecute la celda con el código siguiente:

```python
# Create your TI provider – note you can re-use the TILookup provider (‘ti’) for
# subsequent queries - you don’t have to create it for each query
ti = TILookup()

# Look up an IP address
ti_resp = ti.lookup_ioc("85.214.149.236")

ti_df = ti.result_to_df(ti_resp)
ti.browse_results(ti_df, severities="all")
```

En la salida se muestran detalles sobre los resultados. Por ejemplo:

:::image type="content" source="media/notebook-get-started/test-virustotal-ip.png" alt-text="Captura de pantalla de una dirección IP que aparece en los datos de VirusTotal.":::

Asegúrese de desplazarse hacia abajo para ver los resultados completos. Para obtener más información, vea [Búsquedas de inteligencia sobre amenazas en MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/TIProviders.html).

**Para probar la búsqueda IP de geolocalización**:

Para obtener los detalles de geolocalización de una dirección IP mediante el servicio MaxMind, ejecute la celda con el código siguiente:

```python
# create an instance of the GeoLiteLookup provider – this
# can be re-used for subsequent queries.
geo_ip = GeoLiteLookup()
raw_res, ip_entity = geo_ip.lookup_ip("85.214.149.236")
display(ip_entity[0])
```

En la salida se muestra información de geolocalización de la dirección IP. Por ejemplo:

```output
ipaddress
{ 'AdditionalData': {},
  'Address': '85.214.149.236',
  'Location': { 'AdditionalData': {},
                'CountryCode': 'DE',
                'CountryName': 'Germany',
                'Latitude': 51.2993,
                'Longitude': 9.491,
                'Type': 'geolocation',
                'edges': set()},
  'ThreatIntelligence': [],
  'Type': 'ipaddress',
  'edges': set()}
```

> [!NOTE]
> La primera vez que ejecute este código, debería ver que el controlador de GeoLite descarga su base de datos.
>

Para obtener más información, vea [Proveedores GeoIP de MSTICPy](https://msticpy.readthedocs.io/en/latest/data_acquisition/GeoIPLookups.html).

## <a name="configure-key-vault-settings"></a>Establecimiento de la configuración de Key Vault

Esta sección solo es relevante cuando se almacenan secretos en Azure Key Vault.

Al almacenar secretos en Azure Key Vault, primero debe crear la instancia de Key Vault, en el [portal de administración de almacenes de claves global de Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.KeyVault%2Fvaults).

La configuración necesaria son todos los valores que se obtienen de las propiedades del almacén, aunque algunos pueden tener otros nombres. Por ejemplo:

- **VaultName** se muestra en la parte superior izquierda de la pantalla **Propiedades** de Azure Key Vault
- **TenantId** se muestra como **Id. de directorio**
- **AzureRegion** se muestra como **Ubicación**
- **Authority** es la nube del servicio de Azure.

Solo se necesitan los valores **VaultName**, **TenantId** y **Authority** para recuperar secretos del almacén. Los demás valores son necesarios si opta por crear un almacén desde MSTICPy. Para obtener más información, vea [Especificación de secretos como secretos de Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets).

La opción **Use KeyRing** (Usar conjunto de claves) está seleccionada de forma predeterminada y le permite almacenar en caché las credenciales de Key Vault en un conjunto de claves local. Para obtener más información, vea [ Documentación de conjunto de claves](https://keyring.readthedocs.io/en/latest/index.html).

> [!CAUTION]
> No utilice la opción **Use KeyRing** (Usar conjunto de claves) si no confía completamente en el proceso del host en el que se ejecuta el cuaderno.
>
> En este caso, el *proceso* es el servidor central de Jupyter, donde se ejecuta el kernel del cuaderno, y no necesariamente la máquina en la que se ejecuta el explorador. Si usa Azure Machine Learning, el *proceso* será la instancia de Proceso de Azure Machine Learning que haya seleccionado. Keyring realiza su almacenamiento en caché en el host donde se ejecuta el kernel del cuaderno.
>

**Para agregar la configuración de Key Vault en el editor de configuraciones de MSTICPy**:

1.  Continúe a la celda siguiente, con el código siguiente y ejecútelo:

    ```python
    mpedit.set_tab("Key Vault")
    mpedit
    ```

1. Escriba los detalles de la instancia de Key Vault. Por ejemplo:

    :::image type="content" source="media/notebook-get-started/add-kv-settings.png" alt-text="Captura de pantalla de la sección de instalación de Key Vault":::

1. Seleccione **Save** (Guardar) y después **Save Settings** (Guardar configuración).

### <a name="test-key-vault"></a>Prueba del almacén de claves

Para probar el almacén de claves, compruebe si puede conectarse y ver los secretos. Si no ha agregado un secreto, no verá ningún detalle. Si es necesario, agregue un secreto de prueba desde el portal de Azure Key Vault al almacén y compruebe que se muestra en Azure Sentinel.

Por ejemplo:

```Python
mpconfig = MpConfigFile()
mpconfig.refresh_mp_config()
mpconfig.show_kv_secrets()
```

> [!CAUTION]
> No deje la salida que se muestra en el cuaderno guardado. Si hay secretos reales en la salida, use el comando **Borrar salida** del cuaderno antes de guardarlo.
>
> Además, elimine las copias del cuaderno almacenadas en caché. Por ejemplo, busque en la subcarpeta **.ipynb_checkpoints** del directorio del cuaderno y elimine las copias que encuentre de este cuaderno. Al guardar el cuaderno con una salida borrada se debe sobrescribir la copia del punto de control.
>

Una vez que ha configurado Key Vault, puede usar el botón **Upload to KV** (Cargar en KV) de las secciones Data Providers (Proveedores de datos) y TI Providers (Proveedores de TI) para mover la configuración seleccionada al almacén. MSTICPy generará un nombre predeterminado para el secreto en función de la ruta de la configuración, como `TIProviders-VirusTotal-Args-AuthKey`.

Si el valor se carga correctamente, se elimina el contenido del campo **Value** (Valor) en el editor de configuraciones y la configuración subyacente se reemplaza por un valor de marcador de posición. MSTICPy lo usará para indicar que debe generar automáticamente la ruta de Key Vault al intentar recuperar la clave.

Si ya tiene los secretos necesarios almacenados en una instancia de Key Vault puede escribir el nombre del secreto en el campo **Value** (Valor). Si el secreto no está almacenado en el almacén predeterminado (los valores especificados en la sección [Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html#key-vault)), puede especificar una ruta de **Nombre_del_almacén/Nombre_del_secreto**.

Actualmente no se admite la captura de la configuración de un almacén en otro inquilino. Para obtener más información, vea [Especificación de secretos como secretos de Key Vault](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html#specifying-secrets-as-key-vault-secrets).

## <a name="specify-the-azure-cloud-and-azure-authentication-methods"></a>Especificación de la nube de Azure y los métodos de autenticación de Azure

Si usa una nube de Azure soberana o gubernamental, en lugar de la nube pública o global de Azure, debe seleccionar la nube adecuada en la configuración. Para la mayoría de las organizaciones, la nube global es el valor predeterminado.

También puede usar esta configuración de Azure para definir las preferencias predeterminadas para el tipo de autenticación de Azure.

**Para especificar la nube de Azure y los métodos de autenticación de Azure**:

1.  Continúe a la celda siguiente, con el código siguiente y ejecútelo:

    ```python
    mpedit.set_tab("Azure")
    mpedit
    ```

1. Seleccione la nube que use la organización o deje la opción **global** seleccionada predeterminada.

1. Seleccione uno o varios de los métodos siguientes:

    - **env** para almacenar las credenciales de Azure en variables de entorno.
    - **msi** para usar Managed Service Identity, que es una identidad asignada al host o la máquina virtual donde se ejecuta el centro de Jupyter. MSI no se admite actualmente en instancias de Proceso de ML de Azure.
    - **cli** para usar las credenciales de una sesión de la CLI de Azure autenticada.
    - **interactive** para usar el flujo de autorización de dispositivos interactivo mediante un [código de dispositivo de un solo uso](#authenticate-to-your-azure-sentinel-workspace-from-your-notebook).

    > [!TIP]
    > En la mayoría de los casos, se recomienda seleccionar varios métodos, como **cli** e **interactive**. La autenticación de Azure probará cada uno de los métodos configurados en el orden indicado anteriormente hasta que uno sea correcto.
    >

1. Seleccione **Save** (Guardar) y después **Save Settings** (Guardar configuración).

Por ejemplo:

:::image type="content" source="media/notebook-get-started/settings-for-azure-gov-cloud.png" alt-text="Captura de pantalla de la configuración definida para la nube de Azure Government.":::


## <a name="next-steps"></a>Pasos siguientes

En este artículo se han descrito los aspectos básicos del uso de MSTICPy con cuadernos de Jupyter en Azure Sentinel. Para obtener más información, vea [Configuraciones avanzadas para cuadernos de Jupyter y MSTICPy en Azure Sentinel](notebooks-msticpy-advanced.md).

También puede probar otros cuadernos almacenados en el [repositorio Azure Sentinel Notebooks en GitHub](https://github.com/Azure/Azure-Sentinel-Notebooks) como:

- [Paseo por las características de Cybersec](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/A%20Tour%20of%20Cybersec%20notebook%20features.ipynb)
- [Ejemplos de Machine Learning](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/9bba6bb9007212fca76169c3d9a29df2da95582d/Machine%20Learning%20in%20Notebooks%20Examples.ipynb)
- La [serie de cuadernos de Entity Explorer](https://github.com/Azure/Azure-Sentinel-Notebooks/), que permiten profundizar en detalles sobre un host, una cuenta, una dirección IP y otras entidades.

> [!TIP]
> Si usa el cuaderno descrito en este tutorial en otro entorno de Jupyter, puede usar cualquier kernel que admita Python 3.6 o posterior.
>
> Para usar cuadernos de MSTICPy fuera de Azure Sentinel y Azure Machine Learning (ML), también tendrá que configurar el entorno de Python. Instale Python 3.6 o posterior con la distribución Anaconda, que incluye muchos de los paquetes necesarios.
>

### <a name="more-reading-on-msticpy-and-notebooks"></a>Más información sobre MSTICPy y los cuadernos

En la tabla siguiente se enumeran más referencias para obtener información sobre MSTICPy, Azure Sentinel y cuadernos de Jupyter.

|Firmante  |Más referencias  |
|---------|---------|
|**MSTICPy**     |      - [Configuración del paquete MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [Editor de configuraciones de MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [Configuración del entorno de Notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb).<br>    - [Cuaderno MPSettingsEditor](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb). <br><br>**Nota**: El repositorio Azure-Sentinel-Notebooks de GitHub también contiene un archivo *msticpyconfig.yaml* de plantilla con secciones comentadas, lo que puede ayudarle a comprender la configuración.      |
|**Azure Sentinel y cuadernos de Jupyter**     |      - [Cuadernos de Jupyter: introducción](https://realpython.com/jupyter-notebook-introduction/)<br>    - [Documentación de MSTICPy](https://msticpy.readthedocs.io/)<br>    - [Documentación de cuadernos de Azure Sentinel](notebooks.md)<br>    - [The Infosec Jupyterbook](https://infosecjupyterbook.com/introduction.html)<br>    - [Tutorial del Cuaderno del Explorador de host de Linux](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [Por qué usar Jupyter para investigaciones de seguridad](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Investigaciones de seguridad con Azure Sentinel y Notebooks](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Documentación de Pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Documentación de Bokeh](https://docs.bokeh.org/en/latest/)       |
|     |         |

