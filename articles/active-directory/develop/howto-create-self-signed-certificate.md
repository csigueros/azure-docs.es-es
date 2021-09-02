---
title: Creación de un certificado público autofirmado para autenticar la aplicación | Azure
titleSuffix: Microsoft identity platform
description: Cree un certificado público autofirmado para autenticar la aplicación.
services: active-directory
author: FaithOmbongi
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: ombongifaith
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: scenarios:getting-started
ms.openlocfilehash: 55076f382b24c8bcf994b452e8c26e5bb6cc23b9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781251"
---
# <a name="how-to-create-a-self-signed-public-certificate-to-authenticate-your-application"></a>Procedimiento: Creación de un certificado público autofirmado para autenticar la aplicación

Azure Active Directory admite dos tipos de autenticación disponibles para las entidades de servicio: **autenticación basada en contraseña** (secreto de aplicación) y **autenticación basada en certificados**. Aunque los secretos de la aplicación se pueden crear fácilmente en Azure Portal, se recomienda que la aplicación use un certificado.

Para las pruebas, puede usar un certificado público autofirmado en lugar de un certificado firmado por la entidad de certificación (CA). En este artículo se muestra cómo usar Windows PowerShell para crear y exportar un certificado autofirmado.

> [!CAUTION]
> El uso de un certificado autofirmado solo se recomienda para el desarrollo, no para producción.

Va a configurar varios parámetros para el certificado. Por ejemplo, los algoritmos criptográficos y algoritmos hash, el período de validez del certificado y el nombre de dominio. A continuación, exporte el certificado con o sin su clave privada en función de las necesidades de la aplicación. 

La aplicación que inicia la sesión de autenticación requiere la clave privada, mientras que la aplicación que confirma la autenticación requiere la clave pública. Por lo tanto, si va a autenticarse desde la aplicación de escritorio de PowerShell en Azure AD, solo exportará la clave pública (archivo `.cer`) y la cargará en Azure Portal. La aplicación de PowerShell usa la clave privada del almacén de certificados local para iniciar la autenticación y obtener tokens de acceso para Microsoft Graph.

Es posible que la aplicación también se esté ejecutando desde otra máquina, como Azure Automation. En este escenario, exportará el par de claves pública y privada desde el almacén de certificados local, cargará la clave pública en Azure Portal y la clave privada (un archivo `.pfx`) en Azure Automation. La aplicación que se ejecuta en Azure Automation usará la clave privada para iniciar la autenticación y obtener tokens de acceso para Microsoft Graph.

En este artículo se usa el cmdlet `New-SelfSignedCertificate` de PowerShell para crear el certificado autofirmado y el cmdlet `Export-Certificate` para exportarlo a una ubicación fácilmente accesible. Estos cmdlets están integrados en versiones modernas de Windows (Windows 8.1 y versiones posteriores, y Windows Server 2012R2 y versiones posteriores). El certificado autofirmado tendrá la siguiente configuración:

+ Una longitud de clave de 2048 bits. Aunque se admiten valores más largos, se recomienda encarecidamente el tamaño de 2048 bits para obtener la mejor combinación de seguridad y rendimiento.
+ Usa el algoritmo criptográfico de RSA. Azure AD solo admite actualmente RSA.
+ El certificado se firma con el algoritmo hash SHA256. Azure AD también admite certificados firmados con algoritmos hash SHA384 y SHA512.
+ El certificado solo es válido durante un año.
+ El certificado se admite para su uso para la autenticación de cliente y servidor.

> [!NOTE]
> Para personalizar la fecha de inicio y expiración, así como otras propiedades del certificado, consulte la [referencia de `New-SelfSignedCertificate`](/powershell/module/pki/new-selfsignedcertificate?view=windowsserver2019-ps&preserve-view=true).


## <a name="option-1--create-and-export-your-public-certificate-without-a-private-key"></a>Opción 1: Crear y exportar el certificado público sin una clave privada

