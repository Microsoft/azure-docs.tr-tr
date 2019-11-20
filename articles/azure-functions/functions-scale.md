---
title: Azure Işlevleri ölçeklendirme ve barındırma | Microsoft Docs
description: Azure Işlevleri tüketim planı ve Premium plan arasında seçim yapma hakkında bilgi edinin.
author: ggailey777
manager: gwallace
keywords: Azure işlevleri, işlevler, tüketim planı, Premium plan, olay işleme, Web kancaları, dinamik işlem, sunucusuz mimari
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bf713029f26ac7ec0b6c043fb887fa5190083888
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576070"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Işlevleri ölçeklendirme ve barındırma

Azure 'da bir işlev uygulaması oluşturduğunuzda, uygulamanız için bir barındırma planı seçmeniz gerekir. Azure Işlevleri için kullanılabilen üç barındırma planı vardır: [Tüketim planı](#consumption-plan), [Premium plan](#premium-plan)ve [App Service planı](#app-service-plan).

Seçtiğiniz barındırma planı aşağıdaki davranışları belirler:

* İşlev uygulamanız nasıl ölçeklendirilir.
* Her işlev uygulaması örneği için kullanılabilir kaynaklar.
* VNET bağlantısı gibi gelişmiş özellikler için destek.

Hem tüketim hem de Premium planlar, kodunuz çalışırken otomatik olarak işlem gücü ekler. Uygulamanız, yükü işlemek için gerektiğinde ölçeklendirilir ve kod çalışmayı durdurduktan sonra ölçeklenir. Tüketim planı için, boş VM 'Ler için ödeme yapmak veya kapasiteyi önceden ayırmak zorunda kalmazsınız.  

Premium plan, Premium işlem örnekleri gibi ek özellikler sağlar, örneklerin sonsuza kadar sıcak kalmasına ve VNet bağlantısına sahip olmasını sağlar.

