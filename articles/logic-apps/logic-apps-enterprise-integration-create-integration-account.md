---
title: Creación y administración de cuentas de integración
description: Cree y administre cuentas de integración para crear flujos de trabajo de integración empresarial B2B en Azure Logic Apps con Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: fa900b7df1db1efbc8fe28a96cd2048113d7390d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550381"
---
# <a name="create-and-manage-integration-accounts-for-b2b-workflows-in-azure-logic-apps-using-the-enterprise-integration-pack"></a>Creación y administración de cuentas de integración para flujos de trabajo B2B en Azure Logic Apps con Enterprise Integration Pack

Para poder crear flujos de trabajo de integración de negocio a negocio (B2B) y de empresa mediante Azure Logic Apps, debe crear un recurso de *cuenta de integración*. Esta cuenta es un contenedor escalable basado en la nube en Azure que simplifica la forma de almacenar y administrar artefactos B2B que se definen y usan en los flujos de trabajo de escenarios B2B. Tales artefactos incluyen [entidades](logic-apps-enterprise-integration-partners.md), [contratos](logic-apps-enterprise-integration-agreements.md), [mapas](logic-apps-enterprise-integration-maps.md), [esquemas](logic-apps-enterprise-integration-schemas.md), [certificados](logic-apps-enterprise-integration-certificates.md), etc. También debe tener una cuenta de integración para intercambiar electrónicamente mensajes B2B con otras organizaciones. Cuando otras organizaciones usen protocolos y formatos de mensaje diferentes de los que usa su organización, debe convertir estos formatos para que el sistema de su organización pueda procesar esos mensajes. Entre los protocolos estándar del sector admitidos están [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), [EDIFACT](logic-apps-enterprise-integration-edifact.md) y [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

> [!TIP]
> Para crear una cuenta de integración para usarla en un [entorno de servicio de integración](connect-virtual-network-vnet-isolated-environment-overview.md), consulte [Creación de cuentas de integración en un ISE](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

En este artículo se muestra cómo completar las tareas siguientes:

* Crear una cuenta de integración.
* Vincular una cuenta de integración a un recurso de aplicación lógica.
* Cambiar el plan de tarifa de la cuenta de integración.
* Desvincular la cuenta de integración de una aplicación lógica.
* Mover una cuenta de integración a otra suscripción o grupo de recursos de Azure.
* Eliminar una cuenta de integración.

Si no está familiarizado con Azure Logic Apps, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) Para más información sobre la integración empresarial B2B, revise [Flujos de trabajo de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Debe usar la misma suscripción de Azure para la cuenta de integración y para el recurso de aplicación lógica.

* Si va a usar el tipo de recurso [**Logic App (Consumption)** (Aplicación lógica [Consumo])](logic-apps-overview.md#resource-type-and-host-environment-differences), debe tener un recurso de aplicación lógica que pueda [vincular a la cuenta de integración](#link-account). Este vínculo es necesario para poder usar los artefactos en el flujo de trabajo. Puede crear los artefactos sin este vínculo, pero el vínculo es necesario cuando esté listo para usar estos artefactos en los flujos de trabajo.

* Si va a usar el tipo de recurso [**Logic App (Standard)** (Aplicación lógica [Estándar])](logic-apps-overview.md#resource-type-and-host-environment-differences), puede agregar directamente mapas y esquemas al recurso de aplicación lógica mediante Azure Portal o Visual Studio Code. A continuación, puede usar estos artefactos en varios flujos de trabajo dentro del *mismo recurso de aplicación lógica*. Todavía tiene que crear una cuenta de integración para los demás artefactos B2B y usar operaciones B2B, como las operaciones [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), [EDIFACT](logic-apps-enterprise-integration-edifact.md) y [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). Sin embargo, no es necesario vincular la cuenta de integración con el recurso de aplicación lógica, por lo que la funcionalidad de vinculación no existe.

## <a name="create-integration-account"></a>Creación de una cuenta de integración

Las cuentas de integración están disponibles en distintos niveles con [precios variables](https://azure.microsoft.com/pricing/details/logic-apps/). En función del nivel que elija, la creación de una cuenta de integración podría generar costos. Para obtener más información, revise [Modelos de precios y facturación en Azure Logic Apps](logic-apps-pricing.md#integration-accounts) y [Precios de Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

En función de sus requisitos y escenarios, determine el nivel de cuenta de integración adecuado para crear. Tanto la cuenta de integración como el recurso de aplicación lógica deben estar en la *misma* ubicación o región de Azure. En la tabla siguiente se describen los niveles disponibles:

| Nivel | Descripción |
|------|-------------|
| **Basic** | Para los escenarios en los que quiere utilizar solo el control de mensajes o actuar como un asociado de pequeña empresa que tiene una relación empresarial con una entidad empresarial mayor. <p><p>Compatible con el Acuerdo de Nivel de Servicio de Logic Apps. |
| **Estándar** | Para los escenarios en los que se tienen relaciones B2B más complejas y un número mayor de entidades que debe administrar. <p><p>Compatible con el Acuerdo de Nivel de Servicio de Logic Apps. |
| **Gratis** | Para escenarios de exploración, no en escenarios de producción. Este nivel tiene límites en cuanto a disponibilidad, rendimiento y uso de regiones. Por ejemplo, el nivel Gratis solo está disponible para las regiones públicas en Azure, como Oeste de EE. UU. o Sudeste Asiático, pero no para [Azure China 21Vianet](/azure/china/overview-operations) o [Azure Government](../azure-government/documentation-government-welcome.md). <p><p>**Nota**: No compatible con el Acuerdo de Nivel de Servicio de Logic Apps. |
|||

En esta tarea, puede usar Azure Portal, la [CLI de Azure](/cli/azure/resource#az_resource_create) o [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con sus credenciales de su cuenta de Azure.

1. En el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

1. En **Cuentas de integración**, seleccione **Crear**.

1. En el panel **Crear una cuenta de integración**, proporcione la siguiente información sobre la cuenta de integración:

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Suscripción** | Sí | <*Azure-subscription-name*> | El nombre de la suscripción a Azure |
   | **Grupos de recursos** | Sí | <*nombre del grupo de recursos de Azure*> | Nombre del [grupo de recursos de Azure](../azure-resource-manager/management/overview.md) que se va a usar para organizar recursos relacionados. En este ejemplo, cree un grupo de recursos llamado `FabrikamIntegration-RG`. |
   | **Nombre de la cuenta de integración** | Sí | <*integration-account-name*> | El nombre de la cuenta de integración, que solo puede contener letras, números, guiones (`-`), caracteres de subrayado (`_`), paréntesis (`(`,`)`) y puntos (`.`). En este ejemplo se usa `Fabrikam-Integration`. |
   | **Región** | Sí | <*Azure-region*> | Región de Azure en la que quiere almacenar los metadatos de la cuenta de integración. Seleccione la misma ubicación que la aplicación lógica o cree las aplicaciones lógicas en la misma ubicación que la cuenta de integración. En este ejemplo, use `West US`. <p>**Nota**: Para crear una cuenta de integración dentro de un [ de servicio de integración (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), seleccione **Associate with integration service environment** (Asociar con el entorno de servicio de integración) y seleccione su ISE como ubicación. Para obtener más información, consulte [Creación de cuentas de integración en un ISE](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Plan de tarifa** | Sí | <*pricing-level*> | Plan de tarifa de la cuenta de integración, que puede cambiar más adelante. En este ejemplo, seleccione **Gratis**. Para más información, revise la siguiente documentación: <p>- [Modelo de precios de Logic Apps](logic-apps-pricing.md#integration-accounts) <br>- [Límites y configuración de Logic Apps](logic-apps-limits-and-config.md#integration-account-limits) <br>- [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Habilitación de análisis de registros** | No | No seleccionado | En este ejemplo, no seleccione esta opción. |
   |||||

1. Cuando termine, seleccione **Revisar y crear**.

   Una vez completada la implementación, Azure abre la cuenta de integración.

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Para agregar la extensión [az logic integration-account](/cli/azure/logic/integration-account), use el comando [az extension add](/cli/azure/extension#az_extension_add):

   ```azurecli
   az extension add –-name logic
   ```

1. Para crear un grupo de recursos o usar uno ya existente, ejecute el comando [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   Para enumerar las cuentas de integración de un grupo de recursos, use el comando [az logic integration-account list](/cli/azure/logic/integration-account#az_logic_integration_account_list):

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. Para crear una cuenta de integración, ejecute el comando [az logic integration-account create](/cli/azure/logic/integration-account#az_logic_integration_account_create):

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   El nombre de la cuenta de integración solo puede contener letras, números, guiones (-), guiones bajos (_), paréntesis ((,)) y puntos (.).

   Para ver una cuenta de integración concreta, use el comando [az logic integration-account show](/cli/azure/logic/integration-account#az_logic_integration_account_show):

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   Puede cambiar la SKU o el plan de tarifa mediante el comando [az logic integration-account update](/cli/azure/logic/integration-account#az_logic_integration_account_update):

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   Para más información sobre los precios, consulte estos recursos:

   * [Modelo de precios de Logic Apps](logic-apps-pricing.md#integration-accounts)
   * [Límites y configuración de Logic Apps](logic-apps-limits-and-config.md#integration-account-limits)
   * [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)

Para importar una cuenta de integración mediante un archivo JSON, use el comando [az logic integration-account import](/cli/azure/logic/integration-account#az_logic_integration_account_import):

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

---

<a name="link-account"></a>

## <a name="link-to-logic-app-consumption-resource-only"></a>Vinculación a la aplicación lógica (solo recursos de Consumo)

Para que el flujo de trabajo de **Logic App (Consumption)** (Aplicación lógica [Consumo]) pueda acceder a los artefactos B2B de la cuenta de integración, primero debe vincular el recurso de aplicaión lógica a la cuenta de integración. Tanto la aplicación lógica como la cuenta de integración deben usar la misma suscripción y región de Azure. Para completar esta tarea, puede utilizar Azure Portal. Si usa Visual Studio y la aplicación lógica está en un [proyecto de grupo de recursos de Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md), puede [vincular la aplicación lógica a una cuenta de integración mediante Visual Studio](manage-logic-apps-with-visual-studio.md#link-integration-account).

1. En [Azure Portal](https://portal.azure.com), abra una aplicación lógica existente o cree una.

1. En el menú de la aplicación lógica, en **Configuración**, seleccione **Configuración de flujo de trabajo**. En **Cuenta de integración**, abra la lista **Select an Integration account** (Seleccionar una cuenta de integración), y seleccione la cuenta de integración deseada.

   ![Captura de pantalla que muestra Azure Portal con el menú de la cuenta de integración con el panel "Configuración del flujo de trabajo" abierto y la lista "Select an Integration account" (Seleccionar una cuenta de integración) abierta.](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Para finalizar la vinculación, seleccione **Guardar**.

   ![Captura de pantalla que muestra el panel "Configuración del flujo de trabajo" y "Guardar" seleccionado.](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Una vez que la cuenta de integración se haya vinculado correctamente, Azure muestra un mensaje de confirmación.

   ![Captura de pantalla que muestra el mensaje de confirmación de Azure.](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Ahora, la aplicación lógica puede usar los artefactos de la cuenta de integración, además de conectores B2B, como la validación XML y la codificación o descodificación de archivos sin formato.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Cambiar el plan de tarifa

Para aumentar los [límites](logic-apps-limits-and-config.md#integration-account-limits) de una cuenta de integración, puede [actualizar a un plan de tarifa superior](#upgrade-pricing-tier), si está disponible. Por ejemplo, puede actualizar del nivel Gratis al nivel básico o estándar. También puede [cambiar a un nivel inferior](#downgrade-pricing-tier), si está disponible. Para más información sobre los precios, revise la siguiente documentación:

* [Precios de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modelo de precios de Logic Apps](logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Actualización del plan de tarifa

Para realizar este cambio, puede usar Azure Portal o la CLI de Azure.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con sus credenciales de su cuenta de Azure.

1. En el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

   Azure muestra todas las cuentas de integración en las suscripciones de Azure.

1. En **Cuentas de integración**, seleccione la cuenta de integración que quiera mover. En el menú de la cuenta de integración, seleccione **Información general**.

   ![Captura de pantalla que muestra Azure Portal con el menú de la cuenta de integración y la opción "Información general" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. En el panel Información general, seleccione **Actualizar plan de tarifa**, donde se enumeran todos los niveles superiores disponibles. Al seleccionar un nivel, el cambio surte efecto de inmediato.

   ![Captura de pantalla que muestra el panel "Información general" de la cuenta de integración con la opción "Actualizar plan de tarifa" seleccionada.](media/logic-apps-enterprise-integration-create-integration-account/upgrade-pricing-tier.png)

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

1. Si aún no lo ha hecho, [instale los requisitos previos de la CLI de Azure](/cli/azure/get-started-with-azure-cli).

1. En Azure Portal, abra el entorno de [Azure Cloud Shell](../cloud-shell/overview.md).

   ![Captura de pantalla que muestra la barra de herramientas de Azure Portal con la opción "Cloud Shell" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. En el símbolo del sistema, escriba el comando [**az resource**](/cli/azure/resource#az_resource_update) y establezca `skuName` en el nivel superior que quiera.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Por ejemplo, si tiene el nivel básico, puede establecer `skuName` en `Standard`:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Cambio del plan de tarifa a un nivel inferior

Para cambiarlo, use la [CLI de Azure](/cli/azure/get-started-with-azure-cli).

1. Si aún no lo ha hecho, [instale los requisitos previos de la CLI de Azure](/cli/azure/get-started-with-azure-cli).

1. En Azure Portal, abra el entorno de [Azure Cloud Shell](../cloud-shell/overview.md).

   ![Captura de pantalla que muestra la barra de herramientas de Azure Portal con la opción "Cloud Shell" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. En el símbolo del sistema, escriba el comando [**az resource**](/cli/azure/resource#az_resource_update) y establezca `skuName` en el nivel inferior que quiera.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Por ejemplo, si tiene el nivel estándar, puede establecer `skuName` en `Basic`:

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Desvinculación de aplicación lógica

Si quiere vincular la aplicación lógica a otra cuenta de integración o dejar de usar una cuenta de integración con la aplicación lógica, elimine el vínculo mediante Azure Resource Explorer.

1. Abra la ventana del explorador y vaya a [Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com). Inicie sesión con las mismas credenciales de la cuenta de Azure.

   ![Captura de pantalla que muestra un explorador web con Azure Resource Explorer.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. En el cuadro de búsqueda, escriba el nombre de la aplicación lógica y abra la aplicación lógica.

   ![Captura de pantalla que muestra el cuadro de búsqueda del explorador, que contiene el nombre de la aplicación lógica.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. En la barra de título del explorador, seleccione **Lectura/escritura**.

   ![Captura de pantalla que muestra la barra de título con la opción "Lectura y escritura" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. En la pestaña **Datos**, seleccione **Editar**.

   ![Captura de pantalla que muestra la pestaña "Datos" con la opción "Editar" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. En el editor, busque el objeto `integrationAccount` y elimine esa propiedad, que tiene este formato:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Por ejemplo:

   ![Captura de pantalla que muestra cómo buscar el objeto "integrationAccount".](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. En la pestaña **Datos**, seleccione **Colocar** para guardar los cambios.

   ![Captura de pantalla que muestra la pestaña "Datos" con la opción "Colocar" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Abra la aplicación lógica en Azure Portal. En el menú de la aplicación lógica, en **Configuración del flujo de trabajo**, compruebe que la propiedad **Cuenta de integración** aparece ahora vacía.

   ![Captura de pantalla que muestra Azure Portal con el menú de la aplicación lógica y la opción "Configuración del flujo de trabajo" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover la cuenta de integración

Puede mover la cuenta de integración a otro grupo de recursos de Azure o suscripción a Azure. Al mover recursos, Azure crea nuevos identificadores de recurso, por lo que debe asegurarse de usar los nuevos identificadores en su lugar y actualizar los scripts o las herramientas asociados a los recursos que ha movido. Si quiere cambiar la suscripción, también debe especificar un grupo de recursos nuevo o existente.

Para esta tarea, puede usar Azure Portal si sigue los pasos de esta sección o la [CLI de Azure](/cli/azure/resource#az_resource_move).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con sus credenciales de su cuenta de Azure.

1. En el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

   Azure muestra todas las cuentas de integración en las suscripciones de Azure.

1. En **Cuentas de integración**, seleccione la cuenta de integración que quiera mover. En el menú de la cuenta de integración, seleccione **Información general**.

1. En el panel Información general, junto a **Grupo de recursos** o **Nombre de suscripción**, seleccione **Cambiar**.

   ![Captura de pantalla que muestra Azure Portal y el panel "Información general" con "Cambiar" seleccionado junto a "Grupo de recursos" o "Nombre de suscripción".](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Seleccione los recursos relacionados que también quiera mover.

1. En función de lo que seleccione, siga estos pasos para cambiar el grupo de recursos o la suscripción:

   * Grupo de recursos: En la lista **Grupo de recursos**, seleccione el grupo de recursos de destino. O bien, para crear otro grupo de recursos, seleccione **Crear un nuevo grupo de recursos**.

   * Suscripción: En la lista **Suscripción**, seleccione la suscripción de destino. En la lista **Grupo de recursos**, seleccione el grupo de recursos de destino. O bien, para crear otro grupo de recursos, seleccione **Crear un nuevo grupo de recursos**.

1. Para confirmar que comprende que los scripts o las herramientas asociados a los recursos que se han movido no funcionarán hasta que los actualice con los nuevos identificadores de recurso, seleccione el cuadro de confirmación y después seleccione **Aceptar**.

1. Cuando finalice, asegúrese de actualizar todos los scripts con los nuevos identificadores de los recursos que ha movido.  

## <a name="delete-integration-account"></a>Eliminar una cuenta de integración

Para esta tarea, puede usar Azure Portal si sigue los pasos de esta sección, la [CLI de Azure](/cli/azure/resource#az_resource_delete) o [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con sus credenciales de su cuenta de Azure.

1. En el cuadro de búsqueda principal de Azure, escriba `integration accounts` y seleccione **Cuentas de integración**.

   Azure muestra todas las cuentas de integración en las suscripciones de Azure.

1. En **Cuentas de integración**, seleccione la cuenta de integración que quiera eliminar. En el menú de la cuenta de integración, seleccione **Información general**.

   ![Captura de pantalla que muestra Azure Portal con la lista "Cuentas de integración" y el menú de la cuenta de integración con la opción "Información general" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. En el panel Información general, seleccione **Eliminar**.

   ![Captura de pantalla que muestra el panel "Información general" con la opción "Eliminar" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Para confirmar que quiere eliminar la cuenta de integración, seleccione **Sí**.

   ![Captura de pantalla que muestra el cuadro de confirmación y la opción "Sí" seleccionada.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

<a name="delete-account-azure-cli"></a>

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Puede eliminar una cuenta de integración mediante el comando [az logic integration-account delete](/cli/azure/logic/integration-account#az_logic_integration_account_delete):

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

---

## <a name="next-steps"></a>Pasos siguientes

* [Creación de asociados comerciales en la cuenta de integración](logic-apps-enterprise-integration-partners.md)
* [Creación de contratos entre asociados en la cuenta de integración](logic-apps-enterprise-integration-agreements.md)
