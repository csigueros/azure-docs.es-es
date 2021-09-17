---
title: 'Servicios de datos habilitados para Azure Arc: notas de la versión'
description: Notas de la versión más recientes
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 08/19/2021
ms.topic: conceptual
ms.openlocfilehash: 5c8918870274cb0ea443dd1f4c93f39c301c6287
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446504"
---
# <a name="release-notes---azure-arc-enabled-data-services"></a>Notas de la versión: servicios de datos habilitados para Azure Arc

En este artículo se resaltan las funcionalidades, las características y las mejoras que se han publicado u optimizado recientemente para los servicios de datos habilitados para Azure Arc.

## <a name="july-2021"></a>Julio de 2021

Esta versión se publica el 30 de julio de 2021.

Esta versión anuncia la disponibilidad general para el [nivel de servicio de uso general](service-tiers.md) de SQL Managed Instance habilitado para Azure Arc en modo de conexión indirecta.

   > [!NOTE]
   > Además, esta versión proporciona los siguientes servicios habilitados para Azure Arc en versión preliminar: 
   > - SQL Managed Instance en modo de conexión directa
   > - [Nivel de servicio Crítico para la empresa](service-tiers.md) de SQL Managed Instance
   > - Hiperescala de PostgreSQL

### <a name="breaking-changes"></a>Cambios importantes

#### <a name="tools"></a>Herramientas

Utilice las herramientas siguientes:
- [Compilación para los participantes del programa Insider de Azure Data Studio](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main).
- [Extensión `arcdata` para la CLI (`az`) de Azure](install-arcdata-extension.md). 


#### <a name="data-controller"></a>Controlador de datos

