---
title: Respuesta a las alertas de Azure Defender para Key Vault
description: Obtenga información sobre los pasos necesarios para responder a las alertas de Azure Defender para Key Vault.
author: memildin
ms.author: memildin
ms.date: 09/13/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 45ed38f3325ae66c72fff7f0aec35347b5e28ace
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664986"
---
# <a name="respond-to-azure-defender-for-key-vault-alerts"></a>Respuesta a las alertas de Azure Defender para Key Vault
Cuando reciba una alerta de [Azure Defender para Key Vault](defender-for-key-vault-introduction.md), se recomienda investigar la alerta y responder a ella, tal y como se describe a continuación. Azure Defender para Key Vault protege las aplicaciones y las credenciales, por lo que, incluso si está familiarizado con la aplicación o el usuario que desencadenó la alerta, es importante comprobar la situación relativa a cada alerta.  

Las alertas de Azure Defender para Key Vault incluyen los siguientes elementos:

- Identificador de objeto
- Nombre principal de usuario o dirección IP del recurso sospechoso 

En función del *tipo* de acceso que se haya producido, algunos campos pueden no estar disponibles. Por ejemplo, si una aplicación ha accedido al almacén de claves, no verá un nombre principal de usuario asociado. Si el tráfico se originó fuera de Azure, no verá un identificador de objeto.

> [!TIP]
> A las máquinas virtuales de Azure se les asignan direcciones IP de Microsoft. Esto significa que una alerta puede contener una dirección IP de Microsoft aunque guarde relación con actividad registrada fuera de Microsoft. Por lo tanto, aunque una alerta tenga una dirección IP de Microsoft, debe investigarse de todos modos tal y como se describe en esta página.

## <a name="step-1-identify-the-source"></a>Paso 1. Identificación del origen

1. Compruebe si el tráfico se originó en el inquilino de Azure. Si el firewall del almacén de claves está habilitado, es probable que haya proporcionado acceso al usuario o a la aplicación que desencadenó esta alerta.
1. Si no puede comprobar el origen del tráfico, continúe con el [Paso 2. Respuesta consecuente](#step-2-respond-accordingly).
1. Si puede identificar el origen del tráfico en el inquilino, póngase en contacto con el usuario o el propietario de la aplicación. 

> [!CAUTION]
> Azure Defender para Key Vault está diseñado para ayudar a identificar la actividad sospechosa causada por credenciales robadas. **No** descarte la alerta simplemente porque reconoce el usuario o la aplicación. Póngase en contacto con el propietario de la aplicación o el usuario y compruebe que la actividad es legítima. Puede crear una regla de supresión para eliminar el ruido si es necesario. Obtenga más información en [Supresión de alertas de Azure Defender](alerts-suppression-rules.md).


## <a name="step-2-respond-accordingly"></a>Paso 2. Respuesta consecuente 
Si no reconoce el usuario o la aplicación, o si cree que el acceso no debe haberse autorizado:

- Si el tráfico procedía de una dirección IP no reconocida:
    1. Habilite el firewall de Azure Key Vault como se describe en [Configuración de firewalls y redes virtuales de Azure Key Vault](../key-vault/general/network-security.md).
    1. Configure el firewall con recursos de confianza y redes virtuales.

- Si el origen de la alerta era una aplicación no autorizada o un usuario sospechoso:
    1. Abra la configuración de la directiva de acceso del almacén de claves.
    1. Quite la entidad de seguridad correspondiente o restrinja las operaciones que la entidad de seguridad puede realizar.  

- Si el origen de la alerta tiene un rol de Azure Active Directory en el inquilino:
    1. Póngase en contacto con el administrador.
    1. Determine si es necesario reducir o revocar los permisos de Azure Active Directory.

## <a name="step-3-measure-the-impact"></a>Paso 3. Medida del impacto
Cuando se haya mitigado el evento, investigue los secretos del almacén de claves que se han visto afectados:
1. Abra la página **Seguridad** en Azure Key Vault y vea la alerta desencadenada.
1. Seleccione la alerta específica que se desencadenó.
    Revise la lista de los secretos a los que se accedió y la marca de tiempo.
1. Opcionalmente, si tiene habilitados los registros de diagnóstico del almacén de claves, revise las operaciones anteriores para la dirección IP del autor de la llamada correspondiente, la entidad de seguridad de usuario o el identificador de objeto.  

## <a name="step-4-take-action"></a>Paso 4. Realizar acción 
Una vez que haya compilado la lista de los secretos, las claves y los certificados a los que ha accedido el usuario o la aplicación sospechosos, debe girar esos objetos inmediatamente.

1. Los secretos afectados deben deshabilitarse o eliminarse del almacén de claves.
1. Si las credenciales se usaron para una aplicación específica:
    1. Póngase en contacto con el administrador de la aplicación y pídale que realice una auditoría de su entorno para los usos de las credenciales en peligro, ya que se vulneraron.
    1. Si se usaron credenciales en peligro, el propietario de la aplicación debe identificar la información a la que se obtuvo acceso y mitigar el impacto.


## <a name="next-steps"></a>Pasos siguientes

En esta página se explica el proceso de respuesta a una alerta de Azure Defender para Key Vault. Para obtener información relacionada, consulte las páginas siguientes:

- [Introducción a Azure Defender para Key Vault](defender-for-key-vault-introduction.md)
- [Eliminación de alertas de Azure Defender](alerts-suppression-rules.md)
- [Exportación continua de alertas y recomendaciones de seguridad](continuous-export.md)