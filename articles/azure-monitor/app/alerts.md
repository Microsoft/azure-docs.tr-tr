---
title: Azure Application Insights uyarı ayarlama | Microsoft Docs
description: Web uygulamanızda yavaş yanıt süreleri, özel durumlar ve diğer performans veya kullanım değişiklikleri hakkında bildirim alın.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.openlocfilehash: a21e2676d1b03472c58e2f95095a1a59d00b16be
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678400"
---
# <a name="set-alerts-in-application-insights"></a>Application Insights uyarıları ayarlama
[Azure Application Insights][start] , Web uygulamanızdaki performans veya kullanım ölçümlerinde değişiklikler yapmak için sizi uyarır. 

Application Insights, performans sorunlarını tanılamanıza ve kullanım düzenlerini anlamanıza yardımcı olmak üzere [çok çeşitli platformlarda][platforms] canlı uygulamanızı izler.

Birden çok uyarı türü vardır:

* [**Ölçüm uyarıları**](../../azure-monitor/platform/alerts-metric-overview.md) , bir ölçüm bir dönem için yanıt süreleri, özel durum sayısı, CPU kullanımı veya sayfa görünümleri gibi bir eşik değeri kesiştiği zaman size bildirir.
* [**Günlük uyarıları**](../../azure-monitor/platform/alerts-unified-log.md) , uyarı sinyalinin özel bir kusto sorgusuna dayalı olduğu uyarıları anlatmak için kullanılır.
* [**Web testleri**][availability] siteniz Internet 'te kullanılamadığında veya yavaş yanıt vermeyi bildirir. [Daha fazla bilgi edinin][availability].
* [**Proaktif Tanılamalar**](../../azure-monitor/app/proactive-diagnostics.md) , olağan dışı performans desenleri hakkında sizi bilgilendirmek üzere otomatik olarak yapılandırılır.

## <a name="set-a-metric-alert"></a>Ölçüm uyarısı ayarla
Uyarı kuralları sekmesini açın ve ardından Ekle düğmesini kullanın.

