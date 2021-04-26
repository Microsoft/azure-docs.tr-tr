---
title: ML kaynaklarını yönetmek için REST kullanma
titleSuffix: Azure Machine Learning
description: Çalışma alanı gibi Azure Machine Learning kaynaklarını oluşturmak, çalıştırmak ve silmek için REST API 'Lerini kullanma veya modelleri kaydetme.
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 01/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b61050db29ff960b7923f2211b2f09649608b37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520328"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>REST kullanarak Azure ML kaynakları oluşturma, çalıştırma ve silme



Azure ML kaynaklarınızı yönetmenin birkaç yolu vardır. [Portal](https://portal.azure.com/), [komut satırı ARABIRIMI](/cli/azure)veya [Python SDK](/python/api/overview/azure/ml/intro)'yı kullanabilirsiniz. Ya da REST API seçebilirsiniz. REST API, kaynakları oluşturmak, almak, güncelleştirmek ve silmek için standart bir şekilde HTTP fiillerini kullanır. REST API, HTTP istekleri yapan herhangi bir dil veya araçla birlikte çalışabilir. REST 'in doğrudan yapısı, komut dosyası ortamlarında ve MLOps otomasyonunda iyi bir seçenek sunar. 

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Yetkilendirme belirteci alma
> * Hizmet sorumlusu kimlik doğrulamasını kullanarak düzgün şekilde biçimlendirilen REST isteği oluşturma
> * Azure ML 'nin hiyerarşik kaynakları hakkında bilgi almak için GET istekleri kullanın
> * Kaynak oluşturmak ve değiştirmek için PUT ve POST isteklerini kullanma
> * Kaynakları temizlemek için SILME isteklerini kullanma 
> * Dağıtılan modellere puan vermek için anahtar tabanlı yetkilendirmeyi kullanma

## <a name="prerequisites"></a>Önkoşullar

- Yönetici haklarına sahip olduğunuz bir **Azure aboneliği** . Bu tür bir aboneliğiniz yoksa [ücretsiz veya ücretli kişisel aboneliği](https://aka.ms/AMLFree) deneyin
- Bir [Azure Machine Learning çalışma alanı](./how-to-manage-workspace.md)
- Yönetim REST istekleri hizmet sorumlusu kimlik doğrulamasını kullanır. Çalışma alanınızda hizmet sorumlusu oluşturmak için [Azure Machine Learning kaynakları ve iş akışları için kimlik doğrulama ayarlama](./how-to-setup-authentication.md#service-principal-authentication) bölümündeki adımları izleyin
- **Kıvrımlı** yardımcı programı. **Kıvrımlı** program, [Linux Için Windows alt sistemi](/windows/wsl/install-win10) veya herhangi bir UNIX dağıtımı ile kullanılabilir. PowerShell **'de,** **çağırma-WebRequest** için bir diğer addır ve `curl -d "key=val" -X POST uri` olur `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` . 

## <a name="retrieve-a-service-principal-authentication-token"></a>Hizmet sorumlusu kimlik doğrulama belirtecini alma

Yönetim REST isteklerinin kimliği OAuth2 örtük bir akışta doğrulanır. Bu kimlik doğrulama akışı, aboneliğinizin hizmet sorumlusu tarafından sunulan bir belirteci kullanır. Bu belirteci almak için şunlar gerekir:

- Kiracı KIMLIĞINIZ (aboneliğinizin ait olduğu organizasyonu tanımlama)
- İstemci KIMLIĞINIZ (oluşturulan belirteçle ilişkilendirilecektir)
- İstemci gizli anahtarı (korunması gerekir)

Hizmet sorumlunuzu oluşturma yanıtından bu değerlerin olması gerekir. Bu değerleri almak [Azure Machine Learning kaynakları ve iş akışları için kimlik doğrulamasını ayarlama](./how-to-setup-authentication.md#service-principal-authentication)bölümünde ele alınmıştır. Şirket aboneliğinizi kullanıyorsanız, hizmet sorumlusu oluşturma izniniz olmayabilir. Bu durumda, [ücretsiz ya da ücretli bir kişisel abonelik](https://aka.ms/AMLFree)kullanmanız gerekir.

Bir belirteci almak için:

1. Bir terminal penceresi açın
1. Komut satırına aşağıdaki kodu girin
1. , Ve için kendi değerlerinizi yerine koyun `{your-tenant-id}` `{your-client-id}` `{your-client-secret}` . Bu makale boyunca, küme ayraçları ile çevrelenen dizeler, kendi uygun değerlerinizle değiştirmeniz gereken değişkenlerdir.
1. Yeni bir “kurtarma VM’si” oluşturmak ve sorunlu VM’nin işletim sistemi diskini kurtarma VM’sine veri diski olarak takmak için

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Yanıt bir saat için iyi bir erişim belirteci sağlamalıdır:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Sonraki tüm yönetim isteklerinin kimliğini doğrulamak için kullandığınız gibi belirteci unutmayın. Tüm isteklerde bir yetkilendirme üst bilgisi ayarlayarak bunu yapabilirsiniz:

```bash
curl -h "Authorization:Bearer {your-access-token}" ...more args...
```

Değeri, belirteci eklemeden önce tek bir boşluk dahil olmak üzere, "taşıyıcı" dizesi ile başlayacağını unutmayın.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Aboneliğinizle ilişkili kaynak gruplarının bir listesini alın

Aboneliğinizle ilişkili kaynak gruplarının listesini almak için şunu çalıştırın:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Azure 'da birçok REST API yayımlandı. Her hizmet sağlayıcısı API 'leri kendi temposunda güncelleştirir, ancak mevcut programları bozmadan bu şekilde devam eder. Hizmet sağlayıcı, `api-version` uyumluluk sağlamak için bağımsız değişkenini kullanır. `api-version`Bağımsız değişken hizmetten hizmete değişir. Machine Learning hizmeti için, örneğin, geçerli API sürümü `2019-11-01` . Depolama hesapları için bu `2019-06-01` . Anahtar kasaları için, bu `2019-09-01` . Tüm REST çağrılarının `api-version` bağımsız değişkenini beklenen değere ayarlaması gerekir. API gelişmeye devam ettiğinde, belirtilen sürümün söz dizimine ve semantiğine güvenebilirsiniz. Bağımsız değişken olmadan bir sağlayıcıya istek gönderirseniz `api-version` , yanıt, desteklenen değerlerin okunabilir bir listesini içerir. 

Yukarıdaki çağrı, formun sıkıştırılmış JSON yanıtına neden olur: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Çalışma alanları ve kaynakları hakkında detaya gitme

Bir kaynak grubundaki çalışma alanları kümesini almak için, ve yerine şunu çalıştırın `{your-subscription-id}` `{your-resource-group}` `{your-access-token}` : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Bu kez, bir liste içeren bir JSON listesi ve bir çalışma alanı ayrıntılarıyla her bir öğe alacaksınız:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Bir çalışma alanındaki kaynaklarla çalışmak için genel **Management.Azure.com** sunucusundan, çalışma alanının konumuna özel bir REST API sunucusuna geçiş yapacaksınız. `discoveryUrl`YUKARıDAKI JSON yanıtında anahtarın değerini aklınızda edin. Bu URL 'YI alırsanız şöyle bir yanıt alırsınız:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Yanıtın değeri, `api` ek istekler için kullanacağınız sunucunun URL 'sidir. Örneğin, denemeleri listelemek için aşağıdaki komutu gönderin. `regional-api-server`Yanıtın değeriyle değiştirin `api` (örneğin, `centralus.api.azureml.ms` ). Ayrıca `your-subscription-id` ,, `your-resource-group` , `your-workspace-name` ve `your-access-token` her zamanki gibi değiştirin:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Benzer şekilde, çalışma alanınızdaki kayıtlı modelleri almak için şunu gönderin:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Yolun, `history/v1.0` modelleri listelemeye çalışırken denemeleri listelendiğine dikkat edin `modelmanagement/v1.0` . REST API, her biri ayrı bir yol ile birkaç işlem grubuna bölünmüştür. 

|Alan|Yol|
|-|-|
|Artifacts|/Rest/api/azureml|
|Veri depolama alanları|/azure/machine-learning/how-to-access-data|
|Hiper parametre ayarı|Hiper sürücü/v 1.0/|
|Modeller|modelmanagement/v 1.0/|
|Çalıştırma geçmişi|yürütme/v 1.0/ve History/v 1.0/|

Genel örüntüsünün kullanıldığı REST API keşfedebilirsiniz:

|URL bileşeni|Örnek|
|-|-|
| https://| |
| bölgesel-API-sunucu/ | centralus.api.azureml.ms/ |
| işlemler-yol/ | geçmiş/v 1.0/ |
| Abonelikler/{aboneliğiniz-kimliğiniz}/ | Abonelikler/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{-Resource-Group}/ | resourceGroups/MyResourceGroup/ |
| sağlayıcılar/işlem-sağlayıcı/ | sağlayıcılar/Microsoft. MachineLearningServices/ |
| sağlayıcı-kaynak-yol/ | çalışma alanları/MLWorkspace/MyWorkspace/FirstExperiment/çalıştırmalar/1/ |
| işlemler-uç nokta/ | yapıtlar/meta veriler/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>PUT ve POST isteklerini kullanarak kaynak oluşturma ve değiştirme

GET fiiliyle kaynak almaya ek olarak REST API, ML çözümlerini eğitmek, dağıtmak ve izlemek için gerekli tüm kaynakların oluşturulmasını destekler. 

ML modellerini eğitim ve çalıştırma işlem kaynakları gerektirir. Bir çalışma alanının işlem kaynaklarını şu şekilde listeleyebilirsiniz: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Adlandırılmış bir işlem kaynağı oluşturmak veya üzerine yazmak için bir PUT isteği kullanırsınız. Aşağıdaki şekilde,,,, ve değerlerinin,,,, ve değerlerinin yanı sıra,,,, ve değerleri için de bilinen `your-subscription-id` `your-resource-group` `your-workspace-name` `your-access-token` `your-compute-name` değişimler buna `location` `vmSize` `vmPriority` `scaleSettings` `adminUserName` ek olarak `adminUserPassword` . [Machine Learning işlem oluşturma veya GÜNCELLEŞTIRME SDK başvurusunda](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)belirtilen şekilde, aşağıdaki komut, 30 dakika sonra ölçeklenebilen ayrılmış, tek düğümlü bir Standard_D1 (temel bir CPU işlem kaynağı) oluşturur:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> Windows terminallerinde JSON verilerini gönderirken çift tırnak sembollerine atlamanız gerekebilir. Diğer bir deyişle, gibi metinler `"location"` olur `\"location\"` . 

Başarılı bir istek bir yanıt alır `201 Created` , ancak bu yanıtın sağlama işleminin başlamış olduğunu unutmayın. Başarılı tamamlamayı onaylamak için yoklamalısınız (veya portalını kullanmanız gerekir).

### <a name="create-an-experimental-run"></a>Deneysel çalıştırma oluşturma

Bir deneme içinde bir çalıştırmaya başlamak için, eğitim betiğinizi ve ilgili dosyaları içeren bir ZIP klasörü ve bir çalıştırma tanımı JSON dosyası gerekir. ZIP klasörü, kök dizininde Python giriş dosyasına sahip olmalıdır. Örnek olarak, aşağıdaki gibi, **train.zip** adlı bir klasör gibi, önemsiz bir Python programını ZIP.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Bu sonraki kod parçacığını **definition.js** olarak kaydedin. "Komut dosyası" değerinin, yeni sıkıştırdığınız Python dosyası adıyla eşleştiğinden emin olun. "Hedef" değerinin kullanılabilir bir işlem kaynağı adıyla eşleştiğini doğrulayın. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

İçeriği kullanarak bu dosyaları sunucuya gönderin `multipart/form-data` :

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

Başarılı bir POST isteği `200 OK` , oluşturulan çalıştırmanın tanımlayıcısını içeren yanıt gövdesiyle bir durum oluşturur:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Daha önce tanıdık olması gereken REST modelini kullanarak bir çalıştırmayı izleyebilirsiniz:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Artık ihtiyacınız olmayan kaynakları silme

Bazıları, ancak tüm kaynakları SIL fiilini desteklemez. Silme kullanım örnekleri için REST API işlemeden önce [API başvurusunu](/rest/api/azureml/) denetleyin. Örneğin, bir modeli silmek için şunu kullanabilirsiniz:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Dağıtılan bir modeli Puanlama için REST kullanma

Bir modeli, bir hizmet sorumlusu ile kimlik doğrulaması yapmak üzere dağıtmak mümkün olsa da, istemciye yönelik çoğu dağıtımda anahtar tabanlı kimlik doğrulaması kullanılır. Uygun anahtarı, Studio 'nun **uç noktalar** sekmesinde dağıtımınızın sayfasında bulabilirsiniz. Aynı konum, uç noktanızın Puanlama URI 'sini gösterir. Modelinizin girişleri adlı bir JSON dizisi olarak modellenmelidir `data` :

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>REST kullanarak çalışma alanı oluşturma 

Her Azure ML çalışma alanının, diğer dört Azure kaynağına bağımlılığı vardır: yönetim özellikli bir kapsayıcı kayıt defteri, bir Anahtar Kasası, Application Insights kaynağı ve depolama hesabı. Bu kaynaklar mevcut olana kadar bir çalışma alanı oluşturamazsınız. Her bir kaynağı oluşturma hakkındaki ayrıntılar için REST API başvuruya başvurun.

Bir çalışma alanı oluşturmak için şuna benzer bir çağrı koyun `management.azure.com` . Bu çağrı çok sayıda değişken ayarlamanızı gerektirdiğinden, bu makalede ele alınan diğer çağrılarla yapısal olarak aynıdır. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Bir `202 Accepted` Yanıt almalısınız ve döndürülen üst bilgilerde bir `Location` URI. Bağımlı kaynaklarınızdan biriyle ilgili bir sorun varsa (örneğin, kapsayıcı kayıt defterinizde yönetici erişimini etkinleştirmeyi unuttuysanız), bu URI 'yi dağıtım hakkında daha fazla bilgi alabilirsiniz. 

## <a name="troubleshooting"></a>Sorun giderme

### <a name="resource-provider-errors"></a>Kaynak sağlayıcısı hataları

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Çalışma alanı taşınıyor

> [!WARNING]
> Azure Machine Learning çalışma alanınızı farklı bir aboneliğe taşımak veya sahip olunan aboneliğin yeni bir kiracıya taşınması desteklenmez. Bunun yapılması hatalara neden olabilir.

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry silme

Azure Machine Learning çalışma alanı bazı işlemler için Azure Container Registry (ACR) kullanır. İlk kez ihtiyaç duyduğunda, otomatik olarak bir ACR örneği oluşturur.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Tüm [AzureML REST API başvurusunu](/rest/api/azureml/)gezin.
- Tasarımcı [ile otomobil fiyatını tahmin](./tutorial-designer-automobile-price-train-score.md)etmek için tasarımcıyı nasıl kullanacağınızı öğrenin.
- [Jupi Not defterleri ile Azure Machine Learning](..//machine-learning/samples-notebooks.md)keşfedebilirsiniz.
