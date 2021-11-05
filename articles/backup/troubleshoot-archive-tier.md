---
title: Solución de problemas del nivel de archivo
description: Aprenda a solucionar errores del nivel de archivo para Azure Backup.
ms.topic: troubleshooting
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 560c8900e5d0c06dc436ec1222583db58f7cdda3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090994"
---
# <a name="troubleshooting-recovery-point-archive-using-archive-tier"></a>Solución de problemas de archivo de puntos de recuperación mediante el nivel de archivo

En este artículo se brindan detalles para la solución de problemas de códigos de error que aparecen cuando un punto de recuperación no se puede mover al archivo.

## <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Mensaje de error**: Recovery-Point Type is not eligible for Archive Move (El tipo de punto de recuperación no es válido para mover a archivo).

**Descripción**: este código de error se muestra cuando el tipo de punto de recuperación seleccionado no es válido para moverlo al archivo.

**Acción recomendada**: compruebe que el punto de recuperación sea apto. Consulte [Cargas de trabajo compatibles](archive-tier-support.md#supported-workloads).

## <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Mensaje de error**: Recovery-Point having active dependencies for restore is not eligible for Archive Move (El punto de recuperación que tiene dependencias activas para la restauración no es apto para mover al archivo).

**Descripción**: el punto de recuperación seleccionado tiene dependencias activas y, por tanto, no se puede mover al archivo.

**Acción recomendada**: compruebe que el punto de recuperación sea apto. Consulte [Cargas de trabajo compatibles](archive-tier-support.md#supported-workloads).

## <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Mensaje de error**: Recovery-Point is not eligible for Archive Move as lifespan spent in Vault-Standard-Tier is lesser than the required minimum (El punto de recuperación no es apto para mover al archivo, ya que la vida útil pasada en el nivel estándar del almacén es inferior al mínimo recomendado).

**Descripción**: el punto de recuperación tiene que permanecer en el nivel estándar durante un mínimo de tres meses para las máquinas virtuales de Azure, y de 45 días para SQL Server en VM de Azure.

**Acción recomendada**: compruebe que el punto de recuperación sea apto. Consulte [Cargas de trabajo compatibles](archive-tier-support.md#supported-workloads).

## <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Mensaje de error**: Recovery-Point remaining lifespan is lesser than the required minimum (El tiempo de vida restante del punto de recuperación es inferior al mínimo requerido).

**Descripción**: la duración mínima necesaria para que un punto de recuperación sea apto para mover al archivo es de seis meses.

**Acción recomendada**: compruebe que el punto de recuperación sea apto. Consulte [Cargas de trabajo compatibles](archive-tier-support.md#supported-workloads).

## <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Mensaje de error**: Recovery-Point is not eligible for archive move as it has already been moved to archive tier (El punto de recuperación no es apto para mover al nivel de archivo porque ya se movió al nivel de archivo).

**Descripción**: el punto de recuperación seleccionado ya está en archivo. Por lo tanto, no es apto para moverlo al nivel de archivo.

## <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Mensaje de error**: Datasource Type is not eligible for Recommendation API (El tipo de origen de datos no es apto para la API de recomendación).

**Descripción**: la API de recomendación solo es aplicable a máquinas virtuales de Azure. No es aplicable al tipo de origen de datos seleccionado.

## <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Mensaje de error**: Recovery Point is already rehydrated (El punto de recuperación ya está rehidratado). No se permite la rehidratación en este RP.

**Descripción**: el punto de recuperación seleccionado ya está rehidratado.

## <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Mensaje de error**: Recovery-Point is not eligible for Archive Move (El punto de recuperación no es apto para mover al archivo).

**Descripción**: el punto de recuperación seleccionado no es apto para mover al archivo.

## <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Mensaje de error**: Archive Recovery Point is not rehydrated. Retry Restore after rehydration completed on Archive RP (El punto de recuperación del archivo no está rehidratado. Vuelva a intentar la restauración una vez completada la rehidratación en el RP del archivo).

**Descripción**: el punto de recuperación no está rehidratado. Pruebe a restaurar después de rehidratar el punto de recuperación.

## <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Mensaje de error**: Rehydration is only supported for Archive Recovery Points (La rehidratación solo se admite para puntos de recuperación de archivo).

**Descripción**: no se permite la rehidratación para el punto de recuperación seleccionado.

## <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Mensaje de error**: Rehydration is already In-Progress for Archive Recovery Point (La rehidratación ya está en curso para el punto de recuperación de archivo).

**Descripción**: la rehidratación para el punto de recuperación seleccionado ya está en curso.

## <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Mensaje de error**: El punto de recuperación no se puede mover al nivel de archivo debido a que la duración de retención especificada en la directiva es insuficiente.

**Acción recomendada**: actualice la directiva en el elemento protegido con la configuración de retención adecuada y vuelva a intentarlo.

## <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Mensaje de error**: We're still determining if this Recovery Point can be moved (Aún se está determinando si se puede mover este punto de recuperación).

**Descripción**: todavía se está determinando la preparación para mover el punto de recuperación.

**Acción recomendada**: vuelva a comprobar después de esperar un tiempo.
