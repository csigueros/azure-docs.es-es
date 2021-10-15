---
title: Adición de certificados para proteger mensajes B2B de flujos de trabajo
description: Agregue certificados a la cuenta de integración para proteger los mensajes B2B en flujos de trabajo con Azure Logic Apps y Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/23/2021
ms.openlocfilehash: b68bc9f1f77c6ad5b60cd5b2ea9c4b644acb4c5b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361061"
---
# <a name="add-certificates-to-integration-accounts-for-securing-messages-in-workflows-with-azure-logic-apps"></a>Adición de certificados a cuentas de integración para proteger mensajes en flujos de trabajo con Azure Logic Apps

Cuando necesite intercambiar mensajes confidenciales en un flujo de trabajo de negocio a negocio (B2B) de aplicación lógica, puede aumentar la seguridad en torno a esta comunicación mediante certificados. Un certificado es un documento digital que ayuda a proteger la comunicación de las maneras siguientes:

* Comprueba las identidades de los participantes en las comunicaciones electrónicas.

* Cifra el contenido del mensaje.

* Firma digitalmente los mensajes.

Puede usar los siguientes tipos de certificados en los flujos de trabajo:

* [Certificados públicos](https://en.wikipedia.org/wiki/Public_key_certificate), que se deben comprar a una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) de una red pública de Internet. Estos certificados no necesita clave.

* Certificados privados o [*certificados autofirmados*](https://en.wikipedia.org/wiki/Self-signed_certificate), que el usuario crea y emite. Sin embargo, estos certificados requieren claves privadas.

Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) Para más información sobre la integración empresarial B2B, revise [Flujos de trabajo de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md), donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica.

  * Si usa el tipo de recurso [**Logic App (Consumption)**](logic-apps-overview.md#resource-type-and-host-environment-differences) (Aplicación lógica [Consumo]), tendrá que [vincular la cuenta de integración al recurso de aplicación lógica](logic-apps-enterprise-integration-create-integration-account.md#link-account) para poder utilizar los artefactos en el flujo de trabajo.

    Para crear y agregar certificados para usarlos en los flujos de trabajos de **Logic App (Consumption)** (Aplicación lógica [Consumo]), no necesita un recurso de aplicación lógica. Sin embargo, cuando esté listo para usar esos certificados en los flujos de trabajo, el recurso de aplicación lógica necesitará una cuenta de integración vinculada que almacene esos certificados.

  * Si usa el tipo de recurso [**Logic App (Standard)**](logic-apps-overview.md#resource-type-and-host-environment-differences) (Aplicación lógica [Estándar]), la cuenta de integración no necesita un vínculo al recurso de aplicación lógica, pero sigue siendo necesario para almacenar otros artefactos, como asociados, contratos y certificados, junto con el uso de las operaciones [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) y [EDIFACT](logic-apps-enterprise-integration-edifact.md). La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

    > [!NOTE]
    > Actualmente, solo el tipo de recurso **Logic App (Consumption)** (Aplicación lógica [Consumo]) admite operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). El tipo de recurso **Logic App (Standard)** (Aplicación lógica [Estándar]) no incluye operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

* En el caso de los certificados privados, debe cumplir los siguientes requisitos previos:

  * Agregar una clave privada en [Azure Key Vault](../key-vault/general/overview.md) y tener el **nombre de la clave**. Para más información, consulte [Adición de la clave privada a Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate).

  * Autorizar al servicio Azure Logic Apps a realizar operaciones en el almacén de claves. Para conceder acceso a la entidad de servicio de Azure Logic Apps, use el comando de PowerShell, [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), por ejemplo:

    `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

    [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

  * [Agregar un certificado público correspondiente](#add-public-certificate) al almacén de claves. Este certificado aparece en la configuración de **envío** y **recepción** del contrato [ para firmar y cifrar los mensajes](logic-apps-enterprise-integration-agreements.md). Por ejemplo, revise [Referencia para la configuración de mensajes de AS2 en Azure Logic Apps](logic-apps-enterprise-integration-as2-message-settings.md).

* Al menos dos [entidades](logic-apps-enterprise-integration-partners.md) y un [contrato entre ellas](logic-apps-enterprise-integration-agreements.md) en la cuenta de integración En un contrato hacen falta un asociado anfitrión y un asociado invitado. Además, en un contrato es necesario que ambos asociados usen el mismo calificador de *identidad empresarial* o uno compatible que sea adecuado para un contrato AS2, X12, EDIFACT o RosettaNet.

* Opcionalmente, el recurso de aplicación lógica y el flujo de trabajo donde quiere usar el certificado. El flujo de trabajo requiere cualquier desencadenador que inicie el flujo de trabajo de la aplicación lógica. Si no ha creado un flujo de trabajo de aplicación lógica antes, consulte [Inicio rápido: Creación de la primera aplicación lógica](quickstart-create-first-logic-app-workflow.md).

<a name="add-public-certificate"></a>

## <a name="add-a-public-certificate"></a>Adición de un certificado público

Para usar un *certificado público* en el flujo de trabajo, primero debe agregar el certificado a la cuenta de integración.

1. En el cuadro de búsqueda de [Azure Portal](https://portal.azure.com), escriba `integration accounts` y seleccione **Cuentas de integración**.

1. En **Cuentas de integración**, seleccione la cuenta de integración en la que quiere agregar el certificado.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Certificados**.

1. En el panel **Certificados**, seleccione **Agregar**.

1. En el panel **Agregar certificado**, proporcione la siguiente información sobre el certificado:

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Nombre** | Sí | <*nombre-certificado*> | El nombre del certificado, que es `publicCert` en este ejemplo. |
   | **Tipo de certificado** | Yes | **Public** | Su tipo de certificado |
   | **Certificate** | Yes | <*nombre-archivo-del-certificado*> | Para buscar el archivo de certificado que quiere agrega, seleccione el icono de carpeta junto al cuadro **Certificado**. |
   |||||

   ![Captura de pantalla que muestra Azure Portal y la cuenta de integración con la opción "Agregar" seleccionada y el panel "Agregar certificado" con los detalles del certificado público.](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

1. Cuando finalice, seleccione **Aceptar**.

   Azure valida el certificado que eligió y después lo carga.

   ![Captura de pantalla que muestra Azure Portal y la cuenta de integración con el certificado público en la lista "Certificados".](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png)

<a name="add-public-certificate"></a>

## <a name="add-a-private-certificate"></a>Adición de un certificado privado

Para usar un *certificado privado* en el flujo de trabajo, primero debe agregar el certificado a la cuenta de integración. Asegúrese de que también ha cumplido los [requisitos previos de los certificados privados](#prerequisites).

1. En el cuadro de búsqueda de [Azure Portal](https://portal.azure.com), escriba `integration accounts` y seleccione **Cuentas de integración**.

1. En **Cuentas de integración**, seleccione la cuenta de integración en la que quiere agregar el certificado.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Certificados**.

1. En el panel **Certificados**, seleccione **Agregar**.

1. En el panel **Agregar certificado**, proporcione la siguiente información sobre el certificado:

   | Propiedad | Obligatorio | Value | Descripción |
   |----------|----------|-------|-------------|
   | **Nombre** | Sí | <*nombre-certificado*> | El nombre del certificado, que es `privateCert` en este ejemplo. |
   | **Tipo de certificado** | Yes | **Privado** | Su tipo de certificado |
   | **Certificate** | Yes | <*nombre-archivo-del-certificado*> | Para buscar el archivo de certificado que quiere agrega, seleccione el icono de carpeta junto al cuadro **Certificado**. En el almacén de claves que contiene la clave privada, el archivo que agregue ahí es el certificado público. |
   | **Grupo de recursos** | Sí | <*grupo-recursos-de-cuenta-integración*> | Grupo de recursos de su cuenta de integración, que es `Integration-Account-RG` en este ejemplo. |
   | **Key Vault** | Sí | <*nombre-almacén-claves*> | Nombre del almacén de claves |
   | **Key name** | Yes | <*key-name*> | Nombre de la clave |
   |||||

   ![Captura de pantalla que muestra Azure Portal y la cuenta de integración con la opción "Agregar" seleccionada y el panel "Agregar certificado" con los detalles del certificado privado.](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

1. Cuando finalice, seleccione **Aceptar**.

   Azure valida el certificado que eligió y después lo carga.

   ![Captura de pantalla que muestra Azure Portal y la cuenta de integración con el certificado privado en la lista "Certificados".](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png)

## <a name="next-steps"></a>Pasos siguientes

* [Intercambio de mensajes AS2](logic-apps-enterprise-integration-as2.md)
* [Intercambio de mensajes EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Intercambio de mensajes X12](logic-apps-enterprise-integration-x12.md)
* [Intercambio de mensajes de RosettaNet](logic-apps-enterprise-integration-rosettanet.md)
