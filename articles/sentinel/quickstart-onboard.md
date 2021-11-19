---
title: 'Inicio rápido: Incorporación a Microsoft Sentinel'
description: En este inicio rápido, aprenderá cómo incorporar Microsoft Sentinel habilitándolo primero y, a continuación, conectando los orígenes de datos.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: quickstart
ms.date: 11/09/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 46ac4308035491dc2d6b75e34bc0c58a58f93535
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520814"
---
# <a name="quickstart-on-board-microsoft-sentinel"></a>Inicio rápido: Incorporación a Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este inicio rápido, aprenderá cómo incorporar Microsoft Sentinel. Para incorporar Microsoft Sentinel, primero debe habilitarlo y, después, conectar sus orígenes de datos.

Microsoft Sentinel incluye varios conectores para soluciones de Microsoft, que están disponibles inmediatamente y proporcionan integración en tiempo real, entre las que se incluyen las soluciones de Microsoft 365 Defender (anteriormente, Protección contra amenazas de Microsoft), orígenes de Microsoft 365 (como Office 365), Azure AD, Microsoft Defender for Identity (anteriormente, Azure ATP), Microsoft Defender for Cloud Apps, alertas de seguridad de Microsoft Defender for Cloud y más. Además, hay conectores integrados al amplio ecosistema de seguridad para soluciones que no son de Microsoft. También puede usar el formato de evento común, Syslog o las API REST para conectar los orígenes de datos con Microsoft Sentinel.

Después de conectar los orígenes de datos, puede elegir de una galería de libros creados de forma experta que exponen información basada en los datos. Estos libros se pueden personalizar fácilmente en función de sus necesidades.

>[!IMPORTANT]
> Para obtener información sobre los cargos en los que se incurre al usar Microsoft Sentinel, consulte los [precios de Microsoft Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) y los [costos y facturación de Microsoft Sentinel](azure-sentinel-billing.md).

## <a name="global-prerequisites"></a>Requisitos previos globales

- Suscripción activa de Azure. Si no tiene una, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de comenzar.

- Área de trabajo de Log Analytics. Aprenda a [crear un área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md). Para obtener más información sobre las áreas de trabajo de Log Analytics, consulte [Diseño de su implementación de Azure Monitor Logs](../azure-monitor/logs/design-logs-deployment.md).

- Para habilitar Microsoft Sentinel, necesita permisos de colaborador en la suscripción en la que reside el área de trabajo de Microsoft Sentinel. 
- Para usar Microsoft Sentinel, necesita permisos de colaborador o lector en el grupo de recursos al que pertenece el área de trabajo.
- Es posible que se necesiten permisos adicionales para conectarse a orígenes de datos específicos.
- Microsoft Sentinel es un servicio de pago. Para más información, consulte [Acerca de Microsoft Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).

Para más información, consulte [Actividades previas a la implementación y requisitos previos para implementar Microsoft Sentinel](prerequisites.md).

### <a name="geographical-availability-and-data-residency"></a>Disponibilidad geográfica y residencia de datos

- Microsoft Sentinel se puede ejecutar en áreas de trabajo de las [regiones con disponibilidad general de Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), excepto en China y Alemania (soberana). Las regiones nuevas de Log Analytics pueden tardar algún tiempo en incorporarse al servicio Microsoft Sentinel. 

- Los datos que genera Microsoft Sentinel, como incidentes, marcadores y reglas de análisis, pueden contener datos del cliente procedentes de las áreas de trabajo de Log Analytics del cliente. Estos datos generados por Microsoft Sentinel se guardan en la geografía que se muestra en la tabla siguiente, según la geografía en que se encuentra el área de trabajo:

    | Geografía del área de trabajo | Geografía de datos generados por Microsoft Sentinel |
    | --- | --- |
    | Estados Unidos<br>India | Estados Unidos |
    | Europa<br>Francia | Europa |
    | Australia | Australia |
    | Reino Unido | Reino Unido |
    | Canadá | Canadá |
    | Japón | Japón |
    | Asia Pacífico | Asia Pacífico* |
    | Brasil | Brasil* |
    | Noruega | Noruega |
    | África | África |
    | Corea | Corea |
    | Alemania | Alemania |
    | Emiratos Árabes Unidos | Emiratos Árabes Unidos |
    | Suiza | Suiza |
    |

    \*Actualmente, la residencia de datos en una sola región solo se proporciona en la región Sudeste Asiático (Singapur) del área geográfica de Asia Pacífico, y en la región Sur de Brasil (estado de Sao Paulo) del área geográfica de Brasil. Para todas las demás regiones, los datos de los clientes se pueden almacenar en cualquier lugar del área geográfica del área de trabajo donde se incorpore Sentinel.

    > [!IMPORTANT]
    > - Al habilitar ciertas reglas que usan el motor de aprendizaje automático (ML), **concede a Microsoft permiso para copiar los datos ingeridos pertinentes fuera de la geografía del área de trabajo de Microsoft Sentinel**, según sea necesario para que el motor de aprendizaje automático procese estas reglas.

