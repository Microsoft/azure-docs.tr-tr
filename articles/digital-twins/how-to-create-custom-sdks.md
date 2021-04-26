---
title: Oto Rest ile özel dil SDK 'Ları oluşturma
titleSuffix: Azure Digital Twins
description: Azure dijital TWINS kodunu yayımlanmış SDK 'Ları olmayan diğer dillerde yazmak için, AutoRest 'i nasıl kullanacağınızı öğrenin.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751121"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Oto Rest kullanarak Azure dijital TWINS için özel dil SDK 'Ları oluşturma

[Yayımlanmış bir Azure dijital TWINS SDK 'sı](how-to-use-apis-sdks.md)olmayan bir dil kullanarak Azure Digital TWINS ile çalışmanız gerekiyorsa, bu makalede kendi SDK 'nizi seçtiğiniz dilde oluşturmak Için, oto Rest 'in nasıl kullanılacağı gösterilir. 

Bu makaledeki örneklerde bir [veri düzlemi SDK 'sı](how-to-use-apis-sdks.md#overview-data-plane-apis)oluşturulması gösterilmektedir, ancak bu işlem bir  [Denetim düzlemi SDK 'sı](how-to-use-apis-sdks.md#overview-control-plane-apis) oluşturmak için de çalışır.

## <a name="prerequisites"></a>Önkoşullar

Bir SDK oluşturmak için önce yerel makinenizde aşağıdaki kurulumu gerçekleştirmeniz gerekir:
* [**Oto Rest**](https://github.com/Azure/autorest)'i (sürüm 3) Install 2.0.4413 (sürüm 3)
* Otomatik Rest kullanımı için önkoşul olan [**Node.js**](https://nodejs.org)'yi yükler
* [ **Visual Studio 'yu** yükler](https://visualstudio.microsoft.com/downloads/)
* [Veri düzlemi Swagger klasöründen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)en son Azure dijital TWINS **veri düzlemi Swagger** (openapı) dosyasını, birlikte gelen örnek klasörüyle birlikte indirin. Swagger dosyası *digitaltwins.js* adlı bir dosyadır.

>[!TIP]
> Bunun yerine bir **Denetim düzlemi SDK 'sı** oluşturmak için, bu makaledeki adımları, veri düzlemi yerine [Denetim düzlemi Swagger klasöründen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) en son **Denetim düzlemi Swagger** (openapı) dosyasını kullanarak doldurun.

Makinenizde yukarıdaki listeden her şey varsa, bir SDK oluşturmak için, oto Rest 'i kullanmaya hazırsınız demektir.

## <a name="create-the-sdk-using-autorest"></a>Oto Rest kullanarak SDK oluşturma 

Node.js yüklendikten sonra, gerekli olan oto Rest sürümünün yüklü olduğundan emin olmak için bu komutu çalıştırabilirsiniz:
```cmd/sh
npm install -g autorest@2.0.4413
```

Azure dijital TWINS Swagger dosyasında oto Rest 'i çalıştırmak için aşağıdaki adımları izleyin:
1. Azure dijital TWINS Swagger dosyasını ve buna eşlik eden örnek klasörünü çalışma dizinine kopyalayın.
2. Bu çalışma dizinine geçiş yapmak için bir komut istemi penceresi kullanın.
3. Aşağıdaki komutla, oto REST komutunu çalıştırın. `<language>`Yer tutucusunu tercih ettiğiniz dille değiştirin: `python` , `java` ,, vb `go` . ( [Oto Rest Benioku](https://github.com/Azure/autorest)dosyasında seçeneklerin tam listesini bulabilirsiniz.)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Sonuç olarak, çalışma dizininizde *Digitaltwınsapı* adlı yeni bir klasör görürsünüz. Oluşturulan SDK dosyaları *Digitaltwınsapı* ad alanına sahip olacaktır. Bu makaledeki kullanım örneklerinin geri kalanı aracılığıyla bu ad alanını kullanmaya devam edersiniz.

Oto Rest, çok çeşitli dil kodu oluşturucuları destekler.

## <a name="make-the-sdk-into-a-class-library"></a>SDK 'Yı bir sınıf kitaplığı olarak oluşturma

Doğrudan bir .NET çözümüne, oto Rest tarafından oluşturulan dosyaları dahil edebilirsiniz. Ancak, Azure dijital TWINS SDK 'sını çeşitli ayrı projelere (istemci uygulamalarınız, Azure Işlevleri Uygulamalarınız ve daha fazlası) dahil etmek isteyebilirsiniz. Bu nedenle, oluşturulan dosyalardan ayrı bir proje (.NET sınıf kitaplığı) oluşturmak yararlı olabilir. Daha sonra, bu sınıf kitaplığı projesini bir proje başvurusu olarak çeşitli çözümlere dahil edebilirsiniz.

Bu bölüm, SDK 'nın kendi projesi olan ve diğer projelere dahil olabilen bir sınıf kitaplığı olarak nasıl oluşturulacağı hakkında yönergeler sağlar. Bu adımlar **Visual Studio 'yu** kullanır.

Adımlar aşağıdaki gibidir:

1. Sınıf kitaplığı için yeni bir Visual Studio çözümü oluşturma
2. Proje adı olarak *Digitaltwınsapı* kullanın
3. Çözüm Gezgini, oluşturulan çözümün *Digitaltwınsapı* projesini sağ seçin ve *> var olan öğeyi Ekle...* seçeneğini belirleyin.
4. SDK 'Yı oluşturduğunuz klasörü bulun ve kök düzeyindeki dosyaları seçin
5. "Tamam" düğmesine basın
6. Projeye bir klasör ekleyin (Çözüm Gezgini içinde projeyi sağ seçin ve *> yeni klasör ekle*' yi seçin.
7. Klasör *modellerini* adlandırın
8. Çözüm Gezgini *modeller* klasörünü sağ seçin ve *> var olan öğeyi Ekle...* seçeneğini belirleyin.
9. Oluşturulan SDK 'nın *modeller* klasöründeki dosyaları seçin ve "Tamam" düğmesine basın

SDK 'Yı başarılı bir şekilde derlemek için, projenizin bu başvurulara ihtiyacı olacak:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Bunları eklemek için *NuGet paket yöneticisi > açık araçlar > çözüm Için NuGet Paketlerini Yönet...*.

1. Panelde, *tarayıcı* sekmesinin seçili olduğundan emin olun
2. *Microsoft. Rest* 'i arayın
3. `ClientRuntime`Ve paketlerini seçip `ClientRuntime.Azure` çözümünüze ekleyin

Artık projeyi oluşturabilir ve yazdığınız herhangi bir Azure dijital TWINS uygulamasına bir proje başvurusu olarak ekleyebilirsiniz.

## <a name="tips-for-using-the-sdk"></a>SDK 'Yı kullanmaya yönelik ipuçları

Bu bölüm, oluşturulan SDK 'Yı kullanmaya yönelik genel bilgileri ve yönergeleri içerir.

### <a name="synchronous-and-asynchronous-calls"></a>Zaman uyumlu ve zaman uyumsuz çağrılar

Tüm SDK işlevleri, zaman uyumlu ve zaman uyumsuz sürümlerde gelir.

### <a name="typed-and-untyped-data"></a>Türü belirtilmiş ve türsüz veriler

REST API çağrılar genellikle kesin türü belirtilmiş nesneleri döndürür. Ancak Azure dijital TWINS, kullanıcıların TWINS için kendi özel türlerini tanımlamasına izin vermediğinden, birçok Azure dijital TWINS çağrısı için statik dönüş verilerini önceden tanımlamanız mümkün değildir. Bunun yerine, API 'Ler uygun olduğunda türü kesin belirlenmiş sarmalayıcı türleri döndürür ve özel olarak yazılmış ikizi verileri Json.NET nesnelerinde bulunur (API imzalarında "Object" veri türünün göründüğü her yerde kullanılır). Bu nesneleri kodunuzda uygun şekilde çevirebilirsiniz.

### <a name="error-handling"></a>Hata işleme

SDK 'da bir hata oluştuğunda (404 gibi HTTP hataları dahil), SDK bir özel durum oluşturur. Bu nedenle, try/catch blokları içindeki tüm API çağrılarını kapsüllemek önemlidir.

İşte bir ikizi eklemeye ve bu işlemdeki hataları yakamaya çalışan bir kod parçacığı:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Sayfalama

Oto Rest, SDK için iki tür sayfalama deseni üretir:
* Sorgu API 'SI hariç tüm API 'Ler için bir tane
* Bir sorgu API 'SI için

Sorgu olmayan disk belleği düzeninde, Azure dijital TWINS 'ten gelen bir giden ilişki listesinin nasıl alınacağını gösteren örnek bir yöntem aşağıda verilmiştir:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

İkinci model yalnızca sorgu API 'SI için oluşturulur. Açıkça bir kullanır `continuationToken` .

>[!TIP]
> Sayfaların alınması için bir temel neden, sorgu API çağrısı için [sorgu birimi ücretlerini](concepts-query-units.md) hesaplaalmaktır.

Bu düzene sahip bir örnek aşağıda verilmiştir:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Sonraki adımlar

SDK 'nizi kullanabileceğiniz bir istemci uygulaması oluşturma adımlarını gözden geçir:
* [*Öğretici: istemci uygulamasını kodlayın*](tutorial-code.md)
