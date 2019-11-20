---
title: Dayanıklı İşlevler performansı ve ölçeği-Azure
description: Azure Işlevleri için Dayanıklı İşlevler uzantısına giriş.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 5efe571e2c7ff75ace584755324964003176b5f0
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614705"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Dayanıklı İşlevler performans ve ölçek (Azure Işlevleri)

Performansı ve ölçeklenebilirliği iyileştirmek için [dayanıklı işlevler](durable-functions-overview.md)'in benzersiz ölçeklendirme özelliklerini anlamak önemlidir.

Ölçek davranışını anlamak için, temel alınan Azure depolama sağlayıcısının bazı ayrıntılarını anlamanız gerekir.

## <a name="history-table"></a>Geçmiş tablosu

**Geçmiş** tablosu, bir görev hub 'ında bulunan tüm düzenleme örnekleri için geçmiş olaylarını Içeren bir Azure depolama tablosudur. Bu tablonun adı *Taskhubname*geçmişi biçiminde olur. Örnekler çalıştırıldığında, bu tabloya yeni satırlar eklenir. Bu tablonun bölüm anahtarı, düzenleme örnek KIMLIĞINDEN türetilir. Çoğu durumda, Azure Storage 'da iç bölümlerin en iyi şekilde dağıtılmasını sağlayan bir örnek KIMLIĞI rasgeledir.

Bir Orchestration örneğinin çalıştırılması gerektiğinde, geçmiş tablosunun uygun satırları belleğe yüklenir. Daha sonra bu *geçmiş olaylar* daha önceden denetim noktası durumuna geri dönmek için Orchestrator işlev koduna yeniden yürütülür. Durumu bu şekilde yeniden derlemek için yürütme geçmişinin kullanılması olay kaynağını belirleme [düzeniyle](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)etkilenir.

## <a name="instances-table"></a>Örnekler tablosu

**Örnekler** tablosu, bir görev hub 'ında bulunan tüm düzenleme ve varlık örneklerinin durumlarını içeren başka bir Azure depolama tablosudur. Örnek oluşturulurken bu tabloya yeni satırlar eklenir. Bu tablonun bölüm anahtarı Orchestration örnek KIMLIĞI veya varlık anahtarıdır ve satır anahtarı sabit bir sabittir. Düzenleme veya varlık örneği başına bir satır vardır.

