---
title: Escenarios detectados por el motor Fusion de Azure Sentinel
description: Obtenga información sobre los escenarios detectados por Fusion, que se enumeran aquí agrupados por clasificación de amenazas.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 25706c4a80e63ed76cc94d24582bcf6433032950
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093159"
---
# <a name="scenarios-detected-by-the-azure-sentinel-fusion-engine"></a>Escenarios detectados por el motor Fusion de Azure Sentinel

En este documento se enumeran los tipos de ataques de varias fases basados en escenario, agrupados por clasificación de amenazas, que Azure Sentinel detecta mediante el motor de correlación Fusion.

Dado que [Fusion](fusion.md) correlaciona varias señales de diferentes productos para detectar ataques avanzados en varias fases, las detecciones de Fusion correctas se presentan como **incidentes de Fusion** en la página **Incidentes** de Azure Sentinel y no como **alertas**, y se almacenan en la tabla *Incidentes* de **Registros** y no en la tabla *SecurityAlerts*.

Para habilitar estos escenarios de detección de ataques con tecnología de Fusion, cualquier origen de datos enumerado debe ingerirse en el área de trabajo de Log Analytics.

> [!NOTE]
> Algunos de estos escenarios se encuentran en **versión preliminar**. Aparecerá indicado.


## <a name="compute-resource-abuse"></a>Uso abusivo de recursos de proceso

### <a name="multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in"></a>Varias actividades de creación de máquinas virtuales después de un inicio de sesión sospechoso en Azure Active Directory
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial e impacto 

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y secuestro de recursos (T1496)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha creado un número anómalo de máquinas virtuales en una sola sesión después de un inicio de sesión sospechoso en una cuenta de Azure AD. Este tipo de alerta indica, con un alto grado de confianza, que la cuenta que aparece en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para crear nuevas máquinas virtuales con fines no autorizados, como la ejecución de operaciones de minería de datos de cifrado. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de varias actividades de creación de máquinas virtuales son:

- **Un viaje imposible a una ubicación inusual que conduce a varias actividades de creación de máquinas virtuales**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a varias actividades de creación de máquinas virtuales**

- **Un evento de inicio de sesión desde un dispositivo infectado que conduce a varias actividades de creación de máquinas virtuales**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a varias actividades de creación de máquinas virtuales**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a varias actividades de creación de máquinas virtuales**


## <a name="credential-access"></a>Acceso de credencial
(Nueva clasificación de amenazas)

### <a name="multiple-passwords-reset-by-user-following-suspicious-sign-in"></a>Restablecimiento de varias contraseñas por parte del usuario después de un inicio de sesión sospechoso
En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** acceso inicial, acceso a credenciales

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078), fuerza bruta (T1110)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Azure Active Directory Identity Protection

**Descripción:** los incidentes de Fusion de este tipo indican que un usuario ha restablecido varias contraseñas después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta evidencia sugiere que la cuenta anotada en la descripción del incidente de Fusion ha estado en peligro y se ha usado para realizar varios restablecimientos de contraseña, con el fin de obtener acceso a varios sistemas y recursos. La manipulación de cuentas (incluido el restablecimiento de contraseñas) puede ayudar a los adversarios a mantener el acceso tanto a las credenciales como a determinados niveles de permiso dentro de un entorno. Estas son las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con varios restablecimientos de contraseña:

- **Un viaje imposible a una ubicación inusual que conduce a varios restablecimientos de contraseña**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a varios restablecimientos de contraseña**

- **Un evento de inicio de sesión desde un dispositivo infectado que conduce a varios restablecimientos de contraseña**

- **Un evento de inicio de sesión desde una IP anónima que conduce a varios restablecimientos de contraseña**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a varios restablecimientos de contraseña**

### <a name="suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>Inicio de sesión sospechoso que coincide con un inicio de sesión correcto en una VPN de Palo Alto por IP con varios inicios de sesión en Azure AD con errores
En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** acceso inicial, acceso a credenciales

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078), fuerza bruta (T1110)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Azure Active Directory Identity Protection

**Descripción:** los incidentes de Fusion de este tipo indican que un inicio de sesión sospechoso en una cuenta de Azure AD ha coincidido con un inicio de sesión correcto a través de una VPN de Palo Alto desde una dirección IP desde la que se han producido varios inicios de sesión en Azure AD con errores en un período de tiempo similar. Aunque no hay evidencia de un ataque en varias fases, la correlación de estas alertas de menor fidelidad da lugar a un incidente de alta fidelidad que sugiere un acceso inicial malintencionado a la red de la organización. Esto también podría indicar de que un atacante intenta usar técnicas de fuerza bruta para obtener acceso a una cuenta de Azure AD. Las permutaciones de alertas de inicios de sesión en Azure AD sospechosos con alertas de "IP con varios inicios de sesión de Azure AD con errores que inician sesión correctamente en la VPN de Palo Alto" son:

- **Viaje imposible a una ubicación atípica que coincide con una IP con varios inicios de sesión en Azure AD con errores en la VPN de Palo Alto**

- **Evento de inicio de sesión desde una ubicación desconocida que coincide con una IP con varios inicios de sesión en Azure AD con errores en la VPN de Palo Alto**

- **Evento de inicio de sesión desde un dispositivo infectado que coincide con una IP con varios inicios de sesión en Azure AD con errores en la VPN de Palo Alto**

- **Evento de inicio de sesión desde una IP anónima que coincide con una IP con varios inicios de sesión en Azure AD con errores en la VPN de Palo Alto**

- **Evento de inicio de sesión desde un usuario con credenciales filtradas que coincide con una IP con varios inicios de sesión en Azure AD con errores en la VPN de Palo Alto**

## <a name="credential-harvesting"></a>Recolección de credenciales
(Nueva clasificación de amenazas)
### <a name="malicious-credential-theft-tool-execution-following-suspicious-sign-in"></a>Ejecución de la herramienta de robo de credenciales malintencionada después del inicio de sesión sospechoso

