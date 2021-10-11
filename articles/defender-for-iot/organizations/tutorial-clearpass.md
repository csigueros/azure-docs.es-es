---
title: Integración de ClearPass con Azure Defender para IoT
description: En este tutorial, aprenderá a integrar Azure Defender para IoT con ClearPass.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 10/04/2021
ms.custom: template-tutorial
ms.openlocfilehash: 7ff16da99f994ff4b708f0fb6f4a40e72f61df78
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129421245"
---
# <a name="tutorial-integrate-clearpass-with-azure-defender-for-iot"></a>Tutorial: Integración de ClearPass con Azure Defender para IoT

Este tutorial le ayudará a aprender a integrar ClearPass Policy Manager (CPPM) con Azure Defender para IoT.

La plataforma de Defender para IoT ofrece supervisión continua de amenazas de ICS y detección de dispositivos, combinando un conocimiento profundo de los protocolos, dispositivos y aplicaciones industriales con detección de anomalías de comportamiento específicas de ICS, inteligencia sobre amenazas, análisis de riesgos y modelado de amenazas automatizado.

Defender para IoT detecta, detecta y clasifica los puntos de conexión de OT e ICS, y comparte información directamente con ClearPass mediante el marco de trabajo ClearPass Security Exchange y la API abierta.

Defender para IoT actualiza automáticamente la base de datos de puntos de conexión de ClearPass Policy Manager con datos de clasificación de puntos de conexión y varios atributos de seguridad personalizados.

La integración permite lo siguiente:

- Ver las amenazas de seguridad de ICS y SCADA identificadas por los motores de seguridad de Defender para IoT.

- Ver la información de inventario de dispositivos detectada por el sensor de Defender para IoT. El sensor ofrece visibilidad centralizada de todos los dispositivos de red y puntos de conexión en toda la infraestructura de TI y OT. Desde aquí, se puede definir y administrar un punto de conexión centralizado y una directiva de seguridad perimetral en el sistema de ClearPass.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Crear un usuario de API de ClearPass
> - Crear un perfil de operador de ClearPass
> - Crear un cliente de API de OAuth de ClearPass
> - Configurar Defender para IoT para la integración con ClearPass
> - Definir la regla de reenvío de ClearPass
> - Supervisar la comunicación de ClearPass y Defender para IoT

## <a name="prerequisites"></a>Requisitos previos

### <a name="aruba-clearpass-requirements"></a>Requisitos de Aruba ClearPass

CPPM se ejecuta en dispositivos de hardware con software preinstalado o como una máquina virtual en los siguientes hipervisores. No se admiten hipervisores que se ejecutan en un equipo cliente como VMware Player.

- VMware ESXi 5.5, 6.0, 6.5, 6.6 o superior.

- Microsoft Hyper-V Server 2012 R2 o 2016 R2.

- Hyper-V en Microsoft Windows Server 2012 R2 o 2016 R2.

- KVM en CentOS 7.5 o posterior.

### <a name="defender-for-iot-requirements"></a>Requisitos de Defender para IoT

- Defender para IoT, versión 2.5.1 o posteriores.

