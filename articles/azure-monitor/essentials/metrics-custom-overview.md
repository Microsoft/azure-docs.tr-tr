---
title: Azure Izleyici 'de özel ölçümler (Önizleme)
description: Azure Izleyici 'de özel ölçümler ve bunların nasıl modellendiği hakkında bilgi edinin.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: bd7f19df5eed87f2fb02af4b5f2577340bcbfd60
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812111"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Azure Izleyici 'de özel ölçümler (Önizleme)

Azure 'da kaynak ve uygulama dağıtırken, performans ve sistem durumu hakkında Öngörüler kazanmak için telemetri toplamaya başlamak isteyeceksiniz. Azure bazı ölçümleri sizin için kullanıma hazır hale getirir. Bu ölçümler [Standart veya platform](./metrics-supported.md)olarak adlandırılır. Ancak, bunlar doğası ile sınırlıdır. 

Daha derin Öngörüler sağlamak için bazı özel performans göstergeleri veya işletmeye özgü ölçümler toplamak isteyebilirsiniz. Bu **özel** ölçümler, uygulama Telemetriniz, Azure kaynaklarınızda çalışan bir aracı, hatta dışarıdan bir izleme sistemi ve doğrudan Azure izleyici 'ye gönderilebilir. Azure Izleyici 'de yayımlandıktan sonra, Azure tarafından oluşturulan standart ölçümleri kullanarak Azure kaynaklarınızın ve uygulamalarınızın özel ölçümlerine gözatabilir, bunları sorgulayabilir ve uyarı verebilirsiniz.

Azure Izleyici özel ölçümleri genel önizlemede geçerli. 

## <a name="methods-to-send-custom-metrics"></a>Özel ölçümleri gönderme yöntemleri

