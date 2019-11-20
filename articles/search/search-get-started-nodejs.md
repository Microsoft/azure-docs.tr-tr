---
title: "Hızlı başlangıç: REST API 'Leri kullanarak Node. js içinde arama dizini oluşturma"
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama için Node. js örneği, nasıl oluşturma, veri yükleme ve JavaScript 'ten sorgu yapma.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.devlang: nodejs
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 8b7308b5289ba747dba90716b495ca1ba6afb209
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719996"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-nodejs-using-rest-apis"></a>Hızlı başlangıç: REST API 'Leri kullanarak Node. js ' de Azure Bilişsel Arama dizini oluşturma
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Bir Azure Bilişsel Arama dizini oluşturan, yükleyen ve sorgulayan bir Node. js uygulaması oluşturun. Bu makalede, uygulamanın adım adım nasıl oluşturulacağı gösterilmektedir. Alternatif olarak, [kaynak kodunu ve verileri indirebilir](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/) ve uygulamayı komut satırından çalıştırabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Aşağıdaki hizmetler, Araçlar ve veriler bu hızlı başlangıçta kullanılır.

+ [Node.js](https://nodejs.org).
+ [NPM](https://www.npmjs.com) , Node. js tarafından yüklenmelidir.
+ Bu makalede veya [deponun **hızlı başlangıç** dizininden](https://github.com/Azure-Samples/azure-search-javascript-samples/)örnek bir dizin yapısı ve eşleşen belgeler sağlanır.
+ Geçerli aboneliğinizde [bir Azure bilişsel arama hizmeti oluşturun](search-create-service-portal.md) veya [var olan bir hizmeti bulun](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) . Bu hızlı başlangıç için ücretsiz bir hizmet kullanabilirsiniz.

Önerilen:

* [Visual Studio Code](https://code.visualstudio.com).
* VSCode için [prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) ve [eslint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) uzantıları.

<a name="get-service-info"></a>
## <a name="get-keys-and-urls"></a>Anahtar ve URL 'Leri al

Hizmete yapılan çağrılar, her istekte bir URL uç noktası ve erişim anahtarı gerektirir. Her ikisiyle de bir arama hizmeti oluşturulur. bu nedenle, aboneliğinize Azure Bilişsel Arama eklediyseniz, gerekli bilgileri almak için aşağıdaki adımları izleyin:

1. [Azure Portal oturum açın](https://portal.azure.com/)ve arama hizmetinize **genel bakış** sayfasında, arama hizmetinizin adını alın. Uç nokta URL 'sini inceleyerek hizmet adınızı doğrulayabilirsiniz. Uç nokta URL 'niz `https://mydemo.search.windows.net`, hizmet adınız `mydemo`olur.

2. **Ayarlar** > **anahtarlar**' da, hizmette tam haklar için bir yönetici anahtarı alın. Üzerinde bir tane almanız gereken iş sürekliliği için iki adet değiştirilebilir yönetici anahtarı vardır. Nesneleri eklemek, değiştirmek ve silmek için isteklerde birincil veya ikincil anahtarı kullanabilirsiniz.

    Sorgu anahtarını da alın. Salt okuma erişimiyle sorgu istekleri vermek en iyi uygulamadır.

![Hizmet adı ve yönetici ve sorgu anahtarlarını alın](media/search-get-started-nodejs/service-name-and-keys.png)

Tüm istekler, hizmetinize gönderilen her isteğin üstbilgisinde bir API anahtarı gerektirir. Geçerli bir anahtar, istek başına, isteği gönderen uygulama ve onu işleyen hizmet arasında güven oluşturur.

## <a name="set-up-your-environment"></a>Ortamınızı ayarlama

Bir PowerShell konsolunu veya Node. js ' yi yüklediğiniz başka bir ortamı açarak başlayın.

1. `quickstart` adı vererek bir geliştirme dizini oluşturun:

    ```powershell
    mkdir quickstart
    cd quickstart
    ```

2. `npm init`çalıştırarak NPM ile boş bir proje başlatın. Lisans dışında, "MıT" olarak ayarlamanız gereken varsayılan değerleri kabul edin. 

1. Kod ve geliştirmeye yardımcı olacak paketler ekleyin:

    ```powershell
    npm install nconf node-fetch
    npm install --save-dev eslint eslint-config-prettier eslint-config-airbnb-base eslint-plugin-import prettier
    ```

4. **Package. JSON** dosyanızın şuna benzer göründüğünü denetleyerek projeleri ve bağımlılıklarını yapılandırdığınızdan emin olun:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Azure_Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "nconf": "^0.10.0",
        "node-fetch": "^2.6.0"
      },
      "devDependencies": {
        "eslint": "^6.1.0",
        "eslint-config-airbnb-base": "^13.2.0",
        "eslint-config-prettier": "^6.0.0",
        "eslint-plugin-import": "^2.18.2",
        "prettier": "^1.18.2"
      }
    }
    ```
Arama hizmeti verilerinizi tutmak için bir **azure_search_config. JSON** dosyası oluşturun:

```json
{
    "serviceName" : "[SERVICE_NAME]",
    "adminKey" : "[ADMIN_KEY]",
    "queryKey" : "[QUERY_KEY]",
    "indexName" : "hotels-quickstart"
}
```

`[SERVICE_NAME]` değerini, arama hizmetinizin adıyla değiştirin. `[ADMIN_KEY]` ve `[QUERY_KEY]` daha önce kaydettiğiniz anahtar değerleriyle değiştirin. 

## <a name="1---create-index"></a>1-Dizin oluşturma 

**Hotels_quickstart_index. JSON**dosyası oluşturun.  Bu dosya, Azure Bilişsel Arama 'nin bir sonraki adımda yüklediğiniz belgelerle nasıl çalıştığını tanımlar. Her alan bir `name` tanımlanır ve belirtilen bir `type`olur. Her alan Ayrıca, Azure Bilişsel Arama alan üzerinde arama, filtreleme, sıralama ve model kullanıp kullanamayacağını belirten bir dizi dizin özniteliklerine sahiptir. Alanların çoğu basit veri türleridir, ancak `AddressType` benzer şekilde, dizininizdeki zengin veri yapıları oluşturmanıza imkan tanıyan karmaşık türlerdir.  [Desteklenen veri türleri](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) ve [dizin öznitelikleri](https://docs.microsoft.com/azure/search/search-what-is-an-index#index-attributes)hakkında daha fazla bilgi edinebilirsiniz. 

Aşağıdakileri **hotels_quickstart_index. JSON** dosyasına ekleyin veya [dosyayı indirin](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```
    

Genel olarak geçerli olacak koddan belirli bir senaryonun özelliklerini ayırmak iyi bir uygulamadır. **AzureSearchClient. js** dosyasında tanımlanan `AzureSearchClient` sınıfı, Istek URL 'lerinin nasıl oluşturulduğunu, Fetch API kullanarak istek yapmayı ve yanıtın durum koduna tepki vermesini bilecektir.

**Düğüm getirme** paketini içeri aktarıp basit bir sınıf oluşturarak **AzureSearchClient. js** ' de çalışmaya başlayın. `AzureSearchClient` sınıfının değiştirilebilir bölümlerini, oluşturucusuna çeşitli yapılandırma değerlerini geçirerek yalıtın:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    constructor(searchServiceName, adminKey, queryKey, indexName) {
        this.searchServiceName = searchServiceName;
        this.adminKey = adminKey;
        // The query key is used for read-only requests and so can be distributed with less risk of abuse.
        this.queryKey = queryKey;
        this.indexName = indexName;
        this.apiVersion = '2019-05-06';
    }

    // All methods go inside class body here!
}

module.exports = AzureSearchClient;
```

Sınıfının ilk sorumluluğu, çeşitli isteklerin gönderileceği URL 'Leri oluşturmayı öğrenmektir. Sınıf oluşturucusuna geçirilen yapılandırma verilerini kullanan örnek yöntemleriyle bu URL 'Leri derleyin. Oluşturdukları URL 'nin bir API sürümüne özgü olduğunu ve bu sürümü belirten bir bağımsız değişken (Bu uygulamada, `2019-05-06`) olması gerektiğini unutmayın. 

Bu yöntemlerin ilki dizinin URL 'sini döndürür. Aşağıdaki yöntemi sınıf gövdesinin içine ekleyin:

```javascript
getIndexUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}?api-version=${this.apiVersion}`; }

```

