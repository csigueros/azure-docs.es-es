---
title: Conexión de orígenes de datos a Azure Sentinel| Microsoft Docs
description: Obtenga información sobre cómo conectar orígenes de datos como Microsoft 365 Defender (anteriormente Protección contra amenazas de Microsoft), Microsoft 365 y Office 365, Azure AD, ATP y Cloud App Security a Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: c9bce99f26d7d23d2bf655719373f43e78b5a8cb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749366"
---
# <a name="connect-data-sources"></a>Conexión con orígenes de datos

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Tras habilitar Azure Sentinel, lo primero que debe hacer es conectar los orígenes de datos. Azure Sentinel incluye varios conectores para productos de Microsoft, que están disponibles inmediatamente y proporcionan integración en tiempo real. Por ejemplo, los conectores de servicio a servicio incluyen conectores de Microsoft 365 Defender y orígenes de Microsoft 365, como Office 365, Azure Active Directory (Azure AD), Microsoft Defender for Identity y Microsoft Cloud App Security.

También puede habilitar conectores integrados en el amplio ecosistema de seguridad para productos que no son de Microsoft. Por ejemplo, puede usar [Syslog](#syslog), el [formato de evento común (CEF)](#common-event-format-cef) o las [API de REST](#rest-api-integration) para conectar los orígenes de datos a Azure Sentinel.

Para obtener más información, consulte [Conectores de datos de asociados de Azure Sentinel](partner-data-connectors.md) y el [catálogo de soluciones de Azure Sentinel](sentinel-solutions-catalog.md).

## <a name="enable-a-data-connector"></a>Habilitación de un conector de datos

Para habilitar un conector de datos en Azure Sentinel:

1. En el menú de navegación izquierdo de Azure Sentinel, seleccione **Conectores de datos**. En la página **Conectores de datos** se muestra la lista completa de los conectores que Azure Sentinel proporciona, y su estado. Seleccione el conector al que desea conectarse y, después, seleccione **Abrir la página del conector**. 

   ![Galería de conectores de datos](./media/collect-data/collect-data-page.png)
   
   Para obtener más información, consulte [Integración de servicio a servicio](#service-to-service-integration) y [Conectores de datos de asociados de Azure Sentinel](partner-data-connectors.md).
   

1. En la página del conector, asegúrese de que cumple todos los requisitos previos y siga las instrucciones para conectar los datos a Azure Sentinel. Los registros pueden tardar algún tiempo en iniciar la sincronización con Azure Sentinel. Después de conectarse, verá un resumen de los datos en el gráfico **Datos recibidos**, y el estado de conectividad de los tipos de datos.

   ![Configuración de conectores de datos](./media/collect-data/opened-connector-page.png)
  
1. Seleccione la pestaña **Siguientes pasos** para ver el contenido que Azure Sentinel proporciona para el tipo de datos específico.

   ![Pasos siguientes para los conectores](./media/collect-data/data-insights.png)

<a name="agent-options"></a>
## <a name="data-connection-methods"></a>Métodos de conexión de datos
<a name="map-data-types-with-azure-sentinel-connection-options"></a>

Azure Sentinel admite los siguientes métodos de conexión de datos:

### <a name="service-to-service-integration"></a>Integración de servicio a servicio

Azure Sentinel usa la base de Azure para proporcionar compatibilidad integrada de servicio a servicio con los siguientes servicios de Microsoft y Amazon Web Services:

- [Amazon Web Services: CloudTrail](connect-aws.md)
- [Azure Active Directory](connect-azure-active-directory.md), incluidos los registros de auditoría y registros de inicio de sesión
- [Azure Active Directory Identity Protection](connect-azure-ad-identity-protection.md)
- [Azure Activity](connect-azure-activity.md) (Actividad de Azure)
- [Azure DDoS Protection](connect-azure-ddos-protection.md)
- Alertas de [Azure Defender](connect-azure-security-center.md) desde Azure Security Center
- [Azure Defender para IoT](connect-asc-iot.md) (anteriormente conocido como Azure Security Center para IoT)
- [Azure Firewall](connect-azure-firewall.md)
- [Azure Information Protection](connect-azure-information-protection.md). Para más información, consulte [Modificación de informes y creación de consultas personalizadas](/azure/information-protection/reports-aip#how-to-modify-the-reports-and-create-custom-queries).
- [Azure Key Vault](connect-azure-key-vault.md)
- [Azure Kubernetes Service (AKS)](connect-azure-kubernetes-service.md)
- [Azure SQL Database](connect-azure-sql-logs.md)
- [Cuenta de Azure Storage](connect-azure-storage-account.md)
- [Azure Web Application Firewall (WAF)](connect-azure-waf.md) (anteriormente conocido como Microsoft WAF)
- [Servidor de nombres de dominio](connect-dns.md)
- [Dynamics 365](connect-dynamics-365.md)
- [Microsoft 365 Defender](connect-microsoft-365-defender.md), incluidos los incidentes de Microsoft 365 Defender y los datos sin procesar de Microsoft 365 Defender para punto de conexión
- [Microsoft Cloud App Security (MCAS)](connect-cloud-app-security.md)
- [Microsoft Defender para Endpoint](connect-microsoft-defender-advanced-threat-protection.md) (anteriormente Protección contra amenazas avanzada de Microsoft)
- [Microsoft Defender for Identity](connect-azure-atp.md) (anteriormente Azure ATP)
- [Microsoft Defender para Office 365](connect-office-365-advanced-threat-protection.md) (anteriormente conocido como Protección contra amenazas avanzada de Office 365)
- [Office 365](connect-office-365.md), incluido Microsoft Teams
- [Eventos de seguridad (de Windows)](connect-windows-security-events.md). Para obtener más información, consulte [Configuración de libros de protocolos poco seguros](./get-visibility.md#use-built-in-workbooks).
- [Firewall de Windows](connect-windows-firewall.md)

### <a name="rest-api-integration"></a>Integración de API REST

Muchas tecnologías de seguridad proporcionan un conjunto de API para recuperar archivos de registro y algunos orígenes de datos pueden usar esas API para conectarse a Azure Sentinel.

Los conectores de datos que usan API se integran desde el lado del proveedor o se integran mediante Azure Functions, como se describe en las secciones siguientes.

Para obtener una lista completa e información sobre estos conectores, consulte [Conectores de datos de asociados de Azure Sentinel](partner-data-connectors.md).

#### <a name="rest-api-integration-on-the-provider-side"></a>Integración de la API de REST en el lado del proveedor

Una integración de la API creada por el proveedor se conecta con los orígenes de datos del proveedor e inserta los datos en las tablas de registro personalizadas de Azure Sentinel mediante la [API de recopilador de datos de Azure Monitor](../azure-monitor/logs/data-collector-api.md).

Para obtener más información sobre cómo configurar estos conectores de datos para enviar registros a Azure Sentinel, consulte la documentación del proveedor pertinente. 

#### <a name="rest-api-integration-using-azure-functions"></a>Integración de la API de REST mediante Azure Functions

Las integraciones que usan [Azure Functions](../azure-functions/index.yml) para conectarse con una API de proveedor dan formato primero a los datos y, a continuación, los envían a las tablas de registro personalizadas de Azure Sentinel mediante la [API de recopilador de datos de Azure Monitor](../azure-monitor/logs/data-collector-api.md).

Para obtener más información sobre cómo configurar estos conectores de datos para conectarse con la API del proveedor y recopilar registros en Azure Sentinel, siga los pasos que se muestran para cada conector de datos en Azure Sentinel.

Las integraciones que usan Azure Functions también tienen un botón **Implementar en Azure** en su página del conector de datos de Azure Sentinel. Para estas integraciones, haga lo siguiente a fin de simplificar la configuración:

1. Seleccione **Implementar en Azure**.
1. Escriba los valores de parámetro para conectarse con la API del proveedor.
1. Escriba el identificador de área de trabajo de Azure Sentinel y la clave para conectarse con Log Analytics, y envíe los datos a Azure Sentinel.

> [!IMPORTANT]
> Las integraciones que usan Azure Functions pueden incurrir en costos de ingesta de datos adicionales, ya que hospeda Azure Functions en el inquilino de Azure. Para más información, consulte la [página de precios de Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

### <a name="agent-based-integration"></a>Integración basada en agente

Azure Sentinel puede usar el protocolo Syslog para conectarse a través de un agente a cualquier origen de datos que pueda realizar el streaming de registros en tiempo real. Por ejemplo, la mayoría de los orígenes de datos locales se conectan a través de la integración basada en agente.
En las secciones siguientes se describen los distintos tipos de conectores de datos basados en agente de Azure Sentinel. Siga los pasos descritos en cada página del conector de datos de Azure Sentinel para configurar las conexiones mediante mecanismos basados en agente.

Para obtener una lista completa de firewalls, servidores proxy y puntos de conexión que se conecten a Azure Sentinel a través de CEF o Syslog, consulte [Conectores de datos de asociados de Azure Sentinel](partner-data-connectors.md).

#### <a name="syslog"></a>syslog

Puede transmitir eventos de dispositivos compatibles con Syslog basados en Linux a Azure Sentinel mediante el agente de Log Analytics para Linux, anteriormente conocido como agente de OMS. El agente de Log Analytics es compatible con cualquier dispositivo que le permita instalar el agente de Log Analytics directamente en el dispositivo.

El demonio de Syslog integrado del dispositivo recopila los eventos locales de los tipos especificados y los reenvía localmente al agente, que los transmite al área de trabajo de Log Analytics. Después de una configuración correcta, los datos aparecen en la tabla Syslog de Log Analytics.

Dependiendo del tipo de dispositivo, el agente se instala directamente en el dispositivo o en un reenviador de registros basado en Linux. El agente de Log Analytics recibe eventos del demonio de Syslog a través de UDP. Si se espera que una máquina Linux recopile un gran volumen de eventos Syslog, enviará los eventos a través de TCP desde el demonio de Syslog al agente y desde allí a Log Analytics.

Para obtener más información, consulte [Conexión de los dispositivos basados en Syslog a Azure Sentinel](connect-syslog.md). 

#### <a name="common-event-format-cef"></a>Formato de evento común (CEF)

Los formatos de registro varían, pero muchos orígenes admiten el formato basado en CEF. El agente de Azure Sentinel, que es realmente el agente de Log Analytics, convierte los registros con formato CEF en un formato que Log Analytics puede ingerir.

En el caso de los orígenes de datos que emiten datos en CEF, configure el agente de Syslog y, a continuación, configure el flujo de datos CEF. Después de una configuración correcta, los datos aparecen en la tabla **CommonSecurityLog**.

Para obtener más información, consulte [Conexión de los dispositivos basados en CEF a Azure Sentinel](connect-common-event-format.md).

#### <a name="custom-logs"></a>Registros personalizados

Algunos orígenes de datos tienen registros disponibles para su recopilación como archivos en Windows o Linux. Puede recopilar estos registros mediante el agente de recopilación de registros personalizados de Log Analytics.

Siga los pasos descritos en cada página del conector de datos de Azure Sentinel para conectarse mediante el agente de recopilación de registros personalizados de Log Analytics. Después de una configuración correcta, los datos aparecen en tablas personalizadas.

Para obtener más información, consulte [Recopilación de registros personalizados con el agente de Log Analytics](../azure-monitor/agents/data-sources-custom-logs.md).

## <a name="data-connector-support"></a>Soporte técnico de los conectores de datos

Tanto Microsoft como otras organizaciones crean conectores de datos de Azure Sentinel. Cada conector de datos tiene uno de los siguientes tipos de soporte técnico:

| Tipo de soporte técnico| Descripción|
|-------------|------------|
|**Soporte técnico de Microsoft**|Se aplica a:<ul><li>Conectores de datos para orígenes de datos donde Microsoft es el proveedor y creador de datos.</li><li>Algunos conectores de datos creados por Microsoft para orígenes de datos que no son de Microsoft.</li></ul>Microsoft admite y mantiene los conectores de datos en esta categoría de acuerdo con [Planes de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/options/#overview).<br><br>Los asociados o los conectores de datos de soporte técnico de la comunidad creados por cualquier entidad distinta de Microsoft.|
|**Soporte técnico de asociados**|Se aplica a los conectores de datos creados por entidades distintas de Microsoft.<br><br>La empresa asociada proporciona soporte técnico o mantenimiento para estos conectores de datos. La empresa asociada puede ser un fabricante de software independiente, un proveedor de servicios administrados (MSP/MSSP), un integrador de sistemas (SI) o cualquier organización cuya información de contacto se proporcione en la página de Azure Sentinel para ese conector de datos.<br><br>Para cualquier problema con el conector de datos compatible con asociados, póngase en contacto con el contacto de soporte técnico de los conectores de datos especificado.|
|**Soporte técnico de la comunidad**|Se aplica a los conectores de datos creados por Microsoft o desarrolladores asociados que no incluyen contactos para el soporte técnico y el mantenimiento de los conectores de datos en la página del conector de datos especificada de Azure Sentinel.<br><br>Si tiene preguntas o problemas relacionados con estos conectores de datos, puede [registrar un problema](https://github.com/Azure/Azure-Sentinel/issues/new/choose) en la [comunidad GitHub de Azure Sentinel](https://aka.ms/threathunters).|

### <a name="find-the-support-contact-for-a-data-connector"></a>Búsqueda del contacto de soporte técnico para una conector de datos

Para encontrar la información de contacto del soporte técnico para un conector de datos:

1. En el menú izquierdo de Azure Sentinel, seleccione **Conectores de datos**.
   
1. Seleccione el conector para el que desea encontrar información de soporte técnico.
   
1. Consulte el campo **Compatible con** en el panel lateral del conector de datos.
   
   ![Captura de pantalla que muestra el campo Compatible con de un conector de datos en Azure Sentinel.](./media/collect-data/connectors.png)
   
   El campo **Compatible con** tiene un vínculo de contacto de soporte técnico que puede usar para acceder al soporte técnico y mantenimiento para el conector de datos seleccionado.



## <a name="next-steps"></a>Pasos siguientes

- Para empezar a trabajar con Azure Sentinel, necesita una suscripción a Microsoft Azure. Si no tiene ninguna suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/free/).
- Aprenda a [incorporar los datos en Azure Sentinel](quickstart-onboard.md), [obtenga visibilidad sobre ellos y aprenda a defenderse de posibles amenazas](get-visibility.md).