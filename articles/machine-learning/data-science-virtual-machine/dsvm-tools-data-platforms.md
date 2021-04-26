---
title: Desteklenen veri platformları
titleSuffix: Azure Data Science Virtual Machine
description: Azure Veri Bilimi Sanal Makinesi için desteklenen veri platformları ve araçları hakkında bilgi edinin.
keywords: veri bilimi araçları, veri bilimi sanal makinesi, veri bilimi için araçlar, linux veri bilimi
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 927e945a0d045abcd1caa2951dbd484224b2f425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519550"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Veri Bilimi Sanal Makinesi desteklenen veri platformları

Veri Bilimi Sanal Makinesi (DSVM) ile analizlerinizi çok çeşitli veri platformları için oluşturabilirsiniz. Uzak veri platformlarına yönelik arabirimlerin yanı sıra, DSVM, hızlı geliştirme ve prototip oluşturma için yerel bir örnek sağlar.

DSVM 'de aşağıdaki veri platformu araçları desteklenir.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Yerel bir ilişkisel veritabanı örneği      |
| Desteklenen DSVM sürümleri      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Tipik kullanımlar      | <ul><li>Daha küçük veri kümesiyle yerel olarak hızlı geliştirme</li><li>Veritabanı Içinde Çalıştır R</li></ul> |
| Örneklere bağlantılar      | <ul><li>Yeni bir York City veri kümesinin küçük bir örneği SQL veritabanına yüklenir:<br/>  `nyctaxi`</li><li>Microsoft Machine Learning Server ve veritabanı içi analizlerini gösteren Jupyter örneği şurada bulunabilir:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| DSVM 'deki ilgili araçlar       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC sürücüleri</li><li>pyodbc, RODBC</li><li>Apache Drill</li></ul> |

> [!NOTE]
> SQL Server Developer sürümü yalnızca geliştirme ve test amaçları için kullanılabilir. Üretimde çalıştırmak için bir lisansa veya SQL Server sanal makinelerinden birine ihtiyacınız vardır.


### <a name="setup"></a>Kurulum

Veritabanı sunucusu zaten önceden yapılandırılmıştır ve SQL Server (gibi) ile ilgili Windows Hizmetleri `SQL Server (MSSQLSERVER)` otomatik olarak çalışacak şekilde ayarlanır. Tek el ile yapılan adım, Microsoft Machine Learning Server kullanarak veritabanı Içi analizlerin etkinleştirilmesini içerir. SQL Server Management Studio (SSMS) ' de bir kerelik eylem olarak aşağıdaki komutu çalıştırarak analizlerinizi etkinleştirebilirsiniz. Makine Yöneticisi olarak oturum açtıktan sonra SSMS 'de yeni bir sorgu açarak ve seçilen veritabanının şu olduğundan emin olduktan sonra bu komutu çalıştırın `master` :

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(% COMPUTERNAME% öğesini VM adınızla değiştirin.)

SQL Server Management Studio çalıştırmak için program listesinde "SQL Server Management Studio" araması yapabilir veya Windows Search kullanarak dosyayı bulup çalıştırabilirsiniz. Kimlik bilgileri istendiğinde, **Windows kimlik doğrulaması** ' nı seçin ve makine adını veya ```localhost``` **SQL Server adı** alanını kullanın.

### <a name="how-to-use-and-run-it"></a>Kullanma ve çalıştırma

Varsayılan olarak, varsayılan veritabanı örneğiyle veritabanı sunucusu otomatik olarak çalışır. SQL Server veritabanına yerel olarak erişmek için VM 'deki SQL Server Management Studio gibi araçları kullanabilirsiniz. Yerel yönetici hesaplarının, veritabanında Yönetici erişimi vardır.

Ayrıca, DSVM, Python ve Machine Learning Server dahil olmak üzere birden çok dilde yazılan uygulamalardan SQL Server, Azure SQL veritabanları ve Azure SYNAPSE Analytics ile iletişim sağlamak için ODBC ve JDBC sürücülerle birlikte gelir.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM 'de nasıl yapılandırılır ve yüklenir? 

 SQL Server, standart şekilde yüklenir. Bu, adresinde bulunabilir `C:\Program Files\Microsoft SQL Server` . Veritabanı Içi Machine Learning Server örneği konumunda bulunur `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` . DSVM 'de ' de yüklü olan ayrı bir tek başına Machine Learning Server örneği de vardır `C:\Program Files\Microsoft\R Server\R_SERVER` . Bu iki Machine Learning Server örneği kitaplıkları paylaşmaz.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2. x (tek başına)

