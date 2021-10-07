---
title: Inicio rápido de uso de Azure App Configuration con aplicaciones de Python | Microsoft Docs
description: En este inicio rápido se crea una aplicación de Python con Azure App Configuration para centralizar el almacenamiento y la administración de la configuración de aplicaciones de forma independiente del código.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 046f845906845dbf1bf01a1a24de6e65da8a2a7c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233188"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Inicio rápido: Creación de una aplicación de Python con Azure App Configuration

En este inicio rápido, usará Azure App Configuration para centralizar el almacenamiento y la administración de la configuración de aplicaciones mediante la [biblioteca cliente de App Configuration para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
- Python 2.7, 3.6 o posterior: para información sobre cómo configurar Python en Windows, consulte la [documentación de Python en Windows](/windows/python/)

## <a name="create-an-app-configuration-store"></a>Creación de un almacén de App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Seleccione **Explorador de configuración** > **Crear** > **Clave-valor** para agregar los siguientes pares clave-valor:

    | Clave | Value |
    |---|---|
    | TestApp:Settings:Message | Datos de Azure App Configuration |

    Deje **Etiqueta** y **Tipo de contenido** en blanco, por ahora.

8. Seleccione **Aplicar**.

## <a name="setting-up-the-python-app"></a>Configuración de la aplicación de Python

1. En este tutorial, creará un directorio para el proyecto denominado *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Cambie al directorio recién creado *app-configuration-quickstart*.

    ```console
    cd app-configuration-quickstart
    ```

1. Instale la biblioteca cliente de Azure App Configuration mediante el comando `pip install`.

    ```console
    pip install azure-appconfiguration
    ```

1. Cree un archivo llamado *app-configuration-quickstart.py* en el directorio *app-configuration-quickstart* y agregue el código siguiente:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Los fragmentos de código de este inicio rápido le ayudarán a empezar a usar la biblioteca cliente de App Configuration para Python. También debe tener en cuenta el control de excepciones en su aplicación según sus necesidades. Para más información sobre el control de excepciones, consulte la [documentación del SDK de Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Configuración de la cadena de conexión de App Configuration

1. Establezca una variable de entorno llamada **AZURE_APP_CONFIG_CONNECTION_STRING** y defínala como la clave de acceso al almacén de App Configuration. En la línea de comandos, ejecute el siguiente comando:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Si usa Windows PowerShell, ejecute el siguiente comando:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Si usa macOS o Linux, ejecute el siguiente comando:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Reinicie el símbolo del sistema para permitir que el cambio surta efecto. Imprima el valor de la variable de entorno para comprobar que se ha establecido correctamente.

## <a name="code-samples"></a>Ejemplos de código

En los fragmentos de código de ejemplo de esta sección se muestra cómo realizar operaciones comunes con la biblioteca cliente de App Configuration para Python. Agregue estos fragmentos de código al bloque `try` del archivo *app-configuration-quickstart.py* que creó anteriormente.

> [!NOTE]
> La biblioteca cliente de App Configuration hace referencia a un objeto clave-valor como `ConfigurationSetting`. Por lo tanto, en este artículo, los pares **clave-valor** del almacén de App Configuration se conocen como **valores de configuración**.

* [Conexión a un almacén de App Configuration](#connect-to-an-app-configuration-store)
* [Obtención de un valor de configuración](#get-a-configuration-setting)
* [Incorporación de un valor de configuración](#add-a-configuration-setting)
* [Obtención de una lista de valores de configuración](#get-a-list-of-configuration-settings)
* [Bloqueo de un valor de configuración](#lock-a-configuration-setting)
* [Desbloqueo de un valor de configuración](#unlock-a-configuration-setting)
* [Actualización de un valor de configuración](#update-a-configuration-setting)
* [Eliminación de un valor de configuración](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Conexión a un almacén de App Configuration

El siguiente fragmento de código crea una instancia de **AzureAppConfigurationClient** mediante la cadena de conexión almacenada en las variables de entorno.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Obtención de un valor de configuración

El siguiente fragmento de código recupera un valor de configuración por nombre de `key`.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Incorporación de un valor de configuración

El siguiente fragmento de código crea un objeto `ConfigurationSetting` con los campos `key` y `value` e invoca el método `add_configuration_setting`. Este método producirá una excepción si intenta agregar un valor de configuración que ya existe en el almacén. Para evitar esta excepción, puede utilizar en su lugar el método [set_configuration_setting](#update-a-configuration-setting).

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Obtención de una lista de valores de configuración

En el fragmento de código siguiente se recupera una lista de valores de configuración. Se pueden proporcionar los argumentos `key_filter` y `label_filter` para filtrar los pares clave-valor por `key` y `label`, respectivamente. Para más información sobre el filtrado, consulte cómo [realizar consultas de los valores de configuración](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Bloqueo de un valor de configuración

El estado de bloqueo de un par clave-valor en App Configuration está indicado por el atributo `read_only` del objeto `ConfigurationSetting`. Si `read_only` es `True`, el valor está bloqueado. Se puede invocar el método `set_read_only` con el argumento `read_only=True` para bloquear el valor de configuración.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Desbloqueo de un valor de configuración

Si el atributo `read_only` de `ConfigurationSetting` es `False`, el valor está desbloqueado. Se puede invocar el método `set_read_only` con el argumento `read_only=False` para desbloquear el valor de configuración.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Actualización de un valor de configuración

El método `set_configuration_setting` se puede utilizar para actualizar un valor existente o para crear un valor. El siguiente fragmento de código cambia el valor de un valor de configuración existente.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Eliminación de un valor de configuración

El siguiente fragmento de código elimina un valor de configuración por el nombre de `key`.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Ejecución de la aplicación

En este inicio rápido, ha creado una aplicación de Python que usa la biblioteca cliente de Azure App Configuration para recuperar un valor de configuración creado mediante Azure Portal, agregar un nuevo valor, recuperar una lista de valores existentes, bloquear y desbloquear un valor, actualizar un valor y, por último, eliminar un valor.

Llegados a este punto, el archivo *app-configuration-quickstart.py* debe tener el código siguiente:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

En la ventana de la consola, vaya al directorio que contiene el archivo *app-configuration-quickstart.py* y, luego, ejecute el siguiente comando de Python para ejecutar la aplicación:

```console
python app-configuration-quickstart.py
```

Debería ver la siguiente salida:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Limpieza de recursos


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado un almacén de App Configuration y ha aprendido a acceder a los pares clave-valor desde una aplicación de Python.

Para encontrar otros ejemplos de código, visite:

> [!div class="nextstepaction"]
> [Ejemplos de la biblioteca cliente de Azure App Configuration](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
