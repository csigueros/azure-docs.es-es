---
title: Ejecución de contenedores con privilegios en un clúster de Red Hat OpenShift de Azure | Microsoft Docs
description: Ejecute contenedores con privilegios para supervisar la seguridad y el cumplimiento.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, red hat
ms.openlocfilehash: 167a031098feb7764b205b81bcf63f68422be10e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308212"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Ejecución de contenedores con privilegios en un clúster de Red Hat OpenShift en Azure

> [!IMPORTANT]
> Red Hat OpenShift en Azure 3.11 se retirará el 30 de junio de 2022. La compatibilidad con la creación de nuevos clústeres de Red Hat OpenShift en Azure 3.11 continúa hasta el 30 de noviembre de 2020. Después de la retirada, los clústeres de Red Hat OpenShift en Azure 3.11 que queden se cerrarán para evitar puntos vulnerables de seguridad.
> 
> Siga esta guía para [crear un clúster de la versión 4 de Red Hat OpenShift en Azure](tutorial-create-cluster.md).
> Si tiene alguna pregunta específica, póngase en [contacto con nosotros](mailto:arofeedback@microsoft.com).

No puede ejecutar contenedores con privilegios arbitrarios en clústeres de Red Hat OpenShift de Azure.
Se permite que dos soluciones de cumplimiento y supervisión de la seguridad se ejecuten en clústeres de ARO.
En este documento se describen las diferencias con respecto a la documentación genérica de implementación de OpenShift de los proveedores de productos de seguridad.


Antes de seguir las instrucciones del proveedor, lea estas instrucciones.
Los títulos de sección de los pasos específicos del producto que se indican a continuación hacen referencia directa a los títulos de sección de la documentación de los proveedores.

## <a name="before-you-begin"></a>Antes de empezar

En la documentación de la mayoría de los productos de seguridad se supone que tiene privilegios de administrador de clústeres.
Los administradores de clientes no tienen todos los privilegios en Red Hat OpenShift de Azure. Los permisos necesarios para modificar los recursos del clúster son limitados.

En primer lugar, ejecute `oc get scc` para asegurarse de que el usuario haya iniciado sesión en el clúster como administrador de clientes. Todos los usuarios que son miembros del grupo de administradores de clientes tienen permisos para ver las restricciones de contexto de seguridad (SCC) del clúster.

A continuación, asegúrese de que la versión binaria `oc` sea `3.11.154`.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Pasos específicos del producto para Aqua Security
Las instrucciones básicas que se van a modificar se pueden encontrar en la [documentación de implementación de Aqua Security](https://docs.aquasec.com/docs/openshift-red-hat). Los pasos que se describen aquí se ejecutarán en combinación con la documentación de implementación de Aqua.

El primer paso consiste en anotar los SCC necesarios que se van a actualizar. Estas anotaciones impiden que el pod de sincronización del clúster revierta los cambios a estos SSC.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Paso 1: Preparación de los requisitos previos
Recuerde iniciar sesión en el clúster con el rol de administrador de clientes de ARO en lugar de administrador de clústeres.

Cree el proyecto y la cuenta de servicio.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

En lugar de asignar el rol de lector del clúster, asigne el rol de clúster de administrador de clientes a la cuenta de Aqua con el siguiente comando.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Siga el resto de instrucciones del paso 1.  Estas instrucciones describen cómo configurar el secreto del registro de Aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Paso 2: Implementación del servidor, la base de datos y la puerta de enlace de Aqua
Siga los pasos que se proporcionan en la documentación de Aqua para instalar aqua-console.yaml.

Modifique el archivo `aqua-console.yaml` proporcionado.  Quite los dos objetos principales con la etiqueta `kind: ClusterRole` y `kind: ClusterRoleBinding`.  Estos recursos no se crearán, ya que el administrador de clientes no tiene permiso en este momento para modificar objetos `ClusterRole` y `ClusterRoleBinding`.

La segunda modificación se realizará en la parte `kind: Route` de `aqua-console.yaml`. Reemplace el siguiente YAML en el objeto `kind: Route` del archivo `aqua-console.yaml`.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Siga el resto de instrucciones.

### <a name="step-3-login-to-the-aqua-server"></a>Paso 3: Inicio de sesión en el servidor de Aqua
Esta sección no se modifica de ninguna manera.  Siga la documentación de Aqua.

Use el siguiente comando para obtener la dirección de la consola de Aqua.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Paso 4: Implementación de los ejecutores de Aqua
Al implementar los ejecutores, establezca los campos siguientes:

| Campo          | Value         |
| -------------- | ------------- |
| Orquestador   | OpenShift     |
| ServiceAccount | aqua-account  |
| Project        | aqua-security |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Pasos específicos del producto para Prisma Cloud/Twistlock

Las instrucciones básicas que se van a modificar se pueden encontrar en la [documentación de implementación en la nube de Prisma](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html).

Empiece por instalar la herramienta `twistcli` tal como se describe en las secciones "Instalación de Prisma Cloud" y "Descarga del software de Prisma Cloud".

Creación de un nuevo proyecto de OpenShift
```
oc new-project twistlock
```

Omita la sección opcional "Inserción de las imágenes de Prisma Cloud en un registro privado". No funcionará en Red Hat OpenShift en Azure. En su lugar, use el registro en línea.

Puede seguir la documentación oficial y aplicar las correcciones que se describen a continuación.
Comience con la sección "Instalación de la consola".

### <a name="install-console"></a>Instalación de la consola

Durante `oc create -f twistlock_console.yaml` en el paso 2, se producirá un error al crear el espacio de nombres.
Puede pasarlo por alto sin ningún riesgo, ya que el espacio de nombres se ha creado anteriormente con el comando `oc new-project`.

Utilice `azure-disk` como tipo de almacenamiento.

### <a name="create-an-external-route-to-console"></a>Creación de una ruta externa a la consola

Puede seguir la documentación o las instrucciones siguientes si prefiere el comando oc.
Copie la siguiente definición de ruta en un archivo denominado twistlock_route.yaml en el equipo.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
A continuación, ejecute:
```
oc create -f twistlock_route.yaml
```

Puede obtener la dirección URL asignada a la consola de Twistlock con este comando: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Configuración de la consola

Siga la documentación de Twistlock.

### <a name="install-defender-for-cloud"></a>Instalación de Defender for Cloud

Durante `oc create -f defender.yaml` en el paso 2, obtendrá errores al crear el rol de clúster y el enlace del rol de clúster.
Puede pasarlos por alto.

Los defensores solo se implementarán en nodos de proceso. No tiene que limitarlos con un selector de nodos.
