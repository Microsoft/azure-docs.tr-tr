---
title: Azure Application Insights 'de Kullanıcı Akışları Kullanıcı gezinti desenlerini analiz edin | Microsoft docs
description: Kullanıcıların Web uygulamanızın sayfaları ve özellikleri arasında nasıl gezinmesini analiz edin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b4ebdfa692bb8727556a37698e3a280f40bc1b0f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899477"
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Application Insights Kullanıcı Akışları Kullanıcı gezinti desenlerini analiz etme

![Application Insights Kullanıcı Akışları aracı](./media/usage-flows/00001-flows.png)

Kullanıcı Akışları Araç, kullanıcıların sitenizin sayfaları ve özellikleri arasında nasıl gezineceğini görselleştirir. Şunun gibi sorulara yanıt vermek harika olur:

* Kullanıcılar sitenizdeki bir sayfadan uzaklaşmak ister misiniz?
* Kullanıcılar sitenizdeki bir sayfaya ne tıklasın?
* Kullanıcıların sitenizdeki en iyi şekilde dalgalanma yerleri nerede?
* Kullanıcıların aynı eylemi ve üzerinde yineleneceği konumlar var mı?

Kullanıcı Akışları Aracı, belirttiğiniz ilk sayfa görünümü, özel olay veya özel durumdan başlar. Bu ilk olay verildiğinde, Kullanıcı Akışları Kullanıcı oturumları sırasında ve daha sonra gerçekleşen olayları gösterir. Değişen kalınlıkta çizgiler, her bir yolun Kullanıcı tarafından kaç kez izlendiði gösterilmektedir. Özel **oturum başlatma** düğümleri sonraki düğümlerin bir oturumu başladığını gösterir. **Oturum sona ermiş** düğümler, önceki düğümden sonra kullanıcıların büyük olasılıkla sitenizi nerede kapatmış olduğunu vurgulamada kaç kullanıcının sayfa görünümü veya özel olay gönderdiğini gösterir.

