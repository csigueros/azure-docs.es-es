---
title: Problemas conocidos con SFTP en Azure Blob Storage (versión preliminar) | Microsoft Docs
description: Obtenga información sobre las limitaciones y los problemas conocidos de la compatibilidad con el Protocolo de transferencia de archivos segura (SFTP) en Azure Blob Storage.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 11/15/2021
ms.author: normesta
ms.reviewer: ylunagaria
ms.openlocfilehash: c9c7a1395006aa7613aa40ff9ea7b402f69bbf5d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557220"
---
# <a name="known-issues-with-secure-file-transfer-protocol-sftp-support-in-azure-blob-storage-preview"></a>Problemas conocidos de la compatibilidad con el protocolo de transferencia de archivos segura (SFTP) en Azure Blob Storage (versión preliminar)

En este artículo se describen las limitaciones y los problemas conocidos de la compatibilidad con SFTP en Azure Blob Storage.

> [!IMPORTANT]
> La compatibilidad con SFTP se encuentra actualmente en versión preliminar y está disponible en [estas regiones](secure-file-transfer-protocol-support.md#regional-availability).
> 
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>
> Para inscribirse en la versión preliminar, visite [este formulario](https://forms.office.com/r/gZguN0j65Y).

## <a name="data-redundancy-options"></a>Opciones de redundancia de datos

- El almacenamiento con redundancia geográfica (GRS) y el almacenamiento con redundancia de zona geográfica (GZRS) no se admiten en las cuentas que tienen SFTP habilitado.

## <a name="authorization"></a>Authorization

- Los usuarios locales son la única forma de administración de identidades que se admite actualmente para el punto de conexión SFTP.

- Azure Active Directory (Azure AD), la firma de acceso compartido (SAS) y la autorización de clave de cuenta no se admiten para el punto de conexión SFTP.

- No se admiten listas de control de acceso (ACL) de tipo POSIX para el punto de conexión SFTP.

  > [!NOTE]
  > Una vez que los datos se ingieren en Azure Storage, puede usar toda la gama de opciones de seguridad de Azure Storage. Aunque los mecanismos de autorización como el control de acceso basado en roles (RBAC) y las listas de control de acceso no se admiten como medio para autorizar a un cliente SFTP que se conecta, se pueden usar para autorizar el acceso a través de herramientas de Azure (como Azure Portal, la CLI de Azure, los comandos de Azure PowerShell y AzCopy), así como el SDK de Azure y la API de REST de Azure. 

- No se admiten operaciones de nivel de cuenta como enumerar, colocar, obtener, crear o eliminar contenedores.
 
## <a name="networking"></a>Redes

- No se admiten puntos de conexión DNS con particiones.

- Para acceder a la cuenta de almacenamiento mediante SFTP, la red debe permitir el tráfico en el puerto 22.

- Cuando se configura un firewall, las conexiones de IP no permitidas no se rechazan según lo previsto. Sin embargo, si hay una conexión correcta para un usuario autenticado, se rechazarán todas las operaciones del plano de datos.

## <a name="supported-algorithms"></a>Algoritmos admitidos

| Clave de host | Intercambio de claves | Cifras/cifrado | Integridad/MAC | Clave pública |
|----------|--------------|--------------------|---------------|------------|
| rsa-sha2-256 | ecdh-sha2-nistp384 | aes128-gcm@openssh.com | hmac-sha2-256 | ssh-rsa |
| rsa-sha2-512 | ecdh-sha2-nistp256 | aes256-gcm@openssh.com | hmac-sha2-512 | ecdsa-sha2-nistp256 |
| ecdsa-sha2-nistp256 | diffie-hellman-group14-sha256 | aes128-cbc| | ecdsa-sha2-nistp384 |
| ecdsa-sha2-nistp384| diffie-hellman-group16-sha512 | aes256-cbc |  | 
||| aes192-cbc ||

## <a name="security"></a>Seguridad

- Las claves de host se publican [aquí](secure-file-transfer-protocol-host-keys.md). Durante la versión preliminar pública, las claves de host rotarán una vez al mes como máximo.

- Hay varias razones diferentes para ver la advertencia "La identificación del host remoto ha cambiado":

  - Se actualizó la clave de host remoto (las claves de host rotan periódicamente).
  
  - El cliente seleccionó un algoritmo de clave de host diferente al almacenado en el archivo local "known_hosts" de SSH. OpenSSH usará una clave de confianza si el host (account.blob.core.windows.net) coincide, incluso cuando el algoritmo no coincide necesariamente.
  
  - La cuenta de almacenamiento devuelve una conmutación por error a otra región.
  
  - El host remoto (account.blob.core.windows.net) se está simulando.

## <a name="integrations"></a>Integraciones

- No se admite la fuente de cambios.

- Las métricas de la cuenta, como las transacciones y la capacidad, están disponibles. Filtre los registros por operaciones para ver la actividad de SFTP.

- Network File System (NFS) 3.0 y SFTP no se pueden habilitar en la misma cuenta de almacenamiento.

## <a name="performance"></a>Rendimiento

- La carga del rendimiento con la configuración predeterminada puede ser lenta para algunos clientes. Se espera algo así porque SFTP es un protocolo de chat y envía pequeñas solicitudes de mensajes. Aumentar el tamaño del búfer y usar varias conexiones simultáneas puede mejorar considerablemente la velocidad. 

  - En el caso de WinSCP, puede usar un máximo de 9 conexiones simultáneas para cargar varios archivos. 

  - Para OpenSSH en Windows, puede aumentar el tamaño del búfer a 100 000: sftp -B 100000 testaccount.user1@testaccount.blob.core.windows.net. 

  - Para OpenSSH en Linux, puede aumentar el tamaño del búfer a 262 000: sftp -B 262000 -R 32 testaccount.user1@testaccount.blob.core.windows.net. 

- Hay un tiempo de espera de 4 minutos para las conexiones inactivas. Parecerá que OpenSSH deja de responder y, a continuación, se desconectará. Algunos clientes se vuelven a conectar automáticamente. 

- La carga del tamaño máximo de archivo está limitada por el tamaño del mensaje de cliente. Consulte los ejemplos que tiene a continuación: 

  - Mensaje de 32 000 (valor predeterminado de OpenSSH) * 50 000 bloques = 1,52 GB 

  - Mensaje de 100 000 (valor máximo de OpenSSH para Windows) * 50 000 bloques = 4,77 GB 

  - Mensaje de 256 000 (valor máximo de OpenSSH para Linux) * 50 000 bloques = 12,20 GB 

## <a name="other"></a>Otros

- No se admiten los vínculos simbólicos.

- PowerShell y la CLI de Azure y no se admiten. Puede aprovechar las plantillas del portal y de ARM en la versión preliminar pública.

## <a name="see-also"></a>Vea también

- [Compatibilidad con el protocolo de transferencia de archivos segura (SFTP) en Azure Blob Storage](secure-file-transfer-protocol-support.md)