---
title: Dijital ikizleri yönetme
titleSuffix: Azure Digital Twins
description: Bkz. ayrı ayrı TWINS ve ilişkileri alma, güncelleştirme ve silme.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 666e77a06bd2934622400cc2f11830d6ebc34ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954658"
---
# <a name="manage-digital-twins"></a>Dijital ikizleri yönetme

Ortamınızdaki varlıklar [dijital TWINS](concepts-twins-graph.md)tarafından temsil edilir. Dijital iklerinizi yönetmek oluşturma, değiştirme ve kaldırma işlemini içerebilir. Bu işlemleri yapmak için, [**Digitaltwins API 'lerini**](/rest/api/digital-twins/dataplane/twins), [.net (C#) SDK 'Sını](/dotnet/api/overview/azure/digitaltwins/client)veya [Azure dijital TWINS CLI](how-to-use-cli.md)' yi kullanabilirsiniz.

Bu makale, dijital TWINS yönetimine odaklanır; ilişkiler ve [ikizi Graf](concepts-twins-graph.md) bir bütün olarak çalışmak için bkz. [*nasıl yapılır: ilişkiler Ile ikizi grafiğini yönetme*](how-to-manage-graph.md).

> [!TIP]
> Tüm SDK işlevleri, zaman uyumlu ve zaman uyumsuz sürümlerde gelir.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-twins"></a>TWINS 'yi yönetmenin yolları

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-a-digital-twin"></a>Dijital ikizi oluşturma

Bir ikizi oluşturmak için, `CreateOrReplaceDigitalTwinAsync()` hizmet istemcisinde aşağıdaki gibi yöntemi kullanın:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwinCall":::

Dijital bir ikizi oluşturmak için şunları sağlamanız gerekir:
* Dijital ikizi için istenen KIMLIK
* Kullanmak istediğiniz [model](concepts-models.md)

İsteğe bağlı olarak, dijital ikizi tüm özellikleri için başlangıç değerleri sağlayabilirsiniz. Özellikler isteğe bağlı olarak değerlendirilir ve daha sonra ayarlanabilir, ancak **ayarlanana kadar bir ikizi parçası olarak gösterilmez.**

>[!NOTE]
>İkizi özelliklerinin **başlatılması gerekmiyorsa, ikizi oluşturulduğunda ikizi 'in** tüm [bileşenlerinin](concepts-models.md#elements-of-a-model) ayarlanması gerekir. Bunlar boş nesneler olabilirler, ancak bileşenlerin kendisi mevcut olmalıdır.

Model ve herhangi bir başlangıç özelliği değeri `initData` , ilgili verileri içeren BIR JSON dizesi olan parametresi aracılığıyla sağlanır. Bu nesneyi yapılandırma hakkında daha fazla bilgi için sonraki bölüme geçin.

