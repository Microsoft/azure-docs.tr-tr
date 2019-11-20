---
title: Azure Application Insights’ta özel panolar oluşturma | Microsoft Docs
description: Azure Application Insights’ı kullanarak özel KPI panoları oluşturma öğreticisi.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.custom: mvc
ms.openlocfilehash: 47d8eaff5f154e198c277ec2b63a2d09e66f7180
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900499"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Azure Application Insights’ı kullanarak özel KPI panoları oluşturma

Azure portalında, her biri farklı kaynak gruplarında ve aboneliklerde yer alan çeşitli Azure kaynaklarındaki verilerin görselleştirildiği kutucular içeren birden çok pano oluşturabilirsiniz.  Uygulamanızın durumunu ve performansını tam olarak gösteren özel panolar oluşturmak için, Azure Application Insights’daki farklı grafikleri ve görünümleri sabitleyebilirsiniz. Bu öğretici, Azure Application Insights’taki çeşitli veri ve görselleştirme türlerini içeren özel bir pano oluşturma konusunda size rehberlik etmektedir.  Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Azure’da özel bir pano oluşturma
> * Kutucuk Galerisinden kutucuk ekleme
> * Application Insights’taki standart ölçümleri panoya ekleme
> * Özel bir Application Insights ölçüm grafiğini panoya ekleme
> * Bir Günlükler (Analiz) sorgusunun sonuçlarını panoya ekleme



## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

