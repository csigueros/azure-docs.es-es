---
title: 'Novedades Notas de la versión: Azure Blockchain Service'
description: Obtenga información sobre las novedades de Azure Blockchain Service, como las notas de la versión más recientes, las versiones, los problemas conocidos y los próximos cambios.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea44366dd88ae1b98865727c9a3d3e038c924120
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2021
ms.locfileid: "122652053"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Novedades de Azure Blockchain Service

> Reciba notificaciones para volver a visitar esta página y obtener actualizaciones; para ello, copie y pegue la dirección URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` en el [![icono del lector de fuentes RSS](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522) del lector de fuentes RSS.

Azure Blockchain Service recibe las mejoras de manera continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Funcionalidades nuevas
- Actualizaciones de versiones
- Problemas conocidos

---

## <a name="may-2021"></a>Mayo de 2021

[!INCLUDE [Retirement note](./includes/retirement.md)]

## <a name="june-2020"></a>Junio de 2020

### <a name="version-upgrades"></a>Actualizaciones de versiones

- Versión de Quorum actualizada a 2.6.0. Con la versión 2.6.0, puede enviar transacciones privadas firmadas. Para más información sobre el envío de transacciones privadas, consulte la [documentación de la API Quorum](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis).
- Actualización de la versión de Tessera a 0.10.5.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>El tamaño del contrato y el de la transacción aumentaron a 128 KB

Escriba:  Cambio de configuración

El tamaño del contrato (MaxCodeSize) aumentó a 128 KB para poder implementar contratos inteligentes de mayor tamaño. Además, el tamaño de la transacción (txnSizeLimit) aumentó también a 128 KB. Los cambios de configuración se aplican a los nuevos consorcios creados en Azure Blockchain Service después del 19 de junio de 2020.

### <a name="trietimeout-value-reduced"></a>Valor de TrieTimeout reducido

Escriba:  Cambio de configuración

El valor de TrieTimeout se ha reducido para que el estado en memoria se escriba en el disco con más frecuencia. El menor valor garantiza una recuperación más rápida de un nodo en el caso excepcional de que se produzca un bloqueo de nodo.

## <a name="may-2020"></a>Mayo de 2020

### <a name="version-upgrades"></a>Actualizaciones de versiones

- Actualización de la versión de Ubuntu a 18.04
- Actualización de la versión de Quorum a 2.5.0
- Actualización de la versión de Tessera 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure Blockchain Service admite el envío de transacciones rawPrivate

Escriba:  Característica

Los clientes pueden firmar transacciones privadas fuera de la cuenta en el nodo.

### <a name="two-phase-member-provisioning"></a>Aprovisionamiento de miembros en dos fases

Escriba:  Mejora

Las dos fases ayudan a optimizar los escenarios en los que se crea un miembro en un consorcio de larga duración. La infraestructura de miembros se aprovisiona en la primera fase. En la segunda fase, el miembro se sincroniza con la cadena de bloques. El aprovisionamiento en dos fases ayuda a evitar errores de creación de miembros debido al agotamiento de los tiempos de espera.

## <a name="known-issues"></a>Problemas conocidos

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>La función eth.estimateGas produce una excepción en Quorum v2.6.0

En Quorum v2.6.0, las llamadas a la función *eth.estimateGas* sin proporcionar el parámetro *value* adicional provocan una excepción de *bloqueo del controlador del método*. Se ha notificado al equipo de Quorum y se espera una corrección a finales de julio del 2020. Puede usar las siguientes soluciones alternativas hasta que esté disponible una corrección:

- Evite usar *eth.estimateGas*, ya que puede afectar al rendimiento. Para más información sobre los problemas de rendimiento de eth.estimateGas, consulte [La llamada a la función eth.estimateGs reduce el rendimiento](#calling-ethestimategas-function-reduces-performance). Incluya un valor de gas para cada transacción. La mayoría de las bibliotecas llamarán a eth.estimateGas si no se proporciona un valor de gas, lo que ocasiona que Quorum v2.6.0 se bloquee.
- Si tiene que llamar a *eth.estimateGas*, como solución alternativa, el equipo de Quorum le sugiere que pase el parámetro adicional *value* como *0*.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>La minería de datos se detiene si hay menos de cuatro nodos de validador

Las redes de producción deben tener al menos cuatro nodos de validador. Quorum recomienda que se requieran al menos cuatro nodos de validador para cumplir la tolerancia a errores del bloqueo de IBFT (3F+1). Debe tener al menos dos nodos de nivel *Estándar* de Azure Blockchain Service para obtener cuatro nodos de validador. Un nodo estándar se aprovisiona con dos nodos de validador.

Si la red de cadena de bloques de Azure Blockchain Service no tiene cuatro nodos de validación, la minería de datos podría detenerse en la red. Puede detectar que la minería de datos se ha detenido si establece una alerta en los bloques procesados. En una red en buen estado, el bloque procesado será de 60 bloques por nodo en cinco minutos.

Como mitigación, el equipo de Azure Blockchain Service tiene que reiniciar el nodo. Los clientes deben abrir una solicitud de soporte técnico para reiniciar el nodo. El equipo de Azure Blockchain Service está trabajando para detectar y corregir los problemas de minería de datos automáticamente.

Use el nivel *Estándar* para las implementaciones de nivel de producción. Use el nivel *Básico* para desarrollo, pruebas y prueba de concepto. Después de la creación de un miembro, no se puede cambiar el plan de tarifa entre básico y estándar.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>Blockchain Data Manager requiere el nodo de nivel Estándar

Use el nivel *Estándar* si usa Blockchain Data Manager. El nivel *Básico* solo tiene 4 GB de memoria. Por lo tanto, no es posible escalar al uso que requieren Blockchain Data Manager y otros servicios que se ejecutan en él.

Use el nivel *Básico* para desarrollo, pruebas y prueba de concepto. Después de la creación de un miembro, no se puede cambiar el plan de tarifa entre básico y estándar.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Si hay un gran volumen de llamadas a cuentas de desbloqueo, se provoca el bloqueo de geth.

Un gran volumen de llamadas a cuentas de desbloqueo durante el envío de transacciones puede provocar que geth se bloquee en un nodo de transacción. Como resultado, debe detener la ingesta de transacciones. De lo contrario, aumenta la cola de transacciones pendientes.

Geth se reinicia automáticamente en menos de un minuto. En función del nodo, la sincronización puede tardar mucho tiempo. El equipo de Azure Blockchain Service está habilitando una característica de archivado que reducirá el tiempo de sincronización.

Para identificar los bloqueos de geth, puede consultar los registros en busca de mensajes de error en los mensajes de Blockchain en los registros de aplicaciones. También puede comprobar si los bloques procesados disminuyen mientras aumentan las transacciones pendientes.

Para mitigar el problema, envíe transacciones firmadas en lugar de transacciones sin firmar con un comando para desbloquear la cuenta. En el caso de las transacciones que ya están firmadas externamente, no es necesario desbloquear la cuenta.

Si desea enviar transacciones sin firmar, desbloquee la cuenta durante un tiempo infinito mediante el envío de 0 como parámetro de hora en el comando de desbloqueo. Puede volver a bloquear la cuenta después de enviar todas las transacciones.

A continuación se muestran los parámetros de geth que Azure Blockchain Service usa. Puede ajustarlos.

- Istanbul block period (Período de bloque de Estambul): 5 segundos
- Floor gas limit (Límite de gas de piso:): 700 000 000
- Ceil gas limit (Límite de gas de techo): 800 000 000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Un gran volumen de transacciones privadas reduce el rendimiento

Si usa el nivel básico de Azure Blockchain Service y las transacciones privadas, Tessera puede bloquearse.

Puede detectar el bloqueo de Tessera si revisa los registros de aplicación de Blockchain y busca el mensaje `Tessera crashed. Restarting Tessera...`.

Azure Blockchain Service reinicia Tessera cuando se produce un bloqueo. El reinicio tarda aproximadamente un minuto.

Use el nivel *Estándar* si va a enviar un gran volumen de transacciones privadas. Use el nivel *Básico* para desarrollo, pruebas y prueba de concepto. Después de la creación de un miembro, no se puede cambiar el plan de tarifa entre básico y estándar.

### <a name="calling-ethestimategas-function-reduces-performance"></a>La llamada a la función eth.estimateGas reduce el rendimiento.

Al llamar varias veces a la función *eth.estimateGas*, se reducen drásticamente las transacciones por segundo. No utilice la función *eth.estimateGas* para cada envío de transacción. La función *eth.estimateGas* consume mucha memoria.

Si es posible, use un valor de gas moderado para el envío de transacciones y reduzca al mínimo el uso de *eth.estimateGas*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Los bucles ilimitados en los contratos inteligentes reducen el rendimiento

Evite su uso en los contratos inteligentes, ya que pueden reducir el rendimiento. Para obtener más información, consulte los siguientes recursos:

- [Evitar bucles ilimitados](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Procedimientos recomendados de seguridad para contratos inteligentes](https://github.com/ConsenSys/smart-contract-best-practices)
- [Instrucciones para los contratos inteligentes proporcionadas por Quorum](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Directrices sobre los límites de gas y los bucles proporcionados por Solidity](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)