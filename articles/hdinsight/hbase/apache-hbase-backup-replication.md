---
title: Apache HBase için yedekleme & çoğaltma, Phoenix-Azure HDInsight
description: Azure HDInsight 'ta Apache HBase ve Apache Phoenix için yedekleme ve çoğaltmayı ayarlama
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 1d5bcf9c04ad02eaf297f8971aa0f4ff599888c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942994"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>HDInsight 'ta Apache HBase ve Apache Phoenix için yedekleme ve çoğaltma ayarlama

Apache HBase, veri kaybına karşı koruma için çeşitli yaklaşımları destekler:

* Klasörü Kopyala `hbase`
* Dışarı aktar ve Içeri aktar
* Tabloları Kopyala
* Anlık Görüntüler
* Çoğaltma

> [!NOTE]  
> Apache Phoenix meta verilerini HBase tablolarında depolar, böylece, HBase Sistem kataloğu tablolarını yedeklerken meta veriler yedeklenir.

Aşağıdaki bölümlerde bu yaklaşımların her biri için kullanım senaryosu açıklanır.

## <a name="copy-the-hbase-folder"></a>HBase klasörünü Kopyala

Bu yaklaşımda, bir tablo veya sütun ailelerinin bir alt kümesini seçmeden tüm HBase verilerini kopyalayabileceksiniz. Sonraki yaklaşımlar daha fazla denetim sağlar.

HDInsight 'ta HBase, küme oluştururken seçilen varsayılan depolamayı kullanır, Azure Storage blob 'ları veya Azure Data Lake Storage. Her iki durumda da HBase, verilerini ve meta veri dosyalarını aşağıdaki yolda depolar:

`/hbase`

* Bir Azure depolama hesabında, `hbase` klasörü blob kapsayıcısının kökünde bulunur:

  `wasbs://<containername>@<accountname>.blob.core.windows.net/hbase`

* Azure Data Lake Storage, klasör, `hbase` bir küme sağlanırken belirttiğiniz kök yolun altında bulunur. Bu kök yol `clusters` , genellikle HDInsight kümenizin ardından adlı bir alt klasörü olan bir klasörüne sahiptir:

  `/clusters/<clusterName>/hbase`

Her iki durumda da `hbase` klasör, HBase 'nin diske temizlenen tüm verileri içerir, ancak bellek içi verileri içermeyebilir. HBase verilerinin doğru bir gösterimi olarak bu klasöre güvenebilmeniz için önce kümeyi kapatmanız gerekir.

Kümeyi sildikten sonra, verileri yerinde bırakabilir ya da verileri yeni bir konuma kopyalayabilirsiniz:

* Geçerli depolama konumunu işaret eden yeni bir HDInsight örneği oluşturun. Yeni örnek, tüm mevcut verilerle oluşturulur.

* `hbase`Klasörü farklı bir Azure Storage blob kapsayıcısına veya Data Lake Storage konuma kopyalayın ve ardından bu verilerle yeni bir küme başlatın. Azure depolama için [AzCopy](../../storage/common/storage-use-azcopy-v10.md)kullanın ve Data Lake Storage [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)kullanın.

## <a name="export-then-import"></a>Dışarı aktar ve Içeri aktar

