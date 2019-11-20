---
title: 'Azure Data Factory Spark kullanarak verileri dönüştürme '
description: Bu öğretici, Azure Data Factory'de bir Spark etkinliği kullanarak verileri dönüştürmeye ilişkin adım adım yönergeler sağlar.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: b0df6264eb38cbbacd5f624b634eea5f742adbc7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683334"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Azure Data Factory'de bir Spark etkinliği kullanarak verileri bulutta dönüştürme
Bu öğreticide, Azure portalını kullanarak bir Azure Data Factory işlem hattı oluşturursunuz. Bu işlem hattı bir Spark etkinliği ve isteğe bağlı bir Azure HDInsight bağlı hizmetini kullanarak verileri dönüştürür. 

Bu öğreticide aşağıdaki adımları gerçekleştireceksiniz:

> [!div class="checklist"]
> * Veri fabrikası oluşturma. 
> * Spark etkinliği kullanan bir işlem hattı oluşturun.
> * İşlem hattı çalıştırması tetikleyin.
> * İşlem hattı çalıştırmasını izleme.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure depolama hesabı**. Bir Python betiği ve giriş dosyası oluşturup Azure Depolama hizmetine yüklersiniz. Spark programının çıktısı bu depolama hesabında depolanır. İsteğe bağlı Spark kümesi, birincil depolama alanıyla aynı depolama hesabını kullanır.  

> [!NOTE]
> HdInsight standart katmanda yalnızca genel amaçlı depolama hesaplarını destekler. Hesabın premium veya yalnızca blob depolama hesabı olmadığından emin olun.

* **Azure PowerShell**. [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-Az-ps) bölümündeki yönergeleri izleyin.


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Python betiğini Blob depolama hesabınıza yükleme
1. Aşağıdaki içeriğe sahip **WordCount_Spark.py** adlı bir Python dosyası oluşturun: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. *&lt;storageAccountName&gt;* ’i Azure depolama hesabınızın adıyla değiştirin. Ardından dosyayı kaydedin. 
1. Azure Blob depolama alanında henüz yoksa **adftutorial** adlı bir kapsayıcı oluşturun. 
1. **Spark** adlı bir klasör oluşturun.
1. **Spark** klasörünün altında **script** adlı bir alt klasör oluşturun. 
1. **WordCount_Spark.py** dosyasını **script** alt klasörüne yükleyin. 


