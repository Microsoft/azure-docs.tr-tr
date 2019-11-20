---
title: 'Data Factory öğreticisi: Ilk veri işlem hattı '
description: Bu Azure Data Factory öğreticide, Hadoop kümesinde Hive betiği kullanarak verileri işleyen bir veri fabrikası oluşturma ve zamanlama işlemi gösterilmektedir.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 90084864f9a93117a0f94dc1d36e6119e88ee335
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682941"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için ilk işlem hattınızı oluşturma
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager Şablonu](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory'nin geçerli sürümünü kullanıyorsanız [Hızlı Başlangıç: Azure Data Factory'yi kullanarak veri fabrikası oluşturma](../quickstart-create-data-factory-dot-net.md) konusunu inceleyin.

Bu öğreticide, bir veri işlem hattı ile ilk Azure veri fabrikanızı oluşturacaksınız. İşlem hattı, çıkış verileri oluşturmak için bir Azure HDInsight (Hadoop) kümesinde Hive betiği çalıştırarak giriş verilerini dönüştürür.  

Bu makalede öğretici için genel bakış ve Önkoşullar sunulmaktadır. Önkoşulları tamamladıktan sonra, aşağıdaki araçlardan/SDK 'Lardan birini kullanarak öğreticiyi yapabilirsiniz: Visual Studio, PowerShell, Kaynak Yöneticisi şablonu REST API. Bu seçeneklerden birini kullanarak öğreticiyi yapmak için, bu makalenin sonundaki açılan listede bulunan (veya) bağlantılar ' ı seçin.    

## <a name="tutorial-overview"></a>Öğreticiye genel bakış
Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

1. Bir **Veri Fabrikası**oluşturun. Veri Fabrikası, verileri taşımak ve dönüştürmek için bir veya daha fazla veri işlem hattı içerebilir. 

    Bu öğreticide, veri fabrikasında bir işlem hattı oluşturacaksınız. 
2. İşlem **hattı**oluşturun. İşlem hattında bir veya daha fazla etkinlik bulunabilir (Örnek: Kopya Etkinliği, HDInsight Hive Etkinliği). Bu örnek, bir HDInsight Hadoop kümesinde Hive betiği çalıştıran HDInsight Hive etkinliğini kullanır. Betik ilk olarak Azure Blob depolamada depolanan ham Web günlüğü verilerine başvuran bir tablo oluşturur ve sonra ham verileri yıla ve aya göre bölümlendirir.

    Bu öğreticide, işlem hattı bir Azure HDInsight Hadoop kümesinde Hive sorgusu çalıştırarak verileri dönüştürmek için Hive etkinliğini kullanır. 
3. **Bağlı hizmetler**oluşturun. Bir veri deposunu veya işlem hizmetini, veri fabrikasına bağlamak için bir bağlı hizmet oluşturursunuz. Azure Depolama gibi bir veri deposu, veri işlem hattındaki etkinliklerin giriş/çıkış verilerini tutar. HDInsight Hadoop kümesi gibi bir işlem hizmeti verileri işler/dönüştürür.

    Bu öğreticide, iki bağlı hizmet oluşturacaksınız: **Azure depolama** ve **Azure HDInsight**. Azure depolama bağlı hizmeti, girdi/çıktı verilerini tutan bir Azure Depolama hesabını veri fabrikasına bağlar. Azure HDInsight bağlı hizmeti, verileri veri fabrikasına dönüştürmek için kullanılan bir Azure HDInsight kümesini bağlar. 
3. Girdi ve çıktı **veri kümeleri** oluşturun. Giriş veri kümesi, veri işlem hattındaki bir etkinlik için girişi ve çıktı veri kümesi, etkinliğin çıktısını temsil eder.

    Bu öğreticide, giriş ve çıkış veri kümeleri, Azure Blob depolama alanındaki giriş ve çıkış verilerinin konumlarını belirtir. Azure depolama bağlı hizmeti, hangi Azure Storage hesabının kullanıldığını belirtir. Giriş veri kümesi, giriş dosyalarının nerede olduğunu ve çıkış veri kümesi 'nin çıkış dosyalarının nereye yerleştirileceğini belirtir. 


