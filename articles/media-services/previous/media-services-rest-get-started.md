---
title: REST kullanarak isteğe bağlı içerik sunmaya başlayın | Microsoft Docs
description: Bu öğretici, REST API kullanarak Azure Media Services bir isteğe bağlı içerik teslim uygulaması uygulama adımlarında size yol gösterir.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f0f9b2c974c0a095719973b1c6173d682718dbbf
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "69014864"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>REST kullanarak isteğe bağlı içerik sunmaya başlayın  

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>En son sürüm olan [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)’ü inceleyin. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-from-v2-to-v3.md)

Bu hızlı başlangıç, Azure Media Services (AMS) REST API 'Lerini kullanarak Isteğe bağlı video (VoD) içerik teslim uygulaması uygulama adımlarında size yol gösterir.

Öğretici, temel Media Services iş akışını ve Media Services geliştirmek için gereken en genel programlama nesnelerini ve görevleri tanıtır. Öğreticinin tamamlanmasında, karşıya yüklediğiniz, Kodladığınız ve indirdiğiniz bir örnek medya dosyasını akışla veya aşamalı olarak indirebilirsiniz.

Aşağıdaki resimde Media Services OData modeliyle VoD uygulamaları geliştirirken en sık kullanılan nesnelerin bazıları gösterilmektedir.

Resmi tam boyutlu görüntülemek için tıklayın.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Önkoşullar
REST API 'Leri ile Media Services geliştirmeye başlamak için aşağıdaki Önkoşullar gereklidir.

