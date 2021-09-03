---
title: Vínculo privado para Azure API for FHIR
description: En este artículo se describe cómo configurar un punto de conexión privado para los servicios Azure API for FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/27/2021
ms.author: zxue
ms.openlocfilehash: 6b025c35c5faa64a2333710693c992a410cafecc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780284"
---
# <a name="configure-private-link"></a>Configuración del vínculo privado

El vínculo privado le permite acceder a Azure API for FHIR a través de un punto de conexión privado, que es una interfaz de red que lo conecta de forma privada y segura mediante una dirección IP privada desde la red virtual. Con el vínculo privado, puede acceder a nuestros servicios de forma segura desde la red virtual como un servicio propietario sin tener que pasar por un sistema de nombres de dominio (DNS) público. En este artículo se describe cómo crear, probar y administrar el punto de conexión privado para Azure API for FHIR.

>[!Note]
>Ninguna de las instancias de Private Link ni de la API de Azure para FHIR se pueden migrar de un grupo de recursos a otro o de una suscripción a otra, una vez que la instancia de Private Link está habilitada. Para realizar una migración, primero elimine la instancia de Private Link y, luego, mueva Azure API for FHIR. Cree una instancia de Private Link nueva una vez que se complete la migración. Evalúe las posibles ramificaciones de seguridad antes de eliminar la instancia de Private Link.
>
>Si la exportación de registros y métricas de auditoría está habilitada para Azure API for FHIR, actualice la configuración de exportación a través de la **configuración de diagnóstico** desde el portal.

## <a name="prerequisites"></a>Requisitos previos

Antes de crear un punto de conexión privado, primero tendrá que crear algunos recursos de Azure:

- Grupo de recursos: el grupo de recursos de Azure que contendrá la red virtual y el punto de conexión privado.
- Azure API for FHIR: el recurso FHIR que quiere colocar detrás de un punto de conexión privado.
- Virtual Network: la red virtual a la que se conectarán los servicios de cliente y el punto de conexión privado.

Para más información, consulte [Documentación de Private Link](../../private-link/index.yml).

## <a name="create-private-endpoint"></a>Creación de un punto de conexión privado

Para crear un punto de conexión privado, un desarrollador con permisos de control de acceso basado en rol (RBAC) en el recurso FHIR puede usar Azure Portal, [Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)o la [CLI de Azure](../../private-link/create-private-endpoint-cli.md). En este artículo se lo guiará a través de los pasos con Azure Portal. Se recomienda usar Azure Portal, ya que automatiza la creación y configuración de la zona de DNS privado. Para más información, consulte las [guías de inicio rápido de Private Link](../../private-link/create-private-endpoint-portal.md).

Hay dos formas de crear un punto de conexión privado. El flujo de aprobación automática permite a un usuario con permisos de RBAC en el recurso FHIR crear un punto de conexión privado sin necesidad de aprobación. El flujo de aprobación manual permite a un usuario sin permisos en el recurso FHIR solicitar un punto de conexión privado para que los propietarios del recurso FHIR lo aprueben.

> [!NOTE]
> Cuando se crea un punto de conexión privado aprobado para Azure API for FHIR, el tráfico público hacia él se deshabilita automáticamente. 

### <a name="auto-approval"></a>Aprobación automática

Asegúrese de que la región del punto de conexión privado nuevo es la misma que la de la red virtual. La región del recurso FHIR puede ser diferente.

![Pestaña Aspectos básicos de Azure Portal](media/private-link/private-link-portal2.png)

En el caso del tipo de recurso, busque y seleccione **Microsoft.HealthcareApis/services**. Para el recurso, seleccione el recurso FHIR. Para el subrecurso de destino, seleccione **FHIR**.

![Pestaña Recursos de Azure Portal](media/private-link/private-link-portal1.png)

Si no tiene una configuración de zona de DNS privado existente, seleccione **(New)privatelink.azurehealthcareapis.com**. Si ya ha configurado la zona de DNS privado, puede seleccionarla en la lista. Debe tener el formato **privatelink.azurehealthcareapis.com**.

![Pestaña Configuración de Azure Portal](media/private-link/private-link-portal3.png)

Una vez que se completó la implementación, puede volver a la pestaña **Conexiones de punto de conexión privado**, en la que verá **Aprobado** como estado de la conexión.

### <a name="manual-approval"></a>Aprobación manual

Para la aprobación manual, seleccione la segunda opción en Recurso, "Conéctese a un recurso de Azure por identificador de recurso o alias". En Subrecurso de destino, escriba "fhir" en Aprobación automática.

![Aprobación manual](media/private-link/private-link-manual.png)

Una vez que se ha completado la implementación, puede volver a la pestaña "Conexiones de punto de conexión privado", en la que puede Aprobar, Rechazar o Quitar la conexión.

![Opciones](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Prueba del punto de conexión privado

Para asegurarse de que el servidor FHIR no recibe tráfico público después de deshabilitar el acceso a la red pública, seleccione el punto de conexión /metadata para el servidor desde el equipo. Debería recibir un mensaje 403 Prohibido. 


> [!NOTE]
> Pueden pasar hasta 5 minutos después de actualizar la marca de acceso a la red pública antes de que se bloquee el tráfico público.

Para asegurarse de que el punto de conexión privado puede enviar tráfico al servidor:

1. Cree una máquina virtual (VM) que esté conectada a la red virtual y la subred en la que está configurado el punto de conexión privado. Para asegurarse de que el tráfico de la máquina virtual solo usa la red privada, deshabilite el tráfico saliente de Internet a través de una regla de grupo de seguridad de red (NSG).
2. Use RDP en la máquina virtual.
3. Acceda al punto de conexión /metadata del servidor de FHIR desde la máquina virtual. Debe recibir la instrucción de funcionalidad como respuesta.

## <a name="manage-private-endpoint"></a>Administración de un punto de conexión privado

### <a name="view"></a>Ver

Los puntos de conexión privados y la controladora de interfaz de red (NIC) asociada son visibles en Azure Portal desde el grupo de recursos en el que se crearon.

![Visualización de recursos](media/private-link/private-link-view.png)

### <a name="delete"></a>Eliminar

Los puntos de conexión privados solo se pueden eliminar desde Azure Portal en la hoja **Información general** o al seleccionar la opción **Quitar** de la pestaña **Redes: Conexiones de punto de conexión privado**. Al seleccionar **Quitar**, se eliminará el punto de conexión privado y la NIC asociada. Si elimina todos los puntos de conexión privados del recurso FHIR y el acceso a la red pública está deshabilitado, no llegará ninguna solicitud al servidor FHIR.

![Eliminación del punto de conexión privado](media/private-link/private-link-delete.png)
