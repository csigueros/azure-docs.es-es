---
title: Supervisión de Key Vault con información de Key Vault | Microsoft Docs
description: En este artículo se describe la información de Key Vault.
services: azure-monitor
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 09/10/2020
ms.openlocfilehash: 953d6300b8ac19ddfcfe3b37a1cfde50480e19a4
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110095129"
---
# <a name="monitoring-your-key-vault-service-with-key-vault-insights"></a>Supervisión de un servicio de Key Vault con información de Key Vault
La información de Key Vault proporciona una supervisión completa de los almacenes de claves y ofrece una vista unificada del rendimiento, los errores, la latencia y las solicitudes de Key Vault.
Este artículo le ayudará a entender cómo se incorpora y se personaliza la experiencia de la información de Key Vault.

## <a name="introduction-to-key-vault-insights"></a>Introduction a la información de Key Vault

Antes de saltar a la experiencia, debe entender cómo se presenta y se visualiza la información.
-    **Perspectiva a gran escala**, mostrando una vista de instantánea del rendimiento en función de las solicitudes, el desglose de errores y una visión general de las operaciones y la latencia.
-   **Análisis en profundidad** de un almacén de claves determinado para realizar un análisis detallado.
-    **Personalizable**, que le permite cambiar las métricas que desea ver y modificar, o establecer umbrales en consonancia con sus límites y guardar su propio libro. Los gráficos del libro se pueden anclar a los paneles de Azure.

