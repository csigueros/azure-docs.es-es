---
title: Uso de secretos de Azure Key Vault en actividades de canalización
description: Obtenga información sobre cómo capturar credenciales almacenadas desde Azure Key Vault y usarlas durante las ejecuciones de canalización de factoría de datos.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b94ceb3bc190f57e3e5190e89e1018fc8a6e77f6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831152"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Uso de secretos de Azure Key Vault en actividades de canalización

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Puede almacenar credenciales o valores de secreto en una instancia de Azure Key Vault y usarlos durante la ejecución de la canalización para pasarlos a las actividades.

## <a name="prerequisites"></a>Prerequisites

Esta característica se basa en la identidad administrada de Data Factory.  Obtenga información sobre cómo funciona en [Identidad administrada de Data Factory](./data-factory-service-identity.md) y asegúrese de que la instancia de Data Factory tenga una asociada.

## <a name="steps"></a>Pasos

1. Abra las propiedades de la factoría de datos y copie el valor Id. de aplicación de identidad administrada.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png" alt-text="Valor de identidad administrada":::

2. Abra las directivas de acceso del almacén de claves y agregue los permisos de identidad administrada para obtener y enumerar los secretos.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png" alt-text="Captura de pantalla que muestra la página &quot;Directivas de acceso&quot; con la acción &quot;Agregar directiva de acceso&quot; resaltada.":::

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png" alt-text="Directivas de acceso de Key Vault":::

    Haga clic en **Agregar** y luego en **Guardar**.

3. Navegue hasta el secreto de Key Vault y copie el identificador secreto.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png" alt-text="Identificador secreto":::

    Anote el URI del secreto que quiera obtener durante la ejecución de la canalización de factoría de datos.

4. En la canalización de Data Factory, agregue una nueva actividad web y configúrela como se indica a continuación.  

    |Propiedad  |Value  |
    |---------|---------|
    |Salida segura     |True         |
    |URL     |[El valor de URI del secreto]?api-version=7.0         |
    |Método     |GET         |
    |Authentication     |MSI         |
    |Resource        |https://vault.azure.net       |

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png" alt-text="Actividad web":::

    > [!IMPORTANT]
    > Debe agregar **?api-version=7.0** al final del URI del secreto.  

    > [!CAUTION]
    > Establezca la opción Salida segura en true para evitar que el valor de secreto se registre en texto sin formato.  Todas las actividades adicionales que consuman este valor deben tener la opción Entrada segura establecida en true.

5. Para usar el valor en otra actividad, utilice la expresión de código siguiente **@activity("Web1").output.value**.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png" alt-text="Expresión de código":::

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar Azure Key Vault para almacenar credenciales para almacenes de datos y procesos, vea [Almacenamiento de credenciales en Azure Key Vault](./store-credentials-in-key-vault.md).