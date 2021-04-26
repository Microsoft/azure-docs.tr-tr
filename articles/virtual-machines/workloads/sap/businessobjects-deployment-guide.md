---
title: Azure 'da SAP BusinessObjects bı platformu dağıtımı | Microsoft Docs
description: Azure 'da SAP BusinessObjects bı platformunu planlayın, dağıtın ve yapılandırın
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 3b3c313df5704e49481c66ad682faccd48d180ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505911"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Azure’da SAP BusinessObjects İş Zekası platformu planlama ve uygulama kılavuzu

## <a name="overview"></a>Genel Bakış

Bu kılavuzun amacı, Azure 'da SAP BOBI platformu olarak da bilinen SAP BusinessObjects bı platformunu planlama, dağıtma ve yapılandırmaya yönelik yönergeler sağlamaktır. Bu kılavuz, SAP BOBI platformu için uygun olan genel Azure hizmetlerini ve özelliklerini kapsayacak şekilde hazırlanmıştır. Bu kılavuz, tüm olası yapılandırma seçeneklerinin ayrıntılı bir listesi değildir. Tipik dağıtım senaryolarında ortak olan çözümleri ele alır.

Bu kılavuz, standart SAP BOBI platformu yükleme ve yönetim kılavuzlarını, işletim sistemini veya herhangi bir veritabanı belgesini değiştirme amacını taşımamaktadır.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Azure 'da SAP BusinessObjects bı platformunu planlayın ve uygulayın

Microsoft Azure, bilgi işlem, depolama, ağ ve diğer pek çok farklı hizmet yelpazesi sunarak işletmelerin uygulamalarını uzun tedarik döngüleri olmadan oluşturmasını sağlar. Azure sanal makineler (VM), iş gereksinimlerine göre SAP NetWeaver tabanlı uygulamalar, SAP Hybrin, SAP BusinessObjects bı platformu gibi farklı SAP uygulamaları için isteğe bağlı ve ölçeklenebilir bilgi işlem kaynaklarını dağıtmalarına yardımcı olur. Azure, şirketlerin Azure sanal makinelerini şirket içi etki alanları, özel bulutları ve SAP sistem yatağuna tümleştirmelerini sağlayan şirketler arası bağlantıyı da destekler.

Bu belge, Azure 'da SAP BusinessObjects bı platformunda planlama ve uygulama konusunda rehberlik sağlar. SAP BOBI 'nin yüklemelerine ve dağıtımına yönelik birincil kaynakları temsil eden SAP yükleme belgelerini ve SAP notlarını tamamlar.

### <a name="architecture-overview"></a>Mimariye genel bakış

