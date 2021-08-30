---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: mimart
ms.openlocfilehash: 991883785974648597141f0333679b39477a6ff9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779865"
---
## <a name="configure-logging"></a>registro

La biblioteca MSAL genera mensajes de registro que pueden ayudar a diagnosticar problemas. La aplicación puede configurar el registro y tener un control personalizado sobre el nivel de detalle y si se registran o no datos de la organización y personales. 

Se recomienda establecer una devolución de llamada del registro de MSAL y proporcionar una forma de que los usuarios envíen registros cuando tengan problemas de autenticación. MSAL ofrece varios niveles de detalle de registro:

- Error: indica que algo no ha funcionado bien y se ha generado un error. Se usa para la depuración y la identificación de problemas.
- Advertencia: No se ha producido necesariamente un error, pero es necesario realizar un diagnóstico e identificar los problemas.
- Info: MSAL registrará los eventos de carácter informativo, no relacionados necesariamente con la depuración.
- Verbose: Predeterminada. MSAL registra todos los detalles del comportamiento de la biblioteca.

De forma predeterminada, el registrador de MSAL no captura datos personales u organizativos. La biblioteca ofrece la opción de habilitar el registro de datos personales y organizativos si decide hacerlo.



