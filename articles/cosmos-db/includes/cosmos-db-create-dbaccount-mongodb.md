---
title: Archivo de inclusión
description: archivo de inclusión
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: 3f7a37ce78e49abe1356180818a215488fe82ea3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515634"
---
1. En una nueva ventana del explorador, inicie sesión en [Azure Portal](https://portal.azure.com/).

2. En el menú de la izquierda, seleccione **Crear un recurso**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png" alt-text="Captura de pantalla de creación de un recurso en Azure Portal.":::
   
3. En la página **Nuevo**, seleccione **Bases de datos** > **Azure Cosmos DB**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png" alt-text="Captura de pantalla del de las bases de datos de Azure Portal":::
   
4. En la págin **Seleccionar opción de API**, seleccione **Azure Cosmos DB API para MongoDB** > **Crear**.

   La API determina el tipo de cuenta que se va a crear. Seleccione **Azure Cosmos DB API para Mongo DB**, ya que va a crear una colección que funciona con MongoDB en este inicio rápido. Para más información, consulte [Introducción a Azure Cosmos DB API para MongoDB](../mongodb-introduction.md).

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-select-api.png" alt-text="Captura de pantalla del panel de selección de la opción de API.":::

5. En la página **Crear una cuenta de Azure Cosmos DB**, especifique la configuración de la nueva cuenta de Azure Cosmos DB.

   |Configuración|Value|Descripción |
   |---|---|---|
   |Suscripción|Nombre de suscripción|Seleccione la suscripción de Azure que quiere usar para esta cuenta de Azure Cosmos DB. |
   |Grupo de recursos|Definición de un nombre de grupo de recursos|Seleccione un grupo de recursos o seleccione **Crear nuevo** y escriba un nombre único para el grupo de recursos nuevo. |
   |Nombre de cuenta|Escriba un nombre único.|Escriba un nombre único para identificar la cuenta de Azure Cosmos DB. El URI de la cuenta será *mongo.cosmos.azure.com* y se anexará al nombre único de la cuenta.<br><br>El nombre de la cuenta solo puede utilizar letras minúsculas, números y guiones (-), y debe tener entre 3 y 44 caracteres de longitud.|
   |Location|Región más cercana a los usuarios|Seleccione una ubicación geográfica para hospedar la cuenta de Azure Cosmos DB. Use la ubicación más cercana a los usuarios para que puedan acceder de la forma más rápida posible a los datos.|
   |Capacity mode (Modo de capacidad)|Rendimiento aprovisionado o sin servidor|Seleccione **Provisioned throughput** (Rendimiento aprovisionado) para crear una cuenta en modo de [rendimiento aprovisionado](../set-throughput.md). Seleccione **Serverless** (Sin servidor) para crear una cuenta en modo [sin servidor](../serverless.md).<br><br>**Nota**: solo se admiten la versiones 4 y 3.6 de MongoDB API en las cuentas sin servidor. Si elige la versión 3.2, se forzará la cuenta al modo de rendimiento aprovisionado.|
   |Aplicar el descuento del nivel Gratis de Azure Cosmos DB|**Aplicar** o **No aplicar**|Con el nivel Gratis de Azure Cosmos DB, recibirá los primeros 1000 RU/s y 25 GB de almacenamiento gratis en una cuenta. Más información acerca del [nivel Gratis](https://azure.microsoft.com/pricing/details/cosmos-db/).|
   | Versión | Elección de la versión de servidor necesaria | La API de Azure Cosmos DB para MongoDB es compatible con la versión 4.0, 3.6 y 3.2 del servidor. Puede [actualizar o degradar](../mongodb/upgrade-mongodb-version.md) una cuenta después de crearla. |

   > [!NOTE]
   > Puede tener una cuenta de Azure Cosmos DB de nivel Gratis por cada suscripción de Azure y debe optar por tenerla al crear la cuenta. Si no ve la opción para aplicar el descuento por nivel Gratis, significará que en otra cuenta de la suscripción ya se ha habilitado el nivel Gratis.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png" alt-text="Captura de pantalla de la página de nueva cuenta de Azure Cosmos DB."::: 

1. En la pestaña **Distribución global**, configure los detalles siguientes. Puede dejar los valores predeterminados para este inicio rápido:

   |Configuración|Value|Descripción |
   |---|---|---|
   |Redundancia geográfica|Deshabilitar|Habilite o deshabilite la distribución global en su cuenta. Para ello, debe emparejar su región con una región de par. Puede agregar más regiones a su cuenta más adelante.|
   |Escrituras en varias regiones|Deshabilitar|La funcionalidad de escrituras en varias regiones le permite aprovechar el rendimiento aprovisionado para sus bases de datos y contenedores de todo el mundo.|

   > [!NOTE]
   > Las siguientes opciones no están disponibles si selecciona **Serverless** (Sin servidor) en **Capacity mode** (Modo de capacidad):
   > - Aplicación de descuento por nivel Gratis
   > - Redundancia geográfica
   > - Escrituras en varias regiones

1. Opcionalmente, puede configurar detalles adicionales en las pestañas siguientes:

   * **Redes**: configure el [acceso desde una red virtual](../how-to-configure-vnet-service-endpoint.md).
   * **Directiva de copia de seguridad**: configure una directiva de copia de seguridad [periódica](../configure-periodic-backup-restore.md) o [continua](../provision-account-continuous-backup.md).
   * **Cifrado**: use una clave administrada por el servicio o una [clave administrada por el cliente](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account).
   * **Etiquetas**: son pares nombre-valor que permiten categorizar los recursos y ver una facturación consolidada mediante la aplicación de la misma etiqueta en varios recursos y grupos de recursos.

1. Seleccione **Revisar + crear**.

4. La cuenta tarda unos minutos en crearse. Espere a que el portal muestre la página **¡Enhorabuena! La cuenta de Azure Cosmos DB API para MongoDB está lista**.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png" alt-text="Captura de pantalla del panel de notificaciones de Azure Portal."::: 
