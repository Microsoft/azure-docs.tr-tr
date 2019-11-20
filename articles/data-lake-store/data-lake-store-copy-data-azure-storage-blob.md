---
title: Azure depolama Bloblarından veri kopyalama Data Lake Storage 1.
description: Azure depolama Bloblarından veri kopyalamak için AdlCopy aracını kullanın Azure Data Lake Storage 1.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 0225405c5d3a511bbb2bbb08c1c13e5adedd5096
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903767"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Azure depolama Bloblarından veri kopyalama Azure Data Lake Storage 1.

> [!div class="op_single_selector"]
> * [DistCp’yi kullanma](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy’yi kullanma](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage 1., aşağıdaki kaynaklardan veri kopyalamak için [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358)olan bir komut satırı aracı sağlar:

* Azure depolama Bloblarından Data Lake Storage 1. Azure depolama bloblarına Data Lake Storage 1. veri kopyalamak için AdlCopy kullanamazsınız.
* İki Data Lake Storage 1. hesabı arasında.

Ayrıca, AdlCopy aracını iki farklı modda kullanabilirsiniz:

* Araç, görevi gerçekleştirmek için Data Lake Storage 1. kaynakları kullandığı **tek başına**.
* Data Lake Analytics hesabınıza atanan birimlerin kopyalama işlemini gerçekleştirmek için kullanıldığı **Data Lake Analytics bir hesabı kullanarak**. Kopyalama görevlerini öngörülebilir bir şekilde gerçekleştirmeyi ararken bu seçeneği kullanmak isteyebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye başlamadan önce aşağıdakilere sahip olmanız ve aşağıdaki işlemleri yapmış olmanız gerekir:

* **Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/pricing/free-trial/).
* **Azure depolama Blobları** kapsayıcısı bazı verilerle.
* **Data Lake Storage 1. hesabı**. Bir oluşturma hakkında yönergeler için bkz. Azure Data Lake Storage 1. kullanmaya [başlama](data-lake-store-get-started-portal.md)
* **Data Lake Analytics hesabı (isteğe bağlı)** -Data Lake Analytics hesabının nasıl oluşturulacağı hakkında yönergeler için bkz. [Azure Data Lake Analytics kullanmaya başlama](../data-lake-analytics/data-lake-analytics-get-started-portal.md) .
* **AdlCopy aracı**. [AdlCopy aracını](https://www.microsoft.com/download/details.aspx?id=50358)yükler.

## <a name="syntax-of-the-adlcopy-tool"></a>AdlCopy aracının sözdizimi

AdlCopy aracıyla çalışmak için aşağıdaki sözdizimini kullanın

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Söz diziminde parametreler aşağıda açıklanmıştır:

| Seçenek | Açıklama |
| --- | --- |
| Kaynak |Azure Depolama Blobu 'ndaki kaynak verilerinin konumunu belirtir. Kaynak bir blob kapsayıcısı, blob veya başka bir Data Lake Storage 1. hesabı olabilir. |
| HD |Kopyalamanın Data Lake Storage 1. hedefini belirtir. |
| SourceKey |Azure Depolama Blobu kaynağı için depolama erişim anahtarını belirtir. Bu, yalnızca kaynak bir blob kapsayıcısı veya blob ise gereklidir. |
| Hesap |**İsteğe bağlı**. Kopyalama işini çalıştırmak için Azure Data Lake Analytics hesabı kullanmak istiyorsanız bunu kullanın. Sözdiziminde/Account seçeneğini kullanırsanız ancak bir Data Lake Analytics hesabı belirtmezseniz, AdlCopy işi çalıştırmak için varsayılan bir hesap kullanır. Ayrıca, bu seçeneği kullanırsanız, Data Lake Analytics hesabınız için kaynak (Azure Depolama Blobu) ve hedef (Azure Data Lake Storage 1.) veri kaynakları olarak eklemeniz gerekir. |
| Birimler |Kopyalama işi için kullanılacak Data Lake Analytics birimi sayısını belirtir. Bu seçenek, Data Lake Analytics hesabını belirtmek için **/Account** seçeneğini kullanırsanız zorunludur. |
| Desen |Hangi Blobların veya dosyaların kopyalanacağını gösteren bir Regex deseninin belirtir. AdlCopy, büyük/küçük harfe duyarlı eşleştirmeyi kullanır. Hiçbir model belirtilmediğinde varsayılan model tüm öğeleri kopyalamadır. Birden çok dosya deseni belirtmek desteklenmez. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Azure Storage blobundan veri kopyalamak için AdlCopy (tek başına) kullanın

1. Bir komut istemi açın ve AdlCopy 'in yüklü olduğu dizine gidin, genellikle `%HOMEPATH%\Documents\adlcopy`.
1. Belirli bir blobu kaynak kapsayıcıdan Data Lake Storage 1. klasöre kopyalamak için aşağıdaki komutu çalıştırın:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Örneğin:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE]
    >Yukarıdaki sözdizimi, Data Lake Storage 1. hesabındaki bir klasöre kopyalanacak dosyayı belirtir. AdlCopy Aracı, belirtilen klasör adı yoksa bir klasör oluşturur.

    Data Lake Storage 1. hesabınıza sahip olduğunuz Azure aboneliğinin kimlik bilgilerini girmeniz istenir. Aşağıdakine benzer bir çıktı görürsünüz:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Ayrıca, aşağıdaki komutu kullanarak tüm Blobları bir kapsayıcıdan Data Lake Storage 1. hesabına kopyalayabilirsiniz:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Örneğin:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Bir Azure Blob depolama hesabından kopyalama yapıyorsanız, BLOB depolama tarafında kopyalama sırasında azaltılan bir durum olabilir. Bu, kopyalama işinizin performansını düşürür. Azure Blob depolama sınırları hakkında daha fazla bilgi edinmek için bkz. Azure [aboneliği ve hizmet sınırları](../azure-subscription-service-limits.md)'Nda Azure depolama sınırları.

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Başka bir Data Lake Storage 1. hesabından veri kopyalamak için AdlCopy (tek başına) kullanın