`AzureSearchClient` sonraki sorumluluğu, Fetch API 'SI ile zaman uyumsuz bir istek yapıyor. Zaman uyumsuz statik yöntem `request` bir URL 'yi, HTTP yöntemini ("GET", "PUT", "POST", "DELETE"), istekte kullanılacak anahtarı ve isteğe bağlı bir JSON nesnesini belirten bir dize alır. `headers` değişkeni, "api-Key" HTTP istek üst bilgisi ile `queryKey` (yönetici anahtarı veya salt okuma sorgusu anahtarı) eşler. İstek seçenekleri her zaman kullanılacak `method` ve `headers`içerir. `bodyJson` `null`değilse, HTTP isteğinin gövdesi `bodyJson`dize gösterimine ayarlanır. `request` yöntemi, HTTP isteğini yürütmek için getirme API 'sinin taahhüdünü döndürür.

```javascript
static async request(url, method, apiKey, bodyJson = null) {
    // Uncomment the following for request details:
    /*
    console.log(`\n${method} ${url}`);
    console.log(`\nKey ${apiKey}`);
    if (bodyJson !== null) {
        console.log(`\ncontent: ${JSON.stringify(bodyJson, null, 4)}`);
    }
    */

    const headers = {
        'content-type' : 'application/json',
        'api-key' : apiKey
    };
    const init = bodyJson === null ?
        { 
            method, 
            headers
        }
        : 
        {
            method, 
            headers,
            body : JSON.stringify(bodyJson)
        };
    return fetch(url, init);
}
```

