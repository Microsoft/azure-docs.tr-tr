---
title: Azure Data Factory-sık sorulan sorular
description: Azure Data Factory hakkında sık sorulan sorular.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 7c0b5cbfe77a39cbc8ac660178a1c573d1fe8f7d
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139828"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory-sık sorulan sorular
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [sık sorulan sorular-Data Factory](../frequently-asked-questions.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Genel sorular
### <a name="what-is-azure-data-factory"></a>Azure Data Factory nedir?
Data Factory **, verilerin hareketini ve dönüştürülmesini otomatikleştiren**bulut tabanlı bir veri tümleştirme hizmetidir. Ham malzemeler almak ve bunları mamul mallar halinde dönüştürmek üzere ekipman çalıştıran bir fabrikada olduğu gibi, ham verileri toplayıp bunları kullanıma hazırlama bilgilerine dönüştüren mevcut hizmetleri de Data Factory.

Data Factory, verileri şirket içi ve bulut veri depoları arasında taşımak için veri odaklı iş akışları oluşturmanıza olanak tanır ve Azure HDInsight ve Azure Data Lake Analytics gibi işlem hizmetlerini kullanarak verileri işleyebilir/dönüştürebilirsiniz. İhtiyaç duyduğunuz eylemi gerçekleştiren bir işlem hattı oluşturduktan sonra, düzenli aralıklarla (saatlik, günlük, haftalık vb.) çalışacak şekilde zamanlayabilirsiniz.   

Daha fazla bilgi için bkz. [genel bakış & temel kavramlar](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Azure Data Factory için fiyatlandırma ayrıntılarını nerede bulabilirim?
Azure Data Factory ilişkin fiyatlandırma ayrıntıları için bkz. [Data Factory fiyatlandırma ayrıntıları sayfası][adf-pricing-details] .  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>Azure Data Factory kullanmaya başlamak Nasıl yaparım??
* Azure Data Factory genel bir bakış için bkz. [Azure Data Factory giriş](data-factory-introduction.md).
* Kopyalama etkinliğini kullanarak **verileri kopyalama/taşıma** hakkında bir öğretici için bkz. [Azure Blob depolamadan Azure SQL veritabanı 'na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* HDInsight Hive etkinliğini kullanarak **verileri dönüştürme** hakkında bir öğretici için. Bkz. [Hadoop kümesinde Hive betiği çalıştırarak işlem verileri](data-factory-build-your-first-pipeline.md)

### <a name="what-is-the-data-factorys-region-availability"></a>Data Factory bölgenin kullanılabilirliği nedir?
Data Factory **ABD Batı** ve **Kuzey Avrupa**kullanılabilir. Veri fabrikaları tarafından kullanılan işlem ve depolama hizmetleri diğer bölgelerde olabilir. Bkz. [Desteklenen bölgeler](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Veri fabrikaları/işlem hatları/etkinlik/veri kümeleri için sınırlamalar nelerdir?
[Azure aboneliği ve hizmet sınırları, Kotalar ve kısıtlamalar](../../azure-subscription-service-limits.md#data-factory-limits) makalesinin **Azure Data Factory sınırları** bölümüne bakın.

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Azure Data Factory hizmeti ile yazma/geliştirici deneyimi nedir?
Aşağıdaki araçlardan/SDK 'Lardan birini kullanarak veri fabrikaları yazabilir/oluşturabilirsiniz:

* **Visual Studio** Bir Azure Data Factory oluşturmak için Visual Studio 'Yu kullanabilirsiniz. Ayrıntılar için bkz. [Visual Studio kullanarak ilk veri işlem hattınızı oluşturma](data-factory-build-your-first-pipeline-using-vs.md) .
* **Azure PowerShell** Bkz. PowerShell kullanarak veri fabrikası oluşturmaya yönelik bir öğretici için [Azure PowerShell kullanarak Azure Data Factory oluşturma ve izleme](data-factory-build-your-first-pipeline-using-powershell.md) . Data Factory cmdlet 'lerinin kapsamlı bir belgesi için MSDN Kitaplığı 'ndaki [Data Factory cmdlet başvurusu][adf-powershell-reference] içeriğine bakın.
* **.NET sınıf kitaplığı** Data Factory .NET SDK kullanarak program aracılığıyla veri fabrikaları oluşturabilirsiniz. .NET SDK kullanarak veri fabrikası oluşturma hakkında yönergeler için bkz. [.NET SDK kullanarak veri fabrikaları oluşturma, izleme ve yönetme](data-factory-create-data-factories-programmatically.md) . .NET SDK Data Factory kapsamlı bir belge için bkz. [Data Factory sınıf kitaplığı başvurusu][msdn-class-library-reference] .
* **REST API** Ayrıca, veri fabrikaları oluşturmak ve dağıtmak için Azure Data Factory hizmetinin açığa çıkarılan REST API de kullanabilirsiniz. Data Factory REST API kapsamlı bir belge için bkz. [Data Factory REST API başvurusu][msdn-rest-api-reference] .
* **Azure Resource Manager şablonu** Öğreticiye bakın [: Azure Resource Manager şablonu](data-factory-build-your-first-pipeline-using-arm.md) kullanarak ilk Azure veri fabrikanızı oluşturun ayrıntıları Info.

### <a name="can-i-rename-a-data-factory"></a>Bir veri fabrikasını yeniden adlandırabilir miyim?
Hayır. Diğer Azure kaynakları gibi, Azure Data Factory 'nin adı değiştirilemez.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Bir veri fabrikasını bir Azure aboneliğinden diğerine taşıyabilir miyim?
Evet. Aşağıdaki diyagramda gösterildiği gibi Data Factory dikey penceresindeki **Taşı** düğmesini kullanın:

![Veri fabrikasını taşıma](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Data Factory tarafından desteklenen işlem ortamları nelerdir?
Aşağıdaki tabloda, Data Factory tarafından desteklenen işlem ortamlarının listesi ve bunlar üzerinde çalışabilecek etkinlikler verilmiştir.

| İşlem ortamı | activities |
| --- | --- |
| [İsteğe bağlı HDInsight kümesi](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) veya [kendi HDInsight kümeniz](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop akışı](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](data-factory-compute-linked-services.md#azure-machine-learning-linked-service) |[Machine Learning etkinlikleri: Toplu yürütme ve kaynak güncelleştirme](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics'i](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure SQL veri ambarı](data-factory-compute-linked-services.md#azure-sql-data-warehouse-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Saklı Yordam](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Azure Data Factory SQL Server Integration Services (SSIS) ile nasıl karşılaştırılmaktadır? 
Bkz. [Azure Data Factory vs. MVP 'larımızdan (en değerli profesyonellerden) SSISsunumu:](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) Reza Rad. Data Factory yapılan son değişikliklerden bazıları slayt destede listelenmeyebilir. Azure Data Factory için sürekli olarak daha fazla özellik ekliyoruz. Azure Data Factory için sürekli olarak daha fazla özellik ekliyoruz. Bu güncelleştirmeleri Microsoft 'un bu yıldan daha sonra bir süre içinde veri tümleştirme teknolojilerinin karşılaştırmasına dahil edeceğiz.   

## <a name="activities---faq"></a>Etkinlikler-SSS
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Data Factory ardışık düzeninde kullanabileceğiniz farklı etkinlik türleri nelerdir?
* Verileri taşımak için [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) .
* Verileri işlemek/dönüştürmek için [veri dönüştürme etkinlikleri](data-factory-data-transformation-activities.md) .

### <a name="when-does-an-activity-run"></a>Bir etkinlik ne zaman çalıştırılır?
Çıkış verileri tablosundaki **kullanılabilirlik** yapılandırma ayarı etkinliğin ne zaman çalıştırılacağını belirler. Giriş veri kümeleri belirtilirse, etkinlik, çalışmaya başlamadan önce tüm giriş verileri bağımlılıklarının (yani, **Ready** durumu) karşılanıp karşılanmadığını denetler.

## <a name="copy-activity---faq"></a>Kopyalama etkinliği-SSS
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Her etkinlik için birden çok etkinliğe veya ayrı bir işlem hattına sahip bir işlem hattına sahip olmak daha iyidir mi?
İşlem hatları, ilgili etkinlikleri gruplamak için gerekir. Bunları bağlayan veri kümeleri, ardışık düzen dışında başka bir etkinlik tarafından tüketilmez, etkinlikleri tek bir işlem hattına taşıyabilirsiniz. Bu şekilde, ardışık düzen etkin periyotlarını birbirleriyle hizalamaları için zincirlemek zorunda değilsiniz. Ayrıca, işlem hattı güncelleştirilirken, ardışık düzen için tablolardaki veri bütünlüğü daha iyi korunur. İşlem hattı güncelleştirmesi, temelde işlem hattının içindeki tüm etkinlikleri durduruyor, kaldırır ve yeniden oluşturur. Yazma perspektifinden, işlem hattı için bir JSON dosyasında ilgili etkinliklerdeki verilerin akışını görmek daha da kolay olabilir.

### <a name="what-are-the-supported-data-stores"></a>Desteklenen veri depoları nelerdir?
Data Factory’deki Kopyalama Etkinliği bir kaynak veri deposundan havuz veri deposuna verileri kopyalar. Data Factory aşağıdaki veri depolarını destekler. Herhangi bir kaynaktan gelen veriler herhangi bir havuza yazılabilir. Bir depoya veya depodan veri kopyalama hakkında bilgi edinmek için veri deposuna tıklayın.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> \* taşıyan veri depoları şirket içi veya Azure IaaS üzerinde olabilir ve bir şirket içi/Azure IaaS makinesine [Veri Yönetimi Ağ Geçidi](data-factory-data-management-gateway.md) yüklemenizi gerektirir.

### <a name="what-are-the-supported-file-formats"></a>Desteklenen dosya biçimleri nelerdir?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Kopyalama işlemi nerede gerçekleştirilir?
Ayrıntılar için [genel kullanıma açık veri taşıma](data-factory-data-movement-activities.md#global) bölümüne bakın. Kısacası, şirket içi veri deposu dahil edildiğinde kopyalama işlemi, şirket içi ortamınızda Veri Yönetimi Ağ Geçidi tarafından gerçekleştirilir. Veri taşıma iki bulut deposu arasında olduğunda, kopyalama işlemi aynı coğrafya içindeki havuz konumuna en yakın bölgede gerçekleştirilir.

## <a name="hdinsight-activity---faq"></a>HDInsight etkinliği-SSS
### <a name="what-regions-are-supported-by-hdinsight"></a>HDInsight tarafından desteklenen bölgeler nelerdir?
Aşağıdaki makaledeki coğrafi kullanılabilirlik bölümüne bakın: veya [HDInsight fiyatlandırma ayrıntıları][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>İsteğe bağlı HDInsight kümesi tarafından kullanılan bölge nedir?
İsteğe bağlı HDInsight kümesi, kümeyle birlikte kullanılmak üzere belirttiğiniz depolamanın bulunduğu bölgede oluşturulur.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>HDInsight kümenize ek depolama hesapları nasıl ilişkilendireceğiniz?
Kendi HDInsight kümenizi kullanıyorsanız (BYOC-Kendi kümenizi getir), aşağıdaki konulara bakın:

* [Alternatif depolama hesaplarıyla bir HDInsight kümesi ve Metastores kullanma][hdinsight-alternate-storage]
* [HDInsight Hive ile ek depolama hesapları kullanma][hdinsight-alternate-storage-2]

Data Factory hizmeti tarafından oluşturulan isteğe bağlı bir küme kullanıyorsanız, HDInsight bağlı hizmeti için ek depolama hesapları belirtin, böylece Data Factory hizmeti sizin adınıza kaydedebilir. İsteğe bağlı bağlı hizmet için JSON tanımında, aşağıdaki JSON kod parçacığında gösterildiği gibi alternatif depolama hesapları belirtmek için **Addilinkedservicenames** özelliğini kullanın:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
Yukarıdaki örnekte, otherLinkedServiceName1 ve otherLinkedServiceName2 tanımları, HDInsight kümesinin alternatif depolama hesaplarına erişmesi gereken kimlik bilgilerini içeren bağlı hizmetleri temsil eder.

## <a name="slices---faq"></a>Dilimler-SSS
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Giriş dilimlerim neden kullanılabilir durumda değil?
Giriş verileri veri fabrikasına (Data Factory tarafından üretilmez) harici olduğunda, genel bir hata giriş veri kümesinde **dış** özelliği **true** olarak ayarlamadır.

Aşağıdaki örnekte, **dataSet1**üzerinde yalnızca **External** ' ı true olarak ayarlamanız gerekir.  

**DataFactory1** İşlem hattı 1: DataSet1-> Activity1-> DataSet2-> Activity2-> dataset3 işlem hattı 2: dataset3-> Activity3-> dataset4

Dataset4 alan bir işlem hattına sahip başka bir veri fabrikanızı varsa (Data Factory 1 ' de işlem hattı 2 tarafından üretilen), veri kümesi farklı bir veri Fabrikası (DataFactory1, DataFactory2 değil) tarafından oluşturulduğundan, dataset4 'yi bir dış veri kümesi olarak işaretleyin.  

**DataFactory2**    
İşlem hattı 1: dataset4-> Activity4 ancak-> dataset5

Dış özellik doğru ayarlandıysa, giriş veri kümesi tanımında belirtilen konumda giriş verilerinin olup olmadığını doğrulayın.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Dilim, her gün üretildiğinde gece yarısından farklı bir zamanda nasıl çalıştırılır?
Dilimin üretileceği saati belirtmek için, **fark** özelliğini kullanın. Bu özellik hakkındaki ayrıntılar için bkz. [veri kümesi kullanılabilirliği](data-factory-create-datasets.md#dataset-availability) bölümü. İşte hızlı bir örnek:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Günlük dilimler varsayılan gece yarısı yerine **6** ' da başlar.     

### <a name="how-can-i-rerun-a-slice"></a>Bir dilimi nasıl yeniden çalıştırabilirim?
Aşağıdaki yollarla bir dilimi yeniden çalıştırabilirsiniz:

* Bir etkinlik penceresini veya dilimi yeniden çalıştırmak için Izleme ve yönetme uygulamasını kullanın. Yönergeler için bkz. [Seçili etkinlik pencerelerini yeniden çalıştır](data-factory-monitor-manage-app.md#perform-batch-actions) .   
* Azure portal dilimin **VERI dilimi** dikey penceresindeki komut çubuğunda **Çalıştır** ' a tıklayın.
* Dilim **bekleniyor** olarak set **-AzDataFactorySliceStatus** cmdlet 'ini durum kümesiyle çalıştırın.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Cmdlet hakkındaki ayrıntılar için bkz. [set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] .

### <a name="how-long-did-it-take-to-process-a-slice"></a>Bir dilimi işlemek ne kadar sürer?
Bir veri dilimini işlemeye ne kadar sürdüğünü öğrenmek için, Izleme & uygulamayı yönetme ' de etkinlik penceresi Gezgini ' ni kullanın. Ayrıntılar için [etkinlik penceresi Gezgini](data-factory-monitor-manage-app.md#activity-window-explorer) ' ne bakın.

Azure portal şunları da yapabilirsiniz:  

1. Veri fabrikanızın **VERI fabrikası** dikey penceresinde veri **kümeleri** kutucuğuna tıklayın.
2. **Veri kümeleri** dikey penceresinde belirli bir veri kümesine tıklayın.
3. **Tablo** dikey penceresindeki **son dilimler** listesinden ilgilendiğiniz dilimi seçin.
4. **VERI dilimi** dikey penceresinde **etkinlik çalıştırmaları** listesinden Çalıştır ' a tıklayın.
5. **Etkınlık çalışma ayrıntıları** dikey penceresinde **Özellikler** Bölmesi ' ne tıklayın.
6. **DURATION** alanını bir değer ile görmeniz gerekir. Bu değer, dilimi işlemek için geçen süredir.   

### <a name="how-to-stop-a-running-slice"></a>Çalışan bir dilim nasıl durdurulur?
İşlem hattının yürütülmesini durdurmanız gerekiyorsa, [askıya al-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) cmdlet 'ini kullanabilirsiniz. Şu anda, işlem hattının askıya alınması devam eden dilim yürütmelerini durdurmaz. Devam eden yürütmeler tamamlandığında, hiç ek dilim alınmaz.

Tüm yürütmeleri gerçekten durdurmak istiyorsanız tek yol, işlem hattını silmek ve yeniden oluşturmaktır. İşlem hattını silmeyi seçerseniz, işlem hattı tarafından kullanılan tabloları ve bağlı hizmetleri silmeniz gerekmez.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: https://blogs.msdn.com/b/cindygross/archive/2014/05/05/use-additional-storage-accounts-with-hdinsight-hive.aspx
