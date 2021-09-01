---
title: Importación y ejecución de cuadernos desde un repositorio de GitHub en Azure Cosmos DB
description: Obtenga información sobre cómo conectarse a GitHub e importar cuadernos desde un repositorio de GitHub a su cuenta de Azure Cosmos. Después de la importación, podrá ejecutarlos, editarlos y guardar los cambios en GitHub.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: 5caed47f67e15160f7754bad0003e5675d197573
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114267"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>Importación de cuadernos desde un repositorio de GitHub en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Después de [habilitar la compatibilidad con los cuadernos](enable-notebooks.md) en sus cuentas de Azure Cosmos, puede crear cuadernos, cargar cuadernos nuevos desde su equipo local o importar otros existentes desde sus cuentas de GitHub. En este artículo se muestra cómo conectar el área de trabajo de los cuadernos a GitHub e importar cuadernos desde un repositorio de GitHub a su cuenta de Azure Cosmos. Después de la importación, podrá ejecutarlos, modificarlos y guardar los cambios en GitHub.

## <a name="get-notebooks-from-github"></a>Obtención de cuadernos desde GitHub

Puede conectarse a sus propios repositorios de GitHub u otros repositorios de GitHub públicos para leer, crear y compartir cuadernos en Azure Cosmos DB. Use los pasos siguientes para conectarse a una cuenta de GitHub:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y desplácese hasta la cuenta de Azure Cosmos.

1. Abra la pestaña **Explorador de datos**. En esta pestaña se muestran todas las bases de datos, contenedores y cuadernos existentes.

1. Seleccione el elemento de menú **Conectarse a GitHub**.

1. Se abre una pestaña en la que puede elegir conectarse solo a **Repositorios públicos** o a **Repositorios públicos y privados**.  Después de elegir la opción necesaria, seleccione **Autorizar acceso**. Se necesita la autorización para que Azure Cosmos DB tenga acceso a los repositorios de la cuenta de GitHub.

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="Autorización a Azure Cosmos DB para acceder a los repositorios de GitHub":::

1. Se le redirigirá a la página web "github.com", en la que podrá confirmar la autorización. Seleccione el botón **Autorizar AzureCosmosDBNotebooks** y escriba la contraseña de la cuenta de GitHub en el símbolo del sistema.

1. Una vez que la autorización esté correcta, volverá a su cuenta de Azure Cosmos. A continuación, podrá ver todos los repositorios públicos y privados de la cuenta de GitHub. Puede seleccionar un repositorio de la lista disponible o agregar un repositorio directamente con su dirección URL.

1. Una vez que haya seleccionado el repositorio correspondiente, la entrada del repositorio se moverá de la sección **Repositorios anclados** a la sección **Repositorios anclados**. Si es necesario, también puede elegir una rama específica de ese repositorio para realizar la importación de cuadernos desde la misma.

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="Elección de un repositorio y una rama":::

1. Seleccione **Aceptar** para completar la operación de importación. Todos los cuadernos disponibles en la rama seleccionada del repositorio se importarán en su cuenta de Azure Cosmos.

Después de integrarse con una cuenta de GitHub, solo puede ver la lista de repositorios y cuadernos en la cuenta de Azure Cosmos. Esta afirmación es verdadera incluso si varios usuarios inician sesión en la cuenta de Azure Cosmos DB y agregan sus propias cuentas. En otras palabras, varios usuarios pueden usar la misma cuenta de Azure Cosmos para conectar el área de trabajo del cuaderno a GitHub. No obstante, cada usuario solo verá la lista de repositorios y cuadernos que ha importado. No podrá ver los cuadernos que hayan importado otros usuarios.

Para desconectar la cuenta de GitHub del área de trabajo de cuadernos, abra la pestaña **Explorador de datos**, seleccione `…` junto a **Repositorios de GitHub** y seleccione **Disconnect from GitHub** (Desconectarse de GitHub).

## <a name="edit-a-notebook-and-push-changes-to-github"></a>Edición de un cuaderno y envío de cambios ("push") a GitHub

Puede editar un cuaderno existente o agregar uno nuevo al repositorio y guardar los cambios en GitHub.

Después de editar un cuaderno existente, seleccione **Guardar**. Se abrirá un cuadro de diálogo en el que podrá escribir el mensaje de confirmación ("commit") para los cambios que ha realizado. Seleccione **Confirmar** y se actualizará el cuaderno en GitHub. Inicie sesión en su cuenta de GitHub y compruebe el historial de confirmaciones para validar sus actualizaciones.

En el flujo de GitHub normal, después de confirmar los cambios, normalmente estos se envían a un repositorio remoto. Sin embargo, en este caso, la opción de confirmación tiene el propósito de "almacenar de forma provisional, confirmar y enviar" las actualizaciones en GitHub.

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="Edición de cuadernos y confirmación de cambios en GitHub":::

Después de editar el cuaderno, puede [publicarlo en la galería de cuadernos](publish-notebook-gallery.md). 

## <a name="next-steps"></a>Pasos siguientes

* Conozca las ventajas de los [cuadernos de Jupyter para Azure Cosmos DB.](../cosmosdb-jupyter-notebooks.md)
* [Exploración de la galería de ejemplos de Notebook](https://cosmos.azure.com/gallery.html)
* [Publicación de cuadernos en la galería de cuadernos de Azure Cosmos DB](publish-notebook-gallery.md)
* [Uso de características y comandos de cuadernos de Python](../use-python-notebook-features-and-commands.md)
* [Uso de características y comandos de cuadernos de C#](../use-csharp-notebook-features-and-commands.md)
