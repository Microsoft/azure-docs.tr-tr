---
title: Azure Application Insights kullanım katmanları | Microsoft Docs
description: Farklı kümeleri veya kullanıcıları, oturumları, olayları veya ortak bir şeyi içeren işlemleri çözümleyin
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: d6762ac9253c838f715588451441aa9aa467f673
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899505"
---
# <a name="application-insights-cohorts"></a>Application Insights cohortaları

Bir kohortu, bir dizi Kullanıcı, oturum, olay veya ortak bir şeyi içeren işlemlerdir. Azure Application Insights 'de, cohortalar bir analiz sorgusu tarafından tanımlanır. Belirli bir kullanıcı veya olay kümesini sürekli olarak analiz etmeniz gereken durumlarda, cohortalar, tam olarak ilgilendiğiniz kümeyi hızlı bir şekilde ifade etmek için size daha fazla esneklik sağlayabilir.

![Cohorts bölmesi](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Cohortalar ve temel filtreler

Filtreler benzer yollarla birlikte kullanılır. Ancak, cohorts tanımları özel analiz sorgularından oluşturulmuştur, bu nedenle çok daha uyarlanabilir ve karmaşıktır. Filtrelerin aksine, daha sonra takımınızın diğer üyelerinin bunları yeniden kullanabilmesi için ortak hortları kaydedebilirsiniz.

Uygulamanızda yeni bir özellik çalıştıran kullanıcılar için bir kohortu tanımlayabilirsiniz. Bu kohortu, Application Insights kaynağına kaydedebilirsiniz. Gelecekte bu kaydedilen belirli kullanıcıları çözümlemek kolaydır.

> [!NOTE]
> Oluşturulduktan sonra, kullanıcılar, oturumlar, olaylar ve Kullanıcı Akışları araçlarından birlikte kullanılabilir.

## <a name="example-engaged-users"></a>Örnek: bağlı kullanıcılar

Takımınız, belirli bir ayda uygulamanızı beş veya daha fazla kez kullanan herkes olarak bir kullanıcı tarafından tanımlanır. Bu bölümde, bu kullanıcı tanımlı kullanıcıların bir kohortu tanımlarsınız.

1. Cohorts aracını açın.

2. **Şablon Galerisi** sekmesini seçin. Çeşitli birlikte bir şablon koleksiyonu görürsünüz.

3. **Kullanılan kullanıcılar--güne göre**' ı seçin.

    Bu kohortu için üç parametre vardır:
    * **Etkinlikler**, hangi olayların ve sayfa görünümlerinin "kullanım" olarak sayılmasını seçersiniz.
    * **Dönem**, bir ayın tanımı.
    * **Usedatleastcustom**, kullanıcıların, bir dönemde bir süre içinde kullanıcı olarak saymak için kaç kez kullanması gerektiği.

4. **Usedatleastcustom** değerini **5 + gün**olarak değiştirin ve **süresi** varsayılan 28 gün olarak bırakın.

    ![Bağlı kullanıcılar](./media/usage-cohorts/003.png)

    Artık bu kohortu, geçmiş 28 ' de 5 ayrı gün içinde herhangi bir özel olay veya sayfa görünümüyle gönderilen tüm Kullanıcı kimliklerini temsil eder.

5. **Kaydet**’i seçin.

   > [!TIP]
   > "Bağlı kullanıcılar (5 + gün)" gibi kohortu bir ad verin. Bu Application Insights kaynağına erişimi olan diğer kişilerin bu kohortu görmesini isteyip istemediğinize bağlı olarak, bu dosyayı "Raporlarım" veya "paylaşılan Raporlar" olarak kaydedin.

6. **Galeriye geri dön**seçeneğini belirleyin.

### <a name="what-can-you-do-by-using-this-cohort"></a>Bu kohortu kullanarak ne yapabilirsiniz?

Kullanıcılar aracını açın. **Göster** açılan kutusunda, **ait kullanıcılar**altında oluşturduğunuz kohortu seçin.

Artık kullanıcılar aracı bu kohortu Kullanıcı adına filtrelenmiştir:

![Belirli bir kohortu filtrelenmiş kullanıcılar bölmesi](./media/usage-cohorts/004.png)

Dikkat etmeniz gereken birkaç önemli nokta:

* Bu kümeyi normal filtreler aracılığıyla oluşturamazsınız. Tarih mantığı daha gelişmiş.
* Kullanıcılar aracında normal filtreleri kullanarak bu kohortu daha fazla filtre uygulayabilirsiniz. Bu nedenle, kohortu 28 günlük Windows üzerinde tanımlanmış olsa da, kullanıcılar aracındaki zaman aralığını 30, 60 veya 90 gün olarak ayarlayabilirsiniz.

Bu filtreler sorgu oluşturucu aracılığıyla hızlı bir şekilde ifade etmek imkansız olan daha karmaşık soruları destekler. _Son 28 gün içinde çalışan kişiler bir örnektir. Aynı kişiler son 60 gün boyunca nasıl davranır?_

## <a name="example-events-cohort"></a>Örnek: Event kohortu

Ayrıca, olayların birlikte da kullanılabilmesini sağlayabilirsiniz. Bu bölümde, olaylar ve sayfa görünümlerinin bir kohortu tanımlarsınız. Daha sonra bunları diğer araçlardan nasıl kullanacağınızı görürsünüz. Bu kohortu, takımınızın _etkin kullanımı_ düşündüğü bir olay kümesini veya belirli bir yeni özellikle ilgili bir kümeyi tanımlayabilir.

1. Cohorts aracını açın.

2. **Şablon Galerisi** sekmesini seçin. Çeşitli Tebrikler ilgili şablonlar koleksiyonu görürsünüz.

3. **Olay seçiciyi**seçin.

    ![Olay seçicisinin ekran görüntüsü](./media/usage-cohorts/006.png)

4. **Etkinlikler** açılan kutusunda, kohortu içinde olmasını istediğiniz olayları seçin.

5. Kohortu kaydedin ve bir ad verin.

## <a name="example-active-users-where-you-modify-a-query"></a>Örnek: bir sorguyu değiştirdiğiniz etkin kullanıcılar

Önceki iki cohortlar, açılan kutular kullanılarak tanımlandı. Ancak, toplam esneklik için analiz sorguları kullanarak da birlikte kohortalar tanımlayabilirsiniz. Nasıl yapılacağını görmek için, Birleşik Krallık 'tan bir kohortu kullanıcı oluşturun.

![Cohorts aracının kullanımıyla birlikte izlenecek animasyonlu görüntü](./media/usage-cohorts/cohorts0001.gif)

1. Cohorts aracını açın, **Şablon Galerisi** sekmesini seçin ve **boş kullanıcılar kohortu**' ı seçin.

    ![Boş kullanıcılar kohortu](./media/usage-cohorts/001.png)

    Üç bölüm vardır:
   * Kohortu, takımınızda başkaları için daha ayrıntılı olarak betimleyen bir Markaşağı metin bölümü.

   * Önceki iki örnekten **Etkinlikler** ve diğer açılan kutular gibi kendi parametrelerinizi yaptığınız parametreler bölümü.

   * Bir analiz sorgusu kullanarak kohortu tanımladığınız bir sorgu bölümü.

     Sorgu bölümünde, [bir analiz sorgusu yazarsınız](/azure/kusto/query). Sorgu, tanımlamak istediğiniz kohortu tanımlayan belirli satır kümesini seçer. Cohorts aracı bundan sonra örtülü olarak bir "| sorguya göre user_Id "yan tümcesini özetleme. Bu veriler, bir tablodaki sorgunun altında önizlenmekte olduğundan, sorgunuzun sonuçları döndürdüğünü sağlayabilirsiniz.

     > [!NOTE]
     > Sorguyu görmüyorsanız, bölümü daha uzun hale getirmek ve sorguyu göstermek için yeniden boyutlandırmayı deneyin. Bu bölümün başındaki hareketli. gif, yeniden boyutlandırma davranışını gösterir.

2. Aşağıdaki metni kopyalayıp sorgu düzenleyicisine yapıştırın:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. **Sorguyu Çalıştır**' ı seçin. Tabloda Kullanıcı kimlikleri görünmüyorsa, uygulamanızın kullanıcılara sahip olduğu bir ülkeye/bölgeye geçin.

4. Kohortu öğesini kaydedin ve adlandırın.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

_Belirli bir ülke/bölgeden bir kohortu Kullanıcı tanımladım. Kullanıcılar aracında bu kohortu karşılaştırdığımda, bu ülkede/bölgede yalnızca bir filtre ayarlamaya yönelik farklı sonuçlar görüyorum. Kaydol?_

Cohortalar ve filtreler farklıdır. Birleşik Krallık 'tan (önceki örnekte olduğu gibi tanımlanmıştır) bir kohortu sahip olduğunuzu ve "Country veya Region = Birleşik Krallık" filtresini ayarlamaya ilişkin sonuçları karşılaştırdığınızı varsayalım.

* Kohortu sürümü, geçerli zaman aralığında Birleşik Krallık 'tan bir veya daha fazla olay gönderen kullanıcılardan gelen tüm olayları gösterir. Ülke veya bölgeye göre böldüğünüz takdirde, büyük olasılıkla birçok ülke ve bölge görürsünüz.
* Filtreler sürümü yalnızca Birleşik Krallık 'un olaylarını gösterir. Ancak ülke veya bölgeye göre böldüğünüz takdirde yalnızca Birleşik Krallık ' u görürsünüz.

## <a name="learn-more"></a>Daha fazla bilgi

* [Analytics sorgu dili](https://go.microsoft.com/fwlink/?linkid=856587)
* [Kullanıcılar, oturumlar, olaylar](usage-segmentation.md)
* [Kullanıcı akışları](usage-flows.md)
* [Kullanıma genel bakış](usage-overview.md)