---
title: Solución de problemas de microagente de Defender para IoT (versión preliminar)
description: Aprenda a controlar errores imprevistos o inexplicables.
ms.date: 11/09/2021
ms.topic: reference
ms.openlocfilehash: e30e53e7db9830b9e8f544f761c59ea7b7b3ab94
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709243"
---
# <a name="defender-for-iot-micro-agent-troubleshooting-preview"></a>Solución de problemas de microagente de Defender para IoT (versión preliminar)

Si se produce un error inesperado, puede usar estos métodos de solución de problemas para intentar resolver el problema. Si necesita ayuda, también puede ponerse en contacto con el equipo de producto de Azure Defender for Cloud.   

## <a name="service-status"></a>Estado del servicio 

Para ver el estado del servicio: 

1. Ejecute el siguiente comando.

    ```bash
    systemctl status defender-iot-micro-agent.service 
    ```

1. Compruebe que el servicio es estable. Para ello, debe asegurarse de que está `active` y de que el tiempo de actividad del proceso es el adecuado.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Asegúrese de que el servicio es estable; para ello, compruebe que está activo y que el tiempo de actividad sea el adecuado.":::

Si el agente no aparece en la lista como `inactive`, use el siguiente comando para iniciar el servicio:

```bash
systemctl start defender-iot-micro-agent.service 
```

Sabrá que el servicio se bloquea si el tiempo de actividad del proceso es inferior a 2 minutos. Para resolver este problema, debe [revisar los registros](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>Validación de los privilegios raíz del microagente

Use el siguiente comando para comprobar que el servicio de microagente de Defender para IoT se ejecuta con privilegios raíz.

```bash
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Compruebe que el servicio de microagente de Defender para IoT se ejecuta con privilegios raíz.":::
## <a name="review-the-logs"></a>Revisión de los registros 

Para ver los registros, use el siguiente comando:  

```bash
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Revisión rápida de los registros

Si se produce un problema cuando se ejecuta el microagente, puede ejecutarlo en un estado temporal y podrá ver los registros mediante el siguiente comando:

```bash
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Reinicie el servicio.

Para reiniciar el servicio, use el siguiente comando: 

```bash
sudo systemctl restart defender-iot-micro-agent 
```

## <a name="next-steps"></a>Pasos siguientes

Consulte [Compatibilidad y retirada de características](edge-security-module-deprecation.md).
