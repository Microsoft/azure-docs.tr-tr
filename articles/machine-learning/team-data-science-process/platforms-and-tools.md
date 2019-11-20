---
title: Platform ve veri bilimi projeleri - Team Data Science Process için Araçlar
description: Maddeler halinde listeler ve üzerinde Team Data Science Process Standartlaştırma kuruluşların kullanabileceği veri ve analiz kaynaklarını ele alır.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8ad5c4cb4d17443144febd716391803064ccdad1
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626370"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Platformlar ve araçlar için veri bilimi projeleri

Microsoft, hem bulut hem de şirket içi platformlar için bir tam spektrumlu veri ve Analiz Hizmetleri ve kaynakları sağlar. Veri bilimi projelerinizi yürütülmesini etkili ve ölçeklenebilir hale getirmek için dağıtılabilir. Veri bilimi projeleri bir izlenebilir uygulayan ekipler için yönergeler, sürüm denetimli ve işbirliğine dayalı yolu tarafından sağlanan [Team Data Science Process](overview.md) (TDSP).  Personel roller ve bir veri bilimi takım bu işlemle ilgili hale getirerek işlenen ilişkilendirilen görevlerinin ana hat için bkz. [Team Data Science Process rolleri ve görevleri](roles-tasks.md).

TDSP kullanan veri bilimi ekipleri için kullanılabilir veri ve Analiz Hizmetleri şunlardır:

- Veri bilimi sanal makineleri (Windows ve Linux CentOS)
- HDInsight Spark kümeleri
- SQL Veri Ambarı
- Azure Data Lake
- HDInsight Hive kümeleri
- Azure Dosya Depolama
- SQL Server 2016 R Services

Bu belgede, size kısaca kaynaklar açıklanır ve öğreticiler ve Kılavuzlar TDSP takımlar yayımladıktan bağlantılar sağlar. Bunlar, bunları adım adım kullanın ve bunları, akıllı uygulamalar oluşturmak için kullanmaya başlama öğrenmenize yardımcı olabilir. Bu kaynaklar hakkında daha fazla bilgi ürün sayfalarında kullanılabilir. 

## <a name="data-science-virtual-machine-dsvm"></a>Veri bilimi sanal makinesi (DSVM)

Veri bilimi sanal makinesi hem Windows hem de Linux, Microsoft tarafından sunulan, veri bilimi modelleme ve geliştirme etkinlikleri için popüler araçların yer. Gibi araçları içerir:

- Microsoft R Server Geliştirici sürümü 
- Anaconda Python dağıtım
- Python ve R için Jupyter Not Defterleri 
- Visual Studio Community sürümüyle Python ve R araçları Windows / Linux üzerinde Eclipse
- Windows için Power BI desktop
- SQL Server 2016 Developer Edition Windows / Linux üzerinde Postgres

Ayrıca **ML ve AI Araçları** xgboost, mxnet ve Vowpal Wabbit gibi.

Şu anda DSVM kullanılabilir **Windows** ve **Linux CentOS** işletim sistemleri. Üzerinde yürütmek için planlama veri bilimi projeleri gereksinimlerine göre DSVM (CPU çekirdek sayısı) ve bellek boyutunu seçin. 

Windows DSVM sürümü hakkında daha fazla bilgi için bkz. [Microsoft Veri bilimi sanal makinesi](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) Azure Marketi'nde. DSVM'nin Linux sürümü için bkz: [Linux veri bilimi sanal makinesi](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).

