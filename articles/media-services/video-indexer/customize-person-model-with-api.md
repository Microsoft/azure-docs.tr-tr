---
title: Video Indexer API ile kişi modelini özelleştirme
titleSuffix: Azure Media Services
description: Video Indexer API ile bir kişi modelini özelleştirmeyi öğrenin.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: dced3cde1aae082bd64d785212b40e100013e0bf
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312901"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Video Indexer API ile bir kişi modelini özelleştirme

Video Indexer, video içeriği için yüz algılamayı ve ünlüy tanımayı destekler. Ünlüçilerin tanınma özelliği, ıDB, Viveze ve en popüler LinkedIn etkileyen, sık istenen veri kaynağına göre 1.000.000 yüz içerir. Ünlüme tanıma özelliği tarafından tanınmayan yüzler algılandı ancak sola adlandırılmamış. Videonuzu Video Indexer karşıya yükledikten ve sonuçları geri aldıktan sonra, geri dönüp tanınmamış yüzlere ad verebilirsiniz. Bir yüzü adı ile etiketledikten sonra, yüz ve ad hesabınızın kişi modeline eklenir. Video Indexer gelecekteki videolarınızdaki ve geçmiş videolarınızdaki bu yüzü tanıyacak.

Bu konuda açıklandığı gibi, videoda algılanan yüzeyleri düzenlemek için Video Indexer API 'sini kullanabilirsiniz. [Video Indexer Web sitesini kullanarak kişi modelini özelleştirme](customize-person-model-with-api.md)bölümünde açıklandığı gibi video Indexer Web sitesini de kullanabilirsiniz.

## <a name="managing-multiple-person-models"></a>Birden çok kişi modelini yönetme

Video Indexer hesap başına birden çok kişi modelini destekler. Bu özellik şu anda yalnızca Video Indexer API 'Leri üzerinden kullanılabilir.

Hesabınız farklı kullanım örneği senaryolarına karşı, hesap başına birden fazla kişi modeli oluşturmak isteyebilirsiniz. Örneğin, içeriğiniz spor ile ilgiliyse, her spor (futbol, basketbol, futbol, vb.) için ayrı bir kişi modeli oluşturabilirsiniz.

Bir model oluşturulduktan sonra, bir videoyu karşıya yüklerken/dizinleme yaparken veya yeniden dizinlerken belirli bir kişi modelinin model KIMLIĞINI sağlayarak onu kullanabilirsiniz. Video için yeni bir yüz eğitimi, videonun ilişkilendirildiği özel modeli günceller.

Her hesabın 50 kişi modeli sınırlaması vardır. Birden çok kişi modeli desteği gerekmiyorsa, karşıya yükleme/dizinleme veya yeniden dizin oluşturma sırasında videonuza bir kişi modeli KIMLIĞI atamayın. Bu durumda, Video Indexer hesabınızda varsayılan özel kişi modelini kullanır.

## <a name="create-a-new-person-model"></a>Yeni bir kişi modeli oluşturun

Belirtilen hesapta yeni bir kişi modeli oluşturmak için [kişi modeli oluşturma](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Person-Model) API 'sini kullanın.

Yanıt, aşağıdaki örnek biçimini izleyerek yeni oluşturduğunuz kişi modelinin adı ve oluşturulan model KIMLIĞINI sağlar.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Daha sonra, bir videoyu dizine veya [yeniden dizinlemeyi](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)bir videoyu [karşıya yüklerken](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) **personmodelıd** parametresinin **kimlik** değerini kullanırsınız.

## <a name="delete-a-person-model"></a>Kişi modelini silme

Belirtilen hesaptan özel bir kişi modelini silmek için [kişi modeli silme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Person-Model) API 'sini kullanın.

Kişi modeli başarıyla silindikten sonra, silinen modeli kullanan geçerli videolarınızın dizini yeniden dizinleyene kadar değişmeden kalır. Yeniden dizin oluşturma sırasında, silinen modelde adı geçen yüzler, geçerli videolarınızdaki Video Indexer tarafından tanınmayacak, ancak yüz algılanacaktır. Silinen model kullanılarak dizini oluşturulmuş geçerli videolarınız artık hesabınızın varsayılan kişi modelini kullanacaktır. Silinen modeldeki yüzler aynı zamanda hesabınızın varsayılan modelinde da adlandırılmışsa, bu yüzlerin videolarda tanınabilmesi devam edecektir.

Kişi modeli başarıyla silindiğinde döndürülen içerik yok.

## <a name="get-all-person-models"></a>Tüm kişi modellerini al

Belirtilen hesaptaki tüm kişi modellerini almak için [kişi modeli al](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Person-Models) API 'sini kullanın.

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

Bir videoyu `id` `personModelId` dizine veya [yeniden dizinlemeyi oluşturmaya](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) [yönelik bir video yüklerken](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) parametre için kişi modelinin değerini kullanarak, bir video için kullanmak istediğiniz modeli seçebilirsiniz.

## <a name="update-a-face"></a>Yüz güncelleştirme

Bu komut, videonun KIMLIĞINI ve yüzün kimliğini kullanarak Videonuzdaki bir yüzü bir ad ile güncelleştirmenize olanak tanır. Bu eylem daha sonra videonun karşıya yükleme/dizinleme veya yeniden dizin oluşturma sırasında ilişkilendirildiği kişi modelini güncelleştirir. Hiçbir kişi modeli atanmamışsa, hesabın varsayılan kişi modelini güncelleştirir.

Daha sonra sistem, aynı yüzün aynı yüzü aynı kişi modelini paylaşan diğer geçerli videolarınızdaki tekrarlarını tanır. Diğer geçerli videolarınızdaki yüzün tanınabilmesi, bu bir toplu işlem olduğu için biraz zaman alabilir.

Ünlüklik olarak tanınan Video Indexer bir yüzü yeni bir adla güncelleştirebilirsiniz. Verdiğiniz yeni ad, yerleşik ünlüğünün tanınmasına göre öncelikli olacaktır.

Yüzü güncelleştirmek için, [video yüz güncelleştirme](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Face) API 'sini kullanın.

Adlar kişi modelleri için benzersizdir, bu nedenle aynı kişi modelinde aynı parametre değerini iki farklı yüz verirseniz `name` , video Indexer yüzeyleri aynı kişi olarak görüntüler ve videonuzu yeniden atadıktan sonra bu kullanıcılara dönüştürüledirler.

## <a name="next-steps"></a>Sonraki adımlar

[Video Indexer Web sitesini kullanarak kişi modelini özelleştirme](customize-person-model-with-website.md)
