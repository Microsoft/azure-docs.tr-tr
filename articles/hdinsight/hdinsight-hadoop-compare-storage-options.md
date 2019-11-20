---
title: Depolama seçeneklerini Azure HDInsight kümeleriyle kullanım için karşılaştırın
description: Depolama türlerine genel bir bakış ve bunların Azure HDInsight ile nasıl çalıştıkları hakkında bilgi sağlar.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: b73810b37020bf01c1088f194bd426e93fd95d2c
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180777"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Depolama seçeneklerini Azure HDInsight kümeleriyle kullanım için karşılaştırın

HDInsight kümeleri oluştururken birkaç farklı Azure depolama hizmeti arasında seçim yapabilirsiniz:

* Azure Storage
* Azure Data Lake Storage 2. Nesil
* Azure Data Lake Storage 1. Nesil

Bu makale, bu depolama türlerine ve bunların benzersiz özelliklerine genel bir bakış sağlar.

Aşağıdaki tabloda farklı HDInsight sürümleriyle desteklenen Azure depolama hizmetleri özetlenmektedir:

| Depolama birimi hizmeti | Hesap türü | Ad alanı türü | Desteklenen hizmetler | Desteklenen performans katmanları | Desteklenen erişim katmanları | HDInsight Sürümü | Küme türü |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage 2. Nesil| Genel amaçlı v2 | Hiyerarşik (dosya sistemi) | Blob | Standart | Sık erişimli, seyrek erişimli Arşiv | 3.6 + | Tümü |
|Azure Storage| Genel amaçlı v2 | Object | Blob | Standart | Sık erişimli, seyrek erişimli Arşiv | 3.6 + | Tümü |
|Azure Storage| Genel amaçlı v1 | Object | Blob | Standart | Yok | Tümü | Tümü |
|Azure Storage| BLOB depolama * * | Object | Blok Blobu | Standart | Sık erişimli, seyrek erişimli Arşiv | Tümü | Tümü |
|Azure Data Lake Storage 1. Nesil| Yok | Hiyerarşik (dosya sistemi) | Yok | Yok | Yok | yalnızca 3,6 | HBase dışında tümü |

\* * HDInsight kümeleri için yalnızca ikincil depolama hesapları BlobStorage türünde olabilir ve Sayfa Blobu desteklenen bir depolama seçeneği değildir.

Azure depolama hesabı türleri hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../storage/common/storage-account-overview.md)

Azure depolama erişim katmanları hakkında daha fazla bilgi için bkz [. Azure Blob depolama: Premium (Önizleme), sık erişimli, seyrek erişimli ve arşiv depolama katmanları](../storage/blobs/storage-blob-storage-tiers.md)

Birincil ve isteğe bağlı ikincil depolama için farklı hizmet birleşimleri kullanarak bir küme oluşturabilirsiniz. Aşağıdaki tabloda, HDInsight 'ta Şu anda desteklenen küme depolama yapılandırmalarının özeti verilmiştir:

| HDInsight Sürümü | Birincil depolama alanı | İkincil depolama | Desteklenen |
|---|---|---|---|
| 3,6 & 4,0 | Genel Amaçlı v1, Genel Amaçlı v2 | Genel Amaçlı v1, Genel Amaçlı v2, BlobStorage (blok Blobları) | Evet |
| 3,6 & 4,0 | Genel Amaçlı v1, Genel Amaçlı v2 | Data Lake Storage 2. Nesil | Hayır |
| 3,6 & 4,0 | Genel Amaçlı v1, Genel Amaçlı v2 | Data Lake Storage Gen1 | Evet |
| 3,6 & 4,0 | Data Lake Storage 2. * | Data Lake Storage 2. Nesil | Evet |
| 3,6 & 4,0 | Data Lake Storage 2. * | Genel Amaçlı v1, Genel Amaçlı v2, BlobStorage (blok Blobları) | Evet |
| 3,6 & 4,0 | Data Lake Storage 2. Nesil | Data Lake Storage Gen1 | Hayır |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Evet |
| 3.6 | Data Lake Storage Gen1 | Genel Amaçlı v1, Genel Amaçlı v2, BlobStorage (blok Blobları) | Evet |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage 2. Nesil | Hayır |
| 4.0 | Data Lake Storage Gen1 | Any | Hayır |