![Uyarı kuralları sekmesinde, uyarı Ekle ' yi seçin. Uygulamanızı ölçülecek kaynak olarak ayarlayın, uyarı için bir ad sağlayın ve bir ölçüm seçin.](./media/alerts/01-set-metric.png)

* Kaynağı diğer özelliklerden önce ayarlayın. Performans veya kullanım ölçümlerinde uyarılar ayarlamak istiyorsanız **"(bileşenler)" kaynağını seçin** .
* Uyarıya verdiğiniz ad, kaynak grubu içinde (yalnızca uygulamanız değil) benzersiz olmalıdır.
* Eşik değerini girmeniz istenecek birimleri göz önünde bulunmamaya dikkat edin.
* "E-posta sahipleri..." kutusunu işaret ederseniz, uyarılar bu kaynak grubuna erişebilen herkese e-posta ile gönderilir. Bu kişi kümesini genişletmek için, onları [kaynak grubuna veya aboneliğe](../../azure-monitor/app/resources-roles-access-control.md) (kaynak değil) ekleyin.
* "Ek e-postalar" belirtirseniz, uyarılar bu kişilere veya gruplara gönderilir ("e-posta sahipleri..." seçeneğini iade etmeksizin Box). 
* Uyarılara yanıt veren bir Web uygulaması ayarladıysanız bir Web [kancası adresi](../../azure-monitor/platform/alerts-webhooks.md) ayarlayın. Bu, hem uyarı etkinleştirildiğinde hem de çözümlendiği zaman çağrılır. (Ancak, var olan sorgu parametrelerinin Web kancası özellikleri olarak geçirilmediğini unutmayın.)
* Uyarıyı etkinleştirebilir veya devre dışı bırakabilirsiniz: üstteki düğmelere bakın.

*Uyarı Ekle düğmesini görmüyorum.*

* Bir kuruluş hesabı mı kullanıyorsunuz? Bu uygulama kaynağına sahip veya katkıda bulunan erişiminiz varsa uyarılar ayarlayabilirsiniz. Access Control sekmesine göz atın. [erişim denetimi hakkında bilgi edinin][roles].

> [!NOTE]
> Uyarılar dikey penceresinde, zaten bir uyarı ayarlanmış olduğunu görürsünüz: [proaktif tanılama](../../azure-monitor/app/proactive-failure-diagnostics.md). Otomatik uyarı, belirli bir ölçümü izler, istek hata oranı. Proaktif uyarıyı devre dışı bırakmayı seçmediğiniz takdirde, istek hata oranı üzerine kendi uyarınızı ayarlamanız gerekmez.
> 
> 

## <a name="see-your-alerts"></a>Uyarılarınıza bakın
Bir uyarı, etkin olmayan ve etkin arasındaki durumu değiştirdiğinde bir e-posta alırsınız. 

Her uyarının geçerli durumu, uyarı kuralları sekmesinde gösterilir.

Uyarılar açılan listesinden en son etkinliğin bir özeti vardır:

![Uyarı açılan uyarıları](./media/alerts/010-alert-drop.png)

Durum değişikliklerinin geçmişi etkinlik günlüğünde bulunur:

![Genel Bakış sekmesinde ayarlar, denetim günlükleri ' ne tıklayın.](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Uyarılar nasıl çalışır?
* Bir uyarının üç durumu vardır: "hiçbir şekilde etkin değil", "etkin" ve "çözümlendi." Etkin, belirttiğiniz koşulun en son değerlendirildiği zaman doğru olduğu anlamına gelir.
* Bir uyarının durumu değiştiğinde bir bildirim oluşturulur. (Uyarıyı oluştururken uyarı koşulu zaten doğruysa, koşul false olana kadar bildirim almayabilir.)
* E-postalar kutusunu veya sağlanmış e-posta adreslerini denetlediyseniz her bildirim bir e-posta oluşturur. Ayrıca Bildirimler açılan listesine de bakabilirsiniz.
* Bir ölçüm her ulaştığında bir uyarı değerlendirilir, ancak Aksi takdirde.
* Değerlendirme, ölçümü önceki dönem boyunca toplar ve ardından yeni durumu tespit etmek için bu değeri eşikle karşılaştırır.
* Seçtiğiniz süre, ölçümlerin toplanacak aralığı belirtir. Uyarının ne sıklıkta değerlendirileceğini etkilemez: Bu, ölçümlerin gelişmesinin sıklığından kaynaklanır.
* Belirli bir ölçüm için herhangi bir veri alınırsa, boşluğun uyarı değerlendirmesi ve Ölçüm Gezgini 'ndeki grafiklerde farklı etkileri vardır. Ölçüm Gezgini 'nde, grafiğin örnekleme aralığından daha uzun bir veri görülemeyeceğini, grafik 0 değerini gösterir. Ancak aynı ölçümü temel alan bir uyarı yeniden değerlendirilmez ve uyarının durumu değişmeden kalır. 
  
    Veriler sonuç olarak ulaştığında, grafik sıfır olmayan bir değere geri atlar. Uyarı, belirttiğiniz dönem için kullanılabilir verilere göre değerlendirilir. Yeni veri noktası dönemde kullanılabilir tek tek ise, toplama yalnızca o veri noktasına dayalıdır.
* Bir uyarı, uzun bir süre ayarlamış olsanız bile uyarı ve sağlıklı durumlar arasında sıkça titreşebilir. Ölçüm değeri eşiğin etrafında olursa bu durum oluşabilir. Eşikte bir hysteresin yok: uyarıya geçiş, sağlıklı geçişle aynı değerde olur.

## <a name="what-are-good-alerts-to-set"></a>Ayarlanacak iyi uyarılar nelerdir?
Uygulamanıza bağlıdır. İle başlamak için çok fazla ölçüm ayarlanmamalıdır. Uygulamanızın çalışırken ölçüm grafiklerinize bakmak için bir süre harcaın, normal şekilde davrandığı konusunda fikir edinebilirsiniz. Bu uygulama, performansını geliştirmenin yollarını bulmanıza yardımcı olur. Ardından, ölçümler normal bölgenin dışına gittiğinizde size bildirmek için Uyarılar ayarlayın. 

Popüler uyarılar şunlardır:

* [Tarayıcı ölçümleri][client], özellikle tarayıcı **sayfası yükleme süreleri**, Web uygulamaları için uygundur. Sayfanızda çok sayıda komut dosyası varsa, **tarayıcı özel durumlarına**bakmanız gerekir. Bu ölçümleri ve uyarıları almak için [Web sayfası izlemeyi][client]ayarlamanız gerekir.
* Web uygulamalarının sunucu tarafı için **sunucu yanıt süresi** . Uyarıları ayarlamanın yanı sıra, yüksek istek hızlarıyla orantılı bir şekilde değişiklik olup olmadığını görmek için bu ölçümü göz önünde bulundurun: çeşitleme, uygulamanızın kaynakları tükendiğine işaret edebilir. 
* **Sunucu özel durumları** -bunları görmek için bazı [ek kurulum](../../azure-monitor/app/asp-net-exceptions.md)yapmanız gerekir.

[Öngörülü hata oranı tanılaması](../../azure-monitor/app/proactive-failure-diagnostics.md) 'nın, uygulamanızın hata kodlarıyla olan isteklere nasıl yanıt verdiğini otomatik olarak izleyip izleyeceğinizi unutmayın.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Özel günlük aramasını kullanarak özel durum uyarısı ayarlama

Bu bölümde, sorgu tabanlı özel durum uyarısını nasıl ayarlayacağız. Bu örnekte, son 24 saat içindeki başarısız oran %10 ' dan büyük olduğunda bir uyarı istiyoruz.

1. Azure portal uygulama Insight kaynağına gidin.
2. Sol tarafta, Yapılandır ' ın altındaki **Uyarı**' ı tıklatın.

    ![Sol tarafta, Yapılandır tıklama uyarısı](./media/alerts/1appinsightalert.png)

3. Uyarı sekmesinin en üstünde **Yeni uyarı kuralı**' nı seçin.

     ![Uyarı sekmesinin en üstünde yeni uyarı kuralı ' na tıklayın.](./media/alerts/2createalert.png)

4. Kaynağınızın otomatik olarak seçilmesi gerekir. Bir koşul ayarlamak için **Koşul Ekle**' ye tıklayın.

    ![Koşul Ekle ' ye tıklayın](./media/alerts/3addcondition.png)

5. Sinyal mantığını Yapılandır sekmesinde **özel günlük araması** ' nı seçin.

    ![Özel günlük araması ' na tıklayın](./media/alerts/4customlogsearch.png)

6. Özel günlük araması sekmesinde sorgunuzu "arama sorgusu" kutusuna girin. Bu örnekte, aşağıdaki kusto sorgusunu kullanacağız.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Arama sorgu kutusuna sorgu yazın](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Ayrıca, bu adımları sorgu tabanlı diğer uyarı türleri için de uygulayabilirsiniz. Kusto sorgu dili hakkında daha fazla bilgi edinmek için bu [kusto kullanmaya başlama belgesi](https://docs.microsoft.com/azure/kusto/concepts/) veya bu [SQL to kusto chyiyecek sayfasını](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet) kullanabilirsiniz

7. "Uyarı mantığı" altında, bunun sonuç sayısına veya ölçüm ölçüsüne göre yapılıp yapılmayacağını seçin. Sonra koşulu (büyüktür, eşittir, küçüktür) ve bir eşiği seçin. Bu değerleri değiştirirken, koşul önizleme cümlesi değişikliklerinin olduğunu fark edebilirsiniz. Bu örnekte, "eşittir" olarak kullanıyoruz.

    ![Uyarı mantığı altında, ve koşulunu temel alarak belirtilen seçeneklerden seçim yapın ve ardından bir eşik yazın](./media/alerts/6alertlogic.png)

8. "Değerlendirilen tabanlı" altında, dönemi ve sıklığı ayarlayın. Buradaki süre, Yukarıdaki sorguda dönem için koyduğumuz değerle eşleşmelidir. Ardından **bitti**' ye tıklayın.

    ![En alttaki dönemi ve sıklığı ayarlayıp bitti ' ye tıklayın.](./media/alerts/7evaluate.png)

9. Şimdi, tahmini aylık maliyet ile oluşturduğumuz koşulu görüyoruz. Aşağıda ["eylem grupları"](../platform/action-groups.md) altında, yeni bir grup oluşturabilir veya var olan bir grubu seçebilirsiniz. İsterseniz, eylemleri özelleştirebilirsiniz.

    ![eylem grubu altında Seç veya Oluştur düğmesine tıklayın](./media/alerts/8actiongroup.png)

10. Son olarak uyarı ayrıntılarınızı (uyarı kuralı adı, açıklama, önem derecesi) ekleyin. İşiniz bittiğinde, en altta **Uyarı kuralı oluştur** ' a tıklayın.

    ![Uyarı ayrıntısı altında uyarı kuralı adınızı yazın, bir açıklama yazın ve önem derecesi seçin](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Klasik uyarı e-posta bildirimlerinin aboneliğini kaldırma

Bu bölüm, klasik **kullanılabilirlik uyarıları**, **Klasik Application Insights ölçüm uyarıları**ve **Klasik hata bozukluklar uyarıları**için geçerlidir.

Aşağıdakilerden biri geçerliyse, bu klasik uyarılar için e-posta bildirimleri alıyorsunuz:

* E-posta adresiniz, uyarı kuralı ayarlarındaki bildirim e-posta alıcıları alanında listelenir.

* Abonelik için belirli rolleri tutan kullanıcılara e-posta bildirimleri gönderme seçeneği etkinleştirilir ve söz konusu Azure aboneliği için ilgili bir rol tutabilirsiniz.

![Uyarı bildirimi ekran görüntüsü](./media/alerts/alert-notification.png)

Güvenlik ve gizliliğinizi daha iyi denetlemek için, **bildirim e-posta alıcıları** alanında klasik uyarılarınız için bildirim alıcılarını açıkça belirtmenizi öneririz. Geri uyumluluk için belirli rolleri tutan tüm kullanıcılara bildirme seçeneği sağlanır.

Belirli bir uyarı kuralı tarafından oluşturulan e-posta bildirimlerinin aboneliğini kaldırmak için, **bildirim e-posta alıcıları** alanından e-posta adresinizi kaldırın.

E-posta adresiniz açıkça listelenmiyorsa, belirli rollerin tüm üyelerini otomatik olarak bildirme seçeneğini devre dışı bırakmanız ve bunun yerine bildirim e-postasında bu uyarı kuralı için bildirim alması gereken tüm kullanıcı e-postalarını listeetmenizi öneririz. alıcılar alanı.

## <a name="who-receives-the-classic-alert-notifications"></a>(Klasik) uyarı bildirimlerini kim alıyor?

Bu bölüm yalnızca, klasik uyarılar için geçerlidir ve yalnızca istediğiniz alıcıların bildirimleri almasını sağlamak için uyarı bildirimlerinizi iyileştirmenize yardımcı olur. [Klasik uyarılar](../platform/alerts-classic.overview.md) ve yeni uyarılar deneyimi arasındaki fark hakkında daha fazla bilgi edinmek için [uyarılara genel bakış makalesine](../platform/alerts-overview.md)bakın. Yeni uyarılar deneyiminde uyarı bildirimini denetlemek için [eylem grupları](../platform/action-groups.md)' nı kullanın.

* Klasik uyarı bildirimleri için belirli alıcıların kullanılmasını öneririz.

* Tüm Application Insights ölçümlerinde (kullanılabilirlik ölçümleri dahil) uyarılar için, **toplu/grup** onay kutusu seçeneği etkinse, abonelikte sahip, katkıda bulunan veya okuyucu rolleriyle kullanıcılara gönderir. Aslında, aboneliğe erişimi olan _Tüm_ kullanıcılar Application Insights kaynak kapsamdadır ve bildirimler alacaktır.

> [!NOTE]
> Şu anda **toplu/grup** onay kutusu seçeneğini kullanırsanız ve devre dışı bıraktığınızda, değişikliği döndüremezsiniz.

Kullanıcılara rollerine göre bildirimde bulunan yeni uyarı deneyimini veya neredeyse gerçek zamanlı uyarıları kullanın. [Eylem gruplarıyla](../platform/action-groups.md), katkıda bulunan/sahip/okuyucu rollerinin herhangi birine sahip kullanıcılar için e-posta bildirimleri yapılandırabilirsiniz (tek bir seçenek olarak birlikte birleştirilemez).

## <a name="automation"></a>Otomasyon
* [Uyarıları ayarlamayı otomatikleştirmek için PowerShell 'i kullanma](../../azure-monitor/app/powershell-alerts.md)
* [Uyarıları yanıtlamayı otomatikleştirmek için Web kancalarını kullanma](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Ayrıca bkz.
* [Kullanılabilirlik Web testleri](../../azure-monitor/app/monitor-web-app-availability.md)
* [Uyarıları ayarlamayı otomatikleştirme](../../azure-monitor/app/powershell-alerts.md)
* [Proaktif tanılama](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

