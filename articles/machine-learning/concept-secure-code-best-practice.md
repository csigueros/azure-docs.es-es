---
title: Procedimientos recomendados de código seguro
titleSuffix: Azure Machine Learning
description: Conozca las posibles amenazas de seguridad que puedan existir al desarrollar para Azure Machine Learning, las mitigaciones y los procedimientos recomendados.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: db0696afe7a3b6ba0f28abd4c68f677e90e894b5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430029"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Procedimientos recomendados de código seguro con Azure Machine Learning

En Azure Machine Learning, puede cargar archivos y contenido desde cualquier origen a Azure. El contenido de cuadernos o scripts de Jupyter que carga puede leer datos de las sesiones, acceder a datos dentro de su organización en Azure o ejecutar procesos malintencionados en su nombre.

> [!IMPORTANT]
> Ejecute solo cuadernos o scripts de orígenes de confianza. Por ejemplo, en los que usted o su equipo de seguridad han revisado el cuaderno o script.

## <a name="potential-threats"></a>Amenazas posibles

El desarrollo con Azure Machine Learning a menudo implica entornos de desarrollo basados en la Web (Notebooks y Azure ML Studio). Al usar entornos de desarrollo basados en la Web, las posibles amenazas son las siguientes:

* [Creación de scripts de sitios (XSS)](https://owasp.org/www-community/attacks/xss/)

    * __Inyección de código DOM__: este tipo de ataque puede modificar la interfaz de usuario que se muestra en el explorador. Por ejemplo, al cambiar la forma en que el botón Ejecutar se comporta en una instancia de Jupyter Notebook.
    * __Token de acceso/cookies__: los ataques XSS también pueden acceder a las cookies del explorador y el almacenamiento local. El token de autenticación de Azure Active Directory (AAD) se almacena en el almacenamiento local. Un ataque XSS podría usar este token para realizar llamadas API en su nombre y luego enviar los datos a una API o un sistema externo.

* [Falsificación de solicitud entre sitios (CSRF)](https://owasp.org/www-community/attacks/csrf): este ataque puede reemplazar la dirección URL de una imagen o un vínculo por la dirección URL de un script o API malintencionados. Cuando se carga la imagen o cuando se hace clic en un vínculo, se realiza una llamada a la dirección URL.

## <a name="azure-ml-studio-notebooks"></a>Cuadernos de Azure ML Studio

Azure Machine Learning Studio proporciona una experiencia de cuaderno hospedado en el explorador. Las celdas de un cuaderno pueden generar documentos HTML o fragmentos que contienen código malintencionado.  El código se puede ejecutar cuando se representa el resultado.

__Amenazas posibles__:
* Creación de scripts de sitios (XSS)
* Falsificación de solicitud entre sitios (CSRF)

__Mitigaciones proporcionadas por Azure Machine Learning__:
* La __salida de la celda de código__ está en un espacio aislado en un elemento iframe. El elemento iframe impide que el script tenga acceso al DOM, las cookies o el almacenamiento de sesión primarios.
* El contenido de __celda de Markdown__ se limpia mediante la biblioteca dompurify. Esto impide que se ejecuten scripts malintencionados con celdas de Markdown.
* La __dirección URL de la imagen__ y los __vínculos de Markdown__ se envían a un punto de conexión de propiedad de Microsoft, que comprueba si hay valores malintencionados. Si se detecta un valor malintencionado, el punto de conexión rechaza la solicitud.

__Acciones recomendadas__:
* Compruebe que confía en el contenido de los archivos antes de cargarlos en Studio. Al cargar, debe confirmar que está cargando archivos de confianza.
* Al seleccionar un vínculo para abrir una aplicación externa, se le pedirá que confíe en la aplicación.

## <a name="azure-ml-compute-instance"></a>Instancia de proceso de Azure Machine Learning

La instancia de proceso de Azure Machine Learning hospeda __Jupyter__ y __Jupyter Lab__. Cuando usa cualquiera de las dos aplicaciones mencionadas, las celdas de un cuaderno o código pueden generar documentos HTML o fragmentos que contienen código malintencionado. El código se puede ejecutar cuando se representa el resultado. Las mismas amenazas se aplican también al usar __RStudio__ hospedado en una instancia de proceso.

__Amenazas posibles__:
* Creación de scripts de sitios (XSS)
* Falsificación de solicitud entre sitios (CSRF)

__Mitigaciones proporcionadas por Azure Machine Learning__:
* Ninguno. Jupyter y Jupyter Lab son aplicaciones de código abierto hospedadas en la instancia de proceso de Azure Machine Learning.

__Acciones recomendadas__:
* Compruebe que confía en el contenido de los archivos antes de cargarlos en Studio. Al cargar, debe confirmar que está cargando archivos de confianza.

## <a name="report-security-issues-or-concerns"></a>Informe de problemas o aspectos de seguridad 

Azure Machine Learning es válido en el programa de recompensa de Microsoft Azure. Para más información, visite  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure).

## <a name="next-steps"></a>Pasos siguientes

* [Seguridad de empresa para Azure Machine Learning](concept-enterprise-security.md)