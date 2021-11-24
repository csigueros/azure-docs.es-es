---
title: 'Implementación del conector de IoT en Azure Portal: API de Azure Healthcare'
description: En este artículo, aprenderá a implementar el conector de IoT en el Azure Portal.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/10/2021
ms.author: jasteppe
ms.custom: mode-portal
ms.openlocfilehash: 8323d2026bc45c3c357ad0055cc7da37c5a345b8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061585"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>Implementación del conector de IoT en el Azure Portal

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este inicio rápido, aprenderá a implementar el conector de IoT en el Azure Portal. La configuración de un conector de IoT le permitirá ingerir datos de Internet de las cosas (IoT) en el servicio Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;) mediante un centro de eventos de Azure para los mensajes del dispositivo.

## <a name="prerequisites"></a>Requisitos previos

Es importante que tenga los siguientes requisitos previos completados antes de comenzar los pasos de creación de una instancia del conector de IoT en las API de Azure Healthcare.

* [Cuenta de Azure](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [Grupo de recursos implementado en la Azure Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [Event Hubs espacio de nombres y centro de eventos implementados en la Azure Portal](../../event-hubs/event-hubs-create.md)
* [Área de trabajo implementada en las API de Azure Healthcare](../healthcare-apis-quickstart.md)  
* [Servicio FHIR implementado en las API de Azure Healthcare](../fhir/fhir-portal-quickstart.md) 

## <a name="deploy-iot-connector"></a>Implementación del conector de IoT 

1. Inicie sesión en el [Azure Portal](https://portal.azure.com)y, a continuación, escriba el nombre del recurso del área de trabajo de Healthcare APIs en el **campo Barra** de búsqueda.
 
   ![Captura de pantalla de la especificación del nombre del recurso del área de trabajo en el campo de la barra de búsqueda.](media/select-workspace-resource-group.png#lightbox)

2. Seleccione **Deploy IoT connectors (Implementar conectores de IoT).**

   ![Captura de pantalla de la hoja Conectores de IoT.](media/iot-connector-blade.png#lightbox)

3. A continuación, **seleccione Agregar conector de IoT.**

   ![Captura de pantalla de la adición de conectores de IoT.](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>Configuración del conector de IoT para ingerir datos

En la **pestaña Aspectos** básicos, complete los campos obligatorios en **Detalles de la instancia**.

![Captura de pantalla de los detalles de la instancia de configuración de IoT.](media/basics-instance-details.png#lightbox)

1. Escriba el nombre **del conector de IoT.**

   El **nombre del conector de IoT** es un nombre descriptivo para el conector de IoT. Escriba un nombre único para la IoT Connector. Por ejemplo, puede nombrarlo `healthdemo-iot` .

2. Escriba el nombre **del centro de eventos.**

   El nombre del centro de eventos es el nombre **de la Event Hubs que** ha implementado. 

   Para obtener información sobre Azure Event Hubs, consulte [Inicio rápido: Creación de un centro de eventos mediante Azure Portal](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace).

3. Escriba el **grupo de consumidores**.

   El nombre del grupo de  consumidores se encuentra mediante la barra de búsqueda para ir Event Hubs la instancia de Event Hubs que ha implementado y seleccionando la hoja **Grupos de** consumidores.

   ![Captura de pantalla del nombre del grupo de consumidores.](media/consumer-group-name.png#lightbox)

   Para obtener información sobre los grupos de consumidores, vea [Características y terminología en Azure Event Hubs](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers).

4. Escriba el nombre del espacio **de nombres completo.**

    El **espacio de nombres completo** es el nombre de **host** que se encuentra en la Event Hubs información general del espacio **de** nombres.

    ![Captura de pantalla del espacio de nombres completo.](media/event-hub-hostname.png#lightbox)  

    Para obtener más información sobre los Event Hubs de nombres, vea [Espacio](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) de nombres en el documento Características y terminología Azure Event Hubs.

5. Seleccione **Siguiente: Asignación de dispositivos.** 
  
## <a name="configure-device-mapping-properties"></a>Configuración de propiedades de asignación de dispositivos

> [!TIP]
> El asignador de datos del conector ioMT es una herramienta de código abierto para visualizar la configuración de asignación para normalizar los datos de entrada de un dispositivo y, a continuación, transformarlos en recursos de FHIR. Los desarrolladores pueden usar esta herramienta para editar y probar las asignaciones de destino de dispositivos y FHIR, y para exportar los datos que se cargarán en un conector de IoT en el Azure Portal. Esta herramienta también ayuda a los desarrolladores a comprender las configuraciones de asignación de destino de dispositivos y FHIR de su dispositivo.
>
> Para más información, consulte la documentación de código abierto:
>
> [Asignador de datos del conector ioMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [Asignación de contenido del dispositivo](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. En la **pestaña Asignación de** dispositivos, escriba el código JSON de asignación de dispositivos asociado al conector de IoT.

   ![Captura de pantalla de Configuración de la asignación de dispositivos.](media/configure-device-mapping.png#lightbox)

2. Seleccione **Siguiente: Destino >** configurar las propiedades de destino asociadas al conector de IoT.

## <a name="configure-fhir-destination-mapping-properties"></a>Configuración de las propiedades de asignación de destino de FHIR

En la **pestaña Destino,** escriba las propiedades de destino asociadas al conector de IoT.

   ![Captura de pantalla de Configurar propiedades de destino.](media/configure-destination-properties.png#lightbox)

1. Escriba el identificador de recurso de Azure del **servicio FHIR.**

   El nombre del servidor de **FHIR** (también conocido como  servicio **FHIR)** se encuentra mediante la barra de búsqueda para ir al servicio FHIR que ha implementado y seleccionando la hoja **Propiedades.** Copie y pegue la cadena **de id. de** recurso en el **campo de texto Servidor FHIR.**

    ![Captura de pantalla de Escriba el nombre del servidor FHIR.](media/fhir-service-resource-id.png#lightbox) 

2. Escriba el **nombre de destino**.

   El **nombre de destino** es un nombre descriptivo para el destino. Escriba un nombre único para el destino. Por ejemplo, puede nombrarlo `iotmedicdevice` .

3. Seleccione **Crear** o **Buscar para** el tipo de **resolución**.

    > [!NOTE]
    > Para que el destino del conector de IoT cree un recurso de  observación válido en el servicio FHIR, debe existir un recurso de dispositivo y un recurso de paciente en el servidor de FHIR, por lo que la observación puede hacer referencia correctamente al dispositivo que creó los datos y al paciente desde el que se midieron los datos. Hay dos modos que el conector de IoT puede usar para resolver los recursos del dispositivo y del paciente.

   **Creación**

     El destino del conector de IoT intenta recuperar un recurso de dispositivo del servidor FHIR mediante el identificador de dispositivo incluido en el mensaje del centro de eventos. También intenta recuperar un recurso de paciente del servidor de FHIR mediante el identificador de paciente incluido en el mensaje del centro de eventos. Si no se encuentra ninguno de los recursos, se crearán nuevos recursos (dispositivo, paciente o ambos) que contengan solo el identificador contenido en el mensaje del centro de eventos. Cuando se usa la **opción Crear,** se pueden configurar un identificador de dispositivo y un identificador de paciente en la asignación de dispositivos. En otras palabras, cuando el  destino IoT Connector está en  modo De creación, puede funcionar normalmente sin agregar recursos de dispositivos y pacientes al servidor de FHIR.

   **Búsqueda**

     El destino del conector de IoT intenta recuperar un recurso de dispositivo del servidor FHIR mediante el identificador de dispositivo incluido en el mensaje del centro de eventos. Si no se encuentra el recurso del dispositivo, se producirá un error y los datos no se procesarán. Para **que la** búsqueda funcione correctamente, debe existir un recurso de  dispositivo con un  identificador que coincida con el identificador de dispositivo incluido en el mensaje del centro de eventos y el recurso de dispositivo debe tener una referencia a un recurso de paciente que también exista. En otras palabras, cuando el destino del conector de  IoT está en modo de búsqueda, los recursos del dispositivo y del paciente deben agregarse al servidor de FHIR antes de que se puedan procesar los datos.

   Para obtener más información, consulte la documentación de código abierto [Asignación de destino de FHIR](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping).

4. En **Asignación de destino,** escriba el código JSON dentro del editor de código.

   Para obtener información sobre la herramienta mapeador, vea Herramienta de asignador de datos del conector de [IoMT.](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)

5. Puede seleccionar **Revisar y crear,** o bien puede seleccionar **Siguiente: Etiquetas >** si desea configurar etiquetas.  

## <a name="optional-configure-tags"></a>(Opcional) Configuración de etiquetas

Las etiquetas son pares de nombre y valor que se usan para categorizar los recursos. Para más información sobre las etiquetas, consulte [Uso de etiquetas para organizar los recursos de Azure y la jerarquía de administración.](../../azure-resource-manager/management/tag-resources.md)

En la **pestaña Etiquetas,** escriba las propiedades de etiqueta asociadas al conector de IoT.

   ![Captura de pantalla de las propiedades de etiqueta.](media/tag-properties.png#lightbox)
 
1. Escriba un **nombre**.
2. Escriba un **valor**.
3. Seleccione **Revisar + crear**.

   Debería ver un mensaje **de validación** correcta como el que se muestra en la imagen siguiente. 

   ![Captura de pantalla del mensaje validación correcta.](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > Si el conector de IoT no se validó, revise el mensaje de error de validación y solucione el problema. Se recomienda revisar las propiedades en cada pestaña del conector de IoT que haya configurado.

4. A continuación, seleccione **Crear**.

   El conector de IoT recién implementado se mostrará dentro de la página Grupos de recursos de Azure.

   ![Captura de pantalla del conector de IoT implementado que aparece en la lista Recursos recientes de Azure.](media/azure-resources-iot-connector-deployed.png#lightbox)  

    Ahora que se ha implementado el conector de IoT, vamos a seguir los pasos de asignación de permisos para acceder al centro de eventos y al servicio FHIR. 

## <a name="granting-iot-connector-access"></a>Concesión de acceso al conector de IoT

Para asegurarse de que el conector de IoT funciona correctamente, debe haber concedido permisos de acceso al centro de eventos y al servicio FHIR. 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>Acceso al conector de IoT desde el centro de eventos

1. En la **lista Grupo de recursos de Azure,** seleccione el nombre del espacio Event Hubs espacio de **nombres**.

2. Seleccione la **hoja Control de acceso (IAM)** y, a continuación, seleccione + **Agregar.**   

   ![Captura de pantalla del control de acceso de Event Hubs espacio de nombres.](media/access-control-blade-add.png#lightbox)

3. Seleccione **Agregar asignación de roles**.

   ![Captura de pantalla de adición de asignación de roles.](media/event-hub-add-role-assignment.png#lightbox)
 
4. Seleccione el **rol y,** a continuación, **seleccione Azure Event Hubs receptor de datos**.

   ![Captura de pantalla de los campos necesarios para agregar la asignación de roles.](media/event-hub-add-role-assignment-fields.png#lightbox)

   El Azure Event Hubs receptor de datos permite que el conector de IoT al que se asigna este rol reciba datos de este centro de eventos.

   Para obtener más información sobre los roles de aplicación, vea Autenticación & autorización para las API de atención [sanitaria (versión preliminar).](.././authentication-authorization.md)

5. Seleccione **Asignar acceso a** y mantenga la opción predeterminada seleccionada **Usuario, grupo o entidad de servicio.**

6. En el **campo Seleccionar,** escriba la entidad de seguridad del conector de IoT.  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   Cuando se implementa un conector de IoT, se crea una identidad administrada. El nombre de identificación administrada es una concatenación del nombre del área de trabajo, el tipo de recurso (es decir, el conector de IoT) y el nombre del conector de IoT.

7. Seleccione **Guardar**.

   Una vez que la asignación de roles se haya agregado correctamente al centro de eventos, una notificación mostrará una marca de verificación verde con el texto "Agregar asignación de roles".  Este mensaje indica que el conector de IoT ahora puede leer desde el centro de eventos.

   ![Captura de pantalla del mensaje de asignación de roles agregados.](media/event-hub-added-role-assignment.png#lightbox)

Para obtener más información sobre cómo crear acceso a Event Hubs recursos, vea [Autorización del acceso con Azure Active Directory](../../event-hubs/authorize-access-azure-active-directory.md).  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>Acceso al conector de IoT desde el servicio FHIR

1. En la **lista Grupo de recursos de Azure**, seleccione el nombre del servicio **FHIR.**
 
2. Seleccione la **hoja Control de acceso (IAM)** y, a continuación, seleccione + **Agregar.** 

3. Seleccione **Agregar asignación de roles**.

  ![Captura de pantalla de la adición de asignación de roles para el servicio FHIR.](media/fhir-service-add-role-assignment.png#lightbox)

4. Seleccione el **rol y,** a continuación, **seleccione FHIR Data Writer**.

   El rol escritor de datos de FHIR proporciona acceso de lectura y escritura que el conector de IoT usa para funcionar. Dado que el conector de IoT se implementa como un recurso independiente, el servicio FHIR recibirá solicitudes del conector de IoT. Si el servicio FHIR no sabe quién realiza la solicitud o si no tiene el rol asignado, denegará la solicitud como no autorizada.

   Para obtener más información sobre los roles de aplicación, vea Autenticación & autorización para las API de atención [sanitaria (versión preliminar).](.././authentication-authorization.md)

5. En el **campo Seleccionar,** escriba la entidad de seguridad del conector de IoT.  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. Seleccione **Guardar**.

   ![Captura de pantalla del mensaje de asignación de roles agregado del servicio FHIR.](media/fhir-service-added-role-assignment.png#lightbox)

   Para más información sobre la asignación de roles al servicio FHIR, consulte [Configuración de RBAC de Azure.](.././configure-azure-rbac.md)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a implementar un conector de IoT en el Azure Portal. Para obtener información general sobre el conector de IoT, consulte

>[!div class="nextstepaction"]
>[Introducción al conector de IoT](iot-connector-overview.md)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.
