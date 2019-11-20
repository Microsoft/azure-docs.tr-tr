---
title: Azure Media Services ve Azure CLı ile video dosyaları akışı | Microsoft Docs
description: Bu öğreticinin adımlarını izleyerek yeni bir Azure Media Services hesabı oluşturun, bir dosya kodlayın ve Azure Media Player için akışını yapın.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, akış
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: ''
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 58193a94d09dee5df611acf5d98c8661dd18abbb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639977"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---cli"></a>Öğretici: URL 'yi temel alan uzak bir dosyayı kodlayın ve video CLı 'YI akışa sunun

Bu öğreticide, Azure Media Services ve Azure CLı kullanarak çeşitli tarayıcılarda ve cihazlarda videoların kolayca nasıl kodlanacağı ve akış akışının nasıl yapılacağı gösterilmektedir. HTTPS veya SAS URL 'Leri ya da Azure Blob depolama alanındaki dosyalara yollar kullanarak giriş içeriği belirtebilirsiniz.

Bu makaledeki örnek, bir HTTPS URL 'SI aracılığıyla erişilebilir hale getirmek istediğiniz içeriği kodluyor. Media Services v3 Şu anda HTTPS URL 'Leri üzerinden öbekli aktarım kodlamasını desteklememektedir.

Bu öğreticinin sonuna kadar video akışı sağlayabileceksiniz.  

![Videoyu yürütme](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Media Services hesabı oluşturma

Azure 'da medya içeriğini şifrelemeden, kodlamadan, çözümleyebilmeniz, yönetebilmeniz ve akışınızdan önce bir Media Services hesabı oluşturmanız gerekir. Bu hesabın bir veya daha fazla depolama hesabıyla ilişkilendirilmesi gerekir.

Media Services hesabınız ve tüm ilişkili depolama hesaplarınız aynı Azure aboneliğinde olmalıdır. Gecikme ve veri çıkış maliyetlerini sınırlamak için Media Services hesabıyla aynı yerde olan depolama hesaplarını kullanmanızı öneririz.

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Azure Storage hesabı oluşturma

Bu örnekte, genel amaçlı v2 standart LRS hesabı oluşturacağız.

Depolama hesaplarıyla denemek istiyorsanız kullanın `--sku Standard_LRS`. Üretim için bir SKU seçerken, iş sürekliliği için coğrafi çoğaltma `--sku Standard_RAGRS`sağlayan kullanmayı göz önünde bulundurun. Daha fazla bilgi için bkz. [depolama hesapları](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Azure Media Services hesabı oluşturma

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Aşağıdakine benzer bir yanıt alırsınız:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>Akış uç noktasını başlatma

Aşağıdaki Azure CLı komutu varsayılan **akış uç noktasını**başlatır.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Aşağıdakine benzer bir yanıt alırsınız:

```
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Akış uç noktası zaten çalışıyorsa şu iletiyi alırsınız:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Uyarlamalı bit hızı kodlaması için bir dönüşüm oluşturun

Videoları kodlamak veya analiz etmek için ortak görevleri yapılandırmak üzere bir **dönüşüm** oluşturun. Bu örnekte, uyarlamalı bit hızı kodlaması yapacağız. Sonra, oluşturduğumuz dönüşümün altına bir iş gönderiyoruz. İş, verilen videoya veya ses içeriği girişine dönüştürmeyi uygulamak için Media Services isteğidir.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Aşağıdakine benzer bir yanıt alırsınız:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Çıktı varlığı oluşturma

Kodlama işinin çıkışı olarak kullanılacak bir çıktı **varlığı** oluşturun.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Aşağıdakine benzer bir yanıt alırsınız:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>HTTPS girişi kullanarak bir işi başlatma

İşleri işlem videolarına gönderdiğinizde, Media Services Giriş videosunu nerede bulacağınızı söylemeniz gerekir. Bir seçenek, bu örnekte gösterildiği gibi, iş girişi olarak bir HTTPS URL 'SI belirtmektir.

Çalıştırdığınızda `az ams job start`, iş çıktısında bir etiket ayarlayabilirsiniz. Daha sonra, çıktı varlığının ne için olduğunu tanımlamak için etiketini kullanabilirsiniz.

- Etikete bir değer atarsanız, '--output-varlıklar ' öğesini "assetname = Label" olarak ayarlayın.
- Etikete bir değer atamadıysanız, '--output-varlıklar ' öğesini "assetname =" olarak ayarlayın.

  ' A "=" `output-assets`eklediğimiz konusunda dikkat edin.

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

Aşağıdakine benzer bir yanıt alırsınız:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Durumu kontrol etme

Beş dakika içinde işin durumunu kontrol edin. "Tamamlandı" olmalıdır. Tamamlanmadı, birkaç dakika sonra yeniden denetleyin. İşiniz bittiğinde sonraki adıma gidin ve bir **akış Bulucu**oluşturun.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Akış Bulucu oluşturma ve yol alma

Kodlama tamamlandıktan sonra, bir sonraki adım çıktı kıymetindeki videonun kayıttan yürütmeye yönelik olarak kullanılabilmesini sağlar. Bunu yapmak için ilk olarak bir akış Bulucu oluşturun. Ardından, istemcilerin kullanabileceği akış URL 'Leri oluşturun.

### <a name="create-a-streaming-locator"></a>Akış bulucusu oluşturma

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Aşağıdakine benzer bir yanıt alırsınız:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Akış Bulucu yollarını al

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Aşağıdakine benzer bir yanıt alırsınız:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

HTTP canlı akış (HLS) yolunu kopyalayın. Bu durumda, `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-the-url"></a>URL 'YI oluşturma 

### <a name="get-the-streaming-endpoint-host-name"></a>Akış uç noktası ana bilgisayar adını alma

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```
`hostName` Değeri kopyalayın. Bu durumda, `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-the-url"></a>URL 'YI birleştirme

"https://" + &lt;hostname değer&gt; + &lt;HLS yol değeri&gt;

Bir örneği aşağıda verilmiştir:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Azure Media Player kullanarak kayıttan yürütmeyi test etme

> [!NOTE]
> Bir oynatıcı HTTPS sitesinde barındırılıyorsa, URL 'YI "https" ile başlattığınızdan emin olun.

1. Bir Web tarayıcısı açın ve adresine gidin [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. **URL** kutusunda, önceki bölümde oluşturduğunuz URL 'yi yapıştırın. URL 'yi HLS, Dash veya düz biçimde yapıştırabilirsiniz. Azure Media Player, cihazınızda kayıttan yürütmek için otomatik olarak uygun bir akış protokolü kullanacaktır.
3. **Oynatıcıyı Güncelleştir**' i seçin.

>[!NOTE]
>Azure Media Player, test için kullanılabilir, ancak üretim ortamında kullanılmamalıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunuzda, bu öğretici için oluşturduğunuz Media Services ve depolama hesapları da dahil olmak üzere herhangi bir kaynağa ihtiyacınız yoksa, kaynak grubunu silin.

Bu CLı komutunu çalıştırın:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

[Media Services genel bakış](media-services-overview.md)

