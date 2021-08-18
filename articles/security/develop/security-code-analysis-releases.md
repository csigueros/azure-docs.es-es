---
title: Versiones de Análisis de código de seguridad de Microsoft
description: En este artículo se describen las próximas versiones de la extensión Análisis de código de seguridad de Microsoft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 137e4cf1392db8b1ee10d4441769f602802d6ac6
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112115963"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Versiones y hoja de ruta de Análisis de código de seguridad de Microsoft

> [!Note]
> A partir del 1 de marzo de 2022, se retirará la extensión de seguridad de Code Analysis de Microsoft (MSCA). Los clientes existentes de MSCA conservarán su acceso a la extensión hasta el 1 de marzo de 2022. Consulte en el artículo sobre [herramientas de análisis de código fuente de OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) las opciones alternativas en Azure DevOps. Los clientes que planean migrar a GitHub pueden consultar el artículo sobre la [seguridad avanzada de GitHub](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

El equipo de Análisis de código de seguridad de Microsoft, en colaboración con soporte técnico Developer, está orgulloso de anunciar mejoras recientes y futuras a nuestra extensión MSCA.


## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential Scanner v2.0: Publicado en abril de 2020

### <a name="innovations--improvements"></a>Innovaciones y mejoras

- **Motor principal**

   - Actualización de rendimiento promedio del 25 % con tiempos de ejecución casi lineales
   - Búsqueda y clasificación basadas en contexto y evidencias para mayor precisión
   - Mejoras en las detecciones de contraseñas generales y la lógica de coincidencia para los marcadores de posición obvios (por ejemplo, fakePassword)

- **Cobertura**: Compatibilidad con más de 25 tipos de secretos, incluidos los siguientes más solicitados:

   - Frase de contraseña de certificado de cuenta de Fabric
   - Secreto de cliente/clave de API
   - Encabezado de autorización HTTP
   - Clave de acceso/secreto de cliente de Amazon S3
   - Token de acceso de cliente de Azure Active Directory
   - Clave de API/maestro de funciones de Azure
   - Clave de acceso Power BI
   - Patrón de contraseñas de plantillas de Azure Resource Manager

- **Salidas**

   - Compatibilidad con formatos de archivo de salida de archivo SARIF 2.1 y CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v1.6.0: Publicado en abril de 2020

### <a name="improvements"></a>Mejoras

- CARACTERÍSTICA: Actualice a la versión final de SARIF V2 (versión 2.1.16). Esta actualización habilita el almacenamiento en caché de resultados al pasar: hashes en la línea de comandos, una mejora significativa del rendimiento cuando se analizan de forma recursiva directorios con varias copias de destinos de examen.
- CORRECCIÓN DE ERRORES: Corrección de errores tipográficos en la salida BA2021.DoNotMarkWritableSectionsAsExecutable.
- RENDIMIENTO: Elimine la carga de PDB para todos los modos no mixtos para los ensamblados administrados, incluidos los archivos binarios de la biblioteca IL (precompilados).
- CORRECCIÓN DE FALSOS NEGATIVOS: Verifique que un PDB colocado junto con un binario coincida realmente con el binario analizado.
- CARACTERÍSTICA: Proporcione el argumento --local-symbol-directories para especificar las ubicaciones de búsqueda de PDB adicionales (locales, sin servidor de símbolos)
- CORRECCIÓN DE FALSOS POSITIVOS: Omita el análisis controlado por PDB para el ejecutable de arranque nativo de .NET Core generado (que no es código controlable por el usuario).

## <a name="whats-next-in-q3-cy20"></a>¿Novedades para el tercer trimestre del ejercicio fiscal 2020?

- Herramienta de análisis de seguridad de Java
- Herramienta de análisis de seguridad de Python
- ES Lint reemplazará a TS Lint para TypeScript y JavaScript
- Herramienta de análisis de plantillas de Resource Manager

## <a name="tool-deprecation-notification"></a>Notificación de desuso de la herramienta

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>La detección de riesgos de seguridad de Microsoft (MSRD) está en desuso a partir del 26 de junio de 2020.

El servicio de pruebas de vulnerabilidad ante datos aleatorios o inesperados de MSRD en desuso se sustituirá por una plataforma de pruebas de vulnerabilidad de desarrolladores autohospedados de código abierto para Azure. Actualmente, esta plataforma se está desarrollando y probando en colaboración con muchos de los equipos de producto principales de Microsoft. Esta plataforma de pruebas de vulnerabilidad ante datos aleatorios o inesperados integrará correctores y permitirá pruebas de aprendizaje y adaptables integradas en canalizaciones de CI/CD que crecen con el tiempo con los proyectos de software. La versión de código abierto de esta plataforma está programada para la segunda mitad de 2020.

## <a name="next-steps"></a>Pasos siguientes

Para más instrucciones sobre cómo incorporar e instalar Análisis de código de seguridad de Microsoft, consulte [nuestra guía de incorporación e instalación](security-code-analysis-onboard.md).

Si tiene más preguntas sobre la extensión y las herramientas que se ofrecen, consulte nuestra página de [preguntas más frecuentes.](security-code-analysis-faq.yml)
