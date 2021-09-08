---
title: Solución de problemas de certificados en Azure Stack Edge Pro con GPU | Microsoft Docs
description: En este artículo, se explica cómo solucionar problemas relacionados con los certificados en un dispositivo Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: b5a607b9c0a64c6872ab511f13034e9d6eeb86d7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360513"
---
# <a name="troubleshooting-certificate-errors"></a>Solución de problemas de certificados

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo, aprenderá a solucionar problemas comunes que surgen al instalar certificados en un dispositivo Azure Stack Edge Pro.

## <a name="common-certificate-errors"></a>Errores de certificados comunes

En la tabla siguiente, se muestran errores comunes relacionados con los certificados, así como sus detalles y posibles soluciones:

> [!NOTE]
> Las instancias de &#8220;{0}, {1},..., {n} &#8221; indican parámetros posicionales. Los parámetros posicionales tomarán valores en función de los certificados que esté usando.

| Código de error | Descripción |
|---|---|
| CertificateManagement_UntrustedCertificate | El certificado con el nombre de firmante {0} tiene una cadena de certificados rota. Cargue el certificado de la cadena de firmas antes de cargar este certificado.|
| CertificateManagement_DeviceNotRegistered| El dispositivo no está activado. Solo podrá cargar un certificado compatible cuando lo active.|
| CertificateManagement_ExpiredCertificate | El certificado con el tipo {0} ha caducado o caducará pronto. Compruebe la fecha de caducidad del certificado y, si es necesario, agregue un nuevo certificado.|
| CertificateManagement_FormatMismatch | El formato del certificado no es compatible. Compruebe el formato del certificado y, si es necesario, agregue un nuevo certificado.  Se esperaba {0}, se encontró {1}. |
| CertificateManagement_GenericError | No se pudo realizar la operación de administración de certificados. Vuelva a intentarlo en unos minutos. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. |
| CertificateManagement_HttpsBindingFailure | El certificado con el nombre de firmante {0} no pudo crear un canal https seguro. Compruebe el certificado cargado y, si es necesario, agregue otro nuevo. Este error se produce con el certificado del nodo del dispositivo.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | El certificado con el nombre de firmante {0} no debería tener el uso de la clave establecido en Firma de certificados. Compruebe el uso de la clave del certificado y, si es necesario, agregue un nuevo certificado. Este error se produce con el certificado de la cadena de firma. |
| CertificateManagement_IncorrectKeyNumber | El certificado con el nombre de firmante {0} tiene un número de clave incorrecto: {1}. Compruebe el número de clave del certificado y, si es necesario, agregue un nuevo certificado.|
| CertificateManagement_InvalidP7b | El archivo del certificado cargado no es válido.  Compruebe el formato del certificado y, si es necesario, agregue un nuevo certificado.|
| CertificateManagement_KeyAlgorithmNotRSA | Este certificado no usa el algoritmo de claves RSA. Solo se admiten certificados RSA. |
| CertificateManagement_KeySizeNotSufficient | El certificado con el nombre de firmante {0} tiene un tamaño de clave insuficiente: {1}. El tamaño de clave mínimo es 4096.|
| CertificateManagement_MissingClientOid | El certificado con el nombre de firmante {0} no tiene ningún OID de autenticación del cliente. Compruebe las propiedades del certificado y, si es necesario, agregue un nuevo certificado.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | El certificado con el nombre de firmante {0} no tiene el uso de la clave establecido en Firma digital. Compruebe las propiedades del certificado y, si es necesario, agregue un nuevo certificado. |
| CertificateManagement_MissingKeyCertSignKeyUsage | El certificado con el nombre de firmante {0} no tiene el uso de la clave establecido en Firma de certificados. Compruebe las propiedades del certificado y, si es necesario, agregue un nuevo certificado.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | El certificado con el nombre de firmante {0} no tiene el uso de la clave establecido en Cifrado de clave. Compruebe las propiedades del certificado y, si es necesario, agregue un nuevo certificado. |
| CertificateManagement_MissingServerOid | El certificado con el nombre de firmante {0} no tiene ningún OID de autenticación del servidor. Compruebe las propiedades del certificado y, si es necesario, agregue un nuevo certificado.|
| CertificateManagement_NameMismatch | El tipo de certificado no coincide. Ámbito esperado: {0}, ámbito encontrado {1}. Cargue el certificado adecuado.|
| CertificateManagement_NoPrivateKeyPresent | El certificado con el nombre de firmante {0} no tiene ninguna clave privada presente. Cargue un certificado. pfx con una clave privada.|
| CertificateManagement_NoRSACryptoPrivateKey | No se puede acceder a la clave privada del certificado con el nombre de sujeto {0}. Asegúrese de que usa un certificado compatible. Solo es compatible el Proveedor de servicios criptográficos de Microsoft RSA/SChannel. |
| CertificateManagement_NotSelfSignedCertificate | El certificado con el nombre de firmante {0} no está autofirmado. Los certificados raíz deben estar autofirmados |
| CertificateManagement_NotSupportedOnVirtualAppliance | Esta operación no puede realizarse en el dispositivo virtual. Este error indica que la firma solo tendrá lugar si Azure Data Box Gateway se ejecuta en el dispositivo Tactical Cloud. Este error se produce cuando el dispositivo se administra mediante Windows PowerShell.|
| CertificateManagement_SelfSignedCertificate | El certificado con el nombre de firmante {0} está autofirmado. Cargue un certificado que esté correctamente firmado.|
| CertificateManagement_SignatureAlgorithmSha1 | El certificado con el nombre de firmante {0} tiene un algoritmo de firma que no se admite. No se admite SHA1-RSA. |
| CertificateManagement_SubjectNamesInvalid | El certificado con el nombre de firmante {0} no tiene un nombre de firmante o unos nombres alternativos del firmante correctos para el certificado {1}. Compruebe el certificado cargado y, si es necesario, agregue otro nuevo. También debe comprobar el nombre de DNS para que coincida con los nombres de SANS.|
| CertificateManagement_UnreadableCertificate | No se pudo leer el certificado de tipo {0}. Este error se produce cuando el certificado es ilegible o está dañado. Agregue un nuevo certificado.|
| CertificateSubjectNotFound | No se encuentra ningún certificado con el nombre de firmante {0}. Agregue un nuevo certificado.|
| CertificateRotationGenericFailure | No se pudieron rotar uno o varios certificados. Vuelva a intentarlo en unos minutos. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft.|
| CertificateImportFailure | No se importó el certificado con la huella digital {0} en el nodo {1}. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft. |
| CertificateApplyFailure | No se aplicó el certificado con la huella digital {0} en el nodo {1}. Si el persiste el problema, póngase en contacto con el Soporte técnico de Microsoft.|
| NodeNotReachable | No se pudo validar el certificado en {0}. Compruebe el estado del software y el hardware del sistema.|


## <a name="next-steps"></a>Pasos siguientes

- Revise los [requisitos de los certificados](azure-stack-edge-gpu-certificate-requirements.md).
- [Solución de problemas mediante registros de dispositivos y pruebas de diagnóstico](azure-stack-edge-gpu-troubleshoot.md).
