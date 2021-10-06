---
title: Migración mediante Azure Portal (de Data Lake Storage Gen1 a Gen2)
description: Puede simplificar la tarea de migración entre Azure Data Lake Storage Gen1 y Azure Data Lake Storage Gen2 mediante Azure Portal.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 07/13/2021
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3d78ba06112fce8dff64f2091e434d8f502634d2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662749"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2-by-using-the-azure-portal-preview"></a>Migración de Azure Data Lake Storage Gen1 a Gen2 mediante Azure Portal (versión preliminar)

Puede reducir el número de pasos necesarios para completar una migración mediante Azure Portal. Los datos y los metadatos (como las marcas de tiempo y las listas ACL) se mueven automáticamente a la cuenta habilitada para Gen2. Si realiza una migración completa, no tendrá que apuntar las cargas de trabajo a Gen2 porque las solicitudes se redirigen automáticamente.

> [!IMPORTANT]
> La migración de Gen1 a Gen2 mediante Azure Portal está actualmente en versión preliminar.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

En este artículo se describen las tareas siguientes:

- Paso 1: Inscripción en la versión preliminar

- Paso 2: Creación de una cuenta de almacenamiento que tenga funcionalidades de Gen2

- Paso 3: Comprobación de las asignaciones de roles RBAC

- Paso 4: Realización de la migración

- Paso 5. Prueba de las aplicaciones

- Paso 6: Finalización de la migración

Asegúrese de leer las instrucciones generales sobre cómo migrar de Gen1 a Gen2. Para obtener más información, vea [Migración de Azure Data Lake Storage de Gen1 a Gen2](data-lake-storage-migrate-gen1-to-gen2.md).

> [!NOTE]
> Para facilitar la lectura, en este artículo se usa el término *Gen1* para hacer referencia a Azure Data Lake Storage Gen1 y el término *Gen2* para hacer referencia a Azure Data Lake Storage Gen2.

## <a name="enroll-in-the-preview"></a>Inscripción en la versión preliminar

Para inscribirse en la versión preliminar, visite [este formulario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4SeyCfCfrtBlHWFupvoz_BUMEFNQzBSQTE0OU1aM0hXMDlBNEwzVTYyRy4u&wdLOR=cBC075B83-9324-4399-B94E-05A919D007C9). Después de inscribirse, cree una cuenta de almacenamiento con funcionalidades de Gen2 (consulte la sección siguiente).

Microsoft se pondrá en contacto con usted en aproximadamente siete días para confirmar que la nueva cuenta está habilitada para la migración.

## <a name="create-a-storage-account-with-gen2-capabilities"></a>Creación de una cuenta de almacenamiento con funcionalidades de Gen2

