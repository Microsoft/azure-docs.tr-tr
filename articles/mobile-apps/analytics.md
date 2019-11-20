---
title: Visual Studio App Center ve Azure hizmetleriyle mobil uygulama kullanımını ve Kullanıcı davranışını anlayın
description: Kullanıcıların mobil uygulamanızı nasıl kullandığını anlamak için akıllı iş kararları almanıza yardımcı olan App Center gibi hizmetler hakkında bilgi edinin.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: d60e9437a2f33788eb183cfcad0f3a10d71fb79d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795802"
---
# <a name="analyze-and-understand-usage-of-mobile-applications"></a>Mobil uygulamaların kullanımını çözümleyin ve anlayın
Kullanıcılarınızın uygulamalarınızı nasıl kullandığını ne kadar iyi anlamış olursunuz? Uygulamanızda kaç tane etkin kullanıcı var ve zaman içinde kullanım nasıl değişiyor? Hangi özellikleri kullanıyor ve hangilerinin en çok kullanıldığı. Bu kullanıcılar nerede? Uygulamanın en son sürümünü kaç Kullanıcı kullanıyor? Bu soruların tümü, uygulamanızı başarılı bir işletmeye dönüştürmek için anlaşılması önemlidir. Bu tür kullanım analizi sorularını yanıtlamak için, uygulamalarınızdan kullanım verilerini toplamanız gerekir.

Verilere daha fazla baktığınız ne kadar fazla olduğunda, uygulamanızı geliştirmenin ve kullanıcılarınızı kutlu tutmanız için de daha fazla yol bulabilirsiniz. Eyleme dönüştürülebilir içgörüler bulmak ve kullanıcıları mutlu tutmak için verilerin kullanılması önemlidir.

## <a name="importance-of-analytics"></a>Analiz önemi
- Kullanıcılarınızı, uygulamanızla nasıl etkileşime gireceğini, uygulamanızı daha iyi ayarlamaya ve işinizi büyütmeye yönelik harika deneyimler sağlamanıza göre **anlayın** .
- Uygulamanızı pazara sunma ve müşterinize daha iyi sunma hakkında bilinçli kararlar almak için kullanım ölçümlerini **izleyin** .
- Uygulama performansınızı **ölçün** .
- Uygulamanızın hangi bölümlerinin değer ve performans **sağladığını öğrenin** .
- Karmaşıklık ve bekletme ile ilgili sorunlar hakkında **veri odaklı Öngörüler** .

