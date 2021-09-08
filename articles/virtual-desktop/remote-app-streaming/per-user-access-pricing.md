---
title: 'Inscripción de una suscripción de Azure Virtual Desktop en los precios de acceso por usuario: Azure'
description: Procedimientos para inscribirse en los precios de acceso por usuario para Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a3a72975db0a08d8bf3d35cdc8c177ef4230fd23
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220180"
---
# <a name="enroll-your-subscription-in-per-user-access-pricing"></a>Inscripción de la suscripción en los precios de acceso por usuario

Para que los usuarios externos puedan conectarse a una implementación, es preciso inscribir una suscripción en los precios de acceso por usuario. Los precios de acceso por usuario permiten a los usuarios que no pertenezcan a su organización acceder a las aplicaciones y escritorios de su suscripción. La suscripción inscrita se cobrará todos los meses en función del número de usuarios distintos que se conectan a los recursos de Azure Virtual Desktop.

## <a name="how-to-enroll"></a>Cómo inscribirse

Para inscribir una suscripción de Azure en los precios de acceso por usuario:

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com/).

2. Escriba **Azure Virtual Desktop** en la barra de búsqueda y, luego, busque y seleccione **Azure Virtual Desktop** en Servicios.

3. En la página de información general de **Azure Virtual Desktop**, seleccione **Per-user access pricing** (Precios de acceso por usuario).

4. En la lista de suscripciones, seleccione la suscripción en la que va a implementar los recursos de Azure Virtual Desktop.

5. Seleccione **Enroll** (Inscribir).

6. Revise los Términos del producto y, después, seleccione **Enroll** (Inscribir) para comenzar la inscripción. El proceso de inscripción puede tardar hasta una hora en finalizar.

7. Una vez realizada la inscripción, compruebe el valor de la columna **Per-user access pricing** (Precios de acceso por usuario) de la lista de suscripciones para asegurarse de que ha cambiado de "Enrolling" (Inscribir) a "Enrolled" (Inscrito).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los precios de acceso por usuario, consulte [Descripción de las licencias y los precios de acceso por usuario](licensing.md). Si desea aprender calcular los costos de streaming de aplicaciones por usuario para la implementación, consulte [Estimación de los costos de streaming de aplicaciones por usuario para Azure Virtual Desktop](streaming-costs.md). Para calcular los costos totales de implementación, consulte [Descripción de los costos totales de implementación de Azure Virtual Desktop](total-costs.md). 