* Bir Azure hesabı. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/).
* Bir Media Services hesabı. Bir Media Services hesabı oluşturmak için bkz. [Media Services hesabı oluşturma](media-services-portal-create-account.md).
* Media Services REST API nasıl geliştirileceği hakkında anlamak. Daha fazla bilgi için bkz. [Media Services REST API genel bakış](media-services-rest-how-to-use.md).
* Tercih ettiğiniz ve HTTP isteklerini ve yanıtlarını gönderebilen bir uygulama. Bu öğretici [Fiddler](https://www.telerik.com/download/fiddler)kullanır.

Bu hızlı başlangıçta aşağıdaki görevler gösterilmiştir.

1. Akış uç noktalarını başlatın (Azure portalını kullanarak).
2. REST API Media Services hesabına bağlanın.
3. Yeni bir varlık oluşturun ve REST API bir video dosyası yükleyin.
4. Kaynak dosyayı, REST API bir uyarlamalı bit hızı MP4 dosyaları kümesine kodlayın.
5. Varlığı yayımlayın ve REST API ile akış ve aşamalı indirme URL 'Lerini alın.
6. İçeriğinizi oynatın.

>[!NOTE]
>Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Aynı gün/erişim izinlerini (örneğin, uzun bir süre (karşıya yükleme olmayan ilkeler) yerinde kalması amaçlanan konum belirleyicilerinin ilkeleri gibi her zaman kullandığınız ilke KIMLIĞINI kullanın. Daha fazla bilgi için [bu makaleye](media-services-dotnet-manage-entities.md#limit-access-policies) bakın.

Bu makalede kullanılan AMS REST varlıkları hakkında daha fazla bilgi için bkz. [Azure Media Services REST API'si Reference](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Ayrıca bkz. [Azure Media Services kavramları](media-services-concepts.md).

>[!NOTE]
>Media Services varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Azure portal ile akış uç noktalarını başlatma

Azure Media Services ile çalışırken en sık karşılaşılan senaryolardan biri bit hızı uyarlamalı akış aracılığıyla video teslim edilir. Media Services, bu akış biçimlerinin her birinin önceden paketlenmiş sürümlerini depolamanıza gerek kalmadan, uyarlamalı bit hızı MP4 ile kodlanmış içeriğinizi Media Services tarafından desteklenen akış biçimlerinde (MPEG DASH, HLS, Kesintisiz Akış) tam vaktinde göndermenize olanak tanıyan dinamik paketleme özelliğine sahiptir.

>[!NOTE]
>AMS hesabınız oluşturulduğunda hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir.

Akış uç noktasını başlatmak için aşağıdakileri yapın:

1. [Azure portal](https://portal.azure.com/)’da oturum açın.
2. Ayarlar penceresinde, Akış uç noktaları'na tıklayın.
3. Varsayılan akış uç noktasına tıklayın.

    VARSAYILAN AKIŞ UÇ NOKTASI AYRINTILARI penceresi görüntülenir.

4. Başlat simgesine tıklayın.
5. Yaptığınız değişiklikleri kaydetmek için Kaydet düğmesine tıklayın.

## <a id="connect"></a>REST API Media Services hesabına bağlanma

AMS API 'sine bağlanma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Yeni bir varlık oluşturun ve REST API bir video dosyası yükleyin

Media Services’de dijital dosyalar bir varlığa yüklenir. Varlık varlığı video, ses, görüntüler, küçük resim koleksiyonları, metin parçaları ve kapalı açıklamalı alt yazı dosyaları (ve bu dosyalar hakkındaki meta veriler) içerebilir.  Dosyalar varlığa yüklendikten sonra, içeriğiniz daha fazla işlem ve akış için bulutta güvenli bir şekilde depolanır.

Bir varlık oluştururken sağlamanız gereken değerlerden biri varlık oluşturma seçenekleridir. **Options** özelliği, bir varlığın oluşturulabilen şifreleme seçeneklerini açıklayan bir sabit listesi değeridir. Geçerli bir değer, bu listedeki değerlerin birleşimini değil, aşağıdaki listedeki değerlerden biridir:

* Hiçbiri = **0** -şifreleme kullanılmaz. Bu seçeneği kullandığınızda, içeriğiniz aktarım sırasında veya depolamadaki bekleyen sırada korunmaz.
    Aşamalı indirme kullanarak bir MP4 iletmeyi planlıyorsanız bu seçeneği kullanın.
* Storageencryptıon 1-AES-256 bit şifrelemesini kullanarak açık içeriğinizi yerel olarak şifreler ve sonra da bu dosyayı Rest 'te şifreli olarak depolandığı Azure depolama 'ya yükler.  =  Depolama Şifrelemesi ile korunan varlıklar, kodlamadan önce otomatik olarak şifrelenerek şifrelenmiş bir dosya sistemine yerleştirilir ve yeni bir çıktı varlığı şeklinde geri yüklenmeden önce isteğe bağlı olarak yeniden şifrelenir. Depolama Şifrelemesinin birincil kullanım nedeni, yüksek kaliteli girdi medya dosyalarınızın güvenliğini güçlü şifrelemeyle diskte bekleyen konumda sağlamak istediğiniz durumdur.
* **CommonEncryptionProtected** = **2** -daha önce şifrelenen ve Common Encryption veya PlayReady DRM ile korunan içeriği (örneğin, PlayReady DRM ile korunan kesintisiz akış) karşıya yüklüyorsanız bu seçeneği kullanın.
* EnvelopeEncryptionProtected = **4** – HLS 'leri AES ile şifreli olarak karşıya yüklüyorsanız bu seçeneği kullanın. Dosyalar, Transform Manager tarafından kodlanmış ve şifrelenmiş olmalıdır.

### <a name="create-an-asset"></a>Varlık oluşturma
Varlık, video, ses, görüntüler, küçük resim koleksiyonları, metin parçaları ve kapalı açıklamalı altyazı dosyaları dahil olmak üzere Media Services birden çok tür veya nesne kümesi için bir kapsayıcıdır. REST API bir varlık oluşturmak için POST isteğinin Media Services gönderilmesi ve istek gövdesine varlığınızın herhangi bir özellik bilgisini yerleştirilmesi gerekir.

Aşağıdaki örnek, bir varlığın nasıl oluşturulacağını göstermektedir.

**HTTP Isteği**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**HTTP yanıtı**

Başarılı olursa aşağıdakiler döndürülür:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Assetdosyası oluşturma
[Assetfile](https://docs.microsoft.com/rest/api/media/operations/assetfile) varlığı bir blob kapsayıcısında depolanan bir videoyu veya ses dosyasını temsil eder. Bir varlık dosyası her zaman bir varlıkla ilişkilendirilir ve bir varlık bir veya daha fazla Assetdosyası içerebilir. Bir varlık dosya nesnesi bir blob kapsayıcısındaki dijital dosyayla ilişkilendirilmediğinde Media Services kodlayıcı görevi başarısız olur.

Dijital medya dosyanızı bir blob kapsayıcısına yükledikten sonra, Assetdosyasını medya dosyanız hakkındaki bilgilerle (konunun ilerleyen kısımlarında gösterildiği gibi) güncellemek için **merge** http isteğini kullanırsınız.

**HTTP Isteği**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP yanıtı**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Yazma izniyle AccessPolicy oluşturma
Blob depolamaya herhangi bir dosyayı yüklemeden önce, bir varlığa yazma için erişim ilkesi haklarını ayarlayın. Bunu yapmak için AccessPolicies varlık kümesine bir HTTP isteği GÖNDERIN. Oluşturma sonrasında bir DurationInMinutes değeri tanımlayın veya yanıt olarak 500 Iç sunucu hatası iletisi alırsınız. AccessPolicies hakkında daha fazla bilgi için bkz. [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

Aşağıdaki örnek, bir AccessPolicy nasıl oluşturulacağını gösterir:

**HTTP Isteği**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**HTTP yanıtı**

Başarılı olursa, aşağıdaki yanıt döndürülür:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Karşıya yükleme URL 'sini alın

Gerçek karşıya yükleme URL 'sini almak için SAS Bulucu oluşturun. Konumlandırıcı, bir varlıktaki dosyalara erişmek isteyen istemciler için başlangıç saatini ve bağlantı uç noktası türünü tanımlar. Farklı istemci isteklerini ve ihtiyaçlarını işlemek için, belirli bir AccessPolicy ve varlık çifti için birden çok Konumlandırıcı varlığı oluşturabilirsiniz. Bu belirleyicilerinin her biri, bir URL 'nin kullanılabileceği sürenin uzunluğunu öğrenmek için StartTime değerini ve AccessPolicy DurationInMinutes değerini kullanır. Daha fazla bilgi için bkz. [Locator](https://docs.microsoft.com/rest/api/media/operations/locator).

SAS URL 'SI aşağıdaki biçime sahiptir:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Bazı dikkate alınması gereken noktalar vardır:

* Belirli bir varlıkla ilişkilendirilmiş beş taneden fazla benzersiz Konum Belirleyicisi olamaz. 
* Dosyalarınızı hemen karşıya yüklemeniz gerekiyorsa, StartTime değerini geçerli zamandan beş dakika önce ayarlamanız gerekir. Bunun nedeni, istemci makineniz ve Media Services arasında saat çarpıklığı olabilir. Ayrıca, StartTime değeri aşağıdaki tarih saat biçiminde olmalıdır: YYYY-MM-DDTHH: mm: ssZ (örneğin, "2014-05-23T17:53:50Z").    
* Bir bulucunun kullanım için kullanılabilir olduğu durumlarda, bir bulucu oluşturulduktan sonra 30-40 saniyelik bir gecikme olabilir. Bu sorun hem [SAS URL 'si](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) hem de Kaynak Konum Belirleyicileri için geçerlidir.

Aşağıdaki örnek, bir SAS URL Konumlandırıcısı 'nın, istek gövdesinde (SAS Konumlandırıcı için "1" ve Isteğe bağlı bir kaynak bulucu için "2") tanımlanan tür özelliği tarafından tanımlandığı şekilde nasıl oluşturulacağını gösterir. Döndürülen **Path** özelliği, dosyanızı karşıya yüklemek için kullanmanız gereken URL 'yi içerir.

**HTTP Isteği**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP yanıtı**

Başarılı olursa, aşağıdaki yanıt döndürülür:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>BLOB depolama kapsayıcısına dosya yükleme
AccessPolicy ve Locator kümesine sahip olduğunuzda, gerçek dosya Azure depolama REST API 'Leri kullanılarak bir Azure Blob depolama kapsayıcısına yüklenir. Dosyaları blok Blobları olarak yüklemeniz gerekir. Sayfa Blobları Azure Media Services tarafından desteklenmez.  

> [!NOTE]
> Karşıya yüklemek istediğiniz dosyanın adını, önceki bölümde alınan Konumlandırıcı **yolu** değerine eklemeniz gerekir. Örneğin: `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Azure depolama Blobları ile çalışma hakkında daha fazla bilgi için bkz. [BLOB hizmeti REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Assetdosyasını güncelleştirme
Dosyanızı karşıya yüklediğinize göre, Filevarlık boyutu (ve diğer) bilgilerini güncelleştirin. Örneğin:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP yanıtı**

Başarılı olursa aşağıdakiler döndürülür:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Konumlandırıcı ve AccessPolicy silme
**HTTP Isteği**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP yanıtı**

Başarılı olursa aşağıdakiler döndürülür:

    HTTP/1.1 204 No Content
    ...

**HTTP Isteği**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP yanıtı**

Başarılı olursa aşağıdakiler döndürülür:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Kaynak dosyayı bir uyarlamalı bit hızı MP4 dosyaları kümesine kodlayın

Varlıkları Media Services 'e gönderdikten sonra medya, istemcilere teslim edilmeden önce kodlanmış, transmuxed, su olarak işaretlenmiş ve benzeri olabilir. Bu etkinlikler, yüksek performans ve kullanılabilirlik sağlamak için birden fazla arka plan rol örneğinde zamanlanır ve çalıştırılır. Bu etkinliklere Işler adı verilir ve her bir Iş, varlık dosyası üzerinde asıl işi yapan atomik görevlerden oluşur (daha fazla bilgi için, bkz. [iş](https://docs.microsoft.com/rest/api/media/operations/job), [görev](https://docs.microsoft.com/rest/api/media/operations/task) açıklamaları).

Daha önce belirtildiği gibi, en yaygın senaryolarından biri Azure Media Services ile çalışırken, istemcilerinize bit hızı uyarlamalı akış teslim edilir. Media Services bir uyarlamalı bit hızı MP4 dosyası kümesini dinamik olarak şu biçimlerden birine paketleyebilir: HTTP Canlı Akışı (HLS), Kesintisiz Akış, MPEG DASH.

Aşağıdaki bölümde, bir kodlama görevi içeren bir iş oluşturma işlemi gösterilmektedir. Görev, **Media Encoder Standard**kullanarak Mezzanine dosyasının bir uyarlamalı bit hızı kümesine dönüştürülmesini belirtir. Bu bölümde ayrıca iş işleme ilerleme durumunun nasıl izleneceği gösterilmektedir. İş tamamlandığında, varlıklarınıza erişim sağlamak için gereken konum belirleyicilerinin oluşturulması mümkün olacaktır.

### <a name="get-a-media-processor"></a>Medya işlemcisi al
Media Services, medya işlemcisi kodlama, biçim dönüştürme, şifreleme veya medya içeriğini çözme gibi belirli bir işleme görevini işleyen bir bileşendir. Bu öğreticide gösterilen kodlama görevi için Media Encoder Standard kullanacağız.

Aşağıdaki kod, kodlayıcının kimliğini ister.

**HTTP Isteği**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP yanıtı**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>İş oluştur
Her Işin, gerçekleştirmek istediğiniz işleme türüne bağlı olarak bir veya daha fazla görevi olabilir. REST API aracılığıyla Işleri ve ilgili görevlerini iki şekilde oluşturabilirsiniz: Görevler, Iş varlıklarındaki görevler gezintisi özelliği veya OData toplu işleme aracılığıyla satır içi olarak tanımlanabilir. Media Services SDK toplu işlemeyi kullanır. Ancak, bu makaledeki kod örneklerinin okunabilirliğini için görevler satır içi olarak tanımlanmıştır. Toplu işleme hakkında daha fazla bilgi için bkz. [Açık Veri Protokolü (OData) toplu işleme](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Aşağıdaki örnek, belirli bir çözünürlükte ve kalitede video kodlamak için bir görev ayarlama ile bir Işi nasıl oluşturup nakledeceğiniz gösterilmektedir. Aşağıdaki belgeler bölümü, Media Encoder Standard işlemcisi tarafından desteklenen tüm [görev önayarlarının](https://msdn.microsoft.com/library/mt269960) listesini içerir.  

**HTTP Isteği**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP yanıtı**

Başarılı olursa, aşağıdaki yanıt döndürülür:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Herhangi bir Iş isteğinde dikkat etmeniz gereken birkaç önemli nokta vardır:

* TaskBody özellikleri, giriş sayısını veya görev tarafından kullanılan çıkış varlıklarını tanımlamak için değişmez XML kullanmalıdır. Görev makalesi XML için XML şema tanımı içerir.
* TaskBody tanımında, ve `<inputAsset>` `<outputAsset>` için her bir iç değerin jobınputasset (değer) veya Joi putasset (değer) olarak ayarlanması gerekir.
* Bir görevde birden fazla çıkış varlığı olabilir. Bir joi Putasset (x), bir iş içindeki görevin çıktısı olarak yalnızca bir kez kullanılabilir.
* Jobınputasset veya Joi Putasset değerini bir görevin giriş varlığı olarak belirtebilirsiniz.
* Görevler bir bisiklet oluşturmamalıdır.
* Jobınputasset veya Joi Putasset 'e geçirdiğiniz değer parametresi bir varlık için dizin değerini temsil eder. Gerçek varlıklar, Iş varlığı tanımındaki ınputmediavarlıklar ve Outputmediavarlıklarının gezinti özelliklerinde tanımlanmıştır.

> [!NOTE]
> Media Services OData v3 üzerinde oluşturulduğundan, ınputmediavarlıklarının ve Outputmediavarlıklarının gezinti özelliği koleksiyonlarındaki tek tek varlıklar "__metadata: Uri" ad-değer çifti üzerinden başvuruluyor.
>
>

* Inputmediavarlıkların, Media Services oluşturduğunuz bir veya daha fazla varlık ile eşlenir. Outputmediavarlıklar sistem tarafından oluşturulur. Mevcut bir varlığa başvurmazlar.
* Outputmediavarlıklarının adı assetName özniteliği kullanılarak yapılabilir. Bu öznitelik yoksa, outputmediakıymetin adı, `<outputAsset>` öğenin iç metin değerinin, iş adı değeri veya iş kimliği değeri (ad özelliğinin tanımlı olmadığı durumlarda) bir sonekine sahip olduğu her şey olur. Örneğin, assetName için bir değeri "Sample" olarak ayarlarsanız, Outputmediavarlık Name özelliği "Sample" olarak ayarlanır. Ancak, assetName için bir değer belirtmediyseniz ancak iş adını "NewJob" olarak ayarlarsanız, Outputmediavarlık adı "Joi Putasset (value) _NewJob" olacaktır.

    Aşağıdaki örnekte, assetName özniteliğinin nasıl ayarlanacağı gösterilmektedir:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Görev zincirlemesini etkinleştirmek için:

  * Bir işin en az iki görevi olmalıdır
  * Girişi işteki başka bir görevin çıktısı olan en az bir görev olmalıdır.

Daha fazla bilgi için, [Media Services REST API bir kodlama Işi oluşturma](media-services-rest-encode-asset.md)konusuna bakın.

### <a name="monitor-processing-progress"></a>İzleme Işlemi Ilerleme durumu
Aşağıdaki örnekte gösterildiği gibi, durum özelliğini kullanarak Iş durumunu elde edebilirsiniz:

**HTTP Isteği**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP yanıtı**

Başarılı olursa, aşağıdaki yanıt döndürülür:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>İşi iptal et
Media Services, çalışan işleri CancelJob işlevi aracılığıyla iptal etmenizi sağlar. Bu çağrı, durumu iptal edildiğinde, iptal edildiğinde, hatada veya tamamlandığında bir Işi iptal etmeyi denerseniz bir 400 hata kodu döndürür.

Aşağıdaki örnek, CancelJob çağrısının nasıl çağrılacağını gösterir.

**HTTP Isteği**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Başarılı olursa, ileti gövdesi olmadan 204 yanıt kodu döndürülür.

> [!NOTE]
> İşi CancelJob 'a bir parametre olarak geçirirken iş kimliğini (normalde NB: JID: UUID: someValue) kodlamanız gerekir.
>
>

### <a name="get-the-output-asset"></a>Çıkış varlığını al
Aşağıdaki kod, çıkış varlığı kimliğini nasıl isteyeceğini gösterir.

**HTTP Isteği**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP yanıtı**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>REST API ile varlığı yayımlayın ve akış ve aşamalı indirme URL 'Lerini alın

Bir varlığı akışla aktarmak veya indirmek için söz konusu varlığı önce bir bulucu oluşturarak “yayımlamak” gerekir. Bulucular varlıkta bulunan dosyalara erişim imkanı sağlar. Media Services iki tür bulucuyu destekler: Medya dosyalarını indirmek için kullanılan, medya akışını (örneğin, MPEG DASH, HLS veya Kesintisiz Akış) ve erişim Imzası (SAS) Konumlandırıcı için kullanılan OnDemandOrigin Konumlandırıcı. 

Bulıcıları oluşturduktan sonra, dosyalarınızı akışa almak veya indirmek için kullanılan URL 'Leri oluşturabilirsiniz.

>[!NOTE]
>AMS hesabınız oluşturulduğunda hesabınıza **Durdurulmuş** durumda bir **varsayılan** akış uç noktası eklenir. İçerik akışını başlatmak ve dinamik paketleme ile dinamik şifrelemeden yararlanmak için içerik akışı yapmak istediğiniz akış uç noktasının **Çalışıyor** durumda olması gerekir.

Kesintisiz Akışa ilişkin bir akış URL'si aşağıdaki biçime sahiptir:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS’ye ilişkin bir akış URL'si aşağıdaki biçime sahiptir:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH’e ilişkin bir akış URL'si aşağıdaki biçime sahiptir:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Dosya indirmek için kullanılan bir SAS URL'si aşağıdaki biçime sahiptir:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Bu bölümde, varlıklarınızı "yayımlamak" için gerekli olan aşağıdaki görevlerin nasıl gerçekleştirileceği gösterilmektedir.  

* Okuma izniyle AccessPolicy oluşturma
* İçerik indirmek için SAS URL 'SI oluşturma
* Akış içeriği için kaynak URL 'SI oluşturma

### <a name="creating-the-accesspolicy-with-read-permission"></a>Okuma izniyle AccessPolicy oluşturma
Herhangi bir medya içeriğini indirmeden veya akışa almadan önce, okuma izinleriyle bir AccessPolicy tanımlayın ve istemcileriniz için etkinleştirmek istediğiniz teslim mekanizması türünü belirten uygun Bulucu varlığını oluşturun. Kullanılabilen özellikler hakkında daha fazla bilgi için bkz. [AccessPolicy Entity Properties](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Aşağıdaki örnek, belirli bir varlık için AccessPolicy for Read izinlerinin nasıl ekleneceğini gösterir.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Başarılı olursa, oluşturduğunuz AccessPolicy varlığını açıklayan bir 201 başarılı kodu döndürülür. Daha sonra AccessPolicy ID 'yi, Konumlandırıcı varlığını oluşturmak için teslim etmek istediğiniz dosyayı (örneğin, bir çıktı varlığı) içeren varlığın varlık kimliğiyle birlikte kullanırsınız.

> [!NOTE]
> Bu temel iş akışı, bir varlığı alırken (Bu konunun önceki kısımlarında anlatıldığı gibi) bir dosyayı karşıya yüklerken de aynıdır. Ayrıca, dosyaları karşıya yükleme gibi (veya istemcileriniz) dosyalarınıza hemen erişmeniz gerekiyorsa, StartTime değerini geçerli zamandan beş dakika önce olarak ayarlayın. Bu eylem, istemci ve Media Services arasında saat çarpıklığı olabileceğinden gereklidir. StartTime değeri şu tarih saat biçiminde olmalıdır: YYYY-MM-DDTHH: mm: ssZ (örneğin, "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>İçerik indirmek için SAS URL 'SI oluşturma
Aşağıdaki kod, daha önce oluşturulup karşıya yüklenen bir medya dosyasını indirmek için kullanılabilecek bir URL 'nin nasıl alınacağını gösterir. AccessPolicy okuma izinlerine sahiptir ve Konumlandırıcı yolu bir SAS indirme URL 'sine başvurur.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Başarılı olursa, aşağıdaki yanıt döndürülür:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

Döndürülen **yol** özelliği SAS URL 'sini içerir.

> [!NOTE]
> Depolama şifrelenmiş içeriği indirdiğinizde, işlemeden önce el ile şifresini çözmeniz veya bir işleme görevinde depolama şifre çözme MediaProcessor ' ı kullanarak, bir OutputAsset içinde işlenen dosyaların çıkışını kaldırın ve ardından bu varlıktan indirin. İşleme hakkında daha fazla bilgi için, Media Services REST API bir kodlama Işi oluşturma konusuna bakın. Ayrıca, SAS URL Konumlandırıcı oluşturulduktan sonra güncelleştirilemez. Örneğin, güncelleştirilmiş bir StartTime değeri ile aynı Konumlandırıcı 'yı yeniden kullanamazsınız. Bunun nedeni SAS URL 'Lerinin oluşturulma yöntemidir. Bir bulucunun süresi dolduktan sonra bir varlığa indirme için erişmek istiyorsanız yeni bir StartTime ile yeni bir başlangıç oluşturmanız gerekir.
>
>

### <a name="download-files"></a>Dosyaları indirme
AccessPolicy ve Locator kümesini aldıktan sonra Azure depolama REST API 'Lerini kullanarak dosyaları indirebilirsiniz.  

> [!NOTE]
> İndirmek istediğiniz dosyanın adını, önceki bölümde alınan Konumlandırıcı **yolu** değerine eklemeniz gerekir. Örneğin, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Azure depolama Blobları ile çalışma hakkında daha fazla bilgi için bkz. [BLOB hizmeti REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Daha önce gerçekleştirdiğiniz kodlama işinin bir sonucu olarak (Uyarlamalı MP4 kümesine kodlama), aşamalı olarak indirebileceğiniz birden fazla MP4 dosyasına sahip olursunuz. Örneğin:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Akış içeriği için akış URL 'SI oluşturma
Aşağıdaki kod, akış URL Konumlandırıcısı 'nın nasıl oluşturulacağını gösterir:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Başarılı olursa, aşağıdaki yanıt döndürülür:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Bir akış medya yürütücüsünde Kesintisiz Akış kaynak URL 'sini akışa almak için, Path özelliğini Kesintisiz Akış bildirim dosyasının adı ile ve ardından "/manifest" ile eklemeniz gerekir.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS akışı için, "/manifest" öğesinden sonra Append (format = M3U8-AAPL).

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH akışını sağlamak için, "/manifest" öğesinden sonra (format = MPD-Time-CSF) ekleyin.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>İçeriğinizi yürütün
Videonuzu akışla aktarmak için [Azure Media Services Oynatıcısı](https://aka.ms/azuremediaplayer)’nı kullanın.

Aşamalı indirmeyi test etmek için bir tarayıcıya URL (örneğin, IE, Chrome, Safari) yapıştırın.

## <a name="next-steps-media-services-learning-paths"></a>Sonraki Adımlar: Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geri bildirimde bulunma
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
