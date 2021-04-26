---
title: Uzamsal analiz nedir?
titleSuffix: Azure Cognitive Services
description: Bu belgede Görüntü İşleme uzamsal analiz kapsayıcısının temel kavramları ve özellikleri açıklanmaktadır.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.openlocfilehash: 5aa34ce15d96112450a7c15debcd92312c1d9e19
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284551"
---
# <a name="what-is-spatial-analysis"></a>Uzamsal analiz nedir?

Uzamsal analiz hizmeti, kuruluşların belirli bir alan içindeki hareketlerini ve varlığını anlamak için fiziksel alanlarının değerini en üst düzeye çıkarmalarına yardımcı olur. CCTV veya gözetim kameralarından video almanıza, video akışlarından Öngörüler çıkarmak için AI işlemlerini çalıştırmanıza ve diğer sistemler tarafından kullanılacak olayları oluşturmanıza olanak tanır. Bir kamera akışından gelen girişler sayesinde bir AI işlemi, yüz maskesi ve sosyal distancing yönergeleri ile bir boşluk veya ölçüm uyumluluğu girme gibi şeyler yapabilir.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Ne yapar?

Uzamsal çözümlemenin temel işlemleri, video al, videodaki kişileri algılayan, kişileri bir süre içinde hareket ettikleri sırada izleyen ve kişiler ilgi alanları ile etkileşim kuran olaylar oluşturan bir işlem hattına oluşturulmuştur.

## <a name="spatial-analysis-features"></a>Uzamsal analiz özellikleri

| Özellik | Tanım |
|------|------------|
| **İnsanların algılanması** | Bu bileşen "Bu görüntüdeki kişiler nerededir?" sorusunu yanıtlar. Bir görüntüdeki kişileri bulur ve kişilerin izleme bileşenine her birinin konumunu gösteren bir sınırlayıcı kutu geçirir. |
| **Kişi Izleme** | Bu bileşen, bir kameranın önünde insanlar hareket eden kişilerin algılamalarını zaman içinde bağlar. İnsanların genellikle kişilerin genel görünümüyle ilgili olarak nasıl hareket taşımadığı ve temel bilgiler hakkında zamana bağlı mantık kullanır. Birden çok kamera genelinde kişileri izlemez. Bir kişi, görüntüleme alanından yaklaşık bir dakikadan uzun bir süre çıkıp kamera görünümünü yeniden girerse, sistem bunu yeni bir kişi olarak alır. İnsanlar Izleme, kameraları genelinde kişileri benzersiz bir şekilde tanımlamaz. Yüz tanıma veya yürüyüş izlemeyi kullanmaz. |
| **Yüz maskesini algılama** | Bu bileşen bir kişinin, kameranın görünüm alanındaki yüzünün konumunu algılar ve bir yüz maskesinin varlığını tanımlar. AI işlemi video görüntülerini tarar; bir yüz algılandığında hizmetin yüz etrafında bir sınırlayıcı kutu sağladığını burada bulabilirsiniz. Nesne algılama yeteneklerini kullanarak, sınırlayıcı kutudaki yüz maskeleri varlığını belirler. Yüz maskesini algılama, yüz özniteliklerini tahmin eden veya sınıflandırarak ya da yüz tanımayı gerçekleştirerek başka bir yüzden bir yüzü ayırt etme işlemini kapsamaz. |
| **Ilgilendiğiniz bölge** | Bu, giriş video çerçevesindeki Kullanıcı tanımlı bir bölgedir veya satırdır. Bir kişi videoda bu bölgeyle etkileşime geçtiğinde, sistem bir olay oluşturur. Örneğin, PersonCrossingLine işlemi için videoda bir satır tanımlanmıştır. Bir kişi bu satırı aştığında bir olay oluşturulur. |
| **Olay** | Bir olay, uzamsal çözümlemenin birincil çıktısından oluşur. Her işlem belirli bir olayı düzenli aralıklarla (dakikada bir kez) veya belirli bir tetikleyici meydana geldiğinde yayar. Olay, giriş videosunda ne olduğu ile ilgili bilgiler içerir, ancak herhangi bir görüntü veya video içermez. Örneğin, PeopleCount işlemi, kişi sayısı (tetikleyici) veya her dakikada bir (düzenli olarak) her değiştiğinde güncelleştirilmiş sayımı içeren bir olay yayabilir. |

## <a name="get-started"></a>başlarken

### <a name="public-preview-gating"></a>Genel Önizleme geçişi

Uzamsal analizler için tasarlanan senaryolar için kullanıldığından emin olmak için, bu teknolojiyi bir uygulama işlemi aracılığıyla müşteriler için kullanılabilir hale getiririz. Uzamsal Analize erişim sağlamak için çevrimiçi giriş formumuzu doldurarak başlamanız gerekir. [Uygulamanızı buradan başlatın](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u).

Uzamsal analiz Genel önizlemesine erişim, Microsoft 'un bu geçişli önizleme sırasında sınırlı sayıda müşteriyi desteklemeye yönelik uygunluk ölçütlerinizle, dileyici sürecimize ve kullanılabilirliğine bağlı olarak Microsoft 'un kendi takdirine tabidir. Genel önizlemede, Microsoft ile önemli bir ilişkiye sahip olan müşterilere bakıyoruz, önerilen kullanım durumlarında bizimle çalışmaya ilgileniyor ve sorumlu AI taahhütimize devam eden ek senaryolar var.

### <a name="follow-a-quickstart"></a>Hızlı başlangıcı izleyin

Uzamsal Analize erişim izni verdikten sonra, kapsayıcıyı ayarlamak ve videoyu çözümlemeye başlamak için [hızlı](spatial-analysis-container.md) başlangıcı izleyin.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Uzamsal analiz teknolojisinin sorumlu kullanımı

Uzamsal analiz teknolojisinin sorumlu olduğunu nasıl kullanacağınızı öğrenmek için bkz. [asetat notunun](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Microsoft 'un saydamlık notları, AI teknolojimizin nasıl çalıştığını anlamanıza yardımcı olmak üzere tasarlanmıştır. sistem sahipleri, bu durum sistem performansını ve davranışını etkilemesinin yanı sıra teknoloji, kişiler ve ortam dahil olmak üzere tüm sistem hakkında düşünmesinin önemini de kolaylaştırabilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hızlı başlangıç: uzamsal çözümleme kapsayıcısı](spatial-analysis-container.md)' ' ' ' ' ' ' ' ' ' ' '