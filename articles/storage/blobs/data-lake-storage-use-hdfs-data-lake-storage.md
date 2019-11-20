---
title: Azure Data Lake Storage 2. ile
description: Data Lake Storage 2. için
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 1d5313f3f0fff128dd09f9c9857b7dd9921ea4f8
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992212"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Data Lake Storage 2. ile

Bir [Hadoop Dağıtılmış dosya sistemi (bir)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)ile yaptığınız gibi bir komut satırı arabirimi kullanarak Depolama hesabınızdaki verilere erişebilir ve bunları yönetebilirsiniz. Bu makalede, başlamanıza yardımcı olacak bazı örnekler sağlanmaktadır.

HDInsight, işlem düğümlerine yerel olarak bağlı olan dağıtılmış kapsayıcıya erişim sağlar. Bu kapsayıcıya, Hadoop 'un desteklediği diğer dosya sistemleriyle doğrudan etkileşim kuran kabuğu kullanarak erişebilirsiniz.

GUCLı hakkında daha fazla bilgi için bkz. [resmi belgeler](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) ve bu Ayrıntılar [Kılavuzu](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

>[!NOTE]
>HDInsight yerine Azure Databricks kullanıyorsanız ve bir komut satırı arabirimi kullanarak verilerinizle etkileşim kurmak istiyorsanız, databricks CLı dosya sistemiyle etkileşim kurmak için Databricks CLı 'yi kullanabilirsiniz. Bkz. [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Linux 'ta bir HDInsight Hadoop kümesi ile bir HDInsight Hadoop kümesi kullanma

İlk olarak, [hizmetlere uzaktan erişim](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services)oluşturun. [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 'yi seçerseniz örnek PowerShell kodu aşağıdaki gibi görünür:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Bağlantı dizesi, Azure portal HDInsight kümesi dikey penceresinin "SSH + Cluster LOGIN" bölümünde bulunabilir. Küme oluşturma sırasında SSH kimlik bilgileri belirtildi.

>[!IMPORTANT]
>HDInsight kümesi faturalandırma bir küme oluşturulduktan sonra başlar ve küme silindiğinde duraklar. Fatura dakikalara eşit olarak dağıtıldığından, kullanılmayan kümelerinizi mutlaka silmelisiniz. Bir kümeyi silmeyi öğrenmek için [konusundaki makalemize](../../hdinsight/hdinsight-delete-cluster.md)bakın. Ancak, Data Lake Storage 2. etkinleştirilmiş bir depolama hesabında depolanan veriler, HDInsight kümesi silindikten sonra bile devam ediyor.

## <a name="create-a-container"></a>Bir kapsayıcı oluşturma

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/

* Yer tutucusunu `<container-name>` , kapsayıcınıza vermek istediğiniz adla değiştirin.

* `<storage-account-name>` Yer tutucusunu depolama hesabınızın adıyla değiştirin.

## <a name="get-a-list-of-files-or-directories"></a>Dosya veya dizinlerin listesini al

    hdfs dfs -ls <path>

`<path>` Yer tutucuyu kapsayıcının veya kapsayıcı klasörünün URI 'siyle değiştirin.

Örneğin, `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Dizin oluşturma

    hdfs dfs -mkdir [-p] <path>

`<path>` Yer tutucusunu kök kapsayıcı adı veya Kapsayıcınız içindeki bir klasör ile değiştirin.

Örneğin, `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Dosya veya dizini silme

    hdfs dfs -rm <path>

Yer tutucusunu `<path>` , silmek istediğiniz dosya veya klasörün URI 'siyle değiştirin.

Örneğin, `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Dosyaların ve dizinlerin Access Control listelerini (ACL 'Ler) görüntüleme

    hdfs dfs -getfacl [-R] <path>

Örnek:

`hdfs dfs -getfacl -R /dir`

Bkz. [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Dosya ve dizinlerin ACL 'Lerini ayarla

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Örnek:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Bkz. [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>Dosyaların sahibini değiştirme

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Bkz. [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Dosyaların grup ilişkilendirmesini değiştirme

    hdfs dfs -chgrp [-R] <group> <URI>

Bkz. [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>Dosyaların izinlerini değiştirme

    hdfs dfs -chmod [-R] <mode> <URI>

Bkz. [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

Komutların tüm listesini [Apache Hadoop 2.4.1 dosya sistemi kabuğu Kılavuzu](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) Web sitesinde görüntüleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Databricks Azure Data Lake Storage 2. özellikli bir hesap kullanın](./data-lake-storage-quickstart-create-databricks-account.md)

* [Dosya ve dizinlerdeki erişim denetim listeleri hakkında bilgi edinin](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
