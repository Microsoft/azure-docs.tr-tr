---
title: Azure Izleyici aracısına genel bakış
description: Sanal makinelerin Konuk işletim sisteminden izleme verilerini toplayan Azure Izleyici aracısına (AMA) genel bakış.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.custom: references_regions
ms.openlocfilehash: f1f1ea787406d900c8035c0462ef903b848d7e81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608223"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure Izleyici aracısına genel bakış (Önizleme)
Azure Izleyici Aracısı (AMA), sanal makinelerin Konuk işletim sisteminden izleme verilerini toplar ve Azure Izleyici 'ye gönderir. Bu makaleler, Azure Izleyici aracısına nasıl yükleneceğine ve veri toplamayı nasıl yapılandıracağınızı içeren bir genel bakış sağlar.

## <a name="relationship-to-other-agents"></a>Diğer aracılarla ilişki
Azure Izleyici Aracısı, şu anda Azure Izleyici tarafından sanal makinelerden gelen Konuk verileri toplamak için kullanılan aşağıdaki aracıların yerini alır:

- [Log Analytics Aracısı](./log-analytics-agent.md) -verileri Log Analytics çalışma alanına GÖNDERIR ve VM öngörülerini ve izleme çözümlerini destekler.
- [Tanılama uzantısı](./diagnostics-extension-overview.md) -Azure Izleyici ölçümlerine (yalnızca Windows), Azure Event Hubs ve Azure Storage 'a veri gönderir.
- [Telegraf Agent](../essentials/collect-custom-metrics-linux-telegraf.md) -verileri Azure Izleyici ölçümlerine gönderir (yalnızca Linux).

Azure Izleyici Aracısı, bu işlevselliği tek bir aracıda birleştirmenin yanı sıra mevcut aracılar üzerinde aşağıdaki avantajları sağlar:

- İzleme kapsamı. Farklı VM kümelerinden farklı veri kümeleri için koleksiyon merkezi olarak yapılandırın.  
- Linux çoklu giriş: Linux sanal makinelerinden birden çok çalışma alanına veri gönderme.
- Windows olay filtreleme: hangi Windows olaylarının toplanacağını filtrelemek için XPATH sorguları kullanın.
- İyileştirilmiş uzantı yönetimi: Azure Izleyici Aracısı, geçerli Log Analytics aracılarında yönetim paketlerinden ve Linux eklentilerine göre daha saydam ve denetlenebilir bir genişletilebilirlik işlemek için yeni bir yöntem kullanır.

### <a name="changes-in-data-collection"></a>Veri koleksiyonundaki değişiklikler
Mevcut aracılar için veri toplamayı tanımlamaya yönelik yöntemler birbirinden ayrı olarak birbirinden farklıdır ve her birinin Azure Izleyici aracısında ele alınan sorunları vardır.

- Log Analytics Aracısı, yapılandırmasını bir Log Analytics çalışma alanından alır. Bu, merkezi olarak yapılandırmak kolaydır, ancak farklı sanal makineler için bağımsız tanımları tanımlamaya zordur. Yalnızca bir Log Analytics çalışma alanına veri gönderebilir.

- Tanılama uzantısının her bir sanal makine için bir yapılandırması vardır. Bu, farklı sanal makineler için bağımsız tanımları tanımlamak ve merkezi olarak yönetmek zor bir işlemdir. Yalnızca Azure Izleyici ölçümleri, Azure Event Hubs veya Azure Storage 'a veri gönderebilir. Linux aracıları için, Azure Izleyici ölçümlerine veri göndermek için açık kaynak telegraf Aracısı gerekir.

