---
title: Azure Application Insights ile Hızlı Başlangıç | Microsoft Docs
description: Bir ASP.NET Core Web uygulamasını izleme için hızlı bir şekilde ayarlamak için yönergeler sağlar Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ec39380409d5170efb56530c86a41d934e41728a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900579"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>ASP.NET Core Web Uygulamanızı İzlemeye Başlama

Azure Application Insights ile web uygulamanızı kullanılabilirlik, performans ve kullanım bakımından kolayca izleyebilirsiniz. Ayrıca, bir kullanıcının bildirmesini beklemeden uygulamanızdaki hataları hızlıca tanımlayıp tespit edebilirsiniz. 

Bu hızlı başlangıç, var olan bir ASP.NET Core Web uygulamasına Application Insights SDK ekleme konusunda size rehberlik eder. Visual Studio 'Yu Application Insights yapılandırma hakkında bilgi edinmek için bu [makaleye](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)göz atın.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için:

- Aşağıdaki iş yükleriyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) ' i yükledikten sonra:
  - ASP.NET ve web geliştirme
  - Azure geliştirme
- [.NET Core 2.0 SDK yükleme](https://www.microsoft.com/net/core)
- Bir Azure Aboneliği ve var olan bir .NET Core web uygulaması gerekir.

ASP.NET Core Web uygulamanız yoksa, [ASP.NET Core bir uygulama oluşturmak ve Application Insights eklemek](../../azure-monitor/app/asp-net-core.md) için adım adım kılavuzumuzu kullanabilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure Portal](https://portal.azure.com/)’ında oturum açın.

## <a name="enable-application-insights"></a>Application Insights'ı etkinleştirme

Application Insights, şirket içinde veya bulutta çalışmasından bağımsız olarak İnternet’e bağlı herhangi bir uygulamadan telemetri verilerini toplayabilir. Bu verileri görüntülemeyi başlatmak için aşağıdaki adımları kullanın.

1. **Kaynak oluştur** > **Geliştirici araçları** > **Application Insights** seçeneğini belirleyin.

   > [!NOTE]
   >İlk kez bir Application Insights kaynağı oluşturuyorsanız [Application Insights kaynak oluştur belge oluştur](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) ' a giderek daha fazla bilgi edinebilirsiniz.

    Bir yapılandırma kutusu görünür. Giriş alanlarını doldurmak için aşağıdaki tabloyu kullanın.

   | Ayarlar        |  Değer           | Açıklama  |
   | ------------- |:-------------|:-----|
   | **Adı**      | Genel Olarak Benzersiz Değer | İzlemekte olduğunuz uygulamayı tanımlayan ad |
   | **Kaynak Grubu**     | myResourceGroup      | App Insights verilerini barındıran yeni kaynak grubunun adı |
   | **Konum** | Doğu ABD | Yakınınızda bulunan veya uygulamanızın barındırıldığı konumun yakınında olan bir konum seçin |

2. **Oluştur**’a tıklayın.

## <a name="configure-app-insights-sdk"></a>App Insights SDK’sını Yapılandırma

1. ASP.NET Core Web Uygulaması **projenizi** Visual Studio’da açın > **Çözüm Gezgini**’nde AppName öğesne sağ tıklayın > **Ekle** > **Application Insights Telemetrisi**’ni seçin.

    ![Application Insights Telemetrisi ekleme](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. **Başlarken** düğmesine tıklayın

3. Hesabınızı ve aboneliğinizi seçin > Azure portal oluşturduğunuz **mevcut kaynağı** seçin > **Kaydet**' e tıklayın.

4. **Proje** seçin > **NuGet Paketlerini Yönet** > **paket kaynağı: NuGet.org** > Application Insights SDK paketlerini en son kararlı sürüme **güncelleştirin** .

5. Uygulamanızı başlatmak için **Hata Ayıkla** > **Hata Ayıklamadan Başlat** (Ctrl+F5) öğesini seçin

    ![Application Insights’a Genel Bakış Menüsü](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Verilerin portalda görünmesi 3-5 dakika sürer. Bu uygulama düşük trafikli bir test uygulaması ise, çoğu ölçümün yalnızca etkin istek veya işlem olduğunda yakalandığını aklınızda bulundurun.

## <a name="start-monitoring-in-the-azure-portal"></a>Azure portalında izlemeyi başlatma

1. **Ana** sayfa ' yı seçerek Azure Portal Application Insights **genel bakış** sayfasını yeniden açın ve son zamanlarda, çalışmakta olan uygulamanız hakkındaki ayrıntıları görüntülemek için daha önce oluşturduğunuz kaynağı seçin.

   ![Application Insights’a Genel Bakış Menüsü](./media/dotnetcore-quick-start/4overview.png)

2. Uygulama bileşenleriniz arasındaki bağımlılık ilişkilerinin görsel düzeni için **Uygulama haritası**’na tıklayın. Her bileşen yük, performans, hatalar ve uyarılar gibi KPI'leri gösterir.

   ![Uygulama Eşlemesi](./media/dotnetcore-quick-start/5appmap.png)

3. Application **Analytics** simgesine ![uygulama Haritası simgesine tıklayın ve **Analiz bölümünde](./media/dotnetcore-quick-start/006.png) görüntüleyin**. Bu işlem, Application Insights tarafından toplanan tüm verileri analiz etmeye yönelik zengin bir sorgu dili sağlayan **Application Insights Analizi**’ni açar. Bu örnekte, istek sayısını grafik olarak işleyen bir sorgu oluşturulur. Diğer verileri çözümlemek için kendi sorgularınızı yazabilirsiniz.

   ![Belirli bir süre içindeki kullanıcı isteklerinin analiz grafiği](./media/dotnetcore-quick-start/6analytics.png)

4. **Genel bakış** sayfasına dönün ve KPI panolarını inceleyin.  Bu pano, gelen istek sayısı, bu isteklerin süresi ve oluşan hatalar dahil olmak üzere uygulamanızın sistem durumu hakkında istatistikler sağlar. 

   ![Sistem Durumuna Genel Bakış zaman çizelgesi grafikleri](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Sağ tarafta **ölçümler**' e tıklayın. Kaynağınızın sistem durumunu ve kullanımını araştırmak için ölçüm gezginini kullanın. **Yeni grafik ekle**’ye tıklayarak ek özel görünümler oluşturabilir veya **Düzenle**’yi seçerek mevcut grafik türlerini, yüksekliğini, renk paletini, gruplandırmaları ve ölçümleri değiştirebilirsiniz. Örneğin, ölçümler açılan penceresinden "tarayıcı sayfa yükleme süresi" ni ve toplamasının "Ort" öğesini seçerek ortalama tarayıcı sayfa yükleme süresini görüntüleyen bir grafik yapabilirsiniz. Azure Ölçüm Gezgini hakkında daha fazla bilgi edinmek için [azure Ölçüm Gezgini](../../azure-monitor/platform/metrics-getting-started.md)kullanmaya başlama makalesini ziyaret edin.

     ![Ölçümler sekmesi: ortalama tarayıcı sayfa yükleme süresi grafiği](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Video

- [Application Insights .NET Core ve Visual Studio ile sıfırdan yapılandırma](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) hakkında dış adım adım video.
- [Application Insights .NET Core ve sıfırdan Visual Studio Code yapılandırma](https://youtu.be/ygGt84GDync) hakkında dış adım adım video.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Testi tamamladığınızda, kaynak grubunu ve tüm ilgili kaynakları silebilirsiniz. Bunu yapmak için aşağıdaki adımları izleyin.

1. Azure portalında sol taraftaki menüden, **Kaynak grupları**’na tıklayın ve ardından **myResourceGroup**’a tıklayın.
2. Kaynak grubu sayfanızda, **Sil**’e tıklayın, metin kutusuna **myResourceGroup** yazın ve ardından **Sil**’e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Çalışma zamanı özel durumlarını bulma ve tanılama](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