| Kategori | Değer |
| ------------- | ------------- |
| Nedir o?   | Popüler Apache Spark platformunun tek başına (işlem içi tek düğümlü) örneği; hızlı, büyük ölçekli veri işleme ve makine öğrenimi için bir sistem     |
| Desteklenen DSVM sürümleri      | Linux     |
| Tipik kullanımlar      | <ul><li>Spark/PySpark uygulamalarının yerel olarak daha küçük bir veri kümesiyle ve daha sonra Azure HDInsight gibi büyük Spark kümelerine dağıtımıyla birlikte hızla geliştirilmesi</li><li>Spark bağlamını test Microsoft Machine Learning Server</li><li>ML uygulamaları derlemek için parlak ml veya Microsoft 'un açık kaynaklı [Mmlspark](https://github.com/Azure/mmlspark) kitaplığını kullanın</li></ul> |
| Örneklere bağlantılar      |    Jupyter örneği:<ul><li>~/Notebooks/parlak ml/pyspark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (Spark bağlamı):/Dsvm/Samples/Mrs/mrsmini Contextsample.exe</p> |
| DSVM 'deki ilgili araçlar       | <ul><li>PySpark, Scala</li><li>Jupyter (Spark/PySpark kernels)</li><li>Microsoft Machine Learning Server, parlak r, parlak LYR</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>Nasıl kullanılır?
Veya komutunu çalıştırarak Spark işlerini komut satırına gönderebilirsiniz `spark-submit` `pyspark` . Spark çekirdeğiyle yeni bir not defteri oluşturarak da Jupyter Not defteri oluşturabilirsiniz.

R 'den Spark kullanarak, DSVM 'de bulunan parlak r, parlak LYR ve Microsoft Machine Learning Server gibi kitaplıkları kullanabilirsiniz. Yukarıdaki tablodaki örneklere yönelik işaretçilere bakın.

### <a name="setup"></a>Kurulum
Ubuntu Linux DSVM sürümünde Microsoft Machine Learning Server bir Spark bağlamında çalışmadan önce, yerel tek düğümlü Hadoop ve Yarn örneğini etkinleştirmek için bir kerelik kurulum adımını doldurmanız gerekir. Hadoop Hizmetleri varsayılan olarak DSVM 'de yüklüdür ancak devre dışı bırakılır. Bunları etkinleştirmek için, ilk kez kök olarak aşağıdaki komutları çalıştırın:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Artık bu hizmetleri çalıştırarak Hadoop ile ilgili hizmetleri durdurabilirsiniz ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Bir uzak Spark bağlamında MRS 'nin nasıl geliştirileceğini ve test alınacağını gösteren bir örnek (DSVM 'deki tek başına Spark örneği), dizinde sağlanır ve kullanılabilir `/dsvm/samples/MRS` .


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>DSVM 'de nasıl yapılandırılır ve yüklenir? 
|Platform|Konum yüklemeyi ($SPARK _HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Microsoft MMLSpark Machine-Learning kitaplıklarını kullanarak Azure Blob depolama veya Azure Data Lake Storage verilere erişmek için kitaplıklar, $SPARK _HOME/jars. Spark başladığında bu Jliler otomatik olarak yüklenir. Spark, varsayılan olarak yerel diskte verileri kullanır. 

DSVM 'deki Spark örneğinin blob depolamada veya Azure Data Lake Storage depolanan verilere erişmesi için, `core-site.xml` $SPARK _HOME/conf/core-site.xml. Template ' de bulunan şablona göre dosyayı oluşturmanız ve yapılandırmanız gerekir. Ayrıca blob depolamaya ve Azure Data Lake Storage erişmek için uygun kimlik bilgilerine sahip olmanız gerekir. (Şablon dosyalarının BLOB depolama ve Azure Data Lake Storage yapılandırmalarına yönelik yer tutucuları kullandığına unutmayın.)

Azure Data Lake Storage hizmeti kimlik bilgileri oluşturma hakkında daha ayrıntılı bilgi için bkz. [Azure Data Lake Storage 1. Ile kimlik doğrulama](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). BLOB depolama veya Azure Data Lake Storage kimlik bilgileri core-site.xml dosyasına girildikten sonra, bu kaynaklarda depolanan verilere wasb://veya adl://URI ön eki aracılığıyla başvurabilirsiniz.