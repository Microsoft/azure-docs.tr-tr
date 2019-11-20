---
title: Verileri Azure 'a almak için tek tıklamayla alma 'yı kullanın Veri Gezgini
description: Yalnızca tek tıklamayla alma kullanarak verileri Azure Veri Gezgini 'a alma (yükleme) hakkında bilgi edinin.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 98598a28e14dfd8175cbb019ff1b001c65503580
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73644588"
---
# <a name="use-one-click-ingestion-to-ingest-data-into-azure-data-explorer"></a>Verileri Azure 'a almak için tek tıklamayla alma 'yı kullanın Veri Gezgini

Bu makalede, Azure Veri Gezgini JSON veya CSV biçimlerinde yeni bir tablonun hızlı alımı için tek tıklamayla alma işlemlerinin nasıl kullanılacağı gösterilmektedir. Veriler alındıktan sonra, tabloyu düzenleyebilir ve sorguları Web Kullanıcı arabirimini kullanarak çalıştırabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun.
* [Uygulamada](https://dataexplorer.azure.com/) oturum açın.
* [Azure Veri Gezgini kümesi ve veritabanı](create-cluster-database-portal.md) oluşturma
* [Web Kullanıcı arabiriminde](https://dataexplorer.azure.com/) oturum açın ve [kümenize bir bağlantı ekleyin](/azure/data-explorer/web-query-data#add-clusters)
* Azure depolama 'daki veri kaynağı.

## <a name="ingest-new-data"></a>Yeni verileri alma

1. Web Kullanıcı arabiriminin sol taraftaki menüsünde *veritabanı* veya *tablo* satırına sağ tıklayın ve yeni verileri Al ' ı **(Önizleme)** seçin.

    ![Web Kullanıcı arabiriminde bir tıklama alımı seçin](media/ingest-data-one-click/one-click-ingestion-in-webui.png)   
 
1. **Yeni verileri al (Önizleme)** penceresindeki **kaynak** sekmesinde, **proje ayrıntılarını**doldurun:

    * **Tablo**: açılan listeden mevcut tablo adı ' nı seçin veya yeni bir tablo **oluşturmak için yeni oluştur** ' u seçin.
    * **Depolama alanından** **veya dosyadan**alma **türü** > seçin.
        * **Depolama alanından**seçtiyseniz, URL 'yi depolamaya eklemek Için **depolama bağlantısı** ' nı girin. Özel depolama hesapları için [BLOB SAS URL 'sini](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) kullanın. 
        * **Dosya**' yı seçtiyseniz, **Araştır** ' ı seçin ve dosyayı kutuya sürükleyin.
    * Tablo sütunu yapılandırmanızı görüntülemek ve düzenlemek için **Şemayı Düzenle** ' yi seçin.
 
    ![bir tıklama kaynağı ayrıntıları](media/ingest-data-one-click/one-click-ingestion-source.png) 

    > [!TIP]
    > *Tablo* satırında **Yeni veri al (Önizleme)** seçeneğini belirlerseniz, seçilen tablo adı **Proje ayrıntılarında**görüntülenir.

1. Varolan bir tabloyu seçtiyseniz, kaynak veri sütunlarını hedef tablo sütunlarıyla eşlemek için **harita sütunları** penceresi açılır. 
    * Tablodan bir hedef sütunu kaldırmak için **sütunu atla** ' yı kullanın. 
    * **Yeni** sütun kullanarak tablonuza yeni bir sütun ekleyin. 

    ![Harita sütunları penceresi](media/ingest-data-one-click/one-click-map-columns-window.png)

1. **Şema** sekmesinde:

    * **Sıkıştırılmamış** veya **gzip**> açılan listesinden **sıkıştırma türünü** seçin.
    * **JSON**, **CSV**, **TSV**, **SCSV**, **sohsv**, **tsve**veya **PSV**> açılan listesinden **veri biçimini** seçin. 
        * **JSON** biçimini seçtiğinizde **json düzeyleri**: 1-10 ' i seçin. Düzeyler tablo sütunu veri gösterimi ' nı etkiler. 
        * JSON dışında bir biçim seçerseniz: dosyanın başlık satırını yoksaymak için **sütun adlarını Ekle** onay kutusunu seçin.    
    * **Eşleme adı** otomatik olarak ayarlanır, ancak düzenlenebilir.
    * Varolan bir tabloyu seçtiyseniz **, sütunları eşle düğmesini seçerek** **harita sütunları** penceresini açabilirsiniz.

    ![tek tıklamayla alma CSV biçimi Schema. png](media/ingest-data-one-click/one-click-csv-format.png)

1. **Düzenleyici**' de sağ taraftaki **V** ' yi seçerek düzenleyiciyi açın. Düzenleyicide, girdinizden oluşturulan otomatik sorguları görüntüleyebilir ve kopyalayabilirsiniz. 

1.  Tabloda: 
    * Yeni sütun başlıklarına sağ tıklayarak **veri türünü değiştirin**, **sütunu yeniden adlandırın**, **sütunu silin**, **artan düzende sıralayın**veya **azalan düzende sıralayın**. Mevcut sütunlarda yalnızca veri sıralama kullanılabilir. 
    * Düzenlemek için yeni sütun adına çift tıklayın.

1. Tablo oluşturmak, eşleme oluşturmak ve veri alımı yapmak için **Başlangıç** alımı ' nı seçin.

    ![Tek tıklama alma JSON biçim şeması](media/ingest-data-one-click/one-click-json-format.png) 
 
## <a name="query-data"></a>Verileri sorgulama

1. Veri alma **işlemi tamamlandı** penceresinde, veri alımı başarıyla tamamlanırsa tüm üç adım yeşil onay işaretleriyle işaretlenir. 
 
    ![bir tıklama veri alımı Tamam](media/ingest-data-one-click/one-click-data-ingestion-complete.png)

1. Sorguyu açmak için **V** 'yi seçin. Sorguyu düzenlemek için Web Kullanıcı arabirimine kopyalayın.

1. Sağdaki menü **hızlı sorguları** ve **araçları**içerir. 

    * **Hızlı sorgular** , örnek sorgularla Web Kullanıcı arabirimine bağlantılar içerir.
    * **Araçlar** , ilgili `.drop` komutunu çalıştırarak sorunları gidermenize olanak sağlayan **bırakma komutlarıyla** Web Kullanıcı arabirimine bağlantı içerir.

    > [!TIP]
    > `.drop` komutları kullanılarak veriler kaybolabilir. Bunları dikkatle kullanın.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Veri Gezgini Web Kullanıcı arabirimindeki verileri sorgulama](web-query-data.md)
* [Kusto sorgu dilini kullanarak Azure Veri Gezgini için sorgular yazma](write-queries.md)
