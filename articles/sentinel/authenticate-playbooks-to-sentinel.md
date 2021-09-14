---
title: Autenticación de cuadernos de estrategias en Azure Sentinel | Microsoft Docs
description: Obtenga información sobre cómo proporcionar a los cuadernos de estrategias acceso a Azure Sentinel y autorización para realizar acciones correctivas.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: 6c3e4de61d59841f2856af2194ec22a63b407b5c
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123185442"
---
# <a name="authenticate-playbooks-to-azure-sentinel"></a>Autenticación de cuadernos de estrategias en Azure Sentinel

De la manera en que funciona Logic Apps, tiene que conectarse por separado y autenticarse de forma independiente en cada recurso de cada tipo con el que interactúa, incluido el propio Azure Sentinel. Logic Apps usa [conectores especializados](/connectors/connector-reference/) con este fin, teniendo cada tipo de recurso su propio conector. En este documento se explican los tipos de conexión y autenticación en el [conector de Logic Apps de Azure Sentinel](/connectors/azuresentinel/), que los cuadernos de estrategias pueden usar para interactuar con Azure Sentinel para tener acceso a la información en las tablas del área de trabajo.

Este documento, junto con nuestra guía para [usar desencadenadores y acciones en cuadernos de estrategias](playbook-triggers-actions.md), es un complemento de nuestra otra documentación del cuaderno de estrategias: [Tutorial: Uso de cuadernos de estrategias con reglas de automatización en Azure Sentinel](tutorial-respond-threats-playbook.md).

Para ver una presentación de los cuadernos de estrategias, consulte [Automatización de la respuesta a amenazas con cuadernos de estrategias en Azure Sentinel](automate-responses-with-playbooks.md).

Para obtener la especificación completa del conector de Azure Sentinel, consulte la [documentación del conector de Logic Apps](/connectors/azuresentinel/).

## <a name="authentication"></a>Authentication

El conector de Azure Sentinel en Logic Apps y sus acciones y desencadenadores de componentes pueden funcionar en nombre de cualquier identidad que tenga los permisos necesarios (lectura y/o escritura) o el área de trabajo pertinente. El conector admite varios tipos de identidad:

- [Identidad administrada (versión preliminar)](#authenticate-with-managed-identity)
- [Usuario de Azure AD](#authenticate-as-an-azure-ad-user)
- [Entidad de servicio (aplicación de Azure AD)](#authenticate-as-a-service-principal-azure-ad-application)

    ![Opciones de autenticación](media/authenticate-playbooks-to-sentinel/auth-methods.png)

### <a name="permissions-required"></a>Permisos necesarios

| Componentes del conector o roles | Desencadenadores | Acciones "Get" | Actualizar incidente,<br>agregar un comentario |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Lector de Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Azure Sentinel [Respondedor](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)/[Colaborador](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[Obtenga más información sobre permisos en Azure Sentinel](./roles.md).

### <a name="authenticate-with-managed-identity"></a>Autenticación con una identidad administrada

Este método de autenticación permite conceder permisos directamente al cuaderno de estrategias (un recurso de flujo de trabajo de Logic Apps), de modo que las acciones del conector de Azure Sentinel realizadas por el cuaderno de estrategias funcionen en nombre del cuaderno de estrategias, como si fuera un objeto independiente con sus propios permisos en Azure Sentinel. El uso de este método reduce el número de identidades que tiene que administrar. 

> [!NOTE]
> Para conceder a una identidad administrada acceso a otros recursos (como el área de trabajo de Azure Sentinel), el usuario que ha iniciado sesión debe tener un rol con permisos para escribir asignaciones de roles, como Propietario o Administrador de acceso de usuario del área de trabajo de Azure Sentinel.

Para autenticar con una identidad administrada:


1. [Habilite la identidad administrada](../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal) en el recurso de flujo de trabajo de Logic Apps. En resumen:

    - En el menú de la aplicación lógica, en **Configuración**, seleccione **Identidad**. Seleccione **Asignado por el sistema > Activado > Guardar**. Cuando Azure le pida confirmación, seleccione **Sí**.

    - Ahora, la aplicación lógica puede usar la identidad asignada por el sistema, que se registra con Azure AD y se representa mediante un identificador de objeto.

1. [Dé acceso a esa identidad](../logic-apps/create-managed-service-identity.md#give-identity-access-to-resources) al área de trabajo de Azure Sentinel: 
    1. En el menú de navegación de Azure Sentinel, seleccione **Configuración**.
    1. Seleccione la pestaña **Configuración del área de trabajo**. En el menú del área de trabajo, seleccione **Control de acceso (IAM)** .
   1. En la parte superior, seleccione **Agregar** y elija **Agregar asignación de roles**. Si la opción **Agregar asignación de roles** está deshabilitada, no tiene permisos para asignar roles.
    1. En el nuevo panel que aparece, asigne el rol adecuado:
    
        | Role | Situación |
        | --- | --- |
        | [**Respondedor de Azure Sentinel**](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) | El cuaderno de estrategias tiene pasos que actualizan incidentes o listas de reproducción. |
        | [**Lector de Azure Sentinel**](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) | El cuaderno de estrategias solo recibe incidentes. |
        |
        
        Obtenga más información sobre los [roles disponibles en Azure Sentinel](./roles.md).
    1. En **Asignar acceso a**, elija **Aplicación lógica**.
    1. Elija la suscripción a la que pertenece el cuaderno de estrategias y seleccione el nombre del cuaderno de estrategias.
    1. Seleccione **Guardar**.
    
    
1. Habilite el método de autenticación de identidad administrada en el conector de Logic Apps de Azure Sentinel:

    1. En el diseñador de Logic Apps, agregue un paso del conector de Logic Apps de Azure Sentinel. Si el conector ya está habilitado para una conexión existente, haga clic en el vínculo **Cambiar conexión**.

        ![Cambiar conexión](media/authenticate-playbooks-to-sentinel/change-connection.png)

    1. En la lista resultante de conexiones, seleccione **Agregar nueva** en la parte inferior. 

    1. Cree una nueva conexión seleccionando **Conectar con la identidad administrada (versión preliminar)** .

        ![Opción de identidad administrada](media/authenticate-playbooks-to-sentinel/auth-methods-msi-choice.png)

    1. Rellene un nombre para esta conexión, seleccione **Identidad administrada asignada por el sistema** y seleccione **Crear**.

        ![Conexión con identidad administrada](media/authenticate-playbooks-to-sentinel/auth-methods-msi.png)

### <a name="authenticate-as-an-azure-ad-user"></a>Autenticación como usuario de Azure AD

Para realizar una conexión, seleccione **Iniciar sesión**. Se le pedirá que proporcione su información de cuenta. Una vez hecho esto, siga las instrucciones restantes de la pantalla para crear una conexión.

### <a name="authenticate-as-a-service-principal-azure-ad-application"></a>Autenticación como entidad de servicio (aplicación de Azure AD)

Las entidades de servicio se pueden crear mediante el registro de una aplicación de Azure AD. Es **preferible** usar una aplicación registrada como identidad del conector, en lugar de usar una cuenta de usuario, ya que podrá controlar mejor los permisos, administrar las credenciales y habilitar ciertas limitaciones en el uso del conector.

Para usar su propia aplicación con el conector de Azure Sentinel, realice los pasos siguientes:

1. Registre la aplicación con Azure AD y cree una entidad de servicio. [Más información](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).

1. Obtenga las credenciales (para usarlas en futuras autenticaciones).

    En la hoja Aplicación registrada, obtenga las credenciales de la aplicación para iniciar sesión:

    - **Id. de cliente**: en **Información general**
    - **Secreto de cliente**: en **Certificados y secretos**.

1. Conceda permisos al área de trabajo de Azure Sentinel.

    En este paso, la aplicación tendrá permiso para trabajar con el área de trabajo de Azure Sentinel.

    1. En el área de trabajo de Azure Sentinel, vaya a **Configuración** -> **Configuración del área de trabajo** -> **Control de acceso (IAM)**

    1. Seleccione **Agregar asignación de roles**.

    1. Seleccione el rol que quiere asignar a la aplicación. Por ejemplo, para permitir que la aplicación realice acciones que supongan cambios en el área de trabajo de Sentinel, como actualizar un incidente, seleccione el rol **Colaborador de Azure Sentinel**. Para acciones que solo leen datos, el rol **Lector de Azure Sentinel** es suficiente. [Obtenga más información sobre los roles disponibles en Azure Sentinel](./roles.md).

    1. Busque la aplicación necesaria y guárdela. De manera predeterminada, las aplicaciones de Azure AD no se muestran en las opciones disponibles. Para encontrar la aplicación, busque el nombre y selecciónelo.

1. Authenticate

    En este paso se usan las credenciales de la aplicación para autenticarse en el conector de Sentinel en Logic Apps.

    - Seleccione **Connect with Service Principal** (Conectarse a la entidad de servicio).

        ![Opción de entidad de servicio](media/authenticate-playbooks-to-sentinel/auth-methods-spn-choice.png)

    - Rellene los parámetros necesarios (puede encontrarlos en la hoja Aplicación registrada)
        - **Inquilino**: en **Información general**
        - **Id. de cliente**: en **Información general**
        - **Secreto de cliente**: en **Certificados y secretos**
        
        ![Conectarse a la entidad de servicio](media/authenticate-playbooks-to-sentinel/auth-methods-spn.png)

### <a name="manage-your-api-connections"></a>Administración de las conexiones de API

Cada vez que se crea una autenticación por primera vez, se crea un nuevo recurso de Azure de tipo Conexión de API. Se puede usar la misma conexión de API en todos los desencadenadores y las acciones de Azure Sentinel del mismo grupo de recursos.

Encontrará todas las conexiones de API en la hoja **Conexiones de API** (busque *Conexiones de API* en Azure Portal).

También las puede encontrar yendo a la hoja **Recursos** y filtrando la presentación por tipo *Conexión de API*. De este modo, puede seleccionar varias conexiones para operaciones masivas.

Para cambiar la autorización de una conexión existente, escriba el recurso de conexión y seleccione **Editar conexión de API**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido sobre los distintos métodos de autenticación de un cuaderno de estrategias basado en Logic Apps en Azure Sentinel.
- Obtenga más información sobre cómo [usar los desencadenadores y las acciones en los cuadernos de estrategias](playbook-triggers-actions.md).
