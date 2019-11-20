---
title: Bir kişi modelini özelleştirmek için Video Indexer API 'sini kullanma-Azure
titleSuffix: Azure Media Services
description: Bu makalede, bir kişi modelinin Video Indexer API ile nasıl özelleştirileceği gösterilmektedir.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 44f97e3d9af9daac8d62ae42be76bd73dedbd453
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838257"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Video Indexer API ile bir kişi modelini özelleştirme

Video Indexer, video içeriği için yüz algılamayı ve ünlüy tanımayı destekler. Ünlüçilerin tanınma özelliği, ıMDB, Viveze ve en popüler LinkedIn etkileyen, sık istenen veri kaynağına göre yaklaşık 1.000.000 yüz içerir. Ünlüğlik tanıma özelliği tarafından tanınmayan yüzler algılanır; Ancak, bunlar adlandırılmazlar. Video Indexer videoyu karşıya yükledikten ve sonuçları geri aldıktan sonra, geri dönüp tanınmamış yüzleri adı verebilirsiniz. Bir yüzü adı ile etiketledikten sonra, yüz ve ad hesabınızın kişi modeline eklenir. Video Indexer gelecekteki videolarınızdaki ve geçmiş videolarınızdaki bu yüzü tanıyacak.

Bu konuda açıklandığı gibi, videoda algılanan yüzeyleri düzenlemek için Video Indexer API 'sini kullanabilirsiniz. [Video Indexer Web sitesini kullanarak kişi modelini özelleştirme](customize-person-model-with-api.md)bölümünde açıklandığı gibi video Indexer Web sitesini de kullanabilirsiniz.

## <a name="managing-multiple-person-models"></a>Birden çok kişi modelini yönetme 

Video Indexer hesap başına birden çok kişi modelini destekler. Bu özellik şu anda yalnızca Video Indexer API 'Leri üzerinden kullanılabilir.

Hesabınız farklı kullanım örneği senaryolarına karşı, hesap başına birden fazla kişi modeli oluşturmak isteyebilirsiniz. Örneğin, içeriğiniz spor ile ilgiliyse, her spor (futbol, basketbol, futbol, vb.) için ayrı bir kişi modeli oluşturabilirsiniz. 

Bir model oluşturulduktan sonra, bir videoyu karşıya yüklerken/dizinleme yaparken veya yeniden dizinlerken belirli bir kişi modelinin model KIMLIĞINI sağlayarak onu kullanabilirsiniz. Video için yeni bir yüz eğitimi, videonun ilişkilendirildiği özel modeli günceller.

Her hesabın 50 kişi modeli sınırlaması vardır. Birden çok kişi modeli desteği gerekmiyorsa, karşıya yükleme/dizinleme veya yeniden dizin oluşturma sırasında videonuza bir kişi modeli KIMLIĞI atamayın. Bu durumda, Video Indexer hesabınızda varsayılan özel kişi modelini kullanır.

## <a name="create-a-new-person-model"></a>Yeni bir kişi modeli oluşturun

Belirtilen hesapta yeni bir kişi modeli oluşturun. 

### <a name="request-url"></a>İstek URL'si

Bu bir POST isteğidir.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Aşağıda, istek kıvrımlı.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>İstek parametreleri 

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|ad|string|Evet|Kişi modelinin adı|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu çağrı için gereken başka bir istek gövdesi yok.

### <a name="response"></a>Yanıt

Yanıt, aşağıdaki örnek biçimini izleyerek yeni oluşturduğunuz kişi modelinin adı ve oluşturulan model KIMLIĞINI sağlar.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Daha sonra, bir videoyu dizine veya [yeniden dizin oluşturmaya](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) [bir video yüklerken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) , **personmodelıd** parametresinin **kimlik** değerini kullanmanız gerekir.

## <a name="delete-a-person-model"></a>Kişi modelini silme

Belirtilen hesaptan özel bir kişi modelini silin. 

Kişi modeli başarıyla silindikten sonra, silinen modeli kullanan geçerli videolarınızın dizini yeniden dizinleyene kadar değişmeden kalır. Yeniden dizin oluşturma sırasında, silinen modelde adlandırılmış yüzler, geçerli videolarınızdaki Video Indexer tarafından tanınmaz; bu model kullanılarak dizine alınmış. Ancak, bu yüzler yine de algılanacaktır. Silinen model kullanılarak dizini oluşturulmuş geçerli videolarınız artık hesabınızın varsayılan kişi modelini kullanacaktır. Silinen modeldeki yüzler aynı zamanda hesabınızın varsayılan modelinde da adlandırılmışsa, bu yüzlerin videolarda tanınabilmesi devam edecektir.

