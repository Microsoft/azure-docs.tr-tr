---
title: Media Encoder Standard kullanarak bir Azure varlığını kodlama Microsoft Docs
description: Azure Media Services medya içeriğini kodlamak için Media Encoder Standard nasıl kullanacağınızı öğrenin. Kod örnekleri REST API kullanır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4b7a188a9509c3510de2843b232f57e3437177b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009836"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Media Encoder Standard kullanarak bir varlığı kodlama

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Genel Bakış

Internet üzerinden dijital video teslim etmek için medyayı sıkıştırmanız gerekir. Dijital video dosyaları büyüktür ve Internet üzerinden teslim etmek için çok büyük olabilir ya da müşterilerinizin cihazları düzgün şekilde görüntülenebilir. Kodlama, müşterilerinizin medyanızı görüntülemesi için video ve ses sıkıştırma işlemidir.

Kodlama işleri, Azure Media Services en yaygın işlem işlemlerinden biridir. Bir kodlamadan diğerine medya dosyalarını dönüştürmek için kodlama işleri oluşturursunuz. Kodlarken, Media Services yerleşik Kodlayıcısı 'nı (Media Encoder Standard) kullanabilirsiniz. Media Services iş ortağı tarafından sunulan bir kodlayıcı de kullanabilirsiniz. Üçüncü taraf kodlayıcılar Azure Marketi aracılığıyla kullanılabilir. Kodlarınız için tanımlanan önceden ayarlanmış dizeleri veya önceden ayarlanmış yapılandırma dosyalarını kullanarak kodlama görevlerinin ayrıntılarını belirtebilirsiniz. Kullanılabilir hazır ayarların türlerini görmek için bkz. [Media Encoder Standard Için görev önayarları](./media-services-mes-presets-overview.md).

Her işin, gerçekleştirmek istediğiniz işleme türüne bağlı olarak bir veya daha fazla görevi olabilir. REST API aracılığıyla işleri ve ilgili görevlerini iki şekilde oluşturabilirsiniz:

* Görevler, Iş varlıklarındaki görevler gezintisi özelliği aracılığıyla satır içi olarak tanımlanabilir.
* OData toplu işlemini kullanın.

Kaynak dosyalarınızı her zaman Uyarlamalı bit hızı olan bir MP4 kümesine kodlamanızı ve sonra [dinamik paketleme](media-services-dynamic-packaging-overview.md)kullanarak kümeyi istenen biçime dönüştürmenizi öneririz.

Çıkış varlığınız depolama şifreli ise, varlık teslim ilkesini yapılandırmanız gerekir. Daha fazla bilgi için bkz. [varlık teslim Ilkesini yapılandırma](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Media Services varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).

Medya işlemcilere başvurmayı başlatmaya başlamadan önce, doğru medya işlemci KIMLIĞI olduğunu doğrulayın. Daha fazla bilgi için bkz. [medya Işlemcileri edinme](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Media Services’e bağlanmak

AMS API 'sine bağlanma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Tek bir kodlama göreviyle iş oluşturma

> [!NOTE]
> Media Services REST API çalışırken aşağıdaki noktalar geçerlidir:
>
> Media Services varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).
>
> JSON kullanırken ve istekte **__metadata** anahtar sözcüğünü kullanmak için belirtirken (örneğin, bağlantılı bir nesneye başvurmak Için) **Accept** üst bilgisini [JSON verbose biçimine](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)ayarlamanız gerekir: Accept: Application/JSON; OData = verbose.
>
>

Aşağıdaki örnek, belirli bir çözünürlükte ve kalitede video kodlamak için bir görev ayarlama ile bir işi nasıl oluşturup nakledeceğiniz gösterilmektedir. Media Encoder Standard ile kodlarken, [burada](./media-services-mes-presets-overview.md)belirtilen görev yapılandırması önayarlarını kullanabilirsiniz.

İstek:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}
```

Yanıt:

```console
HTTP/1.1 201 Created

