---
title: VM 'lerde uygulama desenleri SQL Server | Microsoft Docs
description: Bu makalede, Azure VM 'lerinde SQL Server yönelik uygulama desenleri ele alınmaktadır. Çözüm mimarları ve geliştiricilere, iyi uygulama mimarisi ve tasarımı için bir temel sağlar.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 41863c8d-f3a3-4584-ad86-b95094365e05
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mathoma
ms.openlocfilehash: cbc2bfbb68910c3eb12352bebb575c4548885a24
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124022"
---
# <a name="application-patterns-and-development-strategies-for-sql-server-in-azure-virtual-machines"></a>Azure Sanal Makineler'de SQL Server için Uygulama Desenleri ve Geliştirme Stratejileri
[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="summary"></a>Özet:
Azure ortamındaki SQL Server tabanlı uygulamalarınız için hangi uygulama deseninin veya desenlerinin kullanılacağını belirlemek önemli bir tasarım kararına sahiptir ve Azure 'un her bir altyapı bileşeni birlikte nasıl çalışır? SQL Server hakkında katı bir anlamayı gerektirir. Azure altyapı hizmetlerinde SQL Server sayesinde mevcut SQL Server uygulamalarınızı yerleşik Windows Server ile Azure 'daki sanal makinelere kolayca geçirebilir, bakımını yapabilir ve izleyebilirsiniz.

Bu makalenin amacı, çözüm mimarları ve geliştiricilere, mevcut uygulamaları Azure 'a geçirirken ve Azure 'da yeni uygulamalar geliştirirken izleyebilecekleri iyi uygulama mimarisi ve tasarımı için bir temel sağlamaktır.

Her uygulama için bir şirket içi senaryo, ilgili bulut özellikli çözümü ve ilgili teknik önerileri bulacaksınız. Ayrıca, bu makalede, uygulamalarınızı doğru bir şekilde tasarlayabilmeniz için Azure 'a özgü geliştirme stratejileri ele alınmaktadır. Olası birçok uygulama deseni nedeniyle, mimarların ve geliştiricilerin, uygulamaları ve kullanıcıları için en uygun deseni seçmesi önerilir.

**Teknik katkıda bulunanlar:** Lusıs Carlos Vargaz Herhalka, Madhan Arumugam Oymakrishnan

**Teknik gözden geçirenler:** Corey Sanders, Çizidaniel, Narayan Annamalai, nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## <a name="introduction"></a>Giriş
Farklı makinelerdeki farklı uygulama katmanlarının bileşenlerini ve ayrı bileşenlere ayırarak birçok n katmanlı uygulama türü geliştirebilirsiniz. Örneğin, istemci uygulaması ve iş kuralları bileşenlerini bir makineye, ön uç Web katmanına ve veri erişim katmanı bileşenlerine başka bir makineye ve başka bir makinedeki arka uç veritabanı katmanına yerleştirebilirsiniz. Bu tür bir yapılandırma, her bir katmanın birbirlerinden yalıtılmasına yardımcı olur. Verilerin nereden geldiğini değiştirirseniz, istemci veya Web uygulamasını değiştirmeniz gerekmez, ancak yalnızca veri erişim katmanı bileşenlerini değiştirmelisiniz.

Tipik *n katmanlı* bir uygulama sunum katmanını, iş katmanını ve veri katmanını içerir:

| Katman | Açıklama |
| --- | --- |
| **Sununuzda** |*Sunu katmanı* (Web katmanı, ön uç katmanı), kullanıcıların bir uygulamayla etkileşimde bulunduğu katmandır. |
| **İş** |*İş katmanı* (orta katman), sunum katmanının ve veri katmanının birbirleriyle iletişim kurmak için kullandığı katmandır ve sistemin temel işlevlerini içerir. |
| **Veri** |*Veri katmanı* temel olarak bir uygulamanın verilerini depolayan bir sunucu (örneğin, SQL Server çalıştıran bir sunucu). |

Uygulama katmanları, bir uygulamadaki işlevlerin ve bileşenlerin mantıksal gruplandırmaları anlatmaktadır; Katmanlar, işlevlerin ve bileşenlerin ayrı fiziksel sunucular, bilgisayarlar, ağlar veya uzak konumlarda fiziksel dağıtımını anlatmaktadır. Bir uygulamanın katmanları aynı fiziksel bilgisayarda (aynı katman) bulunabilir veya ayrı bilgisayarlara (n-tier) dağılmış olabilir ve her katmandaki bileşenler, iyi tanımlanmış arabirimler aracılığıyla diğer katmanlardaki bileşenlerle iletişim kurar. Terim katmanını, iki katmanlı, üç katmanlı ve n katmanlı gibi fiziksel dağıtım düzenlerine başvurarak düşünebilirsiniz. **2 katmanlı uygulama deseninin** iki uygulama katmanı vardır: uygulama sunucusu ve veritabanı sunucusu. Doğrudan iletişim, uygulama sunucusu ve veritabanı sunucusu arasında gerçekleşir. Uygulama sunucusu hem Web katmanı hem de iş katmanı bileşenlerini içerir. **3 katmanlı uygulama**düzeninde üç uygulama katmanı vardır: Web sunucusu, iş mantığı katmanını ve/veya iş katmanı veri erişimi bileşenlerini ve veritabanı sunucusunu içeren uygulama sunucusu. Web sunucusu ile veritabanı sunucusu arasındaki iletişim uygulama sunucusu üzerinden gerçekleşir. Uygulama katmanları ve katmanları hakkında ayrıntılı bilgi için bkz. [Microsoft uygulama mimarisi Kılavuzu](https://msdn.microsoft.com/library/ff650706.aspx).

Bu makaleyi okumaya başlamadan önce, SQL Server ve Azure 'un temel kavramları hakkında bilgi sahibi olmanız gerekir. Daha fazla bilgi için bkz. [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [Azure sanal makineler 'de SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) ve [Azure.com](https://azure.microsoft.com/).

Bu makalede, basit uygulamalarınızın yanı sıra son derece karmaşık kurumsal uygulamalar için uygun olabilecek çeşitli uygulama desenleri açıklanmaktadır. Her bir modele göre ayrıntıdan önce, Azure [depolama](../../../storage/common/storage-introduction.md), [Azure SQL veritabanı](../../../sql-database/sql-database-technical-overview.md)ve [Azure sanal makinesinde SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)gibi Azure 'daki kullanılabilir veri depolama hizmetlerini öğrenmeniz önerilir. Uygulamalarınız için en iyi tasarım kararları almak için hangi veri depolama hizmetini açık olarak kullanacağınızı anlayın.

### <a name="choose-sql-server-in-an-azure-virtual-machine-when"></a>Azure sanal makinesinde SQL Server seçin, ne zaman:
* SQL Server ve Windows üzerinde denetiminiz olması gerekir. Örneğin, bu SQL Server sürümü, özel düzeltmeler, performans yapılandırması vb. içerebilir.
* Şirket içinde SQL Server tam uyumlulukla ve mevcut uygulamaları Azure 'a olduğu gibi taşımak istemeniz gerekir.
* Azure ortamının özelliklerinden yararlanmak istiyorsunuz, ancak Azure SQL veritabanı, uygulamanızın gerektirdiği tüm özellikleri desteklemiyor. Bu, aşağıdaki alanlara dahil olabilir:
  
  * **Veritabanı boyutu**: Bu makalenin güncelleştirildiği sırada SQL veritabanı 1 TB 'a kadar veri veritabanını destekler. Uygulamanız 1 TB 'den fazla veri gerektiriyorsa ve özel parçalama çözümleri uygulamak istemiyorsanız, bir Azure sanal makinesinde SQL Server kullanmanız önerilir. En son bilgiler için bkz. [Azure SQL veritabanı](https://msdn.microsoft.com/library/azure/dn495641.aspx), [DTU tabanlı satın alma modeli](../../../sql-database/sql-database-service-tiers-dtu.md)ve [sanal çekirdek tabanlı satın alma modeli](../../../sql-database/sql-database-service-tiers-vcore.md)(Önizleme) ölçekleme.
  * **HIPAA uyumluluğu**: Azure sanal makinesindeki SQL Server HIPAA Iş ilişkisi Sözleşmesi (BAA) kapsamında olduğu için sağlık müşterileri ve bağımsız yazılım satıcıları (ISV 'Ler) Azure [SQL veritabanı](../../../sql-database/sql-database-technical-overview.md) yerine [azure sanal makinelerinde SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) seçebilir . Uyumluluk hakkında daha fazla bilgi için [bkz. Microsoft Azure Güven Merkezi: Uyumluluk](https://azure.microsoft.com/support/trust-center/compliance/).
  * **Örnek düzeyi özellikleri**: Şu anda SQL veritabanı, veritabanının dışında (bağlı sunucular, aracı işleri, FILESTREAM, Hizmet Aracısı vb.) canlı olan özellikleri desteklemez. Daha fazla bilgi için bkz. [Azure SQL veritabanı kılavuzları ve sınırlamaları](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## <a name="1-tier-simple-single-virtual-machine"></a>1 katmanlı (basit): tek bir sanal makine
Bu uygulama modelinde, SQL Server uygulamanızı ve veritabanınızı Azure 'daki tek başına bir sanal makineye dağıtırsınız. Aynı sanal makine, istemci/Web uygulamanızı, iş bileşenlerinizi, veri erişim katmanını ve veritabanı sunucusunu içerir. Sunu, iş ve veri erişim kodu mantıksal olarak ayrılır ancak fiziksel olarak tek sunuculu bir makinede bulunur. Çoğu müşteri bu uygulama düzeniyle başlar ve daha sonra sistemine daha fazla web rolü veya sanal makine ekleyerek ölçeklendirebilir.

Bu uygulama deseninin ne zaman yararlı olduğu:

* Platformun uygulamanızın gereksinimlerini yanıtladığı olup olmadığını değerlendirmek için Azure platformuna basit bir geçiş işlemi gerçekleştirmek istiyorsunuz.
* Katmanlar arasındaki gecikmeyi azaltmak için aynı sanal makinede barındırılan tüm uygulama katmanlarını aynı Azure veri merkezinde tutmak istiyorsunuz.
* Kısa süreler için geliştirme ve test ortamlarını hızlıca sağlamak istiyorsunuz.
* Değişen iş yükü düzeyleri için stres testi gerçekleştirmek istiyorsunuz, ancak aynı zamanda sahip olmak istemediğiniz ve çok sayıda fiziksel makineyi her zaman koruyabilmeniz gerekir.

Aşağıdaki diyagramda basit bir şirket içi senaryo ve Azure 'daki tek bir sanal makinede bulut etkin çözümünü nasıl dağıtabileceğiniz gösterilmektedir.

![1 katmanlı uygulama kalıbı](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728008.png)

İş katmanını (iş mantığı ve veri erişimi bileşenleri) sunu katmanıyla aynı fiziksel katmanda dağıtmak, ölçeklenebilirlik veya güvenlik sorunları nedeniyle ayrı bir katman kullanmanız gerekmedikçe uygulama performansını en üst düzeye çıkarabilir.

Bu, ile başlamak için çok yaygın bir model olduğundan, verilerinizi SQL Server VM taşımak için, geçiş konusunda yararlı olabilecek aşağıdaki makaleyi bulabilirsiniz: [Bir veritabanını Azure VM 'de SQL Server geçirme](virtual-machines-windows-migrate-sql.md).

## <a name="3-tier-simple-multiple-virtual-machines"></a>3 katmanlı (basit): birden çok sanal makine
Bu uygulama modelinde, her uygulama katmanını farklı bir sanal makineye yerleştirerek Azure 'da 3 katmanlı bir uygulama dağıtırsınız. Bu, kolay genişleme ve genişleme senaryolar için esnek bir ortam sağlar. Bir sanal makine, istemci/Web uygulamanızı içerdiğinde, diğeri iş bileşenlerinizi barındırır ve diğeri veritabanı sunucusunu barındırır.

Bu uygulama deseninin ne zaman yararlı olduğu:

* Karmaşık veritabanı uygulamalarının Azure sanal makinelerine geçişini gerçekleştirmek istiyorsunuz.
* Farklı uygulama katmanlarının farklı bölgelerde barındırılmasını istiyorsunuz. Örneğin, raporlama amacıyla birden çok bölgeye dağıtılan paylaşılan veritabanlarına sahip olabilirsiniz.
* Şirket içi sanallaştırılmış platformlardaki kurumsal uygulamaları Azure sanal makinelerine taşımak istiyorsunuz. Kurumsal uygulamalarla ilgili ayrıntılı bir tartışma için bkz. [Kurumsal uygulama nedir?](https://msdn.microsoft.com/library/aa267045.aspx).
* Kısa süreler için geliştirme ve test ortamlarını hızlıca sağlamak istiyorsunuz.
* Değişen iş yükü düzeyleri için stres testi gerçekleştirmek istiyorsunuz, ancak aynı zamanda sahip olmak istemediğiniz ve çok sayıda fiziksel makineyi her zaman koruyabilmeniz gerekir.

Aşağıdaki diyagramda, her uygulama katmanını farklı bir sanal makineye yerleştirerek Azure 'da basit bir 3 katmanlı uygulamayı nasıl yerleştirebileceğiniz gösterilmektedir.

![3 katmanlı uygulama kalıbı](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728009.png)

Bu uygulama modelinde, her katmanda yalnızca bir sanal makine (VM) vardır. Azure 'da birden fazla VM varsa, bir sanal ağ ayarlamanızı öneririz. [Azure sanal ağ](../../../virtual-network/virtual-networks-overview.md) , güvenilen bir güvenlik sınırı oluşturur ve VM 'lerin kendi ARALARıNDA özel IP adresi üzerinden iletişim kurmasına izin verir. Ayrıca, tüm Internet bağlantılarının yalnızca sunum katmanına gitmesinin her zaman emin olun. Bu uygulama modelini takip ettiğinizde, erişimi denetlemek için ağ güvenlik grubu kurallarını yönetin. Daha fazla bilgi için bkz. [Azure Portal kullanarak sanal makinenize dış erişime Izin verme](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Diyagramda Internet protokolleri TCP, UDP, HTTP veya HTTPS olabilir.

> [!NOTE]
> Azure 'da bir sanal ağ kurmak ücretsizdir. Ancak, şirket içi ağa bağlanan VPN ağ geçidi için ücretlendirilirsiniz. Bu ücret, bağlantının sağlandığı ve kullanılabilir olduğu sürenin miktarına bağlıdır.
> 
> 

## <a name="2-tier-and-3-tier-with-presentation-tier-scale-out"></a>2 katmanlı ve 3 katmanlı sunum katmanı ölçeği genişletme
Bu uygulama modelinde, her uygulama katmanını farklı bir sanal makineye yerleştirerek 2 katmanlı veya 3 katmanlı veritabanı uygulamasını Azure sanal makinelerine dağıtırsınız. Ayrıca, gelen istemci isteklerinin artış hacmi nedeniyle sunu katmanını ölçeklendirebilirsiniz.

Bu uygulama deseninin ne zaman yararlı olduğu:

* Şirket içi sanallaştırılmış platformlardaki kurumsal uygulamaları Azure sanal makinelerine taşımak istiyorsunuz.
* Gelen istemci isteklerinin artış hacmi nedeniyle sunu katmanını ölçeklendirmek istiyorsunuz.
* Kısa süreler için geliştirme ve test ortamlarını hızlıca sağlamak istiyorsunuz.
* Değişen iş yükü düzeyleri için stres testi gerçekleştirmek istiyorsunuz, ancak aynı zamanda sahip olmak istemediğiniz ve çok sayıda fiziksel makineyi her zaman koruyabilmeniz gerekir.
* İsteğe bağlı olarak ölçeklenebilen ve azaltılabilen bir altyapı ortamına sahip olmak istiyorsunuz.

Aşağıdaki diyagramda, gelen istemci isteklerinin artan hacmi nedeniyle sunu katmanını ölçeklendirerek uygulama katmanlarını Azure 'da birden çok sanal makineye nasıl yerleştirebileceğiniz gösterilmektedir. Diyagramda görüldüğü gibi Azure Load Balancer, trafiği birden çok sanal makineye dağıtmaktan ve ayrıca hangi Web sunucusunun bağlanılacağını belirleyerek sorumludur. Yük dengeleyicinin arkasındaki Web sunucularının birden çok örneğinin olması, sunum katmanının yüksek oranda kullanılabilir olmasını sağlar.

![Uygulama kalıbı-sunum katmanı ölçeği genişletme](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728010.png)

### <a name="best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier"></a>Bir katmanda birden çok VM içeren 2 katmanlı, 3 katmanlı veya n katmanlı desenlere yönelik en iyi uygulamalar
Aynı katmana ait sanal makineleri aynı bulut hizmetinde ve aynı Kullanılabilirlik kümesinde yerleştirmeniz önerilir. Örneğin, **CloudService1** ve **AvailabilitySet1** ' deki bir dizi Web sunucusunu ve **CloudService2** ve **AvailabilitySet2**içindeki bir dizi veritabanı sunucusunu yerleştirin. Azure 'daki bir kullanılabilirlik kümesi, yüksek kullanılabilirlik düğümlerini ayrı hata etki alanlarına ve yükseltme etki alanlarına eklemenizi sağlar.

Bir katmanın birden çok VM örneğine yararlanmak için, uygulama katmanları arasında Azure Load Balancer yapılandırmanız gerekir. Her katmandaki Load Balancer yapılandırmak için, her katmanın VM 'lerinde ayrı bir yük dengeli uç nokta oluşturun. Belirli bir katman için, önce aynı bulut hizmetinde VM 'Ler oluşturun. Bu, aynı ortak sanal IP adresine sahip olduklarından emin olmanızı sağlar. Sonra, o katmandaki sanal makinelerden birinde bir uç nokta oluşturun. Ardından, yük dengeleme için o katmandaki diğer sanal makinelere aynı uç noktayı atayın. Yük dengeli bir küme oluşturarak, trafiği birden çok sanal makine arasında dağıtır ve ayrıca, arka uç VM düğümü başarısız olduğunda hangi düğümün bağlanılacağını belirlemesine de Load Balancer izin verin. Örneğin, bir yük dengeleyicinin arkasındaki Web sunucularının birden çok örneğinin olması, sunum katmanının yüksek oranda kullanılabilir olmasını sağlar.

En iyi uygulama olarak, her zaman tüm internet bağlantılarının sunum katmanına ilk gitgeldiğinden emin olun. Sunum katmanı iş katmanına erişir ve iş katmanı veri katmanına erişir. Sunu katmanına erişime izin verme hakkında daha fazla bilgi için, bkz. [Azure Portal kullanarak sanal makinenize dış erişime Izin verme](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure 'daki Load Balancer, şirket içi bir ortamda yük dengeleyiciler gibi çalışıp çalışmadığını unutmayın. Daha fazla bilgi için bkz. [Azure altyapı hizmetleri Için Yük Dengeleme](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ayrıca, Azure sanal ağ 'ı kullanarak sanal makineleriniz için özel bir ağ ayarlamanızı öneririz. Bu, kendilerine özel IP adresi üzerinden iletişim kurmasını sağlar. Daha fazla bilgi için bkz. [Azure sanal ağı](../../../virtual-network/virtual-networks-overview.md).

## <a name="2-tier-and-3-tier-with-business-tier-scale-out"></a>iş katmanı ölçeği genişletme ile 2 katmanlı ve 3 katmanlı
Bu uygulama modelinde, her uygulama katmanını farklı bir sanal makineye yerleştirerek 2 katmanlı veya 3 katmanlı veritabanı uygulamasını Azure sanal makinelerine dağıtırsınız. Ayrıca, uygulamanızın karmaşıklığı nedeniyle uygulama sunucusu bileşenlerini birden çok sanal makineye dağıtmak isteyebilirsiniz.

Bu uygulama deseninin ne zaman yararlı olduğu:

* Şirket içi sanallaştırılmış platformlardaki kurumsal uygulamaları Azure sanal makinelerine taşımak istiyorsunuz.
* Uygulamanızın karmaşıklığı nedeniyle uygulama sunucusu bileşenlerini birden çok sanal makineye dağıtmak istiyorsunuz.
* İş mantığı ağır şirket içi LOB (iş kolu) uygulamalarını Azure sanal makinelerine taşımak istiyorsunuz. LOB uygulamaları, muhasebe, insan kaynakları (HR), bordro, tedarik zinciri yönetimi ve kaynak planlama uygulamaları gibi bir kuruluşun çalıştırılması için önemli olan bir kritik bilgisayar uygulamaları kümesidir.
* Kısa süreler için geliştirme ve test ortamlarını hızlıca sağlamak istiyorsunuz.
* Değişen iş yükü düzeyleri için stres testi gerçekleştirmek istiyorsunuz, ancak aynı zamanda sahip olmak istemediğiniz ve çok sayıda fiziksel makineyi her zaman koruyabilmeniz gerekir.
* İsteğe bağlı olarak ölçeklenebilen ve azaltılabilen bir altyapı ortamına sahip olmak istiyorsunuz.

Aşağıdaki diyagramda, şirket içi bir senaryo ve bulut etkin çözümü gösterilmektedir. Bu senaryoda, iş mantığı katmanını ve veri erişimi bileşenlerini içeren iş katmanını ölçeklendirerek uygulama katmanlarını Azure 'da birden çok sanal makineye yerleştirebilirsiniz. Diyagramda görüldüğü gibi Azure Load Balancer, trafiği birden çok sanal makineye dağıtmaktan ve ayrıca hangi Web sunucusunun bağlanılacağını belirleyerek sorumludur. Yük dengeleyicinin arkasındaki uygulama sunucularının birden çok örneğinin olması, iş katmanının yüksek oranda kullanılabilir olmasını sağlar. Daha fazla bilgi için bkz. [2 katmanlı, 3 katmanlı ya da bir katmanda birden çok sanal makineye sahip n katmanlı uygulama desenleri Için en iyi uygulamalar](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![İş katmanı ölçeği genişletme ile uygulama deseninin](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728011.png)

## <a name="2-tier-and-3-tier-with-presentation-and-business-tiers-scale-out-and-hadr"></a>2 katmanlı ve 3 katmanlı sunum ve iş katmanları genişleme ve HADR
Bu uygulama modelinde, sunu katmanını (Web sunucusu) ve iş katmanı (uygulama sunucusu) bileşenlerini birden çok sanal makineye dağıtarak 2 katmanlı veya 3 katmanlı veritabanı uygulamasını Azure sanal makinelerine dağıtırsınız. Ayrıca, Azure sanal makinelerinde veritabanlarınız için yüksek kullanılabilirlik ve olağanüstü durum kurtarma çözümleri uygulayabilirsiniz.

Bu uygulama deseninin ne zaman yararlı olduğu:

* SQL Server yüksek kullanılabilirlik ve olağanüstü durum kurtarma özellikleri uygulayarak kurumsal uygulamaları şirket içi sanallaştırılmış platformlardan Azure 'a taşımak istiyorsunuz.
* Gelen istemci isteklerinin artan hacmi ve uygulamanızın karmaşıklığı nedeniyle sunu katmanını ve iş katmanını ölçeklendirmek istiyorsunuz.
* Kısa süreler için geliştirme ve test ortamlarını hızlıca sağlamak istiyorsunuz.
* Değişen iş yükü düzeyleri için stres testi gerçekleştirmek istiyorsunuz, ancak aynı zamanda sahip olmak istemediğiniz ve çok sayıda fiziksel makineyi her zaman koruyabilmeniz gerekir.
* İsteğe bağlı olarak ölçeklenebilen ve azaltılabilen bir altyapı ortamına sahip olmak istiyorsunuz.

Aşağıdaki diyagramda, şirket içi bir senaryo ve bulut etkin çözümü gösterilmektedir. Bu senaryoda, sunu katmanını ve iş katmanı bileşenlerini Azure 'da birden çok sanal makinede ölçeklendirebilirsiniz. Ayrıca, Azure 'daki SQL Server veritabanları için yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR) tekniklerini de uygulayacağınızı görürsünüz.

Farklı VM 'lerde bir uygulamanın birden çok kopyasını çalıştırmak, bu isteklerde Yük Dengeleme istekleri olduğundan emin olun. Birden çok sanal makineniz olduğunda, tüm VM 'lerinizi bir noktada erişilebilir ve çalışır durumda olduğundan emin olmanız gerekir. Yük dengelemeyi yapılandırırsanız, Azure Load Balancer VM 'lerin sistem durumunu izler ve gelen çağrıları sağlıklı çalışan VM düğümlerine doğru şekilde yönlendirir. Sanal makinelerin yük dengelemesini ayarlama hakkında daha fazla bilgi için bkz. [Azure altyapı hizmetleri Için Yük Dengeleme](../tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Bir yük dengeleyicinin arkasında birden çok Web ve uygulama sunucusu örneğinin olması, sununun ve iş katmanlarının yüksek düzeyde kullanılabilir olmasını sağlar.

![Genişleme ve yüksek kullanılabilirlik](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728012.png)

### <a name="best-practices-for-application-patterns-requiring-sql-hadr"></a>SQL HADR gerektiren uygulama desenleri için en iyi uygulamalar
Azure sanal makinelerinde yüksek kullanılabilirlik ve olağanüstü durum kurtarma çözümleri SQL Server ayarlarken, sanal makineleriniz için [Azure sanal ağını](../../../virtual-network/virtual-networks-overview.md) kullanan bir sanal ağ ayarlama zorunludur.  Bir sanal ağ içindeki sanal makineler, bir hizmetin kapalı kalma süresi dolduktan sonra bile kararlı bir özel IP adresine sahip olur, böylece DNS ad çözümlemesi için gereken güncelleştirme süresinden kaçınabilirsiniz. Ayrıca, sanal ağ, şirket içi ağınızı Azure 'a genişletmenizi ve güvenilen bir güvenlik sınırı oluşturmanızı sağlar. Örneğin, uygulamanızda kurumsal etki alanı kısıtlamaları varsa (örneğin, Windows kimlik doğrulaması, Active Directory), [Azure sanal ağını](../../../virtual-network/virtual-networks-overview.md) ayarlama işlemi gereklidir.

Azure 'da üretim kodu çalıştıran müşterilerin çoğu, hem birincil hem de ikincil çoğaltmaları Azure 'da tutmalardır.

Yüksek kullanılabilirlik ve olağanüstü durum kurtarma teknikleri hakkında kapsamlı bilgi ve öğreticiler için bkz. [Azure sanal makinelerinde SQL Server Için yüksek kullanılabilirlik ve olağanüstü durum kurtarma](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="2-tier-and-3-tier-using-azure-vms-and-cloud-services"></a>Azure VM 'Leri ve Cloud Services kullanarak 2 katmanlı ve 3 katmanlı
Bu uygulama modelinde, hem [azure Cloud Services](../../../cloud-services/cloud-services-choose-me.md) (Web ve çalışan rolleri-hizmet olarak platform (PaaS)) hem de [Azure sanal makineleri](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (hizmet olarak altyapı (IaaS)) kullanarak, 2 katmanlı veya 3 katmanlı uygulamayı Azure 'a dağıtırsınız. Sunu katmanı/iş katmanı için [azure Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) kullanmak ve veri katmanı Için [azure sanal makinelerinde](../overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) SQL Server, Azure üzerinde çalışan çoğu uygulama için faydalıdır. Bunun nedeni, Cloud Services üzerinde çalışan bir işlem örneği olması kolay bir yönetim, dağıtım, izleme ve genişleme sağlar.

Azure, Cloud Services sayesinde altyapıyı sizin için korur, rutin bakım gerçekleştirir, işletim sistemlerini düzeltme ekleri yapar ve hizmet ve donanım arızalarını kurtarmaya çalışır. Uygulamanızın ölçeği genişletme, otomatik ve elle genişleme seçenekleri, uygulamanız tarafından kullanılan örnek sayısını veya sanal makineleri artırarak veya azaltarak bulut hizmeti projeniz için kullanılabilir. Ayrıca, Azure 'daki bir bulut hizmeti projesine uygulamanızı dağıtmak için şirket içi Visual Studio 'Yu kullanabilirsiniz.

Özet ' te, sunum/iş katmanına yönelik kapsamlı yönetim görevlerine sahip olmak istemiyorsanız ve uygulamanız yazılım veya işletim sistemi için karmaşık bir yapılandırma gerektirmiyorsa Azure Cloud Services kullanın. Azure SQL veritabanı, aradığınız tüm özellikleri desteklemiyorsa, veri katmanı için bir Azure sanal makinesinde SQL Server kullanın. Azure Cloud Services bir uygulama çalıştırmak ve verileri Azure sanal makinelerinde depolamak, her iki hizmetin da avantajlarını birleştirir. Ayrıntılı bir karşılaştırma için bkz. [Azure 'da geliştirme stratejilerini karşılaştırmak](#comparing-web-development-strategies-in-azure)için bu konudaki bölümüne bakın.

Bu uygulama modelinde sunum katmanı, Azure yürütme ortamında çalışan bir Cloud Services bileşeni olan ve IIS ve ASP.NET tarafından desteklenen Web uygulaması programlaması için özelleştirilmiş bir Web rolü içerir. İş veya arka uç katmanı, Azure yürütme ortamında çalışan bir Cloud Services bileşeni olan bir çalışan rolü içerir ve genelleştirilmiş geliştirme için yararlıdır ve bir Web rolü için arka plan işleme gerçekleştirebilir. Veritabanı katmanı Azure 'da SQL Server bir sanal makinede bulunur. Sunum katmanı ve veritabanı katmanı arasındaki iletişim doğrudan veya iş katmanı-çalışan rolü bileşenleri arasında gerçekleşir.

Bu uygulama deseninin ne zaman yararlı olduğu:

* SQL Server yüksek kullanılabilirlik ve olağanüstü durum kurtarma özellikleri uygulayarak kurumsal uygulamaları şirket içi sanallaştırılmış platformlardan Azure 'a taşımak istiyorsunuz.
* İsteğe bağlı olarak ölçeklenebilen ve azaltılabilen bir altyapı ortamına sahip olmak istiyorsunuz.
* Azure SQL veritabanı, uygulamanızın veya veritabanınızın ihtiyaç duyacağı tüm özellikleri desteklemez.
* Değişen iş yükü düzeyleri için stres testi gerçekleştirmek istiyorsunuz, ancak aynı zamanda sahip olmak istemediğiniz ve çok sayıda fiziksel makineyi her zaman koruyabilmeniz gerekir.

Aşağıdaki diyagramda, şirket içi bir senaryo ve bulut etkin çözümü gösterilmektedir. Bu senaryoda, sunum katmanını Web rollerine, çalışan rollerinin iş katmanına, ancak Azure 'daki sanal makinelerde bulunan veri katmanına yerleştirebilirsiniz. Farklı Web rollerinde sunum katmanının birden çok kopyasını çalıştırmak, isteklerin üzerinden yük dengelemesi yapılmasını sağlar. Azure Cloud Services Azure sanal makineleri ile birleştirdiğinizde, [Azure sanal ağını](../../../virtual-network/virtual-networks-overview.md) de ayarlamanızı öneririz. [Azure sanal ağ](../../../virtual-network/virtual-networks-overview.md)ile buluttaki aynı bulut hizmeti içinde kararlı ve kalıcı özel IP adresleri olabilir. Sanal makineleriniz ve bulut hizmetleriniz için bir sanal ağ tanımladıktan sonra, bunlar arasında özel IP adresi üzerinden iletişim kurarak başlatılabilir. Ayrıca, aynı [Azure sanal ağında](../../../virtual-network/virtual-networks-overview.md) sanal makinelerin ve Azure Web/çalışan rollerinin olması, düşük gecikme süresi ve daha güvenli bağlantı sağlar. Daha fazla bilgi için bkz. [bulut hizmeti nedir?](../../../cloud-services/cloud-services-choose-me.md).

Diyagramda görüldüğü gibi, trafiği birden çok sanal makineye dağıtır ve ayrıca bağlanılacak Web sunucusu veya uygulama sunucusu Azure Load Balancer ' nu belirler. Web ve uygulama sunucularının yük dengeleyici arkasında birden fazla örneğinin olması, sunum katmanının ve iş katmanının yüksek kullanılabilirliğini sağlar. Daha fazla bilgi için bkz. [SQL HADR gerektiren uygulama desenleri Için en iyi uygulamalar](#best-practices-for-application-patterns-requiring-sql-hadr).

![Cloud Services ile uygulama desenleri](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728013.png)

Bu uygulama modelini uygulamaya yönelik başka bir yaklaşım, aşağıdaki diyagramda gösterildiği gibi sunum katmanını ve iş katmanı bileşenlerini içeren birleştirilmiş bir Web rolü kullanmaktır. Bu uygulama stili, durum bilgisi içeren tasarım gerektiren uygulamalar için kullanışlıdır. Azure, Web ve çalışan rolleri için durum bilgisi olmayan işlem düğümleri sağladığından, aşağıdaki teknolojilerden birini kullanarak oturum durumunu depolamak için bir mantık uygulamanızı öneririz: Azure [önbelleğe alma](https://azure.microsoft.com/documentation/services/azure-cache-for-redis/), [Azure Tablo depolama](../../../cosmos-db/table-storage-how-to-use-dotnet.md) veya [Azure SQL veritabanı](../../../sql-database/sql-database-technical-overview.md).

![Cloud Services ile uygulama desenleri](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728014.png)

## <a name="pattern-with-azure-vms-azure-sql-database-and-azure-app-service-web-apps"></a>Azure VM 'Leri, Azure SQL veritabanı ve Azure App Service ile desenler (Web Apps)
Bu uygulama deseninin birincil amacı, çözümünüzde Azure hizmet olarak platform (IaaS) bileşenlerini nasıl birleştirebileceğinizi, çözümünüzde Azure hizmet olarak platform (PaaS) bileşenleri ile nasıl birleştirileceğini gösterir. Bu model, ilişkisel veri depolama için Azure SQL veritabanı ' na odaklanılmıştır. Hizmet sunumu olarak Azure altyapısının bir parçası olan Azure sanal makinesinde SQL Server içermez.

Bu uygulama modelinde, sunu ve iş katmanlarını aynı sanal makineye yerleştirerek ve Azure SQL veritabanı (SQL veritabanı) sunucularındaki bir veritabanına erişerek Azure 'a bir veritabanı uygulaması dağıtırsınız. Sunum katmanını geleneksel IIS tabanlı Web çözümlerini kullanarak uygulayabilirsiniz. Ya da [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)kullanarak birleştirilmiş bir sunu ve iş katmanı uygulayabilirsiniz.

Bu uygulama deseninin ne zaman yararlı olduğu:

* Azure 'da yapılandırılmış mevcut bir SQL veritabanı sunucusu zaten var ve uygulamanızı hızlıca test etmek istiyorsunuz.
* Azure ortamının yeteneklerini test etmek istiyorsunuz.
* Kısa süreler için geliştirme ve test ortamlarını hızlıca sağlamak istiyorsunuz.
* İş mantığı ve veri erişim bileşenleriniz, bir Web uygulaması içinde kendi kendine dahil olabilir.

Aşağıdaki diyagramda, şirket içi bir senaryo ve bulut etkin çözümü gösterilmektedir. Bu senaryoda, uygulama katmanlarını Azure 'daki tek bir sanal makineye yerleştirir ve Azure SQL veritabanı 'ndaki verilere erişin.

![Karışık uygulama kalıbı](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728015.png)

Azure Web Apps kullanarak birleştirilmiş bir Web ve uygulama katmanı uygulamayı seçerseniz, orta katman veya uygulama katmanını bir Web uygulaması bağlamında dinamik bağlantı kitaplıkları (dll 'Ler) olarak tutmanızı öneririz.

Ayrıca, programlama teknikleri hakkında daha fazla bilgi edinmek için bu makalenin sonundaki [Azure 'da Web geliştirme stratejilerini karşılaştırma](#comparing-web-development-strategies-in-azure) bölümünde verilen önerileri gözden geçirin.

## <a name="n-tier-hybrid-application-pattern"></a>N katmanlı karma uygulama kalıbı
N katmanlı karma uygulama düzeninde, uygulamanızı şirket içi ve Azure arasında dağıtılan birden çok katmanda uygulayacağınızı görürsünüz. Bu nedenle, diğer katmanları değiştirmeden belirli bir katmanı değiştirmek veya eklemek için esnek ve yeniden kullanılabilir bir karma sistem oluşturursunuz. Şirket ağınızı buluta genişletmek için [Azure sanal ağ](../../../virtual-network/virtual-networks-overview.md) hizmeti 'ni kullanırsınız.

Bu hibrit uygulama deseninin kullanılması:

* Kısmen bulutta ve kısmen şirket içinde çalışan uygulamalar oluşturmak istiyorsunuz.
* Mevcut bir şirket içi uygulamanın bazı veya tüm öğelerini buluta geçirmek istiyorsunuz.
* Şirket içi sanallaştırılmış platformlardaki kurumsal uygulamaları Azure 'a taşımak istiyorsunuz.
* İsteğe bağlı olarak ölçeklenebilen ve azaltılabilen bir altyapı ortamına sahip olmak istiyorsunuz.
* Kısa süreler için geliştirme ve test ortamlarını hızlıca sağlamak istiyorsunuz.
* Kurumsal veritabanı uygulamalarına yönelik yedeklemeleri almak için uygun maliyetli bir yol istiyorsunuz.

Aşağıdaki diyagramda, şirket içi ve Azure arasında yayılan n katmanlı bir karma uygulama kalıbı gösterilmektedir. Diyagramda gösterildiği gibi, şirket içi altyapı, Kullanıcı kimlik doğrulaması ve yetkilendirmesini desteklemek için [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) etki alanı denetleyicisi içerir. Diyagramda veri katmanının bazı bölümlerinin Azure 'da canlı olarak veri katmanının bir parçası olduğu bir senaryoyu gösterdiği bir senaryo olduğunu unutmayın. Uygulamanızın ihtiyaçlarına bağlı olarak, birkaç başka karma senaryo da uygulayabilirsiniz. Örneğin, sunu katmanını ve iş katmanını şirket içi bir ortamda, ancak Azure 'daki veri katmanında tutabilirsiniz.

![N katmanlı uygulama kalıbı](./media/virtual-machines-windows-sql-server-app-patterns-dev-strategies/IC728016.png)

Azure 'da, Active Directory kuruluşunuz için tek başına bir bulut dizini olarak kullanabilir veya mevcut şirket içi Active Directory [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)da tümleştirebilirsiniz. Diyagramda görüldüğü gibi, iş katmanı bileşenleri, [Azure 'da](virtual-machines-windows-sql-server-iaas-overview.md) özel bır iç IP adresi aracılığıyla, [Azure sanal ağı](../../../virtual-network/virtual-networks-overview.md)aracılığıyla şirket Içi SQL Server veya kullanarak [SQL veritabanı](../../../sql-database/sql-database-technical-overview.md) 'na SQL Server gibi birden çok veri kaynağına erişebilir. NET Framework veri sağlayıcısı teknolojileri. Bu diyagramda, Azure SQL veritabanı isteğe bağlı bir veri depolama hizmetidir.

N katmanlı karma uygulama modelinde, aşağıdaki iş akışını belirtilen sırada uygulayabilirsiniz:

1. [Microsoft değerlendirme ve planlama (eşleme) araç seti](https://microsoft.com/map)kullanılarak buluta taşınması gereken kurumsal veritabanı uygulamalarını belirler. HARITA araç seti, sanallaştırma için göz önünde bulundurmakta olduğunuz bilgisayarlardan envanter ve performans verilerini toplar ve kapasite ve değerlendirme planlaması ile ilgili öneriler sağlar.
2. Depolama hesapları ve sanal makineler gibi Azure platformunda gereken kaynakları ve yapılandırmayı planlayın.
3. Şirket içi ağ ve [Azure sanal ağı](../../../virtual-network/virtual-networks-overview.md)arasında ağ bağlantısı kurun. Şirket içi şirket ağı ile Azure 'da bir sanal makine arasında bağlantı kurmak için aşağıdaki iki yöntemden birini kullanın:
   
   1. Azure 'daki bir sanal makinedeki genel uç noktaları aracılığıyla şirket içi ve Azure arasında bir bağlantı kurun. Bu yöntem kolay bir kurulum sağlar ve sanal makinenizde SQL Server kimlik doğrulaması kullanmanızı sağlar. Ayrıca, VM 'ye yönelik genel trafiği denetlemek için ağ güvenlik grubu kurallarınızı ayarlayın. Daha fazla bilgi için bkz. [Azure Portal kullanarak sanal makinenize dış erişime Izin verme](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
   2. Azure sanal özel ağ (VPN) tüneli aracılığıyla şirket içi ve Azure arasında bir bağlantı kurun. Bu yöntem, etki alanı ilkelerini Azure 'da bir sanal makineye genişletmenizi sağlar. Ayrıca, güvenlik duvarı kurallarını ayarlayabilir ve sanal makinenizde Windows kimlik doğrulamasını kullanabilirsiniz. Şu anda Azure, siteden siteye VPN ve Noktadan siteye VPN bağlantılarını güvenli şekilde destekler:
      
      * Siteden siteye bağlantıyı güvenli hale getirmek için, Azure 'da şirket içi ağınız ve sanal ağınız arasında ağ bağlantısı kurabilirsiniz. Şirket içi veri merkezi ortamınızı Azure 'a bağlamak önerilir.
      * Güvenli Noktadan siteye bağlantı sayesinde, Azure 'daki sanal ağınız ile her yerde çalışan ayrı bilgisayarlardaki ağ bağlantısı kurabilirsiniz. Genellikle geliştirme ve test amaçları için önerilir.
      
      Azure 'da SQL Server bağlanma hakkında daha fazla bilgi için bkz. [Azure 'da SQL Server sanal makinesine bağlanma](virtual-machines-windows-sql-connect.md).
4. Azure 'da bir sanal makine diskinde şirket içi verileri yedekleyen zamanlanan işleri ve uyarıları ayarlayın. Daha fazla bilgi için bkz. Azure [BLOB Storage hizmeti Ile yedekleme ve geri yükleme SQL Server](https://msdn.microsoft.com/library/jj919148.aspx) ve [azure sanal makinelerinde SQL Server Için Yedekleme ve geri](virtual-machines-windows-sql-backup-recovery.md)yükleme.
5. Uygulamanızın ihtiyaçlarına bağlı olarak aşağıdaki üç yaygın senaryodan birini uygulayabilirsiniz:
   
   1. Web sunucunuzu, uygulama sunucunuzu ve duyarsız verileri Azure 'daki bir veritabanı sunucusunda tutabilir, ancak gizli verileri şirket içinde tutabilirsiniz.
   2. Web sunucunuzu ve uygulama sunucunuzu şirket içinde, Azure 'daki bir sanal makinedeki veritabanı sunucusu olarak tutabilirsiniz.
   3. Veritabanı sunucunuzu, Web sunucunuzu ve uygulama sunucunuzu şirket içinde tutabilirsiniz. böylece, Azure 'daki sanal makinelerde veritabanı çoğaltmaları saklarsınız. Bu ayar, şirket içi Web sunucularının veya raporlama uygulamalarının Azure 'daki veritabanı çoğaltmalarına erişmesine izin verir. Bu nedenle, bir şirket içi veritabanında iş yükünü düşürmek için elde edebilirsiniz. Bu senaryoyu, ağır okuma iş yükleri ve geliştirme amaçları için uygulamanızı öneririz. Azure 'da veritabanı çoğaltmaları oluşturma hakkında bilgi için bkz. [Azure sanal makinelerinde SQL Server Için yüksek kullanılabilirlik ve olağanüstü durum kurtarma](virtual-machines-windows-sql-high-availability-dr.md)AlwaysOn kullanılabilirlik grupları.

## <a name="comparing-web-development-strategies-in-azure"></a>Azure 'da Web geliştirme stratejilerini karşılaştırma
Azure 'da çok katmanlı SQL Server tabanlı bir uygulama uygulamak ve dağıtmak için aşağıdaki iki programlama yönteminden birini kullanabilirsiniz:

* Azure 'da geleneksel bir Web sunucusu (IIS-Internet Information Services) kurun ve Azure sanal makinelerinde SQL Server veritabanlarına erişin.
* Azure 'a bir bulut hizmeti uygulayın ve dağıtın. Daha sonra, bu bulut hizmetinin Azure sanal makinelerinde SQL Server veritabanlarına erişebildiğinizden emin olun. Bulut hizmeti, birden çok Web ve çalışan rolü içerebilir.

Aşağıdaki tabloda Azure Cloud Services ve Azure Web Apps Azure sanal makinelerinde SQL Server göre geleneksel web geliştirme hakkında bir karşılaştırma sağlanmaktadır. Azure Web Apps, Azure VM 'de SQL Server ortak sanal IP adresi veya DNS adı aracılığıyla Azure Web Apps için bir veri kaynağı olarak kullanılabilir.

|  | Azure sanal makinelerinde geleneksel web geliştirme | Azure 'da Cloud Services | Azure Web Apps ile Web barındırma |
| --- | --- | --- | --- |
| **Şirket içinden uygulama geçişi** |Mevcut uygulamalar-olduğu gibi. |Uygulamalar Web ve çalışan rollerine ihtiyaç duyar. |Mevcut uygulamalar-olduğu gibi, kendi kendine dahil edilen Web uygulamaları ve hızlı ölçeklenebilirlik gerektiren Web Hizmetleri için de uygundur. |
| **Geliştirme ve dağıtım** |Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS Yöneticisi, PowerShell. |Visual Studio, Azure SDK, TFS, PowerShell. Her bulut hizmetinde, hizmet paketinizi ve yapılandırmanızı dağıtabileceğiniz iki ortam vardır: hazırlama ve üretim. Bir bulut hizmetini üretime yükseltmeden önce test etmek için hazırlama ortamına dağıtabilirsiniz. |Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Dağıtımı, PowerShell. |
| **Yönetim ve kurulum** |Uygulama, veri, güvenlik duvarı kuralları, sanal ağ ve işletim sistemi üzerindeki yönetim görevlerinden siz sorumlusunuz. |Uygulama, veri, güvenlik duvarı kuralları ve sanal ağ üzerindeki yönetim görevlerinden siz sorumlusunuz. |Yalnızca uygulama ve veri üzerindeki yönetim görevlerinden siz sorumlusunuz. |
| **Yüksek kullanılabilirlik ve olağanüstü durum kurtarma (HADR)** |Sanal makineleri aynı Kullanılabilirlik kümesine ve aynı bulut hizmetine yerleştirmeniz önerilir. Sanal makinelerinizin aynı Kullanılabilirlik kümesinde tutulması, Azure 'un yüksek kullanılabilirlik düğümlerini ayrı hata etki alanlarına ve yükseltme etki alanlarına yerleştirmesini sağlar. Benzer şekilde, sanal makinelerinizin aynı bulut hizmetinde tutulması, yük dengelemeyi ve VM 'Leri bir Azure veri merkezi içindeki yerel ağ üzerinden birbirleriyle doğrudan iletişim kurabilmenizi sağlar.<br/><br/>Kapalı kalma süresini önlemek için Azure sanal makinelerinde SQL Server için yüksek kullanılabilirlik ve olağanüstü durum kurtarma çözümü uygulamaktan siz sorumlusunuz. Desteklenen HADR teknolojileri için bkz. [Azure sanal makinelerinde SQL Server Için yüksek kullanılabilirlik ve olağanüstü durum kurtarma](virtual-machines-windows-sql-high-availability-dr.md).<br/><br/>Kendi verilerinizi ve uygulamanızı yedeklemeniz sizin sorumluluğunuzdadır.<br/><br/>Veri merkezindeki konak makinesi donanım sorunları nedeniyle başarısız olursa Azure sanal makinelerinizi taşıyabilir. Ayrıca, ana makine güvenlik veya yazılım güncelleştirmeleri için güncelleştirildiğinde VM 'nizin planlanmış kapalı kalma süresi olabilir. Bu nedenle, sürekli kullanılabilirliği sağlamak için her uygulama katmanında en az iki sanal makine tutmanızı öneririz. Azure tek bir sanal makine için SLA sağlamaz. |Azure, temel alınan donanımdan veya işletim sistemi yazılımından kaynaklanan sorunları yönetir. Uygulamanızın yüksek oranda kullanılabilir olmasını sağlamak için bir Web veya çalışan rolünün birden çok örneğini uygulamanızı öneririz. Bilgi için bkz. [Cloud Services, sanal makineler ve sanal ağ hizmet düzeyi sözleşmesi](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/).<br/><br/>Kendi verilerinizi ve uygulamanızı yedeklemeniz sizin sorumluluğunuzdadır.<br/><br/>Azure VM 'deki bir SQL Server veritabanında bulunan veritabanlarında, herhangi bir kesinti yaşanmasını önlemek için yüksek kullanılabilirlik ve olağanüstü durum kurtarma çözümü uygulamaktan siz sorumlusunuz. Desteklenen HDAR teknolojileri için bkz. Azure sanal makinelerinde SQL Server için yüksek kullanılabilirlik ve olağanüstü durum kurtarma.<br/><br/>**SQL Server veritabanı yansıtma**: Azure Cloud Services (Web/çalışan rolleri) ile kullanın. SQL Server VM 'Ler ve bir bulut hizmeti projesi aynı Azure sanal ağında olabilir. Aynı sanal ağda SQL Server VM, iletişimi SQL Server örneğine yönlendirmek için bir SQL Server diğer ad oluşturmanız gerekir. Ek olarak, diğer ad SQL Server adıyla eşleşmelidir. |Yüksek kullanılabilirlik, Azure çalışan rolleri, Azure Blob depolama ve Azure SQL veritabanı 'ndan devralınır. Örneğin, Azure Storage tüm blob, tablo ve kuyruk verilerinin üç çoğaltmasını tutar. Azure SQL veritabanı, herhangi bir zamanda, bir birincil çoğaltma ve iki ikincil çoğaltma çalıştıran verilerin üç kopyasını tutar. Daha fazla bilgi için bkz. [Azure depolama](https://azure.microsoft.com/documentation/services/storage/) ve [Azure SQL veritabanı](../../../sql-database/sql-database-technical-overview.md).<br/><br/>Azure Web Apps için bir veri kaynağı olarak Azure VM 'de SQL Server kullanırken, Azure Web Apps 'nin Azure sanal ağını desteklemediğini aklınızda bulundurun. Diğer bir deyişle Azure Web Apps Azure 'daki SQL Server VM 'lere tüm bağlantılar sanal makinelerin genel uç noktalarından birine gitmelidir. Bu, yüksek kullanılabilirlik ve olağanüstü durum kurtarma senaryoları için bazı sınırlamalara neden olabilir. Örneğin, Azure 'daki istemci uygulaması veritabanı yansıtma ile SQL Server VM bağlanmak Web Apps yeni birincil sunucuya bağlanamaz. veritabanı yansıtma, Azure sanal ağını Azure 'da SQL Server konak VM 'Ler arasında ayarlamanızı gerektirir. Bu nedenle, Azure Web Apps ile **SQL Server veritabanı yansıtma** kullanımı Şu anda desteklenmiyor.<br/><br/>**SQL Server AlwaysOn kullanılabilirlik grupları**: Azure Web Apps Azure 'da SQL Server VM 'lerle kullanırken AlwaysOn Kullanılabilirlik Grupları ayarlayabilirsiniz. Ancak, genel yük dengeli uç noktalar aracılığıyla iletişimi birincil çoğaltmaya yönlendirmek için AlwaysOn kullanılabilirlik grubu dinleyicisini yapılandırmanız gerekir. |
| **şirketler arası bağlantı** |Şirket içine bağlanmak için Azure sanal ağını kullanabilirsiniz. |Şirket içine bağlanmak için Azure sanal ağını kullanabilirsiniz. |Azure sanal ağı destekleniyor. Daha fazla bilgi için bkz. [sanal ağ tümleştirmesi Web Apps](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/). |
| **Ölçeklenebilirlik** |Ölçek artırma, sanal makine boyutları arttırılarak veya daha fazla disk ekleyerek kullanılabilir. Sanal makine boyutları hakkında daha fazla bilgi için bkz. [Azure Için sanal makine boyutları](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).<br/><br/>**Veritabanı sunucusu için**: Ölçek Genişletme, veritabanı bölümleme teknikleri ve SQL Server AlwaysOn kullanılabilirlik grupları aracılığıyla kullanılabilir.<br/><br/>Ağır okuma iş yükleri için, [AlwaysOn kullanılabilirlik grupları](https://msdn.microsoft.com/library/hh510230.aspx) birden fazla ikincil düğümde ve çoğaltma SQL Server de kullanabilirsiniz.<br/><br/>Ağır yazma iş yükleri için, uygulama ölçeğini genişletmek için birden fazla fiziksel sunucu arasında yatay bölümleme verileri uygulayabilirsiniz.<br/><br/>Ayrıca, [veri bağımlı yönlendirme ile SQL Server](https://technet.microsoft.com/library/cc966448.aspx)kullanarak bir ölçek genişletme uygulayabilirsiniz. Veri bağımlı yönlendirme (DDR) ile, veritabanı isteklerini birden çok SQL Server düğümüne yönlendirmek için, istemci uygulamasındaki bölümlendirme mekanizmasını, genellikle iş katmanı katmanında uygulamanız gerekir. İş katmanı, verilerin nasıl bölümlenilme ve verilerin hangi düğümde bulunduğu ile ilgili eşlemeler içerir.<br/><br/>Sanal makineler çalıştıran uygulamaları ölçekleyebilirsiniz. Daha fazla bilgi için bkz. [bir uygulamayı ölçeklendirme](../../../cloud-services/cloud-services-how-to-scale-portal.md).<br/><br/>**Önemli bir dikkat**: Azure 'daki **Otomatik ölçeklendirme** özelliği, uygulamanız tarafından kullanılan sanal makineleri otomatik olarak artırıp azaltmanızı sağlar. Bu özellik, son kullanıcı deneyiminin yoğun dönemler sırasında olumsuz şekilde etkilenmemesini ve talep azaldığında sanal makinelerin kapatılmasını sağlar. Bulut hizmetiniz SQL Server VM 'Leri içeriyorsa otomatik ölçeklendirme seçeneğini ayarlamanız önerilir. Bunun nedeni, otomatik ölçeklendirme özelliğinin, söz konusu VM 'deki CPU kullanımı bir eşikten daha yüksek olduğunda ve sanal makineyi devre dışı kaldığında sanal makineyi kapatmak için Azure 'un bir sanal makineyi kapatmasına izin verir. Otomatik Ölçeklendirme özelliği, Web sunucuları gibi durum bilgisi olmayan uygulamalar için yararlıdır; burada herhangi bir VM, herhangi bir önceki duruma herhangi bir başvuru olmadan iş yükünü yönetebilir. Ancak, otomatik ölçeklendirme özelliği, yalnızca bir örneğin veritabanına yazmaya izin verdiği SQL Server gibi durum bilgisi olan uygulamalar için yararlı değildir. |Ölçek artırma, birden çok Web ve çalışan rolü kullanılarak kullanılabilir. Web rolleri ve çalışan rollerinin sanal makine boyutları hakkında daha fazla bilgi için bkz. [Cloud Services boyutlarını yapılandırma](../../../cloud-services/cloud-services-sizes-specs.md).<br/><br/>**Cloud Services**kullanırken, işleme dağıtmak için birden çok rol tanımlayabilir ve ayrıca uygulamanızın esnek ölçeklemesini elde edebilirsiniz. Her bulut hizmeti, her biri kendi uygulama dosyalarına ve yapılandırmasına sahip bir veya daha fazla web rolü ve/veya çalışan rolü içerir. Bir rol için dağıtılan rol örneklerinin sayısını (sanal makineler) artırarak ve rol örneklerinin sayısını azaltarak bir bulut hizmetini ölçeklendirerek bir bulut hizmetini ölçeklendirebilirsiniz. Ayrıntılı bilgi için bkz. [Azure yürütme modelleri](../../../cloud-services/cloud-services-choose-me.md).<br/><br/>Ölçek Genişletme, [Cloud Services, sanal makineler ve sanal ağ hizmet düzeyi sözleşmesi](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) ve Load Balancer aracılığıyla yerleşik Azure yüksek kullanılabilirlik desteğiyle kullanılabilir.<br/><br/>Çok katmanlı bir uygulama için, web/çalışan rolleri uygulamasını Azure sanal ağı aracılığıyla veritabanı sunucusu VM 'lerine bağlanmanızı öneririz. Ayrıca, Azure aynı bulut hizmetindeki VM 'Ler için Yük Dengeleme sağlar ve bunlar arasında kullanıcı isteklerini yayın. Bu şekilde bağlı sanal makineler, bir Azure veri merkezi içindeki yerel ağ üzerinden birbirleriyle doğrudan iletişim kurabilir.<br/><br/>Azure portal **Otomatik ölçeklendirmeyi** ve zamanlama sürelerini ayarlayabilirsiniz. Daha fazla bilgi için bkz. [portalda bir bulut hizmeti için otomatik ölçeklendirmeyi yapılandırma](../../../cloud-services/cloud-services-how-to-scale-portal.md). |**Ölçeği artırma ve azaltma**: Web siteniz için ayrılan örnek boyutunu (VM) artırabilir/azaltabilirsiniz.<br/><br/>Ölçeği genişletme: Web siteniz için daha fazla ayrılmış örnek (VM) ekleyebilirsiniz.<br/><br/>Portalda **Otomatik ölçeklendirmeyi** ve zamanlama sürelerini de ayarlayabilirsiniz. Daha fazla bilgi için bkz. [nasıl ölçeklendirilecek Web Apps](../../../app-service/manage-scale-up.md). |

Bu programlama yöntemleri arasında seçim yapma hakkında daha fazla bilgi için [bkz. Azure Web Apps, Cloud Services ve VM 'ler: Ne zaman kullanacağınızı](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="next-steps"></a>Sonraki Adımlar
Azure sanal makinelerinde SQL Server çalıştırma hakkında daha fazla bilgi için bkz. [Azure sanal makinelerine genel bakış SQL Server](virtual-machines-windows-sql-server-iaas-overview.md).