App Service planı, yönettiğiniz özel altyapıdan yararlanmanızı sağlar. İşlev uygulamanız olaylara göre ölçeklendirilmez, yani hiçbir şekilde sıfıra ölçeklenmez. ( [Her zaman açık](#always-on) özelliğinin etkinleştirilmesini gerektirir.)

> [!NOTE]
> İşlev uygulaması kaynağının plan özelliğini değiştirerek tüketim ve Premium planlar arasında geçiş yapabilirsiniz.

## <a name="hosting-plan-support"></a>Barındırma planı desteği

Özellik desteği aşağıdaki iki kategoriye denk gelir:

* _Genel olarak kullanılabilir (GA)_ : üretim kullanımı için tam olarak desteklenir ve onaylanır.
* _Önizleme_: üretim kullanımı için henüz tam olarak desteklenmemiştir ve onaylanmamıştır.

Aşağıdaki tablo, Windows veya Linux üzerinde çalışırken üç barındırma planına yönelik desteğin geçerli olan düzeyini gösterir:

| | Tüketim planı | Premium planı | Adanmış plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | Genel Kullanım | Genel Kullanım | Genel Kullanım |
| Linux | Genel Kullanım | Genel Kullanım | Genel Kullanım |

## <a name="consumption-plan"></a>Tüketim planı

Tüketim planını kullanırken, Azure Işlevleri ana bilgisayarının örnekleri, gelen olayların sayısına göre dinamik olarak eklenir ve kaldırılır. Bu sunucusuz plan otomatik olarak ölçeklendirilir ve yalnızca işlevleriniz çalışırken işlem kaynakları için ücretlendirilirsiniz. Tüketim planında, yapılandırılabilir bir süre sonra bir işlev yürütme zaman aşımına uğrar.

Faturalandırma, yürütme süresi ve kullanılan bellek sayısını temel alır. Faturalandırma, bir işlev uygulaması içindeki tüm işlevler arasında toplanır. Daha fazla bilgi için bkz. [Azure işlevleri fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/functions/).

Tüketim planı varsayılan barındırma plandır ve aşağıdaki avantajları sunar:

* Yalnızca işlevleriniz çalışırken ödeyin
* Yüksek yük dönemlerinde bile otomatik olarak ölçeği genişletme

Aynı bölgedeki işlev uygulamaları aynı tüketim planına atanabilir. Aynı tüketim planında çalışan birden çok uygulamayı kullanmanın bir kısmı veya etkisi yoktur. Aynı tüketim planına birden fazla uygulamanın atanması, her uygulamanın esnekliği, ölçeklenebilirlik veya güvenilirliğini etkilemez.

Tüketim planında çalışırken maliyetleri tahmin etme hakkında daha fazla bilgi edinmek için bkz. [Tüketim planı maliyetlerini anlama](functions-consumption-costs.md).

## <a name="premium-plan"></a>Premium plan

Premium planı kullanırken, Azure Işlevleri ana bilgisayarının örnekleri, tüketim planı gibi gelen olayların sayısına göre eklenir ve kaldırılır.  Premium plan aşağıdaki özellikleri destekler:

* Soğuk başlangıçtan kaçınmak için adet sürekli sıcak örnekler
* VNet bağlantısı
* Sınırsız yürütme süresi
* Premium örnek boyutları (bir çekirdek, iki çekirdek ve dört temel örnek)
* Daha öngörülebilir fiyatlandırma
* Birden çok işlev uygulaması olan planlar için yüksek yoğunluklu uygulama ayırma

Bu seçenekleri nasıl yapılandırabileceğiniz hakkında bilgiler, [Azure işlevleri Premium plan belgesinde](functions-premium-plan.md)bulunabilir.

Yürütme başına faturalandırılması ve tüketilen bellek yerine, Premium plan için faturalandırma, gerekli ve önceden çarpımış örneklerde kullanılan çekirdek saniyelik ve belleğin sayısını temel alır. Plan başına en az bir örnek her zaman sıcak olmalıdır. Bu, yürütmelerin sayısından bağımsız olarak etkin plan başına en az aylık maliyet olduğu anlamına gelir. Premium planındaki tüm işlev uygulamalarının, önceden çarpımış ve etkin örnekleri paylaştığı göz önünde bulundurun.

Aşağıdaki durumlarda Azure Işlevleri Premium planını göz önünde bulundurun:

* İşlev uygulamalarınız sürekli veya neredeyse sürekli çalışır.
* Çok sayıda küçük yürütmeler ve yüksek bir yürütme faturanız, ancak tüketim planında düşük GB ikinci faturanız vardır.
* Tüketim planı tarafından sağlandıkından daha fazla CPU veya bellek seçeneği gerekir.
* Kodunuzun, tüketim planında [izin verilen maksimum yürütme süresinden](#timeout) daha uzun çalışması gerekir.
* Yalnızca Premium bir planda kullanılabilen, VNET/VPN bağlantısı gibi özellikleri gereklidir.

Premium bir planda JavaScript işlevlerini çalıştırırken, daha az vCPU içeren bir örnek seçmeniz gerekir. Daha fazla bilgi için bkz. [tek çekirdekli Premium planları seçme](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Adanmış (App Service) plan

İşlev uygulamalarınız aynı zamanda diğer App Service uygulamalarla aynı ayrılmış VM 'lerde da çalıştırılabilir (temel, standart, Premium ve yalıtılmış SKU 'Lar).

Aşağıdaki durumlarda bir App Service planı düşünün:

* Zaten başka App Service örnekleri çalıştıran, az önce kullanılan VM 'Ler var.
* İşlevlerinizin çalıştırılacağı özel bir görüntü sağlamak istiyorsunuz.

Aynı şekilde, Web Apps gibi diğer App Service kaynaklarda yaptığınız gibi App Service planındaki işlev uygulamaları için de aynı ödeme yaparsınız. App Service planının nasıl çalıştığı hakkında daha fazla bilgi için bkz. [ayrıntılı genel bakış Azure App Service planları](../app-service/overview-hosting-plans.md).

App Service planıyla, daha fazla VM örneği ekleyerek ölçeği el ile değiştirebilirsiniz. Otomatik ölçeklendirme özelliğini de etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [örnek sayısını el ile veya otomatik olarak ölçeklendirme](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Ayrıca, farklı bir App Service planı seçerek ölçeği genişletebilirsiniz. Daha fazla bilgi için bkz. [Azure 'da bir uygulamayı ölçeklendirme](../app-service/manage-scale-up.md). 

JavaScript işlevlerini bir App Service planında çalıştırırken, daha az vCPU içeren bir plan seçmelisiniz. Daha fazla bilgi için bkz. [tek çekirdekli App Service planlarını seçme](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a>Her zaman açık

App Service bir planda çalıştırırsanız, işlev uygulamanızın doğru çalışması için **Always on** ayarını etkinleştirmeniz gerekir. Bir App Service planında, işlevler çalışma zamanı birkaç dakikadan sonra boş kalır, bu nedenle yalnızca HTTP Tetikleyicileri "uyandırır" olur. Her zaman açık, yalnızca bir App Service planında kullanılabilir. Tüketim planında, platform işlev uygulamalarını otomatik olarak etkinleştirir.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Her zaman etkinleştirilmiş olsa bile, tek tek işlevlerde yürütme zaman aşımı, [Host. JSON](functions-host-json.md#functiontimeout) proje dosyasındaki `functionTimeout` ayarıyla denetlenir.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Mevcut bir uygulamanın barındırma planını belirleme

İşlev uygulamanız tarafından kullanılan barındırma planını öğrenmek için, [Azure Portal](https://portal.azure.com)işlev uygulaması Için **genel bakış** sekmesinde **App Service plan/fiyatlandırma katmanı** ' na bakın. App Service planlar için, fiyatlandırma katmanı da belirtilir.

![Portalda ölçeklendirme planını görüntüleme](./media/functions-scale/function-app-overview-portal.png)

Planı aşağıdaki gibi öğrenmek için Azure CLı de kullanabilirsiniz:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

Bu komutun çıktısı `dynamic`olduğunda, işlev uygulamanız tüketim planınızdan olur. Bu komutun çıktısı `ElasticPremium`olduğunda, işlev uygulamanız Premium plandır. Diğer tüm değerler App Service planının farklı katmanlarını gösterir.

## <a name="storage-account-requirements"></a>Depolama hesabı gereksinimleri

Herhangi bir planda, bir işlev uygulaması Azure blob, kuyruk, dosyalar ve tablo depolamayı destekleyen genel bir Azure depolama hesabı gerektirir. Bunun nedeni, Işlevlerin Tetikleyicileri yönetme ve işlev yürütmelerini yönetme gibi işlemler için Azure Storage 'ı temel aldığından, ancak bazı depolama hesapları kuyrukları ve tabloları desteklemezler. Yalnızca BLOB depolama hesapları (Premium Depolama dahil) ve bölgesel olarak yedekli depolama **çoğaltması olan genel** amaçlı depolama hesapları dahil olmak üzere bu hesaplar, bir işlev uygulaması.

İşlev uygulamanız tarafından kullanılan depolama hesabı, Tetikleyiciniz ve bağlamalarınız tarafından, uygulama verilerinizi depolamak için de kullanılabilir. Ancak, depolama yoğun işlemler için ayrı bir depolama hesabı kullanmanız gerekir.   

<!-- JH: Does using a Premium Storage account improve perf? -->

Depolama hesabı türleri hakkında daha fazla bilgi edinmek için bkz. [Azure Storage Services 'A giriş](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Tüketim ve Premium planlar nasıl çalışır?

Tüketim ve Premium planlarında, Azure Işlevleri altyapısı, işlevlerinin tetiklendiği olay sayısına göre Işlevler ana bilgisayarının ek örneklerini ekleyerek CPU ve bellek kaynaklarını ölçeklendirir. Tüketim planında Işlevlerin ana bilgisayarının her örneği 1,5 GB bellek ve bir CPU ile sınırlıdır.  Ana bilgisayarın bir örneği, bir işlev uygulamasının tüm işlevleri bir örnek içinde kaynak paylaşır ve aynı anda ölçeklendirin. Aynı tüketim planını paylaşan işlev uygulamaları bağımsız olarak ölçeklendirilir.  Premium planda, plan boyutunuz söz konusu örnekteki plandaki tüm uygulamalar için kullanılabilir bellek ve CPU 'YU tespit edecektir.  

İşlev kodu dosyaları, işlevin ana depolama hesabındaki Azure dosya paylaşımlarında depolanır. İşlev uygulamasının ana depolama hesabını sildiğinizde, işlev kodu dosyaları silinir ve kurtarılamaz.

### <a name="runtime-scaling"></a>Çalışma zamanı ölçeklendirme

Azure Işlevleri, olayların oranını izlemek ve ölçeğini genişletmek veya ölçeklendirmek için *Ölçek denetleyicisi* adlı bir bileşen kullanır. Ölçek denetleyicisi her tetikleyici türü için buluşsal yöntemler kullanır. Örneğin, bir Azure kuyruk depolama tetikleyicisi kullanırken, sıra uzunluğuna ve en eski sıra iletisinin yaşa göre ölçeklendirilir.

Azure Işlevleri için ölçek birimi, işlev uygulamasıdır. İşlev uygulaması ölçeklenirse, Azure Işlevleri ana bilgisayarının birden çok örneğini çalıştırmak için ek kaynaklar ayrılır. Buna karşılık, işlem talebi azaltıldı, ölçek denetleyicisi işlev ana bilgisayar örneklerini kaldırır. İşlev uygulaması içinde hiçbir işlev çalışmadığı zaman örneklerin sayısı sonunda sıfıra ölçeklendirilir.

![Denetleyici izleme olaylarını ölçeklendirme ve örnek oluşturma](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Ölçeklendirme davranışlarını anlama

Ölçeklendirme, bir dizi etkene göre farklılık gösterebilir ve seçilen tetikleyici ve dile göre farklı şekilde ölçeklendirebilir. ' Nin farkında olması için ölçeklendirmenin bazı çok karmaşık özellikleri vardır:

* Tek bir işlev uygulaması en fazla 200 örneğe ölçeklendirilebilir. Tek bir örnek aynı anda birden fazla ileti veya isteği işleyebilir, bu nedenle eşzamanlı yürütmeler sayısında bir küme sınırı yoktur.
* HTTP Tetikleyicileri için, yeni örnekler yalnızca her 1 saniyede bir en çok olacak şekilde ayrılacaktır.
* HTTP olmayan Tetikleyiciler için, yeni örnekler her 30 saniyede bir en fazla olacak şekilde ayrılacaktır.

Farklı tetikleyiciler Ayrıca aşağıda belgelenen farklı ölçekleme sınırlarına de sahip olabilir:

* [Olay Hub’ı](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Ölçeklenebilir uygulamalar için en iyi uygulamalar ve desenler

Konak yapılandırması, çalışma zamanı kaplama ve kaynak verimliliği dahil, ne kadar iyi ölçeklendirilemeyeceğini etkileyecek bir işlev uygulamasının birçok yönü vardır.  Daha fazla bilgi için [performans konuları makalesinin ölçeklenebilirlik bölümüne](functions-best-practices.md#scalability-best-practices)bakın. Ayrıca, işlev uygulamanız ölçeklenirken bağlantıların nasıl davranacağını de bilmelisiniz. Daha fazla bilgi için bkz. [Azure işlevlerinde bağlantıları yönetme](manage-connections.md).

### <a name="billing-model"></a>Faturalandırma modeli

Farklı planların [faturalandırılması, Azure işlevleri fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/functions/)ayrıntılı olarak açıklanmıştır. Kullanım işlevi uygulama düzeyinde toplanır ve yalnızca işlev kodunun yürütüldüğü süreyi sayar. Faturalandırma için birimler aşağıda verilmiştir:

* **Gigabayt-saniye cinsinden kaynak tüketimi (GB-s)** . Bir işlev uygulaması içindeki tüm işlevler için bellek boyutu ve yürütme süresinin birleşimi olarak hesaplanır. 
* **Yürütmeler**. Bir olay tetikleyicisine yanıt olarak bir işlev yürütüldüğünde her seferinde sayılır.

Tüketim faturanızı nasıl anlayacağınızı öğrenmek için faydalı sorgular ve bilgiler [faturalandırma hakkında SSS bölümünde](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)bulunabilir.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Hizmet sınırlamaları

Aşağıdaki tablo çeşitli barındırma planlarında çalışırken işlev uygulamalarına uygulanan limitleri gösterir:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
