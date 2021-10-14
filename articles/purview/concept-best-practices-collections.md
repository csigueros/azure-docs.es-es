---
title: Arquitectura de las colecciones de Purview y procedimientos recomendados
description: En este artículo se proporcionan ejemplos de arquitecturas de las colecciones de Azure Purview y se explican los procedimientos recomendados.
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 838730453f5d49efd756abce40faec74513d52b2
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390337"
---
# <a name="azure-purview-collections-architectures-and-best-practices"></a>Arquitecturas de las colecciones de Azure Purview y procedimientos recomendados  

En el núcleo de Azure Purview, el mapa de datos es un componente de plataforma como servicio (PaaS) que mantiene un mapa actualizado de recursos y sus metadatos en el patrimonio de datos. Para hidratar el mapa de datos, debe registrar y examinar los orígenes de datos. En una organización puede haber miles de orígenes de datos administrados y controlados por equipos centralizados o descentralizados.  

Las [colecciones](./how-to-create-and-manage-collections.md) de Azure Purview permiten la asignación organizativa de metadatos. Mediante las colecciones, puede administrar y mantener orígenes de datos, exámenes y recursos en una jerarquía en lugar de en una estructura plana. Las colecciones permiten compilar un modelo jerárquico personalizado de la infraestructura de datos en función de cómo piense usar la organización Azure Purview para controlar la infraestructura.

Una colección también proporciona un límite de seguridad para los metadatos del mapa de datos. El acceso a colecciones, orígenes de datos y metadatos se configura y mantiene en función de la jerarquía de colecciones de Azure Purview, mediante un modelo de privilegios mínimos: 
- Los usuarios tienen el acceso mínimo que necesitan para realizar su trabajo. 
- Los usuarios no tienen acceso a datos confidenciales que no necesitan.

## <a name="intended-audience"></a>Destinatarios

- Equipo de arquitectura de datos 
- Equipos de administración y gobernanza de datos
- Equipo de seguridad de datos

## <a name="why-do-you-need-to-define-collections-and-an-authorization-model-for-your-azure-purview-account"></a>¿Por qué es necesario definir colecciones y un modelo de autorización para la cuenta de Azure Purview? 

Considere la posibilidad de implementar colecciones en Azure Purview para cumplir los siguientes requisitos: 

- Organizar orígenes de datos, distribuir recursos y realizar exámenes en función de los requisitos empresariales, la distribución geográfica de los datos y los equipos de administración de datos, los departamentos o las funciones empresariales. 

- Delegar la propiedad de los orígenes de datos y los recursos en los equipos correspondientes mediante la asignación de roles a las colecciones correspondientes. 

- Buscar y filtrar recursos por colecciones. 
 

## <a name="define-a-collection-hierarchy"></a>Definición de una jerarquía de colecciones  

### <a name="design-considerations"></a>Consideraciones de diseño  

- Cada cuenta de Purview se crea con una _colección raíz_ predeterminada. El nombre de la colección raíz es el mismo que el nombre de cuenta de Azure Purview. No se puede eliminar la colección raíz. Para cambiar el nombre descriptivo de la colección raíz, puede modificar el nombre descriptivo de la cuenta de Purview desde el Centro de administración de Purview.   

- Las colecciones pueden contener orígenes de datos, exámenes, recursos y asignaciones de roles.

- Una colección puede tener tantas colecciones secundarias como sea necesario, aunque cada colección solo puede tener una colección primaria. No se pueden implementar colecciones por encima de la colección raíz.

- Los orígenes de datos, los exámenes y los recursos solo pueden pertenecer a una colección. 

- Una jerarquía de colecciones de una instancia de Azure Purview puede admitir hasta 256 colecciones, con un máximo de ocho niveles de profundidad. Esto no incluye a la colección raíz. 

