---
title: Bildirimler ve model yedeklemesi için Azure Storage 'ı tümleştirme
titleSuffix: Azure Cognitive Services
description: Özel Görüntü İşleme modellerini eğdiğinizde veya dışa aktardığınızda anında iletme bildirimleri almak için Azure depolama 'nın nasıl tümleştirileceğini öğrenin. Ayrıca, dışarıya aktarılmış modellerin bir yedeklemesini de kaydedebilirsiniz.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fd4ad1443f466a78abfc569d5f52f6bdeff2d5be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048893"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>Bildirimler ve yedekleme için Azure Storage 'ı tümleştirme

Proje Eğitimi/dışarı aktarma etkinliğinin ve yayımlanan modellerin yedek kopyalarının anında bildirimlerini almak için Özel Görüntü İşleme projenizi bir Azure Blob depolama kuyruğu ile tümleştirebilirsiniz. Bu özellik, uzun işlemler çalışırken hizmetin sonuçları sürekli olarak yoklanmaması için yararlıdır. Bunun yerine, depolama kuyruğu bildirimlerini iş akışınız ile tümleştirebilirsiniz.

Bu kılavuz, bu REST API 'Lerini kıvrımlı ile nasıl kullanacağınızı gösterir. İstekleri vermek için Postman gibi bir HTTP isteği hizmeti de kullanabilirsiniz.

> [!NOTE]
> Anında iletme bildirimleri **CreateProject** API 'sindeki Isteğe bağlı _notificationqueueuri_ parametresine bağlıdır ve model yedeklemeleri de Isteğe bağlı _exportmodelcontaineruri_ parametresini kullanmanızı gerektirir. Bu kılavuz, her ikisini de tüm özellikler kümesi için kullanır.

## <a name="prerequisites"></a>Önkoşullar

- Azure 'da bir Özel Görüntü İşleme kaynağı. Bir tane yoksa, Azure portal gidin ve [Yeni bir özel görüntü işleme kaynağı oluşturun](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true). Bu özellik şu anda bilişsel hizmet kaynağını (hepsi bir anahtarda) desteklememektedir.
- Blob kapsayıcısı olan bir Azure depolama hesabı. Bu adımla ilgili yardıma ihtiyacınız varsa [Azure Storage laboratuvarının 1. alıştırmaını](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) izleyin.
* [PowerShell sürüm 6.0 +](/powershell/scripting/install/installing-powershell-core-on-windows)veya benzer bir komut satırı uygulaması.

## <a name="set-up-azure-storage-integration"></a>Azure depolama tümleştirmesini ayarlama

Azure portal Özel Görüntü İşleme eğitim kaynağına gidin, **kimlik** sayfasını seçin ve sistem tarafından atanan yönetilen kimliği etkinleştirin.

Sonra, Azure portal depolama kaynağına gidin. **Erişim denetimi (IAM)** sayfasına gidin ve her tümleştirme özelliği için bir rol ataması ekleyin:
* Model yedekleme özelliğini kullanmayı planlıyorsanız Özel Görüntü İşleme eğitim kaynağınızı seçin ve **Depolama Blobu veri katılımcısı** rolünü atayın. 
* Ardından, bildirim kuyruğu özelliğini kullanmayı planlıyorsanız Özel Görüntü İşleme eğitim kaynağınızı seçin ve **depolama kuyruğu veri katılımcısı** 'nı atayın.