Özel ölçümler, çeşitli yöntemler aracılığıyla Azure Izleyici 'ye gönderilebilir:
- Azure Application Insights SDK 'sını kullanarak uygulamanızı işaretleyin ve Azure Izleyici 'ye özel telemetri gönderin. 
- Azure Izleyici aracısını (Önizleme) [Windows veya Linux Azure sanal](../agents/azure-monitor-agent-overview.md) makinesine yükleyip Azure izleyici ölçümlerine performans sayaçlarını göndermek için bir [veri toplama kuralı](../agents/data-collection-rule-azure-monitor-agent.md) kullanın.
- [Azure](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)sanal makinenize, [sanal makine ölçek KÜMESINE](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), [klasik VM](../essentials/collect-custom-metrics-guestos-vm-classic.md)'ye veya [Klasik Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) Windows Azure tanılama (wad) uzantısını yükleyip performans sayaçlarını Azure izleyici 'ye gönderin. 
- Azure Izleme çıkış eklentisini kullanarak Azure Linux sanal makinenize etkileyen bir Azure [Data telegraf Aracısı](../essentials/collect-custom-metrics-linux-telegraf.md) 'nı yükleyip ölçümleri gönderin.
- Özel ölçümleri [doğrudan Azure izleyici REST API](./metrics-store-custom-rest-api.md)gönderin `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Fiyatlandırma modeli ve bekletme

Özel ölçümler ve ölçüm sorguları için faturalandırma özelliğinin ne zaman etkinleştirileceğini hakkında ayrıntılı bilgi edinmek için [Azure izleyici fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/monitor/) kontrol edin. Özel ölçümler ve ölçüm sorguları dahil olmak üzere tüm ölçümler için belirli fiyat ayrıntıları bu sayfada bulunabilir. Özet olarak, Azure Izleyici ölçüm deposuna standart ölçümleri (Platform ölçümleri) alma maliyeti yoktur, ancak özel ölçümler genel kullanılabilirliği girerken maliyette yer alacak. Ölçüm API sorguları, ücretlendirilir.

Özel ölçümler, [Platform ölçümleriyle aynı süre](../essentials/data-platform-metrics.md#retention-of-metrics)boyunca tutulur. 

> [!NOTE]  
> Azure Izleyici 'ye Application Insights SDK aracılığıyla gönderilen ölçümler, alınan günlük verileri olarak faturalandırılır. Bunlar yalnızca [özel ölçüm boyutlarında Application Insights özelliği etkinleştir ' in](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) seçili olması halinde ek ölçüm ücretlerine uygulanır. Bu onay kutusu, daha karmaşık bir uyarı sağlamak için özel ölçüm API 'sini kullanarak Azure Izleyici ölçümleri veritabanına veri gönderir.  Bölgenizdeki [Application Insights fiyatlandırma modeli](../app/pricing.md#pricing-model) ve [fiyatları](https://azure.microsoft.com/pricing/details/monitor/)hakkında daha fazla bilgi edinin.


## <a name="how-to-send-custom-metrics"></a>Özel ölçümleri gönderme

Azure Izleyici 'ye özel ölçümler gönderdiğinizde, bildirilen her bir veri noktası veya değer aşağıdaki bilgileri içermelidir.

### <a name="authentication"></a>Kimlik Doğrulaması
Azure Izleyici 'ye özel ölçümler göndermek için, ölçümü gönderen varlığın isteğin **taşıyıcı** üst bilgisinde geçerli bir Azure Active Directory (Azure AD) belirteci olması gerekir. Geçerli bir taşıyıcı belirteç edinmenin birkaç desteklenen yolu vardır:
1. [Azure kaynakları Için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md). Azure kaynağına bir kimlik verir (örneğin, VM). Yönetilen Hizmet Kimliği (MSI), belirli işlemleri gerçekleştirmek için kaynak izinleri vermek üzere tasarlanmıştır. Bir örnek, bir kaynağın kendisiyle ilgili ölçümleri yaymalarına izin verir. Bir kaynağa veya MSI öğesine başka bir kaynakta **Izleme ölçümleri yayımcı** izinleri verilebilir. Bu izinle, MSI diğer kaynaklar için ölçümleri de yayabilir.
2. [Azure AD hizmet sorumlusu](../../active-directory/develop/app-objects-and-service-principals.md). Bu senaryoda, bir Azure AD uygulamasına veya hizmetine bir Azure kaynağı hakkında ölçümleri yayma izinleri atanabilir.
Azure Izleyici, isteğin kimliğini doğrulamak için Azure AD ortak anahtarlarını kullanarak uygulama belirtecini doğrular. Mevcut **Izleme ölçümleri yayımcı** rolü zaten bu izne sahip. Azure portal kullanılabilir. Hizmet sorumlusu, için özel ölçümleri yaydığı kaynaklara bağlı olarak, gereken kapsamda **Izleme ölçümleri yayımcı** rolü verilebilir. Örnek olarak bir abonelik, kaynak grubu veya belirli bir kaynaktır.

> [!TIP]  
> Özel ölçümleri yayan bir Azure AD belirteci istediğinizde, belirtecin istendiği hedef kitle veya kaynak için olduğundan emin olun `https://monitoring.azure.com/` . Sondaki '/' öğesini eklediğinizden emin olun.

### <a name="subject"></a>Konu
Bu özellik, için özel ölçümün hangi Azure Kaynak KIMLIĞIYLE bildirileceğini yakalar. Bu bilgiler, yapılmakta olan API çağrısının URL 'SI ile kodlanacak. Her API yalnızca tek bir Azure kaynağı için ölçüm değerleri gönderebilir.

> [!NOTE]  
> Bir kaynak grubunun veya aboneliğin kaynak KIMLIĞI için özel ölçümleri yayalamazsınız.


### <a name="region"></a>Region
Bu özellik, için ölçüm yaymakta olduğunuz kaynağın hangi Azure bölgesine dağıtıldığını yakalar. Ölçümler, kaynağın dağıtıldığı bölge ile aynı Azure Izleyici bölgesel uç noktasına verilmelidir. Örneğin, Batı ABD dağıtılan bir sanal makinenin özel ölçümleri WestUS bölgesel Azure Izleyici uç noktasına gönderilmelidir. Bölge bilgileri de API çağrısının URL 'SI olarak kodlanır.

