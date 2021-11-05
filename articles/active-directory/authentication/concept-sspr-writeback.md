---
title: 'Escritura diferida de contraseñas local con el autoservicio de restablecimiento de contraseña: Azure Active Directory'
description: Conozca cómo los eventos de restablecimiento o cambio de contraseña en Azure Active Directory pueden volver a escribirse en un entorno de directorio local.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.custom: ignite-fall-2021
ms.openlocfilehash: a695c5d207bb441bfc3393ee0c5c1222efac4e79
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063392"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>¿Cómo funciona la escritura diferida del autoservicio de restablecimiento de contraseña en Azure Active Directory?

El autoservicio de restablecimiento de contraseña (SSPR) de Azure Active Directory (Azure AD) permite a los usuarios restablecer sus contraseñas en la nube, pero la mayoría de las empresas también tienen un entorno de Active Directory Domain Services (AD DS) local para los usuarios. La escritura diferida de contraseñas permite que los cambios de contraseña en la nube se vuelvan a escribir en un directorio local en tiempo real mediante [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) o la [sincronización en la nube de Azure AD Connect](tutorial-enable-cloud-sync-sspr-writeback.md). Cuando los usuarios cambian o restablecen sus contraseñas mediante SSPR en la nube, las contraseñas actualizadas también se escriben en el entorno AD DS local.

> [!IMPORTANT]
> Este artículo teórico explica al administrador cómo funciona la escritura diferida del autoservicio de restablecimiento de contraseña. Los usuarios finales registrados para el restablecimiento de contraseña de autoservicio que necesiten volver a su cuenta deben ir a https://aka.ms/sspr.
>
> Si el equipo de TI no ha habilitado la capacidad para restablecer su propia contraseña, póngase en contacto con el departamento de soporte técnico para obtener ayuda adicional.

La escritura diferida de contraseñas se admite en entornos que usan los siguientes modelos de identidad híbrida:

* [Sincronización de hash de contraseñas](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Autenticación de paso a través](../hybrid/how-to-connect-pta.md)
* [Servicios de federación de Active Directory](../hybrid/how-to-connect-fed-management.md)

La escritura diferida de contraseñas ofrece las siguientes características:

