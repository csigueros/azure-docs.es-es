---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 2586f1a25cb5ed72ce8753efe8f10009ac85165d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570789"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configuración de la sesión de anclaje espacial en la nube

A continuación, vamos a configurar la sesión de anclaje espacial en la nube. En la primera línea, se establece el proveedor del sensor en la sesión. A partir de ahora, todos los delimitadores que se creen en la sesión se asociarán a un conjunto de lecturas del sensor. A continuación, se instancian los criterios de búsqueda de dispositivos cercanos y se inicializan para que coincidan con los requisitos de la aplicación. Por último, se indica a la sesión que use los datos del sensor al localizar los delimitadores mediante la creación de un monitor a partir de nuestros criterios de dispositivos cercanos.
