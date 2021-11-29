---
title: Directiva de protección de la información de SQL en Microsoft Defender for Cloud
description: Aprenda a personalizar las directivas de protección de la información en Microsoft Defender for Cloud.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: defender-for-cloud
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 2300d038cdecd6b994775290e7ae1c3afa874aa3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557201"
---
# <a name="sql-information-protection-policy-in-microsoft-defender-for-cloud"></a>Directiva de protección de la información de SQL en Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

El [mecanismo de clasificación y detección de datos](../azure-sql/database/data-discovery-and-classification-overview.md) de SQL Information Protection proporciona funcionalidades avanzadas para detectar, clasificar, etiquetar e informar los datos confidenciales de las bases de datos. Se integra en [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md), [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) y [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

El mecanismo de clasificación se basa en los siguientes dos elementos:

- **Etiquetas**: Son los atributos principales de clasificación, que se usan para definir el *nivel de confidencialidad de los datos* almacenados en la columna. 
- **Tipos de información**: Proporciona una granularidad adicional en el *tipo de datos* almacenados en la columna.

Las opciones de directiva de protección de la información de Defender para la nube proporcionan un conjunto predefinido de etiquetas y tipos de información que sirven como valores predeterminados para el motor de clasificación. Puede personalizar la directiva según las necesidades de su organización, tal y como se describe a continuación.

:::image type="content" source="./media/sql-information-protection-policy/sql-information-protection-policy-page.png" alt-text="La página que muestra la directiva de SQL Information Protection.":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>¿Cómo acceso a la directiva de SQL Information Protection?

Hay tres maneras de acceder a la directiva de protección de la información:

- **(Recomendado)** Desde la página **Configuración del entorno** de Defender para la nube
- Desde la recomendación de seguridad "Los datos confidenciales de las bases de datos SQL deben clasificarse"
- Desde la página de detección de datos de Azure SQL Database

Cada una de ellas se muestra en la pestaña correspondiente a continuación.



### <a name="from-defender-for-clouds-settings"></a>[**Desde la configuración de Defender para la nube**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-defender-for-clouds-environment-settings-page"></a>Acceso a la directiva desde la página de configuración de Defender para la nube <a name="sqlip-tenant"></a>

En la página de **Configuración del entorno** de Defender para la nube, seleccione **Protección de la información de SQL**.

> [!NOTE]
> Esta opción solo aparece para los usuarios con permisos a nivel de inquilino. [Concesión de permisos de todo el inquilino a uno mismo](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).

:::image type="content" source="./media/sql-information-protection-policy/environment-settings-link-to-information-protection.png" alt-text="Acceso a la directiva de Information Protection de SQL desde la página de configuración del entorno de Microsoft Defender para la nube.":::



### <a name="from-defender-for-clouds-recommendation"></a>[**Desde la recomendación de Defender para la nube**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-defender-for-cloud-recommendation"></a>Acceso a la directiva desde la recomendación Defender para la nube <a name="sqlip-db"></a>

Use la recomendación de Defender para la nube, "Los datos confidenciales de las bases de datos SQL deben clasificarse", para ver la página de clasificación y detección de datos de la base de datos. Allí también verá las columnas detectadas que contienen información que le recomendamos clasificar.

1. En la página de **Recomendaciones** de Defender para la nube, busque la recomendación **Los datos confidenciales en sus bases de datos SQL deben clasificarse**.

    :::image type="content" source="./media/sql-information-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Búsqueda de la recomendación que da acceso a las directivas de SQL Information Protection.":::

1. Desde la página de detalles de la recomendación, seleccione una base de datos en las pestañas **correcto** o **incorrecto**.

1. Se abre la página **Clasificación y detección de datos**. Seleccione **Configurar**.

    :::image type="content" source="./media/sql-information-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Abrir la Directiva de protección de la información de SQL desde la recomendación pertinente en Microsoft Defender for Cloud":::



### <a name="from-azure-sql"></a>[**Desde Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Acceso a la directiva desde Azure SQL <a name="sqlip-azuresql"></a>

1. Desde Azure Portal, abra Azure SQL.

    :::image type="content" source="./media/sql-information-protection-policy/open-azure-sql.png" alt-text="Apertura de Azure SQL desde Azure Portal.":::

1. Seleccione cualquier base de datos.

1. En el área **Seguridad** del menú, abra la página **Clasificación y de detección de datos** (1) y seleccione **Configurar** (2).

    :::image type="content" source="./media/sql-information-protection-policy/access-policy-from-azure-sql.png" alt-text="Apertura de la directiva de SQL Information Protection desde Azure SQL.":::

--- 

## <a name="customize-your-information-types"></a>Personalización de los tipos de información

Para administrar y personalizar los tipos de información:

1. Seleccione **Administrar tipos de información**.

    :::image type="content" source="./media/sql-information-protection-policy/manage-types.png" alt-text="Administración de los tipos de información para la directiva de protección de la información.":::

1. Para agregar un nuevo tipo, seleccione **Crear tipo de información**. Puede configurar un nombre, una descripción y cadenas de patrón de búsqueda para el tipo de información. Las cadenas de patrón de búsqueda pueden usar opcionalmente palabras clave con caracteres comodín (con el carácter "%"), que el motor de detección automática usa para identificar datos confidenciales en las bases de datos, en función de los metadatos de las columnas.
 
    :::image type="content" source="./media/sql-information-protection-policy/configure-new-type.png" alt-text="Configuración de un nuevo tipo de información para la directiva de protección de la información.":::

1. También puede modificar los tipos integrados mediante la adición de cadenas de patrón de búsqueda adicionales, la deshabilitación de algunas de las cadenas existentes o el cambio de la descripción. 

    > [!TIP]
    > No puede eliminar tipos integrados ni cambiar sus nombres. 

1. Los **tipos de información** se enumeran en orden ascendente de detección, lo que significa que se intentará que los tipos que aparezcan más arriba en la lista coincidan antes. Para cambiar la clasificación entre los tipos de información, arrastre los tipos al lugar correcto para volver a ordenarlas en la tabla o usar los botones **Subir** y **Bajar** para cambiar el orden. 

1. Seleccione **Aceptar** cuando haya terminado.

1. Una vez completada la administración de los tipos de información, asegúrese de asociar los tipos pertinentes con las etiquetas pertinentes, haciendo clic en **Configurar** para una etiqueta determinada y agregando o eliminando tipos de información según corresponda.

1. Para aplicar los cambios, seleccione **Guardar** en la página principal **Etiquetas**.
 

## <a name="exporting-and-importing-a-policy"></a>Exportación e importación de una directiva

Puede descargar un archivo JSON con las etiquetas y los tipos de información definidos, editar el archivo en el editor de su elección y, a continuación, importar el archivo actualizado. 

:::image type="content" source="./media/sql-information-protection-policy/export-import.png" alt-text="Exportación e importación de la directiva de protección de la información.":::

> [!NOTE]
> Necesitará permisos de nivel de inquilino para importar un archivo de directiva. 


## <a name="permissions"></a>Permisos

Para personalizar la directiva de protección de la información de su inquilino de Azure, necesitará las siguientes acciones en el grupo de administración raíz del inquilino:
  - Microsoft.Security/informationProtectionPolicies/read
  - Microsoft.Security/informationProtectionPolicies/write 

Obtenga más información en [Concesión y solicitud de visibilidad para todos los inquilinos](tenant-wide-permissions-management.md).

## <a name="manage-sql-information-protection-using-azure-powershell"></a>Administración de la protección de la información de SQL mediante Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): Recupera la directiva de protección de la información de SQL del inquilino en vigor.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): Establece la directiva de protección de la información de SQL del inquilino en vigor.
 

## <a name="next-steps"></a>Pasos siguientes
 
En este artículo, ha aprendido a definir una directiva de protección de la información en Microsoft Defender for Cloud. Para obtener más información acerca de cómo usar SQL Information Protection para clasificar y proteger datos confidenciales en las bases de datos SQL, consulte [Clasificación y detección de datos de Azure SQL Database](../azure-sql/database/data-discovery-and-classification-overview.md).

Para obtener más información sobre las directivas de seguridad y la seguridad de datos en Defender para la nube, consulte los artículos siguientes:
 
- [Configuración de directivas de seguridad en Microsoft Defender for Cloud](tutorial-security-policy.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure
- [Seguridad de datos en Microsoft Defender for Cloud](data-security.md): obtenga información sobre cómo Defender for Cloud administra y protege los datos