Bu tablo, `GetStatusAsync` (.NET) ve `getStatus` (JavaScript) API 'Lerinin yanı sıra [durum sorgusu HTTP API](durable-functions-http-api.md#get-instance-status)'sindeki örnek sorgu isteklerini karşılamak için kullanılır. Sonunda daha önce bahsedilen **Geçmiş** tablosunun içeriğiyle tutarlı tutulur. Örnek sorgu işlemlerini bu şekilde sağlamak için ayrı bir Azure depolama tablosunun kullanılması [komut ve sorgu sorumluluklarının ayrılığı (CQRS) düzeniyle](https://docs.microsoft.com/azure/architecture/patterns/cqrs)etkilenir.

## <a name="internal-queue-triggers"></a>İç sıra Tetikleyicileri

Orchestrator işlevleri ve etkinlik işlevleri, işlev uygulamasının görev hub 'ında iç kuyruklar tarafından tetiklenir. Kuyrukları bu şekilde kullanmak, güvenilir "en az bir kez" ileti teslimi garantisi sağlar. Dayanıklı İşlevler iki tür kuyruk vardır: **Denetim kuyruğu** ve **iş öğesi kuyruğu**.

### <a name="the-work-item-queue"></a>İş öğesi kuyruğu

Dayanıklı İşlevler görev hub 'ı başına bir iş öğesi kuyruğu vardır. Bu temel bir sıradır ve Azure Işlevlerinde diğer `queueTrigger` kuyrukla benzer şekilde davranır. Bu kuyruk, tek bir iletiyi aynı anda kuyruktan ayırarak durum bilgisiz *etkinlik işlevlerini* tetiklemek için kullanılır. Bu iletilerin her biri, etkinlik işlevi girişleri ve yürütülecek işlev gibi ek meta veriler içerir. Bir Dayanıklı İşlevler uygulaması birden çok VM 'ye ölçeklenirken, bu VM 'Ler iş öğesi sırasından iş elde etmek için tüm yarışmaya rekabet sağlar.

### <a name="control-queues"></a>Denetim kuyrukları

Dayanıklı İşlevler görev hub 'ı başına birden fazla *Denetim kuyruğu* vardır. *Denetim kuyruğu* daha basit iş öğesi sırasından daha karmaşıktır. Denetim kuyrukları, durum bilgisi olan Orchestrator ve varlık işlevlerini tetiklemek için kullanılır. Orchestrator ve Entity işlev örnekleri durum bilgisi olmayan tekton olduğundan, VM 'Ler arasında yük dağıtmak için rekabet eden bir tüketici modeli kullanılması mümkün değildir. Bunun yerine, Orchestrator ve varlık iletileri denetim kuyrukları arasında yük dengelidir. Bu davranış hakkında daha fazla ayrıntı, sonraki bölümlerde bulunabilir.

Denetim kuyrukları çeşitli düzenleme yaşam döngüsü ileti türlerini içerir. [Orchestrator denetim iletilerini](durable-functions-instance-management.md), etkinlik işlevi *Yanıt* iletilerini ve Zamanlayıcı iletilerini içeren örneklere örnek olarak verilebilir. 32 ' den fazla ileti, tek bir yoklamada denetim sırasından kaldırılır. Bu iletiler, yük verilerinin yanı sıra hangi düzenleme örneğini amaçladığı dahil meta verileri içerir. Aynı düzenleme örneği için birden fazla sıraya alınmış ileti tasarlanıyorsa, bunlar toplu işlem olarak işlenir.

### <a name="queue-polling"></a>Sıra yoklama

Dayanıklı görev uzantısı, depolama işlem maliyetlerinde boşta sıra yoklamanın etkisini azaltmak için rastgele bir üstel geri alma algoritması uygular. Bir ileti bulunduğunda, çalışma zamanı hemen başka bir iletiyi denetler; hiçbir ileti bulunamadığında, yeniden denemeden önce bir süre bekler. Sonraki başarısız bir kuyruk iletisi almaya çalıştıktan sonra, bekleme süresi, varsayılan olarak 30 saniye olacak şekilde en fazla bekleme süresine ulaşana kadar artar.

En fazla yoklama gecikmesi [Host. JSON dosyasındaki](../functions-host-json.md#durabletask)`maxQueuePollingInterval` özelliği aracılığıyla yapılandırılabilir. Bu özelliğin daha yüksek bir değere ayarlanması, daha yüksek ileti işleme gecikmeleri oluşmasına neden olabilir. Daha yüksek gecikme süreleri yalnızca işlem yapılmadan sonra beklenmelidir. Bu özelliğin daha düşük bir değere ayarlanması, daha yüksek depolama işlemleri nedeniyle depolama maliyetlerinin artmasına neden olabilir.

> [!NOTE]
> Azure işlevleri tüketim ve Premium planlarında çalışırken, [Azure Işlevleri ölçek denetleyicisi](../functions-scale.md#how-the-consumption-and-premium-plans-work) her bir denetimi ve iş öğesi kuyruğunu her 10 saniyede bir yoklamaya çalışır. Bu ek yoklama, işlev uygulaması örneklerinin ne zaman etkinleştireceğinize karar vermek ve ölçek kararları almak için gereklidir. Yazma sırasında, bu 10 saniyelik Aralık sabittir ve yapılandırılamaz.

## <a name="storage-account-selection"></a>Depolama hesabı seçimi

Dayanıklı İşlevler tarafından kullanılan kuyruklar, tablolar ve Bloblar yapılandırılmış bir Azure depolama hesabında oluşturulur. Kullanılacak hesap, **Host. JSON** dosyasında `durableTask/storageProvider/connectionStringName` ayarı (veya dayanıklı işlevler 1. x) `durableTask/azureStorageConnectionStringName` ayarı kullanılarak belirtilebilir.

### <a name="durable-functions-2x"></a>Dayanıklı İşlevler 2. x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Dayanıklı İşlevler 1. x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Belirtilmemişse, varsayılan `AzureWebJobsStorage` depolama hesabı kullanılır. Ancak, performansa duyarlı iş yükleri için varsayılan olmayan bir depolama hesabının yapılandırılması önerilir. Dayanıklı İşlevler Azure Storage 'ı yoğun bir şekilde kullanır ve adanmış bir depolama hesabı kullanılması, Azure Işlevleri ana bilgisayarı tarafından iç kullanımlardan Dayanıklı İşlevler depolama kullanımını yalıtır.

## <a name="orchestrator-scale-out"></a>Orchestrator ölçeği genişletme

Etkinlik işlevleri durum bilgisiz ve VM 'Ler eklenerek otomatik olarak ölçeklendirilir. Diğer yandan Orchestrator işlevleri ve varlıkları bir veya daha fazla denetim kuyruğuna göre *bölümlenmiştir* . Denetim sıralarının sayısı **Host. JSON** dosyasında tanımlanmıştır. Aşağıdaki örnek Host. JSON kod parçacığı, `durableTask/storageProvider/partitionCount` özelliğini (veya Dayanıklı İşlevler 1. x) `durableTask/partitionCount` `3`olarak ayarlar.

### <a name="durable-functions-2x"></a>Dayanıklı İşlevler 2. x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Dayanıklı İşlevler 1. x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Bir görev hub 'ı, 1 ile 16 arasında bölüm arasında yapılandırılabilir. Belirtilmemişse, varsayılan bölüm sayısı **4**' dir.

Birden çok işlev ana bilgisayar örneğine (genellikle farklı VM 'lerde) ölçeklendirirken, her örnek denetim kuyruklarından birinde bir kilit alır. Bu kilitler, BLOB depolama kiraları olarak dahili olarak uygulanır ve bir düzenleme örneğinin veya varlığının aynı anda yalnızca tek bir konak örneği üzerinde çalıştığından emin olun. Bir görev hub 'ı üç denetim kuyruğu ile yapılandırıldıysa, düzenleme örnekleri ve varlıklar, üç VM 'ye kadar fazla yük dengeli olabilir. Etkinlik işlev yürütmesinin kapasitesini artırmak için ek VM 'Ler eklenebilir.

Aşağıdaki diyagramda, Azure Işlevleri 'nin genişleme ortamında depolama varlıklarıyla nasıl etkileşim kurduğu gösterilmektedir.

![Diyagramı Ölçeklendir](./media/durable-functions-perf-and-scale/scale-diagram.png)

Önceki diyagramda gösterildiği gibi, tüm VM 'Ler iş öğesi sırasındaki iletiler için rekabet ediyor. Ancak, denetim kuyruklarından yalnızca üç VM tarafından ileti alabilir ve her VM tek bir denetim kuyruğunu kilitler.

Düzenleme örnekleri ve varlıklar tüm denetim kuyruğu örneklerine dağıtılır. Dağıtım, düzenleme veya varlık adı ile anahtar çiftinin örnek KIMLIĞI karma oluşturarak yapılır. Düzenleme örneği kimlikleri varsayılan olarak rastgele GUID 'lerdir ve örneklerin tüm denetim sıralarında eşit bir şekilde dağıtılmasını sağlar.

Genellikle, Orchestrator işlevlerinin hafif olması amaçlanmıştır ve büyük miktarlarda bilgi işlem gücü gerektirmemelidir. Bu nedenle, düzenleme için harika verimlilik sağlamak üzere çok sayıda denetim kuyruğu bölümü oluşturmak gerekli değildir. Ağır çalışmanın çoğu, sonsuz bir şekilde ölçeklenebilen, durumsuz etkinlik işlevlerinde yapılmalıdır.

## <a name="auto-scale"></a>Otomatik Ölçeklendirme

Tüketim ve elastik Premium planlarında çalışan tüm Azure Işlevlerinde olduğu gibi, Dayanıklı İşlevler [Azure işlevleri ölçek denetleyicisi](../functions-scale.md#runtime-scaling)aracılığıyla otomatik ölçeklendirmeyi destekler. Ölçek denetleyicisi, düzenli aralıklarla _göz atma_ komutları vererek tüm kuyrukların gecikmesini izler. Atılamıyor iletilerinin gecikme sürelerini temel alarak, ölçek denetleyicisi VM ekleme veya kaldırma konusunda karar verir.

Ölçek denetleyicisi denetim sırası ileti gecikmelerinin çok yüksek olduğunu belirlerse, ileti gecikmesi kabul edilebilir bir düzeye düşene veya denetim sırası bölüm sayısına ulaştığı sürece VM örnekleri eklenir. Benzer şekilde, Bölüm sayımından bağımsız olarak iş öğesi kuyruğu gecikme süresi yüksekse, ölçek denetleyicisi sürekli olarak VM örnekleri ekler.

> [!NOTE]
> Dayanıklı İşlevler 2,0 ' den başlayarak, işlev uygulamaları elastik Premium plandaki VNET korumalı hizmet uç noktaları içinde çalışacak şekilde yapılandırılabilir. Bu yapılandırmada Dayanıklı İşlevler, ölçek denetleyicisi yerine ölçek isteklerini başlatacak şekilde tetikler.

## <a name="thread-usage"></a>İş parçacığı kullanımı

Orchestrator işlevleri, yürütmenin çok sayıda yeniden yürütmeye göre belirleyici olmasını sağlamak için tek bir iş parçacığında yürütülür. Bu tek iş parçacıklı yürütme nedeniyle, Orchestrator işlevi iş parçacıklarının CPU yoğun görevleri gerçekleştirmediği, g/ç veya herhangi bir nedenden dolayı engel olmadığı önemli bir işlemdir. G/ç, engelleme veya birden çok iş parçacığı gerektirebilecek herhangi bir iş, etkinlik işlevlerine taşınmalıdır.

Etkinlik işlevleri, normal kuyruk ile tetiklenen işlevlerle aynı davranışa sahiptir. G/ç 'yi güvenle yapabilir, yoğun CPU kullanan işlemler yürütebilir ve birden çok iş parçacığı kullanabilirler. Etkinlik Tetikleyicileri durum bilgisiz olduğundan, sınırsız sayıda VM 'ye serbestçe ölçeklenebilirler.

Varlık işlevleri de tek bir iş parçacığında yürütülür ve işlemler tek seferde işlenir. Ancak, varlık işlevlerinin yürütülebilecek kod türü üzerinde hiçbir kısıtlaması yoktur.

## <a name="concurrency-throttles"></a>Eşzamanlılık kısıtları

Azure Işlevleri tek bir uygulama örneği içinde eşzamanlı olarak birden çok işlevin yürütülmesini destekler. Bu eşzamanlı yürütme paralellik arttırmaya yardımcı olur ve tipik bir uygulamanın zaman içinde deneymesinin "soğuk başladığı" sayısını en aza indirir. Ancak, yüksek eşzamanlılık, ağ bağlantıları veya kullanılabilir bellek gibi VM başına sistem kaynaklarını tüketebilir. İşlev uygulamasının ihtiyaçlarına bağlı olarak, yüksek yükleme durumlarında belleğin tükenme olasılığını ortadan kaldırmak için örnek başına eşzamanlılık azaltma gerekebilir.

Etkinlik, Orchestrator ve varlık işlevi eşzamanlılık sınırları, **Host. JSON** dosyasında yapılandırılabilir. İlgili ayarlar, etkinlik işlevleri için `durableTask/maxConcurrentActivityFunctions` ve hem Orchestrator hem de varlık işlevleri için `durableTask/maxConcurrentOrchestratorFunctions`.

### <a name="functions-20"></a>İşlevler 2,0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>İşlevler 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

Önceki örnekte, tek bir VM 'de aynı anda en fazla 10 Orchestrator veya Entity işlevleri ve 10 etkinlik işlevi çalıştırılabilir. Belirtilmezse, eşzamanlı etkinlik ve Orchestrator ya da varlık işlevi yürütmelerinin sayısı, sanal makine üzerindeki çekirdek sayısına göre 10X üzerinden yapılır.

> [!NOTE]
> Bu ayarlar, tek bir VM 'de bellek ve CPU kullanımının yönetilmesine yardımcı olmak için yararlıdır. Ancak, birden çok VM arasında ölçeklendirildiğinde, her VM 'nin kendi sınırları vardır. Bu ayarlar, genel düzeyde eşzamanlılık denetlemek için kullanılamaz.

## <a name="extended-sessions"></a>Genişletilmiş oturumlar

Genişletilmiş oturumlar, iletileri işlemeyi bitirdikten sonra bile, bellek içinde ayarları ve varlıkları tutan bir ayardır. Genişletilmiş oturumların etkinleştirilmesinin tipik etkisi, Azure depolama hesabına ve genel olarak geliştirilmiş aktarım hızına karşı g/ç 'yi azaltmıştır.

`durableTask/extendedSessionsEnabled`, **Host. JSON** dosyasında `true` olarak ayarlayarak genişletilmiş oturumları etkinleştirebilirsiniz. `durableTask/extendedSessionIdleTimeoutInSeconds` ayarı, boş bir oturumun bellekte tutulacağı süreyi denetlemek için kullanılabilir:

**İşlevler 2,0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**İşlevler 1,0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Bu ayarın farkında olması için iki olası kenarı vardır:

1. İşlev uygulaması bellek kullanımında genel bir artış vardır.
2. Çok sayıda eşzamanlı, kısa süreli Orchestrator veya varlık işlevi yürütmeleri varsa, üretilen iş için genel bir azalma olabilir.

Örnek olarak, `durableTask/extendedSessionIdleTimeoutInSeconds` 30 saniyeye ayarlanırsa, 1 saniyeden kısa bir süre içinde çalışan kısa süreli bir Orchestrator veya varlık işlevi bölümü 30 saniye boyunca belleği kaplar. Ayrıca, daha önce bahsedilen `durableTask/maxConcurrentOrchestratorFunctions` kotasına göre sayılır, bu da diğer Orchestrator veya varlık işlevlerinin çalışmasını engelleyebilir.

Orchestrator ve Entity işlevlerinde genişletilmiş oturumların belirli etkileri, sonraki bölümlerde açıklanmıştır.

### <a name="orchestrator-function-replay"></a>Orchestrator işlevi yeniden yürütme

Daha önce belirtildiği gibi, Orchestrator işlevleri **Geçmiş** tablosunun içerikleri kullanılarak yeniden yürütülür. Varsayılan olarak, bir denetim kuyruğundan bir toplu ileti sıralandığında, Orchestrator işlev kodu yeniden yürütülür. Genişletilmiş Oturumlar etkinleştirildiğinde, Orchestrator işlev örnekleri bellekte daha uzun tutulur ve yeni iletiler tam geçmiş yeniden yürütme olmadan işlenebilir.

Genişletilmiş oturumların performans geliştirmesi en sık aşağıdaki durumlarda gözlemlenmiştir:

* Aynı anda çalışan sınırlı sayıda düzenleme örneği olduğunda.
* Düzenlemeler, hızlıca tamamlanan çok sayıda sıralı eyleme (ör. yüzlerce etkinlik işlevi çağrısı) sahiptir.
* , Fan ve fanı yaparken, aynı anda tamamlanan çok sayıda eylem.
* Orchestrator işlevlerinin büyük iletileri işlemesi veya CPU yoğunluğu olan herhangi bir veri işlemeyi yapması gerektiğinde.

Diğer tüm durumlarda, genellikle Orchestrator işlevleri için bir observable performans geliştirmesi yoktur.

> [!NOTE]
> Bu ayarlar yalnızca bir Orchestrator işlevi tam olarak geliştirilip sınandıktan sonra kullanılmalıdır. Varsayılan agresif yeniden yürütme davranışı, geliştirme sırasında [Orchestrator işlev kodu kısıtlamaları](durable-functions-code-constraints.md) ihlallerinin algılanması için yararlı olabilir ve bu nedenle varsayılan olarak devre dışıdır.

### <a name="entity-function-unloading"></a>Varlık işlevini kaldırma

Varlık işlevleri, tek bir toplu işte 20 ' ye kadar işlem işleyebilir. Bir varlık bir işlem toplu işlemini işlemeyi tamamladığında, durumu devam ettirir ve bellekten kaldırır. Genişletilmiş oturumlar ayarını kullanarak varlıkların bellekten kaldırılmasını erteleyebilirsiniz. Varlıklar, daha önce olduğu gibi durum değişikliklerini kalıcı olmaya devam eder, ancak Azure depolama alanındaki yükleme sayısını azaltmak için yapılandırılan süre boyunca bellekte kalır. Azure depolama 'dan gerçekleştirilen bu yükleme azaltmaları, sık erişilen varlıkların genel performansını iyileştirebilir.

## <a name="performance-targets"></a>Performans hedefleri

Bir üretim uygulaması için Dayanıklı İşlevler kullanmayı planlarken, planlama sürecinde performans gereksinimlerini erken göz önünde bulundurmanız önemlidir. Bu bölümde, bazı temel kullanım senaryoları ve beklenen en fazla aktarım hızı sayısı ele alınmaktadır.

* **Sıralı etkinlik yürütme**: Bu senaryo, bir dizi etkinlik işlevlerini diğeri dışında çalıştıran bir Orchestrator işlevini açıklar. [Işlev zincirleme](durable-functions-sequence.md) örneğine en yakından benzer.
* **Paralel etkinlik yürütme**: Bu senaryo, çok sayıda etkinlik işlevini paralel olarak yürüten bir Orchestrator Işlevini [, fanı-Out, fan-ın](durable-functions-cloud-backup.md) düzenlerini açıklar.
* **Paralel yanıt işleme**: Bu senaryo [, fan ve fan](durable-functions-cloud-backup.md) deseninin ikinci yarısıdır. Bu, fan performansına odaklanır. Fanı 'den farklı olarak, fanı tek bir Orchestrator işlev örneği tarafından yapılır ve bu nedenle yalnızca tek bir VM üzerinde çalıştırılabilir.
* **Dış olay işleme**: Bu senaryo, tek seferde bir tane olmak üzere [dış olaylar](durable-functions-external-events.md)üzerinde bekleyen tek bir Orchestrator işlev örneğini temsil eder.
* **Varlık işlem işleme**: Bu senaryo, _tek_ bir [sayaç varlığının](durable-functions-entities.md) ne kadar hızlı bir işlem akışı işleyeceğini sınar.

> [!TIP]
> Fanı 'den farklı olarak, fanı-ın işlemleri tek bir VM ile sınırlıdır. Uygulamanız, fanı-Out, fanı-ın düzenlerini kullanıyorsa ve fan performansı hakkında endişeleriniz varsa, etkinlik işlevi fanı ' ı birden çok [alt](durable-functions-sub-orchestrations.md)düzenleme sırasında alt olarak bölüyi göz önünde bulundurun.

Aşağıdaki tabloda, daha önce açıklanan senaryolar için beklenen *en fazla* aktarım hızı sayısı gösterilmektedir. "Örnek", Azure App Service bir tek küçük ([a1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) VM üzerinde çalışan bir Orchestrator işlevinin tek bir örneğini ifade eder. Her durumda, [genişletilmiş oturumların](#orchestrator-function-replay) etkin olduğu varsayılır. Gerçek sonuçlar, işlev kodu tarafından gerçekleştirilen CPU veya g/ç çalışmasına bağlı olarak farklılık gösterebilir.

| Senaryo | Aktarım hızı üst sınırı |
|-|-|
| Sıralı etkinlik yürütme | saniyede 5 etkinlik/örnek |
| Paralel Etkinlik yürütme (fan-giden) | saniyede 100 etkinlik, örnek başına |
| Paralel yanıt işleme (fan-ın) | saniyede 150 yanıt/örnek |
| Dış olay işleme | saniyede 50 olay, örnek başına |
| Varlık işlem işleme | saniye başına 64 işlem |

> [!NOTE]
> Bu sayılar Dayanıklı İşlevler uzantısının v 1.4.0 (GA) sürümü itibariyle geçerli. Bu sayılar, özellik ve iyileştirmeler yapıldığından zaman içinde değişebilir.

Bekleyeceğiniz işleme numaralarını görmüyorsanız CPU ve bellek kullanımınız sağlıklı görünüyorsa, sorunun [depolama hesabınızın durumuyla](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)ilgili olup olmadığını denetleyin. Dayanıklı İşlevler uzantısı, Azure depolama hesabına önemli bir yük yerleştirebilir ve yeterince yüksek yükleme, depolama hesabı azaltmasına neden olabilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Olağanüstü durum kurtarma ve coğrafi dağıtım hakkında bilgi edinin](durable-functions-disaster-recovery-geo-distribution.md)
