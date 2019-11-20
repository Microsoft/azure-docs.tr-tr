---
title: 'Öğretici: Azure Not defteri-kişiselleştirici'
titleSuffix: Azure Cognitive Services
description: Bu öğretici bir Azure not defterinde bir müşterinin sipariş etmesi gereken bir tür kahve öneren bir kişiselleştirici döngüsü _system benzetir. Kullanıcılar ve tercihleri bir kullanıcı veri kümesinde depolanır. Kahve hakkındaki bilgiler ayrıca bir kahve veri kümesinde bulunabilir ve saklanır.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 669ebbf595629e8093c51d76b0816edeb5f80f93
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007598"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Öğretici: Azure not defterinde kişiselleştirici kullanma

Bu öğretici, bir kişiselleştirici döngüsünün uçtan uca yaşam döngüsünü gösteren bir Azure not defterinde kişiselleştirici döngüsü çalıştırır. 

Döngü, bir müşterinin sipariş etmesi gereken tür kahve önerisinde bulunur. Kullanıcılar ve tercihleri bir kullanıcı veri kümesinde depolanır. Kahve hakkındaki bilgiler bir kahve veri kümesinde depolanır.

## <a name="users-and-coffee"></a>Kullanıcılar ve kahve

Not defteri rastgele bir Kullanıcı, günün saati ve veri kümesinden Hava durumu türünü seçer. Kullanıcı bilgilerinin Özeti:

|Müşteriler-bağlam özellikleri|Günün saati|Hava durumu türleri|
|--|--|--|
|Alice<br>Bob<br>Küçük harf<br>Dave|BAH<br>Öğleden sonra<br>Akşam|Güneşli<br>Rainy<br>Karlı| 

Kişiselleştiriciye, zaman içinde her kişi için doğru kahve seçimi hakkında bilgi sahibi olmak için, _sistem_ , kahve hakkındaki ayrıntıları da bilir.

|Kahve-eylem özellikleri|Sıcaklık türleri|Kaynak yerleri|Roast türleri|Organik|
|--|--|--|--|--|
|Cappacino|Sık Erişimli|Kenya|Koyu|Organik|
|Soğuk Brew|Soğuk|Brezilya|Açık|Organik|
|Belirtilen Mocha|Soğuk|Etiyopya|Açık|Organik değil|
|Latte|Sık Erişimli|Brezilya|Koyu|Organik değil|


Kişiselleştirici döngüsünün **amacı** , mümkün olduğunca fazla Kullanıcı ve kahve arasındaki en iyi eşleşmeyi bulmalıdır. 

Bu öğreticinin kodu, [kişiselleştirici örnekleri GitHub deposunda](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook)bulunur.

## <a name="how-the-simulation-works"></a>Simülasyonu nasıl çalışacaktır?

