---
title: Implementación de Azure Virtual Machines para SAP NetWeaver | Microsoft Docs
description: Aprenda a implementar software de SAP en máquinas virtuales Linux en Azure.
services: virtual-machines-linux,virtual-machines-windows
author: MSSedusch
manager: juergent
tags: azure-resource-manager
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: 96fdec13ce028f3cac5f42c7092ca9b189923b9f
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577790"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Implementación de Azure Virtual Machines para SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:/cli/azure/install-classic-cli
[azure-cli-2]:/cli/azure/install-azure-cli
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Implementación de DBMS de Azure Virtual Machines para SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Almacenamiento en caché de máquinas virtuales y VHD)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Estructura de una implementación de RDBMS)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Alta disponibilidad y recuperación ante desastres con máquinas virtuales de Azure)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 y versiones posteriores)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 y versiones anteriores)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Uso de una imagen de SQL Server desde Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Resumen general de SQL Server para SAP en Azure)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Especificaciones de RDBMS de SQL Server)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configuración de almacenamiento)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Copia de seguridad y restauración)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Consideraciones de rendimiento para copias de seguridad y restauraciones)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Otros)
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md (Implementación de Azure Virtual Machines para SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Recursos SAP)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Implementación de una máquina virtual mediante una imagen personalizada)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Escenario 1: Implementación de una máquina virtual desde Azure Marketplace para SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Escenario 3: Traslado de una máquina virtual desde una ubicación local con un VHD no generalizado de Azure con SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Escenarios de implementación de máquinas virtuales para SAP en Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Implementación de cmdlets de Azure PowerShell)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Descarga e importación de cmdlets de PowerShell significativos para SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Unión de una máquina virtual a un dominio local - solo Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Descarga, instalación y habilitación del agente de máquina virtual de Azure)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (CLI de Azure)
[deployment-guide-4.5]:vm-extension-for-sap.md (Configuración de la extensión de Azure para SAP)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Configuración de la nueva extensión de Azure para SAP con Azure PowerShell)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Configuración de la nueva extensión de Azure para SAP con la CLI de Azure)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Comprobación de la preparación de la extensión de Azure para SAP)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (Comprobación de la preparación de la nueva extensión de Azure para SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Comprobación del estado de la configuración de la extensión de Azure para SAP)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (Comprobación del estado de la configuración de la nueva extensión de Azure para SAP)


[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Solución de problemas de la extensión de Azure para SAP)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (Solución de problemas de la nueva extensión de Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configuración de la extensión de máquina virtual)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configuración de proxy)
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Comprobaciones y solución de problemas)

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Planeamiento e implementación de Azure Virtual Machines para SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Recursos)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Alta disponibilidad y recuperación ante desastres para la ejecución de SAP NetWeaver en Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Alta disponibilidad en los servidores de aplicaciones de SAP)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Uso del inicio automático en las instancias de SAP)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Solo en la nube: implementaciones de máquina virtual en Azure sin dependencias en la red local del cliente)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Entre locales: implementación de una o varias máquinas virtuales de SAP en Azure completamente integradas con la red local)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Regiones de Azure)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Dominios de error)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Dominios de actualización)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Conjuntos de disponibilidad de Azure)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Concepto de máquinas virtuales de Microsoft Azure)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Procedimiento para mover máquinas virtuales del entorno local a Azure con un disco no generalizado)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Implementación de una máquina virtual con una imagen específica del cliente)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparación para mover máquinas virtuales del entorno local a Azure con un disco no generalizado)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparación para la implementación de una máquina virtual con una imagen específica del cliente para SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparación de máquinas virtuales con SAP para Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Diferencia entre un disco de Azure y una imagen de Azure)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Carga de un VHD desde una instalación local a Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copia de discos entre cuentas de Azure Storage)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Estructura de VM/VHD para implementaciones de SAP)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Configuración de montaje automático para discos conectados)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Solución de supervisión de Azure para SAP)
[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Redes de Microsoft Azure)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Almacenamiento: Microsoft Azure Storage y discos de datos)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (Matriz de disponibilidad de productos SAP)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Implementación y administración de máquinas virtuales con plantillas de Azure Resource Manager y la CLI de Azure)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Administración de máquinas virtuales con Azure Resource Manager y PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines es la solución para organizaciones que necesitan recursos de almacenamiento y proceso, en un tiempo mínimo y sin ciclos de adquisición prolongados. Puede usar Azure Virtual Machines para implementar en Azure aplicaciones clásicas, por ejemplo, aplicaciones basadas en SAP NetWeaver. Amplíe la confiabilidad y disponibilidad de una aplicación sin recursos locales adicionales. Azure Virtual Machines admite la conectividad entre locales, así que podrá integrar Azure Virtual Machines a los dominios locales, las nubes privadas y la infraestructura de sistema SAP de la organización.

En este artículo, trataremos los pasos a seguir para implementar aplicaciones de SAP en máquinas virtuales en Azure, incluidas las opciones de implementación alternativas y solución de problemas. Este artículo se basa en la información de [Implementación y planeamiento de Azure Virtual Machines para SAP NetWeaver][planning-guide]. También complementa la documentación sobre la instalación de SAP y las notas de SAP, que son los recursos principales para la instalación e implementación de software de SAP.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Configurar una máquina virtual de Azure para la implementación de software de SAP conlleva varios pasos y recursos. Antes de empezar, asegúrese de que cumple los requisitos previos para instalar el software SAP en máquinas virtuales en Azure.

### <a name="local-computer"></a>Equipo local

Para administrar máquinas virtuales Windows o Linux, puede usar un script de PowerShell y Azure Portal. Para ambas herramientas, necesita un equipo local que ejecute Windows 7 o una versión posterior de Windows. Si desea administrar solo máquinas virtuales Linux y desea usar un equipo Linux para esta tarea, puede utilizar la CLI de Azure.

### <a name="internet-connection"></a>Conexión a Internet

Para descargar y ejecutar las herramientas y los scripts necesarios para la implementación de software de SAP, debe estar conectado a Internet. Además, la máquina virtual de Azure que ejecuta la extensión de Azure para SAP debe tener acceso a Internet. Si la máquina virtual de Azure forma parte de una red virtual de Azure o de un dominio local, asegúrese de que la configuración de proxy correspondiente esté establecida, tal y como se describe en [Configuración de proxy][deployment-guide-configure-proxy].

### <a name="microsoft-azure-subscription"></a>Suscripción de Microsoft Azure