> [!NOTE]
> Application Insights kaynağınız Kullanıcı Akışları aracını kullanmak için sayfa görünümleri veya özel olaylar içermelidir. [Uygulamanızı, Application Insights JavaScript SDK 'sı ile otomatik olarak sayfa görünümlerini toplamak üzere ayarlamayı öğrenin](../../azure-monitor/app/javascript.md).
>
>

## <a name="start-by-choosing-an-initial-event"></a>İlk olay seçerek başlayın

![Kullanıcı Akışları için bir başlangıç olayı seçin](./media/usage-flows/00002-flows-initial-event.png)

Kullanıcı Akışları aracı ile soruları cevaplamak için, görselleştirme için başlangıç noktası olarak kullanılacak bir başlangıç sayfası görünümü, özel olay veya özel durum seçin:

1. **Kullanıcıların yaptığı şeyler...?** başlığı altında bulunan bağlantıya tıklayın veya **Düzenle** düğmesine tıklayın.
2. **İlk olay** açılan listesinden bir sayfa görünümü, özel olay veya özel durum seçin.
3. **Grafik oluştur**' a tıklayın.

Görselleştirmenin "Adım 1" sütunu, kullanıcıların ilk olaydan en çok ne kadar sıklıkla olduğunu, en çok en az sıklıkta sıralı olduğunu gösterir. "2. adım" ve sonraki sütunlarda kullanıcıların, sitenizde gezindiği tüm yollarla bir resim oluşturulması, bundan sonra ne olduğunu gösterir.

Varsayılan olarak Kullanıcı Akışları Aracı, sitenizdeki sayfa görünümlerinin ve özel olayların yalnızca son 24 saatini rastgele olarak örnekler. Düzenleme menüsünde rastgele örnekleme için zaman aralığını artırabilir ve performans ve doğruluk dengelemesini değiştirebilirsiniz.

Sayfa görünümlerinden bazıları, özel olaylar ve özel durumlar sizin için uygun değilse, gizlemek istediğiniz düğümlerdeki **X** öğesine tıklayın. Gizlemek istediğiniz düğümleri seçtikten sonra görselleştirmenin altındaki **grafik oluştur** düğmesine tıklayın. Gizlediğiniz tüm düğümleri görmek için **Düzenle** düğmesine tıklayın ve **Dışlanan olaylar** bölümüne bakın.

Görselleştirme üzerinde görmeyi beklediğinizi sayfa görünümleri veya özel olaylar eksikse:

* **Düzenleme** menüsündeki **Dışlanan olaylar** bölümünü denetleyin.
* Görselleştirmelere daha az sık olaylar eklemek için **diğer** düğümlerdeki artı düğmelerini kullanın.
* Beklenen sayfa görünümü veya özel olay, kullanıcılar tarafından nadiren gönderiliyorsa, **düzenleme** menüsünde görselleştirmenin zaman aralığını artırmayı deneyin.
* İstediğiniz sayfa görünümü, özel olay veya özel durumun, sitenizin kaynak kodunda Application Insights SDK tarafından toplanmaya ayarlandığından emin olun. [Özel olayları toplama hakkında daha fazla bilgi edinin.](../../azure-monitor/app/api-custom-events-metrics.md)

Görselleştirmede daha fazla adım görmek isterseniz, **önceki adımları** ve görselleştirmenin üstündeki **sonraki adımlar açılan adımlarını** kullanın.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Bir sayfa veya özelliği ziyaret ettikten sonra kullanıcılar nerede gider ve neleri tıklasın?

![Kullanıcıların nerede tıkladığınızı anlamak için Kullanıcı Akışları kullanın](./media/usage-flows/00003-flows-one-step.png)

İlk olaylarınız bir sayfa görünümsüzsa, görselleştirmenin ilk sütunu ("Adım 1"), kullanıcıların sayfayı ziyaret ettikten hemen sonra ne olduğunu anlamak için hızlı bir yoldur. Sitenizi Kullanıcı Akışları görselleştirmenin yanındaki bir pencerede açmayı deneyin. Kullanıcıların sayfayla nasıl etkileşime gireceğini, "Adım 1" sütunundaki olay listesine göre karşılaştırın. Genellikle, sayfada ekibiniz için önemli olan bir kullanıcı arabirimi öğesi, sayfada en çok kullanılan öğe arasında olabilir. Sitenize yönelik tasarım geliştirmeleri için harika bir başlangıç noktası olabilir.

İlk olaylarınız özel bir olaydır, ilk sütunda bu eylemi gerçekleştirdikten sonra kullanıcıların ne olduğu gösterilir. Sayfa görünümlerinde olduğu gibi, kullanıcılarınızın gözlemlenen davranışının takımınızın hedefleri ve beklentileri ile eşleşip eşleşmediğini göz önünde bulundurun. Seçtiğiniz ilk olayınızın "alışveriş sepetine öğe eklediniz" olması halinde, örneğin "kullanıma alma" ve "tamamlanmış satın alma" bölümünde, kısa bir süre sonra görselleştirmede görünüp görünmeyeceğini göz atın. Kullanıcı davranışı beklentilerinizi farklılarken, kullanıcıların sitenizin geçerli tasarımı tarafından nasıl "yakalanacağını" öğrenmesini sağlamak için görselleştirmeyi kullanın.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>Kullanıcıların sitenizdeki en iyi şekilde dalgalanma yerleri nerede?

Özellikle bir akışta erken olarak, görselleştirmedeki bir sütunda yüksek olarak görünen **oturum sona ermiş** düğümleri izleyin. Bu, büyük olasılıkla, sayfaların önceki yolu ve Kullanıcı arabirimi etkileşimleri uygulandıktan sonra sitenizdeki çok sayıda kullanıcı tarafından ortaya bir şekilde yapılır. Bazen bir DUYM sitesinde satın alma işlemi tamamlandıktan sonra karmaşıklık beklenir. Örneğin, genellikle karmaşıklık, Tasarım sorunları, kötü performans veya sitenizdeki diğer sorunlar için iyileştirilen bir oturum açma işlemi olur.

**Oturum sona ermiş** düğümlerin yalnızca bu Application Insights kaynağı tarafından toplanan telemetri temelinde olduğunu aklınızda bulundurun. Application Insights, bazı kullanıcı etkileşimleri için telemetri almazsa, Kullanıcı Akışları aracı oturum sona erdikten sonra bu yollarla, kullanıcılar yine de siteyle etkileşim kazanabilir.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>Kullanıcıların aynı eylemi ve üzerinde yineleneceği konumlar var mı?

Görselleştirmedeki sonraki adımlarda birçok kullanıcı tarafından yinelenen bir sayfa görünümü veya özel olay bulun. Bu, genellikle kullanıcıların sitenizde yinelenen eylemler gerçekleştirme anlamına gelir. Tekrarlamayı bulursanız sitenizin tasarımını değiştirme veya yinelemeyi azaltmak için yeni işlevsellik ekleme hakkında düşünün. Örneğin, bir tablo öğesinin her satırında yinelenen eylemler gerçekleştiren kullanıcılar bulursanız toplu düzenleme işlevselliği ekleme.

## <a name="common-questions"></a>Sık sorulan sorular

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>İlk olay, olay bir oturumda veya bir oturumda göründüğü zaman ilk kez temsil ediyor mu?

Görselleştirmedeki ilk olay, bir Kullanıcı bir oturum sırasında Bu sayfa görünümü veya özel olay ilk kez gönderildiğinde temsil eder. Kullanıcılar ilk olayı bir oturumunda birden çok kez gönderebiliyorsanız, "1. adım" sütununda yalnızca kullanıcıların ilk olayın *ilk* örneğinden sonra nasıl davrandığını gösterir, tüm örnekler değildir.

### <a name="some-of-the-nodes-in-my-visualization-are-too-high-level-for-example-a-node-that-just-says-button-clicked-how-can-i-break-it-down-into-more-detailed-nodes"></a>Görselleştirmedeki bazı düğümler çok yüksek düzeydir. Örneğin, yalnızca "düğme tıkladığını" belirten bir düğüm. Daha ayrıntılı düğümlere nasıl bölünabilirim?

**Düzenleme** menüsündeki **bölünmüş** seçenekleri kullanın:

1. **Olay** menüsünde bölmek istediğiniz olayı seçin.
2. **Boyut** menüsünde bir boyut seçin. Örneğin, "düğme tıklandı" adlı bir olaytınız varsa, "düğme adı" adlı özel bir özellik deneyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanıma genel bakış](usage-overview.md)
* [Kullanıcılar, Oturumlar ve Etkinlikler](usage-segmentation.md)
* [Bekletme](usage-retention.md)
* [Uygulamanıza özel olaylar ekleme](../../azure-monitor/app/api-custom-events-metrics.md)