Çalışan sistemin başlangıcında, kişiselleştirmeden gelen öneriler yalnızca %20 ila %30 arasında (1 ' in yeniden puanı ile gösterilir) geçerlidir. Bazı isteklerden sonra, sistem geliştirilir.

İlk 10.000 istekten sonra çevrimdışı bir değerlendirme çalıştırın. Bu, kişiselleştirici verileri gözden geçirmesine ve daha iyi bir öğrenme ilkesi önerilmesine olanak tanır. Yeni öğrenme ilkesini uygulayın ve Not defterini 2.000 istek ile yeniden çalıştırın. Döngü daha iyi gerçekleştirilir.

## <a name="rank-and-reward-calls"></a>Derecelendirme ve yeniden aramalar

Kişiselleştirici hizmetine yapılan binlerce çağrının her biri için Azure Not defteri, REST API **Derecelendirme** isteği gönderir:

* Sıralama/Istek olayı için benzersiz KIMLIK
* Bağlam-kullanıcının, hava durumunun ve günün zaman içindeki rastgele bir seçimi; bir Web sitesi veya mobil cihazda kullanıcının benzetimini yapın
* Özellikler-Kişiselleştiriciye bir öneri yaptığı _Tüm_ kahve verileri

Sistem kahve seçimlerinin derecesini alır, daha sonra bu tahmini, kullanıcının bilinen seçimiyle aynı gün ve hava durumu için karşılaştırır. Bilinen seçim tahmin edilen seçim ile aynıysa, 1 ' in **yeniden** kişiselleştiriciye geri gönderilir. Aksi takdirde, ödül 0 ' dır. 

> [!Note]
> Bu bir simülasyonu olduğundan, ödül için algoritma basittir. Gerçek dünyada bir senaryoda, algoritma iş mantığını kullanmalıdır, bu da müşterinin deneyiminin çeşitli yönlerine yönelik ağırlıklarla birlikte, ödül puanı tespit etmelidir. 


## <a name="prerequisites"></a>Önkoşullar

* Bir [Azure Not defteri](https://notebooks.azure.com/) hesabı. 
* Bir [Azure kişiselleştirici kaynağı](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). 
    * Kişiselleştirici kaynağını zaten kullandıysanız, kaynağın Azure portal [verileri temizlediğinizden](how-to-settings.md#clear-data-for-your-learning-loop) emin olun. 
* [Bu örneğe](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) ilişkin tüm dosyaları bir Azure Not defteri projesine yükleyin. 

Dosya açıklamaları:

* [Kişiselleştirici. ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) , Bu öğreticinin Jupyter Not defteridir.
* [Kullanıcı veri kümesi](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) bir JSON nesnesinde depolanır.
* [Kahve veri kümesi](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) bir JSON nesnesinde depolanır. 
* [Örnek Istek JSON](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) , derecelendirme API 'SINE yönelik post isteği için beklenen biçimdir.

## <a name="configure-personalizer-resource"></a>Kişiselleştirici kaynağını yapılandırma

Azure portal, [kişiselleştirici](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) kaynağınızı **güncelleştirme modeli sıklığı** 15 saniyeye ve 15 saniyelik bir **yeniden bekleme süresi** ile yapılandırın. Bu değerler **[yapılandırma](how-to-settings.md#configure-service-settings-in-the-azure-portal)** sayfasında bulunur. 

|Ayar|Değer|
|--|--|
|güncelleştirme modeli sıklığı|15 saniye|
|bekleme süresi|15 saniye|

Bu öğreticideki değişiklikleri göstermek için bu değerlerin çok kısa bir süresi vardır. Bu değerler, bir üretim senaryosunda, kişiselleştirici döngüsüyle amacınız elde etmeden kullanılmamalıdır. 

## <a name="set-up-the-azure-notebook"></a>Azure Not defteri 'ni ayarlama

1. Çekirdeği `Python 3.6`olarak değiştirin. 
1. `Personalizer.ipynb` dosyasını açın.

## <a name="run-notebook-cells"></a>Not defteri hücrelerini Çalıştır

Her yürütülebilir hücreyi çalıştırın ve döndürülmesini bekleyin. Hücrenin yanındaki köşeli ayraçlar `*`yerine bir sayı görüntülerne zaman yapıldığını anlarsınız. Aşağıdaki bölümlerde, her hücrenin programlı olarak ne olduğu ve çıktı için ne beklendikleri açıklanmaktadır. 

### <a name="include-the-python-modules"></a>Python modüllerini dahil et

Gerekli Python modüllerini ekleyin. Hücrede çıkış yok.

```python
import json
import matplotlib.pyplot as plt
import random 
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Kişiselleştirici kaynak anahtarını ve adını ayarla

Azure portal, kişiselleştirici kaynağınızın **hızlı başlangıç** sayfasında anahtarınızı ve uç noktayı bulun. `<your-resource-name>` değerini, kişiselleştirici kaynağınızın adı olarak değiştirin. `<your-resource-key>` değerini, kişiselleştirici anahtarınızla değiştirin. 

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Geçerli tarih ve saati Yazdır
Yinelemeli işlevin başlangıç ve bitiş zamanlarını ve yinelemelerini aklınızda bırakmak için bu işlevi kullanın.

Bu hücrelerde çıkış yok. İşlev, çağrıldığında geçerli tarih ve saat çıkışı yapar.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Son model güncelleştirme zamanını al

`get_last_updated`işlevi çağrıldığında, işlev, modelin güncelleştirildiği son değiştirilme tarihini ve saatini yazdırır. 

Bu hücrelerde çıkış yok. İşlevi çağrıldığında son model eğitim tarihini çıktı olarak yapar.

İşlevi, [model özelliklerini almak](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties)IÇIN bir get REST API kullanır. 

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):
    
    print('-----checking model')
    
    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)
    
    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])
    
    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>İlke ve hizmet yapılandırmasını al

Hizmetin durumunu bu iki REST çağrısı ile doğrulayın.

Bu hücrelerde çıkış yok. İşlevi çağrıldığında hizmet değerlerini çıktı olarak yapar.

```python
def get_service_settings():
    
    print('-----checking service settings')
    
    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)
    
    print(response)
    print(response.json())
    
    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)
    
    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>URL 'Ler oluşturun ve JSON veri dosyalarını okuyun

Bu hücre 

* REST çağrılarında kullanılan URL 'Leri oluşturur 
* kişiselleştirici kaynak anahtarınızı kullanarak güvenlik üst bilgisini ayarlar 
* Sıra olay KIMLIĞI için rastgele çekirdek belirler
* JSON veri dosyalarında okur
* çağrı `get_last_updated` yöntemi-öğrenme ilkesi örnek çıktıda kaldırılmıştır
* `get_service_settings` yöntemini çağırır

Hücrede `get_last_updated` ve `get_service_settings` işlevlerine yapılan çağrıdan çıktı vardır.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None 
rankactionsjsonobj = None 
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())
    
with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())  
    
