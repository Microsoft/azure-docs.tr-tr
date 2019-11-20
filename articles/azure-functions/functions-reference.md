---
title: Azure Işlevleri geliştirmeye yönelik kılavuz | Microsoft Docs
description: Tüm programlama dillerinde ve bağlamalarda Azure 'da işlevleri geliştirmek için ihtiyacınız olan Azure Işlevleri kavramlarını ve tekniklerini öğrenin.
author: ggailey777
manager: gwallace
keywords: Geliştirici Kılavuzu, Azure işlevleri, işlevler, olay işleme, Web kancaları, dinamik işlem, sunucusuz mimari
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: c60fedfe855cc803ee2f4b1c463e2b0614239c04
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982645"
---
# <a name="azure-functions-developers-guide"></a>Azure Işlevleri Geliştirici Kılavuzu
Azure Işlevlerinde belirli işlevler, kullandığınız dil veya bağlama ne olursa olsun, birkaç temel teknik kavram ve bileşeni paylaşır. Belirli bir dile veya bağlamaya özgü öğrenme ayrıntılarına geçmeden önce, tüm bunlar için geçerli olan bu genel bakışı okuduğunuzdan emin olun.

Bu makalede, [Azure işlevlerine genel bakış ' ı](functions-overview.md)okuduğunuzu varsaymış olursunuz.

## <a name="function-code"></a>İşlev kodu
Bir *işlev* , Azure işlevlerinde birincil kavramdır. Bir işlev, çeşitli dillerde yazılmış ve bazı config, function. JSON dosyası olmak üzere iki önemli parça içerir. Derlenen diller için, bu yapılandırma dosyası kodunuzdaki ek açıklamalardan otomatik olarak oluşturulur. Komut dosyası dilleri için yapılandırma dosyasını kendiniz sağlamanız gerekir.

Function. JSON dosyası işlevin tetikleyicisini, bağlamalarını ve diğer yapılandırma ayarlarını tanımlar. Her işlevde bir ve yalnızca bir tetikleyici vardır. Çalışma zamanı, izlenecek olayları ve bir işlev yürütmesindeki verilerin içine nasıl geçirileceğini ve geri döneceğini öğrenmek için bu yapılandırma dosyasını kullanır. Aşağıda örnek bir Function. JSON dosyası verilmiştir.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Daha fazla bilgi için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

`bindings` Özelliği, hem Tetikleyicileri hem de bağlamaları yapılandırdığınız yerdir. Her bağlama birkaç ortak ayarı ve belirli bir bağlama türüne özgü bazı ayarları paylaşır. Her bağlama için aşağıdaki ayarlar gereklidir:

| Özellik | Değerler/türler | Açıklamalar |
| --- | --- | --- |
| `type` |dize |Bağlama türü. Örneğin: `queueTrigger`. |
| `direction` |' ın ', ' Out ' |Bağlamanın işleve veri almak veya işlevden veri göndermek için olup olmadığını gösterir. |
| `name` |dize |İşlevdeki bağlantılı veriler için kullanılan ad. İçin C#bu bir bağımsız değişken adıdır; JavaScript için anahtar/değer listesindeki anahtardır. |

## <a name="function-app"></a>İşlev uygulaması
Bir işlev uygulaması, Azure 'da işlevlerinizin çalıştırıldığı bir yürütme bağlamı sağlar. Bu nedenle, işlevleriniz için dağıtım ve yönetim birimidir. İşlev uygulaması, yönetilen, dağıtılan ve birlikte ölçeklenen bir veya daha fazla bağımsız işlevden oluşur. Bir işlev uygulamasındaki tüm işlevler aynı fiyatlandırma planı, dağıtım yöntemi ve çalışma zamanı sürümünü paylaşır. İşlev uygulamasını, işlevlerinizi düzenlemek ve topluca yönetmek için bir yol olarak düşünün. Daha fazla bilgi için bkz. [bir işlev uygulamasını yönetme](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Bir işlev uygulamasındaki tüm işlevlerin aynı dilde yazılması gerekir. Azure Işlevleri çalışma zamanının [önceki sürümlerinde](functions-versions.md) bu gerekli değildir.

## <a name="folder-structure"></a>Klasör yapısı
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Yukarıdaki, bir Işlev uygulaması için varsayılan (ve önerilen) klasör yapısıdır. Bir işlevin kodunun dosya konumunu değiştirmek istiyorsanız, `scriptFile` _function. JSON_ dosyasının bölümünü değiştirin. Ayrıca, Azure 'daki işlev uygulamanıza projenizi dağıtmak için [paket dağıtımı](deployment-zip-push.md) kullanmanızı öneririz. Ayrıca, [sürekli tümleştirme ve dağıtım](functions-continuous-deployment.md) ve Azure DevOps gibi mevcut araçları da kullanabilirsiniz.

> [!NOTE]
> Bir paketi el ile dağıtıyorsanız, _Host. JSON_ dosyanızı ve işlev klasörlerinizi doğrudan `wwwroot` klasörüne dağıttığınızdan emin olun. Dağıtımlarınıza `wwwroot` klasörü eklemeyin. Aksi takdirde, klasörler ile `wwwroot\wwwroot` biter.

#### <a name="use-local-tools-and-publishing"></a>Yerel araçları ve yayımlamayı kullanma
İşlev uygulamaları [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [ıntellij](./functions-create-maven-intellij.md), [tutulma](./functions-create-maven-eclipse.md)ve [Azure Functions Core Tools](./functions-develop-local.md)gibi çeşitli araçlar kullanılarak yazılabilir ve yayımlanabilir. Daha fazla bilgi için bkz. [Azure işlevlerini yerel olarak kod ve test](./functions-develop-local.md)etme.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a>Azure portal işlevleri düzenleme
Azure portal yerleşik Işlevler Düzenleyicisi kodunuzu ve *function. JSON* dosyanızı doğrudan satır içinde güncelleştirmenize olanak tanır. Bu yalnızca küçük değişiklikler veya kavram provaları için önerilir-en iyi yöntem, VS Code gibi bir yerel geliştirme aracı kullanmaktır.

## <a name="parallel-execution"></a>Paralel yürütme
Çoklu iş parçacıklı bir işlev çalışma zamanının daha hızlı bir şekilde tetiklenmesi durumunda, çalışma zamanı işlevi paralel olarak birden çok kez çağırabilir.  Bir işlev uygulaması [Tüketim barındırma planını](functions-scale.md#how-the-consumption-and-premium-plans-work)kullanıyorsa, işlev uygulaması otomatik olarak ölçeklendirebilir.  İşlev uygulamasının her örneği, uygulamanın tüketim barındırma planı üzerinde mi yoksa düzenli bir [App Service barındırma planı](../app-service/overview-hosting-plans.md)üzerinde mi çalıştığı, birden çok iş parçacığı kullanarak eşzamanlı işlev çağrılarını paralel olarak işleyebilir.  Her işlev uygulama örneğindeki maksimum eşzamanlı işlev çağırma sayısı, kullanılan tetikleyici türüne ve işlev uygulamasındaki diğer işlevler tarafından kullanılan kaynaklara göre değişir.

## <a name="functions-runtime-versioning"></a>İşlevler çalışma zamanı sürümü oluşturma

`FUNCTIONS_EXTENSION_VERSION` Uygulama ayarını kullanarak işlevler çalışma zamanının sürümünü yapılandırabilirsiniz. Örneğin, "~ 2" değeri İşlev Uygulaması, ana sürümü olarak 2. x kullanacağınızı gösterir. İşlev uygulamaları, yayımlandıklarında her yeni küçük sürüme yükseltilir. İşlev uygulamanızın tam sürümünün nasıl görüntüleneceği dahil olmak üzere daha fazla bilgi için bkz. [Azure işlevleri çalışma zamanı sürümlerini hedefleme](set-runtime-version.md).

## <a name="repositories"></a>Depoları
Azure Işlevleri için kod açık kaynaktır ve GitHub depolarında depolanır:

* [Azure İşlevleri](https://github.com/Azure/Azure-Functions)
* [Azure Işlevleri ana bilgisayarı](https://github.com/Azure/azure-functions-host/)
* [Azure Işlevleri portalı](https://github.com/azure/azure-functions-ux)
* [Azure Işlevleri şablonları](https://github.com/azure/azure-functions-templates)
* [Azure Web Işleri SDK 'Sı](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK uzantıları](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bağlamalar
Desteklenen tüm bağlamaların bir tablosu aşağıda verilmiştir.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Bağlamalardan gelen hatalarla ilgili sorun mu yaşıyorsunuz? [Azure Işlevleri bağlama hata kodları](functions-bindings-error-pages.md) belgelerini gözden geçirin.

## <a name="reporting-issues"></a>Raporlama sorunları
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md)
* [Azure İşlevleri’ni yerel olarak kodlama ve test etme](./functions-develop-local.md)
* [Azure İşlevleri için En İyi Uygulamalar](functions-best-practices.md)
* [Azure Işlevleri C# geliştirici başvurusu](functions-dotnet-class-library.md)
* [Azure Işlevleri NodeJS geliştirici başvurusu](functions-reference-node.md)
