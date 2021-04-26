---
title: İlişkileri olan ikiz grafiğini yönetme
titleSuffix: Azure Digital Twins
description: Bkz. bir dijital TWINS grafiğini ilişkiler ile bağlayarak yönetme.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: fde473453aa79e0078765df394acdeb54b3c7fe9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102433327"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>İlişkileri kullanarak dijital TWINS grafiğini yönetme

Azure dijital TWINS 'in kalbi, tüm ortamınızı temsil eden [ikizi grafiktir](concepts-twins-graph.md) . İkizi Graph, **ilişkiler** aracılığıyla bağlı tek tek dijital TWINS 'ten oluşur. 

Çalışan bir [Azure dijital TWINS örneğinden](how-to-set-up-instance-portal.md) ve istemci uygulamanızda [kimlik doğrulama](how-to-authenticate-client.md) kodu ayarladıktan sonra, Azure dijital TWINS örneğindeki dijital TWINS ve bunların ilişkilerini oluşturmak, değiştirmek ve silmek Için [**digitaltwins API 'lerini**](/rest/api/digital-twins/dataplane/twins) kullanabilirsiniz. [.Net (C#) SDK 'sını](/dotnet/api/overview/azure/digitaltwins/client)veya [Azure DIGITAL TWINS CLI](how-to-use-cli.md)'yi de kullanabilirsiniz.