> [!NOTE]  
> Genel Önizleme sırasında, özel ölçümler yalnızca Azure bölgelerinin bir alt kümesinde kullanılabilir. Desteklenen bölgelerin bir listesi, bu makalenin sonraki bölümlerinde belgelenmiştir.
>
>

### <a name="timestamp"></a>Timestamp
Azure Izleyici 'ye gönderilen her veri noktasının bir zaman damgasıyla işaretlenmesi gerekir. Bu zaman damgası, ölçüm değerinin ölçüldüğü veya toplandığı tarih/saati yakalar. Azure Izleyici, zaman damgalarına sahip ölçüm verilerini geçmişte ve 5 dakika içinde en fazla 20 dakika olacak şekilde kabul eder. Zaman damgası ISO 8601 biçiminde olmalıdır.

### <a name="namespace"></a>Ad Alanı
Ad alanları, benzer ölçümleri birlikte sınıflandırmanız veya gruplandırmanız için bir yoldur. Ad alanlarını kullanarak, farklı Öngörüler veya performans göstergeleri toplayabilecek ölçüm grupları arasında yalıtım elde edebilirsiniz. Örneğin, uygulamanızı profilini oluşturan bellek kullanım ölçümlerini izleyen **contosomemoryölçümler** adlı bir ad alanı olabilir. **Contosoapptransaction** adlı başka bir ad alanı, uygulamanızdaki Kullanıcı işlemleri hakkında tüm ölçümleri izleyebilir.

### <a name="name"></a>Name
**Ad** , bildirilen ölçümün adıdır. Genellikle, ne ölçülerin tanımlanmasına yardımcı olmak için ad açıklayıcı bir yoldur. Bir örnek, belirli bir VM 'de kullanılan bellek baytlarının sayısını ölçen bir ölçümdür. **Kullanılmakta olan bellek baytları** gibi bir ölçüm adı olabilir.

### <a name="dimension-keys"></a>Boyut anahtarları
Boyut, toplanmakta olan ölçüm hakkında ek özellikler tanımlamaya yardımcı olan bir anahtar veya değer çiftidir. Ek özellikleri kullanarak, daha derin Öngörüler sağlayan ölçüm hakkında daha fazla bilgi toplayabilirsiniz. Örneğin, kullanılmakta olan **bellek baytları** , bir VM 'deki her işlemin kaç baytlık bellek kullandığını yakalayan **işlem** adlı bir boyut anahtarına sahip olabilir. Bu anahtarı kullanarak, belleğe özgü işlemlerin ne kadar iyi kullanıldığını görmek veya bellek kullanımına göre ilk beş işlemi tanımlamak için ölçüyü filtreleyebilirsiniz.
Boyutlar isteğe bağlıdır, tüm ölçümler boyutlara sahip olamaz. Özel bir ölçüm en fazla 10 boyut içerebilir.

### <a name="dimension-values"></a>Boyut değerleri
Ölçüm veri noktasını bildirirken, raporlanan ölçümdeki her boyut anahtarı için karşılık gelen bir boyut değeri vardır. Örneğin, sanal makinenizde ContosoApp tarafından kullanılan belleği raporlamak isteyebilirsiniz:

* Ölçüm adı, **kullanılmakta olan bellek baytı** olacaktır.
* Boyut anahtarı **işlem** olurdu.
* Boyut değeri **ContosoApp.exe**.

Ölçüm değeri yayımlarken, her boyut anahtarı için yalnızca tek bir boyut değeri belirtebilirsiniz. VM 'de birden çok işlem için aynı bellek kullanımını topluyorsanız, bu zaman damgası için birden çok ölçüm değeri rapor edebilirsiniz. Her ölçüm değeri, **işlem** boyutu anahtarı için farklı bir boyut değeri belirler.
Boyutlar isteğe bağlıdır, tüm ölçümler boyutlara sahip olamaz. Ölçüm gönderisi boyut anahtarlarını tanımlıyorsa, karşılık gelen boyut değerleri zorunludur.