Azure Izleyici Aracısı, verileri her aracıdan toplanacak şekilde yapılandırmak için [veri toplama kuralları 'nı (DCR)](data-collection-rule-overview.md) kullanır. Veri toplama kuralları, makinelerin alt kümeleri için benzersiz ve kapsamlı yapılandırmaların etkinleştirilmesinde, koleksiyonda koleksiyon ayarlarının yönetilebilirliğini etkinleştirir. Bunlar, çalışma alanından bağımsızdır ve sanal makineden bağımsızdır. Bu, bir kez tanımlanmalarını ve makineler ve ortamlar arasında yeniden kullanılmasını sağlar. Bkz. [Azure izleyici Aracısı (Önizleme) için veri toplamayı yapılandırma](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Azure Izleyici aracısına geçiş yapmam gerekir mi?
Azure izleyici Aracısı, Azure izleyici [için genel kullanıma sunulan aracılarla](agents-overview.md)birlikte bulunur, ancak Azure izleyici Aracısı genel önizleme dönemi boyunca sanal makinelerinizi geçerli aracılardan geçirmeyi düşünebilirsiniz. Bu belirlenmesi yaparken aşağıdaki faktörleri göz önünde bulundurun.

- **Ortam gereksinimleri.** Azure Izleyici Aracısı, geçerli aracılardan daha sınırlı sayıda desteklenen işletim sistemi, ortam ve ağ gereksinimleri kümesine sahiptir. Yeni işletim sistemi sürümleri ve ağ gereksinimlerinin türleri gibi gelecekteki ortam desteği, büyük olasılıkla yalnızca Azure Izleyici aracısında sağlanacaktır. Ortamınızın Azure Izleyici Aracısı tarafından desteklenip desteklenmediğini değerlendirmelisiniz. Aksi takdirde, geçerli aracıda kalmanıza gerek duyarsınız. Azure Izleyici Aracısı geçerli ortamınızı destekliyorsa, buna geçiş yapmayı göz önünde bulundurmanız gerekir.
- **Genel Önizleme riski toleransı.** Azure Izleyici Aracısı, şu anda desteklenen senaryolar için kapsamlı olarak test edilirken, aracı hala genel önizlemeye sunuldu. Sürüm güncelleştirmeleri ve işlev geliştirmeleri sıklıkla ortaya çıkar ve hataları ortaya çıkarabilir. Veri toplamayı durdurabilecek sanal makinelerinizdeki aracıdaki bir hata riskini değerlendirmelisiniz. Veri koleksiyonundaki bir boşluk, hizmetlerinize önemli bir etkiye sahip değilse Azure Izleyici aracısına devam edin. Herhangi bir kararsızlığa karşı düşük bir toleransınız varsa, Azure Izleyici Aracısı bu duruma ulaşıncaya kadar genel kullanıma açık aracılarla devam etmelisiniz.
- **Geçerli ve yeni özellik gereksinimleri.** Azure Izleyici Aracısı, filtreleme, kapsam oluşturma ve çoklu barındırma gibi çeşitli yeni yetenekler sunar, ancak özel günlük toplama ve çözümlerle tümleştirme gibi diğer işlevlere yönelik geçerli aracılarla henüz eşlik etmemiştir. Azure Izleyici 'deki pek çok yeni özellik yalnızca Azure Izleyici aracısında kullanıma sunulacaktır, bu nedenle zamanla daha fazla işlevsellik yalnızca yeni aracıda kullanılabilir olacaktır. Azure Izleyici aracısının ihtiyacınız olan özelliklere sahip olup olmadığını ve yeni aracıdaki diğer önemli özellikleri almak zorunda kalmadan geçici olarak yapabileceğiniz bazı özellikler olduğunu göz önünde bulundurmanız gerekir. Azure Izleyici aracısında ihtiyacınız olan tüm temel yetenekler varsa, bu duruma geçiş yapmayı düşünün. İhtiyacınız olan kritik özellikler varsa, Azure Izleyici Aracısı eşlik edene kadar geçerli aracıya devam edin.
- **Yeniden çalışma toleransı.** Dağıtım betikleri ve ekleme şablonları gibi kaynaklarla yeni bir ortam ayarlıyorsanız, Azure Izleyici Aracısı genel kullanıma sunulduğunda, bunlara yeniden erişip erişemeyeceğini göz önünde bulundurmanız gerekir. Bu yeniden çalışma çabalarının en az olması durumunda, şimdilik geçerli aracılarla devam edin. Bu işlem önemli miktarda iş alıyorsa yeni ortamınızı yeni aracıyla ayarlamayı düşünün. Azure Izleyici aracısının genel olarak kullanılabilir olması ve 2021 'deki Log Analytics aracıları için yayımlanmış bir kullanım tarihi olması beklenmektedir. Kullanımdan kaldırma başladıktan sonra, geçerli aracılar birkaç yıl içinde desteklenecektir.



## <a name="current-limitations"></a>Geçerli sınırlamalar
Azure Izleyici aracısının genel önizlemesi sırasında aşağıdaki sınırlamalar geçerlidir:

- Azure Izleyici Aracısı, VM öngörüleri ve Azure Güvenlik Merkezi gibi çözümleri ve öngörüleri desteklemez. Şu anda desteklenen tek senaryo, yapılandırdığınız veri toplama kurallarını kullanarak veri topluyor. 
- Veri toplama kurallarının, hedef olarak kullanılan Log Analytics çalışma alanıyla aynı bölgede oluşturulması gerekir.
- Azure sanal makineleri, sanal makine ölçek kümeleri ve Azure Arc etkin sunucuları şu anda desteklenmektedir. Azure Kubernetes hizmeti ve diğer işlem kaynağı türleri şu anda desteklenmemektedir.
- Sanal makinenin aşağıdaki HTTPS uç noktalarına erişimi olmalıdır:
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="supported-regions"></a>Desteklenen bölgeler
Azure Izleyici Aracısı Şu anda aşağıdaki bölgelerdeki kaynakları desteklemektedir:

- Doğu Asya
- Güneydoğu Asya
- Orta Avustralya
- Doğu Avustralya
- Güneydoğu Avustralya
- Orta Kanada
- Kuzey Avrupa
- Batı Avrupa
- Orta Fransa
- Almanya Orta Batı
- Orta Hindistan
- Doğu Japonya
- Güney Kore - Orta
- Güney Afrika - Kuzey
- İsviçre Kuzey
- Güney Birleşik Krallık
- Batı Birleşik Krallık
- Central US
- Doğu ABD
- Doğu ABD 2
- Orta Kuzey ABD
- Orta Güney ABD
- Batı ABD
- Batı ABD 2
- Orta Batı ABD

## <a name="coexistence-with-other-agents"></a>Diğer aracılarla birlikte bulunma
Azure Izleyici Aracısı mevcut aracılarla birlikte çalışabilir, böylece değerlendirme veya geçiş sırasında mevcut işlevlerini kullanmaya devam edebilirsiniz. Bu özellikle, var olan çözümleri destekleme genel önizlemesindeki sınırlamalar nedeniyle önemlidir. Bu, sorgu sonuçlarını çarpıtabileceğinden ve verilerin alımı ve saklanması için ek ücretler elde edilmesine neden olduğundan yinelenen verileri toplamayla ilgili dikkatli olmanız gerekir.

Örneğin, VM öngörüleri, performans verilerini bir Log Analytics çalışma alanına göndermek için Log Analytics aracısını kullanır. Ayrıca, çalışma alanını aracılardan Windows olaylarını ve Syslog olaylarını toplayacak şekilde yapılandırmış olabilirsiniz. Azure Izleyici aracısını yükler ve aynı olaylar ve performans verileri için bir veri toplama kuralı oluşturursanız, bu yinelenen verilerin oluşmasına neden olur.


## <a name="costs"></a>Maliyetler
Azure Izleyici Aracısı için maliyet yoktur, ancak alınan veriler için ücret ödemeniz gerekebilir. Log Analytics veri toplama ve bekletme ve müşteri ölçümleri hakkında ayrıntılı bilgi için bkz. [Azure izleyici fiyatlandırması](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="data-sources-and-destinations"></a>Veri kaynakları ve hedefler
Aşağıdaki tabloda, veri toplama kurallarını kullanarak şu anda Azure Izleyici aracısında toplayabileceğiniz veri türleri ve bu verileri gönderebilirsiniz. Diğer veri türlerini toplamak üzere Azure izleyici aracısını kullanan Öngörüler, çözümler ve diğer çözümlerin listesi için bkz. [Azure izleyici tarafından Izlenen nedir?](../monitor-reference.md) .


Azure Izleyici Aracısı verileri Azure Izleyici ölçümleri 'ne veya Azure Izleyici günlüklerini destekleyen bir Log Analytics çalışma alanına gönderir.

| Veri Kaynağı | Hedefler | Description |
|:---|:---|:---|
| Performans        | Azure İzleyici Ölçümleri<br>Log Analytics çalışma alanı | Sayısal değerler, işletim sistemi ve iş yüklerinin farklı yönlerinin performansını ölçerek. |
| Windows olay günlükleri | Log Analytics çalışma alanı | Windows olay günlüğü sistemine gönderilen bilgiler. |
| Syslog             | Log Analytics çalışma alanı | Linux olay günlüğü sistemine gönderilen bilgiler. |


## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri
Azure Izleyici Aracısı tarafından desteklenen Windows ve Linux işletim sistemi sürümlerinin bir listesi için bkz. [desteklenen işletim sistemleri](agents-overview.md#supported-operating-systems) .



## <a name="security"></a>Güvenlik
Azure Izleyici Aracısı herhangi bir anahtar gerektirmez, bunun yerine [sistem tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)gerektirir. Aracıyı dağıtabilmeniz için, her bir sanal makinede bir sistem tarafından atanmış yönetilen kimliğinizin etkinleştirilmiş olması gerekir.

## <a name="networking"></a>Ağ
Azure Izleyici Aracısı, Azure hizmet etiketlerini (AzureMonitor ve AzureResourceManager etiketleri gerekir) destekler, ancak henüz Azure Izleyici özel bağlantı kapsamları veya doğrudan proxy 'ler ile çalışmaz.


## <a name="next-steps"></a>Sonraki adımlar

- Windows ve Linux sanal makinelerine [Azure izleyici Aracısı 'Nı yükler](azure-monitor-agent-install.md) .
- Aracıdan veri toplamak ve Azure Izleyici 'ye göndermek için [bir veri toplama kuralı oluşturun](data-collection-rule-azure-monitor-agent.md) .