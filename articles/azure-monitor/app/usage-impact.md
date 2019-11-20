---
title: Azure Application Insights kullanımı etkisi | Microsoft docs
description: Farklı özelliklerin uygulamalarınızın parçaları için dönüştürme hızlarını nasıl etkileyebileceğini analiz edin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: fffe71cb80be7795201ab672ca632788f4f18e5c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899456"
---
# <a name="impact-analysis-with-application-insights"></a>Application Insights ile etki analizi

Etki, yük sürelerinin ve diğer özelliklerin uygulamanızın çeşitli bölümleri için dönüştürme oranlarını nasıl etkilediğini analiz eder. Daha hassas bir şekilde koymak için, **sayfa görünümü**, **özel olay**veya **istek** için **herhangi bir boyutun** farklı bir **sayfa görünümü** veya **özel olay**kullanımını nasıl etkilediğini bulur. 

![Etki aracı](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Hangi etkiyi gerçekleştirdiğinizden hala emin değil misiniz?

Etkileri düşünmenin bir yolu, takımınızın bazı yönlerine yönelik olan bağımsız değişkenleri, kullanıcıların dolaşıp gezinmediğini nasıl etkilediğini öğrenmek için en son araçtır. Kullanıcılar belirli bir yavaşlığa göre tolerans sağlarken, etki iyileştirme ve performansın en iyi şekilde ne kadar süre içinde Kullanıcı dönüştürmeyi en üst düzeye çıkarabilme konusunda fikir verir.

Ancak performansın çözümlenmesi yalnızca bir etki alanının özellik alt kümesidir. Etki özel olayları ve boyutları desteklediğinden, kullanıcı tarayıcısı seçiminin farklı dönüştürme ücretleri ile ilişkilendirilmesi gibi sorulara yanıt vermek yalnızca birkaç tıklamayla daha fazla tıklama.

![Tarayıcılar tarafından ekran görüntüsü dönüştürme](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> Application Insights kaynağınız, etki aracını kullanmak için sayfa görünümleri veya özel olaylar içermelidir. [Uygulamanızı, Application Insights JavaScript SDK 'sı ile otomatik olarak sayfa görünümlerini toplamak üzere ayarlamayı öğrenin](../../azure-monitor/app/javascript.md). Ayrıca, bağıntı çözümlenirken örnek boyutu önemli olduğundan emin olmak için aklınızda bulundurun.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>Sayfa yükleme süresi sayfamda kaç kişinin dönüştürülmesini etkiliyor mu?

Etki aracı ile soruları cevaplamak için bir başlangıç sayfa görünümü, özel olay veya istek seçin.

![Etki aracı](./media/usage-impact/0002-dropdown.png)

1. **Sayfa görünümü** açılır listesinden bir sayfa görünümü seçin.
2. Varsayılan **süre** seçiminde (Bu bağlam **süresinde** , **sayfa yükleme süresi**için bir diğer ad) açılan listesini **Çözümle** ' ye bırakın.
3. , Açılan menü **kullanımını etkiler** için bir özel olay seçin. Bu olay, 1. adımda seçtiğiniz sayfa görünümündeki bir UI öğesine karşılık gelmelidir.

![Sonuçların ekran görüntüsü](./media/usage-impact/0003-results.png)

**Ürün sayfası** yükleme süresi, bu örnekte, **satın alma ürününe tıklanan** dönüştürme oranını artırır. Yukarıdaki dağıtıma bağlı olarak, 3,5 saniyelik en iyi sayfa yükleme süresi, %55 oranında bir dönüştürme ücreti elde etmek için hedeflenebilir. 3,5 saniyelik yük süresini azaltmaya yönelik daha fazla performans geliştirmesi, şu anda ek dönüştürme avantajlarıyla bağıntılı değildir.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Sayfa görünümlerini veya yükleme zamanlarını özel şekillerde izlersem ne yapmalıyım?

Etki, hem standart hem de özel özellikleri ve ölçümleri destekler. İstediğiniz her şeyi kullanın. Süre yerine, daha belirgin bir şekilde yararlanmak için birincil ve ikincil olaylarda filtreler kullanın.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Farklı ülkelerden veya bölgelerden kullanıcılar farklı oranlarda mi dönüştürülecek?

1. **Sayfa görünümü** açılır listesinden bir sayfa görünümü seçin.
2. Açılan menüsünü **analiz etme** bölümünde "ülke veya bölge" yi seçin
3. , Açılan menü **kullanımını etkiler** için, 1. adımda seçtiğiniz sayfa GÖRÜNÜMÜNDEKI bir UI öğesine karşılık gelen özel bir olay seçin.

Bu durumda, sonuçlar artık ilk örnekte olduğu gibi sürekli bir x ekseni modeline sığmıyor. Bunun yerine, kesimli bir huniye benzer bir görselleştirme sunulur. Ülkeye/bölgeye göre özel olayınızın dönüştürmesinin çeşitlemesini görüntülemek için **kullanıma** göre sıralayın.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Etki aracı bu dönüştürme oranlarını nasıl hesaplar?

Bu, Impact Aracı, [Pearson bağıntı katsayısını](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)kullanır. Sonuçlar-1 ile 1 arasında, negatif bir doğrusal bağıntıyı temsil eden 1 ile 1 arasında hesaplanır ve pozitif bir doğrusal bağıntıyı temsil eder.

Etki çözümlemenin nasıl çalıştığına ilişkin temel döküm aşağıdaki gibidir:

Let _A_ = ilk açılan pencerede seçtiğiniz ana sayfa görünümü/özel olay/istek. (**Sayfa görünümü için**).

Let _B_ = seçtiğiniz ikincil sayfa görünümü/özel olay (**kullanımını etkiler**).

Etki, seçilen zaman aralığındaki kullanıcılardan gelen tüm oturumların bir örneğine bakar. Her bir oturum için, her _bir bir_' ın her oluşumunu arar.

Oturumlar daha sonra İki koşuldan birine göre iki farklı _alt oturum_ türüne ayrılır:

- Dönüştürülen bir alt oturum, _b_ olayından biten bir oturumdan oluşur ve _b_'den önce oluşan _tüm olayları_ kapsar.
- Dönüştürülmeyen _bir_alt oturum, _B_terminali olmadan meydana geldiğinde oluşur.

Etkili bir şekilde hesaplama, ölçüm veya boyuta göre analiz edilip etmediğimiz temel alınarak değişir. Bir alt oturumdaki tüm _A_içindeki ölçümler için ortalaması alınır. Yani, her _bir_ katkıda bulunan _1/N_ değerini _B_ 'ye atanan değere göre boyutlar için _n_ , alt oturumdaki _A_'nın sayısıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Kullanım deneyimlerini etkinleştirmek için [özel olaylar](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) veya [sayfa görünümleri](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)göndermeye başlayın.
- Özel olayları veya sayfa görünümlerini zaten gönderirseniz, kullanıcıların hizmetinizi nasıl kullandığını öğrenmek için kullanım araçları ' nı araştırın.
    - [Huniler](usage-funnels.md)
    - [Bekletme](usage-retention.md)
    - [Kullanıcı Akışları](usage-flows.md)
    - [Çalışma kitapları](../../azure-monitor/app/usage-workbooks.md)
    - [Kullanıcı bağlamı Ekle](usage-send-user-context.md)