**Tácticas de MITRE ATT&CK:** acceso inicial, acceso a credenciales

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078), volcado de credenciales del sistema operativo (T1003)

**Orígenes de conexión de datos:** Azure Active Directory Identity Protection, Microsoft Defender para punto de conexión

**Descripción:** los incidentes de fusión de este tipo indican que se ejecutó una herramienta de robo de credenciales conocida después de un inicio de sesión de Azure AD sospechoso. Esta evidencia sugiere con un alto grado de confianza que la cuenta de usuario indicada en la descripción de la alerta se ha puesto en peligro y puede haber usado correctamente una herramienta como **Mimikatz** para recolectar credenciales como claves, contraseñas de texto no cifrado o valores hash de contraseñas del sistema. Las credenciales recolectadas pueden permitir que un atacante tenga acceso a datos confidenciales, escale privilegios o realice un ataque lateral a través de la red. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de la herramienta de robo de credenciales malintencionada son:

- **Viaje imposible a ubicaciones inusuales que conduce a la ejecución de la herramienta de robo de credenciales malintencionada**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a la ejecución de la herramienta de robo de credenciales malintencionada**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a la ejecución de la herramienta de robo de credenciales malintencionada**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a la ejecución de la herramienta de robo de credenciales malintencionada**

- **Evento de inicio de sesión del usuario con credenciales con fugas que conduce a la ejecución de la herramienta de robo de credenciales malintencionada**

### <a name="suspected-credential-theft-activity-following-suspicious-sign-in"></a>Sospecha de actividad de robo de credenciales después del inicio de sesión sospechoso

**Tácticas de MITRE ATT&CK:** acceso inicial, acceso a credenciales

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078), credenciales de almacenes de contraseñas (T1555), volcado de credenciales del sistema operativo (T1003)

**Orígenes de conexión de datos:** Azure Active Directory Identity Protection, Microsoft Defender para punto de conexión

**Descripción:** los incidentes de fusión de este tipo indican que la actividad asociada a patrones de robo de credenciales se produjo después de un inicio de sesión de Azure AD sospechoso. Esta evidencia sugiere con un alto grado de confianza que la cuenta de usuario indicada en la descripción de la alerta se ha puesto en peligro y se ha usado para robar credenciales como claves, contraseñas de texto no cifrado, valores hash de contraseñas, etc. Las credenciales robadas pueden permitir que un atacante tenga acceso a datos confidenciales, escale privilegios o realice un ataque lateral a través de la red. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de la actividad de robo de credenciales son:

- **Viaje imposible a ubicaciones inusuales que conduce a la sospecha de actividad de robo de credenciales**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a la sospecha de actividad de robo de credenciales**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a la sospecha de actividad de robo de credenciales**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a la sospecha de actividad de robo de credenciales**

- **Evento de inicio de sesión del usuario con credenciales filtradas que conduce a la sospecha de actividad de robo de credenciales**

## <a name="crypto-mining"></a>Minería de cifrado
(Nueva clasificación de amenazas)

### <a name="crypto-mining-activity-following-suspicious-sign-in"></a>Actividad de minería de cifrado después del inicio de sesión sospechoso

**Tácticas de MITRE ATT&CK:** acceso inicial, acceso a credenciales

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y secuestro de recursos (T1496)

**Orígenes de conexión de datos:** Azure Active Directory Identity Protection, Azure Defender (Azure Security Center)

**Descripción:** Los incidentes de fusión de este tipo indican la actividad de minería de cifrado asociada con un inicio de sesión sospechoso a una cuenta de Azure AD. Esta evidencia sugiere con un alto grado de confianza que la cuenta de usuario indicada en la descripción de la alerta se ha puesto en peligro y se ha usado para atacar recursos del entorno para extraer la moneda de cifrado. Esto puede privar a los recursos de la eficacia de la informática o dar lugar a facturas de utilización de la nube significativamente más altas de lo esperado. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de la actividad de minería de cifrado son:  

- **Viaje imposible a ubicaciones inusuales que conduce a la actividad de minería de cifrado**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a la actividad de minería de cifrado**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a la actividad de minería de cifrado**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a la actividad de minería de cifrado**

- **Evento de inicio de sesión del usuario con credenciales filtradas que conduce a la actividad de minería de cifrado**

## <a name="data-destruction"></a>Destrucción de datos

### <a name="mass-file-deletion-following-suspicious-azure-ad-sign-in"></a>Eliminación masiva de archivos después de un inicio de sesión sospechoso en Azure AD

**Tácticas de MITRE ATT&CK:** Acceso inicial e impacto

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y destrucción de datos (T1485)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha eliminado un número anómalo de archivos únicos después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta evidencia sugiere que la cuenta indicada en la descripción del incidente de Fusion puede haber estado en peligro y se ha usado para destruir datos con fines malintencionados. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de eliminación de archivos masiva son:  

- **Un viaje imposible a una ubicación inusual que conduce a una eliminación de archivos masiva**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a una eliminación de archivos masiva**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a una eliminación de archivos masiva**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a una eliminación de archivos masiva**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a una eliminación de archivos masiva**

### <a name="mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>Eliminación masiva de archivos después de un inicio de sesión correcto en Azure AD desde una IP bloqueada por un dispositivo de firewall de Cisco
En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial e impacto

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y destrucción de datos (T1485)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Microsoft Cloud App Security

**Descripción:** los incidentes de Fusion de este tipo indican que se eliminó un número anómalo de archivos únicos después de un inicio de sesión correcto en Azure AD, a pesar de que un dispositivo de firewall de Cisco ha bloqueado la dirección IP del usuario. Esta evidencia sugiere que la cuenta indicada en la descripción del incidente de Fusion ha estado en peligro y se ha usado para destruir datos con fines malintencionados. Como el firewall bloqueó la IP, la misma IP que ha iniciado sesión correctamente en Azure AD es potencialmente sospechoso y podría indicar que las credenciales de la cuenta de usuario corren peligro.

