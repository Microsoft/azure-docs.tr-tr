---
title: Metin Analizi API'si hakkında sık sorulan sorular
titleSuffix: Azure Cognitive Services
description: Azure bilişsel hizmetler için Metin Analizi API'si ilgili kavramlar, kod ve senaryolar hakkında sık sorulan soruların yanıtlarını bulun.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: aahi
ms.openlocfilehash: c38b7c33cfe787ba933ca1fc4961080eaa4ada61
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276068"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Metin Analizi API'si hakkında sık sorulan sorular (SSS)

 Azure bilişsel hizmetler 'deki Metin Analizi API'si ilgili kavramlar, kod ve senaryolar hakkında sık sorulan soruların yanıtlarını bulun.

## <a name="what-is-the-maximum-size-and-number-of-requests-i-can-make-to-the-api"></a>API 'de yapabildiğim maksimum boyut ve istek sayısı nedir?

Dakika ve saniye başına gönderebilmeniz için isteklerin boyutu ve sayısı hakkında bilgi için [veri sınırları](concepts/data-limits.md) makalesine bakın.

## <a name="can-text-analytics-identify-sarcasm"></a>Metin Analizi, sarcasm tanımlayabilir miyim?

Analiz, ruh algılama yerine pozitif negatif yaklaşım içindir.

Yaklaşım analizinde her zaman bir noktasında kesinlik eksikliği derecesi vardır, ancak içeriğe gizli bir anlamı veya alt metin olmadığında model en yararlı seçenektir. Irony, sarcasm, humor ve benzer incelikli içeriği, amacı sağlamak için kültürel bağlamı ve Norms 'yi kullanır. Bu içerik türü, çözümlemenin en zor bir türüdür. Genellikle, çözümleyici tarafından üretilen belirli bir puan ve bir insan tarafından öznel bir değerlendirme arasındaki en büyük farklılık, nukmiş anlamı olan içeriğe yöneliktir.

## <a name="can-i-add-my-own-training-data-or-models"></a>Kendi eğitim verilerimi veya modellerimi ekleyebilir miyim?

Hayır, modeller önceden eğitilir. Karşıya yüklenen verilerde kullanılabilen tek işlemler Puanlama, anahtar ifade ayıklama ve dil algılama ' dır. Özel modeller barındırmıyor. Özel makine öğrenimi modelleri oluşturmak ve barındırmak istiyorsanız, [Microsoft R Server ' de makine öğrenimi özelliklerini](/r-server/r/concept-what-is-the-microsoftml-package)göz önünde bulundurun.

## <a name="can-i-request-additional-languages"></a>Ek dil isteyebilirim miyim?

Yaklaşım Analizi ve anahtar tümceciği ayıklama, [belirli sayıda dilde](./language-support.md)kullanılabilir. Doğal dil işleme karmaşıktır ve yeni işlevlerin yayımlanmadan önce önemli bir test gerektirir. Bu nedenle, hiç birinin çok daha fazla zaman kaplayan işlevlere bağımlılığı kalmayacak şekilde, önceden duyurduğumuz destekten kaçının. 

Bir sonraki adımda hangi dillerin çalıştığını önceliklendirmemize yardımcı olmak için, [Kullanıcı Sessiyle](https://cognitive.uservoice.com/forums/555922-text-analytics)belirli diller için oy verin. 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Anahtar tümceciği ayıklama neden bazı sözcükler döndürüyor, ancak diğerlerini sağlamıyor?

Anahtar tümceciği ayıklama, önemli olmayan kelimeleri ve tek başına sıfatları ortadan kaldırır. "Spectacsel görünümler" veya "Foggy Hava durumu" gibi sıfatıcı-isim birleşimleri birlikte döndürülür.

Genellikle, çıkış, tümcenin isimleri ve nesnelerinden oluşur. Çıktı önem sırasına göre listelenir ve ilk ifade en önemli öneme sahip olur. Önem derecesi, belirli bir kavramın kaç kez bahsedildiği veya bu öğenin metin içindeki diğer öğelerle ilişkisi ile ölçülür.

## <a name="why-does-output-vary-given-identical-inputs"></a>Çıkış neden farklılık gösterir, aynı girişler verilir?

Model ve algoritmalara yönelik iyileştirmeler, değişikliğin büyük olması durumunda duyurulur veya güncelleştirme küçük olduğunda hizmete sessizce kaydedilir. Zaman içinde, aynı metin girişinin farklı bir yaklaşım puanına veya anahtar tümceciği çıktısına neden olduğunu fark edebilirsiniz. Bu, bulutta yönetilen makine öğrenimi kaynaklarını kullanmanın normal ve bilerek bir sonucudur.

## <a name="service-availability-and-redundancy"></a>Hizmet kullanılabilirliği ve artıklığı

### <a name="is-text-analytics-service-zone-resilient"></a>Metin Analizi hizmet bölgesi dayanıklı mi?

Evet. Metin Analizi hizmet varsayılan olarak bölge esnektir.

### <a name="how-do-i-configure-the-text-analytics-service-to-be-zone-resilient"></a>Metin Analizi hizmeti bölge-dayanıklı olacak şekilde Nasıl yaparım? mı?

Bölge dayanıklılığı sağlamak için hiçbir müşteri yapılandırması gerekmez. Metin Analizi kaynaklar için bölge esnekliği, varsayılan olarak kullanılabilir ve hizmet tarafından yönetilir.

## <a name="next-steps"></a>Sonraki adımlar

Sorunuz, eksik bir özellik veya işlevlerle ilgili mi? [UserVoice Web sitemizdeki](https://cognitive.uservoice.com/forums/555922-text-analytics)BT için istek veya oylama yapmayı deneyin.

## <a name="see-also"></a>Ayrıca bkz.

 * [StackOverflow: Metin Analizi API'si](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 * [StackOverflow: bilişsel hizmetler](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