- Se ha cambiado el parámetro `az arcdata dc create` denominado `--azure-subscription` para usar el parámetro estándar `--subscription`.
- La implementación en AKS HCI requiere una configuración de clase de almacenamiento especial. Consulte los detalles en [Configuración del almacenamiento (Azure Stack HCI con AKS-HCI)](create-data-controller-indirect-cli.md#configure-storage-azure-stack-hci-with-aks-hci).
- Hay un nuevo requisito para permitir conexiones no SSL al exportar datos. Establezca una variable de entorno para suprimir la solicitud interactiva.

### <a name="whats-new"></a>Novedades

#### <a name="data-controller"></a>Controlador de datos

- El modo de conexión directa está en versión preliminar. 

- El modo de conexión directa (versión preliminar) solo está disponible en las siguientes regiones de Azure para esta versión:
   - Centro de EE. UU.
   - Este de EE. UU.
   - Este de EE. UU. 2
   - Oeste de EE. UU. 2
   - Sur de Reino Unido 2
   - Oeste de Europa
   - Norte de Europa
   - Este de Australia
   - Sudeste de Asia
   - Centro de Corea del Sur
   - Centro de Francia

- Actualmente, se pueden agregar usuarios de autenticación básica adicionales a Grafana mediante la experiencia administrativa de Grafana. No se admite la personalización de Grafana modificando los archivos .ini de Grafana.

- Actualmente, no se admite la modificación de la configuración de ElasticSearch y Kibana más allá de lo que está disponible a través de la experiencia administrativa de Kibana. Solo se admite la autenticación básica con un único usuario.
    
- Las métricas personalizadas de Azure Portal se encuentran en versión preliminar.

- La exportación de información de uso/facturación, métricas y registros mediante el comando `az arcdata dc export` requiere omitir la comprobación de SSL por ahora.  Se le pedirá que omita la comprobación de SSL o puede establecer la variable de entorno `AZDATA_VERIFY_SSL=no` para evitar que se le solicite.  Actualmente no hay ninguna manera de configurar un certificado SSL para la API de exportación del controlador de datos.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance habilitado para Azure Arc

- La copia de seguridad automatizada y la restauración a un momento dado se encuentran en versión preliminar.
- Admite la restauración a un momento dado desde una base de datos existente en un SQL Managed Instance habilitado para Azure Arc a una nueva base de datos dentro de la misma instancia.
- Si el valor datetime actual se da como un momento dado en formato UTC, se resuelve como la hora de restauración válida más reciente y restaura la base de datos especificada hasta la última transacción válida.
- Una base de datos se puede restaurar en cualquier momento dado en el que se realizaron las transacciones.
- Con el fin de establecer un objetivo de punto de recuperación específico para un SQL Managed Instance habilitado para Azure Arc, edite el CRD de SQL Managed Instance para establecer la propiedad `recoveryPointObjectiveInSeconds`. Los valores admitidos van de 300 a 600.
- Para deshabilitar las copias de seguridad automatizadas, edite el CRD de la instancia de SQL y establezca la propiedad `recoveryPointObjectiveInSeconds` en 0.

### <a name="known-issues"></a>Problemas conocidos

#### <a name="platform"></a>Plataforma

- Puede crear un controlador de datos, SQL Managed Instance o un grupo de servidores de Hiperescala de PostgreSQL en un clúster de modo de conexión directa con Azure Portal. La implementación del modo de conexión directa no se admite con otras herramientas de servicios de datos habilitadas para Azure Arc. En concreto, no se puede implementar un controlador de datos en modo de conexión directa con ninguna de las herramientas siguientes durante esta versión.
   - Azure Data Studio
   - Herramientas nativas de Kubernetes (`kubectl`)
   - La extensión `arcdata` para la CLI de Azure (`az`)

   En [Creación de un controlador de datos de Azure Arc desde Azure Portal: modo de conexión directa](create-data-controller-direct-azure-portal.md) se explica cómo crear el controlador de datos en el portal.

- Todavía puede usar `kubectl` para crear recursos directamente en un clúster de Kubernetes, pero no se verán reflejados en Azure Portal si está usando el modo de conexión directa.

- En el modo de conexión directa, la carga de uso, las métricas y los registros mediante `az arcdata dc upload` están bloqueados por diseño. El uso se carga automáticamente. La carga del controlador de datos creado en modo de conexión indirecta debe seguir funcionando.
- La carga automática de datos de uso en modo de conexión directa no se realizará correctamente si se usa el proxy mediante `–proxy-cert <path-t-cert-file>`.
- SQL Managed Instance habilitado para Azure Arc e Hiperescala de PostgreSQL habilitada para Azure Arc no tienen la certificación GB18030.
- Actualmente, solo se admite un controlador de datos de Azure Arc por clúster de Kubernetes.

#### <a name="data-controller"></a>Controlador de datos

- Cuando el controlador de datos de Azure Arc se elimina de Azure Portal, se realiza la validación para bloquear la eliminación si hay instancias de SQL Managed Instance habilitado para Azure Arc implementadas en este controlador de datos de Arc. Actualmente, esta validación solo se aplica cuando la eliminación se realiza desde la página Información general del controlador de datos de Azure Arc. 

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Hiperescala de PostgreSQL habilitada para Azure Arc

- En este momento, Hiperescala de PosgreSQL no se puede usar con la versión 1.22 de Kubernetes y versiones posteriores. 
- Las operaciones de copia de seguridad y restauración ya no funcionan en la versión del 30 de julio. Tenga en cuenta que esta es una limitación temporal. Use la versión de junio de 2021 por ahora si necesita crear una copia de seguridad o una restauración. Este problema se corregirá en futuras versiones.

- No es posible habilitar ni configurar la extensión `pg_cron` al mismo tiempo. Debe usar dos comandos para esto. Un comando para habilitarlo y otro para configurarlo. Por ejemplo:

   1. Habilite la extensión:

      ```console
      azdata postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. Reinicie el grupo de servidores.

   1. Configure la extensión:

      ```console
      azdata postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   Si ejecuta el segundo comando antes de que se complete el reinicio, se producirá un error. Si ese es el caso, espere unos instantes más y vuelva a ejecutar el segundo comando.

- Al pasar un valor no válido al parámetro `--extensions` cuando se edita la configuración de un grupo de servidores para habilitar extensiones adicionales, se restablece incorrectamente la lista de extensiones habilitadas al momento de la creación del grupo de servidores e impide que el usuario cree extensiones adicionales. La única solución disponible cuando esto sucede es eliminar el grupo de servidores y volver a implementarlo.

- La restauración a un momento dado no se admite por ahora en el almacenamiento NFS.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance habilitado para Azure Arc

##### <a name="cant-see-resources-in-portal"></a>No se pueden ver los recursos en el portal

- El portal no muestra los recursos de SQL Managed Instance habilitado para Azure Arc creados en la versión de junio. Elimine los recursos de SQL Managed Instance de la vista de lista de grupos de recursos. Es posible que primero tenga que eliminar el recurso de ubicación personalizada.

##### <a name="point-in-time-restorepitr-supportability-and-limitations"></a>Compatibilidad y limitaciones de la restauración a un momento dado (PITR):
    
-  No admite la restauración desde un SQL Managed Instance habilitado para Azure Arc a otro SQL Managed Instance habilitado para Azure Arc.  La base de datos solo se puede restaurar en el mismo SQL Managed Instance habilitado para Arc donde se crearon las copias de seguridad.
-  Actualmente no se admite el cambio de nombre de una base de datos con fines de restauración a un momento dado.
-  Actualmente no hay ningún comando de la CLI ni una API para proporcionar la información de período de tiempo permitida para la restauración a un momento dado. Puede proporcionar un tiempo dentro de un período razonable, desde la hora en que se creó la base de datos, y si la marca de tiempo es válida, la restauración funcionaría. Si la marca de tiempo no es válida, el período de tiempo permitido se proporciona a través de un mensaje de error.
-  No se admite la restauración de una base de datos habilitada para TDE.
-  Actualmente no se puede restaurar una base de datos eliminada.

#####   <a name="automated-backups"></a>Copias de seguridad automatizadas

-  El cambio de nombre de la base de datos detendrá las copias de seguridad automatizadas de esta base de datos.
-  No se aplica ninguna retención. Conservará todas las copias de seguridad siempre que haya espacio disponible. 
-  No se crea una copia de seguridad de las bases de datos de usuario con el modelo de recuperación SIMPLE.
-  No se crea una copia de seguridad del `model` de base de datos del sistema para evitar interferencias con la creación o eliminación de la base de datos. La base de datos se bloquea cuando se realizan operaciones de administración. 
-  Actualmente solo se crea una copia de seguridad de las bases de datos del sistema `master` y `msdb`. Solo se crean copias de seguridad completas cada 12 horas.
-  Solo se crean copias de seguridad de las bases de datos de usuario `ONLINE`.
-  Objetivo de punto de recuperación predeterminado (RPO): 5 minutos. No se puede modificar en la versión actual.
-  Las copias de seguridad se conservan indefinidamente. Para recuperar espacio, elimine manualmente las copias de seguridad.

##### <a name="other-limitations"></a>Otras limitaciones
- Actualmente no se admite la replicación de transacciones.
- El trasvase de registros está bloqueado actualmente.
- Solo se admite la autenticación de SQL Server.

## <a name="june-2021"></a>Junio de 2021

Esta versión preliminar se publicó el 13 de julio de 2021.

### <a name="breaking-changes"></a>Cambios importantes

#### <a name="new-deployment-templates"></a>Nuevas plantillas de implementación

- Las plantillas de implementación nativa de Kubernetes se han modificado para el controlador de datos, el programa previo y SQL Managed Instance. Actualice las plantillas .yaml. [Archivos yaml de ejemplo](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml)

#### <a name="new-azure-cli-extension-for-data-controller-and-azure-arc-enabled-sql-managed-instance"></a>Nueva extensión de la CLI de Azure para el controlador de datos y SQL Managed Instance habilitado para Azure Arc

En esta versión se presenta la extensión `arcdata` a la CLI de Azure. Para agregar la extensión, ejecute el comando siguiente:

```azurecli
az extension add --name arcdata
```

La extensión admite la interacción de la línea de comandos con los recursos del controlador de datos, de SQL Managed Instance y de Hiperescala de PostgreSQL.

Para actualizar los scripts del controlador de datos, reemplace `azdata arc dc...` por `az arcdata dc...`.

Para actualizar los scripts de instancia administrada, reemplace `azdata arc sql mi...` por `az sql mi-arc...`.

Para Hiperescala de PostgreSQL habilitada para Azure Arc, reemplace `azdata arc sql postgres...` por `az postgres arc-server...`.

Además de los parámetros que han existido históricamente en los comandos `azdata`, los mismos comandos de la extensión de la CLI de Azure `arcdata` tienen algunos parámetros nuevos como `--k8s-namespace` y `--use-k8s` que son ahora necesarios. El parámetro `--use-k8s` se usará para diferenciar cuándo se debe enviar el comando a la API de Kubernetes o a la API de ARM. Por ahora, todos los comandos de la CLI de Azure para los servicios de datos habilitados para Arc tienen como destino solo la API de Kubernetes.

Algunas de las formas cortas de los nombres de parámetro (por ejemplo, `--core-limit` como `-cl`) se han quitado o cambiado. Use los nuevos nombres cortos de parámetro o el nombre largo.

El comando `azdata arc dc export` ya no es funcional. En su lugar, use `az arcdata dc export`.

#### <a name="required-property-infrastructure"></a>Propiedad obligatoria: `infrastructure`

La propiedad `infrastructure` es una nueva propiedad necesaria al implementar un controlador de datos. Ajuste los archivos yaml, los scripts azdata/az y las plantillas de ARM para tener en cuenta la especificación de este valor de propiedad. Los valores permitidos son: `alibaba`, `aws`, `azure`, `gpc`, `onpremises` y `other`.

#### <a name="kibana-login"></a>Inicio de sesión de Kibana

Se ha quitado el paquete de seguridad de OpenDistro. Ahora, el inicio de sesión en Kibana se realiza a través de un mensaje genérico de nombre de usuario y contraseña del explorador. Más adelante se proporciona más información sobre cómo configurar opciones de autenticación y autorización adicionales.

#### <a name="crd-version-bump-to-v1beta1"></a>Actualización de la versión de los CRD a `v1beta1`

La versión de todos los CRD se ha actualizado de `v1alpha1` a `v1beta1` en esta versión. Asegúrese de eliminar todos los CRD como parte del proceso de desinstalación si ha implementado una versión de los servicios de datos habilitados para Arc antes de la versión de junio de 2021. Los nuevos CRD implementados con la versión de junio de 2021 tendrán v1beta1 como versión.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance habilitado para Azure Arc

El servicio de copia de seguridad automatizada está disponible y activado de forma predeterminada. Vigile la disponibilidad del espacio en el volumen de copia de seguridad.

### <a name="whats-new"></a>Novedades

En esta versión se presentan las extensiones de la CLI `az` para los servicios de datos habilitados para Azure Arc. Puede obtener más información en [Cambio importante](#breaking-change).

#### <a name="platform"></a>Plataforma

#### <a name="data-controller"></a>Controlador de datos

- Experiencia de usuario simplificada para implementar un controlador de datos en el modo de conexión directa desde Azure Portal. Una vez que un clúster de Kubernetes ha sido habilitado para Arc, puede implementar el controlador de datos completamente desde el portal con el Asistente para crear controladores de datos de Arc en un solo movimiento. Esta implementación también crea la ubicación personalizada y la extensión de servicios de datos habilitados para Arc (programa previo). También puede crear previamente la ubicación personalizada o la extensión y configurar la implementación del controlador de datos para usarlas.
- La nueva propiedad `Infrastructure` es una propiedad necesaria al implementar un controlador de datos de Arc. Esta propiedad será necesaria para fines de facturación. Se proporciona más información en disponibilidad general.
- Varias mejoras de facilidad de uso en la experiencia del usuario del controlador de datos en Azure Portal, incluida la capacidad de ver mejor el estado de implementación de los recursos que se encuentran en el proceso de implementación en el clúster de Kubernetes.
- El controlador de datos carga automáticamente los registros (opcionalmente) y ahora también las métricas en Azure en modo de conexión directa.
- La pila de supervisión (bases de datos o paneles de métricas y registros) ahora se ha empaquetado en su propia definición de recursos personalizados (CRD): `monitors.arcdata.microsoft.com`. Cuando se crea este recurso personalizado, se crean los pods de la pila de supervisión. Cuando se elimina, se eliminan los pods de la pila de supervisión. Cuando se crea el controlador de datos, se crea automáticamente el recurso personalizado de supervisión.
- Nuevas regiones admitidas para el modo de conexión directa (versión preliminar): Este de EE. UU. 2, Oeste de EE. UU. 2, Centro-sur de EE. UU., Sur de Reino Unido, Centro de Francia, Sudeste Asiático, Este de Australia.
- El gráfico de recursos de ubicación personalizada de la hoja de información general ahora muestra los recursos de servicios de datos habilitados para Arc que se implementan en él.
- Se han agregado diagnósticos y soluciones a Azure Portal para el controlador de datos.
- Se ha agregado una nueva propiedad `Observed Generation` a todos los recursos personalizados relacionados con Arc.
- El servicio Administrador de credenciales ahora está incluido y controla la distribución automatizada de certificados a todos los servicios administrados por el controlador de datos.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Hiperescala de PostgreSQL habilitada para Azure Arc

- Hiperescala de PostgreSQL habilitada para Azure Arc ahora admite el almacenamiento NFS.
- Las implementaciones de Hiperescala de PostgreSQL de Azure Arc ahora admiten estrategias de asignación de pods de Kubernetes a nodos con nodeSelector, nodeAffinity y antiafinidad.
- Ahora puede configurar parámetros de proceso (núcleo virtual y memoria) por rol (coordinador o trabajo) al implementar un grupo de servidores de Hiperescala de PostgreSQL o después de la implementación desde Azure Data Studio y desde Azure Portal.
- Desde Azure Portal, ahora puede ver la lista de extensiones de PostgreSQL creadas en el grupo de servidores de Hiperescala de PostgreSQL.
- Desde Azure Portal, puede eliminar grupos de Hiperescala de PostgreSQL habilitada para Arc en un controlador de datos que esté conectado directamente a Azure.


#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance habilitado para Azure Arc

-  Las copias de seguridad automatizadas ahora están habilitadas.
-  Ahora puede restaurar una copia de seguridad de base de datos como una base de datos nueva en la misma instancia de SQL mediante la creación de un nuevo recurso personalizado basado en la definición de recursos personalizados (CRD) `sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`. Para obtener información detallada, consulte la documentación. Todavía no hay ninguna experiencia de interfaz de línea de comandos (`azdata` o `az`), de Azure Portal o de Azure Data Studio para restaurar una base de datos.
-  La versión de los archivos binarios del motor de SQL incluidos en esta versión se alinea con los archivos binarios más recientes que se implementan globalmente en Azure SQL Managed Instance (PaaS en Azure). Esta alineación permite crear copias de seguridad y restauraciones entre Azure SQL Managed Instance PaaS y Azure SQL Managed Instance habilitado para Azure Arc. Más adelante se proporcionan más detalles sobre la compatibilidad.
-  Ahora puede eliminar instancias de SQL Managed Instance de Azure Arc desde Azure Portal en modo de conexión directa.
-  Ahora puede configurar un SQL Managed Instance para tener un plan de tarifa (`GeneralPurpose`, `BusinessCritical`), el tipo de licencia (`LicenseIncluded`, `BasePrice`, que se usa para los precios AHB) y `developer`. No se incurrirá en ningún cargo por el uso de SQL Managed Instance habilitado para Azure Arc hasta la fecha de disponibilidad general (anunciada públicamente como programada para el 30 de julio de 2021) y hasta que actualice a la versión de disponibilidad general del servicio.
-  La extensión `arcdata` para Azure Data Studio ahora tiene parámetros adicionales que se pueden configurar para implementar y editar instancias SQL Managed Instance: habilitar o deshabilitar agente, secreto de inicio de sesión de administrador, anotaciones, etiquetas, anotaciones de servicio, etiquetas de servicio, valores de configuración de SSL/TLS, intercalación, idioma y marcas de seguimiento.
-  Nuevos comandos en `azdata`/tareas de recursos personalizados para configurar grupos de disponibilidad distribuidos. Estos comandos se encuentran en las primeras fases de la versión preliminar; se proporcionará pronto la documentación.

   > [!NOTE]
   > Estos comandos se migrarán a la extensión `az arcdata`.

-  `azdata arc dc export` está desusada. Se reemplaza por `az arcdata dc export` en la extensión `arcdata` para la CLI de Azure (`az`). Usa un enfoque diferente para exportar los datos. Ya no se conecta directamente a la API del controlador de datos. En su lugar, crea una tarea de exportación basada en la definición de recursos personalizados (CRD) `exporttasks.tasks.arcdata.microsoft.com`. El recurso personalizado de la tarea de exportación que se crea impulsa un flujo de trabajo para generar un paquete descargable. La CLI de Azure espera a que finalice esta tarea y, después, recupera la dirección URL segura del estado del recurso personalizado de la tarea para descargar el paquete.
-  Compatibilidad con el uso de clases de almacenamiento basadas en NFS.
- Se han agregado diagnósticos y soluciones a Azure Portal para SQL Managed Instance de Arc.

## <a name="may-2021"></a>Mayo de 2021

Esta versión preliminar se publicó el 2 de junio de 2021.

Como característica en versión preliminar, la tecnología que se presenta en este artículo está sujeta a los [términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="breaking-change"></a>Cambio importante

- Se han modificado las plantillas de implementación nativa de Kubernetes. Actualice las plantillas .yml.
    - Se han actualizado las plantillas para el controlador de datos, el programa previo y SQL Managed Instance: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574).
    - Se han actualizado las plantillas para Hiperescala de PostgreSQL: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574).

### <a name="whats-new"></a>Novedades

#### <a name="platform"></a>Plataforma

- Cree y elimine el controlador de datos, SQL Managed Instance y los grupos de servidores de Hiperescala de PostgreSQL desde Azure Portal.
- Valide las acciones del portal al eliminar servicios de datos de Azure Arc. Por ejemplo, el portal alerta cuando intenta eliminar el controlador de datos cuando hay instancias de SQL Managed Instance implementadas mediante el controlador de datos.
- Cree perfiles de configuración personalizados para admitir la configuración personalizada al implementar el controlador de datos habilitado para Arc mediante Azure Portal.
- De manera opcional, cargue automáticamente los registros en el área de trabajo de Azure Log Analytics en el modo de conexión directa.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Hiperescala de PostgreSQL habilitada para Azure Arc

Esta versión presenta las siguientes características o funcionalidades:

- Elimine una instancia de Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal cuando su controlador de datos se haya configurado para el modo de conexión directa.
- Implemente Hiperescala de PostgreSQL habilitada para Azure Arc desde la página de implementación de Azure Database for Postgres en Azure Portal. Consulte [Selección de la opción de implementación de Azure Database for PostgreSQL: Microsoft Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer).
- Especifique las clases de almacenamiento y las extensiones de Postgres al implementar Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal.
- Reduzca el número de nodos de trabajo en su Hiperescala de PostgreSQL habilitada para Azure Arc. Puede realizar esta operación (conocida como reducción horizontal, en oposición al escalado horizontal, al aumentar el número de nodos de trabajo) desde la línea de comandos `azdata`.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance habilitado para Azure Arc

- La nueva [extensión de la CLI de Azure](/cli/azure/azure-cli-extensions-overview) para SQL Managed Instance habilitado para Arc tiene los mismos comandos que `az sql mi-arc <command>`. Todos los comandos de SQL Managed Instance habilitado para Arc se encuentran en `az sql mi-arc`. Todos los comandos de `azdata` relacionados con Arc estarán en desuso y se moverán a la CLI de Azure en una versión futura.

   Para agregar la extensión:

   ```azurecli
   az extension add --source https://azurearcdatacli.blob.core.windows.net/cli-extensions/arcdata-0.0.1-py2.py3-none-any.whl -y
   az sql mi-arc --help
   ```

- Desencadene manualmente una conmutación por error de mediante Transact-SQL. Ejecute los comandos siguientes en orden:

   1. En la conexión del punto de conexión de la réplica principal:

      ```sql
       ALTER AVAILABILITY GROUP current SET (ROLE = SECONDARY);
      ```

   1. En la conexión del punto de conexión de la réplica secundaria:

      ```sql
      ALTER AVAILABILITY GROUP current SET (ROLE = PRIMARY);
      ```

- El comando `BACKUP` de Transact-SQL está bloqueado, a menos que use el valor `COPY_ONLY`. Esto admite la funcionalidad de restauración a un momento dado.

## <a name="april-2021"></a>Abril de 2021

Esta versión preliminar se ha publicado el 29 de abril de 2021.

### <a name="whats-new"></a>Novedades

En esta sección se describen las nuevas características incorporadas o habilitadas en esta versión.

#### <a name="platform"></a>Plataforma

- Los clústeres de conexión directa cargan automáticamente la información de telemetría de Azure.

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>Hiperescala de PostgreSQL habilitada para Azure Arc

- Hiperescala de PostgreSQL habilitada para Azure Arc ahora se admite en el modo de conexión directa. Ahora puede implementar Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Market Place en Azure Portal.
- Hiperescala de PostgreSQL habilitada para Azure Arc incluye la extensión Citus 10.0, que incorpora almacenamiento de tablas en columnas.
- Hiperescala de PostgreSQL habilitada para Azure Arc ahora admite la administración completa de usuarios y roles.
- Hiperescala de PostgreSQL habilitada para Azure Arc ahora admite extensiones adicionales con `Tdigest` y `pg_partman`.
- Hiperescala de PostgreSQL habilitada para Azure Arc ahora admite la configuración de núcleo virtual y memoria por rol de la instancia de PostgreSQL en el grupo de servidores.
- Hiperescala de PostgreSQL habilitada para Azure Arc ahora admite la configuración de motor o servidor de base de datos por rol de la instancia de PostgreSQL en el grupo de servidores.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance habilitado para Azure Arc

- Restaure una base de datos a SQL Managed Instance con tres réplicas para que se agregue automáticamente al grupo de disponibilidad.
- Conéctese a un punto de conexión secundario de solo lectura en las instancias de SQL Managed Instance implementadas con tres réplicas. Use `azdata arc sql endpoint list` para ver el punto de conexión secundario de solo lectura.

## <a name="march-2021"></a>Marzo de 2021

La versión de marzo de 2021 se introdujo inicialmente el 5 de abril de 2021 y las fases finales de la versión se completaron el 9 de abril de 2021.

Número de versión de la CLI de datos de Azure (`azdata`): 20.3.2. Puede instalar `azdata` desde [Instalación de Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

### <a name="data-controller"></a>Controlador de datos

- Implemente el controlador de datos de servicios de datos habilitados para Azure Arc en modo de conexión directa desde el portal. Comience desde [Implementación del controlador de datos: requisitos previos para el modo de conexión directa](create-data-controller-direct-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Hiperescala de PostgreSQL habilitada para Azure Arc

Las dos definiciones de recurso personalizado (CRD) para PostgreSQL se han consolidado en una sola. Consulte la tabla siguiente.

|Release |CRD |
|-----|-----|
|Febrero de 2021 y anteriores| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|A partir de marzo de 2021 | postgresqls.arcdata.microsoft.com

Eliminará las definiciones de recurso personalizado anteriores a medida que limpie las instalaciones anteriores. Consulte [Limpieza de instalaciones anteriores](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance habilitado para Azure Arc

- Ahora puede crear una instancia administrada de SQL desde Azure Portal en el modo de conexión directa.

- Ahora puede restaurar una base de datos a SQL Managed Instance con tres réplicas para que se agregue automáticamente al grupo de disponibilidad.

- Ahora puede conectarse a un punto de conexión secundario de solo lectura en las instancias de SQL Managed Instance implementadas con tres réplicas. Use `azdata arc sql endpoint list` para ver el punto de conexión secundario de solo lectura.

## <a name="february-2021"></a>Febrero de 2021

### <a name="new-capabilities-and-features"></a>Nuevas características y funcionalidades

Número de versión de la CLI de datos de Azure (`azdata`): 20.3.1. Puede instalar `azdata` desde [Instalación de Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Otras actualizaciones incluyen:

- SQL Managed Instance habilitado para Azure Arc
   - Alta disponibilidad con grupos de disponibilidad AlwaysOn

- Hiperescala de PostgreSQL habilitada para Azure Arc (Azure Data Studio):
   - En la página de información general se muestra el estado del grupo de servidores desglosado por nodo.
   - Una nueva página de propiedades muestra más detalles sobre el grupo de servidores.
   - Configuración de parámetros del motor de Postgres desde la página **Node Parameters** (Parámetros del nodo).

## <a name="january-2021"></a>Enero de 2021

### <a name="new-capabilities-and-features"></a>Nuevas características y funcionalidades

Número de versión de la CLI de datos de Azure (`azdata`): 20.3.0. Puede instalar `azdata` desde [Instalación de Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Otras actualizaciones incluyen:
- Portal localizado disponible en 17 idiomas nuevos.
- Cambios menores en los archivos .yaml nativos de Kube.
- Nuevas versiones de Grafana y Kibana.
- Problemas con entornos de Python cuando se usa azdata en cuadernos resueltos en Azure Data Studio.
- La extensión pg_audit ahora está disponible para Hiperescala de PostgreSQL.
- Ya no es necesario un identificador de copia de seguridad cuando se realiza una restauración completa de una base de datos de Hiperescala de PostgreSQL.
- El estado (estado de mantenimiento) se indica para cada una de las instancias de PostgreSQL que forman un grupo de servidores

   En versiones anteriores, el estado se agregaba en el nivel de grupo de servidores y no en el nivel de nodo de PostgreSQL.

- Las implementaciones de PostgreSQL respetan los parámetros de tamaño de volumen indicados en los comandos de creación.
- Ahora se respetan los parámetros de versión de motor al editar un grupo de servidores.
- Ha cambiado la convención de nomenclatura de los pods para Hiperescala de PostgreSQL habilitada para Azure Arc.

    Ahora tiene el formato `ServergroupName{c, w}-n`. Por ejemplo, un grupo de servidores con tres nodos, un nodo de coordinación y dos nodos de trabajo, se representa como:
   - `Postgres01c-0` (nodo de coordinación)
   - `Postgres01w-0` (nodo de trabajo)
   - `Postgres01w-1` (nodo de trabajo)

## <a name="december-2020"></a>Diciembre de 2020

### <a name="new-capabilities--features"></a>Nuevas características y funcionalidades

Número de versión de la CLI de datos de Azure (`azdata`): 20.2.5. Puede instalar `azdata` desde [Instalación de Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Vea los puntos de conexión para SQL Managed Instance e Hiperescala de PostgreSQL mediante la CLI de Azure Data (`azdata`) con los comandos `azdata arc sql endpoint list` y `azdata arc postgres endpoint list`.

Edite solicitudes y límites de recursos de SQL Managed Instance (CPU y núcleos de CPU) mediante Azure Data Studio.

Hiperescala de PostgreSQL habilitada para Azure Arc ahora admite la restauración a un momento dado, además de la restauración de copia de seguridad completa para las versiones 11 y 12 de PostgreSQL. La funcionalidad de restauración a un momento dado le permite indicar una fecha y hora específicas en las que realizar la restauración.

Ha cambiado la convención de nomenclatura de los pods para Hiperescala de PostgreSQL habilitada para Azure Arc. Ahora tiene el formato: NombreDeGrupoDeServidores{r, s}-_n_. Por ejemplo, un grupo de servidores con tres nodos, un nodo de coordinación y dos nodos de trabajo, se representa como:
- `postgres02r-0` (nodo de coordinación)
- `postgres02s-0` (nodo de trabajo)
- `postgres02s-1` (nodo de trabajo)

### <a name="breaking-change"></a>Cambio importante

#### <a name="new-resource-provider"></a>Nuevo proveedor de recursos

En esta versión se incluye un [proveedor de recursos](../../azure-resource-manager/management/azure-services-resource-providers.md) actualizado denominado `Microsoft.AzureArcData`. Antes de poder usar esta característica, debe registrar este proveedor de recursos.

Para registrar este proveedor de recursos:

1. En Azure Portal, seleccione **Suscripciones**.
2. Elija una suscripción
3. En **Configuración**, seleccione **Proveedores de recursos**.
4. Busque `Microsoft.AzureArcData` y seleccione **Registrar**.

Puede revisar los pasos detallados en [Proveedores y tipos de recursos de Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Este cambio también quita todos los recursos de Azure existentes que haya cargado en Azure Portal. Para usar el proveedor de recursos, debe actualizar el controlador de datos y usar la CLI de `azdata` más reciente.

### <a name="platform-release-notes"></a>Notas de la versión de la plataforma

#### <a name="direct-connectivity-mode"></a>Modo de conectividad directa

En esta versión se presenta el modo de conectividad directa. El modo de conectividad directa permite al controlador de datos cargar automáticamente la información de uso en Azure. Como parte de la carga de uso, el recurso de controlador de datos de Arc se crea de forma automática en el portal, si todavía no se ha creado mediante la carga de `azdata`.

Puede especificar la conectividad directa al crear el controlador de datos. En el ejemplo siguiente se crea un controlador de datos con `az arcdata dc create` denominado `arc` mediante el modo de conectividad directa (`connectivity-mode direct`). Antes de ejecutar el ejemplo, reemplace `<subscription id>` por el identificador de la suscripción.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>Octubre de 2020

Número de versión de la CLI de datos de Azure (`azdata`): 20.2.3. Puede instalar `azdata` desde [Instalación de Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

### <a name="breaking-changes"></a>Últimos cambios

Esta versión presenta los siguientes cambios importantes:

* En la definición de recursos personalizada (CRD) de PostgreSQL, el término `shards` cambia a `workers`. Este término (`workers`) coincide con el nombre del parámetro de la línea de comandos.

* `azdata arc postgres server delete` solicita confirmación antes de eliminar una instancia de PostgreSQL. Use `--force` para omitir la solicitud.

### <a name="additional-changes"></a>Cambios adicionales

* Se ha agregado un nuevo parámetro opcional a `azdata arc postgres server create` denominado `--volume-claim mounts`. El valor es una lista separada por comas de montajes de notificación de volumen. Un montaje de notificación de volumen es un par de tipo de volumen y nombre de PVC. El único tipo de volumen admitido actualmente es `backup`. En PostgreSQL, cuando el tipo de volumen es `backup`, PVC se monta en `/mnt/db-backups`. Esto permite compartir copias de seguridad entre instancias de PostgresSQL para que la copia de seguridad de una instancia de PostgresSQL pueda restaurarse en otra.

* Nuevos nombres cortos para las definiciones de recursos personalizados de PostgresSQL:
  * `pg11`
  * `pg12`
* La carga de telemetría proporciona al usuario:
   * El número de puntos cargados en Azure, o bien
   * Si no se ha cargado ningún dato en Azure, una solicitud para volver a intentarlo.
* `az arcdata dc debug copy-logs` ahora también hace lecturas en la carpeta `/var/opt/controller/log` y recopila los registros del motor de PostgreSQL en Linux.
*   Visualización de un indicador de funcionamiento durante la creación y restauración de copias de seguridad con Hiperescala de PostgreSQL.
* `azdata arc postrgres backup list` ahora incluye información de tamaño de copia de seguridad.
* La propiedad de nombre de administrador de SQL Managed Instance se ha agregado a la columna derecha de la hoja de información general en Azure Portal.
* Azure Data Studio admite la configuración del número de nodos de trabajo, el núcleo virtual y la configuración de memoria para Hiperescala de PostgreSQL.
* La versión preliminar admite la copia de seguridad y restauración de las versiones 11 y 12 de PostgreSQL.

## <a name="september-2020"></a>Septiembre de 2020

Los servicios de datos habilitados para Azure Arc se publican como versión preliminar pública. Los servicios de datos habilitados para Arc permiten administrar los servicios de datos en cualquier lugar.

- Instancia administrada de SQL
- Hiperescala de PostgreSQL

Para obtener instrucciones, consulte [¿Qué son los servicios de datos habilitados para Azure Arc?](overview.md)

## <a name="next-steps"></a>Pasos siguientes

> **¿Solo desea realizar algunas pruebas?**
> Empiece a trabajar rápidamente con el [Inicio rápido de Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) en AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure.

- [Instalación de las herramientas de cliente](install-client-tools.md)
- [Creación del controlador de datos de Azure Arc](create-data-controller.md) (requiere primero la instalación de las herramientas de cliente).
- [Creación de una instancia administrada de Azure SQL en Azure Arc](create-sql-managed-instance.md) (requiere primero la creación de un controlador de datos de Azure Arc).
- [Creación de un grupo de servidores de Hiperescala de Azure Database for PostgreSQL en Azure Arc](create-postgresql-hyperscale-server-group.md) (requiere primero la creación de un controlador de datos de Azure Arc).
- [Proveedores de recursos para servicios de Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