SAP BusinessObjects bı platformu, tek bir Azure sanal makinesinde bulunabilecek veya farklı bileşenler çalıştıran birçok Azure sanal makinesi kümesine ölçeklendirilebilir olan, kendi kendine kapsanan bir sistemdir. SAP BOBI platformu altı kavramsal katmanlardan oluşur: Istemci katmanı, Web katmanı, yönetim katmanı, depolama katmanı, Işleme katmanı ve veri katmanı. (Her bir katman hakkında daha fazla bilgi için, [SAP BusinessObjects Business Intelligence platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) yardım portalında Yönetici Kılavuzu ' na bakın). Her katmandaki üst düzey ayrıntılar aşağıda verilmiştir:

- **Istemci katmanı:** Farklı türde raporlama, analitik ve yönetim özellikleri sağlamak için bı platformuyla etkileşime geçen tüm masaüstü istemci uygulamalarını içerir.
- **Web katmanı:** JAVA Web uygulaması sunucularına dağıtılan Web uygulamalarını içerir. Web uygulamaları, son kullanıcılara bir Web tarayıcısı aracılığıyla bı platform işlevselliği sağlar.
- **Yönetim katmanı:** Bı platformunu oluşturan tüm bileşenleri koordine eder ve denetler. Merkezi yönetim sunucusu (CMS) ve olay sunucusu ile ilişkili hizmetleri içerir
- **Depolama katmanı:** Belgeler ve raporlar gibi dosyaları işlemekten sorumludur. Ayrıca, Kullanıcı raporlara erişirken sistem kaynaklarını kaydetmek için rapor önbelleğe alma işlemini de gerçekleştirir.
- **Işlem katmanı:** Verileri analiz eder ve raporlar ve diğer çıkış türlerini üretir. Rapor verilerini içeren veritabanlarına erişen tek katmandır.
- **Veri katmanı:** CMS sistem veritabanlarını ve denetim veri deposunu barındıran veritabanı sunucularından oluşur.

SAP BI platformu, bir veya daha fazla konakta çalışan bir sunucu koleksiyonundan oluşur. Boyutlandırma, iş ihtiyacı ve ortam türü temelinde doğru dağıtım stratejisini seçmeniz önemlidir. Geliştirme veya test gibi küçük yüklemede, Web uygulaması sunucusu, veritabanı sunucusu ve tüm bı platform sunucuları için tek bir Azure sanal makinesini kullanabilirsiniz. Azure 'da hizmet olarak veritabanı (DBaaS) teklifi kullanıyorsanız, veritabanı sunucusu diğer bileşenlerden ayrı olarak çalışacaktır. Orta ve büyük yükleme için, birden çok Azure sanal makinesi üzerinde çalışan sunuculara sahip olabilirsiniz.

Aşağıdaki şekilde, Azure sanal makinelerinde SAP BOBI platformunun büyük ölçekli dağıtımı mimarisi, her bileşenin dağıtıldığı ve hizmet kesintisi olması durumunda yük devretmeye yönelik kullanılabilirlik kümelerine yerleştirildiği bir şekilde gösterilmiştir.

![Azure 'da SAP BusinessObjects bı platformu mimarisi](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Mimari ayrıntıları

- Yük dengeleyici

  SAP BOBI çok örnekli dağıtım sürümünde, Web uygulaması sunucuları (veya Web Katmanı) iki veya daha fazla konakta çalışır. Kullanıcı yükünü Web sunucuları arasında eşit bir şekilde dağıtmak için, son kullanıcılar ve Web sunucuları arasında yük dengeleyici kullanabilirsiniz. Azure 'da, web sunucularınızda trafiği yönetmek için [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) veya [Azure Application Gateway](../../../application-gateway/overview.md) kullanabilirsiniz.

- Web uygulaması sunucuları

  Web sunucusu, CMC ve bı başlatma paneli gibi SAP BOBI platformunun Web uygulamalarını barındırır. Web sunucusu için yüksek kullanılabilirlik elde etmek için, artıklık ve yük dengelemeyi yönetmek üzere en az iki Web uygulaması sunucusu dağıtmanız gerekir. Azure 'da, bu Web uygulaması sunucuları daha iyi kullanılabilirlik için kullanılabilirlik kümelerine veya kullanılabilirlik bölgelerine yerleştirilebilir.

  Tomcat, SAP BI platformu için varsayılan Web uygulamasıdır. Tomcat için yüksek kullanılabilirlik elde etmek üzere Azure 'da [statik üyelik yakalayıcısını](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) kullanarak oturum çoğaltmasını etkinleştirin. Kullanıcıların, Tomcat hizmeti kesintiye uğrasa bile SAP BI Web uygulamasına erişebilmesini sağlar.

  > [!Important]
  > Varsayılan olarak, Tomcat, Azure 'da desteklenmeyen kümeleme için çok noktaya yayın IP ve bağlantı noktası kullanır (SAP Note [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- Bı platform sunucuları

  Bı platform sunucuları SAP BOBI uygulamasının parçası olan tüm hizmetleri (Yönetim katmanı, işleme katmanı ve depolama katmanı) içerir. Bir Web sunucusu bir istek aldığında, her bir bı platform sunucusunu (özellikle, bir kümedeki tüm CMS sunucuları) algılar ve isteklerinin otomatik olarak yük dengelenmesi gerekir. Bı platformunun bir üyesi başarısız olursa, Web sunucusu istekleri otomatik olarak diğer ana bilgisayarlara gönderir.

  Bı platformu için yüksek kullanılabilirlik veya artıklık sağlamak üzere uygulamayı en az iki Azure sanal makinesine dağıtmanız gerekir. Boyutlandırma temelinde, bı platformunuzu daha fazla Azure sanal makinesi üzerinde çalışacak şekilde ölçeklendirebilirsiniz.

- Dosya deposu sunucusu (FRS)

  Dosya deposu sunucusu, tüm raporları ve oluşturulan diğer bı belgelerini içerir. Çok örnekli dağıtımda, bı platform sunucuları birden çok sanal makinede çalışır ve her VM bu raporlara ve diğer bı belgelerine erişebilmelidir. Bu nedenle, bir FileSystem 'ın tüm bı platform sunucuları genelinde paylaşılması gerekir.

  Azure 'da, [Azure Premium dosyalarını](../../../storage/files/storage-files-introduction.md) ya da dosya deposu sunucusu için [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) kullanabilirsiniz. Bu Azure hizmetlerinin her ikisinde de yerleşik yedeklilik vardır.

  > [!Important]
  > Azure dosyaları için SMB protokolü genel kullanıma sunulmuştur, ancak Azure dosyaları için NFS protokol desteği şu anda önizleme aşamasındadır. Daha fazla bilgi için bkz. [Azure dosyaları Için NFS 4,1 desteği artık önizlemededir](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- CMS & denetim veritabanı
  
  SAP BOBI platformu, CMS veritabanı olarak adlandırılan sistem verilerini depolamak için bir veritabanı gerektirir. Kullanıcı, sunucu, klasör, belge, yapılandırma ve kimlik doğrulama ayrıntıları gibi bı platformu bilgilerini depolamak için kullanılır.

  Azure, CMS veritabanı ve denetim veritabanı için kullanılabilen [MySQL veritabanı](https://azure.microsoft.com/en-us/services/mysql/) ve [Azure SQL veritabanı](https://azure.microsoft.com/en-us/services/sql-database/) hizmet olarak veritabanı (DBaaS) teklifi sunmaktadır. Bu bir PaaS sunumu olduğu için müşterilerin, veritabanlarının çalışması, kullanılabilirliği ve bakımı konusunda endişelenmek zorunda kalmaz. Müşteri, kendi iş ihtiyaçları temelinde CMS ve denetim deposu için kendi veritabanını da seçebilir.

## <a name="support-matrix"></a>Destek matrisi

Bu bölümde, SAP BusinessObjects bı platform sürümü, Işletim sistemi ve Azure 'da veritabanları gibi farklı SAP BOBI bileşeninin desteklenebilirliği açıklanmaktadır.

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects bı platformu

Hizmet olarak Azure altyapısı (IaaS), Azure Işlem 'da SAP BusinessObjects bı platformunu dağıtmanıza ve yapılandırmanıza olanak sağlar. SAP BOBı platformunun aşağıdaki sürümünü destekler-

- SAP BusinessObjects bı platformu 4,3
- SAP BusinessObjects bı platformu 4,2 SP04 +
- SAP BusinessObjects bı platformu 4,1 SP05 +

SAP BI platformu farklı işletim sistemi ve veritabanlarında çalışır. Işletim sistemi ve veritabanı sürümü arasında SAP BOBI platformunun desteklenebilirliği, SAP BOBI için [ürün kullanılabilirliği matrisinde](https://apps.support.sap.com/sap/support/pam) bulunabilir.

### <a name="operating-system"></a>İşletim sistemi

Azure, SAP BusinessObjects bı platform dağıtımı için aşağıdaki işletim sistemlerini destekler.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

[SAP BUSINESSOBJECTS bı platformu Için ürün kullanılabilirlik matrisi (Pam)](https://support.sap.com/pam) bölümünde listelenen işletim sistemi sürümü, Azure altyapısında çalışmak üzere uyumlu oldukları sürece desteklenmektedir.

### <a name="databases"></a>Veritabanları

Bı platformunda CMS ve denetim veri deposu için veritabanı gerekir ve bu, [SAP ürün kullanılabilirliği matrisinde](https://support.sap.com/pam) listelenen desteklenen veritabanlarına yüklenebilen, aşağıdakileri içeren

- Microsoft SQL Server

- [Azure SQL veritabanı](https://azure.microsoft.com/en-us/services/sql-database/) (yalnızca WINDOWS 'DA SAP bobi platformu için desteklenen veritabanı)

  Bu, SQL Server en son kararlı kurumsal sürümüne bağlı olarak, tam olarak yönetilen bir SQL Server veritabanı altyapısıdır. Azure SQL veritabanı, Kullanıcı katılımı olmadan yükseltme, düzeltme eki uygulama ve izleme gibi veritabanı yönetim işlevlerinin çoğunu işler. Azure SQL Veritabanı ile Azure'da çalışan uygulamalar ve çözümler için yüksek oranda kullanılabilir ve yüksek performanslı bir veri depolama katmanı oluşturabilirsiniz. Daha fazla ayrıntı için [Azure SQL veritabanı](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) belgelerini inceleyin.

- [MySQL Için Azure veritabanı](https://azure.microsoft.com/en-us/services/mysql/) (SAP Pam 'de MySQL AB için belirtilen uyumluluk kılavuzlarını izleyin)

  Bu, MySQL Community Edition tarafından desteklenen ilişkisel bir veritabanı hizmetidir. Tam olarak yönetilen bir hizmet olarak veritabanı (DBaaS) teklifi olmak üzere, tahmin edilebilir performans ve dinamik ölçeklenebilirlik ile görev açısından kritik iş yüklerini işleyebilir. Yerleşik yüksek kullanılabilirlik, otomatik yedeklemeler, yazılım düzeltme eki uygulama, otomatik hata algılama ve zaman içinde işlem görevlerini önemli ölçüde azaltan 35 güne kadar bir noktadan noktaya geri yükleme vardır. Daha fazla ayrıntı için [MySQL Için Azure veritabanı](../../../mysql/overview.md) belgelerini inceleyin.

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (sürüm ve kısıtlama Için SAP notuna bakın [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

Bu belgede, **Azure SQL veritabanı Ile Windows üzerinde SAP bobi platformu** dağıtmaya yönelik yönergeler ve **Linux üzerinde MySQL için Azure veritabanı ile SAP bobi platformu** dağıtımı gösterilmektedir. Azure 'da SAP BusinessObjects bı platformunu çalıştırmak için de önerilen yaklaşımımız.

## <a name="sizing"></a>Boyutlandırma

Boyutlandırma, uygulamayı verimli bir şekilde çalıştırmak için donanım gereksinimini belirleme işlemidir. SAP BOBI platformu için, [hızlı sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)adlı SAP boyutlandırma aracı kullanılarak boyutlandırmanın yapılması gerekir. Araç, girişi temel alan SAPS 'yi sağlar ve daha sonra SAP için sertifikalı Azure sanal makine türleri ile eşlenmelidir. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) , desteklenen SAP ürünlerinin ve Azure VM türlerinin yanı sıra SAPS 'lerin listesini sağlar. Boyutlandırma hakkında daha fazla bilgi için [sap bı boyutlandırma Kılavuzu](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons)' na bakın.

SAP BOBI platformu için depolama gereksinimi için Azure, farklı türlerde [yönetilen diskler](../../managed-disks-overview.md)sunmaktadır. SAP BOBI yükleme dizininde, Premium yönetilen disk ve sanal makinelerde çalışan veritabanı için kullanılması önerilir, [SAP iş yükü Için DBMS dağıtımında](dbms_guide_general.md)belirtilen yönergeleri izleyin.

Azure, SAP BOBI platformu veri katmanı- [Azure SQL veritabanı](https://azure.microsoft.com/en-us/services/sql-database) (Windows üzerinde çalışan bı uygulaması) ve [MySQL için Azure veritabanı](https://azure.microsoft.com/en-us/services/mysql) (Linux ve Windows üzerinde çalışan bı uygulaması) Için iki DBaaS sunumunu destekler. Bu nedenle, boyutlandırma sonucuna bağlı olarak, gereksinimlerinize en uygun satın alma modelini seçebilirsiniz.

> [!Tip]
> Hızlı boyutlandırma başvurusu için, SAP BOBI platformu veritabanı katmanının SAPS sonucunu Azure hizmet olarak veritabanı (Azure SQL veritabanı veya MySQL için Azure veritabanı) ile eşleştirirken 800 SAPS = 1 vCPU göz önünde bulundurun.

### <a name="sizing-models-for-azure-sql-database"></a>Azure SQL veritabanı için boyutlandırma modelleri

Azure SQL veritabanı aşağıdaki üç satın alma modelini sunmaktadır:

- Sanal çekirdek tabanlı

  Sanal çekirdek sayısını, bellek miktarını ve depolama alanı miktarını ve hızını seçmenizi sağlar. Sanal çekirdek tabanlı satın alma modeli, maliyet tasarrufu kazanmak için [SQL Server Azure hibrit avantajı](https://azure.microsoft.com/pricing/hybrid-benefit/) kullanmanıza da olanak tanır. Bu model esneklik, denetim ve saydamlık değeri olan müşteriler için uygundur.

  VCore modelinde,-Genel Amaçlı, İş Açısından Kritik ve Hyperscale içeren üç [hizmet katmanı seçeneği](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) sunulur. Hizmet katmanı, kullanılabilirlik ve olağanüstü durum kurtarma ile ilgili depolama mimarisini, boşluk, g/ç sınırlarını ve iş sürekliliği seçeneklerini tanımlar. Her bir hizmet katmanı seçeneği için üst düzey ayrıntılar aşağıda verilmiştir.

  1. **Genel amaçlı** hizmet katmanı iş yükleri için idealdir. Bütçeye dayalı, dengeli ve ölçeklenebilir işlem ve depolama seçenekleri sunar. Daha fazla bilgi için [kaynak seçenekleri ve sınırları](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)bölümüne bakın.
  2. **İş açısından kritik** hizmet katmanı, çeşitli yalıtılmış çoğaltmalar kullanarak en yüksek esnekliği ve başarısız olan ve veritabanı çoğaltması başına en yüksek g/ç performansını sağlayan iş uygulamaları sunar. Daha fazla bilgi için [kaynak seçenekleri ve sınırları](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)bölümüne bakın.
  3. **Hiper ölçek** hizmeti katmanı, yüksek düzeyde ölçeklenebilir depolama ve okuma ölçeği gereksinimlerine sahip iş yükleri için idealdir. Birden fazla yalıtılmış veritabanı çoğaltmasının yapılandırılmasına izin vererek daha yüksek esnekliği hatalara olanak sağlar. Daha fazla bilgi için [kaynak seçenekleri ve sınırları](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)bölümüne bakın.

- DTU tabanlı

  DTU tabanlı satın alma modeli, hafif ve ağır veritabanı iş yüklerini desteklemek için üç hizmet katmanında işlem, bellek ve g/ç kaynaklarının bir karışımını sunar. Her katmandaki işlem boyutları bu kaynakların farklı bir karışımını sağlar ve bu da ek depolama kaynakları ekleyebilirsiniz. Basit, önceden yapılandırma kaynak seçeneklerini isteyen müşteriler için idealdir.

  DTU tabanlı satın alma modelindeki [hizmet katmanları](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) , sabit bir dahil edilen depolama alanı, yedekleme süresi ve sabit fiyat içeren bir dizi işlem boyutuna göre farklılaştırılır.

- Sunucusuz

  Sunucusuz model, iş yükü talebine göre işlem sayısını otomatik olarak ölçeklendirir ve saniye başına kullanılan işlem miktarına göre faturalandırılır. Sunucusuz işlem katmanı, yalnızca depolama faturalandırılırken, etkin olmayan dönemler sırasında veritabanlarını otomatik olarak duraklatır ve etkinlik döndüğünde veritabanlarını otomatik olarak sürdürür. Daha fazla bilgi için [kaynak seçenekleri ve sınırları](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)bölümüne bakın.
  
  Zaman içinde düşük ortalama işlem kullanımı ile aralıklı, öngörülemeyen kullanım için daha uygundur. Bu nedenle, bu model üretim dışı SAP BOBI dağıtımı için kullanılabilir.

> [!Note]
> SAP BOBI için, sanal çekirdek tabanlı model kullanmak ve Genel Amaçlı veya İş Açısından Kritik hizmet katmanını iş gereksinimlerine göre seçmek yararlıdır.

### <a name="sizing-models-for-azure-database-for-mysql"></a>MySQL için Azure veritabanı için boyutlandırma modelleri

MySQL için Azure veritabanı, üç farklı fiyatlandırma katmanlarıyla birlikte gelir. Bunlar, sanal çekirdekler, sanal çekirdek başına bellek ve tarihi depolamak için kullanılan depolama teknolojisi miktarına göre farklılaştırırlar. Seçenekler hakkında üst düzey ayrıntılar aşağıda verilmiştir ve farklı özniteliklerle ilgili daha fazla ayrıntı için MySQL için Azure veritabanı [fiyatlandırma katmanına](../../../mysql/concepts-pricing-tiers.md) bakın.

- Temel

  Hafif işlem ve g/ç performansı gerektiren hedef iş yükleri için kullanılır.

- Genel Amaçlı

  Ölçeklenebilir g/ç aktarım hızı ile dengeli işlem ve bellek gerektiren iş yüklerinin çoğu için uygundur.

- Bellek İçin İyileştirilmiş

  Daha hızlı işlem işleme ve daha yüksek eşzamanlılık için bellek içi performans gerektiren yüksek performanslı veritabanı iş yükleri için.

> [!Note]
> SAP BOBI için, iş yüküne göre Genel Amaçlı veya bellek için Iyileştirilmiş fiyatlandırma katmanını kullanmak uygun değildir.

## <a name="azure-resources"></a>Azure kaynakları

### <a name="choosing-regions"></a>Bölgeleri seçme

Azure bölgesi, çalıştırılacak altyapıyı içeren bir veya bir veri merkezi koleksiyonudur ve farklı Azure hizmetlerini barındırır. Bu altyapı, işlem düğümleri veya depolama düğümleri olarak işlev gösteren çok sayıda düğüm içerir veya ağ işlevselliği çalıştırır. Tüm bölgeler aynı hizmetleri sunmaz.

SAP BI platformu, belirli bölgelerde kullanılamayan, veri katmanı için Azure dosyaları veya Azure NetApp Files ya da hizmet olarak veritabanı (DBaaS) gibi belirli sanal makine türleri gerektirebilecek farklı bileşenler içerir. VM türleriyle ilgili tam bilgileri, Azure depolama türlerini veya diğer Azure hizmetlerini [bölge sitesi tarafından kullanılabilir](https://azure.microsoft.com/en-us/global-infrastructure/services/) şekilde bulabilirsiniz. SAP sistemlerinizi zaten Azure üzerinde çalıştırıyorsanız, büyük olasılıkla bölgeniz tanımlamış olursunuz. Bu durumda, SAP BI platformunun mimarisine karar vermek için öncelikle gerekli hizmetlerin bu bölgelerde kullanılabilir olduğunu araştırmanız gerekir.

### <a name="availability-zones"></a>Kullanılabilirlik alanları

Kullanılabilirlik Alanları bir Azure bölgesindeki fiziksel konumlardan farklıdır. Her kullanılabilirlik alanı, bağımsız güç, soğutma ve ağ ile donatılmış bir veya daha fazla veri merkezinden oluşur.

Her SAP BI platformu için bir katmanda yüksek kullanılabilirlik elde etmek için, Azure 'da en iyi SLA sağlayabilen yüksek kullanılabilirlik çerçevesini uygulayarak VM 'Leri kullanılabilirlik bölgesi arasında dağıtabilirsiniz. Azure 'daki sanal makine SLA 'Sı için, [sanal makine](https://azure.microsoft.com/support/legal/sla/virtual-machines/)SLA 'larının en son sürümünü denetleyin.

Veri katmanı için, hizmet olarak Azure veritabanı (DBaaS) hizmeti, varsayılan olarak yüksek kullanılabilirlik çerçevesi sağlar. Tüm ek bileşenleri yapılandırmanıza gerek kalmadan, planlı ve plansız kesintilerden oluşan veritabanı kapalı kalma süresini azaltmak için yalnızca bölge ve hizmete devralınan yüksek kullanılabilirlik, artıklık ve dayanıklılık özelliklerini seçmeniz yeterlidir. Azure 'da desteklenen DBaaS teklifi için SLA hakkında daha fazla ayrıntı için Azure [veritabanı 'Nda MySQL Için Azure veritabanı](../../../mysql/concepts-high-availability.md) ve [Azure SQL veritabanı için yüksek kullanılabilirlik](../../../azure-sql/database/high-availability-sla.md)bilgilerini inceleyin.

### <a name="availability-sets"></a>Kullanılabilirlik kümeleri

Kullanılabilirlik kümesi, dağıtılan sanal makine (VM) kaynaklarını yalıtmak için bir mantıksal gruplama özelliğidir. Azure, bir kullanılabilirlik kümesi içinde yerleştirdiğiniz VM 'lerin birden çok fiziksel sunucuda, bilgi işlem raflarında, depolama birimlerinde ve ağ anahtarlarında çalıştırılmasını sağlar. Bir donanım veya yazılım hatası oluşursa, sanal makinelerinizin yalnızca bir alt kümesi etkilenir ve genel çözümünüz çalışır durumda kalır. Bu nedenle, sanal makineler kullanılabilirlik kümelerine yerleştirildiğinde, Azure yapı denetleyicisi VM 'Leri farklı [hata](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) ve [yükseltme](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) etki alanlarına dağıtarak, altyapı bakımı veya bir hata etki alanı Içindeki başarısızlık nedeniyle tüm VM 'lerin erişilemez olmasını önler.

SAP BI platformu birçok farklı bileşen içerir ve mimaride, bu bileşenin her birinin kesintiye dayanıklı olduğundan emin olmanız gerekir. Kullanılabilirlik kümeleri içindeki her bir bileşene ait Azure sanal makineleri yerleştirilerek elde edilebilir. Tek bir kullanılabilirlik kümesi içinde farklı VM ailelerinin sanal makinelerini karıştıradığınızda, bu tür kullanılabilirlik kümesine belirli bir sanal makine türü dahil etmeniz için sorunlarla karşılaşabilirsiniz. Bu nedenle, mimariye genel bakış bölümünde vurgulanan şekilde, Web uygulaması için ayrı kullanılabilirlik kümesine, SAP BI platformu için bı uygulamasına sahiptir.

Ayrıca, Azure ölçek birimi içindeki bir Azure kullanılabilirlik kümesi tarafından kullanılabilen güncelleştirme ve hata etki alanlarının sayısı sınırlıdır. Bu nedenle, VM 'Leri tek bir kullanılabilirlik kümesine eklemeye devam ederseniz, iki veya daha fazla VM sonunda aynı hata veya güncelleştirme etki alanında sona bırakılır. Daha fazla bilgi için bkz. SAP için Azure sanal makineler planlama ve uygulama belgesi 'nin [Azure kullanılabilirlik kümeleri](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) bölümü.

Azure kullanılabilirlik kümeleri kavramını ve kullanılabilirlik kümelerinin hata ve yükseltme etki alanlarıyla ilişkisini anlamak için [kullanılabilirliği Yönet](../../availability.md) makalesini okuyun.

> [!Important]
> Azure Kullanılabilirlik Alanları ve Azure kullanılabilirlik kümelerinin kavramları birbirini dışlıyor. Yani, bir çift veya birden çok VM 'yi belirli bir kullanılabilirlik alanına veya Azure kullanılabilirlik kümesine dağıtabileceğiniz anlamına gelir. Ancak ikisini birden değil.

### <a name="virtual-machines"></a>Sanal makineler

Azure sanal makinesi, Azure 'da hizmet olarak altyapı (IaaS) örnekleri olarak özel görüntüler dağıtmanıza olanak sağlayan bir hizmet sunumudur. Web uygulamasını ve bağlı uygulamaları barındırmak, ölçeklendirmek ve yönetmek için isteğe bağlı bilgi işlem ve depolama sağlayarak uygulamaları sürdürmeyi ve işletim sistemlerini basitleştirir.

Azure, tüm uygulama gereksinimleriniz için çeşitli sanal makineler sunar. Ancak, Azure SAP iş yükü için seçimi, SAP iş yükü ve SAP HANA iş yükü için uygun olan farklı VM ailelerinde daha az bir şekilde kullanıma aldı. Daha ayrıntılı bilgi için [Azure dağıtımları için desteklenen SAP yazılımlarını](sap-supported-product-on-azure.md)denetleyin.

SAP BI platformu boyutlandırılmasına bağlı olarak, Azure for SAP ürününde desteklenen Azure sanal makinesine gereksiniminizi eşlemeniz gerekir. SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533) , Windows ve LINUX 'Ta sap ürünleri Için desteklenen Azure VM türlerini listenin iyi bir başlangıç noktasıdır. Ayrıca, yalnızca desteklenen VM türleri seçiminin ötesinde, bu VM türlerinin belirli bir bölgede kullanılıp kullanılamayacağını denetlemeniz gereken bir nokta de vardır. [Bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/) sayfasında VM türünün kullanılabilirliğini kontrol edebilirsiniz. Fiyatlandırma modelini seçmek için, [SAP iş yükü Için Azure sanal makinelerine](planning-guide.md#azure-virtual-machines-for-sap-workload) başvurabilirsiniz

### <a name="storage"></a>Depolama

Azure depolama, yüksek oranda kullanılabilir, güvenli, dayanıklı, ölçeklenebilir ve yedekli depolama sağlayan, Azure tarafından yönetilen bir bulut hizmetidir. Bazı depolama türlerinin SAP senaryoları için sınırlı kullanımı vardır. Ancak birkaç Azure depolama türü, belirli SAP iş yükü senaryoları için uygundur veya iyileştirilmiştir. Daha fazla bilgi için SAP için uygun olan farklı depolama seçeneklerini vurgulamaları için [SAP Iş yükü Kılavuzu Için Azure Depolama türleri](planning-guide-storage.md) ' ne bakın.

Azure depolama 'nın müşteriler ve Ayrıntılar için kullanılabilir farklı depolama türleri, [Azure 'Da hangi disk türlerinin kullanılabildiği](../../disks-types.md)makalesinde okunabilir. SAP BOBI platformu uygulamayı derlemek için aşağıdaki Azure Storage 'ı kullanır-

- Azure tarafından yönetilen diskler

  Bu, Azure tarafından yönetilen blok düzeyinde bir depolama birimidir. Azure sanal makinelerinde yüklü olduğunda SAP BOBI platformu uygulama sunucuları ve veritabanları için diskleri kullanabilirsiniz. Farklı türlerde [Azure yönetilen diskler](../../managed-disks-overview.md) mevcuttur, ancak SAP bobi Platform uygulaması ve veritabanı Için [Premium SSD](../../disks-types.md#premium-ssd) 'lerin kullanılması önerilir.

  Aşağıdaki örnekte, BOBI platformu yükleme dizini için Premium SSD 'ler kullanılır. Sanal makinede yüklü olan veritabanı için, yönetim disklerini veri ve günlük hacimlerinin yönergelerine göre kullanabilirsiniz. CMS ve denetim veritabanları genellikle küçüktür ve diğer SAP OLTP/OLAP veritabanlarıyla aynı depolama performans gereksinimlerine sahip değildir.

- Azure Premium dosyaları veya Azure NetApp Files

  SAP BOBI platformunda, dosya deposu sunucusu (FRS), bu sistemin tüm uygulama sunucuları tarafından kullanılan raporlar, üniveruygulamalar ve bağlantılar gibi içeriklerin depolandığı disk dizinlerine başvurur. [Azure Premium dosyaları](../../../storage/files/storage-files-introduction.md) veya [Azure NetApp FILES](../../../azure-netapp-files/azure-netapp-files-introduction.md) depolama, SAP bobi uygulamaları için paylaşılan bir dosya sistemi olarak kullanılabilir. Bu depolama teklifi tüm bölgelerde kullanılabilir olmadığından, güncel bilgileri öğrenmek için [bölgeye göre kullanılabilir ürünler](https://azure.microsoft.com/en-us/global-infrastructure/services/) bölümüne bakın.

  Hizmet bölgenizde yoksa, dosya sistemini SAP BOBı uygulamasına paylaşabileceğiniz NFS sunucusu oluşturabilirsiniz. Ancak, yüksek oranda kullanılabilirliğini de göz önünde bulundurmanız gerekir.

![Azure 'da SAP BusinessObjects bı platform depolama düzeni](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Ağ

SAP BOBI, hiçbir iş verisi tutan bir raporlama ve analiz bı platformudur. Bu nedenle, sistem tüm verileri getiren ve kullanıcılara Öngörüler sağlayan diğer veritabanı sunucularına bağlanır. Azure, şirket içi sisteme bağlanma gibi SAP BI platformunda, farklı sanal ağdaki sistemlerle ve diğer kullanıcılarla bağlantı kurulmasına olanak sağlayan bir ağ altyapısı sağlar. Daha fazla bilgi için [SAP Iş yüküne yönelik Microsoft Azure ağı](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)' na bakın.

Hizmet olarak veritabanı teklifi için, tüm yeni oluşturulan veritabanları (Azure SQL veritabanı veya MySQL için Azure veritabanı) tüm dış bağlantıları engelleyen bir güvenlik duvarına sahiptir. Bı platformu sanal makinelerinden DBaaS hizmetine erişime izin vermek için, DBaaS sunucunuza erişimi etkinleştirmek üzere bir veya daha fazla sunucu düzeyi güvenlik duvarı kuralı belirtmeniz gerekir. Daha fazla bilgi için bkz. MySQL için Azure veritabanı ve Azure SQL veritabanı için [ağ erişim denetimleri](../../../azure-sql/database/network-access-controls-overview.md) bölümüne yönelik [güvenlik duvarı kuralları](../../../mysql/concepts-firewall-rules.md) .

## <a name="next-steps"></a>Sonraki adımlar

- [Linux üzerinde SAP BusinessObjects bı platform dağıtımı](businessobjects-deployment-guide-linux.md)
- [SAP için Azure sanal makineleri planlama ve uygulama](planning-guide.md)
- [SAP için Azure sanal makineleri dağıtımı](deployment-guide.md)
- [SAP için Azure sanal makineleri DBMS dağıtımı](./dbms_guide_general.md)