---
title: Configuración del punto de conexión privado en Azure Static Web Apps
description: Obtenga información sobre cómo configurar el acceso de punto de conexión privado para Azure Static Web Apps.
services: static-web-apps
author: burkeholland
ms.author: buhollan
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 7/28/2021
ms.openlocfilehash: 8d6f5e019852200068d4db342ef4ab533d8779b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780240"
---
# <a name="configure-private-endpoint-in-azure-static-web-apps"></a>Configuración del punto de conexión privado en Azure Static Web Apps

Puede usar un punto de conexión privado (también denominado vínculo privado) para restringir el acceso a la aplicación web estática de modo que solo sea accesible desde la red privada.

> [!NOTE]
> La compatibilidad con puntos de conexión privados en Static Web Apps está actualmente en versión preliminar.

## <a name="how-it-works"></a>Funcionamiento

Una instancia de Azure Virtual Network (VNet) es una red como la que podría tener en un centro de datos tradicional, pero los recursos de la red virtual se comunican entre sí de forma segura en la red troncal de Microsoft.

La configuración de Static Web Apps con un punto de conexión privado le permite usar una dirección IP privada de la red virtual. Una vez creado este vínculo, la aplicación web estática se integra en la red virtual. Como resultado, la aplicación web estática ya no está disponible para la red pública de Internet y solo es accesible desde máquinas de la red virtual de Azure.

> [!NOTE]
> Colocar la aplicación detrás de un punto de conexión privado significa que la aplicación solo está disponible en la región en la que se encuentra la red virtual. Como resultado, la aplicación ya no estará disponible en varios puntos de presencia.

> [!WARNING]
> Actualmente, los puntos de conexión privados solo protegen el entorno de producción. La compatibilidad con entornos de ensayo se agregará en una próxima actualización del servicio.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa.
  - [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una [red virtual de Azure](../virtual-network/quick-create-portal.md).
- Una aplicación implementada con [Azure Static Web Apps](./get-started-portal.md) que use el plan de hospedaje Estándar.

## <a name="create-a-private-endpoint"></a>Creación de un punto de conexión privado

En esta sección, creará un punto de conexión privado para la aplicación web estática.

> [!IMPORTANT]
> La aplicación web estática debe implementarse en el plan de hospedaje Estándar para usar puntos de conexión privados. Puede cambiar el plan de hospedaje desde la opción **Plan de hospedaje** del menú lateral.

1. En el portal, abra la aplicación web estática.

1. Seleccione la opción **Puntos de conexión privados** del menú lateral.

1. Haga clic en el botón **Agregar**.

1. En el cuadro de diálogo "Agregar un punto de conexión privado", escriba esta información:

   | Configuración                         | Valor                         |
   | ------------------------------- | ----------------------------- |
   | Nombre                            | Escriba **myPrivateEndpoint**.  |
   | Suscripción                    | Seleccione su suscripción.     |
   | Virtual Network                 | Seleccione la red virtual.  |
   | Subnet                          | Seleccione la subred.           |
   | Integración con una zona DNS privada | Deje el valor predeterminado de **Sí**. |

   :::image type="content" source="media/create-private-link-dialog.png" alt-text="./media/create-private-link-dialog.png":::

1. Seleccione **Aceptar**.

## <a name="testing-your-private-endpoint"></a>Prueba del punto de conexión privado

Puesto que la aplicación ya no está disponible públicamente, la única manera de acceder a ella es desde dentro de la red virtual. Para hacer las pruebas, configure una máquina virtual dentro de la red virtual y vaya al sitio.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre los puntos de conexión privados](../private-link/private-endpoint-overview.md)
