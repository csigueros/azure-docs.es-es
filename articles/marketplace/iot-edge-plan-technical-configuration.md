---
title: Establecimiento de la configuración técnica del plan para una oferta del módulo IoT Edge en Azure Marketplace
description: Establezca la configuración técnica del plan para una oferta del módulo IoT Edge en Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: acae63eff8be41453c4483a2d8364531ee109f71
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955697"
---
# <a name="set-plan-technical-configuration-for-an-iot-edge-module-offer"></a>Establecimiento de la configuración técnica del plan para una oferta del módulo IoT Edge

El tipo de oferta del módulo IoT Edge es un tipo específico de contenedor que se ejecuta en un dispositivo IoT Edge. La pestaña **Configuración técnica** del plan le permite ofrecer información de referencia para el repositorio de imágenes de contenedor en [Azure Container Registry](https://azure.microsoft.com/services/container-registry/), junto con los valores de configuración que ayudan a los clientes a usar el módulo.

Una vez enviada la oferta, la imagen de contenedor de IoT Edge se copia en Azure Marketplace en un registro de contenedor público específico. Todas las solicitudes de los usuarios de Azure que usan su módulo se atienden desde el registro de contenedor público de Azure Marketplace, no desde su registro de contenedor privado.

Puede elegir como destino varias plataformas y proporcionar varias versiones a la imagen de contenedor del módulo por medio de etiquetas. Para obtener más información sobre etiquetas y control de versiones, consulte [Preparar los recursos técnicos del módulo IoT Edge](iot-edge-technical-asset.md).

## <a name="image-repository-details"></a>Detalles del repositorio de imágenes

Seleccione **Azure Container Registry** como el origen de la imagen.

Proporcione el **id. de suscripción de Azure** en el que se indica el uso de recursos y los servicios se facturan para la instancia de Azure Container Registry que incluye la imagen de contenedor. Puede encontrar este identificador en la [página Suscripciones](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) de Azure Portal.

Proporcione el [**nombre del grupo de recursos**](../azure-resource-manager/management/manage-resource-groups-portal.md) que contiene la instancia de Azure Container Registry con la imagen de contenedor. El grupo de recursos debe ser accesible en el identificador de suscripción (anterior). Puede encontrar el nombre en la página [Grupos de recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) en Azure Portal.

Proporcione el [**nombre de la instancia de Azure Container Registry**](../container-registry/container-registry-intro.md) que tiene su imagen de contenedor. El registro de contenedor debe estar presente en el grupo de recursos de Azure que proporcionó anteriormente. Proporcione solo el nombre del registro, no el nombre completo del servidor de inicio de sesión. Asegúrese de omitir la parte **azurecr.io** del nombre. Puede encontrar el nombre del registro en la [página Registros de contenedor](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) en Azure Portal.

Proporcione el [**Nombre de usuario de administrador de Azure Container Registry**](../container-registry/container-registry-authentication.md#admin-account) asociado a la instancia de Azure Container Registry que tiene su imagen de contenedor. El nombre de usuario y la contraseña (paso siguiente) son necesarios para asegurarse de que la empresa tiene acceso al registro. Para obtener el nombre de usuario y la contraseña de administrador, establezca la propiedad **admin-enabled** en **True** mediante la Interfaz de la línea de comandos (CLI) de Azure. Opcionalmente, puede establecer **Usuario administrador** en **Habilitar** en Azure Portal.

:::image type="content" source="media/iot-edge/example-iot-update-container-registry.png" alt-text="Muestra el cuadro de diálogo Actualizar registro de contenedor.":::

#### <a name="call-out-description"></a>Descripción destacada

1. Usuario administrador

<br>Proporcione la **contraseña de administrador de la instancia de Azure Container Registry** del nombre de usuario administrador asociado a la instancia de Azure Container Registry que tiene la imagen de contenedor. El nombre de usuario y la contraseña son necesarios para asegurarse de que la empresa tiene acceso al registro. Puede obtener la contraseña de Azure Portal a través de **Container Registry** > **Claves de acceso**, o bien de la CLI de Azure con el [comando show.](/cli/azure/acr/credential#az-acr-credential-show)

:::image type="content" source="media/iot-edge/example-iot-access-keys.png" alt-text="Muestra la pantalla de la clave de acceso en Azure Portal.":::

#### <a name="call-out-descriptions"></a>Descripciones destacadas

1. Claves de acceso
2. Nombre de usuario
3. Contraseña

Proporcione el **nombre del repositorio de Azure Container Registry** que tiene su imagen. Especifique el nombre del repositorio cuando inserte la imagen en el registro. Para encontrar el nombre del repositorio, puede ir a [Container Registry](https://azure.microsoft.com/services/container-registry/) > **página Repositorios**. Para obtener más información, consulte [Visualización de los repositorios de registros de contenedor en Azure Portal](../container-registry/container-registry-repositories.md). Una vez establecido el nombre, no se puede cambiar. Use un nombre único para cada oferta de su cuenta.

> [!NOTE]
> No se admiten instancias cifradas de Azure Container Registry para la certificación del módulo perimetral. Azure Container Registry debe crearse sin el cifrado habilitado.

## <a name="image-versions"></a>Versiones de la imagen

Los clientes deben poder obtener actualizaciones automáticamente de Azure Marketplace cuando publique una actualización. Si no quieren realizar la actualización, deben poder mantener una versión específica de la imagen. Para hacerlo, puede agregar nuevas etiquetas de imagen cada vez que realice una actualización en la imagen.

Seleccione **Agregar versión de imagen** para incluir una **etiqueta de imagen** que apunta a la versión más reciente de la imagen en todas las plataformas admitidas. Debe incluir al menos una etiqueta de versión (por ejemplo, que empiece por xx.xx.xx, donde xx es un número). Los clientes deben usar [etiquetas de manifiesto](https://github.com/estesp/manifest-tool) para dirigirse a varias plataformas. Todas las etiquetas a las que hace referencia una etiqueta de manifiesto también se deben agregar para que podamos cargarlas. Todas las etiquetas de manifiesto (excepto la etiqueta más reciente) deben comenzar por X.Y- o X.Y.Z-, donde X, Y, Z son números enteros. Por ejemplo, si una etiqueta más reciente apunta a `1.0.1-linux-x64`, `1.0.1-linux-arm32` y `1.0.1-windows-arm32`, estas seis etiquetas deben agregarse a este campo. Para obtener más información sobre etiquetas y control de versiones, vea [Preparar los recursos técnicos del módulo IoT Edge](iot-edge-technical-asset.md).

> [!TIP]
> No olvide agregar una etiqueta de prueba a la imagen para facilitar su identificación durante las pruebas.

## <a name="default-deployment-settings"></a>Configuración de implementación predeterminada

Defina la configuración más común para implementar el módulo IoT Edge (opcional). Para optimizar las implementaciones de los clientes, permítales que inicien su módulo IoT Edge directamente con esta configuración predeterminada.

**Rutas predeterminadas**. El centro de IoT Edge administra la comunicación entre los módulos, IoT Hub y los dispositivos. Puede definir rutas predeterminadas de entrada y salida de datos entre módulos y IoT Hub, lo que le ofrece flexibilidad para enviar mensajes donde deben ir sin necesidad de servicios adicionales para procesar los mensajes o escribir código adicional. Las rutas se construyen mediante pares de nombre-valor. Puede definir hasta cinco nombres de ruta predeterminados, cada uno de hasta 512 caracteres de longitud.

Use la [sintaxis de ruta](../iot-edge/module-composition.md#declare-routes) correcta en el valor de ruta (normalmente definida como FROM/message/* INTO $upstream). Esto significa que todos los mensajes enviados por cualquier módulo se envían a su instancia de IoT Hub. Para hacer referencia al módulo, use el nombre de módulo predeterminado, que será el **Nombre de la oferta** sin espacios ni caracteres especiales. Para hacer referencia a otros módulos aún no conocidos, use la convención <FROM_MODULE_NAME> para que los clientes sepan que deben actualizar esta información. Para obtener más información sobre las rutas de IoT Edge, consulte [Declaración de rutas](../iot-edge/module-composition.md#declare-routes).

Por ejemplo, si el módulo ContosoModule escucha las entradas de ContosoInput y emite los datos en ContosoOutput, tiene sentido definir las dos rutas predeterminadas siguientes:

- Nombre 1: ToContosoModule
- Valor 1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Nombre 2: FromContosoModuleToCloud
- Valor 2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Propiedades deseadas del módulo gemelo predeterminado**. Un módulo gemelo es un documento JSON que se encuentra en IoT Hub y que almacena la información de estado de una instancia del módulo, incluidas las propiedades deseadas. Las propiedades deseadas se usan junto con las propiedades notificadas para sincronizar la configuración o las condiciones del módulo. El back-end de solución puede establecer propiedades deseadas, y el módulo puede leerlas. El módulo también puede recibir notificaciones de cambios en las propiedades deseadas. Las propiedades deseadas se crean usando hasta cinco pares nombre-valor, y cada valor predeterminado debe tener menos de 512 caracteres. Puede definir hasta cinco propiedades deseadas gemelas nombre-valor. Los valores de las propiedades deseadas gemelas deben ser un JSON válido, sin escape, sin matrices y con una jerarquía anidada máxima de cuatro niveles. En un escenario donde un parámetro necesario para un valor predeterminado no tiene sentido (por ejemplo, la dirección IP del servidor de un cliente), puede agregar un parámetro como valor predeterminado. Para más información sobre las propiedades deseadas gemelas, consulte [Definición o actualización de las propiedades deseadas](../iot-edge/module-composition.md#define-or-update-desired-properties).

Por ejemplo, si un módulo admite una frecuencia de actualización configurable de forma dinámica mediante propiedades deseadas gemelas, tiene sentido definir la siguiente propiedad deseada gemela predeterminada:

- Nombre 1: RefreshRate
- Valor 1: 60

**Variables de entorno predeterminadas**. Las variables de entorno proporcionan información complementaria a un módulo que facilita el proceso de configuración. Las variables de entorno se crean mediante pares nombre-valor. Cada nombre y valor de la variable de entorno predeterminada debe tener menos de 512 caracteres, y puede definir hasta cinco. Si un parámetro necesario para un valor predeterminado no tiene sentido (por ejemplo, la dirección IP del servidor de un cliente), puede agregar un parámetro como valor predeterminado.

Por ejemplo, si un módulo exige aceptar los términos de uso para iniciarse, puede definir la variable de entorno siguiente:

- Nombre 1: ACCEPT_EULA
- Valor 1: Y

**Opciones de creación del contenedor predeterminadas**. Las opciones de creación del contenedor dirigen la creación del contenedor de Docker del módulo IoT Edge. IoT Edge admite las opciones de creación de contenedor de la API del motor de Docker. Consulte todas las opciones en [Lista de contenedores.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) El campo Opciones de creación debe ser JSON válido, sin escape y de menos de 512 caracteres.

Por ejemplo, si un módulo exige el enlace de puertos, defina las opciones de creación siguientes:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="samples"></a>Ejemplos

A continuación se muestra un ejemplo de los detalles del plan de Azure Marketplace (los precios mostrados son meramente ilustrativos y no pretenden reflejar los costos reales):

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan.png" alt-text="Ilustración de los detalles del plan de Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descripciones destacadas

1. Nombre de la oferta
2. Nombre del plan
3. Description del plan

<br>A continuación se muestra un ejemplo de los detalles del plan en Azure Portal (los precios mostrados son meramente ilustrativos y no pretenden reflejar los costos reales):

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan-details.png" alt-text="Ilustración de los detalles del plan en Azure Portal.":::

#### <a name="call-out-descriptions"></a>Descripciones destacadas

1. Nombre de la oferta
2. Nombre del plan
3. Description del plan

Seleccione **Guardar borrador** y, a continuación, **← Resumen del plan** en el menú de navegación izquierdo para volver a la página de información general del plan.

## <a name="next-steps"></a>Pasos siguientes

- Para **realizar una venta conjunta con Microsoft** (opcional), seleccione esta opción en el menú de navegación izquierdo. Para obtener más información, consulte [Involucración de asociados para realizar una venta conjunta](./co-sell-overview.md).
- Si no va a configurar la venta conjunta o ya ha terminado, es el momento de [revisar y publicar la oferta](review-publish-offer.md).