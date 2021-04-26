---
title: Şablon-Data Lake Storage 1. ile HDInsight kümesi
description: Azure Resource Manager şablonları kullanarak Azure HDInsight kümelerini Azure Data Lake Storage 1. ile oluşturun ve kullanın.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: a7283ad4c4c61ecc293a55ffc4cb9626bb28d630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108737"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak Azure Data Lake Storage 1. bir HDInsight kümesi oluşturma
> [!div class="op_single_selector"]
> * [Portalı kullanma](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell 'i kullanma (varsayılan depolama için)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell 'i kullanma (ek depolama için)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Kaynak Yöneticisi kullanma](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

**Ek depolama alanı olarak** Azure Data Lake Storage 1. Ile bir HDInsight kümesini yapılandırmak için Azure PowerShell nasıl kullanacağınızı öğrenin.

Desteklenen küme türleri için Data Lake Storage 1. varsayılan depolama alanı veya ek depolama hesabı olarak kullanılabilir. Data Lake Storage 1. ek depolama alanı olarak kullanıldığında, kümeler için varsayılan depolama hesabı hala Azure Blob depolama (işb) olur ve kümeyle ilgili dosyalar (örneğin Günlükler vb.) varsayılan depolama alanına yazılır, ancak işlemek istediğiniz veriler bir Data Lake Storage 1. hesabında depolanabilir. Ek depolama hesabı olarak Data Lake Storage 1. kullanmak, performansı veya kümeden depolama alanını okuma/yazma özelliğini etkilemez.

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>HDInsight küme depolaması için Data Lake Storage 1. kullanma

HDInsight 'ı Data Lake Storage 1. ile kullanmayla ilgili bazı önemli noktalar şunlardır:

* HDInsight sürüm 3,5 ve 3,6 ' de varsayılan depolama alanı kullanılabilir olarak Data Lake Storage 1. erişimi olan HDInsight kümeleri oluşturma seçeneği.

* HDInsight sürümleri 3,2, 3,4, 3,5 ve 3,6 sürümleri için ek depolama alanı olarak Data Lake Storage 1. erişimi olan HDInsight kümeleri oluşturma seçeneği.

Bu makalede, ek depolama alanı olarak Data Lake Storage 1. bir Hadoop kümesi sunuyoruz. Data Lake Storage 1. varsayılan depolama alanı olarak bir Hadoop kümesi oluşturma hakkında yönergeler için, bkz. [Azure Portal kullanarak Data Lake Storage 1. bir HDInsight kümesi oluşturma](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticiye başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü edinme](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 veya üstü**. Bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/).
* **Hizmet sorumlusu Azure Active Directory**. Bu öğreticideki adımlarda, Azure AD 'de hizmet sorumlusu oluşturma hakkında yönergeler sağlanmaktadır. Ancak, bir hizmet sorumlusu oluşturabilmeniz için bir Azure AD yöneticisi olmanız gerekir. Bir Azure AD yöneticisiyseniz, bu önkoşulu atlayıp öğreticiye devam edebilirsiniz.

    **Bir Azure AD yöneticisi** değilseniz, hizmet sorumlusu oluşturmak için gereken adımları gerçekleştiremezsiniz. Böyle bir durumda, Data Lake Storage 1. bir HDInsight kümesi oluşturabilmeniz için önce Azure AD yöneticinizin bir hizmet sorumlusu oluşturması gerekir. Ayrıca hizmet sorumlusu, [sertifikayla hizmet sorumlusu oluşturma](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)bölümünde açıklandığı gibi bir sertifika kullanılarak oluşturulmalıdır.

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>Data Lake Storage 1. bir HDInsight kümesi oluşturma
Kaynak Yöneticisi şablonu ve şablonu kullanma önkoşulları, [yeni Data Lake Storage 1. sahip bir HDInsight Linux kümesini dağıtırken](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)GitHub 'da kullanılabilir. Ek depolama alanı olarak Data Lake Storage 1. bir HDInsight kümesi oluşturmak için bu bağlantıda belirtilen yönergeleri izleyin.

Yukarıda bahsedilen bağlantıdaki yönergeler PowerShell gerektirir. Bu yönergelerle başlamadan önce Azure hesabınızda oturum açıp başlattığınızdan emin olun. Masaüstünüzde yeni bir Azure PowerShell penceresi açın ve aşağıdaki kod parçacıklarını girin. Oturum açmanız istendiğinde, abonelik yöneticileri/sahibinden biri olarak oturum çalıştırdığınızdan emin olun:

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

Şablon şu kaynak türlerini dağıtır:

* [Microsoft. DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. HDInsight/kümeler](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>Örnek verileri Data Lake Storage 1. karşıya yükleme
Kaynak Yöneticisi şablonu, Data Lake Storage 1. yeni bir depolama hesabı oluşturur ve bunu HDInsight kümesiyle ilişkilendirir. Artık Data Lake Storage 1. örnek verileri karşıya yüklemeniz gerekir. Data Lake Storage 1. ile depolama hesabındaki verilere erişen bir HDInsight kümesinden işleri çalıştırmak için bu verilere öğreticide daha sonra ihtiyacınız olacak. Verileri karşıya yükleme yönergeleri için bkz. [Data Lake Storage 1. bir dosyayı karşıya yükleme](data-lake-store-get-started-portal.md#uploaddata). Karşıya yüklenecek örnek veri arıyorsanız [Azure Data Lake Git Deposu](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)'ndan **Ambulance Data** klasörünü alabilirsiniz.

## <a name="set-relevant-acls-on-the-sample-data"></a>Örnek verilerde ilgili ACL 'Leri ayarlama
Karşıya yüklediğiniz örnek verilerin HDInsight kümesinden erişilebilir olduğundan emin olmak için HDInsight kümesi ve Data Lake Storage 1. arasında kimlik oluşturmak için kullanılan Azure AD uygulamasının erişmeye çalıştığınız dosya/klasöre erişimi olduğundan emin olmanız gerekir. Bunu yapmak için aşağıdaki adımları uygulayın.

1. HDInsight kümesiyle ilişkili Azure AD uygulamasının adını ve Data Lake Storage 1. olan depolama hesabını bulun. Adı aramanız için bir yol, Kaynak Yöneticisi şablonunu kullanarak oluşturduğunuz HDInsight kümesi dikey penceresini açmak, **küme Azure AD kimlik** sekmesine tıklamanız ve **hizmet sorumlusu görünen adının** değerini arayacaktır.
2. Şimdi, HDInsight kümesinden erişmek istediğiniz dosya/klasör üzerinde bu Azure AD uygulamasına erişim sağlayın. Data Lake Storage 1. dosya/klasör üzerinde doğru ACL 'Leri ayarlamak için, bkz. [Data Lake Storage 1. verileri güvenli hale getirme](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>Data Lake Storage 1. kullanmak için HDInsight kümesinde test işleri çalıştırın
Bir HDInsight kümesini yapılandırdıktan sonra, HDInsight kümesinin Data Lake Storage 1. erişebileceğini sınamak için test işlerini kümede çalıştırabilirsiniz. Bunu yapmak için, daha önce Data Lake Storage 1. ile depolama hesabınıza yüklediğiniz örnek verileri kullanarak bir tablo oluşturan örnek bir Hive işi çalıştıracağız.

Bu bölümde, bir HDInsight Linux kümesine SSH oluşturup örnek Hive sorgusunu çalıştırırsınız. Bir Windows istemcisi kullanıyorsanız, ' den indirilebilen **Putty** kullanmanız önerilir [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

PuTTY kullanma hakkında daha fazla bilgi için bkz. [Windows 'Da HDInsight 'Ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Bağlandıktan sonra, aşağıdaki komutu kullanarak Hive CLı 'yı başlatın:

   ```
   hive
   ```
2. CLı kullanarak, Data Lake Storage 1. ' deki örnek verileri kullanarak **Araçlar** adlı yeni bir tablo oluşturmak için aşağıdaki deyimlerini girin:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Aşağıdakine benzer bir çıktı görmeniz gerekir:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>Data Lake Storage 1., bu komutları kullanarak erişim
HDInsight kümesini Data Lake Storage 1. kullanacak şekilde yapılandırdıktan sonra, mağazaya erişmek için,

Bu bölümde, bir HDInsight Linux kümesine SSH oluşturup, bu komutu çalıştırın. Bir Windows istemcisi kullanıyorsanız, ' den indirilebilen **Putty** kullanmanız önerilir [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) .

PuTTY kullanma hakkında daha fazla bilgi için bkz. [Windows 'Da HDInsight 'Ta Linux tabanlı Hadoop Ile SSH kullanma](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

Bağlandıktan sonra, Data Lake Storage 1. depolama hesabındaki dosyaları listelemek için aşağıdaki bir bu dosya sistemi komutunu kullanın.

```
hdfs dfs -ls adl://<storage account with Data Lake Storage Gen1 name>.azuredatalakestore.net:443/
```

Bu, daha önce karşıya yüklediğiniz dosyayı Data Lake Storage 1. olarak listelemelidir.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

Ayrıca, `hdfs dfs -put` Data Lake Storage 1. ' ye bazı dosyaları yüklemek için komutunu kullanabilir ve ardından `hdfs dfs -ls` dosyaların başarıyla karşıya yüklenip yüklenmediğini doğrulamak için kullanabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar
* [Azure depolama Bloblarından veri kopyalama Data Lake Storage 1.](data-lake-store-copy-data-wasb-distcp.md)
* [Azure HDInsight kümeleri ile Data Lake Storage 1. kullanma](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)