. . .
```

### <a name="set-the-output-assets-name"></a>Çıkış varlığının adını ayarla
Aşağıdaki örnekte, assetName özniteliğinin nasıl ayarlanacağı gösterilmektedir:

```console
{ "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}`
```

### <a name="considerations"></a>Dikkat edilmesi gerekenler
* TaskBody özellikleri, giriş sayısını veya görev tarafından kullanılan çıkış varlıklarını tanımlamak için değişmez XML kullanmalıdır. Görev makalesi XML için XML şema tanımı içerir.
* TaskBody tanımında, ve için her bir iç değerin `<inputAsset>` `<outputAsset>` Jobınputasset (değer) veya Joi putasset (değer) olarak ayarlanması gerekir.
* Bir görevde birden fazla çıkış varlığı olabilir. Bir joi Putasset (x), bir iş içindeki görevin çıktısı olarak yalnızca bir kez kullanılabilir.
* Jobınputasset veya Joi Putasset değerini bir görevin giriş varlığı olarak belirtebilirsiniz.
* Görevler bir bisiklet oluşturmamalıdır.
* Jobınputasset veya Joi Putasset 'e geçirdiğiniz değer parametresi bir varlık için dizin değerini temsil eder. Gerçek varlıklar, iş varlığı tanımındaki ınputmediavarlıklar ve Outputmediavarlıklarının gezinti özelliklerinde tanımlanmıştır.
* Media Services OData v3 üzerinde oluşturulduğundan, ınputmediavarlıklarının ve Outputmediavarlıklarının gezinti özelliği koleksiyonlarındaki tek tek varlıklar "__metadata: URI" ad-değer çifti aracılığıyla başvurulur.
* Inputmediavarlıkların, Media Services oluşturduğunuz bir veya daha fazla varlık ile eşlenir. Outputmediavarlıklar sistem tarafından oluşturulur. Mevcut bir varlığa başvurmazlar.
* Outputmediavarlıklarının adı, assetName özniteliği kullanılarak yapılabilir. Bu öznitelik yoksa, Outputmediakıymetin adı, öğenin iç metin değerinin, `<outputAsset>` Iş adı değeri veya Iş kimliği değeri (ad özelliğinin tanımlı olmadığı durumlarda) bir sonekine sahip olduğu her şey olur. Örneğin, assetName için "Sample" değeri ayarlarsanız, Outputmediavarlık Name özelliği "Sample" olarak ayarlanır. Ancak, assetName için bir değer ayarlamadıysanız ancak iş adını "NewJob" olarak ayarlarsanız, Outputmediavarlık adı "Joi Putasset (değer) _NewJob olur.

## <a name="create-a-job-with-chained-tasks"></a>Zincirleme görevlerle iş oluşturma
Birçok uygulama senaryosunda, geliştiriciler bir dizi işleme görevi oluşturmak ister. Media Services, bir dizi zincirleme görev oluşturabilirsiniz. Her görev farklı işleme adımları gerçekleştirir ve farklı medya işlemcileri kullanabilir. Zincirli görevler bir görevden diğerine bir varlık ve varlık üzerinde doğrusal bir görev dizisi gerçekleştirerek bir görev oluşturabilir. Ancak, bir işte gerçekleştirilen görevlerin sırayla olması gerekmez. Bir zincir görevi oluşturduğunuzda, zincirleme **ITask** nesneleri tek bir **ıjob** nesnesinde oluşturulur.

> [!NOTE]
> Şu anda iş başına 30 görev sınırı vardır. 30 ' dan fazla görevden daha fazla görev zinciri oluşturmanız gerekiyorsa, görevleri içermesi için birden fazla iş oluşturun.
>
>

```console
POST https://media.windows.net/api/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      },
      {  
         "Configuration":"H264 Smooth Streaming 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
      }
   ]
}
```

### <a name="considerations"></a>Dikkat edilmesi gerekenler
Görev zincirlemesini etkinleştirmek için:

* Bir işin en az iki görevi olmalıdır.
* Girişi işteki başka bir görevin çıktısı olan en az bir görev olmalıdır.

## <a name="use-odata-batch-processing"></a>OData toplu işlemeyi kullanma
Aşağıdaki örnek, bir iş ve görev oluşturmak için OData Batch işlemenin nasıl kullanılacağını gösterir. Toplu işleme hakkında daha fazla bilgi için bkz. [Açık Veri Protokolü (OData) toplu işleme](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

```console
POST https://media.windows.net/api/$batch HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Accept: multipart/mixed
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
Host: media.windows.net


--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/Jobs HTTP/1.1
Content-ID: 1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/$1/Tasks HTTP/1.1
Content-ID: 2
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
   "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
```


## <a name="create-a-job-by-using-a-jobtemplate"></a>JobTemplate kullanarak iş oluşturma
Ortak bir görev kümesini kullanarak birden çok varlığı işlem yaparken, varsayılan görev önayarlarını belirtmek veya görevlerin sırasını ayarlamak için bir JobTemplate kullanın.

Aşağıdaki örnek, satır içi olarak tanımlanan TaskTemplate ile bir JobTemplate oluşturmayı gösterir. TaskTemplate, varlık dosyasını kodlamak için MediaProcessor olarak Media Encoder Standard kullanır. Ancak, diğer Mediaişlemciler de kullanılabilir.

```console
POST https://media.windows.net/API/JobTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
```

> [!NOTE]
> Diğer Media Services varlıkların aksine, her TaskTemplate için yeni bir GUID tanımlayıcısı tanımlamanız ve bunu istek gövdesinden Tasktemplateıd ve ID özelliğine yerleştirmeniz gerekir. İçerik Tanımlama Şeması Azure Media Services varlıkları tanımlama bölümünde açıklanan düzeni izlemelidir. Ayrıca, JobTemplates güncelleştirilemez. Bunun yerine, güncelleştirilmiş değişikliklerinizle yeni bir tane oluşturmanız gerekir.
>
>

Başarılı olursa, aşağıdaki yanıt döndürülür:

```console
HTTP/1.1 201 Created

. . .
```

Aşağıdaki örnek, bir JobTemplate kimliğine başvuran bir işin nasıl oluşturulacağını gösterir:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
```

Başarılı olursa, aşağıdaki yanıt döndürülür:

```console
HTTP/1.1 201 Created

. . .
```


## <a name="advanced-encoding-features-to-explore"></a>Araştırılacak gelişmiş kodlama özellikleri
* [Küçük resim oluşturma](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Kodlama sırasında küçük resimler oluşturma](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Kodlama sırasında videoları kırpın](media-services-crop-video.md)
* [Kodlama ön ayarlarını özelleştirme](media-services-custom-mes-presets-with-dotnet.md)
* [Görüntü ile video paylaşma veya filigran](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
Bir varlığı kodlamak için bir iş oluşturmayı öğrenmiş olduğunuza göre, bkz. [Media Services ile iş ilerlemesini denetleme](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Ayrıca bkz.
[Medya Işlemcileri al](media-services-rest-get-media-processor.md)