Azure Data Lake Storage Gen2 no es un tipo de servicio o una cuenta de almacenamiento dedicados. Es un conjunto de funcionalidades que puede obtener habilitando la característica **Espacio de nombres jerárquico** de una cuenta de almacenamiento de Azure. Para crear una cuenta que tenga funcionalidades de Gen2, consulte [Creación de una cuenta de almacenamiento para usarla con Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

A medida que cree la cuenta, asegúrese de configurar las opciones con los valores siguientes.

| Configuración | Valor |
|--|--|
| **Nombre de cuenta de almacenamiento** | El nombre que prefiera. Este nombre no tiene que coincidir con el nombre de la cuenta de Gen1 y puede estar en cualquier suscripción de su elección. |
| **Ubicación** | La misma región usada por la cuenta de Data Lake Storage Gen1. |
| **Replicación** | LRS o ZRS. |
| **Versión de TLS mínima** | 1.0 |
| **NFS v3** | Disabled |
| **Espacio de nombres jerárquico** | habilitado |

> [!NOTE]
> La herramienta de migración de Azure Portal no mueve la configuración de la cuenta. Por lo tanto, después de crear la cuenta, tendrá que configurar manualmente opciones como cifrado, firewalls de red y protección de datos.

## <a name="verify-rbac-role-assignments"></a>Comprobación de las asignaciones de roles RBAC

Para Gen2, asegúrese de que el rol [Propietario de datos de Storage Blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) se haya asignado a la identidad de usuario de Azure Active Directory (Azure AD) en el ámbito de la cuenta de almacenamiento, el grupo de recursos principal o la suscripción.

Para Gen1, asegúrese de que el rol [Propietario](../../role-based-access-control/built-in-roles.md#owner) se haya asignado a la identidad de usuario de Azure AD en el ámbito de la cuenta de Gen1, el grupo de recursos principal o la suscripción.

## <a name="perform-the-migration"></a>Realización de la migración

Antes de empezar, decida si va a copiar solo los datos o a realizar una migración completa.

Si copia solo los datos, ambas cuentas permanecen activas una vez finalizada la migración. Durante la migración, la cuenta de Gen1 pasará a ser de solo lectura. Luego, puede actualizar las cargas de trabajo de proceso para usar la nueva cuenta de almacenamiento habilitada para Gen2. Una vez que haya comprobado que las aplicaciones y cargas de trabajo funcionan según lo previsto, puede retirar la cuenta de Gen1. Microsoft recomienda esta opción.

Si realiza una migración completa, los datos se copian de Gen1 a Gen2. Luego, el URI de Gen1 se redirige al URI de Gen2. Una vez finalizada la migración, no tendrá acceso a su cuenta de Gen1 y todas las solicitudes de Gen1 se redirigirán a la cuenta habilitada para Gen2. La cuenta de Gen1 ya no estará disponible para las operaciones de datos y ya no se le facturarán los datos de esta. Puede eliminar la cuenta de Gen1 una vez que las canalizaciones se ejecuten en la cuenta habilitada para Gen2.

> [!NOTE]
> Gen2 no admite aplicaciones de Azure Data Lake Analytics. Si tiene alguna, asegúrese de moverla a Azure Synapse Analytics o a otra carga de trabajo admitida antes de migrar de Gen1 a Gen2.

### <a name="option-1-copy-data-from-gen1-to-gen2"></a>Opción 1: Copia de datos de Gen1 a Gen2

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) para empezar a trabajar.

2. Busque la cuenta de Data Lake Storage Gen1 y muestre la información general.

3. Seleccione el botón **Migrar datos**.

   > [!div class="mx-imgBorder"]
   > ![Botón para migrar](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. Seleccione **Copy data to a new Gen2 account** (Copiar los datos en una nueva cuenta de Gen2).

   > [!div class="mx-imgBorder"]
   > ![Opción de copia de datos](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-data-option.png)

5. Para dar consentimiento a Microsoft para realizar la migración de datos, active la casilla. Después, haga clic en el botón **Aplicar**.

   > [!div class="mx-imgBorder"]
   > ![Casilla para proporcionar consentimiento](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   Mientras se migran los datos, la cuenta de Gen1 pasa a ser de solo lectura y la cuenta habilitada para Gen2 está deshabilitada. Una vez finalizada la migración, puede leer y escribir en ambas cuentas.

   Puede detener la migración en cualquier momento seleccionando el botón **Detener migración**.

   > [!div class="mx-imgBorder"]
   > ![Opción para detener la migración](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

### <a name="option-2-perform-a-complete-migration"></a>Opción 2: Realización de una migración completa

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) para empezar a trabajar.

2. Busque la cuenta de Data Lake Storage Gen1 y muestre la información general.

3. Seleccione el botón **Migrar datos**.

   > [!div class="mx-imgBorder"]
   > ![Botón para migrar](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-tool.png)

4. Seleccione **Complete migration to a new gen 2 account** (Finalizar la migración en una nueva cuenta de Gen2).

   > [!div class="mx-imgBorder"]
   > ![Opción para finalizar la migración](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-complete-option.png)

5. Para dar consentimiento a Microsoft para realizar la migración de datos, active la casilla. Después, haga clic en el botón **Aplicar**.

   > [!div class="mx-imgBorder"]
   > ![Casilla de consentimiento](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-consent.png)

   - Mientras se migran los datos, la cuenta de Gen1 pasa a ser de solo lectura y la cuenta habilitada para Gen2 está deshabilitada.
   - Mientras se redirige el URI de Gen1, ambas cuentas están deshabilitadas.
   - Una vez finalizada la migración, la cuenta de Gen1 está deshabilitada y puede leer y escribir en la cuenta habilitada para Gen2.

   Puede detener la migración en cualquier momento antes de que se redirija el URI seleccionando el botón **Detener migración**.

   > [!div class="mx-imgBorder"]
   > ![Botón para detener la migración](./media/data-lake-storage-migrate-gen1-to-gen2-azure-portal/migration-stop.png)

## <a name="migrate-workloads-and-applications"></a>Migración de cargas de trabajo y aplicaciones

1. Configure los [servicios de las cargas de trabajo](./data-lake-storage-supported-azure-services.md) para que apunten a su punto de conexión de Gen2.

2. Actualice las aplicaciones para que usen las API de Gen2. Consulte estas guías:

   | Entorno | Artículo |
   |--------|-----------|
   |Explorador de Azure Storage |[Uso del Explorador de Azure Storage para administrar directorios y archivos en Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
   |.NET |[Uso de .NET para administrar directorios y archivos en Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md)|
   |Java|[Uso de Java para administrar directorios y archivos en Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md)|
   |Python|[Uso de Python para administrar directorios y archivos en Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md)|
   |JavaScript (Node.js)|[Uso de JavaScript en Node.js para administrar directorios y archivos en Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-javascript.md)|
   |API DE REST |[API REST de Azure Data Lake Storage](/rest/api/storageservices/data-lake-storage-gen2)|

3. Actualice los scripts para usar los [cmdlets de PowerShell](data-lake-storage-directory-file-acl-powershell.md) y los [comandos de la CLI de Azure](data-lake-storage-directory-file-acl-cli.md) de Data Lake Storage Gen2.

4. Busque referencias del identificador URI que contengan la cadena `adl://` en los archivos de código o en los cuadernos de Databricks, archivos HQL de Apache Hive o cualquier otro archivo que se use como parte de las cargas de trabajo. Sustituya estas referencias por el [identificador URI con formato de Gen2](data-lake-storage-introduction-abfs-uri.md) de la nueva cuenta de almacenamiento. Por ejemplo: el identificador URI de Gen1: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` podría convertirse en `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`.

## <a name="leverage-the-gen1-compatibility-layer-optional"></a>Uso de la capa de compatibilidad de Gen1 (opcional)

Microsoft proporciona compatibilidad de aplicaciones con funcionalidad limitada para que las aplicaciones puedan seguir usando las API de Gen1 para interactuar con los datos de la cuenta habilitada para Gen2. La capa de compatibilidad se ejecuta en el servidor, por lo que no hay nada que instalar.

> [!IMPORTANT]
> Microsoft no recomienda esta funcionalidad como sustituto para migrar las cargas de trabajo y las aplicaciones. La compatibilidad con la capa de compatibilidad de Gen1 finalizará cuando Gen1 se retire el 29 de febrero de 2024.

Para encontrar el menor número de problemas con la capa de compatibilidad, asegúrese de que los SDK de Gen1 usan las siguientes versiones (o superiores).

   | Lenguaje | Versión del SDK |
   |--|--|
   | **.NET** | [2.3.9](https://github.com/Azure/azure-data-lake-store-net/blob/master/CHANGELOG.md) |
   | **Java** | [1.1.21](https://github.com/Azure/azure-data-lake-store-java/blob/master/CHANGES.md) |
   | **Python** | [0.0 51](https://github.com/Azure/azure-data-lake-store-python/blob/master/HISTORY.rst) |

La siguiente funcionalidad no se admite en Gen2 y, por tanto, en la capa de compatibilidad.

- Opción API ListStatus para enumerar antes de una entrada.

- API ListStatus con más de 4000 archivos sin un token de continuación.

- Codificación de fragmentos para operaciones de anexado.

- Cualquier llamada API que use https://management.azure.com/ como audiencia de tokens de Azure Active Directory (Azure AD).

- Nombres de archivos o directorios con solo espacios o tabulaciones, que terminan en `.`, que contienen `:`, o con varias barras diagonales consecutivas (`//`).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

#### <a name="how-much-does-the-data-migration-cost"></a>¿Cuánto cuesta la migración de datos?

Durante la migración de datos, se le facturará el almacenamiento y las transacciones de datos de la cuenta de Gen1. Si elige la opción que copia solo los datos, se le facturará el almacenamiento y las transacciones de datos de ambas cuentas una vez finalizada la migración. Para evitar que se le facture la cuenta de Gen1, tendrá que eliminarla. Elimine la cuenta de Gen1 después de haber finalizado la actualización de las aplicaciones. Si decide realizar una migración completa, solo se le facturará el almacenamiento y las transacciones de datos de la cuenta habilitada para Gen2 después de la migración.

#### <a name="after-the-migration-completes-can-i-choose-to-go-back-to-using-the-gen1-account"></a>Una vez finalizada la migración, ¿puedo volver a usar la cuenta de Gen1?

No. Una vez finalizada la migración, no se podrá acceder a los datos de la cuenta de Gen1. Puede seguir visualizando la cuenta de Gen1 en Azure Portal y, cuando esté listo, eliminar la cuenta.

#### <a name="i-would-like-to-enable-geo-redundant-storage-grs-on-the-gen2-account-how-do-i-do-that"></a>Me gustaría habilitar el almacenamiento con redundancia geográfica (GRS) en la cuenta de Gen2, ¿cómo puedo hacerlo?

Una vez finalizada la migración, en las opciones "Copiar datos" y "Completar la migración", puede continuar y cambiar la opción de redundancia a GRS siempre que no planee usar la capa de compatibilidad de aplicaciones. La compatibilidad de aplicaciones no funcionará en cuentas que usen redundancia GRS.

#### <a name="gen1-doesnt-have-containers-and-gen2-has-them---what-should-i-expect"></a>Gen1 no tiene contenedores y Gen2 los tiene, ¿qué debo esperar?

Cuando se copian los datos en la cuenta habilitada para Gen2, se crea automáticamente un contenedor denominado `Gen1`. Si decide copiar solo los datos, puede cambiar el nombre de ese contenedor una vez finalizada la copia de datos. Si realiza una migración completa y planea usar la capa de compatibilidad de aplicaciones, debe evitar cambiar el nombre del contenedor. Cuando ya no quiera usar la capa de compatibilidad, puede cambiar el nombre del contenedor.

## <a name="next-steps"></a>Pasos siguientes

- Aprenda sobre la migración en general. Para obtener más información, vea [Migración de Azure Data Lake Storage de Gen1 a Gen2](data-lake-storage-migrate-gen1-to-gen2.md).
