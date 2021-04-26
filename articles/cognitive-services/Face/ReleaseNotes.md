---
title: Yüz Tanıma hizmetindeki yenilikler
titleSuffix: Azure Cognitive Services
description: Yüz hizmeti için sürüm notları, çeşitli sürümler için sürüm değişikliklerinin geçmişini içerir.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
ms.custom: contperf-fy21q3
ms.openlocfilehash: c580828d29e92ecef7ecc73b8f3e5843c3ecd23d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078889"
---
# <a name="whats-new-in-face-service"></a>Yüz Tanıma hizmetindeki yenilikler

Azure yüz hizmeti, sürekli olarak güncelleştirilir. Özellik geliştirmeleri, düzeltmeler ve belge güncelleştirmeleriyle güncel kalmak için bu makaleyi kullanın.

## <a name="february-2021"></a>Şubat 2021

### <a name="new-face-api-detection-model"></a>Yeni Yüz Tanıma API'si algılama modeli
* Yeni algılama 03 modeli, şu anda kullanılabilir olan en doğru algılama modelidir. Yeni bir müşteriyseniz, bu modeli kullanmanızı öneririz. Algılama 03, resimlerde bulunan daha küçük yüzlerde hem geri çağırma hem de duyarlık geliştirir (64x64 piksel). Ek geliştirmeler, hatalı pozitif sonuçlar ve döndürülen yüz yönleri üzerinde geliştirilmiş algılama gibi genel bir azalma içerir. Algılama 03 ' i yeni tanıma 04 modeliyle birleştirmek, daha iyi bir tanıma doğruluğu sağlar. Daha fazla ayrıntı için bkz. [yüz algılama modeli belirtme](./face-api-how-to-topics/specify-detection-model.md) .
### <a name="new-detectable-face-attributes"></a>Yeni algılanabilir yüz nitelikleri
* `faceMask`Özniteliği en son algılama 03 modeliyle birlikte kullanılabilir `"noseAndMouthCovered"` . Bu özellik, yüz maskesinin ne kadar uygun olduğunu algılayan ve hem burun hem de ağız içeren ek özniteliktir. En son maske algılama özelliğini kullanmak için kullanıcıların API isteğinde algılama modelini belirtmesi gerekir: model sürümünü _detectionModel_ parametresiyle atayın `detection_03` . Daha fazla ayrıntı için bkz. [yüz algılama modeli belirtme](./face-api-how-to-topics/specify-detection-model.md) .
### <a name="new-face-api-recognition-model"></a>Yeni Yüz Tanıma API'si tanıma modeli
* Yeni tanıma 04 modeli, şu anda kullanılabilir en doğru tanıma modelidir. Yeni bir müşteriyseniz, bu modelin doğrulama ve tanımlama için kullanılmasını öneririz. Bu, tanınma 03 doğruluğunu geliştirir ve bu da, yüz kapamaları (Surgical maskeleri, N95 maskeleri, kumaş maskeleri) içeren kayıtlı kullanıcılara yönelik iyileştirilmiş tanıma da dahildir. Artık müşteriler, kayıtlı bir kullanıcının en son algılama 03 modeliyle bir yüz kapağı takıp takmadığını algılayan ve en son tanıma 04 modeliyle kim olduğunu tanılayan güvenli ve sorunsuz kullanıcı deneyimleri oluşturabilir. Daha fazla ayrıntı için bkz. [yüz tanıma modeli belirtme](./face-api-how-to-topics/specify-recognition-model.md) .


## <a name="january-2021"></a>Ocak 2021
### <a name="mitigate-latency"></a>Gecikme süresini azaltma
* Yüz ekibi, hizmetin kullanımı ve olası risk azaltma stratejilerini açıklayan olası nedenler hakkında yeni bir makale yayımladı. [Yüz hizmetini kullanırken gecikme süresini azaltmaya](./face-api-how-to-topics/how-to-mitigate-latency.md)bakın.

