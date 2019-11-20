---
title: 'Azure Data Factory kullanarak verileri dönüştürme '
description: Hadoop, Machine Learning veya Azure Data Lake Analytics kullanarak Azure Data Factory veri dönüştürmeyi veya verileri işlemeyi öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 268c1e66010720d1da052183165ce1cea50a1095
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092005"
---
# <a name="transform-data-in-azure-data-factory"></a>Azure Data Factory verileri dönüştürme
> [!div class="op_single_selector"]
> * [Veri akışını eşleme](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight akışı](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [Saklı Yordam](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks Not defteri](transform-data-databricks-notebook.md)
> * [Databricks jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET özel](transform-data-using-dotnet-custom-activity.md)

## <a name="overview"></a>Genel Bakış
Bu makalede, ham verilerinizi ölçek ve Öngörüler halinde dönüştürmek ve işlemek için kullanabileceğiniz Azure Data Factory veri dönüştürme etkinlikleri açıklanır. Bir dönüştürme etkinliği Azure Databricks veya Azure HDInsight gibi bir bilgi işlem ortamında yürütülür. Her bir dönüştürme etkinliği hakkında ayrıntılı bilgi içeren makalelere bağlantılar sağlar.

Data Factory, işlem hatlarına tek tek veya başka bir etkinlikle zincirleme [olarak eklenebilen](concepts-pipelines-activities.md) aşağıdaki veri dönüştürme etkinliklerini destekler.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Veri akışları ile Azure Data Factory yerel olarak dönüştürme

### <a name="mapping-data-flows"></a>Veri akışlarını eşleme

Veri akışlarını eşleme, Azure Data Factory ' de görsel olarak tasarlanan veri dönüştürmelerdir. Veri akışları, veri mühendislerinin kod yazmadan grafik veri dönüştürme mantığı geliştirmesini sağlar. Elde edilen veri akışları, ölçeklendirilen Spark kümelerini kullanan Azure Data Factory işlem hatları içinde etkinlik olarak yürütülür. Veri akışı etkinlikleri mevcut Data Factory zamanlama, denetim, akış ve izleme özellikleri aracılığıyla çalıştırılabilir. Daha fazla bilgi için bkz. [veri akışlarını eşleme](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Wrangling veri akışları

Azure Data Factory ' deki wrangling veri akışları, bulut ölçeğinde kod içermeyen veri hazırlığı yapmanıza olanak sağlar. Denetimi veri akışları, [çevrimiçi Power Query](https://docs.microsoft.com/power-query/) ile tümleşir ve Spark yürütmesi aracılığıyla bulut ölçeğinde veriler için Power Query M işlevlerini kullanılabilir hale getirir. Daha fazla bilgi için bkz. [denetimi veri akışları](wrangling-data-flow-overview.md).

## <a name="external-transformations"></a>Dış dönüşümler

İsteğe bağlı olarak, dönüştürmeleri kodlayabilirsiniz ve dış işlem ortamını kendiniz yönetebilirsiniz.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive etkinliği
Bir Data Factory işlem hattının HDInsight Hive etkinliği, kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde Hive sorguları yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [Hive etkinliği](transform-data-using-hadoop-hive.md) makalesi. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig etkinliği
Bir Data Factory işlem hattındaki HDInsight Pig etkinliği, kendi veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizdeki Pig sorgularını yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [Pig etkinlik](transform-data-using-hadoop-pig.md) makalesi. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce etkinliği
Bir Data Factory işlem hattındaki HDInsight MapReduce etkinliği, MapReduce programlarını kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [MapReduce etkinliği](transform-data-using-hadoop-map-reduce.md) makalesi.

### <a name="hdinsight-streaming-activity"></a>HDInsight akış etkinliği
Bir Data Factory işlem hattının HDInsight akış etkinliği, kendi kendinize veya isteğe bağlı Windows/Linux tabanlı HDInsight kümenizde Hadoop akış programlarını yürütür. Bu etkinlik hakkındaki ayrıntılar için bkz. [HDInsight akış etkinliği](transform-data-using-hadoop-streaming.md) .

### <a name="hdinsight-spark-activity"></a>HDInsight Spark etkinliği
Bir Data Factory işlem hattının HDInsight Spark etkinliği kendi HDInsight kümenizdeki Spark programlarını yürütür. Ayrıntılar için bkz. [Azure Data Factory Spark programlarını çağırma](transform-data-using-spark.md). 

### <a name="machine-learning-activities"></a>Machine Learning etkinlikleri
Azure Data Factory, tahmine dayalı analiz için yayımlanmış bir Azure Machine Learning Web hizmeti kullanan işlem hatlarını kolayca oluşturmanızı sağlar. [Toplu yürütme etkinliğini](transform-data-using-machine-learning.md) bir Azure Data Factory işlem hattında kullanarak, toplu işteki verilerde tahmine dayalı hale getirmek için bir Machine Learning Web hizmeti çağırabilirsiniz.

Zaman içinde, Machine Learning Puanlama denemeleri 'un tahmine dayalı modellerinin yeni giriş veri kümeleri kullanılarak geri çekilmesi gerekir. Yeniden eğitim ile işiniz bittiğinde, Puanlama Web hizmetini geri çekme Machine Learning modeliyle güncelleştirmek istersiniz. Web hizmetini yeni eğitilen modelle güncelleştirmek için [kaynak güncelleştirme etkinliğini](update-machine-learning-models.md) kullanabilirsiniz.  

Bu Machine Learning etkinlikleri hakkında daha fazla bilgi için bkz. [Machine Learning etkinlikleri kullanma](transform-data-using-machine-learning.md) . 

### <a name="stored-procedure-activity"></a>Saklı yordam etkinliği
Aşağıdaki veri depolarından birinde saklı bir yordam çağırmak için bir Data Factory Işlem hattındaki SQL Server saklı yordam etkinliğini kullanabilirsiniz: Azure SQL veritabanı, Azure SQL veri ambarı, kuruluşunuzda SQL Server veritabanı veya bir Azure VM. Ayrıntılar için bkz. [saklı yordam etkinliği](transform-data-using-stored-procedure.md) makalesi.  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL etkinliği
Data Lake Analytics U-SQL etkinliği Azure Data Lake Analytics kümesinde bir U-SQL betiği çalıştırır. Ayrıntılar için bkz. [Data Analytics U-SQL etkinliği](transform-data-using-data-lake-analytics.md) makalesi. 

### <a name="databricks-notebook-activity"></a>Databricks Not defteri etkinliği

Bir Data Factory işlem hattındaki Azure Databricks Not defteri etkinliği Azure Databricks çalışma alanınızda bir Databricks Not defteri çalıştırır. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur. Bkz. [Databricks Not defteri çalıştırarak verileri dönüştürme](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks jar etkinliği

Bir Data Factory işlem hattındaki Azure Databricks jar etkinliği Azure Databricks kümenizde Spark jar 'i çalıştırır. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur. Bkz. [Azure Databricks bir jar etkinliği çalıştırarak verileri dönüştürme](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks Python etkinliği

Bir Data Factory işlem hattının Azure Databricks Python etkinliği Azure Databricks kümenizdeki bir Python dosyası çalıştırır. Azure Databricks, Apache Spark çalıştırmak için yönetilen bir platformdur. Bkz. [Azure Databricks bir Python etkinliği çalıştırarak verileri dönüştürme](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Özel etkinlik
Data Factory tarafından desteklenmeyen bir şekilde veri dönüştürmeniz gerekiyorsa, kendi veri işleme mantığınızla özel bir etkinlik oluşturabilir ve etkinliği ardışık düzende kullanabilirsiniz. Özel .NET etkinliğini bir Azure Batch hizmeti veya Azure HDInsight kümesi kullanarak çalışacak şekilde yapılandırabilirsiniz. Ayrıntılar için bkz. [özel etkinlikleri kullanma](transform-data-using-dotnet-custom-activity.md) makalesi. 

R yüklü HDInsight kümenizde R betiklerini çalıştırmak için özel bir etkinlik oluşturabilirsiniz. Bkz. [Azure Data Factory kullanarak R Betiği çalıştırma](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>İşlem ortamları
İşlem ortamı için bağlı bir hizmet oluşturun ve ardından bir dönüştürme etkinliği tanımlarken bağlı hizmeti kullanın. Data Factory tarafından desteklenen iki tür işlem ortamı vardır. 

- **İsteğe**bağlı: Bu durumda, bilgi işlem ortamı Data Factory tarafından tam olarak yönetilir. Bir iş, verileri işlemek ve iş tamamlandığında kaldırılmadan önce Data Factory hizmeti tarafından otomatik olarak oluşturulur. İş yürütme, küme yönetimi ve önyükleme eylemleri için isteğe bağlı işlem ortamının ayrıntılı ayarlarını yapılandırabilir ve kontrol edebilirsiniz. 
- **Kendinizinkini getir**: Bu durumda, kendi bilgi işlem ortamınızı (örneğin HDInsight kümesi) Data Factory bağlı bir hizmet olarak kaydedebilirsiniz. Bilgi işlem ortamı sizin tarafınızdan yönetilir ve Data Factory hizmeti tarafından etkinlikleri yürütmek için kullanılır. 

Data Factory tarafından desteklenen işlem hizmetleri hakkında bilgi edinmek için bkz. [Işlem bağlantılı hizmetleri](compute-linked-services.md) makalesi. 

## <a name="next-steps"></a>Sonraki adımlar
Dönüştürme etkinliği kullanma örneği için aşağıdaki öğreticiye bakın: [öğretici: Spark kullanarak veri dönüştürme](tutorial-transform-data-spark-powershell.md)
