---
title: Azure HDInsight kümeleri ile Azure Data Lake Storage 2. kullanma
description: Azure Data Lake Storage 2. Azure HDInsight kümeleri ile nasıl kullanacağınızı öğrenin.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89b86124d6da0d0d659ed0673585eadbf1008aa3
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847306"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Azure HDInsight kümeleri ile Azure Data Lake Storage 2. kullanma

Azure Data Lake Storage 2., Azure Blob depolama üzerinde oluşturulmuş, büyük veri analizi için adanmış bir bulut depolama hizmetidir. Data Lake Storage 2., Azure Blob depolama ve Azure Data Lake Storage 1. yeteneklerini birleştirir. Elde edilen hizmet; dosya sistemi semantiği, Dizin düzeyi ve dosya düzeyi güvenliği gibi Azure Data Lake Storage 1. Özellikler ve düşük maliyetli, katmanlı depolama, yüksek kullanılabilirlik ve olağanüstü durum kurtarma özellikleriyle birlikte ölçeklenebilirlik sağlar Azure Blob depolama alanından.

## <a name="data-lake-storage-gen2-availability"></a>Data Lake Storage 2. kullanılabilirliği

Data Lake Storage 2., neredeyse tüm Azure HDInsight küme türleri için hem varsayılan hem de ek depolama hesabı olarak bir depolama seçeneği olarak kullanılabilir. Ancak, HBase yalnızca bir Data Lake Storage 2. hesabına sahip olabilir.

Data Lake Storage 2. kullanarak küme oluşturma seçeneklerinin tam karşılaştırması için bkz. [Azure HDInsight kümeleri ile kullanım için depolama seçeneklerini karşılaştırma](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> **Birincil depolama türü**olarak Data Lake Storage 2. seçeneğini belirledikten sonra, ek depolama alanı olarak bir Data Lake Storage 1. hesabı seçemezsiniz.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Azure portal üzerinden Data Lake Storage 2. bir küme oluşturma

Depolama için Data Lake Storage 2. kullanan bir HDInsight kümesi oluşturmak için, Data Lake Storage 2. hesabı yapılandırmak için aşağıdaki adımları izleyin.

### <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

Henüz bir tane yoksa, Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol üst köşedeki **kaynak oluştur ' a**tıklayın.
1. Arama kutusuna **Kullanıcı atandı** yazın ve **Kullanıcı tarafından atanan yönetilen kimlik**' e tıklayın.
1. **Oluştur**'a tıklayın.
1. Yönetilen Kimliğiniz için bir ad girin, doğru aboneliği, kaynak grubunu ve konumu seçin.
1. **Oluştur**'a tıklayın.

Yönetilen kimliklerin Azure HDInsight 'ta nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure HDInsight 'Ta Yönetilen kimlikler](hdinsight-managed-identities.md).

