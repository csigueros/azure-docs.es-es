---
title: Creación de un controlador de datos mediante las herramientas de Kubernetes
description: Creación de un controlador de datos mediante las herramientas de Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 9f7f5569d5381a7d1ff4d7ebbeac535105f22c93
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195064"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Creación de un controlador de datos de Azure Arc mediante las herramientas de Kubernetes


## <a name="prerequisites"></a>Requisitos previos

Revise el tema [Creación del controlador de datos de Azure Arc](create-data-controller.md) para obtener información general.

Para crear el controlador de datos de Azure Arc mediante las herramientas de Kubernetes, debe tener instaladas dichas herramientas.  En los ejemplos de este artículo se usará `kubectl`, pero se podrían emplear enfoques similares con otras herramientas de Kubernetes como el panel de Kubernetes, `oc` o `helm` si está familiarizado con esas herramientas y los formatos YAML y JSON de Kubernetes.

[Instalación de la herramienta kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Algunos de los pasos para crear el controlador de datos de Azure Arc que se indican a continuación requieren permisos de administrador de clústeres de Kubernetes.  Si no es un administrador de clústeres de Kubernetes, deberá pedirle al administrador de clústeres de Kubernetes que realice estos pasos en su nombre.

### <a name="cleanup-from-past-installations"></a>Limpieza de instalaciones anteriores

Si instaló en el pasado el controlador de datos de Azure Arc en el mismo clúster y lo eliminó, es posible que haya algunos objetos de nivel de clúster que siga siendo necesario eliminar. Ejecute los siguientes comandos para eliminar objetos de nivel de clúster del controlador de datos de Azure Arc:

```console
# Cleanup azure arc data service artifacts

# Note: not all of these objects will exist in your environment depending on which version of the Arc data controller was installed

# Custom resource definitions (CRD)
kubectl delete crd datacontrollers.arcdata.microsoft.com
kubectl delete crd postgresqls.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com
kubectl delete crd dags.sql.arcdata.microsoft.com
kubectl delete crd exporttasks.tasks.arcdata.microsoft.com
kubectl delete crd monitors.arcdata.microsoft.com

# Cluster roles and role bindings
kubectl delete clusterrole arcdataservices-extension
kubectl delete clusterrole arc:cr-arc-metricsdc-reader
kubectl delete clusterrole arc:cr-arc-dc-watch
kubectl delete clusterrole cr-arc-webhook-job

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get clusterrolebinding'
kubectl delete clusterrolebinding {namespace}:crb-arc-metricsdc-reader
kubectl delete clusterrolebinding {namespace}:crb-arc-dc-watch
kubectl delete clusterrolebinding crb-arc-webhook-job

# API services
# Up to May 2021 release
kubectl delete apiservice v1alpha1.arcdata.microsoft.com
kubectl delete apiservice v1alpha1.sql.arcdata.microsoft.com

# June 2021 release
kubectl delete apiservice v1beta1.arcdata.microsoft.com
kubectl delete apiservice v1beta1.sql.arcdata.microsoft.com

# GA/July 2021 release
kubectl delete apiservice v1.arcdata.microsoft.com
kubectl delete apiservice v1.sql.arcdata.microsoft.com

# Substitute the name of the namespace the data controller was deployed in into {namespace}.  If unsure, get the name of the mutatingwebhookconfiguration using 'kubectl get mutatingwebhookconfiguration'
kubectl delete mutatingwebhookconfiguration arcdata.microsoft.com-webhook-{namespace}

```

## <a name="overview"></a>Información general

Para crear el controlador de datos de Azure Arc, hay que seguir estos pasos generales:

   > [!IMPORTANT]
   > Para seguir algunos de los pasos que figuran a continuación es necesario contar con permisos de administrador de clústeres de Kubernetes.

1. Cree las definiciones de recursos personalizadas para el controlador de datos de Azure Arc, la instancia administrada de Azure SQL e Hiperescala de PostgreSQL. 
1. Cree un espacio de nombres en el que se generará el controlador de datos. 
1. Cree el servicio de arranque, incluido el conjunto de réplicas, la cuenta de servicio, el rol y el enlace de rol.
1. Cree un secreto para el nombre de usuario y la contraseña del administrador del controlador de datos.
1. Cree el trabajo de implementación de webhook, el rol de clúster y el enlace de rol de clúster. 
1. Cree el controlador de datos.


## <a name="create-the-custom-resource-definitions"></a>Creación de las definiciones de recursos personalizadas

Ejecute el siguiente comando para crear las definiciones de recursos personalizadas.  

   > [!IMPORTANT]
   > Se necesitan permisos de administrador de clústeres de Kubernetes.

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Creación de un espacio de nombres en el que se generará el controlador de datos

Ejecute un comando similar al siguiente para crear un espacio de nombres dedicado en el que se generará el controlador de datos.  En este ejemplo y en el resto de los ejemplos de este artículo se usará un nombre de espacio de nombres de `arc`. Si decide usar otro, utilice el mismo nombre en todos.

```console
kubectl create namespace arc
```
Si va a usar OpenShift, tendrá que editar las anotaciones `openshift.io/sa.scc.supplemental-groups` y `openshift.io/sa.scc.uid-range` en el espacio de nombres usando `kubectl edit namespace <name of namespace>`.  Cambie estas anotaciones ya existentes para que coincidan con este UID y estos Ids. fsGroup/rangos _específicos_.

```console
openshift.io/sa.scc.supplemental-groups: 1000700001/10000
openshift.io/sa.scc.uid-range: 1000700001/10000
```

Si otras personas que no son administradores de clústeres van a usar este espacio de nombres, se recomienda crear un rol de administrador de espacio de nombres y conceder ese rol a esos usuarios a través de un enlace de rol.  El administrador del espacio de nombres debe tener permisos completos en el espacio de nombres.  Puede encontrar roles más pormenorizados y enlaces de rol de ejemplo en el [repositorio Azure Arc GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/rbac).

## <a name="create-the-bootstrapper-service"></a>Creación del servicio de arranque

El servicio de arranque controla las solicitudes entrantes para crear, editar y eliminar recursos personalizados, como un controlador de datos, instancias administradas de SQL o grupos de servidores de Hiperescala de PostgreSQL.

Ejecute el siguiente comando para crear un servicio de arranque, una cuenta de servicio para el servicio de arranque, y un rol y un enlace de rol para la cuenta de servicio de arranque.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Compruebe que el pod de arranque se está ejecutando con el siguiente comando.  Es posible que tenga que ejecutarlo varias veces hasta que el estado cambie a `Running`.

```console
kubectl get pod --namespace arc
```

El archivo de plantilla bootstrapper.yaml tiene como valor predeterminado extraer la imagen del contenedor de arranque de Microsoft Container Registry (MCR).  Si su entorno no tiene acceso directo a Microsoft Container Registry, puede hacer lo siguiente:
- Siga los pasos para [extraer las imágenes de contenedor de Microsoft Container Registry y enviarlas a un registro de contenedor privado](offline-deployment.md).
- [Cree un secreto de extracción de imagen](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) para el registro de contenedor privado.
- Agregue un secreto de extracción de imagen al contenedor de arranque. Consulte el ejemplo siguiente.
- Cambie la ubicación de la imagen de arranque. Consulte el ejemplo siguiente.

En el ejemplo siguiente se da por hecho que ha creado un nombre de secreto de extracción de imagen `arc-private-registry`.

```yaml
#Just showing only the relevant part of the bootstrapper.yaml template file here
    spec:
      serviceAccountName: sa-bootstrapper
      nodeSelector:
        kubernetes.io/os: linux
      imagePullSecrets:
      - name: arc-private-registry #Create this image pull secret if you are using a private container registry
      containers:
      - name: bootstrapper
        image: mcr.microsoft.com/arcdata/arc-bootstrapper:v1.0.0_2021-07-30 #Change this registry location if you are using a private container registry.
        imagePullPolicy: Always
```

## <a name="create-a-secret-for-the-kibanagrafana-dashboards"></a>Creación de un secreto para los paneles de Kibana o Grafana

El nombre de usuario y la contraseña se usan para autenticarse en los paneles de Kibana y Grafana como administrador.  Elija una contraseña segura y compártala solo con aquellos usuarios que necesiten tener estos privilegios.

Un secreto de Kubernetes se almacena como una cadena codificada en Base64, una para el nombre de usuario y otra para la contraseña.

Puede usar una herramienta en línea para codificar en Base64 el nombre de usuario y la contraseña que desee, o bien puede usar las herramientas integradas de la CLI, en función de la plataforma.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

Una vez que haya codificado el nombre de usuario y la contraseña, puede crear un archivo basado en el [archivo de plantilla](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) y reemplazar los valores de nombre de usuario y contraseña por los suyos propios.

Después, ejecute el siguiente comando para crear el secreto.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-webhook-deployment-job-cluster-role-and-cluster-role-binding"></a>Creación del trabajo de implementación de webhook, el rol de clúster y el enlace de rol de clúster

En primer lugar, cree una copia del [archivo de plantilla](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/web-hook.yaml) localmente en el equipo para que pueda modificar algunos de los valores de la configuración.

Edite el archivo y reemplace `{{namespace}}` en todos los lugares por el nombre del espacio de nombres que ha creado en el paso anterior. **Guarde el archivo.**

Ejecute el siguiente comando para crear el rol de clúster y los enlaces de rol de clúster.  

   > [!IMPORTANT]
   > Se necesitan permisos de administrador de clústeres de Kubernetes.

```console
kubectl create -n arc -f <path to the edited template file on your computer>
```


## <a name="create-the-data-controller"></a>Creación del controlador de datos

Ya puede crear su propio controlador de datos.

En primer lugar, cree una copia del [archivo de plantilla](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) localmente en el equipo para que pueda modificar algunos de los valores de la configuración.

Edite los valores siguientes, según sea necesario:

**OBLIGATORIO**
- **location**: cámbielo para que sea la ubicación de Azure donde se almacenarán los _metadatos_ sobre el controlador de datos.  Puede ver la lista de ubicaciones de Azure disponibles en el artículo de [información general sobre controladores de datos](create-data-controller.md).
- **resourceGroup**: el grupo de recursos de Azure donde desea crear el recurso de Azure del controlador de datos en Azure Resource Manager.  Normalmente, este grupo de recursos ya debería existir, pero no se necesita hasta el momento en que se cargan los datos en Azure.
- **subscription**: el GUID de suscripción de Azure de la suscripción en la que desea crear los recursos de Azure.

**SE RECOMIENDA REVISAR Y POSIBLEMENTE CAMBIAR LOS VALORES PREDETERMINADOS**
- **storage..className**: la clase de almacenamiento que se va a utilizar para los archivos de registro y datos del controlador de datos.  Si no sabe con seguridad cuáles son las clases de almacenamiento disponibles en el clúster de Kubernetes, puede ejecutar el siguiente comando: `kubectl get storageclass`.  El valor predeterminado es `default`, que da por sentado que hay una clase de almacenamiento que existe y que se denomina `default`, y no que hay una clase de almacenamiento que _es_ la predeterminada.  Nota: Hay dos valores de configuración de className que debe establecer en la clase de almacenamiento deseada: una para los datos y otra para los registros.
- **serviceType**: cambie el tipo de servicio a `NodePort` si no usa un equilibrador de carga.
- **Seguridad:** para Red Hat OpenShift en Azure o Red Hat OpenShift Container Platform, reemplace la configuración `security:` por los siguientes valores en el archivo .yaml del controlador de datos.

```yml
  security:
    allowDumps: false
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
```

**OPCIONAL**
- **name**: el nombre predeterminado del controlador de datos es `arc`, pero puede cambiarlo si lo desea.
- **displayName**: establézcalo en el mismo valor que el atributo name situado en la parte superior del archivo.
- **registry**: Microsoft Container Registry es el valor predeterminado.  Si extrae las imágenes de Microsoft Container Registry y las [inserta en un registro de contenedor privado](offline-deployment.md), escriba aquí la dirección IP o el nombre DNS del registro.
- **dockerRegistry**: el secreto de extracción de imagen que se va a usar para extraer las imágenes de un registro de contenedor privado, si es necesario.
- **repository**: el repositorio predeterminado de Microsoft Container Registry es `arcdata`.  Si usa un registro de contenedor privado, escriba la ruta de acceso de la carpeta o del repositorio que incluyen las imágenes de contenedor de servicios de datos habilitados para Azure Arc.
- **imageTag**: la etiqueta de versión más reciente actual está predeterminada en la plantilla, pero puede cambiarla si desea utilizar una versión anterior.

En el ejemplo siguiente se muestra un archivo .yaml del controlador de datos completado. Actualice el ejemplo para su entorno, en función de sus requisitos, y la información anterior.

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sa-mssql-controller
---
apiVersion: arcdata.microsoft.com/v1
kind: DataController
metadata:
  generation: 1
  name: arc-dc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    dockerRegistry: arc-private-registry #Create a registry secret named 'arc-private-registry' if you are going to pull from a private registry instead of MCR.
    serviceAccount: sa-arc-controller
  docker:
    imagePullPolicy: Always
    imageTag: v1.0.0_2021-07-30
    registry: mcr.microsoft.com
    repository: arcdata
  infrastructure: other #Must be a value in the array [alibaba, aws, azure, gcp, onpremises, other]
  security:
    allowDumps: true #Set this to false if deploying on OpenShift
    allowNodeMetricsCollection: true #Set this to false if deploying on OpenShift
    allowPodMetricsCollection: true #Set this to false if deploying on OpenShift
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer # Modify serviceType based on your Kubernetes environment
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: indirect
      location: eastus # Choose a different Azure location if you want
      resourceGroup: <your resource group>
      subscription: <your subscription GUID>
    controller:
      displayName: arc-dc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 10Gi
```

Guarde el archivo editado en el equipo local y ejecute el siguiente comando para crear el controlador de datos:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Supervisión del estado de creación

La creación del controlador tardará unos minutos en completarse. Puede supervisar el progreso en otra ventana de terminal con los siguientes comandos:

> [!NOTE]
>  En los siguientes comandos de ejemplo se da por hecho que ha creado un controlador de datos y un espacio de nombres de Kubernetes con el nombre `arc`.  Si ha usado otro nombre de controlador de datos o de espacio de nombres, puede reemplazar `arc` por ese nombre.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

También puede comprobar el estado de creación de un pod determinado ejecutando un comando como el siguiente.  Esto es especialmente útil para solucionar problemas.

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Solución de problemas de creación

Si tiene problemas con la creación, consulte la [guía de solución de problemas](troubleshoot-guide.md).

## <a name="next-steps"></a>Pasos siguientes

- [Creación de una instancia de SQL Managed Instance mediante las herramientas nativas de Kubernetes](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Creación de un grupo de servidores de Hiperescala de PostgreSQL mediante herramientas nativas de Kubernetes](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