> [!TIP]
> Bir ikizi oluşturduktan veya güncelleştirdikten sonra değişiklikler [sorgularda](how-to-query-graph.md)yansıtılmadan önce 10 saniyeye kadar gecikme olabilir. `GetDigitalTwin`API ( [Bu makalede daha sonra](#get-data-for-a-digital-twin)açıklanan) Bu gecikmeyle karşılaşmaz, bu nedenle anlık bir yanıt gerekiyorsa, yeni oluşturduğunuz TWINS 'nizi görmek IÇIN sorgulamak yerine API çağrısını kullanın. 

### <a name="initialize-model-and-properties"></a>Modeli ve özellikleri Başlat

İkizi oluşturulduğu sırada bir ikizi özelliklerini başlatabilirsiniz. 

İkizi oluşturma API 'SI, ikizi özelliklerinin geçerli bir JSON açıklamasına serileştirilmiş bir nesne kabul eder. Bir ikizi için JSON biçiminin açıklaması için bkz. [*Kavramlar: dijital TWINS ve ikizi Graph*](concepts-twins-graph.md) . 

İlk olarak, ikizi ve özellik verilerini temsil eden bir veri nesnesi oluşturabilirsiniz. Bir parametre nesnesini el ile ya da bir belirtilen yardımcı sınıfı kullanarak oluşturabilirsiniz. Her birine bir örnek aşağıda verilmiştir.

#### <a name="create-twins-using-manually-created-data"></a>El ile oluşturulan verileri kullanarak TWINS oluşturma

Herhangi bir özel yardımcı sınıf kullanmadan, bir ikizi özelliklerini temsil edebilirsiniz `Dictionary<string, object>` ; burada, `string` özelliğin adı, `object` özelliği ve değerini temsil eden bir nesnedir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

#### <a name="create-twins-with-the-helper-class"></a>Yardımcı sınıfla TWINS oluşturma

Yardımcı sınıfı, `BasicDigitalTwin` Özellik alanlarını doğrudan bir "ikizi" nesnesinde depolamanıza olanak tanır. `Dictionary<string, object>`Daha sonra doğrudan ikizi nesnesine eklenebilen bir kullanarak özellik listesini oluşturmak isteyebilirsiniz `CustomProperties` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

>[!NOTE]
> `BasicDigitalTwin` nesneler bir alanla gelir `Id` . Bu alanı boş bırakabilirsiniz, ancak bir KIMLIK değeri eklerseniz, çağrıya geçirilen ID parametresiyle eşleşmesi gerekir `CreateOrReplaceDigitalTwinAsync()` . Örnek:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Dijital ikizi için veri al

Aşağıdaki gibi bir yöntemi çağırarak herhangi bir dijital ikizi ayrıntılarına erişebilirsiniz `GetDigitalTwin()` :

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwinCall":::

Bu çağrı, gibi kesin türü belirtilmiş nesne türü olarak ikizi verileri döndürür `BasicDigitalTwin` . `BasicDigitalTwin` , SDK 'ya dahil edilen bir serileştirme yardımcı sınıfıdır ve bu, önceden ayrıştırılmış form içindeki Core ikizi meta verilerini ve özelliklerini döndürür. Bu, ikizi ayrıntılarını görüntülemek için nasıl kullanılacağına ilişkin bir örnek aşağıda verilmiştir:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin" highlight="2":::

Yöntemi ile bir ikizi aldığınızda, yalnızca en az bir kez ayarlanmış olan özellikler döndürülür `GetDigitalTwin()` .

>[!TIP]
>`displayName`Bir ikizi için, model meta verilerinin bir parçası olduğundan, ikizi örneği için veri alınırken gösterilmez. Bu değeri görmek için [modelden geri](how-to-manage-model.md#retrieve-models)alabilirsiniz.

Tek bir API çağrısını kullanarak birden çok TWINS almak için, sorgu API 'SI örneklerine bkz. [*nasıl yapılır: ikizi grafiğini sorgulama*](how-to-query-graph.md).

Bir *ay* tanımlayan aşağıdaki modeli ( [dijital TWINS tanım dili (dtdl)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)olarak yazılmış) göz önünde bulundurun:

:::code language="json" source="~/digital-twins-docs-samples/models/Moon.json":::

`object result = await client.GetDigitalTwinAsync("my-moon");`Bir *ay* türü ikizi çağırma sonucu şöyle görünebilir:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

Dijital ikizi tanımlı özellikleri, Digital ikizi üzerinde en üst düzey özellikler olarak döndürülür. DTDL tanımının parçası olmayan meta veriler veya sistem bilgileri bir `$` ön ek ile döndürülür. Meta veri özellikleri aşağıdaki değerleri içerir:
* `$dtId`: Bu Azure Digital TWINS örneğindeki dijital ikizi KIMLIĞI
* `$etag`: Web sunucusu tarafından atanan standart bir HTTP alanı. Bu, ikizi her güncelleştirildiği sırada yeni bir değere güncelleştirilir ve bu, önceki bir denetim sonrasında ikizi verisinin sunucuda güncelleştirilip güncelleştirilmediğini belirlemekte yararlı olabilir. `If-Match`Yalnızca varlığın ETag 'i belirtilen ETag ile eşleşiyorsa, yalnızca tamamlanmış olan güncelleştirmeler ve silme işlemleri gerçekleştirmek için kullanabilirsiniz. Bu işlemler hakkında daha fazla bilgi için bkz. [Digitaltwins Update](/rest/api/digital-twins/dataplane/twins/digitaltwins_update) ve [Digitaltwins Delete](/rest/api/digital-twins/dataplane/twins/digitaltwins_delete)belgeleri.
* `$metadata`: Aşağıdakiler de dahil olmak üzere diğer özellikler kümesi:
  - Dijital ikizi modelinin DTMı 'ı.
  - Her yazılabilir özellik için eşitleme durumu. Bu, hizmetin ve cihazın ayrılan durumlar (örneğin, bir cihaz çevrimdışı olduğunda) olduğu durumlarda, cihazlar için en yararlı seçenektir. Şu anda bu özellik yalnızca IoT Hub bağlı fiziksel cihazlara uygulanır. Meta veriler bölümündeki verilerle, bir özelliğin tam durumunun yanı sıra son değiştirilme zaman damgalarını anlamak mümkündür. Eşitleme durumu hakkında daha fazla bilgi için bkz. cihaz durumunu eşitlemeye yönelik [bu IoT Hub öğreticisi](../iot-hub/tutorial-device-twins.md) .
  - IoT Hub veya Azure dijital TWINS gibi hizmete özgü meta veriler. 

`BasicDigitalTwin` [*Nasıl yapılır: Azure dijital TWINS API 'Leri ve SDK 'larını kullanma*](how-to-use-apis-sdks.md)gibi serileştirme yardımcı sınıfları hakkında daha fazla bilgi edinebilirsiniz.

## <a name="view-all-digital-twins"></a>Tüm dijital TWINS 'i görüntüle

Örneğinizdeki dijital TWINS 'in tamamını görüntülemek için bir [sorgu](how-to-query-graph.md)kullanın. Sorgu [API 'leri](/rest/api/digital-twins/dataplane/query) veya [CLI komutlarıyla](how-to-use-cli.md)bir sorgu çalıştırabilirsiniz.

Örnekteki tüm dijital TWINS listesini döndürecek temel sorgunun gövdesi aşağıda verilmiştir:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="update-a-digital-twin"></a>Dijital ikizi güncelleştirme

Dijital bir ikizi özelliklerini güncelleştirmek için, değiştirmek istediğiniz bilgileri [JSON yama](http://jsonpatch.com/) biçiminde yazarsınız. Bu şekilde, birden çok özelliği aynı anda değiştirebilirsiniz. Ardından JSON Patch belgesini bir `UpdateDigitalTwin()` yönteme geçirirsiniz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="UpdateTwinCall":::

Bir yama çağrısı, tek bir ikizi üzerinde dilediğiniz kadar özelliği güncelleştirebilir (hatta bunların hepsi de). Birden çok TWINS genelinde özellikleri güncelleştirmeniz gerekiyorsa, her bir ikizi için ayrı bir güncelleştirme çağrısının olması gerekir.

> [!TIP]
> Bir ikizi oluşturduktan veya güncelleştirdikten sonra değişiklikler [sorgularda](how-to-query-graph.md)yansıtılmadan önce 10 saniyeye kadar gecikme olabilir. `GetDigitalTwin`API ( [Bu makalede daha önce](#get-data-for-a-digital-twin)açıklanan) Bu gecikmeyle karşılaşmaz, bu nedenle anlık bir yanıt gerekirse yeni güncellenen TWINS 'nizi görmek IÇIN sorgulamak yerine API çağrısını kullanın. 

JSON yama kodu örneği aşağıda verilmiştir. Bu belge, uygulandığı dijital ikizi 'in *kütle* ve *Radius* özellik değerlerini değiştirir.

:::code language="json" source="~/digital-twins-docs-samples/models/patch.json":::

Azure .NET SDK 'nın [Jsonpatchdocument](/dotnet/api/azure.jsonpatchdocument)kullanarak düzeltme ekleri oluşturabilirsiniz. Aşağıda bir örnek verilmiştir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

### <a name="update-properties-in-digital-twin-components"></a>Dijital ikizi bileşenlerinde güncelleştirme özellikleri

Bir modelin bileşen içerebileceğini ve diğer modellerden sonra yapılmasına izin vermesini geri çekin. 

Bir Digital ikizi bileşenlerindeki özellikler için düzeltme eki uygulamak üzere, JSON yaması içinde yol söz dizimini kullanabilirsiniz:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component.json":::

### <a name="update-a-digital-twins-model"></a>Digital ikizi 'in modelini güncelleştirme

`UpdateDigitalTwin()`İşlevi, dijital bir ikizi farklı bir modele geçirmek için de kullanılabilir. 

Örneğin, Digital ikizi 'ın meta veri alanının yerini alan aşağıdaki JSON Patch belgesini göz önünde bulundurun `$model` :

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-1.json":::

Bu işlem yalnızca düzeltme eki tarafından değiştirilen dijital ikizi yeni modelle uyumlu olduğunda başarılı olur. 

Aşağıdaki örneği inceleyin:
1. *Foo_old* modeliyle dijital bir ikizi düşünün. *foo_old* gerekli bir özellik *kütle* tanımlar.
2. Yeni model *foo_new* bir özellik kütle tanımlar ve yeni bir gerekli özellik *sıcaklığını* ekler.
3. Düzeltme ekiyle sonra, dijital ikizi hem bir kütle hem de sıcaklık özelliği olmalıdır. 

Bu durumun düzeltme ekinin hem model hem de ikizi 'ın sıcaklık özelliğini güncelleştirmesi gerekir, örneğin:

:::code language="json" source="~/digital-twins-docs-samples/models/patch-model-2.json":::

### <a name="handle-conflicting-update-calls"></a>Çakışan güncelleştirme çağrılarını işle

Azure dijital TWINS, tüm gelen isteklerin diğer bir tarihten sonra işlenmesini sağlar. Bu, birden çok işlev aynı anda bir ikizi üzerinde aynı özelliği güncelleştirmeye çalışırsa bile, çakışmayı işlemek için açık kilitleme kodu yazmanıza **gerek yoktur** .

Bu davranış, ikizi esasına göre yapılır. 

Örnek olarak, bu üç çağrının aynı anda ulaştığını gösteren bir senaryo düşünün: 
*   *Twin1* üzerinde yazma özelliği
*   *Twin1* üzerinde B özelliği yazma
*   *Twin2* üzerinde yazma özelliği

*Twin1* değiştiren iki çağrı diğerinden sonra yürütülür ve değişiklik iletileri her değişiklik için oluşturulur. *Twin2* değiştirme çağrısı, bir çakışma olmadan eşzamanlı olarak yürütülemeyebilir.

## <a name="delete-a-digital-twin"></a>Dijital ikizi silme

Yöntemi kullanarak ikizlerini 'yi silebilirsiniz `DeleteDigitalTwin()` . Ancak, daha fazla ilişki olmadığında yalnızca bir ikizi silebilirsiniz. Bu nedenle, önce ikizi 'in gelen ve giden ilişkilerini silin.

Aşağıda, TWINS ve bunların ilişkilerini silmenin kodu örneği verilmiştir. `DeleteDigitalTwin`SDK çağrısı, daha geniş örnek bağlamda nerede olduğunu açıklığa kavuşturacak şekilde vurgulanır.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="DeleteTwin" highlight="7":::

### <a name="delete-all-digital-twins"></a>Tüm dijital TWINS 'i Sil

Tüm TWINS sürümlerini aynı anda silme hakkında bir örnek için öğreticide kullanılan örnek uygulamayı indirin [*: örnek bir istemci uygulamasıyla ilgili temel bilgileri araştırma*](tutorial-command-line-app.md). *Commandloop. cs* dosyası bunu bir `CommandDeleteAllTwins()` işlevde yapar.

## <a name="runnable-digital-twin-code-sample"></a>Runbir Digital ikizi Code örneği

Bir ikizi oluşturmak, ayrıntılarını güncelleştirmek ve ikizi silmek için aşağıdaki çalıştırılabilir kod örneğini kullanabilirsiniz. 

### <a name="set-up-the-runnable-sample"></a>Çalıştırılabilir örneği ayarlama

Kod parçacığı öğreticiden model tanımındaki [Room.js](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) kullanır [*: örnek bir Istemci uygulamasıyla Azure dijital TWINS 'i araştırma*](tutorial-command-line-app.md). Bu bağlantıyı doğrudan dosyaya gitmek veya tam uçtan uca [örnek projenin bir](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)parçası olarak indirmek için kullanabilirsiniz.

Örneği çalıştırmadan önce aşağıdakileri yapın:
1. Model dosyasını indirin, projenize yerleştirin ve `<path-to>` aşağıdaki koddaki yer tutucuyu değiştirip, programınıza onu nerede bulacağınızı söyleyin.
2. Yer tutucusunu `<your-instance-hostname>` Azure dijital TWINS örneğinizin ana bilgisayar adı ile değiştirin.
3. Projenize Azure dijital TWINS ile çalışmak için gerekli olacak iki bağımlılık ekleyin. Birincisi [.net Için Azure Digital TWINS SDK 'sının](/dotnet/api/overview/azure/digitaltwins/client)paketidir, Ikincisi ise Azure 'da kimlik doğrulamaya yardımcı olacak araçlar sağlar.

      ```cmd/sh
      dotnet add package Azure.DigitalTwins.Core
      dotnet add package Azure.Identity
      ```

Ayrıca, örneği doğrudan çalıştırmak istiyorsanız yerel kimlik bilgilerini de ayarlamanız gerekir. Sonraki bölümde bu adım gösterilmektedir.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Örneği çalıştırma

Yukarıdaki adımları tamamladıktan sonra, aşağıdaki örnek kodu doğrudan çalıştırabilirsiniz.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs":::

Yukarıdaki programın konsol çıktısı aşağıda verilmiştir: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="İkizi oluşturulduğunu, güncelleştirildiğini ve silindiğini gösteren konsol çıkışı" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="next-steps"></a>Sonraki adımlar

Bkz. dijital TWINS 'niz arasında ilişkiler oluşturma ve yönetme:
* [*Nasıl yapılır: ikizi grafiğini ilişkilerle yönetme*](how-to-manage-graph.md)