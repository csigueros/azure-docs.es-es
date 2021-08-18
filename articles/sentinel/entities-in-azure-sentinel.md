---
title: Uso de entidades para clasificar y analizar datos en Azure Sentinel | Microsoft Docs
description: Asigne clasificaciones de entidad (usuarios, nombres de host, direcciones IP) a elementos de datos de Azure Sentinel y úselas para comparar, analizar y correlacionar datos de varios orígenes.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 60e92f28d36f9dd4e08338ac90b703f9536cf767
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726071"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Clasificación y análisis de datos mediante entidades en Azure Sentinel

## <a name="what-are-entities"></a>¿Qué son las entidades?

Cuando Azure Sentinel envía o genera alertas, estas contienen elementos de datos que Sentinel puede reconocer y clasificar en categorías como **entidades**. Cuando Azure Sentinel entiende qué tipo de entidad representa un elemento de datos determinado, conoce las preguntas adecuadas que se deben formular sobre este y, a continuación, puede comparar las conclusiones sobre ese elemento en toda la gama de orígenes de datos, realizar su seguimiento fácilmente y hacer referencia a este en toda la experiencia de Sentinel: análisis, investigación, corrección, búsqueda, etc. Algunos ejemplos comunes de entidades son: usuarios, hosts, archivos, procesos, direcciones IP y direcciones URL.

### <a name="entity-identifiers"></a>Identificadores de entidad

Azure Sentinel admite una amplia variedad de tipos de entidad. Cada tipo tiene sus propios atributos únicos, incluidos algunos que pueden usarse para identificar una entidad determinada. Estos atributos se representan como campos en la entidad y se denominan **identificadores**. Consulte la lista completa de entidades admitidas y sus identificadores a continuación.

#### <a name="strong-and-weak-identifiers"></a>Identificadores seguros y no seguros

Como se indicó anteriormente, para cada tipo de entidad hay campos, o conjuntos de campos, que pueden identificarlo. Se puede hacer referencia a estos campos o conjuntos de campos como **identificadores seguros** si pueden identificar de forma única una entidad sin ambigüedad, o como **identificadores no seguros** si pueden identificar una entidad en algunas circunstancias, pero no se garantiza que identifiquen de forma única una entidad en todos los casos. En muchos casos, sin embargo, se puede combinar una selección de identificadores no seguros para generar un identificador seguro.

Por ejemplo, las cuentas de usuario se pueden identificar como entidades de **cuenta** de más de una manera: mediante un único **identificador seguro** como el identificador numérico de una cuenta de Azure AD (el campo **GUID**) o su valor de **Nombre principal de usuario (UPN)** ; o bien, con una combinación de **identificadores no seguros** como sus campos **Nombre** y **NTDomain**. Distintos orígenes de datos pueden identificar al mismo usuario de maneras diferentes. Cada vez que Azure Sentinel encuentra dos entidades que puede reconocer como la misma entidad en función de sus identificadores, combina las dos entidades en una sola entidad, de modo que se pueda controlar de manera correcta y coherente.

Sin embargo, si uno de los proveedores de recursos crea una alerta en la que una entidad no está suficientemente identificada (por ejemplo, con un solo **identificador no seguro** como un nombre de usuario sin el contexto de nombre de dominio), la entidad de usuario no se puede combinar con otras instancias de la misma cuenta de usuario. Esas otras instancias se identificarían como una entidad independiente, y esas dos entidades permanecerían independientes en lugar de unificarse.

Con el fin de minimizar el riesgo de que esto suceda, debe comprobar que todos los proveedores de alertas identifican correctamente las entidades en las alertas que producen. Además, la sincronización de entidades de cuenta de usuario con Azure Active Directory puede crear un directorio unificador que podrá combinar entidades de cuenta de usuario.

#### <a name="supported-entities"></a>Entidades admitidas

En Azure Sentinel se identifican actualmente los siguientes tipos de entidades:

- Cuenta de usuario
- Host
- Dirección IP
- Malware
- Archivo
- Proceso
- Aplicación en la nube
- Nombre de dominio
- Recurso de Azure
- Hash de archivo
- Clave del Registro
- Valor del Registro
- Grupo de seguridad
- Dirección URL
- Dispositivo IoT
- Mailbox
- Clúster de correo
- Mensaje de correo
- Correo de envío

Puede ver los identificadores de estas entidades y otra información importante en la [referencia de entidades](entities-reference.md).

## <a name="entity-mapping"></a>Asignación de entidades

¿Cómo reconoce Azure Sentinel que un fragmento de datos en una alerta identifica una entidad?

Echemos un vistazo al método de procesamiento de datos en Azure Sentinel. Los datos se introducen desde varios orígenes a través de [conectores](connect-data-sources.md), ya sea de servicio a servicio, basados en agente o mediante un servicio syslog y un reenviador de registros. Los datos se almacenan en tablas en el área de trabajo de Log Analytics. Después, estas tablas se consultan a intervalos programados regularmente por las reglas de análisis que se han definido y habilitado. Una de las muchas acciones que realizan estas reglas de análisis es la asignación de campos de datos de las tablas a las entidades reconocidas por Azure Sentinel. Según las asignaciones que defina en las reglas de análisis, Azure Sentinel tomará los campos de los resultados devueltos por la consulta, los reconocerá por los identificadores especificados para cada tipo de entidad y les aplicará el tipo de entidad identificado por esos identificadores.

