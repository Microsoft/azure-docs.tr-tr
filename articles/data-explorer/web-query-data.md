---
title: 'Hızlı Başlangıç: Azure Veri Gezgini Web kullanıcı arabiriminde verileri Sorgulama'
description: Bu hızlı başlangıçta, sorgu ve Azure Veri Gezgini Web kullanıcı arabiriminde veri paylaşma hakkında bilgi edinin.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/04/2019
ms.openlocfilehash: 145e452a3a914e1026d6da11f2ce38e85fa724f0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797182"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>Hızlı Başlangıç: Azure Veri Gezgini Web kullanıcı arabiriminde verileri Sorgulama

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. Azure Veri Gezgini, sorgu çalıştırmanızı ve paylaşmanızı sağlayan bir web uygulaması sunar. Bu uygulama Azure portalda ve tek başına bir web uygulaması olarak sağlanır. Bu makalede uygulamanın birden fazla kümeye bağlanmanızı ve sorgularınızın ayrıntılı bağlantılarını paylaşmanızı sağlayan tek başına sürümünde çalışacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için Azure aboneliğine ek olarak [bir test kümesine ve veritabanına](create-cluster-database-portal.md) ihtiyacınız vardır.

## <a name="sign-in-to-the-application"></a>Uygulamada oturum açma

