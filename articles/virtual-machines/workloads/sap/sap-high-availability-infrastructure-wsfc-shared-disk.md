---
title: WSFC&paylaşılan disk ile SAP yoks/SCS için Azure altyapısı | Microsoft Docs
description: SAP Ass/SCS örneği için bir Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını nasıl hazırlayacağınızı öğrenin.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4218b4c00b79d78965eaf6e73028e63f52b1ff17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673625"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>SAP Ass/SCS için bir Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını hazırlama

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows İşletim Sistemi][Logo_Windows] Windows


Bu makalede, bir SAP ASCS örneğini kümelemek için bir seçenek olarak *Küme Paylaşılan diski* kullanarak bir Windows Yük devretme kümesine yüksek KULLANıLABILIRLIĞE sahip SAP KÖKLERI/SCS örneği yükleme ve yapılandırma için Azure altyapısını hazırlamak üzere uygulamanız gereken adımlar açıklanmaktadır.
*Küme paylaşılan diskinin* iki seçeneği belgelerde sunulmaktadır:

- [Azure paylaşılan diskleri](../../disks-shared.md)
- Kümelenmiş paylaşılan diskin benzetimini yapan yansıtılmış depolama alanı oluşturmak için [SIOS Dataman küme sürümünü](https://us.sios.com/products/datakeeper-cluster/) kullanma 

Sunulan yapılandırma, SAP iş yükleri için en iyi ağ gecikme süresine ulaşmak üzere [Azure yakınlık yerleştirme gruplarına (PPG)](./sap-proximity-placement-scenarios.md) bağlıdır. Belgeler veritabanı katmanını kapsamaz.  

> [!NOTE]
> Azure yakınlık yerleştirme grupları, Azure Paylaşılan disk kullanımı için önkoşuldur.
 

## <a name="prerequisites"></a>Önkoşullar

Yüklemeye başlamadan önce şu makaleyi gözden geçirin:

* [Mimari Kılavuzu: Küme Paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP yoks/SCS örneği oluşturma][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>Yoks VM 'lerini oluşturma

SAP ASCS/SCS kümesi için Azure kullanılabilirlik kümesi 'nde iki VM dağıtın. VM 'Leri aynı yakınlık yerleşimi grubuna dağıtın. VM 'Ler dağıtıldıktan sonra:  
- SAP Ass/SCS örneği için Azure Iç Load Balancer oluşturma 
- AD etki alanına Windows VM 'Leri ekleme

Görüntülenen senaryonun ana bilgisayar adları ve IP adresleri şunlardır:

| Ana bilgisayar adı rolü | Konak adı | Statik IP adresi | Kullanılabilirlik kümesi | Yakınlık yerleşimi grubu |
| --- | --- | --- |---| ---|
| 1. küme düğümü yoks/SCS kümesi |PR1-ascs-10 |10.0.0.4 |PR1-ascs-avset |PR1PPG |
| 2. küme düğümü yoks/SCS kümesi |PR1-ascs-11 |10.0.0.5 |PR1-ascs-avset |PR1PPG |
| Küme ağ adı | pr1clust |10.0.0.42 (**yalnızca** Win 2016 kümesi için) | yok | yok |
| YOKS küme ağı adı | PR1-ascscl |10.0.0.43 | yok | yok |
| ERS küme ağı adı (**yalnızca** ERS2 için) | PR1-erscl |10.0.0.44 | yok | yok |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Azure iç yük dengeleyici oluşturma

SAP ASCS, SAP SCS ve yeni SAP ERS2, sanal konak adı ve sanal IP adresleri kullanın. Azure 'da bir sanal IP adresi kullanmak için bir [yük dengeleyici](../../../load-balancer/load-balancer-overview.md) gereklidir. [Standart yük dengeleyiciyi](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)kullanmanızı kesinlikle öneririz. 

> [!IMPORTANT]
> Kayan IP, Yük Dengeleme senaryolarında NIC ikincil IP yapılandırmasında desteklenmez. Ayrıntılar için bkz. [Azure yük dengeleyici sınırlamaları](../../../load-balancer/load-balancer-multivip-overview.md#limitations). VM için ek IP adresine ihtiyacınız varsa ikinci bir NIC dağıtın.    


Aşağıdaki listede, (A) SCS/ERS yük dengeleyicinin yapılandırması gösterilmektedir. Aynı Azure Yük dengeleyicisinde gerçekleştirilen SAP ASCS ve ERS2 için yapılandırma.  

**A PSC**
- Ön uç yapılandırması
    - Statik yoks/SCS IP adresi **10.0.0.43**
- Arka uç yapılandırması  
    (A) SCS/ERS kümesinin parçası olması gereken tüm sanal makineleri ekleyin. Bu örnekte, **PR1-ascs-10** ve **PR1-ascs-11** VM 'lerinde.
- Araştırma bağlantı noktası
    - Bağlantı noktası 620 **NR** protokol (TCP), Aralık (5), sağlıksız eşik (2) için varsayılan seçeneği bırakın
- Yük Dengeleme kuralları
    - Standart Load Balancer kullanıyorsanız HA bağlantı noktaları ' nı seçin.
    - Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
        - 32 **NR** TCP
        - 36 **NR** TCP
        - 39 **NR** TCP
        - 81 **NR** TCP
        - 5 **NR** 13 TCP
        - 5 **NR** 14 TCP
        - 5 **NR** 16 TCP

    - Boşta zaman aşımı (dakika) değerinin en yüksek değer olan 30 ' a ayarlandığından ve kayan IP (doğrudan sunucu dönüşü) etkinleştirildiğinden emin olun.

**ERS2**

Sıraya alma çoğaltma sunucusu 2 (ERS2) da kümelenmiş olduğundan, ERS2 sanal IP adresinin, yukarıdaki SAP ASCS/SCS IP 'ye ek olarak Azure ıLB üzerinde de yapılandırılması gerekir. Bu bölüm yalnızca sıraya alma çoğaltma sunucusu 2 mimarisi kullanılıyorsa geçerlidir.  
- ikinci ön uç yapılandırması
    - Statik SAP ERS2 IP adresi **10.0.0.44**

- Arka uç yapılandırması  
  VM 'Ler zaten ıLB arka uç havuzuna eklenmiş.  

- 2. araştırma bağlantı noktası
    - Bağlantı noktası 621 **NR**  
    Protokol (TCP), Aralık (5), sağlıksız eşik (2) için varsayılan seçeneği bırakın

- 2. Yük Dengeleme kuralları
    - Standart Load Balancer kullanıyorsanız HA bağlantı noktaları ' nı seçin.
    - Temel Load Balancer kullanıyorsanız, aşağıdaki bağlantı noktaları için Yük Dengeleme kuralları oluşturun
        - 32 **NR** TCP
        - 33 **NR** TCP
        - 5 **NR** 13 TCP
        - 5 **NR** 14 TCP
        - 5 **NR** 16 TCP

    - Boşta zaman aşımı (dakika) değerinin en yüksek değer olan 30 ' a ayarlandığından ve kayan IP (doğrudan sunucu dönüşü) etkinleştirildiğinden emin olun.


> [!TIP]
> [Azure Paylaşılan disk Ile SAP Ass/SCS örneği IÇIN WSFC Azure Resource Manager şablonuyla](https://github.com/robotechredmond/301-shared-disk-sap), ERS1 Ile BIR SAP SID 'Si Için Azure Paylaşılan disk kullanarak altyapı hazırlanmasını otomatik hale getirebilirsiniz.  
> Azure ARM şablonu iki Windows 2019 veya 2016 VM oluşturur, Azure Paylaşılan disk oluşturur ve VM 'lere eklenir. Azure Iç Load Balancer de oluşturulacak ve yapılandırılacaktır. Ayrıntılar için bkz. ARM şablonu. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> ASCS/SCS örneğinin küme düğümlerine kayıt defteri girişleri ekleme

Bağlantılar bir süre boyunca boşta kalırsa ve boşta kalma zaman aşımını aşarsa Azure Load Balancer bağlantıları kapatabilir. SAP çalışma işlemleri, ilk sıraya alma/sıradan çıkarma isteğinin gönderilmesi gerektiği anda SAP sıraya alma işlemine bağlantıları açar. Bu bağlantıları kesintiye uğramamak için her iki küme düğümünde TCP/IP KeepAliveTime ve Keepaliveınterval değerlerini değiştirin. ERS1 kullanıyorsanız, bu makalenin ilerleyen kısımlarında açıklandığı gibi SAP profili parametrelerini de eklemek de gereklidir.
Aşağıdaki kayıt defteri girdilerinin her iki küme düğümünde de değiştirilmesi gerekir:

- KeepAliveTime
- Keepaliveınterval

| Yol| Değişken adı | Değişken türü  | Değer | Belgeler |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (ondalık) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |Keepaliveınterval |REG_DWORD (ondalık) |120000 |[Keepaliveınterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


Değişiklikleri uygulamak için her iki küme düğümünü yeniden başlatın.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Windows VM 'lerini etki alanına ekleme
Sanal makinelere statik IP adresleri atadıktan sonra, sanal makineleri etki alanına ekleyin. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Windows Yük devretme kümesi 'ni yükleyip yapılandırma 

### <a name="install-the-windows-failover-cluster-feature"></a>Windows Yük devretme kümesi özelliğini yükler

Küme düğümlerinden birinde bu komutu çalıştırın:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Özellik yüklemesi tamamlandıktan sonra, her iki küme düğümünü yeniden başlatın.  

### <a name="test-and-configure-windows-failover-cluster"></a>Windows Yük devretme kümesini test etme ve yapılandırma 

Windows 2019 ' de, küme Azure 'da çalıştığını ve küme yönetimi IP 'si için varsayılan seçenek olarak, dağıtılmış ağ adı 'nı kullanacak şekilde otomatik olarak tanıyacaktır. Bu nedenle, herhangi bir küme düğümü yerel IP adresi kullanacaktır. Sonuç olarak, küme için adanmış (sanal) bir ağ adı gerekmez ve bu IP adresinin Azure Iç Load Balancer yapılandırılması gerekmez.

Daha fazla bilgi için bkz. [Windows Server 2019 Yük Devretme Kümelemesi yeni özellikler](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) küme düğümlerinden birinde bu komutu çalıştırın:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Küme bulutu çekirdeğini yapılandırma
Windows Server 2016 veya 2019 kullandığınızda, küme çekirdeği olarak [Azure Cloud tanığını](/windows-server/failover-clustering/deploy-cloud-witness)yapılandırmanızı öneririz.

Küme düğümlerinden birinde bu komutu çalıştırın:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Windows Yük devretme kümesi eşiklerini ayarlama
 
Windows Yük devretme kümesini başarıyla yükledikten sonra, Azure 'da dağıtılan kümeler için uygun olması için bazı eşikleri ayarlamanız gerekir. Değiştirilecek parametreler, [Yük devretme kümesi ağ eşiklerini ayarlama](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)bölümünde belgelenmiştir. ASCS/SCS için Windows kümesi yapılandırmasını oluşturan iki VM 'nizin aynı alt ağda olduğunu varsayarsak, aşağıdaki parametreleri şu değerlerle değiştirin:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- Routinggeçmişini = 30

Bu ayarlar müşterilerle test edilmiştir ve iyi bir uzlaşma sağlar. Bunlar yeterince esnektir, ancak SAP iş yükleri ya da VM hatasında gerçek hata koşulları için yeterince hızlı yük devretme sağlar.  

## <a name="configure-azure-shared-disk"></a>Azure Paylaşılan disk yapılandırma
Bu bölüm yalnızca Azure Paylaşılan disk kullanıyorsanız geçerlidir. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>PowerShell ile Azure Paylaşılan diski oluşturma ve iliştirme
Küme düğümlerinden birinde bu komutu çalıştırın. Kaynak grubunuz, Azure bölgesi, SAPSıD, vb. değerlerini ayarlamanız gerekir.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>Paylaşılan diski PowerShell ile biçimlendirme
1. Disk numarasını alın. Bu PowerShell komutlarını küme düğümlerinden birinde Çalıştır:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Diski biçimlendirin. Bu örnekte, disk numarası 2 ' dir. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Diskin artık bir küme diski olarak görünür olduğunu doğrulayın.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Diski kümeye kaydedin.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS küme paylaşma diski için SIOS Dataman küme sürümü
Bu bölüm yalnızca, küme paylaşılan diskine benzetim yapan yansıtılmış bir depolama alanı oluşturmak için üçüncü taraf yazılım SIOS Dataman kümesi sürümünü kullanıyorsanız geçerlidir.  

Artık Azure 'da çalışan bir Windows Server Yük Devretme Kümelemesi yapılandırmasına sahipsiniz. SAP yoks/SCS örneği yüklemek için, paylaşılan bir disk kaynağınız olması gerekir. Seçeneklerden biri, SIOS Dataman Cluster Edition 'ın kullanılması, paylaşılan disk kaynakları oluşturmak için kullanabileceğiniz bir üçüncü taraf çözümüdür.  

SAP ASCS/SCS küme paylaşma diski için SIOS Dataman küme sürümü yüklemesi şu görevleri içerir:
- Gerekirse Microsoft .NET Framework ekleyin. Bkz. [SIOS belgeleri] (( https://us.sios.com/products/datakeeper-cluster/) en güncel .NET Framework gereksinimleri için). 
- SIOS Dataman 'ı yükler
- SIOS Dataman 'ı yapılandırma

### <a name="install-sios-datakeeper"></a>SIOS Dataman 'ı yükler
Kümedeki her düğüme SIOS Dataman küme sürümü yükleyin. Bir sanal paylaşılan depolama alanını, SIOS \ veri Man ile oluşturmak için eşitlenmiş bir yansıtma oluşturun ve küme paylaşılan depolama benzetimi yapın.

SIOS yazılımını yüklemeden önce, Datasperpersvc etki alanı kullanıcısını oluşturun.

> [!NOTE]
> Data, Persvc etki alanı kullanıcısını her iki küme düğümünde yerel yönetici grubuna ekleyin.
>

1. Her iki küme düğümüne da SIOS yazılımını yükler.

   ![SIOS yükleyicisi][sap-ha-guide-figure-3030]

   ![Şekil 31: SIOS Dataman yüklemesinin Ilk sayfası][sap-ha-guide-figure-3031]

   _SIOS Dataman yüklemesinin ilk sayfası_

2. İletişim kutusunda **Evet**' i seçin.

   ![Şekil 32: Dataman bir hizmetin devre dışı bırakıldığını bildirir][sap-ha-guide-figure-3032]

   _Dataman, bir hizmetin devre dışı bırakılacağını bildirir_

3. İletişim kutusunda **etki alanı veya sunucu hesabı**' nı seçmenizi öneririz.

   ![Şekil 33: SIOS veri Man için Kullanıcı seçimi][sap-ha-guide-figure-3033]

   _SIOS veri Man için Kullanıcı seçimi_

4. Bir etki alanı hesabı Kullanıcı adı ve SIOS veri Man için oluşturduğunuz parolayı girin.

   ![Şekil 34: SIOS Dataman yüklemesi için etki alanı Kullanıcı adı ve parolasını girin][sap-ha-guide-figure-3034]

   _SIOS Dataman yüklemesi için etki alanı Kullanıcı adı ve parolasını girin_

5. Şekil 35 ' de gösterildiği gibi, SIOS Dataman örneğiniz için lisans anahtarını yükler.

   ![Şekil 35: SIOS Dataman lisans anahtarınızı girin][sap-ha-guide-figure-3035]

   _SIOS Dataman lisans anahtarınızı girin_

6. İstendiğinde, sanal makineyi yeniden başlatın.

### <a name="configure-sios-datakeeper"></a>SIOS Dataman 'ı yapılandırma
Her iki düğüme de SIOS veri Man 'ı yükledikten sonra yapılandırmayı başlatın. Yapılandırmanın amacı, sanal makinelerin her birine eklenmiş ek diskler arasında zaman uyumlu veri çoğaltmasına sahip olmaktır.

1. Dataman yönetimi ve yapılandırma aracını başlatın ve ardından **sunucuyu bağla**' yı seçin.

   ![Şekil 36: SIOS Dataman yönetimi ve yapılandırma aracı][sap-ha-guide-figure-3036]

   _SIOS Dataman yönetimi ve yapılandırma aracı_

2. Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini ve ikinci bir adımda ikinci düğümü girin.

   ![Şekil 37: yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini veya ikinci bir adımda ikinci bir adımda ekleyin][sap-ha-guide-figure-3037]

   _Yönetim ve yapılandırma aracının bağlanması gereken ilk düğümün adını veya TCP/IP adresini, ikinci bir adımda ise ikinci düğümü ekleyin_

3. İki düğüm arasında çoğaltma işi oluşturun.

   ![Şekil 38: çoğaltma işi oluşturma][sap-ha-guide-figure-3038]

   _Çoğaltma işi oluşturma_

   Bir sihirbaz, çoğaltma işi oluşturma sürecinde size rehberlik eder.

4. Çoğaltma işinin adını tanımlayın.

   ![Şekil 39: çoğaltma işinin adını tanımlayın][sap-ha-guide-figure-3039]

   _Çoğaltma işinin adını tanımlayın_

   ![Şekil 40: düğüm için, geçerli kaynak düğümü olması gereken temel verileri tanımlayın][sap-ha-guide-figure-3040]

   _Düğüm için, geçerli kaynak düğümü olması gereken temel verileri tanımlayın_

5. Hedef düğümün adını, TCP/IP adresini ve disk birimini tanımlayın.

   ![Şekil 41: geçerli hedef düğümün adını, TCP/IP adresini ve disk birimini tanımlayın][sap-ha-guide-figure-3041]

   _Geçerli hedef düğümün adını, TCP/IP adresini ve disk birimini tanımlayın_

6. Sıkıştırma algoritmalarını tanımlayın. Örneğimizde, çoğaltma akışını sıkıştırmanız önerilir. Özellikle yeniden eşitleme durumlarında, çoğaltma akışının sıkıştırılması, yeniden eşitleme süresini önemli ölçüde azaltır. Sıkıştırma, bir sanal makinenin CPU ve RAM kaynaklarını kullanır. Sıkıştırma hızının arttığı için, kullanılan CPU kaynakları hacmi olur. Bu ayarı daha sonra ayarlayabilirsiniz.

7. Kopyalamanın zaman uyumsuz olarak mı yoksa zaman uyumlu mı oluştuğunu denetlemeniz gereken başka bir ayar. SAP ASCS/SCS yapılandırmasını koruduğunuzda, zaman uyumlu çoğaltma kullanmanız gerekir.  

   ![Şekil 42: çoğaltma ayrıntılarını tanımlama][sap-ha-guide-figure-3042]

   _Çoğaltma ayrıntılarını tanımlama_

8. Çoğaltma işi tarafından çoğaltılan birimin paylaşılan disk olarak bir Windows Server yük devretme kümesi yapılandırmasına temsil edilip edilmeyeceğini tanımlayın. SAP ASCS/SCS yapılandırması için, Windows kümesinin çoğaltılan birimi küme birimi olarak kullanabileceği paylaşılan bir disk olarak görmesi için **Evet** ' i seçin.

   ![Şekil 43: çoğaltılan birimi bir küme birimi olarak ayarlamak için Evet ' i seçin][sap-ha-guide-figure-3043]

   _Çoğaltılan birimi bir küme birimi olarak ayarlamak için **Evet** ' i seçin_

   Birim oluşturulduktan sonra, veri Man Yönetimi ve yapılandırma aracı, çoğaltma işinin etkin olduğunu gösterir.

   ![Şekil 44: SAP ASCS/SCS paylaşılan diski için Dataman zaman uyumlu yansıtma etkin][sap-ha-guide-figure-3044]

   _SAP yoks/SCS paylaşılan diski için Dataman zaman uyumlu yansıtma etkin_

   Yük Devretme Kümesi Yöneticisi Şekil 45 ' de gösterildiği gibi artık diski bir Dataman diski olarak göstermektedir:

   ![Şekil 45: Yük Devretme Kümesi Yöneticisi, Dataman 'ın çoğaltılan diski gösterir][sap-ha-guide-figure-3045]

   _Yük Devretme Kümesi Yöneticisi, Dataman 'ın çoğaltılan diski gösterir_


## <a name="next-steps"></a>Sonraki adımlar

* [SAP Ass/SCS örneği için Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP NetWeaver HA 'yi yükler][sap-high-availability-installation-wsfc-shared-disk]