---
title: Copia de seguridad de Azure Database for PostgreSQL
description: Más información sobre la copia de seguridad de Azure Database for PostgreSQL con retención a largo plazo (versión preliminar)
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 25304aee2a759b55b8b3139aa2ae57511c967595
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389881"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Copia de seguridad de Azure Database for PostgreSQL con retención a largo plazo (versión preliminar)

En este artículo, se describe cómo hacer una copia de seguridad del servidor de Azure Database for PostgreSQL.

## <a name="configure-backup-on-azure-postgresql-databases"></a>Configuración de la copia de seguridad en bases de datos de Azure PostgreSQL

Puede configurar la copia de seguridad de varias bases de datos en varios servidores de Azure PostgreSQL. Para configurar la copia de seguridad en las bases de datos de Azure Database for PostgreSQL mediante Azure Backup, siga estos pasos:

1. Vaya a **Almacén de copia de seguridad** ->  **+Copia de seguridad**.

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-inline.png" alt-text="Captura de pantalla que muestra la opción para agregar una copia de seguridad." lightbox="./media/backup-azure-database-postgresql/adding-backup-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-details-inline.png" alt-text="Captura de pantalla que muestra la opción para agregar información de copia de seguridad." lightbox="./media/backup-azure-database-postgresql/adding-backup-details-expanded.png":::

   También puede navegar a esta página desde el [Centro de copias de seguridad](/azure/backup/backup-center-overview). 