- Por diseño, no se pueden registrar orígenes de datos varias veces en una sola cuenta de Purview. Esta arquitectura ayuda a evitar el riesgo de asignar diferentes niveles de control de acceso a un único origen de datos. Si varios equipos consumen los metadatos de un único origen de datos, puede registrar y administrar el origen de datos en una colección primaria. Luego puede crear los exámenes correspondientes en cada subcolección para que los recursos relevantes aparezcan en cada colección secundaria.

- Los artefactos y las conexiones de linaje se asocian a la colección raíz aunque los orígenes de datos estén registrados en colecciones de nivel inferior.

- Cuando se realiza un nuevo examen, de manera predeterminada, este se implementa en la misma colección que el origen de datos. Opcionalmente, puede seleccionar otra subcolección para realizar el examen. Como resultado, los recursos van a pertenecer a la subcolección. 

- Actualmente no se permite mover orígenes de datos entre colecciones. Si necesita mover un origen de datos a otra colección, debe eliminar todos los recursos, quitar el origen de datos de la colección original y volver a registrarlo en la colección de destino.

- Se permite mover recursos entre colecciones si se concede al usuario el rol de conservador de datos en las colecciones de origen y destino. 

- Actualmente no se permiten ciertas operaciones, como mover y cambiar el nombre de una colección. 

- Puede eliminar una colección si no tiene recursos, exámenes asociados, orígenes de datos ni colecciones secundarias.

- Si existen en el mapa de datos de Azure Purview, los orígenes de datos, los exámenes y los recursos deben pertenecer a una colección.    

<!-- 
- Moving data sources across collections is allowed if the user is granted the Data Source Admin role for the source and destination collections. 

- Moving assets across collections is allowed if the user is granted the Data Curator role for the source and destination collections. 

-->

### <a name="design-recommendations"></a>Recomendaciones de diseño 

