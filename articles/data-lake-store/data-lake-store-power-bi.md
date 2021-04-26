---
title: Azure Data Lake Storage 1. verileri analiz etme Power BI
description: Azure Data Lake Storage 1. depolanan verileri analiz etmek ve görselleştirmek için Power BI Desktop kullanmayı öğrenin.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91574203"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Azure Data Lake Storage 1. Power BI kullanarak verileri çözümleme
Bu makalede, Azure Data Lake Storage 1. depolanan verileri analiz etmek ve görselleştirmek için Power BI Desktop kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiye başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Data Lake Storage 1. hesabı**. [Azure Portal kullanarak Azure Data Lake Storage 1. kullanmaya başlama](data-lake-store-get-started-portal.md)yönergelerini izleyin. Bu makalede, **myadlsg1** adlı bir Data Lake Storage 1. hesabı oluşturmuş olduğunuz ve buna örnek bir veri dosyası (**Drivers.txt**) yüklediğiniz varsayılır. Bu örnek dosya [Azure Data Lake git deposundan](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)indirilebilir.
* **Power BI Desktop**. Bunu [Microsoft Indirme merkezi](https://www.microsoft.com/en-us/download/details.aspx?id=45331)' nden indirebilirsiniz. 

## <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop’ta rapor oluşturma
1. Bilgisayarınızda Power BI Desktop başlatın.
2. **Giriş** şeridinde **veri al**' a ve ardından daha fazla ' ya tıklayın. **Veri al** Iletişim kutusunda **Azure**' a, **Azure Data Lake Store**' a ve ardından **Bağlan**' a tıklayın.
   
    ![Azure Data Lake Store seçeneği vurgulanmış ve Connect seçeneği olarak adlandırılan veri Al iletişim kutusunun ekran görüntüsü.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Data Lake Storage 1. Bağlan")
3. Bir geliştirme aşamasında bağlayıcı hakkında bir iletişim kutusu görürseniz devam etmeyi tercih edin.
4. **Azure Data Lake Store** iletişim kutusunda, Data Lake Storage 1. hesabınızın URL 'sini girin ve ardından **Tamam**' a tıklayın.
   
    ![Data Lake Storage 1. URL 'SI](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Data Lake Storage 1. URL 'SI")
5. Sonraki iletişim kutusunda Data Lake Storage 1. hesabında oturum açmak için **oturum aç** ' a tıklayın. Kuruluşunuzun oturum açma sayfasına yönlendirilirsiniz. Hesapta oturum açmak için istemleri izleyin.
   
    ![Data Lake Storage 1. oturum aç](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Data Lake Storage 1. oturum aç")
6. Başarıyla oturum açtıktan sonra **Bağlan**' a tıklayın.
   
    ![Connect seçeneği olarak adlandırılan Azure Data Lake Store iletişim kutusunun ekran görüntüsü.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Data Lake Storage 1. Bağlan")
7. Sonraki iletişim kutusunda Data Lake Storage 1. hesabınıza yüklediğiniz dosya gösterilir. Bilgileri doğrulayıp **Yükle**' ye tıklayın.
   
    ![Data Lake Storage 1. verileri yükleme](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Data Lake Storage 1. verileri yükleme")
8. Veriler Power BI başarıyla yüklendikten sonra, **alanlar** sekmesinde aşağıdaki alanları görürsünüz.
   
    ![İçeri aktarılan alanlar](./media/data-lake-store-power-bi/imported-fields.png "İçeri aktarılan alanlar")
   
    Ancak, verileri görselleştirmek ve analiz etmek için, aşağıdaki alanlar uyarınca verilerin kullanılabilir olmasını tercih ediyoruz
   
    ![İstenen alanlar](./media/data-lake-store-power-bi/desired-fields.png "İstenen alanlar")
   
    Sonraki adımlarda, içeri aktarılan verileri istenen biçimde dönüştürmek için sorguyu güncelleştireceğiz.
9. **Giriş** şeridinde **sorguları Düzenle**' ye tıklayın.
   
    ![Sorguları Düzenle seçeneği olarak adlandırılan giriş şeridinin ekran görüntüsü.](./media/data-lake-store-power-bi/edit-queries.png "Sorguları düzenle")
10. Sorgu Düzenleyicisi 'nde, **içerik** sütununun altında, **ikili**' a tıklayın.
    
    ![Içerik sütunuyla çağrılan sorgu düzenleyicisinin ekran görüntüsü.](./media/data-lake-store-power-bi/convert-query1.png "Sorguları düzenle")
11. Karşıya yüklediğiniz **Drivers.txt** dosyasını temsil eden bir dosya simgesi görürsünüz. Dosyaya sağ tıklayın ve **CSV**' ye tıklayın.    
    
    ![CSV seçeneği olarak adlandırılan sorgu düzenleyicisinin ekran görüntüsü.](./media/data-lake-store-power-bi/convert-query2.png "Sorguları düzenle")
12. Aşağıda gösterildiği gibi bir çıktı görmeniz gerekir. Verileriniz artık görselleştirmeler oluşturmak için kullanabileceğiniz bir biçimde sunulmaktadır.
    
    ![Çıktı beklenen şekilde görüntülenirken sorgu düzenleyicisinin ekran görüntüsü.](./media/data-lake-store-power-bi/convert-query3.png "Sorguları düzenle")
13. **Giriş** şeridinde **Kapat ve Uygula**' yı ve ardından **Kapat ve Uygula**' yı tıklatın.
    
    ![Kapat ve Uygula seçeneğiyle birlikte giriş şeridinin ekran görüntüsü.](./media/data-lake-store-power-bi/load-edited-query.png "Sorguları düzenle")
14. Sorgu güncelleştirildikten sonra **alanlar** sekmesi görselleştirme için kullanılabilen yeni alanları gösterir.
    
    ![Güncelleştirilmiş alanlar](./media/data-lake-store-power-bi/updated-query-fields.png "Güncelleştirilmiş alanlar")
15. Belirli bir ülke/bölge için her bir şehirde bulunan sürücüleri temsil eden bir pasta grafiği oluşturalım. Bunu yapmak için aşağıdaki seçimleri yapın.
    
    1. Görsel öğeler sekmesinden pasta grafiğinin simgesine tıklayın.
       
        ![Pasta grafik oluştur](./media/data-lake-store-power-bi/create-pie-chart.png "Pasta grafik oluştur")
    2. Kullandığımız sütunlar **sütun 4** ' ün (şehir adı) ve **7. sütun** (ülke/bölge adı). Bu sütunları **alanlar** sekmesinden, aşağıda gösterildiği gibi **görselleştirmeler** sekmesine sürükleyin.
       
        ![Görselleştirmeler oluştur](./media/data-lake-store-power-bi/create-visualizations.png "Görselleştirmeler oluştur")
    3. Pasta grafiğinin şimdi aşağıda gösterildiği gibi bir benzemesi gerekir.
       
        ![Pasta grafik](./media/data-lake-store-power-bi/pie-chart.png "Görselleştirmeler oluştur")
16. Sayfa düzeyi filtrelerinden belirli bir ülke/bölge seçerek, seçili ülkenin/bölgenin her bir şehrindeki sürücü sayısını görebilirsiniz. Örneğin, **görsel öğeler** sekmesinin altında, **sayfa düzeyi filtreleri** altında **Brezilya**' yi seçin.
    
    ![Ülke/bölge seçin](./media/data-lake-store-power-bi/select-country.png "Ülke/bölge seçin")
17. Pasta grafiği, Brezilya şehirlerde bulunan sürücüleri görüntüleyecek şekilde otomatik olarak güncelleştirilir.
    
    ![Ülke/bölge içindeki sürücüler](./media/data-lake-store-power-bi/driver-per-country.png "Ülke/bölge başına sürücü")
18. Görselleştirmeyi bir Power BI Desktop dosyası olarak kaydetmek için **Dosya** menüsünde **Kaydet** ' e tıklayın.

## <a name="publish-report-to-power-bi-service"></a>Raporu Power BI hizmeti yayınla
Power BI Desktop görselleştirmeleri oluşturduktan sonra, Power BI hizmeti yayımlayarak başkalarıyla paylaşabilirsiniz. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [Power BI Desktop yayımlama](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Ayrıca bkz.
* [Data Lake Analytics kullanarak Data Lake Storage 1. verileri çözümleme](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

