---
title: Seguridad de la capa de transporte en Azure Site Recovery
description: Aprenda a habilitar Azure Site Recovery para usar el protocolo de cifrado Seguridad de la capa de transporte (TLS) y proteger los datos cuando se transfieran por una red.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: 3937fd9f88a844c0257bc6cb66b4c3f97a112987
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594633"
---
# <a name="transport-layer-security-in-azure-site-recovery"></a>Seguridad de la capa de transporte en Azure Site Recovery

Seguridad de la capa de transporte (TLS) es un protocolo de cifrado que protege los datos cuando se transfieren a través de una red. Azure Site Recovery utiliza TLS para proteger la privacidad de los datos que se están transfiriendo. Azure Site Recovery ahora usa el protocolo TLS 1.2 para mejorar la seguridad.

## <a name="enable-tls-on-older-versions-of-windows"></a>Habilitación de TLS en versiones anteriores de Windows

Si la máquina ejecuta versiones anteriores de Windows, no olvide instalar las actualizaciones correspondientes tal y como se detalla a continuación y realizar los cambios del Registro que se indican en los artículos de KB correspondientes.

|Sistema operativo  |artículo de KB |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>La actualización instala los componentes necesarios para el protocolo. Después de la instalación, para habilitar los protocolos necesarios, debe actualizar las claves del Registro tal y como se indica en los artículos de KB anteriores.

## <a name="verify-windows-registry"></a>Comprobación del Registro de Windows

### <a name="configure-schannel-protocols"></a>Configuración de protocolos de SChannel

Las siguientes claves del Registro garantizan que el protocolo TLS 1.2 está habilitado en el nivel de componente de SChannel:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>De forma predeterminada, las claves del Registro anteriores se establecen en los valores que se muestran en Windows Server 2012 R2 y versiones posteriores. En estas versiones de Windows, si las claves del Registro no están presentes, no es necesario crearlas.

### <a name="configure-net-framework"></a>Configuración de .NET Framework

Utilice las siguientes claves del Registro para configurar .NET Framework de forma que admita la criptografía segura. Puede obtener más información sobre la [configuración de .NET Framework aquí](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry).

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="why-enable-tls-12"></a>¿Por qué habilitar TLS 1.2?

TLS 1.2 es más seguro que los protocolos criptográficos anteriores, como SSL 2.0, SSL 3.0, TLS 1.0 y TLS 1.1. Los servicios de Azure Site Recovery son totalmente compatibles con TLS 1.2.

### <a name="what-determines-the-encryption-protocol-used"></a>¿Qué determina el protocolo de cifrado usado?

La versión de protocolo más alta admitida por el cliente y el servidor se negocia para establecer la conversación cifrada. Para obtener más información sobre el protocolo de enlace TLS, vea [Establecimiento de una sesión segura mediante TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-if-tls-12-is-not-enabled"></a>¿Qué ocurre si TLS 1.2 no está habilitado?

Para mejorar la seguridad frente a ataques que podrían producirse por cambiar a una versión anterior del protocolo, Azure Site Recovery está empezando a deshabilitar las versiones de TLS anteriores a la 1.2. Esto forma parte de un cambio a largo plazo en los servicios para prohibir las conexiones de protocolos heredados y conjuntos de cifrado. Los servicios y los componentes de Azure Site Recovery son totalmente compatibles con TLS 1.2. Sin embargo, las versiones de Windows que carecen de actualizaciones necesarias o de ciertas configuraciones personalizadas pueden seguir impidiendo la oferta de protocolos TLS 1.2. Esto puede causar errores, incluidos, entre otros, uno o varios de los siguientes:

- La replicación puede producir un error en el origen.
- Error 10054 al conectar componentes de Azure Site Recovery (el host remoto cerró forzosamente una conexión existente).
- Los servicios relacionados con Azure Site Recovery no se inician o no se detienen del modo habitual.

## <a name="additional-resources"></a>Recursos adicionales

- [Protocolo Seguridad de la capa de transporte](/windows/win32/secauthn/transport-layer-security-protocol)
- [Garantía de la compatibilidad con TLS 1.2 en sistemas operativos implementados](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- Para obtener más información, consulte [Procedimientos recomendados sobre la seguridad de la capa de transporte (TLS) con .NET Framework](/dotnet/framework/network-programming/tls).