> [!div class="mx-imgBorder"]
> ![Depolama hesabı rol atama sayfası ekle](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>Tümleştirme URL 'Lerini al

Daha sonra, Özel Görüntü İşleme kaynağınızın bu uç noktalara erişmesine izin veren URL 'Leri alacaksınız.

Bildirim kuyruğu tümleştirme URL 'SI için, depolama hesabınızın **Kuyruklar** sayfasına gidin, yeni bir kuyruk ekleyin ve URL 'sini geçici bir konuma kaydedin.

> [!div class="mx-imgBorder"]
> ![Azure depolama kuyruğu sayfası](./media/storage-integration/queue-url.png) 

Model yedekleme tümleştirmesi URL 'SI için, depolama hesabınızın **kapsayıcılar** sayfasına gidin ve yeni bir kapsayıcı oluşturun. Sonra seçin ve **Özellikler** sayfasına gidin. URL 'YI geçici bir konuma kopyalayın.
 
> [!div class="mx-imgBorder"]
> ![Azure depolama kapsayıcısı özellikleri sayfası](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Özel Görüntü İşleme projeyi tümleştirin

Artık tümleştirme URL 'Lerine sahip olduğunuza göre, Azure depolama özelliklerini tümleştiren yeni bir Özel Görüntü İşleme projesi oluşturabilirsiniz. Ayrıca, özellikleri eklemek için mevcut bir projeyi güncelleştirebilirsiniz.

### <a name="create-new-project"></a>Yeni proje oluşturma

[CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) API 'sini çağırdığınızda, _exportmodelcontaineruri_ ve _notificationqueueuri_ isteğe bağlı parametrelerini ekleyin. Önceki bölümde aldığınız URL değerlerini atayın. 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

`200/OK`Yanıt alırsanız, URL 'lerin başarıyla ayarlandığı anlamına gelir. URL değerlerinizi JSON yanıtında de görmeniz gerekir:

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>Mevcut projeyi Güncelleştir

Mevcut bir projeyi Azure Storage Özellik tümleştirmesi ile güncelleştirmek için, güncelleştirmek istediğiniz projenin KIMLIĞINI kullanarak [Updateproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) API 'sini çağırın. 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

İstek gövdesini (), `body` _Exportmodelcontaineruri_ ve _notificationqueueuri_ IÇIN uygun değerleri doldurarak şu JSON biçimine ayarlayın:

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

`200/OK`Yanıt alırsanız, URL 'lerin başarıyla ayarlandığı anlamına gelir.

## <a name="verify-the-connection"></a>Bağlantıyı doğrulama 

Önceki bölümde bulunan API çağrın Azure depolama hesabınızda yeni bilgileri zaten tetiklemeli olması gerekir. 

Belirttiğiniz kapsayıcıda, bir **Customvision-TestPermission** klasörü içinde bir test blobu olmalıdır. Bu blob yalnızca geçici olarak mevcut olacaktır.

Bildirim kuyruğunuza aşağıdaki biçimde bir test bildirimi görmeniz gerekir:
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>Olay bildirimlerini al

Hazırsanız, normal bir eğitim işlemi yapmak için, projenizdeki [Traınproject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API 'sini çağırın.

Depolama bildirim kuyruğunuza Eğitim bittiğinde bir bildirim alacaksınız:

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"`Alan ya da olabilir `"TrainingCompleted"` `"TrainingFailed"` . `"iterationId"`Alan, eğitilen MODELIN kimliğidir.

## <a name="get-model-export-backups"></a>Model dışarı aktarma yedeklemelerini al

Hazırsanız, belirtilen bir platforma eğitilen bir modeli dışarı aktarmak için [Exportiteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API 'sini çağırın.

Belirlediğiniz depolama kapsayıcıda, dışarıya aktarılmış modelin yedek kopyası görünür. Blob adı şu biçimdedir:

```
{projectId} - {iterationId}.{platformType}
```

Ayrıca, dışa aktarma tamamlandığında kuyruğunuza bir bildirim alırsınız. 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"`Alan ya da olabilir `"ExportCompleted"` `"ExportFailed"` . `"modelUri"`Bu alan, kapsayıcıda depolanan yedekleme modelinin URL 'sini içerir. Bu işlem, baştan itibaren sıra bildirimleri tümleştirirsiniz. Yapmadıysanız, `"modelUri"` alan özel görüntü işleme modeli blobunun SAS URL 'sini gösterir.

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, bir projeyi Özel Görüntü İşleme kaynakları arasında kopyalamayı ve taşımayı öğrendiniz. Daha sonra, Özel Görüntü İşleme ile yapabileceklerinizi görmek için API başvuru belgelerini inceleyin.
* [REST API başvuru belgeleri (eğitim)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [REST API başvuru belgeleri (tahmin)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)