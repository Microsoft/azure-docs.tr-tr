---
title: SAP iş yükü için Azure sanal makineler DBMS dağıtımına yönelik konular | Microsoft Docs
description: SAP iş yükü için Azure sanal makineler DBMS dağıtımına yönelik konular
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101356"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP iş yükü için Azure sanal makineler DBMS dağıtımına yönelik konular
[1114181]: https://launchpad.support.sap.com/#/notes/1114181
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]: https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2171857]: https://launchpad.support.sap.com/#/notes/2171857
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Bu kılavuz, Microsoft Azure SAP yazılımının nasıl uygulanacağı ve dağıtılacağı hakkındaki belgelerin bir parçasıdır. Bu kılavuzu okumadan önce [planlama ve uygulama kılavuzunu][planning-guide]okuyun. Bu belge, Azure hizmet olarak altyapı (IaaS) yeteneklerini kullanarak Microsoft Azure sanal makinelerde (VM) SAP ile ilgili DBMS sistemlerinin genel dağıtım yönlerini ele alır.

Kağıda, belirtilen platformlarda SAP yazılımının yüklemeleri ve dağıtımları için birincil kaynakları temsil eden SAP yükleme belgeleri ve SAP notları da bu şekilde tamamlar.

Bu belgede, SAP ile ilgili DBMS sistemlerini Azure VM 'lerinde çalıştırmanın önemli noktaları tanıtılmaktadır. Bu bölümde belirli DBMS sistemlerine birkaç başvuru vardır. Bunun yerine, belirli DBMS sistemleri bu belge içinde, bu belgeden sonra işlenir.

## <a name="definitions"></a>Tanımlar
Belge boyunca, bu terimler kullanılır:

* **IaaS**: Hizmet olarak altyapı.
* **PaaS**: Hizmet olarak platform.
* **SaaS**: Hizmet olarak yazılım.
* **Sap bileşeni**: ERP merkezi bileşeni (ECC), Iş ambarı (beyaz), çözüm Yöneticisi veya Enterprise Portal (EP) gibi bireysel bir SAP uygulaması. SAP bileşenleri geleneksel ABAP veya Java teknolojilerine veya Iş nesneleri gibi NetWeaver tabanlı olmayan bir uygulamaya dayalı olabilir.
* **SAP ortamı**: Geliştirme, kalite güvencesi, eğitim, olağanüstü durum kurtarma veya üretim gibi bir iş işlevi gerçekleştirmek için mantıksal olarak gruplanmış bir veya daha fazla SAP bileşeni.
* **SAP yatay**: Bu terim, bir müşterinin BT yatay içindeki tüm SAP varlıklarını ifade eder. SAP yatay, tüm üretim ve üretim dışı ortamları içerir.
* **SAP sistemi**: Bir DBMS katmanının ve uygulama katmanının birleşimi; Örneğin, SAP ERP geliştirme sistemi, SAP Business Warehouse test sistemi veya SAP CRM üretim sistemi. Azure dağıtımlarında, bu iki katmanı şirket içi ve Azure arasında bölmek desteklenmez. Sonuç olarak, bir SAP sistemi şirket içinde dağıtılır ya da Azure 'da dağıtılır. SAP 'nin farklı sistemlerini Azure 'da veya şirket içinde dağıtabilirsiniz. Örneğin, Azure 'da SAP CRM geliştirme ve test sistemlerini dağıtabilir, ancak şirket içi SAP CRM üretim sistemini dağıtabilirsiniz.
* **Şirketler arası**: VM 'Lerin şirket içi veri merkezleri ile Azure arasında siteden siteye, çok siteli veya Azure ExpressRoute bağlantısı olan bir Azure aboneliğine dağıtıldığı bir senaryoyu açıklar. Yaygın Azure belgelerinde, bu tür dağıtımlar şirketler arası senaryolar olarak da açıklanmaktadır. 

    Bağlantının nedeni şirket içi etki alanlarını, şirket içi Active Directory ve şirket içi DNS 'yi Azure 'a genişletmenin nedenidir. Şirket içi yatay, aboneliğin Azure varlıklarına genişletilir. Bu uzantıyla birlikte VM 'Ler, şirket içi etki alanının bir parçası olabilir. Şirket içi etki alanının etki alanı kullanıcıları sunuculara erişebilir ve bu VM 'lerde DBMS hizmetleri gibi hizmetleri çalıştırabilir. Şirket içinde dağıtılan VM 'Ler ve Azure 'da dağıtılan VM 'ler arasındaki iletişim ve ad çözümlemesi mümkündür. Bu senaryo, Azure 'da SAP varlıklarını dağıtmak için kullanılan en yaygın senaryodur. Daha fazla bilgi için bkz. [VPN Gateway Için planlama ve tasarım](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> SAP sistemlerinin şirket içi dağıtımları, SAP sistemlerini çalıştıran Azure sanal makinelerinin şirket içi bir etki alanının üyesi olduğu ve üretim SAP sistemlerinde desteklendiği yerdir. Şirketler arası yapılandırma, parçaları dağıtmak veya Azure 'a tam SAP landscapes için desteklenir. Azure 'da tam SAP yatay ' i çalıştırmak, bu VM 'Lerin şirket içi etki alanı ve Active Directory/LDAP 'nin bir parçası olmasını gerektirir. 
>
> Belgelerin önceki sürümlerinde, karma BT senaryoları bahsedildi. *Karma* terim terimi, şirket Içi ve Azure arasında şirketler arası bağlantı olduğunu aslında kapsar. Bu durumda, karma Ayrıca Azure 'daki VM 'Lerin şirket içi Active Directory bir parçası olduğu anlamına gelir.
>
>

Bazı Microsoft belgelerinde, özellikle de DBMS yüksek kullanılabilirlik yapılandırmalarında, şirket içi senaryolar biraz farklı şekilde açıklanmıştır. SAP ile ilgili belgeler söz konusu olduğunda, şirketler arası senaryo siteden siteye veya özel [ExpressRoute](https://azure.microsoft.com/services/expressroute/) bağlantısına ve şirket Içi ile Azure arasında DAĞıTıLAN bir SAP yataya kadar

## <a name="resources"></a>Kaynaklar
Azure 'da SAP iş yükünde başka makaleler de mevcuttur. Azure 'da [SAP iş yüküne başlayın: Kullanmaya](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) başlayın ve ardından ilgilendiğiniz alanı seçin.

Aşağıdaki SAP notları, bu belgede ele alınan alanla ilgili olarak Azure 'daki SAP ile ilgilidir.

| Dekont numarası | Başlık |
| --- | --- |
| [1928533] |Azure 'da SAP uygulamaları: Desteklenen Ürünler ve Azure VM türleri |
| [2015553] |Microsoft Azure SAP: Destek önkoşulları |
| [1999351] |SAP için gelişmiş Azure izleme sorunlarını giderme |
| [2178632] |Microsoft Azure üzerinde SAP için anahtar izleme ölçümleri |
| [1409604] |Windows 'da sanallaştırma: Gelişmiş izleme |
| [2191498] |Azure ile Linux üzerinde SAP: Gelişmiş izleme |
| [2039619] |Oracle veritabanını kullanarak Microsoft Azure SAP uygulamaları: Desteklenen Ürünler ve sürümler |
| [2233094] |DB6: Linux, UNIX ve Windows için IBM DB2 kullanarak Azure 'da SAP uygulamaları: Ek bilgiler |
| [2243692] |Microsoft Azure Linux (IaaS) sanal makinesi: SAP lisans sorunları |
| [1984787] |SUSE LINUX Enterprise Server 12: Yükleme notları |
| [2002167] |Red Hat Enterprise Linux 7. x: Yükleme ve yükseltme |
| [2069760] |Oracle Linux 7. x SAP yüklemesi ve yükseltmesi |
| [1597355] |Linux için takas boşluğu önerisi |
| [2171857] |Oracle Database 12c: Linux 'ta dosya sistemi desteği |
| [1114181] |Oracle Database 11g: Linux 'ta dosya sistemi desteği |


Linux için tüm SAP notları hakkında daha fazla bilgi için bkz. [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Microsoft Azure mimarisine ve Microsoft Azure sanal makinelerin nasıl dağıtıldığını ve çalıştığınıza yönelik bir bilgiye sahip olmanız gerekir. Daha fazla bilgi için bkz. [Azure belgeleri](https://docs.microsoft.com/azure/).

Genel olarak, Windows, Linux ve DBMS yükleme ve yapılandırma temelde şirket içinde yüklediğiniz tüm sanal makineler veya çıplak makineler ile aynıdır. Azure IaaS kullandığınızda farklılık gösteren bazı mimari ve sistem yönetimi uygulama kararları vardır. Bu belgede, Azure IaaS kullandığınızda için hazırlanmaya yönelik belirli mimari ve sistem yönetimi farklılıkları açıklanmaktadır.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS dağıtımları için bir sanal makinenin depolama yapısı
Bu bölümü izlemek için [Dağıtım Kılavuzu][deployment-guide]'nun [Bu][deployment-guide-3] bölümünde sunulan bilgileri okuyun ve anlayın. Bu bölümü kullanmadan önce farklı VM Serisi ve standart ve Premium depolama arasındaki farkları anlamanız ve bilmeniz gerekir. 

Azure VM 'Leri için Azure depolama hakkında bilgi edinmek için bkz.:

- [Azure Windows VM 'leri için yönetilen disklere giriş](../../windows/managed-disks-overview.md).
- [Azure Linux VM 'leri için yönetilen disklere giriş](../../linux/managed-disks-overview.md).

Temel yapılandırmada genellikle işletim sisteminin, DBMS 'nin ve en son SAP ikililerinin veritabanı dosyalarından ayrı olduğu bir dağıtım yapısı önerilir. Azure sanal makineler 'de çalışan SAP sistemlerinin temel VHD veya disk, işletim sistemi, veritabanı yönetim sistemi yürütülebilir dosyaları ve SAP yürütülebilir dosyaları ile yüklenmiş olması önerilir. 

DBMS verileri ve günlük dosyaları standart depolama veya Premium depolama alanında depolanır. Bunlar ayrı disklerde depolanır ve özgün Azure işletim sistemi görüntüsü VM 'sine mantıksal diskler olarak eklenir. Linux dağıtımları için, özellikle SAP HANA için farklı öneriler belgelenmiştir.

Disk düzeninizi planlarken, şu öğeler arasındaki en iyi dengeyi bulun:

* Veri dosyası sayısı.
* Dosyaları içeren disk sayısı.
* Tek bir diskin ıOPS kotaları.
* Disk başına veri aktarım hızı.
* VM boyutu başına olası ek veri disklerinin sayısı.
* Bir VM 'nin sağlayabileceğiniz genel depolama verimi.
* Farklı Azure Depolama türlerinin sağladığı gecikme süresi.
* VM SLA 'Ları

Azure, veri diski başına bir ıOPS kotası uygular. Bu kotalar, standart depolama ve Premium depolamada barındırılan diskler için farklıdır. G/ç gecikmesi, iki depolama türü arasında da farklıdır. Premium Depolama, daha iyi g/ç gecikme süresi sağlar. 

Farklı sanal makine türlerinin her biri, iliştirebilmeniz gereken sınırlı sayıda veri diskine sahiptir. Farklı bir kısıtlama, yalnızca belirli VM türlerinin Premium depolamayı kullanabilledir. Genellikle, CPU ve bellek gereksinimlerine göre belirli bir sanal makine türünü kullanmaya karar verirsiniz. Ayrıca, genellikle disk sayısıyla veya Premium Depolama disklerinin türüne göre ölçeklenen ıOPS, gecikme süresi ve disk aktarım hızı gereksinimlerini de göz önünde bulundurmanız gerekebilir. IOPS sayısı ve her disk tarafından elde edilecek aktarım hızı, özellikle Premium Depolama ile disk boyutunu dikte edebilir.

> [!NOTE]
> DBMS dağıtımları için tüm veriler, işlem günlüğü veya yineleme dosyaları için Premium depolama kullanımını öneririz. Üretim veya üretim dışı sistemler dağıtmak isteyip istemediğiniz önemli değildir.

> [!NOTE]
> Azure 'un benzersiz [tek VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)'sından yararlanmak için, bağlı olan tüm DISKLERIN temel VHD 'yi içeren Premium depolama türü olması gerekir.

> [!NOTE]
> Azure veri merkezlerine bitişik olan ve birlikte bulunan üçüncü taraf veri merkezlerinde bulunan depolama donanımlarında bulunan SAP veritabanlarının veri ve günlük dosyaları gibi ana veritabanı dosyalarını barındırma desteklenmez. SAP iş yükleri için, SAP veritabanlarının veri ve işlem günlük dosyaları için yalnızca yerel Azure hizmeti olarak temsil edilen depolama alanı desteklenir.

Veritabanı dosyalarının yerleştirilmesi ve günlük ve yineleme dosyaları ve kullandığınız Azure depolama türü ıOPS, gecikme süresi ve verimlilik gereksinimleri tarafından tanımlanır. Yeterli ıOPS olması için birden çok disk kullanma veya daha büyük bir Premium Depolama diski kullanma zorlanabilir. Birden çok disk kullanıyorsanız, veri dosyalarını veya günlük ve kayıt dosyalarını içeren diskler arasında bir yazılım şeridi oluşturun. Bu gibi durumlarda, temel alınan Premium Depolama disklerinin ıOPS ve disk aktarım hızı ve standart depolama disklerinin maksimum ulaşılabilir ıOPS değeri, sonuçta elde edilen Stripe kümesi için birikicidir.

Daha önce belirtildiği gibi, ıOPS gereksiniminizi tek bir VHD 'nin sağlayabileceği aşarsa, veritabanı dosyaları için gereken ıOPS sayısını bir dizi VHD 'lerde dengeleyin. IOPS yükünü diskler arasında dağıtmanın en kolay yolu, farklı diskler üzerinde bir yazılım şeridi oluşturmak için kullanılır. Ardından, yazılım Stripe dışına çıkan LUN 'Lara SAP DBMS 'nin bir dizi veri dosyasını yerleştirebilirsiniz. Stripe içindeki disk sayısı, IOPS taleplerine, disk işleme taleplerine ve birim taleplerine göre çalıştırılır.


---
> ![Windows][Logo_Windows] Windows
>
> Birden çok Azure VHD üzerinde dizili kümeler oluşturmak için Windows depolama alanları kullanmanızı öneririz. En az Windows Server 2012 R2 veya Windows Server 2016 kullanın.
>
> ![Linux][Logo_Linux] Linux
>
> Linux 'ta yazılım RAID oluşturmak için yalnızca MDADDM ve mantıksal birim Yöneticisi (LVM) desteklenir. Daha fazla bilgi için bkz.
>
> - MDADDM kullanarak [Linux 'ta yazılım RAID yapılandırma](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
> - LVM ['yi kullanarak Azure 'da bir LINUX VM 'de LVM 'Yi yapılandırma](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
>
>

---

> [!NOTE]
> Azure depolama, VHD 'lerin üç görüntüsünü sağladığından, dizili bir artıklık yapılandırmak mantıklı değildir. Yalnızca, t/ç 'nin farklı VHD 'lere dağıtılması için şeridi yapılandırmanız gerekir.
>

### <a name="managed-or-nonmanaged-disks"></a>Yönetilen veya yönetilmeyen diskler
Bir Azure depolama hesabı, yönetim yapısıdır ve ayrıca Kısıtlamaların bir konusudur. Sınırlamalar, standart depolama hesapları ve Premium Depolama hesapları arasında farklılık gösterir. Yetenekler ve sınırlamalar hakkında daha fazla bilgi için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Standart depolama için, depolama hesabı başına ıOPS 'de bir sınır olduğunu unutmayın. [Azure depolama ölçeklenebilirlik ve performans hedefleri](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)makalesindeki **toplam istek oranı** 'nı içeren satıra bakın. Ayrıca, Azure aboneliği başına depolama hesabı sayısı için bir başlangıç sınırı vardır. Bu depolama hesaplarının limitlerine ulaşmaktan kaçınmak için, farklı depolama hesaplarında daha büyük SAP için VHD 'leri dengeleyin. Bu, binlerce VHD 'den fazlasına sahip birkaç yüz sanal makine hakkında konuşuyorsanız sıkıcı bir çalışmadır.

Bir SAP iş yükü ile birlikte DBMS dağıtımları için standart depolama kullanılması önerilmediği için, standart depolamaya yapılan başvurular ve öneriler bu kısa [makaleyle](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx) sınırlıdır

Microsoft 'un farklı Azure depolama hesaplarında VHD planlama ve dağıtma konusunda yönetim çalışmasından kaçınmak için, Microsoft tarafından 2017 ' de [Azure yönetilen diskler](https://azure.microsoft.com/services/managed-disks/) kullanıma sunulmuştur. Yönetilen diskler standart depolama ve Premium Depolama için kullanılabilir. Yönetilen disklerin yönetilmeyen disklere kıyasla başlıca avantajları şunlardır:

- Yönetilen diskler için Azure, farklı depolama hesaplarında farklı VHD 'leri dağıtım zamanında otomatik olarak dağıtır. Bu şekilde, veri hacmi, g/ç verimlilik ve ıOPS için depolama hesabı limitleri isabet edilmez.
- Azure depolama, yönetilen diskleri kullanarak Azure kullanılabilirlik kümelerinin kavramlarını geliştirir. VM bir Azure kullanılabilirlik kümesinin parçasıysa, temel VHD ve bir sanal makinenin bağlı diski farklı hata ve güncelleştirme etki alanlarına dağıtılır.


> [!IMPORTANT]
> Azure yönetilen disklerinin avantajları söz konusu olduğunda, DBMS dağıtımları ve SAP dağıtımlarınız için Azure yönetilen disklerini genel olarak kullanmanızı öneririz.
>

Yönetilmeyen bilgisayardan yönetilen disklere dönüştürmek için bkz.:

- [Bir Windows sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Bir Linux sanal makinesini yönetilmeyen disklerden yönetilen disklere dönüştürme](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 'Ler ve veri diskleri için önbelleğe alma
Diskleri VM 'lere bağladığınızda, sanal makine arasındaki g/ç trafiğinin Azure Storage 'da bulunan diskler arasında önbelleğe alınıp alınmayacağını seçebilirsiniz. Standart ve Premium Depolama, bu tür bir önbellek için iki farklı teknoloji kullanır.

Aşağıdaki öneriler, bu g/ç özelliklerinin standart DBMS için olduğunu varsayar:

- Genellikle bir veritabanının veri dosyalarına karşı bir okuma iş yüküdür. Bu okumalar, DBMS sistemi için performans açısından kritik öneme sahiptir.
- Veri dosyalarına yazma, denetim noktalarına veya sabit bir akışa göre artışlarıyla 'de gerçekleşir. Günde ortalama, okumadan daha az yazma vardır. Veri dosyalarından okumaların tersi, bu yazma işlemleri zaman uyumsuzdur ve herhangi bir Kullanıcı işlemi tutmayın.
- İşlem günlüğünden veya yineleme dosyalarından her türlü okuma işlemi vardır. İşlem günlüğü yedeklemeleri gerçekleştirirken özel durumlar büyük g/ç.
- İşlem veya yineleme günlük dosyalarına karşı ana yük yazılır. İş yükünün yapısına bağlı olarak, 1 MB veya daha fazla g/ç boyutu için g/ç küçük ya da 4 KB veya daha fazla g/ç boyutu olabilir.
- Tüm yazmamaların diskte güvenilir bir biçimde kalıcı olması gerekir.

Standart depolama için olası önbellek türleri şunlardır:

* Yok.
* Okuma
* Okuma/Yazma

Tutarlı ve belirleyici performans sağlamak için, DBMS ile ilgili veri dosyalarını içeren tüm diskler için standart depolamada önbelleğe alma, dosyaları günlüğe kaydetme ve yineleme ve tablo alanı **yok**olarak ayarlayın. Temel VHD 'nin önbelleğe alınması varsayılan olarak kalabilir.

Premium Depolama için aşağıdaki önbelleğe alma seçenekleri mevcuttur:

* Yok.
* Okuma
* Okuma/yazma
* Hiçbiri + yalnızca Azure a serisi VM 'Ler için olan Yazma Hızlandırıcısı
* Yalnızca Azure d serisi VM 'Ler için olan Yazma Hızlandırıcısı +.

Premium Depolama için SAP veritabanının **veri dosyaları Için okuma önbelleği** kullanmanızı ve **günlük dosyalarının diskleri için önbelleğe alma**seçeneğini seçmenizi öneririz.

D serisi dağıtımlar için, DBMS dağıtımınız için Azure Yazma Hızlandırıcısı kullanmanızı öneririz. Ayrıntılar, kısıtlamalar ve Azure Yazma Hızlandırıcısı dağıtımı için bkz. [Enable yazma Hızlandırıcısı](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Azure kalıcı olmayan diskler
VM dağıtıldıktan sonra Azure VM 'Ler kalıcı olmayan diskler sunar. VM önyüklemesi durumunda, bu sürücülerdeki tüm içerikler silinir. Bu, veri dosyaları ve günlüğe kaydetme ve veritabanlarının yeniden yineleme gibi kalıcı olmayan sürücülerde hiçbir koşul olmaması durumunda verilmiştir. Kalıcı olmayan sürücülerin tempdb ve temp Tablespaces için uygun olabileceği bazı veritabanları için özel durumlar olabilir. Kalıcı olmayan diskler söz konusu VM ailesiyle üretilen iş için sınırlı olduğundan, bu sürücüleri A serisi VM 'Ler için kullanmaktan kaçının. 

Daha fazla bilgi için bkz. [Azure 'Da Windows VM 'lerinde geçici sürücüyü anlama](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> Azure VM 'deki D sürücüsü, Azure işlem düğümündeki bazı yerel diskler tarafından desteklenen kalıcı olmayan bir sürücüdür. Kalıcı olmadığından, D sürücüsündeki içerikte yapılan değişiklikler VM yeniden başlatıldığında kaybedilir. Değişiklikler, depolanan dosyaları, oluşturulan dizinleri ve yüklenen uygulamaları içerir.
>
> ![Linux][Logo_Linux] Linux
>
> Linux Azure VM 'leri, Azure işlem düğümündeki yerel disklerle desteklenen kalıcı olmayan bir sürücü olan/mnt/Resource yolunda bir sürücüyü otomatik olarak bağlayabilir. Kalıcı olmadığından,/mnt/Resource dosyasındaki içerikte yapılan tüm değişiklikler, VM yeniden başlatıldığında kaybedilir. Değişiklikler, depolanan dosyaları, oluşturulan dizinleri ve yüklenen uygulamaları içerir.
>
>

---



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Depolama dayanıklılık
Microsoft Azure Depolama, temel VHD 'yi, işletim sistemi ve bağlı diskler veya bloblarla en az üç ayrı depolama düğümüne depolar. Bu tür bir depolama alanı yerel olarak yedekli depolama (LRS) olarak adlandırılır. LRS, Azure 'daki tüm depolama türleri için varsayılandır.

Başka artıklık yöntemleri vardır. Daha fazla bilgi için bkz. [Azure Storage çoğaltma](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Premium Depolama, DBMS VM 'Leri ve veritabanı ve günlük ve yineleme dosyalarını depolayan diskler için önerilen depolama türüdür. Premium Depolama için kullanılabilen tek yedekleme yöntemi LRS 'dir. Sonuç olarak, veritabanı veri çoğaltmasını başka bir Azure bölgesine veya kullanılabilirlik bölgesine etkinleştirmek için veritabanı yöntemlerini yapılandırmanız gerekir. Veritabanı yöntemleri SQL Server Always on, Oracle Data Guard ve HANA sistem çoğaltmasını içerir.


> [!NOTE]
> DBMS dağıtımları için, coğrafi olarak yedekli depolama (GRS) kullanımı standart depolama için önerilmez. GRS, performansı ciddi şekilde etkiler ve bir VM 'ye bağlı farklı VHD 'lerde yazma sırasını dikkate almaz. Farklı VHD 'lerde yazma sırasının, çoğaltma hedefi tarafında tutarsız veritabanlarına yol açmış olma olasılığı yoktur. Bu durum, veritabanı ve günlük ve yeniden yineleme dosyalarının, genellikle kaynak VM tarafında olduğu gibi birden çok VHD 'ye yayıldığı durumlarda meydana gelir.



## <a name="vm-node-resiliency"></a>VM düğümü dayanıklılığı
Azure, VM 'Ler için çeşitli farklı SLA 'Lar sunar. Daha fazla bilgi için bkz. [sanal makineler Için SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)'nın en son sürümü. DBMS katmanının genellikle SAP sisteminde kullanılabilirliğine önem sağladığından, kullanılabilirlik kümelerini, kullanılabilirlik bölgelerini ve bakım olaylarını anlamanız gerekir. Bu kavramlar hakkında daha fazla bilgi için bkz. Azure ['Da Windows sanal makinelerinin kullanılabilirliğini yönetme](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) ve [Linux sanal makinelerinin kullanılabilirliğini yönetme](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

SAP iş yüküne sahip üretim DBMS senaryolarının en düşük önerisi şunlardır:

- Aynı Azure bölgesindeki ayrı bir kullanılabilirlik kümesinde iki VM dağıtın.
- Bu iki VM 'yi aynı Azure sanal ağında çalıştırın ve NIC 'Lerin aynı alt ağlardan dışarı takılmış olmasını sağlayabilirsiniz.
- İkinci VM ile etkin bir bekleme durumu tutmak için veritabanı yöntemlerini kullanın. Yöntemler SQL Server Always on, Oracle Data Guard veya HANA sistem çoğaltması olabilir.

Ayrıca, başka bir Azure bölgesindeki üçüncü bir sanal makineyi dağıtabilir ve başka bir Azure bölgesinde zaman uyumsuz çoğaltma sağlamak için aynı veritabanı yöntemlerini kullanabilirsiniz.

Azure kullanılabilirlik kümelerini ayarlama hakkında daha fazla bilgi için [Bu öğreticiye](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)bakın.



## <a name="azure-network-considerations"></a>Azure ağ değerlendirmeleri
Büyük ölçekli SAP dağıtımlarında [Azure sanal veri merkezi](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)'nin şema ' i kullanın. Kuruluşunuzun farklı bölümlerine sanal ağ yapılandırmanız ve izinleriniz ve rol atamalarınız için bunu kullanın.

Bu en iyi uygulamalar yüzlerce müşteri dağıtımı sonucudur:

- SAP uygulamasının dağıtıldığı sanal ağlar internet erişimi yok.
- Veritabanı VM 'Leri, uygulama katmanıyla aynı sanal ağda çalışır.
- Sanal ağ içindeki VM 'Ler, özel IP adresinin statik bir ayırmasını vardır. Daha fazla bilgi için bkz. [Azure 'Da IP adresi türleri ve ayırma yöntemleri](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- DBMS VM 'lerine ve bu kaynaklardan yönlendirme kısıtlamaları, yerel DBMS sanal makinelerinde yüklü olan güvenlik duvarları ile birlikte ayarlanmamış. Bunun yerine, trafik yönlendirme [ağ güvenlik grupları (NSG 'ler)](https://docs.microsoft.com/azure/virtual-network/security-overview)ile tanımlanır.
- Trafiği DBMS VM 'ye ayırmak ve yalıtmak için VM 'ye farklı NIC 'ler atayın. Her NIC farklı bir IP adresi alır ve her NIC farklı bir sanal ağ alt ağına atanır. Her alt ağın farklı NSG kuralları vardır. Ağ trafiğinin yalıtımı veya ayrımı, yönlendirme için bir ölçüdür. Ağ işleme kotalarını ayarlamak için kullanılmaz.

> [!NOTE]
> Azure aracılığıyla statik IP adresleri atama, bunları ayrı sanal NIC 'lere atama anlamına gelir. Konuk işletim sistemi içinde bir sanal NIC 'ye statik IP adresleri atamayın. Azure Backup gibi bazı Azure Hizmetleri, en azından birincil sanal NIC 'nin statik IP adreslerine değil, DHCP olarak ayarlanmasıdır. Daha fazla bilgi için bkz. [Azure sanal makine yedeklemesi sorunlarını giderme](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Bir VM 'ye birden çok statik IP adresi atamak için, bir VM 'ye birden fazla sanal NIC atayın.
>


> [!IMPORTANT]
> SAP uygulaması ile SAP NetWeaver-, Hybru-veya S/4HANA tabanlı SAP sisteminin DBMS katmanı arasındaki iletişim yolundaki [ağ sanal](https://azure.microsoft.com/solutions/network-appliances/) gereçlerini yapılandırma desteklenmez. Bu kısıtlama, işlevsellik ve performans nedenleriyle yapılır. SAP uygulama katmanı ve DBMS katmanı arasındaki iletişim yolu doğrudan bir tane olmalıdır. Kısıtlama, bu ASG ve NSG kuralları doğrudan iletişim yoluna izin veriyorsa, [uygulama güvenlik grubu (ASG) ve NSG kurallarını](https://docs.microsoft.com/azure/virtual-network/security-overview) içermez. 
>
> Ağ sanal gereçlerinin desteklenmeyen diğer senaryolar şunlardır:
>
> * [SAP uygulamalarında SUSE Linux Enterprise Server Azure VM 'LERINDE SAP NetWeaver Için yüksek kullanılabilirlik](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)bölümünde açıklandığı gibi, Linux Paceyapıcısı küme düğümlerini ve SBD cihazlarını temsil eden Azure VM 'ler arasındaki iletişim yolları.
> * Azure VM 'Ler ile Windows Server Genişleme Dosya Sunucusu (SOFS) arasındaki iletişim yolları, [Azure 'da bir dosya paylaşımının kullanıldığı bir Windows Yük devretme KÜMESINDE SAP ASCS/SCS örneği kümesinde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)açıklandığı şekilde ayarlanır. 
>
> İletişim yollarındaki ağ sanal gereçleri iki iletişim ortağı arasındaki ağ gecikmesini kolayca ikiye katmanızı sağlayabilir. Ayrıca, SAP uygulama katmanı ve DBMS katmanı arasındaki kritik yollarda üretilen işi kısıtlayabilir. Bazı müşteri senaryolarında, ağ sanal cihazları pacemaker Linux kümelerinin başarısız olmasına neden olabilir. Bunlar, Linux Paceüreticisi küme düğümleri arasındaki iletişimin bir ağ sanal gereci aracılığıyla SBD cihazlarıyla iletişim kurabileceği durumlardır.
>

> [!IMPORTANT]
> Desteklenmeyen başka bir tasarım , SAP uygulama KATMANıNıN ve DBMS katmanının birbirleriyle eşlenmeyen farklı Azure sanal ağlarına yayılmasıdır. [](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Farklı Azure sanal ağları kullanmak yerine bir Azure sanal ağı içindeki alt ağları kullanarak SAP uygulama katmanını ve DBMS katmanını ayırt etmenizi öneririz. 
>
> Öneriyi izlemeden ve bunun yerine iki katmanı farklı sanal ağlara katmamaya karar verirseniz, iki sanal ağ eşlenmiş olmalıdır. [](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 
>
> İki Azure sanal ağı arasındaki ağ trafiğinin [](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) aktarma maliyetlerine tabi olduğunu unutmayın. SAP uygulama katmanı ve DBMS katmanı arasında birçok terabayttan oluşan çok büyük veri hacmi alışverişi yapılır. SAP uygulama katmanının ve DBMS katmanının iki eşlenmiş Azure sanal ağı arasında ayrımı varsa, önemli maliyetler birikmesini sağlayabilirsiniz.

Azure kullanılabilirlik kümesi içindeki üretim DBMS dağıtımınız için iki VM kullanın. Ayrıca, SAP uygulama katmanı için ayrı yönlendirme ve iki DBMS sanal makinesi için yönetim ve işlemler trafiği kullanın. Aşağıdaki resme bakın:

![İki alt ağdaki iki VM 'nin diyagramı](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Trafiği yeniden yönlendirmek için Azure Load Balancer kullanma
SQL Server Always on veya HANA sistem çoğaltması gibi işlevselilerde kullanılan özel sanal IP adreslerinin kullanımı, bir Azure Yük dengeleyicinin yapılandırılmasını gerektirir. Yük dengeleyici, etkin DBMS düğümünü belirlemek ve trafiği yalnızca o etkin veritabanı düğümüne yönlendirmek için araştırma bağlantı noktalarını kullanır. 

Veritabanı düğümünün yük devretmesi varsa, SAP uygulamasının yeniden yapılandırılması gerekmez. Bunun yerine, en yaygın SAP uygulama mimarileri özel sanal IP adresine karşı yeniden bağlanır. Bu arada, yük dengeleyici trafiği ikinci düğüme özel sanal IP adresine yönlendirerek düğüm yük devretmesine yeniden davranır.

Azure iki farklı [yük dengeleyici SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)'su sunar: temel bir SKU ve standart SKU. Azure kullanılabilirlik alanları genelinde dağıtmak istemiyorsanız, temel yük dengeleyici SKU 'SU iyi bir şekilde yapılır.

DBMS VM 'Leri ile SAP uygulama katmanı arasındaki trafik her zaman yük dengeleyici üzerinden her zaman yönlendirilir mı? Yanıt, yük dengeleyiciyi nasıl yapılandırdığınıza bağlıdır. 

Şu anda, DBMS sanal makinesine gelen trafik her zaman yük dengeleyici aracılığıyla yönlendirilir. DBMS VM 'den uygulama katmanı VM 'sine giden trafik yolu, yük dengeleyicinin yapılandırmasına bağlıdır. 

Yük dengeleyici bir DirectServerReturn seçeneği sunar. Bu seçenek yapılandırılırsa, DBMS sanal makinesini SAP uygulama katmanına yönlendiren trafik yük dengeleyici üzerinden *yönlendirilmez* . Bunun yerine, doğrudan uygulama katmanına gider. DirectServerReturn yapılandırılmamışsa, SAP uygulama katmanının geri dönüş trafiği yük dengeleyici aracılığıyla yönlendirilir.

DirectServerReturn ' i SAP uygulama katmanı ve DBMS katmanı arasında konumlandırılmış yük dengeleyiciler ile birlikte yapılandırmanızı öneririz. Bu yapılandırma iki katman arasındaki ağ gecikmesini azaltır.

Bu yapılandırmayı SQL Server her zaman açık olarak ayarlama hakkında bir örnek için bkz. [Azure 'Da Always on kullanılabilirlik grupları için BIR ıLB dinleyicisi yapılandırma](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Azure 'da SAP altyapı dağıtımlarınız için yayımlanmış GitHub JSON şablonlarını bir başvuru olarak kullanırsanız, bu [şablonu SAP 3 katmanlı bir sistem için](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md)inceleyin. Bu şablonda, yük dengeleyici için doğru ayarları da görebilirsiniz.

### <a name="azure-accelerated-networking"></a>Azure hızlandırılmış ağ Iletişimi
Azure VM 'Ler arasındaki ağ gecikmesini daha fazla azaltmak için [Azure hızlandırılmış ağ](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)' ı seçmenizi öneririz. SAP iş yükü için Azure VM 'Leri dağıtırken, özellikle SAP uygulama katmanı ve SAP DBMS katmanı için bu uygulamayı kullanın.

> [!NOTE]
> Tüm VM türleri hızlandırılmış ağı desteklemez. Önceki makalede, hızlandırılmış ağı destekleyen VM türleri listelenmektedir.
>

---
> ![Windows][Logo_Windows] Windows
>
> Windows için hızlandırılmış ağ ile VM 'Leri dağıtmayı öğrenmek için bkz. [hızlandırılmış ağ Ile Windows sanal makinesi oluşturma](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> Linux dağıtımı hakkında daha fazla bilgi için bkz. [hızlandırılmış ağ Ile Linux sanal makinesi oluşturma](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> SUSE, Red Hat ve Oracle Linux söz konusu olduğunda, hızlandırılmış ağ, son sürümlerde desteklenir. SLES 12 SP2 veya RHEL 7,2 gibi eski sürümler Azure hızlandırılmış ağını desteklemez.
>


## <a name="deployment-of-host-monitoring"></a>Konak izlemenin dağıtımı
SAP uygulamalarının Azure sanal makinelerinde üretim kullanımı için SAP, Azure sanal makinelerini çalıştıran fiziksel konaklardan konak izleme verilerini alma özelliği gerektirir. SAPOSCOL ve SAP konak aracısında bu yeteneği sağlayan belirli bir SAP konak Aracısı düzeltme eki düzeyi gereklidir. Tam düzeltme eki düzeyi SAP Note [1409604]' de belgelenmiştir.

Konak verilerini SAPOSCOL ve SAP ana bilgisayar aracısına teslim eden bileşenlerin dağıtımı ve bu bileşenlerin yaşam döngüsü yönetimi hakkında daha fazla bilgi için bkz. [Dağıtım Kılavuzu][deployment-guide].


## <a name="next-steps"></a>Sonraki adımlar
Belirli bir DBMS hakkında daha fazla bilgi için bkz.:

- [SAP iş yükü için SQL Server Azure Sanal Makineler DBMS dağıtımı](dbms_guide_sqlserver.md)
- [SAP iş yükü için Oracle Azure Sanal Makineler DBMS dağıtımı](dbms_guide_oracle.md)
- [SAP iş yükü için IBM DB2 Azure Sanal Makineler DBMS dağıtımı](dbms_guide_ibm.md)
- [SAP iş yükü için SAP ASE Azure Sanal Makineler DBMS dağıtımı](dbms_guide_sapase.md)
- [Azure 'da SAP maxDB, canlı önbellek ve Içerik sunucusu dağıtımı](dbms_guide_maxdb.md)
- [Azure işlemlerinde SAP HANA kılavuzu](hana-vm-operations.md)
- [Azure sanal makineleri için yüksek kullanılabilirlik SAP HANA](sap-hana-availability-overview.md)
- [Azure sanal makinelerinde SAP HANA için yedekleme Kılavuzu](sap-hana-backup-guide.md)