### <a name="mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins"></a>Eliminación masiva de archivos después de iniciar sesión correctamente en la VPN de Palo Alto por IP con varios inicios de sesión en Azure AD con errores
En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** acceso inicial, acceso a credenciales, impacto

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078), fuerza bruta (T1110), destrucción de datos (T1485)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Microsoft Cloud App Security

**Descripción:** los incidentes de Fusion de este tipo indican que un usuario que ha iniciado sesión correctamente a través de una VPN de Palo Alto desde una dirección IP desde la que se produjeron varios inicios de sesión de Azure AD con errores en un período de tiempo similar ha eliminado un número anómalo de archivos únicos. Esta evidencia sugiere que la cuenta de usuario indicada en el incidente de Fusion puede haber estado en peligro debido al uso de técnicas de fuerza bruta y haberse usado para destruir datos con fines malintencionados.

### <a name="suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in"></a>Actividad de eliminación de correo electrónico sospechosa después de un inicio de sesión sospechoso en Azure AD
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial e impacto 

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y destrucción de datos (T1485)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha eliminado un número anómalo de correos electrónicos en una sola sesión después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta evidencia sugiere que la cuenta indicada en la descripción del incidente de Fusion puede haber estado en peligro y se ha usado para destruir datos con fines malintencionados, como perjudicar a la organización u ocultar actividad del correo electrónico relacionada con el correo no deseado. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de actividades sospechosas de eliminación de correos electrónicos son:   

- **Un viaje imposible a una ubicación inusual que conduce a actividades sospechosas de eliminación de correos electrónicos**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a actividades sospechosas de eliminación de correos electrónicos**

- **Un evento de inicio de sesión desde un dispositivo infectado que conduce a actividades sospechosas de eliminación de correos electrónicos**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a actividades sospechosas de eliminación de correos electrónicos**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a actividades sospechosas de eliminación de correos electrónicos**

## <a name="data-exfiltration"></a>Filtración de datos

### <a name="mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>Actividades de reenvío de correo después de nueva actividad en la cuenta de administrador que no se ha visto recientemente
Este escenario pertenece a dos clasificaciones de amenazas de esta lista: **filtración de datos** y **actividad administrativa malintencionada**. Para mayor claridad, aparece en ambas secciones.

En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** acceso inicial, recopilación y filtración

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078) y recopilación de correo electrónico (T1114), filtración a través de un servicio web (T1567)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Microsoft Cloud App Security

**Descripción:** los incidentes de Fusion de este tipo indican que se ha creado una nueva cuenta del administrador del servicio Exchange o que es la primera vez que una cuenta de administrador de Exchange existente ha realizado alguna acción administrativa, en las últimas dos semanas, y que la cuenta luego realizó algunas acciones de reenvío de correo, que son inusuales para una cuenta de administrador. Esta evidencia sugiere que la cuenta de usuario que aparece en la descripción del incidente de Fusion se ha puesto en peligro o se ha manipulado, y se ha usado para el filtrado de datos de la red de su organización.

### <a name="mass-file-download-following-suspicious-azure-ad-sign-in"></a>Descarga masiva de archivos después de un inicio de sesión sospechoso en Azure AD

**Tácticas de MITRE ATT&CK:** Acceso inicial y filtración

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que un usuario ha descargado un número anómalo de archivos después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta indicación proporciona un alto grado de confianza en que la cuenta que aparece en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para el filtrado de datos de la red de su organización. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de descarga masiva de archivos son:  

- **Un viaje imposible a una ubicación inusual que conduce a una descarga de archivos masiva**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a una descarga de archivos masiva**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a una descarga de archivos masiva**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a una descarga de archivos masiva**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a una descarga de archivos masiva**

### <a name="mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance"></a>Descarga masiva de archivos después de un inicio de sesión correcto en Azure AD desde una IP bloqueada por un dispositivo de firewall de Cisco
En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial y filtración

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y filtración a través de un servicio web (T1567)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Microsoft Cloud App Security

**Descripción:** los incidentes de Fusion de este tipo indican que un usuario descargó un número anómalo de archivos después de un inicio de sesión correcto en Azure AD, a pesar de que un dispositivo de firewall de Cisco ha bloqueado la dirección IP del usuario. Esto podría ser un intento por parte de un atacante de filtrar datos de la red de la organización después de poner en peligro la cuenta de un usuario. Como el firewall bloqueó la IP, la misma IP que ha iniciado sesión correctamente en Azure AD es potencialmente sospechoso y podría indicar que las credenciales de la cuenta de usuario corren peligro.

### <a name="mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip"></a>Descarga masiva de archivos que coincide con la operación de archivo de SharePoint desde una dirección IP no vista previamente
En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** filtración

**Técnicas de MITRE ATT&CK:** filtración a través del servicio web (T1567), límites de tamaño de transferencia de datos (T1030)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Microsoft Cloud App Security

**Descripción:** los incidentes de Fusion de este tipo indican que un usuario conectado desde una dirección IP que no se había visto con anterioridad ha descargado un número anómalo de archivos. Aunque no hay evidencia de un ataque en varias fases, la correlación de estas alertas de menor fidelidad da lugar a un incidente de alta fidelidad que sugiere un intento por parte de un atacante de filtrar datos de la red de la organización desde una cuenta de usuario posiblemente en peligro. En entornos estables, es posible que estas conexiones por parte de IP que no se han visto previamente no estén autorizadas, especialmente si se asocian a picos de volumen que podrían asociarse con la filtración de documentos a gran escala.

### <a name="mass-file-sharing-following-suspicious-azure-ad-sign-in"></a>Uso compartido de archivos masivo después de un inicio de sesión sospechoso en Azure AD

