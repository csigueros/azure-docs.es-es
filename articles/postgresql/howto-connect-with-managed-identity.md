---
title: 'Conexión con identidad administrada: Azure Database for PostgreSQL (servidor único)'
description: Obtenga información sobre la conexión y autenticación mediante identidad administrada para la autenticación con Azure Database for PostgreSQL.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7cc4b086a4ec9d89187345a66d6ae3e39c999ca0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232184"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Conexión con identidad administrada a Azure Database for PostgreSQL

Puede usar identidades administradas asignadas por el sistema y asignadas por el usuario para autenticarse en Azure Database for PostgreSQL. En este artículo se muestra cómo usar una identidad administrada asignada por el sistema para permitir el acceso de una máquina virtual (VM) de Azure a un servidor de Azure Database for PostgreSQL. Azure administra automáticamente las identidades administradas, las cuales le permiten autenticarse en servicios que admiten la autenticación de Azure AD sin necesidad de insertar credenciales en el código.

Aprenderá a:
- Conceder a la VM acceso a un servidor de Azure Database for PostgreSQL.
- Crear un usuario en la base de datos que represente la identidad asignada por el sistema de la máquina virtual.
- Obtener un token de acceso mediante la identidad de la VM y usarlo para consultar un servidor de Azure Database for PostgreSQL.
- Implementar la recuperación del token en una aplicación de ejemplo de C#.

## <a name="prerequisites"></a>Prerrequisitos

- Si no está familiarizado con la característica Managed Identities for Azure Resources, consulte esta [introducción](../../articles/active-directory/managed-identities-azure-resources/overview.md). Si no tiene una cuenta de Azure, [regístrese para obtener una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar la creación de recursos necesarios y la administración de roles, la cuenta debe tener permisos de "Propietario" en el ámbito adecuado (su suscripción o grupo de recursos). Si necesita ayuda con la asignación de roles, consulte [Asignación de roles de Azure para administrar el acceso a los recursos de una suscripción de Azure](../../articles/role-based-access-control/role-assignments-portal.md).
- Necesita la VM de Azure (por ejemplo, Ubuntu Linux en ejecución) que le gustaría usar para acceder a la base de datos mediante identidad administrada.
- Necesita un servidor de bases de datos de Azure Database for PostgreSQL que tenga configurada la [Autenticación de Azure AD](howto-configure-sign-in-aad-authentication.md).
- Para seguir el ejemplo de C#, complete primero la guía sobre la [conexión con C#](connect-csharp.md).

## <a name="creating-a-system-assigned-managed-identity-for-your-vm"></a>Creación de una identidad administrada asignada por el sistema para la máquina virtual

Use [az vm identity assign](/cli/azure/vm/identity/) con el comando `identity assign` para habilitar la identidad asignada por el sistema en una VM existente:

```azurecli-interactive
az vm identity assign -g myResourceGroup -n myVm
```

Recupere el identificador de aplicación de la identidad administrada asignada por el sistema (lo necesitará en los pasos siguientes):

```azurecli
# Get the client ID (application ID) of the system-assigned managed identity
az ad sp list --display-name obs-locdev-wus2 --query [*].appId --out tsv
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Creación de un usuario de PostgreSQL para la identidad administrada

Ahora, conéctese como usuario administrador de Azure AD a la base de datos PostgreSQL y ejecute las siguientes instrucciones SQL, pero reemplace `CLIENT_ID` por el identificador de cliente que recuperó para la identidad administrada asignada por el sistema:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

La identidad administrada ahora tiene acceso al autenticarse con el nombre de usuario `myuser` (reemplácelo por un nombre de su elección).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Recuperación del token de acceso desde Azure Instance Metadata Service

La aplicación ahora puede recuperar un token de acceso desde Azure Instance Metadata Service y usarlo para autenticarse en la base de datos.

Esta recuperación de token se realiza mediante una solicitud HTTP a `http://169.254.169.254/metadata/identity/oauth2/token` y el pase de los siguientes parámetros:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (que recuperó anteriormente)

Recibirá un resultado JSON que contiene un campo `access_token`: este valor de texto largo es el token de acceso de identidad administrada, que debe usar como contraseña al conectarse a la base de datos.

Con fines de prueba, puede ejecutar los siguientes comandos en el shell. Tenga en cuenta que necesita `curl`, `jq` y el cliente de `psql` instalado.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Ahora está conectado a la base de datos que configuró anteriormente.

## <a name="connecting-using-managed-identity-in-c"></a>Conexión mediante identidad administrada en C#

En esta sección se muestra cómo obtener un token de acceso mediante la identidad administrada asignada por el usuario de la VM y cómo usarlo para llamar a Azure Database for PostgreSQL. Azure Database for PostgreSQL admite de forma nativa la Autenticación de Azure AD, por lo que puede aceptar directamente los tokens de acceso obtenidos con identidades administradas para recursos de Azure. Al crear una conexión a PostgreSQL, se pasa el token de acceso en el campo de contraseña.

Este es un ejemplo de código .NET para abrir una conexión a PostgreSQL con un token de acceso. Este código debe ejecutarse en la máquina virtual para usar la identidad administrada asignada por el sistema para obtener un token de acceso de Azure AD. Reemplace los valores de HOST, USER, DATABASE y CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;
using Azure.Identity;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        //private static string ClientId = "CLIENT_ID";

        static async Task Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure AD...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                var sqlServerTokenProvider = new DefaultAzureCredential();
                accessToken = (await sqlServerTokenProvider.GetTokenAsync(
                    new Azure.Core.TokenRequestContext(scopes: new string[] { "https://ossrdbms-aad.database.windows.net/.default" }) { })).Token;

            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4}; SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Cuando se ejecute, este comando proporcionará una salida similar a la siguiente:

```
Getting access token from Azure AD...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.11, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Pasos siguientes

* Revisión de los conceptos generales para la [Autenticación de Azure Active Directory con Azure Database for PostgreSQL](concepts-aad-authentication.md)