Use el certificado que cree con este método para autenticarse desde una aplicación que se ejecuta desde la máquina. Por ejemplo, para autenticarse desde Windows PowerShell.

En un símbolo del sistema de PowerShell con privilegios elevados, ejecute el siguiente comando y deje abierta la sesión de la consola de PowerShell. Reemplace `{certificateName}` por el nombre que desee dar al certificado.

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

La variable **$cert** del comando anterior almacena el certificado en la sesión actual y le permite exportarlo. El comando siguiente exporta el certificado en formato `.cer`. También puede exportarlo en otros formatos admitidos en Azure Portal incluidos `.pem` y `.crt`.

```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

El certificado ya está listo para cargarse en Azure Portal. Una vez cargado, recupere la huella digital del certificado para usarla para autenticar la aplicación.


## <a name="option-2-create-and-export-your-public-certificate-with-its-private-key"></a>Opción 2: Crear y exportar el certificado público con su clave privada

Use esta opción para crear un certificado y su clave privada si la aplicación se va a ejecutar desde otra máquina o nube, como Azure Automation.

En un símbolo del sistema de PowerShell con privilegios elevados, ejecute el siguiente comando y deje abierta la sesión de la consola de PowerShell. Reemplace `{certificateName}` por el nombre que desee dar al certificado.

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

La variable **$cert** del comando anterior almacena el certificado en la sesión actual y le permite exportarlo. El comando siguiente exporta el certificado en formato `.cer`. También puede exportarlo en otros formatos admitidos en Azure Portal incluidos `.pem` y `.crt`.


```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

Todavía en la misma sesión, cree una contraseña para la clave privada del certificado y guárdela en una variable. En el siguiente comando, reemplace `{myPassword}` por la contraseña que desea usar para proteger la clave privada del certificado.

```powershell

$mypwd = ConvertTo-SecureString -String "{myPassword}" -Force -AsPlainText  ## Replace {myPassword}

```

Ahora, con la contraseña que ha almacenado en la variable `$mypwd`, proteja y exporte la clave privada.

```powershell

Export-PfxCertificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{privateKeyName}.pfx" -Password $mypwd   ## Specify your preferred location and replace {privateKeyName}

```

El certificado (archivo `.cer`) ya está listo para cargarse en Azure Portal. También tiene una clave privada (archivo `.pfx`) que está cifrada y que otras entidades no pueden leer. Una vez cargado, recupere la huella digital del certificado para usarla para autenticar la aplicación.


## <a name="optional-task-delete-the-certificate-from-the-keystore"></a>Tarea opcional: elimine el certificado del almacén de claves.

Si creó el certificado con la opción 2, puede eliminar el par de claves de su almacén personal. En primer lugar, ejecute el siguiente comando para recuperar la huella digital del certificado.

```powershell

Get-ChildItem -Path "Cert:\CurrentUser\My" | Where-Object {$_.Subject -Match "{certificateName}"} | Select-Object Thumbprint, FriendlyName    ## Replace {privateKeyName} with the name you gave your certificate

```

A continuación, copie la huella digital que se muestra y úsela para eliminar el certificado y su clave privada.

```powershell

Remove-Item -Path Cert:\CurrentUser\My\{pasteTheCertificateThumbprintHere} -DeleteKey

```

### <a name="know-your-certificate-expiry-date"></a>Conocer la fecha de expiración del certificado

El certificado autofirmado que creó siguiendo los pasos anteriores tiene una duración limitada antes de expirar. En la sección **Registros de aplicaciones** de Azure Portal, la pantalla **Certificados y secretos** muestra la fecha de expiración del certificado. Si usa Azure Automation, la pantalla **Certificados** de la cuenta de Automation muestra la fecha de expiración del certificado. Siga los pasos anteriores para crear un certificado autofirmado.

## <a name="next-steps"></a>Pasos siguientes

[Administración de certificados para inicio de sesión único federado en Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
