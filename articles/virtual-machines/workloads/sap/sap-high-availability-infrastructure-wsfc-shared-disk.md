---
title: SAP Ass/SCS için bir Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını hazırlama | Microsoft Docs
description: SAP Ass/SCS örneği için bir Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını nasıl hazırlayacağınızı öğrenin.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63a2a3a68adaa2e389cc2af173c8f75a18fbc36d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078702"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>SAP Ass/SCS için bir Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını hazırlama

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP çoklu SID yüksek kullanılabilirlik yapılandırması)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Bu makalede, bir SAP Ass örneği Kümelemeye yönelik bir seçenek olarak *Küme Paylaşılan diski* kullanarak bir Windows Yük devretme kümesine yüksek KULLANıLABILIRLIĞE sahip SAP sistemi yükleme ve yapılandırma için Azure altyapısını hazırlamak üzere gerçekleştirmeniz gereken adımlar açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Yüklemeye başlamadan önce şu makaleyi gözden geçirin:

* [Mimari Kılavuzu: Küme Paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Altyapıyı mimari Şablon 1 için hazırlama
SAP için Azure Resource Manager şablonları, gerekli kaynakların dağıtımını basitleştirmeye yardımcı olur.

Azure Resource Manager içindeki üç katmanlı şablonlar da yüksek kullanılabilirlik senaryolarını destekler. Örneğin, mimari Şablon 1 ' in iki kümesi vardır. Her küme SAP ASCS/SCS ve DBMS için SAP tek hata noktasıdır.

Burada, bu makalede anladığımız örnek senaryo için Azure Resource Manager şablonları edinebilirsiniz:

* [Azure Market görüntüsü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure yönetilen diskleri kullanarak Azure Marketi görüntüsü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Özel görüntü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Yönetilen diskleri kullanarak özel görüntü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Altyapıyı mimari Şablon 1 için hazırlamak için:

- Azure portal, **Parametreler** bölmesindeki **Systemavailability** kutusunda **ha**' yi seçin.

  ![Şekil 1: SAP yüksek kullanılabilirlik Azure Resource Manager parametrelerini ayarlama][sap-ha-guide-figure-3000]

_**Şekil 1:** SAP yüksek kullanılabilirlik Azure Resource Manager parametrelerini ayarlama_


  Şablonlar şunları oluşturur:

  * **Sanal makineler**:
    * SAP uygulama sunucusu sanal makineleri: \<Sapsystemsıd\>-dı-\<numarası\>
    * YOKS/SCS kümesi sanal makineleri: \<Sapsystemsıd\>-ascs-\<sayı\>
    * DBMS kümesi: \<Sapsystemsid\>-DB-\<sayı\>

  * **Tüm sanal makineler için, ILIŞKILI IP adresleriyle ağ kartları**:
    * \<Sapsystemsıd\>-NIC-dı-\<sayı\>
    * \<Sapsystemsıd\>-NIC-ascs-\<sayı\>
    * \<Sapsystemsıd\>-NIC-DB-\<numarası\>

  * **Azure depolama hesapları (yalnızca yönetilmeyen diskler)** :

  * **Kullanılabilirlik grupları** :
    * SAP uygulama sunucusu sanal makineleri: \<Sapsystemsid\>-avset-dı
    * SAP ASCS/SCS kümesi sanal makineleri: \<Sapsystemsid\>-avset-ascs
    * DBMS kümesi sanal makineleri: \<Sapsystemsid\>-avset-DB

  * **Azure iç yük dengeleyici**:
    * Yoks/SCS örneği ve IP adresi \<sapsystemsıd\>-lb-ascs için tüm bağlantı noktalarıyla
    * SQL Server dbms ve IP adresi \<sapsystemsıd\>-lb-DB olan tüm bağlantı noktalarıyla

  * **Ağ güvenlik grubu**: \<Sapsystemsid\>-NSG-ascs-0  
    * \<Sapsystemsıd\>-ascs-0 sanal makinesine yönelik bir açık dış Uzak Masaüstü Protokolü (RDP) bağlantı noktası ile

> [!NOTE]
> Ağ kartlarının ve Azure iç yük dengeleyicileri 'nin tüm IP adresleri varsayılan olarak dinamiktir. Onları statik IP adresleriyle değiştirin. Bunun nasıl yapılacağını makalenin ilerleyen bölümlerinde anlatmaktadır.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Üretimde kullanmak üzere kurumsal ağ bağlantısı (şirketler arası) ile sanal makineler dağıtma
Üretim SAP sistemleri için Azure VPN Gateway veya Azure ExpressRoute kullanarak Azure sanal makinelerini [Kurumsal ağ bağlantısı (Şirket içi)][planning-guide-2.2] ile dağıtın.

> [!NOTE]
> Azure sanal ağ örneğinizi kullanabilirsiniz. Sanal ağ ve alt ağ zaten oluşturulup hazırlandı.
>
>

1. Azure portal, **Parametreler** bölmesindeki **Neworexistingsubnet** kutusunda **var**' ı seçin.
2. **SubnetID** kutusunda, Azure sanal makinelerinizi dağıtmayı planladığınız, hazırlanmış Azure ağ alt ağınızın kimliğinin tam dizesini ekleyin.
3. Tüm Azure ağ alt ağlarının listesini almak için şu PowerShell komutunu çalıştırın:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   **Kimlik** alanı, alt ağ kimliği için değeri gösterir.
4. Tüm alt ağ KIMLIĞI değerlerinin listesini almak için şu PowerShell komutunu çalıştırın:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Alt ağ KIMLIĞI şöyle görünür:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Test ve tanıtım için yalnızca bulutta SAP örnekleri dağıtın
Yüksek kullanılabilirlik SAP sisteminizi yalnızca bulut dağıtım modelinde dağıtabilirsiniz. Bu tür bir dağıtım öncelikle tanıtım ve test kullanım durumları için yararlıdır. Üretim kullanım örnekleri için uygun değildir.

- Azure portal, **Parametreler** bölmesindeki **Neworexistingsubnet** kutusunda **Yeni**' yi seçin. **SubnetID** alanını boş bırakın.

  SAP Azure Resource Manager şablonu, Azure sanal ağını ve alt ağını otomatik olarak oluşturur.

> [!NOTE]
> Aynı Azure sanal ağ örneğinde Active Directory ve DNS hizmeti için en az bir ayrılmış sanal makine dağıtmanız gerekir. Şablon bu sanal makineleri oluşturmaz.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Altyapıyı mimari Şablon 2 için hazırlama

SAP mimari şablonu 2 için gerekli altyapı kaynaklarının dağıtımını basitleştirmeye yardımcı olmak üzere SAP için bu Azure Resource Manager şablonunu kullanabilirsiniz.

Burada, bu dağıtım senaryosu için Azure Resource Manager şablonları edinebilirsiniz:

* [Azure Market görüntüsü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Yönetilen diskleri kullanarak Azure Marketi görüntüsü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Özel görüntü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Yönetilen diskleri kullanarak özel görüntü](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Altyapıyı mimari Şablon 3 için hazırlama

Altyapıyı hazırlayabilir ve çoklu SID için SAP 'yi yapılandırabilirsiniz. Örneğin, *var olan* bir küme yapılandırmasına ek BIR SAP ascs/SCS örneği ekleyebilirsiniz. Daha fazla bilgi için, [Azure Resource Manager ' de BIR SAP çoklu SID yapılandırması oluşturmak üzere var olan bir küme yapılandırması için ek SAP ASCS/SCS örneği yapılandırma][sap-ha-multi-sid-guide]konusuna bakın.

Yeni bir çoklu SID kümesi oluşturmak istiyorsanız [GitHub 'da çoklu SID hızlı başlangıç şablonlarını](https://github.com/Azure/azure-quickstart-templates)kullanabilirsiniz.

Yeni bir çoklu SID kümesi oluşturmak için aşağıdaki üç şablonu dağıtmanız gerekir:

* [YOKS/SCS şablonu](#ASCS-SCS-template)
* [Veritabanı şablonu](#database-template)
* [Uygulama sunucuları şablonu](#application-servers-template)

Aşağıdaki bölümlerde, şablonlarda sağlamanız gereken şablonlar ve parametreler hakkında daha fazla ayrıntı vardır.

### <a name="ASCS-SCS-template"></a>YOKS/SCS şablonu

YOKS/SCS şablonu, birden fazla ASCS/SCS örneğini barındıran bir Windows Server yük devretme kümesi oluşturmak için kullanabileceğiniz iki sanal makine dağıtır.

Ascs/SCS çok düzeyli şablonunu ayarlamak için, yönetilen diskleri kullanarak ascs [/SCS çok düzeyli şablonda][sap-templates-3-tier-multisid-xscs-marketplace-image] veya [ascs/SCS çoklu SID][sap-templates-3-tier-multisid-xscs-marketplace-image-md]şablonunda aşağıdaki parametrelerin değerlerini girin:

- **Kaynak ön eki**:  Dağıtım sırasında oluşturulan tüm kaynakların ön eki için kullanılan kaynak önekini ayarlayın. Kaynaklar yalnızca bir SAP sistemine ait olmadığından, kaynağın ön eki bir SAP sisteminin SID 'SI değildir.  Ön ek üç ve altı karakter arasında olmalıdır.
- **Yığın türü**: SAP sisteminin yığın türünü seçin. Yığın türüne bağlı olarak, Azure Load Balancer SAP sistemine göre yalnızca bir (ABAP veya Java) veya iki (ABAP + Java) özel IP adresi vardır.
- **Işletim sistemi türü**: Sanal makinelerin işletim sistemini seçin.
- **SAP sistem sayısı**: Bu kümeye yüklemek istediğiniz SAP sistemlerinin sayısını seçin.
- **Sistem kullanılabilirliği**: **Ha**'yi seçin.
- **Yönetici Kullanıcı adı ve yönetici parolası**: Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
- **Yeni veya mevcut alt ağ**: Yeni bir sanal ağ ve alt ağ oluşturulup oluşturulmayacağını veya var olan bir alt ağı kullanacağınızı belirleyin. Şirket içi ağınıza bağlı bir sanal ağınız zaten varsa, **mevcut**' ı seçin.
- **Alt ağ kimliği**: VM 'yi tanımlanmış VM 'ye atanmış bir alt ağa sahip olduğunuz mevcut bir VNet 'e dağıtmak istiyorsanız, söz konusu alt ağın KIMLIĞINI adlandırın. KIMLIK genellikle şöyle görünür:

  /Subscriptions/\<abonelik kimliği\>/ResourceGroups/\<kaynak grubu adı\>/Providers/Microsoft.Network/virtualNetworks/\<sanal ağ adı\>/Subnets/ \<alt ağ adı\>

Şablon, birden çok SAP sistemini destekleyen bir Azure Load Balancer örneğini dağıtır:

- ASCS örnekleri, 00, 10, 20 örnek numarası için yapılandırılır...
- SCS örnekleri 01, 11, 21 örnek numarası için yapılandırılır...
- ASCS sıraya alma çoğaltma sunucusu (ERS) (yalnızca Linux) örnekleri, 02, 12, 22 örnek numarası için yapılandırılır...
- SCS (yalnızca Linux) örnekleri, 03, 13, 23 örnek numarası için yapılandırılır...

Yük dengeleyici 1 VIP (Linux için 2), ASCS/SCS için 1x VIP ve ERS için 1x VIP (yalnızca Linux) içerir.

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP ASCS/SCS bağlantı noktaları
Aşağıdaki liste, tüm yük dengeleme kurallarını içerir (burada x, SAP sisteminin sayısıdır, örneğin, 1, 2, 3...):
- Her SAP sistemi için Windows 'a özel bağlantı noktaları: 445, 5985
- YOKS bağlantı noktaları (örnek numarası x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS bağlantı noktaları (örnek numarası x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Linux 'ta yoks bağlantı noktaları (örnek numarası x2): 33x2, 5x213, 5x214, 5x216
- Linux 'ta SCS 'ler bağlantı noktaları (örnek numarası x3): 33x3, 5x313, 5x314, 5x316

Yük dengeleyici, aşağıdaki araştırma bağlantı noktalarını kullanacak şekilde yapılandırılır (burada x, SAP sisteminin numarasıdır, örneğin, 1, 2, 3...):
- YOKS/SCS iç yük dengeleyici araştırma bağlantı noktası: 620x0
- ERS iç yük dengeleyici yoklama bağlantı noktası (yalnızca Linux): 621x2

### <a name="database-template"></a>Veritabanı şablonu

Veritabanı şablonu, bir SAP sistemine yönelik ilişkisel veritabanı yönetim sistemini (RDBMS) yüklemek için kullanabileceğiniz bir veya iki sanal makine dağıtır. Örneğin, beş SAP sistemi için bir ASCS/SCS şablonu dağıtırsanız, bu şablonu beş kez dağıtmanız gerekir.

Veritabanı çoklu SID şablonunu ayarlamak için, yönetilen diskleri kullanarak veritabanı çok düzeyli [şablon][sap-templates-3-tier-multisid-db-marketplace-image] veya [veritabanı çoklu SID şablonunda][sap-templates-3-tier-multisid-db-marketplace-image-md], aşağıdaki parametrelerin değerlerini girin:

- **SAP sistem kimliği**: Yüklemek istediğiniz SAP sisteminin SAP sistem KIMLIĞINI girin. KIMLIK, dağıtılan kaynakların ön eki olarak kullanılır.
- **Işletim sistemi türü**: Sanal makinelerin işletim sistemini seçin.
- **DbType**: Kümeye yüklemek istediğiniz veritabanının türünü seçin. Microsoft SQL Server yüklemek istiyorsanız **SQL** ' i seçin. SAP HANA sanal makinelere yüklemeyi planlıyorsanız **Hana** ' yı seçin. Doğru işletim sistemi türünü seçtiğinizden emin olun. SQL için **Windows** ' u SEÇIN ve Hana Için bir Linux dağıtımı seçin. Sanal makinelere bağlı Azure Load Balancer seçili veritabanı türünü destekleyecek şekilde yapılandırılmıştır:
  * **SQL**: Yük dengeleyici Yük Dengeleme bağlantı noktası 1433. SQL Server AlwaysOn kurulumu için bu bağlantı noktasını kullandığınızdan emin olun.
  * **HANA**: Yük dengeleyici Yük Dengeleme 35015 ve 35017. **50**örnek numarasıyla SAP HANA yüklediğinizden emin olun.
  Yük dengeleyici, 62550 araştırma bağlantı noktasını kullanır.
- **SAP sistem boyutu**: Yeni sistemin sağladığı SAPS sayısını ayarlayın. Sistemin kaç tane için gerekli olduğundan emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
- **Sistem kullanılabilirliği**: **Ha**'yi seçin.
- **Yönetici Kullanıcı adı ve yönetici parolası**: Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
- **Alt ağ kimliği**: ASCS/SCS şablonunun dağıtımı sırasında kullandığınız alt ağın KIMLIĞINI veya Ass/SCS şablon dağıtımının bir parçası olarak oluşturulan alt ağın KIMLIĞINI girin.

### <a name="application-servers-template"></a>Uygulama sunucuları şablonu

Uygulama sunucuları şablonu, bir SAP sistemi için SAP uygulama sunucusu örnekleri olarak kullanılabilecek iki veya daha fazla sanal makine dağıtır. Örneğin, beş SAP sistemi için bir ASCS/SCS şablonu dağıtırsanız, bu şablonu beş kez dağıtmanız gerekir.

Uygulama sunucuları çoklu SID şablonu ayarlamak için, [uygulama sunucuları çok][sap-templates-3-tier-multisid-apps-marketplace-image] düzeyli şablon veya [uygulama sunucuları çok düzeyli şablon ' u yönetilen diskleri kullanarak][sap-templates-3-tier-multisid-apps-marketplace-image-md]ayarlamak için aşağıdaki parametrelerin değerlerini girin:

  -  **SAP sistem kimliği**: Yüklemek istediğiniz SAP sisteminin SAP sistem KIMLIĞINI girin. KIMLIK, dağıtılan kaynakların ön eki olarak kullanılır.
  -  **Işletim sistemi türü**: Sanal makinelerin işletim sistemini seçin.
  -  **SAP sistem boyutu**: Yeni sistemin sağladığı SAPS sayısı. Sistemin kaç tane için gerekli olduğundan emin değilseniz, SAP Technology Iş ortağınızdan veya sistem tümleştirmenize sorun.
  -  **Sistem kullanılabilirliği**: **Ha**'yi seçin.
  -  **Yönetici Kullanıcı adı ve yönetici parolası**: Makinede oturum açmak için kullanılabilecek yeni bir kullanıcı oluşturun.
  -  **Alt ağ kimliği**: ASCS/SCS şablonunun dağıtımı sırasında kullandığınız alt ağın KIMLIĞINI veya Ass/SCS şablon dağıtımının bir parçası olarak oluşturulan alt ağın KIMLIĞINI girin.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure sanal ağı
Örneğimizde, Azure sanal ağ örneğinin adres alanı 10.0.0.0/16 ' dır. Adres aralığı 10.0.0.0/24 olan alt ağ adlı bir alt ağ vardır. Tüm sanal makineler ve iç yük dengeleyiciler bu sanal ağda dağıtılır.

> [!IMPORTANT]
> Konuk işletim sistemi içindeki ağ ayarlarında herhangi bir değişiklik yapmayın. Buna IP adresleri, DNS sunucuları ve alt ağ dahildir. Tüm ağ ayarlarınızı Azure 'da yapılandırın. Dinamik ana bilgisayar Yapılandırma Protokolü (DHCP) hizmeti, ayarlarınızı yayar.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS IP adresleri

Gerekli DNS IP adreslerini ayarlamak için aşağıdaki adımları izleyin:

1. Azure portal, **DNS sunucuları** bölmesinde, sanal ağ **DNS SUNUCULARıNıZ** seçeneğinin **özel DNS**olarak ayarlandığından emin olun.
2. Sahip olduğunuz ağ türüne göre ayarlarınızı seçin. Daha fazla bilgi için aşağıdaki kaynaklara bakın:
   * [Şirket ağı bağlantısı (Şirket içi)][planning-guide-2.2]: Şirket içi DNS sunucularının IP adreslerini ekleyin.  
   Şirket içi DNS sunucularını Azure 'da çalışan sanal makinelere genişletebilirsiniz. Bu senaryoda, DNS hizmetini çalıştırdığınız Azure sanal makinelerinin IP adreslerini ekleyebilirsiniz.
   * Azure 'da yalıtılmış VM dağıtımları için: Aynı sanal ağ örneğine bir DNS sunucusu olarak hizmet veren ek bir sanal makine dağıtın. DNS hizmetini çalıştırmak için ayarladığınız Azure sanal makinelerinin IP adreslerini ekleyin.

   ![Şekil 2: Azure sanal ağı için DNS sunucularını yapılandırma][sap-ha-guide-figure-3001]

   _**Şekil 2:** Azure sanal ağı için DNS sunucularını yapılandırma_

   > [!NOTE]
   > DNS sunucularının IP adreslerini değiştirirseniz, değişikliği uygulamak ve yeni DNS sunucularını yaymak için Azure sanal makinelerini yeniden başlatmanız gerekir.
   >
   >

Örneğimizde, DNS hizmeti bu Windows sanal makinelerine yüklenir ve yapılandırılır:

| Sanal makine rolü | Sanal makine konak adı | Ağ kartı adı | Statik IP adresi |
| --- | --- | --- | --- |
| İlk DNS sunucusu |domcontenr-0 |PR1-NIC-domcontenr-0 |10.0.0.10 |
| İkinci DNS sunucusu |domcontenr-1 |PR1-NIC-domcontenr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>SAP ASCS/SCS kümelenmiş örneği ve DBMS kümelenmiş örneği için ana bilgisayar adları ve statik IP adresleri

Şirket içi dağıtım için, bu ayrılmış ana bilgisayar adlarına ve IP adreslerine ihtiyacınız vardır:

| Sanal konak adı rolü | Sanal konak adı | Sanal statik IP adresi |
| --- | --- | --- |
| SAP ASCS/SCS ilk küme sanal ana bilgisayar adı (küme yönetimi için) |PR1-ascs-Vir |10.0.0.42 |
| SAP ASCS/SCS örneği sanal ana bilgisayar adı |PR1-ascs-SAP |10.0.0.43 |
| SAP DBMS ikinci küme sanal ana bilgisayar adı (küme yönetimi) |pr1-dbms-vir |10.0.0.32 |

Kümeyi oluşturduğunuzda, PR1-ascs-Vir ve PR1-DBMS-Vir sanal ana bilgisayar adlarını ve kümenin kendisini yöneten ilişkili IP adreslerini oluşturun. Bunun nasıl yapılacağı hakkında bilgi için bkz. küme [düğümlerini bir küme yapılandırmasında toplama][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Diğer iki sanal ana bilgisayar adını, PR1-ascs-sap ve PR1-DBMS-sap ve DNS sunucusunda ilişkili IP adreslerini el ile oluşturabilirsiniz. Kümelenmiş SAP ASCS/SCS örneği ve kümelenmiş DBMS örneği bu kaynakları kullanır. Bunun nasıl yapılacağı hakkında bilgi için bkz. [KÜMELENMIŞ SAP yoks/SCS örneği için sanal konak adı oluşturma][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>SAP sanal makineleri için statik IP adresleri ayarlama
Kümenizde kullanmak üzere sanal makineleri dağıttıktan sonra, tüm sanal makineler için statik IP adresleri ayarlamanız gerekir. Bunu, Konuk işletim sisteminde değil, Azure sanal ağ yapılandırmasında yapın.

1. Azure Portal, **kaynak grubu** > **ağ kartı** > **ayarları** > **IP adresi**' ni seçin.
2. **IP adresleri** bölmesindeki **atama**altında **statik**' ı seçin. **IP adresi** kutusuna, kullanmak istediğiniz IP adresini girin.

   > [!NOTE]
   > Ağ kartının IP adresini değiştirirseniz, değişikliği uygulamak için Azure sanal makinelerini yeniden başlatmanız gerekir.  
   >
   >

   ![Şekil 3: Her bir sanal makinenin ağ kartı için statik IP adresleri ayarla][sap-ha-guide-figure-3002]

   _**Şekil 3:** Her bir sanal makinenin ağ kartı için statik IP adresleri ayarla_

   Active Directory veya DNS hizmetiniz için kullanmak istediğiniz sanal makineler de dahil olmak üzere tüm ağ arabirimleri için bu adımı tekrarlayın.

Örneğimizde şu sanal makineler ve statik IP adresleri vardır:

| Sanal makine rolü | Sanal makine konak adı | Ağ kartı adı | Statik IP adresi |
| --- | --- | --- | --- |
| İlk SAP uygulama sunucusu örneği |PR1-dı-0 |PR1-NIC-dı-0 |10.0.0.50 |
| İkinci SAP uygulama sunucusu örneği |PR1-dı-1 |PR1-NIC-dı-1 |10.0.0.51 |
| ... |... |... |... |
| Son SAP uygulama sunucusu örneği |PR1-dı-5 |pr1-nic-di-5 |10.0.0.55 |
| ASCS/SCS örneği için ilk küme düğümü |PR1-ascs-0 |PR1-NIC-ascs-0 |10.0.0.40 |
| ASCS/SCS örneği için ikinci küme düğümü |PR1-ascs-1 |PR1-NIC-ascs-1 |10.0.0.41 |
| DBMS örneği için ilk küme düğümü |PR1-DB-0 |PR1-NIC-DB-0 |10.0.0.30 |
| DBMS örneği için ikinci küme düğümü |PR1-DB-1 |PR1-NIC-DB-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Azure iç yük dengeleyici için statik IP adresi ayarlama

SAP Azure Resource Manager şablonu, SAP ASCS/SCS örneği kümesi ve DBMS kümesi için kullanılan bir Azure iç yük dengeleyici oluşturur.

> [!IMPORTANT]
> SAP ASCS/SCS 'nin sanal ana bilgisayar adının IP adresi, SAP ASCS/SCS iç yük dengeleyicinin IP adresiyle aynıdır: PR1-lb-ascs.
> DBMS 'nin sanal adının IP adresi, DBMS iç yük dengeleyicinin IP adresiyle aynıdır: PR1-lb-dbms.
>
>

Azure iç yük dengeleyici için bir statik IP adresi ayarlamak için:

1. İlk dağıtım, iç yük dengeleyici IP adresini **dinamik**olarak ayarlar. Azure portal, **IP adresleri** bölmesindeki **atama**altında **statik**' ı seçin.
2. **PR1-lb-ascs** iç yük dengeleyicinin IP adresini SAP ascs/SCS örneğinin sanal ana BILGISAYAR adının IP adresine ayarlayın.
3. İç yük dengeleyici **PR1-lb-DBMS** IP ADRESINI, DBMS örneğinin sanal ana BILGISAYAR adının IP adresine ayarlayın.

   ![Şekil 4: SAP ASCS/SCS örneği için iç yük dengeleyici için statik IP adresleri ayarlama][sap-ha-guide-figure-3003]

   _**Şekil 4:** SAP ASCS/SCS örneği için iç yük dengeleyici için statik IP adresleri ayarlama_

Örneğimizde, şu statik IP adreslerine sahip iki Azure iç yük dengeliyoruz:

| Azure iç yük dengeleyici rolü | Azure iç yük dengeleyici adı | Statik IP adresi |
| --- | --- | --- |
| SAP ASCS/SCS örneği iç yük dengeleyici |PR1-lb-yoks |10.0.0.43 |
| SAP DBMS iç yük dengeleyici |PR1-lb-DBMS |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları

SAP Azure Resource Manager şablonu, ihtiyacınız olan bağlantı noktalarını oluşturur:
* Varsayılan örnek numarası 00 olan bir ABAP Ass örneği
* Varsayılan örnek numarası 01 olan bir Java SCS örneği

SAP ASCS/SCS örneğinizi yüklerken, ABAP ASCS örneğiniz için 00 varsayılan örnek numarasını ve Java SCS örneğiniz için varsayılan örnek numarasını (01) kullanmanız gerekir.

Ardından, SAP NetWeaver bağlantı noktaları için gerekli iç Yük Dengeleme uç noktalarını oluşturun.

Gerekli iç Yük Dengeleme uç noktalarını oluşturmak için, SAP NetWeaver ABAP ASCS bağlantı noktalarında bu yük dengeleme uç noktalarını oluşturun:

| Hizmet/Yük Dengeleme kuralı adı | Varsayılan bağlantı noktası numaraları | Somut bağlantı noktaları (örnek numarası 00 olan ASCS örneği) (10 ile) |
| --- | --- | --- |
| Sıraya alma sunucusu/ *lbrule3200* |32\<ınstancenumarası\> |3200 |
| ABAP Message sunucusu/ *lbrule3600* |36\<ınstancenumarası\> |3600 |
| İç ABAP iletisi/ *lbrule3900* |39\<ınstancenumarası\> |3900 |
| İleti sunucusu HTTP/ *Lbrule8100* |81\<ınstancenumarası\> |8100 |
| SAP başlatma hizmeti yoks HTTP/ *Lbrule50013* |5\<ınstancenumarası\>13 |50013 |
| SAP başlangıç hizmeti yoks HTTPS/ *Lbrule50014* |5\<ınstancenumarası\>14 |50014 |
| Sıraya alma çoğaltması/ *Lbrule50016* |5\<ınstancenumarası\>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5\<ınstancenumarası\>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5\<ınstancenumarası\>14 |51014 |
| Windows Uzaktan Yönetimi (WinRM) *Lbrule5985* | |5985 |
| Dosya paylaşma *Lbrule445* | |445 |

**Tablo 1:** SAP NetWeaver ABAP Ass örneklerinin bağlantı noktası numaraları

Daha sonra, SAP NetWeaver Java SCS bağlantı noktaları için bu yük dengeleme uç noktalarını oluşturun:

| Hizmet/Yük Dengeleme kuralı adı | Varsayılan bağlantı noktası numaraları | Somut bağlantı noktaları (örneğin, örnek numarası 01 olan SCS örneği) (11 ile ÇÖZÜMLEYICILER) |
| --- | --- | --- |
| Sıraya alma sunucusu/ *lbrule3201* |32\<ınstancenumarası\> |3201 |
| Ağ Geçidi sunucusu/ *lbrule3301* |33\<ınstancenumarası\> |3301 |
| Java ileti sunucusu/ *lbrule3900* |39\<ınstancenumarası\> |3901 |
| İleti sunucusu HTTP/ *Lbrule8101* |81\<ınstancenumarası\> |8101 |
| SAP başlangıç hizmeti SCS HTTP/ *Lbrule50113* |5\<ınstancenumarası\>13 |50113 |
| SAP başlangıç hizmeti SCS HTTPS/ *Lbrule50114* |5\<ınstancenumarası\>14 |50114 |
| Sıraya alma çoğaltması/ *Lbrule50116* |5\<ınstancenumarası\>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5\<ınstancenumarası\>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5\<ınstancenumarası\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Dosya paylaşma *Lbrule445* | |445 |

**Tablo 2:** SAP NetWeaver Java SCS örneklerinin bağlantı noktası numaraları

![Şekil 5: Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları][sap-ha-guide-figure-3004]

_**Şekil 5:** Azure iç yük dengeleyici için varsayılan yoks/SCS Yük Dengeleme kuralları_

Yük dengeleyici PR1-lb-DBMS IP adresini, DBMS örneğinin sanal ana bilgisayar adının IP adresine ayarlayın.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Azure iç yük dengeleyici için ASCS/SCS varsayılan Yük Dengeleme kurallarını değiştirme

SAP ASCS veya SCS örnekleri için farklı numaralar kullanmak istiyorsanız, bağlantı noktalarının adlarını ve değerlerini varsayılan değerlerle değiştirmeniz gerekir.

1. Azure Portal  **\<SID\>-lb-ascs yük dengeleyici** > **Yük Dengeleme kuralları**' nı seçin.
2. SAP ASCS veya SCS örneğine ait olan tüm yük dengeleme kuralları için şu değerleri değiştirin:

   * Name
   * Port
   * Arka uç bağlantı noktası

   Örneğin, varsayılan ASCS örnek numarasını 00 ' dan 31 ' e değiştirmek istiyorsanız, Tablo 1 ' de listelenen tüm bağlantı noktaları için değişiklikleri yapmanız gerekir.

   Bağlantı noktası *lbrule3200*için bir güncelleştirme örneği aşağıda verilmiştir.

   ![Şekil 6: Azure iç yük dengeleyici için ASCS/SCS varsayılan Yük Dengeleme kurallarını değiştirme][sap-ha-guide-figure-3005]

   _**Şekil 6:** Azure iç yük dengeleyici için ASCS/SCS varsayılan Yük Dengeleme kurallarını değiştirme_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Windows sanal makinelerini etki alanına ekleme

Sanal makinelere statik bir IP adresi atadıktan sonra, sanal makineleri etki alanına ekleyin.

![Şekil 7: Bir etki alanına sanal makine ekleme][sap-ha-guide-figure-3006]

_**Şekil 7:** Bir etki alanına sanal makine ekleme_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri ekleme

Azure Load Balancer, bağlantılar, belirlenen süre boyunca boşta kaldığında bağlantıları kapatan iç yük dengeleyiciye sahiptir (boşta zaman aşımı). İletişim örneklerinde SAP iş işlemleri, ilk sıraya alma/sıradan çıkarma isteğinin gönderilmesi gerektiği anda SAP sıraya alma işlemine bağlantılar açar. Bu bağlantılar genellikle iş süreci veya sıraya alma işlemi yeniden başlatılana kadar kurulmaya devam eder. Ancak, bağlantı ayarlanan süre boyunca boşta kalırsa, Azure iç yük dengeleyici bağlantıları kapatır. Bu bir sorun değildir çünkü SAP iş işlemi, artık mevcut değilse sıraya alma işlemiyle bağlantıyı yeniden oluşturur. Bu etkinlikler, SAP işlemlerinin geliştirici izlemelerinde belgelenmiştir, ancak bu izlemelerde büyük miktarda ekstra içerik oluşturur. TCP/IP `KeepAliveTime` ve `KeepAliveInterval` her iki küme düğümünde de değişiklik yapmak iyi bir fikirdir. Bu değişiklikleri, makalenin ilerleyen kısımlarında açıklanan SAP profili parametreleriyle TCP/IP parametrelerinde birleştirin.

Yalnızca SAP ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri eklemek için, bu Windows kayıt defteri girdilerini SAP Ass/SCS için her iki Windows küme düğümüne de ekleyin:

| `Path` | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Değişken adı |`KeepAliveTime` |
| Değişken türü |REG_DWORD (ondalık) |
| Value |120000 |
| Belge bağlantısı |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tablo 3:** İlk TCP/IP parametresini değiştirme

Ardından, SAP Ass/SCS için Windows küme düğümlerine bu Windows kayıt defteri girişini ekleyin:

| `Path` | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Değişken adı |`KeepAliveInterval` |
| Değişken türü |REG_DWORD (ondalık) |
| Value |120000 |
| Belge bağlantısı |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tablo 4:** İkinci TCP/IP parametresini değiştirme

Değişiklikleri uygulamak için her iki küme düğümünü yeniden başlatın.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>SAP ASCS/SCS örneği için Windows Server yük devretme kümesi ayarlama

SAP yoks/SCS örneği için Windows Server yük devretme kümesi ayarlama şu görevleri içerir:

- Küme düğümlerini bir küme yapılandırmasında toplayın.
- Bir küme dosyası paylaşma tanığı yapılandırın.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Küme düğümlerini bir küme yapılandırmasında toplayın

1. Rol ve Özellik Ekleme Sihirbazı 'nda, her iki küme düğümüne yük devretme kümelemesi ekleyin.
2. Yük Devretme Kümesi Yöneticisi kullanarak yük devretme kümesini ayarlayın. Yük Devretme Kümesi Yöneticisi, **küme oluştur**' u seçin ve ardından yalnızca ilk kümenin adını ekleyin (düğüm A). İkinci düğümü henüz eklemeyin; ikinci düğümü sonraki bir adımda eklersiniz.

   ![Şekil 8: İlk küme düğümünün sunucusunu veya sanal makine adını ekleyin][sap-ha-guide-figure-3007]

   _**Şekil 8:** İlk küme düğümünün sunucusunu veya sanal makine adını ekleyin_

3. Kümenin ağ adını (sanal ana bilgisayar adı) girin.

   ![Şekil 9: Küme adını girin][sap-ha-guide-figure-3008]

   _**Şekil 9:** Küme adını girin_

4. Kümeyi oluşturduktan sonra bir küme doğrulama testi çalıştırın.

   ![Şekil 10: Küme doğrulama denetimini çalıştırın][sap-ha-guide-figure-3009]

   _**Şekil 10:** Küme doğrulama denetimini çalıştırın_

   İşlemdeki bu noktada disklerle ilgili tüm uyarıları yoksayabilirsiniz. Daha sonra bir dosya paylaşım tanığı ve SIOS Paylaşılan diskleri ekleyeceksiniz. Bu aşamada, bir çekirdeğe sahip olmak için endişelenmeniz gerekmez.

   ![Şekil 11: Hiçbir çekirdek disk bulunamadı][sap-ha-guide-figure-3010]

   _**Şekil 11:** Hiçbir çekirdek disk bulunamadı_

   ![Şekil 12: Çekirdek küme kaynağı için yeni bir IP adresi gerekir][sap-ha-guide-figure-3011]

   _**Şekil 12:** Çekirdek küme kaynağı için yeni bir IP adresi gerekir_

5. Çekirdek küme hizmetinin IP adresini değiştirin. Sunucu IP adresi sanal makine düğümlerinden birine işaret ettiğinden, küme, çekirdek küme hizmetinin IP adresini değiştirene kadar başlayamaz. Bunu, çekirdek küme hizmetinin IP kaynağının **Özellikler** sayfasında yapın.

   Örneğin, PR1-ascs-Vir küme sanal ana bilgisayar adı için bir IP adresi (örneğimizde, 10.0.0.42) atanmamız gerekiyor.

   ![Şekil 13: Özellikler iletişim kutusunda, IP adresini değiştirme][sap-ha-guide-figure-3012]

   _**Şekil 13:** **Özellikler** iletişim kutusunda, IP adresini değiştirme_

   ![Şekil 14: Küme için ayrılan IP adresini atama][sap-ha-guide-figure-3013]

   _**Şekil 14:** Küme için ayrılan IP adresini atama_

6. Küme sanal ana bilgisayar adını çevrimiçi duruma getirin.

   ![Şekil 15: Küme çekirdeği hizmeti doğru IP adresiyle çalışır duruma][sap-ha-guide-figure-3014]

   _**Şekil 15:** Küme çekirdeği hizmeti doğru IP adresiyle çalışır duruma_

7. İkinci küme düğümünü ekleyin.

   Artık çekirdek küme hizmeti çalışır durumda olduğundan, ikinci küme düğümünü ekleyebilirsiniz.

   ![Şekil 16 ikinci küme düğümünü ekleme][sap-ha-guide-figure-3015]

   _**Şekil 16:** İkinci küme düğümünü ekleme_

8. İkinci küme düğümü konağı için bir ad girin.

   ![Şekil 17: İkinci küme düğümü ana bilgisayar adını girin][sap-ha-guide-figure-3016]

   _**Şekil 17:** İkinci küme düğümü ana bilgisayar adını girin_

   > [!IMPORTANT]
   > **Kümeye uygun tüm depolamayı Ekle** onay kutusunun seçili *olmadığından* emin olun.  
   >
   >

   ![Şekil 18: Onay kutusunu seçmeyin][sap-ha-guide-figure-3017]

   _**Şekil 18:** Onay kutusunu seçmeyin_

   Çekirdek ve disklerle ilgili uyarıları yoksayabilirsiniz. [BIR SAP ASCS/SCS küme paylaşma diski için, daha sonra, SIOS 'yi, SIOS Dataman küme sürümü][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]bölümünde açıklandığı gibi daha sonra paylaşabilirsiniz.

   ![Şekil 19: Disk çekirdeği hakkındaki uyarıları yoksay][sap-ha-guide-figure-3018]

   _**Şekil 19:** Disk çekirdeği hakkındaki uyarıları yoksay_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Küme dosyası paylaşma tanığını yapılandırma

Küme dosya paylaşımının tanığını yapılandırmak şu görevleri içerir:

- Dosya paylaşma oluşturun.
- Dosya paylaşımının tanık çekirdeğini Yük Devretme Kümesi Yöneticisi ' de ayarlayın.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Dosya paylaşma oluşturma

1. Çekirdek disk yerine bir dosya paylaşma tanığı seçin. SIOS Dataman bu seçeneği destekler.

   Bu makaledeki örneklerde, dosya paylaşma tanığı Azure 'da çalışan Active Directory veya DNS sunucusudur. Dosya paylaşımının tanığı, domcontenr-0 olarak adlandırılır. Azure 'a (VPN Gateway veya Azure ExpressRoute aracılığıyla) bir VPN bağlantısı yapılandırdığınıza göre, Active Directory veya DNS hizmetiniz şirket içinde yer alır ve dosya paylaşımının bir tanığını çalıştırmak için uygun değildir.

   > [!NOTE]
   > Active Directory veya DNS hizmetiniz yalnızca şirket içinde çalışıyorsa, dosya paylaşma tanığını şirket içinde çalışan Active Directory veya DNS Windows işletim sisteminde yapılandırmayın. Azure 'da çalışan küme düğümleri ve şirket içi Active Directory veya DNS arasındaki ağ gecikmesi çok büyük olabilir ve bağlantı sorunlarına yol açabilir. Dosya paylaşımının tanığını, küme düğümüne kapat çalıştıran bir Azure sanal makinesinde yapılandırmayı unutmayın.  
   >
   >

   Çekirdek sürücüde en az 1.024 MB boş alan gerekir. Çekirdek sürücü için 2.048 MB boş alan öneririz.

2. Küme adı nesnesini ekleyin.

   ![Şekil 20: Küme adı nesnesi için paylaşımdaki izinleri atama][sap-ha-guide-figure-3019]

   _**Şekil 20:** Küme adı nesnesi için paylaşımdaki izinleri atama_

   İzinlerin küme adı nesnesi için paylaşımdaki verileri değiştirme yetkisini (bizim örneğimizde, PR1-ascs-Vir $) içerdiğinden emin olun.

3. Küme adı nesnesini listeye eklemek için **Ekle**' yi seçin. Şekil 22 ' de gösterilenler 'in yanı sıra, bilgisayar nesnelerini denetlemek için filtreyi değiştirin.

   ![Şekil 21: Nesne türlerini bilgisayarları içerecek şekilde değiştirin][sap-ha-guide-figure-3020]

   _**Şekil 21:** **Nesne türlerini** bilgisayarları içerecek şekilde değiştirin_

   ![Şekil 22: Bilgisayarlar onay kutusunu seçin][sap-ha-guide-figure-3021]

   _**Şekil 22:** **Bilgisayarlar** onay kutusunu seçin_

4. Şekil 21 ' de gösterildiği gibi küme adı nesnesini girin. Kayıt zaten oluşturulduğundan, Şekil 20 ' de gösterildiği gibi izinleri değiştirebilirsiniz.

5. Paylaşımın **güvenlik** sekmesini seçin ve ardından küme adı nesnesi için daha ayrıntılı izinler ayarlayın.

   ![Şekil 23: Dosya paylaşımında küme adı nesnesinin güvenlik özniteliklerini ayarlama][sap-ha-guide-figure-3022]

   _**Şekil 23:** Dosya paylaşımında küme adı nesnesinin güvenlik özniteliklerini ayarlama_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Yük Devretme Kümesi Yöneticisi 'de dosya paylaşma tanık çekirdeğini ayarlama

1. Çekirdek ayarlarını yapılandırma Sihirbazı 'Nı açın.

   ![Şekil 24: Küme çekirdek ayarlarını yapılandırma Sihirbazı 'Nı başlatma][sap-ha-guide-figure-3023]

   _**Şekil 24:** Küme çekirdek ayarlarını yapılandırma Sihirbazı 'Nı başlatma_

2. **Çekirdek yapılandırma seçeneğini belirleyin** sayfasında, **çekirdek tanığını Seç**' i seçin.

   ![Şekil 25: Aralarından seçim yapabileceğiniz çekirdek yapılandırma][sap-ha-guide-figure-3024]

   _**Şekil 25:** Aralarından seçim yapabileceğiniz çekirdek yapılandırma_

3. **Çekirdek tanığı seçin** sayfasında, **dosya paylaşma tanığını Yapılandır**' ı seçin.

   ![Şekil 26: Dosya paylaşma tanığını seçin][sap-ha-guide-figure-3025]

   _**Şekil 26:** Dosya paylaşma tanığını seçin_

4. Dosya paylaşımının UNC yolunu (örneğimizde, \\domcontr-0\FSW) girin. Yapabileceğiniz değişikliklerin listesini görmek için **İleri**' yi seçin.

   ![Şekil 27: Tanık paylaşımının dosya paylaşma konumunu tanımlayın][sap-ha-guide-figure-3026]

   _**Şekil 27:** Tanık paylaşımının dosya paylaşma konumunu tanımlayın_

5. İstediğiniz değişiklikleri seçin ve ardından **İleri**' yi seçin. Şekil 28 ' de gösterildiği gibi küme yapılandırmasını başarıyla yeniden yapılandırmanız gerekir:  

   ![Şekil 28: Kümeyi yeniden yapılandırdığınızı onaylama][sap-ha-guide-figure-3027]

   _**Şekil 28:** Kümeyi yeniden yapılandırdığınızı onaylama_

Windows Yük devretme kümesini başarılı bir şekilde yükledikten sonra bazı eşikleri, yük devretme algılamayı Azure 'daki koşullara uyarlayacak şekilde değiştirmeniz gerekir. Değiştirilecek parametreler, [Yük devretme kümesi ağ eşiklerini ayarlama][tuning-failover-cluster-network-thresholds]bölümünde belgelenmiştir. ASCS/SCS için Windows kümesi yapılandırmasını oluşturan iki VM 'nizin aynı alt ağda olduğunu varsayarsak, aşağıdaki parametreleri şu değerlerle değiştirin:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- Routinggeçmişini = 30

Bu ayarlar müşterilerle test edilmiştir ve iyi bir uzlaşma sağlar. Bunlar yeterince esnektir, ancak bir SAP yazılımında veya düğüm ya da VM hatasında gerçek hata koşullarında yeterince hızlı yük devretme sağlar.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>SAP ASCS/SCS küme paylaşma diski için SIOS Dataman küme sürümünü yükleyin

Artık Azure 'da çalışan bir Windows Server Yük Devretme Kümelemesi yapılandırmasına sahipsiniz. SAP yoks/SCS örneği yüklemek için, paylaşılan bir disk kaynağınız olması gerekir. Azure 'da ihtiyacınız olan paylaşılan disk kaynaklarını oluşturamazsınız. SIOS Dataman küme sürümü, paylaşılan disk kaynakları oluşturmak için kullanabileceğiniz bir üçüncü taraf çözümüdür.

SAP ASCS/SCS küme paylaşma diski için SIOS Dataman küme sürümü yüklemesi şu görevleri içerir:

- Microsoft .NET Framework 3,5 ekleyin.
- SIOS Dataman 'ı yükler.
- SIOS Dataman 'ı ayarlayın.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>3,5 .NET Framework ekleyin
.NET Framework 3,5, Windows Server 2012 R2 'de otomatik olarak etkinleştirilmez veya yüklenemez. , SIOS Dataman 'ın veri sahibi yüklediğiniz tüm düğümlerde olması gerektiğinden, kümedeki tüm sanal makinelerin Konuk işletim sistemine .NET Framework 3,5 ' ü yüklemelisiniz.

3,5 .NET Framework eklemenin iki yolu vardır:

- Şekil 29 ' da gösterildiği gibi Windows 'daki rol ve Özellik Ekleme Sihirbazı ' nı kullanın:

  ![Şekil 29: Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 ' i yükler][sap-ha-guide-figure-3028]

  _**Şekil 29:** Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 ' i yükler_

  ![Şekil 30: Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 yüklediğinizde yükleme ilerleme çubuğu][sap-ha-guide-figure-3029]

  _**Şekil 30:** Rol ve Özellik Ekleme Sihirbazı 'Nı kullanarak .NET Framework 3,5 yüklediğinizde yükleme ilerleme çubuğu_

- Dism. exe komut satırı aracını kullanın. Bu yükleme türü için, Windows yükleme medyasında SxS dizinine erişmeniz gerekir. Yükseltilmiş bir komut isteminde şu komutu girin:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS Dataman 'ı yükler

Kümedeki her düğüme SIOS Dataman küme sürümü yükleyin. Bir sanal paylaşılan depolama alanını, SIOS \ veri Man ile oluşturmak için eşitlenmiş bir yansıtma oluşturun ve küme paylaşılan depolama benzetimi yapın.

SIOS yazılımını yüklemeden önce, Datasperpersvc etki alanı kullanıcısını oluşturun.

> [!NOTE]
> Data, Persvc etki alanı kullanıcısını her iki küme düğümünde yerel yönetici grubuna ekleyin.
>
>

SIOS Dataman 'ı yüklemek için:

1. Her iki küme düğümüne da SIOS yazılımını yükler.

   ![SIOS yükleyicisi][sap-ha-guide-figure-3030]

   ![Şekil 31: SIOS Dataman yüklemesinin ilk sayfası][sap-ha-guide-figure-3031]

   _**Şekil 31:** SIOS Dataman yüklemesinin ilk sayfası_

2. İletişim kutusunda **Evet**' i seçin.

   ![Şekil 32: Dataman, bir hizmetin devre dışı bırakılacağını bildirir][sap-ha-guide-figure-3032]

   _**Şekil 32:** Dataman, bir hizmetin devre dışı bırakılacağını bildirir_

3. İletişim kutusunda **etki alanı veya sunucu hesabı**' nı seçmenizi öneririz.

   ![Şekil 33: SIOS veri Man için Kullanıcı seçimi][sap-ha-guide-figure-3033]

   _**Şekil 33:** SIOS veri Man için Kullanıcı seçimi_

4. Bir etki alanı hesabı Kullanıcı adı ve SIOS veri Man için oluşturduğunuz parolayı girin.

   ![Şekil 34: SIOS Dataman yüklemesi için etki alanı Kullanıcı adı ve parolasını girin][sap-ha-guide-figure-3034]

   _**Şekil 34:** SIOS Dataman yüklemesi için etki alanı Kullanıcı adı ve parolasını girin_

5. Şekil 35 ' de gösterildiği gibi, SIOS Dataman örneğiniz için lisans anahtarını yükler.

   ![Şekil 35: SIOS Dataman lisans anahtarınızı girin][sap-ha-guide-figure-3035]

   _**Şekil 35:** SIOS Dataman lisans anahtarınızı girin_

6. İstendiğinde, sanal makineyi yeniden başlatın.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS Dataman 'ı ayarlama

Her iki düğüme de SIOS veri Man 'ı yükledikten sonra yapılandırmayı başlatın. Yapılandırmanın amacı, sanal makinelerin her birine eklenmiş ek diskler arasında zaman uyumlu veri çoğaltmasına sahip olmaktır.

1. Dataman yönetimi ve yapılandırma aracını başlatın ve ardından **sunucuyu bağla**' yı seçin.

   ![Şekil 36: SIOS Dataman yönetimi ve yapılandırma aracı][sap-ha-guide-figure-3036]

   _**Şekil 36:** SIOS Dataman yönetimi ve yapılandırma aracı_

2. Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini ve ikinci bir adımda ikinci düğümü girin.

   ![Şekil 37: Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini, ikinci bir adımda ise ikinci düğümü ekleyin][sap-ha-guide-figure-3037]

   _**Şekil 37:** Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini, ikinci bir adımda ise ikinci düğümü ekleyin_

3. İki düğüm arasında çoğaltma işi oluşturun.

   ![Şekil 38: Çoğaltma işi oluşturma][sap-ha-guide-figure-3038]

   _**Şekil 38:** Çoğaltma işi oluşturma_

   Bir sihirbaz, çoğaltma işi oluşturma sürecinde size rehberlik eder.

4. Çoğaltma işinin adını tanımlayın.

   ![Şekil 39: Çoğaltma işinin adını tanımlayın][sap-ha-guide-figure-3039]

   _**Şekil 39:** Çoğaltma işinin adını tanımlayın_

   ![Şekil 40: Düğüm için, geçerli kaynak düğümü olması gereken temel verileri tanımlayın][sap-ha-guide-figure-3040]

   _**Şekil 40:** Düğüm için, geçerli kaynak düğümü olması gereken temel verileri tanımlayın_

5. Hedef düğümün adını, TCP/IP adresini ve disk birimini tanımlayın.

   ![Şekil 41: Geçerli hedef düğümün adını, TCP/IP adresini ve disk birimini tanımlayın][sap-ha-guide-figure-3041]

   _**Şekil 41:** Geçerli hedef düğümün adını, TCP/IP adresini ve disk birimini tanımlayın_

6. Sıkıştırma algoritmalarını tanımlayın. Örneğimizde, çoğaltma akışını sıkıştırmanız önerilir. Özellikle yeniden eşitleme durumlarında, çoğaltma akışının sıkıştırılması, yeniden eşitleme süresini önemli ölçüde azaltır. Sıkıştırma, bir sanal makinenin CPU ve RAM kaynaklarını kullanır. Sıkıştırma hızının arttığı için, kullanılan CPU kaynakları hacmi olur. Bu ayarı daha sonra ayarlayabilirsiniz.

7. Kopyalamanın zaman uyumsuz olarak mı yoksa zaman uyumlu mı oluştuğunu denetlemeniz gereken başka bir ayar. SAP ASCS/SCS yapılandırmasını koruduğunuzda, zaman uyumlu çoğaltma kullanmanız gerekir.  

   ![Şekil 42: Çoğaltma ayrıntılarını tanımlama][sap-ha-guide-figure-3042]

   _**Şekil 42:** Çoğaltma ayrıntılarını tanımlama_

8. Çoğaltma işi tarafından çoğaltılan birimin paylaşılan disk olarak bir Windows Server yük devretme kümesi yapılandırmasına temsil edilip edilmeyeceğini tanımlayın. SAP ASCS/SCS yapılandırması için, Windows kümesinin çoğaltılan birimi küme birimi olarak kullanabileceği paylaşılan bir disk olarak görmesi için **Evet** ' i seçin.

   ![Şekil 43: Çoğaltılan birimi bir küme birimi olarak ayarlamak için Evet ' i seçin][sap-ha-guide-figure-3043]

   _**Şekil 43:** Çoğaltılan birimi bir küme birimi olarak ayarlamak için **Evet** ' i seçin_

   Birim oluşturulduktan sonra, veri Man Yönetimi ve yapılandırma aracı, çoğaltma işinin etkin olduğunu gösterir.

   ![Şekil 44: SAP yoks/SCS paylaşılan diski için Dataman zaman uyumlu yansıtma etkin][sap-ha-guide-figure-3044]

   _**Şekil 44:** SAP yoks/SCS paylaşılan diski için Dataman zaman uyumlu yansıtma etkin_

   Yük Devretme Kümesi Yöneticisi Şekil 45 ' de gösterildiği gibi artık diski bir Dataman diski olarak göstermektedir:

   ![Şekil 45: Yük Devretme Kümesi Yöneticisi, Dataman 'ın çoğaltılan diski gösterir][sap-ha-guide-figure-3045]

   _**Şekil 45:** Yük Devretme Kümesi Yöneticisi, Dataman 'ın çoğaltılan diski gösterir_

## <a name="next-steps"></a>Sonraki adımlar

* [SAP Ass/SCS örneği için Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP NetWeaver HA 'yi yükler][sap-high-availability-installation-wsfc-shared-disk]