## <a name="enable-microsoft-sentinel"></a>Habilitar Microsoft Sentinel <a name="enable"></a>

1. Inicie sesión en Azure Portal. Asegúrese de que la suscripción en la que se crea Microsoft Sentinel está seleccionada.

1. Busque y seleccione **Microsoft Sentinel**.

   ![Búsqueda de servicios](./media/quickstart-onboard/search-product.png)

1. Seleccione **Agregar**.

1. Seleccione el área de trabajo que quiere usar o cree una nueva. Puede ejecutar Microsoft Sentinel en más de un área de trabajo, pero los datos se aíslan en un área de trabajo única.

   ![Elegir un área de trabajo](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Las áreas de trabajo predeterminadas creadas por Microsoft Defender for Cloud no aparecerán en la lista. No se puede instalar Microsoft Sentinel en ellas.
   >

   >[!IMPORTANT]
   >
   > - Una vez que se implementa en un área de trabajo, Microsoft Sentinel **no admite actualmente** el movimiento de esa área de trabajo a otros grupos de recursos o suscripciones. 
   >
   >   Si ya ha movido el área de trabajo, deshabilite todas las reglas activas en **Análisis** y vuelva a habilitarlas después de cinco minutos. Esto debe ser efectivo en la mayoría de los casos; sin embargo, cabe reiterar que no se admite y se realiza bajo su responsabilidad.

1. Seleccione **Add Microsoft Sentinel** (Agregar Microsoft Sentinel).

## <a name="connect-data-sources"></a>Conexión con orígenes de datos

Microsoft Sentinel ingiere datos de servicios y aplicaciones mediante la conexión y el reenvío de los eventos y registros a Microsoft Sentinel. Tanto en las máquinas físicas como en las virtuales, puede instalar el agente de Log Analytics que recopila los registros y los reenvía a Microsoft Sentinel. En el caso de los firewalls y servidores proxy, Microsoft Sentinel instala el agente de Log Analytics en un servidor de Syslog de Linux, desde el que el agente recopila los archivos de registro y los reenvía a Microsoft Sentinel. 
 
1. En el menú principal, seleccione **Data connectors** (Conectores de datos). Se abre la galería de conectores de datos.

1. La galería de es una lista de todos los orígenes de datos que se pueden conectar. Seleccione un origen de datos y, después, el botón **Open connector page** (Abrir página del conector).

1. En la página del conector encontrará instrucciones para configurar el conector, así como otras instrucciones adicionales que pueda necesitar.

    Por ejemplo, si selecciona el origen de datos **Azure Active Directory**, que permite transmitir registros de Azure AD a Microsoft Sentinel, puede seleccionar el tipo de registros que desea obtener (registros de inicio de sesión o registros de auditoría). <br> Siga las instrucciones de instalación o [consulte la guía de conexión relevante](data-connectors-reference.md) para obtener más información. Para más información acerca de los conectores de datos, consulte [Conectores de datos de Microsoft Sentinel](connect-data-sources.md).

1. En la pestaña **Pasos siguientes** de la página del conector se muestran los libros integrados, las consultas de ejemplo y las plantillas de reglas de análisis pertinentes que acompañan al conector de datos. Puede usarlos tal cual o modificarlos (de cualquiera de las dos formas puede obtener información interesante en los datos).

Una vez conectados los orígenes de datos, los datos comienzan a transmitirse a Microsoft Sentinel y podrá comenzar a trabajar con ellos. Puede ver los registros en los [libros integrados](get-visibility.md) y comenzar a crear consultas en Log Analytics para [investigar los datos](investigate-cases.md).

Para más información, consulte [Procedimientos recomendados para recopilaciones de datos](best-practices-data.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- **Opciones de implementación alternativas**:

    - [Implementación de Microsoft Sentinel mediante API](/rest/api/securityinsights/)
    - [Implementación de Microsoft Sentinel mediante PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)
    - [Implementación de Microsoft Sentinel mediante una plantilla de ARM](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-all-in-one-accelerator/ba-p/1807933)

- **Introducción**:
    - [Introducción a Microsoft Azure Sentinel](get-visibility.md)
    - [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md)
    - [Conexión de su solución externa con Common Event Format](connect-common-event-format.md)