¿Cuál es el objetivo de todo esto?

Cuando Azure Sentinel es capaz de identificar entidades en alertas de diferentes tipos de orígenes de datos y, especialmente, si puede hacerlo con identificadores seguros comunes a cada origen de datos o a un tercer esquema, puede establecer fácilmente la correlación entre todos estos orígenes de datos y alertas. Estas correlaciones ayudan a crear un almacén de información completo y conclusiones sobre las entidades, lo que le ofrece una base sólida para las operaciones de seguridad.

Obtenga información sobre cómo [asignar campos de datos a entidades](map-data-fields-to-entities.md).

Obtenga información acerca de [los identificadores que identifican una entidad de forma segura](entities-reference.md).

## <a name="entity-pages"></a>Páginas de entidad

Cuando se encuentra cualquier entidad (actualmente limitada a usuarios y hosts) en una búsqueda, una alerta o una investigación, puede seleccionar la entidad y llevarla a una **página de entidad**, una hoja de datos llena de información útil sobre esa entidad. Entre los tipos de información que encontrará en esta página se incluyen datos básicos sobre la entidad, una escala de tiempo de eventos importantes relacionados con esta entidad y conclusiones sobre el comportamiento de la entidad.

Las páginas de entidad constan de tres partes:

- El panel de la izquierda contiene la información de identificación de la entidad, recopilada de los orígenes de datos, como Azure Active Directory, Azure Monitor, Azure Security Center y Microsoft Defender.

- En el panel central se muestra una escala de tiempo gráfica y textual de eventos importantes relacionados con la entidad, como alertas, marcadores y actividades. Las actividades son agregaciones de eventos importantes desde Log Analytics. Los equipos de investigación de seguridad de Microsoft desarrollan las consultas que detectan esas actividades.

- En el panel derecho se muestran conclusiones de comportamiento sobre la entidad. Estas conclusiones ayudan a detectar rápidamente las anomalías y las amenazas de seguridad. Los equipos de investigación de seguridad de Microsoft desarrollan las conclusiones, que se basan en los modelos de detección de anomalías.

### <a name="the-timeline"></a>Escala de tiempo

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Escala de tiempo de las páginas de entidad":::

La escala de tiempo es una parte importante de la contribución de la página de entidad al análisis del comportamiento en Azure Sentinel. Presenta una historia sobre eventos relacionados con la entidad, lo que le ayuda a comprender la actividad de la entidad dentro de un período de tiempo específico.

Puede elegir el **intervalo de tiempo** entre varias opciones preestablecidas (como *últimas 24 horas*) o establecerlo en cualquier período de tiempo definido de forma personalizada. Además, puede establecer filtros que limiten la información de la escala de tiempo a tipos específicos de eventos o alertas.

En la escala de tiempo se incluyen los siguientes tipos de elementos:

- Alertas: alertas en las que la entidad se define como **entidad asignada**. Tenga en cuenta que si su organización ha creado [alertas personalizadas mediante reglas de análisis](./detect-threats-custom.md), debe asegurarse de que la asignación de entidades de las reglas se realice correctamente.

- Marcadores: marcadores que incluyen la entidad específica que se muestra en la página.

- Actividades: agregación de eventos importantes relacionados con la entidad.

### <a name="entity-insights"></a>Conclusiones sobre la entidad

Las conclusiones sobre la entidad son consultas definidas por los investigadores de seguridad de Microsoft para ayudar a los analistas a investigar de manera más eficiente y eficaz. Las conclusiones se presentan como parte de la página de entidad y proporcionan información de seguridad valiosa sobre los hosts y los usuarios, en forma de gráficos y datos tabulares. Disponer aquí de la información significa que no tiene que desplazarse a Log Analytics. Las conclusiones incluyen datos sobre inicios de sesión, adiciones a grupos, eventos anómalos, etc., así como algoritmos avanzados de Azure Machine Learning para detectar comportamientos anómalos.

Las conclusiones se basan en los siguientes orígenes de datos:

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Heartbeat (agente de Azure Monitor)
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>Cómo usar páginas de entidad

Las páginas de entidad están diseñadas para formar parte de varios escenarios de uso y se puede acceder a ellas desde la administración de incidentes, el gráfico de investigación, los marcadores o directamente desde la página de búsqueda de entidades en **Análisis de comportamiento de entidades** en el menú principal de Azure Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Casos de uso de páginas de entidad":::

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a trabajar con entidades en Azure Sentinel. Para obtener instrucciones prácticas sobre la implementación y para usar las conclusiones obtenidas, consulte los siguientes artículos:

- [Habilitación del análisis de comportamiento de entidades](./enable-entity-behavior-analytics.md) en Azure Sentinel
- [Búsqueda de amenazas de seguridad](./hunting.md)
