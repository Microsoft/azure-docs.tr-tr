---
title: Azure sanal makinelerinde Ana bilgisayar yeniden barındırma
description: Microsoft Azure üzerindeki sanal makineleri (VM 'Ler) kullanarak IBM Z tabanlı sistemler gibi ana bilgisayar iş yüklerinizi yeniden barındırın.
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.openlocfilehash: 1baba0afc08c6a4df63c38cafe9231411ecf8f1f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048859"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Azure sanal makinelerinde Ana bilgisayar yeniden barındırma

İş yüklerini anabilgisayar ortamlarından buluta geçirmek, altyapınızı modernleştirin ve genellikle maliyetlerde tasarruf etmenizi sağlar. Birçok iş yükü, yalnızca küçük kod değişiklikleriyle (veritabanlarının adlarını güncelleştirme gibi) Azure’a aktarılabilir.

Genellikle, *ana* terimi büyük bir bilgisayar sistemi anlamına gelir. Özellikle de şu anda kullanılmakta olan büyük çoğunluğu, MVS, DOS, Vo, OS/390 veya Z/OS çalıştıran IBM sistem Z sunucularıdır veya IBM Plug uyumlu sistemlerdir.

Bir Azure sanal makinesi (VM), tek bir örnekteki belirli bir uygulama için kaynakları yalıtmak ve yönetmek için kullanılır. IBM z/OS gibi ana bilgisayarlar bu amaçla mantıksal bölümler (LPARS) kullanır. Ana bilgisayar, ilişkili COBOL programlarının bulunduğu bir CICS Region ve IBM DB2 veritabanı için ayrı bir litibarı kullanabilir. [Azure 'daki tipik n katmanlı bir uygulama](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) , Azure VM 'leri her katman için alt ağlara ayrılabilir bir sanal ağa dağıtır.

Azure VM 'Leri, kaldırma ve kaydırma senaryolarını destekleyen ana bilgisayar öykünme ortamlarını ve derleyicileri çalıştırabilir. Geliştirme ve test, genellikle bir ana bilgisayardan Azure dev/test ortamına geçiş yapmak için ilk iş yükleri arasında yapılır. Öykünecek ortak sunucu bileşenleri, aşağıdaki şekilde gösterildiği gibi çevrimiçi işlem işlemi (OLTP), toplu işlem ve veri alma sistemlerini içerir.

![Azure 'daki öykünme ortamları, z/OS tabanlı sistemleri çalıştırmanızı sağlar.](media/01-overview.png)

Bazı anabilgisayar iş yükleri, Azure 'a göreli olarak geçirilebilir, diğerleri ise iş ortağı çözümü kullanılarak Azure 'da yeniden barındırılabilir. Bir iş ortağı çözümü seçme hakkında ayrıntılı kılavuz için, [Azure anabilgisayar geçiş merkezi](https://azure.microsoft.com/migration/mainframe/) size yardımcı olabilir.

## <a name="mainframe-migration"></a>Ana bilgisayar geçişi

Yeniden barındırma, yeniden oluşturma, değiştirme veya devre dışı bırakma IaaS veya PaaS? Ana bilgisayar uygulamanıza yönelik doğru geçiş stratejisini öğrenmek için Azure Mimari Merkezi [ana bilgisayar geçiş](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  kılavuzuna bakın.

## <a name="micro-focus-rehosting-platform"></a>Mikro odak yeniden barındırma platformu

Micro Focus Enterprise Server, kullanılabilir en büyük anabilgisayar yeniden barındırma platformlarından biridir. Azure 'da daha az pahalı bir x86 platformunda z/OS iş yüklerinizi çalıştırmak için kullanabilirsiniz.

Başlamak için:

- [Azure 'da Enterprise Server ve Enterprise Developer 'ı yükler](./microfocus/set-up-micro-focus-azure.md)
- [Azure 'da Kurumsal Geliştirici için CICS BankDemo ayarlama](./microfocus/demo.md)
- [Azure 'da bir Docker kapsayıcısında kurumsal sunucu çalıştırma](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>Azure 'da TmaxSoft OpenFrame

TmaxSoft OpenFrame, Asansör ve kaydırma senaryolarında kullanılan popüler bir anabilgisayar yeniden barındırma çözümüdür. Azure 'daki bir OpenFrame ortamı geliştirme, tanıtımlar, testler veya üretim iş yükleri için uygundur.

Başlamak için:

- [TmaxSoft OpenFrame ile çalışmaya başlama](./tmaxsoft/get-started.md)
- [EKitap 'ı indirin](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12,0

IBM Z geliştirme ve test ortamı (IBM zD&T), Azure 'da, Z/OS tabanlı uygulamaların geliştirilmesi, test edilmesi ve tanıtımları için kullanabileceğiniz üretim dışı bir ortam oluşturur.

Azure 'daki öykünme ortamı, uygulama geliştiricileri denetimli dağıtımlar (Adcd) ile farklı türlerde Z örnekleri barındırabilir. ZD&T Personal Edition, zD&T Parallel sysplex ve zD&T Enterprise Edition 'ı Azure 'da ve Azure Stack çalıştırabilirsiniz.

Başlamak için:

- [Azure 'da IBM zD&T 12,0 'yi ayarlama](./ibm/install-ibm-z-environment.md)
- [ZD&T üzerinde ADCD ayarlama](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>Azure 'da IBM DB2 pureScale

IBM DB2 pureScale ortamı, Azure için bir veritabanı kümesi sağlar. Bu, özgün ortamla aynı değildir, ancak Paralel Sysplex kurulumunda çalışan z/ç için IBM DB2 olarak benzer kullanılabilirlik ve ölçek sunar.

Başlamak için bkz. [Azure 'Da IBM DB2 pureScale](.//ibm/ibm-db2-purescale-azure.md).

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Ana bilgisayar iş yüklerini Azure altyapısına hizmet olarak (IaaS) geçirdiğinizde, Azure VM 'Leri dahil olmak üzere çeşitli isteğe bağlı, ölçeklenebilir bilgi işlem kaynakları arasından seçim yapabilirsiniz. Azure, bir dizi [Linux](../../linux/overview.md) ve [Windows](../../windows/overview.md) VM sunar.

### <a name="compute"></a>İşlem

Azure işlem gücü favorably, ana bilgisayarın kapasitesine göre karşılaştırılır. Bir anabilgisayar iş yükünü Azure 'a taşımayı düşünüyorsanız, ana bilgisayar ölçüsünü saniyede 1.000.000 yönerge (MIPS) ile sanal CPU 'Lara karşılaştırın. 

[Ana bilgisayar işlem ve Azure 'a taşımayı](./concepts/mainframe-compute-azure.md)öğrenin.

### <a name="high-availability-and-failover"></a>Yüksek kullanılabilirlik ve yük devretme

Azure taahhüt tabanlı hizmet düzeyi sözleşmeleri (SLA 'Lar) sağlar. Birden çok-nines kullanılabilirliği varsayılandır ve SLA 'Lar hizmetlerin yerel veya coğrafi tabanlı çoğaltmasıyla iyileştirilebilir. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

VM gibi Azure IaaS ile belirli sistem işlevleri, Yük Devretme Kümelemesi örnekleri ve kullanılabilirlik kümeleri gibi yük devretme desteği sağlar. Azure platformu hizmet olarak platform (PaaS) kaynaklarını kullandığınızda, Platform yük devretmeyi otomatik olarak işler. [Azure SQL veritabanı](../../../azure-sql/database/sql-database-paas-overview.md) ve [Azure Cosmos DB](../../../cosmos-db/introduction.md)örnekler de vardır.

### <a name="scalability"></a>Ölçeklenebilirlik

Ana bilgisayarlar genellikle ölçeği büyütme, bulut ortamları ölçeği genişletme. Azure, gereksinimlerinizi karşılayacak bir dizi [Linux](../../sizes.md) ve [Windows](../../sizes.md) boyutu sunar. Bulut Ayrıca, tam Kullanıcı belirtimleriyle eşleşecek şekilde ölçeği artırır veya küçültün. Kullanım tabanlı bir faturalandırma modeli kapsamında isteğe bağlı olarak işlem gücü, depolama ve hizmet [ölçeği](/azure/architecture/best-practices/auto-scaling) .

### <a name="storage"></a>Depolama

Bulutta, esnek ve ölçeklenebilir bir depolama seçenekleriniz vardır ve yalnızca ihtiyacınız olanlar için ödeme yaparsınız. [Azure depolama](../../../storage/common/storage-introduction.md) , veri nesneleri için yüksek düzeyde ölçeklenebilir bir nesne deposu, bulut için bir dosya sistemi hizmeti, güvenilir bir mesajlaşma deposu ve bir NoSQL deposu sunar. VM 'Ler için, yönetilen ve yönetilmeyen diskler kalıcı ve güvenli disk depolama alanı sağlar.

[Ana bilgisayar depolamayı Azure 'a taşımayı](./concepts/mainframe-storage-azure.md)öğrenin.

### <a name="backup-and-recovery"></a>Yedekleme ve kurtarma

Kendi olağanüstü durum kurtarma sitenizin saklanması pahalı bir önerde olabilir. Azure, yerel veya bölgesel düzeylerde [yedekleme](../../../backup/backup-overview.md), [Kurtarma](../../../site-recovery/site-recovery-overview.md)ve [Artıklık](../../../storage/common/storage-redundancy.md) için kolay ve ekonomik ve uygun maliyetli seçeneklere sahiptir veya coğrafi artıklık aracılığıyla yapılır.

## <a name="azure-government-for-mainframe-migrations"></a>Ana bilgisayar geçişleri için Azure Kamu

Birçok genel sektör varlığı, ana bilgisayarlarını daha modern ve esnek bir platforma taşımak için çok sevecekti. Microsoft Azure Kamu, genel Microsoft Azure platformunun (Federal, eyalet ve yerel kamu sistemleri için paketlenmiş) fiziksel olarak ayrılmış bir örneğidir. Özellikle Birleşik Devletler kamu kurumları ve iş ortakları için birinci sınıf güvenlik, koruma ve uyumluluk hizmetleri sağlar.

Azure Kamu, bu tür bir ortama ihtiyacı olan sistemler için Fedratin yüksek etkisi için (P-ATO) çalıştırmak üzere geçici bir yetki elde edin.

Başlamak için, [anabilgisayar uygulamaları için Microsoft Azure Kamu Bulutu](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)indirin.

## <a name="next-steps"></a>Sonraki adımlar

[İş ortaklarımıza](partner-workloads.md) , anabilgisayar uygulamalarınızı geçirmenize veya yeniden barındırmanıza yardımcı olmak için sorun. 

Ayrıca bkz:

- [Anabilgisayar konuları hakkında teknik incelemeler](mainframe-white-papers.md)
- [Ana bilgisayar geçişi](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Sorun giderme](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
- [Azure geçişine Demystifying ana bilgisayar](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md