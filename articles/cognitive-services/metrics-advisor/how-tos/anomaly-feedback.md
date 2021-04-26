---
title: Ölçüm Danışmanı hizmetine anomali geri bildirimi sağlama
titleSuffix: Azure Cognitive Services
description: Ölçüm Danışmanı Örneğinizde bulunan bozukluklar hakkında nasıl geri bildirim gönderileceğini öğrenin ve sonuçları ayarlayın.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96185020"
---
# <a name="provide-anomaly-feedback"></a>Anomali geri bildirimi sağlama

Kullanıcı geri bildirimi, anomali algılama sistemindeki hataları bulmaya yönelik en önemli yöntemlerden biridir. Burada, kullanıcıların hatalı algılama sonuçlarını doğrudan bir zaman serisinde işaretlemesi ve geri bildirimi hemen uygulaması için bir yol sağlıyoruz. Bu şekilde, bir Kullanıcı anomali algılama sistemine, etkin etkileşimler aracılığıyla belirli bir zaman serisi için anomali algılama işlemini nasıl yapabilirim. 

> [!NOTE]
> Şu anda geri bildirim yalnızca **akıllı algılama** ile anomali algılama sonuçlarını etkiler, **Sabit eşik** ve **değişiklik eşiğini** etkilemez.

## <a name="how-to-give-time-series-feedback"></a>Zaman serisi geri bildirimi verme

Herhangi bir serinin ölçüm ayrıntısı sayfasından geri bildirim sağlayabilirsiniz. Yalnızca herhangi bir noktayı seçmeniz yeterlidir ve aşağıdaki geri bildirim iletişim kutusunu görürsünüz. Seçtiğiniz serinin boyutlarını gösterir. Boyut değerlerini yeniden seçebilir veya bazı zaman serisi verileri almak için bunlardan bazılarını kaldırabilirsiniz. Zaman serisini seçtikten sonra, geri bildirimi eklemek için **Ekle** düğmesini seçin, verebileceğiniz dört tür geri bildirimde bulunabilir. Birden fazla geri bildirim öğesi eklemek için, ek açıklamalarınızı tamamladıktan sonra **Kaydet** düğmesini seçin.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Birkaç noktada mavi çizgi ve kırmızı noktalar içeren zaman serisi verileriyle grafik. Metnin bulunduğu bir noktayı çevreleyen kırmızı kutu: herhangi bir noktayı seçin":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="İki boyut içeren geri bildirim iletişim kutusu ve boyut seçme veya kaldırma ve geri bildirim ekleme seçeneği ekleyin.":::

### <a name="mark-the-anomaly-point-type"></a>Anomali noktası türünü işaretle

