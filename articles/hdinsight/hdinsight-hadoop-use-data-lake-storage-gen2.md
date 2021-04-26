---
title: Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma
description: Azure Data Lake Storage 2. Azure HDInsight kümeleri ile nasıl kullanacağınızı öğrenin.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: ea9dc2627d5a6498f69ca8c61a9cac8089816886
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378593"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight kümeleriyle Azure Data Lake Storage 2. Nesil hizmetini kullanma

[Azure Data Lake Storage 2.](../storage/blobs/data-lake-storage-introduction.md) , [Azure Blob depolama](../storage/blobs/storage-blobs-introduction.md)üzerinde oluşturulmuş, büyük veri analizi için adanmış bir bulut depolama hizmetidir. Data Lake Storage 2., Azure Blob depolama ve Azure Data Lake Storage 1. yeteneklerini birleştirir. Elde edilen hizmet, Azure Data Lake Storage 1.: dosya sistemi semantiği, Dizin düzeyi ve dosya düzeyi güvenlik ve uyumluluk gibi özellikler sunar. Azure Blob depolama 'dan düşük maliyetli, katmanlı depolama, yüksek kullanılabilirlik ve olağanüstü durum kurtarma özellikleri ile birlikte.

Data Lake Storage 2. kullanarak küme oluşturma seçeneklerinin tam karşılaştırması için bkz. [Azure HDInsight kümeleri ile kullanım için depolama seçeneklerini karşılaştırma](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage 2. kullanılabilirliği

Data Lake Storage 2., neredeyse tüm Azure HDInsight küme türleri için hem varsayılan hem de ek depolama hesabı olarak bir depolama seçeneği olarak kullanılabilir. Ancak, HBase Data Lake Storage 2. yalnızca bir hesaba sahip olabilir.

> [!Note]  
> **Birincil depolama türü** olarak Data Lake Storage 2. seçeneğini belirledikten sonra, ek depolama alanı olarak bir Data Lake Storage 1. seçemezsiniz.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Data Lake Storage 2. kullanarak HDInsight kümeleri oluşturma

Data Lake Storage 2. erişimi olan HDInsight kümeleri oluşturma hakkında ayrıntılı yönergeler için aşağıdaki bağlantıları kullanın.

* [Portalı kullanma](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Azure CLI’yı kullanma](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell, Azure Data Lake Storage 2. ile HDInsight kümesi oluşturmak için şu anda desteklenmiyor.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 'ta Data Lake Storage 2. için erişim denetimi

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage 2. ne tür izinler desteklenir?

Data Lake Storage 2. hem Azure rol tabanlı erişim denetimi (Azure RBAC) hem de POSIX benzeri erişim denetim listelerini (ACL 'Ler) destekleyen bir erişim denetimi modeli kullanır. Data Lake Storage 1. yalnızca verilere erişimi denetlemek için erişim denetim listelerini destekler.

Azure RBAC, Azure kaynakları için kullanıcılara, gruplara ve hizmet sorumlularına izin kümelerini etkili bir şekilde uygulamak için rol atamaları kullanır. Genellikle, bu Azure kaynakları en üst düzey kaynaklarla (örneğin, Azure Blob depolama hesapları) kısıtlanır. Azure Blob depolama ve ayrıca Data Lake Storage 2., bu mekanizma dosya sistemi kaynağına genişletilmiştir.

Azure RBAC ile dosya izinleri hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

ACL 'lerle ilgili dosya izinleri hakkında daha fazla bilgi için bkz. [dosya ve dizinlerdeki erişim denetim listeleri](../storage/blobs/data-lake-storage-access-control.md).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Data Lake Storage 2. veri Nasıl yaparım? erişimi denetimi mi?

HDInsight kümenizin Data Lake Storage 2. içindeki dosyalara erişme özelliği, Yönetilen kimlikler aracılığıyla denetlenir. Yönetilen kimlik, kimlik bilgileri Azure tarafından yönetilen Azure Active Directory (Azure AD) ' de kayıtlı bir kimliktir. Yönetilen kimliklerle, hizmet sorumlularını Azure AD 'ye kaydetmeniz gerekmez. Ya da sertifikalar gibi kimlik bilgilerini saklayın.

Azure hizmetlerinin iki tür yönetilen kimliği vardır: sistem tarafından atanan ve Kullanıcı tarafından atanan. HDInsight Data Lake Storage 2. erişmek için Kullanıcı tarafından atanan Yönetilen kimlikler kullanır. `user-assigned managed identity`Tek başına bir Azure kaynağı olarak oluşturulur. Bir oluşturma işlemi çerçevesinde, Azure kullanılan abonelik tarafından güvenilen Azure AD kiracısında bir kimlik oluşturur. Kimlik oluşturulduktan sonra, bir veya birden çok Azure hizmet örneğine atanabilir.

Kullanıcı tarafından atanan kimliğin yaşam döngüsü, bu kimliğin atandığı Azure hizmet örneklerinin yaşam döngüsünden ayrı olarak yönetilir. Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nelerdir?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Nasıl yaparım?, Azure AD kullanıcılarının Hive veya diğer hizmetleri kullanarak Data Lake Storage 2. verileri sorgulaması için izinleri ayarlamak ister misiniz?

Kullanıcıların verileri sorgulamasına yönelik izinleri ayarlamak için, ACL 'lerdeki atanan sorumlu olarak Azure AD güvenlik gruplarını kullanın. Dosya erişim izinlerini bireysel kullanıcılara veya hizmet sorumlularına doğrudan atamayın. İzin akışını denetlemek için Azure AD güvenlik grupları ile, ACL 'Leri tüm dizin yapısına yeniden ayarlamadan kullanıcıları veya hizmet sorumlularını ekleyebilir ve kaldırabilirsiniz. Kullanıcıları yalnızca uygun Azure AD güvenlik grubundan eklemeniz veya kaldırmanız yeterlidir. ACL 'ler devralınmaz, bu nedenle ACL 'Lerin yeniden uygulanması her dosya ve alt dizinde ACL 'nin güncelleştirilmesini gerektirir.

## <a name="access-files-from-the-cluster"></a>Kümeden dosyalara erişme

HDInsight kümesinden Data Lake Storage 2. dosyalara erişmek için çeşitli yollar vardır.

* **Tam adı kullanarak**. Bu yöntemle, erişmek istediğiniz dosyanın tam yolunu girersiniz.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Kısaltılmış yol biçimi kullanarak**. Bu yaklaşımda, yolu küme köküne kadar değiştirirsiniz:

    ```
    abfs:///<file.path>/
    ```

* **Göreli yolu kullanarak**. Bu yöntemle, erişmek istediğiniz dosyanın yalnızca göreli yolunu girersiniz.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Veri erişimi örnekleri

Örnekler, kümenin baş düğümüne bir [ssh bağlantısını](./hdinsight-hadoop-linux-use-ssh-unix.md) temel alır. Örneklerde, üç URI şeması da kullanılır. `CONTAINERNAME`Ve `STORAGEACCOUNT` değerlerini ilgili değerlerle değiştirin

#### <a name="a-few-hdfs-commands"></a>Birkaç bir bu komut

1. Yerel depolamada bir dosya oluşturun.

    ```bash
    touch testFile.txt
    ```

1. Küme depolamada dizinler oluşturun.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Yerel depolamadan küme depolamaya veri kopyalama.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Küme depolamada dizin içeriğini listeleyin.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Hive tablosu oluşturma

Üç dosya konumu tanım amacıyla gösterilmiştir. Gerçek yürütme için girdilerden yalnızca birini kullanın `LOCATION` .

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Sonraki adımlar

* [Data Lake Storage 2. Preview ile Azure HDInsight tümleştirmesi-ACL ve güvenlik güncelleştirmesi](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Azure Data Lake Storage 2. Nesil'e giriş](../storage/blobs/data-lake-storage-introduction.md)
* [Öğretici: Azure HDInsight 'ta etkileşimli sorgu kullanarak verileri ayıklama, dönüştürme ve yükleme](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
