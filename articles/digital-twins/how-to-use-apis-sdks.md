---
title: Azure Digital Twins API’lerini ve SDK’larını kullanma
titleSuffix: Azure Digital Twins
description: Bkz. SDK ile birlikte Azure dijital TWINS API 'Leri ile çalışma.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 21247f6b396cb1f7016c74cbec528149c0583724
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587216"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Azure Digital Twins API’lerini ve SDK’larını kullanma

Azure dijital TWINS, örneğinizi ve onun öğelerini yönetmek için hem **Denetim düzlemi API 'leri** hem de **veri düzlemi API 'leri** ile birlikte sunulur. 
* Denetim düzlemi API 'leri [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) API 'lardır ve örneğinizi oluşturma ve silme gibi kaynak yönetimi işlemlerini kapsar. 
* Veri düzlemi API 'leri Azure dijital TWINS API 'leridir ve modelleri, TWINS 'Leri ve grafiği yönetme gibi veri yönetimi işlemleri için kullanılır.

Bu makale, kullanılabilir API 'Lere ve bunlarla etkileşime yönelik yöntemlere genel bir bakış sunar. REST API 'Leri doğrudan ilişkili Swaggers ( [Postman](how-to-use-postman.md)gibi bir araç aracılığıyla) ya da bir SDK aracılığıyla kullanabilirsiniz.

## <a name="overview-control-plane-apis"></a>Genel Bakış: denetim düzlemi API 'Leri

Denetim düzlemi API 'Leri, Azure dijital TWINS örneğinizi bir bütün olarak yönetmek için kullanılan [ARM](../azure-resource-manager/management/overview.md) API 'lardır, böylece tüm örneğinizi oluşturma veya silme gibi işlemleri kapsar. Bunları, uç noktaları oluşturmak ve silmek için de kullanacaksınız.

En güncel Denetim düzlemi API sürümü _**2020-12-01**_' dir.

