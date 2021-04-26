---
title: "Azure 'da SAP: Azure 'da desteklenen SAP yazılımı"
description: Azure 'da hangi SAP yazılımlarının dağıtılması gerektiğini açıklar
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673532"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure dağıtımları için hangi SAP yazılımı desteklenir?
Bu makalede, Azure dağıtımları için hangi SAP yazılımlarının desteklendiğini ve gereken işletim sistemi yayınlarının veya DBMS sürümlerinin ne olduğu nasıl bulabileceğiniz açıklanır.

Değerlendirme, geçerli SAP yazılımınızın desteklenip desteklenmediğini ve Azure 'da SAP yazılımlarınız ile hangi işletim sistemi ve DBMS sürümlerinin desteklendiğini belirlemek için erişmeniz gerekir:

- SAP destek notları
- SAP ürün kullanılabilirliği matrisi



## <a name="general-restrictions-for-sap-workload"></a>SAP iş yükü için genel kısıtlamalar
SAP iş yükü için kullanılabilen Azure IaaS Hizmetleri, x86 64 veya x64 donanımla sınırlıdır. SAP iş yüküne uygulanan bir SPARC veya güç CPU tabanlı teklifi yoktur. Şirket sistemlerinde, IBM ana bilgisayar veya AS400 gibi donanım mimarilerinde çalışan veya HP-UX, Solaris veya AıX işletim sistemlerinin kullanıldığı müşteriler, DBMS de dahil olmak üzere SAP uygulamalarını aşağıdaki işletim sistemlerinden birine değiştirmeniz gerekir:

- X86-64 platformu için Windows Server 64bit
- X86-64 platformu için SUSE Linux 64bit
- X86-64 platformu için Red Hat Linux 64Bit
- X86-64 platformu için Oracle Linux 64bit

SAP yazılımıyla birlikte, diğer işletim sistemi yayınları veya Linux dağıtımları desteklenmez. Belirli sürüm ve çalışmalarda tam ayrıntılar belgenin ilerleyen kısımlarında açıklanmıştır.