1. Seleccione [Create a Backup Policy](#create-backup-policy) (Crear una directiva de copia de seguridad), que define la programación de la copia de seguridad y la duración de la retención.

   :::image type="content" source="./media/backup-azure-database-postgresql/create-or-add-backup-policy-inline.png" alt-text="Captura de pantalla que muestra la opción para agregar una directiva de copia de seguridad." lightbox="./media/backup-azure-database-postgresql/create-or-add-backup-policy-expanded.png":::

1. **Seleccionar las bases de datos de Azure Database for PostgreSQL de las que se va a hacer una copia de seguridad**: elija uno de los servidores de Azure Database for PostgreSQL entre las suscripciones si se encuentra en la misma región que el almacén. Expanda la flecha para ver la lista de bases de datos dentro de un servidor.

   :::image type="content" source="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-inline.png" alt-text="Captura de pantalla que muestra la opción para seleccionar una base de datos PostgreSQL de Azure." lightbox="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-inline.png" alt-text="Captura de pantalla que muestra cómo elegir un servidor de Azure Database for PostgreSQL." lightbox="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-expanded.png":::


1. **Asigne un almacén de claves de Azure** que almacene las credenciales para conectarse a la base de datos seleccionada. Para asignar el almacén de claves en el nivel de fila individual, haga clic en **Select a key vault and secret** (Seleccionar un almacén de claves y un secreto). También puede asignar el almacén de claves seleccionando varias filas y haciendo clic en Assign key vault (Asignar almacén de claves) en el menú superior de la cuadrícula. 

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-azure-key-vault-inline.png" alt-text="Captura de pantalla que muestra cómo asignar un almacén de claves de Azure." lightbox="./media/backup-azure-database-postgresql/assign-azure-key-vault-expanded.png"::: 

1. Para especificar la información del secreto, use una de las siguientes opciones: 

   1. **Escribir el identificador URI del secreto**: use esta opción si el identificador URI del secreto es compartido o conocido por usted. Puede copiar el **identificador URI del secreto desde el almacén de claves** -> **Secretos (seleccione un secreto)**  -> **Identificador secreto**.

      :::image type="content" source="./media/backup-azure-database-postgresql/enter-secret-uri-inline.png" alt-text="Captura de pantalla en la que se muestra cómo especificar el identificador URI del secreto." lightbox="./media/backup-azure-database-postgresql/enter-secret-uri-expanded.png":::  

      Sin embargo, con esta opción, Azure Backup no obtiene visibilidad sobre el almacén de claves al que se ha hecho referencia. Por lo tanto, no se pueden conceder en línea los permisos de acceso al almacén de claves. El administrador de copias de seguridad y el administrador de Postgres y el almacén de claves deben asegurarse de que el [acceso al almacén de claves del almacén de copia de seguridad se conceda manualmente](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server) fuera del flujo de configuración de copia de seguridad para que la operación de copia de seguridad se realice correctamente.

   1. **Seleccionar el almacén de claves**: use esta opción si conoce el nombre del secreto y el almacén de claves. Con esta opción, usted (administrador de copias de seguridad con acceso de escritura al almacén de claves) puede conceder en línea los permisos de acceso al almacén de claves. El almacén de claves y el secreto pueden existir previamente o crearse sobre la marcha. Asegúrese de que el secreto sea la cadena de conexión del servidor PG en formato ADO.net actualizado con las credenciales del usuario de base de datos al que se han concedido los privilegios de "copia de seguridad" en el servidor. Más información sobre cómo crear los [secretos en el almacén de claves](#create-secrets-in-the-key-vault).

      :::image type="content" source="./media/backup-azure-database-postgresql/assign-secret-store-inline.png" alt-text="Captura de pantalla en la que se muestra cómo asignar un almacén de secretos." lightbox="./media/backup-azure-database-postgresql/assign-secret-store-expanded.png":::

      :::image type="content" source="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-inline.png" alt-text="Captura de pantalla que muestra la selección del secreto de Azure Key Vault." lightbox="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-expanded.png":::   

1. Cuando se completa la actualización de la información del secreto, la validación se inicia una vez actualizada la información del almacén de claves. En este caso, el servicio de copia de seguridad valida si tiene todos los [permisos de acceso](backup-azure-database-postgresql-overview.md#key-vault-based-authentication-model) necesarios para leer los detalles del secreto del almacén de claves y conectarse a la base de datos. Si faltan uno o varios permisos de acceso, se mostrará uno de estos mensajes de error: _Role assignment not done (Asignación de roles no realizada) o User cannot assign roles (El usuario no puede asignar roles)_ .

   :::image type="content" source="./media/backup-azure-database-postgresql/validation-of-secret-inline.png" alt-text="Captura de pantalla que muestra la validación del secreto." lightbox="./media/backup-azure-database-postgresql/validation-of-secret-expanded.png":::   

   1. **User cannot assign roles** (El usuario no puede asignar roles): este mensaje se muestra cuando usted (el administrador de copias de seguridad) no tiene acceso de escritura en el servidor postgreSQL o en el almacén de claves para asignar los permisos que faltan, como se muestra en **View details** (Ver detalles). Descargue la plantilla de asignación desde el botón de acción y haga que la ejecuten el administrador de PostgreSQL o del almacén de claves. Se trata de una plantilla de ARM que le ayuda a asignar los permisos necesarios en los recursos necesarios. Una vez que la plantilla se ejecute correctamente, haga clic en **Re-validate** (Volver a validar) en la página Configure Backup (Configurar copia de seguridad).

      :::image type="content" source="./media/backup-azure-database-postgresql/download-role-assignment-template-inline.png" alt-text="Captura de pantalla que muestra la opción para descargar la plantilla de asignación de roles." lightbox="./media/backup-azure-database-postgresql/download-role-assignment-template-expanded.png":::    

   1. **Role assignment not done** (Asignación de roles no realizada): este mensaje se muestra cuando usted (el administrador de copias de seguridad) tiene acceso de escritura en el servidor postgreSQL o en el almacén de claves para asignar los permisos que faltan, como se muestra en **View details** (Ver detalles). Use el botón de acción **Assign missing roles** (Asignar roles que faltan) del menú de acciones superior para conceder en línea los permisos en el servidor postgreSQL y en el almacén de claves.

      :::image type="content" source="./media/backup-azure-database-postgresql/role-assignment-not-done-inline.png" alt-text="Captura de pantalla que muestra el mensaje de error sobre la asignación de roles no realizada." lightbox="./media/backup-azure-database-postgresql/role-assignment-not-done-expanded.png":::     

1. Seleccione **Assign missing roles** (Asignar roles que faltan) en el menú superior y asigne los roles. Una vez que se inicia el proceso, se conceden al almacén de copia de seguridad los [permisos de acceso que faltan](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) en el almacén de claves o el servidor PG. Puede definir el ámbito en el que se deben conceder los permisos de acceso. Una vez completada la acción, se inicia una nueva validación.

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-missing-roles-inline.png" alt-text="Captura de pantalla que muestra la opción para asignar los roles que faltan." lightbox="./media/backup-azure-database-postgresql/assign-missing-roles-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/define-scope-of-access-permission-inline.png" alt-text="Captura de pantalla que muestra cómo definir el ámbito del permiso de acceso." lightbox="./media/backup-azure-database-postgresql/define-scope-of-access-permission-expanded.png":::     

   - El almacén de copia de seguridad accede a los secretos del almacén de claves y ejecuta una conexión de prueba a la base de datos para validar si las credenciales se han especificado correctamente. También se comprueban los privilegios del usuario de base de datos para ver [si el usuario de base de datos tiene permisos relacionados con la copia de seguridad en la base de datos](backup-azure-database-postgresql-overview.md#database-users-backup-privileges-on-the-database).

   - El administrador de PostgreSQL tendrá todos los permisos de copia de seguridad y restauración en la base de datos de manera predeterminada. Por lo tanto, las validaciones se realizarán correctamente.
   - Es posible que un usuario con pocos privilegios no tenga permisos de copia de seguridad y restauración en la base de datos. Por lo tanto, se produciría un error en las validaciones. Se genera dinámicamente un script de PowerShell (uno por cada registro o base de datos seleccionada). [Ejecute el script de PowerShell para conceder estos privilegios al usuario de base de datos en la base de datos](#create-secrets-in-the-key-vault). Como alternativa, puede asignar estos privilegios mediante PG admin o la herramienta PSQL.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-inline.png" alt-text="Captura de pantalla que muestra los secretos de acceso del almacén de copia de seguridad desde el almacén de claves." lightbox="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/run-test-connection.png" alt-text="Captura de pantalla que muestra el proceso para iniciar la conexión de prueba.":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-inline.png" alt-text="Captura de pantalla que muestra cómo proporcionar las credenciales de usuario para ejecutar la prueba." lightbox="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-expanded.png":::      

1. Mantenga los registros con el estado de preparación para la copia de seguridad establecido como Correcto para continuar con el último paso de envío de la operación.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-readiness-as-success-inline.png" alt-text="Captura de pantalla que muestra que la preparación para la copia de seguridad es correcta." lightbox="./media/backup-azure-database-postgresql/backup-readiness-as-success-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/review-backup-configuration-details-inline.png" alt-text="Captura de pantalla que muestra la página de revisión de la configuración de copia de seguridad." lightbox="./media/backup-azure-database-postgresql/review-backup-configuration-details-expanded.png":::      

1. Envíe la operación de configuración de copia de seguridad y realice un seguimiento del progreso en **Backup instances** (Instancias de Backup).

   :::image type="content" source="./media/backup-azure-database-postgresql/submit-configure-backup-operation-inline.png" alt-text="Captura de pantalla que muestra el envío de la configuración de copia de seguridad y el seguimiento del progreso." lightbox="./media/backup-azure-database-postgresql/submit-configure-backup-operation-expanded.png":::      

## <a name="create-backup-policy"></a>Crear directiva de copia de seguridad

Puede crear una directiva de copia de seguridad sobre la marcha durante la configuración del flujo de copia de seguridad. Como alternativa, vaya a **Centro de copias de seguridad** -> **Directivas de Backup** -> **Agregar**.

1. En Nombre, escriba un nombre para la nueva directiva.

   :::image type="content" source="./media/backup-azure-database-postgresql/enter-name-for-new-policy-inline.png" alt-text="Captura de pantalla que muestra el proceso para especificar un nombre para la nueva directiva." lightbox="./media/backup-azure-database-postgresql/enter-name-for-new-policy-expanded.png":::

1. Defina la programación de las copias de seguridad. Actualmente, solo está disponible la opción de copia de seguridad semanal. Sin embargo, puede programar las copias de seguridad varios días de la semana.

1. Defina la configuración de **Retención**. Puede agregar una o varias reglas de retención. En cada regla de retención se suponen entradas para copias de seguridad específicas, así como el almacén de datos y el tiempo que se retienen esas copias de seguridad.

1. Para almacenar las copias de seguridad en uno de los dos almacenes de datos (o niveles), elija **Backup data store** (Almacén de datos de copia de seguridad) (nivel Estándar) o **Archive data store** (Almacén de datos de archivo) (en versión preliminar).

1. Elija la opción **On-expiry** (Al expirar) para mover la copia de seguridad al almacén de datos de archivo cuando expire en el almacén de datos de copia de seguridad.

   Si no hay ninguna otra regla de retención, se aplica la **regla de retención predeterminada**, que a su vez tiene un valor predeterminado de tres meses.

   - La duración de la retención oscila entre siete días y diez años en el **almacén de datos de copia de seguridad**.
   - La duración de la retención oscila entre seis meses y diez años en el **almacén de datos de archivo**.

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-inline.png" alt-text="Captura de pantalla que muestra el paso 5 para elegir la opción Al expirar para mover la copia de seguridad al almacén de datos de archivo tras su expiración." lightbox="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-expanded.png":::

>[!Note]
>Las reglas de retención se evalúan en un orden de prioridad predeterminado. La prioridad más alta es para la regla anual, seguida de la regla mensual y, luego, la semanal. La configuración de referencia predeterminada se aplica en caso de que no haya ninguna otra regla que cumpla los requisitos. Por ejemplo, el mismo punto de recuperación puede ser la primera copia de seguridad correcta que se realiza cada semana, así como la primera copia de seguridad correcta realizada cada mes. Sin embargo, dado que la prioridad de la regla mensual es mayor que la de la regla semanal, se aplica la retención correspondiente a la primera copia de seguridad correcta realizada cada mes.
## <a name="create-secrets-in-the-key-vault"></a>Creación de los secretos en el almacén de claves

El secreto es la cadena de conexión del servidor PG en formato _ADO.net_ actualizado con las credenciales del usuario de base de datos al que se han concedido los privilegios de **copia de seguridad** en el servidor. Copie la cadena de conexión del servidor PG y edítela en un editor de texto para actualizar el _identificador de usuario y la contraseña_. 

:::image type="content" source="./media/backup-azure-database-postgresql/pg-server-connection-string-inline.png" alt-text="Captura de pantalla que muestra la cadena de conexión del servidor PG como un secreto." lightbox="./media/backup-azure-database-postgresql/pg-server-connection-string-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/create-secret-inline.png" alt-text="Captura de pantalla que muestra la opción para crear un secreto de cadena de conexión de servidor PG." lightbox="./media/backup-azure-database-postgresql/create-secret-expanded.png":::

## <a name="run-powershell-script-to-grant-privileges-to-database-users"></a>Ejecución de un script de PowerShell para conceder privilegios a los usuarios de base de datos

El script de PowerShell generado dinámicamente durante la configuración de la copia de seguridad acepta el usuario de base de datos como entrada, junto con las credenciales del administrador de PG, para conceder los privilegios relacionados con la copia de seguridad al usuario de base de datos en la base de datos.

La [herramienta PSQL](https://www.enterprisedb.com/download-postgresql-binaries) debe estar presente en la máquina y se debe establecer correctamente la variable de entorno PATH en la ruta de acceso de la herramienta PSQL.

:::image type="content" source="./media/backup-azure-database-postgresql/psql-set-environment-inline.png" alt-text="Captura de pantalla que muestra la opción para buscar la aplicación de configuración del entorno." lightbox="./media/backup-azure-database-postgresql/psql-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/system-properties-to-set-environment-inline.png" alt-text="Captura de pantalla que muestra la opción para establecer el entorno en Propiedades del sistema." lightbox="./media/backup-azure-database-postgresql/system-properties-to-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/adding-environment-variables-inline.png" alt-text="Captura de pantalla que muestra las variables de entorno predeterminadas." lightbox="./media/backup-azure-database-postgresql/adding-environment-variables-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/editing-environment-variables-inline.png" alt-text="Captura de pantalla que muestra las variables de entorno que debe establecer." lightbox="./media/backup-azure-database-postgresql/editing-environment-variables-expanded.png":::

Asegúrese de que la **configuración de seguridad de la conexión** de la instancia de Azure Database for PostgreSQL incluya en la lista de permitidos la dirección IP de la máquina para permitir la conectividad de red.

## <a name="generate-an-on-demand-backup"></a>Generación de una copia de seguridad a petición

Para desencadenar una copia de seguridad fuera de la programación especificada en la directiva, vaya a **Instancias de copia de seguridad** -> **Hacer copia de seguridad ahora**.
Elija una regla de retención en la lista de las que se definieron en la directiva de Backup asociada.

:::image type="content" source="./media/backup-azure-database-postgresql/navigate-to-retention-rules-inline.png" alt-text="Captura de pantalla que muestra la opción para ir a la lista de reglas de retención que se definieron en la directiva de copia de seguridad asociada." lightbox="./media/backup-azure-database-postgresql/navigate-to-retention-rules-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/choose-retention-rules-inline.png" alt-text="Captura de pantalla que muestra la opción elegir las reglas de retención que se definieron en la directiva de copia de seguridad asociada." lightbox="./media/backup-azure-database-postgresql/choose-retention-rules-expanded.png":::

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas de la copia de seguridad de base de datos de PostgreSQL con Azure Backup](backup-azure-database-postgresql-troubleshoot.md)