- Una cuenta de Azure. Si aún no tiene una cuenta de Azure, puede [crear una cuenta gratuita de Azure ahora](https://azure.microsoft.com/free/).

## <a name="create-a-clearpass-api-user"></a>Crear un usuario de API de ClearPass

Como parte del canal de comunicaciones entre los dos productos, Defender para IoT usa muchas API (tanto TIPS como REST). El acceso a las API TIPS se valida mediante credenciales de combinación de nombre de usuario y contraseña. Este identificador de usuario debe tener niveles mínimos de acceso. No use un perfil de superadministrador, use la API de administrador como se muestra a continuación.

**Para crear un usuario de API de ClearPass**:

1. En el panel izquierdo, seleccione **Administración** > **Usuarios y privilegios** y, luego, **ADD**.

1. En el cuadro de diálogo **Agregar usuario administrador**, establezca los siguientes parámetros:

    :::image type="content" source="media/tutorial-clearpass/policy-manager.png" alt-text="Captura de pantalla de la vista del cuadro de diálogo del usuario administrador.":::

    | Parámetro | Descripción |
    |--|--|
    | **UserID** | Escriba el identificador de usuario. |
    | **Nombre** | Escriba el nombre de usuario. |
    | **Contraseña** | Escriba la contraseña. |
    | **Habilitar usuario** | Compruebe si esta opción está habilitada. |
    | **Nivel de privilegio** | Seleccione **Administrador de API**. |

1. Seleccione **Agregar**.

## <a name="create-a-clearpass-operator-profile"></a>Creación de un perfil de operador de ClearPass

Defender para IoT usa la API REST como parte de la integración. Las API REST se autentican en un marco de OAuth. Para sincronizar con Defender para IoT, debe crear un cliente de API.

Para proteger el acceso a la API REST para el cliente de API, cree un perfil de operador de acceso restringido.

**Para crear un perfil de operador de ClearPass**:

1. Vaya a la ventana **Editar perfil de operador**.

1. Establezca todas las opciones en **Sin acceso**, excepto las siguientes:

| Parámetro | Descripción |
|--|--|
| **Servicios de API** | Establezca esta opción en **Permitir acceso**. |
| **Administrador de directivas** | Configure las opciones siguientes: <br />- **Diccionarios:** : configure **Atributos** en **Lectura, Escritura, Eliminación**.<br />- **Diccionarios:** : **Conjunto de huellas digitales** en **Lectura, Escritura, Eliminación**.<br />- **Identidad**: configure **Puntos de conexión** en **Lectura, Escritura, Eliminación**. |

:::image type="content" source="media/tutorial-clearpass/api-profile.png" alt-text="Captura de pantalla del perfil del operador de edición.":::

:::image type="content" source="media/tutorial-clearpass/policy.png" alt-text="Captura de pantalla de las opciones de la pantalla Administrador de directivas.":::

## <a name="create-a-clearpass-oauth-api-client"></a>Creación de un cliente de API de OAuth de ClearPass

1. En la ventana principal, seleccione **Administrador** > **Servicios de API** > **Clientes de API**.

1. En la pestaña Crear cliente de API, establezca los parámetros siguientes:

    - **Modo operativo**: este parámetro se usa para las llamadas API a ClearPass. Seleccione **API REST de ClearPass: Cliente**.

    - **Perfil de operador**: use el perfil que creó anteriormente.

    - **Tipo de concesión**: configure **Credenciales del cliente (grant_type = client_credentials)** .

1. Asegúrese de registrar el **secreto de cliente** y el identificador de cliente. Por ejemplo, `defender-rest`.

    :::image type="content" source="media/tutorial-clearpass/aruba.png" alt-text="Captura de pantalla de la página Crear cliente de API.":::

1. En el Administrador de directivas, asegúrese de que recopiló la siguiente lista de información antes de continuar con el paso siguiente.

    - UserID de CPPM

    - Contraseña de UserId de CPPM

    - Identificador de cliente de la API de OAuth2 de CPPM

    - Secreto de cliente de la API de OAuth2 de CPPM

## <a name="configure-defender-for-iot-to-integrate-with-clearpass"></a>Configuración de Defender para IoT para la integración con ClearPass

Para habilitar la visualización del inventario de dispositivos en ClearPass, debe configurar Defender para IoT: sincronización de ClearPass. Una vez completada la configuración de sincronización, la plataforma Defender para IoT actualiza la base de datos de puntos de conexión del administrador de directivas de ClearPass a medida que detecta nuevos puntos de conexión.

**Para configurar la sincronización de ClearPass en el sensor de Defender para IoT**:

1. En el sensor de Defender para IoT, seleccione **Configuración del sistema** en el panel izquierdo.

1. En el panel **Configuración del sistema**, seleccione :::image type="content" source="media/tutorial-clearpass/clearpass-icon.png" alt-text="Captura de pantalla del icono ClearPass en el lado izquierdo.":::

1. Establezca los siguientes parámetros:

    :::image type="content" source="media/tutorial-clearpass/settings.png" alt-text="Captura de pantalla del relleno de la información necesaria en el panel Configuración del sistema.":::

    - **Habilitar sincronización:** habilite la sincronización entre Defender para IoT y ClearPass.

    - **Frecuencia de sincronización:** defina la frecuencia de sincronización en minutos. El valor predeterminado es 60 minutos. El mínimo es de 5 minutos.

    - **Dirección IP de ClearPass:** dirección IP del sistema de ClearPass con el que Defender para IoT está sincronizado.

    - **Id. de cliente:** identificador de cliente que se creó en ClearPass para sincronizar los datos con Defender para IoT.

    - **Secreto de cliente:** secreto de cliente que se creó en ClearPass para sincronizar los datos con Defender para IoT.

    - **Nombre de usuario:** usuario administrador de ClearPass.

    - **Contraseña:** contraseña del administrador de ClearPass.

1. Seleccione **Guardar**.

## <a name="define-a-clearpass-forwarding-rule"></a>Definición de la regla de reenvío de ClearPass

Para habilitar la visualización de las alertas detectadas por Defender para IoT en Aruba, debe establecer la regla de reenvío. Esta regla define qué información sobre ICS y amenazas de seguridad de SCADA identificadas por los motores de seguridad de Defender para IoT se envía a ClearPass.

**Para definir una regla de reenvío de ClearPass en el sensor de Defender para IoT**:

1. En el sensor de Defender para IoT, seleccione **Reenvío**.

1. En el panel **Reenvío**, seleccione **Create Forwarding Rule** (Crear regla de reenvío).

    :::image type="content" source="media/tutorial-clearpass/forwarding.png" alt-text="Captura de pantalla del panel Reenvío con todas sus opciones.":::

1. Agregue el nombre y la gravedad de la regla y, a continuación, en la lista desplegable **Acción**, seleccione **Enviar a** > **ClearPass**.

    :::image type="content" source="media/tutorial-clearpass/rule.png" alt-text="Captura de la pantalla para crear una regla de reenvío.":::

1. En el panel **Actions** (Acciones), establezca los parámetros siguientes:

    :::image type="content" source="media/tutorial-clearpass/actions.png" alt-text="Seleccione las acciones en el panel Acciones.":::

    | Parámetro | Descripción |
    |--|--|
    | **Host** | Escriba la dirección IP del servidor de ClearPass. |
    | **Puerto** | Escriba el puerto de ClearPass en el que se realiza el reenvío. |
    | **Configuración** | Configure las siguientes opciones para permitir la visualización de alertas de Defender para IoT en el sistema de ClearPass: <br />- **Informar de códigos de función ilegales**: infracciones de protocolo: valor de campo ilegal que infringe la especificación del protocolo ICS (posible ataque).<br />- **Informar de las actualizaciones de firmware y programación de PLC no autorizadas:** cambios de PLC no autorizados.<br />- **Informar de la detención de PLC no autorizada:** detención de PLC (tiempo de inactividad).<br />- **Informar de las alertas relacionadas con malware:** bloqueo de los intentos de malware industrial, como TRITON y NotPetya.<br />- **Informar del análisis no autorizado:** análisis no autorizado (reconocimiento potencial). |

1. Seleccione **Submit** (Enviar).

## <a name="monitor-clearpass-and-defender-for-iot-communication"></a>Supervisión de la comunicación de ClearPass y Defender para IoT

Una vez iniciada la sincronización, los datos del punto de conexión se rellenan directamente en la base de datos de puntos de conexión del administrador de directivas. Puede ver la hora de la última actualización desde la pantalla de configuración de integración.

**Para revisar la hora de la última sincronización en ClearPass**:

1. Inicie sesión en el sensor de Defender para IoT.

1. En el panel del lado izquierdo, seleccione **Configuración del sistema**.

1. Seleccione **ClearPass**.

    :::image type="content" source="media/tutorial-clearpass/last-sync.png" alt-text="Captura de pantalla de la vista de la hora y fecha de la última sincronización.":::

Si la sincronización no funciona o muestra un error, es probable que no haya capturado parte de la información. Vuelva a comprobar los datos registrados. Además, puede ver las llamadas API entre Defender para IoT y ClearPass desde **Invitado** > **Administración** > **Soporte técnico** > **Registro de aplicaciones**.

A continuación, se muestra un ejemplo de registros de API entre Defender para IoT y ClearPass.

:::image type="content" source="media/tutorial-clearpass/log.png" alt-text="Captura de pantalla de registros de API entre Defender para IoT y ClearPass.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo empezar a trabajar con la integración de ClearPass. Continúe para obtener información sobre CyberArk.

> [!div class="nextstepaction"]
> [Botón de pasos siguientes](./tutorial-cyberark.md)