La información de Key Vault combina registros y métricas para proporcionar una solución de supervisión global. Todos los usuarios pueden acceder a los datos de supervisión basados en métricas; sin embargo, la inclusión de visualizaciones basadas en registros puede requerir que los usuarios [habiliten el registro de su instancia de Azure Key Vault](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Vista desde Azure Monitor

En Azure Monitor, puede ver los detalles de solicitud, latencia y errores de varios almacenes de claves de su suscripción, así como ayudar a identificar problemas de rendimiento y escenarios de limitación.

Para ver el uso y las operaciones de los almacenes de claves en todas las suscripciones, realice los pasos siguientes:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

2. Seleccione **Supervisar** en el panel izquierdo de Azure Portal y, en la sección Insights, seleccione **Almacenes de claves**.

![Captura de pantalla de la experiencia de información general con varios gráficos](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Libro de información general

En el libro Información general de la suscripción seleccionada, la tabla muestra las métricas de almacén de claves interactivas para almacenes de claves agrupados dentro de la suscripción. Puede filtrar los resultados en función de las opciones que seleccione en las siguientes listas desplegables:

* Suscripciones: solo se muestran las suscripciones que tienen almacenes de clave.

* Almacenes de claves: de forma predeterminada, solo se preseleccionan cinco almacenes de claves como máximo. Si selecciona todos los almacenes de claves o varios de ellos en el selector de ámbito, se devolverán como máximo doscientos almacenes de claves. Por ejemplo, si tenía en total 573 almacenes de claves entre tres suscripciones que ha seleccionado, solo se mostrarán doscientos.

* Intervalo de tiempo: de forma predeterminada, muestra las últimas veinticuatro horas de información en función de las selecciones correspondientes realizadas.

El icono de contador, que se encuentra bajo la lista desplegable, acumula el número total de almacenes de claves de la suscripción seleccionada y refleja cuántos se seleccionan. Hay mapas térmicos de código de colores condicionales para las columnas del libro que notifican las métricas de solicitud, error y latencia. El color más profundo tiene el valor más alto y un color más claro indica los valores más bajos.

## <a name="failures-workbook"></a>Libro de errores

Seleccione **Errores** en la parte superior de la página; se abrirá la pestaña Errores. Muestra el número total de visitas de la API y la frecuencia en el tiempo, junto con la cantidad de ciertos códigos de respuesta.

![Captura de pantalla del libro de errores](./media/key-vaults-insights-overview/failures.png)

La codificación de color condicional o los mapas térmicos de las columnas del libro notifican las métricas del número total de visitas de la API con un valor azul. El color más profundo tiene el valor más alto y un color más claro indica los valores más bajos.

El libro muestra Correctos (códigos de estado 2xx), Authentication Errors (Errores de autenticación) (códigos de estado 401/403), Limitación (códigos de estado 429) y Other Failures (Otros errores) (códigos de estado 4xx).

Para comprender mejor lo que representan cada uno de los códigos de estado, se recomienda leer la documentación sobre los [códigos de estado y respuesta de Azure Key Vault](../../key-vault/general/authentication-requests-and-responses.md).

## <a name="view-from-a-key-vault-resource"></a>Vista de un recurso de Key Vault

Para acceder a la información de Key Vault directamente desde un almacén de claves:

1. En Azure Portal, seleccione Almacenes de claves.

2. En la lista, elija un almacén de claves. En la sección de supervisión, seleccione Insights.

También se puede acceder a estas vistas seleccionando el nombre del recurso de un almacén de claves en el libro de nivel de Azure Monitor.

![Captura de pantalla de vista de un recurso de almacén de claves](./media/key-vaults-insights-overview/key-vault-resource-view.png)

En el libro **Información general** del almacén de claves, se muestran varias métricas de rendimiento que le ayudan a evaluar rápidamente:

- Los gráficos de rendimiento interactivos que muestran los detalles más esenciales relacionados con la disponibilidad, las transacciones y la latencia del almacén de claves.

- Los iconos de métricas y estado que resaltan la disponibilidad del servicio, el recuento total de transacciones con el recurso del almacén de claves la latencia global.

Al seleccionar cualquiera de las demás pestañas para **Errores** u **Operaciones** se abren los libros correspondientes.

![Captura de pantalla de la vista de errores](./media/key-vaults-insights-overview/resource-failures.png)

El libro de errores desglosa los resultados de todas las solicitudes del almacén de claves en el período de tiempo seleccionado y proporciona categorización en Correctos (2xx), Authentication Errors (Errores de autenticación) (401/403), Limitación (429) y Other Failures (Otros errores).

![Captura de pantalla de la vista de operaciones](./media/key-vaults-insights-overview/operations.png)

El libro Operaciones permite a los usuarios profundizar en los detalles completos de todas las transacciones, que se pueden filtrar por el estado del resultado mediante los iconos de nivel superior.

![Captura de pantalla en la que se muestra el libro de operaciones que contiene todos los detalles de todas las transacciones.](./media/key-vaults-insights-overview/info.png)

Los usuarios también pueden ver las vistas en función de tipos de transacciones específicos en la tabla superior, que actualiza dinámicamente la tabla inferior, donde los usuarios pueden ver los detalles de las operaciones completas en un panel de contexto emergente.

>[!NOTE]
> Tenga en cuenta que los usuarios deben tener habilitada la configuración de diagnóstico para ver este libro. Para obtener más información sobre cómo habilitar la configuración de diagnóstico, lea [Registro de Azure Key Vault](../../key-vault/general/logging.md).

## <a name="pin-and-export"></a>Anclar y exportar

Cualquiera de las secciones de métricas se puede anclar a un panel de Azure; para ello, seleccione el icono de chincheta que se encuentra en la parte superior derecha de la sección.

Los libros de información general o errores de varias suscripciones y almacenes de claves admiten la exportación de los resultados en formato Excel; para ello, seleccione el icono de descarga situado a la izquierda del icono de chincheta.

![Captura de pantalla del icono de chincheta seleccionado](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-key-vault-insights"></a>Personalización de la información de Key Vault

En esta sección se resaltan los escenarios comunes para editar el libro y personalizarlo de acuerdo con sus necesidades de análisis de datos:
*  Definir el ámbito del libro para seleccionar siempre una suscripción o unos almacenes de claves determinados
* Cambiar las métricas de la cuadrícula
* Cambiar el umbral de solicitudes
* Cambiar la representación de color

Puede iniciar las personalizaciones habilitando el modo de edición; para ello, seleccione el botón **Personalizar** de la barra de herramientas superior.

![Captura de pantalla del botón Personalizar](./media/key-vaults-insights-overview/customize.png)

Las personalizaciones se guardan en un libro personalizado para evitar sobrescribir la configuración predeterminada del libro publicado. Los libros se guardan en un recurso compartido, bien en la sección Mis informes, que es privada para usted, o en la sección Informes compartidos, a la que puede acceder cualquiera con acceso al grupo de recursos. Después de guardar el libro personalizado, debe ir a la galería de libros para iniciarlo.

![Captura de pantalla de la galería de libros](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Especificación de una suscripción o un almacén de claves

Puede configurar los libros Información general o Errores de varias suscripciones y almacenes de claves para definir el ámbito a una o varias suscripciones o almacenes de claves en concreto en cada ejecución. Para ello, realice los siguientes pasos:

1. Seleccione **Supervisar** en el portal y luego **Almacenes de claves** en el panel de la izquierda.
2. En el libro **Overview** (Información general), elija **Edit** (Editar) en la barra de comandos.
3. En la lista desplegable **Suscripciones**, seleccione una o varias suscripciones que quiera que sean las predeterminadas. Recuerde que el libro admite la selección de hasta 10 suscripciones.
4. En la lista desplegable **Almacenes de claves**, seleccione una o varias cuentas que quiera usar como las predeterminadas. Recuerde que el libro admite la selección de hasta 200 cuentas de almacenamiento.
5. Seleccione **Save as** (Guardar como) en la barra de comandos para guardar una copia del libro con las personalizaciones y, luego, haga clic en **Done editing** (Edición finalizada) para regresar el modo de lectura.

## <a name="troubleshooting"></a>Solución de problemas

Para obtener instrucciones generales para la solución de problemas, consulte el [artículo dedicado de solución de problemas](troubleshoot-workbooks.md) de conclusiones basadas en libros.

Esta sección le ayudará con el diagnóstico y la solución de algunos de los problemas habituales que pueden producirse al usar información de Key Vault. Utilice la siguiente lista para buscar la información relacionada con el problema específico.

### <a name="resolving-performance-issues-or-failures"></a>Resolución de problemas de rendimiento o errores

Para ayudarle a solucionar cualquier problema relacionado con el almacén de claves que detecte en la información de Key Vault, consulte la [documentación de Azure Key Vault](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>¿Por qué solo puedo ver doscientos almacenes de claves?

Hay un límite de doscientos almacenes de claves que se pueden seleccionar y ver. Independientemente del número de suscripciones seleccionadas, el número de almacenes de claves seleccionados tiene un límite de doscientos.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>¿Por qué veo todas mis suscripciones en el selector de suscripciones?

Solo se muestran las suscripciones que contienen almacenes de claves, que se eligen desde el filtro de suscripción seleccionado, que se seleccionan en la sección "Directory + Subscription" (Directorio + Suscripción) en el encabezado de Azure Portal.

![Captura de pantalla del filtro de suscripción](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Quiero realizar cambios o agregar visualizaciones adicionales a las conclusiones de Key Vault, ¿cómo lo hago?

Para realizar cambios, seleccione "Modo de edición" para modificar el libro y después puede guardar el trabajo como un libro nuevo que esté asociado a una suscripción y un grupo de recursos designados.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>¿Cuál es el intervalo de agregación una vez que se ancla cualquier parte de los libros?

Se usa el intervalo de agregación "automático", por lo que depende del intervalo de agregación seleccionado.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>¿Cuál es el intervalo de agregación cuando se ancla cualquier parte del libro?

El intervalo de agregación dependerá de la configuración del panel.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>¿Qué ocurre si deseo ver otros datos o crear mis propias visualizaciones? ¿Cómo puedo realizar cambios en las conclusiones de Key Vault?

Puede editar el libro existente mediante el uso del modo de edición y luego guardar el trabajo como un libro nuevo que tendrá todos los cambios nuevos.

## <a name="next-steps"></a>Pasos siguientes

Conozca los escenarios para los que están concebidos los libros, cómo crear informes y personalizar los ya existentes y otros muchos temas en el artículo [Crear informes interactivos con libros de Azure Monitor](../visualize/workbooks-overview.md).
