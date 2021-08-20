---
title: 'Tutorial: Exportación de datos y visualización de información en Azure IoT Central'
description: En este tutorial, aprenderá a exportar datos desde IoT Central y a visualizar información en un panel de Power BI.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: d94b3e56b3b8f70a03b6a3872b085e450043ab70
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585958"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>Tutorial: Exportación de datos desde Azure IoT Central y visualización de información en Power BI


En los dos tutoriales anteriores, ha creado y personalizado una aplicación de IoT Central con la plantilla de aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra). En este tutorial, configurará la aplicación de IoT Central para que exporte los datos de telemetría recopilados de los dispositivos. A continuación, usará Power BI para crear un panel personalizado para que el administrador de la tienda visualice la información derivada de la telemetría.

En este tutorial, aprenderá a:
> [!div class="checklist"]
> * Configurar una aplicación de IoT Central para exportar los datos de telemetría a un centro de eventos.
> * Usar Logic Apps para enviar datos de un centro de eventos a un conjunto de datos de streaming de Power BI.
> * Crear un panel de Power BI para visualizar los datos del conjunto de datos de streaming.

## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial, necesita:

* Completar los dos tutoriales anteriores, [Creación de una aplicación de análisis en tienda en Azure IoT Central](./tutorial-in-store-analytics-create-app.md) y [Personalización del panel y administración de dispositivos en Azure IoT Central](./tutorial-in-store-analytics-customize-dashboard.md).
* Suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
* Una cuenta de Power BI Si no tiene una cuenta de Power BI, regístrese para obtener una [evaluación gratuita de Power BI Pro](https://app.powerbi.com/signupredirect?pbi_source=web) antes de empezar.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Antes de crear el centro de eventos y la aplicación lógica, debe crear un grupo de recursos para administrarlos. El grupo de recursos debe estar en la misma ubicación que la aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) de IoT Central. Para crear un grupo de recursos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En el panel de navegación izquierdo, seleccione **Grupos de recursos**. A continuación, seleccione **Agregar**.
1. En **Suscripción**, seleccione el nombre de la suscripción de Azure que usó para crear la aplicación de IoT Central.
1. En el nombre del **Grupo de recursos**, escriba _retail-store-analysis_*.
1. En **Región**, seleccione la misma región que eligió para la aplicación de IoT Central.
1. Seleccione **Revisar + crear**.
1. En la página **Revisar + crear**, seleccione **Crear**.

Ahora tiene un grupo de recursos llamado **retail-store-analysis** en la suscripción.

## <a name="create-an-event-hub"></a>Creación de un centro de eventos

Antes de poder configurar la aplicación de supervisión de venta al por menor para que exporte los datos de telemetría, debe crear un centro de eventos para recibir los datos exportados. En los pasos siguientes se muestra cómo crear el centro de eventos:

1. En Azure Portal, seleccione **Crear un recurso** en la parte superior izquierda de la pantalla.
1. En **Buscar en Marketplace**, escriba _Event Hubs_ y, a continuación, presione **Entrar**.
1. En la página **Event Hubs**, seleccione **Crear**.
1. En la página **Crear espacio de nombres**, realice los pasos siguientes:
    * Escriba un nombre único para el espacio de nombres, como _sunombre-retail-store-analysis_. El sistema comprueba si el nombre está disponible.
    * Elija el plan de tarifa **Básico**.
    * Seleccione la misma **Suscripción** que usó para crear la aplicación de IoT Central.
    * Seleccione el grupo de recursos **retail-store-analysis**.
    * Seleccione la misma ubicación que usó para la aplicación de IoT Central.
    * Seleccione **Crear**. Puede que tenga que esperar unos minutos hasta que el sistema aprovisione los recursos.
1. En el portal, vaya al grupo de recursos **retail-store-analysis**. Espere a que la implementación se complete. Es posible que tenga que seleccionar **Actualizar** para actualizar el estado de la implementación. También puede comprobar el estado de la creación del espacio de nombres del centro de eventos en **Notificaciones**.
1. En el grupo de recursos **retail-store-analysis**, seleccione el **Espacio de nombres de Event Hubs**. Puede ver la página principal del **espacio de nombres de Event Hubs** en el portal.

Ahora que tiene un **espacio de nombres de Event Hubs**, puede crear un **centro de eventos** para usarlo con la aplicación de IoT Central:

1. En la página principal del **Espacio de nombres de Event Hubs** en el portal, seleccione **+ Centro de eventos**.
1. En la página **Crear centro de eventos**, escriba _store-telemetry_ como nombre y, a continuación, seleccione **Crear**.

Ahora tiene un centro de eventos que puede usar al configurar la exportación de datos desde la aplicación de IoT Central:

![Centro de eventos](./media/tutorial-in-store-analytics-visualize-insights/event-hub.png)

## <a name="configure-data-export"></a>Configuración de la exportación de datos

Ahora tiene un centro de eventos, puede configurar la aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) para exportar los datos de telemetría desde los dispositivos conectados. En los pasos siguientes se muestra cómo configurar la exportación:

