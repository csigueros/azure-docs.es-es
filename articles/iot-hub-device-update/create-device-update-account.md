---
title: Creación de una cuenta de actualización de dispositivos en Device Update para Azure IoT Hub | Microsoft Docs
description: Cree una cuenta de actualización de dispositivos en Device Update para Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.custom: subject-rbac-steps
ms.openlocfilehash: 5ad119017f4fe1dda0703547480c7444978d1878
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129072"
---
# <a name="device-update-for-iot-hub-resource-management"></a>Administración de recursos de Device Update para IoT Hub

Para empezar a trabajar con Device Update, deberá crear una cuenta en este servicio, crear una instancia y establecer roles de control de acceso. 

## <a name="prerequisites"></a>Requisitos previos

* Acceso a IoT Hub. Se recomienda usar el nivel S1 (Estándar) o superior. 
* Exploradores admitidos:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="create-a-device-update-account"></a>Creación de una cuenta de Device Update

1. Vaya a [Azure Portal](https://portal.azure.com).

2. Haga clic en **Crear un recurso** y busque "Device Update for IoT Hub".

   :::image type="content" source="media/create-device-update-account/device-update-marketplace.png" alt-text="Captura de pantalla del recurso de Device Update para IoT Hub." lightbox="media/create-device-update-account/device-update-marketplace.png":::

3. Haga clic en **Crear** -> **Device Update for IoT Hub**.

4. Especifique la suscripción de Azure que se asociará con la cuenta de Device Update y el grupo de recursos.

5. Especifique un nombre y una ubicación para la cuenta de Device Update.

   :::image type="content" source="media/create-device-update-account/account-details.png" alt-text="Captura de pantalla de detalles de la cuenta." lightbox="media/create-device-update-account/account-details.png":::

 > [!NOTE]
 > Puede ir a la [página de productos de Azure por región](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) para consultar en qué regiones está disponible Device Update para IoT Hub. Si Device Update para IoT Hub no está disponible en su región, puede optar por crear una cuenta en una región disponible próxima. 

6. Opcionalmente, puede seleccionar la casilla para asignarse el rol Administrador de Device Update a sí mismo. También puede usar los pasos enumerados en la sección "Configuración de los roles de control de acceso" para proporcionar una combinación de roles a usuarios y aplicaciones para el nivel de acceso correcto.

8. Haga clic en **Siguiente: Revisar y crear**.

   :::image type="content" source="media/create-device-update-account/account-review.png" alt-text="Captura de pantalla de revisión de los detalles de cuente." lightbox="media/create-device-update-account/account-review.png":::

7. Revise los valores y, luego, seleccione **Crear**. Verá la implementación en curso. 

   :::image type="content" source="media/create-device-update-account/account-deployment-inprogress.png" alt-text="Captura de pantalla de la implementación de la cuenta en curso." lightbox="media/create-device-update-account/account-deployment-inprogress.png":::

8. Verá que el estado de la implementación cambia a "Finalizado" en unos minutos. Haga clic en **Ir al recurso**.

   :::image type="content" source="media/create-device-update-account/account-complete.png" alt-text="Captura de pantalla de la implementación de la cuenta finalizada." lightbox="media/create-device-update-account/account-complete.png":::

## <a name="create-a-device-update-instance"></a>Creación de una instancia de Device Update 

Una instancia de Device Update se asocia con un único centro de IoT. Seleccione el centro de IoT Hub que se usará con Device Update. En este paso se creará una nueva directiva de acceso compartido para asegurarse de que Device Update solo usa los permisos necesarios para trabajar con IoT Hub (escritura del registro y conexión del servicio). Esta directiva garantiza que el acceso solo se limita a Device Update.

Para crear una instancia de Device Update una vez creada una cuenta.

1. Una vez que se encuentre en el recurso de la cuenta recién creada, vaya a la hoja **Instancias** de Administración de instancias.

   :::image type="content" source="media/create-device-update-account/instance-blade.png" alt-text="Captura de pantalla de la administración de instancias en la cuenta." lightbox="media/create-device-update-account/instance-blade.png":::

2. Haga clic en **Crear**, especifique un nombre de instancia y seleccione el centro de IoT.

   :::image type="content" source="media/create-device-update-account/instance-details.png" alt-text="Captura de pantalla de detalles de la instancia." lightbox="media/create-device-update-account/instance-details.png":::

   > [!NOTE] 
   > El centro de IoT que vincule al recurso de Device Update no tiene que estar en la misma región que la cuenta de Device Update. Sin embargo, para mejorar el rendimiento, se recomienda que esté en la misma región o en una región cercana. 

3. Haga clic en **Crear**. Verá la instancia con el estado "Creando". 

   :::image type="content" source="media/create-device-update-account/instance-creating.png" alt-text="Captura de pantalla de la creación de la instancia." lightbox="media/create-device-update-account/instance-creating.png":::

4. Espere entre 5 y 10 minutos para que se complete la implementación de la instancia. Actualice el estado hasta que vea que en "Estado de aprovisionamiento" aparece "Correcto".

   :::image type="content" source="media/create-device-update-account/instance-succeeded.png" alt-text="Captura de pantalla de la creación correcta de la instancia." lightbox="media/create-device-update-account/instance-succeeded.png":::

## <a name="configure-iot-hub"></a>Configuración de IoT Hub 

Para que Device Update puede recibir notificaciones de cambios de IoT Hub, debe integrarse con el centro de eventos "integrado". Al hacer clic en el botón "Configure IoT Hub" (Configurar IoT Hub), se configuran las rutas de mensajes y la directiva de acceso necesarias para comunicarse con los dispositivos IoT. 

Para configurar IoT Hub

1. Una vez que el valor de "Estado de aprovisionamiento" de la instancia se convierta en "Correcto", seleccione la instancia en la hoja Instance Management (Administración de instancias). Haga clic en **Configure IoT Hub** (Configurar IoT Hub).

   :::image type="content" source="media/create-device-update-account/instance-configure.png" alt-text="Captura de pantalla de configuración de IoT Hub para una instancia." lightbox="media/create-device-update-account/instance-configure.png":::

2. Seleccione **I agree to make these changes** (Acepto realizar estos cambios).

   :::image type="content" source="media/create-device-update-account/instance-configure-selected.png" alt-text="Captura de pantalla donde se acepta la configuración de IoT Hub para una instancia." lightbox="media/create-device-update-account/instance-configure-selected.png":::

3. Haga clic en **Update** (Actualizar).

   > [!NOTE] 
   > Si utiliza un nivel Gratis de Azure IoT Hub, el número permitido de rutas de mensajes se limita a 5. Device Update for IoT Hub debe configurar 4 rutas de mensajes para que funcionen según lo previsto. 

[Obtenga información acerca de las rutas de mensajes que se configuran.](device-update-resources.md) 


## <a name="configure-access-control-roles"></a>Configuración de roles de control de acceso

Para que otros usuarios puedan tener acceso a Device Update, se les debe conceder acceso a este recurso. Puede omitir este paso si se asignó el rol Administrador de Device Update durante la creación de la cuenta y no tiene que proporcionar acceso a usuarios o aplicaciones adicionales. 

1. Vaya a Control de acceso (IAM) dentro de la cuenta de Device Update.

   :::image type="content" source="media/create-device-update-account/account-access-control.png" alt-text="Captura de pantalla del control de acceso en la cuenta de Device Update." lightbox="media/create-device-update-account/account-access-control.png":::

2. Haga clic en **Add role assignments** (Agregar asignaciones de roles).

3. En la pestaña Role (Rol), seleccione un rol de Device Update entre las opciones especificadas.
     - Device Update Administrator (Administrador de Device Update)
     - Device Update Reader (Lector de Device Update)
     - Device Update Content Administrator (Administrador de contenido de Device Update)
     - Device Update Content Reader (Lector de contenido de Device Update)
     - Device Update Deployments Administrator (Administrador de implementaciones de Device Update)
     - Device Update Deployments Reader (Lector de implementaciones de Device Update)
     
   :::image type="content" source="media/create-device-update-account/role-assignment.png" alt-text="Captura de pantalla de las asignaciones de roles de control de acceso dentro de la cuenta de Device Update." lightbox="media/create-device-update-account/role-assignment.png":::
    
    [Más información sobre el control de acceso basado en roles en Device Update para IoT Hub](device-update-control-access.md) 
    
4. Haga clic en **Siguiente**.
5. Asignación de acceso a un usuario o grupo de Azure AD
6. Selección de miembros
   
   :::image type="content" source="media/create-device-update-account/role-assignment-2.png" alt-text="Captura de pantalla de la selección de miembros del control de acceso dentro de la cuenta de Device Update." lightbox="media/create-device-update-account/role-assignment-2.png":::

6. Haga clic en **Review + assign** (Revisar y asignar).
7. Revise las nuevas asignaciones de roles y vuelva a hacer clic en **Review + assign** (Revisar y asignar).
8. Ahora está listo para usar la experiencia de Device Update desde el centro de IoT.

## <a name="next-steps"></a>Pasos siguientes

Intente actualizar un dispositivo con uno de los siguientes tutoriales rápidos:

 - [Device Update en un simulador](device-update-simulator.md)
 - [Device Update en Raspberry Pi](device-update-raspberry-pi.md)
 - [Device Update Agent en el paquete de Ubuntu Server 18.04 x64](device-update-ubuntu-agent.md)

[Consulte más información sobre la cuenta y la instancia de Device Update](device-update-resources.md). 

[Consulte más información sobre los roles de control de acceso de Device Update](device-update-control-access.md). 