**Tácticas de MITRE ATT&CK:** Acceso inicial y filtración

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y filtración a través de un servicio web (T1567)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha compartido con otros usuarios un número de archivos por encima de un umbral determinado después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta indicación proporciona un alto grado de confianza en que la cuenta indicada en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para el filtrado de datos de la red de su organización mediante el uso compartido de archivos (como documentos, hojas de cálculo, etc.) con usuarios no autorizados con fines malintencionados. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de uso compartido de archivos masivo son:  

- **Un viaje imposible a una ubicación inusual que conduce a un uso compartido de archivos masivo**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a un uso compartido de archivos masivo**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a un uso compartido de archivos masivo**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a un uso compartido de archivos masivo**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a un uso compartido de archivos masivo**

### <a name="multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in"></a>Varias actividades de uso compartido de informes de Power BI después de un inicio de sesión sospechoso en Azure AD 
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial y filtración 

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y filtración a través de un servicio web (T1567)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha compartido un número anómalo de informes de Power BI en una sola sesión después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta indicación proporciona un alto grado de confianza en que la cuenta que aparece en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para el filtrado de datos de la red de su organización mediante el uso compartido de informes de Power BI con usuarios no autorizados con fines malintencionados. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de varias actividades de uso compartido de informes de Power BI son:  

- **Un viaje imposible a una ubicación inusual que conduce a varias actividades de uso compartido de informes de Power BI**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a varias actividades de uso compartido de informes de Power BI**

- **Un evento de inicio de sesión desde un dispositivo infectado que conduce a varias actividades de uso compartido de informes de Power BI**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a varias actividades de uso compartido de informes de Power BI**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a varias actividades de uso compartido de informes de Power BI**

### <a name="office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in"></a>Filtración de buzones de Office 365 después de un inicio de sesión sospechoso en Azure AD

**Tácticas de MITRE ATT&CK:** Acceso inicial, filtración y recopilación

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078), recopilación de correos electrónicos (T1114) y filtración automatizada (T1020)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha establecido una regla de reenvío de bandeja de entrada sospechosa en la bandeja de entrada de un usuario después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta indicación proporciona un alto grado de confianza en que la cuenta del usuario (indicada en la descripción del incidente de Fusion) se ha puesto en peligro y se ha usado para el filtrado de datos de la red de su organización mediante la habilitación de una regla de reenvío de buzón sin que el usuario real lo sepa. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de filtración de buzones de Office 365 son:

- **Un viaje imposible a una ubicación inusual que conduce a la filtración de los buzones de Office 365**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a la filtración de buzones de Office 365**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a la filtración de buzones de Office 365**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a la filtración de buzones de Office 365**

- **Evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a la filtración de buzones de Office 365**

### <a name="sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection"></a>Operación de archivo de SharePoint desde una dirección IP que no se ha visto previamente tras la detección de malware
En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** filtración, evasión de defensas

**Técnicas de MITRE ATT&CK:** límites del tamaño de la transferencia de datos (T1030)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Microsoft Cloud App Security

**Descripción:** los incidentes de Fusión de este tipo indican que un atacante ha intentado filtrar grandes cantidades de datos mediante la descarga el uso compartido a través de SharePoint mediante el uso de malware. En entornos estables, es posible que estas conexiones por parte de IP que no se han visto previamente no estén autorizadas, especialmente si se asocian a picos de volumen que podrían asociarse con la filtración de documentos a gran escala. 

### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Establecimiento de reglas de manipulación sospechosa de la bandeja de entrada después de un inicio de sesión sospechoso en Azure AD
Este escenario pertenece a dos clasificaciones de amenazas de esta lista: **filtración de datos** y **desplazamiento lateral**. Para mayor claridad, aparece en ambas secciones.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial, desplazamiento lateral y filtración

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078), phishing interno de objetivo definido (T1534) y filtración automatizada (T1020)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se han establecido reglas de bandeja de entrada anómalas en la bandeja de entrada de un usuario después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta evidencia proporciona un alto grado de confianza en que la cuenta indicada en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para manipular las reglas de bandeja de entrada del correo electrónico del usuario con fines malintencionados. Como alternativa, el atacante podría intentar generar correos electrónicos de suplantación de identidad (phishing) desde dentro de la organización (omitiendo los mecanismos de detección de suplantación de identidad en correos electrónicos de orígenes externos) con el fin de desplazarse lateralmente a través de la red obteniendo acceso a cuentas de usuario o a cuentas con privilegios elevados. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta del establecimiento de reglas de manipulación sospechosa de la bandeja de entrada son:

- **Un viaje imposible a una ubicación inusual que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

- **Un evento de inicio de sesión desde un dispositivo infectado que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

### <a name="suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in"></a>Uso compartido sospechoso de informes de Power BI después de un inicio de sesión sospechoso en Azure AD
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial y filtración 

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y filtración a través de un servicio web (T1567)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha producido una actividad sospechosa de uso compartido de informes de Power BI después de un inicio de sesión sospechoso en una cuenta de Azure AD. La actividad de uso compartido se ha identificado como sospechosa porque los informes de Power BI contenían información confidencial identificada mediante el procesamiento de lenguaje natural, y porque se han compartido con una dirección de correo electrónico externa, se han publicado en la Web o se han enviado como una instantánea a una dirección de correo electrónico suscrita externamente. Esta alerta indica con un alto grado de confianza que la cuenta que aparece en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para el filtrado de datos confidenciales de su organización mediante el uso compartido de informes de Power BI con usuarios no autorizados con fines malintencionados. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de uso compartido de informes de Power BI son:  

- **Un viaje imposible a una ubicación inusual que conduce a un uso compartido sospechoso de informes de Power BI**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a un uso compartido sospechoso de informes de Power BI**

- **Un evento de inicio de sesión desde un dispositivo infectado que conduce a un uso compartido sospechoso de informes de Power BI**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a un uso compartido sospechoso de informes de Power BI**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a un uso compartido sospechoso de informes de Power BI**

## <a name="denial-of-service"></a>Denegación de servicio

