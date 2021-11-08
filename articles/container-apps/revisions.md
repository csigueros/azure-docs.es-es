---
title: Revisiones en la versión preliminar de Azure Container Apps
description: Descubra cómo se crean revisiones en Azure Container Apps.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 543d0465e4fe16aafa911d486d2099259ac8a769
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026660"
---
# <a name="revisions-in-azure-container-apps-preview"></a>Revisiones en la versión preliminar de Azure Container Apps

Una revisión es una instantánea inmutable de una aplicación de contenedor.

- Al implementar una aplicación de contenedor, se crea automáticamente la primera revisión.
- Cuando cambia la configuración de `template` de una aplicación de contenedor, se crean automáticamente nuevas revisiones.
- Aunque las revisiones son inmutables, se ven afectadas por los cambios en los valores de configuración global, que se aplican a todas las revisiones.

:::image type="content" source="media/revisions/azure-container-apps-revisions.png" alt-text="Azure Container Apps: contenedores":::

Las revisiones son más útiles cuando se habilita la [entrada](ingress.md) para que la aplicación de contenedor sea accesible a través de HTTP.  Las revisiones se suelen usar cuando se quiere dirigir el tráfico de una instantánea de la aplicación de contenedor a la siguiente. Entre las estrategias típicas de dirección del tráfico se incluyen las [pruebas A/B](https://wikipedia.org/wiki/A/B_testing) y la [implementación de BlueGreen](https://martinfowler.com/bliki/BlueGreenDeployment.html).

En el diagrama siguiente se muestra una aplicación de contenedor con dos revisiones.

:::image type="content" source="media/revisions/azure-container-apps-revisions-traffic-split.png" alt-text="Azure Container Apps: división del tráfico entre revisiones":::

En el escenario mostrado anteriormente se supone que la aplicación de contenedor está en el siguiente estado:

- La [entrada](ingress.md) está habilitada, lo que hace que la aplicación de contenedor esté disponible a través de HTTP.
- La primera revisión se implementa como _Revision 1_ (Revisión 1).
- Después de actualizar el contenedor, se activa una nueva revisión, _Revision 2_ (Revisión 2).
- Las [reglas de división del tráfico](revisions-manage.md#traffic-splitting) están configuradas para que _Revision 1_ (Revisión 1) reciba el 80 % de las solicitudes y _Revision 2_ (Revisión2) el 20 % restante.

## <a name="change-types"></a>Tipos de cambio

Los cambios realizados en una aplicación de contenedor están en una de estas dos categorías: *ámbito de revisión* y *ámbito de aplicación*. Los cambios del ámbito de revisión son cualquier cambio que desencadena una nueva revisión, mientras que los cambios del ámbito de aplicación no crean revisiones.

### <a name="revision-scope-changes"></a>Cambios del ámbito de revisión

Los siguientes tipos de cambios crean una revisión:

- Cambios en contenedores
- Adición o actualización de reglas de escalado
- Cambios en la configuración de Dapr
- Cualquier cambio que afecte a la sección `template` de la configuración

### <a name="application-scope-changes"></a>Cambios del ámbito de aplicación

Los siguientes tipos de cambios no crean una revisión:

- Cambios en las [reglas de división del tráfico](revisions-manage.md#traffic-splitting)
- La activación o desactivación de la [entrada](ingress.md)
- Cambios en los [valores secretos](secure-app.md)
- Cualquier cambio fuera de la sección `template` de la configuración

Aunque los cambios en los secretos son un cambio del ámbito de aplicación, las revisiones deben [reiniciarse](revisions.md) para que un contenedor reconozca los nuevos valores secretos.

## <a name="activation-state"></a>Estado de activación

Las nuevas revisiones permanecen activas hasta que las desactive o se establezca la aplicación de contenedor para desactivar automáticamente las revisiones antiguas.

- Las revisiones inactivas permanecen como registros de instantánea de la aplicación de contenedor en un estado determinado.
- Las revisiones inactivas no se cobran.
- Hasta 100 revisiones permanecen disponibles antes de purgarse.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Protección de una aplicación](get-started.md)