### <a name="metric-values"></a>Ölçüm değerleri
Azure Izleyici, tüm ölçümleri tek dakikalık ayrıntı düzeyi aralıklarında depolar. Belirli bir dakika boyunca bir metriğin birkaç kez örnekleme yapması gerekebileceğini anladık. Bu ölçümlere örnek olarak CPU kullanımı verilebilir. Ya da birçok ayrı olay için ölçülmesi gerekebilir. Bir örnek, oturum açma işlemi gecikmelerinin bir örneğidir. Azure Izleyici 'de için yaymanız ve ödediğiniz ham değer sayısını sınırlandırmak için, değerleri yerel olarak ön toplayabilir ve aktarabilirsiniz:

* **Min**: dakika boyunca tüm örnek ve ölçülerden gözlenen en düşük değer.
* **Max**: dakika boyunca tüm örnek ve ölçülerden gözlenen maksimum değer.
* **Sum**: dakika boyunca tüm örnek ve ölçülerden gözlenen tüm değerlerin toplamı.
* **Sayı**: dakika boyunca gerçekleştirilen örnek sayısı ve ölçümler.

Örneğin, bir dakika boyunca uygulamanıza verilen dört oturum açma işlemi varsa, her biri için sonuç ölçülen gecikme süresi şu şekilde olabilir:

|İşlem 1|İşlem 2|İşlem 3|İşlem 4|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|

Daha sonra, Azure Izleyici 'de ortaya çıkan ölçüm yayını aşağıdaki gibi olacaktır:
* En az: 4
* En fazla: 16
* Toplam: 40
* Sayı: 4

Uygulamanız yerel olarak önceden toplanırsa ve her bir ayrık örneği veya olayı koleksiyon üzerine hemen yaymaya ihtiyaç duyuyorsa, ham ölçü değerlerini yayabilirsiniz. Örneğin, uygulamanızda her oturum açma işlemi gerçekleştiğinde, Azure Izleyici 'de yalnızca tek bir ölçümle bir ölçüm yayımlarsınız. Bu nedenle, 12 MS geçen bir oturum açma işlemi için ölçüm yayını aşağıdaki gibi olacaktır:
* Min: 12
* En fazla: 12
* Toplam: 12
* Sayı: 1

Bu işlemle, belirli bir dakika boyunca aynı ölçüm Plus boyut birleşimine ait birden çok değer yayabilirsiniz. Daha sonra Azure Izleyici, belirli bir dakika boyunca sunulan tüm ham değerleri alır ve bunları birlikte toplar.

### <a name="sample-custom-metric-publication"></a>Örnek özel ölçüm yayını
Aşağıdaki örnekte, bir sanal makine için ölçüm ad alanı **bellek profili** altında **kullanımda olan bellek baytları** adlı bir özel ölçüm oluşturursunuz. Ölçümün **işlem** adlı tek bir boyutu vardır. Verilen zaman damgası için, ölçüm değerlerini iki farklı işlem için yayıyoruz:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, tanılama uzantısı ve etkileyen XData telegraf Aracısı zaten doğru bölgesel uç noktaya karşı ölçüm değerlerini göstermek üzere yapılandırılmıştır ve her bir egörev için önceki tüm özellikleri taşır.
>
>

## <a name="custom-metric-definitions"></a>Özel Ölçüm tanımları
Azure Izleyici 'nin yayınlanmadan önce özel bir ölçümü önceden tanımlamanız gerekmez. Yayımlanan her ölçüm veri noktası ad alanı, ad ve boyut bilgilerini içerir. Bu nedenle, bir özel ölçüm Azure Izleyici 'ye ilk kez yayıldığından, ölçüm tanımı otomatik olarak oluşturulur. Bu ölçüm tanımı, ölçümün ölçüm tanımlarına göre yayıldığını tüm kaynaklar üzerinde bulunabilir.

> [!NOTE]  
> Azure Izleyici, özel bir ölçüm için **birimleri** tanımlamayı henüz desteklememektedir.

