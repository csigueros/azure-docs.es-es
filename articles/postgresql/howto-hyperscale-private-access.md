---
title: 'Habilitación del acceso privado (versión preliminar) en Azure Database for PostgreSQL: Hiperescala (Citus)'
description: 'Configuración de un vínculo privado en un grupo de servidores en Azure Database for PostgreSQL: Hiperescala (Citus)'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/15/2021
ms.openlocfilehash: e7694cd55eb6b6da7adb0313de47575880854cc5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074961"
---
# <a name="private-access-preview-in-azure-database-for-postgresql-hyperscale-citus"></a>Acceso privado (versión preliminar) en Azure Database for PostgreSQL: Hiperescala (Citus)

El [acceso privado](concepts-hyperscale-private-access.md) (versión preliminar) permite que los recursos de una red virtual de Azure se conecten de forma segura y privada a los nodos de un grupo de servidores de Hiperescala (Citus). En este procedimiento se da por supuesto que ya ha creado una red virtual y una subred. Para ver un ejemplo de configuración de los requisitos previos, consulte el [tutorial sobre el acceso privado](tutorial-hyperscale-private-access.md).

## <a name="create-a-server-group-with-a-private-endpoint"></a>Creación de un grupo de servidores con un punto de conexión privado

1. Haga clic en **Crear un recurso** en la esquina superior izquierda de Azure Portal.

2. En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **Azure Database for PostgreSQL**.

3. Para la opción de implementación, seleccione el botón **Crear** en **Grupo de servidores de hiperescala (Citus)**.

4. Rellene el nuevo formulario de detalles del servidor con el grupo de recursos, el nombre deseado del grupo de servidores, la ubicación y la contraseña del usuario de la base de datos.

5. Seleccione **Configurar grupo de servidores**, elija el plan deseado y seleccione **Guardar**.

6. Seleccione **Siguiente: Redes** en la parte inferior de la página.

7. Seleccione **Acceso privado (versión preliminar)**.

    > [!NOTE]
    >
    > El acceso privado está disponible para la versión preliminar solo en [determinadas regiones](concepts-hyperscale-limits.md#regions).
    >
    > Si la opción de acceso privado no se puede seleccionar para el grupo de servidores aunque está dentro de una región permitida, abra una [solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de Azure e incluya su identificador de suscripción de Azure para obtener acceso.

8. Aparece una pantalla llamada **Crear un punto de conexión privado**. Elija los valores adecuados para los recursos existentes y haga clic en **Aceptar**:

    - **Grupos de recursos**
    - **Ubicación**
    - **Nombre**
    - **Recurso secundario de destino**
    - **Red virtual**
    - **Subred**
    - **Integración con una zona DNS privada**

9. Después de crear el punto de conexión privado, seleccione **Revisar y crear** para crear el grupo de servidores de Hiperescala (Citus).

## <a name="enable-private-access-on-an-existing-server-group"></a>Habilitación de acceso privado en un grupo de servidores existente

Para crear un punto de conexión privado para un nodo de un grupo de servidores existente, abra la página **Redes** del grupo de servidores.

1. Seleccione **+ Agregar punto de conexión privado**.

   :::image type="content" source="media/howto-hyperscale-private-access/networking.png" alt-text="Pantalla Redes":::

1. En la pestaña **Aspectos básicos**, confirme los valores de **Suscripción**, **Grupo de recursos** y **Región**. Escriba un **nombre** para el punto de conexión privado, por ejemplo, `my-server-group-eq`.

    > [!NOTE]
    >
    > Se recomienda seleccionar una suscripción y una región que coincidan con las del grupo de servidores, a menos que tenga una buena razón para no hacerlo.  Puede que los valores predeterminados de los campos de formulario no sean correctos; compruébelos y actualícelos si es necesario.

2. Seleccione **Siguiente: Recurso >**. En **Subrecurso de destino**, elija el nodo de destino del grupo de servidores. Por lo general, el nodo deseado es `coordinator`.

3. Seleccione **Siguiente: Configuración >** . Elija los valores deseados para **Red virtual** y **Subred**. Personalice el valor de **Integración de DNS privado** o acepte su configuración predeterminada.

4. Seleccione **Siguiente: Etiquetas** y agregue las etiquetas que prefiera.

5. Por último, seleccione **Revisar y crear**. Revise la configuración y, cuando esté satisfecho, seleccione **Crear**.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre el [acceso privado](concepts-hyperscale-private-access.md) (versión preliminar).
* Siga un [tutorial](tutorial-hyperscale-private-access.md) para ver el acceso privado (versión preliminar) en acción.
