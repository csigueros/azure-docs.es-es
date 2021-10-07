---
title: Uso de Azure Purview Studio
description: En este artículo se describe cómo utilizar Azure Purview Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 2e3bcd7e51226f437f42f03fa43b144bee434f33
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456145"
---
# <a name="use-purview-studio"></a>Uso de Purview Studio

En este artículo se proporciona información general sobre algunas de las características principales de Azure Purview.

## <a name="prerequisites"></a>Requisitos previos

* Ya se ha creado una cuenta de Purview activa en Azure Portal y el usuario tiene permisos de acceso a [Purview Studio](https://web.purview.azure.com/resource/).

## <a name="launch-purview-account"></a>Inicio de la cuenta de Purview

* Para iniciar su cuenta de Purview, vaya a Purview accounts (Cuentas de Purview) en Azure Portal, seleccione la cuenta que desea iniciar e iníciela.

  :::image type="content" source="./media/use-purview-studio/open-purview-studio.png" alt-text="Captura de pantalla de la ventana de Purview en Azure Portal, con el botón Purview Studio resaltado." border="true":::

* Otra manera de iniciar la cuenta de Purview es ir a `https://web.purview.azure.com`, seleccionar **Azure Active Directory** y un nombre de cuenta para iniciarla.

## <a name="home-page"></a>Página de inicio

**Página principal** es la página de inicio del cliente de Azure Purview.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Captura de pantalla de la página principal.":::

En la lista siguiente se resumen las principales características de la **página principal**. Cada número de la lista corresponde a un número resaltado en la captura de pantalla anterior.

1. Nombre descriptivo del catálogo. Puede establecer el nombre del catálogo en **Administración** > **Información de la cuenta**.

2. El análisis de catálogo muestra el número de:

   * Orígenes de datos
   * Recursos
   * Términos del glosario

3. El cuadro de búsqueda permite buscar recursos en el catálogo de datos.

4. Los botones de acceso rápido proporcionan acceso a las funciones de la aplicación que se usan con frecuencia. Los botones que se presentan dependen del rol asignado a su cuenta de usuario en la colección raíz.

   * En el caso del *administrador de la colección*, el botón disponible es **Centro de conocimientos**.
   * En el caso de *conservador de datos*, los botones son **Examinar los activos**, **Manage glossary** (Administrar glosario) y **Centro de conocimientos**.
   * En el caso de *lector de datos*, los botones son **Examinar los activos**, **View glossary** (Ver glosario) y **Centro de conocimientos**.
   * En el caso de *administrador de orígenes de datos* + *conservador de datos*, los botones son **Examinar los activos**, **Manage glossary** (Administrar glosario) y **Centro de conocimientos**.
   * En el caso de *administrador de orígenes de datos* + *conservador de datos*, los botones son **Examinar los activos**, **Manage glossary** (Administrar glosario) y **Centro de conocimientos**.
  
   > [!NOTE]
   > Para más información sobre los roles de Azure Purview, consulte [Control de acceso en Azure Purview](catalog-permissions.md).

5. La barra de navegación izquierda ayuda a localizar las páginas principales de la aplicación.   
6. En la pestaña **Recently accessed** (Accedido recientemente) se muestra una lista de los recursos de datos a los que se ha accedido recientemente. Para información sobre el acceso a los recursos, consulte [Búsqueda en el catálogo de datos](how-to-search-catalog.md) y [Examen por tipo de recurso](how-to-browse-catalog.md).  La pestaña **My items** (Mis elementos) es una lista de los recursos de datos que pertenecen al usuario que ha iniciado sesión.
7. **Vínculos** contiene vínculos al estado de la región, la documentación, los precios, información general y el estado de Purview.
8. La barra de navegación superior contiene información sobre las secciones siguientes: notas de la versión, actualizaciones, cambio de cuenta de Purview, notificaciones, ayuda y comentarios.

## <a name="knowledge-center"></a>Centro de conocimientos

El Centro de conocimientos es donde puede encontrar todos los vídeos y tutoriales relacionados con Purview.

## <a name="guided-tours"></a>Visitas guiadas

Los UX de Azure Purview Studio tendrán visitas guiadas para ver la información general de la página. Para iniciar la visita guiada seleccione **Help** (Ayuda) en la barra superior y seleccione **Guided tour** (Recorrido guiado).

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Captura de pantalla de la visita guiada.":::

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Incorporación de una entidad de seguridad](tutorial-scan-data.md)
