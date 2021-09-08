---
title: Tutorial para pedir Azure Data Box Heavy | Microsoft Docs
description: En este tutorial obtendrá información sobre Azure Data Box Heavy, una solución híbrida que le permite importar datos locales en Azure y cómo pedir Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: alkohli
ms.localizationpriority: high
ms.custom: contperf-fy22q1
ms.openlocfilehash: d5334314626d29dc9e3047bc382d41fcfa318a1d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469525"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Tutorial: Pedir Azure Data Box Heavy


Azure Data Box Heavy es una solución híbrida que permite importar datos locales en Azure de forma rápida, fácil y confiable. Transfiera los datos a un dispositivo de almacenamiento de 770 TB (capacidad utilizable aproximada) que suministra Microsoft y, después, devuelva el dispositivo. Luego, dichos datos se cargan en Azure.

En este tutorial se describe cómo se puede solicitar un dispositivo Azure Data Box Heavy. En este tutorial, obtendrá información sobre lo siguiente:

> [!div class="checklist"]
> * Requisitos previos para Data Box Heavy
> * Solicitar un dispositivo Data Box Heavy
> * Seguimiento del pedido
> * Cancelar el pedido

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar el dispositivo, complete los siguientes requisitos previos de configuración tanto del servicio Data Box como del dispositivo.

### <a name="for-installation-site"></a>Para el sitio de la instalación

Antes de comenzar, asegúrese de que:

- El dispositivo entra por puertas y entradas estándar. Sin embargo, asegúrese de que el dispositivo puede pasar por todas las entradas. Las dimensiones del dispositivo son: ancho: 66,04 cm largo: 121,92 cm alto: 71,12 cm.
- Si la instalación se realiza en una planta que no sea la baja, necesita acceso al dispositivo a través de un ascensor o una rampa. El dispositivo pesa aproximadamente 226 kg.
- Asegúrese de que tiene una sitio llano en el centro de datos que se encuentra próximo a una conexión de red disponible con espacio para un dispositivo con esta superficie.

### <a name="for-service"></a>Para el servicio

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Para el dispositivo

Antes de comenzar, asegúrese de que:
- El dispositivo se ha desembalado.
- Tiene un equipo host conectado a la red del centro de datos. Data Box Heavy copiará los datos desde dicho equipo. El equipo host debe ejecutar un sistema operativo compatible, como se describe en [Azure Data Box Heavy system requirements](data-box-system-requirements.md) (Requisitos del sistema de Azure Data Box).
- Necesita tener un portátil con un cable RJ-45 para conectarse a la interfaz de usuario local y configurar el dispositivo. Use el portátil para configurar cada nodo del dispositivo una vez.
- El centro de datos debe tener una red de alta velocidad. Es muy recomendable tener una conexión de 10 GbE como mínimo.
- Necesita un cable de 40 Gbps o de 10 Gbps por nodo de dispositivo. Elija cables que sean compatibles con la interfaz de red [MCX314A-BCCT de Mellanox](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html):

    - Para el cable de 40 Gbps, el extremo del cable del dispositivo debe ser QSFP+.
    - Para el cable de 10 Gbps, necesitará un cable SFP+ que se conecte a un conmutador 10-G en un extremo, con un adaptador de QSFP+ a SFP+ (o el adaptador QSA) en el extremo que se conecta al dispositivo.
    - Los cables de alimentación se incluyen con el dispositivo.

## <a name="order-data-box-heavy"></a>Solicitar Data Box Heavy

[!INCLUDE [order-data-box-via-portal](../../includes/data-box-order-portal.md)]

## <a name="track-the-order"></a>Seguimiento del pedido

Tras realizar el pedido, puede hacer un seguimiento del estado del mismo desde Azure Portal. Vaya al pedido de Data Box Heavy y, después, vaya a **Información general** para ver el estado. El portal muestra el pedido en estado **Ordered** (Pedido).

Si el dispositivo no está disponible, recibe una notificación. Si el dispositivo está disponible, Microsoft identifica que está listo para el envío y prepara dicho envío. Durante la preparación del dispositivo, se producen las siguientes acciones:

- Se crean recursos compartidos de SMB para cada cuenta de almacenamiento asociada con el dispositivo.
- Para cada cuenta, se generan las credenciales de acceso, como el nombre de usuario y la contraseña.
- También se genera la contraseña del dispositivo, que le ayuda a desbloquear el dispositivo.
- Data Box Heavy está bloqueado para evitar el acceso no autorizado al dispositivo en todo momento.

Una vez que se completa la preparación del dispositivo, el portal muestra el pedido en estado **Processed** (Procesado).

![Pedido de Data Box Heavy procesado](media/data-box-overview/data-box-order-status-processed.png)

Luego, Microsoft prepara y envía el disco a través de un operador regional. Una vez que se realiza el envío, recibe un número de seguimiento. El portal muestra el orden en estado **Dispatched** (Enviado).

![Pedido de Data Box Heavy enviado](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Cancelar el pedido

Para cancelar el pedido, en Azure Portal, vaya a **Información general** y haga clic en **Cancelar** en la barra de comandos.

Después de realizar un pedido, puede cancelarlo en cualquier momento, siempre que no se encuentre en estado procesado.
 
Para eliminar un pedido cancelado, vaya a **Información general** y haga clic en **Eliminar** desde la barra de comandos.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información acerca de varios temas relacionados con Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Prerrequisitos
> * Solicitar Data Box Heavy
> * Seguimiento del pedido
> * Cancelar el pedido

En el siguiente tutorial aprenderá a configurar Data Box Heavy.

> [!div class="nextstepaction"]
> [Configuración de Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
