---
title: Personalización de las actividades en las escalas de tiempo de entidades de Azure Sentinel| Microsoft Docs
description: Agregue actividades personalizadas a aquellas de las que Azure Sentinel hace un seguimiento y muéstrelas en la escala de tiempo de las páginas de entidad.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2021
ms.author: yelevin
ms.openlocfilehash: 4b906e9c9fc72cd44513969c316900b190aa5a76
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689214"
---
# <a name="customize-activities-on-entity-page-timelines"></a>Personalización de actividades en las escalas de tiempo de las páginas de entidad

> [!IMPORTANT]
>
> - La personalización de las actividades está en **VERSIÓN PRELIMINAR.** Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="introduction"></a>Introducción

Azure Sentinel viene preconfigurado para hacer un seguimiento de determinadas actividades que además presenta en la escala de tiempo. Pues además de estas actividades, puede agregar otras para que también se les haga un seguimiento y se presenten en la escala de tiempo. Puede crear actividades personalizadas basadas en consultas de datos de entidades de cualquier origen de datos conectado. En los ejemplos siguientes se muestra cómo podría usar esta funcionalidad:

- Agregar nuevas actividades a la escala de tiempo de la entidad mediante la modificación de las plantillas de actividad estándar existentes.

- Agregar nuevas actividades a partir de registros personalizados: por ejemplo, desde un registro de control de acceso físico, puede agregar actividades de entrada y salida de un usuario para un edificio determinado a la escala de tiempo del usuario.

## <a name="getting-started"></a>Introducción

1. En el menú de navegación de Azure Sentinel, seleccione **Entity behavior** (Comportamiento de entidad).

1. En la hoja **Comportamiento de la entidad**, seleccione **Personalizar página de entidad** en la parte superior de la pantalla.

    :::image type="content" source="./media/customize-entity-activities/entity-behavior-blade.png" alt-text="Página Comportamiento de la entidad":::

1. Aparecerá una página con una lista de las actividades que ha creado en la pestaña **Mis actividades**. En la pestaña **Plantillas de actividad**, verá la colección de actividades ofrecidas de forma predeterminada por los investigadores en materia de seguridad de Microsoft. Estas son las actividades de las que ya se realiza un seguimiento y que se muestran en las escalas de tiempo de las páginas de entidad.

    > [!NOTE]
    > - Siempre que no haya creado ninguna actividad definida por el usuario, las páginas de entidad mostrarán todas las actividades enumeradas en la pestaña **Plantillas de actividad**.
    >
    > - Una vez que defina una actividad personalizada única, las páginas de entidad **solo** mostrarán las actividades que aparecen en la pestaña **Mis actividades**.
    >
    > - Si desea seguir viendo las actividades listas para usar en las páginas de entidad, debe crear una actividad para cada plantilla que quiere que se muestre y de la que quiere llevar un seguimiento. Siga las instrucciones que se indican en la sección "Creación de una actividad a partir de una plantilla" a continuación.

## <a name="create-an-activity-from-a-template"></a>Creación de una actividad a partir de una plantilla

1. Haga clic en la pestaña **Plantillas de actividad** para ver las distintas actividades disponibles de forma predeterminada. Puede filtrar la lista por tipo de entidad, así como por origen de datos. Al seleccionar una actividad de la lista, se mostrarán los detalles siguientes en el panel de vista previa:

    -  Una descripción de la actividad.

    - El origen de datos que proporciona los eventos que componen la entidad.

    - Los identificadores usados para identificar la entidad en los datos sin procesar.

    - La consulta que da como resultado la detección de esta actividad.

1. Haga clic en el botón **Crear actividad** situado en la parte inferior del panel de vista previa para iniciar el Asistente para la creación de actividades.

    :::image type="content" source="./media/customize-entity-activities/activity-details.png" alt-text="Vista de detalles de actividad":::

1. Se abrirá el **Asistente para actividades: Creación de una actividad a partir de una plantilla**, con sus campos ya rellenados a partir de la plantilla. Puede hacer los cambios que desee en las pestañas **General** y **Configuración de actividad**, o bien no modifique nada si quiere seguir viendo la actividad lista para usar.

