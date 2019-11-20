---
title: Azure Geçişi Hakkında | Microsoft Azure
description: Azure Geçişi hizmetine genel bir bakış sağlar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8b1e716e482c49ceba0c600aaba30fe276bdb74d
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748011"
---
# <a name="about-azure-migrate"></a>Azure Geçişi Hakkında

Bu makalede, Azure geçişi hakkında hızlı bir genel bakış sunulmaktadır.

Azure geçişi, kuruluşunuzda Şirket içinden Azure 'a geçiş yapmanıza yardımcı olur. Azure geçişi, şirket içi altyapıyı, uygulamaları ve verileri Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için merkezi bir merkez sağlar.  Azure geçişi şunları sağlar:

- **Birleşik geçiş platformu**: Azure 'a geçiş yolculuğunu başlatmak, çalıştırmak ve izlemek için tek bir portal.
- **Araç aralığı**: hub, Azure geçiş araçları değerlendirmesi ve geçişi sağlar ve diğer araçlar ve bağımsız yazılım SATıCıSı (ISV) teklifleriyle aynı zamanda diğer Azure hizmetleriyle tümleştirilir.
- **Iş yükleri**: Azure geçişi, için değerlendirme ve geçiş sağlar:
    - **Sunucular**: Azure geçişi sunucu değerlendirmesi, Azure geçişi sunucu geçişi ve diğer araçları kullanarak sunucuların Azure VM 'lerine değerlendirmesi ve geçirilmesi.
    - **Veritabanları**: Azure SQL DB 'ye veya Azure SQL yönetilen örneğine şirket içi veritabanlarının değerlendirmesi ve geçirilmesi için MICROSOFT ve ISV araçlarından yararlanın.
    - **Web uygulamaları**: Azure App Service için şirket içi Web uygulamalarını değerlendirmek ve geçirmek üzere Azure App Service Yardımcısı kullanın.
    - **Sanal masaüstleri**: Azure 'da şirket içi sanal masaüstü ALTYAPıSıNı (VDI) değerlendirmek ve Windows sanal masaüstüne GEÇIRMEK için ISV araçları kullanın.
    - **Veriler**: büyük miktarlarda verileri Azure 'a hızla ve ekonomik bir şekilde geçirmek için Azure Data Box ürün ailesini kullanın.

## <a name="azure-migrate-versions"></a>Azure geçişi sürümleri

Şu anda Azure geçişi hizmetinin iki sürümü vardır:

- **Geçerli sürüm**: bu sürümü kullanarak Azure geçişi projelerini oluşturun, şirket içi makineleri bulun ve değerlendirmeleri ve geçişleri düzenleyin. Bu sürümdeki yenilikler hakkında [daha fazla bilgi edinin](whats-new.md) .
- **Önceki sürüm**: Azure geçişi 'nin önceki sürümünü kullandıysanız (yalnızca şirket Içi VMware VM 'lerinin değerlendirmesi desteklenir), artık geçerli sürümü kullanmalısınız. Artık önceki sürümü kullanarak Azure geçişi projelerini oluşturamazsınız ve yeni bulmalar gerçekleştirmemenizi öneririz. Mevcut projelere erişmek için, **tüm hizmetler**> Azure Portal **Azure geçişi**' ni arayın. Azure geçişi panosunda, eski Azure geçiş projelerine erişim için bir bildirim ve bir bağlantı vardır.



## <a name="isv-integration"></a>ISV tümleştirmesi

Azure geçişi, yerel Azure araçlarına ek olarak çeşitli ISV teklifleri ile tümleşir. 

