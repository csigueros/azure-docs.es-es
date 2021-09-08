---
title: Solución de problemas de CI/CD, Azure DevOps y GitHub en ADF
titleSuffix: Azure Data Factory & Azure Synapse
description: Use distintos métodos para solucionar problemas de CI/CD en ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.subservice: ci-cd
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 06/27/2021
ms.openlocfilehash: 8f94e6b0e4afd06a68263efb0d78f3962bbd8560
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866413"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Solución de problemas de CI/CD, Azure DevOps y GitHub en ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo vamos a explorar métodos habituales de solución de problemas de integración continua/implementación continua (CI/CD), Azure DevOps y GitHub en Azure Data Factory.

Si tiene preguntas o problemas en relación con el uso de las técnicas de DevOps o el control de código fuente, aquí encontrará algunos artículos que pueden resultarle útiles:

- Consulte [Control de código fuente en ADF](source-control.md) para obtener información sobre cómo se lleva a cabo el control de código fuente en ADF. 
- Consulte [CI/CD en ADF](continuous-integration-deployment.md) para obtener más información sobre cómo se lleva a cabo la integración continua/implementación continua de DevOps en ADF.

## <a name="common-errors-and-messages"></a>Errores habituales y mensajes

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Error de conexión al repositorio de Git debido a un inquilino distinto

#### <a name="issue"></a>Incidencia

A veces se producen problemas de autenticación, como el estado HTTP 401. Esto ocurre especialmente cuando tiene varios inquilinos con una cuenta de invitado, lo que puede complicar las cosas.

#### <a name="cause"></a>Causa

Según se observa, el token se ha obtenido del inquilino original, pero ADF está en el inquilino invitado e intenta usar el token para visitar DevOps en el inquilino invitado. Este no es el comportamiento esperado.

#### <a name="recommendation"></a>Recomendación

Debe usar el token emitido desde el inquilino invitado. Por ejemplo, tiene que asignar la misma instancia de Azure Active Directory para que sea su inquilino invitado y DevOps, de modo que pueda establecer correctamente el comportamiento del token y usar el inquilino correcto.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Los parámetros de plantilla del archivo de parámetros no son válidos

#### <a name="issue"></a>Incidencia

Si eliminamos un desencadenador en la rama de desarrollo que ya está disponible en la rama de prueba o de producción con la **misma** configuración (por ejemplo, la frecuencia y el intervalo), la implementación de la canalización de versión se realiza correctamente y el desencadenador correspondiente se eliminará en los entornos respectivos. Sin embargo, si tiene **diferentes** configuraciones (como la frecuencia y el intervalo) para el desencadenador en los entornos de prueba o producción y elimina el mismo desencadenador en el entorno de desarrollo, se produce un error en la implementación.

#### <a name="cause"></a>Causa

Error de la canalización de CI/CD:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Recomendación

Este error se produce porque a menudo se elimina un desencadenador que está parametrizado, por lo que los parámetros no estarán disponibles en la plantilla de Azure Resource Manager (ARM) (dado que el desencadenador ya no existe). Dado que el parámetro ya no está en la plantilla de ARM, es necesario actualizar los parámetros invalidados en la canalización de DevOps. De lo contrario, cada vez que cambian los parámetros de la plantilla de ARM, deben actualizar los parámetros invalidados en la canalización de DevOps (en la tarea de implementación).

### <a name="updating-property-type-is-not-supported"></a>No se admite la actualización del tipo de propiedad

#### <a name="issue"></a>Incidencia

Error de la canalización de versión de CI/CD:

```output
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
```

#### <a name="cause"></a>Causa

Este error se debe a que en la fábrica de destino hay un entorno de ejecución de integración con el mismo nombre, pero de un tipo distinto. El entorno de ejecución de integración debe ser del mismo tipo durante la implementación.

#### <a name="recommendation"></a>Recomendación

