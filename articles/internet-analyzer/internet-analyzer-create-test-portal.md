---
title: Creación de una prueba de Internet Analyzer mediante el portal | Microsoft Docs
description: En este artículo, aprenderá a crear su primera prueba de Internet Analyzer.
services: internet-analyzer
author: KumudD
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: kumud
ms.openlocfilehash: f19a72f40900ee1c876a8d6130a224be868d192e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114455095"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Creación de una prueba de Internet Analyzer mediante el portal (versión preliminar)

Hay dos maneras de crear un recurso de Internet Analyzer: mediante Azure Portal o mediante el uso de la [CLI](internet-analyzer-cli.md). Esta sección le ayuda a crear un nuevo recurso de Azure Internet Analyzer con la experiencia del portal.

> [!IMPORTANT]
> Esta versión preliminar pública se proporciona sin un acuerdo de nivel de servicio y no debe usarse para cargas de trabajo de producción. Puede que algunas características no se admitan, que tengan funcionalidades limitadas o que no estén disponibles en todas las ubicaciones de Azure. Para más información, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Antes de empezar

La versión preliminar pública está disponible para su uso global; sin embargo, el almacenamiento de datos está limitado a *Oeste de EE. UU. 2* durante la versión preliminar.

## <a name="basics"></a>Aspectos básicos

1. Obtenga acceso a la versión preliminar de Internet Analyzer siguiendo las instrucciones de **¿Cómo participar en la versión preliminar?** de las [preguntas frecuentes sobre Azure Internet Analyzer](internet-analyzer-faq.md).
2. En la página principal de [Azure Portal](https://preview.portal.azure.com), haga clic en **+ Crear un recurso**. Internet Analyzer solo está disponible actualmente en la versión preliminar de Azure Portal.
3. En la página **Nuevo**, busque "Internet Analyzer" en el campo *Buscar en Marketplace*.
4. Haga clic en **Internet Analyzer (versión preliminar)**. Asegúrese de que el publicador es *Microsoft* y la categoría es *Redes*.
5. En la página **Internet Analyzer (versión preliminar)**, haga clic en **Crear** para abrir la página **Crear una instancia de Internet Analyzer**.
6. Especifique los siguientes valores de configuración para el recurso de Internet Analyzer:

    * **Suscripción:** la suscripción de Azure que hospeda el nuevo recurso Internet Analyzer. **_Use el mismo identificador de suscripción que utilizó para solicitar el acceso a la versión preliminar._**
    * **Grupo de recursos:** el grupo de recursos de Azure en el que se creará el nuevo recurso Internet Analyzer. Si no tiene un grupo de recursos existente, puede crear uno.
    * **Nombre:** el nombre del nuevo perfil del recurso Internet Analyzer.
    * **Región:** la región pública de Azure en que se creará el recurso. Durante la versión preliminar, solo está disponible *Oeste de EE. UU. 2*.

7. Cuando termine de especificar la configuración del perfil, haga clic en **Revisar y Crear**.

## <a name="configuration"></a>Configuración

Completar los pasos básicos es un requisito previo para configurar una prueba e insertar el cliente de JavaScript. Una vez creado un perfil, vaya a **Configuración > Configuración** para configurar la primera prueba.

1. Asigne un nombre a la prueba en el cuadro **Nombre de la prueba**.
2. Agregue una descripción para la prueba en el campo **Descripción**.
3. Haga clic en **Configurar punto de conexión**: aparecerá una pestaña en el lado derecho. Seleccione el tipo de punto de conexión que quiere configurar: una sola región de Azure, varias regiones de Azure o un punto de conexión personalizado.

    >
    ***Puntos de conexión preconfigurados: combinaciones de una y varias regiones de Azure***
    * Seleccione una región o un conjunto de regiones de una [lista preconfigurada de puntos de conexión de Azure](internet-analyzer-faq.md).
    * A continuación, seleccione el tipo de aplicación o la arquitectura de entrega de contenidos que desea evaluar.
        * Una sola región de Azure: aceleración de sitios ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), almacenamiento en caché de contenido estático ([Azure CDN para Microsoft](https://azure.microsoft.com/services/cdn/)) o ninguno.
        * Varias regiones de Azure: aceleraciones de sitios ([Azure Front Door](https://azure.microsoft.com/services/frontdoor/)), direccionamiento de DNS ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Puntos de conexión personalizados***
    * Siga las instrucciones que aparecen en la página [Crear punto de conexión personalizado](internet-analyzer-custom-endpoint.md).
    * Pegue la ubicación de la dirección URL HTTPS de la imagen de un píxel en el portal.
    >

4. Haga clic en **Agregar** para agregar el punto de conexión a la prueba.
5. Repita los pasos del 1 al 4 para configurar el segundo punto de conexión. El punto de conexión B siempre se mide en relación al punto de conexión A: al configurar los puntos de conexión, tenga en cuenta qué punto de conexión debe ser el control de prueba.
6. Haga clic en el botón **Guardar** para guardar la prueba. Una vez que guarde una prueba, ya no podrá modificar los puntos de conexión de una prueba determinada.
7. Seleccione las pruebas que desea iniciar y haga clic en **Iniciar prueba**. Esto cambiará el valor del campo **_Estado_ *_ de las pruebas a _* _En ejecución_**. Puede iniciar las pruebas en cualquier momento, pero el cliente de JavaScript debe estar insertado para que la prueba comience a recopilar medidas.
8. Agregue más pruebas en cualquier momento. Tenga en cuenta que el cliente de JavaScript único no se generará hasta que se cree una prueba.

## <a name="embed-client"></a>Inserción del cliente

Para comenzar cualquier prueba, el cliente JavaScript debe estar insertado en la aplicación web. Después de configurar al menos una prueba, haga clic en **Revisar y crear**, vaya a **Configuración > Configuración** y copie el cliente JavaScript. Puede encontrar instrucciones específicas en la página [Inserción del cliente de Internet Analyzer](internet-analyzer-embed-client.md).  

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Preguntas frecuentes sobre Internet Analyzer](internet-analyzer-faq.md)
* Más información sobre la inserción del [cliente de Internet Analyzer](internet-analyzer-embed-client.md) y la creación de un [punto de conexión personalizado](internet-analyzer-custom-endpoint.md).
