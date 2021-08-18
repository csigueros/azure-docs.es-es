---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 07/25/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: d20d6ad62c7513ff810c1b151085019136021dc9
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114675190"
---
Hay **12** recomendaciones en esta categoría.

|Recomendación |Descripción |severity |
|---|---|---|
|[ La directiva del filtro de IP predeterminada debe ser Denegar.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5a3d6cdd-8eb3-46d2-ba11-d24a0d47fe65) |La configuración de filtro IP necesita tener reglas definidas para tráfico permitido y denegar de forma predeterminada el resto del tráfico.<br />(Ninguna directiva relacionada) |Media |
|[Los registros de diagnóstico de IoT Hub deben estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/77785808-ce86-4e40-b45f-19110a547397) |Habilite los registros y consérvelos hasta un año. Esto le permite volver a crear seguimientos de actividad con fines de investigación cuando se produce un incidente de seguridad o se pone en peligro la red.<br />(Directiva relacionada: [los registros de diagnóstico de IoT Hub deben estar habilitados](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f383856f8-de7f-44a2-81fc-e5135b5c2aa4)) |Bajo |
|[Credenciales de autenticación idénticas](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/9d07b7e6-2986-4964-a76c-b2689604e212) |Credenciales de autenticación idénticas para la instancia de IoT Hub utilizada por varios dispositivos. Esto puede indicar que hay un dispositivo ilegítimo que suplanta un dispositivo legítimo. También expone el riesgo de suplantación del dispositivo por parte de un atacante<br />(Ninguna directiva relacionada) |Alto |
|[Dispositivos IoT: mensajes infrautilizados de envío del agente.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/a9a59ebb-5d6f-42f5-92a1-036fd0fd1879) |La capacidad del tamaño del mensaje del agente de IoT está actualmente infrautilizada, lo que provoca un aumento en el número de mensajes enviados. Ajuste de los intervalos de mensajes para una mejor utilización<br />(Ninguna directiva relacionada) |Bajo |
|[ Dispositivos IoT: el proceso auditado dejó de enviar eventos.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d74d2738-2485-4103-9919-69c7e63776ec) |Este dispositivo ya no envía eventos de seguridad originados por un proceso Auditd.<br />(Ninguna directiva relacionada) |Alto |
|[Dispositivos IoT: puertos abiertos en el dispositivo.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1a36f14a-8bd8-45f5-abe5-eef88d76ab5b) |Se encontró un punto de conexión de escucha en el dispositivo.<br />(Ninguna directiva relacionada) |Media |
|[ Dispositivos IoT: error de validación de línea base del sistema operativo](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/5f65e47f-7a00-4bf3-acae-90ee441ee876) |Problemas de configuración del sistema identificados relacionados con la seguridad<br />(Ninguna directiva relacionada) |Media |
|[Dispositivos IoT: se encontró una directiva de firewall permisiva en una de las cadenas.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/beb62be3-5e78-49bd-ac5f-099250ef3c7c) |Se encontró una directiva de firewall permitida en las cadenas de firewall principales (ENTRADA/SALIDA). La directiva debe denegar todo el tráfico de manera predeterminada y definir reglas para permitir la comunicación necesaria hacia y desde el dispositivo.<br />(Ninguna directiva relacionada) |Media |
|[ Dispositivos IoT: se encontró una regla de firewall permisiva en la cadena de entrada.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ba975338-f956-41e7-a9f2-7614832d382d) |Se ha encontrado una regla en el firewall que contiene un patrón de permisos para una amplia gama de puertos o direcciones IP.<br />(Ninguna directiva relacionada) |Media |
|[ Dispositivos IoT: se encontró una regla de firewall permisiva en la cadena de salida.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d5a8d84a-9ad0-42e2-80e0-d38e3d46028a) |Se encontró una regla en el firewall que contiene un patrón permisivo para una amplia gama de direcciones IP o puertos.<br />(Ninguna directiva relacionada) |Media |
|[ Dispositivos IoT: es preciso actualizar el conjunto de cifrado de TLS.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/2acc27c6-5fdb-405e-9080-cb66b850c8f5) |Se han detectado configuraciones de TLS no seguras. Se recomienda actualizar el conjunto de cifrado de TLS inmediatamente.<br />(Ninguna directiva relacionada) |Media |
|[Intervalo IP amplio de la regla del filtro de IP.](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/d8326952-60bb-40fb-b33f-51e662708a88) |Un intervalo IP de origen de la regla de filtro IP permitido es demasiado grande. Las reglas excesivamente permisivas podrían exponer su instancia de IoT Hub a agentes malintencionados.<br />(Ninguna directiva relacionada) |Media |
|||
