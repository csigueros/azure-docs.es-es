---
title: Conector de Administración de servicios de TI en Log Analytics
description: En este artículo se proporciona información general sobre el Conector de Administración de servicios de TI (ITSMC) e información sobre cómo usarlo para supervisar y administrar los elementos de trabajo de ITSM en Log Analytics y resolver rápidamente cualquier problema.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 3097023c4b9d19379f44529615a10c100e794700
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "109838682"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-solution"></a>Conexión de Azure a las herramientas de ITSM mediante el la solución Administración de servicios de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

En este artículo se proporciona información sobre cómo configurar el Conector de Administración de servicios de TI (ITSMC) en Log Analytics para administrar de forma centralizada los elementos de trabajo de Administración de servicios de TI (ITSM).

## <a name="add-it-service-management-connector"></a>Adición del Conector de Administración de servicios de TI

Para poder crear una conexión, debe instalar ITSMC.

1. En Azure Portal, seleccione **Crear un recurso**:

   ![Captura de pantalla en la que se muestra el elemento de menú para crear un recurso.](media/itsmc-overview/azure-add-new-resource.png)

2. Busque el **Conector de Administración de servicios de TI** en Azure Marketplace. A continuación, seleccione **Crear**:

   ![Captura de pantalla en la que muestra el botón Crear en Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. En la sección **Área de trabajo de LA**, seleccione el área de trabajo de Log Analytics donde quiera instalar ITSMC.
   > [!NOTE]
   > Solo puede instalar ITSMC en áreas de trabajo de Log Analytics en las siguientes regiones: Este de EE. UU., Oeste de EE. UU. 2, Centro-sur de EE. UU., Centro-oeste de EE. UU., US Gov Arizona, US Gov Virginia, Centro de Canadá, Oeste de Europa, Sur de Reino Unido, Sudeste Asiático, Este de Japón, Centro de la India y Sudeste de Australia.

4. En la sección **Área de trabajo de Log Analytics**, seleccione el grupo de recursos donde quiera crear el recurso de ITSMC:

   ![Captura de pantalla en la que se muestra la sección Área de trabajo de Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   
   > [!NOTE]
   > Como parte de la transición continuada de Microsoft Operations Management Suite (OMS) a Azure Monitor, las áreas de trabajo de OMS ahora se denominan *áreas de trabajo de Log Analytics*.

5. Seleccione **Aceptar**.

Cuando se implemente el recurso de ITSMC, aparecerá una notificación en la esquina superior derecha de la ventana.

## <a name="create-an-itsm-connection"></a>Cree una conexión de ITSM

Después de instalar ITSMC, debe preparar la herramienta de ITSM para permitir la conexión desde ITSMC. En función del producto de ITSM al que se va a conectar, seleccione alguno de los siguientes vínculos para obtener instrucciones:

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

Una vez que haya preparado la herramienta de ITSM, siga estos pasos para crear una conexión:

1. En **Todos los recursos**, busque **ServiceDesk(*nombre de su área de trabajo*)** :

   ![Captura de pantalla en la que se muestran los recursos recientes en Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

1. En **Orígenes de datos del área de trabajo** en el panel de la izquierda, seleccione **Conexiones de ITSM**:

   ![Captura de pantalla en la que se muestra el elemento de menú Conexiones de ITSM.](media/itsmc-overview/add-new-itsm-connection.png)

1. Seleccione **Agregar conexión**.

1. Especifique la configuración de conexión según el producto de ITSM que esté usando:

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   > De forma predeterminada, ITSMC actualiza los datos de configuración de la conexión una vez cada 24 horas. Para actualizar los datos de la conexión al instante para reflejar las modificaciones o actualizaciones de plantilla que se realicen, seleccione el botón **Sincronizar** que se muestra en el panel de la conexión:
   >
   > ![Captura de pantalla en la que se muestra el botón Sincronizar en el panel de la conexión.](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="create-itsm-work-items-from-azure-alerts"></a>Creación de elementos de trabajo de ITSM a partir de alertas de Azure

Después de crear la conexión de ITSM, puede usar ITSM para crear elementos de trabajo en la herramienta de ITSM en función de las alertas de Azure. Para crear los elementos de trabajo, usará la acción de ITSM en grupos de acciones.

Los grupos de acciones proporcionan una manera modular y reutilizable de desencadenar acciones para las alertas de Azure. Puede usar los grupos de acciones con alertas de métricas, de registros de actividad y de Log Analytics en Azure Portal.

> [!NOTE]
> Después de crear la conexión de ITSM, debe esperar 30 minutos para que finalice el proceso de sincronización.

### <a name="define-a-template"></a>Definición de una plantilla

Algunos tipos de elemento de trabajo pueden utilizar plantillas que se definen en la herramienta de ITSM. Con el uso de plantillas, puede definir los campos que se rellenarán automáticamente en función de los valores fijos de un grupo de acciones. Puede definir qué plantilla le gustaría usar como parte de la definición de un grupo de acciones. Puede encontrar información en los documentos de ServiceNow sobre cómo crear plantillas (aquí)[https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/form-administration/task/t_CreateATemplateUsingTheTmplForm.html ].

Para crear un grupo de acciones:

1. En Azure Portal, seleccione **Alertas**.
2. En el menú de la parte superior de la pantalla, seleccione **Administrar acciones**:

    ![Captura de pantalla en la que se muestra el elemento de menú Administrar acciones.](media/itsmc-overview/action-groups-selection-big.png)

   Aparece la ventana **Crear grupo de acciones**.

3. Seleccione las opciones de **Suscripción** y **Grupo de recursos** donde quiere crear el grupo de acciones. Proporcione valores en **Nombre del grupo de acciones** y **Nombre para mostrar** para el grupo de acciones. Después, seleccione **Next: notificaciones**.

    ![Captura de pantalla en la que se muestra la ventana Crear grupo de acciones.](media/itsmc-overview/action-groups-details.png)

4. En la pestaña **Notificaciones**, seleccione **Siguiente: acciones**.
5. En la pestaña **Acciones**, seleccione **ITSM** en la lista **Tipo de acción**. En **Nombre**, proporcione un nombre para la acción. A continuación, seleccione el botón del lápiz que representa **Editar detalles**.

    ![Captura de pantalla en la que se muestran las selecciones para crear un grupo de acciones.](media/itsmc-definition/action-group-pen.png)

6. En la lista **Suscripción**, seleccione la suscripción que contiene el área de trabajo de Log Analytics. En la lista **Conexión**, seleccione el nombre del conector de ITSM. Irá seguido del nombre del área de trabajo. Por ejemplo, *MyITSMConnector(MyWorkspace)* .

7. Seleccione un tipo de **Elemento de trabajo**.

8. En la última sección de la interfaz para crear un grupo de acciones de ITSM, puede definir cuántos elementos de trabajo se crearán para cada alerta.

   > [!NOTE]
   > Esta sección solo es pertinente para las alertas de búsqueda de registros. Para todos los demás tipos de alerta, se creará un elemento de trabajo por cada alerta.

   * Si seleccionó **Incidente** o **Alerta** en la lista desplegable **Elemento de trabajo**, tiene la opción de crear elementos de trabajo individuales para cada elemento de configuración.
    
     ![Captura de pantalla en la que se muestra el área de vales de ITSM con Incidente seleccionado como elemento de trabajo.](media/itsmc-overview/itsm-action-configuration.png)
    
     * Si selecciona la casilla **Crear elementos de trabajo individuales para cada elemento de configuración**, cada elemento de configuración en cada alerta creará un elemento de trabajo. Dado que varias alertas se producirán para los mismos elementos de configuración afectados, habrá más de un elemento de trabajo para cada elemento de configuración.

       Por ejemplo, una alerta que tenga tres elementos de configuración creará tres elementos de trabajo. Una alerta que tenga un elemento de configuración creará un elemento de trabajo.
        
     * Si desactiva la casilla **Crear elementos de trabajo individuales para cada elemento de configuración**, ITSM creará un único elemento de trabajo para cada regla de alerta y lo anexará todos los elementos de configuración afectados. Si el anterior está cerrado, se creará un nuevo elemento de trabajo.

       >[!NOTE]
       > En este caso, algunas de las alertas desencadenadas no generarán elementos de trabajo nuevos en la herramienta de ITSM.

       Por ejemplo, una alerta que tenga tres elementos de configuración creará un elemento de trabajo. Si una alerta para la misma regla de alertas que el ejemplo anterior tiene un elemento de configuración, dicho elemento de configuración se adjuntará a la lista de elementos de configuración afectados en el elemento de trabajo creado. Una alerta para una regla de alertas diferente que tenga un elemento de configuración creará un elemento de trabajo.

   * Si seleccionó **Evento** en la lista desplegable **Elemento de trabajo**, tiene la opción de crear elementos de trabajo individuales para cada entrada de registro o para cada elemento de configuración.
    
     ![Captura de pantalla en la que se muestra el área de vales de ITSM con Evento seleccionado como elemento de trabajo.](media/itsmc-overview/itsm-action-configuration-event.png)

     * Si selecciona **Crear elementos de trabajo individuales para cada entrada de registro (no se rellena el campo de elemento de configuración. Puede dar como resultado un gran número de elementos de trabajo).** , se creará un elemento de trabajo por cada fila de los resultados de búsqueda de la consulta de alerta de búsqueda de registros. La propiedad description de la carga útil del elemento de trabajo contendrá la fila de los resultados de la búsqueda.
      
     * Si selecciona **Crear elementos de trabajo individuales para cada elemento de configuración**, cada elemento de configuración en cada alerta creará un elemento de trabajo. Cada elemento de configuración puede tener más de un elemento de trabajo en el sistema de ITSM. Esta opción es la misma que la selección de la casilla que aparece después de seleccionar **Incidente** como tipo de elemento de trabajo.
9. Como parte de la definición de la acción, puede definir campos predefinidos que contendrán valores constantes como parte de la carga. En función del tipo de elemento de trabajo, hay tres opciones que se pueden usar como parte de la carga:
    * **Ninguno**: use una carga normal en ServiceNow sin campos ni valores predefinidos adicionales.
    * **Use default fields** (Usar campos predeterminados): se usa un conjunto de campos y valores que se enviarán automáticamente como parte de la carga a ServiceNow. Esos campos no son flexibles y los valores se definen en las listas de ServiceNow.
    * **Use saved templates from ServiceNow** (Usar plantillas guardadas de ServiceNow): usar un conjunto predefinido de campos y valores que se definió como parte de una definición de plantilla en ServiceNow. Si ya ha definido la plantilla en ServiceNow, puede usarla desde la lista **Plantilla**; de lo contrario, puede definirla en ServiceNow para más [detalles](#define-a-template).

10. Seleccione **Aceptar**.

Al crear o editar una regla de alerta de Azure, use un grupo de acciones, que tiene una acción de ITSM. Cuando se desencadena la alerta, se crea o actualiza el elemento de trabajo en la herramienta ITSM.

> [!NOTE]
> Para obtener información sobre los precios de la acción de ITSM, vea la [página de precios](https://azure.microsoft.com/pricing/details/monitor/) de los grupos de acciones.
>
> El campo de descripción breve de la definición de la regla de alerta está limitado a 40 caracteres cuando se envía mediante la acción de ITSM.

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas en ITSMC](./itsmc-resync-servicenow.md)
