---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 09/13/2021
ms.openlocfilehash: 58f0c7bab2308912979d1a8dfc442f7af4d27591
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128548205"
---
En Visual Studio Code, el proyecto de aplicación lógica tiene uno de los siguientes tipos:

* Basado en paquete de extensiones (Node.js), que es el tipo predeterminado.
* Basado en paquetes NuGet (.NET), que se puede convertir desde el tipo predeterminado.

En función de estos tipos, el proyecto incluye carpetas y archivos ligeramente diferentes. Un proyecto basado en NuGet incluye una carpeta .bin que contiene paquetes y otros archivos de biblioteca. Un proyecto basado en paquetes no incluye la carpeta .bin ni otros archivos. Algunos escenarios requieren un proyecto basado en NuGet para que la aplicación se ejecute, por ejemplo, si quiere desarrollar y ejecutar operaciones integradas personalizadas. Para obtener más información sobre cómo convertir el proyecto para que use NuGet, consulte [Habilitación de la creación de conectores integrados](../articles/logic-apps/create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring).

Para el proyecto basado en paquetes predeterminado, el proyecto tiene una estructura de carpetas y archivos similar a la del ejemplo siguiente:

```text
MyBundleBasedLogicAppProjectName
| .vscode
| Artifacts
  || Maps 
     ||| MapName1
     ||| ...
  || Schemas
     ||| SchemaName1
     ||| ...
| WorkflowName1
  || workflow.json
  || ...
| WorkflowName2
  || workflow.json
  || ...
| workflow-designtime
| .funcignore
| connections.json
| host.json
| local.settings.json
```

En el nivel raíz del proyecto, puede encontrar los siguientes archivos y carpetas con otros elementos:

| Nombre | Archivo o carpeta | Descripción |
|------|----------------|-------------|
| **.vscode** | Carpeta | Contiene archivos de configuración relacionados con Visual Studio Code, como **extensions.json**, **launch.json**, **settings.json** y **tasks.json**. |
| **Artefactos** | Carpeta | Contiene artefactos de la cuenta de integración que se definen y usan en los flujos de trabajo que admiten escenarios de negocio a negocio (B2B). Por ejemplo, la estructura de ejemplo incluye mapas y esquemas para operaciones de transformación y validación de XML. |
| **<*WorkflowName*>** | Carpeta | Con cada flujo de trabajo, la carpeta <*WorkflowName*> incluye un archivo **workflow.json**, que contiene la definición JASON subyacente de ese flujo de trabajo. |
| **workflow-designtime** | Carpeta | Contiene archivos de configuración relacionados con el entorno de desarrollo. |
| **.funcignore** | Archivo | Contiene información relacionada con la instancia de [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) instalada. |
| **connections.json** | Archivo | Contiene los metadatos, los puntos de conexión y las claves de las conexiones administradas y de Azure Functions que se usan en los flujos de trabajo. <p><p>**Importante**: Para usar diferentes conexiones y funciones en cada entorno, asegúrese de parametrizar este archivo **connections.json** y de actualizar los puntos de conexión. |
| **host.json** | Archivo | Contiene valores y opciones de configuración específicos del runtime, como, por ejemplo, los límites predeterminados para la plataforma de Azure Logic Apps de un solo inquilino, las aplicaciones lógicas, los flujos de trabajo, los desencadenadores y las acciones. En el nivel raíz del proyecto de aplicación lógica, el archivo de metadatos **host.json** contiene los valores y las opciones de configuración predeterminados que *todos los flujos de trabajo* de la misma aplicación lógica usan mientras se ejecutan, ya sea localmente o en Azure. <p>**Nota**: Al crear la aplicación lógica, Visual Studio Code crea un archivo **host.snapshot.*.json** de copia de seguridad en el contenedor de almacenamiento. Si elimina la aplicación lógica, este archivo de copia de seguridad no se elimina. Si crea otra aplicación lógica con el mismo nombre, se crea otro archivo de instantánea. Solo puede tener 10 instantáneas para la misma aplicación lógica. Si se supera este número, verá el siguiente error: <p>`Microsoft.Azure.WebJobs.Script.WebHost: Repository has more than 10 non-decryptable secrets backups (host))` <p>Para resolver este error, elimine los archivos de instantáneas adicionales del contenedor de almacenamiento. |
| **local.settings.json** | Archivo | Contiene la configuración de la aplicación, las cadenas de conexión y otras configuraciones que los flujos de trabajo usan mientras se ejecutan localmente. En otras palabras, esta configuración y estos valores *solo* se aplican cuando se ejecutan los proyectos en el entorno de desarrollo local. Durante la implementación en Azure, el archivo y la configuración se omiten y no se incluyen con la implementación. <p><p>Este archivo almacena valores y opciones de configuración como *variables de entorno local* que las herramientas de desarrollo local usan como valores `appSettings`. Puede llamar a estas variables de entorno y hacer referencia a ellas tanto en tiempo de ejecución como en tiempo de implementación mediante la *configuración de la aplicación* y los *parámetros*. <p><p>**Importante**: el archivo **local.settings.json** puede contener secretos, por lo que debe asegurarse de excluir también este archivo del control de código fuente del proyecto. |
||||