Aşağıdaki görüntüde gösterildiği gibi, geri bildirim iletişim kutusu seçtiğiniz noktanın zaman damgasını otomatik olarak doldurur, ancak bu değer de düzenleyebilirsiniz. Sonra bu öğeyi, veya olarak tanımlamak istediğinizi seçersiniz `Anomaly` `NotAnomaly` `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Anomali, NotAnomaly ve otomatik algıla seçenekleriyle açılan menü menüsü":::

Bu seçim, geri bildiriminizi aynı serinin gelecekteki anomali algılama işlemeye uygular. İşlenen noktaları yeniden hesaplanmayacak. Yani, NotAnomaly olarak bir anomali seçtiyseniz, gelecekte benzer bozukluklar bastırılır ve bir `NotAnomaly` noktayı olarak işaretlediyseniz, `Anomaly` benzer noktaları gelecekte tespit eteceğiz `Anomaly` . Seçilirse `AutoDetect` , daha sonra aynı noktaya ait önceki geri bildirimin tümü göz ardı edilir.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Birden çok sürekli noktaya yönelik geri bildirim sağlayın 

Aynı anda birden çok sürekli noktaya yönelik anomali geri bildirimi vermek isterseniz, açıklama eklemek istediğiniz noktaların grubunu seçin. Anomali geri bildirim sağladığınızda seçili zaman aralığının otomatik olarak doldurulduğunu görürsünüz.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Anomali geri bildirim menüsü ve belirli bir zaman aralığı":::

Tek bir noktanın anomali geri bildirimlerinizden etkilenip etkilenmediğini görüntülemek için, bir zaman serisine göz atarken tek bir nokta seçin. Anomali algılama sonucu geri bildirimle değiştirilmişse, araç ipucu **geri bildirimde bulunan geri bildirim: true** olur. Bu durum **geri bildirimle etkilendi: false** olduğunda, bu nokta için bir anomali geri bildirim hesaplamasının gerçekleştirildiği, ancak anomali algılama sonucu değiştirilmemelidir.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Sözcüklerin bulunduğu araç ipucu görüntüsü: geri bildirimle etkilendi: true, kırmızı renkle vurgulanır":::

Geri bildirim verme önermediğimiz bazı durumlar vardır:

- Anomali bir tatil nedeniyle oluşur. Daha kesin olacağı için, bu tür bir false alarm çözümlemek için önceden ayarlanmış bir olay kullanılması önerilir.
- Anomali, bilinen bir veri kaynağı değişikliği nedeniyle oluşur. Örneğin, o anda bir yukarı akış sistem değişikliği gerçekleşti. Bu durumda, sistemimizin değer değişikliğine neyin neden olduğunu bilmediği ve benzer değer değişikliklerinin yeniden gerçekleştiği durumlar nedeniyle bir anomali uyarı vermesi beklenmektedir. Bu nedenle, bu tür bir sorunu olarak not ekleme öneremiyoruz `NotAnomaly` .

## <a name="change-points"></a>Değişiklik noktaları

Bazen verilerin eğilimi değişikliği, anomali algılama sonuçlarını etkiler. Bir noktanın anomali olup olmadığı gibi bir karar verirken, geçmiş verilerinin en son penceresi göz önünde bulundurulacaktır. Zaman seriniz bir eğilim değişikliğine sahip olduğunda, tam değişiklik noktasını işaretleyebilirsiniz ve bu, gelecekteki analizde anomali algımuza yardımcı olur.

Aşağıdaki şekilde gösterildiği gibi, `ChangePoint` geri bildirim türünü seçebilir ve `ChangePoint` `NotChangePoint` aşağı açılan listeden,, veya öğesini seçebilirsiniz `AutoDetect` .

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Değişiklik noktası, NotChangePoint ve otomatik algıla seçeneklerini içeren açılan menü ile nokta menüsünü değiştirme":::

> [!NOTE]
> Verileriniz değişikliği devam ederse, yalnızca bir noktayı bir olarak işaretlemeniz gerekir; bu nedenle, `ChangePoint` bir tane belirlerseniz, `timerange` son noktanın zaman damgasını ve zamanını otomatik olarak dolduracağız. Bu durumda, ek açıklama yalnızca 12 noktadan sonra anomali algılama sonuçlarını etkiler.

## <a name="seasonality"></a>Mevsimsellik

Dönemsel veriler için, anomali algılama gerçekleştirirken bir adım, zaman serisinin dönemini (mevsimsellik) tahmin etmek ve anomali algılama aşamasına uygulamaktır. Bazen kesin bir dönemi belirlemek zordur ve dönem da değişebilir. Yanlış tanımlanmış bir süre, anomali algılama sonuçlarınız üzerinde yan etkilere neden olabilir. Geçerli dönemi bir araç ipucuyla bulabilirsiniz, adının adı `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Sözcüklerin süresi ve yedi vurgulanmış sayı içeren araç ipucu kaplaması.":::