### <a name="multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in"></a>Varias actividades de eliminación de máquinas virtuales después de un inicio de sesión sospechoso en Azure AD
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial e impacto

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y denegación de servicio del punto de conexión (T1499)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha eliminado un número anómalo de máquinas virtuales en una sola sesión después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta indicación proporciona un alto grado de confianza en que la cuenta que aparece en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para intentar alterar o destruir el entorno de nube de la organización. Las permutaciones de alertas de inicio de sesión sospechoso en Azure AD con la alerta de varias actividades de eliminación de máquinas virtuales son:  

- **Un viaje imposible a una ubicación inusual que conduce a varias actividades de eliminación de máquinas virtuales**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a varias actividades de eliminación de máquinas virtuales**

- **Un evento de inicio de sesión desde un dispositivo infectado que conduce a varias actividades de eliminación de máquinas virtuales**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a varias actividades de eliminación de máquinas virtuales**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a varias actividades de eliminación de máquinas virtuales**

## <a name="lateral-movement"></a>Desplazamiento lateral

### <a name="office-365-impersonation-following-suspicious-azure-ad-sign-in"></a>Suplantación de Office 365 después de un inicio de sesión sospechoso en Azure AD

**Tácticas de MITRE ATT&CK:** Acceso inicial y desplazamiento lateral

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y phishing interno de objetivo definido (T1534)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha producido un número anómalo de acciones de suplantación después de un inicio de sesión sospechoso en una cuenta de Azure AD. Algunos programas de software cuentan con opciones que permiten a los usuarios suplantar a otros usuarios. Por ejemplo, los servicios de correo electrónico permiten que un usuario autorice a otros a enviar correos electrónicos en su nombre. Esta alerta indica con un alto grado de confianza que la cuenta que aparece en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para llevar a cabo actividades de suplantación con fines malintencionados, como el envío de correos electrónicos de suplantación de identidad (phishing) para distribuir malware o desplazarse lateralmente. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de suplantación de Office 365 son:  

- **Un viaje imposible a una ubicación inusual que conduce a la suplantación de Office 365**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a una suplantación de Office 365**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a una suplantación de Office 365**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a una suplantación de Office 365**

- **Evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a la suplantación de Office 365**
 
### <a name="suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in"></a>Establecimiento de reglas de manipulación sospechosa de la bandeja de entrada después de un inicio de sesión sospechoso en Azure AD
Este escenario pertenece a dos clasificaciones de amenazas de esta lista: **desplazamiento lateral** y **filtración de datos**. Para mayor claridad, aparece en ambas secciones.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial, desplazamiento lateral y filtración

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078), phishing interno de objetivo definido (T1534) y filtración automatizada (T1020)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se han establecido reglas de bandeja de entrada anómalas en la bandeja de entrada de un usuario después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta evidencia proporciona un alto grado de confianza en que la cuenta indicada en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para manipular las reglas de bandeja de entrada del correo electrónico del usuario con fines malintencionados. Como alternativa, el atacante podría intentar generar correos electrónicos de suplantación de identidad (phishing) desde dentro de la organización (omitiendo los mecanismos de detección de suplantación de identidad en correos electrónicos de orígenes externos) con el fin de desplazarse lateralmente a través de la red obteniendo acceso a cuentas de usuario o a cuentas con privilegios elevados. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta del establecimiento de reglas de manipulación sospechosa de la bandeja de entrada son:

- **Un viaje imposible a una ubicación inusual que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

- **Un evento de inicio de sesión desde una ubicación desconocida que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

- **Un evento de inicio de sesión desde un dispositivo infectado que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a una regla de manipulación sospechosa de la bandeja de entrada**

## <a name="malicious-administrative-activity"></a>Actividad administrativa malintencionada

### <a name="suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in"></a>Actividad de administración de aplicaciones en la nube sospechosa después de un inicio de sesión sospechoso en Azure AD

**Tácticas de MITRE ATT&CK:** Acceso inicial, persistencia, evasión de defensas, desplazamiento lateral, recopilación, filtración e impacto

**Técnicas de MITRE ATT&CK:** N/D

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha realizado un número anómalo de actividades administrativas en una sola sesión después de un inicio de sesión sospechoso en Azure AD desde la misma cuenta. Esta evidencia sugiere que la cuenta que aparece en la descripción del incidente de Fusion puede haber estado en peligro y se ha usado para realizar cualquier cantidad de acciones administrativas no autorizadas con fines malintencionados. También indica que es posible que se haya puesto en peligro una cuenta con privilegios administrativos. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de actividades sospechosas de administración de aplicaciones en la nube son:  

- **Un viaje imposible a una ubicación inusual que conduce a una actividad sospechosa de administración de aplicaciones en la nube**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a una actividad de administración de aplicaciones en la nube sospechosa**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a una actividad de administración de aplicaciones en la nube sospechosa**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a una actividad de administración de aplicaciones en la nube sospechosa**

- **Evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a una actividad de administración de aplicaciones en la nube sospechosa**

### <a name="mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently"></a>Actividades de reenvío de correo después de nueva actividad en la cuenta de administrador que no se ha visto recientemente
Este escenario pertenece a dos clasificaciones de amenazas de esta lista: **actividad administrativa malintencionada** y **filtración de datos**. Para mayor claridad, aparece en ambas secciones.

En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** acceso inicial, recopilación y filtración

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078) y recopilación de correo electrónico (T1114), filtración a través de un servicio web (T1567)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Microsoft Cloud App Security

**Descripción:** los incidentes de Fusion de este tipo indican que se ha creado una nueva cuenta del administrador del servicio Exchange o que es la primera vez que una cuenta de administrador de Exchange existente ha realizado alguna acción administrativa, en las últimas dos semanas, y que la cuenta luego realizó algunas acciones de reenvío de correo, que son inusuales para una cuenta de administrador. Esta evidencia sugiere que la cuenta de usuario que aparece en la descripción del incidente de Fusion se ha puesto en peligro o se ha manipulado, y se ha usado para el filtrado de datos de la red de su organización.

