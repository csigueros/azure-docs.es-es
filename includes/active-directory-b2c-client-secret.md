---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: kengaderdus
ms.openlocfilehash: a83277bb3f4965d0559b9de7f3a07b1b3f856f97
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036427"
---
#### <a name="app-registrations"></a>[Registros de aplicaciones](#tab/app-reg-ga/) 

1. En **Administrar**, seleccione **Certificados y secretos**.
1. Seleccione **Nuevo secreto de cliente**.
1. Escriba una descripción para el secreto de cliente en el cuadro **Descripción**. Por ejemplo, *clientsecret1*.
1. En **Expira**, seleccione el tiempo durante el cual el secreto es válido y, a continuación, seleccione **Agregar**.
1. Registre el **Valor** del secreto. Este valor se usa para la configuración en un paso posterior.

#### <a name="applications-legacy"></a>[Aplicaciones (heredado)](#tab/applications-legacy/)

1. En **ACCESO DE API**, seleccione **Claves**.
1. Escriba una descripción para la clave en el cuadro **Descripción de la clave**. Por ejemplo, *clientsecret1*.
1. Seleccione una **Duración** para la validez y, luego, **Guardar**.
1. Anote el **VALUE** de la clave. Este valor se usa para la configuración en un paso posterior.