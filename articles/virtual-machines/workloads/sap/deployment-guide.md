---
title: SAP NetWeaver için Azure sanal makineler dağıtımı | Microsoft Docs
description: Azure 'da Linux sanal makinelerinde SAP yazılımı dağıtmayı öğrenin.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: 5d6ea75936383388a57a7822f054e0ea7297471e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695524"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>SAP NetWeaver için Azure sanal makineler dağıtımı

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

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (SAP için Azure sanal makineleri DBMS dağıtımı)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (VM 'Ler ve VHD 'Ler için önbelleğe alma)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Yazılım RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Depolama)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS dağıtımının yapısı)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Azure VM 'Leri ile yüksek kullanılabilirlik ve olağanüstü durum kurtarma)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 ve üzeri)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 ve önceki sürümleri)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Azure Marketi 'nden bir SQL Server görüntüsü kullanma)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Azure 'da SAP için genel SQL Server Özeti)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS için Ayrıntılar)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Depolama yapılandırması)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Yedekleme ve geri yükleme)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Yedekleme ve geri yükleme için performans konuları)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Farklı)
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

[deployment-guide]:deployment-guide.md (SAP için Azure sanal makineleri dağıtımı)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP kaynakları)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Özel bir görüntü kullanarak VM dağıtma)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Senaryo 1: SAP için Azure Marketi 'nden bir VM dağıtma)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Senaryo 2: SAP için özel bir görüntü ile VM dağıtma)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Senaryo 3: SAP ile Genelleştirilmiş olmayan bir Azure VHD kullanarak VM 'yi Şirket içinden taşıma)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Microsoft Azure üzerinde SAP için VM 'Lerin dağıtım senaryoları)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell cmdlet 'leri dağıtma)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (SAP ile ilgili PowerShell cmdlet 'lerini indirme ve içeri aktarma)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (VM 'yi şirket içi etki alanına ekleme-yalnızca Windows)
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 ('Un)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Azure VM aracısını indirme, yükleme ve etkinleştirme)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLı)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (SAP için Azure uzantısını yapılandırma)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Yeni SAP için Azure uzantısını Azure PowerShell ile yapılandırın)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Azure CLı ile SAP için yeni Azure uzantısını yapılandırma)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (SAP için Azure uzantısı için hazırlık denetimi)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (SAP için yeni Azure uzantısı için hazırlık denetimi)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (SAP yapılandırması için Azure uzantısı için sistem durumu denetimi)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (SAP yapılandırması için yeni Azure uzantısı için sistem durumu denetimi)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (SAP için Azure uzantısı sorunlarını giderme)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (SAP için yeni Azure uzantısı sorunlarını giderme)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (SAP için Azure uzantısı sorunlarını giderme-desteğe başvurun)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (SAP için Azure uzantısı sorunlarını giderme-kurulum betiğini çalıştırma)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (SAP için Azure uzantısı sorunlarını giderme-Sysprep sonrasında yeniden dağıtma)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( SAP için Azure uzantısı sorunlarını giderme-Internet bağlantısını çözme)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (VM uzantısını yapılandırma)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Proxy 'yi yapılandırma)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Denetimler ve sorun giderme)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (SAP için Azure sanal makineleri planlama ve uygulama)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Kaynakların)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Azure sanal makinelerde çalışan SAP NetWeaver için yüksek kullanılabilirlik ve olağanüstü durum kurtarma)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP uygulama sunucuları için yüksek kullanılabilirlik)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (SAP örnekleri için autostart 'ı kullanma)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Şirket içi müşteri ağında bağımlılıklar olmadan Azure 'da yalnızca bulut sanal makine dağıtımları)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Azure 'da tek veya birden çok SAP VM 'lerinin şirket içi ağla tam olarak tümleştirildiği şirketler arası dağıtım)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure bölgeleri)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Hata etki alanları)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Yükseltme etki alanları)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure kullanılabilirlik kümeleri)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure sanal makineler kavramı)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Bir VM 'yi Şirket içinden, genelleştirilmiş olmayan bir disk ile Azure 'a taşıma)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Müşteriye özgü görüntüyle VM dağıtma)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Genelleştirilmiş olmayan bir diskle bir VM 'yi Şirket içinden Azure 'a taşımaya yönelik hazırlık)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (SAP için müşteriye özgü bir görüntüyle VM dağıtmaya hazırlanma)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Azure için SAP ile VM 'Leri hazırlama)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure disk ve Azure görüntüsü arasındaki fark)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Şirket içinden Azure 'a VHD yükleme)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Azure depolama hesapları arasında diskleri kopyalama)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP dağıtımları için VM/VHD yapısı)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (İliştirilmiş diskler için otomatik bağlama ayarlama)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (SAP için Azure Izleme çözümü)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure ağı)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Depolama: Microsoft Azure Depolama ve veri diskleri)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP ürün kullanılabilirliği matrisi)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Azure Resource Manager şablonları ve Azure CLı kullanarak sanal makineleri dağıtma ve yönetme)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Azure Resource Manager ve PowerShell kullanarak sanal makineleri yönetme)
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

Azure sanal makineleri, işlem ve depolama kaynakları, en kısa sürede ve uzun tedarik döngüleri olmadan ihtiyaç duyulan kuruluşların çözümüdür. Azure 'da SAP NetWeaver tabanlı uygulamalar gibi klasik uygulamaları dağıtmak için Azure sanal makinelerini kullanabilirsiniz. Uygulamanın güvenilirliğini ve kullanılabilirliğini ek şirket içi kaynaklar olmadan genişletin. Azure sanal makineleri, şirket içi etki alanları, özel bulutlar ve SAP sistem yataya Azure sanal makinelerini tümleştirmenize olanak sağlamak için şirketler arası bağlantıyı destekler.

Bu makalede, farklı dağıtım seçenekleri ve sorun giderme dahil olmak üzere Azure 'daki sanal makinelerde (VM) SAP uygulamaları dağıtma adımları ele alınmaktadır. Bu makalede, [SAP NetWeaver için planlama ve uygulama Için Azure sanal makineler][planning-guide]'de bilgi oluşturulur. Ayrıca SAP yazılım yükleme ve dağıtmaya yönelik birincil kaynaklar olan SAP yükleme belgelerini ve SAP notlarını da tamamlar.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

SAP yazılım dağıtımı için bir Azure sanal makinesi ayarlama, birden çok adım ve kaynak içerir. Başlamadan önce, Azure 'daki sanal makinelere SAP yazılımı yükleme önkoşullarını karşıladığınızdan emin olun.

### <a name="local-computer"></a>Yerel bilgisayar

Windows veya Linux VM 'lerini yönetmek için bir PowerShell betiği ve Azure portal kullanabilirsiniz. Her iki araç için de Windows 7 veya sonraki bir Windows sürümünü çalıştıran yerel bir bilgisayara ihtiyacınız vardır. Yalnızca Linux VM 'lerini yönetmek istiyorsanız ve bu görev için bir Linux bilgisayar kullanmak istiyorsanız, Azure CLı kullanabilirsiniz.

### <a name="internet-connection"></a>Internet bağlantısı

SAP yazılım dağıtımı için gerekli olan araçları ve betikleri indirmek ve çalıştırmak için, Internet 'e bağlı olmanız gerekir. SAP için Azure uzantısını çalıştıran Azure sanal makinesinin Ayrıca Internet erişimine ihtiyacı vardır. Azure VM bir Azure sanal ağının veya şirket içi etki alanının parçasıysa, [proxy 'Yi yapılandırma][deployment-guide-configure-proxy]bölümünde açıklandığı gibi ilgili proxy ayarlarının ayarlandığından emin olun.

### <a name="microsoft-azure-subscription"></a>Microsoft Azure aboneliği

Etkin bir Azure hesabınız olması gerekir.

### <a name="topology-and-networking"></a>Topoloji ve ağ

Azure 'da SAP dağıtımının topolojisini ve mimarisini tanımlamanız gerekir:

* Kullanılacak Azure depolama hesapları
* SAP sistemini dağıtmak istediğiniz sanal ağ
* SAP sistemini dağıtmak istediğiniz kaynak grubu
* SAP sistemini dağıtmak istediğiniz Azure bölgesi
* SAP yapılandırması (iki katmanlı veya üç katmanlı)
* VM 'lere takılacak sanal makine boyutları ve ek veri disklerinin sayısı
* SAP düzeltme ve aktarım sistemi (CTS) yapılandırması

SAP yazılım dağıtım işlemine başlamadan önce Azure depolama hesaplarını (gerekliyse) veya Azure sanal ağlarını oluşturun ve yapılandırın. Bu kaynakları oluşturma ve yapılandırma hakkında daha fazla bilgi için bkz. [SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama][planning-guide].

### <a name="sap-sizing"></a>SAP boyutlandırma

SAP boyutlandırma için aşağıdaki bilgileri öğrenin:

* Örneğin, SAP Quick sizer aracını ve SAP uygulama performansı standardı (SAPS) numarasını kullanarak tahmini SAP iş yükü
* SAP sisteminin gerekli CPU kaynağı ve bellek tüketimi
* Saniye başına gerekli giriş/çıkış (g/ç) işlemleri
* Azure 'da VM 'Ler arasında nihai iletişimin gerekli ağ bant genişliği
* Şirket içi varlıklar ve Azure tarafından dağıtılan SAP sistemi arasında gerekli ağ bant genişliği

### <a name="resource-groups"></a>Kaynak grupları

Azure Resource Manager, Azure aboneliğinizdeki tüm uygulama kaynaklarını yönetmek için kaynak gruplarını kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure Resource Manager'a genel bakış][resource-group-overview].

## <a name="resources"></a>Kaynaklar

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP kaynakları

SAP yazılım dağıtımınızı ayarlarken, aşağıdaki SAP kaynaklarına ihtiyacınız vardır:

* SAP Note [1928533], şunları içerir:
  * SAP yazılımının dağıtımı için desteklenen Azure VM boyutlarının listesi
  * Azure VM boyutları için önemli kapasite bilgileri
  * Desteklenen SAP yazılımı ve işletim sistemi (OS) ve veritabanı birleşimleri
  * Microsoft Azure 'de Windows ve Linux için gereken SAP Kernel sürümü