Mobil uygulama analizlerini etkinleştirmek için aşağıdaki hizmetleri kullanın.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Analytics](/appcenter/analytics/) , Kullanıcı oturumları, en iyi cihazlar, işletim sistemi sürümleri ve davranış analizleri hakkında ayrıntılı raporlama ve Öngörüler ile önemli öneme odaklanarak hedef kitlenizi büyütmenize olanak tanır. Kapsamlı uygulama analiziyle her şeyi izlemek için kolayca özel olaylar oluşturun.

   **Temel Özellikler**
   - Kullanım düzenlerini, Kullanıcı **benimsemeyi** ve diğer katılım ölçümlerini ücretsiz izleyin.
   - Özel olaylar aracılığıyla eğilimleri, Kullanıcı davranışını ve katılımı **belirler** .
   - **Tek bir panoda** **kullanıma hazır ölçümler** ve uygulama kullanımı (günlük, haftalık, aylık), oturum, cihaz özellikleri ve Kullanıcı demografları hakkında **ayrıntılı Öngörüler** .
   - Sınırsız saklama için **tüm analiz verilerinizi sürekli olarak Azure 'a aktarın** . Azure Blob depolama ve Azure Application Insights dışa aktarmayı destekler.
   - Bekletme, huni Analizi ve cohortalar gibi daha ayrıntılı bilgiler için **Azure Application Insights Ile tümleştirin**
   - Dakikalar içinde kullanmaya başlamak için **tek SATıRLıK SDK tümleştirmesi** .
   - **Platform desteği** -IOS, Android, MacOS, tvOS, Xamarin, tepki veren yerel, Unity, Cordova.

   **Başvur**
   - [App Center kaydolun](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
   - [App Center Analytics ile çalışmaya başlama](/appcenter/analytics/)

## <a name="azure-monitor"></a>Azure İzleyici
Azure Izleyici, performansı en üst düzeye çıkarmak ve mobil uygulamanızı izlemek için telemetri toplama ve çözümleme araçları sağlayan [Application Insights](/azure/azure-monitor/app/app-insights-overview) içerir. App Center Analytics 'i kullanarak Application Insights avantajlarından yararlanabilir ve Application Insights 'e dışarı aktarma ayarı yapabilirsiniz. Application Insights, uygulamalarınızdan özel olay telemetrisini sorgulayabilir, kaydedebilir, filtreleyebilir ve analiz araçlarının ötesinde App Center sağlar.

**Temel Özellikler**
   - Özel olay telemetrinizi **sorgulayın** .
   - Güçlü segmentasyon özelliklerine sahip olay telemetrisini **filtreleyin** .
   - Uygulamanızda dönüştürme, bekletme ve gezinme düzenlerini **çözümleyin** .
     - **Huniler**, dönüştürme oranlarını analiz etmek ve izlemek için kullanılır.
     - Uygulamanızın zaman içindeki kullanıcıları ne kadar iyi koruduğunu çözümlemek için **bekletme** .
     - **Çalışma kitapları**, görsellerle metinleri paylaşılabilir bir raporda bir araya getirmek için kullanılır.
     - **Kohortlar**, başka analiz araçlarından kolayca başvuruda bulunabilmek amacıyla belirli kullanıcı veya olay gruplarını adlandırmak ve kaydetmek için kullanılır.

**Başvur**
- [Azure portalda](https://portal.azure.com/)
- [App Center ve Application Insights mobil uygulamanızı çözümleyin](/azure/azure-monitor/learn/mobile-center-quickstart)
- [Application Insights ile kullanım Analizi](/azure/azure-monitor/app/usage-overview)

## <a name="azure-playfab"></a>Azure PlayFab
[Azure PlayFab](https://playfab.com/) , dünya çapındaki bulut bağlantılı Oyunlar oluşturmanız için gereken oyun Hizmetleri, gerçek zamanlı analiz ve LiveOps ile tam bir arka uç platformu sunar. Bu hizmetler oyun geliştiricileri için başlatma sınırlamalarını azaltarak, oyunlarıyla ölçeklenebilen büyük ve küçük Studios uygun maliyetli geliştirme çözümleri sunar. PlayFab, geliştiricilerin oyunları oluşturmak ve çalıştırmak, oyun verilerini çözümlemek ve genel oyun deneyimlerini geliştirmek için akıllı bulutu kullanmasını sağlar.

**Temel Özellikler**
   - Gerçek zamanlı panoları **izleyin** .
   - En üst ölçümler aracılığıyla oyununuzun performansını **değerlendirin** .
   - Oyun Yöneticisi 'nde görüntülenebilen, otomatik olarak oluşturulan raporlar aracılığıyla oyununuzun özetlerini günlük ve aylık performanslarını gözden geçirmek için **rapor** edin.
   - **A/B** denemeleri çalıştırmak ve belirli bir değişken için en uygun ayarı öğrenmek için test etme.
   - Oyuncuların **Segmentlenmesi** , otomatik oyuncu gruplandırmaları tanımlamanızı sağlar.
    
**Başvur**
- [PlayFab portalı](https://developer.playfab.com/en-US/sign-up)
- [Analizler](/gaming/playfab/#pivot=documentation&panel=analytics)
- [Hızlı başlangıçlar](/gaming/playfab/#pivot=documentation&panel=quickstarts)    