[Uygulamada](https://dataexplorer.azure.com/) oturum açın.

## <a name="add-clusters"></a>Küme ekleme

Uygulamayı ilk açtığınızda herhangi bir bağlantı bulunmaz.

![Küme ekleme](media/web-query-data/add-cluster.png)

Sorgu çalıştırmaya başlamak için en az bir küme bağlantısı eklemeniz gerekir. Bu bölümde öğrenmenize yardımcı olmak için oluşturduğumuz Azure Veri Gezgini *help kümesine* bir önceki hızlı başlangıçta oluşturduğunuz test kümesine bağlantı ekleyeceksiniz.

1. Uygulamanın sol üst köşesinden **Küme ekle**'yi seçin.

1. İçinde **Ekle küme** iletişim kutusu, URI girin ve ardından **Ekle**.

   Yardım küme URI'si, kullanabilir `https://help.kusto.windows.net`. Kendi kümenizi varsa, kümenizin bir URI sağlayın. Örneğin, `https://mydataexplorercluster.westus.kusto.windows.net` aşağıdaki görüntüde gösterildiği gibi:

    ![Sunucu Portal URI](media/web-query-data/server-uri.png)

1. Sol taraftaki bölmede **help** kümesini görmeniz gerekir. Erişebileceğiniz örnek tabloları görmek için **Samples** veritabanını genişletin.

    ![Örnek veritabanları](media/web-query-data/sample-databases.png)

    Bu hızlı başlangıcın ilerleyen bölümlerinde ve diğer Azure Veri Gezgini makalelerinde **StormEvents** tablosunu kullanacağız.

Şimdi oluşturduğunuz test kümesini ekleyin.

1. **Küme ekle**'yi seçin.

1. **Küme ekle** iletişim kutusunda test kümenizin URL'sini `https://<ClusterName>.<Region>.kusto.windows.net/` biçiminde girin ve **Ekle**'yi seçin.

    Aşağıdaki örnekte **help** kümesini ve **docscluster.westus** adlı yeni kümeyi (tam URL: `https://docscluster.westus.kusto.windows.net/`) görebilirsiniz.

    ![Test kümesi](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>Sorgu çalıştırma

Artık bağlı olduğunuz kümelerden istediğiniz birinde sorgu çalıştırabilirsiniz (test kümenizde veri olduğunu kabul ediyoruz). Burada **help** kümesine odaklanacağız.

1. Sol taraftaki bölmede **help** kümesinin altında **Samples** veritabanını seçin.

1. Aşağıdaki sorguyu kopyalayıp sorgu penceresine yapıştırın. Pencerenin en üstünde **Çalıştır**’ı seçin.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Bu sorgu **StormEvents** tablosundaki en yeni on kaydı döndürür. Sonucun sol tarafı aşağıdaki tabloya benzer olmalıdır.

    ![Sorgu sonuç kümesi](media/web-query-data/result-set-01.png)

    Aşağıdaki görüntüde eklenmiş kümeler ve sorgu sonucuyla uygulamanın bulunduğu durum gösterilmektedir.

    ![Uygulamanın tamamı](media/web-query-data/full-application.png)

1. Aşağıdaki sorguyu kopyalayıp sorgu penceresinde ilk sorgunun altına yapıştırın. İlk sorgu gibi ayrı satırlar halinde biçimlendirilmemiş olduğuna dikkat edin.

    ```Kusto
    StormEvents | sort by StartTime desc | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. Penceredeki yeni sorguya tıklayarak sorguyu seçin. Shift+Alt+F tuşlarına basarak sorguyu aşağıda gösterilen şekilde biçimlendirin.

    ![Biçimlendirilmiş sorgu](media/web-query-data/formatted-query.png)

1. Sorgu çalıştırma kısayolu olan Shift+Enter tuşlarına basın.

   Bu sorgu da ilk sorguyla aynı kayıtları döndürür ancak yalnızca `project` deyiminde belirtilen sütunları dahil eder. Sonuç aşağıdaki tabloya benzer olmalıdır.

    ![Sorgu sonuç kümesi](media/web-query-data/result-set-02.png)

1. Pencerenin en üstündeki **Hatırla**’yı seçin.

    Sorgu penceresi artık sorguyu yeniden çalıştırmak zorunda kalmadan ilk sorgunun sonuç kümesini gösterir. Analiz sırasında genellikle birden fazla sorgu çalıştırılır ve **Hatırla** özelliği önceki sorguların sonuçlarını yeniden görüntülemenizi sağlar.

1. Şimdi farklı bir çıkış türü görmek için bir sorgu daha çalıştıralım.

    ```Kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```
    Sonuç aşağıdaki grafiğe benzer olmalıdır.

    ![Sütun grafiği](media/web-query-data/column-chart.png)

## <a name="work-with-the-table-grid"></a>Tablo kılavuzuyla çalışma

Temel sorguların nasıl çalıştığını gördünüz. Şimdi sonuçları özelleştirmek ve ek analiz gerçekleştirmek için tablo kılavuzunu nasıl kullanabileceğinize bakalım.

1. İlk sorguyu yeniden çalıştırın. Farenizle **State** sütununun üzerine gelin, menüyü seçin ve **Gruplandırma ölçütü: State** girişini seçin.

    ![Gruplandırma ölçütü: State](media/web-query-data/group-by.png)

1. Kılavuzda **California**'yı genişleterek bu eyalete ait sonuçları inceleyin.

    ![Sorgu sonuç kümesi](media/web-query-data/result-set-03.png)

    Bu gruplandırma türü keşif analizi sırasında kullanışlı olabilir.

1. Farenizi **Group** sütununun üzerine getirip **Sütunları sıfırla**'yı seçin.

    ![Sütunları sıfırla](media/web-query-data/reset-columns.png)

    Bu işlem kılavuzu özgün durumuna döndürür.

1. Aşağıdaki sorguyu çalıştırın.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. Kılavuzun sağ tarafında **Sütunlar**'ı seçerek araç panelini açın.

    ![Araç paneli](media/web-query-data/tool-panel.png)

    Bu panel Excel'deki özet tablo alan listesine benzerdir ve doğrudan kılavuzda daha ayrıntılı analiz gerçekleştirmenizi sağlar.

1. Seçin **Pivot modu**, sütunları'ı şu şekilde sürükleyin: **Durum** için **satır grupları**; **DamageProperty** için **değerleri**; ve **EventType** için **sütun etiketleri**.  

    ![Özet modu](media/web-query-data/pivot-mode.png)

    Sonuç aşağıdaki özet tabloya benzer olmalıdır.

    ![Özet tablo](media/web-query-data/pivot-table.png)

    Vermont ve Alabama'nın aynı kategoride iki olaya, Texas'ın ise farklı kategorilerde iki olaya sahip olduğunu göreceksiniz. Özet tablolar buna benzer durumları hızla fark etmenizi sağlar. Hızlı analiz için ideal araçlardır.

## <a name="share-queries"></a>Sorguları paylaşma

Çoğu zaman oluşturduğunuz sorguları paylaşmak istersiniz. Kümeye erişimi olan diğer kullanıcıların sorguları çalıştırabilmesi için ayrıntılı bağlantı sunabilirsiniz.

1. Sorgu penceresinde kopyaladığınız ilk sorgu seçin.

1. Pencerenin en üstündeki **Paylaş**’ı seçin.

1. **Bağlantı, panoya kopyala**'yı seçin.

1. Bağlantıyı ve sorguyu bir metin dosyasına kopyalayın.

1. Bağlantıyı yeni bir tarayıcı penceresine yapıştırın. Sorgu çalıştıktan sonra sonuç aşağıdakine benzer olmalıdır.

    ![Paylaşılan sorgu](media/web-query-data/shared-query.png)

## <a name="provide-feedback"></a>Geri bildirimde bulunma

Veri Gezgini şu anda önizleme aşamasında ve deneyimlerinizle ilgili geri bildirimleriniz bizim için çok önemli. Geri bildirimlerinizi hemen veya uygulamayla biraz daha zaman geçirdikten sonra gönderebilirsiniz.

1. Uygulamanın sağ üst köşesindeki geri bildirim simgesini seçin: ![Geri bildirim simgesi](media/web-query-data/icon-feedback.png).

1. Geri bildiriminizi girin ve ardından **Gönder**'i seçin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu hızlı başlangıçta herhangi bir kaynak oluşturmadınız ancak kümelerden birini veya ikisini birden uygulamadan kaldırmak isterseniz kümeye sağ tıklayıp **Bağlantıyı kaldır**'ı seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Veri Gezgini için sorgu yazma](write-queries.md)
