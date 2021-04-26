---
title: Azure 'da paylaşılan disk kullanarak WSFC üzerinde küme SAP yoks/SCS örneği | Microsoft Docs
description: Küme Paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneğini nasıl kümeleyeceğinizi öğrenin.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c903cf06981e1336ae30942775de11d09bb1299b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675351"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Azure 'da küme paylaşılan diski kullanarak bir Windows Yük devretme kümesinde SAP ASCS/SCS örneği oluşturma

> ![Windows İşletim Sistemi][Logo_Windows] Windows
>

Windows Server Yük Devretme Kümelemesi, Windows 'da yüksek kullanılabilirliğe sahip SAP Ass/SCS yüklemesi ve DBMS 'nin temelidir.

Yük devretme kümesi, uygulamaların ve hizmetlerin kullanılabilirliğini artırmak için birlikte çalışan 1 + n bağımsız sunucu (düğümler) grubudur. Bir düğüm hatası oluşursa, Windows Server Yük Devretme Kümelemesi oluşabilecek hata sayısını hesaplar ve uygulamalar ve hizmetler sağlamak için sağlıklı bir kümeyi sürdürür. Yük Devretme Kümelemesi elde etmek için farklı çekirdek modlarında seçim yapabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu makaledeki görevlere başlamadan önce, aşağıdaki makaleyi gözden geçirin:

* [SAP NetWeaver için Azure sanal makineler yüksek kullanılabilirliğe sahip mimari ve senaryolar][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Azure 'da Windows Server Yük Devretme Kümelemesi

Azure sanal makineleri ile Windows Server Yük Devretme Kümelemesi ek yapılandırma adımları gerektirir. Bir küme oluşturduğunuzda, SAP ASCS/SCS örneği için birkaç IP adresi ve sanal konak adı ayarlamanız gerekir.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Azure 'da ad çözümlemesi ve küme sanal ana bilgisayar adı

Azure bulut platformu, kayan IP adresleri gibi sanal IP adreslerini yapılandırma seçeneği sunmaz. Bulutta küme kaynağına ulaşmak üzere bir sanal IP adresi ayarlamak için alternatif bir çözüme ihtiyacınız vardır. 

Azure Load Balancer hizmeti, Azure için bir *iç yük dengeleyici* sağlar. İç yük dengeleyici ile istemciler küme sanal IP adresi üzerinden kümeye ulaşabilirler. 

İç yük dengeleyiciyi küme düğümlerini içeren kaynak grubuna dağıtın. Ardından, iç yük dengeleyicinin araştırma bağlantı noktalarını kullanarak tüm gerekli bağlantı noktası iletme kurallarını yapılandırın. İstemciler sanal ana bilgisayar adı aracılığıyla bağlanabilir. DNS sunucusu, küme IP adresini çözümler ve iç yük dengeleyici, kümenin etkin düğümüne bağlantı noktası iletmeyi işler.

> [!IMPORTANT]
> Kayan IP, Yük Dengeleme senaryolarında NIC ikincil IP yapılandırmasında desteklenmez. Ayrıntılar için bkz. [Azure yük dengeleyici sınırlamaları](../../../load-balancer/load-balancer-multivip-overview.md#limitations). VM için ek IP adresine ihtiyacınız varsa ikinci bir NIC dağıtın.  

![Şekil 1: Azure 'da paylaşılan disk olmadan Windows Yük Devretme Kümelemesi yapılandırması][sap-ha-guide-figure-1001]

_Azure 'da paylaşılan disk olmadan Windows Server Yük Devretme Kümelemesi yapılandırması_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>Küme paylaşılan diskleriyle SAP yoks/SCS HA
Windows 'da SAP ASCS/SCS örneği SAP Merkezi Hizmetleri, SAP ileti sunucusu, sıraya alma sunucusu süreçler ve SAP Küresel Ana bilgisayar dosyalarını içerir. SAP Küresel Ana bilgisayar dosyaları tüm SAP sistemi için merkezi dosyaları depolar.

SAP ASCS/SCS örneği aşağıdaki bileşenlere sahiptir:

* SAP Merkezi Hizmetleri:
    * İki işlem, bir ileti ve sıraya alma sunucusu ve \<ASCS/SCS virtual host name> Bu iki işleme erişmek için kullanılan bir.
    * Dosya yapısı: S:\usr\sap \\ &lt; SID &gt; \ Ass/SCS\<instance number\>


* SAP Küresel Ana bilgisayar dosyaları:
  * Dosya yapısı: S:\usr\sap \\ &lt; SID &gt; \sys \. ..
  * Aşağıdaki UNC yolunu kullanarak bu genel S:\usr\sap \\ &lt; SID &gt; \sys \. .. Files öğesine erişim sağlayan sapmnt dosya paylaşma:

    \\\\<ASCS/SCS sanal ana bilgisayar adı \> \ sapmnt \\ &lt; SID &gt; \ sys \. ..


![Şekil 2: bir SAP ASCS/SCS örneği için süreçler, dosya yapısı ve küresel ana bilgisayar sapmnt dosya paylaşma][sap-ha-guide-figure-8001]

_Bir SAP ASCS/SCS örneği için süreçler, dosya yapısı ve küresel ana bilgisayar sapmnt dosya paylaşma_

Yüksek kullanılabilirliğe sahip bir ayarda SAP ASCS/SCS örneklerini kümelerinolursunuz. SAP ASCS/SCS ve SAP Küresel Ana bilgisayar dosyalarını yerleştirmek için *Kümelenmiş Paylaşılan diskleri* (örneğimizde sürücü S) kullanıyoruz.

![Şekil 3: paylaşılan disk ile SAP ASCS/SCS HA mimarisi][sap-ha-guide-figure-8002]

_Paylaşılan disk ile SAP ASCS/SCS HA mimarisi_


Sıraya alma sunucusu çoğaltma 1 mimarisi ile:
* Aynı, \<ASCS/SCS virtual host name> SAP iletisi ve sıraya alma sunucu işlemlerine ve SAP Küresel Ana bilgisayar dosyalarına sapmnt dosya paylaşımıyla erişmek için de kullanılır.
* Aynı küme paylaşılan disk sürücüsü öğeleri arasında paylaşılır.  

Sıraya alma sunucusu çoğaltma 2 mimarisi: 
* Aynı \<ASCS/SCS virtual host name> şekilde, SAP ileti sunucusu işlemine ve SAP Küresel Ana bilgisayar dosyalarına sapmnt dosya paylaşımıyla erişmek için de kullanılır.
* Aynı küme paylaşılan disk sürücüsü öğeleri arasında paylaşılır.
* \<ERS virtual host name>Sıraya alma sunucusu işlemine erişmek için ayrı  

![Şekil 4: paylaşılan disk ile SAP yoks/SCS HA mimarisi][sap-ha-guide-figure-8003]

_Paylaşılan disk ile SAP ASCS/SCS HA mimarisi_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Paylaşılan disk ve sıraya alma çoğaltma sunucusu 

1. Paylaşılan disk, sıraya alma çoğaltma sunucusu (ERS) örneği olan sıraya alma sunucusu çoğaltma 1 mimarisiyle desteklenir.   

   - kümelenmemiş
   - kullanılan `localhost` ad
   - , küme düğümlerinin her birinde yerel disklerde dağıtılır

2. Paylaşılan disk Ayrıca, sıraya alma çoğaltma sunucusu 2 (ERS2) örneği olan sıraya alma sunucusu çoğaltma 2 mimarisi ile desteklenir:  

   - kümelenmiş
   - adanmış sanal/ağ ana bilgisayar adını kullanır
   - , (A) SCS IP adresine ek olarak Azure Iç Load Balancer üzerinde yapılandırılacak sanal ana bilgisayar adının IP adresine ihtiyacı vardır
   - kümelenmiş düğümlerin her birinde **yerel disklerde** dağıtılır, bu nedenle paylaşılan disk gerekmez

   > [!TIP]
   > Sıraya alma çoğaltma sunucusu 1 ve 2 (ERS1 ve ERS2) hakkında daha fazla bilgiye buradan ulaşabilirsiniz:  
   > [Microsoft yük devretme kümesinde sıraya alma çoğaltma sunucusu](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Yük devretme kümesi ortamlarında yeni sıraya alma çoğaltıcı](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>SAP iş yükleri için Azure 'da paylaşılan disk seçenekleri

Azure 'da bir Windows Yük devretme kümesinde paylaşılan disk için iki seçenek vardır:

- Azure [paylaşılan diskler](../../disks-shared.md) -özelliği, aynı anda birden çok VM 'ye Azure yönetilen disk iliştirmeye olanak tanır. 
- Küme paylaşılan depolama benzetimi yapan yansıtılmış bir depolama alanı oluşturmak için 3. taraf yazılım [SIOS Dataman küme sürümünü](https://us.sios.com/products/datakeeper-cluster) kullanma. 

Paylaşılan disk için teknolojiyi seçerken aşağıdaki noktalara dikkat edin:

**SAP iş yükleri için Azure Paylaşılan disk**
- Azure yönetilen diskini, bakımını yapmak ve çalıştırmak için ek yazılım gerekmeden aynı anda birden çok VM 'ye eklemenize olanak tanır 
- Tek bir depolama kümesinde tek bir Azure Paylaşılan diskle birlikte çalışır. Bu, SAP çözümünüzün güvenilirliğini etkiler.
- Şu anda desteklenen tek dağıtım, kullanılabilirlik kümesindeki Azure Paylaşılan Premium diskdir. Azure Paylaşılan disk, bölgesel dağıtımında desteklenmiyor.     
- Aynı anda gerekli sayıda VM 'ye iliştirilebilmek için [Premium SSD aralıklarında](../../disks-shared.md#disk-sizes) belirtilen minimum disk boyutuyla Azure Premium disk 'i sağlamayı unutmayın (genellikle sap ascs Windows Yük devretme kümesi için 2). 
- Azure Paylaşılan Ultra disk, kullanılabilirlik kümesi veya bölgesel dağıtımında dağıtımı desteklemediğinden SAP iş yükleri için desteklenmez.  
 
**HARFI**
- SIOS çözümü iki disk arasında gerçek zamanlı zaman uyumlu veri çoğaltması sağlar
- İki yönetilen diskle çalışan SIOS çözümü ile, kullanılabilirlik kümelerini veya kullanılabilirlik bölgelerini kullanıyorsanız, yönetilen diskler farklı depolama kümelerine göre çalışır. 
- Kullanılabilirlik bölgelerinde dağıtım destekleniyor
- Ek olarak satın almanız gereken üçüncü taraf yazılım yükleme ve işletim sistemlerini gerektirir

### <a name="shared-disk-using-azure-shared-disk"></a>Azure Paylaşılan disk kullanarak paylaşılan disk

Microsoft, paylaşılan bir disk seçeneği ile SAP Ass/SCS yüksek kullanılabilirliğini uygulamak için kullanılabilen [Azure Paylaşılan diskleri](../../disks-shared.md)sunmaktadır.

#### <a name="prerequisites-and-limitations"></a>Önkoşullar ve sınırlamalar

Şu anda Azure Premium SSD disklerini SAP ASCS/SCS örneği için Azure Paylaşılan diski olarak kullanabilirsiniz. Şu kısıtlamalar Şu anda yerinde:

-  [Azure Ultra disk](../../disks-types.md#ultra-disk) , SAP iş yükleri Için Azure Paylaşılan diski olarak desteklenmez. Şu anda Azure sanal makinelerini, kullanılabilirlik kümesi 'nde Azure Ultra disk kullanarak yerleştirmek mümkün değildir
-  Premium SSD disklere sahip [Azure Paylaşılan disk](../../disks-shared.md) yalnızca kullanılabilirlik kümesindeki VM 'ler ile desteklenir. Kullanılabilirlik Alanları dağıtımında desteklenmez. 
-  Azure Paylaşılan disk değeri [MAXSHARES](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) , kaç küme düğümünün paylaşılan diski kullanabileceğinizi belirler. Genellikle SAP ASCS/SCS örneği için Windows Yük devretme kümesinde iki düğüm yapılandıracaksınız, bu nedenle değerinin `maxShares` iki olarak ayarlanması gerekir.
-  Tüm SAP yoks/SCS kümesi VM 'lerinin aynı [Azure yakınlık yerleşimi grubuna](../../windows/proximity-placement-groups.md)dağıtılması gerekir.   
   PPG olmadan Azure Paylaşılan disk ile kullanılabilirlik kümesi 'nde Windows kümesi VM 'Leri dağıtabilirsiniz, ancak sanal makineler ve depolama katmanı arasında daha düşük gecikme süresi elde etmek için PPG, Azure Paylaşılan disklerinin ve küme VM 'lerinin fiziksel yakınlığını kapatacaktır.    

Azure Paylaşılan diskine ilişkin sınırlamalar hakkında daha fazla ayrıntı için lütfen Azure Paylaşılan disk belgelerinin [kısıtlama](../../disks-shared.md#limitations) bölümünü dikkatle inceleyin.

> [!IMPORTANT]
> SAP ASCS/SCS Windows Yük devretme kümesini Azure Paylaşılan disk ile dağıttığınızda, dağıtımınızın tek bir depolama kümesinde tek bir paylaşılan diskle birlikte çalışır durumda olacağını unutmayın. Azure Paylaşılan diskinin dağıtıldığı depolama kümesiyle ilgili sorunlar durumunda SAP ASCS/SCS örneğiniz etkilenebilir.    

> [!TIP]
> SAP dağıtımınızı planlarken önemli noktalar için [Azure Planlama Kılavuzu 'Nda SAP NetWeaver](./planning-guide.md) ve [SAP Iş yükleri için Azure depolama Kılavuzu](./planning-guide-storage.md) ' nu gözden geçirin.

### <a name="supported-os-versions"></a>Desteklenen işletim sistemi sürümleri

Hem Windows Server 2016 hem de 2019 desteklenir (en son veri merkezi görüntülerini kullanın).

**Windows Server 2019 Datacenter** kullanmanızı kesinlikle öneririz; örneğin:
- Windows 2019 yük devretme kümesi hizmeti Azure ile uyumlu
- Azure Schedule olaylarını izleyerek Azure ana bilgisayar bakımı ve geliştirilmiş deneyimle ilgili tümleştirme ve tanıma özellikleri eklendi.
- Dağıtılmış ağ adı (varsayılan seçenektir) kullanılabilir. Bu nedenle, küme ağ adı için ayrılmış bir IP adresine sahip olmanız gerekmez. Ayrıca, Azure Iç Load Balancer bu IP adresini yapılandırmaya gerek yoktur. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Azure 'da SIOS Dataman ile paylaşılan diskler

Paylaşılan disk için başka bir seçenek de, küme paylaşılan depolama benzetimi yapan yansıtılmış bir depolama alanı oluşturmak için üçüncü taraf yazılım SIOS Dataman küme sürümünü kullanmaktır. SIOS çözümü gerçek zamanlı zaman uyumlu veri çoğaltması sağlar.

Bir küme için paylaşılan disk kaynağı oluşturmak için:

1. Bir Windows küme yapılandırmasındaki her bir sanal makineye ek bir disk ekleyin.
2. Her iki sanal makine düğümünde de SIOS Dataman küme sürümünü çalıştırın.
3. Disk bağlantılı ek birimin içeriğini kaynak sanal makineden hedef sanal makinenin ek disk ekli birimine yansıtan şekilde, SIOS Dataman küme sürümünü yapılandırın. SIOS Dataman, kaynak ve hedef yerel birimleri soyutlar ve sonra bunları bir paylaşılan disk olarak Windows Server Yük Devretme Kümelemesi 'ne gösterir.

[SIOS Verilerman](https://us.sios.com/products/datakeeper-cluster/)hakkında daha fazla bilgi alın.

![Şekil 5: Azure 'da SIOS Dataman ile Windows Server Yük Devretme Kümelemesi yapılandırması][sap-ha-guide-figure-1002]

_Azure 'da SIOS Dataman ile Windows Yük Devretme Kümelemesi yapılandırması_

> [!NOTE]
> SQL Server gibi bazı DBMS ürünleriyle yüksek kullanılabilirlik için paylaşılan disklere ihtiyacınız yoktur. SQL Server AlwaysOn, DBMS verilerini ve günlük dosyalarını bir küme düğümünün yerel diskinden başka bir küme düğümünün yerel diskine çoğaltır. Bu durumda, Windows küme yapılandırmasına paylaşılan bir disk gerekmez.
>

## <a name="next-steps"></a>Sonraki adımlar

* [SAP Ass/SCS örneği için Windows Yük devretme kümesi ve paylaşılan disk kullanarak SAP HA için Azure altyapısını hazırlama][sap-high-availability-infrastructure-wsfc-shared-disk]

* [SAP NetWeaver HA 'yi bir Windows Yük devretme kümesine ve bir SAP ASCS/SCS örneği için paylaşılan diske yükler][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

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


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md