Necesita una cuenta de Azure activa.

### <a name="topology-and-networking"></a>Topología y redes

Debe definir la topología y la infraestructura de la implementación de SAP en Azure:

* Cuentas de Azure Storage que se utilizarán
* Red virtual en la que desea implementar el sistema SAP
* Grupo de recursos en el que desea implementar el sistema SAP
* Región de Azure en que desea implementar el sistema SAP
* Configuración de SAP (dos o tres niveles)
* Tamaños de máquina virtual y número de discos de datos adicionales que se montarán en las VM
* Configuración de SAP Correction and Transport System (CTS)

Cree y configure las cuentas de Azure Storage (si fuese necesario) o las redes virtuales de Azure antes de comenzar el proceso de implementación del software de SAP. Para más información acerca de cómo crear y configurar estos recursos, consulte [Implementación y planeamiento de Azure Virtual Machines para SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>Ajuste del tamaño de SAP

Obtenga la siguiente información para ajustar el tamaño de SAP:

* Calcule la carga de trabajo de SAP proyectada, por ejemplo, con la herramienta SAP Quick Sizer, y el valor del estándar de rendimiento de las aplicaciones SAP (SAPS)
* Cuánta memoria y cuántos recursos de CPU consume el sistema SAP
* Número de operaciones de entrada/salida (E/S) por segundo
* Ancho de banda de red que se requiere ante una eventual comunicación entre distintas máquinas virtuales en Azure
* Ancho de banda de red que se requiere entre los recursos locales y los sistemas SAP implementados en Azure

### <a name="resource-groups"></a>Grupos de recursos

En Azure Resource Manager, puede usar los grupos de recursos para administrar todos los recursos de aplicación de la suscripción de Azure. Para más información, consulte [Información general de Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Recursos

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos de SAP

Cuando se configura la implementación de software de SAP, se necesitan los siguientes recursos SAP:

* Nota de SAP [1928533], que incluye:
  * La lista de tamaños de máquina virtual de Azure que se admiten para la implementación del software de SAP
  * Información importante sobre la capacidad para los tamaños de máquina virtual de Azure
  * Software de SAP admitido y combinaciones de sistema operativo y base de datos
  * Versión del kernel de SAP requerida para Windows y Linux en Microsoft Azure

* La nota de SAP [2015553] enumera los requisitos previos para las implementaciones de software de SAP admitidas por SAP en Azure.
* La nota de SAP [2178632] contiene información detallada sobre todas las métricas de supervisión notificadas para SAP en Azure.
* La nota de SAP [1409604] incluye la versión de SAP Host Agent necesaria para Windows en Azure.
* La nota de SAP [2191498] incluye la versión de SAP Host Agent necesaria para Linux en Azure.
* La nota de SAP [2243692] incluye información acerca de las licencias de SAP en Linux en Azure.
* La nota de SAP [1984787] incluye información general sobre SUSE Linux Enterprise Server 12.
* La nota de SAP [2002167] incluye información general sobre Red Hat Enterprise Linux 7.x.
* La nota de SAP [2069760] incluye información general sobre Oracle Linux 7.x.
* La nota de SAP [1999351] contiene más soluciones de problemas de la extensión de Azure para SAP.
* La nota de SAP [1597355] incluye información general sobre el espacio de intercambio para Linux.
* La página de [SCN de SAP en Azure](https://wiki.scn.sap.com/wiki/x/Pia7Gg) tiene noticias y una colección de recursos útiles.
* La [WIKI de la comunidad SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene todas las notas de SAP que se necesitan para Linux.
* Cmdlets de PowerShell específicos de SAP que forman parte de [Azure PowerShell][azure-ps].
* Comandos de la CLI de Azure específicos de SAP que forman parte de la [CLI de Azure][azure-cli].

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos de Windows

Estos artículos de Microsoft se refieren a las implementaciones de SAP en Azure:

* [Implementación y planeamiento de Azure Virtual Machines para SAP NetWeaver][planning-guide]
* [Implementación de Azure Virtual Machines para SAP NetWeaver (este artículo)][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Escenarios de implementación de software de SAP en máquinas virtuales de Azure

Tiene varias opciones para implementar máquinas virtuales y sus discos asociados en Azure. Es importante comprender las diferencias entre las opciones de implementación, porque podría seguir pasos diferentes para preparar las máquinas virtuales para la implementación en función del tipo de implementación que elija.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Escenario 1: Implementación de una máquina virtual desde Azure Marketplace para SAP

Para implementar una máquina virtual se puede usar una imagen proporcionada por Microsoft o por un tercero en Azure Marketplace. Marketplace ofrece algunas imágenes de sistema operativo estándar de Windows Server y diferentes distribuciones de Linux. También puede implementar una imagen que incluya SKU del sistema de administración de base de datos (DBMS), por ejemplo, Microsoft SQL Server. Para más información sobre el uso de imágenes con SKU de DBMS, consulte [Implementación de DBMS de Azure Virtual Machines para SAP en NetWeaver][dbms-guide].

El diagrama de flujo siguiente muestra la secuencia de pasos específica de SAP para implementar una máquina virtual desde Azure Marketplace:

![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP con una imagen de máquina virtual de Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Creación de una máquina virtual con Azure Portal

La manera más fácil de crear una máquina virtual nueva con una imagen de Azure Marketplace es mediante Azure Portal.

1.  Ir a <https://portal.azure.com/#create/hub>.  O bien, en el menú de Azure Portal, seleccione **+ Nuevo**.
1.  Seleccione **Proceso** y, a continuación, seleccione el tipo de sistema operativo que desea implementar. Por ejemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) u Oracle Linux 7.2. La vista de lista predeterminada no muestra todos los sistemas operativos compatibles. Seleccione **ver todo** para obtener una lista completa. Para más información acerca de los sistemas operativos compatibles para la implementación de software de SAP, consulte la nota de SAP [1928533].
1.  En la siguiente página, revise los términos y condiciones.
1.  En la lista **Seleccionar un modelo de implementación**, seleccione **Resource Manager**.
1.  Seleccione **Crear**.

El asistente le guiará a través de los parámetros que se requieren para crear la máquina virtual junto con todos los recursos necesarios, como las interfaces de red o las cuentas de almacenamiento. Algunos de estos parámetros son:

1. **Aspectos básicos**:
   * **Name**: nombre del recurso (la máquina virtual).
   * **Tipo de disco de máquina virtual**: seleccione el tipo de disco del disco del SO. Si desea usar Premium Storage para los discos de datos, se recomienda usar Premium Storage para el disco del SO.
   * **Nombre de usuario y contraseña** o **Clave pública SSH**: escriba el nombre de usuario y la contraseña que se crearon durante el aprovisionamiento. En una máquina virtual Linux, también puede escribir la clave de Secure Shell (SSH) pública que se usa para iniciar sesión en la máquina.
   * **Suscripción**: seleccione la suscripción que quiere usar para aprovisionar la nueva máquina virtual.
   * **Grupo de recursos**: nombre del grupo de recursos de la máquina virtual. Puede escribir el nombre de un grupo de recursos nuevo o uno existente.
   * **Ubicación**: lugar en donde se implementará la nueva máquina virtual. Si desea conectar la máquina virtual con la red local, asegúrese de seleccionar la ubicación de la red virtual que conecta Azure con su red local. Para más información, consulte [Redes de Microsoft Azure][planning-guide-microsoft-azure-networking] en [Planeación e implementación de Azure Virtual Machines para SAP en NetWeaver][planning-guide].
1. **Size**:

   Lea la nota de SAP [1928533] para ver una lista de los tipos de máquinas virtuales que se admiten. Asegúrese de seleccionar el tipo de máquina virtual correcto si quiere usar Azure Premium Storage. No todos los tipos de VM son compatibles con Premium Storage. Para más información consulte [Almacenamiento: Microsoft Azure Storage y los discos de datos][planning-guide-storage-microsoft-azure-storage-and-data-disks] y [Azure Storage para cargas de trabajo de SAP](./planning-guide-storage.md) en [Planeación e implementación de Azure Virtual Machines para SAP en NetWeaver][planning-guide].

1. **Configuración**:
   * **Storage**
     * **Tipo de disco**: seleccione el tipo de disco del disco del SO. Si desea usar Premium Storage para los discos de datos, se recomienda usar Premium Storage para el disco del SO.
     * **Usar discos administrados**: si desea usar Managed Disks, seleccione Sí. Para más información sobre Managed Disks, consulte el capítulo [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) en la guía de planeamiento.
     * **Cuenta de almacenamiento**: Seleccione una cuenta de almacenamiento existente o cree una. No todos los tipos de almacenamiento admiten la ejecución de aplicaciones SAP. Para más información sobre los tipos de almacenamiento, consulte [Estructura de almacenamiento de una máquina virtual para las implementaciones de RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Network**
     * **Red virtual** y **Subred**: seleccione la red virtual que está conectada a la red local si desea integrar la máquina virtual en la intranet.
     * **Dirección IP pública**: seleccione la dirección IP pública que quiere usar o escriba los parámetros para crear una dirección IP pública. Puede usar una dirección IP pública para tener acceso a la máquina virtual a través de Internet. Asegúrese de crear también un grupo de seguridad de red para ayudar a proteger el acceso a la máquina virtual.
     * **Grupo de seguridad de red**: para más información, consulte [Control del flujo de tráfico de red con grupos de seguridad de red][virtual-networks-nsg].
   * **Extensiones**: puede instalar extensiones de máquina virtual agregándolas a la implementación. No es necesario agregar extensiones en este paso. Las extensiones necesarias para la compatibilidad con SAP se instalan más adelante. Vea el capítulo [Configuración de la extensión de Azure para SAP][deployment-guide-4.5] de esta guía.
   * **Alta disponibilidad**: seleccione un conjunto de disponibilidad o escriba los parámetros para crear un conjunto de disponibilidad. Para más información, consulte [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3].
   * **Supervisión**
     * **Diagnósticos de arranque**: puede seleccionar **Deshabilitar** para el diagnóstico de arranque.
     * **Diagnósticos del SO invitado**: puede seleccionar **Deshabilitar** para el diagnóstico de supervisión.

1. **Resumen**:

   Revise las opciones seleccionadas y, a continuación, seleccione **Aceptar**.

La máquina virtual se implementa en el grupo de recursos que seleccionó.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Creación de una máquina virtual con una plantilla

También puede crear una máquina virtual con una de las plantillas de SAP publicadas en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github]. O bien puede crear una máquina virtual manualmente con [Azure Portal][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] o la [CLI de Azure][virtual-machines-linux-tutorial].

* [**Plantilla de configuración de dos niveles (solo una máquina virtual)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual.
* [**Plantilla de configuración de dos niveles (solo una máquina virtual): Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual y Managed Disks.
* [**Plantilla de configuración de tres niveles (varias máquinas virtuales)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Use esta plantilla para crear un sistema de tres niveles con varias máquinas virtuales.
* [**Plantilla de configuración de tres niveles (varias máquinas virtuales): Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Use esta plantilla para crear un sistema de tres niveles con varias máquinas virtuales y Managed Disks.

En Azure Portal, especifique los siguientes parámetros para la plantilla:

1. **Aspectos básicos**:
   * **Suscripción**: suscripción que desea usar para implementar la plantilla.
   * **Grupo de recursos**: grupo de recursos que desea usar para implementar la plantilla. Puede crear un grupo de recursos o seleccionar uno en la suscripción.
   * **Ubicación**: dónde se implementará la plantilla. Si ha seleccionado un grupo de recursos, se utilizará la ubicación de ese grupo de recursos.

1. **Configuración**:
   * **Identificador de sistema SAP**: identificador del sistema SAP (SID).
   * **Tipo de SO**: sistema operativo que quiere implementar, por ejemplo, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) u Oracle Linux 7.2.

     La vista de lista no muestra todos los sistemas operativos compatibles. Para más información acerca de los sistemas operativos compatibles para la implementación de software de SAP, consulte la nota de SAP [1928533].
   * **Tamaño del sistema SAP**: tamaño del sistema SAP.

     La cantidad de SAPS que el sistema nuevo proporciona. Si no está seguro de cuántos SAPS necesita el sistema, consulte con el integrador de sistemas o el asociado tecnológico de SAP.
   * **Disponibilidad del sistema**: disponibilidad del sistema (solo para las plantillas de tres niveles).

     Seleccione **HA** para una configuración en la que se puede realizar una instalación de alta disponibilidad. Se crean dos servidores de base de datos y dos servidores para ABAP SAP Central Services (ASCS).
   * **Tipo de almacenamiento**: Tipo de almacenamiento que se va a usar (solo para plantillas de dos niveles).

     En los sistemas grandes, se recomienda usar Azure Premium Storage. Para más información acerca de los tipos de almacenamiento, consulte estos recursos:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso de almacenamiento de SSD Premium de Azure para la instancia de DBMS de SAP)
      * [Estructura de almacenamiento de una máquina virtual para las implementaciones de RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Almacenamiento de alto rendimiento para cargas de trabajo de Azure Virtual Machines][storage-premium-storage-preview-portal]
      * [Introducción a Microsoft Azure Storage][storage-introduction]
   * **Nombre de usuario de administrador** y **Contraseña de administrador**: un nombre de usuario y una contraseña.
     Se crea un nuevo usuario para iniciar sesión en la máquina virtual.
   * **New or existing subnet**: (Subred nueva o existente): determina si es necesario crear una red virtual y una subred nuevas o si se debe usar una subred existente. Si ya tiene una red virtual conectada a la red local, seleccione **Existing** (Existente).
   * **Identificador de subred**: Si quiere implementar la máquina virtual en una red virtual existente en la que tiene una subred definida a la que se debe asignar la máquina virtual, asigne un nombre al identificador de esa subred específica. El identificador suele tener este formato: /subscriptions/&lt;id. de suscripción>/resourceGroups/&lt;nombre del grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nombre de red virtual>/subnets/&lt;nombre de subred>

1. **Términos y condiciones**:  
    Lea y acepte los términos legales.

1. Seleccione **Comprar**.

El agente de máquina virtual de Azure se implementa de manera predeterminada cuando se usa una imagen de Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Configuración de los valores de proxy

Según cómo esté configurada la red local, quizás tenga que configurar el servidor proxy en su máquina virtual. Si la máquina virtual está conectada a la red local a través de VPN o ExpressRoute, es posible que la VM no pueda tener acceso a Internet y no pueda descargar las extensiones de VM necesarias ni recopilar información de la infraestructura de Azure para SAP Host Agent a través de la extensión de SAP para Azure. Para más información, consulte [Configuración de proxy][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Unión a un dominio (solo Windows)

Si la implementación de Azure está conectada a una instancia local de Active Directory o DNS mediante una conexión de VPN de sitio a sitio de Azure o ExpressRoute (esto se denomina *entre locales* en [Planeación e implementación de Azure Virtual Machines para SAP en NetWeaver][planning-guide]), se espera que la máquina virtual se una a un dominio local. Para más información acerca de las consideraciones para esta tarea, consulte [Unión de una máquina virtual a un dominio local (solo Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configuración de la extensión de máquina virtual

Para asegurarse de que SAP es compatible con el entorno, configure la extensión de Azure para SAP tal y como se describe en [Configuración de la extensión de Azure para SAP][deployment-guide-4.5]. 

#### <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

Después de crear y de implementar la máquina virtual, debe instalar los componentes de software necesarios en la máquina virtual. Debido a la secuencia de instalación de software e implementación en este tipo de implementación de máquina virtual, el software que se va a instalar ya debe estar disponible, ya sea en Azure, en otra máquina virtual o como un disco que se puede conectar. O bien, considere la posibilidad de utilizar un escenario entre locales en los que ya existe conexión con los recursos locales (recursos compartidos de instalación).

Una vez que se ha implementado la máquina virtual en Azure, siga las mismas directrices e instrucciones para instalar el software de SAP en la máquina virtual de la misma forma que lo haría en un entorno local. Para instalar el software de SAP en una máquina virtual de Azure, SAP y Microsoft recomiendan cargar y guardar los medios de instalación de SAP en los discos duros virtuales de Azure o Managed Disks, o bien crear una máquina virtual de Azure que funcione como un servidor de archivos y que contenga todos los medios de instalación de SAP necesarios.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP

Como las diferentes versiones de un sistema operativo o DBMS tienen requisitos de revisión diferentes, las imágenes que encuentre en Azure Marketplace podrían no ajustarse a sus necesidades. En su lugar, quizás quiera crear una máquina virtual con su propia imagen de máquina virtual de sistema operativo o DBMS, que puede implementar de nuevo más tarde.
Los pasos para crear una imagen privada de Linux y una imagen privada de Windows son diferentes.

---
> ![Logotipo de Windows.][Logo_Windows] Windows
>
> Para preparar una imagen de Windows que pueda usar para implementar varias máquinas virtuales, la configuración de Windows (como el nombre de host y el SID de Windows) debe estar resumida o generalizada en la máquina virtual local. Puede utilizar [sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) para ello.
>
> ![Logotipo de Linux.][Logo_Linux] Linux
>
> Para preparar una imagen de Linux que se pueda usar para implementar varias máquinas virtuales, parte de la configuración de Linux debe estar resumida o generalizada en la máquina virtual local. Puede utilizar `waagent -deprovision` para ello. Para más información, consulte [Capturar una máquina virtual Linux en Azure][virtual-machines-linux-capture-image] y [Guía de usuario del agente de Linux de Azure][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Puede preparar y crear una imagen personalizada y, a continuación, usarla para crear varias máquinas virtuales nuevas. Esto se describe en [Implementación y planeamiento de Azure Virtual Machines para SAP NetWeaver][planning-guide]. Puede configurar el contenido de base de datos con el administrador de aprovisionamiento de software de SAP para instalar un nuevo sistema SAP (restaura una copia de seguridad de base de datos desde un disco conectado a la máquina virtual) o puede restaurar directamente una copia de seguridad de base de datos desde Azure Storage (si DBMS lo permite). Para más información, consulte [Implementación de DBMS en Azure Virtual Machines para SAP NetWeaver][dbms-guide]. Si ya instaló un sistema SAP en la máquina virtual local (especialmente para los sistemas de dos niveles), puede adaptar la configuración del sistema SAP después de realizar la implementación de la máquina virtual de Azure mediante el procedimiento de cambio de nombre del sistema admitido por el administrador de aprovisionamiento de software de SAP (nota de SAP [1619720]). De lo contrario, puede instalar el software de SAP después de la implementación de la máquina virtual de Azure.

El diagrama de flujo siguiente muestra la secuencia de pasos específica de SAP para implementar una máquina virtual desde una imagen personalizada:

![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP con una imagen de máquina virtual de Marketplace privado][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Creación de una máquina virtual con Azure Portal

La manera más fácil de crear una máquina virtual nueva desde una imagen de Managed Disks es mediante Azure Portal. Para obtener más información sobre cómo crear una imagen de Managed Disks, lea [Captura de una imagen administrada de una máquina virtual generalizada en Azure](../../windows/capture-image-resource.md).

1.  Ir a <https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. O bien, en el menú de Azure Portal, seleccione **Imágenes**.
1.  Seleccione la imagen de Managed Disks que desee implementar y haga clic en **Crear VM**.

El asistente le guiará a través de los parámetros que se requieren para crear la máquina virtual junto con todos los recursos necesarios, como las interfaces de red o las cuentas de almacenamiento. Algunos de estos parámetros son:

1. **Aspectos básicos**:
   * **Name**: nombre del recurso (la máquina virtual).
   * **Tipo de disco de máquina virtual**: seleccione el tipo de disco del disco del SO. Si desea usar Premium Storage para los discos de datos, se recomienda usar Premium Storage para el disco del SO.
   * **Nombre de usuario y contraseña** o **Clave pública SSH**: escriba el nombre de usuario y la contraseña que se crearon durante el aprovisionamiento. En una máquina virtual Linux, también puede escribir la clave de Secure Shell (SSH) pública que se usa para iniciar sesión en la máquina.
   * **Suscripción**: seleccione la suscripción que quiere usar para aprovisionar la nueva máquina virtual.
   * **Grupo de recursos**: nombre del grupo de recursos de la máquina virtual. Puede escribir el nombre de un grupo de recursos nuevo o uno existente.
   * **Ubicación**: lugar en donde se implementará la nueva máquina virtual. Si desea conectar la máquina virtual con la red local, asegúrese de seleccionar la ubicación de la red virtual que conecta Azure con su red local. Para más información, consulte [Redes de Microsoft Azure][planning-guide-microsoft-azure-networking] en [Planeación e implementación de Azure Virtual Machines para SAP en NetWeaver][planning-guide].
1. **Size**:

   Lea la nota de SAP [1928533] para ver una lista de los tipos de máquinas virtuales que se admiten. Asegúrese de seleccionar el tipo de máquina virtual correcto si quiere usar Azure Premium Storage. No todos los tipos de VM son compatibles con Premium Storage. Para más información consulte [Almacenamiento: Microsoft Azure Storage y los discos de datos][planning-guide-storage-microsoft-azure-storage-and-data-disks] y [Azure Storage para cargas de trabajo de SAP](./planning-guide-storage.md) en [Planeación e implementación de Azure Virtual Machines para SAP en NetWeaver][planning-guide].

1. **Configuración**:
   * **Storage**
     * **Tipo de disco**: seleccione el tipo de disco del disco del SO. Si desea usar Premium Storage para los discos de datos, se recomienda usar Premium Storage para el disco del SO.
     * **Usar discos administrados**: si desea usar Managed Disks, seleccione Sí. Para más información sobre Managed Disks, consulte el capítulo [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) en la guía de planeamiento.
   * **Network**
     * **Red virtual** y **Subred**: seleccione la red virtual que está conectada a la red local si desea integrar la máquina virtual en la intranet.
     * **Dirección IP pública**: seleccione la dirección IP pública que quiere usar o escriba los parámetros para crear una dirección IP pública. Puede usar una dirección IP pública para tener acceso a la máquina virtual a través de Internet. Asegúrese de crear también un grupo de seguridad de red para ayudar a proteger el acceso a la máquina virtual.
     * **Grupo de seguridad de red**: para más información, consulte [Control del flujo de tráfico de red con grupos de seguridad de red][virtual-networks-nsg].
   * **Extensiones**: puede instalar extensiones de máquina virtual agregándolas a la implementación. No es necesario agregar extensiones en este paso. Las extensiones necesarias para la compatibilidad con SAP se instalan más adelante. Vea el capítulo [Configuración de la extensión de Azure para SAP][deployment-guide-4.5] de esta guía.
   * **Alta disponibilidad**: seleccione un conjunto de disponibilidad o escriba los parámetros para crear un conjunto de disponibilidad. Para más información, consulte [Conjuntos de disponibilidad de Azure][planning-guide-3.2.3].
   * **Supervisión**
     * **Diagnósticos de arranque**: puede seleccionar **Deshabilitar** para el diagnóstico de arranque.
     * **Diagnósticos del SO invitado**: puede seleccionar **Deshabilitar** para el diagnóstico de supervisión.

1. **Resumen**:

   Revise las opciones seleccionadas y, a continuación, seleccione **Aceptar**.

La máquina virtual se implementa en el grupo de recursos que seleccionó.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Creación de una máquina virtual con una plantilla

Para crear una implementación con una imagen de sistema operativo privada mediante Azure Portal, use una de las siguientes plantillas de SAP. Estas plantillas están publicadas en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github]. También puede crear una máquina virtual manualmente con [PowerShell][virtual-machines-upload-image-windows-resource-manager].

* [**Plantilla de configuración de dos niveles (solo una máquina virtual)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual.
* [**Plantilla de configuración de dos niveles (solo una máquina virtual): imagen de Managed Disks** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual y una imagen de Managed Disks.
* [**Plantilla de configuración de tres niveles (varias máquinas virtuales)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Use esta plantilla para crear un sistema de tres niveles con varias máquinas virtuales o su propia imagen de sistema operativo.
* [**Plantilla de configuración de tres niveles (varias máquinas virtuales): imagen de Managed Disks** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Use esta plantilla para crear un sistema de tres niveles con varias máquinas virtuales o su propia imagen de SO y una imagen de Managed Disks.

En Azure Portal, especifique los siguientes parámetros para la plantilla:

1. **Aspectos básicos**:
   * **Suscripción**: suscripción que desea usar para implementar la plantilla.
   * **Grupo de recursos**: grupo de recursos que desea usar para implementar la plantilla. Puede crear un grupo de recursos o seleccionar uno en la suscripción.
   * **Ubicación**: dónde se implementará la plantilla. Si ha seleccionado un grupo de recursos, se utilizará la ubicación de ese grupo de recursos.
1. **Configuración**:
   * **Identificador de sistema SAP**: identificador del sistema SAP.
   * **Tipo de SO**: sistema operativo que quiere implementar (Windows o Linux).
   * **Tamaño del sistema SAP**: tamaño del sistema SAP.

     La cantidad de SAPS que el sistema nuevo proporciona. Si no está seguro de cuántos SAPS necesita el sistema, consulte con el integrador de sistemas o el asociado tecnológico de SAP.
   * **Disponibilidad del sistema**: disponibilidad del sistema (solo para las plantillas de tres niveles).

     Seleccione **HA** para una configuración en la que se puede realizar una instalación de alta disponibilidad. Se crean dos servidores de base de datos y dos servidores para ASCS.
   * **Tipo de almacenamiento**: Tipo de almacenamiento que se va a usar (solo para plantillas de dos niveles).

     En los sistemas grandes, se recomienda usar Azure Premium Storage. Para más información acerca de los tipos de almacenamiento, consulte estos recursos:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso de almacenamiento de SSD Premium de Azure para la instancia de DBMS de SAP)
      * [Estructura de almacenamiento de una máquina virtual para las implementaciones de RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Almacenamiento de alto rendimiento para cargas de trabajo de la máquina virtual de Azure][storage-premium-storage-preview-portal]
      * [Introducción a Microsoft Azure Storage][storage-introduction]
   * **User Image VHD URI** (URI del VHD de la imagen del usuario) (solo plantilla de imagen de disco no administrado): identificador URI del disco duro virtual de la imagen de SO privada; por ejemplo, https://&lt;nombreDeCuenta&gt;.blob.core.windows.net/vhds/userimage.vhd.
   * **User image storage account** (Cuenta de almacenamiento de imagen de usuario) (solo plantilla de imagen de disco no administrado): nombre de la cuenta de almacenamiento donde se almacena la imagen de sistema operativo privada, por ejemplo, &lt;nombreDeCuenta&gt; en https://&lt;nombreDeCuenta&gt;.blob.core.windows.net/vhds/userimage.vhd.
   * **userImageId** (solo plantilla de imagen de disco administrado): Identificador de la imagen de disco administrado que quiere usar
   * **Nombre de usuario de administrador** y **Contraseña de administrador**: nombre de usuario y contraseña.

     Se crea un nuevo usuario para iniciar sesión en la máquina virtual.
   * **New or existing subnet**: (Subred nueva o existente): determina si es necesario crear una red virtual y una subred nuevas o si se debe usar una subred existente. Si ya tiene una red virtual conectada a la red local, seleccione **Existing** (Existente).
   * **Identificador de subred**: Si quiere implementar la máquina virtual en una red virtual existente en la que tiene una subred definida a la que se debe asignar la máquina virtual, asigne un nombre al identificador de esa subred específica. El identificador suele tener este formato: /subscriptions/&lt;id. de suscripción>/resourceGroups/&lt;nombre del grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nombre de red virtual>/subnets/&lt;nombre de subred>

1. **Términos y condiciones**:  
    Lea y acepte los términos legales.

1. Seleccione **Comprar**.

#### <a name="install-the-vm-agent-linux-only"></a>Instalación del agente de máquina virtual (solo Linux)

Para utilizar las plantillas que se describen en la sección anterior, el agente de Linux ya debe estar instalado en la imagen de usuario, o se producirá un error en la implementación. Descargue e instale el agente de máquina virtual en la imagen de usuario, tal y como se describe en [Descarga, instalación y habilitación del agente de máquina virtual de Azure][deployment-guide-4.4]. Si no usa las plantillas, también puede instalar más adelante el agente de máquina virtual.

#### <a name="join-a-domain-windows-only"></a>Unión a un dominio (solo Windows)

Si la implementación de Azure está conectada a una instancia local de Active Directory o DNS mediante una conexión de VPN de sitio a sitio de Azure o Azure ExpressRoute (esto se denomina *entre entornos locales* en [Planeación e implementación de Azure Virtual Machines para SAP en NetWeaver][planning-guide]), se espera que la máquina virtual se una a un dominio local. Para más información acerca de las consideraciones para este paso, consulte [Unión de una máquina virtual a un dominio local (solo Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configuración de los valores de proxy

Según cómo esté configurada la red local, quizás tenga que configurar el servidor proxy en su máquina virtual. Si la máquina virtual está conectada a la red local a través de VPN o ExpressRoute, es posible que la VM no pueda tener acceso a Internet y no pueda descargar las extensiones de VM necesarias ni recopilar información de la infraestructura de Azure para SAP Host Agent a través de la extensión de SAP para Azure, consulte [Configuración de proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configuración de la extensión de máquina virtual de Azure para SAP

Para asegurarse de que SAP es compatible con el entorno, configure la extensión de Azure para SAP tal y como se describe en [Configuración de la extensión de Azure para SAP][deployment-guide-4.5]. 

### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Escenario 3: Traslado de una máquina virtual local con un VHD no generalizado de Azure con SAP

En este escenario, tiene previsto mover un sistema SAP específico desde un entorno local a Azure. Esto puede hacerse cargando a Azure el disco duro virtual que contiene el sistema operativo, los archivos binarios de SAP y los archivos binarios de DBMS, así como los discos duros virtuales con los archivos de registro y de datos del DBMS. A diferencia del escenario descrito en [Escenario 2: Implementación de una máquina virtual con una imagen personalizada para SAP][deployment-guide-3.3], en este caso se mantiene el nombre de host, el SID de SAP y las cuentas de usuario de SAP en la máquina virtual de Azure, dado que están configurados en el entorno local. No es necesario generalizar el sistema operativo. Este escenario se da con mayor frecuencia entre entornos locales, donde una parte del panorama SAP se ejecuta en un entorno local y otra parte del mismo se ejecuta en Azure.

En este escenario, el agente de máquina virtual **no** se instala automáticamente durante la implementación. Como el agente de máquina virtual y la extensión de Azure para SAP son un requisito previo para ejecutar SAP en NetWeaver en Azure, debe descargar, instalar y habilitar los dos componentes manualmente después de crear la máquina virtual.

Para más información acerca del agente de máquina virtual de Azure, consulte los siguientes recursos.

---
> ![Logotipo de Windows.][Logo_Windows] Windows
>
> [Información general del agente de máquina virtual de Azure][virtual-machines-windows-agent-user-guide]
>
> ![Logotipo de Linux.][Logo_Linux] Linux
>
> [Guía de usuario del Agente de Linux de Azure][virtual-machines-linux-agent-user-guide]
>
>

---

El siguiente diagrama de flujo muestra la secuencia de pasos para trasladar una máquina virtual local mediante un disco duro virtual de Azure no generalizado:

![Diagrama de flujo de la implementación de máquinas virtuales para sistemas SAP mediante un disco de máquina virtual][deployment-guide-figure-400]

Si el disco ya está cargado y definido en Azure (consulte [Implementación y planeamiento de Azure Virtual Machines para SAP NetWeaver][planning-guide]), realice las tareas que se describen en las próximas secciones.

#### <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Para crear una implementación con un disco de sistema operativo privado mediante Azure Portal, use la plantilla de SAP que está publicada en el [repositorio azure-quickstart-templates de GitHub][azure-quickstart-templates-github]. También puede crear una máquina virtual manualmente con PowerShell.

* [**Plantilla de configuración de dos niveles (solo una máquina virtual)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual.
* [**Plantilla de configuración de dos niveles (solo una máquina virtual): Managed Disks** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Use esta plantilla para crear un sistema de dos niveles con solo una máquina virtual y Managed Disks.

En Azure Portal, especifique los siguientes parámetros para la plantilla:

1. **Aspectos básicos**:
   * **Suscripción**: suscripción que desea usar para implementar la plantilla.
   * **Grupo de recursos**: grupo de recursos que desea usar para implementar la plantilla. Puede crear un grupo de recursos o seleccionar uno en la suscripción.
   * **Ubicación**: dónde se implementará la plantilla. Si ha seleccionado un grupo de recursos, se utilizará la ubicación de ese grupo de recursos.
1. **Configuración**:
   * **Identificador de sistema SAP**: identificador del sistema SAP.
   * **Tipo de SO**: sistema operativo que quiere implementar (Windows o Linux).
   * **Tamaño del sistema SAP**: tamaño del sistema SAP.

     La cantidad de SAPS que el sistema nuevo proporciona. Si no está seguro de cuántos SAPS necesita el sistema, consulte con el integrador de sistemas o el asociado tecnológico de SAP.
   * **Tipo de almacenamiento**: Tipo de almacenamiento que se va a usar (solo para plantillas de dos niveles).

     En los sistemas grandes, se recomienda usar Azure Premium Storage. Para más información acerca de los tipos de almacenamiento, consulte estos recursos:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso de almacenamiento de SSD Premium de Azure para la instancia de DBMS de SAP)
      * [Estructura de almacenamiento de una máquina virtual para las implementaciones de RDBMS](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Almacenamiento de alto rendimiento para cargas de trabajo de Azure Virtual Machines][storage-premium-storage-preview-portal]
      * [Introducción a Microsoft Azure Storage][storage-introduction]
   * **OS disk VHD URI** (URI del VHD de disco de SO) (solo plantilla de disco no administrado): identificador URI del disco de sistema operativo privado; por ejemplo, https://&lt;nombreDeCuenta&gt;.blob.core.windows.net/vhds/osdisk.vhd.
   * **Identificador de Managed Disks del SO** (solo plantilla de disco administrado): Identificador del disco del SO de Managed Disks, /subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN.
   * **New or existing subnet**: (Subred nueva o existente): determina si es necesario crear una red virtual y una subred nuevas o si se debe usar una subred existente. Si ya tiene una red virtual conectada a la red local, seleccione **Existing** (Existente).
   * **Identificador de subred**: Si quiere implementar la máquina virtual en una red virtual existente en la que tiene una subred definida a la que se debe asignar la máquina virtual, asigne un nombre al identificador de esa subred específica. El identificador suele tener este formato: /subscriptions/&lt;id. de suscripción>/resourceGroups/&lt;nombre del grupo de recursos>/providers/Microsoft.Network/virtualNetworks/&lt;nombre de red virtual>/subnets/&lt;nombre de subred>

1. **Términos y condiciones**:  
    Lea y acepte los términos legales.

1. Seleccione **Comprar**.

#### <a name="install-the-vm-agent"></a>Instalar el agente de VM

Para usar las plantillas que se describen en la sección anterior, el agente de máquina virtual debe estar instalado en el disco de sistema operativo, o se producirá un error en la implementación. Descargue e instale el agente de máquina virtual en la máquina virtual, tal y como se describe en [Descarga, instalación y habilitación del agente de máquina virtual de Azure][deployment-guide-4.4].

Si no usa las plantillas descritas en la sección anterior, también puede instalar más adelante el agente de máquina virtual.

#### <a name="join-a-domain-windows-only"></a>Unión a un dominio (solo Windows)

Si la implementación de Azure está conectada a una instancia local de Active Directory o DNS mediante una conexión de VPN de sitio a sitio de Azure o ExpressRoute (esto se denomina *entre locales* en [Planeación e implementación de Azure Virtual Machines para SAP en NetWeaver][planning-guide]), se espera que la máquina virtual se una a un dominio local. Para más información acerca de las consideraciones para esta tarea, consulte [Unión de una máquina virtual a un dominio local (solo Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Configuración de los valores de proxy

Según cómo esté configurada la red local, quizás tenga que configurar el servidor proxy en su máquina virtual. Si la máquina virtual está conectada a la red local a través de VPN o ExpressRoute, es posible que la VM no pueda tener acceso a Internet y no pueda descargar las extensiones de VM necesarias ni recopilar información de la infraestructura de Azure para SAP Host Agent a través de la extensión de SAP para Azure, consulte [Configuración de proxy][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Configuración de la extensión de máquina virtual de Azure para SAP

Para asegurarse de que SAP es compatible con el entorno, configure la extensión de Azure para SAP tal y como se describe en [Configuración de la extensión de Azure para SAP][deployment-guide-4.5]. 

## <a name="detailed-tasks-for-sap-software-deployment"></a>Tareas detalladas para la implementación de software de SAP

En esta sección se detallan los pasos para realizar tareas específicas en el proceso de configuración e implementación.

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Unión de una máquina virtual a un dominio local (solo Windows)

Si implementa máquinas virtuales de SAP en un escenario entre entornos locales en los que las instancias de Active Directory y DNS locales se extienden a Azure, se espera que las máquinas virtuales estén unidas a un dominio local. Los pasos detallados para unir una máquina virtual a un dominio local y el software adicional que se requiere para ser miembro de un dominio local varía según el cliente. Por lo general, para unir una máquina virtual a un dominio local, debe instalar software adicional, como software antimalware, de copia de seguridad o de supervisión.

En este escenario, también tiene que asegurarse de que si se fuerza la configuración de proxy de Internet cuando una máquina virtual se une a un dominio en su entorno, la cuenta de sistema local de Windows (S-1-5-18) en la máquina virtual invitada tiene la misma configuración de proxy. La manera más sencilla es obligar al servidor proxy mediante una directiva de grupo de dominio que se aplica a los sistemas de del dominio.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Descarga, instalación y habilitación del agente de máquina virtual de Azure

Para las máquinas virtuales que se implementan desde una imagen de sistema operativo que no está generalizada (por ejemplo, una imagen que no se origina en la herramienta de preparación del sistema de Windows o sysprep), debe descargar, instalar y habilitar manualmente el agente de máquina virtual de Azure.

Si implementa una máquina virtual desde Azure Marketplace, este paso no es necesario. Las imágenes de Azure Marketplace ya tienen el agente de máquina virtual de Azure.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Descargue el agente de máquina virtual de Azure:
   1.  Descargue el [paquete del instalador del agente de máquina virtual de Azure](https://go.microsoft.com/fwlink/?LinkId=394789).
   1.  Almacene el paquete MSI del agente de máquina virtual localmente en su equipo o en un servidor.
1. Instale el agente de máquina virtual de Azure:
   1.  Conéctese a la máquina virtual de Azure mediante el Protocolo de Escritorio remoto (RDP).
   1.  Abra una ventana del Explorador de Windows en la máquina virtual y seleccione el directorio de destino del archivo MSI del agente de máquina virtual.
   1.  Arrastre el archivo MSI del instalador del agente de máquina virtual de Azure desde su equipo o servidor local y suéltelo en el directorio de destino en la máquina virtual.
   1.  Haga doble clic en el archivo MSI en la máquina virtual.
1. En el caso de las máquinas virtuales unidas a dominios locales, asegúrese de que las posibles configuraciones de proxy de Internet se apliquen también a la cuenta de sistema local de Windows (S-1-5-18) en la máquina virtual, tal y como se describe en [Configuración de proxy][deployment-guide-configure-proxy]. El agente de máquina virtual se ejecuta en este contexto y necesita poder conectarse a Azure.

No se necesita ninguna interacción del usuario para actualizar el agente de máquina virtual de Azure. El agente de máquina virtual se actualiza automáticamente y no requiere que la máquina virtual se reinicie.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Use los comandos siguientes para instalar el agente de máquina virtual para Linux:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) u Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Si el agente ya está instalado, para actualizar el agente de Linux de Azure, siga los pasos que se indican en [Actualización del agente Linux de Azure en una máquina virtual a la última versión desde GitHub][virtual-machines-linux-update-agent].

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configuración de proxy

Los pasos que se siguen para configurar el proxy en Windows son diferentes en Linux.

#### <a name="windows"></a>Windows

La configuración de proxy debe establecerse correctamente para que la cuenta de sistema local tenga acceso a Internet. Si la configuración de proxy no se establece mediante directiva de grupo, puede configurar las opciones de la cuenta de sistema local.

1. Vaya a **Inicio**, escriba **gpedit.msc** y seleccione **ENTRAR**.
1. Seleccione **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Internet Explorer**. Asegúrese de que la opción **Configuración de proxy por equipo y no por usuario** esté desactivada o no configurada.
1. En el **Panel de Control**, vaya a **Centro de redes y recursos compartidos** > **Opciones de Internet**.
1. En la pestaña **Conexiones**, seleccione el botón **Configuración de LAN**.
1. Desactive la casilla **Detectar la configuración automáticamente**.
1. Active la casilla **Usar un servidor proxy para la LAN** y escriba el puerto y la dirección del servidor proxy.
1. Seleccione el botón **Advanced** (Opciones avanzadas).
1. En el cuadro **Excepciones**, escriba la dirección IP **168.63.129.16**. Seleccione **Aceptar**.

#### <a name="linux"></a>Linux

Configure el proxy correcto en el archivo de configuración del agente invitado de Microsoft Azure, que está en \\etc\\waagent.conf.

Establezca los siguientes parámetros:

1. **HTTP proxy host** (Host de proxy HTTP). Por ejemplo, establézcalo en **proxy.corp.local**.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **HTTP proxy port** (Puerto de proxy HTTP). Por ejemplo, establézcalo en **80**.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Reinicie el agente.

   ```console
   sudo service waagent restart
   ```

Si desea usar los repositorios de Azure, asegúrese de que el tráfico a estos repositorios no pase por la intranet local. Si creó rutas definidas por el usuario para habilitar la tunelización forzada, asegúrese de agregar una ruta que enrute el tráfico hacia los repositorios directamente a Internet y no a través de su conexión de sitio a sitio.

La extensión de VM para SAP también debe poder acceder a Internet. Asegúrese de instalar la nueva extensión de VM para SAP y siga los pasos descritos en [Configuración de la extensión de VM de Azure para soluciones de SAP con la CLI de Azure](vm-extension-for-sap-new.md#fa4428b9-bed6-459a-9dfb-74cc27454481) en la guía de instalación de la extensión de VM para SAP y así configurar el proxy.

* **SLES**

  También es necesario agregar rutas para las direcciones IP que aparecen en \\etc\\regionserverclnt.cfg. La ilustración siguiente muestra un ejemplo:

  ![Tunelización forzada][deployment-guide-figure-50]


* **RHEL**

  También es necesario que agregue rutas para las direcciones IP de los hosts que aparecen en \\etc\\yum.repos.d\\rhui-load-balancers. La ilustración anterior muestra un ejemplo.

* **Oracle Linux**

  No hay ningún repositorio para Oracle Linux en Azure. Debe configurar sus propios repositorios para Oracle Linux o usar los repositorios públicos.

Para más información sobre las rutas definidas por el usuario, consulte [Rutas definidas por el usuario y reenvío IP][virtual-networks-udr-overview].

### <a name="azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Extensión de Azure para SAP

> [!NOTE]
> Declaración general de soporte técnico:  
> La compatibilidad con la extensión de Azure para SAP se proporciona a través de los canales de soporte técnico de SAP. Si necesita ayuda con la extensión de Azure para SAP, abra un caso de soporte técnico con [Soporte técnico de SAP](https://support.sap.com/). 

Cuando la máquina virtual esté preparada tal y como se describe en [Escenarios de implementación de máquinas virtuales para SAP en Azure][deployment-guide-3], el Agente de máquina virtual de Azure se instala en la máquina virtual. El siguiente paso es implementar la extensión de Azure para SAP, que está disponible en el repositorio de extensiones de Azure, en los centros de datos globales de Azure. Para más información, consulte [Configuración de la extensión de Azure para SAP][deployment-guide-4.5].