### <a name="request-url"></a>İstek URL'si

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Aşağıda, istek kıvrımlı.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|id|string|Evet|Kişi modeli kimliği (kişi modeli oluşturulduğunda oluşturulur)|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu çağrı için gereken başka bir istek gövdesi yok.

### <a name="response"></a>Yanıt

Kişi modeli başarıyla silindiğinde döndürülen içerik yok.

## <a name="get-all-person-models"></a>Tüm kişi modellerini al

Belirtilen hesaptaki tüm kişi modellerini al. 

### <a name="request-call"></a>İstek çağrısı

Bu bir GET isteğidir.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Aşağıda, istek kıvrımlı.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|

### <a name="request-body"></a>İstek gövdesi

Bu çağrı için gereken başka bir istek gövdesi yok.

### <a name="response"></a>Yanıt

Yanıt, hesabınızdaki tüm kişi modellerinin (belirtilen hesaptaki varsayılan kişi modeli dahil) ve adlarının ve kimliklerinin her birinin aşağıdaki örnekte yer aldığı bir listesini sağlar.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Videoyu dizine ekleme veya [yeniden dizin oluşturma](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) [amacıyla bir video yüklerken](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) , **Personmodelıd** parametresi için kişi modelinin **kimlik** değerini kullanarak bir video için kullanmak istediğiniz modeli seçebilirsiniz.

## <a name="update-a-face"></a>Yüz güncelleştirme

Bu komut, videonun KIMLIĞINI ve yüzün kimliğini kullanarak Videonuzdaki bir yüzü bir ad ile güncelleştirmenize olanak tanır. Bu, daha sonra videonun karşıya yükleme/dizinleme veya yeniden dizin oluşturma sırasında ilişkilendirildiği kişi modelini güncelleştirir. Hiçbir kişi modeli atanmamışsa, hesabın varsayılan kişi modelini güncelleştirir. 

Bu durumda, aynı yüzün aynı yüzü, aynı kişi modelini paylaşan diğer geçerli videolarınızdaki tekrarlarını tanır. Diğer geçerli videolarınızdaki yüzün tanınabilmesi, bu bir toplu işlem olduğu için biraz zaman alabilir.

Ünlüklik olarak tanınan Video Indexer bir yüzü yeni bir adla güncelleştirebilirsiniz. Verdiğiniz yeni ad, yerleşik ünlüğünün tanınmasına göre öncelikli olacaktır.

### <a name="request-call"></a>İstek çağrısı

Bu bir POST isteğidir.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Aşağıda, istek kıvrımlı.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Video Indexer geliştirici portalını kullanarak gerekli parametrelere bakın ve test edin](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>İstek parametreleri

|**Ad**|**Tür**|**Gerekli**|**Açıklama**|
|---|---|---|---|
|location|string|Evet|Çağrının yönlendirileceği Azure bölgesi. Daha fazla bilgi için bkz. [Azure bölgeleri ve video Indexer](regions.md).|
|Accoun|string|Evet|Hesap için genel benzersiz tanımlayıcı|
|VideoID|string|Evet|Güncelleştirmek istediğiniz yüzün göründüğü videonun kimliği. Bu, video karşıya yüklendiğinde ve dizine eklendiğinde oluşturulur.|
|FaceID|integer|Evet|Güncellenen yüzün kimliği. Video dizininden çok yönlü kimliği alabilirsiniz|
|accessToken|string|Evet|Çağrıya göre kimlik doğrulaması için erişim belirteci (kapsam [hesabı erişim belirteci](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)olmalıdır). Erişim belirteçlerinin süresi 1 saat içinde doluyor.|
|ad|string|Evet|Yüzü güncelleştiren yeni ad.|

Adlar kişi modelleri için benzersizdir. bu nedenle, aynı kişi modelinde aynı **ad** parametre değerini iki farklı yüz olarak verirseniz, video Indexer yüzeyleri aynı kişi olarak görüntüler ve videonuzu yeniden atadıktan sonra bu kullanıcılara dönüştürülebilmenizi sağlar. 

### <a name="request-body"></a>İstek gövdesi

Bu çağrı için gereken başka bir istek gövdesi yok.

### <a name="response"></a>Yanıt

Yüz başarıyla güncelleştirildiği zaman döndürülen içerik yok.

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer Web sitesini kullanarak kişi modelini özelleştirme](customize-person-model-with-website.md)
