---
title: Cifrado de los datos en reposo en la respuesta a preguntas personalizada
titleSuffix: Azure Cognitive Services
description: Microsoft ofrece claves de cifrado administradas por Microsoft y también le permite administrar las suscripciones de Cognitive Services con sus propias claves, llamadas claves administradas por el cliente (CMK). En este artículo se trata el cifrado de datos en la respuesta a preguntas personalizada y cómo habilitar y administrar CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: egeaney
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 8570a1160eddbed5b372daa529339ea3672482cb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093327"
---
# <a name="custom-question-answering-encryption-of-data-at-rest"></a>Cifrado de los datos en reposo en la respuesta a preguntas personalizada

La respuesta a preguntas cifra automáticamente los datos cuando se conservan en la nube, lo que ayuda a satisfacer los objetivos de cumplimiento y seguridad de la organización.

## <a name="about-encryption-key-management"></a>Información sobre la administración de claves de cifrado

De forma predeterminada, su suscripción usa claves de cifrado administradas por Microsoft. También puede administrar la suscripción con sus propias claves, que se denominan claves administradas por el cliente (CMK). Estas claves ofrecen mayor flexibilidad para crear, rotar, deshabilitar y revocar los controles de acceso. También permite auditar las claves de cifrado que se usan para proteger los datos. Si la opción CMK está configurada en su suscripción, dispondrá de un cifrado doble, lo que constituye un segundo nivel de protección, y al mismo tiempo podrá controlar la clave de cifrado mediante Azure Key Vault.

La respuesta a preguntas personalizada usa la [compatibilidad con CMK de Azure Search](../../../../search/search-security-manage-encryption-keys.md) y asocia automáticamente la clave proporcionada para cifrar los datos almacenados en el índice de Azure Search.

> [!IMPORTANT]
> El recurso de servicio Azure Search debe haberse creado después de enero de 2019 de enero y no puede estar en el nivel gratis (compartido). No se admite la configuración de claves administradas por el cliente en Azure Portal.

## <a name="enable-customer-managed-keys"></a>Habilitar claves administradas del cliente

Siga estos pasos para habilitar CMK:

1.  Vaya a la pestaña **Cifrado** del recurso de idioma con la característica de respuesta a preguntas personalizada habilitada.
2.  Seleccione la opción **Claves administradas de cliente**. Especifique los detalles de sus [claves administradas por el cliente](../../../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) y seleccione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Clave administrada por el cliente de respuesta a preguntas](../media/encrypt-data-at-rest/question-answering-cmk.png)
   
3.  Si se guarda correctamente, la clave administrada por el cliente se usará para cifrar los datos almacenados en el índice de Azure Search.

> [!IMPORTANT]
> Se recomienda establecer la clave administrada por el cliente en un servicio Azure Cognitive Search nuevo antes de crear knowledge bases. Si establece CMK en un recurso de idioma con bases de conocimiento existentes, es posible que pierda el acceso a ellas. En Azure Cognitive Search puede encontrar más información sobre cómo [trabajar con contenido cifrado](../../../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content).

> [!NOTE]
> Para solicitar la capacidad de usar claves administradas por el cliente, rellene y envíe el [formulario de solicitud de claves administradas por el cliente de Cognitive Services](https://aka.ms/cogsvc-cmk).

## <a name="regional-availability"></a>Disponibilidad regional

Las claves administradas por el cliente están disponibles en todas las regiones de Azure Search.

## <a name="encryption-of-data-in-transit"></a>Cifrado de datos en tránsito

El portal de Language Studio se ejecuta en el explorador del usuario. Cada acción desencadena una llamada directa a la API del servicio cognitivo correspondiente. Por lo tanto, la respuesta a preguntas es compatible con los datos en tránsito.

## <a name="next-steps"></a>Pasos siguientes

* [Cifrado en Azure Search mediante CMK en Azure Key Vault](../../../../search/search-security-manage-encryption-keys.md)
* [Cifrado de datos en reposo](../../../../security/fundamentals/encryption-atrest.md)
* [Más información sobre Azure Key Vault](../../../../key-vault/general/overview.md)
