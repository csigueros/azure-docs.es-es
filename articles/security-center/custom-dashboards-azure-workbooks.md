---
title: Galería de libros en Azure Security Center
description: Aprenda a crear informes completos e interactivos de los datos de Azure Security Center con la galería de Libros de Azure Monitor integrada.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: dda75aedab0c17f14d2725ff9759d7ab30203474
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712922"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Creación de informes completos e interactivos de los datos de Security Center

Los [libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) proporcionan un lienzo flexible para el análisis de datos y la creación de informes visuales completos en Azure Portal. Permiten acceder a varios orígenes de datos desde Azure y combinarlos en experiencias interactivas unificadas.

Los libros proporcionan un amplio conjunto de funcionalidades para visualizar los datos de Azure. Para obtener ejemplos detallados de cada tipo de visualización, consulte la [documentación y ejemplos de visualizaciones](../azure-monitor/visualize/workbooks-text-visualizations.md). 

En Azure Security Center, puede acceder a los libros integrados para realizar un seguimiento de la postura de seguridad de su organización. También puede crear libros personalizados para ver una amplia variedad de datos de Security Center u otros orígenes de datos admitidos.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Libro de puntuación de seguridad a lo largo del tiempo.":::

## <a name="availability"></a>Disponibilidad

| Aspecto                          | Detalles                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Estado de la versión:                  | Disponibilidad general (GA)                                                                                                                    |
| Precios:                        | Gratuito                                                                                                                                         |
| Roles y permisos necesarios: | Para guardar los libros, debe tener al menos permisos de [Colaborador de libros](../role-based-access-control/built-in-roles.md#workbook-contributor) en el grupo de recursos de destino. |
| Nubes:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nacionales o soberanas (Azure Government, Azure China 21Vianet) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Galería de libros en Azure Security Center

Con la funcionalidad integrada de los libros de Azure, Azure Security Center facilita la creación de sus propios libros personalizados e interactivos. Security Center también incluye una galería con los siguientes libros listos para su personalización:

- [Libro "Puntuación de seguridad a lo largo del tiempo"](#use-the-secure-score-over-time-workbook): realice el seguimiento de las puntuaciones de las suscripciones y de los cambios en las recomendaciones sobre sus recursos.
- [Libro "Actualizaciones del sistema"](#use-the-system-updates-workbook): vea las actualizaciones del sistema que faltan por recursos, sistema operativo, gravedad, etc.
- [Libro "Conclusiones de la evaluación de vulnerabilidad"](#use-the-vulnerability-assessment-findings-workbook): vea los resultados de los exámenes de vulnerabilidades de los recursos de Azure.
- [Libro "Cumplimiento a lo largo del tiempo"](#use-the-compliance-over-time-workbook): vea el estado de cumplimiento de una suscripción con los estándares normativos o del sector que ha seleccionado. 

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galería de libros integrados en Azure Security Center.":::

Elija uno de los libros proporcionados o cree uno propio.

> [!TIP]
> Use el botón **Editar** para personalizar a su gusto cualquiera de los libros proporcionados. Cuando haya terminado de editar, seleccione **Guardar**, y los cambios se guardarán en un nuevo libro.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Edición de los libros proporcionados para personalizarlos según sus necesidades particulares.":::

### <a name="use-the-secure-score-over-time-workbook"></a>Uso del libro "Puntuación de seguridad a lo largo del tiempo"

En este libro se usan los datos de la puntuación de seguridad del área de trabajo de Log Analytics. Esos datos se tienen que exportar desde la herramienta de exportación continua, como se describe en [Configuración de la exportación continua desde las páginas de Security Center en Azure Portal](continuous-export.md?tabs=azure-portal).

Al configurar la exportación continua, establezca la frecuencia de exportación en las **streaming updates** (actualizaciones de streaming) e **instantáneas**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="Para el libro de puntuación de seguridad a lo largo del tiempo, tendrá que seleccionar ambas opciones de la configuración de frecuencia de exportación en la configuración de exportación continua.":::

> [!NOTE]
> Las instantáneas se exportan semanalmente, por lo que tendrá que esperar al menos una semana para que se exporte la primera instantánea y poder ver los datos de este libro.

> [!TIP]
> Para configurar la exportación continua en toda la organización, use las directivas DeployIfNotExist de Azure Policy proporcionadas que se describen en [Configuración de la exportación continua a escala](continuous-export.md?tabs=azure-policy).

El libro de puntuación de seguridad a lo largo del tiempo tiene cinco gráficos para las suscripciones que informan a las áreas de trabajo seleccionadas:

|Grafo  |Ejemplo  |
|---------|---------|
|**Tendencias de puntuación de la última semana y el último mes**<br>Use esta sección para supervisar la puntuación actual y las tendencias generales de las puntuaciones de las suscripciones.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Tendencias para la puntuación de seguridad en el libro integrado.":::|
|**Puntuación agregada para todas las suscripciones seleccionadas**<br>Mantenga el puntero sobre cualquier punto de la línea de tendencia para ver la puntuación agregada en cualquier fecha del intervalo de tiempo seleccionado.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Puntuación agregada para todas las suscripciones seleccionadas.":::|
|**Recomendaciones con el mayor número de recursos incorrectos**<br>Esta tabla le ayuda a evaluar las recomendaciones para el mayor número de recursos cuyo estado ha cambiado a incorrecto durante el período seleccionado.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Recomendaciones con el mayor número de recursos incorrectos.":::|
|**Puntuaciones de controles de seguridad específicos**<br>Los controles de seguridad de Security Center son agrupaciones lógicas de recomendaciones. En este gráfico se muestran, de un vistazo, las puntuaciones semanales de todos los controles.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Puntuaciones de los controles de seguridad durante el período de tiempo seleccionado.":::|
|**Cambios en los recursos**<br>Aquí se enumeran las recomendaciones con el mayor número de recursos que han cambiado de estado (correcto, incorrecto o no aplicable) durante el período seleccionado. Seleccione cualquier recomendación de la lista para abrir una nueva tabla donde se muestren los recursos específicos.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Recomendaciones con el mayor número de recursos que han cambiado de estado de mantenimiento.":::|

### <a name="use-the-system-updates-workbook"></a>Uso del libro "Actualizaciones del sistema"

Este libro se basa en la recomendación de seguridad "Las actualizaciones del sistema deben estar instaladas en las máquinas".

El libro le ayuda a identificar las máquinas con actualizaciones pendientes.

Puede ver la situación de las suscripciones seleccionadas según:

- La lista de recursos con actualizaciones pendientes
- La lista de actualizaciones que faltan en los recursos

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Libro Actualizaciones del sistema de Security Center basado en la recomendación de seguridad de las actualizaciones que faltan":::

### <a name="use-the-vulnerability-assessment-findings-workbook"></a>Uso del libro "Conclusiones de la evaluación de vulnerabilidades"

Security Center incluye examinadores de vulnerabilidades para sus máquinas, contenedores en registros de contenedor y servidores SQL Server.

Más información sobre el uso de estos examinadores:

- [Análisis de máquinas con el examinador de Qualys integrado](deploy-vulnerability-assessment-vm.md)
- [Búsqueda de vulnerabilidades en las imágenes del registro](defender-for-container-registries-usage.md)
- [Examen de recursos de SQL en busca de vulnerabilidades](defender-for-sql-on-machines-vulnerability-assessment.md)

Los resultados de cada uno de estos examinadores se informan en recomendaciones independientes:

- Es necesario corregir las vulnerabilidades de las máquinas virtuales
- Es necesario corregir las vulnerabilidades de las imágenes de Azure Container Registry (con tecnología de Qualys)
- Se deben corregir las conclusiones de la evaluación de vulnerabilidades de las bases de datos SQL
- Es necesario corregir los resultados de la evaluación de vulnerabilidades de los servidores SQL Server en las máquinas

Este libro recopila estas conclusiones y las organiza por gravedad, tipo de recurso y categoría.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Informe Conclusiones de la evaluación de vulnerabilidades de Security Center.":::


### <a name="use-the-compliance-over-time-workbook"></a>Uso del libro "Cumplimiento a lo largo del tiempo"

Azure Security Center compara continuamente la configuración de los recursos con los requisitos de los estándares del sector, las regulaciones y los bancos de pruebas. Los estándares integrados incluyen NIST SP 800-53, SWIFT CSP CSCF v2020, Canada Federal PBMM, HIPAA HITRUST, etc. Puede seleccionar los estándares específicos pertinentes para su organización mediante el panel de cumplimiento normativo. Obtenga más información en [Personalización del conjunto de estándares en el panel de cumplimiento normativo](update-regulatory-compliance-packages.md).

Este libro le permite realizar un seguimiento del estado de cumplimiento a lo largo del tiempo con los distintos estándares que ha agregado al panel.

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-select-standards.png" alt-text="Seleccione los estándares para el informe de cumplimiento a lo largo del tiempo.":::

Al seleccionar un estándar en el área de información general del informe, el panel inferior muestra un desglose más detallado:

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Desglose detallado de los cambios relacionados con un estándar específico.":::

Puede continuar con la exploración en profundidad (hasta el nivel de recomendación) para ver los recursos que han pasado o han fallado cada control. 

> [!TIP]
> Para cada panel del informe, puede exportar los datos a Excel con la opción "Exportar a Excel".
>
> :::image type="content" source="media/custom-dashboards-azure-workbooks/export-workbook-data.png" alt-text="Exportación de datos del libro de cumplimiento a Excel.":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importación de libros desde otras galerías de libros

Si ha creado libros en otros servicios de Azure y quiere moverlos a la galería de libros de Azure Security Center:

1. Abra el libro de destino.

1. En la barra de herramientas, seleccione **Editar**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Edición de un libro de Azure Monitor.":::

1. En la barra de herramientas, seleccione **</>** para ir al Editor avanzado.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Inicio del Editor avanzado para obtener el código JSON de la plantilla de la galería.":::

1. Copie el archivo JSON de la plantilla de la galería del libro.

1. Abra la galería de libros en Security Center y, en la barra de menús, seleccione **Nuevo**.
1. Seleccione **</>** para ir al Editor avanzado.
1. Pegue todo el código JSON de la plantilla de la galería.
1. Seleccione **Aplicar**.
1. En la barra de herramientas, seleccione **Guardar como**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Almacenamiento del libro en la galería en Security Center.":::

1. Escriba los detalles necesarios para guardar el libro:
   1. Un nombre para el libro.
   2. La región deseada.
   3. Una suscripción, un grupo de recursos y el uso compartido, según corresponda.

Encontrará el libro guardado en la categoría **Libros modificados recientemente**.


## <a name="next-steps"></a>Pasos siguientes

En este artículo se describe la página de libros de Azure Monitor integrada de Security Center con los informes integrados y la opción de crear sus propios informes personalizados e interactivos.

- Obtenga más información sobre los [libros de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md).
- Los libros integrados extraen sus datos de las recomendaciones de Security Center. Obtenga información sobre las diversas recomendaciones de seguridad en [Guía de referencia sobre las recomendaciones de seguridad](recommendations-reference.md).