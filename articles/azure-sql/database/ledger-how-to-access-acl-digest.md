---
title: Acceso a los resúmenes almacenados en Azure Confidential Ledger
description: Acceda a los resúmenes almacenados en Azure Confidential Ledger con el libro de contabilidad de Azure SQL Database.
ms.custom: references_regions
ms.date: 07/23/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: 7a5253879daf3aaa9551b91a91c38135d29be10e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730362"
---
# <a name="access-the-digests-stored-in-confidential-ledger"></a>Acceso a los resúmenes almacenados en Confidential Ledger

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Actualmente, el libro de contabilidad de Azure SQL Database se encuentra en versión preliminar pública y está disponible en el Oeste de Europa, Sur de Brasil y Centro-oeste de EE. UU.

En este artículo se muestra cómo acceder a un resumen de [libro de contabilidad de Azure SQL Database](ledger-overview.md) almacenado en [Azure Confidential Ledger](../../confidential-ledger/index.yml) para obtener garantías de integridad y seguridad de un extremo a otro. En este artículo se explica cómo obtener acceso a la información almacenada y comprobar su integridad.

## <a name="prerequisites"></a>Requisitos previos

- Python 2.7, 3.5.3 o versiones posteriores.
- Azure SQL Database con el libro de contabilidad habilitado. Si aún no ha creado una base de datos en SQL Database, consulte [Inicio rápido: Creación de una base de datos en Azure SQL Database con el libro de contabilidad habilitado](ledger-create-a-single-database-with-ledger-enabled.md).
- [Biblioteca cliente de Azure Confidential Ledger para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger).
- Una instancia en ejecución de [Confidential Ledger](../../confidential-ledger/index.yml).

## <a name="how-does-the-integration-work"></a>¿Cómo funciona la integración?