Kaynak HDInsight kümesinde, kaynak tablodaki varsayılan bağlı depolamaya veri aktarmak için [Export yardımcı programını](https://hbase.apache.org/book.html#export) (HBase ile birlikte) kullanın. Ardından dışarı aktarılan klasörü hedef depolama konumuna kopyalayabilir ve hedef HDInsight kümesinde [Içeri aktarma yardımcı programını](https://hbase.apache.org/book.html#import) çalıştırabilirsiniz.

Tablo verilerini dışarı aktarmak için ilk olarak kaynak HDInsight kümenizin baş düğümüne SSH ekleyin ve ardından aşağıdaki `hbase` komutu çalıştırın:

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"
```

Dışarı aktarma dizini zaten mevcut olmamalıdır. Tablo adı büyük/küçük harfe duyarlıdır.

Tablo verilerini içe aktarmak için, hedef HDInsight kümenizin baş düğümüne SSH ekleyin ve ardından aşağıdaki `hbase` komutu çalıştırın:

```console
hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"
```

Tablo zaten var olmalıdır.

Varsayılan depolama alanına veya bağlı depolama seçeneklerinden herhangi birine tam verme yolunu belirtin. Örneğin, Azure depolama 'da:

`wasbs://<containername>@<accountname>.blob.core.windows.net/<path>`

Azure Data Lake Storage 2., sözdizimi şöyledir:

`abfs://<containername>@<accountname>.dfs.core.windows.net/<path>`

Azure Data Lake Storage 1., sözdizimi şöyledir:

`adl://<accountName>.azuredatalakestore.net:443/<path>`

Bu yaklaşım tablo düzeyinde ayrıntı düzeyi sağlar. Ayrıca, dahil edilecek satırlar için bir tarih aralığı belirtebilirsiniz. Bu işlem, işlemi artımlı olarak gerçekleştirmenize olanak tanır. Her tarih, UNIX dönemi sonrasında milisaniye cinsindendir.

```console
hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>
```

Dışarı aktarılacak her bir satırın sürüm sayısını belirtmeniz gerektiğini unutmayın. Tarih aralığındaki tüm sürümleri dahil etmek için, 100000 gibi `<numberOfVersions>` olası en yüksek satır sürümlerindekinden daha büyük bir değere ayarlayın.

## <a name="copy-tables"></a>Tabloları Kopyala

[Copytable yardımcı programı](https://hbase.apache.org/book.html#copy.table) , verileri bir kaynak tablosundan, satır satırına, kaynak ile aynı şemaya sahip mevcut bir hedef tabloya kopyalar. Hedef tablo aynı kümede veya farklı bir HBase kümesinde olabilir. Tablo adları büyük/küçük harfe duyarlıdır.

Küme içinde CopyTable ' ı, kaynak HDInsight kümenizin baş düğümüne SSH olarak kullanmak ve ardından şu `hbase` komutu çalıştırın:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>
```

Farklı bir kümedeki bir tabloya kopyalamak üzere CopyTable kullanmak için, `peer` anahtarı hedef kümenin adresiyle ekleyin:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>
```

Hedef adres, aşağıdaki üç bölümden oluşur:

`<destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>`

* `<ZooKeeperQuorum>` Apache ZooKeeper düğümlerinin virgülle ayrılmış listesidir, örneğin:

    zk0-hluc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net, ZK4-haloc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net, zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` HDInsight 'ta varsayılan değer 2181 ' `<ZnodeParent>` dir ve şu `/hbase-unsecure` şekilde tamamlanır `<destinationAddress>` :

    zk0-haloc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net, ZK4-haloc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net, zk3-haloc 2.54 o2oqawzlwevlfxgay2500xtg. DX. Internal. cloudapp. net: 2181:/HBase-Unsecure

HDInsight kümeniz için bu değerleri alma hakkında ayrıntılı bilgi için bu makaledeki [Apache ZooKeeper çekirdek listesini el Ile toplama](#manually-collect-the-apache-zookeeper-quorum-list) bölümüne bakın.

CopyTable yardımcı programı, kopyalanacak satırların zaman aralığını belirtmek ve kopyalanacak bir tablodaki sütun ailelerinin alt kümesini belirtmek için de parametreleri destekler. CopyTable tarafından desteklenen parametrelerin tüm listesini görmek için, hiçbir parametre olmadan CopyTable komutunu çalıştırın:

```console
hbase org.apache.hadoop.hbase.mapreduce.CopyTable
```

CopyTable, hedef tabloya kopyalanacak kaynak tablo içeriğinin tamamını tarar. Bu, CopyTable yürütülürken HBase kümenizin performansını azaltabilir.

> [!NOTE]  
> Tablolar arasında veri kopyalamayı otomatik hale getirmek için `hdi_copy_table.sh` GitHub 'Daki [Azure HBase utils](https://github.com/Azure/hbase-utils/tree/master/replication) deposunda betiğe bakın.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Apache ZooKeeper çekirdek listesini el ile topla

Her iki HDInsight kümesi de aynı sanal ağ içinde olduğunda, daha önce açıklandığı gibi iç konak adı çözümlemesi otomatiktir. Bir VPN Gateway bağlı iki ayrı sanal ağda HDInsight kümeleri için CopyTable kullanmak istiyorsanız, çekirdekte Zookeeper düğümlerinin ana bilgisayar IP adreslerini sağlamanız gerekir.

Çekirdek ana bilgisayar adlarını almak için aşağıdaki kıvrımlı komutunu çalıştırın:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"
```

Kıvrımlı komutu, HBase yapılandırma bilgilerini içeren bir JSON belgesi alır ve GREP komutu yalnızca "HBase. Zookeeper. Quorum" girişini döndürür, örneğin:

```output
"hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"
```

Çekirdek ana bilgisayar adları değeri, iki nokta üst üste sağ tarafındaki tüm dizedir.

Bu konaklara ait IP adreslerini almak için, önceki listede bulunan her bir konak için aşağıdaki kıvrımlı komutunu kullanın:

```console
curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"
```

Bu kıvrımlı komutunda, `<zookeeperHostFullName>` örnek gibi bir ZooKeeper konağının tam DNS adıdır `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net` . Komutun çıktısı, belirtilen konağın IP adresini içerir, örneğin:

`100    "ip" : "10.0.0.9",`

Çekirdekte tüm ZooKeeper düğümlerinin IP adreslerini topladıktan sonra hedef adresi yeniden derleyin:

`<destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>`

Örneğimizde:

`<destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure`

## <a name="snapshots"></a>Anlık Görüntüler

[Anlık görüntüler](https://hbase.apache.org/book.html#ops.snapshots) , HBase veri deposundaki verilerin bir zaman içinde yedeklenmesini sağlar. Anlık görüntü işlemi etkin bir şekilde bir meta veri işlemi olduğu için anlık görüntüler en düşük düzeyde ek olur ve saniyede tamamlanır. Anlık görüntü sırasında gerçek veri kopyalanmaz. Anlık görüntüler, güncelleştirme, silme ve eklemelerin hepsi yeni veri olarak temsil edildiği için,. Bir anlık görüntüyü aynı kümeye geri yükleyebilir (*kopyalayabilir*) veya bir anlık görüntüyü başka bir kümeye aktarabilirsiniz.

Bir anlık görüntü oluşturmak için HDInsight HBase kümenizin baş düğümüne SSH ve `hbase` kabuğu başlatın:

```console
hbase shell
```

HBase kabuğu 'nda, ve bu anlık görüntünün adlarıyla birlikte Snapshot komutunu kullanın:

```console
snapshot '<tableName>', '<snapshotName>'
```

Bir anlık görüntüyü kabukta ada göre geri yüklemek için `hbase` , önce tabloyu devre dışı bırakın, sonra anlık görüntüyü geri yükleyin ve tabloyu yeniden etkinleştirin:

```console
disable '<tableName>'
restore_snapshot '<snapshotName>'
enable '<tableName>'
```

Bir anlık görüntüyü yeni bir tabloya geri yüklemek için clone_snapshot kullanın:

```console
clone_snapshot '<snapshotName>', '<newTableName>'
```

Bir anlık görüntüyü başka bir küme tarafından kullanılmak üzere bir anlık görüntü olarak dışarı aktarmak için önce daha önce açıklandığı gibi anlık görüntüyü oluşturun ve ardından ExportSnapshot yardımcı programını kullanın. Bu yardımcı programı SSH oturumunun içinden, kabukta değil baş düğüme çalıştırın `hbase` :

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>
```

, `<hdfsHBaseLocation>` Kaynak kümeniz tarafından erişilebilen depolama konumlarından herhangi biri olabilir ve hedef kümeniz tarafından kullanılan HBase klasörüne işaret etmelidir. Örneğin, kaynak kümenize bağlı ikincil bir Azure depolama hesabınız varsa ve bu hesap hedef kümenin varsayılan depolaması tarafından kullanılan kapsayıcıya erişim sağlıyorsa, şu komutu kullanabilirsiniz:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Kaynak kümenize bağlı ikincil bir Azure depolama hesabınız yoksa veya kaynak kümeniz şirket içi bir kümeniz (veya HDI olmayan bir küme ise), HDI kümenizin depolama hesabına erişmeye çalıştığınızda yetkilendirme sorunlarıyla karşılaşabilirsiniz. Bu sorunu çözmek için, aşağıdaki örnekte gösterildiği gibi depolama hesabınıza ait anahtarı bir komut satırı parametresi olarak belirtin. Azure portal depolama hesabınıza anahtarı alabilirsiniz.

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.myaccount.blob.core.windows.net=mykey -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'
```

Hedef kümeniz bir ADLS Gen 2 kümenizle, ADLS Gen 2 tarafından kullanılan yapılandırmaların ayarlanmasını sağlamak için yukarıdaki komutu değiştirin:

```console
hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -Dfs.azure.account.key.<account_name>.dfs.core.windows.net=<key> -Dfs.azure.account.auth.type.<account_name>.dfs.core.windows.net=SharedKey -Dfs.azure.always.use.https.<account_name>.dfs.core.windows.net=false -Dfs.azure.account.keyprovider.<account_name>.dfs.core.windows.net=org.apache.hadoop.fs.azurebfs.services.SimpleKeyProvider -snapshot 'Snapshot1' -copy-to 'abfs://<container>@<account_name>.dfs.core.windows.net/hbase'
```

Anlık görüntü verildikten sonra, hedef kümenin baş düğümüne SSH ekleyin ve `restore_snapshot` daha önce açıklandığı gibi komutunu kullanarak anlık görüntüyü geri yükleyin.

Anlık görüntüler, komut sırasında bir tablonun tüm yedeklemesini sağlar `snapshot` . Anlık görüntüler, Windows 'un zaman içinde Artımlı anlık görüntüler gerçekleştirme veya anlık görüntüye eklenecek sütun ailelerinin alt kümelerini belirtme olanağı sağlamaz.

## <a name="replication"></a>Çoğaltma

[HBase çoğaltma](https://hbase.apache.org/book.html#_cluster_replication) , kaynak kümede en az ek yük ile zaman uyumsuz bir mekanizma kullanarak bir kaynak kümeden bir hedef kümeye işlem otomatik olarak gönderir. HDInsight 'ta, şu durumlarda kümeler arasında çoğaltma ayarlayabilirsiniz:

* Kaynak ve hedef kümeler aynı sanal ağ içinde.
* Kaynak ve hedef kümeleri, bir VPN ağ geçidiyle bağlı farklı sanal ağlarlarlardır, ancak her iki küme de aynı coğrafi konumda bulunur.
* Kaynak kümesi ve hedef kümeleri, bir VPN ağ geçidiyle bağlanmış farklı sanal ağlarlarlardır ve her küme farklı bir coğrafi konumda bulunur.

Çoğaltmayı ayarlamak için genel adımlar şunlardır:

1. Kaynak kümede, tabloları oluşturun ve verileri doldurun.
2. Hedef kümede, kaynak tablonun şemasıyla boş hedef tablolar oluşturun.
3. Hedef kümeyi kaynak kümeye bir eş olarak kaydedin.
4. İstenen kaynak tablolarında çoğaltmayı etkinleştirin.
5. Mevcut verileri kaynak tablolardan hedef tablolara kopyalayın.
6. Çoğaltma, yeni veri değişikliklerini kaynak tablolardaki hedef tablolara otomatik olarak kopyalar.

HDInsight üzerinde çoğaltmayı etkinleştirmek için, çalışan kaynak HDInsight kümenize bir betik eylemi uygulayın. Kümenizde çoğaltmayı etkinleştirmeye veya Azure Kaynak Yönetimi şablonları kullanılarak sanal ağlarda oluşturulan örnek kümeler üzerinde çoğaltma ile denemeler yapmak için bkz. [Apache HBase çoğaltmasını yapılandırma](apache-hbase-replication.md). Bu makalede ayrıca Phoenix meta verilerinin çoğaltılmasını etkinleştirmeye yönelik yönergeler de yer alır.

## <a name="next-steps"></a>Sonraki adımlar

* [Apache HBase çoğaltmasını yapılandırma](apache-hbase-replication.md)
* [HBase Içeri ve dışarı aktarma yardımcı programıyla çalışma](/archive/blogs/data_otaku/working-with-the-hbase-import-and-export-utility)
