---
title: Azure Cosmos DB içindeki saklı yordamları, Tetikleyicileri ve UDF 'Leri yazma
description: Saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri Azure Cosmos DB nasıl tanımlayacağınızı öğrenin
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 7600d8aa2f78e06ea4046273635fdbba18042010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028871"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri Azure Cosmos DB yazma
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB, **saklı yordamlar**, **Tetikleyiciler** ve **Kullanıcı tanımlı işlevler (UDF 'Ler)** yazmanıza olanak tanıyan JavaScript 'in dil ile tümleşik, işlemsel yürütmesini sağlar. Azure Cosmos DB içinde SQL API 'sini kullanırken, JavaScript dilinde saklı yordamları, Tetikleyicileri ve UDF 'Leri tanımlayabilirsiniz. Mantığınızı JavaScript 'te yazabilir ve veritabanı altyapısının içinde yürütebilirsiniz. Azure Cosmos DB ve [Cosmos db SQL API Istemci SDK](sql-api-dotnet-samples.md)'larının [Azure Portal](https://portal.azure.com/), [JavaScript Dil tümleşik sorgu API 'sini](javascript-query-api.md) kullanarak Tetikleyiciler, saklı yordamlar ve UDF 'ler oluşturabilir ve bunları çalıştırabilirsiniz. 

Saklı yordam, tetikleyici ve Kullanıcı tanımlı işlevi çağırmak için kaydetmeniz gerekir. Daha fazla bilgi için bkz. [Azure Cosmos DB saklı yordamlar, Tetikleyiciler, Kullanıcı tanımlı işlevlerle çalışma](how-to-use-stored-procedures-triggers-udfs.md).

> [!NOTE]
> Bölümlenmiş kapsayıcılar için, saklı bir yordam yürütürken, istek seçeneklerinde bir bölüm anahtarı değeri belirtilmelidir. Saklı yordamlar her zaman bir bölüm anahtarına göre kapsamlandırılır. Farklı bir bölüm anahtarı değerine sahip öğeler, saklı yordama görünür olmayacaktır. Bu ayrıca tetikleyicilere de uygulanır.
> [!Tip]
> Cosmos, saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevlerle kapsayıcıları dağıtmaya destekler. Daha fazla bilgi için bkz [. sunucu tarafı işlevleriyle Azure Cosmos DB kapsayıcısı oluşturma.](./manage-with-templates.md#create-sproc)

## <a name="how-to-write-stored-procedures"></a><a id="stored-procedures"></a>Saklı yordamları yazma

Saklı yordamlar JavaScript kullanılarak yazılır, Azure Cosmos kapsayıcısı içinde öğeleri oluşturabilir, güncelleştirebilir, okuyabilir, sorgulayabilir ve silebilir. Saklı yordamlar her koleksiyon için kaydedilir ve bu koleksiyonda bulunan herhangi bir belge veya ek üzerinde çalışabilir.

Bir "Merhaba Dünya" yanıtı döndüren basit bir saklı yordam aşağıda verilmiştir.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

Bağlam nesnesi Azure Cosmos DB ' de gerçekleştirilebilecek tüm işlemlere erişim sağlar ve istek ve yanıt nesnelerine erişim sağlar. Bu durumda, istemciye geri gönderilecek yanıtın gövdesini ayarlamak için Response nesnesini kullanırsınız.

Yazıldıktan sonra, saklı yordamın bir koleksiyonla kayıtlı olması gerekir. Daha fazla bilgi için bkz. [Azure Cosmos DB 'da saklı yordamları kullanma](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) .

### <a name="create-an-item-using-stored-procedure"></a><a id="create-an-item"></a>Saklı yordam kullanarak bir öğe oluşturma

Saklı yordam kullanarak bir öğe oluşturduğunuzda, öğe Azure Cosmos kapsayıcısına eklenir ve yeni oluşturulan öğe için bir KIMLIK döndürülür. Öğe oluşturma zaman uyumsuz bir işlemdir ve JavaScript geri çağırma işlevlerine bağımlıdır. Geri çağırma işlevinin iki parametresi vardır-bir işlem başarısız olması ve bir dönüş değeri için başka bir hata olması durumunda hata nesnesi için bir tane. Bu durumda, oluşturulan nesne. Geri çağırma içinde, özel durumu işleyebilir veya bir hata oluşturabilirsiniz. Bir geri çağırma sağlanmamışsa ve bir hata varsa Azure Cosmos DB çalışma zamanı bir hata oluşturur.

Saklı yordam ayrıca açıklamayı ayarlamak için bir parametre içerir, bu da bir Boole değeridir. Parametresi true olarak ayarlandığında ve açıklama eksik olduğunda, saklı yordam bir özel durum oluşturur. Aksi takdirde, saklı yordamın geri kalanı çalışmaya devam eder.

Aşağıdaki örnek saklı yordam, giriş olarak yeni Azure Cosmos öğelerinin bir dizisini alır, bunu Azure Cosmos kapsayıcısına ekler ve ekli öğelerin sayısını döndürür. Bu örnekte, [hızlı başlangıç .NET SQL API](create-sql-api-dotnet.md) 'sindeki ToDoList örneğini geliştirdik.

```javascript
function createToDoItems(items) {
    var collection = getContext().getCollection();
    var collectionLink = collection.getSelfLink();
    var count = 0;

    if (!items) throw new Error("The array is undefined or null.");

    var numItems = items.length;

    if (numItems == 0) {
        getContext().getResponse().setBody(0);
        return;
    }

    tryCreate(items[count], callback);

    function tryCreate(item, callback) {
        var options = { disableAutomaticIdGeneration: false };

        var isAccepted = collection.createDocument(collectionLink, item, options, callback);

        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    function callback(err, item, options) {
        if (err) throw err;
        count++;
        if (count >= numItems) {
            getContext().getResponse().setBody(count);
        } else {
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Saklı yordamlar için giriş parametreleri olarak diziler

Azure portal ' de bir saklı yordam tanımlarken, giriş parametreleri her zaman saklı yordama bir dize olarak gönderilir. Bir dizi dizeyi girdi olarak iletseniz bile, dizi dizeye dönüştürülür ve saklı yordama gönderilir. Bu sorunu geçici olarak çözmek için, saklı yordamınız içinde dizeyi bir dizi olarak ayrıştırarak bir işlev tanımlayabilirsiniz. Aşağıdaki kod, bir dize giriş parametresinin dizi olarak nasıl ayrıştıralınacağını gösterir:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a name="transactions-within-stored-procedures"></a><a id="transactions"></a>Saklı yordamlar içindeki işlemler

Saklı yordam kullanarak bir kapsayıcı içindeki öğelere işlem uygulayabilirsiniz. Aşağıdaki örnek, tek bir işlemde iki ekip arasında oyuncu ticareti için bir fantetin oyun uygulaması içindeki işlemleri kullanır. Saklı yordam, her biri bağımsız değişken olarak geçirilen Player kimliklerine karşılık gelen iki Azure Cosmos öğesini okumaya çalışır. Her iki oyuncu de bulunursa, saklı yordam, takımlarını değiştirerek öğeleri günceller. Bu şekilde herhangi bir hatayla karşılaşılırsa, saklı yordam, işlemi örtük olarak iptal eden bir JavaScript özel durumu oluşturur.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery =
    {
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };

    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 =
            {
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}]
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a name="bounded-execution-within-stored-procedures"></a><a id="bounded-execution"></a>Saklı yordamlar içinde sınırlı yürütme

Aşağıda, öğeleri bir Azure Cosmos kapsayıcısına toplu olarak içeri aktaran saklı yordamın bir örneği verilmiştir. Saklı yordam, ' den Boolean dönüş değerini denetleyerek sınırlı yürütmeyi işler `createDocument` ve sonra toplu işlerin ilerlemesini izlemek ve devam ettirmek için saklı yordamın her bir çağrısında yerleştirilen öğe sayısını kullanır.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

### <a name="async-await-with-stored-procedures"></a><a id="async-promises"></a>Saklı yordamlarla zaman uyumsuz await

Aşağıda, bir yardımcı işlevi kullanan, zaman uyumsuz-await kullanan bir saklı yordamın örneği verilmiştir. Saklı yordam bir öğe için sorgular ve değiştirir.

```javascript
function async_sample() {
    const ERROR_CODE = {
        NotAccepted: 429
    };

    const asyncHelper = {
        queryDocuments(sqlQuery, options) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.queryDocuments(__.getSelfLink(), sqlQuery, options, (err, feed, options) => {
                    if (err) reject(err);
                    resolve({ feed, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        },

        replaceDocument(doc) {
            return new Promise((resolve, reject) => {
                const isAccepted = __.replaceDocument(doc._self, doc, (err, result, options) => {
                    if (err) reject(err);
                    resolve({ result, options });
                });
                if (!isAccepted) reject(new Error(ERROR_CODE.NotAccepted, "replaceDocument was not accepted."));
            });
        }
    };

    async function main() {
        let continuation;
        do {
            let { feed, options } = await asyncHelper.queryDocuments("SELECT * from c", { continuation });

            for (let doc of feed) {
                doc.newProp = 1;
                await asyncHelper.replaceDocument(doc);
            }

            continuation = options.continuation;
        } while (continuation);
    }

    main().catch(err => getContext().abort(err));
}
```

## <a name="how-to-write-triggers"></a><a id="triggers"></a>Tetikleyicileri yazma

Azure Cosmos DB ön Tetikleyicileri ve Tetikleyicileri destekler. Ön Tetikleyiciler, bir veritabanı öğesi değiştirilmeden önce yürütülür ve bir veritabanı öğesi değiştirildikten sonra Tetikleyiciler yürütülür. Tetikleyiciler otomatik olarak yürütülmez, bunların yürütülmesini istediğiniz her bir veritabanı işlemi için belirtilmesi gerekir. Bir tetikleyiciyi tanımladıktan sonra, Azure Cosmos DB SDK 'larını kullanarak [bir ön tetikleyiciyi kaydetmeniz ve çağırmanız](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) gerekir.

### <a name="pre-triggers"></a><a id="pre-triggers"></a>Ön Tetikleyiciler

Aşağıdaki örnek, oluşturulmakta olan bir Azure Cosmos öğesinin özelliklerini doğrulamak için bir ön tetikleyicisinin nasıl kullanıldığını gösterir. Bu örnekte, yeni eklenen bir öğeye bir zaman damgası özelliği eklemek için [hızlı başlangıç .NET SQL API](create-sql-api-dotnet.md)'sindeki ToDoList örneğini geliştirdik.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Ön tetikleyicilerin hiçbir giriş parametresi olamaz. Tetikleyicide istek nesnesi, işlemle ilişkili istek iletisini işlemek için kullanılır. Önceki örnekte, ön tetikleyici bir Azure Cosmos öğesi oluştururken çalıştırılır ve istek iletisi gövdesi JSON biçiminde oluşturulacak öğeyi içerir.

Tetikleyiciler kaydedildikten sonra, birlikte çalışacağı işlemleri belirtebilirsiniz. Bu tetikleyici, bir değeri ile oluşturulmalıdır `TriggerOperation` , bu da `TriggerOperation.Create` tetikleyiciyi aşağıdaki kodda gösterildiği gibi değiştirme işleminde kullanılması anlamına gelir.

Ön tetikleyiciyi kaydetme ve çağırma örnekleri için bkz. [Tetikleyiciler](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) ve [Tetikleyiciler sonrası](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) makaleler. 

### <a name="post-triggers"></a><a id="post-triggers"></a>Tetikleyiciler sonrası

Aşağıdaki örnek, bir tetikleme sonrası göstermektedir. Bu tetikleyici, meta veri öğesi için sorgular ve yeni oluşturulan öğe hakkındaki ayrıntılarla günceller.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

Dikkat edilmesi gereken tek şey, Azure Cosmos DB tetikleyicilerin işlem yürütmesinin önemli bir örneğidir. Son tetikleyici, temeldeki öğenin kendisi için aynı işlemin bir parçası olarak çalışır. Tetikleyici sonrası yürütme işlemi sırasında tüm işlem başarısız olur. Yürütülen her şey geri alınacaktır ve bir özel durum döndürülür.

Ön tetikleyiciyi kaydetme ve çağırma örnekleri için bkz. [Tetikleyiciler](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) ve [Tetikleyiciler sonrası](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) makaleler. 

## <a name="how-to-write-user-defined-functions"></a><a id="udfs"></a>Kullanıcı tanımlı işlevleri yazma

Aşağıdaki örnek, çeşitli gelir ayraçları için gelir vergisini hesaplamak üzere bir UDF oluşturur. Daha sonra bu kullanıcı tanımlı işlev bir sorgu içinde kullanılır. Bu örneğin amaçları doğrultusunda, özelliklerle birlikte "ıngelir" adlı bir kapsayıcı olduğunu varsayar:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Aşağıdakiler, çeşitli gelir ayraçları için gelir vergisini hesaplamak üzere bir işlev tanımıdır:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Kullanıcı tanımlı bir işlevi kaydetme ve kullanma örnekleri için, bkz. [Azure Cosmos DB makalesinde Kullanıcı tanımlı işlevleri kullanma](how-to-use-stored-procedures-triggers-udfs.md#udfs) .

## <a name="logging"></a>Günlüğe Kaydetme 

Saklı yordam, Tetikleyiciler veya Kullanıcı tanımlı işlevleri kullanırken, komutu kullanarak adımları günlüğe kaydedebilirsiniz `console.log()` . Bu komut, `EnableScriptLogging` Aşağıdaki örnekte gösterildiği gibi true olarak ayarlandığında, hata ayıklama için bir dize odaklanacaktır:

```javascript
var response = await client.ExecuteStoredProcedureAsync(
document.SelfLink,
new RequestOptions { EnableScriptLogging = true } );
Console.WriteLine(response.ScriptLog);
```

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos DB saklı yordamları, Tetikleyicileri ve Kullanıcı tanımlı işlevleri yazma veya kullanma hakkında daha fazla bilgi edinin:

* [Azure Cosmos DB'de saklı yordamları, tetikleyicileri ve kullanıcı tanımlı işlevleri kaydetme ve kullanma](how-to-use-stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB içinde JavaScript sorgu API 'sini kullanarak saklı yordamları ve Tetikleyicileri yazma](how-to-write-javascript-query-api.md)

* [Azure Cosmos DB içinde Azure Cosmos DB saklı yordamlar, Tetikleyiciler ve Kullanıcı tanımlı işlevlerle çalışma](stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB 'de JavaScript Dil tümleşik sorgu API 'SI ile çalışma](javascript-query-api.md)