get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Çıktının `rewardWaitTime` ve `modelExportFrequency` her ikisinin de 15 saniye olarak ayarlandığını doğrulayın. 
    
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>İlk REST çağrısının sorunlarını giderme

Bu önceki hücre, Kişiselleştiriciye çağıran ilk hücredir. Çıkışdaki REST durum kodunun `<Response [200]>`olduğundan emin olun. 404 gibi bir hata alırsanız ancak kaynak anahtarınızın ve adınızın doğru olduğundan emin olun, Not defterini yeniden yükleyin.

Kahve ve kullanıcı sayısının her ikisi de 4 olduğundan emin olun. Bir hata alırsanız, tüm 3 JSON dosyalarını karşıya yüklediğinize bakın. 

### <a name="set-up-metric-chart-in-azure-portal"></a>Ölçüm grafiğini Azure portal ayarlama

Bu öğreticide daha sonra, 10.000 isteklerinin uzun süre çalışan işlemi tarayıcıdan bir güncelleştirme metin kutusuyla görünür. Uzun süre çalışan işlem sona erdiğinde bir grafik veya toplam toplam olarak bakmak daha kolay olabilir. Bu bilgileri görüntülemek için kaynakla birlikte sunulan ölçümleri kullanın. Artık, hizmete yönelik bir isteği tamamladığınıza göre grafiği oluşturabilir ve uzun süre çalışan işlem devam ederken grafiği düzenli aralıklarla yenileyebilirsiniz.

1. Azure portal, kişiselleştirici kaynağı ' nı seçin.
1. Kaynak gezintisinde, Izleme altındaki **ölçümler** ' i seçin. 
1. Grafikte **ölçüm Ekle**' yi seçin.
1. Kaynak ve ölçüm ad alanı zaten ayarlanmış. Yalnızca **başarılı çağrıların** ölçüsünü ve **Toplam**toplamayı seçmeniz gerekir.
1. Zaman filtresini son 4 saat olarak değiştirin.

    ![Son 4 saat için başarılı çağrılar için ölçüm ekleyerek ölçüm grafiğini Azure portal ayarlayın.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Grafikte üç başarılı çağrı görmeniz gerekir. 

### <a name="generate-a-unique-event-id"></a>Benzersiz bir olay KIMLIĞI oluştur

Bu işlev her bir derecelendirme çağrısı için benzersiz bir KIMLIK üretir. KIMLIK, derecelendirme ve yeniden çağırma bilgilerini belirlemek için kullanılır. Bu değer, Web görünümü KIMLIĞI veya işlem KIMLIĞI gibi bir iş işleminden gelebilir.

Hücrede çıkış yok. İşlevi çağrıldığında benzersiz KIMLIĞI çıktı olarak yapar.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Rastgele Kullanıcı, hava durumu ve günün saatini alın

Bu işlev, benzersiz bir Kullanıcı, hava durumu ve günün saatini seçer ve ardından bu öğeleri, sıralama isteğine göndermek için JSON nesnesine ekler.

Hücrede çıkış yok. İşlev çağrıldığında rastgele kullanıcının adını, rastgele hava durumunu ve günün rastgele saatini döndürür.

4 Kullanıcı ve tercihlerinin listesi, kısaltma için yalnızca bazı Tercihler gösteriliyor: 

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):   
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Tüm kahve verilerini Ekle

