---
title: 'Inicio rápido: Microsoft Azure Confidential Ledger con Azure Portal'
description: Aprenda a usar Microsoft Azure Confidential Ledger desde Azure Portal
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/18/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 265dfc023d0adf384eb483efc16a40380e395fb3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458159"
---
# <a name="quickstart-create-a-confidential-ledger-using-the-azure-portal"></a>Inicio rápido: Creación de un libro de contabilidad confidencial con Azure Portal

Azure Confidential Ledger es un servicio en la nube que proporciona un almacén de alta integridad para registros de datos confidenciales y registros que requieren que los datos se mantengan intactos. Para más información sobre Azure Confidential Ledger y para obtener ejemplos de lo que se puede almacenar en un libro de contabilidad confidencial, consulte [Acerca de Microsoft Azure Confidential Ledger](overview.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

En este inicio rápido, creará un libro de contabilidad confidencial mediante [Azure Portal](https://portal.azure.com). 

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-confidential-ledger"></a>Creación de un libro de contabilidad confidencial

1. En el menú de Azure Portal o en la página principal, seleccione **Crear un recurso**.

1. En el cuadro de búsqueda, escriba "Confidential Ledger".

1. En la lista de resultados, elija **Confidential Ledger**.

1. En la sección Confidential Ledger, elija **Crear**.

1. En la sección Create confidential ledger (Crear libro de contabilidad confidencial), proporcione la siguiente información:
    - **Nombre**: proporcione un nombre único para el libro de contabilidad confidencial.
    - **Suscripción**: Elija una suscripción.
    - **Grupo de recursos**: seleccione **Crear nuevo*** y escriba un nombre para el grupo de recursos.
    - **Ubicación**: en el menú desplegable, elija una ubicación.
    - Deje las restantes opciones con sus valores predeterminados.
   
1. Seleccione la pestaña **Seguridad** .

1. Ahora debe agregar un usuario basado en Azure AD o en certificados al libro de contabilidad confidencial con el rol "Administrador". En este inicio rápido, agregaremos un usuario basado en Azure AD. Seleccione **+ Add AAD-Based User** (Agregar usuario basado en AAD).

1. Debe agregar un usuario basado en Azure AD o en certificados. Busque la dirección de correo electrónico en el panel derecho. Seleccione la fila y, a continuación, elija **Seleccionar** en la parte inferior del panel.

1. En el campo desplegable **Ledger Role** (Rol de libro de contabilidad), seleccione **Administrador**.

1. Seleccione **Revisar + crear**. Seleccione **Crear** una vez que se pase la validación.

Una vez finalizada la implementación, seleccione **Go to resource** (Ir al recurso).

:::image type="content" source="./media/confidential-ledger-portal-quickstart.png" alt-text="Pantalla de creación del portal de ACL":::

Tome nota de las dos propiedades siguientes:
- **Nombre de libro de contabilidad confidencial**: en el ejemplo, es "test-create-ledger-demo". Utilizará este nombre para otros pasos.
- **Punto de conexión del libro de contabilidad**: en el ejemplo, este punto de conexión es `https://test-create-ledger-demo.confidential-ledger.azure.net/`. 

Necesitará estos nombres de propiedad para realizar transacciones con el libro de contabilidad confidencial desde el plano de datos.
 
## <a name="clean-up-resources"></a>Limpieza de recursos

Otros artículos sobre Azure Confidential basados en este inicio rápido. Si tiene pensado seguir trabajando con otros artículos, considere la posibilidad de dejar estos recursos activos. 

Cuando ya no lo necesite, elimine el grupo de recursos, lo que elimina el libro de contabilidad confidencial y los recursos relacionados. Para eliminar el grupo de recursos mediante el portal:

1.  Escriba el nombre del grupo de recursos en el cuadro de búsqueda de la parte superior del portal. Cuando vea el grupo de recursos que se utiliza en esta guía de inicio rápido en los resultados de búsqueda, selecciónelo.

1.  Seleccione **Eliminar grupo de recursos**.

1.  En el cuadro **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS**, escriba el nombre del grupo de recursos y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha creado una instancia de Confidential Ledger desde Azure Portal. Para más información sobre Azure Confidential Ledger y cómo integrarlo con las aplicaciones, continúe con los artículos siguientes.

- [Introducción a Microsoft Azure Confidential Ledger](overview.md)