Bu makale, ilişkilerin ve grafiğin tamamını yönetmeye odaklanır. tek tek dijital TWINS ile çalışmak için bkz. [*nasıl yapılır: dijital TWINS 'ı yönetme*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-graph"></a>Grafiği yönetme yolları

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

Ayrıca, Azure Digital TWINS Explorer örneğini kullanarak grafiğinizde değişiklikler yapabilirsiniz. Bu, TWINS ve grafınızı görselleştirmenize ve arka planda SDK 'nın kullanılmasını sağlar. Sonraki bölümde bu örnek ayrıntılı olarak açıklanmaktadır.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>İlişki oluşturma

İlişkiler, ikizi grafiğinin temelini oluşturan farklı dijital TWINS 'in birbirlerine nasıl bağlandığını tanımlar.

İlişkiler, çağrı kullanılarak oluşturulur `CreateOrReplaceRelationshipAsync()` . 

Bir ilişki oluşturmak için şunu belirtmeniz gerekir:
* Kaynak ikizi KIMLIĞI ( `srcId` Aşağıdaki kod örneğinde): ilişkinin kaynaklandığı IKIZI kimliği.
* Target ikizi ID ( `targetId` Aşağıdaki kod örneğinde): ilişkinin ulaştığı IKIZI kimliği.
* Bir ilişki adı ( `relName` Aşağıdaki kod örneğinde): bunun gibi bir genel tür ilişkisi _vardır_.
* İlişki KIMLIĞI ( `relId` Aşağıdaki kod örneğinde): Bu ilişkinin özel adı, _Relationship1_ gibi bir şeydir.

İlişki KIMLIĞI, belirtilen kaynak ikizi içinde benzersiz olmalıdır. Genel olarak benzersiz olması gerekmez.
Örneğin, ikizi *foo* için, her bir özel ilişki kimliği benzersiz olmalıdır. Ancak, başka bir ikizi *çubuğu* , bir *foo* ilişkisinin aynı kimliğiyle eşleşen bir giden ilişkiye sahip olabilir.

Aşağıdaki kod örneğinde, Azure dijital TWINS Örneğinizde nasıl ilişki oluşturacağınız gösterilmektedir. Daha büyük bir program bağlamında görünebilen özel bir yöntemin içinde SDK çağrısını (vurgulanmış) kullanır.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="CreateRelationshipMethod" highlight="13":::

Bu özel işlev artık şöyle bir ilişki _içeren_ bir ilişki oluşturmak için çağrılabilir: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseCreateRelationship":::

Birden çok ilişki oluşturmak isterseniz, farklı ilişki türlerini bağımsız değişkene geçirerek aynı yönteme yapılan çağrıları tekrarlayabilirsiniz. 

Yardımcı sınıfı hakkında daha fazla bilgi için `BasicRelationship` bkz. [*nasıl yapılır: Azure dijital TWINS API 'Leri ve SDK 'larını kullanma*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>TWINS arasında birden çok ilişki oluşturma

İlişkiler aşağıdakilerden biri olarak sınıflandırılabilir: 

* Giden ilişkiler: Bu ikizi 'e ait olan Ilişkiler, diğer TWINS 'e bağlamak için dışarıya işaret eden bir Ilişki. `GetRelationshipsAsync()`Yöntemi, bir ikizi giden ilişkisini almak için kullanılır.
* Gelen ilişkiler: bir "gelen" bağlantısı oluşturmak için bu ikizi 'e işaret eden diğer TWINS 'e ait Ilişkiler. `GetIncomingRelationshipsAsync()`Yöntemi, bir ikizi gelen ilişkilerini almak için kullanılır.

İki TWINS arasında sahip olduğunuz ilişki sayısında herhangi bir kısıtlama yoktur; TWINS arasında istediğiniz sayıda ilişkiye sahip olabilirsiniz. 

Bu, aynı anda iki farklı ilişki türünü tek seferde ifade ettiğiniz anlamına gelir. Örneğin, *Ikizi A* , *depolanan* bir ilişki ve *ikizi B* ile *üretilmiş* ilişkiye sahip olabilir.

İsterseniz aynı türde ilişki türünün aynı ilişki türünde birden fazla örneğini de oluşturabilirsiniz. Bu örnekte, ilişkilerin farklı ilişki kimliklerine sahip olduğu sürece *Ikizi A* , *ikizi B* ile iki farklı *saklı* ilişkiye sahip olabilir.

## <a name="list-relationships"></a>Liste ilişkileri

Grafikteki belirli bir ikizi **giden** ilişkiler listesine erişmek için `GetRelationships()` yöntemini şöyle kullanabilirsiniz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

Bu, `Azure.Pageable<T>` `Azure.AsyncPageable<T>` çağrının zaman uyumlu veya zaman uyumsuz sürümünü kullanıp kullanmayacağınızı bağlı olarak bir veya döndürür.

İlişkilerin bir listesini alan bir örnek aşağıda verilmiştir. Daha büyük bir program bağlamında görünebilen özel bir yöntemin içinde SDK çağrısını (vurgulanmış) kullanır.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod" highlight="8":::

Artık aşağıdaki gibi, TWINS 'nin giden ilişkilerini görmek için bu özel yöntemi çağırabilirsiniz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindOutgoingRelationships":::

Alınan ilişkileri kullanarak, grafiğinizde diğer TWINS 'ye gidebilirsiniz. Bunu yapmak için, `target` döndürülen ilişkiden alanı okuyun ve bir sonraki çağrınızdan kimlik olarak kullanın `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Dijital ikizi gelen ilişkilerini bulma

Azure dijital TWINS 'in Ayrıca belirli bir ikizi **gelen** tüm ilişkileri bulması için bir API 'si vardır. Bu, genellikle ters gezinmede veya bir ikizi silinirken yararlıdır.

>[!NOTE]
> `IncomingRelationship` çağrılar, ilişkinin tam gövdesini döndürmez. Sınıfı hakkında daha fazla bilgi için `IncomingRelationship` [başvuru belgelerine](/dotnet/api/azure.digitaltwins.core.incomingrelationship)bakın.

Önceki bölümde yer alan kod örneği, bir ikizi giden ilişkilerin bulunmasına odaklanılmıştır. Aşağıdaki örnek benzer şekilde yapılandırılmıştır, ancak bunun yerine ikizi *gelen* ilişkileri bulur. Bu örnek ayrıca, daha büyük bir program bağlamında görünebilen özel bir yöntemin içinde SDK çağrısını (vurgulanmış) kullanır.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindIncomingRelationshipsMethod" highlight="8":::

Şu şekilde, TWINS 'in gelen ilişkilerini şu şekilde görmek için bu özel yöntemi çağırabilirsiniz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFindIncomingRelationships":::

### <a name="list-all-twin-properties-and-relationships"></a>Tüm ikizi özelliklerini ve ilişkilerini listeleyin

Bir ikizi giden ve gelen ilişkilerini listelemek için yukarıdaki yöntemleri kullanarak, ikizi özellikleri ve ilişkilerinin her iki türü de dahil olmak üzere tam ikizi bilgilerini yazdıran bir yöntem oluşturabilirsiniz. Bu amaçla Yukarıdaki özel yöntemlerin nasıl birleştirileceğini gösteren örnek bir özel yöntem aşağıda verilmiştir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FetchAndPrintMethod":::

Artık bu özel işlevi şu şekilde çağırabilirsiniz: 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseFetchAndPrint":::

## <a name="update-relationships"></a>İlişkileri Güncelleştir

İlişkiler yöntemi kullanılarak güncelleştirilir `UpdateRelationship` . 

>[!NOTE]
>Bu yöntem bir ilişkinin **özelliklerini** güncelleştirmek içindir. İlişkinin kaynak ikizi veya hedef ikizi ' i değiştirmeniz gerekiyorsa, [ilişkiyi silmeniz](#delete-relationships) ve yeni TWINS kullanarak [bir tane oluşturmanız](#create-relationships) gerekir.

İstemci çağrısı için gerekli parametreler, kaynak ikizi (ilişkinin kaynaklandığı ikizi), güncelleştirilecek ilişkinin KIMLIĞI ve özellikleri ve güncelleştirmek istediğiniz yeni değerleri içeren bir [JSON yama](http://jsonpatch.com/) belgesi kimliğidir.

Bu yöntemin nasıl kullanılacağını gösteren örnek kod aşağıda verilmiştir. Bu örnek, daha büyük bir program bağlamında görünebilen özel bir yöntemin içinde SDK çağrısını (vurgulanmış) kullanır.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UpdateRelationshipMethod" highlight="6":::

Bu özel yönteme yapılan çağrıya bir örnek, bir JSON yama belgesini bir özelliği güncelleştirmek için bilgilerle geçirerek bir örnektir.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseUpdateRelationship":::

## <a name="delete-relationships"></a>İlişkileri Sil

İlk parametre kaynak ikizi belirtir (ilişkinin kaynaklandığı ikizi). Diğer parametre ilişki KIMLIĞIDIR. İlişki kimlikleri yalnızca bir ikizi kapsamı içinde benzersiz olduğundan, hem ikizi ID hem de ilişki KIMLIĞI gereklidir.

Bu yöntemin nasıl kullanılacağını gösteren örnek kod aşağıda verilmiştir. Bu örnek, daha büyük bir program bağlamında görünebilen özel bir yöntemin içinde SDK çağrısını (vurgulanmış) kullanır.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="DeleteRelationshipMethod" highlight="5":::

Artık şöyle bir ilişki silmek için bu özel yöntemi çağırabilirsiniz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="UseDeleteRelationship":::

## <a name="runnable-twin-graph-sample"></a>Runikizi Graf örneği

Aşağıdaki yürütülebilir kod parçacığı, dijital TWINS ve ilişkilerden ikizi bir grafik oluşturmak için bu makaledeki ilişki işlemlerini kullanır.

### <a name="set-up-the-runnable-sample"></a>Çalıştırılabilir örneği ayarlama

Kod parçacığı öğreticiden model tanımlarında [*Room.js*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) ve [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) kullanır [*: örnek bir Istemci uygulamasıyla Azure dijital TWINS 'i araştırma*](tutorial-command-line-app.md). Bu bağlantıları doğrudan dosyalara gitmek veya tam uçtan uca [örnek projenin bir](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)parçası olarak indirmek için kullanabilirsiniz. 

Örneği çalıştırmadan önce aşağıdakileri yapın:
1. Model dosyalarını indirin, projenize yerleştirin ve `<path-to>` aşağıdaki koddaki yer tutucuları değiştirip programınıza onları nerede bulacağınızı söyleyin.
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

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs":::

Yukarıdaki programın konsol çıktısı aşağıda verilmiştir: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="İkizi ayrıntılarını, gelen ve giden TWINS ilişkilerini gösteren konsol çıkışı." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> İkizi Graph, TWINS arasında ilişki oluşturma kavramıdır. İkizi grafiğinin görsel temsilini görüntülemek istiyorsanız, bu makalenin [*görselleştirme*](how-to-manage-graph.md#visualization) bölümüne bakın. 

## <a name="create-graph-from-a-csv-file"></a>CSV dosyasından grafik oluşturma

Pratik kullanım durumlarında, ikizi hiyerarşileri genellikle farklı bir veritabanında depolanan verilerden veya belki de bir elektronik tabloda veya CSV dosyasında oluşturulur. Bu bölümde, bir CSV dosyasından verileri okuma ve bunun dışında bir ikizi Graf oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

Bir dizi dijital TWINS ve ilişki tanımlayan aşağıdaki veri tablosunu göz önünde bulundurun.

|  Model Kimliği    | İkizi KIMLIĞI (benzersiz olmalıdır) | İlişki adı  | Hedef ikizi KIMLIĞI  | İkizi init verileri |
| --- | --- | --- | --- | --- |
| dtmı: örnek: kat; 1    | Floor1 | contains | Room1 | |
| dtmı: örnek: kat; 1    | Floor0 | contains | Room0 | |
| dtmı: örnek: Oda; 1    | Room1 | | | {"Sıcaklık": 80} |
| dtmı: örnek: Oda; 1    | Room0 | | | {"Sıcaklık": 70} |

Bu verileri Azure dijital TWINS 'e getirmenin bir yolu, tabloyu bir CSV dosyasına dönüştürmenin yanı sıra, TWINS ve ilişkiler oluşturmak üzere dosyayı komutlara yorumlamak için kod yazmaktır. Aşağıdaki kod örneği, CSV dosyasındaki verilerin okunmasını ve Azure dijital TWINS 'de bir ikizi grafiği oluşturmayı gösterir.

Aşağıdaki kodda, CSV dosyası *data.csv* olarak adlandırılır ve Azure Digital TWINS örneğinizin **ana bilgisayar adını** temsil eden bir yer tutucu vardır. Örnek ayrıca, bu işleme yardımcı olmak üzere projenize ekleyebileceğiniz çeşitli paketleri de kullanır.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graphFromCSV.cs":::

## <a name="next-steps"></a>Sonraki adımlar

Azure dijital TWINS ikizi grafiğini sorgulama hakkında bilgi edinin:
* [*Kavramlar: sorgu dili*](concepts-query-language.md)
* [*Nasıl yapılır: ikizi grafiğini sorgulama*](how-to-query-graph.md)