![Kullanıcı tarafından atanan yönetilen kimlik oluşturma](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Data Lake Storage 2. hesabı oluşturma

Azure Data Lake Storage 2. depolama hesabı oluşturun.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol üst köşedeki **kaynak oluştur ' a**tıklayın.
1. Arama kutusuna **depolama** yazın ve **depolama hesabı**' na tıklayın.
1. **Oluştur**'a tıklayın.
1. **Depolama hesabı oluştur** ekranında:
    1. Doğru aboneliği ve kaynak grubunu seçin.
    1. Data Lake Storage 2. hesabınız için bir ad girin. Depolama hesabı adlandırma kuralları hakkında daha fazla bilgi için bkz. [Azure kaynakları Için adlandırma kuralları](/azure/architecture/best-practices/resource-naming#storage).
    1. **Gelişmiş** sekmesine tıklayın.
    1. **Data Lake Storage 2.** altındaki **hiyerarşik ad alanı** ' nın yanında **etkin** ' e tıklayın.
    1. **Gözden geçir ve oluştur**’a tıklayın.
    1. **Oluştur**'a tıklayın

Depolama hesabı oluşturma sırasında diğer seçenekler hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Azure Data Lake Storage 2. depolama hesabı oluşturma](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Azure portal depolama hesabı oluşturmayı gösteren ekran görüntüsü](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Data Lake Storage 2. hesabındaki yönetilen kimlik için izinleri ayarlayın

Yönetilen kimliği depolama hesabındaki **Depolama Blobu veri sahibi** rolüne atayın.

1. [Azure Portal](https://portal.azure.com)depolama hesabınıza gidin.
1. Depolama hesabınızı seçin ve ardından **erişim denetimi (IAM)** öğesini seçerek hesabın erişim denetimi ayarlarını görüntüleyin. Rol atamalarının listesini görmek için **rol atamaları** sekmesini seçin.

    ![Depolama erişim denetimi ayarlarını gösteren ekran görüntüsü](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Yeni bir rol eklemek için **+ rol ataması Ekle** düğmesini seçin.
1. **Rol ataması Ekle** penceresinde, **Depolama Blobu veri sahibi** rolünü seçin. Ardından, yönetilen kimliğe ve depolama hesabına sahip aboneliği seçin. Ardından, daha önce oluşturduğunuz Kullanıcı tarafından atanan yönetilen kimliği bulmak için arama yapın. Son olarak, yönetilen kimliği seçin ve **Seçilen Üyeler**altında listelenecektir.

    ![RBAC rolü atamayı gösteren ekran görüntüsü](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. **Kaydet**’i seçin. Seçtiğiniz kullanıcı tarafından atanan kimlik artık seçili rol altında listelenmiştir.
1. Bu ilk kurulum tamamlandıktan sonra Portal aracılığıyla bir küme oluşturabilirsiniz. Küme, depolama hesabıyla aynı Azure bölgesinde olmalıdır. Küme oluşturma menüsünün **depolama** bölümünde aşağıdaki seçenekleri belirleyin:

    * **Birincil depolama türü**için **Azure Data Lake Storage 2.** ' yi seçin.
    * **Bir depolama hesabı seçin**altında yeni oluşturulan Data Lake Storage 2. Depolama hesabını arayıp seçin.

        ![Azure HDInsight ile Data Lake Storage 2. kullanmaya yönelik depolama ayarları](./media/hdinsight-hadoop-use-data-lake-storage-gen2/primary-storage-type-adls-gen2.png)

    * **Kimlik**altında, doğru aboneliği ve yeni oluşturulan kullanıcı tarafından atanan yönetilen kimliği seçin.

        ![HDInsight ile Data Lake Storage 2. kullanmaya yönelik kimlik ayarları](./media/hdinsight-hadoop-use-data-lake-storage-gen2/managed-identity-cluster-creation.png)

> [!Note]
> Depolama hesabı düzeyinde bir ikincil Data Lake Storage 2. hesabı eklemek için, daha önce oluşturulan yönetilen kimliği, eklemek istediğiniz yeni Data Lake Storage 2. depolama hesabına atamanız yeterlidir. HDInsight üzerinde "ek depolama hesapları" dikey penceresi aracılığıyla bir ikincil Data Lake Storage 2. hesabı eklemenin desteklenmesinin tavsiye edilir.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Azure CLı aracılığıyla Data Lake Storage 2. bir küme oluşturma

Örnek bir [şablon dosyası indirebilir](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) ve [örnek bir parametre dosyası indirebilirsiniz](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Aşağıdaki şablon ve Azure CLı kod parçacığını kullanmadan önce aşağıdaki yer tutucuları doğru değerlerle değiştirin:

| Yer tutucu | Açıklama |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure aboneliğinizin KIMLIĞI |
| `<RESOURCEGROUPNAME>` | Yeni küme ve depolama hesabının oluşturulmasını istediğiniz kaynak grubu. |
| `<MANAGEDIDENTITYNAME>` | Azure Data Lake Storage 2. hesabınızda izin verilecek yönetilen kimliğin adı. |
| `<STORAGEACCOUNTNAME>` | Oluşturulacak yeni Azure Data Lake Storage 2. hesabı. |
| `<CLUSTERNAME>` | HDInsight Kümenizin adı. |
| `<PASSWORD>` | İmza panosu ve SSH kullanarak kümede oturum açmak için seçtiğiniz parola. |

Aşağıdaki kod parçacığı aşağıdaki ilk adımları yapar:

1. Azure hesabınızda oturum açar.
1. Oluşturma işlemlerinin yapılacağı etkin aboneliği ayarlar.
1. Yeni dağıtım etkinlikleri için yeni bir kaynak grubu oluşturur.
1. Kullanıcı tarafından atanan yönetilen kimlik oluşturur.
1. Data Lake Storage 2. özelliklerini kullanmak için Azure CLı 'ya bir uzantı ekler.
1. `--hierarchical-namespace true` bayrağını kullanarak yeni bir Data Lake Storage 2. hesabı oluşturur.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Sonra portalda oturum açın. Yeni Kullanıcı tarafından atanan yönetilen kimliği, [Azure Portal kullanma](hdinsight-hadoop-use-data-lake-storage-gen2.md)bölümündeki adım 3 ' te anlatıldığı gibi depolama hesabındaki **Depolama Blobu veri katılımcısı** rolüne ekleyin.

Kullanıcı tarafından atanan yönetilen kimlik için rol atadıktan sonra, aşağıdaki kod parçacığını kullanarak şablonu dağıtın.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>HDInsight 'ta Data Lake Storage 2. için erişim denetimi

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Data Lake Storage 2. ne tür izinler desteklenir?

Data Lake Storage 2. hem rol tabanlı erişim denetimi (RBAC) hem de POSIX benzeri erişim denetim listelerini (ACL 'Ler) destekleyen bir erişim denetimi modeli kullanır. Data Lake Storage 1. yalnızca verilere erişimi denetlemek için erişim denetim listelerini destekler.

RBAC, Azure kaynakları için kullanıcılara, gruplara ve hizmet sorumlularına izin kümelerini etkili bir şekilde uygulamak için rol atamaları kullanır. Genellikle, bu Azure kaynakları en üst düzey kaynaklarla (örneğin, Azure depolama hesapları) kısıtlanır. Azure depolama ve ayrıca Data Lake Storage 2., bu mekanizma dosya sistemi kaynağına genişletilmiştir.

 RBAC ile dosya izinleri hakkında daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

ACL 'lerle ilgili dosya izinleri hakkında daha fazla bilgi için bkz. [dosya ve dizinlerdeki erişim denetim listeleri](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Data Lake Storage 2. veri Nasıl yaparım? erişimi denetimi mi?

HDInsight kümenizin Data Lake Storage 2. içindeki dosyalara erişme özelliği, Yönetilen kimlikler aracılığıyla denetlenir. Yönetilen kimlik, kimlik bilgileri Azure tarafından yönetilen Azure Active Directory (Azure AD) ' de kayıtlı bir kimliktir. Yönetilen kimliklerle, hizmet sorumlularını Azure AD 'ye kaydetmeniz veya sertifikalar gibi kimlik bilgilerini korumanız gerekmez.

Azure hizmetlerinin iki tür yönetilen kimliği vardır: sistem tarafından atanan ve Kullanıcı tarafından atanan. HDInsight Data Lake Storage 2. erişmek için Kullanıcı tarafından atanan Yönetilen kimlikler kullanır. Kullanıcı tarafından atanan yönetilen kimlik, tek başına bir Azure kaynağı olarak oluşturulur. Bir oluşturma işlemi çerçevesinde, Azure kullanılan abonelik tarafından güvenilen Azure AD kiracısında bir kimlik oluşturur. Kimlik oluşturulduktan sonra, bir veya birden çok Azure hizmet örneğine atanabilir.

Kullanıcı tarafından atanan kimliğin yaşam döngüsü, bu kimliğin atandığı Azure hizmet örneklerinin yaşam döngüsünden ayrı olarak yönetilir. Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları için Yönetilen kimlikler nasıl çalışır?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Nasıl yaparım?, Azure AD kullanıcılarının Hive veya diğer hizmetleri kullanarak Data Lake Storage 2. verileri sorgulaması için izinleri ayarlamak ister misiniz?

Kullanıcıların verileri sorgulamasına yönelik izinleri ayarlamak için, ACL 'lerdeki atanan sorumlu olarak Azure AD güvenlik gruplarını kullanın. Dosya erişim izinlerini bireysel kullanıcılara veya hizmet sorumlularına doğrudan atamayın. İzin akışını denetlemek için Azure AD güvenlik grupları 'nı kullandığınızda, ACL 'Leri tüm dizin yapısına yeniden ayarlamadan kullanıcıları veya hizmet sorumlularını ekleyebilir ve kaldırabilirsiniz. Kullanıcıları yalnızca uygun Azure AD güvenlik grubundan eklemeniz veya kaldırmanız yeterlidir. ACL 'ler devralınmaz, bu nedenle ACL 'Lerin yeniden uygulanması her dosya ve alt dizinde ACL 'nin güncelleştirilmesini gerektirir.

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

Örnekler, kümenin baş düğümüne bir [ssh bağlantısını](./hdinsight-hadoop-linux-use-ssh-unix.md) temel alır. Örneklerde, üç URI şeması da kullanılır. `CONTAINERNAME` ve `STORAGEACCOUNT` ilgili değerlerle değiştirin

#### <a name="a-few-hdfs-commands"></a>Birkaç bir bu komut

1. Yerel depolamada basit bir dosya oluşturun.

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

Üç dosya konumu tanım amacıyla gösterilmiştir. Gerçek yürütme için `LOCATION` girdilerden yalnızca birini kullanın.

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
* [Azure Data Lake Storage 2. giriş](../storage/blobs/data-lake-storage-introduction.md)
* [Öğretici: Azure HDInsight 'ta etkileşimli sorgu kullanarak verileri ayıklama, dönüştürme ve yükleme](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
