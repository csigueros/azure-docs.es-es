---
title: Definición del control de acceso de usuarios globales
description: En las organizaciones grandes, los permisos de usuario pueden ser complejos y estar determinados por una estructura organizativa global, además de la estructura de la zona y del sitio estándar.
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020482"
---
# <a name="define-global-access-control"></a>Definición del control de acceso global

En las organizaciones grandes, los permisos de usuario pueden ser complejos y estar determinados por una estructura organizativa global, además de la estructura de la zona y del sitio estándar.

Para admitir la demanda de permisos de acceso de usuarios que son globales y más complejos, puede crear una topología empresarial global basada en unidades de negocio, regiones y sitios. A continuación, puede definir permisos de acceso de usuarios en torno a estas entidades.

Trabajar con las herramientas de acceso para la topología empresarial ayuda a las organizaciones a implementar estrategias de confianza cero al controlar mejor dónde los usuarios administran y analizan los dispositivos en la plataforma de Azure Defender para IoT.

## <a name="about-access-groups"></a>Acerca de los grupos de acceso

El control de acceso global se establece mediante la creación de grupos de acceso de usuarios. Los grupos de acceso se componen de reglas con respecto a qué usuarios pueden acceder a determinadas entidades empresariales. Trabajar con grupos permite controlar el acceso de vista y configuración a Defender para IoT para roles de usuario específicos en las unidades de negocio, las regiones y los sitios pertinentes.

Por ejemplo, permitir a los analistas de seguridad de un grupo de Active Directory acceder a todas las líneas de producción de automóviles y vidrio del Oeste de Europa, junto con una línea de plástico en una región.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagrama del grupo de analistas de seguridad en Active Directory":::

Antes de crear grupos de acceso, se recomienda lo siguiente:

- Configure detenidamente su topología empresarial. Para más información sobre la topología empresarial, vea [Uso de las vistas del mapa del sitio](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Planee qué usuarios estarán asociados a los grupos de acceso que cree. Hay dos opciones disponibles para asignar usuarios a los grupos de acceso:

  - **Asignar grupos de Active Directory**: compruebe que ha configurado una instancia de Active Directory para integrarla con la consola de administración local.
  
  - **Asignar usuarios locales**: compruebe que ha creado usuarios. Para más información, vea [Definición de usuarios](how-to-create-and-manage-users.md#define-users).

No se pueden asignar usuarios administradores a los grupos de acceso. Estos usuarios tienen acceso a todas las entidades de la topología empresarial de forma predeterminada.

## <a name="create-access-groups"></a>Creación de grupos de acceso

En esta sección se describe cómo crear grupos de acceso. Las unidades de negocio y las regiones globales predeterminadas se crean para el primer grupo que se crea. Puede editar las entidades predeterminadas al definir el primer grupo.

Para crear grupos:

1. Seleccione **Access Groups** (Grupos de acceso) en el menú lateral de la consola de administración local.

2. Seleccione :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. En el cuadro de diálogo **Add Access Group** (Agregar grupo de acceso), escriba un nombre para el grupo de acceso. La consola admite 64 caracteres. Asigne el nombre de forma que le resulte más fácil distinguir este grupo de otros.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Cuadro de diálogo para agregar un grupo de acceso donde se crean grupos de acceso":::

3. Si aparece la opción **Assign an Active Directory Group** (Asignar un grupo de Active Directory), puede asignar un grupo de usuarios de Active Directory a este grupo de acceso.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Opción para asignar un grupo de Active Directory en el cuadro de diálogo para crear un grupo de acceso":::

   Si la opción no aparece y desea incluir grupos de Active Directory en grupos de acceso, seleccione **Configuración del sistema**. En el panel **Integraciones**, defina los grupos. Escriba el nombre de un grupo exactamente como aparece en la configuración de Active Directory, y en minúscula.

5. En el panel **Usuarios**, asigne al grupo tantos usuarios como sea necesario. También puede asignar usuarios a grupos diferentes. Si trabaja de esta manera, debe crear y guardar el grupo de acceso y las reglas y, a continuación, asignar usuarios al grupo desde el panel **Usuarios**.

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Administrar roles de usuarios y asignarlos según proceda":::

6. Cree reglas en el cuadro de diálogo **Add Rules for *name*** (Agregar reglas para nombre) en función de los requisitos de acceso de la topología empresarial. Las opciones que aparecen aquí se basan en la topología que ha diseñado en las ventanas **Vista empresarial** y **Administración del sitio**. 

   Puede crear más de una regla por grupo. Es posible que tenga que crear más de una regla por grupo cuando esté trabajando con una granularidad de acceso compleja en varios sitios. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Agregar una regla al sistema":::

Las reglas que cree aparecerán en el cuadro de diálogo **Add Access Group** (Agregar grupo de acceso). Allí, puede eliminarlas o modificarlas.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Ver y editar todos los grupos de acceso desde esta ventana":::

### <a name="about-rules"></a>Acerca de las reglas

Cuando cree reglas, tenga en cuenta la información siguiente:

- Cuando un grupo de acceso contiene varias reglas, la lógica de la regla agrega todas las reglas. Por ejemplo, las reglas usan la lógica AND, y no la lógica OR.

- Para que una regla se aplique correctamente, debe asignar los sensores a las zonas en la ventana **Administración del sitio**.

- Solo puede asignar un elemento por regla. Por ejemplo, puede asignar una unidad de negocio, una región y un sitio para cada regla. Cree más reglas para el grupo si, por ejemplo, desea que los usuarios de un grupo de Active Directory tengan acceso a distintas unidades de negocio en regiones diferentes.

- Si cambia una entidad y el cambio afecta a la lógica de la regla, se eliminará la regla. Si los cambios realizados en una entidad de la topología afectan a la lógica de la regla, de modo que se eliminen todas las reglas, el grupo de acceso permanece, pero los usuarios no pueden iniciar sesión en la consola de administración local. Se pide a los usuarios que se pongan en contacto con su administrador para iniciar sesión.

- Si no hay ninguna unidad de negocio o región seleccionada, los usuarios tendrán acceso a todas las unidades de negocio y regiones definidas.

## <a name="see-also"></a>Consulte también

[Acerca de los usuarios de la consola de Defender para IoT](how-to-create-and-manage-users.md)