## <a name="malicious-execution-with-legitimate-process"></a>Ejecución malintencionada con un proceso legítimo

### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>PowerShell estableció una conexión de red sospechosa, seguida por tráfico anómalo marcado por el firewall de Palo Alto Networks.
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Ejecución

**Técnicas de MITRE ATT&CK:** Intérprete de scripts y comandos (T1059)

**Orígenes de conexión de datos:** Microsoft Defender para punto de conexión (anteriormente conocido como Protección contra amenazas avanzada de Microsoft Defender o MDATP) y Palo Alto Networks 

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha realizado una solicitud de conexión de salida a través de un comando de PowerShell y, después, el firewall de Palo Alto Networks ha detectado una actividad de entrada anómala. Esta evidencia sugiere que es probable que un atacante haya obtenido acceso a la red y esté intentando realizar acciones malintencionadas. Los intentos de conexión de PowerShell que siguen este patrón podrían indicar actividades de control y comando de malware, solicitudes de descarga de malware adicional o que un atacante está estableciendo acceso interactivo remoto. Igual que sucede con todos los ataques que aprovechan recursos ya existentes en el entorno de destino, esta actividad podría ser un uso legítimo de PowerShell. Pero la ejecución de comandos de PowerShell seguida por una actividad de firewall entrante sospechosa aumenta la confianza en que PowerShell se está usando de manera malintencionada y debe investigarse más a fondo. En los registros de Palo Alto, Azure Sentinel se centra en [registros de amenazas](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs) y el tráfico se considera sospechoso cuando se permiten amenazas (datos sospechosos, archivos, inundaciones, paquetes, exámenes, spyware, direcciones URL, virus, puntos vulnerables, virus Wildfire y malware Wildfire). También puede consultar el registro de amenazas de Palo Alto que se corresponde con el [tipo de amenaza/contenido](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) enumerado en la descripción del incidente de Fusion para obtener más información sobre las alertas.

### <a name="suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Ejecución de WMI remota sospechosa seguida por tráfico anómalo marcado por el firewall de Palo Alto Networks
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Ejecución y detección

**Técnicas de MITRE ATT&CK:** Instrumental de administración de Windows (T1047)

**Orígenes de conexión de datos:** Microsoft Defender para punto de conexión (anteriormente MDATP) y Palo Alto Networks 

**Descripción:** Los incidentes de Fusion de este tipo indican que los comandos de la interfaz de administración de Windows (WMI) se han ejecutado de forma remota en un sistema y, después, el firewall de Palo Alto Networks ha detectado una actividad de entrada sospechosa. Esta evidencia sugiere que es posible que un atacante haya conseguido acceder a la red y esté intentando desplazarse lateralmente, escalar privilegios o ejecutar cargas malintencionadas. Igual que sucede con todos los ataques que aprovechan recursos ya existentes en el entorno de destino, esta actividad podría ser un uso legítimo de WMI. Pero la ejecución remota de comandos de WMI seguida por una actividad de firewall entrante sospechosa aumenta la confianza en que WMI se está usando de manera malintencionada y debe investigarse más a fondo. En los registros de Palo Alto, Azure Sentinel se centra en [registros de amenazas](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs) y el tráfico se considera sospechoso cuando se permiten amenazas (datos sospechosos, archivos, inundaciones, paquetes, exámenes, spyware, direcciones URL, virus, puntos vulnerables, virus Wildfire y malware Wildfire). También puede consultar el registro de amenazas de Palo Alto que se corresponde con el [tipo de amenaza/contenido](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) enumerado en la descripción del incidente de Fusion para obtener más información sobre las alertas.

### <a name="suspicious-powershell-command-line-following-suspicious-sign-in"></a>Línea de comandos de PowerShell sospechosa después del inicio de sesión sospechoso

**Tácticas de MITRE ATT&CK:** acceso inicial, ejecución

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078), intérprete de scripts y comandos (T1059)

**Orígenes de conexión de datos:** Azure Active Directory Identity Protection, Microsoft Defender para punto de conexión (anteriormente MDATP)

**Descripción:** los incidentes de fusión de este tipo indican que un usuario ejecutó comandos de PowerShell potencialmente malintencionados después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta evidencia sugiere, con un alto grado de confianza, que la cuenta indicada en la descripción de la alerta se ha puesto en peligro y se han realizado otras acciones malintencionadas. Los atacantes a menudo usan PowerShell para ejecutar cargas malintencionadas en memoria sin dejar artefactos en el disco, con el fin de evitar la detección mediante mecanismos de seguridad basados en disco como los programas de detección de virus. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta del comando de PowerShell sospechosa son:

- **Viaje imposible a ubicaciones inusuales que conduce a la línea de comandos de PowerShell sospechosa**

- **Evento de inicio de sesión desde una ubicación desconocida que conduce a la línea de comandos de PowerShell sospechosa**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a la línea de comandos de PowerShell sospechosa**

- **Evento de inicio de sesión desde una dirección IP anónima que conduce a la línea de comandos de PowerShell sospechosa**

- **Evento de inicio de sesión del usuario con credenciales filtradas que conduce a la línea de comandos de PowerShell sospechosa**

## <a name="malware-c2-or-download"></a>Comando y control (C2) o descarga de malware

### <a name="beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service"></a>Patrón de señal detectado por Fortinet después de varios inicios de sesión de usuario con error en un servicio

En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** acceso inicial, comando y control

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078), puerto no estándar (T1571), T1065 (retirada)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Microsoft Cloud App Security

**Descripción:** los incidentes de Fusion de este tipo indican patrones de comunicación, desde una dirección IP interna a una externa, que son coherentes con la señalización, después de varios inicios de sesión de usuario con errores en un servicio desde una entidad interna relacionada. La combinación de estos dos eventos podría indicar que se ha producido una infección por malware o que hay un host en peligro que hace la filtración de datos. 

