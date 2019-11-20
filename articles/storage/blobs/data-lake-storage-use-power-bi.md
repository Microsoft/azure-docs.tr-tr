---
title: Power BI kullanarak Azure Data Lake Storage 2. verileri analiz etme | Microsoft Docs
description: Azure Data Lake Storage 2. depolanan verileri çözümlemek için Power BI kullanın
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: d76ea317271ae0e8eb0d54fcfee5dc005d836fc1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984954"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Azure Data Lake Storage 2. Power BI kullanarak verileri çözümleme

Bu makalede, hiyerarşik ad alanı (Azure Data Lake Storage 2.) olan bir depolama hesabında depolanan verileri analiz etmek ve görselleştirmek için Power BI Desktop kullanmayı öğreneceksiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce aşağıdakilere sahip olmanız gerekir:

> [!div class="checklist"]
> * Azure aboneliği. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).
> * Hiyerarşik bir ad alanına sahip bir depolama hesabı. Bir tane oluşturmak için [Bu](data-lake-storage-quickstart-create-account.md) yönergeleri izleyin.
> Bu makalede adlı `myadlsg2`bir hesap oluşturduğunuz varsayılır.
> * Depolama hesabınızda bulunan adlı `Drivers.txt` örnek bir veri dosyası.
> Bu örneği [Azure Data Lake git deposundan](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)indirebilir ve ardından bu dosyayı depolama hesabınıza yükleyebilirsiniz.
> * **Power BI Desktop**. Bunu [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=45331)' nden indirebilirsiniz. 

## <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop'ta rapor oluşturma

1. Bilgisayarınızda Power BI Desktop başlatın.
2. Şeridin **giriş** sekmesinde **veri al**' a ve ardından **daha fazla**' ya tıklayın.
3. **Veri al** Iletişim kutusunda **Azure**' a, **Azure Data Lake Store Gen2 (Beta)** öğesine ve ardından **Bağlan**' a tıklayın.

    ![Veri Al sayfası](media/data-lake-storage-use-power-bi/get-data-page.png)

4. **Azure Data Lake Storage 2.** iletişim kutusunda, kapsayıcı uç nokta biçimini kullanarak Azure Data Lake Storage 2. hesabınıza, dosya sistemine veya alt klasörüne URL sağlayabilirsiniz. Data Lake Storage 2. URL 'leri aşağıdaki düzene `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` sahiptir ve ardından **Tamam**' a tıklayın.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. Sonraki iletişim kutusunda, depolama hesabınızda oturum açmak için **oturum aç** ' a tıklayın. Kuruluşunuzun oturum açma sayfasına yönlendirilirsiniz. Hesapta oturum açmak için istemleri izleyin.

    ![Sayfada oturum aç](media/data-lake-storage-use-power-bi/sign-in.png)

6. Başarıyla oturum açtıktan sonra **Bağlan**' a tıklayın.

    ![Oturum açık sayfası](media/data-lake-storage-use-power-bi/signed-in.png)

7. Sonraki iletişim kutusunda, depolama hesabınıza yüklediğiniz dosyayı da içeren yukarıdaki 4. adımda verdiğiniz URL 'nin altındaki tüm dosyalar gösterilir. Bilgileri doğrulayıp **Yükle**' ye tıklayın.

    ![Dosya sistemleri](media/data-lake-storage-use-power-bi/file-systems.png)

8. Veriler Power BI başarıyla yüklendikten sonra, **alanlar** sekmesinde aşağıdaki alanları görürsünüz.

    ![Alanlar sekmesi](media/data-lake-storage-use-power-bi/fields.png)

    Ancak, verileri görselleştirmek ve analiz etmek için aşağıdaki alanlar uyarınca verilerin kullanılabilir olmasını tercih ediyoruz.

    ![Alanlar](media/data-lake-storage-use-power-bi/preferred-fields.png)

    Sonraki adımlarda, içeri aktarılan verileri istenen biçimde dönüştürmek için sorguyu güncelleştireceğiz.

9. Şeritteki **giriş** sekmesinde **sorguları Düzenle**' ye tıklayın.

    ![Sorgular](media/data-lake-storage-use-power-bi/queries.png)

10. **Sorgu Düzenleyicisi**'Nde, **içerik** sütununun altında, **ikili**' a tıklayın. Dosya otomatik olarak CSV olarak algılanır ve aşağıda gösterildiği gibi bir çıktı görmeniz gerekir. Verileriniz artık görselleştirmeler oluşturmak için kullanabileceğiniz bir biçimde sunulmaktadır.

    ![Output](media/data-lake-storage-use-power-bi/binary.png)

11. Şeritteki **giriş** sekmesinde, **Kapat** ve **Uygula**' yı tıklatın ve ardından **Kapat** ve **Uygula**' yı tıklatın.

    ![Kapat ve uygula](media/data-lake-storage-use-power-bi/close-apply.png)

12. Sorgu güncelleştirildikten sonra **alanlar** sekmesi görselleştirme için kullanılabilen yeni alanları gösterir.

    ![Yeni alanlar](media/data-lake-storage-use-power-bi/new-fields.png)

13. Belirli bir ülkenin her bir şehirde bulunan sürücüleri temsil eden bir pasta grafik oluşturalım. Bunu yapmak için aşağıdaki seçimleri yapın.

    **Görsel öğeler** sekmesinden pasta grafiğinin simgesine tıklayın.

    ![Görsel öğeler](media/data-lake-storage-use-power-bi/visualizations.png)

    Kullandığımız sütunlar, sütun 4 (şehrin adı) ve sütun 7 (ülkenin adı). Bu sütunları **alanlar** sekmesinden, aşağıda gösterildiği gibi **görselleştirmeler** sekmesine sürükleyin.

    ![Alanları sürükle](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    Pasta grafiğinin şimdi aşağıda gösterildiği gibi bir benzemesi gerekir.

    ![Pasta grafiği](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Sayfa düzeyi filtrelerinden belirli bir ülkeyi seçerek artık seçili ülkenin her bir şehrindeki sürücü sayısını görebilirsiniz. Örneğin, **görsel öğeler** sekmesinin altında, **sayfa düzeyi filtreleri**altında **Brezilya**' yi seçin.

    ![Sayfa filtreleri](media/data-lake-storage-use-power-bi/page-filters.png)

15. Pasta grafiği, Brezilya şehirlerde bulunan sürücüleri görüntüleyecek şekilde otomatik olarak güncelleştirilir.

    ![Brezilya](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. Görselleştirmeyi bir Power BI Desktop dosyası olarak kaydetmek için **Dosya** menüsünde **Kaydet** ' e tıklayın.

## <a name="publish-report-to-power-bi-service"></a>Raporu Power BI hizmeti yayınla

Power BI Desktop ' de görselleştirmeleri oluşturduktan sonra, Power BI hizmeti yayımlayarak başkalarıyla paylaşabilirsiniz. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [Power BI Desktop yayımlama](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).
