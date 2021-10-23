---
title: Supervisión de los servicios y aplicaciones de Azure mediante Grafana
description: Enrute los datos de Azure Monitor y Application Insights para que puedan verse en Grafana.
ms.topic: conceptual
ms.date: 10/10/2021
ms.openlocfilehash: 691ba341369778692c92fec8ffc47cd60fac3348
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002764"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Supervisar los servicios de Azure en Grafana
Los servicios y las aplicaciones de Azure se pueden supervisar usando [Grafana](https://grafana.com/) y el [complemento de origen de datos de Azure Monitor](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/) que se incluye. El complemento recupera datos de tres servicios de Azure:
- Métricas de Azure Monitor, para obtener datos numéricos de series temporales de recursos de Azure. 
- Registros de Azure Monitor, para obtener registros y datos de rendimiento de recursos de Azure, con los que se pueden realizar consultas mediante el lenguaje de consulta Kusto (KQL).
- Azure Resource Graph, para consultar e identificar rápidamente recursos de Azure en suscripciones.

Posteriormente, puede visualizar estos datos de rendimiento y disponibilidad en los paneles de Grafana.

Siga los pasos que figuran a continuación para configurar un servidor de Grafana, usar paneles predefinidos de Azure Monitor y crear otros personalizados con métricas y registros de Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Configuración de un servidor de Grafana

### <a name="set-up-grafana-locally"></a>Configuración de Grafana localmente
Para configurar un servidor local de Grafana, [descargue e instale Grafana en su entorno local](https://grafana.com/grafana/download).

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Configuración de Grafana en Azure a través de Azure Marketplace
1. Vaya a Azure Marketplace y seleccione Grafana de Grafana Labs.

2. Rellene los nombres y los detalles pertinentes. Cree un nuevo grupo de recursos. Lleve un seguimiento de los valores de nombre de usuario de máquina virtual, contraseña de máquina virtual y contraseña de administrador del servidor de Grafana que haya elegido.  

3. Elija un tamaño de máquina virtual y una cuenta de almacenamiento.

4. Configure la red.

5. Vea el resumen y seleccione **Crear** después de aceptar los términos de uso.

6. Una vez finalizada la implementación, seleccione **Ir al grupo de recursos**. Verá una lista de los recursos recién creados.

    ![Objetos del grupo de recursos de Grafana](media/grafana-plugin/grafana-resource-group.png)

    Si selecciona el grupo de seguridad de red (*grafana-nsg* en este caso), verá que se usa el puerto 3000 para tener acceso al servidor Grafana.

7. Obtenga la dirección IP pública del servidor de Grafana; vuelva a la lista de recursos y seleccione **Dirección IP pública**.

## <a name="sign-in-to-grafana"></a>Inicio de sesión en Grafana

> [!IMPORTANT]
> El explorador Internet Explorer y las versiones anteriores del explorador Microsoft Edge no son compatibles con Grafana. Debe usar un explorador basado en Chromium, incluida la versión actual de Microsoft Edge. Consulte [Exploradores compatibles con Grafana](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers).

1. Con la dirección IP del servidor, abra la página de inicio de sesión en *http://\<IP address\>:3000* o *\<DNSName>\:3000* en el explorador. Aunque 3000 es el puerto predeterminado, tenga en cuenta que es posible que haya seleccionado un puerto diferente durante la instalación. Debería aparecer una página de inicio de sesión del servidor de Grafana que creó.

    ![Pantalla de inicio de sesión de Grafana](./media/grafana-plugin/login-screen.png)

2. Inicie sesión con el nombre de usuario *admin* y la contraseña de administrador del servidor de Grafana que creó anteriormente. Si usa una instalación local, la contraseña predeterminada sería *admin*, y se le pedirá que la cambie en el primer inicio de sesión.

## <a name="configure-data-source-plugin"></a>Configurar el complemento de origen de datos

Una vez que haya iniciado sesión correctamente, debería aparecer la opción de agregar su primer origen de datos.

![Agregar origen de datos](./media/grafana-plugin/add-data-source.png)

1. Seleccione **Agregar origen de datos**, filtre por el nombre *Azure* y seleccione el origen de datos de **Azure Monitor**.

![Origen de datos de Azure Monitor](./media/grafana-plugin/azure-monitor-data-source-list.png)

2. Escoja un nombre para el origen de datos y elija entre una identidad administrada o un registro de aplicaciones para la autenticación.

Si la instancia de Grafana se hospeda en una máquina virtual de Azure con una identidad administrada habilitada, puede usar este enfoque para la autenticación. Sin embargo, si la instancia de Grafana no está hospedada en Azure o no tiene habilitada una identidad administrada, deberá usar un registro de aplicaciones con una entidad de servicio de Azure para configurar la autenticación.

### <a name="use-managed-identity"></a>Uso de identidad administrada

1. Habilite una identidad administrada en su máquina virtual y cambie a "true" el valor de compatibilidad con identidad administrada del servidor de Grafana.
    * La identidad administrada de su máquina virtual de hospedaje debe tener asignado el [rol "Lector de supervisión"](/azure/azure-monitor/roles-permissions-security) para la suscripción, el grupo de recursos o los recursos que usted vaya a visualizar en Grafana.
    * Además, deberá actualizar el valor "managed_identity_enabled = true" en la configuración del servidor de Grafana. Consulte [Configuración de Grafana](https://grafana.com/docs/grafana/latest/administration/configuration/) para obtener más información. Una vez completados ambos pasos, puede guardar y probar el acceso.

2. Seleccione **Guardar y probar** para que Grafana pruebe las credenciales. Debería aparecer un mensaje similar al siguiente.  
    
   ![Aprobación de la configuración de identidad administrada de origen de datos de Grafana](./media/grafana-plugin/managed-identity.png)

### <a name="or-use-app-registration"></a>Uso de un registro de aplicaciones

1. Cree una entidad de servicio: Grafana usa una entidad de servicio de Azure Active Directory para conectarse a las API de Azure Monitor y recopilar datos. Debe crear una entidad de servicio, o usar una existente, para administrar el acceso a los recursos de Azure.
    * Vea [estas instrucciones](/azure/active-directory/develop/howto-create-service-principal-portal) para crear una entidad de servicio. Copie y guarde el identificador de inquilino (id. de directorio), el identificador de cliente (id. de aplicación) y el secreto de cliente (valor de clave de aplicación).
    * Consulte [Asignación de aplicación a un rol](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) para asignar el [rol "Lector de supervisión"](/azure/azure-monitor/roles-permissions-security) a la aplicación de Azure Active Directory en la suscripción, el grupo de recursos o el recurso que quiera supervisar.
  
2. Proporcione los detalles de conexión que desee usar.
    * Al configurar el complemento, puede indicar qué nube de Azure (pública, Azure US Gov, Azure Alemania o Azure China) quiere que supervise el complemento.
        > [!NOTE]
        > Algunos campos del origen de datos se denominan de forma diferente a su configuración de Azure correlacionada:
        > * El id. de inquilino es el identificador de Azure Directory
        > * El id. de cliente es el id. de la aplicación de Azure Active Directory
        > * El secreto de cliente es el valor de clave de la aplicación de Azure Active Directory

3. Seleccione **Guardar y probar** para que Grafana pruebe las credenciales. Debería aparecer un mensaje similar al siguiente.  
    
   ![Aprobación de la configuración de registro de aplicaciones de origen de datos de Grafana](./media/grafana-plugin/app-registration.png)

## <a name="use-azure-monitor-data-source-dashboards"></a>Uso de paneles de orígenes de datos de Azure Monitor

El complemento de Azure Monitor incluye varios paneles predefinidos que puede importar para empezar a trabajar.

1. Haga clic en la pestaña **Paneles** del complemento de Azure Monitor para ver una lista de los paneles disponibles.

   ![Paneles de orígenes de datos de Azure Monitor](./media/grafana-plugin/azure-data-source-dashboards.png)

2. Haga clic en **Importar** para descargar un panel.

3. Haga clic en el nombre del panel importado para abrirlo.

4. Use los selectores desplegables de la parte superior del panel para elegir la suscripción, el grupo de recursos y el recurso de su interés.

   ![Paneles de información de Storage](./media/grafana-plugin/storage-insights-dashboard.png)

## <a name="build-grafana-dashboards"></a>Creación de paneles de Grafana

1. Vaya a la página principal de Grafana y seleccione **Create your first dashboard** (Crear su primer panel).

2. En el nuevo panel, seleccione **Add an empty panel** (Agregar un panel vacío).

3. Aparecerá un panel *Time series* (Serie temporal) vacío con el editor de consultas que se muestra a continuación. Seleccione el origen de datos de Azure Monitor que ha configurado.
   * Recopilación de métricas de Azure Monitor: seleccione **Metrics** (Métricas) en la lista desplegable del servicio. Aparece una lista de selectores, donde puede seleccionar los recursos y las métricas que quiere supervisar en este gráfico. Para recopilar métricas de una VM, use el espacio de nombres **Microsoft.Compute/VirtualMachines**. Cuando haya seleccionado las VM y las métricas, puede empezar a ver los datos en el panel.
     ![Configuración de un gráfico de Grafana para métricas de Azure Monitor](./media/grafana-plugin/metrics-config.png)
   * Recopilación de datos de registro de Azure Monitor: seleccione **Logs** (Registros) en la lista desplegable del servicio. Seleccione el recurso o el área de trabajo de Log Analytics que desee consultar y defina el texto de consulta. Tenga presente que el complemento de Azure Monitor permite consultar los registros de recursos específicos o desde un área de trabajo de Log Analytics. En el editor de consultas que figura a continuación, puede copiar cualquier consulta de registro que ya tenga o crear otra. A medida que escribe la consulta, aparecerá IntelliSense y sugerirá las opciones de Autocompletar. Por último, seleccione el tipo de visualización, **Time series**, y ejecute la consulta.
    
     > [!NOTE]
     >
     > La consulta predeterminada que se proporciona con el complemento usa dos macros: "$__timeFilter() y $__interval. 
     > Estas macros permiten a Grafana calcular dinámicamente el intervalo de tiempo y el intervalo de agregación, al acercarse a una parte de un gráfico. Puede quitar estas macros y usar un filtro de tiempo estándar, como *TimeGenerated > ago(1h)* , pero significaría que el gráfico no admitiese la característica de acercamiento.
    
     En el siguiente ejemplo se muestra una consulta que se ejecuta en un recurso de Application Insights para el tiempo medio de respuesta de todas las solicitudes.

     ![Configuración del gráfico de Grafana para Azure Log Analytics](./media/grafana-plugin/logs-config.png)

    * Además de las consultas de métricas y registros que se muestran anteriormente, el complemento de Azure Monitor admite consultas de [Azure Resource Graph](/azure/governance/resource-graph/concepts/explore-resources).

## <a name="advanced-grafana-features"></a>Características avanzadas de Grafana

### <a name="variables"></a>variables
El usuario del panel puede seleccionar algunos valores de recursos y consultas mediante listas desplegables de la interfaz de usuario y actualizarlos en el recurso o la consulta.
Considere a modo de ejemplo la siguiente consulta, en la que se muestra el uso de un área de trabajo de Log Analytics:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Puede configurar una variable que enumere todas las **áreas de trabajo** disponibles y, a continuación, actualizar el recurso que se consulta en función de una selección del usuario.
Para crear una variable, haga clic en botón de configuración del panel en la parte superior derecha, seleccione **Variables** y luego **New**.
En la página de la variable, defina el origen de datos y la consulta que se ejecutará con el fin de obtener la lista de valores.

![Definición de variable en Grafana](./media/grafana-plugin/define-variable.png)

Una vez creada, cambie el recurso de la consulta para usar los valores seleccionados. Sus gráficos responderán en consecuencia:

![Consulta con variable](./media/grafana-plugin/query-with-variable.png)

Consulte la lista completa de las [variables de plantilla](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/) disponibles en el complemento de Azure Monitor.

### <a name="create-dashboard-playlists"></a>Creación de listas de reproducción de panel

Una de las muchas características útiles de Grafana es la lista de reproducción de panel. Puede crear varios paneles y agregarlos a una lista de reproducción, configurando un intervalo para que cada panel se muestre. Vaya al elemento de menú "Dashboards" (Paneles) y seleccione **Playlists** (Listas de reproducción) para crear una lista de reproducción de los paneles existentes por los que desplazarse. Puede que quiera que se muestren en un monitor de pared de grandes dimensiones a modo de panel de estado del grupo.

![Ejemplo de lista de reproducción de Grafana](./media/grafana-plugin/playlist.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ha configurado un entorno de Grafana en Azure, se le cobrará cuando se ejecutan las VM, así las esté usando o no. Para evitar estos cargos extra, limpie el grupo de recursos creado en este artículo.

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en **Grafana**.
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba **Grafana** en el cuadro de texto y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes
* [Comparación de las métricas y los registros de Azure Monitor](../data-platform.md)
