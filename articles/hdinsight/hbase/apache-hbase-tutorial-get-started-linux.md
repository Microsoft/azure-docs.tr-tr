---
title: Öğretici-Azure HDInsight 'ta Apache HBase kullanma
description: HDInsight 'ta Hadoop kullanmaya başlamak için bu Apache HBase öğreticisini izleyin. HBase kabuğundan tablolar oluşturun ve Hive kullanarak bunları sorgulayın.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/22/2021
ms.openlocfilehash: 50216b83475e92cbabb5ed78c3836264ceff4600
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866616"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Öğretici: Azure HDInsight 'ta Apache HBase kullanma

Bu öğreticide, Azure HDInsight 'ta Apache HBase kümesi oluşturma, HBase tabloları oluşturma ve Apache Hive kullanarak sorgu tabloları oluşturma işlemlerinin nasıl yapılacağı gösterilmiştir.  Genel HBase bilgileri için bkz. [HDInsight HBase’e genel bakış](./apache-hbase-overview.md).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Apache HBase kümesi oluşturma
> * HBase tabloları oluşturma ve veri ekleme
> * Apache HBase 'i sorgulamak için Apache Hive kullanma
> * Curl kullanarak HBase REST API’lerini kullanma
> * Küme durumunu denetleme

## <a name="prerequisites"></a>Önkoşullar