## <a name="you-start-here"></a>Buradan başlayın
Sizin için başlangıç noktası [sap destek notuna #1928533](https://launchpad.support.sap.com/#/notes/1928533). Bu SAP notlarından yukarıdan aşağıya kadar, desteklenen yazılımların ve VM 'lerin birkaç alanı gösteriliyor

İlk bölüm, genel olarak Azure VM 'lerinde SAP yazılımıyla desteklenen işletim sürümleri için en düşük gereksinimleri listeler. Bu en düşük gereksinimlere ulaşmadıysanız ve bu işletim sistemlerinin eski sürümlerini çalıştırırsanız, işletim sistemi sürümünüzü en düşük sürüme veya hatta daha yeni sürümlere yükseltmeniz gerekir. Genel olarak Azure 'un bu işletim sistemlerinden bazılarının eski sürümlerini destekleyeceği doğru bir uygulamadır. Ancak listelenen kısıtlamalar veya minimum yayınlar, yürütülen testleri ve nitelikleri temel alır ve daha ileri geri alınamaz. 


> [!NOTE]
>Daha yeni işletim sistemi sürümleri gerektirecek belirli sanal makine türleri, HANA büyük örnekler veya SAP iş yükleri vardır. Bu gibi durumlar belge genelinde bahsedilir. Bu gibi durumlar, SAP notları veya diğer SAP yayınlarında açıkça belgelenmiştir.

Aşağıdaki bölümde desteklenen ve desteklenen SAP çekirdekleri tarafından desteklenen ve daha önemli olan genel SAP platformları listelenmiştir. Minimum çekirdek sürümleri gerektiren ve desteklenen ve desteklenen NetWeaver/ABAP veya Java yığınlarını listeler. Azure 'da daha fazla yeni ABAP yığını desteklenir, ancak Azure değişiklikleri daha yeni yığınların geliştirilmesi başlangıcından itibaren uygulandığından en düşük çekirdek yayınlarına gerek yoktur

Şunları denetlemeniz gerekir:

- Çalıştırdığınız SAP uygulamalarının belirtilen en düşük yayınlar kapsamında olup olmadığı. Aksi takdirde, yeni hedef sürümde hangi işletim sistemi derlemeleri ve DBMS birleşimlerinin desteklendiği, SAP ürün kullanılabilirliği matrisini kontrol etmeniz gerekir. Bu nedenle, doğru işletim sistemi sürümü ve DBMS sürümünü seçebilmenizi sağlayabilirsiniz.
- SAP çekirdekleri Azure 'a geçiş sırasında güncelleştirmeniz gerekip gerekmediğini belirtir
- SAP destek paketlerini güncelleştirmeniz gerekip gerekmediğini belirtir. Özellikle de daha yeni bir DBMS sürümüne geçmeniz gereken durumlar için gerekebilecek temel destek paketleri


Sonraki bölümde, Windows ve Linux için Azure 'da SAP tarafından desteklenen diğer SAP ürünleri ve DBMS sürümleri hakkında daha ayrıntılı bilgi sağlanır. 

> [!NOTE]
> Farklı DBMS 'nin en düşük sürümleri dikkatle seçilir ve her zaman DBMS 'nin tüm yelpazesini yansıtmayabilir, genel olarak Azure 'da farklı DBMS satıcıları destekler. Bu en düşük yayınları tanımlamak için birçok SAP iş yüküne ilişkin ilgili dikkat edilmesi gereken noktalar dikkate alınmıştır. Eski DBMS sürümlerini test etmek ve nitelemek için çaba yoktur. 

> [!NOTE]
> Listelenen en düşük sürümler, işletim sistemlerinin ve veritabanı sürümlerinin eski sürümlerini temsil eder. En son işletim sistemi sürümlerini ve veritabanı sürümlerini kullanmak çok teşvik ediyoruz. Birçok durumda, en son işletim sistemi ve veritabanı yayınları, genel bulutta çalışan kullanım durumunu ve genel bulutta veya daha fazla Azure 'da çalışmaya en uygun şekilde uyarlanmasını sağlar

## <a name="oracle-dbms-support"></a>Oracle DBMS desteği
Azure 'da desteklenen bir işletim sistemi, Oracle DBMS yayınları ve Oracle işlevselliği, [sap destek not#2039619](https://launchpad.support.sap.com/#/notes/2039619)'de özellikle listelenmiştir. Bu NotDan bir tanesi şu şekilde özetlenebilir:

- NetWeaver için sertifikalı Azure VM 'lerinde desteklenen minimum Oracle sürümü Oracle 11g Release 2 patchset 3 (11.2.0.4)
- Yalnızca Windows ve Oracle Linux uygun Konuk işletim sistemleri. İşletim sisteminin ve ilgili minimum DBMS sürümlerinin tam sürümleri notta listelenmiştir
- Oracle Linux desteği, Oracle DBMS istemcisine de genişletilir. Bu, ABAP veya Java yığınının iletişim kutusu örnekleri gibi tüm SAP bileşenlerinin Oracle Linux de çalışması gerektiği anlamına gelir. Yalnızca bu tür bir SAP sisteminde, Oracle DBMS 'ye bağlanmayan SAP bileşenlerinin farklı bir Linux işletim sistemi çalıştırmasına izin verilir
- Oracle RAC desteklenmiyor 
- Oracle ASM, bazı durumlar için desteklenir. Ayrıntılar notta listelenmiştir
- Unicode olmayan SAP sistemleri yalnızca Windows Konuk işletim sistemi ile çalışan uygulama sunucularıyla desteklenir. DBMS 'nin Konuk işletim sistemi Oracle Linux veya Windows olabilir. Bu kısıtlamanın nedeni SAP ürün kullanılabilirliği matrisi (PAM) denetlenirken görünür. Oracle Linux için SAP hiçbir şekilde Unicode olmayan SAP çekirdekleri yayımlamadı

Hedeflenen Azure altyapısıyla desteklenen DBMS sürümlerini bilmenin gerekli olduğu işletim sistemi yayınları ve DBMS 'ler, çalıştırmak istediğiniz SAP ürün sürümlerinizin ile desteklenip desteklenmediğini belirten SAP ürün kullanılabilirliği matrisini denetlemeniz gerekir. 

## <a name="oracle-linux"></a>Oracle Linux
Oracle Linux etrafında en belirgin sorulardan biri, SAP 'nin Oracle Linux bir parçası olan Red Hat çekirdeğini destekleyip desteklemediğini belirtir. Ayrıntılar için bkz. [sap destek notunun #1565179](https://launchpad.support.sap.com/#/notes/1565179).

## <a name="other-database-than-sap-hana"></a>SAP HANA diğer veritabanı
SAP iş yükü için HANA olmayan veritabanlarının desteklenmesi, [sap destek notunda #1928533](https://launchpad.support.sap.com/#/notes/1928533)belgelenmiştir.


## <a name="sap-hana-support"></a>SAP HANA desteği
Azure 'da, HANA veritabanını çalıştırmak için kullanılabilecek iki hizmet vardır:

- Azure Sanal Makineler
- [HANA büyük örnekler](./hana-overview-architecture.md)

SAP HANA çalıştırmak için SAP daha fazla ve daha güçlü koşullara sahiptir. Bu altyapı, NetWeaver veya diğer SAP uygulamaları ve DBMS çalıştırmalarından daha fazlasını karşılar. Sonuç olarak, SAP HANA DBMS 'yi çalıştırmaya yönelik daha az sayıda Azure sanal makinesi vardır. SAP HANA için desteklenen desteklenen Azure altyapısının listesi, [SAP HANA donanım dizini](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)adlı bölümünde bulunabilir. 

> [!NOTE]
> Mektupla başlayan birimler [Hana büyük örnekler](./hana-overview-architecture.md) birimleridir. 

> [!NOTE]
> SAP 'nin SAP HANA ana sürümlere bağımlı belirli bir sertifikası yoktur. [Hana sertifikalı IaaS platformlarındaki](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)sütun **sertifikasyon senaryosu** olan yaygın GÖRÜŞLERDE, sütun, **Hana büyük veya küçük yayın sertifikalı bir ifade** yapmaz. Belirli birimlere yönelik sertifikalı işletim sistemi yayınları de HANA 1,0 yayınları tarafından desteklendiği sürece, HANA 1,0 ve HANA 2,0 için kullanılabilen tüm birimlerin kabul etmeniz gerekir. 

SAP HANA kullanımı için, genel NetWeaver durumları için farklı en düşük işletim sistemi yayınları uygulanabilir. Bu gibi farklılık gösterebileceğinden, her birim için desteklenen işletim sistemlerini ayrı ayrı denetlemeniz gerekir. Her bir birime tıklayarak bunu yapabilirsiniz. Daha fazla ayrıntı görüntülenecektir. Listelenen ayrıntıların biri, bu belirli birim için desteklenen farklı işletim sistemleridir.

> [!NOTE]
> Azure HANA büyük örnek birimleri, Azure VM 'lerine kıyasla desteklenen işletim sistemleriyle daha kısıtlayıcıdır. Diğer yandan Azure sanal makineleri, en düşük yayınlar olarak daha yeni işletim yayınları uygulayabilir. Bu durum özellikle, Linux çekirdekler 'de gerekli değişiklikler olan büyük VM birimlerinin bazıları için geçerlidir

Azure altyapısı için desteklenen işletim sistemini bilmenin yanı sıra, hedeflediğiniz Azure birimleri ile desteklenen tam SAP HANA yayınlar ve düzeltme eki düzeyleri için [sap destek not#2235581](https://launchpad.support.sap.com/#/notes/2235581) denetlemeniz gerekir. 

> [!IMPORTANT]
> Desteklenen SAP HANA yayınları ve düzeltme eki düzeylerini denetleme adımı çok önemlidir. Birçok durumda, belirli bir işletim sistemi sürümünün desteklenmesi SAP HANA yürütülebilirlerin belirli bir düzeltme eki düzeyine bağımlıdır.

Hedeflenen Azure altyapısında çalıştırabileceğiniz belirli HANA sürümlerini öğrendiğinizde, filtrelenmiş HANA sürümlerini destekleyen SAP ürün sürümleriyle ilgili kısıtlamalar olup olmadığını öğrenmek için SAP ürün kullanılabilirliği matrisini denetlemeniz gerekir


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Sertifikalı Azure VM 'Leri ve HANA büyük örnek birimleri ve iş işlem performansı
Desteklenen işletim sistemi sürümlerinin, DBMS sürümlerinin ve bağımlı Azure altyapı birimleri için SAP yazılım sürümlerinin hesaplanmasının yanı sıra, bu birimleri SAP tarafından ' SAP ' biriminde ifade edilen iş işlem aktarım hızına göre nitelemeniz gerekir. Tüm SAP boyutlandırma işlemleri, SAPS hesaplamalarına bağlıdır. Mevcut SAP sistemlerini değerlendirirken, genellikle altyapı sağlayıcınızın yardımıyla birimlerin SAPS 'sini hesaplayabilirsiniz. DBMS katmanının ve uygulama katmanının yanı sıra. Yeni işlevlerin oluşturulduğu diğer durumlarda, SAP ile boyutlandırma alıştırması, uygulama katmanı ve DBMS katmanı için gereken SAPS numaralarını açığa çıkarmakta olabilir. Altyapı sağlayıcısı olarak Microsoft, NetWeaver ve/veya HANA sertifikalı farklı birimlerin SAP verimlilik boyutunu sağlamaya yönelik olarak tasarlanmıştır.

Azure VM 'Leri için bu SAPS üretilen iş numaraları, [sap destek not#1928533](https://launchpad.support.sap.com/#/notes/1928533)bölümünde belgelenmiştir. Azure HANA büyük örnek birimleri için, SAPS üretilen iş numaraları [sap destek notunda belgelenmiştir #2316233](https://launchpad.support.sap.com/#/notes/2316233)

[Sap destek notuna #1928533](https://launchpad.support.sap.com/#/notes/1928533), aşağıdaki açıklamalar geçerlidir:

- **D serisi Azure VM 'leri ve Azure VM 'leri Mv2-Series, diğer Azure VM türleri için farklı en düşük işletim sistemi sürümleri geçerlidir**. Daha yeni işletim sistemi sürümlerinin gereksinimi, belirli Azure VM türlerinde çalışan işletim sistemlerini etkinleştirmek veya bu VM türlerindeki SAP iş yükünün performansını ve üretilen iş yükünü iyileştirmek için işletim sistemi sürümlerinde sağlamaları gereken farklı işletim sistemi satıcılarının değişikliklere dayalıdır
- Farklı VM türlerini belirten iki tablo vardır. İkinci tablo yalnızca Azure Standart depolama alanını destekleyen Azure sanal makine türleri için SAPS aktarım hızını belirtir. Notun ikinci tablosunda belirtilen birimlerde DBMS dağıtımı desteklenmez


## <a name="other-sap-products-supported-on-azure"></a>Azure 'da desteklenen diğer SAP ürünleri
Bu varsayımıyla, Azure gibi hiper ölçekli bulutların çoğunun Azure 'da işlevsel sorunlar olmadan çalıştırılması gerekir. Bununla birlikte, özel bulut görselleştirmesinin tersine, SAP farklı heripscale bulut sağlayıcıları için açıkça farklı SAP ürünleri desteğini ifade etmektedir. Sonuç olarak, farklı SAP ürünleri için Azure desteğini gösteren farklı SAP destek notları vardır. 

Iş nesneleri bı platformunda [sap destek notuna #2145537](https://launchpad.support.sap.com/#/notes/2145537) , Azure 'DA desteklenen SAP Business Objects ürünlerinin bir listesini sağlar. Listelenen veya desteklenmeyen ve listelenen en düşük sürümlerden daha yeni olan yazılım sürümlerinin ve işletim sistemi sürümlerinin bileşenleri veya birleşimleri hakkında sorularınız varsa, destek sağlayan bileşene karşı bir SAP destek isteği açmanız gerekir.

Iş nesneleri veri Hizmetleri için, [sap destek notunun #22288344](https://launchpad.support.sap.com/#/notes/2288344) Azure ÜZERINDE çalışan sap veri hizmetlerinin en düşük desteğini açıklar. 

> [!NOTE]
> SAP destek notunda gösterildiği gibi, Azure 'da desteklenecek doğru destek paketi düzeyini belirlemek için SAP PAM ' i denetlemeniz gerekir

Azure Kubernetes Hizmetleri 'nde (AKS) SAP veri hub 'ı/Vora desteği [sap destek notunda](https://launchpad.support.sap.com/#/notes/2464722) ayrıntılı olarak #2464722

SAP BPC 10,1 SP08 desteği [sap destek notunda açıklanmaktadır #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Azure 'da SAP Hybrsıs ticaret platformu desteği, [Hybrin belgelerinde](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html)ayrıntılı olarak açıklanmıştır. SAP Hybrsıs ticaret platformu için desteklenen DBMS itibariyle şu şekilde listeler:

- Windows işletim sistemi platformunda SQL Server ve Oracle. SAP NetWeaver için aynı en düşük yayınlar geçerlidir. Ayrıntılar için bkz. [sap destek notuna #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- Red Hat ve SUSE Linux üzerinde SAP HANA. SAP HANA sertifikalı VM türleri, [Bu belgede](#sap-hana-support)daha önce belgelenen şekilde gereklidir. SAP (Hybrsıs) ticaret platformu OLTP iş yükü olarak kabul edilir
- SQL Azure DB as SAP (Hybrsıs) ticaret platformu sürüm 1811




## <a name="next-steps"></a>Sonraki Adımlar
[SAP NetWeaver Için Azure sanal makineleri planlama ve uygulama](./planning-guide.md) 'da sonraki adımları okuyun
