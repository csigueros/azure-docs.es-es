---
title: Cuotas en Azure Static Web Apps
description: Más información sobre las cuotas asociadas a Azure Static Web Apps
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 10/13/2021
ms.author: cshoe
ms.openlocfilehash: 4565012ec824961f971f1f1fb34ab330ae11372c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999309"
---
# <a name="quotas-in-azure-static-web-apps"></a>Cuotas en Azure Static Web Apps

Existen las siguientes cuotas para Azure Static Web Apps.

| Característica                     | Plan gratuito        | Plan Estándar |
|-----------------------------|------------------|---------------|
| Ancho de banda incluido          | 100 GB al mes, por suscripción | 100 GB al mes, por suscripción |
| Ancho de banda de uso por encima del límite           | No disponible      | 0,20 USD por GB |
| Aplicaciones por suscripción a Azure | 10               | Sin límite |
| Tamaño de la aplicación                    | 250 MB           | 500 MB |
| Tamaño del plan                   | Tamaño máximo de aplicación de 500 MB para una sola implementación y 0,50 GB como máximo para todos los entornos de almacenamiento provisional y producción  | Tamaño máximo de aplicación de 500 MB para una sola implementación y 2,00 GB como máximo combinado en todos los entornos de ensayo y producción |
| Entornos de preproducción | 3                | 10 |
| Dominios personalizados              | 2 por aplicación        | 5 por aplicación |
| Autorización (roles integrados) | Usuarios finales ilimitados que pueden autenticarse con el rol `authenticated` integrado | Usuarios finales ilimitados que pueden autenticarse con el rol `authenticated` integrado |
| Autorización (roles integrados) | Máximo de 25 usuarios finales que pueden pertenecer a roles personalizados a través de [invitaciones](authentication-authorization.md?tabs=invitations#role-management) | Un máximo de 25 usuarios finales que pueden pertenecer a roles personalizados a través de [invitaciones](authentication-authorization.md?tabs=invitations#role-management) o usuarios finales ilimitados a los que se pueden asignar roles personalizados a través de una [función sin servidor](authentication-authorization.md?tabs=function#role-management) |

## <a name="github-storage"></a>Almacenamiento de GitHub

Los paquetes y las Acciones de GitHub usan el almacenamiento de GitHub, que tiene su propio conjunto de cuotas. Cuando crea un sitio de Azure Static Web Apps, GitHub almacena los artefactos para el sitio incluso después de la implementación.

Para obtener más información, consulte los siguientes recursos.

- [Administración del espacio de almacenamiento de acciones](https://github.community/t5/GitHub-Actions/Managing-Actions-storage-space/td-p/38944)
- [Acerca de la facturación para las Acciones de GitHub](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/about-billing-for-github-actions#about-billing-for-github-actions)
- [Administración del límite de gasto para las acciones de GitHub](https://help.github.com/github/setting-up-and-managing-billing-and-payments-on-github/managing-your-spending-limit-for-github-actions)

## <a name="next-steps"></a>Pasos siguientes

- [Información general](overview.md)
