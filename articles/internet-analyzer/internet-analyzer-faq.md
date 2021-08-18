---
title: Preguntas frecuentes sobre Internet Analyzer | Microsoft Docs
description: Las preguntas más frecuentes sobre Azure Internet Analyzer.
services: internet-analyzer
author: KumudD
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: kumud
ms.openlocfilehash: 9a9bedc1dcc1d217a3f7dac270245afa923482d8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454839"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Preguntas frecuentes sobre Azure Internet Analyzer (versión preliminar)

Estas son las preguntas más frecuentes sobre Azure Internet Analyzer: si tiene alguna otra pregunta, vaya al [foro de comentarios](https://aka.ms/internetAnalyzerFeedbackForum) y publique su pregunta. Si una pregunta es frecuente, se agrega a este artículo para que se pueda encontrar de forma rápida y sencilla.

## <a name="how-do-i-participate-in-the-preview"></a>¿Cómo participo en la versión preliminar?

La versión preliminar está disponible solo para algunos clientes. Si está interesado en participar en la versión preliminar, siga los pasos que se describen a continuación:

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com).
2. Vaya a la página de **Suscripciones**.
3. Haga clic en la suscripción de Azure con la que planea usar Internet Analyzer.
4. Vaya a la configuración de **Proveedores de recursos** en la suscripción.
5. Busque **Microsoft.Network** y haga clic en el botón **Registrar** (o en el botón **Volver a registrar**).
![solicitud de acceso](./media/ia-faq/request-preview-access.png)

6. [Solicite la aprobación](https://aka.ms/internetAnalyzerContact) al proporcionarnos su dirección de correo electrónico y el identificador de suscripción de Azure que usó para solicitar acceso.
7. Una vez aprobada la solicitud, recibirá una confirmación por correo electrónico y podrá crear, actualizar o modificar los recursos de Internet Analyzer desde la suscripción de Azure recién permitida.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>¿Es necesario instalar el cliente para ejecutar una prueba?

Sí, el cliente de Internet Analyzer debe estar instalado en su aplicación para recopilar métricas específicas de los usuarios. [Vea cómo instalar el cliente.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>¿Me cobrarán por participar en la versión preliminar?
No, el uso de la versión preliminar de Azure Internet Analyzer es gratuito. Durante la versión preliminar no hay ningún contrato de nivel de servicio.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>¿Para qué escenarios está diseñado Internet Analyzer?

Internet Analyzer está diseñado para proporcionar información sobre el rendimiento de la red en función de la población de usuarios. Para ayudar a tomar las mejores decisiones de rendimiento para los usuarios, Internet Analyzer compara el rendimiento de dos puntos de conexión de Internet con su propia población de usuarios. Aunque Internet Analyzer puede responder a muchas preguntas, algunas de las más habituales son:

* ¿Cuál es el impacto, en cuanto a rendimiento, de la migración a la nube? 
    * *Prueba sugerida: personalizado (la infraestructura local actual) frente a Azure (cualquier punto de conexión preconfigurado)*
* ¿Qué valor aporta poner los datos en el borde, en lugar de hacerlo en un centro de datos? 
    *  *Prueba sugerida: Azure frente a Azure Front Door, Azure frente a Azure CDN de Microsoft*
* ¿Cuál es el beneficio, en cuanto a rendimiento, de usar Azure Front Door?
    *  *Prueba sugerida: personalizado/Azure/CDN frente a Azure Front Door*
* ¿Cuál es el beneficio, en cuanto a rendimiento, de usar Azure CDN de Microsoft? 
    *  *Prueba sugerida: personalizado/Azure/AFD frente a Azure CDN de Microsoft*
* ¿Cómo funciona Azure CDN de Microsoft? 
    *  *Prueba sugerida: personalizado (otro punto de conexión de CDN) frente a Azure CDN de Microsoft*
* ¿Cuál es la mejor nube para su población de usuarios finales en cada región? 
    *  *Prueba sugerida: personalizado (otro servicio en la nube) frente a Azure (cualquier punto de conexión preconfigurado)*

## <a name="which-tests-can-i-run-in-preview"></a>¿Qué pruebas se pueden ejecutar en la versión preliminar?

Cada prueba que cree en Internet Analyzer consta de dos puntos de conexión: el punto de conexión A y el punto de conexión B. Cualquiera de las siguientes combinaciones se puede ejecutar como prueba:  
* Dos puntos de conexión preconfigurados,
* Un punto de conexión personalizado y uno preconfigurado, o bien
* Dos [puntos de conexión personalizados](internet-analyzer-custom-endpoint.md) (un punto de conexión personalizado debe residir en Azure).

Los siguientes puntos de conexión preconfigurados están disponibles durante la versión preliminar:
* **Regiones de Azure**
    * Sur de Brasil
    * Centro de la India
    * Centro de EE. UU.
    * Este de Asia
    * Este de EE. UU.
    * Japón Occidental
    * Norte de Europa
    * Norte de Sudáfrica
    * Sudeste de Asia
    * Norte de Emiratos Árabes Unidos
    * Oeste de Reino Unido  
    * Oeste de Europa
    * Oeste de EE. UU.
    * Oeste de EE. UU. 2
* **Varias combinaciones de regiones de Azure**
    * Este de EE. UU., Sur de Brasil
    * Este de EE. UU., Asia Oriental
    * Oeste de Europa, Sur de Brasil
    * Oeste de Europa, Sudeste Asiático
    * Oeste de Europa, Norte de Emiratos Árabes Unidos
    * Oeste de EE. UU., Este de EE. UU.
    * Oeste de EE. UU., Oeste de Europa
    * Oeste de EE. UU., Norte de Emiratos Árabes Unidos
    * Oeste de Europa, Norte de Emiratos Árabes Unidos, Sudeste Asiático
    * Oeste de EE. UU., Oeste de Europa, Asia Oriental
    * Oeste de EE. UU., Norte de Europa, Sudeste Asiático, Norte de Emiratos Árabes Unidos, Norte de Sudáfrica 
* **Azure + Azure Front Door**: implementados en una o varias de las combinaciones de regiones de Azure de la lista anterior.
* **Azure + Azure CDN de Microsoft**: implementados en una o varias de las combinaciones de regiones de Azure de la lista anterior.
* **Azure + Azure Traffic Manager**: implementados en cualquier combinación de varias de las regiones de Azure de la lista anterior.

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>¿En qué se diferencia Internet Analyzer de otros servicios de supervisión proporcionados por Azure?

Internet Analyzer le ayuda a comprender el rendimiento de los usuarios finales y a tomar decisiones para mejorar su rendimiento. Aunque otras herramientas de supervisión de Azure proporcionan información detallada sobre los servicios de Azure, Internet Analyzer se centra en medir el rendimiento de Internet de un extremo a otro para los usuarios.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>¿Cómo se administran los datos de medición mediante Internet Analyzer?

Azure tiene [procesos de seguridad exhaustivos y satisface un amplio intervalo de estándares de cumplimiento](https://azure.microsoft.com/support/trust-center/). Solo usted y el equipo que designe tienen acceso a sus datos. El personal de Microsoft puede acceder a ellos de forma restringida solo en circunstancias concretas y siempre con su conocimiento. Los datos se cifran en tránsito y en reposo.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte la [Información general de Internet Analyzer](internet-analyzer-overview.md).