Denetim düzlemi API 'Lerini kullanmak için:
* [Denetim düzlemi Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)deposundaki en son Swagger klasörüne başvurarak API 'leri doğrudan çağırabilirsiniz. Bu klasör ayrıca kullanımı gösteren örneklerin bir klasörünü de içerir.
* Şu anda, içindeki denetim API 'Leri için SDK 'Lara erişebilirsiniz...
  - [**.Net (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([başvuru [otomatik olarak oluşturulan]](/dotnet/api/overview/azure/digitaltwins/management)) ([kaynak](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([başvuru [otomatik olarak oluşturulan]](/java/api/overview/azure/digitaltwins)) ([kaynak](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([kaynak](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([kaynak](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Git**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([kaynak](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

Ayrıca, [Azure Portal](https://portal.azure.com) ve [CLI](how-to-use-cli.md)aracılığıyla Azure dijital TWINS ile etkileşime girerek denetim düzlemi API 'leri uygulayabilirsiniz.

## <a name="overview-data-plane-apis"></a>Genel Bakış: veri düzlemi API 'Leri

Veri düzlemi API 'Leri, Azure dijital TWINS örneğinizin içindeki öğeleri yönetmek için kullanılan Azure dijital TWINS API 'lardır. Yollar oluşturma, model yükleme, ilişki oluşturma ve TWINS yönetimi gibi işlemleri içerirler. Bunlar, aşağıdaki kategorilere büyük ölçüde ayrılabilir:
* **Digitaltwınmodeller** -digitaltwınmodeller kategorisi, bir Azure dijital TWINS örneğindeki [modelleri](concepts-models.md) yönetmek için API 'ler içerir. Yönetim etkinlikleri, DTDL 'de yazılan modellerin karşıya yükleme, doğrulama, alma ve silme işlemlerini içerir.
* **Digitaltwins** -digitaltwıns kategorisi, geliştiricilerin bir Azure dijital TWINS örneğinde bulunan [dijital TWINS](concepts-twins-graph.md) ve ilişkilerini oluşturmalarına, değiştirmesine ve silmesine izin veren API 'leri içerir.
* **Sorgu** -sorgu kategorisi, geliştiricilerin ilişkiler genelinde [ikizi grafiğinde dijital TWINS kümeleri bulmasına](how-to-query-graph.md) olanak tanır.
* **Olay** rotaları-olay rotaları kategorisi, verileri sistem ve aşağı akış Hizmetleri aracılığıyla [yönlendiren](concepts-route-events.md)API 'leri içerir.

En güncel veri düzlemi API sürümü _**2020-10-31**_' dir.

Veri düzlemi API 'Lerini kullanmak için:
* API 'Leri doğrudan çağırabilirsiniz...
   - [veri düzlemi Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)deposundaki en son Swagger klasörüne başvuruluyor. Bu klasör ayrıca kullanımı gösteren örneklerin bir klasörünü de içerir. 
   - [API başvuru belgelerini](/rest/api/azure-digitaltwins/)görüntüleme.
* **.Net (C#) SDK 'sını** kullanabilirsiniz. .NET SDK 'Yı kullanmak için...
   - paketi NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core)' dan görüntüleyebilir ve ekleyebilirsiniz. 
   - [SDK başvuru belgelerini](/dotnet/api/overview/azure/digitaltwins/client)görüntüleyebilirsiniz.
   - örnek bir klasör içeren SDK kaynağını GitHub 'da bulabilirsiniz: [.net Için Azure IoT dijital TWINS istemci kitaplığı](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - Bu makalenin [*.net (C#) SDK (veri düzlemi)*](#net-c-sdk-data-plane) bölümüne devam ederek ayrıntılı bilgi ve kullanım örnekleri görebilirsiniz.
* **Java SDK 'sını** kullanabilirsiniz. Java SDK 'sını kullanmak için...
   - paketi Maven 'ten görüntüleyebilir ve yükleyebilirsiniz: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - [SDK başvuru belgelerini](/java/api/overview/azure/digitaltwins/client) görüntüleyebilirsiniz
   - SDK kaynağını GitHub 'da bulabilirsiniz: [Java Için Azure IoT dijital TWINS istemci kitaplığı](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* **JavaScript SDK 'sını** kullanabilirsiniz. JavaScript SDK 'sını kullanmak için...
   - paketi NPM 'den görüntüleyebilir ve yükleyebilirsiniz: [JavaScript Için Azure Azure Digital TWINS çekirdek istemci kitaplığı](https://www.npmjs.com/package/@azure/digital-twins-core).
   - [SDK başvuru belgelerini](/javascript/api/@azure/digital-twins-core/)görüntüleyebilirsiniz.
   - SDK kaynağını GitHub 'da bulabilirsiniz: [JavaScript Için Azure Azure dijital TWINS çekirdek istemci kitaplığı](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* **Python SDK 'sını** kullanabilirsiniz. Python SDK 'Yı kullanmak için...
   - paketi PyPi 'dan görüntüleyebilir ve yükleyebilirsiniz: [Python Için Azure Azure Digital TWINS çekirdek istemci kitaplığı](https://pypi.org/project/azure-digitaltwins-core/).
   - [SDK başvuru belgelerini](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)görüntüleyebilirsiniz.
   - SDK kaynağını GitHub 'da bulabilirsiniz: [Python Için Azure Azure dijital TWINS çekirdek istemci kitaplığı](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Diğer bir dil için, oto Rest kullanarak bir SDK oluşturabilirsiniz. [*Nasıl yapılır: Azure dijital TWINS için özel SDK 'Lar oluşturma*](how-to-create-custom-sdks.md)bölümündeki yönergeleri Izleyerek oto Rest kullanın.

[CLI](how-to-use-cli.md)aracılığıyla Azure dijital TWINS ile etkileşime girerek Tarih düzlemi API 'leri de uygulayabilirsiniz.

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (veri düzlemi)

Azure Digital Twins .NET (C#) SDK 'Sı, .NET için Azure SDK 'sının bir parçasıdır. Açık kaynaktır ve Azure dijital TWINS veri düzlemi API 'Lerini temel alır.

> [!NOTE]
> SDK tasarımı hakkında daha fazla bilgi için bkz. [Azure SDK 'ları için genel tasarım ilkeleri](https://azure.github.io/azure-sdk/general_introduction.html) ve belirli [.net tasarım yönergeleri](https://azure.github.io/azure-sdk/dotnet_introduction.html).

SDK 'yı kullanmak için, projenize **Azure. DigitalTwins. Core** NuGet paketini ekleyin. Ayrıca, **Azure. Identity** paketinin en son sürümüne de ihtiyacınız olacaktır. Visual Studio 'da, NuGet Paket Yöneticisi 'Ni ( *araçlar > NuGet Paket Yöneticisi aracılığıyla erişilir > çözüm Için NuGet Paketlerini Yönet*) kullanarak bu paketleri ekleyebilirsiniz. Alternatif olarak, aşağıdaki NuGet paketi bağlantılarında bulunan komutlarla birlikte .NET komut satırı aracını kullanarak bunları projenize ekleyebilirsiniz:
* [**Azure. DigitalTwins. Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Bu paket, [.net Için Azure Digital TWINS SDK 'sına](/dotnet/api/overview/azure/digitaltwins/client)yöneliktir. 
* [**Azure. Identity**](https://www.nuget.org/packages/Azure.Identity). Bu kitaplık, Azure 'da kimlik doğrulamaya yardımcı olacak araçlar sağlar.

API 'Leri uygulamada kullanma hakkında ayrıntılı bilgi için bkz. [*öğretici: istemci uygulaması kodu*](tutorial-code.md). 

### <a name="net-sdk-usage-examples"></a>.NET SDK kullanım örnekleri

.NET SDK 'nın kullanımını gösteren bazı kod örnekleri aşağıda verilmiştir.

Hizmette kimlik doğrulaması yapın:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Bir modeli karşıya yükleyin:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Liste modelleri:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

TWINS oluştur:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Sorgu TWINS ve sonuçlar aracılığıyla döngü:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Bu örnek uygulama kodundan izlenecek yol için [*öğreticiye bakın: istemci uygulamasını kodlayın*](tutorial-code.md) . 

[.Net (C#) SDK 'sı Için GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)deposunda ek örnekler de bulabilirsiniz.

#### <a name="serialization-helpers"></a>Serileştirme yardımcıları

Serileştirme yardımcıları, temel bilgilere erişim için hızlı bir şekilde ikizi verileri oluşturmak veya seri durumdan çıkarmak amacıyla SDK içinde kullanılabilen yardımcı işlevlerdir. Core SDK yöntemleri varsayılan olarak JSON olarak ikizi verisi döndürdüğü için, ikizi verilerini daha fazla bölmek üzere bu yardımcı sınıfları kullanmak yararlı olabilir.

Kullanılabilir yardımcı sınıfları şunlardır:
* `BasicDigitalTwin`: Genel olarak dijital bir ikizi 'ın temel verilerini temsil eder
* `BasicDigitalTwinComponent`: Genel bir bileşen, `Contents` bir bileşenin özelliklerinde bir bileşeni temsil eder `BasicDigitalTwin`
* `BasicRelationship`: Genel olarak bir ilişkinin temel verilerini temsil eder
* `DigitalTwinsJsonPropertyName`: JSON serileştirme ve özel dijital ikizi türleri için seri hale getirme içinde kullanılacak dize sabitlerini içerir

##### <a name="deserialize-a-digital-twin"></a>Dijital ikizi serisini kaldırma

Ya da gibi tercih ettiğiniz JSON kitaplığını kullanarak ikizi verilerinin serisini her zaman kaldırabilirsiniz `System.Text.Json` `Newtonsoft.Json` . Bir ikizi temel erişimi için yardımcı sınıflar bunu daha kullanışlı hale getirir.

`BasicDigitalTwin`Yardımcı sınıfı ayrıca, ile ikizi üzerinde tanımlanan özelliklere erişmenizi sağlar `Dictionary<string, object>` . İkizi özelliklerini listelemek için şunu kullanabilirsiniz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin``System.Text.Json`öznitelikleri kullanır. `BasicDigitalTwin` [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true)ile birlikte kullanmak için, varsayılan oluşturucuya sahip istemciyi başlatmalısınız veya serileştirici seçeneğini özelleştirmek Istiyorsanız, [jsonobjectserializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true)kullanın.

##### <a name="create-a-digital-twin"></a>Dijital ikizi oluşturma

Sınıfını kullanarak `BasicDigitalTwin` , bir ikizi örneği oluşturmak için veri hazırlayacaksınız:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Yukarıdaki kod, aşağıdaki "el ile" varyanta eşdeğerdir:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>İlişki serisini kaldırma

İlişki verilerinin her zaman seçtiğiniz bir türle serisini kaldırabilirsiniz. Bir ilişkiye temel erişim için türü kullanın `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

`BasicRelationship`Yardımcı sınıfı ayrıca, bir ile ilişki üzerinde tanımlanan özelliklere erişmenizi sağlar `IDictionary<string, object>` . Özellikleri listelemek için şunları kullanabilirsiniz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Bir ilişki oluşturma

Sınıfını kullanarak `BasicRelationship` , bir ikizi örneğinde ilişki oluşturmaya yönelik verileri de hazırlayacaksınız:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>İkizi güncelleştirmesi için bir düzeltme eki oluşturma

TWINS ve ilişkiler için yapılan çağrıları güncelleştirme, [JSON Patch](http://jsonpatch.com/) yapısını kullanır. JSON yama işlemleri listesi oluşturmak için, `JsonPatchDocument` aşağıda gösterildiği gibi kullanabilirsiniz.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Genel API/SDK kullanım notları

> [!NOTE]
> Azure dijital TWINS 'in Şu anda **çıkış noktaları arası kaynak paylaşımını (CORS)** desteklemediğini lütfen unutmayın. Etki ve çözüm stratejileri hakkında daha fazla bilgi için bkz. *Kavramlar: Azure dijital TWINS çözümleri Için güvenlik*. [](concepts-security.md#cross-origin-resource-sharing-cors)

Aşağıdaki listede, API 'Leri ve SDK 'ları kullanmaya yönelik ek ayrıntılar ve genel yönergeler verilmiştir.

* Azure dijital TWINS API 'Lerine doğrudan çağrı yapmak için Postman gibi bir HTTP REST test aracı kullanabilirsiniz. Bu işlem hakkında daha fazla bilgi için bkz. [*nasıl yapılır: Istekleri Postman Ile oluşturma*](how-to-use-postman.md).
* SDK 'yı kullanmak için, sınıfın örneğini oluşturun `DigitalTwinsClient` . Oluşturucu, paketteki çeşitli kimlik doğrulama yöntemleriyle elde edilebilir kimlik bilgileri gerektirir `Azure.Identity` . Daha fazla bilgi için `Azure.Identity` bkz. [ad alanı belgeleri](/dotnet/api/azure.identity). 
* Başlarken faydalı olduğunu fark edebilirsiniz `InteractiveBrowserCredential` , ancak [yönetilen kimliğin](/dotnet/api/azure.identity.interactivebrowsercredential)kimlik bilgileri de dahil olmak üzere, Azure Digital TWINS ['e karşı MSI ile ayarlanan Azure işlevleri](../app-service/overview-managed-identity.md?tabs=dotnet) kimlik doğrulaması için kullanacağınız diğer birkaç seçenek vardır. Hakkında daha fazla bilgi için `InteractiveBrowserCredential` bkz. [sınıf belgeleri](/dotnet/api/azure.identity.interactivebrowsercredential).
* Azure dijital TWINS API 'Lerine yönelik istekler, Azure Digital TWINS örneğinin bulunduğu aynı [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) kiracısının parçası olan bir kullanıcı veya hizmet sorumlusu gerektirir. Azure dijital TWINS uç noktalarının kötü taramasını engellemek için, kaynak kiracının dışından erişim belirteçleri olan istekler bir "404 Sub-Domain bulunamadı" hata iletisi döndürür. Bu hata, kullanıcıya veya hizmet sorumlusuna Azure [AD B2B](../active-directory/external-identities/what-is-b2b.md) işbirliği aracılığıyla bir Azure dijital TWINS veri sahibi veya Azure Digital TWINS veri okuyucusu rolü verilse *bile* döndürülür. Birden çok kiracı genelinde erişim elde etme hakkında daha fazla bilgi için bkz. [*nasıl yapılır: yazma uygulaması kimlik doğrulaması kodu*](how-to-authenticate-client.md#authenticate-across-tenants).
* Tüm hizmet API çağrıları, sınıf üzerinde üye işlevleri olarak gösterilir `DigitalTwinsClient` .
* Tüm hizmet işlevleri, zaman uyumlu ve zaman uyumsuz sürümlerde bulunur.
* Tüm hizmet işlevleri, 400 veya üzeri bir dönüş durumu için bir özel durum oluşturur. Çağrıları bir bölüme sardığınızdan `try` ve en azından yakaladığınızdan emin olun `RequestFailedExceptions` . Bu tür özel durum hakkında daha fazla bilgi için [buraya](/dotnet/api/azure.requestfailedexception)bakın.
* Çoğu hizmet yöntemi döndürür `Response<T>` veya ( `Task<Response<T>>` zaman uyumsuz çağrılar için), burada `T` hizmet çağrısının dönüş nesnesinin sınıfıdır. [`Response`](/dotnet/api/azure.response-1)Sınıfı, hizmet döndürmesini kapsüller ve dönüş değerlerini kendi `Value` alanına sunar.  
* Disk belleğine alınmış sonuçları olan hizmet yöntemleri, `Pageable<T>` veya `AsyncPageable<T>` sonuçlarını döndürür. Sınıfı hakkında daha fazla bilgi için buraya bakın `Pageable<T>` . hakkında daha fazla bilgi için [](/dotnet/api/azure.pageable-1) `AsyncPageable<T>` [buraya](/dotnet/api/azure.asyncpageable-1)bakın.
* Bir döngüsü kullanarak, disk belleğine alınmış sonuçları yineleyebilirsiniz `await foreach` . Bu süreç hakkında daha fazla bilgi için [buraya](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)bakın.
* Temel alınan SDK `Azure.Core` . SDK altyapısı ve türleri hakkında başvuru için bkz. [Azure ad alanı belgeleri](/dotnet/api/azure) .


Hizmet yöntemleri mümkün olduğunda kesin türü belirtilmiş nesneler döndürür. Ancak, Azure dijital TWINS, çalışma zamanında Kullanıcı tarafından özel olarak yapılandırılmış modelleri temel aldığı için (hizmete yüklenen DTDL modelleri aracılığıyla), çoğu hizmet API 'Leri JSON biçiminde ikizi verileri alır ve döndürür.

## <a name="monitor-api-metrics"></a>API ölçümlerini izleme

İstekler, gecikme ve başarısızlık oranı gibi API ölçümleri [Azure Portal](https://portal.azure.com/)görüntülenebilir. 

Portal giriş sayfasında, ayrıntılarını almak için Azure dijital TWINS örneğinizi arayın. *Ölçümler* sayfasını açmak Için Azure Digital TWINS örneğinin menüsündeki **ölçümler** seçeneğini belirleyin.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure dijital TWINS için ölçüm sayfasını gösteren ekran görüntüsü":::

Buradan, örneğiniz için ölçümleri görüntüleyebilir ve özel görünümler oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bkz. Postman kullanarak API 'lere doğrudan istek yapma:
* [*Nasıl yapılır: Postman ile istek yapma*](how-to-use-postman.md)

Ya da, bu öğreticiyle bir istemci uygulaması oluşturarak .NET SDK 'Yı kullanmayı deneyin:
* [*Öğretici: istemci uygulamasını kodlayın*](tutorial-code.md)