Tanıtım amacıyla, HTTP isteği başarılı değilse yalnızca bir özel durum oluşturur. Gerçek bir uygulamada büyük olasılıkla, arama hizmeti isteğinden `response` HTTP durum kodunu günlüğe kaydetme ve tanılama yapabilirsiniz. 
    
```javascript
static throwOnHttpError(response) {
    const statusCode = response.status;
    if (statusCode >= 300){
        console.log(`Request failed: ${JSON.stringify(response, null, 4)}`);
        throw new Error(`Failure in request. HTTP Status was ${statusCode}`);
    }
}
```

Son olarak, Azure Bilişsel Arama dizinini algılamaya, silmeye ve oluşturmaya yönelik yöntemleri ekleyin. Bu yöntemlerin hepsi aynı yapıya sahiptir:

* İsteğin oluşturulacağı uç noktayı alın.
* İsteği uygun uç nokta, HTTP fiili, API anahtarı ve uygunsa bir JSON gövdesi ile oluşturun. `indexExistsAsync()` ve `deleteIndexAsync()` bir JSON gövdesine sahip değildir, ancak `createIndexAsync(definition)`.
* isteğin yanıtını `await`.  
* Yanıtın durum kodu üzerinde işlem yapın.
* Uygun bir değer (Boolean, `this`veya sorgu sonuçları) için bir Promise döndürün. 

```javascript
async indexExistsAsync() { 
    console.log("\n Checking if index exists...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "GET", this.adminKey);
    // Success has a few likely status codes: 200 or 204 (No Content), but accept all in 200 range...
    const exists = response.status >= 200 && response.status < 300;
    return exists;
}

async deleteIndexAsync() {
    console.log("\n Deleting existing index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "DELETE", this.adminKey);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}

async createIndexAsync(definition) {
    console.log("\n Creating index...");
    const endpoint = this.getIndexUrl();
    const response = await AzureSearchClient.request(endpoint, "PUT", this.adminKey, definition);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

Yöntemlerinizin sınıfın içinde olduğunu ve sınıfı dışarı aktarıp çıkardığınızı doğrulayın. **AzureSearchClient. js** ' nin en dıştaki kapsamı:

```javascript
const fetch = require('node-fetch');

class AzureSearchClient {
    // ... code here ...
}

