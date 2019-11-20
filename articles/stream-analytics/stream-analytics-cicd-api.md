---
title: IoT Edge üzerinde Azure Stream Analytics için CI/CD 'yi yapmak için REST API 'Lerini kullanma
description: REST API 'Leri kullanarak Azure Stream Analytics için sürekli tümleştirme ve dağıtım işlem hattı uygulamayı öğrenin.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: a716991eaa84a6937c959885ff9c4ae5c18be35e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163626"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>API 'Leri kullanarak IoT Edge Stream Analytics için CI/CD uygulayın

REST API 'Leri kullanarak Azure Stream Analytics işleri için sürekli tümleştirmeyi ve dağıtımı etkinleştirebilirsiniz. Bu makalede, hangi API 'Lerin kullanılacağı ve bunların nasıl kullanılacağı örnekleri sunulmaktadır. REST API 'Leri Azure Cloud Shell desteklenmez.

## <a name="call-apis-from-different-environments"></a>Farklı ortamlardan API 'Leri çağırma

REST API 'Ler hem Linux hem de Windows 'dan çağrılabilir. Aşağıdaki komutlar API çağrısı için uygun söz dizimini gösterir. Belirli API kullanımı, bu makalenin sonraki bölümlerinde özetlenmiştir.

### <a name="linux"></a>Linux

Linux için `Curl` veya `Wget` komutlarını kullanabilirsiniz:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Windows için PowerShell 'i kullanın: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Kenarda bir ASA işi oluşturma 
 
Stream Analytics işi oluşturmak için, Stream Analytics API 'sini kullanarak PUT metodunu çağırın.

|Yöntem|İstek URL'si|
|------|-----------|
|KONUR|https://management.azure.com/subscriptions/{**abonelik-kimliği**}/ResourceGroups/{**Resource-Group-Name**}/Providers/Microsoft.StreamAnalytics/streamingjobs/{**iş-adı**}? api-Version = 2017-04 -01-Preview|
 
**Kıvrımlı**kullanılarak komut örneği:

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
JSON 'da istek gövdesi örneği:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Daha fazla bilgi için [API belgelerine](/rest/api/streamanalytics/stream-analytics-job)bakın.  
 
## <a name="publish-edge-package"></a>Edge paketini Yayımla 
 
IoT Edge üzerinde Stream Analytics işi yayımlamak için, Edge paketi yayımlama API 'sini kullanarak POST yöntemini çağırın.

|Yöntem|İstek URL'si|
|------|-----------|
|Yayınla|https://management.azure.com/subscriptions/{**SubscriptionID**}/ResourceGroups/{**resourcegroupname**}/Providers/Microsoft.StreamAnalytics/streamingjobs/{**JobName**}/publishedgepackage? api-Version = 2017-04 -01-Preview|

Bu zaman uyumsuz işlem, iş başarıyla yayımlanana kadar 202 durumunu döndürür. Konum yanıt üst bilgisi işlemin durumunu almak için kullanılan URI 'yi içerir. İşlem çalışırken, konum üstbilgisindeki URI 'ye yapılan bir çağrı 202 durumunu döndürür. İşlem tamamlandığında, konum üstbilgisindeki URI 200 durumunu döndürür. 

Bir Edge paketi örneği, **kıvrımlı**kullanarak çağrı yayınlama: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
POST çağrısını yaptıktan sonra boş bir gövdeye sahip bir yanıt beklemeniz gerekir. Yanıtın başında bulunan URL 'YI bulun ve daha fazla kullanmak üzere kaydedin.
 
Yanıt beden URL örneği:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Yanıtın baş bölümünde bulduğunuz URL ile bir API çağrısı yapmak için aşağıdaki komutu çalıştırmadan önce bir ila iki dakika bekleyin. 200 yanıtını alamazsanız komutu yeniden deneyin.
 
**Kıvrmadan**döndürülen URL ile API çağrısı yapma örneği:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Yanıt, kenar dağıtım betiğine eklemek için gereken bilgileri içerir. Aşağıdaki örneklerde, hangi bilgilerin toplanması gerektiğini ve dağıtım bildiriminde nereye ekleneceğini gösterilmektedir.
 
Başarıyla yayımlandıktan sonra örnek yanıt gövdesi:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Dağıtım bildiriminin örneği: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Dağıtım bildiriminin yapılandırmasından sonra dağıtım için [Azure CLI ile Azure IoT Edge modüllerini dağıtma](../iot-edge/how-to-deploy-modules-cli.md) bölümüne bakın.


## <a name="next-steps"></a>Sonraki adımlar 
 
* [IoT Edge üzerinde Azure Stream Analytics](stream-analytics-edge.md)
* [IoT Edge öğreticide ASA](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Visual Studio araçlarını kullanarak Stream Analytics Edge işleri geliştirme](stream-analytics-tools-for-visual-studio-edge-jobs.md)
