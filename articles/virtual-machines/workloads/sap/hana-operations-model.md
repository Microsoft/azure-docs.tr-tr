---
title: Azure 'da SAP HANA işlem modeli (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA işlem modeli (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a8ea845dd53048766abc337a1351a408ea7f1bb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099702"
---
# <a name="operations-model-and-responsibilities"></a>İşlem modeli ve sorumluluklar

Azure 'da SAP HANA (büyük örnekler) ile birlikte sunulan hizmet, Azure IaaS Hizmetleri ile hizalanır. SAP HANA için en iyi duruma getirilmiş yüklü bir işletim sistemine sahip bir HANA büyük örnek örneği alırsınız. Azure IaaS VM 'lerinde olduğu gibi, işletim sistemini sağlamlaştırma, ek yazılım yükleme, HANA yükleme, işletim SISTEMI ve HANA 'yi çalıştırma ve işletim SISTEMI ve HANA 'yı güncelleştirme sorumluluğu sizin sorumluluğunuzdadır. Microsoft, işletim sistemi güncelleştirmelerini veya HANA güncelleştirmelerini sizin için zorlamaz.

![Azure 'da SAP HANA sorumlulukları (büyük örnekler)](./media/hana-overview-architecture/image2-responsibilities.png)

Diyagramda gösterildiği gibi, Azure 'daki SAP HANA (büyük örnekler) çok kiracılı bir IaaS teklifidir. Çoğu bölüm için sorumluluk bölümü işletim sistemi altyapı sınırında olur. Microsoft, işletim sistemi satırının altındaki hizmetin tüm yönlerini sorumludur. Satırın üstündeki hizmetin tüm yönleri sizin sorumluluğunuzdadır. İşletim sistemi sorumluluğundadır. Uyumluluk, güvenlik, uygulama yönetimi, temel ve işletim sistemi yönetimi için kullanabileceğiniz en güncel şirket içi yöntemleri kullanmaya devam edebilirsiniz. Sistemler, ağınızda olduğu gibi görünür.

Bu hizmet SAP HANA için iyileştirilmiştir, bu nedenle en iyi sonuçlar için temel altyapı yeteneklerini kullanmak üzere Microsoft ile çalışmanız gereken bölgeler vardır.

Aşağıdaki listede katmanların ve sorumluluklarınızın her biri hakkında daha fazla ayrıntı verilmiştir:

**Ağ iletişimi**: SAP HANA çalıştıran büyük örnek damgasına yönelik tüm iç ağlar. Sorumluluğunda depolama erişimi, örnekler arasındaki bağlantı (genişleme ve diğer işlevler için), yatağa bağlantı ve SAP uygulama katmanının VM 'lerde barındırıldığı Azure bağlantısı dahildir. Ayrıca olağanüstü durum kurtarma amaçları için Azure veri merkezleri arasında WAN bağlantısı içerir. Tüm ağlar kiracıya göre bölümlenmiştir ve hizmet kalitesi uygulanmış olmalıdır.

**Depolama alanı**: SAP HANA sunucularının yanı sıra anlık görüntüler için gereken tüm birimler için sanallaştırılmış bölümlenmiş depolama. 

**Sunucular**: Kiracılara atanan SAP HANA veritabanlarını çalıştırmak için adanmış fiziksel sunucular. Bir SKU 'nun tür ı sınıfı sunucular, donanım soyutlandır. Bu tür sunucularla sunucu yapılandırması, bir fiziksel donanımdan başka bir fiziksel donanıma taşınabilecek profillerde toplanır ve saklanır. Bir profilin işlemlere göre bu tür bir (el ile) taşınması, bir bit ile Azure hizmet düzeltme ile karşılaştırılabilir. Tür II sınıf SKU 'Larının sunucuları böyle bir yetenek sunmaz.

**SDDC**: Veri merkezlerini yazılım tanımlı varlıklar olarak yönetmek için kullanılan yönetim yazılımı. Microsoft 'un kaynakları ölçek, kullanılabilirlik ve performans nedenleriyle havuzalmasına olanak tanır.

**O/SN**: Sunucularda çalışan seçtiğiniz işletim sistemi (SUSE Linux veya Red Hat Linux). İle sağladığınız işletim sistemi görüntüleri, SAP HANA çalıştırmak için tek bir Linux satıcısı tarafından Microsoft 'a sunulmaktadır. Belirli SAP HANA iyileştirilmiş görüntü için Linux satıcısına sahip bir aboneliğiniz olması gerekir. Görüntüleri işletim sistemi satıcısına kaydetmekten siz sorumlusunuz. 

Microsoft 'un devreden Multipath noktasından, Linux işletim sisteminin daha fazla düzeltme ekiyle sorumlu olursunuz. Bu düzeltme eki, başarılı bir SAP HANA yüklemesi için gerekli olabilecek ve SAP HANA en iyi duruma getirilmiş işletim sistemi görüntülerine belirli Linux satıcılarına dahil edilmemiş ek paketler içerir. (Daha fazla bilgi için bkz. SAP 'nin HANA yükleme belgeleri ve SAP notları.) 