* SAP Note [2015553] , Azure 'da SAP tarafından desteklenen SAP yazılım dağıtımları için önkoşulları listeler.
* SAP Note [2178632] , Azure 'da SAP için raporlanan tüm izleme ölçümleriyle ilgili ayrıntılı bilgiler içerir.
* SAP Note [1409604] , Azure 'da Windows IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
* SAP Note [2191498] , Azure 'da Linux IÇIN gereken SAP konak Aracısı sürümüne sahiptir.
* SAP Note [2243692] , Azure 'da LINUX üzerinde SAP lisanslama hakkında bilgi içerir.
* SAP Note [1984787] , SUSE Linux Enterprise Server 12 hakkında genel bilgiler içerir.
* SAP Note [2002167] , Red Hat Enterprise Linux 7. x hakkında genel bilgiler içerir.
* SAP Note [2069760] , Oracle Linux 7. x hakkında genel bilgiler içerir.
* SAP Note [1999351] , SAP Için Azure Uzantısı ile ilgili ek sorun giderme bilgilerine sahiptir.
* SAP Note [1597355] , Linux için takas alanı hakkında genel bilgiler içerir.
* [Azure SCN üzerinde SAP](https://wiki.scn.sap.com/wiki/x/Pia7Gg) , Haberler ve yararlı kaynaklara yönelik bir koleksiyon içerir.
* [SAP COMMUNITY WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 'nin Linux için gereklı tüm sap notları vardır.
* [Azure PowerShell][azure-ps]PARÇASı olan SAP 'A özgü PowerShell cmdlet 'leri.
* [Azure CLI][azure-cli]'nın PARÇASı olan SAP 'ye özgü Azure CLI komutları.

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows kaynakları

Bu Microsoft makaleleri Azure 'da SAP dağıtımlarını kapsar:

* [SAP NetWeaver için Azure sanal makineleri planlama ve uygulama][planning-guide]
* [SAP NetWeaver için Azure sanal makineler dağıtımı (Bu makale)][deployment-guide]
* [SAP NetWeaver için Azure sanal makineler DBMS dağıtımı][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Azure VM 'lerinde SAP yazılımı için dağıtım senaryoları

Azure 'da VM 'Leri ve ilişkili diskleri dağıtmaya yönelik birden çok seçeneğiniz vardır. Sanal makinelerinizi seçtiğiniz dağıtım türüne göre dağıtıma hazırlamak için farklı adımlar uygulayabileceğiniz için dağıtım seçenekleri arasındaki farklılıkları anlamak önemlidir.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Senaryo 1: SAP için Azure Marketi 'nden bir VM dağıtma

VM 'nizi dağıtmak için Microsoft veya Azure Market 'te üçüncü taraf tarafından sunulan bir görüntüyü kullanabilirsiniz. Market, Windows Server ve farklı Linux dağıtımlarının bazı standart işletim sistemi görüntülerini sunmaktadır. Ayrıca, veritabanı yönetim sistemi (DBMS) SKU 'Larını içeren bir görüntü da dağıtabilirsiniz. Örneğin, Microsoft SQL Server. DBMS SKU 'Ları ile görüntüleri kullanma hakkında daha fazla bilgi için bkz. [SAP NetWeaver Için Azure sanal MAKINELER DBMS dağıtımı][dbms-guide].

Aşağıdaki akış çizelgesi, Azure Marketi 'nden bir VM dağıtmak için SAP 'ye özgü adımların sırasını göstermektedir:

![Azure Marketi 'nden bir VM görüntüsü kullanarak SAP sistemleri için VM dağıtımı akış çizelgesi][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure portal kullanarak bir sanal makine oluşturun

Azure Marketi 'nden bir görüntüyle yeni bir sanal makine oluşturmanın en kolay yolu Azure portal kullanmaktır.

1.  <https://portal.azure.com/#create/hub> öğesine gidin.  Ya da Azure portal menüsünde **+ Yeni**' yi seçin.
1.  **İşlem**' ı seçin ve ardından dağıtmak istediğiniz işletim sistemi türünü seçin. Örneğin, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) veya Oracle Linux 7,2. Varsayılan liste görünümü desteklenen tüm işletim sistemlerini göstermez. Tam liste için **Tümünü göster** ' i seçin. SAP yazılım dağıtımı için desteklenen işletim sistemleri hakkında daha fazla bilgi için bkz. SAP Note [1928533].
1.  Sonraki sayfada hüküm ve koşulları gözden geçirin.
1.  **Bir dağıtım modeli seçin** kutusunda **Kaynak Yöneticisi**' yi seçin.
1.  **Oluştur**’u seçin.

Sihirbaz, ağ arabirimleri ve depolama hesapları gibi gerekli tüm kaynaklara ek olarak, sanal makineyi oluşturmak için gerekli parametreleri ayarlama sırasında size rehberlik eder. Bu parametrelerden bazıları şunlardır:

1. **Temel bilgiler**:
   * **Ad**: kaynağın adı (sanal makine adı).
   * **VM disk türü**: işletim sistemi diskinin disk türünü seçin. Veri diskleriniz için Premium Depolama kullanmak istiyorsanız, işletim sistemi diski için Premium Depolama kullanmanızı da öneririz.
   * **Kullanıcı adı ve parola** ya da **SSH ortak anahtarı**: sağlama sırasında oluşturulan kullanıcının Kullanıcı adını ve parolasını girin. Linux sanal makinesi için, makinede oturum açmak için kullandığınız genel Secure Shell (SSH) anahtarını girebilirsiniz.
   * **Abonelik**: yeni sanal makineyi sağlamak için kullanmak istediğiniz aboneliği seçin.
   * **Kaynak grubu**: VM için kaynak grubunun adı. Yeni bir kaynak grubunun adını ya da zaten var olan bir kaynak grubunun adını girebilirsiniz.
   * **Konum**: yeni sanal makinenin dağıtılacağı yer. Sanal makineyi şirket içi ağınıza bağlamak istiyorsanız, Azure 'u şirket içi ağınıza bağlayan sanal ağın konumunu seçtiğinizden emin olun. Daha fazla bilgi için bkz. [SAP NetWeaver Için Azure sanal makinelerinde planlama ve uygulama][planning-guide] [Microsoft Azure ağ iletişimi][planning-guide-microsoft-azure-networking] .
1. **Boyut**:

     Desteklenen VM türlerinin bir listesi için bkz. SAP Note [1928533]. Azure Premium Depolama kullanmak istiyorsanız, doğru VM türünü seçtiğinizden emin olun. Tüm VM türleri Premium depolamayı desteklemez. Daha fazla bilgi için bkz. [Depolama: Microsoft Azure depolama ve veri diskleri][planning-guide-storage-microsoft-azure-storage-and-data-disks] ve SAP NETWEAVER for [SAP iş yükleri Için](./planning-guide-storage.md) Azure [sanal makinelerinde planlama ve uygulama][planning-guide].

1. **Ayarlar**:
   * **Depolama**
     * **Disk türü**: işletim sistemi diskinin disk türünü seçin. Veri diskleriniz için Premium Depolama kullanmak istiyorsanız, işletim sistemi diski için Premium Depolama kullanmanızı da öneririz.
     * **Yönetilen diskleri kullan**: yönetilen diskleri kullanmak istiyorsanız Evet ' i seçin. Yönetilen diskler hakkında daha fazla bilgi için bkz. planlama kılavuzunda bölüm [yönetimli diskler](./planning-guide-storage.md#microsoft-azure-storage-resiliency) .
     * **Depolama hesabı**: var olan bir depolama hesabını seçin veya yeni bir tane oluşturun. Tüm depolama türleri SAP uygulamalarını çalıştırmak için çalışmaz. Depolama türleri hakkında daha fazla bilgi için bkz. [bir sanal makinenin depolama yapısına RDBMS dağıtımları](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Ağ**
     * **Sanal ağ** ve **alt ağ**: sanal makineyi intranetle bütünleştirmek için, şirket içi ağınıza bağlı sanal ağı seçin.
     * **Genel IP adresi**: kullanmak ISTEDIĞINIZ genel IP adresini seçin veya yeni BIR genel IP adresi oluşturmak için parametreler girin. Internet üzerinden sanal makinenize erişmek için genel bir IP adresi kullanabilirsiniz. Sanal makinenize erişimi güvenli hale getirmeye yardımcı olmak için bir ağ güvenlik grubu da oluşturduğunuzdan emin olun.
     * **Ağ güvenlik grubu**: daha fazla bilgi için bkz. ağ [güvenlik grupları ile ağ trafiği akışını denetleme][virtual-networks-nsg].
   * **Uzantılar**: sanal makine uzantılarını dağıtıma ekleyerek yükleyebilirsiniz. Bu adımda uzantı eklemeniz gerekmez. SAP desteği için gerekli uzantılar daha sonra yüklenir. Bu kılavuzda [, bkz. sap Için Azure uzantısını yapılandırma][deployment-guide-4.5] bölümü.
   * **Yüksek kullanılabilirlik**: bir kullanılabilirlik kümesi seçin veya yeni bir kullanılabilirlik kümesi oluşturmak için parametreleri girin. Daha fazla bilgi için bkz. [Azure kullanılabilirlik kümeleri][planning-guide-3.2.3].
   * **İzleme**
     * **Önyükleme tanılaması**: önyükleme tanılaması Için **devre dışı bırak** seçeneğini belirleyebilirsiniz.
     * **Konuk işletim sistemi tanılama**: izleme tanılaması Için **devre dışı** seçeneğini belirleyebilirsiniz.

1. **Özet**:

   Seçimlerinizi gözden geçirin ve ardından **Tamam**' ı seçin.

Sanal makineniz seçtiğiniz kaynak grubunda dağıtılır.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Şablon kullanarak sanal makine oluşturma

[Azure-QuickStart-Templates GitHub deposunda][azure-quickstart-templates-github]yayımlanan SAP şablonlarından birini kullanarak bir sanal makine oluşturabilirsiniz. Ayrıca, [Azure Portal][virtual-machines-windows-tutorial], [POWERSHELL][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]veya [Azure CLI][virtual-machines-linux-tutorial]kullanarak el ile bir sanal makine oluşturabilirsiniz.

* [**İki katmanlı yapılandırma (yalnızca bir sanal makine) şablonu** (SAP-2-Tier-Market-image)][sap-templates-2-tier-marketplace-image]

  Yalnızca bir sanal makine kullanarak iki katmanlı bir sistem oluşturmak için bu şablonu kullanın.
* [**İki katmanlı yapılandırma (yalnızca bir sanal makine) şablon tarafından yönetilen diskler** (SAP-2-Tier-Market-Image-MD)][sap-templates-2-tier-marketplace-image-md]

  Yalnızca bir sanal makine ve yönetilen diskler kullanarak iki katmanlı bir sistem oluşturmak için bu şablonu kullanın.
* [**Üç katmanlı yapılandırma (birden çok sanal makine) şablonu** (SAP-3 katmanlı-Market-image)][sap-templates-3-tier-marketplace-image]

  Birden çok sanal makine kullanarak üç katmanlı bir sistem oluşturmak için bu şablonu kullanın.
* [**Üç katmanlı yapılandırma (birden çok sanal makine) şablon tarafından yönetilen diskler** (SAP-3-Tier-Market-Image-MD)][sap-templates-3-tier-marketplace-image-md]

  Birden çok sanal makine ve yönetilen disk kullanarak üç katmanlı bir sistem oluşturmak için bu şablonu kullanın.

Azure portal, şablon için aşağıdaki parametreleri girin:

1. **Temel bilgiler**:
   * **Abonelik**: şablonu dağıtmak için kullanılacak abonelik.
   * **Kaynak grubu**: şablonu dağıtmak için kullanılacak kaynak grubu. Yeni bir kaynak grubu oluşturabilir veya abonelikte var olan bir kaynak grubunu seçebilirsiniz.
   * **Konum**: şablonun dağıtılacağı yer. Var olan bir kaynak grubunu seçtiyseniz bu kaynak grubunun konumu kullanılır.

1. **Ayarlar**:
   * **SAP SISTEM kimliği**: SAP sistem KIMLIĞI (SID).
   * **İşletim sistemi türü**: dağıtmak istediğiniz işletim sistemi (örneğin, Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7,2 (RHEL 7,2) veya Oracle Linux 7,2.

     Liste görünümü desteklenen tüm işletim sistemlerini göstermez. SAP yazılım dağıtımı için desteklenen işletim sistemleri hakkında daha fazla bilgi için bkz. SAP Note [1928533].
   * **SAP sistem boyutu**: SAP sisteminin boyutu.

     Yeni sistemin sağladığı SAPS sayısı. Sistemin kaç tane için gerekli olduğundan emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
   * **Sistem kullanılabilirliği** (yalnızca üç katmanlı şablon): sistem kullanılabilirliği.

     Yüksek kullanılabilirliğe sahip bir yükleme için uygun olan bir yapılandırma için **ha** 'yi seçin. İki veritabanı sunucusu ve ABAP SAP Merkezi Hizmetleri (yoks) için iki sunucu oluşturulur.
   * **Depolama türü** (yalnızca iki katmanlı şablon): kullanılacak depolamanın türü.

     Daha büyük sistemler için, Azure Premium Depolama kullanmanızı kesinlikle öneririz. Depolama türleri hakkında daha fazla bilgi için şu kaynaklara bakın:
      * [SAP DBMS örneği için Azure Premium SSD depolama kullanımı][2367194]
      * [RDBMS dağıtımları için bir sanal makinenin depolama yapısı](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Depolama: Azure sanal makine iş yükleri için yüksek performanslı depolama][storage-premium-storage-preview-portal]
      * [Microsoft Azure Depolama'ya Giriş][storage-introduction]
   * **Yönetici Kullanıcı adı** ve **yönetici parolası**: bir Kullanıcı adı ve parola.
     Sanal makinede oturum açmak için yeni bir Kullanıcı oluşturulur.
   * **Yeni veya mevcut alt ağ**: yeni bir sanal ağın ve alt ağın oluşturulup oluşturulmayacağını veya var olan bir alt ağın kullanılıp kullanılmadığını belirler. Şirket içi ağınıza bağlı bir sanal ağınız zaten varsa, **mevcut**' ı seçin.
   * **Alt ağ kimliği**: VM 'yi tanımlanmış bir alt ağa sahip olduğunuz mevcut bir VNET 'e dağıtmak istiyorsanız, söz konusu alt ağın kimliğini adlandırın. KIMLIK genellikle şuna benzer:/Subscriptions/ &lt; abonelik kimliği>/resourceGroups/ &lt; kaynak grubu adı>/Providers/Microsoft.Network/virtualNetworks/ &lt; sanal ağ adı>/Subnets/ &lt; alt ağ adı>

1. **Hüküm ve koşullar**:  
    Yasal koşulları gözden geçirin ve kabul edin.

1. **Satın al**'ı seçin.

Azure VM Aracısı, Azure Marketi 'nden bir görüntü kullandığınızda varsayılan olarak dağıtılır.

#### <a name="configure-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma

Şirket içi ağınızın nasıl yapılandırıldığına bağlı olarak, VM 'niz üzerinde proxy ayarlamanız gerekebilir. VM 'niz VPN veya ExpressRoute aracılığıyla şirket içi ağınıza bağlıysa, sanal makine Internet 'e erişemeyebilir ve gerekli VM uzantılarını indiremez veya Azure için SAP uzantısı aracılığıyla SAP konak Aracısı için Azure altyapı bilgilerini toplayamaz. Daha fazla bilgi için bkz. [proxy 'Yi yapılandırma][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Etki alanına ekleme (yalnızca Windows)

Azure dağıtımınız bir Azure siteden siteye VPN bağlantısı veya ExpressRoute aracılığıyla şirket içi Active Directory veya DNS örneğine bağlıysa (Bu, [SAP NetWeaver Için Azure sanal makinelerinde planlama ve uygulama][planning-guide]olarak *Şirket içi* olarak adlandırılır), VM 'nin şirket içi etki alanına katılması beklenmektedir. Bu görevle ilgili hususlar hakkında daha fazla bilgi için bkz. [BIR VM 'yi şirket içi etki alanına ekleme (yalnızca Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>VM uzantısını yapılandırma

SAP 'nin ortamınızı desteklediğinden emin olmak için SAP için Azure uzantısını [yapılandırma][deployment-guide-4.5]başlığı altında AÇıKLANDıĞı şekilde SAP Için Azure uzantısını ayarlayın. SAP [kaynakları][deployment-guide-2.2]' nda listelenen kaynaklarda SAP önkoşulları ve SAP kernel ve SAP ana bilgisayar aracısının gerekli en düşük sürümlerini denetleyin.

#### <a name="vm-extension-for-sap-check"></a>SAP denetimi için VM Uzantısı

[Denetimler ve sorun giderme][deployment-guide-troubleshooting-chapter]bölümünde AÇıKLANDıĞı gibi SAP Için VM uzantısının çalışıp çalışmadığını denetleyin.

#### <a name="post-deployment-steps"></a>Dağıtım sonrası adımları

VM 'yi oluşturduktan ve VM dağıtıldıktan sonra, gerekli yazılım bileşenlerini sanal makineye yüklemeniz gerekir. Bu sanal makine dağıtımında Dağıtım/yazılım yükleme sırası nedeniyle, yüklenecek yazılımın Azure 'da, başka bir VM 'de veya eklenebilecek bir disk olarak zaten mevcut olması gerekir. Ya da şirket içi varlıklara (yükleme paylaşımları) bağlantının verildiği bir şirketler arası senaryo kullanmayı düşünün.

VM 'nizi Azure 'da dağıttıktan sonra, VM 'nize SAP yazılımını şirket içi bir ortamda olduğu gibi yüklemek için aynı yönergeleri ve araçları izleyin. SAP yazılımını bir Azure VM 'ye yüklemek için SAP ve Microsoft, SAP yükleme medyasını Azure VHD 'leri veya yönetilen disklere yükleyip depolamanızı veya tüm gerekli SAP yükleme medyasına sahip bir dosya sunucusu olarak çalışacak bir Azure VM oluşturmanızı önerir.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Senaryo 2: SAP için özel bir görüntü ile VM dağıtma

Bir işletim sisteminin veya DBMS 'nin farklı sürümlerinde farklı düzeltme gereksinimleri olduğundan, Azure Marketi 'nde bulduğunuz görüntüler gereksinimlerinize uygun olmayabilir. Bunun yerine, daha sonra yeniden dağıtabileceğiniz kendi OS/DBMS VM görüntünüzü kullanarak bir VM oluşturmak isteyebilirsiniz.
Linux için özel bir görüntü oluşturmak üzere Windows için bir tane oluşturmak üzere farklı adımlar kullanırsınız.

---
> ![Windows logosu.][Logo_Windows] Windows
>
> Birden çok sanal makine dağıtmak için kullanabileceğiniz bir Windows görüntüsünü hazırlamak için, Windows ayarlarının (Windows SID ve ana bilgisayar adı gibi) Şirket içi VM 'de soyut veya Genelleştirilmiş olması gerekir. Bunu yapmak için [Sysprep 'i](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) kullanabilirsiniz.
>
> ![Linux logosu.][Logo_Linux] Linux
>
> Birden çok sanal makine dağıtmak için kullanabileceğiniz bir Linux görüntüsünü hazırlamak için bazı Linux ayarlarının, şirket içi VM 'de soyut veya Genelleştirilmiş olması gerekir. `waagent -deprovision`Bunu yapmak için kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure 'da çalışan bir Linux sanal makinesini][virtual-machines-linux-capture-image] ve [Azure Linux Aracısı kullanıcı kılavuzunu][virtual-machines-linux-agent-user-guide-command-line-options]yakalama.
>
>

---
Özel bir görüntü hazırlayabilir ve oluşturabilir ve sonra birden çok yeni VM oluşturmak için kullanabilirsiniz. Bu, [SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama][planning-guide]konusunda açıklanmaktadır. Yeni bir SAP sistemi yüklemek (sanal makineye bağlı bir diskten bir veritabanı yedeklemesini geri yükler) veya DBMS 'niz destekliyorsa, bir veritabanı yedeklemesini doğrudan Azure Storage 'dan geri yükleyerek, veritabanı içeriğinizi SAP yazılım sağlama Yöneticisi 'ni kullanarak ayarlayın. Daha fazla bilgi için bkz. [SAP NetWeaver Için Azure sanal MAKINELER DBMS dağıtımı][dbms-guide]. Şirket içi sanal makinenize zaten bir SAP sistemi yüklediyseniz (özellikle iki katmanlı sistemler için), SAP yazılım sağlama Yöneticisi tarafından desteklenen sistem yeniden adlandırma yordamını kullanarak SAP sistem ayarlarını Azure VM dağıtımı sonrasında uyarlayabilirsiniz (SAP Note [1619720]). Aksi takdirde, Azure VM 'yi dağıttıktan sonra SAP yazılımını yükleyebilirsiniz.

Aşağıdaki akış çizelgesi, bir sanal makineyi özel bir görüntüden dağıtmaya yönelik, SAP 'ye özgü adımların sırasını göstermektedir:

![Özel Market 'te VM görüntüsü kullanarak SAP sistemleri için VM dağıtımı akış çizelgesi][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure portal kullanarak bir sanal makine oluşturun

Yönetilen bir disk görüntüsünden yeni bir sanal makine oluşturmanın en kolay yolu Azure portal kullanmaktır. Bir disk yönetme görüntüsü oluşturma hakkında daha fazla bilgi için, [Azure 'da Genelleştirilmiş BIR VM 'nin yönetilen görüntüsünü](../../windows/capture-image-resource.md) okuyun

1.  <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> öğesine gidin. Ya da Azure portal menüsünde **görüntüler**' i seçin.
1.  Dağıtmak istediğiniz yönetilen disk görüntüsünü seçin ve **sanal makine oluştur** ' a tıklayın.

Sihirbaz, ağ arabirimleri ve depolama hesapları gibi gerekli tüm kaynaklara ek olarak, sanal makineyi oluşturmak için gerekli parametreleri ayarlama sırasında size rehberlik eder. Bu parametrelerden bazıları şunlardır:

1. **Temel bilgiler**:
   * **Ad**: kaynağın adı (sanal makine adı).
   * **VM disk türü**: işletim sistemi diskinin disk türünü seçin. Veri diskleriniz için Premium Depolama kullanmak istiyorsanız, işletim sistemi diski için Premium Depolama kullanmanızı da öneririz.
   * **Kullanıcı adı ve parola** ya da **SSH ortak anahtarı**: sağlama sırasında oluşturulan kullanıcının Kullanıcı adını ve parolasını girin. Linux sanal makinesi için, makinede oturum açmak için kullandığınız genel Secure Shell (SSH) anahtarını girebilirsiniz.
   * **Abonelik**: yeni sanal makineyi sağlamak için kullanmak istediğiniz aboneliği seçin.
   * **Kaynak grubu**: VM için kaynak grubunun adı. Yeni bir kaynak grubunun adını ya da zaten var olan bir kaynak grubunun adını girebilirsiniz.
   * **Konum**: yeni sanal makinenin dağıtılacağı yer. Sanal makineyi şirket içi ağınıza bağlamak istiyorsanız, Azure 'u şirket içi ağınıza bağlayan sanal ağın konumunu seçtiğinizden emin olun. Daha fazla bilgi için bkz. [SAP NetWeaver Için Azure sanal makinelerinde planlama ve uygulama][planning-guide] [Microsoft Azure ağ iletişimi][planning-guide-microsoft-azure-networking] .
1. **Boyut**:

     Desteklenen VM türlerinin bir listesi için bkz. SAP Note [1928533]. Azure Premium Depolama kullanmak istiyorsanız, doğru VM türünü seçtiğinizden emin olun. Tüm VM türleri Premium depolamayı desteklemez. Daha fazla bilgi için bkz. [Depolama: Microsoft Azure depolama ve veri diskleri][planning-guide-storage-microsoft-azure-storage-and-data-disks] ve SAP NETWEAVER for [SAP iş yükleri Için](./planning-guide-storage.md) Azure [sanal makinelerinde planlama ve uygulama][planning-guide].

1. **Ayarlar**:
   * **Depolama**
     * **Disk türü**: işletim sistemi diskinin disk türünü seçin. Veri diskleriniz için Premium Depolama kullanmak istiyorsanız, işletim sistemi diski için Premium Depolama kullanmanızı da öneririz.
     * **Yönetilen diskleri kullan**: yönetilen diskleri kullanmak istiyorsanız Evet ' i seçin. Yönetilen diskler hakkında daha fazla bilgi için bkz. planlama kılavuzunda bölüm [yönetimli diskler](./planning-guide-storage.md#microsoft-azure-storage-resiliency) .
   * **Ağ**
     * **Sanal ağ** ve **alt ağ**: sanal makineyi intranetle bütünleştirmek için, şirket içi ağınıza bağlı sanal ağı seçin.
     * **Genel IP adresi**: kullanmak ISTEDIĞINIZ genel IP adresini seçin veya yeni BIR genel IP adresi oluşturmak için parametreler girin. Internet üzerinden sanal makinenize erişmek için genel bir IP adresi kullanabilirsiniz. Sanal makinenize erişimi güvenli hale getirmeye yardımcı olmak için bir ağ güvenlik grubu da oluşturduğunuzdan emin olun.
     * **Ağ güvenlik grubu**: daha fazla bilgi için bkz. ağ [güvenlik grupları ile ağ trafiği akışını denetleme][virtual-networks-nsg].
   * **Uzantılar**: sanal makine uzantılarını dağıtıma ekleyerek yükleyebilirsiniz. Bu adımda uzantı eklemeniz gerekmez. SAP desteği için gerekli uzantılar daha sonra yüklenir. Bu kılavuzda [, bkz. sap Için Azure uzantısını yapılandırma][deployment-guide-4.5] bölümü.
   * **Yüksek kullanılabilirlik**: bir kullanılabilirlik kümesi seçin veya yeni bir kullanılabilirlik kümesi oluşturmak için parametreleri girin. Daha fazla bilgi için bkz. [Azure kullanılabilirlik kümeleri][planning-guide-3.2.3].
   * **İzleme**
     * **Önyükleme tanılaması**: önyükleme tanılaması Için **devre dışı bırak** seçeneğini belirleyebilirsiniz.
     * **Konuk işletim sistemi tanılama**: izleme tanılaması Için **devre dışı** seçeneğini belirleyebilirsiniz.

1. **Özet**:

   Seçimlerinizi gözden geçirin ve ardından **Tamam**' ı seçin.

Sanal makineniz seçtiğiniz kaynak grubunda dağıtılır.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Şablon kullanarak sanal makine oluşturma

Azure portal özel bir işletim sistemi görüntüsü kullanarak bir dağıtım oluşturmak için aşağıdaki SAP şablonlarından birini kullanın. Bu şablonlar [Azure-QuickStart-Templates GitHub deposunda][azure-quickstart-templates-github]yayımlanır. Ayrıca, [PowerShell][virtual-machines-upload-image-windows-resource-manager]kullanarak el ile bir sanal makine da oluşturabilirsiniz.

* [**İki katmanlı yapılandırma (yalnızca bir sanal makine) şablonu** (SAP-2-katmanlı-Kullanıcı-görüntü)][sap-templates-2-tier-user-image]

  Yalnızca bir sanal makine kullanarak iki katmanlı bir sistem oluşturmak için bu şablonu kullanın.
* [**İki katmanlı yapılandırma (yalnızca bir sanal makine) şablon tarafından yönetilen disk görüntüsü** (SAP-2-Tier-User-Image-MD)][sap-templates-2-tier-user-image-md]

  Yalnızca bir sanal makine ve yönetilen disk görüntüsü kullanarak iki katmanlı bir sistem oluşturmak için bu şablonu kullanın.
* [**Üç katmanlı yapılandırma (birden çok sanal makine) şablonu** (SAP-3 katmanlı-Kullanıcı-görüntü)][sap-templates-3-tier-user-image]

  Birden çok sanal makine veya kendi işletim sistemi görüntünüzü kullanarak üç katmanlı bir sistem oluşturmak için bu şablonu kullanın.
* [**Üç katmanlı yapılandırma (birden çok sanal makine) şablon tarafından yönetilen disk görüntüsü** (SAP-3-Tier-User-Image-MD)][sap-templates-3-tier-user-image-md]

  Birden çok sanal makine veya kendi işletim sistemi görüntünüzü ve yönetilen bir disk görüntüsünü kullanarak üç katmanlı bir sistem oluşturmak için bu şablonu kullanın.

Azure portal, şablon için aşağıdaki parametreleri girin:

1. **Temel bilgiler**:
   * **Abonelik**: şablonu dağıtmak için kullanılacak abonelik.
   * **Kaynak grubu**: şablonu dağıtmak için kullanılacak kaynak grubu. Yeni bir kaynak grubu oluşturabilir veya abonelikte var olan bir kaynak grubunu seçebilirsiniz.
   * **Konum**: şablonun dağıtılacağı yer. Var olan bir kaynak grubunu seçtiyseniz bu kaynak grubunun konumu kullanılır.
1. **Ayarlar**:
   * **SAP SISTEM kimliği**: SAP sistem kimliği.
   * **İşletim sistemi türü**: dağıtmak istediğiniz işletim sistemi türü (Windows veya Linux).
   * **SAP sistem boyutu**: SAP sisteminin boyutu.

     Yeni sistemin sağladığı SAPS sayısı. Sistemin kaç tane için gerekli olduğundan emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
   * **Sistem kullanılabilirliği** (yalnızca üç katmanlı şablon): sistem kullanılabilirliği.

     Yüksek kullanılabilirliğe sahip bir yükleme için uygun olan bir yapılandırma için **ha** 'yi seçin. İki veritabanı sunucusu ve yoks için iki sunucu oluşturulur.
   * **Depolama türü** (yalnızca iki katmanlı şablon): kullanılacak depolamanın türü.

     Daha büyük sistemler için, Azure Premium Depolama kullanmanızı kesinlikle öneririz. Depolama türleri hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:
      * [SAP DBMS örneği için Azure Premium SSD depolama kullanımı][2367194]
      * [RDBMS dağıtımları için bir sanal makinenin depolama yapısı](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Depolama: Azure sanal makine iş yükleri için yüksek performanslı depolama][storage-premium-storage-preview-portal]
      * [Microsoft Azure Depolama'ya Giriş][storage-introduction]
   * **Kullanıcı görüntüsü VHD URI 'si** (yalnızca yönetilmeyen disk görüntü şablonu): özel işletim SISTEMI görüntüsü VHD 'sinin URI 'si (örneğin, https:// &lt; accountname>. blob.Core.Windows.net/VHDs/userimage.vhd).
   * **Kullanıcı görüntüsü depolama hesabı** (yalnızca yönetilmeyen disk görüntüsü şablonu): özel işletim sistemi görüntüsünün depolandığı depolama hesabının adı, örneğin &lt; AccountName> https:// &lt; AccountName>. blob.Core.Windows.net/VHDs/userimage.vhd.
   * **Userımageıd** (yalnızca yönetilen disk görüntüsü şablonu): kullanmak Istediğiniz yönetilen DISK görüntüsünün kimliği
   * **Yönetici Kullanıcı adı** ve **yönetici parolası**: Kullanıcı adı ve parola.

     Sanal makinede oturum açmak için yeni bir Kullanıcı oluşturulur.
   * **Yeni veya mevcut alt ağ**: yeni bir sanal ağın ve alt ağın oluşturulup oluşturulmayacağını veya var olan bir alt ağın kullanılıp kullanılmadığını belirler. Şirket içi ağınıza bağlı bir sanal ağınız zaten varsa, **mevcut**' ı seçin.
   * **Alt ağ kimliği**: VM 'yi tanımlanmış bir alt ağa sahip olduğunuz mevcut bir VNET 'e dağıtmak istiyorsanız, söz konusu alt ağın kimliğini adlandırın. KIMLIK genellikle şuna benzer:/Subscriptions/ &lt; abonelik kimliği>/resourceGroups/ &lt; kaynak grubu adı>/Providers/Microsoft.Network/virtualNetworks/ &lt; sanal ağ adı>/Subnets/ &lt; alt ağ adı>

1. **Hüküm ve koşullar**:  
    Yasal koşulları gözden geçirin ve kabul edin.

1. **Satın al**'ı seçin.

#### <a name="install-the-vm-agent-linux-only"></a>VM Aracısı 'nı (yalnızca Linux) yükler

Önceki bölümde açıklanan şablonları kullanmak için, Linux aracısının Kullanıcı görüntüsünde zaten yüklü olması gerekir, aksi durumda dağıtım başarısız olur. [Azure VM aracısını indirme, yükleme ve etkinleştirme][deployment-guide-4.4]bölümünde açıklandığı gıbı, VM aracısını Kullanıcı görüntüsüne indirin ve yükleyin. Şablonları kullanmıyorsanız, VM aracısını daha sonra da yükleyebilirsiniz.

#### <a name="join-a-domain-windows-only"></a>Etki alanına ekleme (yalnızca Windows)

Azure dağıtımınız bir Azure siteden siteye VPN bağlantısı veya Azure ExpressRoute aracılığıyla şirket içi Active Directory veya DNS örneğine bağlıysa (Bu, [SAP NetWeaver Için Azure sanal makinelerinde planlama ve uygulama][planning-guide]halinde *Şirket içi* olarak adlandırılır), VM 'nin şirket içi etki alanına katılması beklenmektedir. Bu adımla ilgili konular hakkında daha fazla bilgi için bkz. [BIR VM 'yi şirket içi etki alanına ekleme (yalnızca Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma

Şirket içi ağınızın nasıl yapılandırıldığına bağlı olarak, VM 'niz üzerinde proxy ayarlamanız gerekebilir. VM 'niz VPN veya ExpressRoute aracılığıyla şirket içi ağınıza bağlıysa, sanal makine Internet 'e erişemeyebilir ve gerekli VM uzantılarını indiremeyebilir veya Azure için SAP uzantısı aracılığıyla SAP konak Aracısı için Azure altyapı bilgilerini toplayamayabilir. [proxy 'Yi yapılandırma][deployment-guide-configure-proxy]konusuna bakın.

#### <a name="configure-azure-vm-extension-for-sap"></a>SAP için Azure VM uzantısını yapılandırma

SAP 'nin ortamınızı desteklediğinden emin olmak için SAP için Azure uzantısını [yapılandırma][deployment-guide-4.5]başlığı altında AÇıKLANDıĞı şekilde SAP Için Azure uzantısını ayarlayın. SAP [kaynakları][deployment-guide-2.2]' nda listelenen kaynaklarda SAP önkoşulları ve SAP kernel ve SAP ana bilgisayar aracısının gerekli en düşük sürümlerini denetleyin.

#### <a name="sap-vm-extension-check"></a>SAP VM uzantı denetimi

[Denetimler ve sorun giderme][deployment-guide-troubleshooting-chapter]bölümünde AÇıKLANDıĞı gibi SAP Için VM uzantısının çalışıp çalışmadığını denetleyin.


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Senaryo 3: SAP ile Genelleştirilmiş olmayan bir Azure VHD kullanarak şirket içi VM taşıma

Bu senaryoda, belirli bir SAP sistemini şirket içi bir ortamdan Azure 'a taşımayı planlarsınız. Bu işlemi, işletim sistemi, SAP ikilileri ve sonuç olarak DBMS ikililerini içeren VHD 'yi karşıya yükleyerek ve DBMS 'nin veri ve günlük dosyalarına sahip VHD 'leri Azure 'a yükleyerek yapabilirsiniz. Senaryo 2 ' de açıklanan senaryonun aksine, [SAP için özel bir görüntü Ile VM dağıtma][deployment-guide-3.3], bu durumda, şirket içi ortamda yapılandırıldıklarından, Azure VM 'de konak adı, SAP SID ve SAP Kullanıcı hesaplarını korumnyolursunuz. İşletim sistemini genelleştirmek zorunda değilsiniz. Bu senaryo, SAP 'nin bir kısmının şirket içinde çalıştığı ve Azure üzerinde çalıştığı bir parçası olan şirket içi senaryolar için çoğu zaman geçerlidir.

Bu senaryoda, VM Aracısı dağıtım **sırasında otomatik olarak yüklenmez.** VM Aracısı ve SAP için Azure uzantısı, Azure üzerinde SAP NetWeaver çalıştırmak için gerektiğinden, sanal makineyi oluşturduktan sonra her iki bileşeni de el ile indirmeniz, yüklemeniz ve etkinleştirmeniz gerekir.

Azure VM Aracısı hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın.

---
> ![Windows logosu.][Logo_Windows] Windows
>
> [Azure sanal makine aracısına genel bakış][virtual-machines-windows-agent-user-guide]
>
> ![Linux logosu.][Logo_Linux] Linux
>
> [Azure Linux Aracısı Kullanım Kılavuzu][virtual-machines-linux-agent-user-guide]
>
>

---

Aşağıdaki akış çizelgesi, genelleştirilmiş olmayan bir Azure VHD kullanarak bir şirket içi VM 'yi taşımaya yönelik adımların sırasını göstermektedir:

![VM diski kullanarak SAP sistemleri için VM dağıtımı akış çizelgesi][deployment-guide-figure-400]

Disk zaten Azure 'da yüklenip tanımlanmışsa (bkz. [SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama][planning-guide]), sonraki birkaç bölümde açıklanan görevleri yapın.

#### <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Azure portal aracılığıyla özel bir işletim sistemi diski kullanarak bir dağıtım oluşturmak için [Azure-QuickStart-Templates GitHub deposunda][azure-quickstart-templates-github]yayımlanan SAP şablonunu kullanın. Ayrıca, PowerShell kullanarak el ile bir sanal makine da oluşturabilirsiniz.

* [**İki katmanlı yapılandırma (yalnızca bir sanal makine) şablonu** (SAP-2-katmanlı-User-disk)][sap-templates-2-tier-os-disk]

  Yalnızca bir sanal makine kullanarak iki katmanlı bir sistem oluşturmak için bu şablonu kullanın.
* [**İki katmanlı yapılandırma (yalnızca bir sanal makine) şablon tarafından yönetilen disk** (SAP-2-Tier-User-disk-MD)][sap-templates-2-tier-os-disk-md]

  Yalnızca bir sanal makine ve yönetilen disk kullanarak iki katmanlı bir sistem oluşturmak için bu şablonu kullanın.

Azure portal, şablon için aşağıdaki parametreleri girin:

1. **Temel bilgiler**:
   * **Abonelik**: şablonu dağıtmak için kullanılacak abonelik.
   * **Kaynak grubu**: şablonu dağıtmak için kullanılacak kaynak grubu. Yeni bir kaynak grubu oluşturabilir veya abonelikte var olan bir kaynak grubunu seçebilirsiniz.
   * **Konum**: şablonun dağıtılacağı yer. Var olan bir kaynak grubunu seçtiyseniz bu kaynak grubunun konumu kullanılır.
1. **Ayarlar**:
   * **SAP SISTEM kimliği**: SAP sistem kimliği.
   * **İşletim sistemi türü**: dağıtmak istediğiniz işletim sistemi türü (Windows veya Linux).
   * **SAP sistem boyutu**: SAP sisteminin boyutu.

     Yeni sistemin sağladığı SAPS sayısı. Sistemin kaç tane için gerekli olduğundan emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
   * **Depolama türü** (yalnızca iki katmanlı şablon): kullanılacak depolamanın türü.

     Daha büyük sistemler için, Azure Premium Depolama kullanmanızı kesinlikle öneririz. Depolama türleri hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:
      * [SAP DBMS örneği için Azure Premium SSD depolama kullanımı][2367194]
      * [RDBMS dağıtımları için bir sanal makinenin depolama yapısı](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Depolama: Azure sanal makine iş yükleri için yüksek performanslı depolama][storage-premium-storage-preview-portal]
      * [Microsoft Azure Depolama'ya Giriş][storage-introduction]
   * **Işletim sistemi DISKI VHD URI 'si** (yalnızca yönetilmeyen disk şablonu): özel işletim sistemi diskinin URI 'si (örneğin, https:// &lt; accountname>. blob.Core.Windows.net/VHDs/OSDisk.vhd).
   * **Işletim sistemi diski yönetilen DISK kimliği** (yalnızca yönetilen disk şablonu): yönetilen disk işletim SISTEMI diskinin kimliği,/Subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/Group/Providers/Microsoft.COMPUTE/Disks/WIN
   * **Yeni veya mevcut alt ağ**: yeni bir sanal ağ ve alt ağ oluşturulup oluşturulmayacağını veya var olan bir alt ağın kullanılıp kullanılmadığını belirler. Şirket içi ağınıza bağlı bir sanal ağınız zaten varsa, **mevcut**' ı seçin.
   * **Alt ağ kimliği**: VM 'yi tanımlanmış bir alt ağa sahip olduğunuz mevcut bir VNET 'e dağıtmak istiyorsanız, söz konusu alt ağın kimliğini adlandırın. KIMLIK genellikle şuna benzer:/Subscriptions/ &lt; abonelik kimliği>/resourceGroups/ &lt; kaynak grubu adı>/Providers/Microsoft.Network/virtualNetworks/ &lt; sanal ağ adı>/Subnets/ &lt; alt ağ adı>

1. **Hüküm ve koşullar**:  
    Yasal koşulları gözden geçirin ve kabul edin.

1. **Satın al**'ı seçin.

#### <a name="install-the-vm-agent"></a>VM aracısını yükler

Önceki bölümde açıklanan şablonları kullanmak için, VM aracısının işletim sistemi diskine yüklenmesi gerekir, aksi durumda dağıtım başarısız olur. [Azure VM aracısını indirme, yükleme ve etkinleştirme][deployment-guide-4.4]bölümünde açıklandığı gibi VM aracısını sanal makineye indirin ve yükleyin.

Önceki bölümde açıklanan şablonları kullanmıyorsanız, daha sonra VM aracısını da yükleyebilirsiniz.

#### <a name="join-a-domain-windows-only"></a>Etki alanına ekleme (yalnızca Windows)

Azure dağıtımınız bir Azure siteden siteye VPN bağlantısı veya ExpressRoute aracılığıyla şirket içi Active Directory veya DNS örneğine bağlıysa (Bu, [SAP NetWeaver Için Azure sanal makinelerinde planlama ve uygulama][planning-guide]olarak *Şirket içi* olarak adlandırılır), VM 'nin şirket içi etki alanına katılması beklenmektedir. Bu görevle ilgili hususlar hakkında daha fazla bilgi için bkz. [BIR VM 'yi şirket içi etki alanına ekleme (yalnızca Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Ara sunucu ayarlarını yapılandırma

Şirket içi ağınızın nasıl yapılandırıldığına bağlı olarak, VM 'niz üzerinde proxy ayarlamanız gerekebilir. VM 'niz VPN veya ExpressRoute aracılığıyla şirket içi ağınıza bağlıysa, sanal makine Internet 'e erişemeyebilir ve gerekli VM uzantılarını indiremeyebilir veya Azure için SAP uzantısı aracılığıyla SAP konak Aracısı için Azure altyapı bilgilerini toplayamayabilir. [proxy 'Yi yapılandırma][deployment-guide-configure-proxy]konusuna bakın.

#### <a name="configure-azure-vm-extension-for-sap"></a>SAP için Azure VM uzantısını yapılandırma

SAP 'nin ortamınızı desteklediğinden emin olmak için SAP için Azure uzantısını [yapılandırma][deployment-guide-4.5]başlığı altında AÇıKLANDıĞı şekilde SAP Için Azure uzantısını ayarlayın. SAP [kaynakları][deployment-guide-2.2]' nda listelenen kaynaklarda SAP önkoşulları ve SAP kernel ve SAP ana bilgisayar aracısının gerekli en düşük sürümlerini denetleyin.

#### <a name="sap-vm-check"></a>SAP VM denetimi

[Denetimler ve sorun giderme][deployment-guide-troubleshooting-chapter]bölümünde AÇıKLANDıĞı gibi SAP için VM uzantısının çalışıp çalışmadığını denetleyin.

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>SAP için Azure uzantısı yapılandırmasını güncelleştirme

Aşağıdaki senaryolardan birinde SAP için Azure uzantısı yapılandırmasını güncelleştirin:
* Birleşik Microsoft/SAP ekibi, VM uzantısının yeteneklerini genişletir ve daha fazla veya daha az sayaç ister.
* Microsoft, verileri teslim eden temel Azure altyapısının yeni bir sürümünü tanıtır ve SAP için Azure uzantısının bu değişikliklere uyarlanmasını gerektirir.
* Ek veri disklerini Azure sanal makinenize bağlarsınız veya bir veri diski kaldırırsınız. Bu senaryoda, depolamayla ilgili verilerin koleksiyonunu güncelleştirin. Uç nokta ekleyerek veya silerek veya bir sanal makineye IP adresleri atayarak yapılandırmanızın değiştirilmesi uzantı yapılandırmasını etkilemez.
* Azure VM 'nizin boyutunu (örneğin, a5 boyutundan başka bir VM boyutuna) değiştirirsiniz.
* Yeni ağ arabirimlerini Azure sanal makinenize eklersiniz.

Ayarları güncelleştirmek için SAP için Azure uzantısını [yapılandırma][deployment-guide-4.5]bölümündeki ADıMLARı izleyerek SAP Için Azure uzantısı yapılandırmasını güncelleştirin.

## <a name="detailed-tasks-for-sap-software-deployment"></a>SAP yazılım dağıtımı için ayrıntılı görevler

Bu bölümde, yapılandırma ve dağıtım sürecinde belirli görevleri gerçekleştirmek için ayrıntılı adımlar bulunur.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell cmdlet 'lerini dağıtma

[Azure PowerShell modülünü Install](/powershell/azure/install-az-ps) makalesinde açıklanan adımları izleyin

Genellikle aylık olarak güncellenen PowerShell cmdlet 'lerinin güncelleştirmeleri için sık kontrol edin. [Bu](/powershell/azure/install-az-ps#update-the-azure-powershell-module) makalede açıklanan adımları izleyin. SAP Note [1928533] veya sap Note [2015553]' de aksi belirtilmedikçe, Azure PowerShell cmdlet 'lerinin en son sürümüyle çalışmanız önerilir.

Bilgisayarınızda yüklü olan Azure PowerShell cmdlet 'lerinin sürümünü denetlemek için şu PowerShell komutunu çalıştırın:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLı 'yı dağıtma

[Azure CLI 'Yı yüklemeyin](/cli/azure/install-azure-cli) makalesinde açıklanan adımları izleyin

Genellikle aylık olarak güncellenen Azure CLı güncelleştirmeleri için sık kontrol edin.

Bilgisayarınızda yüklü olan Azure CLı sürümünü denetlemek için şu komutu çalıştırın:

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>VM 'yi şirket içi etki alanına ekleme (yalnızca Windows)

SAP VM 'lerini şirket içi Active Directory ve DNS Azure 'da genişletilebileceği şirketler arası bir senaryoda dağıtırsanız, VM 'Lerin şirket içi etki alanına katılması beklenmektedir. Bir VM 'yi şirket içi etki alanına katmak için uygulayabileceğiniz ayrıntılı adımlar ve şirket içi etki alanının üyesi olması gereken ek yazılımlar, müşteriye göre değişir. Genellikle, bir VM 'yi şirket içi etki alanına katmak için kötü amaçlı yazılımdan koruma yazılımı ve yedekleme veya izleme yazılımı gibi ek yazılımlar yüklemeniz gerekir.

Bu senaryoda Ayrıca, bir VM ortamınızdaki bir etki alanına katıldığında Internet proxy ayarlarının zorlandığından emin olmanız gerekir. Konuk VM 'deki Windows yerel sistem hesabı (S-1-5-18) aynı proxy ayarlarına sahiptir. En kolay seçenek, etki alanındaki sistemler için geçerli olan bir etki alanı grup ilkesi kullanarak proxy 'yi zorlamaktır.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Azure VM aracısını indirme, yükleme ve etkinleştirme

Genelleştirilmiş olmayan bir işletim sistemi görüntüsünden dağıtılan sanal makineler için (örneğin, Windows sistem hazırlığı veya Sysprep, aracı), Azure VM aracısını el ile indirmeniz, yüklemeniz ve etkinleştirmeniz gerekir.

Azure Marketi 'nden bir VM dağıtırsanız, bu adım gerekli değildir. Azure Marketi 'ndeki görüntülerin Azure VM Aracısı zaten var.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Azure VM aracısını indirin:
   1.  [Azure VM Aracısı yükleyici paketini](https://go.microsoft.com/fwlink/?LinkId=394789)indirin.
   1.  VM Aracısı MSI paketini bir kişisel bilgisayarda veya sunucuda yerel olarak depolayın.
1. Azure VM aracısını yükler:
   1.  Uzak Masaüstü Protokolü (RDP) kullanarak dağıtılan Azure VM 'ye bağlanın.
   1.  VM 'de bir Windows Explorer penceresi açın ve VM aracısının MSI dosyası için hedef dizini seçin.
   1.  Azure VM Aracısı yükleyicisi MSI dosyasını yerel bilgisayarınızdan/sunucudan VM 'deki VM aracısının hedef dizinine sürükleyin.
   1.  VM 'deki MSI dosyasına çift tıklayın.
1. Şirket içi etki alanlarına katılmış VM 'Ler için, [proxy 'Yi yapılandırma][deployment-guide-configure-proxy]bölümünde açıklandığı gibi, son Internet proxy ayarlarının VM 'Deki Windows yerel sistem hesabı (S-1-5-18) için de aynı olduğundan emin olun. VM Aracısı bu bağlamda çalışır ve Azure 'a bağlanabiliyor olması gerekir.

Azure VM aracısını güncelleştirmek için Kullanıcı etkileşimi gerekmez. VM Aracısı otomatik olarak güncelleştirilir ve VM 'nin yeniden başlatılmasını gerektirmez.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Linux için VM aracısını yüklemek üzere aşağıdaki komutları kullanın:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) veya Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Aracı zaten yüklüyse, Azure Linux aracısını güncelleştirmek için [BIR VM 'Deki Azure Linux Aracısı 'Nı GitHub 'dan en son sürüme güncelleştirme][virtual-machines-linux-update-agent]bölümünde açıklanan adımları uygulayın.

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Proxy 'yi yapılandırma

Windows 'da proxy 'yi yapılandırmak için gerçekleştirmeniz gereken adımlar, Linux 'ta proxy 'yi yapılandırma yollarından farklıdır.

#### <a name="windows"></a>Windows

Yerel sistem hesabının Internet 'e erişmesi için proxy ayarlarının doğru ayarlanmış olması gerekir. Proxy ayarlarınız grup ilkesi tarafından ayarlanmamışsa, yerel sistem hesabı için ayarları yapılandırabilirsiniz.

1. **Başlat**' a gidin, **gpedit. msc** yazın ve ardından **ENTER**' u seçin.
1.   >    >  **Windows bileşenleri**  >  **Internet Explorer** Yönetim Şablonları bilgisayar yapılandırması ' nı seçin. **Proxy ayarlarını makine başına (Kullanıcı başına değil)** ayarla ayarının devre dışı veya yapılandırılmamış olduğundan emin olun.
1. **Denetim Masası**' nda **Ağ ve Paylaşım Merkezi**  >  **Internet seçenekleri**' ne gidin.
1. **Bağlantılar** sekmesinde, **LAN ayarları** düğmesini seçin.
1. **Ayarları otomatik olarak algıla** onay kutusunu temizleyin.
1. **LAN için bir proxy sunucusu kullan** onay kutusunu seçin ve ardından proxy adresini ve bağlantı noktasını girin.
1. **Gelişmiş** düğmesini seçin.
1. **Özel durumlar** kutusuna **168.63.129.16** IP adresini girin. **Tamam**’ı seçin.

#### <a name="linux"></a>Linux

Aynı anda \\ waagent. conf dosyasında bulunan Microsoft Azure Konuk aracısının yapılandırma dosyasında doğru proxy 'yi yapılandırın \\ .

Aşağıdaki parametreleri ayarlayın:

1. **Http proxy Konağı**. Örneğin, bunu **proxy. Corp. Local** olarak ayarlayın.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **Http proxy bağlantı noktası**. Örneğin, bunu **80** olarak ayarlayın.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Aracıyı yeniden başlatın.

   ```console
   sudo service waagent restart
   ```

Ya \\ \\ da waagent. conf içindeki ara sunucu ayarları, gerekli VM uzantıları için de geçerlidir. Azure depolarını kullanmak istiyorsanız, bu depoların trafiğinin şirket içi intranetinizden gitmediğinden emin olun. Zorlamalı tüneli etkinleştirmek için Kullanıcı tanımlı yollar oluşturduysanız, siteden siteye VPN bağlantınız üzerinden değil, trafiği depolara doğrudan Internet 'e yönlendiren bir yol eklediğinizden emin olun.

* **SLES**

  Ayrıca, \\ vs regionserverclnt. cfg ' de LISTELENEN IP adreslerine yönelik yollar eklemeniz gerekir \\ . Aşağıdaki şekilde bir örnek gösterilmektedir:

  ![Zorlamalı tünel oluşturma][deployment-guide-figure-50]


* **RHEL**

  Ayrıca, \\ vb. \\ . Repos. d \\ rhuı-Load-dengeleyicilerde LISTELENEN konakların IP adresleri için yollar eklemeniz gerekir. Bir örnek için, yukarıdaki şekle bakın.

* **Oracle Linux**

  Azure üzerinde Oracle Linux için depo yok. Oracle Linux için kendi depolarınızı yapılandırmanız veya ortak depoları kullanmanız gerekir.

Kullanıcı tanımlı rotalar hakkında daha fazla bilgi için bkz. [Kullanıcı tanımlı rotalar ve IP iletimi][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>SAP için Azure uzantısını yapılandırma

> [!NOTE]
> Genel destek ekstresi:  
> SAP için Azure uzantısı desteği SAP destek kanalları aracılığıyla sağlanır. SAP için Azure Uzantısı ile ilgili yardıma ihtiyacınız varsa, lütfen [SAP desteğiyle](https://support.sap.com/)bir destek talebi açın. 

VM 'yi [Azure 'DA SAP Için VM 'Lerin dağıtım senaryolarında][deployment-guide-3]açıklandığı şekilde hazırladığınızda, Azure VM Aracısı sanal makineye yüklenir. Sonraki adım, genel Azure veri merkezlerinde Azure uzantı deposunda bulunan SAP için Azure uzantısını dağıtmaktır. Daha fazla bilgi için bkz. [SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama][planning-guide-9.1].

SAP için Azure uzantısının yeni bir sürümünü serbest bırakma sürecimiz. Yeni uzantı, eklenen diskler, ağ arabirimleri ve sanal makinenin kendisi hakkında bilgi almak için sanal makinenin sistem tarafından atanan kimliğini kullanır. Bu kaynaklara erişebilmek için sanal makinenin sistem kimliğinin, sanal makine, işletim sistemi diski, veri diskleri ve ağ arabirimleri için okuyucu izni olması gerekir. Şu anda yalnızca yeni uzantıyı aşağıdaki senaryolarda yüklemeyi öneriyoruz:

1. Uzantıyı Terrayform, Azure Resource Manager şablonları veya Azure CLı veya Azure PowerShell diğer yollarla yüklemek istiyorsunuz
1. Uzantıyı SUSE SLES 15 veya üzeri bir sürüme yüklemek istiyorsunuz.
1. Microsoft veya SAP desteği, yeni uzantıyı yüklemenizi ister
1. Azure Ultra disk veya Standart yönetilen diskler kullanmak istiyorsunuz

Bu senaryolar için, Azure PowerShell için [Azure PowerShell ile yeni Azure uzantısını yapılandırma][deployment-guide-configure-new-extension-ps] bölümündeki adımları izleyin veya Azure CLI IÇIN [Azure CLI ile SAP Için yeni Azure uzantısını][deployment-guide-configure-new-extension-cli] yapılandırın.

SAP için Azure uzantısının standart sürümünü yüklemek ve yapılandırmak üzere [Azure PowerShell][deployment-guide-4.5.1] veya [Azure CLI][deployment-guide-4.5.2] 'yi izleyin.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Linux ve Windows VM 'Leri için Azure PowerShell

PowerShell kullanarak SAP için Azure uzantısı 'nı yüklemek için:

1. Azure PowerShell cmdlet 'inin en son sürümünü yüklediğinizden emin olun. Daha fazla bilgi için bkz. [Azure PowerShell cmdlet 'Leri dağıtma][deployment-guide-4.1].  
1. Aşağıdaki PowerShell cmdlet’ini çalıştırın.
    Kullanılabilir ortamların bir listesi için cmdlet 'ini çalıştırın `Get-AzEnvironment` . Küresel Azure 'u kullanmak istiyorsanız, ortamınız **Azurecyüksek**' dir. Azure Çin 21Vianet için **AzureChinaCloud** öğesini seçin.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Hesap verilerinizi girdikten sonra betik gerekli uzantıları dağıtır ve gerekli özellikleri sunar. Bu işlem birkaç dakika sürebilir.
Hakkında daha fazla bilgi için `Set-AzVMAEMExtension` bkz. [set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![SAP 'ye özgü Azure cmdlet 'inin başarıyla yürütülmesi Set-AzVMAEMExtension][deployment-guide-figure-900]

`Set-AzVMAEMExtension`Yapılandırma, SAP için konak verileri toplamayı yapılandırmak için tüm adımları yapar.

Betik çıktısı aşağıdaki bilgileri içerir:

* İşletim sistemi diski ve tüm ek veri diskleri için veri koleksiyonunun yapılandırıldığını onaylayın.
* Sonraki iki ileti, belirli bir depolama hesabı için depolama ölçümlerini yapılandırmayı onaylayın.
* Bir çıkış satırı, SAP yapılandırması için VM uzantısının gerçek güncelleştirme durumunu verir.
* Başka bir çıktı satırı yapılandırmanın dağıtıldığını veya güncelleştirildiğini onaylar.
* Çıktının son satırı bilgilendirme amaçlıdır. SAP yapılandırması için VM uzantısının test edilmesine yönelik seçeneklerinizi gösterir.
* SAP yapılandırması için Azure VM uzantısının tüm adımlarının başarıyla yürütüldüğünü ve Azure altyapısının gerekli verileri sağladığını denetlemek için SAP için Azure [Uzantısı Için hazırlık denetimi][deployment-guide-5.1]bölümünde açıklandığı gıbı, SAP Için Azure uzantısı için hazır olma denetimi ile devam edin.
* Azure Tanılama ilgili verilerin toplanması için 15-30 dakika bekleyin.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Linux sanal makineleri için Azure CLı

Azure CLı kullanarak SAP için Azure uzantısı 'nı yüklemek için:

   1. Azure klasik CLI 'yı [yüklemeyin][azure-cli].
   1. Azure hesabınızla oturum açın:

      ```console
      azure login
      ```

   1. Azure Resource Manager moduna geç:

      ```console
      azure config mode arm
      ```

   1. SAP için Azure uzantısını etkinleştir:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Azure CLı 2,0 kullanarak yüklemesi

   1. Azure CLI 2,0 'yi, [Azure clı 2,0][azure-cli-2]' de açıklandığı gibi yükler.
   1. Azure hesabınızla oturum açın:

      ```azurecli
      az login
      ```

   1. Azure CLı AEM uzantısını yükler
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Uzantıyı ile birlikte yükler
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. SAP için Azure uzantısının Azure Linux VM 'de etkin olduğunu doğrulayın. Dosya \\ var \\ lib \\ AzureEnhancedMonitor \\ PerfCounters 'in var olup olmadığını denetleyin. Varsa, bir komut isteminde, SAP için Azure uzantısı tarafından toplanan bilgileri göstermek için bu komutu çalıştırın:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Çıktı şuna benzer:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Yeni SAP için Azure uzantısını Azure PowerShell ile yapılandırın

SAP için yeni VM uzantısı, VM 'nin izleme ve yapılandırma verilerine erişmek için VM 'ye atanan bir yönetilen kimlik kullanır. PowerShell 'i kullanarak SAP için yeni Azure uzantısı 'nı yüklemek için, önce VM 'ye bu tür bir kimlik atamanız ve bu kimliğe bu kimlik, sanal makine tarafından kullanılan tüm kaynaklara (örneğin, diskler ve ağ arabirimleri) vermesi gerekir.

> [!NOTE]
> Aşağıdaki adımlarda, kaynak grubu veya ayrı kaynaklar (sanal makine, veri diskleri vb.) üzerinde sahip ayrıcalıklar gerekir

1. SAP konak Aracısı 7,21 PL 47 veya üstünü kullandığınızdan emin olun.
1. SAP için VM uzantısının geçerli sürümünü kaldırdığınızdan emin olun. Aynı sanal makinede SAP için VM uzantısının her iki sürümünün de yüklenmesi desteklenmez.
1. Azure PowerShell cmdlet 'inin en son sürümünü yüklediğinizden emin olun (en az 4.3.0). Daha fazla bilgi için bkz. [Azure PowerShell cmdlet 'Leri dağıtma][deployment-guide-4.1].
1. Aşağıdaki PowerShell cmdlet’ini çalıştırın.
    Kullanılabilir ortamların bir listesi için cmdlet 'ini çalıştırın `Get-AzEnvironment` . Küresel Azure 'u kullanmak istiyorsanız, ortamınız **Azurecyüksek**' dir. Azure Çin 21Vianet için **AzureChinaCloud** öğesini seçin.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Azure CLı ile SAP için yeni Azure uzantısını yapılandırma

SAP için yeni VM uzantısı, VM 'nin izleme ve yapılandırma verilerine erişmek için VM 'ye atanan bir yönetilen kimlik kullanır. Azure CLı kullanarak SAP için yeni Azure uzantısı 'nı yüklemek için, önce VM 'ye bu tür bir kimlik atamanız ve bu kimliğe bu kimlik, sanal makine tarafından kullanılan tüm kaynaklara (örneğin, diskler ve ağ arabirimleri) vermesi gerekir.

> [!NOTE]
> Aşağıdaki adımlarda, kaynak grubu veya ayrı kaynaklar (sanal makine, veri diskleri vb.) üzerinde sahip ayrıcalıklar gerekir

1. SAP konak Aracısı 7,21 PL 47 veya üstünü kullandığınızdan emin olun.
1. SAP için VM uzantısının geçerli sürümünü kaldırdığınızdan emin olun. Aynı sanal makinede SAP için VM uzantısının her iki sürümünün de yüklenmesi desteklenmez.
1. Azure CLI 2,0 'yi, [Azure clı 2,0][azure-cli-2]' de açıklandığı gibi yükler.

1. Azure hesabınızla oturum açın:

   ```azurecli
   az login
   ```

1. Azure CLı AEM uzantısını yükler. En az sürüm 0.2.0 veya üstünü kullandığınızdan emin olun.
  
   ```azurecli
   az extension add --name aem
   ```
  
1. Yeni uzantıyı ile birlikte yükler
  
   ```azurecli
   az vm aem set -g <resource-group-name> -n <vm name> --install-new-extension
   ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Denetimler ve sorun giderme

Azure VM 'nizi dağıttıktan ve SAP için ilgili Azure uzantısını ayarladıktan sonra, uzantının tüm bileşenlerinin beklenen şekilde çalışıp çalışmadığını denetleyin.

SAP için Azure [Uzantısı Için hazır olma denetimi][deployment-guide-5.1]bölümünde AÇıKLANDıĞı gibi SAP Için Azure uzantısı için hazırlık denetimini çalıştırın. Tüm hazırlık denetimi sonuçları pozitifse ve tüm ilgili performans sayaçları Tamam görünüyorsa, SAP için Azure uzantısı başarıyla ayarlanır. SAP ana bilgisayar aracısının SAP [kaynaklarında][deployment-guide-2.2]SAP notları bölümünde açıklandığı gibi yüklenmesine devam edebilirsiniz. Hazırlık denetimi sayaçların eksik olduğunu gösteriyorsa, SAP için Azure uzantısı için [sistem durumu denetimi][deployment-guide-5.2]bölümünde açıklandığı gıbı, SAP Için Azure uzantısı için sistem durumu denetimini çalıştırın. Daha fazla sorun giderme seçeneği için bkz. [SAP Için Azure uzantısı sorunlarını giderme][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>SAP için Azure uzantısı için hazırlık denetimi

> [!NOTE]
> VM uzantısının iki sürümü vardır. Bu bölümde varsayılan VM Uzantısı ele alınmaktadır. Yeni VM uzantısını yüklediyseniz, lütfen [SAP için yeni Azure uzantısı için bölüm hazırlığı denetimine][deployment-guide-5.1-new] bakın

Bu denetim SAP uygulamanızın içinde görüntülenen tüm performans ölçümlerinin SAP için temeldeki Azure uzantısı tarafından sağlandığından emin olur.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM 'de hazırlık denetimini çalıştırma

1. Azure sanal makinesinde oturum açın (yönetici hesabı kullanarak gerekli değildir).
1. Bir komut istemi penceresi açın.
1. Komut isteminde, SAP için Azure uzantısının yükleme klasörü olarak değiştirin: C: \\ Packages \\ eklentileri \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; Version>\\ Drop

   Uzantının yolundaki *sürümü* farklılık gösterebilir. Yükleme klasöründe uzantının birden çok sürümü için klasörler görürseniz, AzureEnhancedMonitoring Windows hizmetinin yapılandırmasını denetleyin ve sonra *yürütülebilir dosyanın yolu* olarak belirtilen klasöre geçin.

   ![SAP için Azure uzantısını çalıştıran hizmetin özellikleri][deployment-guide-figure-1000]

1. Komut isteminde, hiçbir parametre olmadan **azperflib.exe** çalıştırın.

   > [!NOTE]
   > Azperflib.exe bir döngüde çalışır ve toplanan sayaçları her 60 saniyede bir güncelleştirir. Döngüyü sonlandırmak için komut Istemi penceresini kapatın.
   >
   >

SAP için Azure uzantısı yüklü değilse veya AzureEnhancedMonitoring hizmeti çalışmıyorsa, uzantı doğru şekilde yapılandırılmamıştır. Uzantının nasıl dağıtılacağı hakkında ayrıntılı bilgi için bkz. [SAP Için Azure uzantısı sorunlarını giderme][deployment-guide-5.3].

> [!NOTE]
> Azperflib.exe, kendi amaçları için kullanılamayan bir bileşendir. Bu, SAP konak aracısının VM ile ilgili Azure altyapı verilerini özel olarak sağlayan bir bileşendir.
> 

##### <a name="check-the-output-of-azperflibexe"></a>azperflib.exe çıkışını denetleyin

Azperflib.exe çıktı, SAP için doldurulan tüm Azure performans sayaçlarını gösterir. Toplanan sayaçların listesinin en altında, bir Özet ve sistem durumu göstergesi, SAP için Azure uzantısının durumunu gösterir.

![Sorun olmadığını gösteren azperflib.exe yürüterek sistem durumu denetimi çıkışı][deployment-guide-figure-1100]
<a name="figure-11"></a>

Yukarıdaki şekilde gösterilen, boş olarak bildirilen **sayaçların toplam** çıkış ve **sistem durumu** için döndürülen sonucu denetleyin.

Sonuç değerlerini aşağıdaki gibi yorumlayın:

| Sonuç değerlerini Azperflib.exe | SAP sistem durumu için Azure uzantısı |
| --- | --- |
| **API çağrıları-kullanılamıyor** | Mevcut olmayan sayaçlar, sanal makine yapılandırması için geçerli olmayabilir ya da hatalar olabilir. Bkz. **sistem durumu**. |
| **Toplam sayaç-boş** |Aşağıdaki iki Azure depolama sayacı boş olabilir: <ul><li>Depolama okuma Işlem gecikmesi sunucusu msn</li><li>Depolama okuma Işlem gecikmesi E2E msn</li></ul>Diğer tüm sayaçların değerleri olmalıdır. |
| **Sistem durumu** |Yalnızca geri dönüş durumu **Tamam** gösteriyorsa Tamam 'a tıklayın. |
| **Tanılama** |Sağlık durumu hakkında ayrıntılı bilgi. |

**Sistem** durumu değeri **Tamam** değilse, [SAP yapılandırması için Azure uzantısı sistem durumu denetimi][deployment-guide-5.2]konusundaki yönergeleri izleyin.

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Bir Linux VM 'de hazırlık denetimini çalıştırma

1. SSH kullanarak Azure sanal makinesine bağlanın.

1. SAP için Azure uzantısının çıkışını denetleyin.

   a.  `more /var/lib/AzureEnhancedMonitor/PerfCounters` komutunu çalıştırın

   **Beklenen sonuç**: performans sayaçlarının listesini döndürür. Dosya boş olmamalıdır.

   b. `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` komutunu çalıştırın

   **Beklenen sonuç**: hatanın **hiçbiri** olduğu bir satır döndürür, örneğin, **3; config; Hata;; 0; 0; hiçbiri; 0; 1456416792; TST-servercs;**

   c. `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` komutunu çalıştırın

   **Beklenen sonuç**: boş veya yok döndürüyor.

Yukarıdaki denetim başarılı olmazsa, şu ek denetimleri çalıştırın:

1. Waagent 'ın yüklü ve etkin olduğundan emin olun.

   a.  `sudo ls -al /var/lib/waagent/` komutunu çalıştırın

     **Beklenen sonuç**: waagent dizininin içeriğini listeler.

   b.  `ps -ax | grep waagent` komutunu çalıştırın

   **Beklenen sonuç**: şuna benzer bir giriş görüntüler: `python /usr/sbin/waagent -daemon`

1. SAP için Azure uzantısının yüklü olduğundan ve çalıştığından emin olun.

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` komutunu çalıştırın

   **Beklenen sonuç**: SAP dizini Için Azure uzantısının içeriğini listeler.

   b. `ps -ax | grep AzureEnhanced` komutunu çalıştırın

   **Beklenen sonuç**: şuna benzer bir giriş görüntüler: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. SAP ana bilgisayar aracısını SAP Note [1031096]' de açıklandığı gibi yükleyip çıktısını denetleyin `saposcol` .

   a.  `/usr/sap/hostctrl/exe/saposcol -d` komutunu çalıştırın

   b.  `dump ccm` komutunu çalıştırın

   c.  **Virtualization_Configuration \ gelişmiş Izleme erişim** ölçüsünün **doğru** olup olmadığını denetleyin.

Zaten yüklü bir SAP NetWeaver ABAP uygulama sunucusu varsa, işlem ST06 ' yi açın ve izlemenin etkin olup olmadığını denetleyin.

Bu denetimlerden herhangi biri başarısız olursa ve uzantının nasıl yeniden dağıtılması hakkında ayrıntılı bilgi için bkz. [SAP Için Azure uzantısı sorunlarını giderme][deployment-guide-5.3].

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>SAP için yeni Azure uzantısı için hazırlık denetimi

> [!NOTE]
> VM uzantısının iki sürümü vardır. Bu bölümde yeni VM Uzantısı ele alınmaktadır. Varsayılan VM uzantısını yüklediyseniz, lütfen [SAP Için Azure uzantısı için bölüm hazırlığı denetimine][deployment-guide-5.1]bakın.

Bu denetim SAP uygulamanızın içinde görüntülenen tüm performans ölçümlerinin SAP için temeldeki Azure uzantısı tarafından sağlandığından emin olur.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM 'de hazırlık denetimini çalıştırma

1. Azure sanal makinesinde oturum açın (yönetici hesabı kullanarak gerekli değildir).
1. Bir Web tarayıcısı açın ve şuraya gidin http://127.0.0.1:11812/azure4sap/metrics
1. Tarayıcı, sanal makinenizin izleme verilerini içeren bir XML dosyası görüntülemelidir veya indirmelidir. Böyle bir durum söz konusu değilse, SAP için Azure uzantısının yüklü olduğundan emin olun.

##### <a name="check-the-content-of-the-xml-file"></a>XML dosyasının içeriğini denetle

Adresinden erişebileceğiniz XML dosyası, http://127.0.0.1:11812/azure4sap/metrics SAP için doldurulmuş tüm Azure performans sayaçlarını içerir. Ayrıca, SAP için Azure uzantısının durumunun bir özetini ve sistem durumunu içerir.

**Sağlayıcı sistem durumu açıklama** öğesinin değerini denetleyin. Değer **Tamam** DEĞILSE, [SAP yapılandırması Için yeni Azure uzantısı için sistem durumu denetimi][deployment-guide-5.2-new]konusundaki yönergeleri izleyin.

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Bir Linux VM 'de hazırlık denetimini çalıştırma

1. SSH kullanarak Azure sanal makinesine bağlanın.

1. Aşağıdaki komutun çıkışını denetleyin

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Beklenen sonuç**: sanal makinenin, disklerinin ve ağ arabirimlerinin izleme bilgilerini IÇEREN bir XML belgesi döndürür.

Yukarıdaki denetim başarılı olmazsa, şu ek denetimleri çalıştırın:

1. Waagent 'ın yüklü ve etkin olduğundan emin olun.

   a.  `sudo ls -al /var/lib/waagent/` komutunu çalıştırın

     **Beklenen sonuç**: waagent dizininin içeriğini listeler.

   b.  `ps -ax | grep waagent` komutunu çalıştırın

   **Beklenen sonuç**: şuna benzer bir giriş görüntüler: `python /usr/sbin/waagent -daemon`

1. SAP için Azure uzantısının yüklü olduğundan ve çalıştığından emin olun.

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'` komutunu çalıştırın

   **Beklenen sonuç**: SAP dizini Için Azure uzantısının içeriğini listeler.

   b. `ps -ax | grep AzureEnhanced` komutunu çalıştırın

   **Beklenen sonuç**: şuna benzer bir giriş görüntüler: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. SAP ana bilgisayar aracısını SAP Note [1031096]' de açıklandığı gibi yükleyip çıktısını denetleyin `saposcol` .

   a.  `/usr/sap/hostctrl/exe/saposcol -d` komutunu çalıştırın

   b.  `dump ccm` komutunu çalıştırın

   c.  **Virtualization_Configuration \ gelişmiş Izleme erişim** ölçüsünün **doğru** olup olmadığını denetleyin.

Zaten yüklü bir SAP NetWeaver ABAP uygulama sunucusu varsa, işlem ST06 ' yi açın ve izlemenin etkin olup olmadığını denetleyin.

Bu denetimlerden herhangi biri başarısız olursa ve uzantının nasıl yeniden dağıtılması hakkında ayrıntılı bilgi için bkz. [SAP için yeni Azure uzantısı sorunlarını giderme][deployment-guide-5.3-new].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>SAP yapılandırması için Azure uzantısı için sistem durumu denetimi

> [!NOTE]
> VM uzantısının iki sürümü vardır. Bu bölümde varsayılan VM Uzantısı ele alınmaktadır. Yeni VM uzantısını yüklediyseniz, lütfen [SAP yapılandırması için yeni Azure uzantısı için bölüm sistem durumu denetimine][deployment-guide-5.2-new]bakın.

Altyapı verilerinden bazıları [SAP Için Azure uzantısı Için hazırlık denetimi][deployment-guide-5.1]bölümünde açıklanan test tarafından belirtilen şekilde doğru şekilde iletilemiyorsa, `Test-AzVMAEMExtension` Azure altyapısının ve SAP için Azure uzantısının doğru yapılandırılıp yapılandırılmadığını denetlemek için cmdlet 'ini çalıştırın.

1. [Azure PowerShell cmdlet 'Leri dağıtma][deployment-guide-4.1]bölümünde açıklandığı gibi Azure PowerShell cmdlet 'inin en son sürümünü yüklediğinizden emin olun.
1. Aşağıdaki PowerShell cmdlet’ini çalıştırın. Kullanılabilir ortamların bir listesi için cmdlet 'ini çalıştırın `Get-AzEnvironment` . Küresel Azure 'u kullanmak için **Azurecyüksek** ortamını seçin. Azure Çin 21Vianet için **AzureChinaCloud** öğesini seçin.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Betik, seçtiğiniz sanal makinenin yapılandırmasını sınar.

   ![SAP için Azure uzantısının başarılı testinin çıkışı][deployment-guide-figure-1300]

Her sistem durumu denetimi sonucunun **Tamam** olduğundan emin olun. Bazı denetimler **Tamam**' ı görüntülemediği takdirde, güncelleştirme CMDLET 'ini [SAP Için Azure uzantısını yapılandırma][deployment-guide-4.5]bölümünde anlatıldığı gibi çalıştırın. 15 dakika bekleyin ve SAP [yapılandırması Için Azure uzantısı IÇIN sap ve sistem durumu denetimi][deployment-guide-5.2]için [hazırlık denetimi][deployment-guide-5.1] bölümünde açıklanan denetimleri tekrarlayın. Denetimler hala bazı veya tüm sayaçlarla ilgili bir sorun gösteriyorsa, bkz. [SAP Için Azure uzantısı sorunlarını giderme][deployment-guide-5.3].

> [!Note]
> Yönetilen standart Azure disklerini kullandığınız durumlarda bazı uyarılar yaşayabilirsiniz. "Tamam" döndüren testler yerine uyarılar görüntülenecektir. Bu, normal ve bu disk türü olması durumunda tasarlanmıştır. Ayrıca bkz [. sap Için Azure uzantısı sorunlarını giderme][deployment-guide-5.3]
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>SAP yapılandırması için yeni Azure uzantısı için sistem durumu denetimi

> [!NOTE]
> VM uzantısının iki sürümü vardır. Bu bölümde yeni VM Uzantısı ele alınmaktadır. Varsayılan VM uzantısını yüklediyseniz, lütfen [SAP yapılandırması Için Azure uzantısı için bölüm sistem durumu denetimine][deployment-guide-5.2]bakın.

Altyapı verilerinden bazıları [SAP Için Azure uzantısı Için hazırlık denetimi][deployment-guide-5.1-new]bölümünde açıklanan test tarafından belirtilen şekilde doğru şekilde iletilemiyorsa, `Get-AzVMExtension` SAP için Azure uzantısı 'nın yüklü olup olmadığını denetlemek için cmdlet 'ini çalıştırın. `Test-AzVMAEMExtension`Henüz yeni uzantıyı desteklemez. Cmdlet yeni uzantıyı destekledikten sonra bu makaleyi güncelleştireceğiz.

1. [Azure PowerShell cmdlet 'Leri dağıtma][deployment-guide-4.1]bölümünde açıklandığı gibi Azure PowerShell cmdlet 'inin en son sürümünü yüklediğinizden emin olun.
1. Aşağıdaki PowerShell cmdlet’ini çalıştırın. Kullanılabilir ortamların bir listesi için cmdlet 'ini çalıştırın `Get-AzEnvironment` . Küresel Azure 'u kullanmak için **Azurecyüksek** ortamını seçin. Azure Çin 21Vianet için **AzureChinaCloud** öğesini seçin.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Cmdlet 'i seçtiğiniz sanal makinede SAP için VM uzantısının yapılandırmasını sınar.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>SAP için Azure uzantısı sorunlarını giderme

> [!NOTE]
> VM uzantısının iki sürümü vardır. Bu bölümde varsayılan VM Uzantısı ele alınmaktadır. Yeni VM uzantısını yüklediyseniz, lütfen bkz. [SAP için yeni Azure uzantısı sorun giderme][deployment-guide-5.3-new]bölümü.

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows logosu.][Logo_Windows] Azure performans sayaçları hiç görünmüyor

AzureEnhancedMonitoring Windows hizmeti, Azure 'da performans ölçümlerini toplar. Hizmet doğru şekilde yüklenmemişse veya sanal makinenizde çalışmıyorsa, hiçbir performans ölçümü toplanamaz.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP için Azure uzantısının yükleme dizini boş

###### <a name="issue"></a>Sorun

Yükleme dizini C: \\ \\ \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. AzureCATExtensionHandler \\ &lt; Sürüm>\\ Drop paketleri boş.

###### <a name="solution"></a>Çözüm

Uzantı yüklü değil. Bunun bir ara sunucu sorunu olup olmadığını (daha önce açıklandığı gibi) öğrenin. Makineyi yeniden başlatmanız veya yapılandırma betiğini yeniden çalıştırmanız gerekebilir `Set-AzVMAEMExtension` .

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>SAP için Azure uzantısı hizmeti yok

###### <a name="issue"></a>Sorun

AzureEnhancedMonitoring Windows hizmeti yok.

Azperflib.exe çıktısı bir hata oluşturur:

![azperflib.exe yürütülmesi SAP için Azure uzantısı hizmetinin çalışmadığını gösterir][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Çözüm

Hizmet yoksa, SAP için Azure uzantısı doğru şekilde yüklenmemiştir. [Azure 'DA SAP Için VM 'Lerin dağıtım senaryolarında][deployment-guide-3]dağıtım senaryolarınız için açıklanan adımları kullanarak uzantıyı yeniden dağıtın.

Uzantıyı dağıttıktan sonra, bir saatten sonra Azure performans sayaçlarının Azure sanal makinesinde sağlanmış olup olmadığını denetleyin.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>SAP için Azure uzantısı hizmeti var, ancak başlatılamıyor

###### <a name="issue"></a>Sorun

AzureEnhancedMonitoring Windows hizmeti var ve etkin, ancak başlatılamıyor. Daha fazla bilgi için uygulama olay günlüğünü denetleyin.

###### <a name="solution"></a>Çözüm

Yapılandırma yanlış. VM 'de SAP için Azure uzantısını, [SAP Için Azure uzantısını yapılandırma][deployment-guide-4.5]başlığı altında açıklandığı gibi yeniden başlatın.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Windows logosu.][Logo_Windows] Bazı Azure performans sayaçları eksik

AzureEnhancedMonitoring Windows hizmeti, Azure 'da performans ölçümlerini toplar. Hizmet, çeşitli kaynaklardan veri alır. Bazı yapılandırma verileri yerel olarak toplanır ve bazı performans ölçümleri Azure Tanılama okunurdur. Depolama sayaçları, depolama aboneliği düzeyinde günlüğe kaydetme işleminden kullanılır.

SAP Note [1999351] kullanarak sorun giderme sorunu çözmezse, `Set-AzVMAEMExtension` yapılandırma betiğini yeniden çalıştırın. Depolama analizi veya tanılama sayaçları etkinleştirildikten sonra hemen oluşturulamaabileceğinden, bir saat beklemeniz gerekebilir. Sorun devam ederse, bir Linux sanal makinesi için Windows için BC-OP-NT-AZR veya BC-OP-LNX-AZR bileşen üzerinde bir SAP müşteri destek iletisi açın.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux logosu.][Logo_Linux] Azure performans sayaçları hiç görünmüyor

Azure 'daki performans ölçümleri bir Daemon tarafından toplanır. Arka plan programı çalışmıyorsa, hiçbir performans ölçümü toplanamaz.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP için Azure uzantısının yükleme dizini boş

###### <a name="issue"></a>Sorun

Var olan \\ \\ LIB \\ WAAGENT dizininde \\ SAP için Azure uzantısı için bir alt dizin yok.

###### <a name="solution"></a>Çözüm

Uzantı yüklü değil. Bunun bir ara sunucu sorunu olup olmadığını (daha önce açıklandığı gibi) öğrenin. Makineyi yeniden başlatmanız ve/veya yapılandırma betiğini yeniden çalıştırmanız gerekebilir `Set-AzVMAEMExtension` .

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Set-AzVMAEMExtension ve Test-AzVMAEMExtension yürütmesi, Standart yönetilen disklerin desteklenmediğini belirten uyarı iletilerini gösterir

###### <a name="issue"></a>Sorun

Set-AzVMAEMExtension yürütürken veya Test-AzVMAEMExtension gibi iletiler gösterildiğinde:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Daha önce açıklandığı gibi azperfli.exe yürütülerek sağlıklı olmayan bir durumu gösteren bir sonuç elde edebilirsiniz. 

###### <a name="solution"></a>Çözüm

İletiler standart yönetilen disklerin, standart Azure depolama hesaplarının istatistiklerini denetlemek için SAP için SAP uzantısı tarafından kullanılan API 'Leri dağıtmadığından kaynaklanmıştır. Bu sorun önemli değildir. Standart Disk Depolama hesapları toplama verilerinin toplanması nedeni, sık oluşan giriş ve çıkışları azaltmaktadır. Yönetilen diskler, bir depolama hesabındaki disk sayısını sınırlayarak bu tür azaltmaya engel olur. Bu nedenle, bu veri türü önemli değildir.


#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Linux logosu.][Logo_Linux] Bazı Azure performans sayaçları eksik

Azure 'daki performans ölçümleri, çeşitli kaynaklardan veri alan bir Daemon tarafından toplanır. Bazı yapılandırma verileri yerel olarak toplanır ve bazı performans ölçümleri Azure Tanılama okunurdur. Depolama sayaçları, depolama aboneliğinizdeki günlüklerden gelir.

Bilinen sorunların tam ve güncel listesi için bkz. sap Note [1999351], SAP Için Azure Uzantısı ile ilgili ek sorun giderme bilgileri.

SAP Not [1999351] kullanarak sorun giderme sorunu çözmezse, `Set-AzVMAEMExtension` yapılandırma betiğini [SAP için Azure uzantısını yapılandırma][deployment-guide-4.5]bölümünde açıklandığı gibi yeniden çalıştırın. Depolama analizi veya tanılama sayaçları etkinleştirildikten sonra hemen oluşturulamaabileceğinden bir saat beklemeniz gerekebilir. Sorun devam ederse, bir Linux sanal makinesi için Windows için BC-OP-NT-AZR veya BC-OP-LNX-AZR bileşen üzerinde bir SAP müşteri destek iletisi açın.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>SAP için yeni Azure uzantısı sorunlarını giderme

> [!NOTE]
> VM uzantısının iki sürümü vardır. Bu bölümde yeni VM Uzantısı ele alınmaktadır. Varsayılan VM uzantısını yüklediyseniz, lütfen bkz. [SAP Için Azure uzantısı sorun giderme][deployment-guide-5.3]bölümü.

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows logosu.][Logo_Windows] Azure performans sayaçları hiç görünmüyor

AzureEnhancedMonitoring işlemi, Azure 'da performans ölçümlerini toplar. İşlem sanal makinenizde çalışmıyorsa, hiçbir performans ölçümü toplanamaz.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP için Azure uzantısının yükleme dizini boş

###### <a name="issue"></a>Sorun

Yükleme dizini C: \\ \\ \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; Sürüm> paket eklentileri boş.

###### <a name="solution"></a>Çözüm

Uzantı yüklü değil. Bunun bir ara sunucu sorunu olup olmadığını (daha önce açıklandığı gibi) öğrenin. Makineyi yeniden başlatmanız veya VM uzantısını yeniden yüklemeniz gerekebilir.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Windows logosu.][Logo_Windows] Bazı Azure performans sayaçları eksik

AzureEnhancedMonitoring Windows işlemi, Azure 'da performans ölçümlerini toplar. İşlem birkaç kaynaktan veri alır. Bazı yapılandırma verileri yerel olarak toplanır ve bazı performans ölçümleri Azure Izleyici 'den okunurdur.

SAP Not [1999351] kullanarak sorun giderme sorunu çözmezse, bir Linux sanal makinesi için WINDOWS için BC-op-NT-azr veya BC-op-LNX-azr bileşen ÜZERINDE bir SAP müşteri destek iletisi açın. Lütfen C: \\ Packages \\ eklentileri \\ Microsoft. AzureCAT. AzureEnhancedMonitoring. MonitorX64Windows \\ &lt; Sürüm>\\logapp.txt günlük dosyasını olaya ekleyin.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux logosu.][Logo_Linux] Azure performans sayaçları hiç görünmüyor

Azure 'daki performans ölçümleri bir Daemon tarafından toplanır. Arka plan programı çalışmıyorsa, hiçbir performans ölçümü toplanamaz.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>SAP için Azure uzantısının yükleme dizini boş

###### <a name="issue"></a>Sorun

Var olan \\ \\ LIB \\ WAAGENT dizininde \\ SAP için Azure uzantısı için bir alt dizin yok.

###### <a name="solution"></a>Çözüm

Uzantı yüklü değil. Bunun bir ara sunucu sorunu olup olmadığını (daha önce açıklandığı gibi) öğrenin. Makineyi yeniden başlatmanız ve/veya VM uzantısını yeniden yüklemeniz gerekebilir.

#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Linux logosu.][Logo_Linux] Bazı Azure performans sayaçları eksik

Azure 'daki performans ölçümleri, çeşitli kaynaklardan veri alan bir Daemon tarafından toplanır. Bazı yapılandırma verileri yerel olarak toplanır ve bazı performans ölçümleri Azure Izleyici 'den okunurdur.

Bilinen sorunların tam ve güncel listesi için bkz. sap Note [1999351], SAP Için Azure Uzantısı ile ilgili ek sorun giderme bilgileri.

SAP Not [1999351] kullanarak sorun giderme sorunu çözmezse, uzantıyı [SAP Için Azure uzantısını yapılandırma][deployment-guide-4.5]başlığı altında açıklandığı gibi yeniden yüklemelisiniz. Sorun devam ederse, bir Linux sanal makinesi için Windows için BC-OP-NT-AZR veya BC-OP-LNX-AZR bileşen üzerinde bir SAP müşteri destek iletisi açın. Lütfen/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux- &lt; sürüm>/logapp.txt günlük dosyasını olaya ekleyin.

## <a name="azure-extension-error-codes"></a>Azure uzantısı hata kodları

| Hata KIMLIĞI | Hata açıklaması | Çözüm |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | Uygulama yapılandırması eksik. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Uygulama yapılandırmasında dağıtım KIMLIĞI yok. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Uygulama yapılandırmasında Roleınstanceıd yok. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Uygulama yapılandırmasında Roleınstanceıd yok. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Azure yapılandırması okunamıyor. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | Uygulama yapılandırma dosyası eksik. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Uygulama yapılandırmasında VM boyutu yok. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/üzerinde | Globalmemorykara sex sayacı başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | MaxHwFrequency sayacı başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/Bu | NIC sayaçları başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Disk eşleme sayacı başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | İşlemci adı sayacı başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Disk eşleme sayacı başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | ' Disk türü ' ölçümü uzantı yapılandırma dosyasında config.xml yok. V 2.2.0.68 12/16/2015 ' de ' disk türü ' diğer sayaçlarla birlikte sunulmuştur. Uzantıyı 12/16/2015 ' dan önce dağıttıysanız, eski yapılandırma dosyasını kullanır. Azure uzantı çerçevesi, uzantıyı otomatik olarak daha yeni bir sürüme yükseltir, ancak config.xml değişmeden kalır. Yapılandırmayı güncelleştirmek için en son PowerShell kurulum betiğini indirip yürütün. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Disk önbelleğe alma yok. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Disk SLA 'Sı işleme yok. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Disk SLA ıOPS yok. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Disk eşleme sayacı başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Son donanım değiştirme sayacı başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | NIC sayaçları başarısız oldu | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/bir | VM 'nin Sysprep 'i nedeniyle Windows SID 'nizin değişmesi. | [Sysprep sonrasında yeniden Dağıt][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>Str/007 | Depolama analizinden erişim başarısız oldu. <br /><br />Yeni oluşturulan bir VM 'deki depolama Analizi verilerinin popülasyonu bir saat kadar sürebilir, hata bir süre sonra kaybolabilir. Hata hala görünüyorsa, kurulum betiğini yeniden çalıştırın. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="str_010"></a>Str/010 | Depolama Analizi sayaç yok. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="str_009"></a>Str/009 | Depolama Analizi başarısız oldu. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="wad_004"></a>WAD/004 | Hatalı WAD yapılandırması. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="wad_002"></a>WAD/002 | Beklenmeyen WAD biçimi. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="wad_001"></a>WAD/001 | WAD sayaçları bulunamadı. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/. | Eski WAD sayaçları bulundu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="wad_003"></a>WAD/003 | WAD tablosu okunamıyor. WAD tablosuna bağlantı yok. Bunun çeşitli nedenleri olabilir:<br /><br /> 1) eski yapılandırma <br />2) Azure ile ağ bağlantısı yok <br />3) WAD kurulumu ile ilgili sorunlar | [kurulum betiğini Çalıştır][deployment-guide-run-the-script]<br />[Internet bağlantısını onarın][deployment-guide-fix-internet-connection]<br />[desteğe başvurun][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Perfmon NIC ölçümleri başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Perfmon disk ölçümleri başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | Bazı tercihmon ölçümleri başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Perfmon, bir sayaç oluşturamadı. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Ölçüm sağlayıcısı yapılandırılmadı. | [desteğe başvurun][deployment-guide-contact-support] |
| <a name="str_006"></a>Str/006 | Hatalı Depolama Analizi yapılandırması. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="str_032"></a>Str/bir | Depolama Analizi ölçümleri başarısız oldu. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Ölçüm sağlayıcılarından biri başarısız oldu. | [kurulum betiğini Çalıştır][deployment-guide-run-the-script] |
| <a name="str_034"></a>Str/034 | Sağlayıcı iş parçacığı başarısız oldu. | [desteğe başvurun][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Sunulan çözümler hakkında ayrıntılı yönergeler

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Kurulum betiğini Çalıştır

Uzantıyı yeniden yüklemek için bu kılavuzdaki [SAP Için Azure uzantısını yapılandırma][deployment-guide-4.5] başlıklı bölümde yer alarak bulunan adımları izleyin. Bazı sayaçların sağlama için 30 dakikaya kadar ihtiyacı olabileceğini unutmayın.

Hatalar kaybdeğilse, [desteğe başvurun][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Desteğe başvurun

Beklenmeyen bir hata veya bilinen bir çözüm yok. C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Drop (Windows) veya/var/log/Azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) klasöründe bulunan AzureEnhancedMonitoring_service. log dosyasını toplayın ve daha fazla yardım için sap desteği ile iletişime geçin.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Sysprep sonrasında yeniden Dağıt

Genelleştirilmiş Sysprep uygulanmış bir işletim sistemi görüntüsü (SAP yazılımı içerebilen) oluşturmayı planlıyorsanız, bu görüntüde SAP için Azure uzantısı dahil değildir. Genelleştirilmiş işletim sistemi görüntüsünün yeni örneğinden dağıtıldıktan sonra SAP için Azure uzantısını yüklemelisiniz.

Ancak, genelleştirilmiş ve Sysprep uygulanmış işletim sistemi görüntünüz zaten SAP için Azure uzantısını içeriyorsa, yeni dağıtılan sanal makine örneğinde, uzantıyı yeniden yapılandırmak için aşağıdaki geçici çözümü uygulayabilirsiniz:

* Yeni dağıtılan sanal makine örneğinde aşağıdaki klasörlerin içeriğini silin:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \Runtimesettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler \\ \<version\> \status

* Uzantıyı yeniden yüklemek için bu kılavuzdaki [SAP Için Azure uzantısını yapılandırma][deployment-guide-4.5] başlıklı bölümde yer alarak bulunan adımları izleyin.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Internet bağlantısını onarın

SAP için Azure uzantısını çalıştıran Microsoft Azure sanal makinenin Internet erişimi olması gerekir. Bu Azure VM, bir Azure sanal ağının veya şirket içi etki alanının parçasıysa ilgili proxy ayarlarının ayarlandığından emin olun. Bu ayarlar ayrıca, LocalSystem hesabının Internet 'e erişmesi için de geçerli olmalıdır. Bu kılavuzdaki [Ara sunucuyu yapılandırma][deployment-guide-configure-proxy] bölümünü izleyin.

Ayrıca, Azure VM 'niz için bir statik IP adresi ayarlamanız gerekirse, Azure VM 'nin içinde el ile ayarlamayın, ancak [Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md), [Azure CLI](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) [Azure Portal](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)kullanarak ayarlayın. Statik IP, Azure DHCP hizmeti aracılığıyla dağıtılır.

Azure VM içindeki bir statik IP adresinin el ile ayarlanması desteklenmez ve SAP için Azure uzantısıyla ilgili sorunlara neden olabilirler.
