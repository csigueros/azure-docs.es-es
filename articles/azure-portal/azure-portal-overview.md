---
title: Introducción a Azure Portal
description: Azure Portal es una interfaz gráfica de usuario que puede usarse para administrar los servicios de Azure. Aprenda a navegar y buscar recursos en Azure Portal.
keywords: portal
ms.date: 08/30/2021
ms.topic: overview
ms.openlocfilehash: 32ca1f0032ab5ced2157bb73ccf5c3f40df86707
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224524"
---
# <a name="azure-portal-overview"></a>Introducción a Azure Portal

En este artículo se presenta Azure Portal, se identifican elementos de la página del portal y se le ayuda a familiarizarse con la experiencia de administración de Azure Portal.

## <a name="what-is-the-azure-portal"></a>¿Qué es Azure Portal?

Azure Portal es una consola unificada basada en web que proporciona una alternativa a las herramientas de línea de comandos. Con Azure Portal, puede administrar su suscripción a Azure mediante una interfaz gráfica de usuario. Puede compilar, administrar y supervisar todo, desde aplicaciones web sencillas hasta complejas implementaciones en la nube. Cree paneles personalizados para una vista organizada de recursos. Configure opciones de accesibilidad para una experiencia óptima.

Azure Portal está diseñado para proporcionar resistencia y disponibilidad continua. Tiene una presencia en cada centro de datos de Azure. Esta configuración hace que Azure Portal sea resistente a errores individuales de centros de datos y evita que la red se ralentice al estar cerca de los usuarios. Azure Portal no deja de actualizarse y no requiere tiempo de inactividad para las actividades de mantenimiento.

## <a name="azure-portal-menu"></a>Menú de Azure Portal

