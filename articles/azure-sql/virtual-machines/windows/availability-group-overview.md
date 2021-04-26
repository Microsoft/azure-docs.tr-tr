---
title: SQL Server Always on kullanılabilirlik gruplarına genel bakış
description: Bu makalede, Azure sanal makinelerinde her zaman açık kullanılabilirlik grupları SQL Server açıklanır.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 8bbd56499c9b62248662fc5e8df0d5b3e1b672d4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102504176"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM 'lerinde SQL Server her zaman kullanılabilirlik grubu
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Bu makalede, Azure sanal makinelerinde (VM) SQL Server için her zaman açık kullanılabilirlik grupları tanıtılmaktadır. 

## <a name="overview"></a>Genel Bakış

Azure sanal makinelerinde her zaman açık kullanılabilirlik grupları, [Şirket içi kullanılabilirlik gruplarına](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)benzerdir. Ancak, sanal makineler Azure 'da barındırıldığından, VM artıklığı ve Azure ağı üzerinde yönlendirme trafiği gibi bazı ek hususlar de vardır. 

Aşağıdaki diyagramda Azure VM 'lerinde SQL Server için bir kullanılabilirlik grubu gösterilmektedir:

![Kullanılabilirlik Grubu](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM artıklığı 

Yedeklilik ve yüksek kullanılabilirliği artırmak için SQL Server VM 'Lerin aynı [kullanılabilirlik kümesinde](../../../virtual-machines/availability-set-overview.md)veya farklı [kullanılabilirlik bölgelerinde](../../../availability-zones/az-overview.md)olması gerekir.

Aynı Kullanılabilirlik kümesine bir VM kümesinin yerleştirilmesi, donanım arızasından kaynaklanan bir veri merkezi içindeki kesintilerden (bir kullanılabilirlik kümesindeki VM 'Ler, kaynakları paylaşmaz) veya güncelleştirmelerden (bir kullanılabilirlik kümesindeki VM 'ler aynı anda güncellenmez) karşı koruma sağlar. Bir bölgedeki veri merkezleri kümesini temsil eden her bölge ile tüm veri merkezinde hataya karşı koruma Kullanılabilirlik Alanları.  Kaynakların farklı Kullanılabilirlik Alanları yerleştirildiğinden emin olduktan sonra, tüm sanal makinelerinizi çevrimdışına alabilir.

Azure VM 'Leri oluştururken, kullanılabilirlik kümelerini yapılandırma ile Kullanılabilirlik Alanları arasında seçim yapmanız gerekir.  Azure VM, her ikisine de katılamaz.


## <a name="connectivity"></a>Bağlantı 

Geleneksel bir şirket içi dağıtımda istemciler, sanal ağ adını (VNN) kullanarak kullanılabilirlik grubu dinleyicisine bağlanır ve dinleyici, trafiği kullanılabilirlik grubundaki uygun SQL Server çoğaltmaya yönlendirir. Ancak, trafiği Azure ağında yönlendirmek için ek bir gereksinim vardır. 

Azure VM 'lerinde SQL Server, trafiği kullanılabilirlik grubu dinleyicinize yönlendirmek için bir [yük dengeleyici](availability-group-vnn-azure-load-balancer-configure.md) yapılandırın veya SQL Server 2019 CU8 ve sonraki bir sürümü kullanıyorsanız, geleneksel vnn kullanılabilirlik grubu dinleyicisinin yerini alacak bir [dağıtılmış ağ adı (DNN) dinleyicisi](availability-group-distributed-network-name-dnn-listener-configure.md) yapılandırabilirsiniz. 

Küme bağlantı seçenekleri hakkında daha fazla bilgi için bkz. [Azure VM 'lerinde SQL Server HADR bağlantıları yönlendirme](hadr-cluster-best-practices.md#connectivity). 

### <a name="vnn-listener"></a>VNN dinleyicisi 

İstemciden gelen trafiği Azure ağındaki geleneksel kullanılabilirlik grubu sanal ağ adı (VNN) dinleyicisine yönlendirmek için bir [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) kullanın. 

Yük dengeleyici, VNN dinleyicisi için IP adreslerini barındırır. Birden fazla kullanılabilirlik grubunuz varsa, her grup bir VNN dinleyicisi gerektirir. Bir yük dengeleyici birden çok dinleyicisi destekleyebilir.

Başlamak için bkz. [Yük dengeleyiciyi yapılandırma](availability-group-vnn-azure-load-balancer-configure.md). 

### <a name="dnn-listener"></a>DNN dinleyicisi

SQL Server 2019 CU8, dağıtılmış ağ adı (DNN) dinleyicisi için destek sunar. DNN dinleyicisi, Azure ağındaki trafiği yönlendirmek için bir Azure Load Balancer gereksinimini ortadan kaldıran geleneksel kullanılabilirlik grubu dinleyicisinin yerini alır. 

DNN dinleyicisi, dağıtımı basitleşerek Azure 'da önerilen HADR bağlantı çözümüdür, bakım ve maliyeti azaltır ve hata durumunda yük devretme süresini azaltır. 

Mevcut bir VNN dinleyicisini değiştirmek için DNN dinleyicisini kullanın veya alternatif olarak, mevcut bir VNN dinleyicisi ile birlikte kullanın, böylece kullanılabilirlik grubunuzun iki ayrı bağlantı noktası olması gerekir. bu sayede, bir tane, VNN dinleyicisi adını (ve varsayılan olmayan bağlantı noktasını) ve bir tane DNN dinleyici adını ve bağlantı noktasını kullanarak bir tane. Bu, yük dengeleyici yük devretme gecikmesini önlemek isteyen, ancak dağıtılmış kullanılabilirlik grupları, hizmet Aracısı veya FILESTREAM gibi VNN dinleyicisine bağlı SQL Server özelliklerden faydalanan müşteriler için yararlı olabilir. Daha fazla bilgi edinmek için bkz. [DNN dinleyicisi ve SQL Server özelliği birlikte çalışabilirliği](availability-group-dnn-interoperability.md)

Başlamak için bkz. [DNN dinleyicisini yapılandırma](availability-group-distributed-network-name-dnn-listener-configure.md).


## <a name="deployment"></a>Dağıtım 

Azure VM 'lerinde SQL Server için bir kullanılabilirlik grubu dağıtmaya yönelik birden çok seçenek mevcuttur, bazıları diğerlerinden daha fazla otomasyon sağlar. 

Aşağıdaki tabloda kullanılabilir seçeneklerin karşılaştırması verilmiştir:

| | Azure portalı | Azure CLı/PowerShell | Hızlı başlangıç şablonları | El ile |
|---------|---------|---------|---------|---------|
|**SQL Server sürümü** |2016 + |2016 +|2016 +|2012 +|
|**SQL Server yayını** |Kurumsal |Kurumsal |Kurumsal |Kurumsal, standart|
|**Windows Server sürümü**| 2016 + | 2016 + | 2016 + | Tümü|
|**Sizin için kümeyi oluşturur**|Yes|Yes | Yes |Hayır|
|**Sizin için kullanılabilirlik grubunu oluşturur** |Yes |Hayır|Hayır|Hayır|
|**Bağımsız olarak dinleyici ve yük dengeleyici oluşturur** |Hayır|Hayır|Hayır|Yes|
|**Bu yöntem kullanılarak DNN dinleyicisi oluşturulabilir mi?**|Hayır|Hayır|Hayır|Yes|
|**WSFC çekirdek yapılandırması**|Bulut tanığı|Bulut tanığı|Bulut tanığı|Tümü|
|**Birden çok bölgeye sahip DR** |Hayır|Hayır|Hayır|Yes|
|**Multisubnet desteği** |Yes|Yes|Yes|Yes|
|**Mevcut bir AD için destek**|Yes|Yes|Yes|Yes|
|**Aynı bölgede multizone ile DR**|Yes|Yes|Yes|Yes|
|**AD olmadan dağıtılmış AG**|Hayır|Hayır|Hayır|Yes|
|**Küme olmadan dağıtılmış AG** |Hayır|Hayır|Hayır|Yes|

Daha fazla bilgi için bkz. [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [hızlı başlangıç şablonları](availability-group-quickstart-template-configure.md)ve [el ile](availability-group-manually-configure-prerequisites-tutorial.md).

## <a name="considerations"></a>Dikkat edilmesi gerekenler 

Azure IaaS VM konuk yük devretme kümesinde sunucu başına (küme düğümü) tek bir NIC ve tek bir alt ağ kullanılması önerilir. Azure ağ iletişimi, Azure IaaS VM Konuk kümesinde ek NIC 'Lerin ve alt ağların gereksiz olmasını sağlayan fiziksel yedekliliğe sahiptir. Küme doğrulama raporu, düğümlerin yalnızca tek bir ağ üzerinde erişilebilir durumda olduğuna dair bir uyarı gösterse de bu uyarı Azure IaaS VM konuk yük devretme kümelerinde güvenli bir şekilde yoksayılabilir. 

## <a name="next-steps"></a>Sonraki adımlar

[HADR en iyi yöntemlerini](hadr-cluster-best-practices.md) gözden geçirin ve [Azure Portal](availability-group-azure-portal-configure.md), [Azure CLI/PowerShell](./availability-group-az-commandline-configure.md), [hızlı başlangıç şablonları](availability-group-quickstart-template-configure.md) veya [el ile](availability-group-manually-configure-prerequisites-tutorial.md)kullanılabilirlik grubunuzu dağıtmaya başlayın.

Alternatif olarak, bir [kümesiz kullanılabilirlik grubunu](availability-group-clusterless-workgroup-configure.md) veya bir kullanılabilirlik grubunu [birden çok bölgeye](availability-group-manually-configure-multiple-regions.md)dağıtabilirsiniz.
