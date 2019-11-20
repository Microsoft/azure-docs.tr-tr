---
title: 'Veri dönüştürme: Işlem & dönüştürme verileri '
description: Hadoop, Machine Learning veya Azure Data Lake Analytics kullanarak Azure Data Factory veri dönüştürmeyi veya verileri işlemeyi öğrenin.
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
ms.openlocfilehash: 3af01ef76421e95f74a65fe597cafff5fa9ef39e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682620"
---
# <a name="transform-data-in-azure-data-factory"></a>Azure Data Factory verileri dönüştürme
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Akışı](data-factory-hadoop-streaming-activity.md)
> * [Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
> * [Saklı Yordam](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET özel](data-factory-use-custom-activities.md)

## <a name="overview"></a>Genel Bakış
> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [Data Factory veri dönüştürme etkinlikleri](../transform-data.md).

Bu makalede, ham verilerinizi tahmine dayalı ve Öngörüler halinde dönüştürmek ve işlerken kullanabileceğiniz Azure Data Factory veri dönüştürme etkinlikleri açıklanmaktadır. Bir dönüştürme etkinliği, Azure HDInsight kümesi veya bir Azure Batch gibi bir bilgi işlem ortamında yürütülür. Her bir dönüştürme etkinliği hakkında ayrıntılı bilgi içeren makalelere bağlantılar sağlar.

Data Factory, işlem hatlarına tek tek veya başka bir etkinlikle zincirleme [olarak eklenebilen](data-factory-create-pipelines.md) aşağıdaki veri dönüştürme etkinliklerini destekler.

> [!NOTE]
> Adım adım yönergeler içeren bir anlatım için bkz. [Hive dönüşümle işlem hattı oluşturma](data-factory-build-your-first-pipeline.md) makalesi.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive etkinliği
Bir Data Factory işlem hattının HDInsight Hive etkinliği, kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde Hive sorguları yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [Hive etkinliği](data-factory-hive-activity.md) makalesi. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig etkinliği
Bir Data Factory işlem hattındaki HDInsight Pig etkinliği, kendi veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizdeki Pig sorgularını yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [Pig etkinlik](data-factory-pig-activity.md) makalesi. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce etkinliği
Bir Data Factory işlem hattındaki HDInsight MapReduce etkinliği, MapReduce programlarını kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [MapReduce etkinliği](data-factory-map-reduce.md) makalesi.

## <a name="hdinsight-streaming-activity"></a>HDInsight akış etkinliği
Bir Data Factory işlem hattının HDInsight akış etkinliği, kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde Hadoop akış programlarını yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [HDInsight akış etkinliği](data-factory-hadoop-streaming-activity.md) .

## <a name="hdinsight-spark-activity"></a>HDInsight Spark Etkinliği
Bir Data Factory işlem hattının HDInsight Spark etkinliği kendi HDInsight kümenizdeki Spark programlarını yürütür. Ayrıntılar için bkz. [Azure Data Factory Spark programlarını çağırma](data-factory-spark.md). 

## <a name="machine-learning-activities"></a>Machine Learning etkinlikleri
Azure Data Factory, tahmine dayalı analiz için yayımlanmış bir Azure Machine Learning Web hizmeti kullanan işlem hatlarını kolayca oluşturmanızı sağlar. [Toplu yürütme etkinliğini](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) bir Azure Data Factory işlem hattında kullanarak, toplu işteki verilerde tahmine dayalı hale getirmek için bir Machine Learning Web hizmeti çağırabilirsiniz.

Zaman içinde, Machine Learning Puanlama denemeleri 'un tahmine dayalı modellerinin yeni giriş veri kümeleri kullanılarak geri çekilmesi gerekir. Yeniden eğitim ile işiniz bittiğinde, Puanlama Web hizmetini geri çekme Machine Learning modeliyle güncelleştirmek istersiniz. Web hizmetini yeni eğitilen modelle güncelleştirmek için [kaynak güncelleştirme etkinliğini](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) kullanabilirsiniz.  

Bu Machine Learning etkinlikleri hakkında daha fazla bilgi için bkz. [Machine Learning etkinlikleri kullanma](data-factory-azure-ml-batch-execution-activity.md) . 

## <a name="stored-procedure-activity"></a>Saklı yordam etkinliği
Aşağıdaki veri depolarından birinde saklı bir yordam çağırmak için bir Data Factory Işlem hattındaki SQL Server saklı yordam etkinliğini kullanabilirsiniz: Azure SQL veritabanı, Azure SQL veri ambarı, kuruluşunuzda SQL Server veritabanı veya bir Azure VM. Ayrıntılar için bkz. [saklı yordam etkinliği](data-factory-stored-proc-activity.md) makalesi.  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL etkinliği
Data Lake Analytics U-SQL etkinliği Azure Data Lake Analytics kümesinde bir U-SQL betiği çalıştırır. Ayrıntılar için bkz. [Data Analytics U-SQL etkinliği](data-factory-usql-activity.md) makalesi. 

## <a name="net-custom-activity"></a>.NET özel etkinliği
Data Factory tarafından desteklenmeyen bir şekilde veri dönüştürmeniz gerekiyorsa, kendi veri işleme mantığınızla özel bir etkinlik oluşturabilir ve etkinliği ardışık düzende kullanabilirsiniz. Özel .NET etkinliğini bir Azure Batch hizmeti veya Azure HDInsight kümesi kullanarak çalışacak şekilde yapılandırabilirsiniz. Ayrıntılar için bkz. [özel etkinlikleri kullanma](data-factory-use-custom-activities.md) makalesi. 

R yüklü HDInsight kümenizde R betiklerini çalıştırmak için özel bir etkinlik oluşturabilirsiniz. Bkz. [Azure Data Factory kullanarak R Betiği çalıştırma](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>İşlem ortamları
İşlem ortamı için bağlı bir hizmet oluşturun ve ardından bir dönüştürme etkinliği tanımlarken bağlı hizmeti kullanın. Data Factory tarafından desteklenen iki tür işlem ortamı vardır. 

1. **İsteğe**bağlı: Bu durumda, bilgi işlem ortamı Data Factory tarafından tam olarak yönetilir. Bir iş, verileri işlemek ve iş tamamlandığında kaldırılmadan önce Data Factory hizmeti tarafından otomatik olarak oluşturulur. İş yürütme, küme yönetimi ve önyükleme eylemleri için isteğe bağlı işlem ortamının ayrıntılı ayarlarını yapılandırabilir ve kontrol edebilirsiniz. 
2. **Kendinizinkini getir**: Bu durumda, kendi bilgi işlem ortamınızı (örneğin HDInsight kümesi) Data Factory bağlı bir hizmet olarak kaydedebilirsiniz. Bilgi işlem ortamı sizin tarafınızdan yönetilir ve Data Factory hizmeti tarafından etkinlikleri yürütmek için kullanılır. 

Data Factory tarafından desteklenen işlem hizmetleri hakkında bilgi edinmek için bkz. [Işlem bağlantılı hizmetleri](data-factory-compute-linked-services.md) makalesi. 

## <a name="summary"></a>Özet
Azure Data Factory, etkinlikler için aşağıdaki veri dönüştürme etkinliklerini ve işlem ortamlarını destekler. Dönüştürme etkinlikleri, tek tek veya başka bir etkinlikle zincirleme olarak işlem hattına eklenebilir.

| Veri dönüştürme etkinliği | İşlem ortamı |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Akışı](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Machine Learning etkinlikleri: Toplu Yürütme ve Kaynak Güncelleştirme](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Saklı Yordam](data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Veri Ambarı veya SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] veya Azure Batch |

