---
title: Preparación para el cambio de dirección IP de TLS/SSL
description: Si la dirección IP de TLS/SSL se va a cambiar, conozca qué debe hacer para que la aplicación continúe funcionando después del cambio.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 3712931f73463ec1a799f003b82197752a735136
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895290"
---
# <a name="how-to-prepare-for-a-tlsssl-ip-address-change"></a>Preparación de un cambio de dirección IP de TLS/SSL

Si ha recibido una notificación de que la dirección IP de TLS/SSL de la aplicación Azure App Service va a cambiar, siga las instrucciones de este artículo para liberar la dirección IP de TLS/SSL existente y asignar una nueva.

## <a name="release-tlsssl-ip-addresses-and-assign-new-ones"></a>Liberación de las direcciones IP de TLS/SSL y asignación de otras nuevas

1.  Abra [Azure Portal](https://portal.azure.com).

2.  En el menú de navegación izquierdo, seleccione **App Services**.

3.  Seleccione la aplicación de App Service en la lista.

4.  En el encabezado **Configuración**, haga clic en **Configuración de SSL** en el menú de navegación izquierdo.

1. En la sección de enlaces de TLS/SSL, seleccione el registro de nombre de host. En el editor que se abre, elija **SNI SSL** en el menú desplegable **Tipo de SSL** y haga clic en **Agregar enlace**. Cuando aparezca un mensaje de finalización correcta de la operación, se habrá liberado la dirección IP existente.

6.  En la sección **Enlaces SSL**, seleccione de nuevo el mismo registro de nombre de host con el certificado. En el editor que se abre, elija **SSL basada en IP** en el menú desplegable **Tipo de SSL** y haga clic en **Agregar enlace**. Cuando aparezca un mensaje de finalización correcta de la operación, habrá adquirido una nueva dirección IP existente.

7.  Si se configura un registro A (registro DNS que apunta directamente a la dirección IP) en el portal de registro de dominios (proveedor de DNS de terceros o Azure DNS), reemplace la dirección IP existente por la que se generó recientemente. Para encontrar la nueva dirección IP, siga las instrucciones de la sección siguiente.

## <a name="find-the-new-ssl-ip-address-in-the-azure-portal"></a>Busque la nueva dirección IP de SSL en Azure Portal

1.  Espere unos minutos y, a continuación, abra [Azure Portal](https://portal.azure.com).

2.  En el menú de navegación izquierdo, seleccione **App Services**.

3.  Seleccione la aplicación de App Service en la lista.

4.  En el encabezado **Configuración**, haga clic en **Propiedades** en el menú de navegación izquierdo y busque la sección denominada **Dirección IP virtual**.

5. Copie la dirección IP y vuelva a configurar el registro de dominios o mecanismo de IP.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se explica cómo preparar un cambio de dirección IP que Azure inició. Para más información sobre las direcciones IP en Azure App Service, consulte [direcciones IP de entrada y salida en Azure App Service](overview-inbound-outbound-ips.md).
