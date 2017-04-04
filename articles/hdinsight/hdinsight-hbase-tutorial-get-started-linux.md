---
title: "Azure HDInsight’ta HBase ile çalışmaya başlama | Microsoft Docs"
description: "HDInsight’ta Hadoop ile Apache HBase kullanmaya başlamak için bu HBase öğreticisini izleyin. HBase kabuğundan tablolar oluşturun ve Hive kullanarak bunları sorgulayın."
keywords: "apache hbase,hbase,hbase kabuğu,hbase öğreticisi"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 21d8dff230e045607b70013f4eabf1bfe8ec3993
ms.lasthandoff: 03/25/2017


---
# <a name="hbase-tutorial-get-started-using-apache-hbase-in-hdinsight"></a>HBase öğreticisi: HDInsight’ta Apache HBase kullanmaya başlayın

HDInsight’ta HBase kümesi oluşturma, HBase tabloları oluşturma ve tabloları Hive kullanarak sorgulama hakkında bilgi edinin. Genel HBase bilgileri için bkz. [HDInsight HBase’e genel bakış][hdinsight-hbase-overview].

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Ön koşullar
HBase öğreticisine başlamadan önce aşağıdakilere sahip olmanız gerekir:

* **Bir Azure aboneliği**. Bkz. [Azure ücretsiz deneme sürümü alma](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Secure Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>Erişim denetimi gereksinimleri
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>HBase kümesi oluşturma
Aşağıdaki yordamda 3.4 sürümü Linux tabanlı HBase kümesi ve bağlı varsayılan Azure Storage hesabı oluşturmak için Azure Resource Manager şablonu kullanılmaktadır. Yordamda ve diğer küme oluşturma yöntemlerinde kullanılan parametreleri anlamak için bkz. [HDInsight’ta Linux tabanlı Hadoop kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md).

1. Azure Portal'da bir şablonu açmak için aşağıdaki görüntüye tıklayın. Şablon, ortak bir blob kapsayıcısında bulunur. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **Özel dağıtım** dikey penceresine şu değerleri girin:
   
   * **Abonelik**: Kümeyi oluşturmak için kullanılacak Azure aboneliğinizi seçin.
   * **Kaynak grubu**: Yeni bir Azure Resource Management grubu oluşturun veya var olan gruplardan birini kullanın.
   * **Konum**: Kaynak grubu konumunu belirtin. 
   * **ClusterName**: Oluşturacağınız HBase kümesi için bir ad girin.
   * **Küme oturum açma adı ve parolası**: Varsayılan oturum açma adı **admin** şeklindedir.
   * **SSH kullanıcı adı ve parolası**: Varsayılan kullanıcı adı **sshuser** şeklindedir.  Bunu yeniden adlandırabilirsiniz.
     
     Diğer parametreler isteğe bağlıdır.  
     
     Her kümenin bir Azure Depolama hesabı bağımlılığı vardır. Bir küme silindikten sonra veriler depolama hesabında saklanır. Kümenin varsayılan depolama hesabı adı, "depo" ifadesi eklenmiş küme adıdır. Şablon değişkenleri bölümüne sabit kodlanır.
3. **Yukarıdaki hüküm ve koşulları kabul ediyorum**’u seçip **Satın al**’a tıklayın. Bir küme oluşturmak yaklaşık 20 dakika sürer.

> [!NOTE]
> Bir HBase kümesi silindikten sonra aynı varsayılan blob kapsayıcısını kullanarak başka bir HBase kümesi oluşturabilirsiniz. Yeni küme özgün kümede oluşturduğunuz HBase tablolarını seçer. Tutarsızlıkları önlemek için kümeyi silmeden önce HBase tablolarını devre dışı bırakmanız önerilir.
> 
> 

## <a name="create-tables-and-insert-data"></a>Tablo oluşturma ve veri ekleme
HBase kümelerine bağlanmak ve HBase Kabuğu kullanarak HBase tabloları oluşturmak, veri eklemek ve verileri sorgulamak için SSH kullanabilirsiniz. Daha fazla bilgi için bkz. [HDInsight ile SSH kullanma](hdinsight-hadoop-linux-use-ssh-unix.md).

Çoğu kişi için veriler tablo biçiminde görünür:

![HDInsight HBase tablo verileri][img-hbase-sample-data-tabular]

Bir BigTable uygulaması olan HBase’de aynı veriler şu şekilde görünür:

![HDInsight HBase BigTable verileri][img-hbase-sample-data-bigtable]

Bir sonraki yordamı tamamladıktan sonra bunlar daha anlamlı olacaktır.  

**HBase kabuğunu kullanmak için**

1. SSH’den aşağıdaki komutu çalıştırın:
   
        hbase shell
2. İki sütun ailesi ile bir HBase oluşturun:
   
        create 'Contacts', 'Personal', 'Office'
        list
3. Bazı verileri ekleyin:
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![HDInsight Hadoop HBase kabuğu][img-hbase-shell]
4. Tek bir satır alın
   
        get 'Contacts', '1000'
   
    Yalnızca bir satır olduğundan tarama komutunu kullanmanızla aynı sonuçları görürsünüz.
   
    HBase tablo şeması hakkında daha fazla bilgi için bkz. [HBase Şema Tasarımına Giriş][hbase-schema]. HBase komutları hakkında daha fazla bilgi için bkz. [Apache HBase başvuru kılavuzu][hbase-quick-start].
5. Kabuktan çıkış yapma
   
        exit

**Verileri kişi HBase tablosuna toplu olarak yüklemek için**

HBase’de verileri tablolara yüklemek için bazı yöntemler vardır.  Daha fazla bilgi için bkz. [Toplu yükleme](http://hbase.apache.org/book.html#arch.bulk.load).

Örnek veri dosyası genel blob kapsayıcısına (*wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*) yüklenmiştir.  Veri dosyasının içeriği şudur:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Bir metin dosyası oluşturabilir ve isterseniz dosyayı kendi depolama hesabınıza yükleyebilirsiniz. Yönergeler için bkz. [HDInsight'ta Hadoop işleri için verileri karşıya yükleme][hdinsight-upload-data].

> [!NOTE]
> Bu yordam son yordamda oluşturduğunuz Kişiler HBase tablosunu kullanır.
> 
> 

1. SSH’de, veri dosyalarını StoreFiles’a dönüştürmek ve Dimporttsv.bulk.output tarafından belirtilen göreli bir yola depolamak için aşağıdaki komutu çalıştırın:  HBase Kabuğu'ndan çıkış yapmak için çıkış komutunu kullanın.
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
2. Verileri /example/data/storeDataFileOutput konumundan HBase tablosuna yüklemek için aşağıdaki komutu çalıştırın:
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
3. HBase kabuğunu açabilir ve tarama komutunu kullanarak tablo içeriğini listeleyebilirsiniz.

## <a name="use-hive-to-query-hbase"></a>Hive kullanarak HBase sorgulama
Hive kullanarak HBase tablolarındaki verileri sorgulayabilirsiniz. Bu bölüm HBase tablosuyla eşlenen bir Hive tablosu oluşturur ve HBase tablosunda verileri sorgulamak için kullanır.

> [!NOTE]
> Hive ve HBase aynı sanal ağ içindeki farklı kümelerdeyse, Hive kabuğunu çağırırken zookeper çekirdeği geçirmeniz gerekir:
>
>       hive --hiveconf hbase.zookeeper.quorum=zk0-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk1-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk2-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net --hiveconf zookeeper.znode.parent=/hbase-unsecure  
>
>

1. **PuTTY** uygulamasını açın ve kümeye bağlanın.  Önceki yordamda bulunan yönergelere bakın.
2. Hive kabuğunu açın.
   
       hive
       
3. HBase tablosuyla eşlenen bir Hive tablosu oluşturmak için aşağıdaki HiveQL betiğini çalıştırın. Bu deyimi çalıştırmadan önce HBase kabuğunu kullanarak bu öğreticinin daha önceki bölümlerinde başvurulan örnek tablosunu oluşturduğunuzdan emin olun.
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
4. HBase tablosundaki verileri sorgulamak için aşağıdaki HiveQL betiğini çalıştırın:
   
         SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>Curl kullanarak HBase REST API’lerini kullanma
> [!NOTE]
> Curl’ü veya WebHCat ile başka bir REST iletişimini kullanırken HDInsight küme yöneticisinin kullanıcı adı ve parolasını sağlayarak isteklerin kimliğini doğrulamanız gerekir. Ayrıca, sunucuya istek göndermek için kullanılan Tekdüzen Kaynak Tanımlayıcısı’nın (URI) bir parçası olarak küme adını kullanmanız gerekir.
> 
> Bu bölümdeki komutlar için **USERNAME** ifadesini küme kimliğini doğrulayacak kullanıcı ile, **PASSWORD** ifadesini ise kullanıcı hesabının parolası ile değiştirin. **CLUSTERNAME** değerini kümenizin adıyla değiştirin.
> 
> REST API’sinin güvenliği [temel kimlik doğrulaması](http://en.wikipedia.org/wiki/Basic_access_authentication) ile sağlanır. Kimlik bilgilerinizin sunucuya güvenli bir şekilde gönderilmesi için istekleri her zaman Güvenli HTTP (HTTPS) kullanarak yapmanız gerekir.
> 
> 

1. HDInsight kümenize bağlanabildiğinizi doğrulamak için bir komut satırında aşağıdaki komutu kullanın:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
   
    Aşağıdakine benzer bir yanıt almanız gerekir:
   
        {"status":"ok","version":"v1"}
   
    Bu komutta kullanılan parametreler aşağıdaki gibidir:
   
   * **-u** - İstek kimliğini doğrulamak için kullanılan kullanıcı adı ve parola.
   * **-G** - Bunun bir GET isteği olduğunu belirtir.
2. Mevcut HBase tablolarını listelemek için şu komutu kullanın:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/
3. İki sütunlu aileler içeren yeni bir HBase tablosu oluşturmak için aşağıdaki komutu kullanın:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v
   
    Şema JSon biçiminde sağlanır.
4. Bazı verileri eklemek için aşağıdaki komutu kullanın:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
        -v
   
    -d anahtarında belirtilen değerleri base64 ile kodlamanız gerekir.  Örnekte:
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole
     
     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) birden fazla (toplu) değer eklemenizi sağlar.
5. Bir satır almak için aşağıdaki komutu kullanın:
   
        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

HBase Rest hakkında daha fazla bilgi için bkz. [Apache HBase Başvuru Kılavuzu](https://hbase.apache.org/book.html#_rest).

>
> [!NOTE]
> Thrift, HDInsight’ta HBase tarafından desteklenmez.
>

## <a name="check-cluster-status"></a>Küme durumunu denetleme
HDInsight içinde HBase, kümelerin izlenmesi için bir Web Kullanıcı Arabirimi ile birlikte gönderilir. Web Kullanıcı Arabirimini kullanarak istatistikler veya bölgeler hakkında bilgi isteyebilirsiniz.

**HBase Master Kullanıcı Arabirimi’ne erişmek için**

1. https://&lt;Kumeadi>.azurehdinsight.net adresinden Ambari Web Kullanıcı Arabirimini açın.
2. Soldaki menüden **HBase**’e tıklayın.
3. Sayfanın üstündeki **Hızlı bağlantılar**’a tıklayın, etkin Zookeeper düğümü bağlantısının üzerine gelin ve **HBase Master Kullanıcı Arabirimi**’ne tıklayın.  Kullanıcı arabirimi başka bir tarayıcı sekmesinde açılır:

  ![HDInsight HBase HMaster Kullanıcı Arabirimi](./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

  HBase Master Kullanıcı Arabirimi aşağıdaki bölümleri içerir:

  - Bölge sunucuları
  - Yedekleme yöneticileri
  - Tablolar
  - Görevler
  - Yazılım öznitelikleri

## <a name="delete-the-cluster"></a>Küme silme
Tutarsızlıkları önlemek için kümeyi silmeden önce HBase tablolarını devre dışı bırakmanız önerilir.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Sonraki adımlar
HDInsight’a yönelik bir HBase öğreticisinde bir HBase kümesi oluşturmayı ve tablo oluşturup bu tablolardaki verileri HBase kabuğundan görüntülemeyi öğrendiniz. Ayrıca HBase tablolarındaki veriler üzerinde bir Hive sorgusu kullanmayı, HBase C# REST API’lerini kullanarak bir HBase tablosu oluşturmayı ve tablodan veri almayı öğrendiniz.

Daha fazla bilgi için bkz:

* [HDInsight HBase'e genel bakış][hdinsight-hbase-overview]: HBase büyük miktarda yapılandırılmamış ve yarı yapılandırılmış veri için rastgele erişim ve güçlü tutarlılık sağlayan, Hadoop'ta yerleşik bir Apache, açık kaynak, NoSQL veritabanıdır.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

