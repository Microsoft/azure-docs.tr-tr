---
title: Azure Application Insights Power BI dışa aktarma | Microsoft Docs
description: Analiz sorguları Power BI ' de görüntülenebilir.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/10/2018
ms.openlocfilehash: 3ec20f82bc12b60e5b88a02bb92a4a0ada4dcae7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668018"
---
# <a name="feed-power-bi-from-application-insights"></a>Application Insights akış Power BI
[Power BI](https://www.powerbi.com/) , verileri çözümlemenize ve öngörüleri paylaşmanıza yardımcı olan bir iş araçları paketidir. Her cihazda zengin panolar bulunur. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)Analytics sorguları dahil olmak üzere birçok kaynaktaki verileri birleştirebilirsiniz.

Power BI Application Insights verileri vermenin üç yöntemi vardır:

* [**Analiz sorgularını dışarı aktarın**](#export-analytics-queries). Bu, tercih edilen yöntemdir. İstediğiniz herhangi bir sorguyu yazın ve Power BI dışarı aktarın. Bu sorguyu, diğer tüm verilerle birlikte bir panoya yerleştirebilirsiniz.
* [**Sürekli dışarı aktarma ve Azure Stream Analytics**](../../azure-monitor/app/export-stream-analytics.md). Bu yöntem, verilerinizi uzun süreler boyunca depolamak istiyorsanız yararlıdır. Genişletilmiş bir veri saklama gereksinimi yoksa, analiz sorgusunu dışarı aktar yöntemini kullanın. Sürekli dışa aktarma ve Stream Analytics daha fazla çalışma ve ek depolama ek yükü içerir.
* **Power BI bağdaştırıcısı**. Grafik kümesi önceden tanımlanmıştır, ancak diğer kaynaklardan kendi sorgularınızı ekleyebilirsiniz.

> [!NOTE]
> Power BI bağdaştırıcısı artık **kullanım dışıdır**. Bu çözüm için önceden tanımlanmış grafikler statik düzenlenemeyen sorgular tarafından doldurulur. Bu sorguları düzenleyebilme ve verilerinizin belirli özelliklerine bağlı olarak, Power BI bağlantısının başarılı olması, ancak hiçbir veri doldurulmadığı için mümkündür. Bu, sabit kodlanmış sorgu içinde ayarlanan dışlama ölçütlerinden kaynaklanır. Bu çözüm bazı müşteriler için hala çalışabilir, ancak bağdaştırıcı esnekliği olmaması nedeniyle önerilen çözüm, [**analiz sorgusu dışarı aktarma**](#export-analytics-queries) işlevini kullanmaktır.

## <a name="export-analytics-queries"></a>Analiz sorgularını dışarı aktarma
Bu yol, istediğiniz herhangi bir analiz sorgusunu yazmanızı veya kullanım rolalarından dışa aktarıp Power BI panosuna dışarı aktarmayı sağlar. (Bağdaştırıcı tarafından oluşturulan panoya ekleyebilirsiniz.)

### <a name="one-time-install-power-bi-desktop"></a>Bir kez: Power BI Desktop yüklemesi
Application Insights sorgunuzu içeri aktarmak için, Power BI Masaüstü sürümünü kullanırsınız. Daha sonra onu Web 'de veya Power BI bulut çalışma alanınızda yayımlayabilirsiniz. 

[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/)'i yükler.

### <a name="export-an-analytics-query"></a>Analiz sorgusunu dışarı aktarma
1. [Analizi açın ve sorgunuzu yazın](../../azure-monitor/log-query/get-started-portal.md).
2. Sonuçlardan memnun olana kadar sorguyu test edin ve daraltın. Dışarı aktarmadan önce sorgunun Analize doğru çalıştığından emin olun.
3. **Dışarı aktar** menüsünde **Power BI (d)** öğesini seçin. Metin dosyasını kaydedin.
   
    ![Dışa aktarma menüsü vurgulanmış analiz ekran görüntüsü](./media/export-power-bi/analytics-export-power-bi.png)
4. Power BI Desktop **veri al** > **boş sorgu**seçeneğini belirleyin. Ardından, sorgu Düzenleyicisi 'nde, **Görünüm**altında **Gelişmiş Düzenleyici**' yi seçin.

    Dışarıya aktarılmış e-dil betiğini Gelişmiş Düzenleyici yapıştırın.

    ![Gelişmiş Düzenleyici vurgulanmış Power BI Desktop ekran görüntüsü](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Power BI Azure 'a erişmesine izin vermek için kimlik bilgilerini sağlamanız gerekebilir. Microsoft hesabı oturum açmak için **kuruluş hesabını** kullanın.
   
    ![Power BI Sorgu Ayarları iletişim kutusunun ekran görüntüsü](./media/export-power-bi/power-bi-import-sign-in.png)

    Kimlik bilgilerini doğrulamanız gerekiyorsa sorgu Düzenleyicisi 'ndeki **veri kaynağı ayarları** menü komutunu kullanın. Azure için kullandığınız kimlik bilgilerini belirttiğinizden emin olun; bu, Power BI kimlik bilgilerinizle farklı olabilir.
6. Sorgunuz için bir görselleştirme seçin ve x ekseni, y ekseni ve ayırma boyutu alanlarını seçin.
   
    ![Görselleştirme seçeneklerinin Power BI Desktop ekran görüntüsü](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Raporunuzu Power BI bulut çalışma alanınıza yayımlayın. Buradan, eşitlenen bir sürümü diğer Web sayfalarına ekleyebilirsiniz.
   
    ![Yayımla düğmesi vurgulanmış Power BI Desktop ekran görüntüsü](./media/export-power-bi/publish-power-bi.png)
8. Raporu aralıklarla el ile yenileyin veya Seçenekler sayfasında zamanlanmış yenileme ayarlayın.

### <a name="export-a-funnel"></a>Huni dışarı aktarma
1. [Huni yapın](../../azure-monitor/app/usage-funnels.md).
2. **Power BI**seçin.

   ![Power BI düğmesinin ekran görüntüsü](./media/export-power-bi/button.png)

3. Power BI Desktop **veri al** > **boş sorgu**seçeneğini belirleyin. Ardından, sorgu Düzenleyicisi 'nde, **Görünüm**altında **Gelişmiş Düzenleyici**' yi seçin.

   ![Boş sorgu düğmesi vurgulanmış Power BI Desktop ekran görüntüsü](./media/export-power-bi/blankquery.png)

   Dışarıya aktarılmış e-dil betiğini Gelişmiş Düzenleyici yapıştırın. 

   ![Gelişmiş Düzenleyici vurgulanmış Power BI Desktop ekran görüntüsü](./media/export-power-bi/advancedquery.png)

4. Sorgudaki öğeleri seçin ve bir huni görselleştirmesi seçin.

   ![Görselleştirme seçeneklerinin Power BI Desktop ekran görüntüsü](./media/export-power-bi/selectsequence.png)

5. Başlığı, anlamlı olacak şekilde değiştirin ve raporunuzu Power BI bulut çalışma alanınıza yayımlayın. 

   ![Başlık değişikliği vurgulanmış şekilde Power BI Desktop ekran görüntüsü](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Sorun giderme

Kimlik bilgileriyle veya veri kümesinin boyutuyla ilgili hatalarla karşılaşabilirsiniz. Bu hatalar hakkında ne yapacaklarınız hakkında bazı bilgiler aşağıda verilmiştir.

### <a name="unauthorized-401-or-403"></a>Yetkisiz (401 veya 403)
Yenileme belirteciniz güncellenmemişse bu durum oluşabilir. Hala erişiminizin olduğundan emin olmak için aşağıdaki adımları deneyin:

1. Azure portal oturum açın ve kaynağa erişebildiğinizden emin olun.
2. Panonun kimlik bilgilerini yenilemeyi deneyin.
3. PowerBI Desktop 'ınızdan önbelleği temizlemeye çalışın.


   Erişiminiz varsa ve kimlik bilgilerini yenilediğinizde, lütfen bir destek bileti açın.

### <a name="bad-gateway-502"></a>Hatalı ağ geçidi (502)
Bu, genellikle çok fazla veri döndüren bir analiz sorgusunun oluşmasına neden olur. Sorgu için daha küçük bir zaman aralığı kullanmayı deneyin. 

Analiz sorgusundan gelen veri kümesini azaltmak gereksinimlerinizi karşılamıyorsa, daha büyük bir veri kümesi çekmek için [API](https://dev.applicationinsights.io/documentation/overview) 'yi kullanmayı düşünün. Bu, API 'yi kullanmak için e-Query dışarı aktarmanın nasıl dönüştürüleceği aşağıda gösterilmiştir.

1. Bir [API anahtarı](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)oluşturun.
2. Azure Resource Manager URL 'sini Application Insights API 'siyle değiştirerek analizden verdiğiniz Power BI d betiğini güncelleştirin.
   * Https 'yi Değiştir **:\//Management.Azure.com/subscriptions/...**
   * ile, **https:\//api.applicationinsights.io/Beta/Apps/...**
3. Son olarak, kimlik bilgilerini temel olarak güncelleştirin ve API anahtarınızı kullanın.

**Mevcut komut dosyası**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Betik güncelleştirildi**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Örnekleme hakkında
Uygulamanız tarafından gönderilen veri miktarına bağlı olarak, yalnızca telemetriizin yüzdesini gönderen Uyarlamalı örnekleme özelliğini kullanmak isteyebilirsiniz. Örnekleme, SDK veya alma sırasında el ile ayarlamanız durumunda da geçerlidir. [Örnekleme hakkında daha fazla bilgi edinin](../../azure-monitor/app/sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI bağdaştırıcısı (kullanım dışı)
Bu yöntem, sizin için telemetri için tamamen bir pano oluşturur. İlk veri kümesi önceden tanımlanmıştır, ancak buna daha fazla veri ekleyebilirsiniz.

### <a name="get-the-adapter"></a>Bağdaştırıcıyı al
1. [Power BI](https://app.powerbi.com/)oturum açın.
2. Sol alt köşedeki](./media/export-power-bi/001.png), **hizmetlerde** **veri al ![al** simgesinin ekran görüntüsü ' ni açın.

    ![Application Insights veri kaynağından al 'ın ekran görüntüleri](./media/export-power-bi/002.png)

3. Application Insights altında **Şimdi al** ' ı seçin.

   ![Application Insights veri kaynağından al 'ın ekran görüntüleri](./media/export-power-bi/003.png)
4. Application Insights kaynağınızın ayrıntılarını girip **oturum açın**.

    ![Application Insights veri kaynağından al 'ın ekran görüntüsü](./media/export-power-bi/005.png)

     Bu bilgiler Application Insights Genel Bakış bölmesinde bulunabilir:

     ![Application Insights veri kaynağından al 'ın ekran görüntüsü](./media/export-power-bi/004.png)

5. Yeni oluşturulan Application Insights Power BI uygulamasını açın.

6. Verilerin içeri aktarılması için bir dakika veya iki bekleyin.

    ![Power BI bağdaştırıcısının ekran görüntüsü](./media/export-power-bi/010.png)

Panoyu düzenleyebilir, Application Insights grafiklerini diğer kaynaklardaki verilerle ve analiz sorgularıyla birleştiren bir şekilde düzenleyebilirsiniz. Görselleştirme galerisinde daha fazla grafik alabilirsiniz ve her grafiğin ayarlayabileceğiniz parametreleri vardır.

İlk içeri aktarma işleminden sonra Pano ve raporlar her gün güncelleştirilmeye devam eder. Veri kümesindeki yenileme zamanlamasını kontrol edebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [Power BI-öğrenme](https://www.powerbi.com/learning/)
* [Analiz öğreticisi](../../azure-monitor/log-query/get-started-portal.md)