Bu işlev, derece isteğine gönderilmek üzere bir kahve listesinin tamamını JSON nesnesine ekler. 

Hücrede çıkış yok. İşlev çağrıldığında `rankjsonobj` değiştirir.


Tek bir kahve özelliklerinin örneği şunlardır: 

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"
        
    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Bilinen Kullanıcı tercihi ile tahmin karşılaştırması

Bu işlev, her yineleme için derece API 'SI çağrıldıktan sonra çağrılır.

Bu işlev, kullanıcının bu filtreler için Kişiselleştiriciye yönelik önerisi ile, günün hava durumu ve saati temelinde kafebinin tercihini karşılaştırır. Öneri eşleşiyorsa, 1 Puanı döndürülür, aksi takdirde puan 0 ' dır. Hücrede çıkış yok. İşlevi çağrıldığında puanı çıkış yapar.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1 
    return 0
``` 

### <a name="loop-through-calls-to-rank-and-reward"></a>Derece ve ödül çağrıları aracılığıyla döngü

Sonraki hücre, dizüstü bilgisayarın _ana_ çalışmadır, rastgele bir Kullanıcı alma, kahve listesini alma, her Ikisini de derecelendirme API 'sine gönderme. Kullanıcının bilinen tercihleriyle tahmine göre karşılaştırma yapın ve ardından kişiselleştirici hizmetine geri dönme gönderilmesini sağlar. 

Döngü `num_requests` süreler boyunca çalışır. Kişiselleştirici, bir model oluşturmak için birkaç bin çağrıya ihtiyaç duyuyor. 

Sıralama API 'sine gönderilen JSON örneği aşağıdadır. Breçekimi için kahve listesi tamamlanmamış. `coffee.json`için tüm JSON 'yi görebilirsiniz.

Sıralama API 'sine gönderilen JSON:

```json
{ 
   'contextFeatures':[ 
      { 
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[ 
      { 
         'id':'Cappucino',
         'features':[ 
            { 
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[ 

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

Derecelendirme API 'sinden JSON yanıtı:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ], 
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2', 
    'rewardActionId': 'Latte'
}
```

Son olarak, her döngü kullanıcının rastgele seçimini, hava durumunu, günün saatini ve belirlenen yeniden ödül sayısını gösterir. 1 ' in yeniden, belirtilen kullanıcı, hava durumu ve günün saati için doğru kahve türünü seçtiği kişiselleştirici kaynağını gösterir.

```console
1 Alice Rainy Morning Latte 1
```

İşlev şunları kullanır:

* Derece: bir POST REST API [sıra alır](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank). 
* Ödül: [rapor ödül](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)için bir gönderi REST API.

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1
    
    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']
    
    
    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat) 

        # show JSON to send to Rank
        print('To: ', jsonFormat)    

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction 
        print ('From: ',response.json())    

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service 
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified) 

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1
        
    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>10.000 yineleme için Çalıştır
10.000 yinelemeleri için kişiselleştirici döngüsünü çalıştırın. Bu, uzun süre çalışan bir olaydır. Not defterini çalıştıran tarayıcıyı kapatmayın. Hizmete yapılan toplam çağrıları görmek için Azure portal ölçüm grafiğini düzenli aralıklarla yenileyin. Her döngü yinelemesi için bir derecelendirme ve bir çağrı olan 20.000 çağrının etrafında yineleme yapılır. 

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Gelişimi görmek için grafik sonuçları 

`count` ve `rewards`bir grafik oluşturun.

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>10.000 derecelendirme isteği için grafik Çalıştır

`createChart` işlevini çalıştırın.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Grafik okunuyor

Bu grafik, geçerli varsayılan öğrenme ilkesi için modelin başarısını gösterir. 

![Bu grafik, test süresi için geçerli öğrenme ilkesinin başarısını gösterir.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Testin sonuna kadar ideal hedef olan döngü, araştırmayı yüzde 100 ' a yakın olan bir başarı oranının ortalamasını elde edilir. Araştırmayla varsayılan değeri %20 ' dir. 

`100-20=80`

Bu araştırma değeri, **yapılandırma** sayfasında, kişiselleştirici kaynağı için Azure Portal bulunur. 

Sıralama API 'sine verilerinize göre daha iyi bir öğrenme ilkesi bulmak için, kişiselleştirici döngüize yönelik portalda bir [çevrimdışı değerlendirme](how-to-offline-evaluation.md) çalıştırın.

## <a name="run-an-offline-evaluation"></a>Çevrimdışı değerlendirme çalıştırma

1. Azure portal, kişiselleştirici kaynağın **değerlendirmeler** sayfasını açın.
1. **Değerlendirme oluştur**' u seçin.
1. Döngü değerlendirmesi için gerekli değerlendirme adı ve tarih aralığı verilerini girin. Tarih aralığı yalnızca değerlendirmenize odaklandığınız günleri içermelidir. 
    Azure portal ![, kişiselleştirici kaynağın değerlendirmeler sayfasını açın. Değerlendirme oluştur ' u seçin. Değerlendirme adını ve tarih aralığını girin.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Bu çevrimdışı değerlendirmeyi çalıştırmanın amacı, bu döngüde kullanılan özellikler ve eylemler için daha iyi bir öğrenme ilkesi olup olmadığını belirlemektir. Daha iyi öğrenme ilkesini bulmak için **Iyileştirme bulmanın** açık olduğundan emin olun.

1. Değerlendirmeyi başlatmak için **Tamam ' ı** seçin. 
1. Bu **değerlendirmeler** sayfasında, yeni değerlendirme ve geçerli durumu listelenir. Sahip olduğunuz veri miktarına bağlı olarak, bu değerlendirme biraz zaman alabilir. Sonuçları görmek için birkaç dakika sonra bu sayfaya geri dönebilirsiniz. 
1. Değerlendirme tamamlandığında değerlendirmeyi seçip **farklı öğrenme Ilkelerinin karşılaştırmasını**seçin. Bu, kullanılabilir öğrenme ilkelerini ve verilerle nasıl davrandığını gösterir. 
1. Tabloda en üstteki öğrenme ilkesini seçin ve **Uygula**' yı seçin. Bu, modelinize ve geri iadeinize _en iyi_ öğrenme ilkesini uygular. 

## <a name="change-update-model-frequency-to-5-minutes"></a>Güncelleştirme modeli sıklığını 5 dakikaya Değiştir

1. Azure portal, hala kişiselleştirici kaynağında, **yapılandırma** sayfasını seçin. 
1. **Model güncelleştirme sıklığını** değiştirin ve **bekleme süresini** 5 dakikaya çevirin ve **Kaydet**' i seçin.

Daha fazla [bekleme süresi](concept-rewards.md#reward-wait-time) ve [model güncelleştirme sıklığı](how-to-settings.md#model-update-frequency)hakkında daha fazla bilgi edinin.

```python
#Verify new learning policy and times
get_service_settings()
```

Çıktının `rewardWaitTime` ve `modelExportFrequency` her ikisinin de 5 dakika olarak ayarlandığını doğrulayın. 
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Yeni öğrenme ilkesini doğrula 

Azure not defterine dönün ve yalnızca 2.000 yinelemesi için aynı döngüyü çalıştırarak devam edin. Hizmete yapılan toplam çağrıları görmek için Azure portal ölçüm grafiğini düzenli aralıklarla yenileyin. Her döngü yinelemesi için bir derecelendirme ve bir çağrı olan 4.000 çağrının etrafında yineleme yapılır. 

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>2\.000 derecelendirme isteği için grafik Çalıştır

`createChart` işlevini çalıştırın.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>İkinci grafiği gözden geçirme

İkinci grafik, Kullanıcı tercihleri ile hizalanan derece tahminlerde görünür bir artış göstermelidir. 

![İkinci grafik, Kullanıcı tercihleri ile hizalanan derece tahminlerde görünür bir artış göstermelidir.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Öğretici serisine devam etmeyi planlamıyorsanız, aşağıdaki kaynakları temizleyin:

* Azure Not defteri projenizi silin. 
* Kişiselleştirici kaynağını silin. 

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekte kullanılan [Jupyter Not defteri ve veri dosyaları](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) , kişiselleştirici için GitHub deposunda mevcuttur. 