İşletim SISTEMI düzeltme eki uygulama, işletim sistemini ve sürücülerini belirli sunucu donanımına göre hatalı duruma getirmek veya en iyi duruma getirmek sizin sorumluluğunuzdadır. Ayrıca, işletim sisteminin güvenlik veya işlevsel düzeltme ekiyle de sorumlu olursunuz. 

Sorumluluğu, izleme ve Kapasite planlamasını de içerir:

- CPU kaynak tüketimi.
- Bellek tüketimi.
- Boş alan, ıOPS ve gecikmeyle ilgili disk birimleri.
- HANA büyük örnek ile SAP uygulama katmanı arasındaki ağ birimi trafiği.

HANA büyük örneğinin temel altyapısı, işletim sistemi biriminin yedeklenmesi ve geri yüklenmesi için işlevsellik sağlar. Bu işlevselliğin kullanılması da sizin sorumluluğunuzdadır.

**Ara yazılım**: SAP HANA örneği öncelikle. Yönetim, işlemler ve izleme sorumluluğu sizin sorumluluğunuzdadır. Yedekleme ve geri yükleme ve olağanüstü durum kurtarma amacıyla depolama anlık görüntülerini kullanmak için, sunulan işlevleri kullanabilirsiniz. Bu yetenekler altyapı tarafından sağlanır. Sorumluluklarınız Ayrıca, bu yetenekler ile yüksek kullanılabilirlik veya olağanüstü durum kurtarma tasarlamayı, bunlardan yararlanarak ve depolama anlık görüntülerinin başarıyla yürütülüp yürütülmediğini tespit etmek için izleme içerir.

**Veri**: SAP HANA tarafından yönetilen verileriniz ve birimlerde veya dosya paylaşımlarında bulunan yedeklemeler dosyaları gibi diğer veriler. Sorumluluklarınız, disk boş alanı izlemeyi ve birimlerdeki içeriği yönetmeyi içerir. Ayrıca, disk birimlerinin ve depolama anlık görüntülerinin yedeklerinin başarılı bir şekilde yürütülmesinin izlenmesinden de sorumlu olursunuz. Veri çoğaltmanın olağanüstü durum kurtarma sitelerine başarıyla yürütülmesi Microsoft 'un sorumluluğundadır.

**Uygulamaları** SAP uygulama örnekleri veya SAP olmayan uygulamalar söz konusu uygulamaların uygulama katmanı. Sorumluluklarınız, bu uygulamaların dağıtım, yönetim, işlemler ve izlenmesini içerir. CPU kaynak tüketimine, bellek tüketimine, Azure depolama tüketimine ve sanal ağlarda ağ bant genişliği tüketimine yönelik kapasite planlaması sizin sorumluluğunuzdadır. Ayrıca, sanal ağlardan gelen kaynak tüketimine yönelik kapasite planlamasının Azure 'da SAP HANA (büyük örnekler) için de sorumlu olursunuz.

**WAN 'lar**: Şirket içinden iş yükleri için Azure dağıtımlarından oluşturduğunuz bağlantılar. HANA büyük örneği olan tüm müşteriler bağlantı için Azure ExpressRoute kullanır. Bu bağlantı Azure (büyük örnekler) çözümünde SAP HANA bir parçası değildir. Bu bağlantının kurulumundan siz sorumlusunuz.

**Arşiv**: Depolama hesaplarında kendi yöntemlerinizi kullanarak veri kopyalarını arşivlemeyi tercih edebilirsiniz. Arşivleme için yönetim, uyumluluk, maliyetler ve işlemler gerekir. Azure 'da arşiv kopyaları ve yedeklemeler oluşturmaktan ve bunları uyumlu bir şekilde depolamadan sorumlusunuz.

Bkz. [Azure 'da SAP HANA SLA (büyük örnekler)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Sonraki adımlar**
- [Azure 'da SAP HANA (büyük örnekler) mimarisine](hana-architecture.md) başvurun