- Azure’a .NET uygulaması dağıtma ve [Application Insights SDK’sını etkinleştirme](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Azure'da oturum açın
[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="create-a-new-dashboard"></a>Yeni pano oluşturma
Tek bir pano çeşitli uygulamalardan, kaynak gruplarından ve aboneliklerden gelen kaynakları içerebilir.  Uygulamanız için yeni bir pano oluşturarak öğreticiyi kullanamaya başlayın.  

1. Pano bölmesinde **Yeni Pano**' yı seçin.

   ![Yeni pano](media/tutorial-app-dashboards/1newdashboard.png)

1. Pano için bir ad yazın.
1. Panonuza ekleyebileceğiniz kutucuk çeşitleri için **Kutucuk Galerisi**’ne göz atın.  Galeriden kutucuk eklemenin yanı sıra, grafikleri ve diğer görünümleri doğrudan Application Insights Pano 'ya sabitleyebilirsiniz.
1. **Markdown** kutucuğunu bulun ve panonuza sürükleyin.  Bu kutucuk, panonuza açıklayıcı metin eklemek için ideal olan markaşağı içinde biçimlendirilen metin eklemenize olanak tanır.
1. Metni kutucuğun özelliklerine ekleyin ve pano tuvalinizde yeniden boyutlandırın.
    
    ![Markdown kutucuğunu düzenleme](media/tutorial-app-dashboards/2dashboard-text.png)

1. Kutucuk özelleştirme modundan çıkmak için ekranın üst kısmındaki **özelleştirmeyi bitti** ' ye tıklayın.

## <a name="add-health-overview"></a>Sistem durumuna genel bakış ekleme
Statik metin içeren bir pano çok ilginç değildir, bu nedenle artık Application Insights bir kutucuk ekleyerek uygulamanız hakkındaki bilgileri gösterebilirsiniz.  Kutucuk Galerisi’nden Application Insights kutucukları ekleyebilir veya bunları Application Insights ekranlarından doğrudan sabitleyebilirsiniz.  Bu, bildiğiniz grafikleri ve görünümleri panonuza sabitlemeden önce yapılandırmanıza olanak tanır.  İlk olarak uygulamanız için sistem durumuna standart genel bakışı ekleyin.  Bu işlem yapılandırma gerektirmez ve panoda çok az özelleştirme yapmaya izin verir.


1. Giriş ekranında **Application Insights** kaynağını seçin.
2. **Genel bakış** bölmesinde, kutucuğu simgenin](media/tutorial-app-dashboards/pushpin.png) ![sabitleme simgesine tıklayarak, kutucuğu görüntülediğiniz son panoya ekleyin.  
 
3. Sağ üst köşede, Kutucuğunuz panonuza sabitlendiğini belirten bir bildirim görüntülenir. Panonuza geri dönmek veya Pano bölmesini kullanmak için bildirimde **sabitlenmiş ' e** tıklayın.
4. Bu kutucuk artık panonuza eklenir. Kutucuğun konumunu değiştirmek için **Düzenle** ' yi seçin. Tıklayın ve konuma sürükleyin ve ardından **özelleştirmeyi bitti**' ye tıklayın. Artık panonuz yararlı bilgiler içeren bir kutucuğa sahip olur.

    ![Zaman çizelgesine genel bakış içeren pano](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Özel ölçüm grafiği ekleme
**Ölçümler** paneli, isteğe bağlı filtreler ve gruplandırma ile zaman içinde Application Insights tarafından toplanan bir ölçüm grafiği oluşturmanıza olanak tanır.  Application Insights’taki her öğe gibi bu grafiği de panoya ekleyebilirsiniz.  Bu işlem öncelikle birkaç basit özelleştirme gerektirir.

1. Giriş ekranında **Application Insights** kaynağını seçin.
1. **Ölçümler**’i seçin.  
2. Boş bir grafik oluşturulur ve bir ölçüm eklemeniz istenir.  Grafiğe bir ölçüm ve isteğe bağlı olarak bir filtre ve gruplandırma ekleyin.  Aşağıdaki örnekte başarı ölçütüne göre gruplandırılmış sunucu isteklerinin sayısı gösterilmektedir.  Bu, başarılı ve başarısız isteklerin sürekli bir görünümünü sunar.

    ![Ölçüm ekleme](media/tutorial-app-dashboards/metrics.png)

4. Sağ tarafta **panoya sabitle ' yi** seçin. Bu, görünümü üzerinde çalıştığınız en son panoya ekler.

3.  Sağ üst köşede, Kutucuğunuz panonuza sabitlendiğini belirten bir bildirim görüntülenir. Panonuza geri dönmek veya Pano dikey penceresini kullanmak için bildirimde **sabitlenmiş ' e** tıklayın.

4. Bu kutucuk artık panonuza eklenir. Kutucuğun konumunu değiştirmek için **Düzenle** ' yi seçin. Tıklayın ve konuma sürükleyin ve ardından **özelleştirmeyi bitti**' ye tıklayın.

## <a name="add-logs-analytics-query"></a>Günlük ekleme (Analiz) sorgusu
Azure Application Insights günlükleri (Analiz), toplanan Application Insights tüm verileri analiz etmenizi sağlayan zengin bir sorgu dili sağlar. Grafikler ve diğer görünümlerde olduğu gibi, bir günlük sorgusunun çıkışını panonuza ekleyebilirsiniz.

Azure Applications Insights günlükleri (Analiz) ayrı bir hizmet olduğundan, bir günlük sorgusu içerecek şekilde panonuzu paylaşmanız gerekir. Bir Azure panosunu paylaştığınızda, bir Azure kaynağı olarak yayımlarsınız ve bu, diğer kullanıcılar ve kaynaklar için kullanılabilir hale gelir.  

1. Pano ekranının üst kısmındaki **Paylaş** seçeneğine tıklayın.

    ![Panoyu yayımlama](media/tutorial-app-dashboards/8dashboard-share.png)

2. Panoyu paylaşmak için **Pano adını** aynı tutun ve **Abonelik Adı**’nı.  **Yayımla**’ta tıklayın.  Pano artık diğer hizmetler ve abonelikler tarafından kullanılabilir.  İsteğe bağlı olarak panoya erişebilecek belirli kullanıcıları tanımlayabilirsiniz.
1. Giriş ekranında **Application Insights** kaynağını seçin.
2. İzleme altında sol taraftaki **Günlükler (Analiz)** öğesine tıklayarak Günlükler (Analiz) portalını açın.
3. En çok istekte bulunulan 10 sayfayı ve bunlara ait istek sayılarını döndüren şu sorguyu yazın:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Sorgunun sonuçlarını doğrulamak için **Çalıştır** ' a tıklayın.
5. Sabitleme simgesine tıklayın ![PIN simgesi](media/tutorial-app-dashboards/pushpin.png) ve panonun adını seçin. Bu seçeneğin, son panonun kullanıldığı önceki adımların aksine bir panoyu seçtiğinizde, Günlükler (Analiz) konsolunun ayrı bir hizmet olması ve kullanılabilir tüm paylaşılan panolardan seçim yapması gerekir.

5. Panoya geri geçmeden önce başka bir sorgu ekleyin, ancak bu kez bir pano olarak işlenir, böylece bir panodaki günlük sorgusunu görselleştirmek için farklı yollar görürsünüz. En çok istisnayı içeren ilk 10 işlemi özetleyen aşağıdaki sorguyu kullanın.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. **Grafik** seçeneğini belirleyin ve ardından çıktıyı görselleştirmek için seçimi **Halka** olarak değiştirin.

    ![Günlükler (Analiz) grafiği](media/tutorial-app-dashboards/11querychart.png)

6. Sabitleme simgesine tıklayın ![PIN simgesi](media/tutorial-app-dashboards/pushpin.png) grafiği panonuza sabitlemek için sağ üst köşede, bu kez panonuza geri dönmek için bağlantıyı seçin.
4. Sorguların sonuçları seçtiğini biçimde panonuza eklenir.  Tıklayın ve her bir konuma sürükleyin ve ardından **özelleştirmeyi bitti**' ye tıklayın.
5. Kalem simgesini seçin ![Kalem simgesi](media/tutorial-app-dashboards/pencil.png) Her başlıkta açıklayıcı bir başlık verin.

5. Artık Application Insights ' den çok sayıda grafik ve görselleştirme içeren yaptığınız değişiklikleri panonuza yeniden yayımlamak için **Share** ' u seçin.


## <a name="next-steps"></a>Sonraki adımlar
Artık nasıl özel pano oluşturulacağını öğrendiniz. Bir örnek olay içeren diğer Application Insights belgelerine de göz atabilirsiniz.

> [!div class="nextstepaction"]
> [Derin tanılama](../../azure-monitor/app/devops.md)
