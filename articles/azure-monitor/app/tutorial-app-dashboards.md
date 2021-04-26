---
title: Azure Application Insights’ta özel panolar oluşturma | Microsoft Docs
description: Azure Application Insights’ı kullanarak özel KPI panoları oluşturma öğreticisi.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 7bad0b17bb2fed9d9f54cdd44042ec88708f35fd
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103734"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Azure Application Insights’ı kullanarak özel KPI panoları oluşturma

Azure portalında, her biri farklı kaynak gruplarında ve aboneliklerde yer alan çeşitli Azure kaynaklarındaki verilerin görselleştirildiği kutucular içeren birden çok pano oluşturabilirsiniz.  Uygulamanızın durumunu ve performansını tam olarak gösteren özel panolar oluşturmak için, Azure Application Insights’daki farklı grafikleri ve görünümleri sabitleyebilirsiniz. Bu öğretici, Azure Application Insights’taki çeşitli veri ve görselleştirme türlerini içeren özel bir pano oluşturma konusunda size rehberlik etmektedir.

 Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Azure’da özel bir pano oluşturma
> * Kutucuk Galerisinden kutucuk ekleme
> * Application Insights’taki standart ölçümleri panoya ekleme
> * Özel bir Application Insights ölçüm grafiğini panoya ekleme
> * Bir Günlükler (Analiz) sorgusunun sonuçlarını panoya ekleme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- Azure’a .NET uygulaması dağıtma ve [Application Insights SDK’sını etkinleştirme](../app/asp-net.md).

> [!NOTE]
> Panolarla çalışmak için gerekli izinler, [panolar için erişim denetimini anlama](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards)makalesinde açıklanmaktadır.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-a-new-dashboard"></a>Yeni pano oluşturma

> [!WARNING]
> Application Insights kaynağınızı farklı bir kaynak grubuna veya aboneliğe taşırsanız, eski kutucukları kaldırarak ve yeni bir konumdaki aynı Application Insights kaynağındaki yeni kutucukları sabitleyerek panoyu el ile güncelleştirmeniz gerekir.

Tek bir pano çeşitli uygulamalardan, kaynak gruplarından ve aboneliklerden gelen kaynakları içerebilir.  Uygulamanız için yeni bir pano oluşturarak öğreticiyi kullanamaya başlayın.  

