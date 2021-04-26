---
title: Dayanıklı İşlevler-Azure 'da veri kalıcılığı ve serileştirilmesi
description: Azure Işlevleri için Dayanıklı İşlevler uzantısının verileri nasıl devam etmediğini öğrenin
author: ConnorMcMahon
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: azfuncdf
ms.openlocfilehash: 91c04b3943af5eee436bb4a18a946000b76cff3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057992"
---
# <a name="data-persistence-and-serialization-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler 'de veri kalıcılığı ve serileştirmesi (Azure Işlevleri)

Dayanıklı İşlevler, güvenilir bir yürütme sağlamak için işlev parametrelerini, dönüş değerlerini ve diğer durumu sürekli olarak dayanıklı bir arka uca devam ettirir. Ancak, dayanıklı depolamaya kalıcı olan verilerin miktarı ve sıklığı, uygulama performansını ve depolama işlem maliyetlerini etkileyebilir. Uygulamanızın mağazalarınızın türüne bağlı olarak, veri saklama ve gizlilik ilkelerinin de göz önünde bulundurulması gerekebilir.

## <a name="azure-storage"></a>Azure Depolama

Varsayılan olarak, Dayanıklı İşlevler verileri, belirttiğiniz bir [Azure depolama](https://azure.microsoft.com/services/storage/) hesabındaki sıralara, tablolara ve bloblara devam ettirir.

### <a name="queues"></a>Kuyruklar

Dayanıklı İşlevler, tüm işlev yürütmelerini güvenilir bir şekilde zamanlamak için Azure depolama kuyruklarını kullanır. Bu kuyruk iletileri, iletinin yürütmeyi zamanlamak için kullanılıp kullanılmadığını veya bir çağırma işlevine geri değer döndürmesinin ne olduğuna bağlı olarak işlev girişleri veya çıktılar içerir. Bu kuyruk iletileri, Yönlendirme ve uçtan uca bağıntı gibi Dayanıklı İşlevler iç amaçlar için kullanılan ek meta verileri de içerir. Bir işlevin alınan bir iletiye yanıt olarak yürütülmesi tamamlandıktan sonra, bu ileti silinir ve yürütme sonucu Azure depolama tabloları veya Azure depolama Blobları için de kalıcı olabilir.

Tek bir [görev hub 'ında](durable-functions-task-hubs.md)dayanıklı işlevler, zamanlama etkinlik işlevleri için adlı bir *iş öğesi* kuyruğuna `<taskhub>-workitem` ve Orchestrator ve Entity işlevlerini çizelgelemek veya yeniden başlatmak için adlı bir veya daha fazla *Denetim kuyruğu* için ileti oluşturur ve ekler `<taskhub>-control-##` . Denetim sıralarının sayısı, uygulamanız için yapılandırılan bölüm sayısına eşittir. Kuyruklar ve bölümler hakkında daha fazla bilgi için bkz. [performans ve ölçeklenebilirlik belgeleri](durable-functions-perf-and-scale.md).

### <a name="tables"></a>Tables

İletileri başarıyla işlem yaptıktan sonra, sonuçta elde edilen eylemlerinin kayıtları adlı *Geçmiş* tablosuna kalıcı hale getirilir `<taskhub>History` . Orchestration girdileri, çıktılar ve özel durum verileri de adlı *örnekler* tablosunda kalıcı hale getirilir `<taskhub>Instances` .

### <a name="blobs"></a>Bloblar

Çoğu durumda Dayanıklı İşlevler, verileri kalıcı hale getirmek için Azure Storage Bloblarını kullanmaz. Ancak, kuyruklar ve tablolar, Dayanıklı İşlevler tüm gerekli verileri bir depolama satırına veya kuyruk iletisine kalıcı hale getirmeyi engelleyebilen [Boyut sınırlarına](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-queue-storage-limits) sahiptir. Örneğin, bir sıraya kalıcı olması gereken verilerin bir parçası serileştirildiğinde 45 KB 'tan büyükse Dayanıklı İşlevler verileri sıkıştırır ve bunun yerine bir blob 'da depolar. Bu şekilde blob depolamaya veri kalıcı hale geldiğinde, dayanıklı Işlev tablo satırında veya kuyruk iletisinde o bloba bir başvuru depolar. Verileri almak için Dayanıklı İşlevler gerektiğinde blob 'dan otomatik olarak alınır. Bu Bloblar blob kapsayıcısında depolanır `<taskhub>-largemessages` .

> [!NOTE]
> Büyük iletiler için ek sıkıştırma ve BLOB işlem adımları, CPU ve g/ç gecikme süresi maliyetlerine göre maliyetli olabilir. Ayrıca, Dayanıklı İşlevler kalıcı verilerin belleğe yüklenmesi gerekir ve bu işlemi aynı anda birçok farklı işlev yürütmeleri için de gerektirebilir. Sonuç olarak, kalıcı büyük veri yükleri de yüksek bellek kullanımına neden olabilir. Bellek yükünü en aza indirmek için, büyük veri yüklerini el ile (örneğin, blob depolamada) kalıcı olarak düşünün ve bunun yerine bu verilere yönelik başvuruları geçirin. Bu şekilde, kodunuz yalnızca [Orchestrator işlev yeniden yürütüldüğünde](durable-functions-orchestrations.md#reliability)gereksiz yüklere engel olmak için gerektiğinde verileri yükleyebilir. Ancak, disklerin yaşam süreleri boyunca farklı VM 'lerde yürütülemediğinden, disk üzerindeki durumun kullanılabilir olmadığı garanti edilmediği için, yükü diske *depolamak önerilmez.*

### <a name="types-of-data-that-is-serialized-and-persisted"></a>Serileştirilmiş ve kalıcı olan veri türleri
Aşağıda Dayanıklı İşlevler özellikleri kullanılırken seri hale getirilecek ve kalıcı olacak farklı veri türlerinin bir listesi verilmiştir:

- Tüm kimlikler ve işlenmemiş özel durumlar dahil olmak üzere Orchestrator, etkinlik ve varlık işlevlerinin tüm girişleri ve çıkışları
- Orchestrator, etkinlik ve varlık işlev adları
- Dış olay adları ve yükleri
- Özel düzenleme durumu yükleri
- Düzenleme sonlandırma iletileri
- Dayanıklı Zamanlayıcı yükleri
- Kalıcı HTTP isteği ve yanıt URL 'Leri, üstbilgileri ve yükleri
- Varlık çağrısı ve sinyal yükleri
- Varlık durumu yükleri

### <a name="working-with-sensitive-data"></a>Gizli verilerle çalışma
Azure depolama 'yı kullanırken tüm veriler Rest 'de otomatik olarak şifrelenir. Ancak, depolama hesabına erişimi olan herkes verileri şifrelenmemiş biçimde okuyabilir. Hassas veriler için daha güçlü koruma gerekiyorsa, verileri önceden şifrelenmiş bir biçimde devam Dayanıklı İşlevler için kendi şifreleme anahtarlarınızı kullanarak verileri ilk kez şifrelemeyi düşünün.

Alternatif olarak, .NET Kullanıcıları otomatik şifrelemeyi sağlayan özel serileştirme sağlayıcıları uygulama seçeneğine sahiptir. [Bu GitHub örneğinde](https://github.com/charleszipp/azure-durable-entities-encryption), şifrelemeye sahip özel seri hale getirme örneği bulunabilir.

> [!NOTE]
> Uygulama düzeyinde şifrelemeyi uygulamaya karar verirseniz, düzenleme ve varlıkların sınırsız miktarda süre için mevcut olduğunu unutmayın. Bu durum, bir düzenleme veya varlık anahtar döndürme ilkenizin daha uzun bir süre çalışabilir olduğundan, şifreleme anahtarlarınızı döndürme zamanı geldiğinde önemlidir. Bir anahtar döndürme gerçekleşse, verilerinizi şifrelemek için kullanılan anahtar, düzenleme veya varlığınızın bir sonraki açılışında bu dosyanın şifresini çözmek için artık kullanılamıyor olabilir. Bu nedenle, yalnızca düzenlemeler ve varlıkların görece kısa süreler için çalıştırılması beklendiğinde, müşteri şifrelemesi önerilir.

## <a name="customizing-serialization-and-deserialization"></a>Serileştirme ve seri durumdan çıkarma özelleştirme

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default-serialization-logic"></a>Varsayılan serileştirme mantığı

Dayanıklı İşlevler, düzenleme ve varlık verilerini JSON 'a serileştirmek için dahili olarak [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm) kullanır. Json.NET için kullanımları Dayanıklı İşlevler varsayılan ayarlar şunlardır:

**Girişler, çıktılar ve durum:**

```csharp
JsonSerializerSettings
{
    TypeNameHandling = TypeNameHandling.None,
    DateParseHandling = DateParseHandling.None,
}
```

**Larý**

```csharp
JsonSerializerSettings
{
    ContractResolver = new ExceptionResolver(),
    TypeNameHandling = TypeNameHandling.Objects,
    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
}
```

Daha ayrıntılı belgeler hakkında bilgi edinin `JsonSerializerSettings` [](https://www.newtonsoft.com/json/help/html/SerializationSettings.htm).

## <a name="customizing-serialization-with-net-attributes"></a>.NET öznitelikleriyle Serileştirmeyi özelleştirme

Veri serileştirilirken, Json.NET sınıfların ve özelliklerin JSON 'dan serileştirilme ve seri durumdan çıkarılme şeklini denetleyen [çeşitli öznitelikler](https://www.newtonsoft.com/json/help/html/SerializationAttributes.htm) arar. Dayanıklı İşlevler API 'Lerine geçirilen veri türü için kaynak kodu sahibiyseniz, serileştirme ve serisini kaldırma özelliklerini özelleştirmek için bu öznitelikleri türe eklemeyi göz önünde bulundurun.

## <a name="customizing-serialization-with-dependency-injection"></a>Bağımlılığı ekleme ile serileştirme özelleştirme

.NET ve Işlevleri Işlevleri üzerinde çalışan işlev uygulamaları, verilerin ve özel durumların serileştirilme şeklini özelleştirmek için [bağımlılık ekleme (dı)](../functions-dotnet-dependency-injection.md) özelliğini kullanabilir. Aşağıdaki örnek kod, `IMessageSerializerSettingsFactory` ve hizmet arabirimlerinin özel uygulamalarını kullanarak varsayılan JSON.net serileştirme ayarlarını geçersiz kılmak IÇIN dı 'nin nasıl kullanılacağını gösterir `IErrorSerializerSettingsFactory` .

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Azure.WebJobs.Extensions.DurableTask;
using Microsoft.Extensions.DependencyInjection;
using Newtonsoft.Json;
using System.Collections.Generic;

[assembly: FunctionsStartup(typeof(MyApplication.Startup))]
namespace MyApplication
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddSingleton<IMessageSerializerSettingsFactory, CustomMessageSerializerSettingsFactory>();
            builder.Services.AddSingleton<IErrorSerializerSettingsFactory, CustomErrorSerializerSettingsFactory>();
        }

        /// <summary>
        /// A factory that provides the serialization for all inputs and outputs for activities and
        /// orchestrations, as well as entity state.
        /// </summary>
        internal class CustomMessageSerializerSettingsFactory : IMessageSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }

        /// <summary>
        /// A factory that provides the serialization for all exceptions thrown by activities
        /// and orchestrations
        /// </summary>
        internal class CustomErrorSerializerSettingsFactory : IErrorSerializerSettingsFactory
        {
            public JsonSerializerSettings CreateJsonSerializerSettings()
            {
                // Return your custom JsonSerializerSettings here
            }
        }
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="serialization-and-deserialization-logic"></a>Serileştirme ve seri durumdan çıkarma mantığı

Azure Işlevleri düğüm uygulamaları serileştirme ve [ `JSON.Parse()` seri durumundan çıkarma](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) [ `JSON.stringify()` için](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) kullanır. Çoğu tür, sorunsuz bir şekilde serileştirmelidir ve serileştirilir. Varsayılan mantığın yetersiz olduğu durumlarda, `toJSON()` nesne üzerinde bir yöntem tanımlamak serileştirme mantığını hijaya eder. Ancak, nesne serisini kaldırma için örneksel yok.

Serileştirme/seri kaldırma işlem hattının tam özelleştirmesi için, serileştirme ve seri durumdan çıkarma işlemlerini kendi kodunuzla işlemeyi ve verileri dizeler olarak geçirmeyi düşünün.


# <a name="python"></a>[Python](#tab/python)

### <a name="serialization-and-deserialization-logic"></a>Serileştirme ve seri durumdan çıkarma mantığı

Dayanıklı İşlevler seri hale getirilen ve verilerinizi doğru bir şekilde serileştirerek emin olmak için tür ek açıklamaları kullanmanız önemle önerilir. Birçok yerleşik tür otomatik olarak işlenirken, bazı yerleşik veri türleri seri durumundan çıkarma sırasında türü korumak için tür ek açıklamaları gerektirir.

Özel veri türleri için sınıfınızdan statik bir yöntemi dışarı aktararak bir veri türünün JSON serileştirme ve serisini kaldırma işlemlerini tanımlamanız `to_json` gerekir `from_json` .

---