- Revise los [procedimientos recomendados de la cuenta de Azure Purview](./deployment-best-practices.md#determine-the-number-of-purview-instances) y defina el número adecuado de cuentas de Purview necesarias en la organización antes de planear la estructura de las colecciones.  

- Se recomienda diseñar la arquitectura de las colecciones en función de los requisitos de seguridad y la estructura de gobernanza y administración de datos de la organización. Revise los [arquetipos de colecciones](#collections-archetypes) recomendados en este artículo.

- Para la futura escalabilidad, se recomienda crear una colección de nivel superior para la organización debajo de la colección raíz. Asigne los roles pertinentes en la colección de nivel superior en lugar de en la colección raíz.  

- Considere la administración de seguridad y acceso como parte del proceso de toma de decisiones de diseño al compilar colecciones en Azure Purview. 

- Cada colección tiene un atributo de nombre y un atributo de nombre descriptivo. Si usa Azure [Purview Studio](https://web.purview.azure.com/resource/) para implementar una colección, el sistema asigna automáticamente un nombre aleatorio de seis letras a la colección para evitar la duplicación. Para reducir la complejidad, evite el uso de nombres descriptivos duplicados en las colecciones, especialmente en el mismo nivel.  

- Si puede, evite duplicar la estructura organizativa en una jerarquía de colecciones profundamente anidadas. Si no puede evitar hacerlo, asegúrese de usar nombres diferentes para cada colección de la jerarquía para que sean fáciles de distinguir.

- Automatice la implementación de colecciones mediante la API si piensa implementar colecciones y asignaciones de roles en masa.

- Use un nombre de entidad de seguridad de servicio (SPN) dedicado para ejecutar operaciones en las colecciones y la asignación de roles mediante la API. El uso de una SPN reduce el número de usuarios que tienen derechos elevados y sigue las instrucciones con privilegios mínimos.

## <a name="define-an-authorization-model"></a>Definición de un modelo de autorización

Los roles del plano de datos de Azure Purview se administran en Azure Purview. Después de implementar una cuenta de Purview, a su creador se le asignan automáticamente los siguientes roles en la colección raíz. Puede usar [Purview Studio](https://web.purview.azure.com/resource/) o un método de programación para asignar y administrar roles directamente en Azure Purview.

  - Los **administradores de colecciones** pueden editar las colecciones de Purview y sus detalles y agregar subcolecciones. También pueden agregar usuarios a otros roles de Purview en colecciones donde son administradores.
  - Los **administradores de orígenes de datos** pueden administrar orígenes de datos y exámenes de datos.
  - Los **conservadores de datos** pueden crear, leer, modificar y eliminar recursos de datos del catálogo y establecer relaciones entre recursos.
  - Los **lectores de datos** pueden acceder a los recursos de datos del catálogo, pero no modificarlos.

### <a name="design-considerations"></a>Consideraciones de diseño  

- La administración de acceso de Azure Purview se ha movido al plano de datos. Ya no se usan los roles de Azure Resource Manager, así que se debe usar Azure Purview para asignar roles. 

- En Azure Purview se pueden asignar roles a usuarios, grupos de seguridad y entidades de servicio (incluidas identidades administradas) desde Azure Active Directory (Azure AD) en el mismo inquilino de Azure AD donde está implementada la cuenta de Purview.
  
- Primero debe agregar cuentas de invitado al inquilino de Azure AD como usuarios B2B para poder asignar roles de Purview a usuarios externos. 

- De manera predeterminada, los administradores de colecciones no tienen acceso para leer ni modificar recursos, aunque pueden elevar su acceso y agregarse a más roles.

- De manera predeterminada, todas las colecciones secundarias heredan automáticamente todas las asignaciones de roles. Pero puede habilitar **Restringir permisos heredados** en cualquier colección, excepto la colección raíz. **Restringir permisos heredados** quita los roles heredados de todas las colecciones primarias, excepto el rol de administrador de colecciones. 

- Para la conexión de Azure Data Factory: para conectarse a Azure Data Factory debe ser administrador de colecciones en la colección raíz.

- Si necesita conectarse a Azure Data Factory para el linaje, conceda el rol de conservador de datos a la identidad administrada de la factoría de datos en el nivel de la colección raíz de Purview. Al conectar Data Factory a Purview en la interfaz de usuario de creación, Data Factory intenta agregar estas asignaciones de roles automáticamente. Si tiene el rol de administrador de colecciones en la colección raíz de Purview, esta operación se realiza correctamente. 

### <a name="design-recommendations"></a>Recomendaciones de diseño 

- Considere la posibilidad de implementar el [acceso de emergencia](/azure/active-directory/users-groups-roles/directory-emergency-access) o una estrategia de emergencia para el rol de administrador de colecciones en el nivel de colección raíz de Azure Purview para evitar bloqueos de nivel de cuenta de Purview. Documente el proceso para usar cuentas de emergencia. 

    > [!NOTE]
    > En determinados escenarios, es posible que tenga que usar una cuenta de emergencia para iniciar sesión en Azure Purview. Es posible que necesite este tipo de cuenta para corregir problemas de acceso de nivel de organización si nadie más puede iniciar sesión en Purview o si otros administradores no pueden realizar determinadas operaciones por problemas de autenticación corporativa. Se recomienda encarecidamente seguir los procedimientos recomendados de Microsoft relacionados con la implementación de [cuentas de acceso de emergencia](/azure/active-directory/users-groups-roles/directory-emergency-access) mediante usuarios solo en la nube.
    >
    > Siga las instrucciones de [este artículo](./concept-account-upgrade.md#what-happens-when-your-upgraded-account-doesnt-have-a-collection-admin) para recuperar el acceso a la colección raíz de Purview si el administrador de colecciones anterior no está disponible.

- Minimice el número de administradores de colecciones raíz. Asigne un máximo de tres usuarios administradores de colecciones en la colección raíz, incluidos el SPN y las cuentas de emergencia. Asigne los roles de administrador de colecciones a la colección de nivel superior o a subcolecciones en su lugar.

- Asigne roles a grupos en lugar de a usuarios individuales para reducir la sobrecarga administrativa y los errores de administración de roles individuales. 

- Asigne la entidad de servicio en la colección raíz con fines de automatización.

- Para aumentar la seguridad, habilite el acceso condicional de Azure AD con autenticación multifactor para, al menos, los administradores de colecciones, los administradores de orígenes de datos y los conservadores de datos. Asegúrese de que las cuentas de emergencia se excluyan de la directiva de acceso condicional.
 
## <a name="collections-archetypes"></a>Arquetipos de colecciones

Puede implementar la colección de Azure Purview en función de modelos de gobernanza y administración de datos centralizados, descentralizados o híbridos. Base esta decisión en los requisitos empresariales y de seguridad.

### <a name="example-1-single-region-organization"></a>Ejemplo 1: organización de una sola región 

Esta estructura es adecuada para organizaciones que: 
- Están situadas principalmente en una única ubicación geográfica. 
- Tienen un equipo de gobernanza y administración de datos centralizado donde el siguiente nivel de administración de datos recae en departamentos, equipos o proyectos.  

La jerarquía de colecciones consta de estos segmentos verticales: 

- Colección raíz (predeterminada)
- Contoso (colección de nivel superior)
- Departamentos (una colección delegada para cada departamento) 
- Equipos o proyectos (segregación adicional basada en proyectos)

Cada origen de datos se registra y examina en su colección correspondiente. Por lo tanto, los recursos también aparecen en la misma colección. 

Los orígenes de datos compartidos de nivel de organización se registran y examinan en la colección Hub-Shared. 

Los orígenes de datos compartidos de nivel de departamento se registran y examinan en las colecciones de departamento. 

:::image type="content" source="media/concept-best-practices/collections-example-1.png" alt-text="Captura de pantalla que muestra el primer ejemplo de colecciones de Azure Purview."lightbox="media/concept-best-practices/collections-example-1.png":::

### <a name="example-2-multiregion-organization"></a>Ejemplo 2: organización de varias regiones

Este escenario es útil para organizaciones: 
- Que tienen presencia en varias regiones. 
- Cuyo equipo de gobernanza de datos está centralizado o descentralizado en cada región.
- Cuyos equipos de administración de datos están distribuidos en cada ubicación geográfica. 

La jerarquía de colecciones consta de estos segmentos verticales: 

- Colección raíz (predeterminada)
- FourthCoffee (colección de nivel superior)
- Ubicaciones geográficas (colecciones de nivel medio basadas en las ubicaciones geográficas donde se encuentran los orígenes de datos y los propietarios de datos)
- Departamentos (una colección delegada para cada departamento) 
- Equipos o proyectos (segregación adicional basada en equipos o proyectos)

En este escenario cada región tiene una subcolección propia bajo la colección de nivel superior de la cuenta de Purview. Los orígenes de datos se registran y examinan en las subcolecciones correspondientes de sus propias ubicaciones geográficas. Por lo tanto, los recursos también aparecen en la jerarquía de subcolecciones de la región. 

Si tiene equipos centralizados de administración y gobernanza de datos, puede concederles acceso desde la colección de nivel superior. Así, obtienen información de todo el patrimonio de datos del mapa de datos. Opcionalmente, el equipo centralizado puede registrar y examinar cualquier origen de datos compartido.

Los equipos de gobernanza y administración de datos basados en regiones pueden obtener acceso desde sus colecciones correspondientes en un nivel inferior.

Los orígenes de datos compartidos de nivel de departamento se registran y examinan en las colecciones de departamento. 

:::image type="content" source="media/concept-best-practices/collections-example-2.png" alt-text="Captura de pantalla que muestra el segundo ejemplo de colecciones de Azure Purview."lightbox="media/concept-best-practices/collections-example-2.png":::

### <a name="example-3-multiregion-data-transformation"></a>Ejemplo 3: varias regiones, transformación de datos

Este escenario puede ser útil si se quiere distribuir la administración de acceso a metadatos en función de las ubicaciones geográficas y los estados de transformación de datos. Los científicos de datos y los ingenieros de datos que pueden transformar datos para que sean más significativos pueden administrar zonas Raw y Refine. Luego pueden mover los datos a zonas Produce o Curated.  

La jerarquía de colecciones consta de estos segmentos verticales: 

- Colección raíz (predeterminada)
- Fabrikam (colección de nivel superior)
- Ubicaciones geográficas (colecciones de nivel medio basadas en las ubicaciones geográficas donde se encuentran los orígenes de datos y los propietarios de datos)
- Fases de transformación de datos (Raw, Refine, Produce o Curated) 

Los científicos de datos y los ingenieros de datos pueden tener el rol de conservadores de datos en sus zonas correspondientes para poder conservar metadatos. El acceso de lector de datos a la zona Curated se puede conceder a roles de datos completos y usuarios empresariales. 

:::image type="content" source="media/concept-best-practices/collections-example-3.png" alt-text="Captura de pantalla que muestra el tercer ejemplo de colecciones de Azure Purview."lightbox="media/concept-best-practices/collections-example-3.png":::

### <a name="example-4-multiregion-business-functions"></a>Ejemplo 4: varias regiones, funciones empresariales 

Las organizaciones que necesitan organizar los metadatos y la administración de acceso de conformidad con las funciones empresariales pueden usar esta opción.

La jerarquía de colecciones consta de estos segmentos verticales: 

- Colección raíz (predeterminada)
- AdventureWorks (colección de nivel superior)
- Ubicaciones geográficas (colecciones de nivel medio basadas en las ubicaciones geográficas donde se encuentran los orígenes de datos y los propietarios de datos)
- Principales funciones empresariales o clientes (segregación adicional basada en funciones o clientes)

Cada región tiene una subcolección propia bajo la colección de nivel superior de la cuenta de Purview. Los orígenes de datos se registran y examinan en las subcolecciones correspondientes de sus propias ubicaciones geográficas. Por lo tanto, los recursos se agregan a la jerarquía de subcolecciones de la región. 

Si tiene equipos centralizados de administración y gobernanza de datos, puede concederles acceso desde la colección de nivel superior. Así, obtienen información de todo el patrimonio de datos del mapa de datos. Opcionalmente, el equipo centralizado puede registrar y examinar cualquier origen de datos compartido.

Los equipos de gobernanza y administración de datos basados en regiones pueden obtener acceso desde sus colecciones correspondientes en un nivel inferior.
Cada unidad de negocio tiene su propia subcolección.

:::image type="content" source="media/concept-best-practices/collections-example-4.png" alt-text="Captura de pantalla que muestra el cuarto ejemplo de colecciones de Azure Purview."lightbox="media/concept-best-practices/collections-example-4.png":::

## <a name="access-management-options"></a>Opciones de administración de acceso

Si quiere implementar la democratización de datos en una organización completa, asigne el rol de lector de datos en la colección de nivel superior a los usuarios empresariales, de gobernanza y de administración de datos. Asigne los roles de administrador de orígenes de datos y conservador de datos en los niveles de subcolección a los equipos de administración y gobernanza de datos correspondientes.

Si necesita restringir el acceso a la búsqueda y detección de metadatos en la organización, asigne los roles de lector de datos y conservador de datos en el nivel de colección específico. Por ejemplo, podría restringir a los empleados de los Estados Unidos para que solo puedan leer datos en el nivel de colección de Estados Unidos y no en la colección de LATAM. 

Puede aplicar una combinación de estos dos escenarios en el mapa de datos de Purview si se necesita la democratización de datos total con algunas excepciones para algunas colecciones. Puede asignar roles de Purview en la colección de nivel superior y restringir la herencia a las colecciones secundarias específicas.

Asigne el rol de administrador de colecciones al equipo de administración y seguridad de datos centralizado en la colección de nivel superior. Delegue la administración de colecciones adicional de colecciones de nivel inferior en los equipos correspondientes.

## <a name="next-steps"></a>Pasos siguientes
-  [Creación de una colección y asignación de permisos en Purview](./quickstart-create-collection.md)
-  [Creación y administración de colecciones en Azure Purview](./how-to-create-and-manage-collections.md)
-  [Control de acceso en Azure Purview](./catalog-permissions.md)
