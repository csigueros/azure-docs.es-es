---
title: Flujos de trabajo de integración empresarial B2B
description: Aprenda a crear flujos de trabajo B2B automatizados para la integración empresarial mediante Azure Logic Apps y Enterprise Integration Pack.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: overview
ms.date: 09/14/2021
ms.openlocfilehash: 91ea9eb37e7c4e2e97513a1496a52d2521e9f4b2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128652151"
---
# <a name="b2b-enterprise-integration-workflows-with-azure-logic-apps-and-enterprise-integration-pack"></a>Flujos de trabajo de integración empresarial B2B con Azure Logic Apps y Enterprise Integration Pack

Para las soluciones de negocio a negocio (B2B) y comunicación directa entre organizaciones, se pueden crear flujos de trabajo de integración empresarial automatizados y escalables mediante el uso de [Azure Logic Apps](logic-apps-overview.md) con Enterprise Integration Pack (EIP).

## <a name="what-is-the-enterprise-integration-pack"></a>¿Qué es Enterprise Integration Pack?

Si conoce Microsoft BizTalk Server o Azure BizTalk Services, EIP se rige por conceptos similares y facilita el uso de las funcionalidades de B2B. Sin embargo, una diferencia importante es que EIP arquitectónicamente se basa en *cuentas de integración*. Estas cuentas son contenedores basados en la nube de Azure que simplifican la forma de almacenar, administrar y usar artefactos B2B para la comunicación B2B, entre los que se incluyen [socios comerciales](logic-apps-enterprise-integration-partners.md), [contratos](logic-apps-enterprise-integration-agreements.md), [mapas](logic-apps-enterprise-integration-maps.md), [esquemas](logic-apps-enterprise-integration-schemas.md), [certificados](logic-apps-enterprise-integration-certificates.md), etc.

Con estos artefactos, puede crear soluciones de integración y flujos de trabajo B2B que incluyen servicios en la nube, como Azure, Microsoft y otras aplicaciones de software como servicio (SaaS), sistemas locales y aplicaciones personalizadas mediante Azure Logic Apps con más de [400 operaciones integradas y conectores administrados](/connectors/connector-reference/connector-reference-logicapps-connectors). Por ejemplo, puede crear y ejecutar código personalizado desde los flujos de trabajo mediante operaciones de ejecución de código integradas y Azure Functions. También puede usar [conectores de integración empresarial](../connectors/managed.md#enterprise-connectors) que admitan los siguientes estándares del sector:

* Intercambio electrónico de datos (EDI)
* Enterprise Application Integration (EAI)

Aunque las organizaciones usan distintos protocolos y formatos para la comunicación B2B, puede intercambiar mensajes electrónicamente con otras organizaciones. Puede transformar estos distintos formatos en un formato que los sistemas de las organizaciones pueden procesar mediante EIP, que admite protocolos estándar del sector, como [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), [EDIFACT](logic-apps-enterprise-integration-edifact.md) y [RosettaNet](logic-apps-enterprise-integration-rosettanet.md). La seguridad de los mensajes también se puede mejorar mediante el uso tanto del cifrado como de las firmas digitales.

## <a name="what-do-i-need-to-get-started"></a>¿Qué necesito para comenzar?

* Una cuenta y una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md) para almacenar los artefactos B2B que defina y desee usar.

* Los artefactos B2B, como las [entidades](logic-apps-enterprise-integration-partners.md), los [contratos](logic-apps-enterprise-integration-agreements.md), las [mapas](logic-apps-enterprise-integration-maps.md), los [esquemas](logic-apps-enterprise-integration-schemas.md), los [certificados](logic-apps-enterprise-integration-certificates.md), etc.

* Para crear mapas y esquemas, puede usar la [Extensión de Herramientas de integración empresarial de Microsoft Azure Logic Apps](https://aka.ms/vsenterpriseintegrationtools) y Visual Studio 2019. Si usa Visual Studio 2015, puede usar la extensión de [herramientas de Integración empresarial para Visual Studio 2015 2.0 de Microsoft Azure Logic Apps](https://aka.ms/vsmapsandschemas).

   > [!IMPORTANT]
   > No instale esta extensión junto con la extensión BizTalk Server. Tener ambas extensiones podría producir un comportamiento inesperado. Asegúrese de que solo tiene una de estas extensiones instaladas.

   > [!NOTE]
   > En los monitores de alta resolución, puede que experimente un [problema de visualización con el diseñador de mapas](/visualstudio/designers/disable-dpi-awareness) en Visual Studio. Para resolver este problema de visualización, o bien [reinicie Visual Studio en modo de no reconocimiento de PPP](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process) o agregue el [valor del registro DPIUNAWARE](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry).

Después de crear una cuenta de integración y agregar los artefactos, puede empezar a crear flujos de trabajo B2B mediante la creación de un recurso de aplicación lógica. Si no conoce las aplicaciones lógicas, pruebe a [crear un flujo de aplicaciones lógicas básicas de ejemplo](quickstart-create-first-logic-app-workflow.md). También puede crear, administrar e implementar aplicaciones lógicas mediante [Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md), [Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) o [PowerShell](/powershell/module/az.logicapp).

> [!IMPORTANT]
> Si usa el tipo de recurso **Logic App (Consumption)** (Aplicación lógica [Consumo]), tendrá que vincular la cuenta de integración al recurso de aplicación lógica para poder seleccionar los artefactos B2B que se usan en el flujo de trabajo. Sin embargo, para definir y agregar esos artefactos a su cuenta de integración, aún no necesita un recurso de aplicación lógica.
>
> Si usa el tipo de recurso **Logic App (Standard)** (Aplicación lógica [estándar]), puede agregar esquemas y asignaciones directamente al recurso de la aplicación lógica y usar esos artefactos en varios flujos de trabajo dentro del *mismo recurso de aplicación lógica*. 
> Aún necesita una cuenta de integración para almacenar otros artefactos, como asociados y contratos, pero la vinculación ya no es necesaria, por lo que esta funcionalidad no existe. Para más información sobre estos tipos de recursos, consulte [¿Qué es Azure Logic Apps: tipo de recurso y entornos de host?](logic-apps-overview.md#resource-type-and-host-environment-differences)

En el diagrama siguiente se muestran los pasos de alto nivel para empezar a crear flujos de trabajo de aplicación lógica B2B:

![Diagrama conceptual que muestra los pasos de los requisitos previos para crear flujos de trabajo de aplicación lógica B2B.](media/logic-apps-enterprise-integration-overview/overview.png)

## <a name="try-now"></a>Probar ahora

[Implementación de una aplicación lógica de ejemplo completamente operativa que envíe y reciba mensajes AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.logic/logic-app-as2-send-receive)

## <a name="next-steps"></a>Pasos siguientes

* [Crear socios comerciales](logic-apps-enterprise-integration-partners.md)
* [Crear acuerdos](logic-apps-enterprise-integration-agreements.md)
* [Incorporación de esquemas](logic-apps-enterprise-integration-schemas.md)
* [Incorporación de asignaciones](logic-apps-enterprise-integration-maps.md)
* [Migración desde BizTalk Services](logic-apps-move-from-mabs.md)