1. Azure portal sol taraftaki menü açılan menüsünde, **Pano**' yı seçin.

    ![Azure Portal menü açılan kutusu](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. Pano bölmesinde **Yeni Pano** ' yı ve **boş Pano**' yı seçin.

   ![Yeni pano](media/tutorial-app-dashboards/new-dashboard.png)

3. Pano için bir ad yazın.
4. Panonuza ekleyebileceğiniz kutucuk çeşitleri için **Kutucuk Galerisi**’ne göz atın.  Galeriden kutucuk eklemenin yanı sıra, grafikleri ve diğer görünümleri doğrudan Application Insights Pano 'ya sabitleyebilirsiniz.
5. **Markdown** kutucuğunu bulun ve panonuza sürükleyin.  Bu kutucuk, panonuza açıklayıcı metin eklemek için ideal olan markaşağı içinde biçimlendirilen metin eklemenize olanak tanır. Daha fazla bilgi için bkz. [özel içerik göstermek Için Azure panolarında markaşağı kutucuk kullanma](../../azure-portal/azure-portal-markdown-tile.md).
6. Metni kutucuğun özelliklerine ekleyin ve pano tuvalinizde yeniden boyutlandırın.

    [![Markdown kutucuğunu düzenleme](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/markdown.png#lightbox)

7. Kutucuk özelleştirme modundan çıkmak için ekranın üst kısmındaki **özelleştirmeyi bitti** ' yi seçin.

## <a name="add-health-overview"></a>Sistem durumuna genel bakış ekleme

Statik metin içeren bir pano çok ilginç değildir, bu nedenle artık Application Insights bir kutucuk ekleyerek uygulamanız hakkındaki bilgileri gösterebilirsiniz. Kutucuk Galerisi’nden Application Insights kutucukları ekleyebilir veya bunları Application Insights ekranlarından doğrudan sabitleyebilirsiniz. Bu, bildiğiniz grafikleri ve görünümleri panonuza sabitlemeden önce yapılandırmanıza olanak tanır.  İlk olarak uygulamanız için sistem durumuna standart genel bakışı ekleyin.  Bu işlem yapılandırma gerektirmez ve panoda çok az özelleştirme yapmaya izin verir.


1. Giriş ekranında **Application Insights** kaynağını seçin.
2. **Genel bakış** bölmesinde, ![ ](media/tutorial-app-dashboards/pushpin.png) kutucuğu bir panoya eklemek için raptiye simgesini sabitle simgesini seçin.
3. "Panoya sabitle" sekmesinde, kutucuğun ekleneceği panoyu seçin veya yeni bir tane oluşturun.
 
3. Sağ üst köşede, Kutucuğunuz panonuza sabitlendiğini belirten bir bildirim görüntülenir.  Panonuza geri dönmek veya Pano bölmesini kullanmak için bildirimde **sabitlenmiş olarak sabitlenmiş** ' i seçin.
4. Bu kutucuk artık panonuza eklenir. Kutucuğun konumunu değiştirmek için **Düzenle** ' yi seçin. Seçin ve konuma sürükleyin ve ardından **özelleştirmeyi bitti**' yi seçin. Artık panonuz yararlı bilgiler içeren bir kutucuğa sahip olur.

    [![Düzenleme modunda Pano](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Özel ölçüm grafiği ekleme

**Ölçümler** paneli, isteğe bağlı filtreler ve gruplandırma ile zaman içinde Application Insights tarafından toplanan bir ölçüm grafiği oluşturmanıza olanak tanır.  Application Insights’taki her öğe gibi bu grafiği de panoya ekleyebilirsiniz.  Bu işlem öncelikle birkaç basit özelleştirme gerektirir.

1. Giriş ekranında **Application Insights** kaynağını seçin.
1. **Ölçümler**’i seçin.  
2. Boş bir grafik oluşturulur ve bir ölçüm eklemeniz istenir.  Grafiğe bir ölçüm ve isteğe bağlı olarak bir filtre ve gruplandırma ekleyin.  Aşağıdaki örnekte başarı ölçütüne göre gruplandırılmış sunucu isteklerinin sayısı gösterilmektedir.  Bu, başarılı ve başarısız isteklerin sürekli bir görünümünü sunar.

    [![Ölçüm ekleme](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Sağ tarafta **panoya sabitle ' yi** seçin.

3.  Sağ üst köşede, Kutucuğunuz panonuza sabitlendiğini belirten bir bildirim görüntülenir. Panonuza geri dönmek veya Pano sekmesini kullanmak için bildirimde **sabitlenmiş olarak sabitlenmiş** ' i seçin.

4. Bu kutucuk artık panonuza eklenir. Kutucuğun konumunu değiştirmek için **Düzenle** ' yi seçin. Kutucuğu seçin ve konuma sürükleyin ve ardından **özelleştirmeyi bitti**' yi seçin.

## <a name="add-logs-query"></a>Günlük sorgusu Ekle

Azure Application Insights günlükleri, toplanan Application Insights tüm verileri analiz etmenizi sağlayan zengin bir sorgu dili sağlar. Grafikler ve diğer görünümlerde olduğu gibi, bir günlük sorgusunun çıkışını panonuza ekleyebilirsiniz.

1. Giriş ekranında **Application Insights** kaynağını seçin.
2. "İzleme" altında sol taraftaki **Günlükler** ' i seçerek Günlükler sekmesini açın.
3. En çok istekte bulunulan 10 sayfayı ve bunlara ait istek sayılarını döndüren şu sorguyu yazın:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Sorgunun sonuçlarını doğrulamak için **Çalıştır** ' ı seçin.
5. Bu görselleştirmenin panonuzda da gösterilmesi için raptiye simgesini ![Raptiye simgesi](media/tutorial-app-dashboards/pushpin.png) ve panonun adını seçin.

5. Panoya geri dönerek bir sorgu ekleyin, ancak bir panodaki günlük sorgusunu görselleştirmenin farklı yollarını görmeniz için bir grafik olarak işlenir. En çok istisnayı içeren ilk 10 işlemi özetleyen aşağıdaki sorguyu kullanın.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. **Grafik** seçeneğini belirleyin ve ardından çıktıyı görselleştirmek için seçimi **Halka** olarak değiştirin.

    [![Yukarıdaki sorguyla halka grafik](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Bu görselleştirmenin panonuzda da gösterilmesi için raptiye simgesini ![Raptiye simgesi](media/tutorial-app-dashboards/pushpin.png) sağ üst köşede, grafiği panonuza sabitleyin ve ardından panonuza geri dönün.
7. Sorguların sonuçları seçtiğini biçimde panonuza eklenir. Her bir konumu seçin ve sürükleyin ve ardından **özelleştirmeyi bitti**' yi seçin.
8. Kalem simgesini seçin ![Kalem simgesi](media/tutorial-app-dashboards/pencil.png) Her başlıkta açıklayıcı bir başlık verin.

## <a name="share-dashboard"></a>Panoyu paylaş

1. Yaptığınız değişiklikleri yayımlamak için panonun en üstünde bulunan **paylaşma** ' yı seçin.
2. İsteğe bağlı olarak panoya erişebilecek belirli kullanıcıları tanımlayabilirsiniz. Daha fazla bilgi için bkz. [Azure 'un rol tabanlı erişim denetimi kullanarak Azure panoları paylaşma](../../azure-portal/azure-portal-dashboard-share-access.md).
3. **Yayımla**’yı seçin.

## <a name="next-steps"></a>Sonraki adımlar

Artık nasıl özel pano oluşturulacağını öğrendiniz. Bir örnek olay içeren diğer Application Insights belgelerine de göz atabilirsiniz.

> [!div class="nextstepaction"]
> [Derin tanılama](../app/devops.md)
