---
title: Lista de comprobación previa a la implementación de Azure Defender para IoT
description: En este artículo, se proporciona información y una lista de comprobación que se deben usar antes de la implementación al preparar el sitio.
ms.date: 07/18/2021
ms.topic: checklist
ms.openlocfilehash: ac60e574a3d61bfa0c3106375d0606b7de6d9494
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676955"
---
# <a name="pre-deployment-checklist-overview"></a>Introducción a la lista de comprobación previa a la implementación

En este artículo, se proporciona información y una lista de comprobación que se deben usar antes de la implementación al preparar el sitio para garantizar una incorporación correcta.

- El sensor físico de Defender para IoT se debe conectar a los conmutadores administrados que ven las comunicaciones industriales entre las capas 1 y 2 (en algunos casos, también en la capa 3).
- El sensor escucha en un puerto de reflejo del conmutador (puerto SPAN) o un TAP.
- El puerto de administración está conectado a la red empresarial o corporativa mediante SSL.

## <a name="checklist"></a>Lista de comprobación

Tener una visión general del diagrama de la red industrial permitirá a los ingenieros del sitio definir la ubicación adecuada para el equipamiento de Azure Defender para IoT.

### <a name="1-global-network-diagram"></a>1. Diagrama de red global

El diagrama de red global proporciona un diagrama del entorno de OT industrial.

:::image type="content" source="media/resources-sensor-deployment-checklist/purdue-model.png" alt-text="Aquí es donde encaja el sensor de Azure Defender para IoT en la configuración.":::

:::image type="content" source="media/resources-sensor-deployment-checklist/backbone-switch.png" alt-text="Aquí es donde encaja el sensor de Azure Defender para IoT en el modelo vencido.":::

> [!Note] 
> El dispositivo de Defender para IoT debe estar conectado a un conmutador de nivel inferior que vea el tráfico entre los puertos del conmutador. 

### <a name="2-committed-devices"></a>2. Dispositivos confirmados

Proporcione el número aproximado de dispositivos de red que se van a supervisar. Necesitará esta información al incorporar la suscripción al portal de Azure Defender para IoT. Durante el proceso de incorporación, se le pedirá que escriba el número de dispositivos en incrementos de 1000.

### <a name="3-optional-subnet-list"></a>3. (Opcional) Lista de subredes 

Proporcione una lista de subredes de las redes de producción.

| **#** | **Nombre de subred** | **Descripción** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="4-vlans"></a>4. VLAN

Proporcionar una lista de VLAN de las redes de producción.

| **#** | **Nombre de la VLAN** | **Descripción** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="5-switch-models-and-mirroring-support"></a>5. Modelos de conmutador y compatibilidad con la creación de reflejo

Para comprobar que los conmutadores tienen la funcionalidad de creación de reflejo del puerto, proporcione los números de modelo del conmutador a los que debe conectarse la plataforma de Defender para IoT.

| **#** | **Switch** | **Modelo** | **Compatibilidad con la creación de reflejo del tráfico (SPAN, RSPAN o ninguno)** |
|--|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="6-third-party-switch-management"></a>6. Administración de conmutadores de terceros

¿Un tercero administra los conmutadores? S o N 

En caso afirmativo, ¿quién? __________________________________ 

¿Cuál es su directiva? __________________________________ 

### <a name="7-serial-connection"></a>7. Conexión serie

¿Hay dispositivos que se comunican a través de una conexión en serie en la red? Sí o no 

En caso afirmativo, especifique el protocolo de comunicación en serie: ________________ 

En caso afirmativo, indique en el diagrama de red qué dispositivos se comunican con los protocolos serie y dónde se encuentran.

*Agregue el diagrama de red con las conexiones serie marcadas.*

### <a name="8-vendors-and-protocols-industrial-equipment"></a>8. Proveedores y protocolos (equipamiento industrial)

Proporcionar una lista de proveedores y protocolos del equipo industrial. (Opcional)

| **#** | **Proveedor** | **Protocolo de comunicación** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

Por ejemplo:

- Siemens

- Rockwell Automation: Ethernet o IP

- Emerson: DeltaV, Ovation

### <a name="9-qos"></a>9. Calidad de servicio

Para QoS, la configuración predeterminada del sensor es de 1.5 Mbps. Especifique si quiere cambiarlo: ________________ 

   Unidad de negocio (BU): ________________

### <a name="10-sensor"></a>10. Sensor  

El dispositivo del sensor está conectado al puerto SPAN del conmutador a través de un adaptador de red. Está conectado a la red corporativa del cliente para su administración a través de otro adaptador de red dedicado.

Proporcione los detalles de la dirección de la NIC del sensor que se conectará a la red corporativa: 

| Elemento | Dispositivo 1 | Dispositivo 2 | Dispositivo 3 |
|--|--|--|--|
| Dirección IP del dispositivo |  |  |  |
| Subnet |  |  |  |
| Puerta de enlace predeterminada |  |  |  |
| DNS |  |  |  |
| Nombre de host |  |  |  |

### <a name="11-idraciloserver-management"></a>11. iDRAC/iLO/Administración del servidor

| Elemento | Dispositivo 1 | Dispositivo 2 | Dispositivo 3 |
|--|--|--|--|
| Dirección IP del dispositivo |  |  |  |
| Subnet |  |  |  |
| Puerta de enlace predeterminada |  |  |  |
| DNS |  |  |  |

### <a name="12-on-premises-management-console"></a>12. Consola de administración local  

| Elemento | Activo | Pasivo (cuando se usa alta disponibilidad) |
|--|--|--|
| Dirección IP |  |  |
| Subnet |  |  |
| Puerta de enlace predeterminada |  |  |
| DNS |  |  |

### <a name="13-snmp"></a>13. SNMP  

| Elemento | Detalles |
|--|--|
| IP |  |
| Dirección IP |  |
| Nombre de usuario |  |
| Contraseña |  |
| Tipo de autenticación | MD5 o SHA |
| Cifrado | DES o AES |
| Clave secreta |  |
| Cadena de comunidad SNMP v2 |

### <a name="14-ssl-certificate"></a>14. Certificado SSL

¿Tiene previsto usar un certificado SSL? Sí o no

En caso afirmativo, ¿qué servicio usará para generarlo? ¿Qué atributos incluirá en el certificado (por ejemplo, dominio o dirección IP)?

### <a name="15-smtp-authentication"></a>15. Autenticación SMTP

¿Tiene previsto usar SMTP para reenviar alertas a un servidor de correo electrónico? Sí o no

En caso afirmativo, ¿qué método de autenticación usará?  

### <a name="16-active-directory-or-local-users"></a>16. Active Directory o usuarios locales

Póngase en contacto con un administrador de Active Directory para crear un grupo de usuarios del sitio de Active Directory o usuarios locales. Asegúrese de que los usuarios estén listos para el día de la implementación.

### <a name="17-iot-device-types-in-the-network"></a>17. Tipos de dispositivos IoT en la red

| Tipo de dispositivo | Número de dispositivos en la red | Ancho de banda promedio |
|--|--|--|
| Por ejemplo, Cámara |  |
| Por ejemplo, Máquina de rayos X |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

## <a name="next-steps"></a>Pasos siguientes

[Acerca de la configuración de red de Azure Defender para IoT](how-to-set-up-your-network.md)

[Acerca de la instalación de Defender para IoT](how-to-install-software.md)
