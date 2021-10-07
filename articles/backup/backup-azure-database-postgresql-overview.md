---
title: Acerca de la copia de seguridad de Azure Database for PostgreSQL
description: Introducción a la copia de seguridad de Azure Database for PostgreSQL (versión preliminar)
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: references_regions
ms.openlocfilehash: 3740d4c7d149181638da93a3a5ad713c2c230f29
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154420"
---
# <a name="about-azure-database-for-postgresql-backup-preview"></a>Acerca de la copia de seguridad de Azure Database for PostgreSQL (versión preliminar)

Azure Backup y los servicios de base de datos de Azure se han unido para crear una solución de copia de seguridad de clase empresarial para servidores de Azure Database for PostgreSQL que conserve las copias de seguridad hasta 10 años. Además de la retención a largo plazo, la solución ofrece las siguientes funcionalidades:

- Copia de seguridad a petición y programada controlada por el cliente en el nivel de base de datos individual.
- Restauraciones en el nivel de base de datos en cualquier servidor PostgreSQL o en cualquier almacenamiento de blobs.
- Supervisión central de todas las operaciones y trabajos.
- Las copias de seguridad se almacenan en dominios de seguridad y de error independientes. Si el servidor de origen o la suscripción están en peligro por cualquier circunstancia, las copias de seguridad permanecen seguras en el [almacén de Backup](/azure/backup/backup-vault-overview) (en cuentas de almacenamiento administradas por Azure Backup).
- El uso de **pg_dump** permite una mayor flexibilidad en las restauraciones. Esto le ayuda a restaurar entre versiones de base de datos. 

Puede usar esta solución de forma independiente o sumada a la [solución de copia de seguridad nativa que ofrece Azure Database for PostgreSQL](/azure/postgresql/concepts-backup), con una retención de hasta 35 días. La solución nativa es adecuada para las recuperaciones operativas, como la recuperación a partir de las copias de seguridad más recientes. La solución de Azure Backup le ayuda a satisfacer sus necesidades de cumplimiento y ofrece procesos de copia de seguridad y restauración más detallados y flexibles.

## <a name="support-matrix"></a>Matrices compatibles