### <a name="beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in"></a>Patrón de señal detectado por Fortinet después de un inicio de sesión sospechoso en Azure AD

En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** acceso inicial, comando y control

**Técnicas de MITRE ATT&CK:** cuenta válida (T1078), puerto no estándar (T1571), T1065 (retirada)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Azure Active Directory Identity Protection

**Descripción:** los incidentes de Fusion de este tipo indican patrones de comunicación, desde una dirección IP interna a una externa, que son coherentes con la señalización, después de un inicio de sesión de usuario de una naturaleza sospechosa en Azure AD. La combinación de estos dos eventos podría indicar que se ha producido una infección por malware o que hay un host en peligro que hace la filtración de datos. Las permutaciones del patrón de señal detectadas por las alertas de Fortinet con alertas de inicio de sesión en Azure AD son:   

- **Viaje imposible a una ubicación atípica que provoca que Fortinet detecte un patrón de señal**

- **Evento de inicio de sesión desde una ubicación desconocida que provoca que Fortinet detecte un patrón de señal**

- **Evento de inicio de sesión desde un dispositivo infectado que provoca que Fortinet detecte un patrón de señal**

- **Evento de inicio de sesión desde una dirección IP anónima que provoca que Fortinet detecte un patrón de señal**

- **Evento de inicio de sesión desde un usuario con credenciales filtradas que provoca que Fortinet detecte un patrón de señal**

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Solicitud de red a un servicio de anonimización de TOR, seguida por un tráfico anómalo marcado por el firewall de Palo Alto Networks.
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Comando y control

**Técnicas de MITRE ATT&CK:** Canal cifrado (T1573) y Proxy (T1090)

**Orígenes de conexión de datos:** Microsoft Defender para punto de conexión (anteriormente MDATP) y Palo Alto Networks 

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha realizado una solicitud de conexión de salida al servicio de anonimización de TOR y, después, el firewall de Palo Alto Networks ha detectado una actividad de entrada anómala. Esta evidencia sugiere que es probable que un atacante haya accedido a la red e intente ocultar sus acciones e intenciones. Las conexiones a la red TOR que siguen este patrón podrían indicar actividades de control y comando de malware, solicitudes de descarga de malware adicional o que un atacante está estableciendo acceso interactivo remoto. En los registros de Palo Alto, Azure Sentinel se centra en [registros de amenazas](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs) y el tráfico se considera sospechoso cuando se permiten amenazas (datos sospechosos, archivos, inundaciones, paquetes, exámenes, spyware, direcciones URL, virus, puntos vulnerables, virus Wildfire y malware Wildfire). También puede consultar el registro de amenazas de Palo Alto que se corresponde con el [tipo de amenaza/contenido](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) enumerado en la descripción del incidente de Fusion para obtener más información sobre las alertas.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Una conexión saliente a IP con un historial de intentos de acceso no autorizados, seguida por tráfico anómalo marcado por el firewall de Palo Alto Networks
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Comando y control

**Técnicas de MITRE ATT&CK:** No aplicable

**Orígenes de conexión de datos:** Microsoft Defender para punto de conexión (anteriormente MDATP) y Palo Alto Networks 

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha establecido una conexión de salida a una dirección IP con un historial de intentos de acceso no autorizado y, después, el firewall de Palo Alto Networks ha detectado una actividad de entrada anómala. Esta evidencia sugiere que es probable que un atacante haya obtenido acceso a la red. Los intentos de conexión que siguen este patrón podrían indicar actividades de control y comando de malware, solicitudes de descarga de malware adicional o que un atacante está estableciendo acceso interactivo remoto. En los registros de Palo Alto, Azure Sentinel se centra en [registros de amenazas](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs) y el tráfico se considera sospechoso cuando se permiten amenazas (datos sospechosos, archivos, inundaciones, paquetes, exámenes, spyware, direcciones URL, virus, puntos vulnerables, virus Wildfire y malware Wildfire). También puede consultar el registro de amenazas de Palo Alto que se corresponde con el [tipo de amenaza/contenido](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) enumerado en la descripción del incidente de Fusion para obtener más información sobre las alertas.

## <a name="persistence"></a>Persistencia
(Nueva clasificación de amenazas)

### <a name="rare-application-consent-following-suspicious-sign-in"></a>Consentimiento poco frecuente de la aplicación después de un inicio de sesión sospechoso

En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** persistencia, acceso inicial

**Técnicas de MITRE ATT&CK:** crear cuenta (T1136), cuenta válida (T1078)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Azure Active Directory Identity Protection

**Descripción:** los incidentes de Fusion de este tipo indican que un usuario que nunca, o casi nunca, ha concedido el consentimiento a una aplicación se lo conceda tras un inicio de sesión sospechoso relacionado en una cuenta de Azure AD. Esta evidencia sugiere que la cuenta indicada en la descripción del incidente de Fusion puede haber estado en peligro y se ha usado para acceder o manipular la aplicación con fines malintencionados.  Dar consentimiento a la aplicación, Agregar entidad de servicio y Agregar OAuth2PermissionGrant suelen ser eventos poco frecuentes. Los atacantes pueden usar este tipo de cambio de configuración para establecer o mantener su posición en los sistemas. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de consentimiento poco usual de aplicaciones son:

- **Un viaje imposible a una ubicación inusual que provoca un consentimiento muy inusual de la aplicación**

- **Evento de inicio de sesión desde una ubicación desconocida que provoca un consentimiento poco usual de aplicaciones**

- **Evento de inicio de sesión desde un dispositivo infectado que provoca un consentimiento poco usual de aplicaciones**

- **Evento de inicio de sesión desde una IP anónima que provoca un consentimiento poco usual de aplicaciones**

- **Evento de inicio de sesión desde un usuario con credenciales filtradas que provoca un consentimiento poco usual de aplicaciones**