Azure Data Factory ayrıntılı bir genel bakış için bkz. [Azure Data Factory makalesine giriş](data-factory-introduction.md) .
  
Bu öğreticide oluşturduğunuz örnek veri fabrikasının **Diyagram görünümü** aşağıda verilmiştir. **Myfirstpipeline** , bir girdi olarak **azureblobınput** veri kümesini tüketen ve çıktı olarak **AzureBlobOutput** veri kümesi üreten Hive türünde bir etkinliğe sahiptir. 

![Data Factory öğreticisinde diyagram görünümü](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Bu öğreticide, **adfgetstarted** Azure Blob kapsayıcısının **inputdata** klasörü Input. log adlı bir dosya içerir. Bu günlük dosyasında üç aydan girişler vardır: Ocak, Şubat ve Mart 2016. Girdi dosyasındaki, her aya ait örnek satırlar şunlardır. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Dosya, işlem hattı tarafından HDInsight Hive etkinliği ile işlendiğinde etkinlik, giriş verilerini yıl ve aya göre bölümlere ayıran HDInsight kümesi üzerinde bir Hive betiği çalıştırır. Betik, her bir aya ait girişlerin bulunduğu bir dosya içeren üç çıktı klasörü oluşturur.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Yukarıda gösterilen örnek satırlarda, ilk biri (2016-01-01 ile) ay = 1 klasöründeki 000000_0 dosyasına yazılır. Benzer şekilde, ikinci satır month=2 klasöründeki dosyaya ve üçüncü satır month=3 klasöründeki dosyaya yazılır.  

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticiye başlamadan önce aşağıdaki önkoşullara sahip olmanız gerekir:

1. **Azure aboneliği**: Aboneliğiniz yoksa yalnızca birkaç dakika içinde ücretsiz bir deneme hesabı oluşturabilirsiniz. Nasıl ücretsiz bir deneme hesabı edinebileceğinizi öğrenmek için [Ücretsiz Deneme](https://azure.microsoft.com/pricing/free-trial/) makalesine bakın.
2. **Azure Depolama**: Bu öğreticide, verileri depolamak için bir Azure depolama hesabı kullanılmaktadır. Azure depolama hesabınız yoksa [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md) makalesine bakın. Depolama hesabını oluşturduktan sonra **Hesap adı** ve **erişim anahtarı**' na göz önüne alın. Bkz. [Depolama erişim tuşlarını görüntüleme, kopyalama ve yeniden oluşturma](../../storage/common/storage-account-manage.md#access-keys).
3. Şu adreste bulunan Hive sorgu dosyasını (**HQL**) indirip gözden geçirin: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Bu sorgu, çıkış verileri oluşturmak için giriş verilerini dönüştürür. 
4. Şu adreste bulunan örnek giriş dosyasını (**input. log**) indirip gözden geçirin: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Azure Blob depolamada **adfgetstarted** adlı bir blob kapsayıcısı oluşturun. 
6. **Partitionweblogs. HQL** dosyasını **adfgetstarted** kapsayıcısındaki **betik** klasörüne yükleyin. [Microsoft Azure Depolama Gezgini](https://storageexplorer.com/)gibi araçları kullanın. 
7. **Input. log** dosyasını **adfgetstarted** kapsayıcısındaki **inputdata** klasörüne yükleyin. 

Önkoşulları tamamladıktan sonra, öğreticiyi gerçekleştirmek için aşağıdaki araçlardan/SDK 'Lardan birini seçin: 

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager Şablonu](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio, veri fabrikalarınızı oluşturmanın GUI yolunu sağlar. Öte yandan, PowerShell, Kaynak Yöneticisi şablonu ve REST API seçenekleri veri fabrikalarınızı oluşturmaya yönelik betik/programlama yöntemi sağlar.

> [!NOTE]
> Bu öğreticideki veri işlem hattı, çıkış verileri üretmek üzere giriş verilerini dönüştürür. Bir kaynak veri deposundan hedef veri deposuna verileri kopyalamaz. Azure Data Factory kullanarak verileri kopyalama öğreticisi için bkz. [Öğretici: Blob Depolama’dan SQL Veritabanı’na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliği diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Ayrıntılı bilgi için bkz. [Data Factory’de zamanlama ve yürütme](data-factory-scheduling-and-execution.md). 





  
