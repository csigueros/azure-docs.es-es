---
title: 'Error de suscripciones no encontradas: inicio de sesión de Azure Portal'
description: Proporciona la solución al error No se encontraron suscripciones durante el inicio de sesión en Azure Portal.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/28/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a90285db3b95402581ba917cef0833e93da91f44
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425792"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal"></a>Error No se encontraron suscripciones durante el inicio de sesión en Azure Portal

Puede recibir un mensaje de error "No se encontraron suscripciones" al intentar iniciar sesión en [Azure Portal](https://portal.azure.com/). En este artículo se brinda una solución para este problema.

## <a name="symptom"></a>Síntoma

Al intentar iniciar sesión en [Azure Portal](https://portal.azure.com/),recibirá el siguiente mensaje de error: "No se encontraron suscripciones".

## <a name="cause"></a>Causa

Este problema se produce si seleccionó el directorio equivocado, o si la cuenta no tiene permisos suficientes.

## <a name="solution"></a>Solución

### <a name="scenario-error-message-is-received-in-the-azure-portal"></a>Escenario: se recibe un mensaje de error en [Azure Portal](https://portal.azure.com)

Para corregir este problema:

* Asegúrese de que se seleccionó el directorio correcto de Azure haciendo clic en la cuenta pertinente en la esquina superior derecha.

  ![Selección del directorio en la parte superior derecha de Azure Portal](./media/no-subscriptions-found/directory-switch.png)
* Si se ha seleccionado el directorio correcto de Azure pero sigue apareciendo el mensaje de error, [asigne el rol Propietario a la cuenta](../../role-based-access-control/role-assignments-portal.md).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).