Bazı genel veri bilimi görevlerini verimli bir şekilde DSVM üzerinde çalıştırma hakkında bilgi edinmek için bkz: [veri bilimi sanal makinesi üzerinde yapabileceğiniz on işlem](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark kümeleri

Apache Spark, büyük veri analizi uygulamalarının performansını artırmak üzere bellek içi işlemeyi destekleyen altyapısıdır bir açık kaynak paralel olur. Spark işleme altyapısı hız, kullanım kolaylığı ve Gelişmiş analiz için oluşturulmuştur. Spark'ın bellek içi hesaplama özellikleri onu ve grafik hesaplamaları machine learning'de yinelemeli algoritmalar için iyi bir seçim haline getirir. Azure'da depolanan mevcut verilerinizi kolayca Spark kullanarak işleyebileceğiniz şekilde Spark Azure Blob Depolama (WASB) ile de uyumludur.

HDInsight’ta Spark kümesi oluşturduğunuzda, Spark yüklenmiş ve yapılandırılmış olarak Azure işlem kaynakları oluşturursunuz. HDInsight Spark kümesi oluşturmak için yaklaşık 10 dakika sürer. Azure Blob Depolama alanında işlenmek üzere data Store. Bir küme ile Azure Blob Depolama kullanma hakkında daha fazla bilgi için bkz: [kullanım HDFS uyumlu Azure Blob Depolama, HDInsight Hadoop ile](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

TDSP ekibinden Microsoft Veri bilimi çözümleri oluşturmak için Azure HDInsight Spark kümeleri kullanma hakkında iki uçtan uca izlenecek yollar yayımlanan bir Python ve diğer Scala kullanarak. Azure HDInsight hakkında daha fazla bilgi için **Spark kümeleri**, bkz: [genel bakış: HDInsight Linux üzerinde Apache Spark](../../hdinsight/spark/apache-spark-overview.md). Bir veri bilimi çözümü kullanarak oluşturmayı öğrenmek için **Python** bir Azure HDInsight Spark kümesinde bkz [genel bakış, verilerin Azure HDInsight üzerinde Spark'ı kullanarak bilimi](spark-overview.md). Bir veri bilimi çözümü kullanarak oluşturmayı öğrenmek için **Scala** bir Azure HDInsight Spark kümesinde bkz [Azure üzerinde Scala ve Spark kullanan veri bilimi](scala-walkthrough.md). 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL Veri Ambarı

Azure SQL veri ambarı, işlem kaynakları fazladan sağlama veya gereksiz ödeme yapmadan kolayca ve saniye cinsinden ölçeklendirmenize olanak tanır. Ayrıca, bulut maliyetlerinizi daha iyi yönetmek için özgürlüğünü işlem kaynaklarının kullanımı, duraklatmak için benzersiz seçeneği sunar. Ölçeklenebilir işlem kaynakları dağıtma olanağı, Azure SQL veri ambarı'na tüm verilerinizi getirin mümkün kılar. En düşük depolama maliyetleri ve analiz etmek istediğiniz veri kümeleri yalnızca kısımlarına işlem çalıştırabilirsiniz. 

Azure SQL veri ambarı hakkında daha fazla bilgi için bkz. [SQL veri ambarı](https://azure.microsoft.com/services/sql-data-warehouse) Web sitesi. SQL veri ambarı ile uçtan uca Gelişmiş analiz çözümleri oluşturmayı öğrenmek için bkz: [Team Data Science Process'in çalışması: SQL veri ambarı'nı kullanarak](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Bir Kurumsal Çapta depo her türde herhangi bir resmi gereksinim veya uygulanan şema önce tek bir konumda toplanan verileri Azure veri gölü gibidir. Her türde verinin boyutu ya da yapı bağımsız olarak bir veri gölü veya ne kadar hızlı içe alındığından tutulması için bu esneklik sağlar. Kuruluşlar, ardından Hadoop kullanabilir veya bulmak için Gelişmiş analiz bu veri gölleri desen. Veri gölleri veri puanlamalar ve veri ambarı'na taşımadan önce daha düşük maliyetli veri hazırlığı için bir depo görebilir.

Azure Data Lake hakkında daha fazla bilgi için bkz. [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Bir Azure Data Lake ile ölçeklenebilir uçtan uca veri bilimi çözümü oluşturmayı öğrenmek için bkz: [Azure Data lake'te ölçeklenebilir veri bilimi: Uçtan uca kılavuz](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop) kümesi

Apache Hive bir veri ambarı için veri özetleme, sorgulama ve HiveQL, SQL'e benzer bir sorgu dili kullanarak veri analizi sağlayan, Hadoop sistemidir. Hive, verilerinizin etkileşimli olarak keşfetmek için ya da yeniden kullanılabilir toplu işleme işleri oluşturmak için kullanılabilir.

Hive, büyük ölçüde yapılandırılmamış veriler üzerinde Proje yapısı sağlar. Yapı tanımladıktan sonra kullanın veya bile biliyorsanız, Java veya MapReduce zorunda kalmadan bir Hadoop kümesi, verileri sorgulamak için Hive'ı kullanabilirsiniz. HiveQL (Hive sorgu dili), T-SQL benzer ifadelerle sorgular yazmaya olanak tanır.

Veri uzmanları için Hive Python User-Defined işlevler (UDF'ler) kayıtlarını işlemek için Hive sorguları çalıştırabilirsiniz. Bu özelliği, veri analizi Hive sorguları yeteneğini önemli ölçüde genişletir. Özellikle, bunlar çoğunlukla ile tanıdık dillerde ölçeklenebilir özellik Mühendisliği yürütmek veri bilimcilerine izin verir: Python ve SQL benzeri HiveQL. 

Azure HDInsight kümeleri Hive hakkında daha fazla bilgi için bkz. [HiveQL HDInsight, Hadoop ile Hive kullanma ve](../../hdinsight/hadoop/hdinsight-use-hive.md). Ölçeklenebilir uçtan uca veri bilimi çözümü olan Azure HDInsight kümeleri Hive oluşturmayı öğrenmek için bkz: [Team Data Science Process'in çalışması: HDInsight Hadoop kümeleri kullanarak](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Azure Dosya Depolama 

Azure dosya depolama standart sunucu ileti bloğu (SMB) protokolünü kullanarak bulutta dosya paylaşımları sağlayan bir hizmettir. SMB 2.1 ve SMB 3.0 desteklenir. Azure File Storage, Azure’a dosya paylaşımı kullanan eski uygulamaları maliyetli yeniden yazdırmaya ihtiyaç duymadan ve hızla taşıyabilmenizi sağlar. Azure Virtual Machines’de, Cloud Services’da veya şirket içi istemcilerde çalışan uygulamalar, bir masaüstü uygulamanın tipik SMB paylaşımı bağladığı gibi buluta bir dosya paylaşımı bağlayabilir. Ardından herhangi sayıda uygulama bileşeni eş zamanlı olarak File Storage paylaşımını bağlayıp buna erişim sağlayabilir.

Proje verileri proje takım üyelerinizle paylaşın yer olarak bir Azure dosya depolama oluşturma özelliği, veri bilimi projeleri için özellikle yararlıdır. Her birinin ardından verilerin aynı kopyasını Azure dosya depolama alanına erişebilir. Bunlar ayrıca, bu dosya depolama proje yürütülmesi sırasında oluşturulan özellik kümeleri paylaşmak için kullanabilirsiniz. Bir istemci engagement projesiyse istemcilerinize özellikler ve proje verilerini sizinle paylaşmak için kendi Azure aboneliği kapsamında bir Azure dosya depolama oluşturabilirsiniz. Bu şekilde, istemci projesi veri varlıklarını tam denetime sahiptir. Azure dosya depolama hakkında daha fazla bilgi için bkz. [Windows üzerinde Azure dosya depolama ile çalışmaya başlama](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files) ve [Azure dosya depolamayı Linux ile kullanma konusunda](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R Services

R Services (veritabanında) yeni Öngörüler keşfederek akıllı uygulama geliştirme ve dağıtma için bir platform sağlar. R topluluk tarafından sağlanan birçok paketleri de dahil olmak üzere zengin ve güçlü R dil modelleri oluşturun ve SQL Server verilerinizden Öngörüler oluşturmak için kullanabilirsiniz. R Services (veritabanında) R dili, SQL Server ile tümleştirmek için analiz maliyetlerini ve veri taşıma ile ilişkili güvenlik riskleri ortadan verilere yaklaştırılmasıyla tutulur.

R Services (veritabanında) SQL Server araçları ve teknolojileri kapsamlı bir dizi açık kaynak R diliyle destekler. Bunlar, üstün performans, güvenlik, güvenilirlik ve yönetilebilirlik sunar. Kolay ve bilindik araçları kullanarak R çözümleri dağıtabilirsiniz. Üretim uygulamalarınızı, R çalışma çağırın ve Öngörüler ve Transact-SQL kullanarak görselleri alma. Ayrıca R çözümlerinizin performansı ve ölçeği artırmak için ScaleR kitaplıkları kullanın. Daha fazla bilgi için [SQL Server R Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

TDSP takım Microsoft SQL Server 2016 R Services veri bilimi çözümlerini oluşturmak nasıl gösteren iki uçtan uca izlenecek yollar yayımladığı: biri R programcılarının, diğeri SQL geliştiricileri için. İçin **R programcılarının**, bkz: [veri bilimi uçtan uca kılavuz](https://docs.microsoft.com/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). İçin **SQL geliştiricileri**, bkz: [(eğitim) SQL geliştiricileri için veritabanında Advanced Analytics](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix"></a>Ek: Veri bilimi projeler için Araçlar

### <a name="install-git-credential-manager-on-windows"></a>Windows üzerinde Git kimlik bilgisi Yöneticisi'ni yükleyin

TDSP takip ediyorsanız **Windows**, yüklemeniz gereken **Git Credential Manager'ı (GCM)** Git depoları ile iletişim kurmak için. GCM yüklemek için önce yüklemeniz gerekir **Chocolaty**. Chocolaty ve GCM yüklemek için Windows PowerShell aşağıdaki komutları çalıştırın. bir **yönetici**:  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>(CentOS) Linux makinelerinde Git yükleme

(CentOS) Linux makinelerinde Git'i yüklemek için aşağıdaki bash komutu çalıştırın:

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>(CentOS) Linux makinelerinde genel SSH anahtarı oluşturma

Git komutlarını çalıştırmak için Linux (CentOS) makineleri kullanıyorsanız, bu makine Azure DevOps Hizmetleri tarafından değerlendirilmiştir. böylece, makinenizin ortak SSH anahtarının, Azure DevOps hizmetlerinizi eklemeniz gerekir. İlk olarak, bir ortak SSH anahtarını oluşturun ve SSH ortak anahtarları, Azure DevOps Hizmetleri Güvenlik ayar sayfasında anahtarın eklemek gerekir. 

- SSH anahtarı oluşturmak için aşağıdaki iki komutu çalıştırın: 

        ssh-keygen
        cat .ssh/id_rsa.pub

![SSH anahtarı oluşturmak için komutları](./media/platforms-and-tools/resources-1-generate_ssh.png)

- Kopyalama tüm ssh anahtarı dahil olmak üzere *ssh-rsa*. 
- Azure DevOps hizmetleriniz için oturum açın. 
- Tıklayın **< adınız\>**  tıklayın ve sayfanın sağ üst köşesinde, **güvenlik**. 
    
    ![Adınıza tıklayın ve ardından güvenlik öğesini tıklatın](./media/platforms-and-tools/resources-2-user-setting.png)

- Tıklayın **SSH ortak anahtarları**, tıklatıp **+ Ekle**. 

    ![SSH ortak anahtarları ve ardından tıklama + Ekle'e tıklayın](./media/platforms-and-tools/resources-3-add-ssh.png)

- Yapıştırma ssh anahtarı yalnızca kopyaladığınız metin kutusuna ve kaydedin.


## <a name="next-steps"></a>Sonraki adımlar

Tam işlem için tüm adımları gösteren uçtan uca talimatlara **belirli senaryoları** de sağlanır. Listelenen ve küçük resim açıklamasında ile bağlantılı [örnek izlenecek yollar](walkthroughs.md) konu. Bunlar, bulut, şirket içi araçları ve Hizmetleri, bir iş akışı veya akıllı bir uygulama oluşturmak için işlem hattı birleştirme işlemini göstermektedir. 

Azure Machine Learning Studio kullanma adımları Team Data Science Process içinde yürütülen örnekler için bkz [Azure ML ile](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) öğrenme yolu.