## <a name="december-2020"></a>Aralık 2020
### <a name="customer-configuration-for-face-id-storage"></a>Yüz KIMLIĞI depolaması için müşteri yapılandırması
* Yüz hizmeti müşteri görüntülerini depolarken, ayıklanan yüz özellikleri sunucuda depolanır. Yüz [kimliği, yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)özelliğinin bir tanıtıcısıdır ve benzer şekilde, yüz tanıma, [yüz-doğrulama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)ve [yüz-bulma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)gibi görünür. Depolanan yüz özelliklerinin süresi dolacak ve özgün algılama çağrısından sonra 24 saat silinmelidir. Müşteriler artık bu yüz kimliklerinin önbelleğe alınma süresini tespit edebilir. En büyük değer 24 saate kadar sürer, ancak en az 60 saniyelik bir değer ayarlanabilir. Önbelleğe alınan yüz kimlikleri için yeni zaman aralıkları, 60 saniye ile 24 saat arasında herhangi bir değerdir. Daha fazla ayrıntı, [yüz algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API başvurusunda ( *Faceidtimetolive* parametresi) bulunabilir.

## <a name="november-2020"></a>Kasım 2020
### <a name="sample-face-enrollment-app"></a>Örnek yüz kayıt uygulaması
* Takım, anlamlı bir izin oluşturmak ve yüksek kaliteli kayıtlar aracılığıyla yüksek doğruluk düzeyi tanıma sistemleri oluşturmak için en iyi uygulamaları göstermek üzere örnek bir yüz kayıt uygulaması yayımladı. Açık kaynaklı örnek, [kayıt uygulama](build-enrollment-app.md) kılavuzunda ve [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample)'da, geliştiricilerin dağıtması veya özelleştirmesi için hazırlanma bölümünde bulunabilir. 

## <a name="august-2020"></a>Ağustos 2020
### <a name="customer-managed-encryption-of-data-at-rest"></a>Bekleyen verilerin müşteri tarafından yönetilen şifrelenmesi
* Yüz hizmeti, buluta kalıcı hale geldiğinde verilerinizi otomatik olarak şifreler. Yüz hizmeti şifrelemesi, kurumsal güvenlik ve uyumluluk taahhütlerinizi karşılamanıza yardımcı olmak için verilerinizi korur. Aboneliğiniz varsayılan olarak Microsoft tarafından yönetilen şifreleme anahtarlarını kullanır. Aboneliğinizi, müşteri tarafından yönetilen anahtarlar (CMK) adlı kendi anahtarlarınız ile yönetmeye yönelik yeni bir seçenek de vardır. Daha fazla ayrıntı, [müşteri tarafından yönetilen anahtarlarda](./encrypt-data-at-rest.md)bulunabilir.

## <a name="april-2020"></a>Nisan 2020
### <a name="new-face-api-recognition-model"></a>Yeni Yüz Tanıma API'si tanıma modeli
* Yeni tanıma 03 modeli, şu anda kullanılabilir en doğru modeldir. Yeni bir müşteriyseniz, bu modeli kullanmanızı öneririz. Tanınma 03 hem benzerlik karşılaştırmaları hem de kişi ile eşleşen karşılaştırmalar için geliştirilmiş doğruluk sağlar. Daha fazla ayrıntı için [bir yüz tanıma modeli belirtin](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>Haziran 2019

### <a name="new-face-api-detection-model"></a>Yeni Yüz Tanıma API'si algılama modeli
* Yeni algılama 02 modeli, küçük, yan görünüm, occluded ve bulanık yüzler üzerinde geliştirilmiş doğruluk özelliklerine sahiptir. Bu uygulamayı, [yüz algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), çok [yönlü liste-ekle yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [Largeçok yönlü liste-ekleme yüzü](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [kişilik grubu kişi-ekleme](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) yüz ve [largepersongroup Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) Ile, parametrede yeni yüz algılama modeli adını belirterek yüz `detection_02` ekleme `detectionModel` . [Algılama modeli belirtme hakkında](Face-API-How-to-Topics/specify-detection-model.md)daha fazla bilgi.

## <a name="april-2019"></a>Nisan 2019

### <a name="improved-attribute-accuracy"></a>Geliştirilmiş öznitelik doğruluğu
* Ve özniteliklerinin genel doğruluğu geliştirildi `age` `headPose` . `headPose`Özniteliği, `pitch` Şimdi etkinleştirilen değer ile de güncelleştirilir. Bu öznitelikleri, `returnFaceAttributes` [yüz algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) parametresi parametresinde belirterek kullanın `returnFaceAttributes` . 
### <a name="improved-processing-speeds"></a>İyileştirilmiş işleme hızları
* [Yüz algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), çok [yönlü liste-ekleme yüzü](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [largecelist-Add yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [kişilik grubu kişi-yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ve [largepersongroup kişi ekleme-yüz işlemleri ekleme](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) .

## <a name="march-2019"></a>Mart 2019

### <a name="new-face-api-recognition-model"></a>Yeni Yüz Tanıma API'si tanıma modeli
* Tanıma 02 modelinin doğruluğu artmıştır. Parametre içinde yeni yüz tanıma modeli adını belirterek, [yüz algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), çok [yönlü liste-oluştur](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [largecelist-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [Persongroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) ve [largepersongroup-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) Create ile kullanın `recognition_02` `recognitionModel` . [Bir tanıma modeli belirtme hakkında](Face-API-How-to-Topics/specify-recognition-model.md)daha fazla bilgi.

## <a name="january-2019"></a>Ocak 2019

### <a name="face-snapshot-feature"></a>Yüz anlık görüntü özelliği
* Bu özellik, hizmetin abonelikler arasında veri geçişini desteklemesini sağlar: [anlık görüntü](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). [Yüz verilerinizi farklı bir yüz aboneliğine geçirme hakkında](Face-API-How-to-Topics/how-to-migrate-face-data.md)daha fazla bilgi.

## <a name="october-2018"></a>Ekim 2018

### <a name="api-messages"></a>API iletileri
* ,,, Ve için,,, ve için bir arada `status` `createdDateTime` bulunan Açıklama `lastActionDateTime` `lastSuccessfulTrainingDateTime` [-eğitim durumunu Al](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [largepersongroup-Get eğitim durumu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)ve [largecelıst-eğitim durumunu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)al.

## <a name="may-2018"></a>Mayıs 2018

### <a name="improved-attribute-accuracy"></a>Geliştirilmiş öznitelik doğruluğu
* Geliştirilmiş `gender` öznitelik önemli ölçüde ve geliştirilmiş,,,, `age` `glasses` `facialHair` `hair` `makeup` öznitelikleri. Onları, [yüz algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametresiyle kullanın.
### <a name="increased-file-size-limit"></a>Daha fazla dosya boyutu sınırı
* [Yüz algılama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), çok [yönlü liste-ekleme yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [Largecem listesi-ekleme yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [kişiskii grup kişi-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) yüz ve [largepersongroup kişi ekleme-yüz ekleme](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2018"></a>Mart 2018

### <a name="new-data-structure"></a>Yeni veri yapısı
* [Largecelist](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) ve [largepersongroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). [Büyük ölçekli özelliği kullanma hakkında](Face-API-How-to-Topics/how-to-use-large-scale.md)daha fazla ayrıntı.
* [](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` [1, 5] ile [1, 100] arasında ve varsayılan olarak 10 ' a kadar artan yüz-parametre.

## <a name="may-2017"></a>Mayıs 2017

### <a name="new-detectable-face-attributes"></a>Yeni algılanabilir yüz nitelikleri
* `hair` `makeup` `accessory` `occlusion` `blur` `exposure` `noise` [Yüz Algıla](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametresinde,,,,, ve öznitelikleri eklendi.
* Bir kişilik grubundaki desteklenen 10.000 kişi ve [yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)tanıma.
* [Persongroup kişi listesindeki](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) desteklenen sayfalandırma isteğe bağlı parametrelerle: `start` ve `top` .
* Farklı çok yönlü listelerle ve PersonGroup 'taki farklı kişilerle yüz ekleme/silme için desteklenen eşzamanlılık.

## <a name="march-2017"></a>Mart 2017

### <a name="new-detectable-face-attribute"></a>Yeni algılanabilir yüz özniteliği
* `emotion` [Yüzü Algıla](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parametresinde öznitelik eklendi.
### <a name="fixed-issues"></a>Düzeltilen sorunlar
* Yüz, çok yönlü liste gibi yüz [algılamalarından](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) döndürülen dikdörtgenle yeniden algılanamadı `targetFace` . [yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) ve [kişilik grubu ekleme-yüz ekleme](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).
* Algılanabilir yüz boyutu, kesin olarak 36x36 ila 4096x4096 piksel arasında olduğundan emin olmak üzere ayarlanır.

## <a name="november-2016"></a>Kasım 2016
### <a name="new-subscription-tier"></a>Yeni abonelik katmanı
* [Persongroup kişisi](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) kullanırken ek kalıcı yüzeyleri depolamak Için yüz depolama Standart abonelik eklendi-yüz veya çok [yönlü liste ekle-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) tanımlama veya benzerlik eşleştirme için yüz ekleme. Depolanan görüntüler, 1000 yüz başına 0,5 $ üzerinden ücretlendirilir ve bu fiyat, günlere eşit olarak dağıtılır. Ücretsiz katman abonelikleri 1.000 toplam kişi ile sınırlı olmaya devam eder.

## <a name="october-2016"></a>Ekim 2016
### <a name="api-messages"></a>API iletileri
* İçindeki birden fazla yüzün hata iletisini, ' ın ' üzerinde birden fazla yüz, çok `targetFace` [yönlü liste-ekleme yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) ve [Kişiskii grubu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)gibi bir yüz ekleme

## <a name="july-2016"></a>Temmuz 2016
### <a name="new-features"></a>Yeni özellikler
* Yüz içinde nesne kimlik doğrulaması için desteklenen yüz [-doğrulama](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
* `mode`İki çalışma modunun seçilmesine olanak sağlayan isteğe bağlı parametre eklendi `matchPerson` : `matchFace` ve [yüz yüze, benzer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) ve varsayılan olarak bul `matchPerson` .
* `confidenceThreshold`Kullanıcı için isteğe bağlı parametresi, [yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)' da bir kişinin bir kişi nesnesine ait olup olmadığı eşiğini ayarlamak için eklenmiştir.
* `start` `top` Kullanıcının başlangıç noktasını ve toplam persongroup sayısını listeye belirtmesini sağlamak Için, [persongroup-List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) içinde isteğe bağlı ve parametreler eklendi.

## <a name="v10-changes-from-v0"></a>V0 'den v 1.0 değişiklikleri

* Service Root bitiş noktası sürümünden ' ```https://westus.api.cognitive.microsoft.com/face/v0/``` ye güncelleştirildi ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` . Değişiklikler uygulandı: [yüz tanıma](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [yüz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)tanıma, yüz tanıma, [benzer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) ve [yüz grubunu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)bul.
* Minimum algılanabilir yüz boyutu 36x36 piksel olarak güncelleştirildi. 36x36 pikselden küçük yüzler algılanmayacak.
* Yüz v0 'daki Person grubu ve kişi verileri kullanım dışı bırakıldı. Yüz V 1.0 hizmetiyle bu verilere erişilemez.
* 30 Haziran 2016 tarihinde Yüz Tanıma API'si v0 uç noktası kullanımdan kaldırıldı.