\* = Bu, bir veya birden çok Data Lake Storage 2. hesabı olabilir, çünkü tüm kurulum, küme erişimi için aynı yönetilen kimliği kullanır.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Hadoop ile Azure Data Lake Storage 2. kullanma

Azure Data Lake Storage 2., Azure Data Lake Storage 1. temel özellikleri alır ve bunları Azure Blob depolama alanına tümleştirir. Bu özellikler Hadoop, Azure Active Directory (Azure AD) ve POSIX tabanlı erişim denetim listeleri (ACL 'Ler) ile uyumlu bir dosya sistemi içerir. Bu birleşim, blob depolamanın katmanlama ve veri yaşam döngüsü yönetimini de kullanırken Azure Data Lake Storage 1. performansından yararlanmanızı sağlar.

Azure Data Lake Storage 2. hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 2. giriş](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. temel işlevselliği

* **Hadoop ile uyumlu olan erişim:** Azure Data Lake Storage 2., verileri bir Hadoop Dağıtılmış Dosya Sistemi (bir) ile yaptığınız gibi yönetebilir ve erişebilirsiniz. Azure blob dosya sistemi (ABFS) sürücüsü, Azure HDInsight ve Azure Databricks dahil olmak üzere tüm Apache Hadoop ortamlarında kullanılabilir. Data Lake Storage 2. depolanan verilere erişmek için ABFS kullanın.

* **POSIX izinlerinin bir üst kümesi:** Data Lake Gen2 için güvenlik modeli, Data Lake Storage 2. özgü bazı ayrıntı düzeyi ile birlikte ACL ve POSIX izinlerini destekler. Ayarlar, Apache Hive ve Apache Spark gibi yönetim araçları veya çerçeveler aracılığıyla yapılandırılabilir.

* **Maliyet verimliliği:** Data Lake Storage 2. düşük maliyetli depolama kapasitesi ve işlemler sunar. Azure Blob depolama yaşam döngüsü gibi özellikler, verilerin yaşam döngüsü boyunca taşınmasıyla faturalandırma tarifelerinin daha düşük maliyetlerine yardımcı olur.

* **BLOB depolama araçları, çerçeveler ve uygulamalarla uyumluluk:** Data Lake Storage 2. blob depolamaya yönelik geniş bir araç, çerçeve ve uygulama dizisiyle çalışmaya devam eder.

* **İyileştirilmiş sürücü:** ABFS sürücüsü, büyük veri analizi için özel olarak iyileştirilmiştir. Karşılık gelen REST API 'Leri, dağıtılmış dosya sistemi (DFS) uç noktası üzerinden dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 ' deki yenilikler

#### <a name="managed-identities-for-secure-file-access"></a>Güvenli dosya erişimi için Yönetilen kimlikler

Azure HDInsight, Azure Data Lake Storage 2. içindeki dosyalara küme erişiminin güvenliğini sağlamak için Yönetilen kimlikler kullanır. Yönetilen kimlikler, Azure hizmetlerini otomatik olarak yönetilen kimlik bilgileri kümesiyle sağlayan Azure Active Directory özelliğidir. Bu kimlik bilgileri, Active Directory kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanılabilir. Yönetilen kimliklerin kullanılması, kimlik bilgilerini kod veya yapılandırma dosyalarında depolamanızı gerektirmez.

Daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Azure blob dosya sistemi sürücüsü

Apache Hadoop uygulamalar yerel disk depolamadan veri okumayı ve yazmayı bekler. ABFS gibi bir Hadoop dosya sistemi sürücüsü, Hadoop uygulamalarının normal Hadoop dosya sistemi işlemlerine öykünürken bulut depolamayla çalışabilmesine olanak sağlar. Sürücü, uygulamadan alınan komutları gerçek bulut depolama platformunun anladığı işlemlere dönüştürür.

Daha önce, Hadoop dosya sistemi sürücüsü, tüm dosya sistemi işlemlerini istemci tarafında yapılan çağrılar REST API Azure Storage 'a dönüştüryordu ve sonra REST API çağırırdı. Bununla birlikte, bu istemci tarafı dönüştürmesi, bir dosyanın yeniden adlandırılması gibi tek bir dosya sistemi işlemi için birden çok REST API çağrısı sonucu oluşur. ABFS, bazı Hadoop dosya sistemi mantığını istemci tarafından sunucu kenarına taşımıştır. Azure Data Lake Storage 2. API 'SI artık BLOB API 'SI ile paralel olarak çalışır. Artık yaygın Hadoop dosya sistemi işlemleri tek bir REST API çağrısıyla yürütülemediğinden, bu geçiş performansı geliştirir.

Daha fazla bilgi için bkz [. Azure blob dosya sistemi sürücüsü (ABFS): Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md)Için adanmış bir Azure depolama sürücüsü.

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 için URI düzeni 

Azure Data Lake Storage 2., HDInsight 'tan Azure Storage 'daki dosyalara erişmek için yeni bir URI şeması kullanır:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI şeması, SSL şifreli erişim sağlar.

`<FILE_SYSTEM_NAME>`Data Lake Storage 2. dosya sisteminin yolunu tanımlar.

`<ACCOUNT_NAME>`Azure depolama hesabı adını tanımlar. Tam uygun etki alanı adı (FQDN) gereklidir.

`<PATH>`dosya ya da dizin \ yol adı.

`<FILE_SYSTEM_NAME>` Ve`<ACCOUNT_NAME>` değerleri belirtilmemişse, varsayılan dosya sistemi kullanılır. Varsayılan dosya sistemindeki dosyalar için göreli bir yol veya mutlak bir yol kullanın. Örneğin, `hadoop-mapreduce-examples.jar` HDInsight kümeleriyle birlikte gelen dosyaya aşağıdaki yollardan biri kullanılarak başvurulabilir:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Dosya adı HDInsight sürüm `hadoop-examples.jar` 2,1 ve 1,6 kümelerinde bulunur. HDInsight dışındaki dosyalarla çalışırken, çoğu yardımcı programın ABFS biçimini tanımadığı ancak bunun yerine temel bir yol biçimi `example/jars/hadoop-mapreduce-examples.jar`beklediği fark edeceksiniz.

Daha fazla bilgi için bkz. [Azure Data Lake Storage 2. URI 'Si kullanma](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Azure depolama, HDInsight ile sorunsuz bir şekilde tümleşen sağlam bir genel amaçlı depolama çözümüdür. HDInsight, Azure Depolama’daki bir blob kapsayıcıyı kümenin varsayılan dosya sistemi olarak kullanabilir. Bir bir bir, bir bir bir bir bir bir bir işlem arabirimi aracılığıyla, HDInsight 'taki tüm bileşen kümesi blob olarak depolanan yapılandırılmış veya yapılandırılmamış verilerde doğrudan çalışabilir

HDInsight günlüklerini ve geçici dosyaları kendi iş verilerinizde yalıtmak için varsayılan küme depolama alanı ve iş verileriniz için ayrı depolama kapsayıcıları kullanmanız önerilir. Ayrıca, her bir depolama maliyetini azaltmak için, uygulama ve sistem günlükleri içeren varsayılan blob kapsayıcısını silmenizi öneririz. Kapsayıcıyı silmeden önce günlükleri aldığınızdan emin olun.

Depolama hesabınızı **Seçili ağlardaki** **güvenlik duvarları ve sanal ağlar** kısıtlamalarına göre güvenli hale getirmek isterseniz, HDInsight 'ın depolama verilerinize erişebilmesi Için **güvenilir Microsoft hizmetlerine izin ver...** özel durumunu etkinleştirdiğinizden emin olun. hesabı.

### <a name="hdinsight-storage-architecture"></a>HDInsight depolama mimarisi

Aşağıdaki diyagramda Azure Storage 'ın HDInsight mimarisinin soyut bir görünümü sunulmaktadır:

![HDInsight depolama mimarisi](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "HDInsight depolama mimarisi")

HDInsight, işlem düğümlerine yerel olarak bağlı olan dağıtılmış dosya sistemine erişim imkanı sağlar. Bu dosya sistemine tam uygun URI kullanılarak erişilebilir, örneğin:

    hdfs://<namenodehost>/<path>

HDInsight aracılığıyla Azure Storage 'daki verilere de erişebilirsiniz. Sözdizimi aşağıdaki gibidir:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

HDInsight kümeleri ile Azure depolama hesabı kullanırken aşağıdaki ilkeleri göz önünde bulundurun:

* **Bir kümeye bağlı depolama hesaplarındaki kapsayıcılar:** Hesap adı ve anahtarı oluşturma sırasında kümeyle ilişkili olduğundan, bu kapsayıcılardaki bloblara tam erişiminiz vardır.

* **Bir kümeye bağlı *olmayan* depolama hesaplarındaki genel kapsayıcılar veya genel Bloblar:** Kapsayıcılarda bloblara salt okuma izninizin olması gerekir.
  
  > [!NOTE]  
  > Ortak kapsayıcılar, bu kapsayıcıda kullanılabilen tüm Blobların bir listesini almanızı ve kapsayıcı meta verilerini almanızı sağlar. Genel blob'lar, yalnızca tam URL'yi biliyorsanız blob erişiminize izin verir. Daha fazla bilgi için bkz. [Kapsayıcılara ve bloblara anonim okuma erişimini yönetme](../storage/blobs/storage-manage-access-to-resources.md).

* **Bir kümeye bağlı *olmayan* depolama hesaplarındaki özel kapsayıcılar:** Web Hcat işlerini gönderdiğinizde depolama hesabını tanımlamadığınız müddetçe kapsayıcılardaki bloblara erişemezsiniz. 

Oluşturma işleminde tanımlanan depolama hesapları ve bunların anahtarların %HADOOP_HOME%/conf/core-site.xml küme düğümlerinde depolanır. HDInsight, varsayılan olarak Core-site. xml dosyasında tanımlanan depolama hesaplarını kullanır. Bu ayarı, [Apache ambarı](./hdinsight-hadoop-manage-ambari.md)'nı kullanarak değiştirebilirsiniz.

Apache Hive, MapReduce, Apache Hadoop streaming ve Apache Pig dahil olmak üzere birden çok WebHCat işi, depolama hesaplarının ve meta verilerin bir açıklamasını taşıyabilir. (Bu, şu anda depolama hesapları olan Pig için geçerlidir ancak meta veriler için değil.) Daha fazla bilgi için bkz. [alternatif depolama hesaplarıyla HDInsight kümesi ve meta depolar kullanma](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Bloblar yapılandırılmış ve yapılandırılmamış veriler için kullanılabilir. Blob kapsayıcıları, verileri anahtar/değer çiftleri olarak depolar ve Dizin hiyerarşisine sahip değildir. Bununla birlikte, anahtar adı, bir dosyanın dizin yapısında depolanmış gibi görünmesini sağlamak için eğik çizgi karakteri (/) içerebilir. Örneğin, bir Blobun anahtarı olabilir `input/log1.txt`. Gerçek `input` dizin yok, ancak anahtar adındaki eğik çizgi karakteri nedeniyle anahtar bir dosya yolu gibi görünüyor.

### <a id="benefits"></a>Azure Depolamanın yararları
Birlikte bulunan işlem kümeleri ve depolama kaynakları, kapsanan performans maliyetlerine sahiptir. Bu maliyetler, işlem kümelerinin Azure bölgesindeki depolama hesabı kaynaklarına yakın şekilde oluşturulduğu şekilde azaltıldığında. Bu bölgede, işlem düğümleri Azure Storage içindeki yüksek hızlı ağ üzerinden verilere verimli bir şekilde erişebilir.

Verileri, Azure Storage 'da, her ne kadar çok avantaj elde edersiniz:

* **Verilerin yeniden kullanımı ve paylaşılması:** Bilgisayar \ ' daki veriler, işlem kümesi içinde bulunur. Yalnızca işlem kümesi erişimi olan uygulamalar HDFS API'lerini kullanarak verileri kullanabilir. Azure depolama 'daki verilere, buna karşılık olarak,,, bu,,,,,,,,,,,,,,,,, Bu düzenleme nedeniyle, verileri oluşturmak ve kullanmak için daha büyük bir uygulamalar kümesi (diğer HDInsight kümeleri dahil olmak üzere) ve araçları kullanılabilir.

* **Veri arşivleme:** Veriler Azure depolama 'da depolandığında, hesaplama için kullanılan HDInsight kümeleri, Kullanıcı verilerini kaybetmeden güvenle silinebilir.

* **Veri depolama maliyeti:** Verilerin DFS 'de uzun süreli depolanması, verileri Azure Storage 'da depolamaktan daha maliyetlidir çünkü bir işlem kümesinin maliyeti Azure Storage maliyetinden daha yüksektir. Ayrıca, verilerin her işlem kümesi üretimi için yeniden yüklenmesi gerektiğinden, veri yükleme maliyetlerini de kaydediyorsunuz.

* **Elastik genişleme:** Her ne kadar ölçekli bir dosya sistemi sağlar, ancak ölçek kümeniz için oluşturduğunuz düğüm sayısına göre belirlenir. Ölçeği değiştirmek, Azure depolama 'da otomatik olarak aldığınız esnek ölçeklendirme özelliklerine bağlı olandan daha karmaşık olabilir.

* **Coğrafi çoğaltma:** Azure depolama, coğrafi olarak çoğaltılabilir. Coğrafi çoğaltma size coğrafi kurtarma ve veri yedekliliği sağlasa da, coğrafi olarak çoğaltılan konuma yönelik bir yük devretme performansı önemli ölçüde etkiler ve ek ücret ödemeniz gerekebilir. Bu nedenle, coğrafi çoğaltma ve yalnızca verilerin değeri ek maliyeti ortaya çıkarabiliyorsanız seçin.

Belirli MapReduce işleri ve paketleri, Azure depolama 'da depolamak istemediğiniz ara sonuçlar oluşturabilir. Bu durumda, verileri yerel olarak depolamayı tercih edebilirsiniz. HDInsight, Hive işlerinde ve diğer işlemlerde bu ara sonuçlardan birkaçı için DFS kullanır.

> [!NOTE]  
> En çok bu komut (örneğin `ls` `copyFromLocal`,,, ve `mkdir`) Azure depolama 'da beklendiği gibi çalışır. Yalnızca yerel bir IBU uygulamaya özgü komutlar (DFS olarak adlandırılır), `fschk` ve `dfsadmin`gibi Azure depolama 'da farklı davranışlar gösterir.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage 1. genel bakış

Azure Data Lake Storage 1., büyük veri analizi iş yükleri için kurumsal çapta bir hiper ölçek deposudur. Azure Data Lake kullanarak, işletimsel ve araştırmacı analizler için herhangi bir boyut, tür ve Alım hızına ait verileri tek bir yerde yakalayabilirsiniz.

Web, uyumlu REST API 'Lerini kullanarak Hadoop 'tan (bir HDInsight kümesiyle kullanılabilir) Data Lake Storage 1. erişin. Data Lake Storage 1., depolanan verilerde analizler sağlamak için tasarlanmıştır ve veri analizi senaryolarında performans için ayarlanır. Bu, gerçek dünya kurumsal kullanım örnekleri için gerekli olan özellikleri içerir. Bu yetenekler güvenlik, yönetilebilirlik, ölçeklenebilirlik, güvenilirlik ve kullanılabilirliği içerir.

Azure Data Lake Storage 1. hakkında daha fazla bilgi için [Azure Data Lake Storage 1. ayrıntılı genel bakış](../data-lake-store/data-lake-store-overview.md)konusuna bakın.

Data Lake Storage 1. temel özellikleri şunlardır.

### <a name="compatibility-with-hadoop"></a>Hadoop ile uyumluluk

Data Lake Storage 1., bir Apache Hadoop dosya sistemidir ve bu, Hadoop ekosistemi ile birlikte çalışmaktadır.  Webbir API kullanan mevcut HDInsight uygulamalarınız veya hizmetleriniz Data Lake Storage 1. kolayca tümleştirilebilir. Data Lake Storage 1. Ayrıca, uygulamalar için bir Web, uyumlu REST arabirimi sunar.

Data Lake Storage 1. depolanan veriler, MapReduce veya Hive gibi Hadoop analitik çerçeveleri kullanılarak kolayca çözümlenebilir. Azure HDInsight kümeleri, Data Lake Storage 1. depolanan verilere doğrudan erişmek için sağlanabilir ve yapılandırılabilir.

### <a name="unlimited-storage-petabyte-files"></a>Sınırsız depolama, petabayt boyutlu dosyalar

Data Lake Storage 1., sınırsız depolama sağlar ve analiz için çeşitli verileri depolamak için uygundur. Hesap boyutları, dosya boyutları veya bir veri Gölü içinde depolanabilecek veri miktarı için sınır yoktur. Tek tek dosyalar, kilobayt ile petabaytlarca arasında değişebilir, Data Lake Storage 1. her türlü veriyi depolamak için harika bir seçim yapabilir. Veriler birden çok kopya yapılarak durarak depolanır ve verilerin Veri Gölü ne kadar süreyle depolanabileceğini sınırlayabilirsiniz.

### <a name="performance-tuning-for-big-data-analytics"></a>Büyük veri analizi için performans ayarlama

Data Lake Storage 1., büyük miktarlarda veriyi sorgulamak ve analiz etmek için büyük ölçekli verimlilik gerektiren büyük ölçekli analitik sistemleri çalıştırmak için oluşturulmuştur. Veri Gölü, bir dosyanın parçalarını birkaç ayrı depolama sunucusu üzerinden yayar. Verileri analiz edilirken, bu kurulum dosya paralel olarak okunduğunuz zaman okuma verimini geliştirir.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Kuruluş için hazırlık: Yüksek oranda kullanılabilir ve güvenli

Data Lake Storage 1. sektör standardı kullanılabilirliği ve güvenilirliği sağlar. Veri varlıkları, beklenmedik şekilde depolanır: yinelenen kopyalar, beklenmeyen hatalara karşı koruma. Kuruluşlar, kendi çözümlerinde Data Lake Storage 1., mevcut veri platformunun önemli bir parçası olarak kullanabilir.

Data Lake Storage 1. Ayrıca, depolanan veriler için kurumsal düzeyde güvenlik sağlar. Daha fazla bilgi için bkz. [Azure Data Lake Storage 1. verileri güvenli hale getirme](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Esnek veri yapıları

Data Lake Storage 1., önceki Dönüştürmelere gerek kalmadan, tüm verileri yerel biçiminde, olduğu gibi depolayabilirler. Data Lake Storage 1., veriler yüklenmeden önce bir şemanın tanımlanmasını gerektirmez. Bireysel analitik çerçeve, verileri Yorumlar ve analiz sırasında bir şemayı tanımlar. Rastgele boyut ve formatların dosyalarını depolayabildiğinden, Data Lake Storage 1. yapılandırılmış, semisde ve yapılandırılmamış verileri işleyebilir.

Veriler için Data Lake Storage 1. kapsayıcılar temelde klasörler ve dosyalardır. SDK, Azure portal ve Azure PowerShell kullanarak depolanan veriler üzerinde işlem yapabilirsiniz. Bu arabirimleri ve uygun kapsayıcıları kullanarak verilerinizi depoya yerleştirdiğiniz sürece, herhangi bir veri türünü saklayabilirsiniz. Data Lake Storage 1., depoladığı verilerin türüne göre hiçbir özel veri işleme gerçekleştirmez.

## <a name="DataLakeStoreSecurity"></a>Data Lake Storage 1. veri güvenliği
Data Lake Storage 1., kimlik doğrulaması için Azure Active Directory kullanır ve verilerinize erişimi yönetmek için erişim denetim listelerini (ACL 'Ler) kullanır.

| **Özelliği** | **Açıklama** |
| --- | --- |
| Authentication |Data Lake Storage 1., Data Lake Storage 1. depolanan tüm veriler için kimlik ve erişim yönetimi için Azure Active Directory (Azure AD) ile tümleşir. Tümleştirme nedeniyle tüm Azure AD özelliklerinden faydaların Data Lake Storage 1. Bu özellikler çok faktörlü kimlik doğrulaması, koşullu erişim, rol tabanlı erişim denetimi, uygulama kullanımı izleme, güvenlik izleme ve uyarı vb. içerir. Data Lake Storage 1., REST arabirimi içinde kimlik doğrulaması için OAuth 2,0 protokolünü destekler. [Azure Active Directory kullanarak Azure Data Lake Storage 1. Içindeki kimlik doğrulamaya](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md) bakın|
| Erişim denetimi |Data Lake Storage 1., Webdapterprotocol tarafından sunulan POSIX stili izinleri destekleyerek erişim denetimi sağlar. ACL’ler kök klasörde, alt klasörlerde ve dosyalarda tek tek etkinleştirilebilir. ACL 'Lerin Data Lake Storage 1. bağlamında nasıl çalıştığı hakkında daha fazla bilgi için, bkz. [Data Lake Storage 1. Access Control](../data-lake-store/data-lake-store-access-control.md). |
| Şifreleme |Data Lake Storage 1., hesapta depolanan veriler için de şifreleme sağlar. Data Lake Storage 1. bir hesap oluştururken şifreleme ayarlarını belirtirsiniz. Verilerinizin şifrelenmesini veya şifrelemeyi kabul etmesinin tercih edilebilir. Daha fazla bilgi için bkz. [Data Lake Storage 1. şifreleme](../data-lake-store/data-lake-store-encryption.md). Şifrelemeyle ilgili yapılandırma sağlama yönergeleri için bkz. [Azure Portal kullanarak Azure Data Lake Storage 1. kullanmaya başlama](../data-lake-store/data-lake-store-get-started-portal.md). |

Data Lake Storage 1. verilerin güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Azure Data Lake Storage 1. depolanan verileri güvenli hale getirme](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage 1. ile uyumlu uygulamalar
Data Lake Storage 1., Hadoop ekosistemindeki çoğu açık kaynaklı bileşenlerle uyumludur. Ayrıca diğer Azure hizmetleriyle sorunsuz şekilde tümleştirilir.  Data Lake Storage 1. hem açık kaynaklı bileşenlerle hem de diğer Azure hizmetleriyle nasıl kullanılabileceği hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları izleyin.

* Data Lake Storage 1. birlikte çalışan açık kaynaklı uygulamaların bir listesi için [Azure Data Lake Storage 1. ile birlikte çalışan açık kaynaklı büyük veri uygulamalarına](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) bakın.
* Daha geniş bir senaryoyu etkinleştirmek için diğer Azure hizmetleriyle Data Lake Storage 1. nasıl kullanacağınızı anlamak için bkz. [Azure Data Lake Storage 1. diğer Azure hizmetleriyle tümleştirme](../data-lake-store/data-lake-store-integrate-with-other-services.md) .
* Verileri alma, verileri işleme, verileri indirme ve verileri görselleştirme gibi senaryolarda Data Lake Storage 1. kullanmayı öğrenmek için [büyük veri gereksinimleri için Azure Data Lake Storage 1. kullanma](../data-lake-store/data-lake-store-data-scenarios.md) konusuna bakın.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage 1. dosya sistemi (adl://)
Hadoop ortamlarında (bir HDInsight kümesiyle kullanılabilir), yeni dosya sistemi, AzureDataLakeFilesystem (adl://) aracılığıyla Data Lake Storage 1. erişebilirsiniz. Adl://kullanan uygulamaların ve hizmetlerin performansı, şu anda Webbir 'da kullanılamayan yollarla iyileştirilebilir. Sonuç olarak, Data Lake Storage 1. kullandığınızda önerilen adl://kullanarak en iyi performansı kullanbilme esnekliği elde edersiniz veya Webbir API 'yi doğrudan kullanmaya devam ederek mevcut kodu koruyabilirsiniz. Azure HDInsight, Data Lake Storage 1. en iyi performansı sağlamak için AzureDataLakeFilesystem 'in tam avantajlarından yararlanır.

Aşağıdaki kullanarak verilerinize Data Lake Storage 1. erişin:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Data Lake Storage 1. verilere erişme hakkında daha fazla bilgi için bkz. [depolanan verilerde kullanılabilir eylemler](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Sonraki adımlar

* [Azure Data Lake Storage 2. giriş](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Depolama’ya giriş](../storage/common/storage-introduction.md)
