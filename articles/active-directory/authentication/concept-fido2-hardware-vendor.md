---
title: Convertirse en un proveedor de claves de seguridad FIDO2 compatible con Microsoft para iniciar sesión en Azure AD
description: Aquí se explica el proceso para convertirse en asociado de hardware FIDO2
ms.date: 08/02/2021
services: active-directory
ms.service: active-directory
ms.subservice: authentication
author: knicholasa
ms.author: nichola
ms.topic: conceptual
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50d21b99fd1def56b60896d157c8a35b9381a003
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780495"
---
# <a name="become-a-microsoft-compatible-fido2-security-key-vendor"></a>Convertirse en un proveedor de claves de seguridad FIDO2 compatible con Microsoft

La mayoría de las brechas relacionadas con la piratería usan contraseñas robadas o débiles. A menudo, el equipo de TI aplicará una mayor complejidad de las contraseñas o cambios frecuentes de contraseña para reducir el riesgo de un incidente de seguridad. Sin embargo, esto aumenta los costos del departamento de soporte técnico y conduce a experiencias de usuario deficientes, ya que se requiere que los usuarios memoricen o almacenen contraseñas nuevas y complejas.

Las claves de seguridad FIDO2 ofrecen una alternativa. Las claves de seguridad FIDO2 pueden reemplazar las credenciales débiles por credenciales seguras de clave pública y privada con respaldo de hardware que no se pueden reutilizar, reproducir ni compartir entre servicios. Las claves de seguridad admiten escenarios de dispositivo compartido, lo que le permite llevar sus credenciales con usted y autenticarse de forma segura en un dispositivo Windows 10 unido a Azure Active Directory que forme parte de la organización.

Microsoft se asocia con proveedores de claves de seguridad FIDO2 para asegurarse de que los dispositivos de seguridad funcionen en Windows, el explorador Microsoft Edge y las cuentas Microsoft en línea, para habilitar la autenticación segura sin contraseña.

Puede convertirse en un proveedor de claves de seguridad FIDO2 compatible con Microsoft mediante el siguiente proceso.  Microsoft no se compromete a realizar actividades de comercialización con el asociado y evaluará la prioridad del asociado en función de la demanda del cliente.

1. En primer lugar, el autenticador debe tener una certificación FIDO2. No podremos trabajar con proveedores que no tengan una certificación FIDO2. Para más información sobre la certificación, visite este sitio web: [https://fidoalliance.org/certification/](https://fidoalliance.org/certification/)
2. Una vez que tenga una certificación FIDO2, rellene su solicitud a nuestro formulario aquí: [https://forms.office.com/r/NfmQpuS9hF](https://forms.office.com/r/NfmQpuS9hF). Nuestro equipo de ingeniería solo probará la compatibilidad de los dispositivos FIDO2. No realizaremos pruebas de la seguridad de las soluciones.
3. Una vez que se confirma un avance a la fase de pruebas, el proceso suele tardar entre 3 y 6 meses. Los pasos suelen implicar:
    - Debate inicial entre Microsoft y su equipo.
        - Comprobación de la certificación de FIDO Alliance o la ruta de acceso a la certificación si no está completa
        - Recepción de información general del dispositivo por parte del proveedor
    - Microsoft compartirá nuestros scripts de prueba con usted. Nuestro equipo de ingeniería podrá responder a sus preguntas si tiene alguna necesidad específica.
    - Completará y enviará todos los resultados aprobados al equipo de ingeniería de Microsoft.
    - Una vez que Microsoft lo confirme, enviará varios ejemplos de hardware o de la solución de cada dispositivo al equipo de ingeniería de Microsoft.
        - Tras la recepción, el equipo de ingeniería de Microsoft llevará a cabo la comprobación del script de prueba y el flujo de experiencia del usuario.
4. Tras superar correctamente todas las pruebas por parte del equipo de ingeniería de Microsoft, Microsoft confirmará que el dispositivo del proveedor aparece en [FIDO MDS](https://fidoalliance.org/metadata/).
5. Microsoft agregará la clave de seguridad FIDO2 al back-end de Azure AD y a nuestra lista de proveedores de FIDO2 aprobados.

## <a name="current-partners"></a>Asociados actuales

En la tabla siguiente se indican los asociados que son proveedores de claves de seguridad FIDO2 compatibles con Microsoft.

| **Proveedor** | **Vínculo** |
| --- | --- |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| Go-Trust ID | [https://www.gotrustid.com/](https://www.gotrustid.com/idem-key) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Kensington  | [https://www.kensington.com/solutions/product-category/why-biometrics/](https://www.kensington.com/solutions/product-category/why-biometrics/) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Nymi   | [https://www.nymi.com/product](https://www.nymi.com/product) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| Thales | [https://cpl.thalesgroup.com/access-management/authenticators/fido-devices](https://cpl.thalesgroup.com/access-management/authenticators/fido-devices) |
| Thetis | [https://thetis.io/collections/fido2](https://thetis.io/collections/fido2) |
| Token2 Switzerland | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |
| TrustKey Solutions | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net/) |
| Yubico | [https://www.yubico.com/solutions/passwordless/](https://www.yubico.com/solutions/passwordless/) |

## <a name="next-steps"></a>Pasos siguientes

[Compatibilidad de FIDO2](fido2-compatibility.md)