module.exports = AzureSearchClient;
```

Nesne odaklı bir sınıf, olası yeniden kullanılabilir **AzureSearchClient. js** modülü için iyi bir seçimdir, ancak **index. js**adlı bir dosyaya yerleştirmeniz gereken ana program için gerekli değildir. 

**İndex. js** oluşturun ve şunu yaparak başlayın:

* JSON, ortam değişkenleri veya komut satırı bağımsız değişkenleriyle yapılandırmayı belirtme esnekliği sağlayan **NConf** paketi.
* **Hotels_quickstart_index. JSON** dosyasındaki veriler.
* `AzureSearchClient` modülü.

```javascript
const nconf = require('nconf');

const indexDefinition = require('./hotels_quickstart_index.json');
const AzureSearchClient = require('./AzureSearchClient.js');
```

[ **NConf** paketi](https://github.com/indexzero/nconf) , yapılandırma verilerini ortam değişkenleri veya komut satırı gibi çeşitli biçimlerde belirtmenize olanak tanır. Bu örnek, **azure_search_config. JSON** dosyasını okumak ve dosyanın içeriğini sözlük olarak döndürmek için temel bir şekilde **NConf** kullanır. **NConf**'in `get(key)` işlevini kullanarak, yapılandırma bilgilerinin düzgün bir şekilde özelleştirildiğini hızlı bir şekilde denetleyebilirsiniz. Son olarak, işlev yapılandırmayı döndürür:

```javascript
function getAzureConfiguration() {
    const config = nconf.file({ file: 'azure_search_config.json' });
    if (config.get('serviceName') === '[SEARCH_SERVICE_NAME' ) {
        throw new Error("You have not set the values in your azure_search_config.json file. Change them to match your search service's values.");
    }
    return config;
}
```

`sleep` işlevi, belirli bir süre sonra çözümlenen bir `Promise` oluşturur. Bu işlevin kullanılması, zaman uyumsuz dizin işlemlerinin tamamlanmasını beklerken ve kullanılabilir hale geleken uygulamanın duraklatılmasını sağlar. Böyle bir gecikme eklemek, genellikle yalnızca tanıtımlar, testler ve örnek uygulamalar için gereklidir.

```javascript
function sleep(ms) {
    return(
        new Promise(function(resolve, reject) {
            setTimeout(function() { resolve(); }, ms);
        })
    );
}
```

Son olarak, ana zaman uyumsuz `run` işlevini belirtin ve çağırın. Bu işlev, `Promise`s 'nin çözümlenmesi için gereken şekilde bekleyen diğer işlevleri sırayla çağırır.

* Daha önce yazdığınız `getAzureConfiguration()` yapılandırmayı alın
* Yapılandırmanızda değerler geçirerek yeni bir `AzureSearchClient` örneği oluşturma
* Dizinin mevcut olup olmadığını denetleyin ve varsa silin
* **Hotels_quickstart_index. JSON** öğesinden yüklenen `indexDefinition` kullanarak dizin oluşturma

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get["serviceName"]);
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
    } catch (x) {
        console.log(x);
    }
}

run();
```

`run()`için son çağrıyı unutmayın! Bir sonraki adımda `node index.js` çalıştırdığınızda programınızın giriş noktasıdır.

`AzureSearchClient.indexExistsAsync()` ve `AzureSearchClient.deleteIndexAsync()` parametre almaz. Bu işlevler, `bodyJson` bağımsız değişken olmadan `AzureSearchClient.request()` çağırır. `AzureSearchClient.request()`içinde `bodyJson === null` `true`olduğu için `init` yapısı yalnızca HTTP fiili ("GET" ve `indexExistsAsync()` için "SIL") olarak ayarlanmıştır ve bu da istek anahtarını belirten üst bilgiler.`deleteIndexAsync()`  

