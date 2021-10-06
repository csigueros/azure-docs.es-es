---
title: Definición de socios comerciales para flujos de trabajo
description: Agregue socios comerciales a la cuenta de integración para flujos de trabajo en Azure Logic Apps mediante Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: f0e0fed5bf7e3354cffa8d6799c4d3e5d39595d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549842"
---
# <a name="add-trading-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>Adición de socios comerciales a cuentas de integración para flujos de trabajo en Azure Logic Apps

Para representar a la organización y a terceros en flujos de trabajo de integración empresarial de negocio a negocio (B2B), cree un *socio comercial* en la [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) para representar a cada participante en una relación empresarial. Los asociados son entidades empresariales que participan en transacciones B2B e intercambian mensajes entre sí.

> [!IMPORTANT]
> Antes de definir estos asociados, tenga una conversación con sus asociados sobre cómo identificar y validar los mensajes que se envían entre sí. Para participar en un contrato e intercambiar mensajes entre sí, los asociados de la cuenta de integración deben usar los mismos *calificadores de negocio* o compatibles. Después de acordar estos detalles, estará listo para crear asociados en la cuenta de integración.

En este artículo se muestra cómo crear y administrar asociados, que posteriormente puede usar para crear contratos que definan el protocolo específico estándar del sector para intercambiar mensajes entre asociados.

Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](logic-apps-overview.md) Para obtener más información sobre la integración empresarial B2B, revise [Flujos de trabajo de integración empresarial B2B con Azure Logic Apps](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Si aún no tiene suscripción, [regístrese para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [recurso de cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) donde se definen y almacenan artefactos, como socios comerciales, contratos, certificados, entre otros, para su uso en los flujos de trabajo de integración empresarial y B2B. Este recurso tiene que satisfacer los siguientes requisitos:

  * Estar asociado a la misma suscripción de Azure que el recurso de aplicación lógica.

  * Existe en la misma ubicación o región de Azure que el recurso de aplicación lógica.

  * Si usa el [tipo de recurso **Aplicación lógica (consumo)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), la cuenta de integración necesita un [vínculo al recurso de aplicación lógica](logic-apps-enterprise-integration-create-integration-account.md#link-account) para poder utilizar los artefactos en el flujo de trabajo.

  * Si usa el [tipo de recurso **Aplicación lógica (estándar)** ](logic-apps-overview.md#resource-type-and-host-environment-differences), la cuenta de integración no necesita un vínculo al recurso de aplicación lógica, pero sigue siendo necesario para almacenar otros artefactos, como asociados, contratos y certificados, junto con el uso de las operaciones [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) y [EDIFACT](logic-apps-enterprise-integration-edifact.md). La cuenta de integración todavía tiene que cumplir otros requisitos, como usar la misma suscripción de Azure y existir en la misma ubicación que el recurso de aplicación lógica.

  > [!NOTE]
  > Actualmente, solo el tipo de recurso **Aplicación lógica (consumo)** admite operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). El tipo de recurso **Aplicación lógica (estándar)** no incluye operaciones de [RosettaNet](logic-apps-enterprise-integration-rosettanet.md).

<a name="add-partner"></a>

## <a name="add-a-partner"></a>Adición de un asociado

1. En el cuadro de búsqueda de [Azure Portal](https://portal.azure.com), escriba `integration accounts` y seleccione **Cuentas de integración**.

1. En **Cuentas de integración**, seleccione la cuenta de integración en la que quiera agregar los asociados.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Asociados**.

1. En el panel **Asociados**, seleccione **Agregar**.

1. En el panel **Agregar asociado**, proporcione la siguiente información sobre el asociado:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Nombre** | Sí | El nombre del asociado |
   | **Calificador** | Sí | El cuerpo de autenticación que proporciona identidades de negocio únicas a las organizaciones, por ejemplo, **D-U-N-S (Dun & Bradstreet)** . <p>Los asociados pueden optar por una identidad de negocio definida mutuamente. Para estos escenarios, seleccione **Definidos mutuamente** para EDIFACT o **Definidos mutuamente (X12)** para X12. <p>Para RosettaNet, seleccione solo **DUNS**, que es el estándar. <p>**Importante**: Para que los asociados de la cuenta de integración participen en un contrato e intercambien mensajes entre sí, deben usar el mismo calificador o uno compatible. |
   | **Valor** | Sí | Un valor que identifica los documentos que reciben sus aplicaciones lógicas. <p>Para los asociados que usen RosettaNet, este valor debe ser un número de nueve dígitos que se corresponde con el número DUNS. Puede proporcionar más información a los asociados de RosettaNet, como su clasificación e información de contacto, si primero crea los asociados y después [edita sus definiciones](#edit-partner). |
   ||||

1. Cuando finalice, seleccione **Aceptar**.

   Ahora el asociado aparece en la lista **Asociados**.

<a name="edit-partner"></a>

## <a name="edit-a-partner"></a>Edición de un asociado

1. En [Azure Portal](https://portal.azure.com), abra la cuenta de integración.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Asociados**.

1. En el panel **Asociados**, seleccione el asociado, después **Editar** y realice los cambios.

   Para los asociados que usan RosettaNet, en **Propiedades de asociados de RosettaNet**, puede proporcionar más información como se describe en la tabla siguiente:

   | Propiedad | Obligatorio | Descripción |
   |----------|----------|-------------|
   | **Clasificación de asociados** | No | Tipo de organización del asociado |
   | **Código de la cadena de suministro** | No | Código de cadena de suministro del asociado, por ejemplo, "Tecnologías de la información" o "Componentes electrónicos" |
   | **Nombre de contacto** | No | Nombre de contacto del asociado |
   | **Correo electrónico** | No | Dirección de correo electrónico del asociado |
   | **Fax** | No | Número de fax del asociado |
   | **Teléfono** | No | Número de teléfono del asociado |
   ||||

1. Cuando finalice, seleccione **Aceptar**.

<a name="delete-partner"></a>

## <a name="delete-a-partner"></a>Eliminación de un asociado

1. En [Azure Portal](https://portal.azure.com), abra la cuenta de integración.

1. En el menú de la cuenta de integración, en **Configuración**, seleccione **Asociados**.

1. En el panel **Asociados**, seleccione el asociado que quiera eliminar y, después, seleccione **Eliminar**.

1. Para confirmar que quiere eliminar al asociado, seleccione **Sí**.

## <a name="next-steps"></a>Pasos siguientes

* [Adición de acuerdos entre socios comerciales](logic-apps-enterprise-integration-agreements.md)