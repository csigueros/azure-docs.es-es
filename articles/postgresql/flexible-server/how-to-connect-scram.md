---
title: 'Conectividad mediante SCRAM en Azure Database for PostgreSQL: Servidor flexible'
description: 'Instrucciones e información sobre cómo configurar y conectarse mediante SCRAM en Azure Database for PostgreSQL: Servidor flexible.'
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/23/2021
ms.openlocfilehash: 7fc67ab7162c7fe62df226fd39aefcd350bbdf82
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112897289"
---
# <a name="scram-authentication-in-azure-database-for-postgresql---flexible-server"></a>Autenticación de SCRAM en Azure Database for PostgreSQL: Servidor flexible

> [!IMPORTANT]
> Azure Database for PostgreSQL: servidor flexible está en versión preliminar

Salted Challenge Response Authentication Mechanism (SCRAM) es un protocolo de autenticación mutua basado en contraseña. Se trata de un esquema de desafío-respuesta que agrega varios niveles de seguridad y evita el examen de contraseñas en conexiones que no son de confianza. SCRAM admite el almacenamiento de contraseñas en el servidor en un formato hash criptográfico que proporciona seguridad avanzada. 

Para acceder al servidor de bases de datos PostgreSQL mediante el método de autenticación SCRAM, las bibliotecas cliente deben admitir SCRAM.  Consulte la [lista de controladores](https://wiki.postgresql.org/wiki/List_of_drivers) que admiten SCRAM.

## <a name="configuring-scram-authentication"></a>Configuración de la autenticación SCRAM

1. Cambie password_encryption a SCRAM-SHA-256. Actualmente PostgreSQL solo admite SCRAM mediante SHA-256.
        :::image type="content" source="./media/how-to-configure-scram/1-password-encryption.png" alt-text="Habilitación del cifrado de contraseñas SCRAM"::: 
2. Permita SCRAM-SHA-256 como método de autenticación.
        :::image type="content" source="./media/how-to-configure-scram/2-auth-method.png" alt-text="Elección del método de autenticación"::: 
    >[!Important]
    > Puede optar por aplicar solo la autenticación SCRAM si selecciona únicamente el método SCRAM-SHA-256. Al hacerlo, los usuarios con autenticación MD5 ya no podrán conectarse al servidor. Por tanto, antes de aplicar SCRAM, se recomienda tener MD5 y SCRAM-SHA-256 como métodos de autenticación hasta que actualice todas las contraseñas de usuario a SCRAM-SHA-256. Puede comprobar el tipo de autenticación para los usuarios mediante la consulta mencionada en el paso 7.
3. Guarde los cambios. Se trata de propiedades dinámicas y no es necesario reiniciar el servidor.
4. Desde el cliente de Postgres, conéctese al servidor de Postgres. Por ejemplo,
   
    ```bash
    psql "host=myPGServer.postgres.database.azure.com port=5432 dbname=postgres user=myDemoUser password=MyPassword sslmode=require"

    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 12.6)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.
    ```

5. Compruebe el cifrado de contraseña.
   
    ```SQL
    postgres=> show password_encryption;
     password_encryption
    ---------------------
    scram-sha-256
    (1 row)
    ```

6. Después, puede actualizar la contraseña de los usuarios.

    ```SQL
    postgres=> \password myDemoUser
    Enter new password:
    Enter it again:
    postgres=>
    ```

7. Puede comprobar los tipos de autenticación de usuario mediante la función `azure_roles_authtype()`. 

    ``` SQL
    postgres=> SELECT * from azure_roles_authtype();
            rolename          | authtype
    ---------------------------+-----------
    azuresu                   | NOLOGIN
    pg_monitor                | NOLOGIN
    pg_read_all_settings      | NOLOGIN
    pg_read_all_stats         | NOLOGIN
    pg_stat_scan_tables       | NOLOGIN
    pg_read_server_files      | NOLOGIN
    pg_write_server_files     | NOLOGIN
    pg_execute_server_program | NOLOGIN
    pg_signal_backend         | NOLOGIN
    replication               | NOLOGIN
    myDemoUser                | SCRAM-256
    azure_pg_admin            | NOLOGIN
    srtest                    | SCRAM-256
    sr_md5                    | MD5
    (14 rows)
    ```

8. Después, puede conectarse al servidor desde el cliente que admite la autenticación SCRAM.

## <a name="next-steps"></a>Pasos siguientes
- [Creación y administración de redes virtuales en Servidor flexible de Azure Database for PostgreSQL mediante la CLI de Azure](./how-to-manage-virtual-network-cli.md).
- Obtenga más información acerca de las [redes en Azure Database for PostgreSQL: Servidor flexible](./concepts-networking.md).
- Información más detallada sobre las [Reglas de firewall de Azure Database for PostgreSQL: Servidor flexible](./concepts-networking.md#public-access-allowed-ip-addresses).
