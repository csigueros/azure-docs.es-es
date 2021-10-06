---
title: 'Inicio rápido: Creación de una cuenta de Purview mediante Python'
description: Cree una cuenta de Azure Purview mediante Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.devlang: python
ms.topic: quickstart
ms.date: 09/27/2021
ms.openlocfilehash: 7a6d13da2ee3138e6dfc5eca4a5b75f454f90457
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212498"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Inicio rápido: Creación de una cuenta de Purview mediante Python

En este inicio rápido, creará una cuenta de Purview mediante programación con Python. La [referencia de Python para Purview](/python/api/azure-mgmt-purview/) está disponible, pero este artículo le llevará por todos los pasos necesarios para crear una cuenta con Python.

Azure Purview es un servicio de gobernanza de datos que le ayuda a administrar y gobernar el escenario de datos. Al conectarse a los datos de sus orígenes locales, multinube y de software como servicio (SaaS), Purview crea un mapa actualizado de la información. Identifica y clasifica los datos confidenciales y proporciona un linaje completo. Los consumidores de datos pueden descubrir datos en la organización y los administradores de datos pueden auditar, proteger y garantizar el uso correcto de los datos.

Para más información sobre Purview, [consulte nuestra página de información general](overview.md). Para más información sobre la implementación de Purview en la organización, [consulte nuestros procedimientos recomendados sobre implementación](deployment-best-practices.md).

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

## <a name="install-the-python-package"></a>Instalar el paquete de Python

1. Abra un terminal o símbolo del sistema con privilegios de administrador.
2. En primer lugar, instale el paquete de Python para los recursos de administración de Azure:

    ```python
    pip install azure-mgmt-resource
    ```

3. Para instalar el paquete de Python para Purview, ejecute el siguiente comando:

    ```python
    pip install azure-mgmt-purview
    ```

    El [SDK de Python para Purview](https://github.com/Azure/azure-sdk-for-python) admite Python 2.7, 3.3, 3.4, 3.5, 3.6 y 3.7.

4. Para instalar el paquete de Python para la autenticación de Azure Identity, ejecute el siguiente comando:

    ```python
    pip install azure-identity
    ```

    > [!NOTE]
    > El paquete "azure-identity" puede tener conflictos con "azure-cli" en algunas dependencias comunes. Si se encuentra con algún problema de autenticación, elimine el paquete "azure-cli" y sus dependencias, o bien use un equipo limpio sin una instalación del paquete "azure-cli".

## <a name="create-a-purview-client"></a>Creación de un cliente de Purview

1. Cree un archivo llamado **purview.py**. Agregue las siguientes instrucciones para agregar referencias a espacios de nombres.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Agregue el código siguiente al método **Main**, que crea una instancia de la clase PurviewManagementClient. Este objeto se usa para crear una cuenta de Purview, eliminarla, comprobar la disponibilidad de nombre y otras operaciones del proveedor de recursos.

    ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Creación de una cuenta de Purview

1. Agregue el código siguiente al método **Main**, que crea una **cuenta de Purview**. Si ya existe el grupo de recursos, convierta en comentario la primera instrucción `create_or_update`.

   ```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
      ```

2. Ahora, agregue la siguiente instrucción para invocar el método **Main** cuando se ejecute el programa:

   ```python
   # Start the main method
   main()
   ```

## <a name="full-script"></a>Script completo

Este es el código de Python completo:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Ejecución del código

Compile e inicie la aplicación. La consola imprime el progreso de la creación de la cuenta de Purview. Espere hasta que se complete.
Este es la salida de ejemplo:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Comprobación del resultado

Vaya a la página **Purview accounts** (Cuentas de Purview) en Azure Portal y compruebe la cuenta creada con el código anterior.

## <a name="delete-purview-account"></a>Eliminación de la cuenta de Purview

Para eliminar una cuenta de Purview, agregue el siguiente código al programa y ejecute:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Pasos siguientes

En el código de este tutorial se crea y se elimina una cuenta de Purview. Ahora puede descargar el SDK de Python y conocer otras acciones del proveedor de recursos que puede realizar para una cuenta de Purview.

Consulte los artículos siguientes para aprender a navegar por Purview Studio, crear una colección y conceder acceso a Purview.

* [Uso de Purview Studio](use-purview-studio.md)
* [Crear una colección](quickstart-create-collection.md)
* [Adición de usuarios a una cuenta de Azure Purview](catalog-permissions.md)