Buna karşılık _`AzureSearchClient.createIndexAsync(indexDefinition)` yöntemi bir_ parametre alır. `index.js``run` işlevi, **hotels_quickstart_index. JSON** dosyasının içeriğini `AzureSearchClient.createIndexAsync(indexDefinition)` yöntemine geçirir. `createIndexAsync()` yöntemi bu tanımı `AzureSearchClient.request()`geçirir. `AzureSearchClient.request()`, `bodyJson === null` artık `false`olduğundan `init` yapısı yalnızca HTTP fiili ("PUT") ve üst bilgileri değil, `body` dizin tanım verilerine ayarlar.

### <a name="prepare-and-run-the-sample"></a>Örneği hazırlama ve çalıştırma

Aşağıdaki komutlar için bir Terminal penceresi kullanın.

1. **Package. JSON** dosyasını ve kodunuzun geri kalanını içeren klasöre gidin.
1. Örnek için paketleri `npm install`ile birlikte yükler.  Bu komut, kodun bağımlı olduğu paketleri indirir.
1. Programınızı `node index.js`çalıştırın.

Program tarafından gerçekleştirilen eylemleri açıklayan bir ileti serisi görmeniz gerekir. İsteklerle daha fazla ayrıntı görmek isterseniz, **AzureSearchClient. js**içindeki [`AzureSearchClient.request()`https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/AzureSearchClient.js#L21-L27) yönteminin başındaki satırların açıklamasını kaldırın. 

Azure portal arama hizmetinize **Genel Bakış ' ı** açın. **Dizinler** sekmesini seçin. Aşağıdakine benzer bir şey görmeniz gerekir:

![Azure portal, arama hizmetine genel bakış, dizinler sekmesinin ekran görüntüsü](media/search-get-started-nodejs/create-index-no-data.png)

Sonraki adımda, dizine veri ekleyeceksiniz. 

## <a name="2---load-documents"></a>2-belge yükleme 

Azure Bilişsel Arama 'de belgeler, sorguların dizin oluşturma ve çıkışlara yönelik giriş olan veri yapılarıdır. Bu tür verileri dizine NAKLETMENIZ gerekir. Bu, önceki adımda gerçekleştirilen işlemlerden farklı bir uç nokta kullanır. **AzureSearchClient. js** ' i açın ve `getIndexUrl()`sonra aşağıdaki yöntemi ekleyin:

```javascript
 getPostDataUrl() { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs/index?api-version=${this.apiVersion}`;  }
```

`AzureSearchClient.createIndexAsync(definition)`benzer şekilde, `AzureSearchClient.request()` çağıran ve otel verilerinde, gövdesi olarak geçen bir işleve ihtiyacınız vardır. **AzureSearchClient. js** ' de, `createIndexAsync(definition)`sonra `postDataAsync(hotelsData)` ekleyin:

```javascript
async postDataAsync(hotelsData) {
    console.log("\n Adding hotel data...");
    const endpoint = this.getPostDataUrl();
    const response = await AzureSearchClient.request(endpoint,"POST", this.adminKey, hotelsData);
    AzureSearchClient.throwOnHttpError(response);
    return this;
}
```

 Belge girişleri bir veritabanındaki satırlar, blob depolamada Bloblar veya bu örnekte olduğu gibi, diskteki JSON belgeleri olabilir. [Oteller. JSON](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/hotels.json) dosyasını indirebilir ya da aşağıdaki içerikle kendi **oteller. JSON** dosyanızı oluşturabilirsiniz:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}

```

Bu verileri programınıza yüklemek için, üst kısımdaki `hotelData` başvuruda bulunan satırı ekleyerek **index. js** ' yi değiştirin:

```javascript
const nconf = require('nconf');

const hotelData = require('./hotels.json');
const indexDefinition = require('./hotels_quickstart_index.json');
```