1. Inicie sesión en la aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) de IoT Central.
1. Seleccione **Data export** (Exportación de datos) en el panel izquierdo.
1. Seleccione **New > Azure Event Hubs** (Nuevo > Azure Event Hubs).
1. Escriba _Telemetry export_ (Exportación de telemetría) en **Display Name** (Nombre para mostrar).
1. Seleccione el **espacio de nombres de Event Hubs**.
1. Seleccione el centro de eventos **store-telemetry**.
1. Desactive **Devices** (Dispositivos) y **Device Templates** (Plantillas de dispositivo) en la sección **Data to export** (Datos a exportar).
1. Seleccione **Guardar**.

La exportación de datos puede tardar unos minutos en iniciar el envío de los datos de telemetría al centro de eventos. Puede ver el estado de la exportación en la página **Data exports** (Exportaciones de datos):

![Configuración de la exportación de datos continua](./media/tutorial-in-store-analytics-visualize-insights/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>Creación de los conjuntos de datos de Power BI

El panel de Power BI mostrará los datos de la aplicación de supervisión de venta al por menor. En esta solución, se usan conjuntos de datos de streaming de Power BI como origen de datos para el panel de Power BI. En esta sección, definirá el esquema de los conjuntos de datos de streaming para que la aplicación lógica pueda reenviar los datos procedentes del centro de eventos. En los pasos siguientes se muestra cómo crear dos conjuntos de datos de streaming para los sensores ambientales y un conjunto de datos de streaming para el sensor de ocupación:

1. Inicie sesión en su cuenta de **Power BI**.
1. Seleccione **Áreas de trabajo** y, después, seleccione **Crear un área de trabajo**.
1. En la página **Crear un área de trabajo**, escriba _In-store analytics - checkout_ (Análisis en tienda: finalización de la compra) como el **Nombre del área de trabajo de** .
1. Desplácese a la parte inferior de la página **Bienvenido al área de trabajo In-store analytics - checkout** y seleccione **Omitir**.
1. En la página del área de trabajo, seleccione **Crear > Conjunto de datos de streaming**.
1. En la página **Nuevo conjunto de datos de streaming**, seleccione **API** y, a continuación, seleccione **Siguiente**.
1. Escriba _Sensor zona 1_ como **Nombre del conjunto de datos**.
1. Escriba los tres **valores de la secuencia** de la tabla siguiente:

    | Nombre del valor  | Tipo de valor |
    | ----------- | ---------- |
    | Timestamp   | DateTime   |
    | Humedad    | Number     |
    | Temperatura | Number     |

1. Active **Análisis del historial de datos**.
1. Seleccione **Crear** y, a continuación, seleccione **Listo**.
1. Cree otro conjunto de datos de streaming llamado **Sensor zona 2** con el mismo esquema y la misma configuración que el conjunto de datos **Sensor zona 1**.

Ahora tiene dos conjuntos de datos de streaming. La aplicación lógica enrutará los datos de telemetría de los dos sensores ambientales conectados a la aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) a estos dos conjuntos de datos:

![Conjuntos de datos de zona](./media/tutorial-in-store-analytics-visualize-insights/dataset-1.png)

Esta solución utiliza un conjunto de datos de streaming para cada sensor porque no es posible aplicar filtros a los datos de streaming en Power BI.

También necesita un conjunto de datos de streaming para los datos de telemetría de ocupación:

1. En la página del área de trabajo, seleccione **Crear > Conjunto de datos de streaming**.
1. En la página **Nuevo conjunto de datos de streaming**, seleccione **API** y, a continuación, seleccione **Siguiente**.
1. Escriba _Sensor de ocupación_ como **Nombre del conjunto de datos**.
1. Escriba los cinco **valores de la secuencia** de la tabla siguiente:

    | Nombre del valor     | Tipo de valor |
    | -------------- | ---------- |
    | Timestamp      | DateTime   |
    | Queue Length 1 | Number     |
    | Queue Length 2 | Number     |
    | Dwell Time 1   | Number     |
    | Dwell Time 2   | Number     |

1. Active **Análisis del historial de datos**.
1. Seleccione **Crear** y, a continuación, seleccione **Listo**.

Ahora tiene un tercer conjunto de datos de streaming que almacena valores del sensor de ocupación simulado. Este sensor informa de la longitud de la cola en las dos zonas de cajas de la tienda y cuánto tiempo esperan los clientes en dichas colas:

![Conjunto de datos de ocupación](./media/tutorial-in-store-analytics-visualize-insights/dataset-2.png)

## <a name="create-a-logic-app"></a>Creación de una aplicación lógica

En esta solución, la aplicación lógica lee los datos de telemetría del centro de eventos, analiza los datos y, a continuación, los envía a los conjuntos de datos de streaming de Power BI que ha creado.

Antes de crear la aplicación lógica, necesita los identificadores de dispositivo de los dos sensores RuuviTag que conectó a la aplicación de IoT Central en el tutorial [Creación de una aplicación de análisis en tienda en Azure IoT Central](./tutorial-in-store-analytics-create-app.md):

1. Inicie sesión en la aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) de IoT Central.
1. En el panel izquierdo, seleccione **Devices** (Dispositivos). A continuación, seleccione **RuuviTag**.
1. Tome nota de los **identificadores de dispositivo**. En la siguiente captura de pantalla, los identificadores son **f5dcf4ac32e8** y **e29ffc8d5326**:

    ![Identificadores de dispositivo](./media/tutorial-in-store-analytics-visualize-insights/device-ids.png)

En los pasos siguientes se muestra cómo crear la aplicación lógica en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione **Crear un recurso** en la parte superior izquierda de la pantalla.
1. En **Buscar en Marketplace**, escriba _aplicación lógica_ y, a continuación, presione **Entrar**.
1. En la página **Aplicación lógica**, seleccione **Crear**.
1. En la página **Crear aplicación lógica**:
    * Escriba un nombre único para la aplicación lógica, como _sunombre-retail-store-analysis_.
    * Seleccione la misma **Suscripción** que usó para crear la aplicación de IoT Central.
    * Seleccione el grupo de recursos **retail-store-analysis**.
    * Seleccione la misma ubicación que usó para la aplicación de IoT Central.
    * Seleccione **Crear**. Puede que tenga que esperar unos minutos hasta que el sistema aprovisione los recursos.
1. En Azure Portal, vaya a la nueva aplicación lógica.
1. En la página **Diseñador de aplicaciones lógicas**, seleccione **Aplicación lógica en blanco**.
1. En **Buscar conectores y desencadenadores**, escriba _Event Hubs_.
1. En **Desencadenadores**, seleccione **Cuando los eventos estén disponibles en el centro de eventos**.
1. Escriba _store-telemetry_ como **Nombre de la conexión** y seleccione el **Espacio de nombres de Event Hubs**.
1. Seleccione la directiva **RootManageSharedAccess** y seleccione **Crear**.
1. En la acción **Cuando los eventos estén disponibles en el centro de eventos**:
    * En **Nombre del centro de eventos**, seleccione **store-telemetry**.
    * En **Tipo de contenido**, seleccione **application/json**.
    * Establezca el **Intervalo** en tres y la **Frecuencia** en segundos.
1. Seleccione **Guardar** para guardar la aplicación lógica.

Para agregar la lógica al diseño de la aplicación lógica, seleccione **Vista de código**:

1. Reemplace `"actions": {},` por el siguiente código JSON. Reemplace los dos marcadores de posición `[YOUR RUUVITAG DEVICE ID 1]` y `[YOUR RUUVITAG DEVICE ID 2]` por los identificadores que anotó de los dos dispositivos RuuviTag:

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. Seleccione **Guardar** y, después, seleccione **Diseñador** para ver la versión visual de la lógica que ha agregado:

    ![Diseño de la aplicación lógica](./media/tutorial-in-store-analytics-visualize-insights/logic-app.png)

1. Seleccione **Cambiar por identificador de dispositivo** para expandir la acción. A continuación, seleccione **Entorno zona 1** y seleccione **Agregar una acción**.
1. En **Buscar conectores y acciones**, escriba **Power BI** y, a continuación, presione **Entrar**.
1. Seleccione la acción **Agregar filas a un conjunto de datos (versión preliminar)** .
1. Seleccione **Iniciar sesión** y siga las indicaciones para iniciar sesión en la cuenta de Power BI.
1. Una vez finalizado el proceso de inicio de sesión, en la acción **Agregar filas a un conjunto de datos**:
    * Seleccione **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) como el área de trabajo.
    * Seleccione **Sensor zona 1** como el conjunto de datos.
    * Seleccione **RealTimeData** como la tabla.
    * Seleccione **Agregar nuevo parámetro** y, a continuación, seleccione los campos **Timestamp**, **Humidity** y **Temperature**.
    * Seleccione el campo **Timestamp** y, después, seleccione **x-opt-enqueuedtime** de la lista **Contenido dinámico**.
    * Seleccione el campo **Humidity** y, a continuación, seleccione **Ver más** junto a **Analizar telemetría**. A continuación, seleccione **humidity**.
    * Seleccione el campo **Temperature** y, a continuación, seleccione **Ver más** junto a **Analizar telemetría**. A continuación, seleccione **temperature**.
    * Haga clic en **Guardar** para guardar los cambios. La acción **Entorno zona 1** es similar a la captura de pantalla siguiente: ![Entorno zona 1](./media/tutorial-in-store-analytics-visualize-insights/zone-1-action.png)
1. Seleccione la acción **Entorno zona 2** y seleccione **Agregar una acción**.
1. En **Buscar conectores y acciones**, escriba **Power BI** y, a continuación, presione **Entrar**.
1. Seleccione la acción **Agregar filas a un conjunto de datos (versión preliminar)** .
1. En la acción **Agregar filas a un conjunto de datos 2**:
    * Seleccione **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) como el área de trabajo.
    * Seleccione **Sensor zona 2** como el conjunto de datos.
    * Seleccione **RealTimeData** como la tabla.
    * Seleccione **Agregar nuevo parámetro** y, a continuación, seleccione los campos **Timestamp**, **Humidity** y **Temperature**.
    * Seleccione el campo **Timestamp** y, después, seleccione **x-opt-enqueuedtime** de la lista **Contenido dinámico**.
    * Seleccione el campo **Humidity** y, a continuación, seleccione **Ver más** junto a **Analizar telemetría**. A continuación, seleccione **humidity**.
    * Seleccione el campo **Temperature** y, a continuación, seleccione **Ver más** junto a **Analizar telemetría**. A continuación, seleccione **temperature**.
    Haga clic en **Guardar** para guardar los cambios.  La acción **Entorno zona 2** es similar a la captura de pantalla siguiente: ![Entorno zona 2](./media/tutorial-in-store-analytics-visualize-insights/zone-2-action.png)
1. Seleccione la acción **Ocupación** y, a continuación, seleccione la acción **Cambiar por identificador de interfaz**.
1. Seleccione la acción **Interfaz de tiempo de permanencia** y seleccione **Agregar una acción**.
1. En **Buscar conectores y acciones**, escriba **Power BI** y, a continuación, presione **Entrar**.
1. Seleccione la acción **Agregar filas a un conjunto de datos (versión preliminar)** .
1. En la acción **Agregar filas a un conjunto de datos**:
    * Seleccione **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) como el área de trabajo.
    * Seleccione **Sensor de ocupación** como el conjunto de datos.
    * Seleccione **RealTimeData** como la tabla.
    * Seleccione **Agregar nuevo parámetro** y, a continuación, seleccione los campos **Timestamp**, **Dwell Time 1** y **Dwell Time 2**.
    * Seleccione el campo **Timestamp** y, después, seleccione **x-opt-enqueuedtime** de la lista **Contenido dinámico**.
    * Seleccione el campo **Dwell Time 1** y, a continuación, seleccione **Ver más** junto a **Analizar telemetría**. A continuación, seleccione **DwellTime1**.
    * Seleccione el campo **Dwell Time 2** y, a continuación, seleccione **Ver más** junto a **Analizar telemetría**. A continuación, seleccione **DwellTime2**.
    * Haga clic en **Guardar** para guardar los cambios. La acción **Interfaz de tiempo de permanencia** es similar a la captura de pantalla siguiente: ![Captura de pantalla que muestra la acción "Interfaz de tiempo de permanencia".](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-1.png)