**'Ye** | **Özellik**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Sunucuları geçirme
[Cloudamize](https://www.cloudamize.com/platform) | Sunucuları değerlendir
[Corent teknolojisi](https://www.corenttech.com/AzureMigrate/) | Sunucuları değerlendirin ve geçirin
[Cihaz 42](https://docs.device42.com/) | Sunucuları değerlendir
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDı 'yi değerlendir
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Sunucuları geçirme
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Sunucuları değerlendir
[Unifrivcloud](https://www.cloudatlasinc.com/cloudrecon/) | Sunucuları ve veritabanlarını değerlendir

## <a name="azure-tool-integration"></a>Azure araç tümleştirmesi

Tablo, Azure geçişi ile tümleştirilmiş diğer araçları özetler.

**Araç** | **Ayrıntılar**
--- | ---
Azure geçişi: Sunucu değerlendirmesi | Sunucuları değerlendir
Azure geçişi: sunucu geçişi | Sunucuları geçirme
Veritabanı Geçiş Yardımcısı (DMA) | Veritabanlarını değerlendir
Veritabanı geçiş hizmeti (DMS) | Veritabanlarını geçirme
Movere | Sunucuları değerlendir
Web uygulaması Geçiş Yardımcısı | Web uygulamalarını değerlendirin ve geçirin



### <a name="selecting-a-tool"></a>Araç seçme

İhtiyacınız olan aracı belirleyip Azure geçişi projesine ekleyin.

- Bir ISV aracı veya Movere ekliyorsanız:
    - Bir lisans edinerek veya ücretsiz deneme için kaydolduktan sonra araç ilkesine uygun olarak çalışmaya başlayın. Araçlar için lisanslama, ISV veya araç lisanslama modeline göre belirlenir.
    - Her araçta Azure geçişi 'ne bağlanma seçeneği vardır. Aracı Azure geçişi ile bağlamak için araç yönergelerini ve belgeleri izleyin.
- Azure geçişi projesi içindeki geçiş yolculuğunun Azure ve diğer araçların tamamında merkezi olarak izlenmesi.



## <a name="azure-migrate-server-assessment-tool"></a>Azure geçişi sunucu değerlendirmesi aracı

Azure geçişi: Sunucu değerlendirmesi Aracı, Azure 'a geçiş için şirket içi VMware VM 'leri, Hyper-V VM 'Leri ve fiziksel sunucuları bulur ve değerlendirir. Aşağıdakileri belirlemenize yardımcı olur:

- **Azure hazırlığı:** Şirket içi makinelerin Azure 'a geçiş için hazırlanın olup olmadığını değerlendirin.
- **Azure boyutlandırma:** Geçişten sonra Azure VM 'lerinin tahmini boyutu.
- **Azure maliyet tahmini:** Azure 'da şirket içi sunucuları çalıştırmaya yönelik tahmini maliyetler.
- **Bağımlılık görselleştirmesi:** Çapraz sunucu bağımlılıkları (bağımlılık görselleştirmesi etkinse) ve bağımlı sunucuları Azure 'a taşımanın en iyi yolları.

Sunucu değerlendirmesi, şirket içinde dağıttığınız bir basit gereç kullanır ve sunucu değerlendirmesiyle kayıt yaptırın.

- Gereç, şirket içi makineleri bulur.
- Sunucu değerlendirmesini bağlar ve sürekli olarak makine meta verilerini ve performans verilerini Azure geçişi 'ne gönderir.
- Gereç keşfi aracısız. Bulunan makinelerde hiçbir şeyin yüklü olması gerekmez.
- Bulmadan sonra, bulunan makineleri gruplar halinde toplamanız gerekir. Genellikle, birlikte geçirmek istediğiniz makineleri toplayın.
- Bir grup için değerlendirme oluşturursunuz. Ardından, geçiş stratejinizi anlamak için değerlendirmeyi analiz edersiniz.

## <a name="azure-migrate-server-migration-tool"></a>Azure Geçişi Sunucu Geçişi aracı

Azure geçişi: sunucu geçiş aracı, şirket içi VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, diğer sanallaştırılmış makineler ve genel bulut VM 'lerini Azure 'a geçirmenize yardımcı olur. Makineleri değerlendirdikten veya değerlendirmeden sonra geçirebilirsiniz.


## <a name="database-migration-assistant"></a>Veritabanı Geçiş Yardımcısı

Azure geçişi, Azure SQL VERITABANı, Azure SQL yönetilen örneği veya SQL Server çalıştıran Azure VM 'lerine geçiş için şirket içi SQL Server veritabanlarını değerlendirmek üzere Microsoft Data Migration Yardımcısı (DMA) ile tümleşir. DMA, geçiş için olası engelleyici sorunlar hakkında bilgi sağlar. Bu, desteklenmeyen özellikleri ve geçişten sonra faydalanabilecek yeni özellikleri tanımlar ve veritabanı geçişinin doğru yolunu tanımlamanızı sağlar. [Daha fazla bilgi edinin](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration-service"></a>Veritabanı Geçiş Hizmeti

Azure geçişi, şirket içi veritabanlarını Azure 'a geçirmek için Azure veritabanı geçiş hizmeti (DMS) ile tümleşir. SQL, Azure SQL VERITABANı ve Azure SQL yönetilen örnekleri çalıştıran Azure VM 'lerine şirket içi veritabanlarını geçirmek için DMS kullanın. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="movere"></a>Movere

 
Movere, tüm BT ortamlarını tek bir gün içinde doğru şekilde sunarak iş zekası artıran bir SaaS platformudur. Kuruluşlar arttıkça, değiştirirken ve dijital olarak iyileştirirken, uygulamalar platforma, uygulamaya veya coğrafi hale bakılmaksızın ortamları üzerinde görünürlük ve denetim sahibi olmaları gereken güveni sağlar. Movere Microsoft tarafından [alındı](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) ve artık tek başına bir teklif olarak satılmamaktadır.  Movere, Microsoft çözüm değerlendirmesi ve bulut Ekonomikler programları aracılığıyla kullanılabilir. Movere hakkında [daha fazla bilgi edinin](https://www.movere.io) . Sorularınız varsa, bunları ' ye gönderebilirsiniz: movereq@microsoft.com veya Microsoft temsilcinize başvurun.

Ayrıca, yerleşik geçiş hizmetimiz olan Azure geçişi ' ne da bakmanız önerilir. Azure geçişi, buluta geçişinizi basitleştirecek bir merkezi Merkez sağlar. Hub, fiziksel ve sanal sunucular, veritabanları ve uygulamalar dahil farklı iş yükleri için kapsamlı destek sunar. Uçtan uca görünürlük, bulma, değerlendirme ve geçiş genelinde ilerlemeyi izlemeyi kolaylaştırır. Hem Azure hem de partner ISV araçları ile birlikte Azure geçişi, sanal ve fiziksel sunucu keşfi, performans tabanlı doğru boyutlandırma, maliyet planlama, içeri aktarma tabanlı değerlendirmeler ve aracısız uygulama gibi çok çeşitli özelliklere sahiptir. bağımlılık analizi. Kullanmaya başlamak için uzman yardımı arıyorsanız, Microsoft, yolculukta size rehberlik etmek için [Azure uzman tarafından yönetilen hizmet sağlayıcısına](https://azure.microsoft.com/partners) sahiptir. [Azure geçişi Web sitesine](https://azure.microsoft.com/services/azure-migrate/)göz atın. 
 

## <a name="web-app-migration-assistant"></a>Web uygulaması Geçiş Yardımcısı

Azure geçişi Azure App Service Geçiş Yardımcısı tümleştirilir. Azure geçişi hub 'ında, aşağıdaki gibi, yardımcı kullanarak şirket içi Web uygulamalarını değerlendirebilir ve Azure 'a geçirebilirsiniz:

- **Web uygulamalarını çevrimiçi değerlendirin**: Azure App Service geçiş için şirket içi web sitelerini değerlendirmek üzere Azure App Service Geçiş Yardımcısı kullanın.
- **Web uygulamalarını geçirme**: Azure App Service Geçiş Yardımcısı kullanarak .net ve PHP Web uygulamalarını Azure 'a geçirin.

Yardımcı hakkında [daha fazla bilgi edinin](https://appmigration.microsoft.com/) .



## <a name="offline-data-migration"></a>Çevrimdışı veri geçişi

Büyük miktarlardaki verileri Azure 'a çevrimdışı taşımak için Azure Data Box ürünlerini kullanabilirsiniz. [Daha fazla bilgi](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Sonraki adımlar

- [VMware VM](tutorial-assess-vmware.md) 'Leri ve [Hyper-V VM](tutorial-assess-hyper-v.md)'lerini değerlendirmek için öğreticilerimizi deneyin.
- Azure Geçişi fiyatlandırması hakkında [daha fazla bilgi edinin](https://azure.microsoft.com/pricing/details/azure-migrate/).
- Azure Geçişi hakkında [sık sorulan soruları gözden geçirin](resources-faq.md).
