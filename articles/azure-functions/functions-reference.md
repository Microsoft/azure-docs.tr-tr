---
title: Azure Işlevleri geliştirmeye yönelik kılavuz
description: Tüm programlama dillerinde ve bağlamalarda Azure 'da işlevleri geliştirmek için ihtiyacınız olan Azure Işlevleri kavramlarını ve tekniklerini öğrenin.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614828"
---
# <a name="azure-functions-developer-guide"></a>Azure İşlevleri geliştirici kılavuzu
Azure Işlevlerinde belirli işlevler, kullandığınız dil veya bağlama ne olursa olsun, birkaç temel teknik kavram ve bileşeni paylaşır. Belirli bir dile veya bağlamaya özgü öğrenme ayrıntılarına geçmeden önce, tüm bunlar için geçerli olan bu genel bakışı okuduğunuzdan emin olun.

Bu makalede, [Azure işlevlerine genel bakış ' ı](functions-overview.md)okuduğunuzu varsaymış olursunuz.

## <a name="function-code"></a>İşlev kodu
Bir *işlev* , Azure işlevlerinde birincil kavramdır. Bir işlev iki önemli parça içerir. Bu kod, çeşitli dillerde yazılmış ve bazı config, dosyadaki function.js. Derlenen diller için, bu yapılandırma dosyası kodunuzdaki ek açıklamalardan otomatik olarak oluşturulur. Komut dosyası dilleri için yapılandırma dosyasını kendiniz sağlamanız gerekir.

Dosyadaki function.js, işlevin tetikleyicisi, bağlamaları ve diğer yapılandırma ayarlarını tanımlar. Her işlevde tek bir tane tetikleyici olması gerekir. Çalışma zamanı, izlenecek olayları ve bir işlev yürütmesindeki verilerin içine nasıl geçirileceğini ve geri döneceğini öğrenmek için bu yapılandırma dosyasını kullanır. Aşağıda, dosyasında function.jsbir örnek verilmiştir.

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

`bindings`Özelliği, hem Tetikleyicileri hem de bağlamaları yapılandırdığınız yerdir. Her bağlama birkaç ortak ayarı ve belirli bir bağlama türüne özgü bazı ayarları paylaşır. Her bağlama için aşağıdaki ayarlar gereklidir:

| Özellik    | Değerler | Tür | Yorumlar|
|---|---|---|---|
| tür  | Bağlamanın adı.<br><br>Örneğin, `queueTrigger`. | string | |
| yön | `in`, `out`  | string | Bağlamanın işleve veri almak veya işlevden veri göndermek için olup olmadığını gösterir. |
| name | İşlev tanımlayıcısı.<br><br>Örneğin, `myQueue`. | string | İşlevdeki bağlantılı veriler için kullanılan ad. C# için bu bir bağımsız değişken adıdır; JavaScript için anahtar/değer listesindeki anahtardır. |

## <a name="function-app"></a>İşlev uygulaması
Bir işlev uygulaması, Azure 'da işlevlerinizin çalıştırıldığı bir yürütme bağlamı sağlar. Bu nedenle, işlevleriniz için dağıtım ve yönetim birimidir. İşlev uygulaması, yönetilen, dağıtılan ve birlikte ölçeklenen bir veya daha fazla bağımsız işlevden oluşur. Bir işlev uygulamasındaki tüm işlevler aynı fiyatlandırma planı, dağıtım yöntemi ve çalışma zamanı sürümünü paylaşır. İşlev uygulamasını, işlevlerinizi düzenlemek ve topluca yönetmek için bir yol olarak düşünün. Daha fazla bilgi için bkz. [bir işlev uygulamasını yönetme](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Bir işlev uygulamasındaki tüm işlevlerin aynı dilde yazılması gerekir. Azure Işlevleri çalışma zamanının [önceki sürümlerinde](functions-versions.md) bu gerekli değildir.

## <a name="folder-structure"></a>Klasör yapısı
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Yukarıdaki, bir Işlev uygulaması için varsayılan (ve önerilen) klasör yapısıdır. Bir işlevin kodunun dosya konumunu değiştirmek istiyorsanız, `scriptFile` dosyadaki _function.js_ bölümünü değiştirin. Ayrıca, Azure 'daki işlev uygulamanıza projenizi dağıtmak için [paket dağıtımı](deployment-zip-push.md) kullanmanızı öneririz. Ayrıca, [sürekli tümleştirme ve dağıtım](functions-continuous-deployment.md) ve Azure DevOps gibi mevcut araçları da kullanabilirsiniz.

> [!NOTE]
> Bir paketi el ile dağıtıyorsanız, _host.js_ dosya ve işlev klasörlerinde doğrudan klasöre dağıttığınızdan emin olun `wwwroot` . `wwwroot`Dağıtımlarınıza klasörü eklemeyin. Aksi takdirde, klasörler ile biter `wwwroot\wwwroot` .

#### <a name="use-local-tools-and-publishing"></a>Yerel araçları ve yayımlamayı kullanma
İşlev uygulamaları [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](./create-first-function-vs-code-csharp.md), [ıntellij](./functions-create-maven-intellij.md), [tutulma](./functions-create-maven-eclipse.md)ve [Azure Functions Core Tools](./functions-develop-local.md)gibi çeşitli araçlar kullanılarak yazılabilir ve yayımlanabilir. Daha fazla bilgi için bkz. [Azure işlevlerini yerel olarak kod ve test](./functions-develop-local.md)etme.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Azure portal işlevleri düzenleme
Azure portal yerleşik Işlevler Düzenleyicisi kodunuzu ve *function.jsdosya üzerinde* doğrudan satır içi olarak güncelleştirmenizi sağlar. Bu yalnızca küçük değişiklikler veya kavram provaları için önerilir-en iyi yöntem, VS Code gibi bir yerel geliştirme aracı kullanmaktır.

## <a name="parallel-execution"></a>Paralel yürütme
Çoklu iş parçacıklı bir işlev çalışma zamanının daha hızlı bir şekilde tetiklenmesi durumunda, çalışma zamanı işlevi paralel olarak birden çok kez çağırabilir.  Bir işlev uygulaması [Tüketim barındırma planını](event-driven-scaling.md)kullanıyorsa, işlev uygulaması otomatik olarak ölçeklendirebilir.  İşlev uygulamasının her örneği, uygulamanın tüketim barındırma planı üzerinde mi yoksa düzenli bir [App Service barındırma planı](../app-service/overview-hosting-plans.md)üzerinde mi çalıştığı, birden çok iş parçacığı kullanarak eşzamanlı işlev çağrılarını paralel olarak işleyebilir.  Her işlev uygulama örneğindeki maksimum eşzamanlı işlev çağırma sayısı, kullanılan tetikleyici türüne ve işlev uygulamasındaki diğer işlevler tarafından kullanılan kaynaklara göre değişir.

## <a name="functions-runtime-versioning"></a>İşlevler çalışma zamanı sürümü oluşturma

Uygulama ayarını kullanarak Işlevler çalışma zamanının sürümünü yapılandırabilirsiniz `FUNCTIONS_EXTENSION_VERSION` . Örneğin, "~ 3" değeri, işlev uygulamanızın ana sürümü olarak 3. x kullanacağını gösterir. İşlev uygulamaları, yayımlandıklarında her yeni küçük sürüme yükseltilir. İşlev uygulamanızın tam sürümünün nasıl görüntüleneceği dahil olmak üzere daha fazla bilgi için bkz. [Azure işlevleri çalışma zamanı sürümlerini hedefleme](set-runtime-version.md).

## <a name="repositories"></a>Depolar
Azure Işlevleri için kod açık kaynaktır ve GitHub depolarında depolanır:

* [Azure İşlevleri](https://github.com/Azure/Azure-Functions)
* [Azure Işlevleri ana bilgisayarı](https://github.com/Azure/azure-functions-host/)
* [Azure Işlevleri portalı](https://github.com/azure/azure-functions-ux)
* [Azure Işlevleri şablonları](https://github.com/azure/azure-functions-templates)
* [Azure Web İşleri SDK'sı](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure Web İşleri SDK'sı Uzantıları](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Bağlamalar
Desteklenen tüm bağlamaların bir tablosu aşağıda verilmiştir.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Bağlamalardan gelen hatalarla ilgili sorun mu yaşıyorsunuz? [Azure Işlevleri bağlama hata kodları](functions-bindings-error-pages.md) belgelerini gözden geçirin.


## <a name="connections"></a>Bağlantılar

İşlev projeniz, bağlantı bilgilerine yapılandırma sağlayıcısından ada göre başvurur. Bağlantı ayrıntılarını doğrudan kabul etmez ve bunların ortamlar genelinde değiştirilmesine izin verir. Örneğin, bir tetikleyici tanımı bir `connection` özelliği içerebilir. Bu bir bağlantı dizesine başvurabilir, ancak bağlantı dizesini doğrudan bir içinde ayarlayamazsınız `function.json` . Bunun yerine, `connection` bağlantı dizesini içeren bir ortam değişkeninin adına ayarlanır.

Varsayılan yapılandırma sağlayıcısı ortam değişkenlerini kullanır. Bunlar, Azure Işlevleri hizmetinde çalışırken ya da yerel olarak geliştirilirken [yerel ayarlar dosyasından](functions-run-local.md#local-settings-file) [uygulama ayarları](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) tarafından ayarlanabilir.

### <a name="connection-values"></a>Bağlantı değerleri

Bağlantı adı tek bir tam değere çözümlenirse, çalışma zamanı, genellikle bir gizli _dizi içeren bağlantı dizesi_ olarak değeri tanımlar. Bağlantı dizesinin ayrıntıları, bağlanmak istediğiniz hizmet tarafından tanımlanır.

Ancak, bir bağlantı adı birden çok yapılandırma öğesi koleksiyonuna da başvurabilir. Ortam değişkenleri, Çift alt çizgi ile biten paylaşılan bir ön ek kullanılarak bir koleksiyon olarak değerlendirilir `__` . Bu gruba, bu önek için bağlantı adı ayarlanarak izin verebilirsiniz.

Örneğin, `connection` bir Azure Blob tetikleyici tanımının özelliği olabilir `Storage1` . Adı olarak yapılandırılmış tek bir dize değeri olmadığı sürece `Storage1` `Storage1__serviceUri` `serviceUri` bağlantının özelliği için kullanılır. Bağlantı özellikleri her hizmet için farklıdır. Bağlantıyı kullanan uzantı için belgelere bakın.

### <a name="configure-an-identity-based-connection"></a>Kimlik tabanlı bağlantı yapılandırma

Azure Işlevlerinde bazı bağlantılar, gizli anahtar yerine bir kimlik kullanacak şekilde yapılandırılmıştır. Destek, bağlantıyı kullanarak uzantıya bağlıdır. Bazı durumlarda, bağlanmakta olduğunuz hizmet kimlik tabanlı bağlantıları desteklediğinden bile, Işlevlerde bir bağlantı dizesi de gerekebilir.

> [!IMPORTANT]
> Bağlama uzantısı kimlik tabanlı bağlantıları desteklese de bu yapılandırma, tüketim planında henüz desteklenmeyebilir. Aşağıdaki destek tablosuna bakın.

Kimlik tabanlı bağlantılar aşağıdaki tetikleyici ve bağlama uzantıları tarafından desteklenir:

| Uzantı adı | Uzantı sürümü                                                                                     | Tüketim planında destekleniyor |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Azure Blob     | [Sürüm 5.0.0-Beta1 veya üzeri](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | No                                    |
| Azure Kuyruk    | [Sürüm 5.0.0-Beta1 veya üzeri](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | No                                    |
| Azure Event Hubs    | [Sürüm 5.0.0-Beta1 veya üzeri](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | No                                    |

> [!NOTE]
> Temel davranışlar için Işlevler çalışma zamanı tarafından kullanılan depolama bağlantılarında kimlik tabanlı bağlantılar için destek henüz kullanılamamaktadır. Bu, `AzureWebJobsStorage` ayarın bir bağlantı dizesi olması gerektiği anlamına gelir.

#### <a name="connection-properties"></a>Bağlantı özellikleri

Azure hizmeti için kimlik tabanlı bağlantı aşağıdaki özellikleri kabul eder:

| Özellik    | Uzantılar için gerekli | Ortam değişkeni | Description |
|---|---|---|---|
| Hizmet URI 'SI | Azure blobu, Azure kuyruğu | `<CONNECTION_NAME_PREFIX>__serviceUri` |  Bağlanmakta olduğunuz hizmetin veri düzlemi URI 'SI. |
| Tam nitelikli ad alanı | Event Hubs | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | Tam nitelikli Olay Hub 'ı ad alanı. |

Belirli bir bağlantı türü için ek seçenekler desteklenebilir. Bağlantıyı kuran bileşen için lütfen belgelere bakın.

Azure Işlevleri hizmetinde barındırıldığında, kimlik tabanlı bağlantılar [yönetilen bir kimlik](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json)kullanır. Sistem tarafından atanan kimlik varsayılan olarak kullanılır. Diğer bağlamlarda (yerel geliştirme gibi) çalıştırıldığında geliştirici kimliğiniz bunun yerine alternatif bağlantı parametreleri kullanılarak özelleştirilebilecek olsa da kullanılır.

##### <a name="local-development"></a>Yerel geliştirme

Yerel olarak çalışırken, yukarıdaki yapılandırma çalışma zamanına yerel geliştirici kimliğinizi kullanmasını söyler. Bağlantı aşağıdaki konumlardan sırayla bir belirteç almaya çalışır:

- Microsoft uygulamaları arasında paylaşılan yerel bir önbellek
- Visual Studio 'da geçerli kullanıcı bağlamı
- Visual Studio Code geçerli kullanıcı bağlamı
- Azure CLı 'de geçerli kullanıcı bağlamı

Bu seçeneklerden hiçbiri başarılı olmazsa bir hata oluşur.

Bazı durumlarda, farklı bir kimlik kullanımını belirtmek isteyebilirsiniz. Bağlantı için alternatif kimliğe işaret eden yapılandırma özellikleri ekleyebilirsiniz.

> [!NOTE]
> Azure Işlevleri hizmetinde barındırılırken aşağıdaki yapılandırma seçenekleri desteklenmez.

İstemci KIMLIĞI ve gizli bir Azure Active Directory hizmet sorumlusu kullanarak bağlanmak için, yukarıdaki [bağlantı özelliklerine](#connection-properties) ek olarak aşağıdaki gerekli özelliklerle bağlantıyı tanımlayın:

| Özellik    | Ortam değişkeni | Description |
|---|---|---|
| Kiracı Kimliği | `<CONNECTION_NAME_PREFIX>__tenantId` | Azure Active Directory kiracı (Dizin) KIMLIĞI. |
| İstemci Kimliği | `<CONNECTION_NAME_PREFIX>__clientId` |  Kiracıdaki bir uygulama kaydının istemci (uygulama) KIMLIĞI. |
| Gizli anahtar | `<CONNECTION_NAME_PREFIX>__clientSecret` | Uygulama kaydı için oluşturulan bir istemci gizli anahtarı. |

`local.settings.json`Azure blob ile kimlik tabanlı bağlantı için gereken özellikler örneği: 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>Kimliğe izin ver

Kullanılan kimliğin istenen eylemleri gerçekleştirmek için izinleri olması gerekir. Bu, genellikle Azure RBAC 'de bir rol atanarak veya bağlanmakta olduğunuz hizmete bağlı olarak bir erişim ilkesinde kimlik belirtilerek yapılır. İzinlerin gerekli olduğu ve nasıl ayarlanabileceği hakkında her hizmet için belgelere bakın.

> [!IMPORTANT]
> Bazı izinler, tüm bağlamlarda gerekli olmayan hizmet tarafından açığa çıkabilir. Mümkün olduğunda, **en az ayrıcalık ilkesine** bağlı olarak yalnızca kimlik gerekli ayrıcalıkları veriliyor. Örneğin, uygulamanın bir Blobun okuması gerekiyorsa, [Depolama Blobu veri sahibi](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) bir okuma işlemi için aşırı Izinleri Içerdiğinden [Depolama Blobu veri okuyucusu](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) rolünü kullanın.


## <a name="reporting-issues"></a>Raporlama sorunları
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md)
* [Azure İşlevleri’ni yerel olarak kodlama ve test etme](./functions-develop-local.md)
* [Azure Işlevleri için en iyi uygulamalar](functions-best-practices.md)
* [Azure Işlevleri C# Geliştirici Başvurusu](functions-dotnet-class-library.md)
* [Azure Işlevleri Node.js geliştirici başvurusu](functions-reference-node.md)