1. Seleccione la acción **Interfaz de recuento de personas** y seleccione **Agregar una acción**.
1. En **Buscar conectores y acciones**, escriba **Power BI** y, a continuación, presione **Entrar**.
1. Seleccione la acción **Agregar filas a un conjunto de datos (versión preliminar)** .
1. En la acción **Agregar filas a un conjunto de datos**:
    * Seleccione **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) como el área de trabajo.
    * Seleccione **Sensor de ocupación** como el conjunto de datos.
    * Seleccione **RealTimeData** como la tabla.
    * Seleccione **Agregar nuevo parámetro** y, a continuación, seleccione los campos **Timestamp**, **Queue Length 1** y **Queue Length 2**.
    * Seleccione el campo **Timestamp** y, después, seleccione **x-opt-enqueuedtime** de la lista **Contenido dinámico**.
    * Seleccione el campo **Queue Length 1** y, a continuación, seleccione **Ver más** junto a **Analizar telemetría**. A continuación, seleccione **count1**.
    * Seleccione el campo **Queue Length 2** y, a continuación, seleccione **Ver más** junto a **Analizar telemetría**. A continuación, seleccione **count2**.
    * Haga clic en **Guardar** para guardar los cambios. La acción **Interfaz de recuento de personas** es similar a la captura de pantalla siguiente: ![Acción de ocupación](./media/tutorial-in-store-analytics-visualize-insights/occupancy-action-2.png)

La aplicación lógica se ejecuta automáticamente. Para ver el estado de cada ejecución, vaya a la página **Información general** de la aplicación lógica en Azure Portal:

## <a name="create-a-power-bi-dashboard"></a>Creación de un panel de Power BI

Ahora tiene datos de telemetría que fluyen desde la aplicación de IoT Central al centro de eventos. A continuación, la aplicación lógica analiza los mensajes del centro de eventos y los agrega a un conjunto datos de streaming de Power BI. Ahora puede crear un panel de Power BI para visualizar los datos de telemetría:

1. Inicie sesión en su cuenta de **Power BI**.
1. Seleccione **Áreas de trabajo > In-store analytics - checkout**.
1. Seleccione **Crear > Panel**.
1. Escriba **Store analytics** como el nombre del panel y seleccione **Crear**.

### <a name="add-line-charts"></a>Adición de gráficos de líneas

Agregue cuatro iconos de gráfico de líneas para mostrar la temperatura y la humedad de los dos sensores ambientales. Utilice la información de la tabla siguiente para crear los iconos. Para agregar cada icono, empiece por seleccionar **...(Más opciones) > Agregar icono**. Seleccione **Datos de transmisión personalizados** y, a continuación, seleccione **Siguiente**:

| Configuración | Gráfico 1 | Gráfico 2 | Gráfico 3 | Gráfico 4 |
| ------- | -------- | -------- | -------- | -------- |
| Dataset | Sensor zona 1 | Sensor zona 1 | Sensor zona 2 | Sensor zona 2 |
| Tipo de visualización | Gráfico de líneas | Gráfico de líneas | Gráfico de líneas | Gráfico de líneas |
| Eje | Timestamp | Timestamp | Timestamp | Timestamp |
| Valores | Temperatura | Humedad | Temperatura | Humedad |
| Período de tiempo | 60 minutos | 60 minutos | 60 minutos | 60 minutos |
| Título | Temperatura (1 hora) | Humedad (1 hora) | Temperatura (1 hora) | Humedad (1 hora) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

En la captura de pantalla siguiente se muestra la configuración del primer gráfico:

![Configuración del gráfico de línea](./media/tutorial-in-store-analytics-visualize-insights/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>Adición de tarjetas para mostrar los datos ambientales

Agregue cuatro iconos de tarjeta para mostrar los valores de temperatura y humedad más recientes de los dos sensores ambientales. Utilice la información de la tabla siguiente para crear los iconos. Para agregar cada icono, empiece por seleccionar **...(Más opciones) > Agregar icono**. Seleccione **Datos de transmisión personalizados** y, a continuación, seleccione **Siguiente**:

| Configuración | Tarjeta 1 | Tarjeta 2 | Tarjeta 3 | Tarjeta 4 |
| ------- | ------- | ------- | ------- | ------- |
| Dataset | Sensor zona 1 | Sensor zona 1 | Sensor zona 2 | Sensor zona 2 |
| Tipo de visualización | Card | Card | Card | Card |
| Fields | Temperatura | Humedad | Temperatura | Humedad |
| Título | Temperatura (F) | Humedad (%) | Temperatura (F) | Humedad (%) |
| Subtítulo | Zona 1 | Zona 1 | Zona 2 | Zona 2 |

En la captura de pantalla siguiente se muestra la configuración de la primera tarjeta:

![Configuración de tarjeta](./media/tutorial-in-store-analytics-visualize-insights/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>Adición de iconos para mostrar los datos de ocupación en la zona de cajas

Agregue cuatro iconos de tarjeta para mostrar la longitud de la cola y el tiempo de permanencia de las dos zonas de cajas de la tienda. Utilice la información de la tabla siguiente para crear los iconos. Para agregar cada icono, empiece por seleccionar **...(Más opciones) > Agregar icono**. Seleccione **Datos de transmisión personalizados** y, a continuación, seleccione **Siguiente**:

| Configuración | Tarjeta 1 | Tarjeta 2 | Tarjeta 3 | Tarjeta 4 |
| ------- | ------- | ------- | ------- | ------- |
| Dataset | Sensor de ocupación | Sensor de ocupación | Sensor de ocupación | Sensor de ocupación |
| Tipo de visualización | Gráfico de columnas agrupadas | Gráfico de columnas agrupadas | Indicador | Indicador |
| Eje    | Timestamp | Timestamp | N/D | N/D |
| Value | Dwell Time 1 | Dwell Time 2 | Queue Length 1 | Queue Length 2 |
| Período de tiempo | 60 minutos | 60 minutos |  N/D | N/D |
| Título | Tiempo de permanencia | Tiempo de permanencia | Longitud de la cola | Longitud de la cola |
| Subtítulo | Zona de cajas 1 | Zona de cajas 2 | Zona de cajas 1 | Zona de cajas 2 |

Cambie el tamaño y reorganice los iconos en el panel para que se parezca a la siguiente captura de pantalla:

![Captura de pantalla que muestra el panel de Power BI con iconos cambiados y reorganizados.](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard.png)

Puede agregar algunos recursos gráficos adicionales para personalizar aún más el panel:

![Panel de Power BI](./media/tutorial-in-store-analytics-visualize-insights/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si ha terminado con la aplicación de IoT Central, puede eliminarla; para ello, inicie sesión en la aplicación y vaya a la página **Application Settings** (Configuración de la aplicación) en la sección **Administration** (Administración).

Si desea mantener la aplicación pero quiere reducir los costos asociados a ella, deshabilite la exportación de datos que envía los datos de telemetría al centro de eventos.

Puede eliminar el centro de eventos y la aplicación lógica en Azure Portal mediante la eliminación del grupo de recursos llamado **retail-store-analysis**.

Puede eliminar el panel y los conjuntos de datos de Power BI mediante la eliminación del área de trabajo en la página de configuración de Power BI del área de trabajo.

## <a name="next-steps"></a>Pasos siguientes

En estos tres tutoriales se ha mostrado una solución integral que usa la plantilla de aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) de IoT Central. Ha conectado dispositivos a la aplicación, ha usado IoT Central para supervisar los dispositivos y ha usado Power BI para crear un panel para ver la información de los datos de telemetría de los dispositivos. El siguiente paso recomendado es explorar otra de las plantillas de aplicación de IoT Central:

> [!div class="nextstepaction"]
> [Creación de soluciones de energía con IoT Central](../energy/overview-iot-central-energy.md)
