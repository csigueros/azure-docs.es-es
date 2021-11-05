---
title: Ventajas y características de Microsoft Defender para Key Vault
description: Obtenga información sobre las ventajas y características de Azure Defender para Key Vault.
author: memildin
ms.author: memildin
ms.date: 10/20/2021
ms.topic: overview
ms.service: security-center
ms.custom: references_regions, ignite-fall-2021
manager: rkarlin
ms.openlocfilehash: bec034539b7969a7552952eddd6d7673e35ebcaf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055925"
---
# <a name="introduction-to-microsoft-defender-for-key-vault"></a>Introducción a Microsoft Defender para Key Vault

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Key Vault es un servicio en la nube que protege las claves de cifrado y los secretos, como certificados, cadenas de conexión y contraseñas. 

Habilite **Microsoft Defender para Key Vault** para obtener protección nativa avanzada contra amenazas en la nube para Azure Key Vault y proporcionar una nivel adicional de inteligencia de seguridad. 

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|El servicio **Azure Defender para Key Vault** se factura según se indica en la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/no-icon.png"::: Nacionales o soberanas (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-key-vault"></a>¿Cuáles son las ventajas de Azure Defender para Key Vault?

Azure Defender detecta intentos potencialmente perjudiciales e inusuales de acceder a las cuentas de Key Vault o vulnerarlas. Esta capa de protección le permite afrontar las amenazas sin ser un experto en seguridad y sin la necesidad de administrar sistemas de supervisión de la seguridad de terceros.  

Cuando se producen actividades anómalas, Azure Defender muestra alertas y, opcionalmente, las envía por correo electrónico a los miembros correspondientes de la organización. Dichas alertas incluyen detalles acerca de cualquier actividad sospechosa y recomendaciones acerca de cómo investigar amenazas y mitigarlas. 

## <a name="microsoft-defender-for-key-vault-alerts"></a>Alertas de Microsoft Defender para Key Vault
Cuando reciba una alerta de Microsoft Defender para Key Vault, es recomendable investigarla y responder, tal y como se describe en [Respuesta a las alertas de Microsoft Defender para Key Vault](defender-for-key-vault-usage.md). Microsoft Defender para Key Vault protege las aplicaciones y credenciales, por lo que aunque usted conozca la aplicación o el usuario que desencadenó la alerta, es importante comprobar las circunstancias que rodean a cada alerta.

Las alertas aparecen en la página de **seguridad** de Key Vault, las protecciones de cargas de trabajo y la página de alertas de Defender for Cloud.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Página de seguridad de Azure Key Vault":::


> [!TIP]
> Para simular alertas de Microsoft Defender para Key Vault, siga las indicaciones del artículo sobre la [validación de la detección de amenazas de Azure Key Vault en Microsoft Defender for Cloud](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="respond-to-microsoft-defender-for-key-vault-alerts"></a>Respuesta a las alertas de Microsoft Defender para Key Vault
Cuando reciba una alerta de [Microsoft Defender para Key Vault](defender-for-key-vault-introduction.md), es recomendable investigarla y responder, tal y como se describe a continuación. Microsoft Defender para Key Vault protege las aplicaciones y credenciales, por lo que aunque usted conozca la aplicación o el usuario que desencadenó la alerta, es importante comprobar las circunstancias que rodean a cada alerta.  

Las alertas de Microsoft Defender para Key Vault constan de los siguientes elementos:

- Identificador de objeto
- Nombre principal de usuario o dirección IP del recurso sospechoso 

En función del *tipo* de acceso que se haya producido, algunos campos pueden no estar disponibles. Por ejemplo, si una aplicación ha accedido al almacén de claves, no verá un nombre principal de usuario asociado. Si el tráfico se originó fuera de Azure, no verá un identificador de objeto.

> [!TIP]
> A las máquinas virtuales de Azure se les asignan direcciones IP de Microsoft. Esto significa que una alerta puede contener una dirección IP de Microsoft aunque guarde relación con actividad registrada fuera de Microsoft. Por lo tanto, aunque una alerta tenga una dirección IP de Microsoft, debe investigarse de todos modos tal y como se describe en esta página.

### <a name="step-1-identify-the-source"></a>Paso 1. Identificación del origen

1. Compruebe si el tráfico se originó en el inquilino de Azure. Si el firewall del almacén de claves está habilitado, es probable que haya proporcionado acceso al usuario o a la aplicación que desencadenó esta alerta.
1. Si no puede comprobar el origen del tráfico, continúe con el [Paso 2. Respuesta consecuente](#step-2-respond-accordingly).
1. Si puede identificar el origen del tráfico en el inquilino, póngase en contacto con el usuario o el propietario de la aplicación. 

> [!CAUTION]
> Microsoft Defender para Key Vault se ha concebido para ayudar a identificar la actividad sospechosa asociada al robo de credenciales. **No** descarte la alerta simplemente porque reconoce el usuario o la aplicación. Póngase en contacto con el propietario de la aplicación o el usuario y compruebe que la actividad es legítima. Puede crear una regla de supresión para eliminar el ruido si es necesario. Obtenga más información en [Eliminación de alertas de seguridad](alerts-suppression-rules.md).


### <a name="step-2-respond-accordingly"></a>Paso 2. Respuesta consecuente 
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

### <a name="step-3-measure-the-impact"></a>Paso 3. Medida del impacto
Cuando se haya mitigado el evento, investigue los secretos del almacén de claves que se han visto afectados:
1. Abra la página **Seguridad** en Azure Key Vault y vea la alerta desencadenada.
1. Seleccione la alerta específica que se desencadenó.
    Revise la lista de los secretos a los que se accedió y la marca de tiempo.
1. Opcionalmente, si tiene habilitados los registros de diagnóstico del almacén de claves, revise las operaciones anteriores para la dirección IP del autor de la llamada correspondiente, la entidad de seguridad de usuario o el identificador de objeto.  

### <a name="step-4-take-action"></a>Paso 4. Realizar acción 
Una vez que haya compilado la lista de los secretos, las claves y los certificados a los que ha accedido el usuario o la aplicación sospechosos, debe girar esos objetos inmediatamente.

1. Los secretos afectados deben deshabilitarse o eliminarse del almacén de claves.
1. Si las credenciales se usaron para una aplicación específica:
    1. Póngase en contacto con el administrador de la aplicación y pídale que realice una auditoría de su entorno para los usos de las credenciales en peligro, ya que se vulneraron.
    1. Si se usaron credenciales en peligro, el propietario de la aplicación debe identificar la información a la que se obtuvo acceso y mitigar el impacto.





## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre Microsoft Defender para Key Vault.

Para obtener material relacionado, consulte los siguientes artículos: 

- [Alertas de seguridad de Key Vault](alerts-reference.md#alerts-azurekv): la sección sobre Key Vault de la tabla de referencia para todas las alertas de Microsoft Defender for Cloud.
- [Exportación continua de datos de Defender for Cloud](continuous-export.md)
- [Eliminación de alertas de seguridad](alerts-suppression-rules.md)
