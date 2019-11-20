---
title: Azure Application Insights’ta müşterilerinizi anlama | Microsoft Docs
description: Müşterilerin uygulamanızı nasıl kullandığını anlamak için Azure Application Insights’ı kullanma öğreticisi.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/20/2017
ms.custom: mvc
ms.openlocfilehash: 08da1ad5d934116db8f110d7b75a8e0a60ffc84a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894681"
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Müşterilerin uygulamanızı nasıl kullandığını anlamak için Azure Application Insights’ı kullanın

Azure Application Insights, kullanıcıların uygulamanızla nasıl etkileşim kurduğunu anlamanıza yardımcı olmak için kullanım bilgileri toplar.  Bu öğreticide bu bilgileri analiz etmek için kullanabileceğiniz farklı kaynaklar gösterilmektedir.  Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Uygulamanıza erişen kullanıcılarla ilgili bilgileri analiz etme
> * Oturum bilgilerini kullanarak müşterilerin uygulamanızı nasıl kullandığını analiz etme
> * İstenen kullanıcı etkinliğini gerçek etkinlikle karşılaştırmanızı sağlayan huniler tanımlama 
> * Görselleştirmeleri ve sorguları tek bir belgede birleştirmenizi sağlayan bir çalışma kitabı oluşturma
> * Benzer kullanıcıları gruplayarak birlikte analiz etme
> * Uygulamanıza dönüş yapan kullanıcıları öğrenme
> * Kullanıcıların uygulamanızda nasıl gezindiğini inceleme


## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- Aşağıdaki iş yükleriyle [Visual Studio 2019](https://www.visualstudio.com/downloads/) ' i yükledikten sonra:
    - ASP.NET ve web geliştirme
    - Azure geliştirme