Bu tür anomali algılama hatasını çözme dönemi için geri bildirim sağlayabilirsiniz. Şekilde gösterildiği gibi, bir dönem değeri ayarlayabilirsiniz. Birim `interval` bir ayrıntı düzeyi anlamına gelir. Burada sıfır Aralık, verilerin mevsimsiz olduğu anlamına gelir. Ayrıca, `AutoDetect` önceki geri bildirimleri iptal etmek ve işlem hattının dönemi otomatik olarak algılamasına izin vermek istiyorsanız öğesini de seçebilirsiniz. 
 
> [!NOTE]
> Bir zaman damgası veya timerange atamanız gerekmiyorsa, dönem, geri bildirimde bulunmanız gereken andan itibaren tüm zaman serisi 'teki gelecekteki anomali algılamaları etkileyecektir.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Otomatik Algıla döneminin ayarlandığı menü, zaman aralıklı olmayan ' ı işaret eden sıfır olarak ayarlanır.":::

## <a name="provide-comment-feedback"></a>Açıklama geri bildirimi sağla

Ayrıca, ek açıklama ekleyebilir ve verilerinize bağlam sağlayabilirsiniz. Açıklama eklemek için bir zaman aralığı seçin ve açıklamalarınız için metni ekleyin.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Metin tabanlı yorum eklemek için bir zaman aralığı ve kutu ayarlama özelliğine sahip menü":::

## <a name="time-series-batch-feedback"></a>Zaman serisi toplu iş geri bildirimi

Daha önce açıklandığı gibi, geri bildirim kalıcı bir boyut filtresi tarafından tanımlanan bir dizi zaman serisini almak için boyut değerlerini yeniden seçmenize veya kaldırmanıza olanak tanır. Ayrıca, sol panelden geri bildirimde bulunmak için "+" düğmesine tıklayıp Boyutlar ve boyut değerleri ' ni seçerek bunu kalıcı olarak açabilirsiniz.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Word geri bildirimi yanında kırmızı renkli mavi ve işareti vurgulanmış menü":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Dim1 ve Dim2 tarafından belirtilen iki boyut içeren geri bildirim menüsü Ekle":::

## <a name="how-to-view-feedback-history"></a>Geri bildirim geçmişini görüntüleme

Geri bildirim geçmişini görüntülemenin iki yolu vardır. Sol panelden geri bildirim geçmişi düğmesini seçebilirsiniz ve geri bildirim listesinin kalıcı olduğunu görürsünüz. Tek seriler veya boyut filtreleri için daha önce verdiğiniz tüm geri bildirimleri listeler.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Geri bildirim listesi menüsü '":::

Geri bildirim geçmişini görüntülemenin bir başka yolu da bir seridir. Her serinin sağ üst köşesinde birkaç düğme görürsünüz. Geri bildirimi göster düğmesini seçin ve çizgi anomali noktalarından geri bildirim girişlerini gösterecek şekilde değişir. Yeşil bayrak bir değişiklik noktasını temsil eder ve mavi noktaları diğer geri bildirim noktalarıdır. Ayrıca bunları seçebilir ve bu nokta için verilen geri bildirimin ayrıntılarını listeleyen bir geri bildirim listesi kalıcı olur.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Geri bildirim geçmişi grafiği":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="İki boyutlu geri bildirim listesi menüsü":::

> [!NOTE]
> Ölçüme erişimi olan herkesin geri bildirimde bulunmasına izin verilir, bu nedenle diğer veri akışı sahipleri tarafından verilen geri bildirimleri görebilirsiniz. Aynı noktayı başka biriyle düzenlerseniz geri bildiriminiz önceki geri bildirim girişinin üzerine yazılır.       

## <a name="next-steps"></a>Sonraki adımlar
- [Bir olayı tanılayın](diagnose-incident.md).
- [Ölçümleri yapılandırma ve ince ayar algılama yapılandırması](configure-metrics.md)
- [Kanca kullanarak uyarıları yapılandırma ve bildirimler alma](../how-tos/alerts.md)