## <a name="using-custom-metrics"></a>Özel ölçümleri kullanma
Özel ölçümler Azure Izleyici 'ye gönderildikten sonra, Azure portal aracılığıyla bunlara gözatabilir ve bunları Azure Izleyici REST API 'Leri aracılığıyla sorgulayabilirsiniz. Ayrıca, belirli koşullar karşılandığında size bildirimde bulunan uyarılar oluşturabilirsiniz.

> [!NOTE]
> Özel ölçümleri görüntülemek için bir okuyucu veya katkıda bulunan rolü olmanız gerekir. Bkz. [Izleme okuyucu](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Özel ölçümlerinize Azure portal aracılığıyla gözatamazsınız
1.    [Azure Portal](https://portal.azure.com) gidin.
2.    **İzleyici** bölmesini seçin.
3.    **Ölçümler**’i seçin.
4.    Özel ölçümler oluşturmuş olduğunuz bir kaynağı seçin.
5.    Özel ölçümünüzün ölçüm ad alanını seçin.
6.    Özel ölçümü seçin.

> [!NOTE]
> Azure portal ölçümleri görüntüleme hakkında daha fazla bilgi için bkz. [Azure Ölçüm Gezgini](./metrics-getting-started.md) kullanmaya başlama.

## <a name="supported-regions"></a>Desteklenen bölgeler
Genel Önizleme sırasında, özel ölçümleri yayımlama özelliği yalnızca Azure bölgelerinin bir alt kümesinde bulunabilir. Bu kısıtlama, ölçümlerin yalnızca desteklenen bölgelerden birindeki kaynaklar için yayımlanalabileceği anlamına gelir. Azure bölgeleri hakkında daha fazla bilgi için bkz. [Azure coğrafi lıkları](https://azure.microsoft.com/global-infrastructure/geographies/) . Aşağıdaki uç noktalarında kullanılan Azure bölge kodu, yalnızca boşluktan kaldırılan bölgenin adıdır ve aşağıdaki tabloda özel ölçümler için desteklenen Azure bölgelerinin kümesi listelenmektedir. Ayrıca, bu bölgelerdeki kaynaklara yönelik ölçümlerin yayımlanacağı ilgili uç noktaları listeler:

|Azure bölgesi |Bölgesel uç nokta ön eki|
|---|---|
| Tüm genel bulut bölgeleri | https://<azure_region_code>. monitoring.azure.com |
| **Azure Devlet Kurumları** | |
| US Gov Arizona | https: \/ /usgovarizona.Monitoring.Azure.us |
| **Çin** | |
| Çin Doğu 2 | https: \/ /chinaeast2.Monitoring.Azure.cn |

## <a name="latency-and-storage-retention"></a>Gecikme süresi ve depolama saklama

Yeni bir ölçüm veya bir ölçüme eklenen yeni bir boyutun eklenmesi, en fazla 2 ila 3 dakika sürer. Sistemde bir kez, verilerin 30 saniyeden az %99 ' de bir süre içinde görüntülenmesi gerekir. 

Bir ölçümü siler veya bir boyut kaldırırsanız, değişiklik sistemden silinecek bir aya kadar sürebilir.

## <a name="quotas-and-limits"></a>Kotalar ve sınırlar
Azure Izleyici, özel ölçümler üzerinde aşağıdaki kullanım sınırlarını uygular:

|Kategori|Sınır|
|---|---|
|Etkin zaman serisi/abonelikler/bölge|50,000|
|Ölçüm başına Boyut anahtarları|10|
|Ölçüm ad alanları, ölçüm adları, Boyut anahtarları ve boyut değerleri için dize uzunluğu|256 karakter|

Etkin bir zaman serisi, son 12 saat içinde yayımlanmış ölçüm değerleri olan ölçüm, boyut anahtarı veya boyut değerinin herhangi bir benzersiz birleşimi olarak tanımlanır.

50.000 zaman serisi sınırını anlamak için aşağıdaki ölçümü göz önünde bulundurun:

Boyutlarla *sunucu yanıt süresi* : *bölge*, *bölüm*, *CustomerID*

Bu ölçümle, 10 bölgeiniz varsa, 20 departman ve 100 müşteri, size 10 x 20 x 100 = 2000 zaman serisi sağlar. 

100 bölgeiniz varsa, 200 departmanlar ve 2000 müşteriler 100 x 200 x 2000 = 40.000.000 zaman serisi, bu da yalnızca bu ölçüm için sınırın üzerinde. 

Bu sınır, tek bir ölçüm için değildir. Bu, bir abonelik ve bölge genelinde tüm ölçümlerin toplamına yöneliktir.  

## <a name="design-limitations-and-considerations"></a>Tasarım sınırlamaları ve konuları

**Denetim amacıyla Application Insights kullanmayın** – Application Insights telemetri işlem hattı, performans etkisini en aza indirmek ve ağ trafiğini uygulamanızı izlemeden sınırlamak için iyileştirilmiştir. Bu nedenle, ilk veri kümesi çok büyük hale gelirse, bir veya örneklerinizi kısıtlar (yalnızca telemetrinizin yüzdesini alır ve REST 'yi yoksayar). Bu davranış nedeniyle, bazı kayıtların bırakılmakta olma olasılığı olduğu için bunu denetim amacıyla kullanamazsınız. 

**Adda bir değişken bulunan ölçümler** – ölçüm adının bir parçası olarak bir değişken kullanmayın, bunun yerine bir sabit kullanın. Değişken, her değeri değiştirdiğinde, Azure Izleyici yeni bir ölçüm oluşturur ve ölçüm sayısı limitlerine hızlıca ulaşacaktır. Genellikle, geliştiriciler ölçüm adına bir değişken eklemek istediklerinde, gerçekten bir ölçüm içinde birden çok zaman serisi 'i izlemek ve değişken ölçüm adları yerine boyutları kullanması gerekir. 

**Yüksek kardinalite ölçüm boyutları** -bir boyutta ("yüksek kardinalite") çok sayıda geçerli değere sahip ölçümler 50.000 sınırına ulaşmaya çok daha fazladır. Genel olarak, asla bir boyut veya ölçüm adında sürekli değişen bir değer kullanmamalısınız. Örneğin, zaman damgası hiçbir zaman bir boyut olmamalıdır. Sunucu, müşteri veya ProductID, ancak bu türden her biri daha az sayıda varsa kullanılabilir. Bir test olarak, bu verileri bir grafikte grafik üzerinde barındırmanız durumunda kendinize sorun.  10 veya belki de 100 sunucularınız varsa, bunları karşılaştırma için bir grafikte görmek faydalı olabilir. Ancak, 1000 varsa, bu durum okunmadığında ortaya çıkan grafik büyük olasılıkla zor olabilir. En iyi yöntem, 100 geçerli değerler için daha az bir değer olmasını sağlar. 300 kadar gri bir alandır.  Bu miktarın üzerine gitmeniz gerekiyorsa Azure Izleyici özel günlüklerini kullanın.   

Ad veya yüksek kardinalite boyutunda bir değişkeniniz varsa, şunlar meydana gelebilir:
- Kısıtlama nedeniyle ölçümler güvenilir hale getirilir
- Ölçüm Gezgini çalışmıyor
- Uyarı ve bildirimler öngörülemeyen hale gelir
- Maliyetler unexpectedably artırabilir-boyutlara sahip özel ölçümler genel önizlemede olduğundan, Microsoft bu şekilde ücretlendirmez. Ancak, daha sonra ücretler başladıktan sonra beklenmeyen ücretler uygulanır. Plan, izlenen zaman serisi sayısına ve yapılan API çağrılarının sayısına göre ölçüm tüketimine göre ücretlendirilir.  

## <a name="next-steps"></a>Sonraki adımlar
Farklı hizmetlerden özel ölçümler kullanın: 
 - [Sanal Makineler](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Sanal makine ölçek kümesi](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure sanal makineleri (klasik)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Telegraf aracısını kullanan Linux sanal makinesi](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [Klasik Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)