|Soporte técnico  |Detalles  |
|---------|---------|
|Implementaciones admitidas   |  [Azure Database for PostgreSQL: Servidor único](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Regiones de Azure compatibles |  Este de EE. UU., Este de EE. UU. 2, Centro de EE. UU., Centro-sur de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, Centro-oeste de EE. UU., Sur de Brasil, Centro de Canadá, Norte de Europa, Oeste de Europa, Sur de Reino Unido, Oeste de Reino Unido, Centro-oeste de Alemania, Norte de Suiza, Oeste de Suiza, Este de Asia, Sudeste de Asia, Este de Japón, Oeste de Japón, Centro de Corea del Sur, Sur de Corea del Sur, Centro de la India, Este de Australia, Centro de Australia, Centro de Australia 2 y Norte de Emiratos Árabes Unidos  |
|Versiones admitidas de Azure PostgreSQL    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Consideraciones y limitaciones de las características

- Las operaciones solo se admiten desde Azure Portal. 
- El límite recomendado como tamaño máximo de la base de datos es de 400 GB.
- No se admite la copia de seguridad entre regiones. Por lo tanto, no se puede realizar una copia de seguridad de un servidor de Azure Database for PostgreSQL en un almacén de otra región. Del mismo modo, solo puede restaurar una copia de seguridad en un servidor que se encuentre dentro de la misma región que el almacén. Sin embargo, se admite la copia de seguridad y restauración entre suscripciones. 
- Solo se recuperan los datos durante la restauración; no se restauran los "roles".
- En la versión preliminar, se recomienda ejecutar la solución solo en el entorno de prueba.

## <a name="backup-process"></a>Proceso de copia de seguridad

1. Como administrador de copia de seguridad, puede especificar las bases de datos de Azure Database for PostgreSQL de las que va a realizar una copia de seguridad. Además, también puede especificar los detalles del almacén de claves de Azure que almacena las credenciales necesarias para conectarse a las bases de datos especificadas. El administrador de la base de datos inicializa estas credenciales de forma segura en Azure Key Vault.
1. A continuación, el servicio de copia de seguridad valida si tiene los [permisos adecuados para autenticarse](#azure-backup-authentication-with-the-postgresql-server) en el servidor PostgreSQL especificado y realizar copias de seguridad de sus bases de datos.
1. Azure Backup inicia un rol de trabajo (VM) con una extensión de copia de seguridad instalada para comunicarse con el servidor PostgreSQL protegido. Esta extensión consta de un coordinador y un complemento de PostgreSQL. El coordinador desencadena flujos de trabajo para varias operaciones, como la copia de seguridad y la restauración, y el complemento administra el flujo de datos real.
1. A la hora programada, el coordinador se comunica con el complemento para que empiece a transmitir los datos de la copia de seguridad desde el servidor PostgreSQL mediante **pg_dump (personalizado)** .
1. El complemento envía los datos directamente a las cuentas de almacenamiento administradas por Azure Backup (enmascaradas por el almacén de Backup), lo que elimina la necesidad de una ubicación de almacenamiento provisional. El servicio Azure Backup cifra los datos con claves administradas por Microsoft y los almacena en cuentas de almacenamiento.

 :::image type="content" source="./media/backup-azure-database-postgresql-overview/backup-process.png" alt-text="Diagrama que muestra el proceso de copia de seguridad.":::

## <a name="azure-backup-authentication-with-the-postgresql-server"></a>Autenticación de Azure Backup con el servidor PostgreSQL

Azure Backup sigue estrictas directrices de seguridad establecidas por Azure; no se asumen los permisos en el recurso del que se va a realizar una copia de seguridad y el usuario debe concederlos explícitamente. 

### <a name="key-vault-based-authentication-model"></a>Modelo de autenticación basado en almacén de claves

El servicio Azure Backup debe conectarse a Azure Database for PostgreSQL mientras se hace cada copia de seguridad. Aunque se usa el "nombre de usuario y contraseña" (o una cadena de conexión) correspondientes a la base de datos para realizar esta conexión, estas credenciales no se almacenan con Azure Backup. En su lugar, el administrador de la base de datos debe inicializar estas credenciales de forma segura en [Azure Key Vault como un secreto](/azure/key-vault/secrets/about-secrets). El administrador de la carga de trabajo es responsable de administrar y rotar las credenciales. Azure Backup obtiene los detalles más recientes del secreto del almacén de claves para hacer la copia de seguridad.
 
:::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-based-authentication-model.png" alt-text="Diagrama que muestra el flujo de la carga de trabajo o la base de datos.":::

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-backup"></a>Conjunto de permisos necesarios para la copia de seguridad de base de datos de Azure Database for PostgreSQL

1. Conceda los siguientes permisos de acceso a la MSI del almacén de Backup:

   - Acceso de _Lector_ en el servidor de Azure Database for PostgreSQL.
   - Acceso de _Usuario de secretos de Key Vault_ (u obtener y enumerar secretos) en el almacén de claves de Azure.

1. Acceso a la línea de visión de red en:

   - Servidor de Azure Database for PostgreSQL: marca **Permitir el acceso a servicios de Azure** establecida en **Sí**.
   - Almacén de claves: marca **Permitir servicios de Microsoft de confianza** establecida en **Sí**.

1. Privilegios de copia de seguridad del usuario de base de datos en la base de datos

>[!Note]
>Puede conceder estos permisos dentro del flujo de [configuración de copia de seguridad](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases) con un solo clic si (el administrador de copia de seguridad) tiene acceso de "escritura" en los recursos previstos, o bien usar una plantilla de ARM si no tiene los permisos necesarios (cuando hay varios roles implicados). 

#### <a name="set-of-permissions-needed-for-azure-postgresql-database-restore"></a>Conjunto de permisos necesarios para la restauración de base de datos de Azure Database for PostgreSQL

Los permisos para la restauración son similares a los necesarios para la copia de seguridad y debe conceder los permisos en el servidor PostgreSQL de destino y en el almacén de claves correspondiente. A diferencia de la configuración del flujo de copia de seguridad, la experiencia para conceder estos permisos en línea no está disponible actualmente. Por lo tanto, debe [conceder manualmente el acceso en el servidor Postgres y en el almacén de claves correspondiente](#grant-access-on-the-azure-postgresql-server-and-key-vault-manually).

Además, asegúrese de que el usuario de base de datos (correspondiente a las credenciales almacenadas en el almacén de claves) tiene los siguientes privilegios de restauración en la base de datos:

- ALTER USER username CREATEDB;
- Asigne el rol _azure_pg_admin_ al usuario de base de datos.

### <a name="azure-active-directory-based-authentication-model"></a>Modelo de autenticación basado en Azure Active Directory

Anteriormente, se había lanzado un modelo de autenticación diferente que se basaba completamente en Azure Active Directory (Azure AD). Sin embargo, ahora proporcionamos el nuevo modelo de autenticación basado en almacén de claves (como se explicó anteriormente) como una opción alternativa, lo que facilita el proceso de configuración. 

[Descargue este documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) para obtener un script automatizado y las instrucciones relacionadas para usar este modelo de autenticación. Este script concederá un conjunto de permisos adecuado para realizar copias de seguridad y restauraciones en un servidor de Azure Database for PostgreSQL.

>[!Note]
>Toda la nueva configuración de protección se llevará a cabo solo con el nuevo modelo de autenticación de almacén de claves. Sin embargo, todas las instancias de copia de seguridad existentes que configuraron la protección con la autenticación basada en Azure AD seguirán existiendo y se realizarán copias de seguridad periódicas. Para restaurar estas copias de seguridad, debe seguir la autenticación basada en Azure AD.

## <a name="grant-access-on-the-azure-postgresql-server-and-key-vault-manually"></a>Concesión manual de acceso en el servidor de Azure Database for PostgreSQL y el almacén de claves

Para conceder todos los permisos de acceso necesarios para Azure Backup, consulte las secciones siguientes:

### <a name="access-permissions-on-the-azure-postgresql-server"></a>Permisos de acceso en el servidor de Azure Database for PostgreSQL

1. Establezca el acceso de **Lector** de la MSI del almacén de Backup en el servidor de Azure Database for PostgreSQL.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-inline.png" alt-text="Captura de pantalla que muestra la opción para establecer el acceso de lector de la MSI del almacén de Backup en el servidor de Azure Database for PostgreSQL." lightbox="./media/backup-azure-database-postgresql-overview/set-reader-access-on-azure-postgresql-server-expanded.png":::

1. Acceso a la línea de visión de red en el servidor de Azure Database for PostgreSQL: establezca la marca "Permitir el acceso a servicios de Azure" en "Sí".

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-inline.png" alt-text="Captura de pantalla que muestra la opción de establecer el acceso a la línea de visión de red en el servidor de Azure Database for PostgreSQL." lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-azure-postgresql-server-expanded.png":::

### <a name="access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server"></a>Permisos de acceso en Azure Key Vault (asociados al servidor postgreSQL)

1. Establezca el acceso de **Usuario de secretos de Key Vault** de la MSI del almacén de Backup (u **obtener** y **enumerar** secretos) en el almacén de claves de Azure. Para asignar permisos, puede usar asignaciones de roles o directivas de acceso. No es necesario agregar el permiso con ambas opciones, ya que no ayuda.

   - Mediante la autorización del control de acceso basado en roles de Azure (RBAC de Azure) (es decir, el modelo de permisos se ha establecido en el control de acceso basado en roles de Azure):

     - En Control de acceso, conceda el acceso de _Usuario de secretos de Key Vault_ a la MSI del almacén de Backup en el almacén de claves. Los portadores de ese rol podrán leer secretos.
     - [Conceda permisos para que las aplicaciones accedan a una instancia de Azure Key Vault mediante Azure RBAC](/azure/key-vault/general/rbac-guide?tabs=azure-cli).

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-inline.png" alt-text="Captura de pantalla que muestra la opción para proporcionar acceso de usuario de secretos." lightbox="./media/backup-azure-database-postgresql-overview/key-vault-secrets-user-access-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-inline.png" alt-text="Captura de pantalla que muestra la opción para conceder a la MSI del almacén de Backup acceso de usuario de secretos en el almacén de claves." lightbox="./media/backup-azure-database-postgresql-overview/grant-permission-to-applications-azure-rbac-expanded.png":::  

   - Mediante directivas de acceso (es decir, el modelo de permisos se ha establecido en la directiva de acceso del almacén):

     - Establezca los permisos para obtener y enumerar secretos.
     - Más información sobre la [Asignación de una directiva de acceso de Azure Key Vault](/azure/key-vault/general/assign-access-policy?tabs=azure-portal)

     :::image type="content" source="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-inline.png" alt-text="Captura de pantalla que muestra la opción para conceder permiso con el modelo de permisos establecido en el modelo de directiva de acceso del almacén." lightbox="./media/backup-azure-database-postgresql-overview/permission-model-is-set-to-vault-access-policy-expanded.png":::  
 

1. Acceso a la línea de visión de red en el almacén de claves: establezca la marca **Permitir servicios de Microsoft de confianza** en **Sí**.

   :::image type="content" source="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-inline.png" alt-text="Captura de pantalla que muestra cómo establecer la marca Permitir servicios de Microsoft de confianza en Sí para el acceso a la línea de visión de red en el almacén de claves." lightbox="./media/backup-azure-database-postgresql-overview/network-line-of-sight-access-on-key-vault-expanded.png":::

### <a name="database-users-backup-privileges-on-the-database"></a>Privilegios de copia de seguridad del usuario de base de datos en la base de datos

Ejecute la siguiente consulta en la herramienta [PG admin](#using-the-pg-admin-tool) (reemplace _username_ por el identificador del usuario de base de datos):

```
DO $do$
DECLARE
sch text;
BEGIN
EXECUTE format('grant connect on database %I to %I', current_database(), 'username');
FOR sch IN select nspname from pg_catalog.pg_namespace
LOOP
EXECUTE format($$ GRANT USAGE ON SCHEMA %I TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL TABLES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON TABLES TO username $$, sch);
EXECUTE format($$ GRANT SELECT ON ALL SEQUENCES IN SCHEMA %I TO username $$, sch);
EXECUTE format($$ ALTER DEFAULT PRIVILEGES IN SCHEMA %I GRANT SELECT ON SEQUENCES TO username $$, sch);
END LOOP;
END;
```

## <a name="using-the-pg-admin-tool"></a>Uso de la herramienta PG admin

[Descargue la herramienta PG admin](https://www.pgadmin.org/download/) si aún no lo ha hecho. Puede conectarse al servidor de Azure Database for PostgreSQL mediante esta herramienta. Además, puede agregar bases de datos y nuevos usuarios a este servidor.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-inline.png" alt-text="Captura de pantalla que muestra el proceso para conectarse al servidor de Azure Database for PostgreSQL mediante la herramienta PG admin." lightbox="./media/backup-azure-database-postgresql-overview/connect-to-azure-postgresql-server-using-pg-admin-tool-expanded.png":::

Cree un nuevo servidor con el nombre que prefiera. Escriba el nombre de host o el nombre de dirección igual que el **nombre del servidor** que se muestra en la vista de recursos de Azure Database for PostgreSQL en Azure Portal.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-inline.png" alt-text="Captura de pantalla que muestra la opción de crear un nuevo servidor mediante la herramienta PG admin." lightbox="./media/backup-azure-database-postgresql-overview/create-new-server-using-pg-admin-tool-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-inline.png" alt-text="Captura de pantalla que muestra la opción para escribir el nombre de host o el nombre de dirección igual que el nombre del servidor." lightbox="./media/backup-azure-database-postgresql-overview/enter-host-name-or-address-name-same-as--server-name-expanded.png":::

Asegúrese de agregar la _dirección del identificador de cliente actual_ a las reglas de firewall para que se permita la conexión.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-inline.png" alt-text="Captura de pantalla que muestra el proceso para agregar la dirección del identificador de cliente actual a las reglas de firewall." lightbox="./media/backup-azure-database-postgresql-overview/add-current-client-id-address-to-firewall-rules-expanded.png":::

Puede agregar nuevas bases de datos y usuarios de base de datos al servidor. Para los usuarios de base de datos, agregue un nuevo **Inicio de sesión/Roles de grupo**. Asegúrese de que **Can login?** (¿Puede iniciar sesión?) esté establecido en **Sí**.

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-inline.png" alt-text="Captura de pantalla que muestra el proceso para agregar nuevas bases de datos y usuarios de base de datos al servidor." lightbox="./media/backup-azure-database-postgresql-overview/add-new-databases-and-database-users-to-server-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-inline.png" alt-text="Captura de pantalla que muestra el proceso para agregar un nuevo inicio de sesión o rol de grupo para los usuarios de base de datos." lightbox="./media/backup-azure-database-postgresql-overview/add-new-login-group-roles-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-inline.png" alt-text="Captura de pantalla que muestra la comprobación de que la opción para poder iniciar sesión esté establecida en Sí." lightbox="./media/backup-azure-database-postgresql-overview/set-can-login-to-yes-expanded.png":::

## <a name="next-steps"></a>Pasos siguientes

[Copia de seguridad de Azure Database for PostgreSQL](backup-azure-database-postgresql.md)