Şimdi **index. js**içindeki `run()` işlevini değiştirin. Dizinin kullanılabilir olması birkaç saniye sürebilir, bu nedenle `AzureSearchClient.postDataAsync(hotelData)`çağrılmadan önce 2 saniyelik bir duraklama ekleyin:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
    } catch (x) {
        console.log(x);
    }
}
```

Programı `node index.js`yeniden çalıştırın. Adım 1 ' de gördüğenlerden biraz farklı bir ileti kümesi görmeniz gerekir. Bu kez _, Dizin mevcut_ olur ve uygulama yeni dizin oluşturmadan ve verileri kendisine göndermeksizin silme hakkında bir ileti görmeniz gerekir. 

## <a name="3---search-an-index"></a>3 - Dizin arama

Azure portal arama hizmetinize **genel bakış** bölümünde **dizinler** sekmesine dönün. Dizininiz artık dört belge içerir ve bir miktar depolama alanı tüketir (UI 'nin dizinin temel durumunu düzgün bir şekilde yansıtması birkaç dakika sürebilir). **Arama Gezgini**'ne alınacak dizin adına tıklayın. Bu sayfa, veri sorgularıyla denemeler yapmanıza olanak sağlar. `*&$count=true` bir sorgu dizesinde aramayı deneyin ve tüm belgelerinizi ve sonuç sayısını geri almanız gerekir. Sorgu dizesi `historic&highlight=Description&$filter=Rating gt 4` deneyin ve tek bir belgeyi geri almanız gerekir ve bu "geçmiş" kelimesiyle birlikte `<em></em>` etiketlere sarmalanır. [Azure bilişsel arama 'de bir sorgu oluşturma](https://docs.microsoft.com/azure/search/search-query-overview)hakkında daha fazla bilgi edinin. 

**İndex. js** ' i açarak ve bu kodu üst kısımdaki şekilde ekleyerek kodda bu sorguları yeniden oluşturun:

```javascript
const queries = [
    "*&$count=true",
    "historic&highlight=Description&$filter=Rating gt 4&"
];
```

Aynı **index. js** dosyasında, aşağıda gösterilen `doQueriesAsync()` işlevi yazın. Bu işlev bir `AzureSearchClient` nesnesi alır ve `AzureSearchClient.queryAsync` yöntemini `queries` dizisindeki her değere uygular. Yalnızca tüm sorgular çözümlendikten sonra çözümlenen tek bir `Promise` döndürmek için `Promise.all()` işlevini kullanır. `JSON.stringify(body, null, 4)` çağrısı sorgu sonucunu daha okunaklı olacak şekilde biçimlendirir.

```javascript
async function doQueriesAsync(client) {
    return Promise.all(
        queries.map( async query => {
            const result = await client.queryAsync(query);
            const body = await result.json();
            const str = JSON.stringify( body, null, 4);
            console.log(`Query: ${query} \n ${str}`);
        })
    );
}
```

`run()` işlevini, dizin oluşturucunun çalışması için yeterince uzun süre duraklatıp ve sonra `doQueriesAsync(client)` işlevini çağırmak için değiştirin:

```javascript
const run = async () => {
    try {
        const cfg = getAzureConfiguration();
        const client = new AzureSearchClient(cfg.get("serviceName"), cfg.get("adminKey"), cfg.get("queryKey"), cfg.get("indexName"));
        
        const exists = await client.indexExistsAsync();
        await exists ? client.deleteIndexAsync() : Promise.resolve();
        // Deleting index can take a few seconds
        await sleep(2000);
        await client.createIndexAsync(indexDefinition);
        // Index availability can take a few seconds
        await sleep(2000);
        await client.postDataAsync(hotelData);
        // Data availability can take a few seconds
        await sleep(5000);
        await doQueriesAsync(client);
    } catch (x) {
        console.log(x);
    }
}
```

`AzureSearchClient.queryAsync(query)`uygulamak için, **AzureSearchClient. js**dosyasını düzenleyin. Arama farklı bir uç nokta gerektirir ve arama terimleri URL bağımsız değişkenleri haline gelir. bu nedenle, zaten yazdığınız `getIndexUrl()` ve `getPostDataUrl()` yöntemlerinin yanı sıra işlev `getSearchUrl(searchTerm)` ekleyin.

```javascript
getSearchUrl(searchTerm) { return `https://${this.searchServiceName}.search.windows.net/indexes/${this.indexName}/docs?api-version=${this.apiVersion}&search=${searchTerm}&searchMode=all`; }
 ```

`queryAsync(searchTerm)` işlevi ayrıca **AzureSearchClient. js** ' de gider ve `postDataAsync(data)` ve diğer sorgulama işlevleriyle aynı yapıyı izler: 

```javascript
async queryAsync(searchTerm) {
    console.log("\n Querying...")
    const endpoint = this.getSearchUrl(searchTerm);
    const response = await AzureSearchClient.request(endpoint, "GET", this.queryKey);
    AzureSearchClient.throwOnHttpError(response);
    return response;
}
```

Arama terimi URL 'nin bir parçası olduğundan, arama "GET" fiili ve gövde olmadan gerçekleştirilir. `queryAsync(searchTerm)`, yönetici anahtarını kullanan diğer işlevlerden farklı olarak `this.queryKey`kullandığından emin olun. Adın gösterdiği gibi sorgu anahtarları yalnızca dizini sorgulamak için kullanılabilir ve dizini herhangi bir şekilde değiştirmek için kullanılamaz. Bu nedenle, sorgu anahtarlarının istemci uygulamalarına dağıtılması daha güvenlidir.

Programı `node index.js`çalıştırın. Şimdi, önceki adımlara ek olarak sorgular gönderilir ve sonuçlar konsola yazılır.

### <a name="about-the-sample"></a>Örnek hakkında

Örnek, Azure Bilişsel Arama Dizin oluşturma ve sorgulama hakkında temel bilgileri göstermek için yeterli miktarda otel verisi kullanır.

**AzureSearchClient** sınıfı, arama hizmeti için yapılandırma, URL 'ler ve temel http isteklerini kapsar. **İndex. js** dosyası, Azure bilişsel arama hizmeti için yapılandırma verilerini, dizin oluşturma için karşıya yüklenecek otel verilerini ve `run` işlevinde, siparişlerde ve çeşitli işlemleri yürüten şekilde yükler.

`run` işlevinin genel davranışı, varsa Azure Bilişsel Arama dizinini silmek, dizini oluşturmak, bazı verileri eklemek ve bazı sorgular gerçekleştirmek için kullanılır.  

## <a name="clean-up"></a>Temizleme 

Kendi aboneliğinizde çalışırken, sizin oluşturduğunuz kaynaklara hala ihtiyacınız olup olmadığını belirlemek için bir projenin sonunda iyi bir fikir olur. Çalışan kaynaklar sizin için ücret verebilir. Kaynakları tek tek silebilir veya kaynak grubunu silerek tüm kaynak kümesini silebilirsiniz.

Sol gezinti bölmesindeki **tüm kaynaklar** veya **kaynak grupları** bağlantısını kullanarak portalda kaynakları bulabilir ve yönetebilirsiniz.
Ücretsiz bir hizmet kullanıyorsanız, üç Dizin, Dizin Oluşturucu ve veri kaynağı ile sınırlı olduğunu unutmayın. Sınırın altında kalmak için portalda ayrı ayrı öğeleri silebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu Node. js hızlı başlangıç sürümünde bir dizin oluşturmak, belgeler ile yüklemek ve sorguları çalıştırmak için bir dizi görev üzerinden çalıştık. Yapılandırmayı okuma ve sorguları tanımlama gibi mümkün olan en basit yol için bazı adımlar yaptık. Gerçek bir uygulamada, bu kaygıları esneklik ve kapsülleme sağlayacak ayrı modüller halinde yerleştirmek isteyeceksiniz. 
 
Azure Bilişsel Arama 'de zaten bir arka plana sahipseniz, bu örneği, öneri araçları (tür kullanımı veya otomatik tamamlama sorguları), filtreler ve çok yönlü gezinme için bir Springboard olarak kullanabilirsiniz. Azure Bilişsel Arama 'de yeni başladıysanız, oluşturabileceğiniz özellikler hakkında daha fazla öğreticiyi anlamak için başka öğreticiler denemeyi öneririz. Daha fazla kaynak bulmak için [belge sayfamızı](https://azure.microsoft.com/documentation/services/search/) ziyaret edin. 

> [!div class="nextstepaction"]
> [JavaScript kullanarak bir Web sayfasından Azure Bilişsel Arama çağırma](https://github.com/liamca/azure-search-javascript-samples)
