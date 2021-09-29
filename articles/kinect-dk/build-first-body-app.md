---
title: 'Inicio rápido: Compilación de una aplicación de seguimiento corporal de Azure Kinect'
description: Instrucciones detalladas para crear la primera aplicación de seguimiento corporal de Azure Kinect
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, sensor, sdk, body, tracking, joint, application, first
ms.openlocfilehash: 7667157e90e9920130084e26c23a7628c7f55b11
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755667"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>Inicio rápido: Compilación de una aplicación de seguimiento corporal de Azure Kinect

¿Desea una introducción al SDK de Body Tracking? Este inicio rápido le ayudará a empezar a realizar el seguimiento corporal. Puede encontrar más ejemplos en el [repositorio Azure-Kinect-Sample](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="prerequisites"></a>Requisitos previos

- [Configuración de Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Configuración del SDK de Body Tracking](body-sdk-setup.md)
- Recorra el inicio rápido en el que se indica cómo [compilar la primera aplicación de Azure Kinect](build-first-app.md).
- Familiarícese con las siguientes funciones del SDK de Sensor:
  - [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- Examine la documentación de las siguientes funciones del SDK de Body Tracking:
  - [k4abt_tracker_create()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>Encabezados

El seguimiento corporal utiliza un solo encabezado, `k4abt.h`. Incluya este encabezado, además de `k4a.h`. Asegúrese de que el compilador que elija está configurado para las carpetas `lib` y `include` del SDK de Sensor y del SDK de Body Tracking. También necesita establecer un vínculo con los archivos `k4a.lib` y `k4abt.lib`. La ejecución de la aplicación requiere que `k4a.dll`, `k4abt.dll`, `onnxruntime.dll` y `dnn_model.onnx` estén en la ruta de acceso de ejecución de las aplicaciones.

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>Apertura del dispositivo e inicio de la cámara

La primera aplicación de seguimiento corporal presupone que hay un único dispositivo Azure Kinect conectado al equipo.

El seguimiento corporal utiliza el SDK de Sensor. Para usar el seguimiento corporal, primero es preciso abrir y configurar el dispositivo. Utilice la función [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) para abrir el dispositivo y, después, configúrelo con un objeto [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html). Para obtener los mejores resultados posibles, establezca el modo de profundidad en `K4A_DEPTH_MODE_NFOV_UNBINNED` o `K4A_DEPTH_MODE_WFOV_2X2BINNED`. El rastreador corporal no se ejecutará si el modo de profundidad está establecido en `K4A_DEPTH_MODE_OFF` o `K4A_DEPTH_MODE_PASSIVE_IR`.

En [esta página](find-then-open-device.md) puede encontrar más información sobre cómo buscar y abrir el dispositivo.

Puede encontrar más información sobre los modos de profundidad de Azure Kinect en estas páginas: [especificaciones de hardware](hardware-specification.md) y enumeraciones [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d).

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>Creación del seguimiento

El primer paso para obtener los resultados del seguimiento corporal es crear un rastreador corporal. Necesita la estructura [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) de calibración del sensor. La calibración del sensor se puede consultar mediante la función [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78).

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Obtención de capturas del dispositivo Azure Kinect

En [esta página](retrieve-images.md) puede encontrar más información sobre la recuperación de datos de imágenes.

```C
// Capture a depth frame
k4a_capture_t sensor_capture;
k4a_device_get_capture(device, &sensor_capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>Poner en cola la captura y sacar los resultados

El rastreador mantiene internamente una cola de entrada y una cola de salida para procesar asincrónicamente las capturas de Azure Kinect DK de forma más eficaz. El siguiente paso es usar la función `k4abt_tracker_enqueue_capture()` para agregar una nueva captura a la cola de entrada. Utilice la función `k4abt_tracker_pop_result()` para sacar un resultado de la cola de salida. El valor del tiempo de espera depende de la aplicación y controla el tiempo de espera de la cola.

La primera aplicación de seguimiento corporal usa el patrón de procesamiento en tiempo real. Para obtener una explicación detallada de los otros patrones, consulte el artículo sobre cómo [obtener los resultados del seguimiento corporal](get-body-tracking-results.md).

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>Acceso a los datos de resultados del seguimiento corporal

Los resultados del seguimiento corporal de la captura de cada sensor se almacenan en una estructura [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html) del armazón corporal. Cada armazón corporal contiene tres componentes clave: una colección de estructuras corporales, un mapa del índice corporal en 2D y la captura de entrada.

La primera aplicación de seguimiento corporal solo accede al número de cuerpos detectados. Para obtener una explicación detallada de los datos que hay en un armazón corporal, consulte el artículo sobre el [acceso a los datos de un armazón corporal](access-data-body-frame.md).

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>Limpieza

El último paso es apagar el rastreador corporal y liberar el objeto de seguimiento corporal. También debe detener y cerrar el dispositivo.

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>Origen completo

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Obtención de resultados del seguimiento corporal](get-body-tracking-results.md)