Puede [elegir el modo predeterminado del menú del portal](set-preferences.md#set-menu-behavior). Se puede acoplar o puede actuar como un panel flotante.

Cuando el menú del portal está en modo flotante, se oculta hasta que se necesita. Seleccione el icono de menú para abrir o cerrar el menú.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png" alt-text="Captura de pantalla del menú de Azure Portal en modo de control flotante.":::

Si selecciona el modo acoplado para el menú del portal, siempre está visible. Puede contraer el menú para proporcionar más espacio de trabajo.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png" alt-text="Captura de pantalla del menú de Azure Portal en modo acoplado.":::

## <a name="azure-home"></a>Inicio de Azure

Como nuevo suscriptor a servicios de Azure, lo primero que ve nada más [iniciar sesión en el portal](https://portal.azure.com) es **Inicio de Azure**. En esta página se compilan recursos que le ayudan a sacar el máximo partido a su suscripción a Azure. Se incluyen vínculos a cursos en línea gratuitos, documentación, servicios principales y sitios útiles para mantenerse al día y administrar el cambio de su organización. Para obtener acceso de forma rápida y sencilla al trabajo en curso, también mostramos una lista de sus últimos recursos visitados.

No puede personalizar la página principal, pero puede elegir si desea ver **Inicio** o **Panel** como la vista predeterminada. Al iniciar sesión por primera vez, aparece un aviso en la parte superior de la página donde puede guardar su preferencia. Puede [cambiar la selección de la página de inicio en cualquier momento en **Configuración del portal**](set-preferences.md#startup-page).

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png" alt-text="Captura de pantalla que muestra las opciones de la página de inicio en Azure Portal.":::

## <a name="azure-dashboard"></a>Panel de Azure

Los paneles proporcionan una vista enfocada de los recursos de su suscripción que más le importan. Le hemos facilitado un panel predeterminado para empezar. Puede personalizar este panel para reunir los recursos que más usa en una sola vista. Los cambios que realice en la vista predeterminada solo afectarán a su experiencia. Sin embargo, puede crear paneles adicionales para su propio uso o publicar paneles personalizados y compartirlos con otros usuarios de su organización. Para obtener más información, consulte [Creación y uso compartido de paneles en Azure Portal](../azure-portal/azure-portal-dashboards.md).

Como se indicó anteriormente, puede [establecer la página de inicio en Panel](set-preferences.md#startup-page) si desea ver el [panel](azure-portal-dashboards.md) usado más recientemente al iniciar sesión en Azure Portal.

## <a name="getting-around-the-portal"></a>Desplazamiento por el portal

Es útil para entender el diseño básico del portal y cómo interactuar con él. Aquí, presentaremos los componentes de la interfaz de usuario y parte de la terminología que empleamos para proporcionar instrucciones. Para un recorrido más detallado por el portal, consulte la lección del curso [Desplazarse por el portal](/learn/modules/tour-azure-portal/3-navigate-the-portal).

El menú de Azure Portal y el encabezado de página son elementos globales que están siempre presentes. Estas características persistentes son el "armazón" de la interfaz de usuario asociada a cada característica o servicio individual y el encabezado proporciona acceso a los controles globales. La página de configuración (a veces denominada "hoja") de un recurso también puede tener un menú para ayudarle a moverse entre características.

En la siguiente ilustración se etiquetan los elementos básicos de Azure Portal, cada uno de los cuales se describe en la siguiente tabla. En este ejemplo, el foco actual es una máquina virtual, pero los mismos elementos se aplican independientemente del tipo de recurso o servicio con el que trabaje.

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-callouts.png" alt-text="Captura de pantalla que muestra la vista del portal en pantalla completa y una clave para los elementos de interfaz de usuario.":::

:::image type="content" source="media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png" alt-text="Captura de pantalla que muestra el menú del portal expandido y una clave para los elementos de la interfaz de usuario.":::

|Key|Descripción
|:---:|---|
|1|Encabezado de página. Aparece en la parte superior de todas las páginas del portal e incluye elementos globales.|
|2|Búsqueda global. Use la barra de búsqueda para encontrar rápidamente un recurso específico, un servicio o documentación.|
|3|Controles globales. Como todos los elementos globales, estas características persisten en todo el portal e incluyen: Cloud Shell, filtro de suscripciones, notificaciones, configuración del portal, ayuda y soporte técnico y envíenos comentarios.|
|4|Su cuenta. Vea información sobre su cuenta, cambie directorios, cierre sesión o inicie sesión con otra cuenta.|
|5|Menú de Azure Portal. Este elemento global puede ayudarle a navegar entre servicios. A veces se hace referencia como barra lateral. (Los elementos 9 y 10 de esta lista aparecen en este menú).|
|6|Menú de recursos. Muchos servicios incluyen un menú de recursos que ayuda a administrar el servicio. Es posible que vea referencias a este elemento como panel izquierdo. Aquí verá comandos contextuales para el foco actual.|
|7|Barra de comandos. Los controles son contextuales para el enfoque actual.|
|8|Panel de trabajo. Muestra los detalles sobre el recurso que se encuentra actualmente en el foco.|
|9|Ruta de navegación. Puede usar los vínculos de la ruta de navegación para retroceder un nivel en su flujo de trabajo.|
|10|Control maestro para crear un nuevo recurso en la suscripción actual. Expanda o abra el menú de Azure Portal para buscar **+ Crear un recurso**. También puede seleccionar esa opción en la página **Inicio**. A continuación, busque o examine Azure Marketplace para el tipo de recurso que desea crear.|
|11|La lista de favoritos del menú de Azure Portal. Para aprender a personalizar la lista, vea [Adición, eliminación y ordenación de favoritos](../azure-portal/azure-portal-add-remove-sort-favorites.md).|

## <a name="get-started-with-services"></a>Introducción a los servicios

Si es un suscriptor nuevo, tendrá que crear un recurso antes de que haya algo para administrar. Seleccione **+ Crear un recurso** para ver los servicios disponibles en Azure Marketplace. Aquí encontrará cientos de aplicaciones y servicios de varios proveedores, todos certificados para ejecutarse en Azure.

Se han rellenado previamente sus [Favoritos](../azure-portal/azure-portal-add-remove-sort-favorites.md) en la barra lateral con vínculos a servicios usados con frecuencia.  Para ver todos los servicios disponibles, seleccione **Todos los servicios** en la barra lateral.

> [!TIP]
> La forma más rápida de buscar un recurso, servicio o documentación es usar *Search* en el encabezado global.

Vea este vídeo para una demostración del uso de la búsqueda global en Azure Portal.

> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[How to use global search in the Azure portal](https://www.youtube.com/watch?v=nZ7WwTZcQbo) (La búsqueda global en Azure Portal)

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre dónde ejecutar Azure Portal en [Exploradores y dispositivos compatibles](../azure-portal/azure-portal-supported-browsers-devices.md).
* Permanezca conectado en los desplazamientos con [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/).
* Incorpórese y configure el entorno en la nube con el [Centro de inicio rápido de Azure](../azure-portal/azure-portal-quickstart-center.md).