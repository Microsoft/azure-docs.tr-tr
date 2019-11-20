---
title: Hive tabloları oluşturma ve Blob depolama alanından - Team Data Science Process veri yükleme
description: Hive tabloları oluşturma ve Azure blob depolamadan veri yükleme için Hive sorguları kullanın. Hive tablolarını bölümlemek ve en iyi duruma getirilmiş satır sütunlu (sorgu performansını artırmak için biçimlendirme ORC) kullanın.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: af9c072c428c486cab89288db4c9ee1c26513185
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250139"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Hive tabloları oluşturma ve Azure Blob depolamadan veri yükleme

Bu makalede, Hive tabloları oluşturma ve Azure blob depolamadan veri yükleme genel Hive sorguları gösterir. Hive tablolarını bölümleme ve en iyi duruma getirilmiş satır sütunlu (sorgu performansını artırmak için biçimlendirme ORC) kullanarak, bazı yönergeler de sağlanır.

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, olduğunu varsayar:

* Bir Azure depolama hesabı oluşturuldu. Yönergelere ihtiyacınız varsa bkz [Azure depolama hesapları hakkında](../../storage/common/storage-introduction.md).
* HDInsight hizmeti ile özelleştirilmiş bir Hadoop kümesi hazırlandı.  Yönergelere ihtiyacınız varsa bkz. [HDInsight 'Ta kümeleri ayarlama](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Kümeye uzaktan erişimin etkinleştirilmesi, oturum ve Hadoop komut satırı konsolu açılır. Yönergelere ihtiyacınız varsa bkz. [Apache Hadoop kümelerini yönetme](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Azure blob depolama alanına veri yükleme
Bir Azure sanal makinesi bölümlerinde sağlanan yönergeleri izleyerek oluşturduysanız [Gelişmiş analiz için Azure sanal Makine'yi ayarlayın](../../machine-learning/data-science-virtual-machine/overview.md), bu komut dosyası için indirilip *C:\\kullanıcılar \\ \<kullanıcı adı\>\\belgeleri\\veri bilimi betikleri* sanal makinesinde dizin. Bu Hive sorguları yalnızca kendi veri şemasını ve Azure blob depolama yapılandırması gönderimi için hazır olmasını uygun alanları eklenti gereklidir.

Hive tablolarını verilerini olduğunu varsayıyoruz bir **sıkıştırılmamış** tablosal biçimde ve verileri varsayılan (veya ek bir) yüklendiğini Hadoop kümesi tarafından kullanılan depolama hesabı kapsayıcısı.

Uygulama için isterseniz **NYC taksi seyahat verilerini**, gerekir:

* **indirme** 24 [NYC taksi seyahat verilerini](https://www.andresmh.com/nyctaxitrips) (12 seyahat dosyalar ve 12 taksi dosyaları)
* **Unzip** .csv dosyalarına tüm dosyaları ve ardından
* Bunları Azure Storage hesabının varsayılan (veya uygun kapsayıcısına) birine **yükleyin** ; Böyle bir hesabın seçenekleri [Azure HDInsight kümeleri Ile Azure depolama kullanma](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) konusunda görünür. Bu işlem depolama hesabındaki varsayılan kapsayıcı .csv dosyalarını yüklemek için bulunabilir [sayfa](hive-walkthrough.md#upload).

## <a name="submit"></a>Hive sorguları göndermek nasıl
Hive sorgularını kullanarak gönderilebilir:

1. [Hadoop kümesinin baş içinde Hadoop komut satırı aracılığıyla Hive sorguları göndermek](#headnode)
2. [Hive Düzenleyicisi ile Hive sorguları göndermek](#hive-editor)
3. [Azure PowerShell komutları ile Hive sorguları göndermek](#ps)

Hive sorguları SQL benzeri. SQL ile hakkında bilginiz varsa, bulabilirsiniz [Hive SQL kullanıcılar sayfasını hile](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) yararlıdır.

Bir Hive sorgusu gönderirken ekranda veya baş düğümü üzerindeki yerel bir dosyaya veya bir Azure blob'a oluşmasından Hive sorguları çıkışının hedef de denetleyebilirsiniz.

### <a name="headnode"></a> 1. Hadoop kümesinin baş içinde Hadoop komut satırı aracılığıyla Hive sorguları göndermek
Hive sorgusu karmaşıksa, doğrudan Hadoop baş düğümünde gönderme küme genellikle daha hızlı Hive Düzenleyicisi ya da Azure PowerShell betikleri ile gönderme daha geri dönüş için yol açar.

Hadoop kümesinin baş düğüme oturum açmak için masaüstünde baş düğümü, Hadoop komut satırını açın ve komutu girin `cd %hive_home%\bin`.

Size, Hadoop komut satırı Hive sorguları göndermek için üç yolunuz vardır:

* doğrudan
* .hql dosyalarını kullanma
* Hive Komut Konsolu ile

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Hive sorguları doğrudan, Hadoop komut satırı gönderin.
Komutu gibi çalıştırabilirsiniz `hive -e "<your hive query>;` doğrudan, Hadoop komut satırı basit Hive sorguları göndermek için. Burada kırmızı kutu Hive sorgusu gönderen komut özetlemekte ve Hive sorgusu çıkışı yeşil kutuyu özetler bir örnek aşağıda verilmiştir.

![Hive sorgusu çıkışı ile Hive sorgu göndermek için komutu](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>.Hql dosyalarında Hive sorguları göndermek
Komut satırı ya da Hive komut konsolunda sorguları düzenleme, Hive sorgusu daha karmaşıktır ve birden fazla satır olduğunda pratik değildir. Hive sorguları baş düğümün, yerel bir dizinde .hql dosyasına kaydetmek için Hadoop kümesi baş düğümünde bir metin Düzenleyicisi kullanmak için bir alternatiftir. Hive sorgusu .hql dosyasındaki kullanarak gönderilebilir sonra `-f` bağımsız değişkeni aşağıdaki gibi:

    hive -f "<path to the .hql file>"

![Hive sorgusu .hql dosyasında](./media/move-hive-tables/run-hive-queries-3.png)

**Hive sorguları ilerleme durumu ekranı Yazdır Gizle**

Hive sorgusu, Hadoop komut satırında gönderildikten sonra varsayılan olarak, Map/Reduce işinin ilerleme durumunu ekranda yazdırılır. Map/Reduce işi ilerleme ekranı Yazdır bastırmak için bağımsız değişken kullanabilirsiniz `-S` (büyük harf "S") komut satırı şu şekilde:

    hive -S -f "<path to the .hql file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Hive komut konsolunda Hive sorguları göndermek.
Komutunu çalıştırarak, Hive komut konsolunda da ilk girebilirsiniz `hive` Hadoop komut satırı ve ardından Hive komut konsolunda Hive sorguları göndermek. Bir örnek aşağıda verilmiştir. Bu örnekte, iki kırmızı kutuları, Hive komut konsoluna girmek için kullanılan komutlar ve Hive komut konsolunda sırasıyla gönderilen Hive sorgusu vurgulayın. Yeşil kutuyu Hive sorgusu çıkışı vurgular.

![Hive komut konsolunu açın ve komutu girin, Hive sorgusu çıkışı görüntülemek](./media/move-hive-tables/run-hive-queries-2.png)

Önceki örneklerde, ekranda Hive sorgu sonuçları doğrudan çıkış. Yerel bir dosyaya baş düğüme veya bir Azure blob çıktı yazabilirsiniz. Ardından, Hive sorguları çıkışı daha fazla analiz için diğer araçları kullanabilirsiniz.

**Hive sorgu sonuçları yerel bir dosyaya çıktı.**
Hive sorgu sonuçlarını baş düğümü üzerindeki yerel bir dizine çıkarmak için Hive sorgusunu, Hadoop komut satırını aşağıdaki şekilde gönderin vardır:

    hive -e "<hive query>" > <local path in the head node>

Aşağıdaki örnekte, Hive sorgusu çıkışı bir dosyaya yazılır `hivequeryoutput.txt` dizinde `C:\apps\temp`.

![Hive sorgusu çıkışı](./media/move-hive-tables/output-hive-results-1.png)

**Hive sorgu sonuçları için bir Azure blob çıktı**

Ayrıca, Hadoop kümesi varsayılan kapsayıcı içinde bir Azure blob'a Hive sorgu sonuçları çıkış sağlayabilir. Bu Hive sorgu aşağıdaki gibidir:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Aşağıdaki örnekte, Hive sorgusu çıkışı bir blob dizinine yazılır `queryoutputdir` Hadoop kümesi varsayılan kapsayıcı içinde. Burada, yalnızca blob adı olmayan bir dizin adı sağlamanız gerekir. Dizin hem blob adları gibi sağlarsanız, bir hata oluşturulur `wasb:///queryoutputdir/queryoutput.txt`.

![Hive sorgusu çıkışı](./media/move-hive-tables/output-hive-results-2.png)

Azure Depolama Gezgini'ni kullanarak Hadoop kümesi varsayılan kapsayıcı açarsanız, aşağıdaki resimde gösterildiği gibi Hive sorgusu çıkışı görebilirsiniz. Yalnızca blob adları belirtilen harflerle almak için (kırmızı kutu ile vurgulanan) filtre uygulayabilirsiniz.

![Hive sorgusu çıkışı gösteren Azure Depolama Gezgini](./media/move-hive-tables/output-hive-results-3.png)

### <a name="hive-editor"></a> 2. Hive Düzenleyicisi ile Hive sorguları göndermek
Bir Web tarayıcısına *https:\//\<Hadoop kümesi adı >. azurehdinsight. net/Home/hiveeditor* biçiminde bir URL girerek sorgu konsolunu (Hive Düzenleyicisi) de kullanabilirsiniz. Siz bu konsolun bakın oturum ve Hadoop kümesi kimlik bilgilerinizi buraya nedenle gerekir.

### <a name="ps"></a> 3. Azure PowerShell komutları ile Hive sorguları göndermek
Hive sorguları göndermek için PowerShell de kullanabilirsiniz. Yönergeler için [gönderme Hive işleri PowerShell kullanarak](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-tables"></a>Hive veritabanı ve tablo oluşturma
Hive sorguları paylaşılan [GitHub deposu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) ve oradan indirilebilir.

Bir Hive tablosu oluşturur Hive sorgusu aşağıda verilmiştir.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Açıklamaları takın gereken alanların ve diğer yapılandırmalar şunlardır:

* **veritabanı adı:\>oluşturmak istediğiniz veritabanının adı. \<** Varsayılan veritabanı sorgusunu kullanmak istiyorsanız *veritabanı oluşturun...*  atlanabilir.
* **tablo adı:\>belirtilen veritabanı içinde oluşturmak istediğiniz tablonun adı. \<** Varsayılan veritabanını kullanmak istiyorsanız tablo *\<adı\>* , veritabanı adı\>olmadan \<doğrudan başvuruda bulunabilir.
* **alan ayırıcısı:\>veri dosyasında Hive tablosuna yüklenecek alanları sınırlandıran ayırıcı. \<**
* **satır ayırıcı:\>veri dosyasındaki satırları sınırlandıran ayırıcı. \<**
* **depolama konumu:\>Hive tablolarının verilerini kaydetmek için Azure depolama konumu. \<** *Konum\<depolamakonumunu\>* belirtmezseniz, veritabanı ve tablolar varsayılan olarak Hive kümesinin varsayılan kapsayıcısında *Hive/ambar/* dizinde depolanır. Depolama konumu belirtmek istiyorsanız, depolama konumu, tablo ve veritabanı için varsayılan kapsayıcı içinde olması gerekir. Bu konumun, *' wasb:///\<Directory 1 >/'* veya *'\<wasb:///Dizin 1 >/\<dizin 2 >/'* vb. biçimindeki kümenin varsayılan kapsayıcısına göre konum olarak bahsedilmelidir. Sorgu yürütüldükten sonra göreli dizinleri varsayılan kapsayıcı içinde oluşturulur.
* **TBLPROPERTIES ("Skip. Header. Line. Count" = "1")** : Veri dosyasında bir başlık satırı varsa, bu özelliği *Create Table* sorgusunun **sonuna** eklemeniz gerekir. Aksi takdirde, üstbilgi satırını tablosuna bir kayıt olarak yüklenir. Veri dosyasındaki bir üst bilgi satırı yoksa, bu yapılandırma sorguda atlanmış olabilir.

## <a name="load-data"></a>Verileri Hive tablolarına yükleme
Bir Hive tablosuna veri yükler Hive sorgusu aşağıda verilmiştir.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **BLOB verilerininyolu\>: \<** Hive tablosuna yüklenecek blob dosyası HDInsight Hadoop kümesinin varsayılan kapsayıcıda ise,  *\<\> blob verileri yolu* *Bu kapsayıcıda ' wasb://\<Directory ' biçiminde olmalıdır >/blob\<dosya adı > '* . Blob dosya HDInsight Hadoop kümesi ek bir kapsayıcı da olabilir. Bu durumda,  *\<blob verileri\> yolu* *'\<wasb://Container name >\<depolama hesabı adı >. blob. Core. Windows. net/\<BLOB dosya adı > '* biçiminde olmalıdır.

  > [!NOTE]
  > Hive tablosu yüklenmek üzere blob verilerini varsayılan veya ek kapsayıcı Hadoop kümesi için depolama hesabının olması gerekir. Aksi takdirde, *veri yükleme* sorgu başarısız olursa şikayetçi verilere erişemez.
  >
  >

## <a name="partition-orc"></a>Gelişmiş konular: Tablo ve depolama Hive verileri ORC biçiminde bölümlenmiş
Veriler büyükse, tablo bölümleme yalnızca tablo bazı bölümleri taraması gereken sorguları için yararlıdır. Örneğin, bir web sitesi günlük verilerini tarihlerine göre bölümlemek şüphelenilebilir.

Hive tablolarını bölümleme yanı sıra en iyi duruma getirilmiş satır sütunlu (ORC) biçiminde Hive verileri depolamak faydalı olacaktır. ORC biçimlendirme hakkında daha fazla bilgi için bkz: <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">kullanarak ORC dosyalarını Hive okuma, yazma ve veri işleme performansını geliştirir</a>.

### <a name="partitioned-table"></a>Bölümlenmiş bir tablo
Bölümlenmiş bir tablo oluşturur ve verileri içine yükler Hive sorgusunu aşağıda verilmiştir.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Bölümlenmiş tablolar sorgulanırken bölüm koşulu eklemek için önerilir **başına** , `where` bu as yan tümcesi çalışıp çalışmadığını arama önemli ölçüde artırır.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Hive verileri ORC biçiminde Store
ORC biçiminde depolanan Hive tablolarına, blob depolama alanından verileri doğrudan yüklenemiyor. , Yüklemek için uygulamanız gereken adımlar şunlardır blobları Azure verileri ORC biçiminde depolanan Hive tablolarına.

Bir dış tablo oluşturma **DEPOLANAN AS TEXTFILE** ve yük verileri blob depolama alanından tabloya.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Dış tablo adım 1, aynı alan sınırlayıcı ile aynı şemaya sahip iç tablo oluşturun ve Hive verileri ORC biçiminde depolar.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

1\. adım dış tablodaki verileri seçin ve ORC tabloya Ekle

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Textfile tablo *\<\>veritabanı adı.\< Dış textfile tablo adında\>* bölümler bulunur, adım `SELECT * FROM <database name>.<external textfile table name>` 3 ' te komut, Bölüm değişkenini döndürülen veri kümesindeki bir alan olarak seçer. Veritabanı adına ekleniyor. *\<\>\< Orc tablo adı\>* *\<veritabanı adından\>bu yana başarısız\< oluyor. Orc tablo adında\>* , tablo şemasında bir alan olarak bölüm değişkeni yok. Bu durumda, *\<veritabanı adına\>\< eklenecek alanları özel olarak seçmeniz gerekir. Orc tablosu adı\>* aşağıdaki gibidir:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

*\<\>* Tüm veriler *\< veritabanı adınaeklendikten\>sonra, aşağıdaki sorgu kullanılırken dış textfile tablo adının düşürülme güvenlidir. \< ORC tablo adı\>* :

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Bu yordamı tamamladıktan sonra verileri ORC biçiminde kullanıma hazır bir tablo olmalıdır.  
