---
title: Creación de una aplicación iOS
description: Pasos para crear una cuenta de Azure Maps y la primera aplicación de iOS.
author: deniseatmicrosoft
ms.author: v-dbogomolov
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 46e5ec41c0cea9a417b920e8441ff1129040c209
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389615"
---
#  <a name="create-an-ios-app-public-preview"></a>Creación de una aplicación iOS (versión preliminar pública)

En este artículo se muestra cómo agregar Azure Maps a una aplicación de iOS. Le guía por estos pasos básicos:

* Configure un entorno de desarrollo.
* Cree su propia cuenta de Azure Maps.
* Obtenga la clave principal de Azure Maps que se usará en la aplicación.
* Haga referencia a las bibliotecas de Azure Maps desde el proyecto.
* Agregue un control de Azure Maps a la aplicación.

## <a name="prerequisites"></a>Requisitos previos

* Cree una cuenta de Azure Maps, para lo que debe iniciar sesión en [Azure Portal](https://portal.azure.com/). Si no tiene una suscripción a Azure, [cree una cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* [Cree una cuenta de Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).
* [Obtenga una clave de suscripción principal](quick-demo-map-app.md#get-the-primary-key-for-your-account), también conocida como clave principal o clave de suscripción. Para más información sobre la autenticación en Azure Maps, consulte [Administración de la autenticación en Azure Maps](how-to-manage-authentication.md).
* Descargue [XCode desde Mac App Store](https://apps.apple.com/cz/app/xcode/id497799835?mt=12) gratis.

## <a name="create-an-azure-maps-account"></a>Crear una cuenta de Azure Maps

Cree una nueva cuenta de Azure Maps con los pasos siguientes:

1. En la esquina superior izquierda de [Azure Portal](https://portal.azure.com/), haga clic en **Crear un recurso**.

2. En el cuadro _Buscar en el Marketplace_, escriba **Azure Maps**.

3. En los _Resultados_, seleccione **Azure Maps**. Haga clic en el botón **Crear** que aparece debajo del mapa.

4. En la página **Create Maps Account** (Crear una cuenta de Azure Maps), escriba los siguientes valores:
   - La _suscripción_ que quiere usar para esta cuenta.
   - El nombre del _grupo de recursos_ para esta cuenta. Puede elegir _Crear nuevo_ o _Usar existente_ para el grupo de recursos.
   - El _nombre_ de la nueva cuenta.
   - El _Plan de tarifa_ de la cuenta.
   - Lea la _licencia_ y la _declaración de privacidad_ y active la casilla para aceptar los términos.
   - Haga clic en el botón **Crear**.

   :::image source="./media/quick-ios-app/create-account.png" alt-text="Captura de pantalla para crear una cuenta de Azure Maps.":::

## <a name="get-the-primary-key-for-your-account"></a>Obtención de la clave principal de una cuenta

Una vez que se haya creado correctamente la cuenta de Maps, recupere la clave principal que le permite consultar las API de Maps.

1. Abra su cuenta de Maps en el portal.

2. En la sección de configuración, seleccione **Autenticación**.

3. Copie la **clave principal** al Portapapeles. Guárdela localmente para usarla más adelante en este tutorial.

   > [!Note]
   > Si usa la clave de suscripción de Azure en lugar de la clave principal de Azure Maps, la asignación no se representará correctamente. Además, por motivos de seguridad, se recomienda que rote entre las claves principal y secundaria. Para rotar las claves, actualice la aplicación para que use la clave secundaria, impleméntela y, luego, presione el botón de ciclo/actualización situado junto a la clave principal para generar una nueva clave principal. La clave principal anterior se deshabilitará. Para más información sobre la rotación de claves, consulte [Configuración de Azure Key Vault con la auditoría y la rotación de claves](../key-vault/secrets/tutorial-rotation-dual.md).

   :::image source="./media/quick-ios-app/get-key.png" alt-text="Captura de pantalla para obtener la clave de suscripción.":::

## <a name="create-a-project-in-xcode"></a>Creación de un proyecto en Xcode

En primer lugar, cree un nuevo proyecto de aplicación de iOS. Siga estos pasos para crear un proyecto de Xcode:

1. En **Archivo**, seleccione **Nuevo** -> **Proyecto**.

2. En la pestaña **iOS**, seleccione **Aplicación** y, a continuación, seleccione **Siguiente**.

3. Escriba el nombre de la aplicación, el identificador de lote y seleccione **Siguiente**.

   Para obtener más ayuda con la creación de un proyecto, consulte [Creación de un proyecto de Xcode para una aplicación](https://developer.apple.com/documentation/xcode/creating-an-xcode-project-for-an-app).

:::image source="./media/quick-ios-app/create-app.png" alt-text="Cree la primera aplicación iOS.":::

## <a name="install-the-azure-maps-ios-sdk"></a>Instalación del SDK de iOS para Azure Maps

El siguiente paso en la creación de la aplicación es instalar el SDK de iOS para Azure Maps. Para instalar el SDK, siga estos pasos:

1. En el **explorador de proyectos**, seleccione el archivo del proyecto.

2. En el **editor de proyectos**, seleccione el proyecto.

3. Cambie a la pestaña **Paquete de Swift**.

4. Agregue el SDK de iOS para Azure Maps: `{link goes here}`.

   :::image source="./media/quick-ios-app/add-project.png" alt-text="Captura de pantalla para agregar un proyecto de iOS.":::
  
## <a name="add-mapcontrol-view"></a>Adición de la vista MapControl

1. Agregue `UIView` personalizado para ver el controlador.

2. Seleccione la clase `MapControl` del módulo `AzureMapsControl`.

   :::image source="./media/quick-ios-app/add-map-control.png" alt-text="Captura de pantalla para agregar control de Azure Maps.":::

3. En el archivo **AppDelegate.swift**, tendrá que:

   - Agregar la importación del SDK de Azure Maps.
   - Establecer la información de autenticación de Azure Maps.
   
   La configuración de la información de autenticación en la clase AzureMaps de forma global mediante los métodos `AzureMaps.configure(subscriptionKey:)` o `AzureMaps.configure(aadClient:aadAppId:aadTenant:)` hace que no tenga que agregar la información de autenticación en cada vista.

4. Seleccione el botón de ejecución, como se indica en el siguiente gráfico (o presione `CMD` + `R`), para compilar la aplicación.

   :::image source="./media/quick-ios-app/run.png" alt-text="Captura de pantalla de la ejecución de la aplicación iOS.":::

   Xcode tardará unos segundos en crear la aplicación. Una vez finalizada la compilación, puede probar la aplicación en el dispositivo iOS simulado. Debería ver un mapa como el siguiente:

   :::image source="./media/quick-ios-app/example.png" alt-text="Captura de pantalla del primer mapa en la aplicación iOS.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

> [!WARNING]
> Los tutoriales que se enumeran en la sección [Pasos siguientes](#next-steps) describen cómo usar y configurar Azure Maps con su cuenta. Si tiene pensado seguir con los tutoriales, no elimine los recursos que se crearon con este inicio rápido.

Si no tiene previsto continuar con los tutoriales, siga estos pasos para realizar la limpieza de recursos:

1. Cierre Xcode y elimine el proyecto que ha creado.
2. Si ha probado la aplicación en un dispositivo externo, desinstálela en ese dispositivo.

Si no planea continuar desarrollando con el SDK de iOS para Azure Maps:

1. Vaya a la página de Azure Portal. En la página principal del portal, seleccione **Todos los recursos**. O bien, haga clic en el icono de menú en la esquina superior izquierda. Seleccione **Todos los recursos**.
2. Haga clic en la cuenta de Azure Maps. Haga clic en **Eliminar** en la parte superior de la página.
3. Si no planea seguir desarrollando aplicaciones de iOS, puede desinstalar Xcode.

<!--
For more code examples, see these guides:

*  [Manage authentication in Azure Maps](how-to-manage-authentication.md)
*  [Change map styles in iOS maps](Set%20map%20style%20%28iOS%20SDK%29.md)
*  [Add a symbol layer](Add%20a%20symbol%20layer%20%28iOS%20SDK%29.md)
*  [Add a line layer](Add%20a%20line%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
*  [Add a polygon layer](Add%20a%20polygon%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
-->

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado la cuenta de Azure Maps y ha creado una aplicación de demostración. Consulte los siguientes tutoriales para más información sobre Azure Maps:

> [!div class="nextstepaction"]
> [Carga de datos GeoJSON en Azure Maps](tutorial-load-geojson-file-android.md)
