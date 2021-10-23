---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: kengaderdus
ms.openlocfilehash: 4ab45c09771c734a1670123a261a6c13b5759676
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040776"
---
## <a name="configure-logging"></a>registro

La biblioteca MSAL genera mensajes de registro que pueden ayudar a diagnosticar problemas. La aplicación puede configurar el registro. La aplicación también le da un control personalizado sobre el nivel de detalle y si se registran o no datos de la organización y personales. 

Se recomienda establecer una devolución de llamada del registro de MSAL y proporcionar una forma de que los usuarios envíen registros cuando tengan problemas de autenticación. MSAL ofrece varios niveles de detalle de registro:

- **Error**: indica que algo no ha funcionado bien y se ha generado un error. Este nivel se usa para depurar e identificar problemas.
- **Advertencia**: no se ha producido necesariamente un error, pero la información está pensada para diagnósticos e identificar problemas.
- **Información**: MSAL registra eventos diseñados para fines informativos y no necesariamente para la depuración.
- **Detallado**: este es el nivel predeterminado. MSAL registra todos los detalles del comportamiento de la biblioteca.

De forma predeterminada, el registrador de MSAL no captura datos personales u organizativos. La biblioteca ofrece la opción de habilitar el registro de datos personales y organizativos si decide hacerlo.
