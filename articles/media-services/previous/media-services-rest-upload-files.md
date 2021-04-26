---
title: REST kullanarak bir Azure Media Services hesabına dosya yükleme | Microsoft Docs
description: REST kullanarak varlık oluşturup karşıya yükleyerek Media Services medya içeriğini nasıl alabileceğinizi öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 9f27a427df07302840ce719d35c7876f9dc17dbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012947"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>REST kullanarak Media Services hesabına dosya yükleme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Media Services’de dijital dosyalar bir varlığa yüklenir. Varlık [varlığı video](/rest/api/media/operations/asset) , ses, görüntüler, küçük resim koleksiyonları, metin parçaları ve kapalı açıklamalı alt yazı dosyaları (ve bu dosyalar hakkındaki meta veriler) içerebilir.  Dosyalar varlığa yüklendikten sonra, içeriğiniz daha fazla işlem ve akış için bulutta güvenli bir şekilde depolanır. 

Bu öğreticide, bir dosya ve onunla ilişkili başka bir işlemi karşıya yüklemeyi öğreneceksiniz:

> [!div class="checklist"]
> * Tüm karşıya yükleme işlemleri için Postman ayarla
> * Media Services’e bağlanmak 
> * Yazma izniyle bir erişim ilkesi oluşturma
> * Varlık oluşturma
> * SAS Bulucu oluşturma ve karşıya yükleme URL 'sini oluşturma
> * Karşıya yükleme URL 'sini kullanarak blob depolamaya bir dosya yükleme
> * Karşıya yüklediğiniz medya dosyası için varlık içinde meta veri oluşturma

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
- [Azure Portal kullanarak bir Azure Media Services hesabı oluşturun](media-services-portal-create-account.md).
- [AAD kimlik doğrulamasına genel bakış makalesini Azure Media Services API 'sine](media-services-use-aad-auth-to-access-ams-api.md) göz atın.
- Ayrıca, [rest makalesine MEDIA SERVICES API 'sine erişmek Için Azure AD kimlik doğrulamasını kullanma](./media-services-rest-connect-with-aad.md) konusunu gözden geçirin.
- [Media Services REST API çağrılarında Postman yapılandırma](media-rest-apis-with-postman.md)bölümünde açıklandığı gibi **Postman** 'ı yapılandırın.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Media Services REST API kullanılırken aşağıdaki noktalar geçerlidir:
 
* Media Services REST API kullanarak varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md). <br/>Bu öğreticide kullanılan Postman koleksiyonu tüm gerekli üst bilgileri ayarlamayı dikkate alır.
* Media Services, akış içeriği için URL 'Ler oluştururken IAssetFile.Name özelliğinin değerini kullanır (örneğin, http://{AMSAccount}. Origin. mediaservices. Windows. net/{GUID}/{ıassetfile. Name}/streamingParameters.) Bu nedenle, yüzde kodlamalı izin verilmez. **Name** özelliğinin değeri, Şu sayıda [kodlamaya ayrılan karakterlerden](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)herhangi birini içeremez:! * ' ();: @ &= + $,/?% # [] ". Ayrıca, dosya adı uzantısı için yalnızca bir '. ' olabilir.
* Adın uzunluğu 260 karakterden büyük olmamalıdır.
* Media Services ile işleme için desteklenen dosya boyutlarına yönelik üst sınır uygulanır. Dosya boyutu sınırlaması hakkında ayrıntılı bilgi için [bu](media-services-quotas-and-limitations.md) makaleye bakın.

## <a name="set-up-postman"></a>Postman’i ayarlama

Bu öğretici için Postman 'ı ayarlama adımları için bkz. [Postman 'ı yapılandırma](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Media Services’e bağlanmak

1. Ortamınıza bağlantı değerleri ekleyin. 

    **Mediaservices** [ortamının](postman-environment.md) bir parçası olan bazı değişkenlerin, [koleksiyonda](postman-collection.md)tanımlanan işlemleri yürütmeye başlayabilmeniz için el ile ayarlanması gerekir.

    İlk beş değişkenin değerlerini almak için bkz. [Azure AD kimlik doğrulamasıyla Azure Media Services API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Sağ üst kısımdaki "COG" simgesini ve "Yönetim ortamları" sekmesinden seçilen ilk beş değişkeni gösteren ekran görüntüsü.](./media/media-services-rest-upload-files/postman-import-env.png)
2. **Mediafilename** ortam değişkeni için değeri belirtin.

    Karşıya yüklemeyi planladığınız medyanın dosya adını belirtin. Bu örnekte, BigBuckBunny.mp4 yükleyeceğiz. 
3. Dosyadaki **AzureMediaServices.postman_environment.js** inceleyin. Koleksiyondaki neredeyse tüm işlemlerin bir "test" betiği yürütmesini görürsünüz. Betikler yanıt tarafından döndürülen bazı değerleri alır ve uygun ortam değişkenlerini ayarlar.

    Örneğin, ilk işlem bir erişim belirteci alır ve diğer tüm işlemlerde kullanılan **accesstoken** ortam değişkeninde ayarlanır.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. **Postman** penceresinin sol tarafında 1 ' e tıklayın **. AAD kimlik doğrulama belirteci al**  ->  **hizmet sorumlusu için Azure AD belirteci alın**.

    URL bölümü **Azureadstsendpoint** ortam değişkeniyle doldurulmuştur (öğreticide daha önce, koleksiyonu destekleyen ortam değişkenlerinin değerlerini ayarlarsınız).

    !["1" gösteren ekran görüntüsü. D kimlik doğrulama belirteci alın-"Postman" penceresinden ve "Gönder" düğmesine seçili bir hizmet sorumlusu için Azure A D belirteci alın.](./media/media-services-rest-upload-files/postment-get-token.png)

5. **Gönder**’e basın.

    "Access_token" içeren yanıtı görebilirsiniz. "Test" betiği bu değeri alır ve **accesstoken** ortam değişkenini (yukarıda açıklandığı gibi) ayarlar. Ortam değişkenlerinizi incelerseniz, bu değişkenin artık işlemlerin geri kalanında kullanılan erişim belirteci (taşıyıcı belirteci) değerini içerdiğini görürsünüz. 

    Belirtecin süresi dolarsa "hizmet sorumlusu için Azure AD belirteci al" adımını yeniden deneyin. 

## <a name="create-an-access-policy-with-write-permission"></a>Yazma izniyle bir erişim ilkesi oluşturma

### <a name="overview"></a>Genel Bakış 

>[!NOTE]
>Farklı AMS ilkeleri için sınır 1.000.000 ilkedir (örneğin, Bulucu ilkesi veya ContentKeyAuthorizationPolicy için). Uzun süre boyunca kullanılmak için oluşturulan bulucu ilkeleri gibi aynı günleri / erişim izinlerini sürekli olarak kullanıyorsanız, aynı ilke kimliğini kullanmalısınız (karşıya yükleme olmayan ilkeler için). Daha fazla bilgi için [Bu](media-services-dotnet-manage-entities.md#limit-access-policies) makaleye bakın.

Blob depolamaya herhangi bir dosyayı yüklemeden önce, bir varlığa yazma için erişim ilkesi haklarını ayarlayın. Bunu yapmak için AccessPolicies varlık kümesine bir HTTP isteği GÖNDERIN. Oluşturma sonrasında bir DurationInMinutes değeri tanımlayın veya yanıt olarak 500 Iç sunucu hatası iletisi alırsınız. AccessPolicies hakkında daha fazla bilgi için bkz. [AccessPolicy](/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Erişim ilkesi oluşturma

1.   ->  **Karşıya yüklemek için AccessPolicy oluştur AccessPolicy** öğesini seçin.
2. **Gönder**’e basın.

    ![Sol taraftaki menüden ve "Gönder" düğmesinin seçili olduğu "AccessPolicy-Create AccessPolicy for upload" seçeneğini gösteren ekran görüntüsü.](./media/media-services-rest-upload-files/postman-access-policy.png)

    "Test" betiği AccessPolicy kimliğini alır ve uygun ortam değişkenini ayarlar.

## <a name="create-an-asset"></a>Varlık oluşturma

### <a name="overview"></a>Genel Bakış

[Varlık](/rest/api/media/operations/asset) , video, ses, görüntüler, küçük resim koleksiyonları, metin parçaları ve kapalı açıklamalı altyazı dosyaları dahil olmak üzere Media Services birden çok tür veya nesne kümesi için bir kapsayıcıdır. REST API bir varlık oluşturmak için POST isteğinin Media Services gönderilmesi ve istek gövdesine varlığınızın herhangi bir özellik bilgisini yerleştirilmesi gerekir.

Bir varlık oluştururken ekleyebileceğiniz özelliklerden biri **Seçenekler**. Şu şifreleme seçeneklerinden birini belirtebilirsiniz: **hiçbiri** (varsayılan, şifreleme yok), **storageşifrelenmiştir** (istemci tarafı depolama şifrelemesi ile önceden şifrelenmiş Içerik Için), **CommonEncryptionProtected** veya **EnvelopeEncryptionProtected**. Şifrelenmiş bir varlığınız olduğunda, bir teslim İlkesi yapılandırmanız gerekir. Daha fazla bilgi için bkz. [varlık teslim Ilkelerini yapılandırma](media-services-rest-configure-asset-delivery-policy.md).

Varlığınız şifrelendiyse, bir **contentkey** oluşturmanız ve bunu aşağıdaki makalede açıklandığı gibi varlığınızla bağlamanız gerekir: [nasıl bir contentkey oluşturulur](media-services-rest-create-contentkey.md). Dosyaları varlığa yükledikten sonra, **Assetfile** varlığındaki şifreleme özelliklerini **varlık** şifrelemesi sırasında aldığınız değerlerle güncelleştirmeniz gerekir. Bunu, **birleştirme** http isteğini kullanarak yapın. 

Bu örnekte, şifrelenmemiş bir varlık oluşturuyoruz. 

### <a name="create-an-asset"></a>Varlık oluşturma

1.   ->  **Varlık oluştur varlık**' ı seçin.
2. **Gönder**’e basın.

    !["Koleksiyonlar" menüsünden Seçili "varlıklar-varlık oluştur" u ve "Gönder" düğmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/media-services-rest-upload-files/postman-create-asset.png)

    "Test" betiği varlık kimliğini alır ve uygun ortam değişkenini ayarlar.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>SAS Bulucu oluşturma ve karşıya yükleme URL 'sini oluşturma

### <a name="overview"></a>Genel Bakış

AccessPolicy ve Locator kümesine sahip olduğunuzda, gerçek dosya Azure depolama REST API 'Leri kullanılarak bir Azure Blob depolama kapsayıcısına yüklenir. Dosyaları blok Blobları olarak yüklemeniz gerekir. Sayfa Blobları Azure Media Services tarafından desteklenmez.  

Azure depolama Blobları ile çalışma hakkında daha fazla bilgi için bkz. [BLOB hizmeti REST API](/rest/api/storageservices/blob-service-rest-api).

Gerçek karşıya yükleme URL 'sini almak için SAS Konumlandırıcı (aşağıda gösterilmiştir) oluşturun. Konumlandırıcı, bir varlıktaki dosyalara erişmek isteyen istemciler için başlangıç saatini ve bağlantı uç noktası türünü tanımlar. Farklı istemci isteklerini ve ihtiyaçlarını işlemek için, belirli bir AccessPolicy ve varlık çifti için birden çok Konumlandırıcı varlığı oluşturabilirsiniz. Bu belirleyicilerinin her biri, bir URL 'nin kullanılabileceği sürenin uzunluğunu öğrenmek için StartTime değerini ve AccessPolicy DurationInMinutes değerini kullanır. Daha fazla bilgi için bkz. [Locator](/rest/api/media/operations/locator).

SAS URL 'SI aşağıdaki biçime sahiptir:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Dikkat edilmesi gerekenler

Bazı dikkate alınması gereken noktalar vardır:

* Belirli bir varlıkla ilişkilendirilmiş beş taneden fazla benzersiz Konum Belirleyicisi olamaz. Daha fazla bilgi için bkz. Locator.
* Dosyalarınızı hemen karşıya yüklemeniz gerekiyorsa, StartTime değerini geçerli zamandan beş dakika önce ayarlamanız gerekir. Bunun nedeni, istemci makineniz ve Media Services arasında saat çarpıklığı olabilir. Ayrıca, StartTime değeri şu tarih saat biçiminde olmalıdır: YYYY-MM-DDTHH: mm: ssZ (örneğin, "2014-05-23T17:53:50Z").    
* Bir bulucunun kullanım için kullanılabilir olduğu durumlarda, bir bulucu oluşturulduktan sonra 30-40 saniyelik bir gecikme olabilir.

### <a name="create-a-sas-locator"></a>SAS Bulucu oluşturma

1. **Bulucu**  ->  **oluşturma SAS Bulucu** seçin.
2. **Gönder**’e basın.

    "Test" betiği, belirttiğiniz medya dosyası adına ve SAS Bulucu bilgilerine göre "karşıya yükleme URL 'sini" oluşturur ve uygun ortam değişkenini ayarlar.

    !["Koleksiyonlar" menüsünden ve "Gönder" düğmesinin seçili olduğu "Konumlandırıcı-s bir S bulucusu oluştur" öğesini gösteren ekran görüntüsü.](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Karşıya yükleme URL 'sini kullanarak blob depolamaya bir dosya yükleme

### <a name="overview"></a>Genel Bakış

Karşıya yükleme URL 'sine sahip olduğunuza göre, artık dosyanızı SAS kapsayıcısına yüklemek için Azure Blob API 'Lerini kullanarak bazı kodlar yazmanız gerekir. Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

- [Azure Depolama REST API’sini kullanma](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Blobu koy](/rest/api/storageservices/put-blob)
- [Blobları blob depolamaya yükleme](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Postman ile karşıya dosya yükleme

Örnek olarak, küçük bir. mp4 dosyasını karşıya yüklemek için Postman kullanıyoruz. Postman aracılığıyla ikili yükleme sırasında bir dosya boyutu sınırı olabilir.

Karşıya yükleme isteği **AzureMedia** koleksiyonunun bir parçası değil. 

Yeni bir istek oluşturun ve ayarlayın:
1. **+** Yeni bir istek sekmesi oluşturmak için ' a basın.
2. **{{Uploadurl}}** **öğesini seçerek URL** 'ye yapıştırın.
2. **Yetkilendirme** sekmesini olduğu gibi bırakın (bunu **taşıyıcı belirtece** ayarlamayın).
3. **Üstbilgiler** sekmesinde, şunu belirtin: **anahtar**: "x-MS-blob-türü" ve **değer**: "blockblob".
2. **Gövde** sekmesinde **ikili**' ya tıklayın.
4. **Mediafilename** ortam değişkeninde belirttiğiniz adı taşıyan dosyayı seçin.
5. **Gönder**’e basın.

    !["(UploadU R L)" sekmesinin seçili olduğunu gösteren ekran görüntüsü.](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Varlık içinde meta veri oluşturma

Dosya karşıya yüklendikten sonra, varlığınızdan ilişkili BLOB depolama alanına yüklediğiniz medya dosyası için varlık içinde bir meta veri oluşturmanız gerekir.

1. **Assetfiles**  ->  **createfileınfos** öğesini seçin.
2. **Gönder**’e basın.

    ![Dosyayı karşıya yükleme](./media/media-services-rest-upload-files/postman-create-file-info.png)

Dosyanın yüklenmesi ve meta verilerinin ayarlanmış olması gerekir.

## <a name="validate"></a>Doğrulama

Dosyanın başarıyla karşıya yüklendiğini doğrulamak için, [assetdosyasını](/rest/api/media/operations/assetfile) sorgulamak ve **contentfilesize** (veya diğer ayrıntılar) ' ı yeni varlık içinde görmeyi beklediğiniz gibi karşılaştırmak isteyebilirsiniz. 

Örneğin, aşağıdaki **Get** işlemi, varlık dosyanız için dosya verilerini (veya BigBuckBunny.mp4 dosyası) getirir. Sorgu daha önce ayarladığınız [ortam değişkenlerini](postman-environment.md) kullanıyor.

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

Yanıt boyut, ad ve diğer bilgileri içerir.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Sonraki adımlar

Karşıya yüklenen varlıklarınızı artık kodlayabilirsiniz. Daha fazla bilgi için bkz. [Varlıkları kodlama](media-services-portal-encode.md).

Yapılandırılmış kapsayıcıya gelen dosyaya göre bir kodlama işi tetiklemek için Azure İşlevleri’ni de kullanabilirsiniz. Daha fazla bilgi için [bu örneğe](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ) bakın.