El servidor de Azure SQL calcula periódicamente los resúmenes de las [bases de datos del libro de contabilidad](ledger-overview.md#ledger-database) y los almacena en Confidential Ledger. En cualquier momento, puede validar la integridad de los datos. Descargue los resúmenes de Confidential Ledger y compárelos con los resúmenes almacenados en el libro de contabilidad de SQL Database. En los pasos siguientes se explica el proceso.

## <a name="1-find-the-digest-location"></a>1. Buscar la ubicación del resumen

> [!NOTE]
> La consulta devuelve más de una fila si se usaron varias instancias de Confidential Ledger para almacenar el resumen. Para cada fila, repita los pasos del 2 al 6 para descargar los resúmenes de todas las instancias de Confidential Ledger.

Use [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) para ejecutar la siguiente consulta. El resultado muestra el punto de conexión de la instancia de Confidential Ledger donde se almacenan los resúmenes.

```sql
SELECT * FROM sys.database_ledger_digest_locations WHERE path like '%.confidential-ledger.azure.com%'
```

## <a name="2-determine-the-subledgerid"></a>2. Determinar el valor subledgerid

Nos interesa el valor de la columna de ruta de acceso de la salida de la consulta. Consta de dos partes, es decir, `host name` y `subledgerid`. Por ejemplo, en la dirección URL `https://contoso-ledger.confidential-ledger.azure.com/sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`, `host name` es `https://contoso-ledger.confidential-ledger.azure.com` y `subledgerid` es `sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`. Usaremos esto en el paso 4 para descargar los resúmenes.

## <a name="3-obtain-an-azure-ad-token"></a>3. Obtener un token de Azure AD

La API de Confidential Ledger acepta un token de portador de Azure Active Directory (Azure AD) como identidad del autor de la llamada. Esta identidad necesita obtener acceso a Confidential Ledger a través de Azure Resource Manager durante el aprovisionamiento. Al habilitar el libro de contabilidad en SQL Database, se le asigna automáticamente acceso de administrador a Confidential Ledger. Para obtener un token, el usuario debe autenticarse mediante la [CLI de Azure](/cli/azure/install-azure-cli) con la misma cuenta que se usó con Azure Portal. Una vez que el usuario se ha autenticado, puede usar [AzureCliCredential](/python/api/azure-identity/azure.identity.azureclicredential) para recuperar un token de portador y llamar a la API de Confidential Ledger.

Inicie sesión en Azure AD mediante la identidad con acceso a Confidential Ledger.

```azure-cli
az login
```

Recupere el token de portador.

```python
from azure.identity import AzureCliCredential
credential = AzureCliCredential()
```

## <a name="4-download-the-digests-from-confidential-ledger"></a>4. Descargar los resúmenes de Confidential Ledger

El siguiente script de Python descarga los resúmenes de Confidential Ledger. El script usa la [biblioteca cliente de Confidential Ledger para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger).

```python
from azure.identity import AzureCliCredential
from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

ledger_id = "contoso-ledger"
identity_server_url = "https://identity.confidential-ledger.core.azure.com"
sub_ledger_id = "sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000"
ledger_host_url = f"https://{ledger_id}.confidential-ledger.azure.com"
initial_path = f"/app/transactions?api-version=0.1-preview&subLedgerId={sub_ledger_id}"

identity_client = ConfidentialLedgerIdentityServiceClient(identity_server_url)
network_identity = identity_client.get_ledger_identity(
    ledger_id=ledger_id
)

ledger_tls_cert_file_name = f"{ledger_id}_certificate.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)

credential = AzureCliCredential()
ledger_client = ConfidentialLedgerClient(
    endpoint=ledger_host_url, 
    credential=credential,
    ledger_certificate_path=ledger_tls_cert_file_name
)

ranged_result = ledger_client.get_ledger_entries(
    sub_ledger_id=sub_ledger_id
)

entries = 0

for entry in ranged_result:
    entries += 1
    print(f"\nTransaction id {entry.transaction_id} contents: {entry.contents}")

if entries == 0:
    print("\n***No digests are found for the supplied SubledgerID.")
else:
    print("\n***No more digests were found for the supplied SubledgerID.")
```

## <a name="5-download-the-digests-from-the-sql-server"></a>5. Descargar los resúmenes de SQL Server

> [!NOTE]
> Este paso es una manera de confirmar que los hash almacenados en el libro de contabilidad de SQL Database no han cambiado con el tiempo. Para obtener una auditoría completa de la integridad del libro de contabilidad de SQL Database, vea [Comprobación de una tabla de libro de contabilidad para detectar alteraciones](ledger-verify-database.md).

Use [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) para ejecutar la siguiente consulta. La consulta devuelve los resúmenes de los bloques de Genesis.

```sql
SELECT * FROM sys.database_ledger_blocks
```

## <a name="6-comparison"></a>6. Comparación

Compare el resumen recuperado de Confidential Ledger con el resumen devuelto de la base de datos de SQL Database con `block_id` como clave. Por ejemplo, el resumen de `block_id` = `1` es el valor de la columna `previous_block_hash` de la fila `block_id`= `2`. De forma similar, `block_id` = `3` es el valor de la columna `previous_block_id` de la fila `block_id` = `4`. Una discrepancia en el valor de hash es un indicador de una posible alteración de los datos.

Si sospecha que se han alterado los datos, consulte [Comprobación de una tabla de libro de contabilidad para detectar alteraciones](ledger-verify-database.md) para realizar una auditoría completa del libro de contabilidad de SQL Database.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al libro de contabilidad de Azure SQL Database](ledger-overview.md)
- [Libro de contabilidad de base de datos](ledger-database-ledger.md)
- [Administración de resúmenes y comprobación de la base de datos](ledger-digest-management-and-database-verification.md)
- [Tablas de solo adición del libro de contabilidad](ledger-append-only-ledger-tables.md)
- [Tablas actualizables del libro de contabilidad](ledger-updatable-ledger-tables.md)
- [Comprobación de una tabla de libro de contabilidad para detectar alteraciones](ledger-verify-database.md)
