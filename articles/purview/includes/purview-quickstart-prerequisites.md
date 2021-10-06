---
title: Archivo de inclusión
description: archivo de inclusión
services: purview
author: whhender
ms.author: whhender
ms.service: purview
ms.topic: include
ms.custom: include file
ms.date: 09/10/2021
ms.openlocfilehash: 8b2b6cf87380c0c89327a7cf0afd5331a140f836
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212499"
---
## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [suscripción gratuita](https://azure.microsoft.com/free/) antes de empezar.

* Un [inquilino de Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) asociado a la suscripción.

* La cuenta de usuario que use para iniciar sesión en Azure debe ser miembro de los roles *Colaborador* o *Propietario*, o bien ser *administrador* de la suscripción de Azure. Para ver los permisos que tiene en la suscripción, vaya a [Azure Portal](https://portal.azure.com), seleccione su nombre de usuario en la esquina superior derecha, seleccione el icono " **...** " para ver más opciones y, después, seleccione **Mis permisos**. Si tiene acceso a varias suscripciones, elija la correspondiente.

* Ninguna [directiva de Azure](../../governance/policy/overview.md) debe impedir la creación de **cuentas de almacenamiento** o **espacios de nombres del centro de eventos**. Purview implementará una cuenta de almacenamiento administrada y un centro de eventos cuando se cree. Si existe una directiva de bloqueo y debe permanecer vigente, siga nuestra [guía de etiquetas de excepción de Purview](../create-purview-portal-faq.md) y siga los pasos para crear una excepción para las cuentas de Purview.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta de Azure.