1. Cuando esté satisfecho, seleccione la pestaña **Revisar y crear**. Cuando vea el mensaje **Validación completada**, haga clic en el botón **Crear** en la parte inferior.

## <a name="create-an-activity-from-scratch"></a>Creación de una actividad de cero

En la parte superior de la página de actividades, haga clic en **Agregar actividad** para iniciar el Asistente para la creación de actividades.

Se abrirá el **Asistente para actividades: Creación de una actividad**, con sus campos en blanco.

### <a name="general-tab"></a>Pestaña General
1. Escriba un nombre para la actividad (por ejemplo: "usuario agregado al grupo").

1. Escriba una descripción de la actividad (ejemplo: "cambio de pertenencia a grupos de usuarios basado en el identificador de evento de Windows 4728").

1. Seleccione el tipo de la entidad (usuario o host) de la que realizará el seguimiento esta consulta.

1. Puede filtrar por parámetros adicionales para ayudar a ajustar la consulta y optimizar su rendimiento. Por ejemplo, puede filtrar por usuarios de Active Directory seleccionando el parámetro **IsDomainJoined** y estableciendo el valor en **True**.

1. Puede seleccionar el estado inicial de la actividad en **Habilitado** o **Deshabilitado**.

1. Seleccione **Siguiente : Configuración de actividad** para ir a la pestaña siguiente.

    :::image type="content" source="./media/customize-entity-activities/create-new-activity.png" alt-text="Captura de pantalla: Creación de una actividad":::

### <a name="activity-configuration-tab"></a>Pestaña Configuración de actividad

#### <a name="writing-the-activity-query"></a>Escritura de la consulta de actividad

Aquí escribirá o pegará la consulta KQL que se usará para detectar la actividad de la entidad elegida y determinar cómo se representará en la escala de tiempo.

Para correlacionar eventos y detectar la actividad personalizada, KQL requiere una entrada de varios parámetros, dependiendo del tipo de entidad. Los parámetros son los distintos identificadores de la entidad en cuestión.

Es mejor seleccionar un identificador sólido para tener una asignación de uno a uno entre los resultados de la consulta y la entidad. La selección de un identificador débil puede producir resultados inexactos. [Obtenga más información sobre las entidades y la diferencia entre identificadores sólidos y débiles.](entities-in-azure-sentinel.md)

En la tabla siguiente se proporciona información acerca de los identificadores de las entidades.

**Identificadores sólidos para entidades de cuenta y host**

En una consulta se requiere al menos un identificador.

| Entidad | Identificador | Descripción |
| - | - | - |
| **Cuenta** | Account_Sid | SID local de la cuenta en Active Directory |
| | Account_AadUserId | Identificador de objeto de Azure AD del usuario en Azure Active Directory |
| | Account_Name + Account_NTDomain | Similar a SamAccountName (ejemplo: Contoso\Joe) |
| | Account_Name + Account_UPNSuffix | Similar a UserPrincipalName (ejemplo: Joe@Contoso.com) |
| **Host** | Host_HostName + Host_NTDomain | Similar al nombre de dominio completo (FQDN) |
| | Host_HostName + Host_DnsDomain | Similar al nombre de dominio completo (FQDN) |
| | Host_NetBiosName + Host_NTDomain | Similar al nombre de dominio completo (FQDN) |
| | Host_NetBiosName + Host_DnsDomain | Similar al nombre de dominio completo (FQDN) |
| | Host_AzureID | Id. de objeto de Azure AD del host en Azure Active Directory (si está unido a un dominio de AAD) |
| | Host_OMSAgentID | Identificador de agente de OMS del agente instalado en un host específico (único por host) |
|

En función de la entidad seleccionada, verá los identificadores disponibles. Al hacer clic en los identificadores pertinentes, se pegará el identificador en la consulta, en la ubicación del cursor.

> [!NOTE]
> - La consulta puede contener **hasta 10 campos**, por lo que debe proyectar los campos que desea.
>
> - Los campos proyectados deben incluir el campo **TimeGenerated**, a fin de colocar la actividad detectada en la escala de tiempo de la entidad.

