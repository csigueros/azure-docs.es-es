---
title: Envío por correo electrónico de Informes de Azure Backup
description: Creación de tareas automatizadas para recibir informes periódicos por correo electrónico
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: dd5e31663262463449df4377329edc6604ffb691
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235988"
---
# <a name="email-azure-backup-reports"></a>Envío por correo electrónico de Informes de Azure Backup

Con la característica **Informe de correo electrónico** disponible en los informes de Backup, puede crear tareas automatizadas para recibir informes periódicos por correo electrónico. Para funcionar, esta característica implementa una aplicación lógica en el entorno de Azure que consulta los datos de las áreas de trabajo de Log Analytics (LA) seleccionadas, en función de las entradas que proporcione. [Más información sobre Logic Apps y sus precios](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Introducción

Para configurar tareas por correo electrónico mediante Informes de copia de seguridad, siga estos pasos:

1.  Vaya a **Centro de copias de seguridad** > **Informes de Backup** y haga clic en la pestaña **Enviar el informe por correo electrónico**.
2.  Cree una tarea con la siguiente información:
    * **Detalles de la tarea**: el nombre de la aplicación lógica que se va a crear, así como la suscripción, el grupo de recursos y la ubicación en la que debe crearse. Tenga en cuenta que la aplicación lógica puede consultar datos entre varias suscripciones, grupos de recursos y ubicaciones (tal como se ha seleccionado en la sección Filtros de informe), pero se crea en el contexto de una única suscripción, grupo de recursos y ubicación.
    * **Datos para exportar**: la pestaña que quiere exportar. Puede crear una aplicación de una única tarea por pestaña o enviar por correo electrónico todas las pestañas mediante una única tarea seleccionando la opción **All Tabs** (Todas las pestañas).
    * **Opciones de correo electrónico**: la frecuencia del correo electrónico, los identificadores del destinatario y el asunto del correo electrónico.

    ![Pestaña Correo electrónico](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Después de hacer clic en **Enviar** y **Confirmar**, se creará la aplicación lógica. La aplicación lógica y las conexiones de API asociadas se crean con la etiqueta **UsedByBackupReports: true** para facilitar su detectabilidad. Tendrá que realizar un paso de autorización única para que la aplicación lógica se ejecute correctamente, tal como se describe en la sección siguiente.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Autorización de conexiones a Azure Monitor registros y Office 365

La aplicación lógica utiliza el conector [azuremonitorlogs](/connectors/azuremonitorlogs/) para consultar las áreas de trabajo de la aplicación lógica y usa el conector [Office365 Outlook](/connectors/office365connector/) para enviar correos electrónicos. Tendrá que realizar una autorización única para estos dos conectores. 
 
Para llevar a cabo la autorización, siga estos pasos:

1.  Vaya a **Logic Apps** en Azure Portal.
2.  Busque el nombre de la aplicación lógica que haya creado y vaya al recurso.

    ![Logic Apps](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Haga clic en el elemento de menú **Conexiones de API**.

    ![Conexiones de API](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Verá dos conexiones con el formato `<location>-azuremonitorlogs` y `<location>-office365`, es decir, _eastus-azuremonitorlogs_ y _eastus-office365_.
5.  Vaya a cada una de estas conexiones y seleccione el elemento de menú **Editar la conexión de API**. En la pantalla que aparece, seleccione **Autorizar** y guarde la conexión una vez completada la autorización.

    ![Autorización de la conexión](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Para probar si la aplicación lógica funciona después de la autorización, puede volver a la aplicación lógica, abrir **Información general** y seleccionar **Ejecutar desencadenador** en el panel superior para comprobar si se está generando correctamente un correo electrónico.

## <a name="contents-of-the-email"></a>Contenido del correo electrónico

* Todos las tablas y gráficos que se muestran en el portal están disponibles en forma de contenido en línea en el correo electrónico. [Más información](configure-reports.md) sobre los datos que se muestran en Informes de Backup.
* Las cuadrículas que se muestran en el portal están disponibles en forma de datos adjuntos *.csv en el correo electrónico.
* Los datos que se muestran en el correo electrónico utilizan todos los filtros de nivel de informe seleccionados por el usuario en el informe, en el momento de crear la tarea de correo electrónico.
* No se aplican los filtros de nivel de pestaña, como **Nombre de la instancia de copia de seguridad**, **Nombre de directiva**, etc. La única excepción en este caso es la cuadrícula **Retention Optimizations** (Optimizaciones de retención) en la pestaña **Optimizar**, donde se aplican los filtros para la retención de puntos de retención **Cada día**, **Cada semana**, **Cada mes** y **Anualmente**.
* El intervalo de tiempo y el tipo de agregación (para gráficos) se basan en la selección del intervalo de tiempo del usuario en los informes. Por ejemplo, si la selección del intervalo de tiempo es los últimos 60 días (se convierte al tipo de agregación semanal) y la frecuencia del correo electrónico es diaria, el destinatario recibirá un correo electrónico cada día con los gráficos que abarcan los datos tomados durante el último período de 60 días, con datos agregados a nivel semanal.

## <a name="troubleshooting-issues"></a>Solución de problemas

Si no recibe correos electrónicos según lo previsto, incluso después de la implementación correcta de la aplicación lógica, puede seguir los pasos que se indican a continuación para solucionar los problemas de configuración:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Escenario 1: No se recibe un correo electrónico de realización correcta ni de error

* Este problema se puede producir porque el conector de la API de Outlook no está autorizado. Para autorizar la conexión, siga los pasos de autorización indicados anteriormente.

* Este problema también se podría producir si ha especificado un destinatario de correo electrónico incorrecto al crear la aplicación lógica. Para comprobar que el destinatario de correo electrónico se ha especificado correctamente, puede ir a la aplicación lógica en Azure Portal, abrir el Diseñador de aplicación lógica y seleccionar el paso de correo electrónico para ver si se están utilizando los identificadores de correo electrónico correctos.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Escenario 2: Se recibe un correo electrónico de error que indica que la aplicación lógica no se pudo ejecutar hasta completarse

Para solucionar esta incidencia:
1.  Vaya a la aplicación en Azure Portal.
2.  En la parte inferior de la pantalla **Información general**, verá una sección **Historial de ejecuciones**. Puede abrir en la última ejecución y ver los pasos del flujo de trabajo que contengan errores. Estas pueden ser algunas causas posibles:
    * **El conector de registros de Azure Monitor no se ha autorizado**: para corregir este problema, siga los pasos de autorización indicados anteriormente.
    * **Error en la consulta de la aplicación lógica**: en caso de que haya personalizado la aplicación lógica con sus propias consultas, un error en cualquiera de las consultas de la aplicación lógica puede provocar un error en la aplicación lógica. Puede seleccionar el paso apropiado y ver el error que provoca una ejecución incorrecta de la consulta.

Si los problemas persisten, póngase en contacto con el servicio de soporte técnico de Microsoft.

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre los Informes de Backup](./configure-reports.md)