- Tenga en cuenta los [procedimientos recomendados para CI/CD](continuous-integration-deployment.md#best-practices-for-cicd).

- Los entornos de ejecución de integración no cambian a menudo y son similares en todas las fases de CI/CD, por lo que Data Factory espera el mismo nombre y el mismo tipo de entorno de ejecución de integración en todas las fases de CI/CD. Si el nombre y los tipos y propiedades son diferentes, asegúrese de que las configuraciones del entorno de ejecución de integración de origen y de destino sean iguales y, a continuación, implemente la canalización de versión.

- Si quiere compartir entornos de ejecución de integración en todas las fases, considere la posibilidad de usar una factoría ternaria solo para contener los entornos de ejecución de integración compartidos. Puede usar esta factoría compartida en todos los entornos como un tipo de entorno de ejecución de integración vinculado.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Error al crear o actualizar documentos debido a una referencia no válida

#### <a name="issue"></a>Incidencia

Al intentar publicar cambios en una instancia de Data Factory, aparece el mensaje de error siguiente:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>Causa

Ha desasociado la configuración de Git y la ha configurado de nuevo con la marca de importar recursos seleccionada, que establece la instancia de Data Factory como sincronizada. Esto significa que no hay ningún cambio durante la publicación.

#### <a name="resolution"></a>Resolución

Desasocie la configuración de Git y vuelva a configurarla, asegurándose de NO activar la casilla de importar recursos existentes.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>No se puede mover una instancia de Data Factory de un grupo de recursos a otro

#### <a name="issue"></a>Incidencia

No se puede trasladar una instancia de Data Factory de un grupo de recursos a otro y se recibe el siguiente error:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Resolución

Puede eliminar el entorno de ejecución de integración de SSIS y el entorno de ejecución de integración compartido para permitir la operación de traslado. Si no desea eliminar los entornos de ejecución de integración, lo mejor es seguir el documento de copia y clonación para realizar una copia y, tras ello, eliminar la antigua instancia de Data Factory.

###  <a name="unable-to-export-and-import-arm-template"></a>No se puede exportar ni importar la plantilla de ARM

#### <a name="issue"></a>Incidencia

No es posible exportar ni importar la plantilla de ARM. No se ha producido ningún error en el portal; sin embargo, en el seguimiento del explorador, puede ver el siguiente error:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Causa

Ha creado un rol de cliente como usuario y no tenía el permiso necesario. Cuando la fábrica se carga en la interfaz de usuario, se comprueba una serie de valores de control de exposición para la fábrica. En este caso, el rol de acceso del usuario no tiene permiso para acceder a la API *queryFeaturesValue*. Para acceder a esta API, la característica de parámetros globales se desactiva. La ruta de acceso al código de exportación de ARM se basa en parte en la característica de parámetros globales.

#### <a name="resolution"></a>Resolución

Para resolver el problema, debe agregar el siguiente permiso al rol: *Microsoft.DataFactory/factories/queryFeaturesValue/action*. Este permiso se debe incluir de forma predeterminada en el rol "Colaborador de Data Factory".

###  <a name="cannot-automate-publishing-for-cicd"></a>No se puede automatizar la publicación de CI/CD 

#### <a name="cause"></a>Causa

Hasta hace poco, la única forma de publicar la canalización de ADF para implementaciones era usar el clic del botón del portal de ADF. Ahora, puede hacer que el proceso sea automático. 

#### <a name="resolution"></a>Solución

Se ha mejorado el proceso de CI/CD. La característica **Publicación automatizada** toma, valida y exporta todas las características de plantilla de ARM de la experiencia de usuario de ADF. Hace que la lógica se consuma a través de un paquete NPM disponible públicamente [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities). Este método le permite desencadenar mediante programación estas acciones en lugar de tener que ir a la interfaz de usuario de ADF y hacer clic en un botón. Este método proporciona a las canalizaciones de CI/CD una **verdadera** experiencia de integración continua. Siga las [mejoras de publicación de CI/CD de ADF](./continuous-integration-deployment-improvements.md) para obtener información. 

###  <a name="cannot-publish-because-of-4-mb-arm-template-limit"></a>No se puede publicar debido a un límite de la plantilla de ARM de 4 MB.  

#### <a name="issue"></a>Problema

No puede realizar la implementación porque se alcanzó el límite de tamaño total de 4 MB de la plantilla de Azure Resource Manager. Necesita una solución para implementar después de rebasar el límite. 

#### <a name="cause"></a>Causa

Azure Resource Manager restringe el tamaño de la plantilla a 4 MB. Limite el tamaño de la plantilla a 4 MB y cada archivo de parámetros a 64 KB. El límite de 4 MB se aplica al estado final de la plantilla una vez se ha ampliado con definiciones de recursos iterativas y los valores de variables y parámetros. Sin embargo, ha superado el límite. 

#### <a name="resolution"></a>Solución

En el caso de soluciones pequeñas o medianas, es más fácil entender y mantener una única plantilla. Puede ver todos los recursos y valores en un único archivo. Para los escenarios avanzados, las plantillas vinculadas le permiten desglosar la solución en componentes dirigidos. Siga los procedimientos recomendados en [Uso de plantillas vinculadas y anidadas](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise"></a>No se puede establecer la conexión a GIT Enterprise  

##### <a name="issue"></a>Problema

No se puede conectar a GIT Enterprise debido a problemas de permisos. Puede ver un error como **422 - Entidad no procesable.**

#### <a name="cause"></a>Causa

* No ha configurado OAuth para ADF. 
* La dirección URL está mal configurada. El valor RepoConfiguration debe ser de tipo [FactoryGitHubConfiguration](/dotnet/api/microsoft.azure.management.datafactory.models.factorygithubconfiguration?view=azure-dotnet&preserve-view=true).

#### <a name="resolution"></a>Solución 

Conceda acceso de OAuth a ADF al principio. Después, tiene que usar la dirección URL correcta para conectarse a GIT Enterprise. La configuración debe establecerse en las organizaciones del cliente. Por ejemplo, ADF probará *https://hostname/api/v3/search/repositories?q=user%3&lt;customer credential&gt;....* al principio y se producirán errores. A continuación, probará *https://hostname/api/v3/orgs/&lt;org&gt;/&lt; repo&gt;...* y la operación se realizará correctamente.  
 
### <a name="cannot-recover-from-a-deleted-data-factory"></a>No es posible la recuperación desde una factoría de datos eliminada.

#### <a name="issue"></a>Problema
El cliente eliminó la instancia de Data Factory o el grupo de recursos que contiene dicha instancia. Al cliente le gustaría saber cómo restaurar una factoría de datos eliminada.

#### <a name="cause"></a>Causa

Es posible recuperar la instancia de Data Factory solo si el cliente tiene configurado el control de código fuente (DevOps o Git). Esta acción proporcionará todos los recursos publicados más recientes y **no** restaurará la canalización, el conjunto de datos y el servicio vinculado sin publicar. Si no hay ningún control de código fuente, no es posible recuperar una instancia de Data Factory eliminada del back-end porque, una vez que el servicio recibe el comando deleted, se elimina la instancia y no se almacena ninguna copia de seguridad.

#### <a name="resolution"></a>Solución

Para recuperar la instancia de Data Factory eliminada que tiene el control de código fuente, consulte los pasos siguientes:

 * Cree una nueva instancia de Azure Data Factory.

 * Vuelva a configurar Git con los mismos valores, pero asegúrese de importar los recursos existentes de Data Factory en el repositorio seleccionado y elija Nueva rama.

 * Cree una solicitud de incorporación de cambios para combinar los cambios con la rama de colaboración y publíquela.

 * Si el cliente tenía un entorno de ejecución de integración autohospedado en el ADF eliminado, tendrá que crear una nueva instancia en el nuevo ADF, desinstalar y volver a instalar la instancia en su máquina local o VM con la nueva clave obtenida. Una vez completada la configuración del IR, el cliente deberá cambiar el servicio vinculado para que apunte al nuevo IR y probar la conexión, o se producirá el error **Referencia no válida**.

### <a name="cannot-deploy-to-different-stage-using-automatic-publish-method"></a>No se puede implementar en una fase diferente mediante el método de publicación automática

#### <a name="issue"></a>Problema
El cliente ha seguido todos los pasos necesarios, como instalar el paquete NPM y configurar una fase superior mediante Azure DevOps y ADF, pero la implementación no tiene lugar.

#### <a name="cause"></a>Causa

Aunque los paquetes de npm se pueden consumir de varias maneras, una de las ventajas principales es su consumo a través de una Canalización de Azure. En cada fusión mediante combinación en la rama de colaboración, se puede desencadenar una canalización que primero valide todo el código y, a continuación, exporte la plantilla de ARM a un artefacto de compilación, que una canalización de versión puede consumir. En la canalización inicial, el archivo YAML debe ser válido y estar completo.


#### <a name="resolution"></a>Solución

La siguiente sección no es válida porque la carpeta package.json no es válida.

```
- task: Npm@1
  inputs:
    command: 'custom'
    workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
    customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
  displayName: 'Validate'
```
Debe incluir DataFactory en customCommand, como *"run build validate $(Build.Repository.LocalPath)/DataFactory/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/nombreDeSuFábrica".* Asegúrese de que el archivo YAML generado para la fase superior tenga los artefactos JSON necesarios.

### <a name="git-repository-or-purview-connection-disconnected"></a>Repositorio de Git o conexión de Purview desconectados

#### <a name="issue"></a>Incidencia
Al implementar Data Factory, el repositorio de Git o la conexión purview se desconectan.

#### <a name="cause"></a>Causa
Si ha seleccionado **Inclusión en la plantilla de Resource Manager** para implementar parámetros globales, la fábrica se incluye en la plantilla de ARM. Como resultado, se eliminarán otras propiedades de la fábrica tras la implementación.

#### <a name="resolution"></a>Solución
Anule la selección de **Inclusión en la plantilla de Resource Manager** e implemente parámetros globales con PowerShell como se describe en Parámetros globales en CI/CD. 
 
### <a name="extra--left--displayed-in-published-json-file"></a>Aparece un corchete izquierdo "[" adicional en el archivo JSON publicado

#### <a name="issue"></a>Incidencia
Al publicar ADF con DevOps, aparece otro "[". ADF agrega automáticamente un símbolo "[" izquierdo más en ARMTemplate en DevOps. Verá una expresión como "[[" en el archivo JSON.

#### <a name="cause"></a>Causa
Dado que "[" es un carácter reservado para ARM, se agrega un "[" adicional automáticamente para carácter "[" de escape.

#### <a name="resolution"></a>Solución
Este es el comportamiento normal durante el proceso de publicación de ADF para CI/CD.
 
### <a name="perform-cicd-during--progressqueued-stage-of-pipeline-run"></a>Realización de **CI/CD** durante el progreso o la fase en cola de la ejecución de la canalización

#### <a name="issue"></a>Problema
Le recomendamos que realice CI/CD durante el progreso o la fase en cola de la ejecución de canalización.

#### <a name="cause"></a>Causa
Cuando la canalización está en curso o en cola, primero debe supervisar la canalización y las actividades. A continuación, puede decidir esperar hasta que finalice la canalización o puede cancelar la ejecución de canalización. 
 
#### <a name="resolution"></a>Solución
Puede supervisar la canalización mediante el **SDK**, **Azure Monitor** o el [monitor de ADF](./monitor-visually.md). A continuación, puede seguir los [procedimientos recomendados de CI/CD de ADF](./continuous-integration-deployment.md#best-practices-for-cicd) para obtener más instrucciones. 

### <a name="perform-unit-testing-during-adf-development-and-deployment"></a>Realización de **PRUEBAS UNITARIAS** durante el desarrollo e implementación de ADF

#### <a name="issue"></a>Problema
Le recomendamos que realice pruebas unitarias durante el desarrollo y la implementación de canalizaciones de ADF.

#### <a name="cause"></a>Causa
Durante los ciclos de desarrollo e implementación, le recomendamos que realice una prueba unitaria de la canalización antes de publicarla de forma manual o automática. La automatización de pruebas permite ejecutar más pruebas, en menos tiempo, con capacidad de repetición garantizada. Volver a probar automáticamente todas las canalizaciones de ADF antes de la implementación le proporciona cierta protección contra errores de regresión. Las pruebas automatizadas son un componente clave de los enfoques de desarrollo de software de CI/CD: la inclusión de pruebas automatizadas en canalizaciones de implementación de CI/CD para Azure Data Factory puede mejorar significativamente la calidad. A largo plazo, los artefactos de canalización de ADF probados se reutilizan, lo que ahorra tiempo y costo.  
 
#### <a name="resolution"></a>Solución
Dado que los clientes pueden tener requisitos de pruebas unitarias diferentes con conjuntos de aptitudes distintos, la práctica habitual es seguir los pasos siguientes:

1. Configure el proyecto de CI/CD de Azure DevOps o desarrolle una estrategia de pruebas controlada por el SDK de tipo .NET/PYTHON/REST.
2. En el caso de CI/CD, cree un artefacto de compilación que contenga todos los scripts e implemente recursos en la canalización de versión. Para el enfoque basado en SDK, desarrolle unidades de prueba mediante PyTest en Python, C# **Nunit** mediante el SDK de .NET, etc.
3. Ejecute pruebas unitarias como parte de la canalización de versión o de forma independiente con el SDK de Python/PowerShell/.NET/REST de ADF. 

Por ejemplo, imagine que quiere eliminar duplicados en un archivo y, a continuación, almacenar el archivo seleccionado como tabla en una base de datos. Para probar la canalización, configure un proyecto de CI/CD mediante Azure DevOps.
Configure una fase de canalización de PRUEBA en la que implemente la canalización desarrollada. Configure la fase de PRUEBA para ejecutar pruebas de Python y asegurarse de que los datos de la tabla son los esperados. Si no usa CI/CD, use **Nunit** para desencadenar canalizaciones implementadas con las pruebas que quiera. Una vez que esté satisfecho con los resultados, puede publicar la canalización en una factoría de datos de producción. 


## <a name="next-steps"></a>Pasos siguientes

Para obtener más ayuda para solucionar problemas, pruebe los siguientes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
*  [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
