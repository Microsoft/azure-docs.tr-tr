---
title: Azure Application Insights ile Hızlı Başlangıç | Microsoft Docs
description: Application Insights ve App Center izleme için bir mobil uygulamayı hızlı bir şekilde ayarlamaya yönelik yönergeler sağlar
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.reviewer: daviste
ms.custom: mvc
ms.openlocfilehash: fe514e7a05701901759ab5453e1c1d02870da91d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900545"
---
# <a name="start-analyzing-your-mobile-app-with-app-center-and-application-insights"></a>Mobil uygulamanızı App Center ve Application Insights ile analiz etmeye başlama

Bu hızlı başlangıç, uygulamanızın App Center örneğini Application Insights'a bağlama işleminde size yol gösterir. Application Insights ile, telemetrinizi App Center'ın [Analytics](https://docs.microsoft.com/mobile-center/analytics/) hizmetinde sağlanandan daha güçlü araçlarla sorgulayabilir, segmentlere ayırabilir, filtreleyebilir ve analiz edebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için şunlar gerekir:

- Azure aboneliği.
- iOS, Android, Xamarin, Evrensel Windows veya React Native uygulaması.
 
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-up-with-app-center"></a>App Center kaydolun
Başlamak için bir hesap oluşturun ve [App Center kaydolun](https://appcenter.ms/signup?utm_source=ApplicationInsights&utm_medium=Azure&utm_campaign=docs).

## <a name="onboard-to-app-center"></a>App Center'a ekleme

Application Insights'ı mobil uygulamanızla kullanabilmeniz için, önce uygulamanızı [App Center](https://docs.microsoft.com/mobile-center/)'a eklemelisiniz. Application Insights doğrudan mobil uygulamanızdan telemetri almaz. Bunun yerine, uygulamanız özel olay telemetrisini App Center'a gönderir. Ardından, App Center olaylar alındıkça bu özel olayların kopyalarını sürekli Application Insights'a aktarır. (Bu, [APPLICATION INSIGHTS js SDK 'sı](https://github.com/Microsoft/ApplicationInsights-JS) için veya Telemetriyi doğrudan Application Insights ' e gönderilen [tepki verme yerel eklentisine](https://github.com/Microsoft/ApplicationInsights-JS/tree/master/vNext/extensions/applicationinsights-react-native) uygulanmaz.)

Uygulamanızı eklemek için, uygulamanızın desteklediği her platform için App Center hızlı başlangıç yönergelerini izleyin. Her platform için ayrı App Center örnekleri oluşturun:

* [iOS](https://docs.microsoft.com/mobile-center/sdk/getting-started/ios).
* [Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/android).
* [Xamarin](https://docs.microsoft.com/mobile-center/sdk/getting-started/xamarin).
* [Universal Windows](https://docs.microsoft.com/mobile-center/sdk/getting-started/uwp).
* [React Native](https://docs.microsoft.com/mobile-center/sdk/getting-started/react-native).

## <a name="track-events-in-your-app"></a>Uygulamanızda olayları izleme

Uygulamanız App Center'a eklendikten sonra, App Center SDK'sını kullanarak özel olay telemetrisi gönderecek şekilde değiştirilmesi gerekir. Application Insights'a aktarılan tek App Center telemetrisi türü, özel olaylardır.

iOS uygulamalarından özel olayları göndermek için App Center SDK'sında `trackEvent` veya `trackEvent:withProperties` yöntemlerini kullanın. [iOS uygulamalarındaki olayları izleme hakkında daha fazla bilgi edinin.](https://docs.microsoft.com/mobile-center/sdk/analytics/ios)

```Swift
MSAnalytics.trackEvent("Video clicked")
```

Android uygulamalarından özel olayları göndermek için App Center SDK'sında `trackEvent` yöntemini kullanın. [Android uygulamalarındaki olayları izleme hakkında daha fazla bilgi edinin.](https://docs.microsoft.com/mobile-center/sdk/analytics/android)

```Java
Analytics.trackEvent("Video clicked")
```

Diğer uygulama platformlarından özel olayları göndermek için, App Center SDK'larında `trackEvent` yöntemlerini kullanın.

Özel olaylarınızın alındığından emin olmak için, App Center'daki **Analiz** bölümünün altında **Olaylar** sekmesine gidin. Uygulamanızdan gönderilen olayların gösterilmesi birkaç dakika sürebilir.

## <a name="create-an-application-insights-resource"></a>Application Insights kaynağı oluşturma

Uygulamanız özel olayları gönderdikten ve bunlar App Center tarafından alındıktan sonra, Azure Portal'da App Center türünde bir Application Insights kaynağı oluşturmalısınız:

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.
2. **Kaynak oluştur** > **Geliştirici araçları** > **Application Insights** seçeneğini belirleyin.

    > [!NOTE]
    > İlk kez bir Application Insights kaynağı oluşturuyorsanız [Application Insights kaynak oluştur belge oluştur](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) ' a giderek daha fazla bilgi edinebilirsiniz.

    Yapılandırma kutusu görüntülenir. Giriş alanlarını doldurmak için aşağıdaki tabloyu kullanın.

    | Ayarlar        |  Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Adı**      | Genel olarak benzersiz bir değer, örneğin "Uygulamam-iOS" | İzlemekte olduğunuz uygulamayı tanımlayan ad |
     | **Kaynak Grubu**     | Yeni bir kaynak grubu veya menüde var olanlardan biri | İçinde yeni Application Insights kaynağının oluşturulacağı kaynak grubu |
   | **Konum** | Menüden bir konum | Yakınınızda bulunan veya uygulamanızın barındırıldığı konumun yakınında olan bir konum seçin |

3. **Oluştur**’a tıklayın.

Uygulamanız birden çok platformu (iOS, Android, vb.) destekliyorsa, en iyisi her platform için ayrı bir Application Insights kaynağı oluşturmaktır.

## <a name="export-to-application-insights"></a>Application Insights'a aktarma

Yeni Application Insights kaynağında **genel bakış** sayfasında. İzleme anahtarını kaynağından kopyalayın.

Uygulamanızın [App Center](https://appcenter.ms/) örneğinde:

1. **Ayarlar** sayfasında **Dışarı Aktar**'a tıklayın.
2. **Yeni Dışarı Aktarma**'yı seçin, **Application Insights**'ı seçin ve ardından **Özelleştir**'e tıklayın.
3. Application Insights izleme anahtarınızı kutuya yapıştırın.
4. Application Insights kaynağınızı içeren Azure aboneliğinin kullanımını artırmayı onaylayın. Her Application Insights kaynağı için, her ay alınan ilk 1 GB veri ücretsizdir. [Application Insights fiyatlandırması hakkında daha fazla bilgi edinin.](https://azure.microsoft.com/pricing/details/application-insights/)

Uygulamanızın desteklediği her platformda bu işlemi yinelemeyi unutmayın.

[Dışarı aktarma](https://docs.microsoft.com/mobile-center/analytics/export) ayarlandıktan sonra, App Center tarafından alınan her özel olay Application Insights'a kopyalanır. Olayların Application Insights'a ulaşması birkaç dakika sürebileceğinden, olaylar hemen görüntülenmezse başka tanılama işlemlerine geçmeden önce biraz bekleyin.

İlk bağlandığınızda size daha fazla veri vermek için, App Center'daki son 48 saatin özel olayları otomatik olarak Application Insights'a aktarılır.

## <a name="start-monitoring-your-app"></a>Uygulamanızı izlemeyi başlatma

Application Insights, uygulamalarınızın özel olay telemetrisini App Center'ın sağladığı analiz araçlarının çok ötesinde sorgulayabilir, segmentlere ayırabilir, filtreleyebilir ve analiz edebilir.

1. **Özel olay telemetrinizi sorgulayın.** Application Insights **genel bakış** sayfasında **Günlükler (Analiz)** öğesini seçin.

   Application Insights kaynağınız ile ilişkili Application Insights günlükleri (Analiz) portalı açılır. Günlükler (Analiz) portalı Log Analytics sorgu dilini kullanarak verilerinizi doğrudan sorgulamanıza olanak tanıyarak, uygulamanız ve kullanıcılarınızla ilgili rastgele karmaşık sorular sorabilirsiniz.
   
   Günlükler (Analiz) portalında yeni bir sekme açın, sonra aşağıdaki sorguyu yapıştırın. Son 24 saat içinde uygulamanızdan her özel olayı ayrı ayrı kaç kullanıcının gönderdiğini, bu ayrı sayımlara göre sıralanmış olarak döndürür.

   ```AIQL
   customEvents
   | where timestamp >= ago(24h)
   | summarize dcount(user_Id) by name 
   | order by dcount_user_Id desc 
   ```

   ![Günlükler (Analiz) portalı](./media/mobile-center-quickstart/analytics-portal-001.png)

   1. Metin düzenleyicisinde sorgunun herhangi bir yerine tıklayarak sorguyu seçin.
   2. Ardından, **Git**'e tıklayarak sorguyu çalıştırın. 

   [Application Insights Analytics](../../azure-monitor/app/analytics.md) ve [Log Analytics sorgu dili](https://aka.ms/LogAnalyticsLanguageReference) hakkında daha fazla bilgi edinin.


2. **Özel olay telemetrinizi segmentlere ayırın ve filtreleyin.** Application Insights **Genel Bakış** sayfasında, içindekiler tablosundan **Kullanıcılar**'ı seçin.

   ![Kullanıcılar aracı simgesi](./media/mobile-center-quickstart/users-icon-001.png)

   Kullanıcılar aracı, belirli düğmelere tıklayan, belirli ekranları ziyaret eden veya App Center SDK'sıyla olay olarak izlediğiniz başka herhangi bir eylemi gerçekleştiren uygulama kullanıcılarınızın sayısını gösterir. App Center olaylarınızı segmentlere ayırmanın ve filtrelemenin bir yolunu arıyorsanız, Kullanıcılar aracı harika bir seçenektir.

   ![Kullanıcılar aracı](./media/mobile-center-quickstart/users-001.png) 

   Örneğin, **Bölme ölçütü** açılan menüsünde **Ülke veya bölge**'yi seçerek kullanımınızı bölgelere göre segmentlere ayırın.

3. **Uygulamanızdaki dönüştürme, elde tutma ve gezinme desenlerini analiz edin.** Application Insights **Genel Bakış** sayfasında, içindekiler tablosundan **Kullanıcı Akışları**'nı seçin.

   ![Kullanıcı Akışları aracı](./media/mobile-center-quickstart/user-flows-001.png)

   Kullanıcı Akışları aracı bazı başlangıç olaylarından sonra kullanıcıların gönderdiği olayları görselleştirir. Kullanıcıların uygulamanızdaki gezintilerinin genel bir görünümünü elde etme açısından yararlı olur. Ayrıca, kullanıcıların uygulamanızdan ayrıldığı veya aynı eylemleri tekrar tekrar kullandığı yerleri ortaya koyabilir.

   Kullanıcı Akışları'na ek olarak, Application Insights'ta belirli soruları yanıtlamak için başka kullanıcı davranış analizi araçları da vardır:

   * **Huniler**, dönüştürme oranlarını analiz etmek ve izlemek için kullanılır.
   * **Elde tutma**, uygulamanızın zaman içinde kullanıcılarını ne düzeyde elinde tutabildiğini analiz etmek için kullanılır.
   * **Çalışma kitapları**, görsellerle metinleri paylaşılabilir bir raporda bir araya getirmek için kullanılır.
   * **Kohortlar**, başka analiz araçlarından kolayca başvuruda bulunabilmek amacıyla belirli kullanıcı veya olay gruplarını adlandırmak ve kaydetmek için kullanılır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Application Insights'ı App Center ile kullanmaya devam etmek istemiyorsanız, App Center'da dışarı aktarmayı kapatın ve Application Insights kaynağını silin. Bu işlem, Application Insights'ın bu kaynak için sizi daha fazla ücretlendirmesini önler.

App Center'da dışarı aktarmayı kapatmak için:

1. App Center'da **Ayarlar**'a gidin ve **Dışarı Aktar**'ı seçin.
2. Silmek istediğiniz Application Insights dışarı aktarmasına tıklayın, sonra da alt kısımdaki **Dışarı aktarmayı sil**'e tıklayın ve onaylayın.

Application Insights kaynağını silmek için:

1. Azure Portal'ın sol menüsünde **Kaynak grupları**'na tıklayın ve ardından içinde Application Insights kaynağınızın oluşturulduğu kaynak grubunu seçin.
2. İstediğiniz Application Insights kaynağını açın ve silin. Sonra, kaynağın üstteki menüsünde **Sil**'e tıklayın ve onaylayın. Bu işlem, verilerin Application Insights'a aktarılan kopyasını kalıcı olarak siler.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Müşterilerin uygulamanızı nasıl kullandığını anlama](../../azure-monitor/app/usage-overview.md)
