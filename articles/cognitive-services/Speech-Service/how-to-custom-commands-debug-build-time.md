---
title: Özel komutlar uygulaması yazarken hata ayıklama hataları (Önizleme)
titleSuffix: Azure Cognitive Services
description: Bu makalede, özel komutlar uygulaması yazarken hata ayıklamanın nasıl yapılacağını öğreneceksiniz.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: aeb90e8e064c44f4d17f920261ed58310f0e55f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95025710"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Özel bir komut uygulaması yazarken hata ayıklama hataları

Bu makalede, özel komutlar uygulaması oluştururken hata ayıkladığınızda hata ayıklamanın nasıl yapılacağı açıklanır. 

## <a name="errors-when-creating-an-application"></a>Uygulama oluştururken hatalar
Özel komutlar Ayrıca, özel komutlar uygulaması oluştururken [Luo](https://www.luis.ai/) 'da bir uygulama oluşturur. 

[Lusıs, yazma kaynağı başına 500 uygulama kısıtlar](../luis/luis-limits.md). Zaten 500 uygulamasına sahip bir yazma kaynağı kullanıyorsanız, LUSıS uygulamasının oluşturulması başarısız olabilir. 

Seçili LUSıS yazma kaynağında 500 'den az uygulama olduğundan emin olun. Aksi takdirde, yeni bir LUSıS Authoring Resource oluşturabilir, başka birine geçebilir veya LUSıS uygulamalarınızı temizlemeyi deneyebilirsiniz.  

## <a name="errors-when-deleting-an-application"></a>Uygulama silinirken hatalar oluştu
### <a name="cant-delete-luis-application"></a>LUSıS uygulaması silinemiyor
Özel komutlar uygulaması silinirken, özel komutlar özel komutlar uygulamasıyla ilişkili LUO uygulamasını silmeye de çalışabilir.

LUSıS uygulamasını silme işlemi başarısız olduysa, el ile silmek için [lusıs](https://www.luis.ai/) hesabınıza gidin.

### <a name="toomanyrequests"></a>TooManyRequests
Tek seferde çok sayıda uygulamayı silmeye çalıştığınızda, büyük olasılıkla ' TooManyRequests ' hatalarını görürsünüz. Bu hatalar, silme isteklerinizin Azure tarafından kısıtlandığı anlamına gelir. 

Sayfanızı yenileyin ve daha az uygulamayı silmeyi deneyin.

## <a name="errors-when-modifying-an-application"></a>Bir uygulamayı değiştirirken oluşan hatalar

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Bir parametre veya Web uç noktası silinemiyor
Kullanılırken bir parametreyi silmeye izin verilmiyor. Tüm konuşma yanıtlarındaki parametre başvurularını, örnek cümleleri, koşulları ve eylemleri kaldırın ve yeniden deneyin.

### <a name="cant-delete-a-web-endpoint"></a>Web uç noktası silinemiyor
Kullanılırken bir Web uç noktası silme izniniz yok. Web uç noktası kaldırılmadan önce bu Web uç noktasını kullanan tüm **çağrı Web uç noktası** eylemlerini kaldırın.

## <a name="errors-when-training-an-application"></a>Bir uygulamayı eğitmek için hatalar
### <a name="built-in-intents"></a>Built-In amaçları
LUIN yerleşik Evet/Hayır amacı vardır. Yalnızca "Yes", "Hayır" ile örnek cümleler olması eğitimin başarısız olmasına neden olur. 

| Sözcükle | Farklılıklar | 
| ------- | --------- | 
| Yes | Emin olun, tamam |
| No | Nope, değil | 

### <a name="common-sample-sentences"></a>Ortak örnek cümleler
Özel komutlar, farklı komutlar arasında paylaşılan ortak örnek cümlelere izin vermez. Bir komutta bazı örnek cümleler zaten başka bir komutta tanımlanmışsa, bir uygulamanın eğitimi başarısız olabilir. 

Farklı komutlar arasında paylaşılan ortak örnek cümleler olmadığından emin olun. 

Örnek cümlelerinizi farklı komutlarda dengelemenin en iyi uygulaması için [lusıs en iyi uygulaması](../luis/luis-concept-best-practices.md)' na bakın.

### <a name="empty-sample-sentences"></a>Boş örnek cümleler
Her komut için en az bir örnek cümle olması gerekir.

### <a name="undefined-parameter-in-sample-sentences"></a>Örnek tümcelerde Tanımsız parametre
Örnek cümlelerde bir veya daha fazla parametre kullanıldı, ancak tanımlanmamış.

### <a name="training-takes-too-long"></a>Eğitim çok uzun sürüyor
LUSıS eğitiminin daha az örnekle hızla öğrenme amacı vardır. Çok sayıda örnek tümce eklemeyin. 

Benzer birçok örnek cümle varsa, bir parametre tanımlayın, bunları bir düzene ekler ve örnek cümlelere ekleyin.

Örneğin, aşağıdaki örnek tümceler için {araç} parametresini tanımlayabilir ve yalnızca "Book a {araç}" öğesini örnek cümlelere ekleyebilirsiniz.

| Örnek cümleler | Desen | 
| ------- | ------- | 
| Araba kitabı | Bir {araç} kitabı | 
| Uçuş kitabı | Bir {araç} kitabı |
| Bir TAXI defteri | Bir {araç} kitabı |

LUSıS eğitiminin en iyi uygulaması için [lusıs en iyi uygulaması](../luis/luis-concept-best-practices.md)' na bakın.

## <a name="cant-update-luis-key"></a>LUSıS anahtarı güncelleştirilemiyor
### <a name="reassign-to-e0-authoring-resource"></a>E0 Authoring Resource öğesine yeniden ata
LUSıS, E0 yazma kaynağına LUSıS uygulamasının atanmasını desteklemez.

Authoring Resource F0 ' den E0 ' ye değiştirmeniz veya farklı bir E0 kaynağına değiştirmeniz gerekiyorsa, uygulamayı yeniden oluşturun. 

Mevcut bir uygulamayı hızlıca dışarı aktarıp yeni bir uygulamaya aktarmak için [Azure DevOps Ile sürekli dağıtıma](./how-to-custom-commands-deploy-cicd.md)bakın.

### <a name="save-button-is-disabled"></a>Kaydet düğmesi devre dışı
Uygulamanıza hiçbir zaman bir LUSıS tahmin kaynağı atamadıysanız, bir tahmin kaynağı eklemeden yazma kaynağınızı değiştirmeye çalıştığınızda Kaydet düğmesi devre dışı bırakılır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [GitHub 'da örneklere bakın](https://aka.ms/speech/cc-samples)