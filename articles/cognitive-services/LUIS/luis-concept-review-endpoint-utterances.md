---
title: Kullanıcı söylenişi-Lua gözden geçirme
description: Etkin öğrenme sayesinde, gözden geçirme uç noktası, doğru amaç ve varlık için yapılır. LUO, geçersiz olduğu için uç nokta utbotları seçer.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 82f228d5e6f801539c549e16faea371782ad4b59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316452"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Uç nokta uttlerini inceleyerek etkin öğrenmeyi etkinleştirme kavramları
Etkin öğrenme, tahmin doğruluğunu ve uygulamanın en kolay şekilde iyileştirebilmek için üç stratejiden biridir. Etkin öğrenme sayesinde, gözden geçirme uç noktası, doğru amaç ve varlık için yapılır. LUO, geçersiz olduğu için uç nokta utbotları seçer.

## <a name="what-is-active-learning"></a>Etkin öğrenme nedir?
Etkin öğrenme iki adımlı bir işlemdir. İlk olarak, LUO, uygulamanın, doğrulama gerektiren uç noktada aldığı noktaları seçer. İkinci adım, uygulama sahibi veya ortak çalışan tarafından, doğru amaç ve amaç içindeki herhangi bir varlık dahil olmak üzere, [Gözden geçirme](luis-how-to-review-endpoint-utterances.md)için seçilen atları doğrulamak üzere gerçekleştirilir. Söyleylerini inceledikten sonra uygulamayı yeniden eğitin ve yayımlayın.

## <a name="which-utterances-are-on-the-review-list"></a>Gözden geçirme listesinde yer alan söyleyceler
Lua, en üst tetikleme amacı düşük bir puansa veya en üstteki iki amaç puan düzeyi çok yakın olduğunda gözden geçirme listesine bir sayaç ekler.

## <a name="single-pool-for-utterances-per-app"></a>Uygulama başına düşen tek havuz
**Gözden geçirme uç noktası sıralayıcısı** listesi, sürüme göre değişmez. Etkin olarak hangi sürümün ifadesini düzenlediğinizden veya uç noktada uygulamanın hangi sürümünün yayımlandığından bağımsız olarak, gözden geçirilecek tek bir ifade havuzu vardır.

[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9), sürüm adı gereklidir ve uygulamada bulunmalıdır, ancak bu doğrulamanın ötesinde kullanılmamalıdır. Gözden geçirme, tüm uygulama için geçerlidir. Bir _sürümden_ gelen utbotları kaldırırsanız, tüm sürümler etkilenir.

## <a name="where-are-the-utterances-from"></a>Nereden gelen
Uç nokta, uygulamanın HTTP uç noktasındaki son kullanıcı sorgularından alınır. Uygulamanız yayınlanmamışsa veya henüz isabet almadıysa, gözden geçirmeniz gereken herhangi bir söylenme yoktur. Belirli bir amaç veya varlık için uç nokta isabetlerinin alınmadığında, bunları içeren gözden geçirime sahip olursunuz.

## <a name="schedule-review-periodically"></a>Gözden geçirmeyi düzenli aralıklarla zamanlayın
Önerilen mabotları gözden geçirmek her gün yapılması gerekmez, ancak normal LUSıS bakımının bir parçası olmalıdır.

## <a name="delete-review-items-programmatically"></a>Program aracılığıyla gözden geçirme öğelerini Sil
**[Etiketlenmemiş utterslar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API 'sini Sil. **[Günlük dosyalarını dışa aktararak](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** silinmeden önce bu utbotları yedekleyin.

## <a name="enable-active-learning"></a>Etkin öğrenmeyi etkinleştir

Etkin öğrenmeyi etkinleştirmek için Kullanıcı sorgularını günlüğe yazmanız gerekir. Bu, ENDPOINT parametresi ve değeri ile [Endpoint sorgusu](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) çağırarak yapılır `log=true` .

## <a name="next-steps"></a>Sonraki adımlar

* Endpoint deterlerini [incelemeyi](luis-how-to-review-endpoint-utterances.md) öğrenin
