---
title: Administración de paquetes de Python 3 en Azure Automation
description: En este artículo se describe cómo administrar paquetes de Python 3 (versión preliminar) en Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 11/01/2021
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: a33f4f9b6403d1f4aff88cadb57ae0f78f02038b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443764"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Administración de paquetes de Python 3 (versión preliminar) en Azure Automation

En este artículo se describe cómo importar, administrar y usar paquetes de Python 3 (versión preliminar) en la instancia de Azure Automation que se ejecuta en el entorno de espacio aislado de Azure y Hybrid Runbook Workers. Para simplificar los runbooks, puede usar paquetes de Python para importar los módulos que necesite. 

Para admitir runbooks de Python 3 en el servicio de Automation, el paquete de Azure 4.0.0 se instala de forma predeterminada en la cuenta de Automation. La versión predeterminada se puede invalidar si se importan los paquetes de Python en la cuenta de Automation. Asimismo, se da preferencia a la versión importada en la cuenta de Automation. Para importar un solo paquete, consulte [Importación de un paquete](#import-a-package). Para importar un paquete con varios paquetes, consulte [Importación de un paquete con dependencias](#import-a-package-with-dependencies). 

Para obtener información sobre cómo administrar paquetes de Python 2, consulte [Administración de paquetes de Python 2](./python-packages.md).

## <a name="packages-as-source-files"></a>Paquetes como archivos de origen

Azure Automation solo admite un paquete de Python que solo contenga código de Python y no incluya otras extensiones de lenguaje ni código en otros lenguajes. Sin embargo, es posible que el entorno de espacio aislado de Azure no tenga los compiladores necesarios para los archivos binarios de C/C++, por lo que se recomienda usar en su lugar [archivos wheel](https://pythonwheels.com/). El [Índice de paquetes de Python](https://pypi.org/) (PyPI) es un repositorio de software para el lenguaje de programación Python. Al seleccionar un paquete de Python 3 para importarlo a su cuenta de Automation desde PyPI, tenga en cuenta los siguientes elementos del nombre de archivo:

| Elemento de nombre de archivo | Descripción |
|---|---|
|cp38|Automation admite **Python 3.8.x** para trabajos en la nube.|
|AMD64|Los procesos de espacio aislado de Azure tienen una arquitectura **Windows de 64 bits**.|

Por ejemplo, si quiere importar pandas, puede seleccionar un archivo wheel con un nombre similar a `pandas-1.2.3-cp38-win_amd64.whl`.

Algunos paquetes de Python disponibles en PyPI no proporcionan un archivo wheel. En este caso, descargue el origen (archivo .zip o .tar.gz) y genere el archivo wheel mediante `pip`. Por ejemplo, realice los pasos siguientes con una máquina de 64 bits con Python 3.8.x y el paquete wheel instalado:

1. Descarga del código de origen `pandas-1.2.4.tar.gz`.
1. Ejecute pip para obtener el archivo wheel con el siguiente comando: `pip wheel --no-deps pandas-1.2.4.tar.gz`.

## <a name="import-a-package"></a>Importación de un paquete

1. Seleccione **Paquetes de Python** en **Recursos compartidos**, en la cuenta de Automation. A continuación, seleccione **+ Agregar un paquete de Python**.

   :::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Captura de pantalla de la página de paquetes de Python que muestra los paquetes de Python en el menú izquierdo y la opción para agregar un paquete de Python resaltada.":::

1. En la página **Agregar un paquete de Python**, seleccione un paquete local para cargar. El paquete puede ser un archivo **.whl** o **.tar.gz**. 
1. Escriba un nombre y seleccione la **versión del entorno de ejecución** como Python 3.8.x (versión preliminar).
1. Seleccione **Importar**.

   :::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Captura de pantalla que muestra la página Agregar un paquete de Python 3.8.x, con un archivo tar.gz cargado y seleccionado.":::

Una vez que se haya importado un paquete, este se muestra en la página de Paquetes de Python en su cuenta de Automation. Si necesita quitar un paquete, selecciónelo y haga clic en **Eliminar**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Captura de pantalla que muestra la página Paquetes de Python 3.8.x después de importar un paquete.":::

### <a name="import-a-package-with-dependencies"></a>Importación de un paquete con dependencias

Para importar un paquete de Python 3 y sus dependencias, importe el siguiente script de Python en un runbook de Python 3 y, luego, ejecútelo.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Importación del script en un runbook
Para información sobre cómo importar el runbook, consulte [Importación de un runbook desde Azure Portal](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Copie el archivo de GitHub en el almacenamiento al que se pueda acceder desde el portal antes de realizar la importación.

La página **Importar un runbook** adopta como valor predeterminado el nombre del runbook para que coincida con el nombre del script. Si tiene acceso al campo, puede cambiar el nombre. El campo **Tipo de runbook** puede adoptar como valor predeterminado **Python 2**. Si es así, asegúrese de cambiarlo a **Python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Captura de pantalla que muestra la página de importación de runbooks de Python 3.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Ejecución del runbook para importar el paquete y las dependencias

Después de crear y publicar el runbook, ejecútelo para importar el paquete. Consulte [Inicio de un runbook en Azure Automation](start-runbooks.md) para más información sobre la ejecución del runbook.

El script `import_py3package_from_pypi.py` requiere los siguientes parámetros:

| Parámetro | Descripción |
|---------------|-----------------|
|subscription_id | Identificador de suscripción de la cuenta de Automation |
| resource_group | Nombre del grupo de recursos en el que se define la cuenta de Automation |
| automation_account | Nombre de la cuenta de Automation |
| module_name | Nombre del módulo que se va a importar desde `pypi.org` |

Para más información sobre el uso de parámetros con runbooks, consulte [Trabajar con parámetros del runbook](start-runbooks.md#work-with-runbook-parameters).

## <a name="use-a-package-in-a-runbook"></a>Usar un paquete en un runbook

Puede usar el paquete importado en un runbook. Agregue el código siguiente para mostrar todos los grupos de recursos de una suscripción de Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

> [!NOTE]
> El paquete de Python `automationassets` no está disponible en pypi.org, por lo que no está disponible para importarlo en una máquina Windows.

## <a name="identify-available-packages-in-sandbox"></a>Identificación de los paquetes disponibles en el espacio aislado

Use el código siguiente para enumerar los módulos instalados predeterminados:

```python
#!/usr/bin/env python3

import pkg_resources
installed_packages = pkg_resources.working_set
installed_packages_list = sorted(["%s==%s" % (i.key, i.version)
   for i in installed_packages])

for package in installed_packages_list:
    print(package)
```

## <a name="next-steps"></a>Pasos siguientes

Para preparar un runbook de Python, consulte [Creación de un runbook de Python](learn/automation-tutorial-runbook-textual-python-3.md).