Ayrıca iki Data Lake Storage 1. hesap arasında veri kopyalamak için AdlCopy komutunu da kullanabilirsiniz.

1. Bir komut istemi açın ve AdlCopy 'in yüklü olduğu dizine gidin, genellikle `%HOMEPATH%\Documents\adlcopy`.
1. Belirli bir dosyayı bir Data Lake Storage 1. hesabından diğerine kopyalamak için aşağıdaki komutu çalıştırın.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Örneğin:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Yukarıdaki sözdizimi, hedef Data Lake Storage 1. hesabındaki bir klasöre kopyalanacak dosyayı belirtir. AdlCopy Aracı, belirtilen klasör adı yoksa bir klasör oluşturur.
   >
   >

    Data Lake Storage 1. hesabınıza sahip olduğunuz Azure aboneliğinin kimlik bilgilerini girmeniz istenir. Aşağıdakine benzer bir çıktı görürsünüz:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
1. Aşağıdaki komut, kaynak Data Lake Storage 1. hesabındaki belirli bir klasördeki tüm dosyaları hedef Data Lake Storage 1. hesabındaki bir klasöre kopyalar.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Bağımsız bir araç olarak AdlCopy kullanılırken, kopya, paylaşılan, Azure tarafından yönetilen kaynaklarda çalıştırılır. Bu ortamda alabileceğiniz performans, sistem yüküne ve kullanılabilir kaynaklara bağlıdır. Bu mod, bir geçici olarak küçük aktarımlar için en iyi şekilde kullanılır. AdlCopy bağımsız bir araç olarak kullanılırken hiçbir parametrenin ayarlanmış olması gerekmez.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Veri kopyalamak için AdlCopy (Data Lake Analytics hesabıyla) kullanın

Azure depolama Bloblarındaki verileri Data Lake Storage 1. kopyalamak için AdlCopy işini çalıştırmak üzere Data Lake Analytics hesabınızı da kullanabilirsiniz. Bu seçeneği genellikle, taşınacak veriler gigabayt ve terabayt aralığında olduğunda ve daha iyi ve öngörülebilir performans üretimi istediğinizde kullanırsınız.