## <a name="ransomware"></a>Ransomware

### <a name="ransomware-execution-following-suspicious-azure-ad-sign-in"></a>Ejecución de ransomware después de un inicio de sesión sospechoso en Azure AD

**Tácticas de MITRE ATT&CK:** Acceso inicial e impacto

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y datos cifrados para causar impacto (T1486)

**Orígenes de conexión de datos:** Microsoft Cloud App Security y Azure Active Directory Identity Protection

**Descripción:** Los incidentes de Fusion de este tipo indican que se ha detectado un comportamiento anómalo de un usuario que apunta a un ataque de ransomware después de un inicio de sesión sospechoso en una cuenta de Azure AD. Esta indicación proporciona un alto grado de confianza en que la cuenta que aparece en la descripción del incidente de Fusion se ha puesto en peligro y se ha usado para cifrar datos con el fin de chantajear al propietario de los datos o de denegarle el acceso a ellos. Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la alerta de ejecución de ransomware son:  

- **Un viaje imposible a una ubicación inusual que conduce a ransomware en las aplicaciones en la nube**

- **Evento de inicio de sesión desde una ubicación desconocida que conducen a ransomware en las aplicaciones en la nube**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a ransomware en las aplicaciones en la nube**

- **Un evento de inicio de sesión desde una dirección IP anónima que conduce a ransomware en las aplicaciones en la nube**

- **Un evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a ransomware en las aplicaciones en la nube**

## <a name="remote-exploitation"></a>Explotación remota

### <a name="suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Sospecha de uso de un marco de ataque seguida por tráfico anómalo marcado por el firewall de Palo Alto Networks
Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial, ejecución, desplazamiento lateral y elevación de privilegios

**Técnicas de MITRE ATT&CK:** Explotación de una aplicación de acceso público (T1190), explotación de la ejecución del cliente (T1203), explotación de servicios remotos (T1210) y explotación para la elevación de privilegios (T1068)

**Orígenes de conexión de datos:** Microsoft Defender para punto de conexión (anteriormente MDATP) y Palo Alto Networks 

**Descripción:** Los incidentes de Fusion de este tipo indican que se han detectado usos no estándar de protocolos que se asemejan al uso de marcos de ataque (como Metasploit) y, después, el firewall de Palo Alto Networks ha detectado una actividad de entrada sospechosa. Puede tratarse de una indicación inicial de que un atacante ha aprovechado un servicio para acceder a los recursos de la red, o bien que ya ha accedido a ellos y está intentando explotar aún más los sistemas y servicios disponibles para desplazarse lateralmente o escalar privilegios. En los registros de Palo Alto, Azure Sentinel se centra en [registros de amenazas](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs) y el tráfico se considera sospechoso cuando se permiten amenazas (datos sospechosos, archivos, inundaciones, paquetes, exámenes, spyware, direcciones URL, virus, puntos vulnerables, virus Wildfire y malware Wildfire). También puede consultar el registro de amenazas de Palo Alto que se corresponde con el [tipo de amenaza/contenido](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/syslog-field-descriptions/threat-log-fields.html) enumerado en la descripción del incidente de Fusion para obtener más información sobre las alertas.

## <a name="resource-hijacking"></a>Secuestro de recursos
(Nueva clasificación de amenazas)

### <a name="suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in"></a>Implementación sospechosa de un recurso o un grupo de recursos por parte de un autor de una llamada que nunca se había visto después de un inicio de sesión sospechoso en Azure AD
En este escenario se usan las alertas generadas por las **reglas de análisis programadas**.

Este escenario está actualmente en **versión preliminar**.

**Tácticas de MITRE ATT&CK:** Acceso inicial e impacto

**Técnicas de MITRE ATT&CK:** Cuenta válida (T1078) y secuestro de recursos (T1496)

**Orígenes del conector de datos:** Azure Sentinel (regla de análisis programada), Azure Active Directory Identity Protection

**Descripción:** los incidentes de Fusion de este tipo indican que un usuario ha implementado un recurso o un grupo de recursos de Azure (una actividad poco frecuente) después de un inicio de sesión sospechoso, con propiedades que no se han visto recientemente, en una cuenta de Azure AD. Esto podría ser un intento por parte de un atacante de implementar recursos o grupos de recursos con fines malintencionados después de poner en peligro la cuenta de usuario que se indica en la descripción del incidente de Fusion.
Las permutaciones de alertas de inicios de sesión sospechosos en Azure AD con la implementación de recursos o grupos de recursos sospechosa por una alerta de autor de llamada al que nunca se había visto son:

- **Viaje imposible a una ubicación atípica que conduce a la implementación sospechosa de recursos o grupos de recursos por parte de un autor de la llamada al que no se había visto anteriormente**

- **Evento de inicio de sesión desde una ubicación atípica que conduce a la implementación sospechosa de recursos o grupos de recursos por parte de un autor de la llamada al que no se había visto anteriormente**

- **Evento de inicio de sesión desde un dispositivo infectado que conduce a la implementación sospechosa de recursos o grupos de recursos por parte de un autor de la llamada al que no se había visto anteriormente**

- **Evento de inicio de sesión desde una IP anónima que conduce a la implementación sospechosa de recursos o grupos de recursos por parte de un autor de la llamada al que no se había visto anteriormente**

- **Evento de inicio de sesión desde un usuario con credenciales filtradas que conduce a la implementación sospechosa de recursos o grupos de recursos por parte de un autor de la llamada al que no se había visto anteriormente**

## <a name="next-steps"></a>Pasos siguientes

Ahora que conoce más detalles sobre la detección avanzada de ataques de varias fases, puede que le interese el siguiente inicio rápido para aprender a obtener visibilidad sobre sus datos y a detectar posibles amenazas: [Introducción a Azure Sentinel](get-visibility.md).

Si está preparado para investigar los incidentes que se han creado, consulte el siguiente tutorial: [Investigación de incidentes con Azure Sentinel](investigate-cases.md).