* **Aplicación de directivas de contraseñas de Active Directory Domain Services local (AD DS)** : cuando un usuario restablece su contraseña, se comprueba que cumple la directiva de AD DS local antes de confirmarla en ese directorio. En esta revisión se incluye la comprobación del historial, la complejidad, la antigüedad, los filtros de contraseñas y otras restricciones de contraseña que se hayan definido en AD DS.
* **Comentarios sin retraso**: La escritura diferida de contraseñas es una operación sincrónica. Si, por algún motivo, la contraseña no cumple la directiva o no se puede restablecer o modificar, los usuarios reciben una notificación inmediata.
* **Permite el cambio de contraseña en el panel de acceso y Microsoft 365**: cuando los usuarios federados o con sincronización de hash de contraseña modifican las contraseñas expiradas o no expiradas, estas se vuelven a escribir en AD DS.
* **Admite la escritura diferida de contraseñas cuando un administrador las restablece desde Azure Portal**: siempre que un administrador restablece la contraseña de un usuario en [Azure Portal](https://portal.azure.com) y se trata de un usuario federado o con sincronización de hash de contraseña, la contraseña se vuelve a escribir en el entorno local. Esta funcionalidad no se admite actualmente en el Portal de administración de Office.
* **No requiere ninguna regla de firewall de entrada**: la escritura diferida de contraseñas usa una retransmisión de Azure Service Bus como canal de comunicación subyacente. Toda la comunicación es de salida a través del puerto 443.
* **Admite la implementación en paralelo de nivel de dominio** mediante [Azure AD Connect](tutorial-enable-sspr-writeback.md) o la [sincronización en la nube](tutorial-enable-cloud-sync-sspr-writeback.md) para dirigirse a distintos conjuntos de usuarios en función de sus necesidades, incluidos los usuarios que están en dominios desconectados.  

> [!NOTE]
> Las cuentas de administrador que se encuentran dentro de grupos protegidos en AD local se pueden utilizar con la escritura diferida de contraseñas. Los administradores pueden cambiar su contraseña en la nube, pero no pueden restablecer una contraseña olvidada. Para obtener más información sobre los grupos protegidos, consulte [Cuentas y grupos protegidos en Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Para empezar a trabajar con la escritura diferida de SSPR, complete uno o ambos de los tutoriales siguientes:

- [Tutorial: Habilitación de la escritura diferida del autoservicio de restablecimiento de contraseña (SSPR)](tutorial-enable-cloud-sync-sspr-writeback.md).
- [Tutorial: Habilitación de la escritura diferida del autoservicio de restablecimiento de contraseña de la sincronización en la nube de Azure Active Directory Connect en un entorno local (versión preliminar)](tutorial-enable-cloud-sync-sspr-writeback.md)

## <a name="azure-ad-connect-and-cloud-sync-side-by-side-deployment"></a>Implementación en paralelo de Azure AD Connect y la sincronización en la nube

Puede implementar Azure AD Connect y la sincronización en la nube en paralelo en dominios diferentes para dirigirse a distintos conjuntos de usuarios. Esto ayuda a los usuarios existentes con los cambios en la escritura diferida de contraseñas al agregar la opción en los casos en los que los usuarios están en dominios desconectados debido a una fusión o división de la empresa. Azure AD Connect y la sincronización en la nube se pueden configurar en dominios diferentes para que los usuarios de un dominio puedan usar Azure AD Connect mientras que los usuarios de otro dominio usan la sincronización en la nube. La sincronización en la nube también puede proporcionar una mayor disponibilidad porque no se basa en una sola instancia de Azure AD Connect. Para obtener una comparación de características entre las dos opciones de implementación, consulte [Comparación entre Azure AD Connect y la sincronización en la nube](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync).

## <a name="how-password-writeback-works"></a>Funcionamiento de la escritura diferida de contraseñas

Cuando una cuenta de usuario configurada para federación, la sincronización de hash de contraseñas (o, en el caso de una implementación de Azure AD Connect, la autenticación de paso a través) intenta restablecer o cambiar una contraseña en la nube, se producen las siguientes acciones:

1. Se comprueba qué tipo de contraseña tiene el usuario. Si la contraseña del usuario se administra en el entorno local:
   * Se comprueba si el servicio de escritura diferida está en funcionamiento. Si es así, el usuario puede continuar.
   * Si el servicio de escritura diferida no está activo, se informa al usuario de que la contraseña no se puede restablecer en este momento.
1. A continuación, el usuario realiza los pasos de autenticación adecuados y llega a la página **Restablecer contraseña**.
1. El usuario selecciona una nueva contraseña y la confirma.
1. Cuando el usuario selecciona **Enviar**, se cifra la contraseña de texto no cifrado con una clave pública creada durante el proceso de configuración de la escritura diferida.
1. La contraseña cifrada se incluye en una carga que se envía a través de un canal HTTPS a la instancia de Service Bus Relay específica del inquilino (que se establece automáticamente durante el proceso de configuración de la escritura diferida). Esta retransmisión está protegida por una contraseña generada aleatoriamente que solo la instalación local conoce.
1. Una vez que el mensaje llega a Service Bus, el punto de conexión de restablecimiento de contraseña se activa automáticamente y comprueba que tiene una solicitud de restablecimiento pendiente.
1. A continuación, el servicio busca al usuario mediante el atributo delimitador de la nube. Para que esta búsqueda se realice correctamente, deben cumplirse las siguientes condiciones:

   * El objeto de usuario debe existir en el espacio del conector de AD DS.
   * El objeto de usuario debe estar vinculado al objeto de metaverso (MV) correspondiente.
   * El objeto de usuario debe estar vinculado al objeto de conector de Azure AD correspondiente.
   * El vínculo del objeto de conector de AD DS a la máquina virtual debe incluir la regla de sincronización `Microsoft.InfromADUserAccountEnabled.xxx`.

   Cuando la llamada procede de la nube, el motor de sincronización usa el atributo **cloudAnchor** para buscar el objeto de espacio de conector de Azure AD. A continuación, sigue el vínculo para volver al objeto de máquina virtual y, posteriormente, sigue el vínculo para volver al objeto de AD DS. Dado que puede haber varios objetos de AD DS (bosques múltiples) para un mismo usuario, el motor de sincronización se basa en el vínculo `Microsoft.InfromADUserAccountEnabled.xxx` para seleccionar el objeto correcto.

1. Una vez que se encuentre la cuenta de usuario, se intenta restablecer la contraseña directamente en el bosque de AD DS correspondiente.
1. Si la operación de establecimiento de la contraseña se realiza correctamente, se notifica al usuario que se ha cambiado su contraseña.

   > [!NOTE]
   > Si el hash de contraseña del usuario se sincroniza con Azure AD mediante la sincronización de hash de contraseña, existe la posibilidad de que la directiva de contraseñas local sea menos segura que la directiva de contraseñas en la nube. En este caso, se aplica la directiva local. Esta directiva garantiza que la directiva local se aplique en la nube, sin importar si se usa la federación o la sincronización de hash de contraseñas para proporcionar el inicio de sesión único.

1. Si la operación de establecimiento de la contraseña no se realiza, un mensaje de error pide al usuario que vuelva a intentarla. La operación puede producir un error por los siguientes motivos:
    * El servicio estaba inactivo.
    * La contraseña seleccionada no cumple las directivas de la organización.
    * No se ha podido encontrar el usuario en el entorno local de AD DS.

   Los mensajes de error ofrecen instrucciones a los usuarios para que pueden intentar resolver la situación sin intervención del administrador.

## <a name="password-writeback-security"></a>Seguridad de la escritura diferida de contraseñas

La escritura diferida de contraseñas es un servicio muy seguro. Para garantizar que su información esté protegida, se habilita un modelo de seguridad de cuatro niveles tal y como se describe a continuación:

* **Service Bus Relay específico del inquilino**
   * Al configurar el servicio, se configura una instancia de Service Bus Relay específica del inquilino que está protegida por una contraseña segura generada aleatoriamente y a la que Microsoft nunca tiene acceso.
* **Clave de cifrado de contraseñas bloqueadas y criptográficamente seguras**
   * Una vez creada la instancia de Service Bus Relay, se crea una clave simétrica segura que se usa para cifrar la contraseña tal cual llega de la red. Esta clave solo reside en el almacén de secretos de su compañía en la nube, que se bloquea y audita de forma estricta como se haría con cualquier otra contraseña del directorio.
* **Seguridad de la capa de transporte (TLS) estándar del sector**
   1. Cuando se produce una operación de restablecimiento o cambio de contraseña, la contraseña de texto no cifrado se cifra con la clave pública.
   1. La contraseña cifrada se inserta en un mensaje HTTPS que se envía a Service Bus Relay a través de un canal cifrado con certificados TLS/SSL de Microsoft.
   1. Una vez que el mensaje llega a Service Bus, el agente local se activa y se autentica en Service Bus con la contraseña segura que se generó anteriormente.
   1. El agente local selecciona el mensaje cifrado y lo descifra con la clave privada.
   1. El agente local intenta definir la contraseña con SetPassword API de AD DS. Este paso permite aplicar su directiva de contraseñas local de AD DS (por ejemplo, complejidad, antigüedad, historial y filtros) en la nube.
* **Directivas de expiración de mensajes**
   * Si el mensaje se queda en Service Bus porque el servicio local está inactivo, se agotará el tiempo de espera y se eliminará después de varios minutos. El tiempo de espera y la eliminación del mensaje aumenta aún más la seguridad.

### <a name="password-writeback-encryption-details"></a>Detalles de cifrado de la escritura diferida de contraseñas

Después de que un usuario envíe un restablecimiento de contraseña, la solicitud de restablecimiento pasa por varias pasos de cifrado antes de que llegue al entorno local. Estos pasos de cifrado garantizan una seguridad y confiabilidad máximas del servicio. A continuación se detalla la descripción de estos pasos:

1. **Cifrado de contraseña con clave RSA de 2048 bits**: cuando el usuario envía una contraseña para que se escriba en diferido en el entorno local, se cifra la propia contraseña enviada con una clave RSA de 2048 bits.
1. **Cifrado de nivel de paquete con AES-GCM de 256 bits**: todo el paquete (la contraseña y los metadatos necesarios) se cifra mediante AES-GCM (con un tamaño de clave de 256 bits). El cifrado evita que cualquier persona que tenga acceso directo al canal subyacente de Service Bus vea o manipule el contenido.
1. **Toda la comunicación se realiza a través de TLS/SSL**: Toda comunicación con Service Bus tiene lugar en un canal SSL/TLS. Este cifrado protege el contenido de terceras personas no autorizadas.
1. **Sustitución de clave automática cada seis meses**: todas las claves se sustituyen cada seis meses, o cada vez que la escritura diferida de contraseñas se deshabilita y luego se vuelve a habilitar en Azure AD Connect, para garantizar la máxima seguridad y protección del servicio.

### <a name="password-writeback-bandwidth-usage"></a>Uso de ancho de banda de la escritura diferida de contraseñas

La escritura diferida de contraseñas es un servicio de bajo consumo de ancho de banda que solo envía solicitudes al agente local si se dan las siguientes circunstancias:

* Se envían dos mensajes al habilitar o deshabilitar la característica a través de Azure AD Connect.
* Se envía un mensaje cada cinco minutos como latido del servicio siempre que el servicio está en ejecución.
* Se envían dos mensajes cada vez que se envía una nueva contraseña:
   * El primer mensaje es una solicitud para realizar la operación.
   * El segundo mensaje, que contiene el resultado de la operación, se envía en las siguientes circunstancias:
      * Cada vez que se envía una nueva contraseña durante el autoservicio de restablecimiento de contraseña de un usuario.
      * Cada vez que se envía una nueva contraseña durante una operación de cambio de la contraseña de un usuario.
      * Cada vez que se envía una nueva contraseña durante el restablecimiento de contraseña de usuario que inició el administrador (solo desde los portales de administración de Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Consideraciones sobre el ancho de banda y el tamaño de mensaje

El tamaño de cada uno de los mensajes descritos anteriormente suele ser inferior a 1 KB. Incluso bajo cargas extremas, el propio servicio de escritura diferida de contraseñas solo usa como máximo unos kilobits por segundo del ancho de banda. Puesto que cada mensaje se envía en tiempo real solo cuando lo requiere una operación de actualización de contraseña, y dado que el tamaño del mensaje es tan pequeño, el uso de ancho de banda de la funcionalidad de escritura diferida es demasiado pequeño para tener ningún impacto cuantificable real.

## <a name="supported-writeback-operations"></a>Operaciones de reescritura admitidas

Las contraseñas se escriben en diferido en todas las situaciones siguientes:

* **Operaciones de usuario final admitidas**
   * Cualquier operación voluntaria de autoservicio de cambio de contraseña del usuario final.
   * Cualquier operación obligatoria de autoservicio de cambio de contraseña del usuario final (por ejemplo, la expiración de la contraseña).
   * Cualquier operación de autoservicio de restablecimiento de contraseña del usuario final que se origina en el [portal de restablecimiento de contraseñas](https://passwordreset.microsoftonline.com).

* **Operaciones de administrador admitidas**
   * Cualquier operación voluntaria de autoservicio de cambio de contraseña del administrador.
   * Cualquier operación obligatoria de autoservicio de cambio de contraseña del administrador (por ejemplo, la expiración de la contraseña).
   * Cualquier operación de autoservicio de restablecimiento de contraseña del administrador que se origina en el [portal de restablecimiento de contraseñas](https://passwordreset.microsoftonline.com).
   * Cualquier operación de restablecimiento de contraseña del usuario final iniciada por el administrador desde [Azure Portal](https://portal.azure.com).
   * Cualquier operación de restablecimiento de contraseña del usuario final que inicie el administrador desde [API de Microsoft Graph](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http).

## <a name="unsupported-writeback-operations"></a>Operaciones de reescritura no admitidas

Las contraseñas no se vuelven a escribir en ninguna de las situaciones siguientes:

* **Operaciones de usuario final no admitidas**
   * Cualquier usuario final que restablezca su propia contraseña mediante PowerShell versión 1, versión 2 o Microsoft Graph API.
* **Operaciones de administrador no admitidas**
   * Cualquier restablecimiento de contraseña del usuario final que inicie el administrador desde PowerShell, versión 1 o versión 2.
   * Cualquier operación de restablecimiento de contraseña del usuario final que inicie el administrador desde el [Centro de administración de Microsoft 365](https://admin.microsoft.com).
   * Ningún administrador puede usar la herramienta de restablecimiento de contraseña para restablecer su propia contraseña para la escritura diferida de contraseñas.

> [!WARNING]
> El uso de la casilla "El usuario debe cambiar la contraseña en el siguiente inicio de sesión" de las herramientas administrativas de AD DS local, como Usuarios y equipos de Active Directory o el Centro de administración de Active Directory, se admite como característica en versión preliminar de Azure AD Connect. Para más información, consulte [Implementación de la sincronización de hash de contraseñas con la sincronización de Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con la escritura diferida de SSPR, realice el siguiente tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Habilitación de la escritura diferida del autoservicio de restablecimiento de contraseña (SSPR)](./tutorial-enable-sspr-writeback.md).