Bir Azure Depolama Blobu kopyalamak için AdlCopy ile Data Lake Analytics hesabınızı kullanmak için, kaynağın (Azure Depolama Blobu) Data Lake Analytics hesabınız için bir veri kaynağı olarak eklenmesi gerekir. Data Lake Analytics hesabınıza ek veri kaynakları ekleme hakkında yönergeler için bkz. [Data Lake Analytics hesabı veri kaynaklarını yönetme](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Bir Azure Data Lake Storage 1. hesabından kaynak olarak Data Lake Analytics hesabı kullanarak kopyalıyorsanız Data Lake Storage 1. hesabını Data Lake Analytics hesabıyla ilişkilendirmeniz gerekmez. Kaynak depoyu Data Lake Analytics hesabıyla ilişkilendirme gereksinimi yalnızca kaynak bir Azure depolama hesabı olduğunda geçerlidir.
>
>

Data Lake Analytics hesabını kullanarak bir Azure Storage blobundan Data Lake Storage 1. hesabına kopyalamak için aşağıdaki komutu çalıştırın:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Örneğin:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Benzer şekilde, kaynak Data Lake Storage 1. hesabındaki belirli bir klasördeki tüm dosyaları Data Lake Analytics hesabı kullanarak hedef Data Lake Storage 1. hesabındaki bir klasöre kopyalamak için aşağıdaki komutu çalıştırın:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Terabaytlarca veri kopyalarken, kendi Azure Data Lake Analytics hesabınızla AdlCopy kullanmak daha iyi ve öngörülebilir performans sağlar. Ayarlanmalıdır parametresi, kopyalama işi için kullanılacak Azure Data Lake Analytics birimi sayısıdır. Birim sayısını artırmak kopyalama işinizin performansını arttırır. Kopyalanacak her dosya, en fazla bir birim kullanabilir. Kopyalanan dosya sayısından daha fazla birim belirtilmesi, performansı artırmaz.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Model eşleştirmeyi kullanarak veri kopyalamak için AdlCopy kullanma

Bu bölümde, bir kaynaktan veri kopyalamak için AdlCopy 'in nasıl kullanılacağını öğrenirsiniz (aşağıdaki örneğimizde Azure Depolama Blobu), bir hedef Data Lake Storage 1. hesabına, model eşleştirmeyi kullanarak. Örneğin, kaynak blobundan hedefine. csv uzantılı tüm dosyaları kopyalamak için aşağıdaki adımları kullanabilirsiniz.

1. Bir komut istemi açın ve AdlCopy 'in yüklü olduğu dizine gidin, genellikle `%HOMEPATH%\Documents\adlcopy`.
1. Belirli bir Blobun *. csv uzantısına sahip tüm dosyaları kaynak kapsayıcısından Data Lake Storage 1. klasöre kopyalamak için aşağıdaki komutu çalıştırın:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Örneğin:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Faturalandırma

* AdlCopy aracını tek başına olarak kullanırsanız, kaynak Azure depolama hesabı Data Lake Storage 1. hesabıyla aynı bölgede değilse, veri taşıma için çıkış maliyetleri için faturalandırılırsınız.
* AdlCopy aracını Data Lake Analytics hesabınızla birlikte kullanıyorsanız standart [Data Lake Analytics faturalandırma ücretleri](https://azure.microsoft.com/pricing/details/data-lake-analytics/) uygulanır.

## <a name="considerations-for-using-adlcopy"></a>AdlCopy kullanma konuları

* AdlCopy (sürüm 1.0.5 için), binlerce dosya ve klasöre topluca sahip olan kaynaklardaki verilerin kopyalanmasını destekler. Bununla birlikte, büyük bir veri kümesini kopyalarken sorunlarla karşılaşırsanız, dosyaları/klasörleri farklı alt klasörlere dağıtabilir ve bunun yerine bu alt klasörlerin yolunu kaynak olarak kullanabilirsiniz.

## <a name="performance-considerations-for-using-adlcopy"></a>AdlCopy kullanımı için performans konuları

AdlCopy, binlerce dosya ve klasör içeren verilerin kopyalanmasını destekler. Ancak, büyük bir veri kümesini kopyalarken sorunlarla karşılaşırsanız, dosyaları/klasörleri daha küçük alt klasörlere dağıtabilirsiniz. AdlCopy, geçici kopyalar için oluşturulmuştur. Verileri yinelenen bir şekilde kopyalamaya çalışıyorsanız, kopyalama işlemleri etrafında tam yönetim sağlayan [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) kullanmayı göz önünde bulundurmanız gerekir.

## <a name="release-notes"></a>Sürüm notları

* 1.0.13-birden çok AdlCopy komutları arasında aynı Azure Data Lake Storage 1. hesabına veri kopyalıyorsanız, her çalıştırma için kimlik bilgilerinizi yeniden girmeniz gerekmez. AdlCopy artık bu bilgileri birden çok çalıştırma arasında önbelleğe alacak.

## <a name="next-steps"></a>Sonraki adımlar

* [Data Lake Storage Gen1'de verilerin güvenliğini sağlama](data-lake-store-secure-data.md)
* [Data Lake Storage 1. ile Azure Data Lake Analytics kullanma](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight 'ı Data Lake Storage 1. ile kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)