### <a name="upload-the-input-file"></a>Girdi dosyasını yükleme
1. Bazı metinlerle **minecraftstory.txt** adlı bir dosya oluşturun. Spark programı bu metindeki sözcükleri sayar. 
1. **Spark** klasörünün altında **inputfiles** adlı bir alt klasör oluşturun. 
1. **minecraftstory.txt** dosyasını **inputfiles** alt klasörüne yükleyin. 

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
1. Soldaki menüden **Yeni**’yi, sonra **Veri ve Analiz**’i ve ardından **Data Factory**’i seçin. 
   
   ![“Yeni” bölmesinde Data Factory seçimi](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. **Yeni veri fabrikası** bölmesinde **Ad** altına **ADFTutorialDataFactory** girin. 
      
   ![“Yeni veri fabrikası” bölmesi](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Azure data factory adı *küresel olarak benzersiz* olmalıdır. Aşağıdaki hatayı görürseniz veri fabrikasının adını değiştirin. (Örneğin, **&lt;adınız&gt;ADFTutorialDataFactory** biçimini kullanın). Data Factory yapıtlarının adlandırma kuralları için [Data Factory - adlandırma kuralları](naming-rules.md) makalesini inceleyin.
  
   ![Bir ad kullanılamadığında alınan hata](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. **Abonelik** için, veri fabrikasını oluşturmak istediğiniz Azure aboneliğini seçin. 
1. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
   - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin. 
   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
   Bu hızlı başlangıçtaki adımlardan bazıları kaynak grubu için **ADFTutorialResourceGroup** adını kullandığınızı varsayar. Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
1. **Sürüm** bölümünde **V2**'yi seçin.
1. **Konum** için, veri fabrikasının konumunu seçin. 

   Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Data Factory tarafından kullanılan veri depoları (Azure Depolama ve Azure SQL Veritabanı) ve işlemler (HDInsight gibi) başka bölgelerde olabilir.

1. **Oluştur**'u seçin.

1. Oluşturma işlemi tamamlandıktan sonra, **Veri fabrikası** sayfasını görürsünüz. Data Factory kullanıcı arabirimi uygulamasını ayrı bir sekmede başlatmak için **Yazar ve İzleyici** kutucuğunu seçin.

    ![Veri fabrikasının “Yazar ve İzleyici” kutucuğuna sahip ana sayfası](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Bu bölümde iki bağlı hizmet oluşturacaksınız: 
    
- Bir Azure depolama hesabını veri fabrikasına bağlayan **Azure Depolama bağlı hizmeti**. Bu depolama alanı, isteğe bağlı HDInsight kümesi tarafından kullanılır. Ayrıca, çalıştırılacak Spark betiğini içerir. 
- **İsteğe bağlı HDInsight bağlı hizmeti**. Azure Data Factory otomatik olarak bir HDInsight kümesi oluşturur ve Spark programını çalıştırır. Daha sonra, küme önceden yapılandırılmış bir süre boyunca boşta kaldığında HDInsight kümesini siler. 

### <a name="create-an-azure-storage-linked-service"></a>Azure Depolama bağlı hizmeti oluşturma

1. **Başlayalım** sayfasında, sol bölmede bulunan **Düzenle** sekmesine geçin. 

   ![“Başlayalım” sayfası](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. Pencerenin alt kısmındaki **Bağlantılar**’ı ve sonra **+ Yeni**’yi seçin. 

   ![Yeni bağlantı oluşturmaya yönelik düğmeler](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. **Yeni Bağlı Hizmet** penceresinde **Veri Deposu** > **Azure Blob Depolama**’yı ve sonra **Devam**’ı seçin. 

   !["Azure Blob Depolama" kutucuğunu seçme](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. **Depolama hesabı adı** için listeden ad seçip **Kaydet** öğesini seçin. 

   ![Depolama hesabı adı belirtme kutusu](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>İsteğe bağlı bir HDInsight bağlı hizmeti oluşturma

1. Başka bir bağlı hizmet oluşturmak için **+ Yeni** düğmesini tekrar seçin. 
1. **Yeni Bağlı Hizmet** penceresinde **İşlem** > **Azure HDInsight**’ı ve sonra **Devam**’ı seçin. 

   !["Azure HDInsight" kutucuğunu seçme](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları tamamlayın: 

   a. **Ad** için **AzureHDInsightLinkedService** girin.
   
   b. **Tür için** **İsteğe Bağlı HDInsight**’ın seçili olduğunu onaylayın.
   
   c. **Azure depolama bağlı hizmeti**için **AzureBlobStorage1**' yi seçin. Bu bağlı hizmeti daha önce oluşturmuştunuz. Farklı bir ad kullandıysanız, doğru adı burada belirtin. 
   
   d. **Küme türü** için **spark**’ı seçin.
   
   e. **Hizmet sorumlusu kimliği** için, HDInsight kümesi oluşturma iznine sahip hizmet sorumlusunun kimliğini girin. 
   
      Bu hizmet sorumlusu, abonelikte ya da kümenin oluşturulduğu kaynak grubunda Katkıda Bulunan rolünün bir üyesi olmalıdır. Daha fazla bilgi için bkz. [Azure Active Directory uygulaması ve hizmet sorumlusu oluşturma](../active-directory/develop/howto-create-service-principal-portal.md). **Hizmet sorumlusu kimliği** , *uygulama kimliğiyle*eşdeğerdir ve bir **hizmet sorumlusu anahtarı** , bir *istemci parolasının*değeri ile eşdeğerdir.
   
   f. **Hizmet sorumlusu anahtarı** için anahtarı girin. 
   
   g. **Kaynak grubu** için veri fabrikası oluştururken kullandığınız kaynak grubunun aynısını seçin. Spark kümesi bu kaynak grubunda oluşturulur. 
   
   h. **İşletim sistemi türü** seçeneğini genişletin.
   
   i. **Küme kullanıcısı adı** için bir ad girin. 
   
   j. Kullanıcı için **Küme parolası** girin. 
   
   k. **Son**’u seçin. 

   ![HDInsight bağlı hizmet ayarları](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight, desteklediği her bir Azure bölgesinde kullanabileceğiniz toplam çekirdek sayısını sınırlar. İsteğe bağlı HDInsight bağlı hizmeti için HDInsight kümesi, birincil depolama olarak kullanılan Azure Depolama konumunda oluşturulur. Kümenin başarıyla oluşturulabilmesi için yeterince çekirdek kotanızın olduğundan emin olun. Daha fazla bilgi için bkz. [HDInsight’ta Hadoop, Spark, Kafka ve daha fazlası ile küme ayarlama](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

1. **+** (artı) düğmesini ve sonra menüden **İşlem Hattı**'nı seçin.

   ![Yeni işlem hattı oluşturma düğmeleri](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. **Etkinlikler** araç kutusunda **HDInsight**’ı genişletin. **Etkinlikler** araç kutusundan **Spark** etkinliğini işlem hattı tasarımcısının yüzeyine sürükleyin. 

   ![Spark etkinliğini sürükleme](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. Alt kısımdaki **Spark** etkinlik penceresinin özellikler bölümünde aşağıdaki adımları tamamlayın: 

   a. **HDI Kümesi** sekmesine geçin.
   
   b. Önceki yordamda oluşturduğunuz **AzureHDInsightLinkedService** hizmetini seçin. 
        
   ![HDInsight bağlı hizmetini belirtme](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. **Betik/Jar** sekmesine geçin ve aşağıdaki adımları tamamlayın: 

   a. **Iş bağlı hizmeti**için **AzureBlobStorage1**' yi seçin.
   
   b. **Depolamaya Gözat**’ı seçin.

   !["Betik/Jar" sekmesinde Spark betiğini belirtme](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. **adftutorial/spark/script** klasörüne göz atın, **WordCount_Spark.py** dosyasını seçin ve **Son**’a tıklayın.      

1. İşlem hattını doğrulamak için araç çubuğundaki **Doğrula** düğmesini seçin. Doğrulama penceresini kapatmak için **>>** (sağ ok) düğmesini seçin. 
    
   !["Doğrula" düğmesi](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. **Tümünü Yayımla**. Data Factory kullanıcı arabirimi, varlıkları (bağlı hizmetler ve işlem hattı) Azure Data Factory hizmetinde yayımlar. 
    
   !["Tümünü Yayımla" düğmesi](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>İşlem hattı çalıştırması tetikleme
Araç çubuğunda **tetikleyici Ekle** ' yi seçin ve sonra **Şimdi Tetikle**' yi seçin. 

!["Tetikleyici" ve "Şimdi Tetikle" düğmeleri](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. **İzleyici** sekmesine geçin. bir işlem hattı çalıştırması Görtığınızdan emin olun. Spark kümesi oluşturma işlemi yaklaşık 20 dakika sürer. 
   
1. Düzenli aralıklarla **Yenile**’yi seçerek işlem hattı çalıştırmasının durumunu denetleyin. 

   ![“Yenile” düğmeli işlem hattı çalıştırmalarını izleme sekmesi](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. İşlem hattı çalıştırmasıyla ilişkili etkinlik çalıştırmalarını görmek için **Eylemler** sütunundaki **Etkinlik Çalıştırmalarını Göster**’i seçin.

   ![İşlem hattı çalıştırma durumu](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   En üstteki **Tüm Işlem hattı çalıştırmaları** bağlantısını seçerek işlem hattı çalıştırmaları görünümüne dönebilirsiniz.

   !["Etkinlik Çalıştırmaları" görünümü](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Çıktıyı doğrulama
adftutorial kapsayıcısının spark/otuputfiles/wordcount klasöründe çıktı dosyasının oluşturulduğunu doğrulayın. 

![Çıkış dosyasının konumu](./media/tutorial-transform-data-spark-portal/verity-output.png)

Dosya, girdi metin dosyasındaki her bir sözcüğü ve sözcüğün dosyada görünme sayısını içermelidir. Örneğin: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Sonraki adımlar
Bu örnekteki işlem hattı, Spark etkinliğini ve isteğe bağlı bir HDInsight bağlı hizmetini kullanarak verileri dönüştürür. Şunları öğrendiniz: 

> [!div class="checklist"]
> * Veri fabrikası oluşturma. 
> * Spark etkinliği kullanan bir işlem hattı oluşturun.
> * İşlem hattı çalıştırması tetikleyin.
> * İşlem hattı çalıştırmasını izleme.

Sanal ağdaki bir Azure HDInsight kümesinde Hive betiği çalıştırarak verileri dönüştürme hakkında bilgi almak için sonraki öğreticiye ilerleyin: 

> [!div class="nextstepaction"]
> [Öğretici: Azure Sanal Ağ’da Hive kullanarak verileri dönüştürme](tutorial-transform-data-hive-virtual-network-portal.md).