- [Visual Studio Snapshot Debugger](https://aka.ms/snapshotdebugger)’ı indirin ve yükleyin.
- Azure’a .NET uygulaması dağıtma ve [Application Insights SDK’sını etkinleştirme](../../azure-monitor/app/asp-net.md). 
- Özel olaylar/sayfa görüntülemeleri eklemek için [uygulamanızdan telemetri gönderme](../../azure-monitor/app/usage-overview.md#send-telemetry-from-your-app)
- Kullanıcının zaman içinde gerçekleştirdiği işlevleri izlemek ve kullanım özelliklerinden tam olarak faydalanmak için [kullanıcı bağlamı](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context) gönderin.

## <a name="log-in-to-azure"></a>Azure'da oturum açma
[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="get-information-about-your-users"></a>Kullanıcılarınızla ilgili bilgi edinme
**Kullanıcılar** paneli, kullanıcılarınızla ilgili önemli ayrıntıları farklı şekillerde anlamanıza yardımcı olur. Bu paneli kullanarak kullanıcılarınızın bağlandığı konum, istemcilerin ayrıntıları ve erişim sağlanan uygulama bölümleri gibi bilgileri anlayabilirsiniz. 

1. **Application Insights**’ı ve sonra aboneliğinizi seçin.
2. Menüden **Kullanıcılar**'ı seçin.
3. Varsayılan görünümde son 24 saat içinde uygulamanıza bağlanan benzersiz kullanıcı sayısı gösterilir.  Bu bilgileri filtrelemek için zaman aralığını değiştirebilir ve diğer ölçütleri ayarlayabilirsiniz.

    ![Sorgu Tasarımcısı](media/tutorial-users/QueryBuilder.png)

6. **Sırasında** açılan menüsüne tıklayın ve zaman aralığını 7 gün olarak değiştirin.  Bu işlem paneldeki grafiklerde yer alan bilgi miktarını artırır.

    ![Zaman aralığını değiştirme](media/tutorial-users/TimeRange.png)

4. **Bölme ölçütü** açılan menüsüne tıklayarak grafiğe kullanıcı özelliğine göre bir döküm ekleyin.  **Ülke veya bölge**'yi seçin.  Grafik aynı verileri içerir, ancak her ülke/bölge için Kullanıcı sayısı dökümünü görüntülemenize olanak sağlar.

    ![Ülke veya bölge grafiği](media/tutorial-users/CountryorRegion.png)

5. İmleci grafikteki farklı çubukların üzerine konumlandırın ve her bir ülkenin/bölgenin sayımının yalnızca ilgili çubuğun gösterdiği zaman penceresini yansıttığını unutmayın.
6. Sağ taraftaki kullanıcı verilerinizle analiz gerçekleştiren **İçgörüler** sütununa bakın.  Burada zaman içindeki benzersiz oturum sayısı ve kullanıcı verilerinin önemli bir kısmını oluşturan ortak özellikleri içeren kayıtlar gibi bilgiler sunulur 

    ![İçgörüler sütunu](media/tutorial-users/insights.png)


## <a name="analyze-user-sessions"></a>Kullanıcı oturumlarını analiz etme
**Oturumlar** paneli, **Kullanıcılar** paneline benzerdir.  **Kullanıcılar** paneli, uygulamanıza erişen kullanıcılarla ilgili bilgileri anlamanıza yardımcı olurken **Oturumlar** paneli bu kullanıcıların uygulamanızı nasıl kullandığını anlamanıza yardımcı olur.  

1. Menüden **Oturumlar**'ı seçin.
2. Grafiğe göz attığınızda verileri filtrelemek ve ayrıntılara inmek için **Kullanıcılar** paneliyle aynı seçeneklere sahip olduğunuzu göreceksiniz.

    ![Oturumlar Sorgu Tasarımcısı](media/tutorial-users/SessionsBuilder.png)

3. Sağ taraftaki **Bu oturumların örneği** bölmesinde olayların çoğunu içeren oturumlar listelenir.  Bu oturumları analiz etmek ilginizi çekebilir.

    ![Bu oturumların örneği](media/tutorial-users/SessionsSample.png)

4. Bu oturumlardan birine tıklayarak oturumdaki tüm eylemleri gösteren **Oturum Zaman Çizelgesi**'ni görüntüleyebilirsiniz.  Bu seçenek, çok sayıda özel duruma sahip olan oturumlar gibi bilgileri tanımlamanıza yardımcı olabilir.

    ![Oturum Zaman Çizelgesi](media/tutorial-users/SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Benzer kullanıcıları gruplama
**Kohortu** , benzer özelliklere gruplanmış bir Kullanıcı kümesidir.  Kohortları kullanarak diğer panellerdeki verileri filtreleyebilir ve belirli bir kullanıcı grubunu analiz edebilirsiniz.  Örneğin yalnızca bir satın alma işlemini tamamlamış olan kullanıcıları analiz etmek isteyebilirsiniz.

1.  Menüden **Kohortlar**'ı seçin.
2.  Yeni bir kohort oluşturmak için **Yeni**'ye tıklayın.
3.  **Şunu kullanan:** açılan menüsünü seçip bir eylem belirleyin.  Yalnızca raporun zaman aralığında bu eylemi gerçekleştirmiş olan kullanıcılar dahil edilir.

    ![Belirli eylemleri gerçekleştirmiş olan kohort](media/tutorial-users/CohortsDropdown.png)

4.  Menüden **Kullanıcılar**'ı seçin.
5.  **Göster** açılan menüsünde yeni oluşturduğunuz kohortu seçin.  Grafik verileri bu kullanıcılarla sınırlanır.

    ![Kullanıcılar aracındaki kohort](media/tutorial-users/UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>İstenen etkinliği gerçek değerlerle karşılaştırma
Önceki paneller uygulamanızın kullanıcılarının gerçekleştirdiği işlemlere odaklanırken **Huniler** paneli kullanıcılarınızın yapmak istediklerine odaklanır.  Huni, uygulamanızdaki bir adım kümesini ve adımlar arasında geçiş yapan kullanıcıların yüzdesini temsil eder.  Örneğin ürün ararken uygulamanıza bağlanan kullanıcıların yüzdesini ölçen bir huni oluşturabilirsiniz.  Ardından bu ürünü alışveriş sepetine ekleyen kullanıcıların yüzdesini ve satın alma işlemini tamamlayanların yüzdesini görebilirsiniz.

1. Menüden **Huniler**'i seçin ve **Yeni**'ye tıklayın. 

    ![](media/tutorial-users/funnelsnew.png)

2. **Huni Adı** alanına bir ad yazın.
3. Her adım için bir eylem seçerek en az iki adımdan oluşan bir huni oluşturun.  Eylemler listesi, Application Insights'tan alınan kullanım verileriyle oluşturulur.

    ![](media/tutorial-users/funnelsedit.png)

4. **Kaydet**'e tıklayarak huniyi kaydedin ve sonuçlarını görüntüleyin.  Huninin sağ tarafındaki pencere, ilk etkinlikten önceki ve son etkinlikten sonraki en yaygın etkinlikleri göstererek belirli bir işlem sırası etrafındaki kullanıcı eğilimlerini anlamanıza yardımcı olur.

    ![](media/tutorial-users/funnelsright.png)


## <a name="learn-which-customers-return"></a>Dönüş yapan müşterileri öğrenme
**Elde tutma**, uygulamanıza dönüş yapan kullanıcıları anlamanıza yardımcı olur.  

1. Menüden **Elde tutma**'yı seçin.
2. Analiz edilen bilgiler varsayılan olarak bir eylem gerçekleştiren ve ardından herhangi bir eylem gerçekleştirmek için geri dönen kullanıcıları kapsar.  Bu filtreyi yalnızca satın alma işlemini tamamladıktan sonra geri dönen kullanıcılar gibi istediğiniz farklı bir bilgiyle değiştirebilirsiniz.

    ![](media/tutorial-users/retentionquery.png)

3. Ölçütlerle eşleşen geri dönen kullanıcılar farklı süreler için grafik ve tablo biçiminde gösterilir.  Tipik bir düzende, zaman içinde geri dönen kullanıcı sayısında kademeli bir düşüş olacaktır.  Bir zaman aralığından bir sonrakine geçişte ani bir düşüş olması bir sorun olduğunu gösterebilir. 

    ![](media/tutorial-users/retentiongraph.png)

## <a name="analyze-user-navigation"></a>Kullanıcı gezintisini analiz etme
**Kullanıcı akışı**, kullanıcıların uygulamanızın sayfaları ve özellikleri arasında nasıl gezindiğini görselleştirir.  Bu da kullanıcıların belirli bir sayfadan geçiş yaptıkları yerler, uygulamanızdan çıkış yapma şekli düzenli olarak tekrarlanan eylemler gibi sorulara yanıt bulmanıza yardımcı olur.

1.  Menüden **Kullanıcı akışları**'nı seçin.
2.  **Yeni**'ye tıklayarak yeni bir kullanıcı akışı oluşturun ve ardından **Düzenle**'ye tıklayarak ayrıntılarını düzenleyin.
3.  **Zaman Aralığı** değerini 7 güne çıkarın ve ardından ilk olayı seçin.  Akış, bu olayla başlayan kullanıcı oturumlarını izler.

    ![](media/tutorial-users/flowsedit.png)

4.  Kullanıcı akışı görüntülenir ve farklı kullanıcı yolları ile oturum sayıları da gösterilir.  Mavi çizgiler kullanıcının geçerli eylemden sonra bir eylem gerçekleştirdiğini gösterir.  Kırmızı çizgi kullanıcı oturumunun sonlandırıldığını gösterir.

    ![](media/tutorial-users/flows.png)

5.  Akıştaki bir olayı kaldırmak için eylemin köşesindeki **x** simgesine ve ardından **Grafik Oluştur**'a tıklayın.  Grafik yeniden çizilir ve olay örnekleri kaldırılır.  **Düzenle**'ye tıkladığınızda olayın **Dışlanan olaylar** alanına eklenmiş olduğunu görebilirsiniz.

    ![](media/tutorial-users/flowsexclude.png)

## <a name="consolidate-usage-data"></a>Kullanım verilerini birleştirme
**Çalışma kitapları** veri görselleştirmelerini, Analytics sorgularını ve metinleri etkileşimli belgelere dönüştürür.  Çalışma kitaplarını kullanarak yaygın kullanım bilgilerini gruplandırabilir, belirli bir olayla ilgili bilgileri birleştirebilir veya uygulamanızın kullanımıyla ilgili ekibinize rapor verebilirsiniz.

1.  Menüden **Çalışma kitapları**'nı seçin.
2.  Yeni bir çalışma kitabı oluşturmak için **Yeni**'ye tıklayın.
3.  Son bir gündeki tüm kullanım verilerinin çubuk grafik olarak gösterildiği bir sorgu otomatik olarak sağlanır.  Bu sorguyu kullanabilir, el ile düzenleyebilir veya **Örnek sorgular**'a tıklayarak diğer yararlı sorgulardan seçim yapabilirsiniz.

    ![](media/tutorial-users/samplequeries.png)

4.  **Düzenleme bitti**'ye tıklayın.
5.  Çalışma kitabının en üstündeki metni düzenlemek için üst bölmeden **Düzenle**'ye tıklayın.  Bu çalışma kitabı markdown kullanılarak biçimlendirilir.

    ![](media/tutorial-users/markdown.png)

6.  Kullanıcı bilgilerini içeren bir grafik eklemek için **Kullanıcı ekle**'ye tıklayın.  Dilerseniz grafiğin ayrıntılarını düzenleyin ve **Düzenleme bitti**'ye tıklayarak kaydedin.


## <a name="next-steps"></a>Sonraki adımlar
Kullanıcılarınızı analiz etmeyi öğrendiğinize göre bir sonraki öğreticiye geçerek bu bilgileri uygulamanızla ilgili diğer faydalı bilgilerle birleştirmek için özel panolar oluşturmayı öğrenebilirsiniz.

> [!div class="nextstepaction"]
> [Özel panolar oluşturma](../../azure-monitor/learn/tutorial-app-dashboards.md)
