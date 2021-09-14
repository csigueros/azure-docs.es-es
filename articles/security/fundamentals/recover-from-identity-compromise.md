---
title: Uso de los recursos de seguridad de Microsoft y Azure para ayudar a recuperarse ante una vulnerabilidad sistémica por identidad comprometida | Microsoft Docs
description: Aprenda a usar los recursos de seguridad de Microsoft y Azure, como Microsoft 365 Defender, Azure Sentinel, Azure Active Directory y Azure Security Center, así como las recomendaciones de Microsoft para proteger su sistema de las vulnerabilidades sistémicas por identidad comprometida similares al ataque de Nobelium (Solorigate) de diciembre de 2020.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: f2f99e7c9a901b6e76b3d4bbe87967527f836d1b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864349"
---
# <a name="recovering-from-systemic-identity-compromise"></a>Recuperación de un riesgo de identidad sistemático

En este artículo se describen los recursos y las recomendaciones de Microsoft para recuperarse de un ataque de identidad sistémico contra su organización, como el ataque de [Nobelium](https://aka.ms/solorigate) de diciembre de 2020.

El contenido de este artículo se basa en las instrucciones proporcionadas por el equipo de detección y respuesta (DART) de Microsoft, cuya labor se centra en responder a los compromisos y ayudar a los clientes a ser resistentes a los ataques cibernéticos. Para obtener más instrucciones del equipo de DART, consulte su [serie de blogs sobre la seguridad de Microsoft](https://www.microsoft.com/security/blog/microsoft-detection-and-response-team-dart-blog-series/).

Muchas organizaciones han pasado a un enfoque basado en la nube para gozar de una mayor seguridad en la administración de identidades y acceso. Sin embargo, puede que su organización tenga también sistemas locales activos y que use distintos métodos de arquitectura híbrida. En este artículo se parte del hecho de que los ataques de identidad sistémicos afectan a los sistemas híbridos, locales y en la nube, y se proporcionan recomendaciones y referencias para todos estos entornos.

> [!IMPORTANT]
> Esta información se proporciona tal y como está y constituye una orientación general; la determinación final sobre cómo aplicar esta guía a su entorno de TI y sus inquilinos debe tener en cuenta su entorno y necesidades únicos, que cada cliente está en la mejor posición para determinar.
>

## <a name="about-systemic-identity-compromise"></a>Acerca de la vulnerabilidad sistémica de identidad comprometida

Un ataque de identidad sistémico en una organización se produce cuando un atacante logra entrar en la administración de la infraestructura de identidad de la organización.

Si esto le ha ocurrido a su organización, se enfrenta al atacante en una carrera para proteger su entorno antes de que se pueda hacer más daños.

- **Los atacantes con control administrativo sobre la infraestructura de identidad de un entorno** pueden usar ese control para crear, modificar o eliminar identidades y permisos de identidad en ese entorno.

    En una vulnerabilidad local, si los certificados de firma de tokens de SAML de confianza *no* se almacenan en un [HSM](../../key-vault/keys/hsm-protected-keys.md), el ataque incluye acceso a esos certificados.

- **Los atacantes pueden usar el certificado para falsificar los tokens SAML** y suplantar cualquiera de los usuarios y cuentas existentes de la organización sin necesidad de acceder a las credenciales de la cuenta y sin dejar ninguna huella.

- También se puede usar el **acceso a cuentas con privilegios elevados** para agregar credenciales controladas por el atacante a las aplicaciones existentes, lo que permite que los atacantes accedan mediante esos permisos al sistema sin ser detectados, por ejemplo, para llamar a las API.

## <a name="responding-to-the-attack"></a>Respuesta al ataque


La respuesta a la vulnerabilidad sistémica de identidad comprometida debe incluir los pasos que se muestran en la siguiente imagen y tabla:

:::image type="content" source="media/recover-from-identity-compromise/recover-identity-compromise.png" alt-text="Pasos para recuperarse de una vulnerabilidad de identidad comprometida":::


|Paso  |Descripción  |
|---------|---------|
|**Establecer comunicaciones seguras**     |  Una organización que ha visto comprometida su identidad sistémica debe asumir que toda la comunicación se ve afectada. Antes de realizar cualquier acción de recuperación, debe asegurarse de que los miembros del equipo que son clave para su trabajo de investigación y respuesta [pueden comunicarse de forma segura](#establish-secure-communications). <br><br>*La protección de las comunicaciones debe ser el primer paso que se lleve a cabo; esto permitirá continuar sin que lo sepa el atacante.*|
|**Investigar el entorno**   | Después de proteger las comunicaciones del el equipo de investigación principal, puede empezar a buscar puntos de acceso iniciales y las técnicas de persistencia. [Identifique los indicios de identidad comprometida](#identify-indications-of-compromise) y después busque los puntos de acceso iniciales y la persistencia. A la vez, comience a [establecer operaciones de supervisión continuas](#establish-continuous-monitoring) durante las labores de recuperación.        |
|**Mejorar la posición de seguridad**     | [Habilite las características y funcionalidades de seguridad](#improve-security-posture) siguiendo las recomendaciones sobre procedimientos para mejorar la seguridad del sistema en el futuro.  <br><br>Asegúrese de continuar con las labores de [supervisión continua](#establish-continuous-monitoring) a medida que avance y vaya cambiando el panorama de seguridad.    |
|**Recuperar y conservar el control**     |  Debe recuperar el control administrativo del entorno de manos del atacante. Después de conseguir de nuevo el control y haber actualizado la posición de seguridad del sistema, asegúrese de [corregir o bloquear](#remediate-and-retain-administrative-control) todas las posibles técnicas de persistencia y las nuevas vulnerabilidades de seguridad de acceso iniciales.       |
|     |         |

## <a name="establish-secure-communications"></a>Establecimiento de comunicaciones seguras

Antes de empezar a responder, debe asegurarse de que puede comunicarse de forma segura sin que se entere el atacante. Asegúrese de aislar las comunicaciones relacionadas con el incidente para no poner sobre aviso de la investigación al atacante y conseguir que le tomen por sorpresa las acciones de respuesta.

Por ejemplo:

1. Para las comunicaciones individuales y de grupo iniciales, puede que desee usar llamadas RTC, puentes de conferencia que no estén conectados a la infraestructura corporativa y soluciones de mensajería cifrada de un extremo a otro.

    Las comunicaciones fuera de estos marcos deben tratarse como comunicaciones comprometidas que no son de confianza, a menos que se lleven a cabo a través de un canal seguro.

2. Después de esas conversaciones iniciales, puede que desee crear un inquilino de Microsoft 365 completamente nuevo, aislado del inquilino de producción de la organización. Cree cuentas solo para el personal clave que necesite formar parte de la respuesta.

Si crea un nuevo inquilino de Microsoft 365, asegúrese de seguir todos los procedimientos recomendados para el inquilino, especialmente en lo relacionado con las cuentas y los derechos administrativos. Limite los derechos administrativos y no confíe en aplicaciones ni proveedores externos.

> [!IMPORTANT]
> Asegúrese de que no intercambia comunicaciones sobre el nuevo inquilino en sus cuentas de correo electrónico existentes, que pueden haberse visto comprometidas. 

Para más información, consulte [Procedimientos recomendados para usar Microsoft 365 de forma segura](https://www.microsoft.com/security/blog/2019/01/10/best-practices-for-securely-using-microsoft-365-the-cis-microsoft-365-foundations-benchmark-now-available/).

## <a name="identify-indications-of-compromise"></a>Identificación de las indicaciones de compromiso

Se recomienda que los clientes sigan las actualizaciones de los proveedores del sistema, incluidos Microsoft y cualquier asociado, implementen las nuevas detecciones y protecciones proporcionadas, e identifiquen los indicadores de compromiso (IOC) publicados.

Busque actualizaciones en los siguientes productos de seguridad de Microsoft e implemente los cambios recomendados:

- [Azure Sentinel](../../sentinel/index.yml)
- [Soluciones y servicios de seguridad de Microsoft 365](/microsoft-365/security/)
- [Seguridad de Windows 10 Enterprise](/windows/security/)
- [Microsoft Cloud App Security](/cloud-app-security/)

La implementación de nuevas actualizaciones le ayudará a identificar las campañas anteriores y a evitar futuras campañas contra su sistema. Tenga en cuenta que es posible que las listas de IOC no sean exhaustivas y que pueden ampliarse a medida que continúan las investigaciones.

Por lo tanto, también le recomendamos que lleve a cabo las siguientes acciones:

- Asegúrese de que ha aplicado la [documentación de Azure Security Benchmark](/security/benchmark/azure/) y de que supervisa el cumplimiento por medio de [Azure Security Center](../../security-center/index.yml).

- Incorpore fuentes de inteligencia sobre amenazas en su solución de SIEM; por ejemplo, mediante la configuración de conectores de datos de Microsoft 365 en [Azure Sentinel](../../sentinel/understand-threat-intelligence.md).

Para más información, consulte la documentación sobre seguridad de Microsoft:

- [Documentación de Microsoft acerca de la seguridad](/security/)
- [Documentación de Azure Security Center](../index.yml)

## <a name="investigate-your-environment"></a>Investigación del entorno

Una vez que los respondedores de incidentes y el personal clave tienen un lugar seguro para colaborar, puede empezar a investigar el entorno comprometido.

Tendrá que buscar el equilibrio entre la necesidad de llegar al fondo de cada comportamiento anómalo y de tomar medidas rápidas para detener cualquier otra actividad por parte del atacante. Para que las correcciones tengan éxito, es necesario comprender el método inicial de entrada y los métodos de persistencia que usó el atacante, del modo más completo posible en ese momento. Los métodos de persistencia que no se hayan detectado durante la investigación pueden dar lugar a un acceso continuo por parte del atacante y a que se vea de nuevo comprometido el sistema.

En este punto, puede que desee realizar un análisis de riesgos para establecer la prioridad de sus acciones. Para más información, consulte:

- [Amenazas de centro de datos, vulnerabilidad y evaluación de riesgos](/compliance/assurance/assurance-threat-vulnerability-risk-assessment)
- [Seguimiento y respuesta a amenazas emergentes a través del análisis de amenazas](/microsoft-365/security/defender-endpoint/threat-analytics)
- [Administración de amenazas y vulnerabilidades](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)

Los servicios de seguridad de Microsoft proporcionan amplios recursos para investigaciones detalladas. En las secciones siguientes se describen las principales acciones recomendadas.


> [!NOTE]
> Si descubre que uno o varios de los orígenes de registro enumerados no forman parte actualmente del programa de seguridad, se recomienda configurarlos lo antes posible para habilitar las detecciones y las revisiones de registros futuras.
>
> Asegúrese de configurar la retención de registros para servir de apoyo a los objetivos de investigación de su organización en el futuro. Conserve la evidencia según sea necesario para fines legales, normativos o relacionados con los seguros.
>

### <a name="investigate-and-review-cloud-environment-logs"></a>Investigación y revisión de los registros del entorno de nube

Investigue y revise los registros del entorno de nube para ver si hay acciones sospechosas e indicaciones de compromiso de atacantes. Por ejemplo, compruebe los registros siguientes:

- [Registros de auditoría unificados](/powershell/module/exchange/search-unifiedauditlog)
- [Registros de Azure Active Directory (Azure AD)](../../active-directory/reports-monitoring/overview-monitoring.md)
- [Registros locales de Microsoft Exchange](/exchange/mail-flow/transport-logs/transport-logs)
- Registros de VPN, como los de [VPN Gateway](../../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)
- Registros del sistema de ingeniería
- Registros de antivirus y detección de puntos de conexión

### <a name="review-endpoint-audit-logs"></a>Revisión de los registros de auditoría de puntos de conexión

Revise los registros de auditoría de puntos de conexión para comprobar si hay cambios locales, como los siguientes tipos de acciones:

- Cambios de pertenencia a grupos
- Creación de nuevas cuentas de usuario
- Delegaciones dentro de Active Directory

Tenga en cuenta especialmente cualquiera de estos cambios si se producen junto con otros signos típicos de compromiso o actividad.

### <a name="review-administrative-rights-in-your-environments"></a>Revisión de los derechos administrativos en los entornos

Revise los derechos administrativos tanto en el entorno local como de nube. Por ejemplo:

|Entorno  |Descripción  |
|---------|---------|
|**Todos los entornos de nube**    |       - Revise los derechos de acceso con privilegios en la nube y quite los permisos innecesarios.<br>    - Implemente Privileged Identity Management (PIM).<br>    - Configure directivas de acceso condicional para limitar el acceso administrativo durante la protección.      |
|**Todos los entornos locales**     |       - Revise el acceso con privilegios en el entorno local y quite los permisos innecesarios.<br>   - Reduzca la pertenencia a grupos integrados.<br>    - Compruebe las delegaciones de Active Directory.<br>    - Proteja el entorno de nivel 0 y limite quién tiene acceso a los recursos de este nivel.      |
|**Todas las aplicaciones empresariales**     | Revise los permisos delegados y las concesiones de consentimiento que permitan cualquiera de las siguientes acciones: <br><br>  - Modificación de usuarios y roles con privilegios <br>- Lectura o acceso a todos los buzones <br>- Envío o reenvío de correo electrónico en nombre de otros usuarios <br>- Acceso a todo el contenido del sitio de OneDrive o SharePoint <br>- Adición de entidades de servicio que puedan leer y escribir en el directorio      |
|**Entornos de Microsoft 365**     |Revise las opciones de acceso y configuración del entorno de Microsoft 365, entre las que se incluyen: <br>- Uso compartido de SharePoint Online <br>- Microsoft Teams <br>- Power Apps <br>- Microsoft OneDrive para la Empresa          |
| **Revisión de las cuentas de usuario en los entornos**   |- Revise y quite las cuentas de usuario invitado que ya no son necesarias. <br>- Revise las configuraciones de correo electrónico para delegados, permisos de carpeta de buzones, registros de dispositivos móviles de ActiveSync, reglas de bandeja de entrada y las opciones de Outlook en la web. <br>- Revise los derechos de ApplicationImpersonation y reduzca cualquier uso de la autenticación heredada tanto como sea posible. <br>- Compruebe que se aplica MFA y que la información de contacto de MFA y del autoservicio de restablecimiento de contraseña (SSPR) de todos los usuarios es correcta.         |
|     |         |

## <a name="establish-continuous-monitoring"></a>Establecimiento de la supervisión continua

La detección del comportamiento del atacante incluye varios métodos y depende de las herramientas de seguridad que la organización tenga disponibles para responder al ataque.

Por ejemplo, los servicios de seguridad de Microsoft pueden tener recursos e instrucciones específicos pertinentes para el ataque, tal como se describe en las secciones siguientes.

> [!IMPORTANT]
> Si la investigación encuentra pruebas de que se han obtenido permisos administrativos por verse comprometido el sistema y han proporcionado acceso a la cuenta de administrador global de la organización o al certificado de firma de tokens SAML de confianza, le recomendamos que adopte medidas para [corregir y conservar el control administrativo](#remediate-and-retain-administrative-control).
>

### <a name="monitoring-with-azure-sentinel"></a>Supervisión con Azure Sentinel

Azure Sentinel cuenta con muchos recursos integrados para ayudar en la investigación, como libros de búsqueda y reglas de análisis que pueden ayudar a detectar ataques en áreas pertinentes de su entorno.

Para más información, consulte:

- [Visualización de los datos recopilados](../../sentinel/get-visibility.md)
- [Detección de amenazas integrada](../../sentinel/detect-threats-built-in.md)

### <a name="monitoring-with-microsoft-365-defender"></a>Supervisión con Microsoft 365 Defender

Se recomienda buscar instrucciones específicas pertinentes para el ataque en Microsoft 365 Defender para punto de conexión y Antivirus de Microsoft Defender.

Busque otros ejemplos de detecciones, consultas de búsqueda e informes de análisis de amenazas en el Centro de seguridad de Microsoft, como en Microsoft 365 Defender, Microsoft 365 Defender for Identity y Microsoft Cloud App Security. Para garantizar la cobertura, asegúrese de instalar el [agente de Microsoft Defender for Identity](/defender-for-identity/install-step4) en los servidores de ADFS, además de hacerlo en todos los controladores de dominio.

Para más información, consulte:

- [Seguimiento y respuesta a amenazas emergentes a través del análisis de amenazas](/windows/security/threat-protection/microsoft-defender-atp/threat-analytics)
- [Comprender el informe de analistas en análisis de amenazas en Microsoft 365 Defender](/microsoft-365/security/defender/threat-analytics-analyst-reports)

### <a name="monitoring-with-azure-active-directory"></a>Supervisión con Azure Active Directory

Los registros de inicio de sesión de Azure Active Directory pueden mostrar si la autenticación multifactor se usa correctamente. Para acceder a los registros de inicio de sesión directamente desde el área de Azure Active Directory en Azure Portal, use el cmdlet **Get-AzureADAuditSignInLogs** o consúltelos en el área **Logs** (Registros) de Azure Sentinel.

Por ejemplo, busque o filtre los resultados que aparecen cuando el campo **MFA results** (Resultados de MFA) tiene el valor **MFA requirement satisfied by claim in the token** (Requisito de MFA satisfecho por notificación en el token). Si su organización usa ADFS y las notificaciones registradas no están incluidas en la configuración de este sistema, pueden ser un indicio de actividad por parte del atacante.

Busque o filtre aún más los resultados para excluir ruido adicional. Por ejemplo, es posible que quiera incluir solo los resultados de dominios federados. Si encuentra inicios de sesión sospechosos, explore aún más en función de las direcciones IP, las cuentas de usuario, etc.

En la tabla siguiente se describen más métodos para usar los registros de Azure Active Directory en la investigación:

|Método  |Descripción  |
|---------|---------|
|**Análisis de eventos de inicio de sesión de riesgo**     |  Azure Active Directory y su plataforma de protección de identidad pueden generar eventos de riesgo asociados al uso de tokens SAML generados por el atacante. <br><br>Estos eventos se pueden etiquetar como *propiedades desconocidas*, *dirección IP anónima*, *viaje imposible*, etcétera. <br><br>Se recomienda analizar detalladamente todos los eventos de riesgo asociados a las cuentas que tienen privilegios administrativos, incluidos los que se puedan haber descartado o corregido automáticamente. Por ejemplo, un evento de riesgo o una dirección IP anónima podrían corregirse automáticamente porque el usuario ha superado la autenticación multifactor. <br><br>Asegúrese de usar [ADFS Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) para que todos los eventos de autenticación sean visibles en Azure AD. |
|**Detección de propiedades de autenticación de dominio**     |  Cualquier intento por parte del atacante de manipular las directivas de autenticación de dominio aparecerá en los registros de auditoría de Azure Active Directory y se reflejará en el registro de auditoría unificado. <br><br> Por ejemplo, revise los eventos asociados a **Se ha establecido una autenticación de dominio** en el registro de auditoría unificado, los registros de auditoría de Azure AD o el entorno de SIEM para comprobar que todas las actividades incluidas se esperaban y se habían planeado.   |
|**Detección de credenciales para aplicaciones de OAuth**     |  Los atacantes que han adquirido el control de una cuenta con privilegios pueden buscar una aplicación con la capacidad de acceder al correo electrónico de cualquier usuario de la organización y, a continuación, agregar credenciales controladas por ellos a esa aplicación. <br><br>Por ejemplo, puede que desee buscar cualquiera de las siguientes actividades, que se corresponderían con el comportamiento de un atacante: <br>- Adición o actualización de credenciales de entidad de servicio <br>- Actualización de certificados y secretos de aplicación <br>- Adición de una concesión de asignación de roles de aplicación a un usuario <br>- Adición de Oauth2PermissionGrant |
|**Detección del acceso al correo electrónico por parte de las aplicaciones**     |  Busque acceso al correo electrónico por parte de las aplicaciones de su entorno. Por ejemplo, use las [características de auditoría avanzada de Microsoft 365](/microsoft-365/compliance/mailitemsaccessed-forensics-investigations) para investigar las cuentas comprometidas. |
|**Detección de inicios de sesión no interactivos en entidades de servicio**     | Los informes de inicio de sesión de Azure Active Directory proporcionan detalles sobre los inicios de sesión no interactivos que han usado credenciales de entidad de servicio.  Por ejemplo, puede usar estos informes para encontrar datos valiosos para la investigación, como una dirección IP usada por el atacante para acceder a las aplicaciones de correo electrónico.        |
|     |         |


## <a name="improve-security-posture"></a>Mejora de la posición de seguridad

Si se ha producido un evento de seguridad en los sistemas, se recomienda reflexionar sobre la estrategia de seguridad y las prioridades actuales.

A menudo se pide a los respondedores de incidentes que proporcionen recomendaciones sobre las inversiones a las que la organización debe dar prioridad ahora que se ha enfrentado a nuevas amenazas.

Además de las recomendaciones documentadas en este artículo, se recomienda considerar la posibilidad de dar prioridad a las áreas de atención que responden a las técnicas posteriores a la vulnerabilidad de seguridad usadas por este atacante y a las brechas comunes de la posición de seguridad que las permiten.

En las secciones siguientes se incluyen recomendaciones para mejorar la posición de seguridad, tanto general como de identidad.

### <a name="improve-general-security-posture"></a>Mejora de la posición de seguridad general

Se recomiendan las siguientes acciones para garantizar la posición de seguridad general:

- **Revise la [puntuación de seguridad de Microsoft](/microsoft-365/security/mtp/microsoft-secure-score)** para obtener recomendaciones personalizadas sobre aspectos básicos de seguridad para los productos y servicios de Microsoft que consume.

- **Asegúrese de que su organización tiene soluciones de EDR y SIEM activas**, como [Microsoft 365 Defender para punto de conexión](/microsoft-365/security/defender/microsoft-365-defender) y [Azure Sentinel](../../sentinel/overview.md).

- **Revise el [modelo de acceso empresarial](/security/compass/privileged-access-access-model) de Microsoft**.

### <a name="improve-identity-security-posture"></a>Mejora de la posición de seguridad de identidad

Se recomiendan las siguientes acciones para garantizar la posición de seguridad relacionada con la identidad:

- **Revise los [cinco pasos de Microsoft para asegurar su infraestructura de identidad](steps-secure-identity.md)** y dé prioridad a los pasos que correspondan a su arquitectura de identidad.

- **[Considere la posibilidad de migrar a los valores predeterminados de seguridad de Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)** para la directiva de autenticación.

- **Deje de usar la autenticación heredada en su organización**, si los sistemas o las aplicaciones todavía la requieren. Para más información, consulte [Procedimientos: Bloqueo de la autenticación heredada en Azure AD con acceso condicional](../../active-directory/conditional-access/block-legacy-authentication.md).

    > [!NOTE]
    > El equipo de Exchange planea [deshabilitar la autenticación básica para los protocolos EAS, EAP, POP, IMAP y RPS](https://developer.microsoft.com/en-us/office/blogs/deferred-end-of-support-date-for-basic-authentication-in-exchange-online/) en la segunda mitad de 2021.
    >
    > Los valores predeterminados de seguridad y las directivas de autenticación son independientes, pero proporcionan características complementarias.
    >
    > Se recomienda que los clientes usen directivas de autenticación para desactivar la autenticación básica en un subconjunto de protocolos de Exchange Online o que desactiven gradualmente la autenticación básica en una organización grande.
    >

- **Trate la infraestructura de ADFS y la infraestructura de AD Connect como un recurso de nivel 0.**

- **Restrinja el acceso administrativo local al sistema**, incluida la cuenta que se usa para ejecutar el servicio ADFS.

    El privilegio mínimo necesario para la cuenta que ejecuta ADFS es la asignación de derechos de usuario *Iniciar sesión como servicio*.

- **Restrinja el acceso administrativo a usuarios limitados y desde intervalos de direcciones IP limitados** mediante directivas de Windows Firewall para Escritorio remoto.

    Se recomienda configurar un jumpbox de nivel 0 o un sistema equivalente.

- **Bloquee todo el acceso SMB de entrada** a los sistemas desde cualquier lugar del entorno. Para obtener más información, consulte el artículo sobre [cómo proteger el tráfico SMB en Windows](https://techcommunity.microsoft.com/t5/itops-talk-blog/beyond-the-edge-how-to-secure-smb-traffic-in-windows/ba-p/1447159). También se recomienda transmitir los registros de Windows Firewall a una solución de SIEM para una supervisión proactiva y histórica.

- Si usa una cuenta de servicio y su entorno lo admite, **migre de una cuenta de servicio a una cuenta de servicio administrada de grupo (gMSA)** . Si no puede cambiar a una cuenta gMSA, rote la contraseña de la cuenta de servicio a una contraseña compleja.

- **Asegúrese de que el registro detallado esté habilitado en los sistemas ADFS**. Por ejemplo, ejecute los siguientes comandos:

    ```powershell
    Set-AdfsProperties -AuditLevel verbose
    Restart-Service -Name adfssrv
    Auditpol.exe /set /subcategory:”Application Generated” /failure:enable /success:enable
    ```

## <a name="remediate-and-retain-administrative-control"></a>Corrección y conservación del control administrativo

Si la investigación ha identificado que el atacante tiene control administrativo en el entorno local o en la nube de la organización, debe recuperar el control de tal forma que se asegure de que el atacante no es persistente.

En esta sección se proporcionan los posibles métodos y pasos que se deben tener en cuenta al crear el plan de recuperación del control administrativo.

> [!IMPORTANT]
> Los pasos exactos necesarios en la organización dependerán de la persistencia que haya descubierto en la investigación y de la confianza que tenga en que la investigación haya sido completa y haya descubierto todos los métodos de entrada y persistencia posibles.
>
> Asegúrese de que las acciones que se realizan se aplican en un dispositivo de confianza, creado a partir de [origen limpio](/security/compass/privileged-access-access-model). Por ejemplo, use una [estación de trabajo de acceso con privilegios](/security/compass/privileged-access-deployment) nueva.
>

En las secciones siguientes se incluyen los siguientes tipos de recomendaciones para corregir y conservar el control administrativo:

- Eliminación de la confianza en los servidores actuales
- Rotación del certificado de firma de tokens SAML o sustitución de los servidores de ADFS si es necesario
- Actividades de corrección específicas para entornos locales o en la nube

### <a name="remove-trust-on-your-current-servers"></a>Eliminación de la confianza en los servidores actuales

Si su organización ha perdido el control de los certificados de firma de tokens o la confianza federada, el enfoque más seguro es quitar la confianza y cambiar a la identidad controlada en la nube mientras se corrige el entorno local.

La eliminación de la confianza y el cambio a la identidad controlada en la nube requiere un planeamiento cuidadoso y una profunda comprensión de los efectos de la operación empresarial que supone aislar la identidad. Para más información, consulte el artículo [Protección de Microsoft 365 contra ataques locales](../../active-directory/fundamentals/protect-m365-from-on-premises-attacks.md).

### <a name="rotate-your-saml-token-signing-certificate"></a>Rotación del certificado de firma de tokens SAML

Si su organización decide *no* [quitar la confianza](#remove-trust-on-your-current-servers) al recuperar el control administrativo en el entorno local, tendrá que rotar el certificado de firma de tokens SAML después de recuperarlo y de bloquear la capacidad de los atacantes de acceder de nuevo al certificado de firma.

La rotación del certificado de firma de tokens una sola vez sigue permitiendo que funcione el certificado de firma de tokens anterior. Este comportamiento es una funcionalidad integrada para las rotaciones de certificados normales que permite un período de gracia para que las organizaciones actualicen las confianzas de usuario autenticado antes de que expire el certificado.

Si ha habido un ataque, no quiere que el atacante conserve el acceso de ningún modo. Asegúrese de seguir los pasos que se indican a continuación para garantizar que el atacante no mantiene la capacidad de falsificación de tokens para el dominio.

> [!CAUTION]
> El último paso de este procedimiento cierra la sesión de los usuarios en sus teléfonos, sesiones de correo web actuales y cualquier otro elemento que use los tokens y los tokens de actualización asociados.
>

> [!TIP]
> Al realizar estos pasos en el entorno de ADFS, se crea un certificado principal y uno secundario, y se promueve automáticamente el certificado secundario a principal después de un período predeterminado de 5 días.
>
> Si tiene relaciones de confianza de usuarios autenticados, esto puede tener efectos 5 días después del cambio inicial del entorno de ADFS y debe tenerse en cuenta en el plan. También puede resolverlo reemplazando el certificado principal una tercera vez, usando de nuevo la marca **Urgent**, y quitando el certificado secundario o desactivando la rotación automática de certificados.
>

**Para rotar completamente el certificado de firma de tokens y evitar la nueva falsificación de tokens por parte de un atacante**

1. Asegúrese de que el valor del parámetro **AutoCertificateRollover** está establecido en **True**:

    ``` powershell
    Get-AdfsProperties | FL AutoCert*, Certificate*
    ```
    Si **AutoCertificateRollover** no está establecido en **True**, establezca el valor de la manera siguiente:

    ``` powershell
    Set-ADFSProperties -AutoCertificateRollover $true
    ```

1. Conéctese al servicio de Microsoft Online Services:

    ``` powershell
    Connect-MsolService
    ```

1. Ejecute el siguiente comando y tome nota de la huella digital y las fechas de expiración del certificado de firma de tokens local y en la nube:

    ``` powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

    Por ejemplo:

    ```powershell
    ...
    [Not Before]
        12/9/2020 7:57:13 PM

    [Not After]
        12/9/2021 7:57:13 PM

    [Thumbprint]
        3UD1JG5MEFHSBW7HEPF6D98EI8AHNTY22XPQWJFK6
    ```

1. Reemplace el certificado de firma de tokens principal con el modificador **Urgent**. Este comando hace que ADFS reemplace el certificado principal inmediatamente, sin convertirlo en un certificado secundario:

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing -Urgent
    ```

1. Cree un certificado de firma de token secundario, sin el modificador **Urgent**. Este comando permite que haya dos certificados de firma de tokens locales antes de sincronizar con Azure Cloud.

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing
    ```

1. Actualice el entorno de nube con los certificados principal y secundario locales para quitar inmediatamente el certificado de firma de tokens publicado en la nube.

    ```powershell
    Update-MsolFederatedDomain -DomainName <domain>
    ```

    > [!IMPORTANT]
    > Si este paso no se realiza con este método, es posible que el antiguo certificado de firma de tokens todavía pueda autenticar a los usuarios.

1. Para asegurarse de que estos pasos se han realizado correctamente, compruebe que el certificado mostrado antes en el paso 3 ya no existe:

    ```powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

1. Revoque los tokens de actualización por medio de PowerShell para impedir el acceso con los tokens antiguos. 

    Para más información, consulte:

    - [Revocación del acceso de usuario en Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken en la documentación de PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



### <a name="replace-your-adfs-servers"></a>Reemplazo de los servidores de ADFS

Si, en lugar de [rotar el certificado de firma de tokens SAML](#rotate-your-saml-token-signing-certificate), decide reemplazar los servidores de ADFS por sistemas limpios, deberá quitar el sistema de archivos distribuido de Azure existente del entorno y, a continuación, crear uno nuevo. 

Para más información, consulte la sección [Eliminación de una configuración](../../active-directory/cloud-sync/how-to-configure.md#remove-a-configuration). 

### <a name="cloud-remediation-activities"></a>Actividades de corrección en la nube

Además de las recomendaciones indicadas anteriormente en este artículo, también se recomiendan las actividades siguientes para los entornos de nube:

|Actividad  |Descripción  |
|---------|---------|
|**Restablecimiento de contraseñas**     |   Restablezca las contraseñas de las [cuentas de acceso de emergencia](../../active-directory/roles/security-emergency-access.md) y reduzca el número de este tipo de cuenta al mínimo necesario.    |
|**Restringir las cuentas de acceso con privilegios**     |    Asegúrese de que las cuentas de servicio y de usuario con acceso con privilegios son cuentas solo en la nube y que no usan cuentas locales sincronizadas o federadas con Azure Active Directory.  |
|**Exigir MFA**     | Aplique la autenticación multifactor (MFA) en todos los usuarios con privilegios elevados del inquilino. Se recomienda aplicar MFA a todos los usuarios del inquilino.       |
|**Limitar el acceso administrativo**     |    Implemente [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) (PIM) y el acceso condicional para limitar el acceso administrativo.  <br><br>Para usuarios de Microsoft 365, implemente [Privileged Access Management](https://techcommunity.microsoft.com/t5/microsoft-security-and/privileged-access-management-in-office-365-is-now-generally/ba-p/261751) (PAM) para limitar el acceso a capacidades confidenciales, como eDiscovery, Administrador global, Administración de cuentas, etc.    |
|**Revisar o reducir los permisos delegados y la otorgación de consentimientos**     |  Revise y reduzca los permisos delegados u [otorgaciones de consentimiento](/graph/auth-limit-mailbox-access) de las aplicaciones empresariales que permitan cualquiera de las siguientes funcionalidades: <br><br>- Modificación de roles y usuarios con privilegios <br>- Lectura, envío de correo electrónico o acceso a todos los buzones <br>- Acceso a contenido de OneDrive, Teams o SharePoint <br>- Adición de entidades de servicio que puedan leer y escribir en el directorio <br>- Permisos de aplicación frente a acceso delegado       |
|     |         |

### <a name="on-premises-remediation-activities"></a>Actividades de corrección locales

Además de las recomendaciones indicadas anteriormente en este artículo, también se recomiendan las actividades siguientes para los entornos locales:

|Actividad  |Descripción  |
|---------|---------|
|**Recompilar los sistemas afectados**     |   Recompile los sistemas identificados como comprometidos por el atacante durante la investigación.      |
|**Eliminar usuarios administradores innecesarios**     |   Quite miembros innecesarios de los grupos de administradores de dominio, operadores de copia de seguridad y administradores empresariales. Para obtener más información, consulte [Protección del acceso con privilegios](/security/compass/overview). |
|**Restablecer contraseñas de las cuentas con privilegios**     |  Restablezca las contraseñas de todas las cuentas con privilegios del entorno. <br><br>**Nota**: Las cuentas con privilegios no se limitan a los grupos integrados, sino que también pueden ser grupos que tienen acceso delegado a la administración de servidores, la administración de estaciones de trabajo u otras áreas del entorno.      |
|**Restablecer la cuenta krbtgt**     | Restablezca la cuenta **krbtgt** dos veces con el script [New-KrbtgtKeys](https://github.com/microsoft/New-KrbtgtKeys.ps1/blob/master/New-KrbtgtKeys.ps1). <br><br>**Nota**: Si usa controladores de dominio de solo lectura, deberá ejecutar el script por separado para estos controladores y los de lectura y escritura.        |
|**Programar un reinicio del sistema**     |   Después de validar que no existen o permanecen en el sistema mecanismos de persistencia creados por el atacante, programe un reinicio del sistema para ayudar a eliminar el malware residente en memoria. |
|**Restablecer la contraseña de DSRM**     |  Restablezca la contraseña de DSRM (modo de restauración de servicios de directorio) de cada controlador de dominio a una contraseña que sea única y compleja.       |
|     |         |

### <a name="remediate-or-block-persistence-discovered-during-investigation"></a>Corrección o bloqueo de la persistencia detectada durante la investigación

La investigación es un proceso iterativo y deberá encontrar el equilibrio entre el deseo de la organización de corregir a medida que se identifican las anomalías y la posibilidad de que la corrección alerte al atacante de su detección y le dé tiempo a reaccionar.

Por ejemplo, un atacante que sea consciente de la detección podría cambiar las técnicas o crear más persistencia.

Asegúrese de corregir las técnicas de persistencia que haya identificado en las fases anteriores de la investigación.

### <a name="remediate-user-and-service-account-access"></a>Corrección del acceso a cuentas de usuario y servicio

Además de las acciones recomendadas indicadas anteriormente, se recomienda tener en cuenta los pasos siguientes para corregir y restaurar cuentas de usuario:

- **Aplique el acceso condicional basado en dispositivos de confianza**. Si es posible, se recomienda aplicar el *acceso condicional basado en la ubicación* para adaptarse a los requisitos de la organización.

- **Restablezca las contraseñas** después de la expulsión en las cuentas de usuario que puedan haberse visto comprometidas. Asegúrese de implementar también un plan a medio plazo para restablecer las credenciales de todas las cuentas del directorio.

- **Revoque los tokens de actualización** inmediatamente después de rotar las credenciales.

    Para más información, consulte:

    - [Revocación del acceso de usuario en un emergencia de Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken en la documentación de PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



## <a name="next-steps"></a>Pasos siguientes

- **Consulte la ayuda de los productos de Microsoft**, incluidos el Centro de seguridad de Microsoft 365, el Centro de seguridad y cumplimiento de Microsoft 365 y el Centro de seguridad de Microsoft Defender seleccionando el botón **Ayuda** ( **?** ) en la barra de navegación superior.

- **Para obtener ayuda para la implementación**, póngase en contacto con nosotros en [FastTrack](https://fasttrack.microsoft.com).

- **Si tiene necesidades relacionadas con el soporte técnico del producto**, abra un caso de soporte técnico de Microsoft en https://support.microsoft.com/contactus.

    > [!IMPORTANT]
    > Si cree que se ha visto comprometido y necesita ayuda por medio de una respuesta a incidentes, abra un caso de soporte técnico de nivel **Sev A**.
    >