* Bir SSH istemcisi. Daha fazla bilgi için bkz. [SSH kullanarak HDInsight'a (Apache Hadoop) bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Bu makaledeki örneklerde, kıvrımlı komutları için Windows 10 ' da bash kabuğu kullanılır. Yükleme adımları için bkz. [Windows 10 Linux Için Windows alt sistemi yükleme kılavuzu](/windows/wsl/install-win10) .  Diğer [UNIX kabukları](https://www.gnu.org/software/bash/) de çalışacaktır.  Kıvrımlı örnekler, bazı hafif değişikliklerle bir Windows komut istemi üzerinde çalışabilir.  Ya da Windows PowerShell cmdlet 'i [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod)' da kullanabilirsiniz.

## <a name="create-apache-hbase-cluster"></a>Apache HBase kümesi oluşturma

Aşağıdaki yordam bir HBase kümesi oluşturmak için bir Azure Resource Manager şablonu kullanır. Şablon, bağımlı varsayılan Azure Depolama hesabını da oluşturur. Yordamda ve diğer küme oluşturma yöntemlerinde kullanılan parametreleri anlamak için bkz. [HDInsight’ta Linux tabanlı Hadoop kümeleri oluşturma](../hdinsight-hadoop-provision-linux-clusters.md).

1. Azure portal şablonu açmak için aşağıdaki görüntüyü seçin. Şablon, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/)bulunur.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. **Özel dağıtım** iletişim kutusunda aşağıdaki değerleri girin:

    |Özellik |Açıklama |
    |---|---|
    |Abonelik|Kümeyi oluşturmak için kullanılan Azure aboneliğinizi seçin.|
    |Kaynak grubu|Bir Azure Kaynak yönetimi grubu oluşturun veya mevcut bir tane kullanın.|
    |Konum|Kaynak grubunun konumunu belirtin. |
    |ClusterName|HBase kümesi için bir ad girin.|
    |Küme oturum açma adı ve parolası|Varsayılan oturum açma adı **admin**' dir.|
    |SSH kullanıcı adı ve parolası|Varsayılan kullanıcı adı **sshuser** şeklindedir.|

    Diğer parametreler isteğe bağlıdır.  

    Her kümenin bir Azure Depolama hesabı bağımlılığı vardır. Bir kümeyi sildikten sonra veriler depolama hesabında kalır. Kümenin varsayılan depolama hesabı adı, "depo" ifadesi eklenmiş küme adıdır. Şablon değişkenleri bölümünde sabit kodlanmış.

3. **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**' u seçin ve ardından **satın al**' ı seçin. Bir küme oluşturmak yaklaşık 20 dakika sürer.

Bir HBase kümesi silindikten sonra aynı varsayılan blob kapsayıcısını kullanarak başka bir HBase kümesi oluşturabilirsiniz. Yeni küme özgün kümede oluşturduğunuz HBase tablolarını seçer. Tutarsızlıkları önlemek için kümeyi silmeden önce HBase tablolarını devre dışı bırakmanız önerilir.

## <a name="create-tables-and-insert-data"></a>Tablo oluşturma ve veri ekleme

SSH kullanarak HBase kümelerine bağlanabilir ve ardından, HBase tabloları oluşturmak, veri eklemek ve verileri sorgulamak için [Apache HBase kabuğu](https://hbase.apache.org/0.94/book/shell.html) 'nu kullanabilirsiniz.

Çoğu kişi için veriler tablo biçiminde görünür:

:::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png" alt-text="HDInsight Apache HBase tablo verileri" border="false":::

HBase 'de ( [Cloud BigTable](https://cloud.google.com/bigtable/)'ın bir uygulamasında), aynı veriler şöyle görünür:

:::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png" alt-text="HDInsight Apache HBase BigTable verileri" border="false":::

**HBase kabuğunu kullanmak için**

1. `ssh`HBase kümenize bağlanmak için komutunu kullanın. Aşağıdaki komutu, `CLUSTERNAME` kümenizin adıyla değiştirerek düzenleyin ve ardından şu komutu girin:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `hbase shell`HBase etkileşimli kabuğunu başlatmak için komutunu kullanın. SSH bağlantınıza aşağıdaki komutu girin:

    ```bash
    hbase shell
    ```

1. `create`İki sütunlu ailelerle bir HBase tablosu oluşturmak için komutunu kullanın. Tablo ve sütun adları büyük/küçük harfe duyarlıdır. Aşağıdaki komutu girin:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. `list`HBase 'deki tüm tabloları listelemek için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbase
    list
    ```

1. Belirli bir tablodaki belirtilen bir `put` sütundaki değerleri eklemek için komutunu kullanın. Aşağıdaki komutları girin:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. `scan`Tablo verilerini taramak ve döndürmek için komutunu kullanın `Contacts` . Aşağıdaki komutu girin:

    ```hbase
    scan 'Contacts'
    ```

    :::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png" alt-text="HDInsight Apache Hadoop HBase kabuğu" border="false":::

1. `get`Bir satırın içeriğini getirmek için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    `scan`Yalnızca bir satır olduğundan, komutunu kullanarak benzer sonuçlar görürsünüz.

    HBase tablo şeması hakkında daha fazla bilgi için bkz. [Apache HBase şema tasarımına giriş](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). HBase komutları hakkında daha fazla bilgi için bkz. [Apache HBase başvuru kılavuzu](https://hbase.apache.org/book.html#quickstart).

1. `exit`HBase etkileşimli kabuğunu durdurmak için komutunu kullanın. Aşağıdaki komutu girin:

    ```hbaseshell
    exit
    ```

**Verileri kişi HBase tablosuna toplu olarak yüklemek için**

HBase’de verileri tablolara yüklemek için bazı yöntemler vardır.  Daha fazla bilgi için bkz. [Toplu yükleme](https://hbase.apache.org/book.html#arch.bulk.load).

Örnek veri dosyası, ortak blob kapsayıcısı `wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt` içinde bulunabilir.  Veri dosyasının içeriği şudur:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

İsterseniz, bir metin dosyası oluşturabilir ve dosyayı kendi depolama hesabınıza yükleyebilirsiniz. Yönergeler için bkz. [HDInsight 'ta Apache Hadoop işleri için verileri karşıya yükleme](../hdinsight-upload-data.md).

Bu yordam `Contacts` , son yordamda oluşturduğunuz HBase tablosunu kullanır.

1. Açık SSH bağlantınızdan, veri dosyasını StoreFiles 'a dönüştürmek ve tarafından belirtilen göreli bir yola depolamak için aşağıdaki komutu çalıştırın `Dimporttsv.bulk.output` .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Verileri HBase tablosuna yüklemek için aşağıdaki komutu çalıştırın `/example/data/storeDataFileOutput` :

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. HBase kabuğunu açabilir ve `scan` tablo içeriğini listelemek için komutunu kullanabilirsiniz.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Apache HBase 'i sorgulamak için Apache Hive kullanma

[Apache Hive](https://hive.apache.org/)kullanarak HBase tablolarında verileri sorgulayabilirsiniz. Bu bölümde HBase tablosuyla eşlenen bir Hive tablosu oluşturur ve HBase tablosunda verileri sorgulamak için kullanırsınız.

1. Açık SSH bağlantınızdan, Beeline başlamak için aşağıdaki komutu kullanın:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Beeline hakkında daha fazla bilgi için bkz. [Beeline ile HDInsight’ta Hadoop ile Hive kullanma](../hadoop/apache-hadoop-use-hive-beeline.md).

1. HBase tablosuyla eşlenen bir Hive tablosu oluşturmak için aşağıdaki [Hiveql](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) betiğini çalıştırın. Bu ifadeyi çalıştırmadan önce HBase kabuğunu kullanarak bu makalede daha önce bahsedilen örnek tabloyu oluşturduğunuzdan emin olun.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. HBase tablosundaki verileri sorgulamak için aşağıdaki HiveQL betiğini çalıştırın:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Beeline çıkmak için kullanın `!exit` .

1. SSH bağlantısından çıkmak için kullanın `exit` .

### <a name="separate-hive-and-hbase-clusters"></a>Hive ve HBase kümelerini ayır

HBase verilerine erişim için Hive sorgusunun HBase kümesinden yürütülmesi gerekmez. Hive ile birlikte gelen herhangi bir küme (Spark, Hadoop, HBase veya etkileşimli sorgu dahil), HBase verilerini sorgulamak için aşağıdaki adımlar tamamlandıysa kullanılabilir:

1. Her iki küme de aynı sanal ağa ve alt ağa bağlanmalıdır
2. `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml`HBase küme bir düğümünden Hive kümesi baş 'a kopyalama

### <a name="secure-clusters"></a>Güvenli kümeler

HBase verileri Ayrıca, ESP etkin HBase kullanılarak Hive ile sorgulanabilir: 

1. Çok kümeli bir model takip edildiğinde her iki küme de ESP etkin olmalıdır. 
2. Hive 'nin HBase verilerini sorgusuna izin vermek için, kullanıcının HBase `hive` Apache Ranger eklentisi aracılığıyla HBase verilerine erişim izni verildiğinden emin olun
3. Ayrı, ESP etkin kümeler kullanılırken, `/etc/hosts` HBase küme baş içeriğinin `/etc/hosts` Hive küme baş listesine eklenmesi gerekir. 
> [!NOTE]
> Kümeleri ölçeklendirdikten sonra `/etc/hosts` tekrar eklenmesi gerekir

## <a name="use-hbase-rest-apis-using-curl"></a>Curl kullanarak HBase REST API’lerini kullanma

REST API’sinin güvenliği [temel kimlik doğrulaması](https://en.wikipedia.org/wiki/Basic_access_authentication) ile sağlanır. Kimlik bilgilerinizin sunucuya güvenli bir şekilde gönderilmesi için istekleri her zaman Güvenli HTTP (HTTPS) kullanarak yapmalısınız.

1. HDInsight kümesinde HBase REST API 'Lerini etkinleştirmek için, **komut dosyası eylemi** bölümüne aşağıdaki özel başlatma betiğini ekleyin. Kümeyi oluştururken veya küme oluşturulduktan sonra başlangıç betiğini ekleyebilirsiniz. **Düğüm türü** için, betiğin yalnızca HBase bölge sunucularında yürütüldüğünden emin olmak Için **bölge sunucuları** ' nı seçin.


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. Kullanım kolaylığı için ortam değişkenini ayarlayın. `MYPASSWORD`Küme oturum açma parolasıyla değiştirerek aşağıdaki komutları düzenleyin. `MYCLUSTERNAME`Değerini HBase kümenizin adıyla değiştirin. Ardından komutları girin.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Mevcut HBase tablolarını listelemek için şu komutu kullanın:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. İki sütunlu aileler içeren yeni bir HBase tablosu oluşturmak için aşağıdaki komutu kullanın:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Şema JSon biçiminde sağlanır.
1. Bazı verileri eklemek için aşağıdaki komutu kullanın:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Base64,-d anahtarında belirtilen değerleri kodlayın. Örnekte:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ = =: Personal: Name
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) birden fazla (toplu) değer eklemenizi sağlar.

1. Bir satır almak için aşağıdaki komutu kullanın:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

HBase Rest hakkında daha fazla bilgi için bkz. [Apache HBase Başvuru Kılavuzu](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Thrift, HDInsight’ta HBase tarafından desteklenmez.
>
> Curl’ü veya WebHCat ile başka bir REST iletişimini kullanırken HDInsight küme yöneticisinin kullanıcı adı ve parolasını sağlayarak isteklerin kimliğini doğrulamanız gerekir. Ayrıca, sunucuya istek göndermek için kullanılan Tekdüzen Kaynak Tanımlayıcısı’nın (URI) bir parçası olarak küme adını kullanmanız gerekir:
>
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> Aşağıdakine benzer bir yanıt almanız gerekir:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>Küme durumunu denetleme

HDInsight içinde HBase, kümelerin izlenmesi için bir Web Kullanıcı Arabirimi ile birlikte gönderilir. Web Kullanıcı Arabirimini kullanarak istatistikler veya bölgeler hakkında bilgi isteyebilirsiniz.

**HBase Master Kullanıcı Arabirimi’ne erişmek için**

1. ' De, `https://CLUSTERNAME.azurehdinsight.net` `CLUSTERNAME` HBase Kümenizin adı olduğu gibi, ambarı Web Kullanıcı arabiriminde oturum açın.

1. Sol menüden **HBase** ' i seçin.

1. Sayfanın üst kısmındaki **hızlı bağlantılar** ' ı seçin, etkin Zookeeper düğümü bağlantısına gelin ve **HBase Master Kullanıcı arabirimi**' ni seçin.  Kullanıcı arabirimi başka bir tarayıcı sekmesinde açılır:

   :::image type="content" source="./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png" alt-text="HDInsight Apache HBase HMaster Kullanıcı arabirimi" border="false":::

   HBase Master Kullanıcı Arabirimi aşağıdaki bölümleri içerir:

   - Bölge sunucuları
   - Yedekleme yöneticileri
   - tablolarda
   - Görevler
   - Yazılım öznitelikleri

## <a name="cluster-recreation"></a>Küme yeniden oluşturma

Bir HBase kümesi silindikten sonra aynı varsayılan blob kapsayıcısını kullanarak başka bir HBase kümesi oluşturabilirsiniz. Yeni küme özgün kümede oluşturduğunuz HBase tablolarını seçer. Ancak Tutarsızlıklardan kaçınmak için, kümeyi silmeden önce HBase tablolarını devre dışı bırakmanızı öneririz. 

HBase komutunu kullanabilirsiniz `disable 'Contacts'` . 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, oluşturduğunuz HBase kümesini aşağıdaki adımlarla silin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Üstteki **arama** kutusuna **HDInsight** yazın.
1. **Hizmetler** altında **HDInsight kümeleri** ' ni seçin.
1. Görüntülenen HDInsight kümeleri listesinde, bu öğretici için oluşturduğunuz kümenin yanındaki **...** öğesine tıklayın.
1. **Sil**'e tıklayın. **Evet**'e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Apache HBase kümesi oluşturmayı öğrendiniz. Ve tablo oluşturma ve bu tablolardaki verileri HBase kabuğundan görüntüleme. Ayrıca, HBase tablolarındaki veriler üzerinde Hive sorgusu kullanmayı öğrendiniz. HBase C# REST API 'Lerini kullanarak bir HBase tablosu oluşturma ve tablodaki verileri alma. Daha fazla bilgi edinmek için şu makalelere bakın:

> [!div class="nextstepaction"]
> [HDInsight HBase 'e genel bakış](./apache-hbase-overview.md)