```kusto
SecurityEvent
| where EventID == "4728"
| where (SubjectUserSid == '{{Account_Sid}}' ) or (SubjectUserName == '{{Account_Name}}' and SubjectDomainName == '{{Account_NTDomain}}' )
| project TimeGenerated, SubjectUserName, MemberName, MemberSid, GroupName=TargetUserName
```

:::image type="content" source="./media/customize-entity-activities/new-activity-query.png" alt-text="Captura de pantalla: Especificación de una consulta para detectar la actividad":::

#### <a name="presenting-the-activity-in-the-timeline"></a>Presentación de la actividad en la escala de tiempo

Para mayor comodidad, puede que desee determinar cómo se presenta la actividad en la escala de tiempo al agregar parámetros dinámicos a la salida de la actividad.

Azure Sentinel proporciona parámetros integrados que puede usar y también puede usar otros en función de los campos proyectados en la consulta.

Use el formato siguiente para los parámetros: `{{ParameterName}}`

Una vez que la consulta de actividad supera la validación y muestra el vínculo **Ver resultados de la consulta** debajo de la ventana de consulta, podrá expandir la sección **Valores disponibles** para ver los parámetros disponibles para usar al crear un título de actividad dinámica.

Seleccione el icono **Copiar** que se encuentra junto a un parámetro específico a fin de copiar ese parámetro en el Portapapeles para que pueda pegarlo en el campo **Título de actividad** anterior.

Agregue cualquiera de estos parámetros a la consulta:

- Cualquier campo proyectado en la consulta.

- Identificadores de entidad de cualquiera de las entidades mencionadas en la consulta.

- `StartTimeUTC`, para agregar la hora de inicio de la actividad en hora UTC.

- `EndTimeUTC`, para agregar la hora de finalización de la actividad en hora UTC.

- `Count`, para resumir varias salidas de consultas de KQL en una salida única.

    El parámetro `count` agrega los comandos siguientes a la consulta en segundo plano, aunque no se muestre completamente en el editor:

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>
    ```

    A continuación, cuando se usa el filtro **Tamaño de cubo** en las páginas de entidad, también se agrega el comando siguiente a la consulta que se ejecuta en segundo plano:

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>, bin (TimeGenerated, Bucket in Hours)
    ```

Por ejemplo:

:::image type="content" source="./media/customize-entity-activities/new-activity-title.png" alt-text="Captura de pantalla: Visualización de los valores disponibles para el título de la actividad":::

Cuando esté satisfecho con la consulta y el título de la actividad, seleccione **Siguiente: Revisar**.

### <a name="review-and-create-tab"></a>Pestaña Revisar y crear

1. Compruebe toda la información de configuración de la actividad personalizada.

1. Cuando aparezca el mensaje **Validación completada**, haga clic en **Crear** para crear la actividad. Puede editarlo o cambiarlo más adelante en la pestaña **Mis actividades**.

## <a name="manage-your-activities"></a>Administración de las actividades 

Administre las actividades personalizadas desde la pestaña **Mis actividades**. Haga clic en los puntos suspensivos (...) al final de la fila de una actividad para:

- Editar la actividad.
- Duplicar la actividad para crear una nueva, ligeramente diferente.
- Eliminar la actividad.
- Deshabilitar la actividad (sin eliminarla).

## <a name="view-activities-in-an-entity-page"></a>Ver las actividades en una página de entidad.

Cada vez que escriba una página de entidad, se ejecutarán todas las consultas de actividad habilitadas para esa entidad, lo que le proporciona información actualizada en la escala de tiempo de la entidad. Verá las actividades en la escala de tiempo, junto con alertas y marcadores. 

Puede usar el filtro **Contenido de la escala de tiempo** para presentar solo actividades (o cualquier combinación de actividades, alertas y marcadores).  

También puede usar el filtro **Actividades** para presentar u ocultar actividades específicas. 

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a crear actividades personalizadas para las escalas de tiempo de la página de su entidad. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Obtenga la imagen completa sobre las [páginas de entidad](identify-threats-with-entity-behavior-analytics.md).
- Consulte la lista completa de [entidades e identificadores](entities-reference.md).
