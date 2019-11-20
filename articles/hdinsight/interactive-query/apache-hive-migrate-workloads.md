---
title: Azure HDInsight 3,6 Hive iş yüklerini HDInsight 'a geçirme 4,0
description: HDInsight 3,6 ' de Apache Hive iş yüklerini HDInsight 4,0 ' ye geçirmeyi öğrenin.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 1b270663a83461ecd777599fead9d717e93482c0
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930898"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3,6 Hive iş yüklerini HDInsight 'a geçirme 4,0

Bu belgede HDInsight 3,6 ' de Apache Hive ve LLAP iş yüklerinin HDInsight 4,0 ' ye nasıl geçirileceği gösterilmektedir. HDInsight 4,0, gerçekleştirilmiş görünümler ve sorgu sonucu önbelleği gibi daha yeni Hive ve LLAP özellikleri sağlar. İş yüklerinizi HDInsight 4,0 ' e geçirdiğinizde, Hive 3 ' ün HDInsight 3,6 ' de kullanılamayan daha yeni özelliklerinden yararlanabilirsiniz.

Bu makalede aşağıdaki konular ele alınmaktadır:

* Hive meta verilerinin HDInsight 'a geçirilmesi 4,0
* ACID ve ACID olmayan tabloların güvenli geçişi
* HDInsight sürümleri arasında Hive güvenlik ilkelerinin korunması
* HDInsight 3,6 ' den HDInsight 4,0 ' den sorgu yürütme ve hata ayıklama

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Apache Hive meta verileri HDInsight 'a geçirme 4,0

Hive 'nin bir avantajı, meta verileri bir dış veritabanına dışarı aktarma (Hive meta veri deposu olarak adlandırılır) özelliğidir. **Hive meta** veri tablosu, tablo depolama konumu, sütun adları ve tablo dizini bilgileri de dahil olmak üzere tablo istatistiklerini depolamaktan sorumludur. Meta veri deposu veritabanı şeması Hive sürümleri arasında farklılık gösterir. HDInsight 4,0 ile uyumlu olması için HDInsight 3,6 Hive meta veri deposunu yükseltmek üzere aşağıdakileri yapın.

1. Dış meta veri deposu 'nun yeni bir kopyasını oluşturun. HDInsight 3,6 ve HDInsight 4,0 farklı meta veri deposu şemaları gerektirir ve tek bir meta veri deposu paylaşamaz. HDInsight kümesine dış meta veri deposu ekleme hakkında daha fazla bilgi edinmek için bkz. [Azure HDInsight 'ta dış meta veri depoları kullanma](../hdinsight-use-external-metadata-stores.md) . 
2. Yürütme için düğüm türü olarak "baş düğümler" ile HDI 3,6 kümenize karşı bir betik eylemi başlatın. Aşağıdaki URI 'yi "Bash betiği URI 'SI" olarak işaretlenen metin kutusuna yapıştırın: https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh. "Bağımsız değişkenler" olarak işaretlenen metin kutusunda, **kopyalanmış** Hive meta veri deposu için sunucuadı, veritabanı, Kullanıcı adı ve parolayı boşluklarla ayırarak girin. ServerName ' i belirtirken ". database.windows.net" eklemeyin.

> [!Warning]
> HDInsight 3,6 meta veri şemasını HDInsight 4,0 şemasına dönüştüren yükseltme, geri alınamaz.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Hive tablolarını HDInsight 4,0 'e geçirme

Hive meta veri deposunu HDInsight 4,0 ' ye geçirmeye yönelik önceki adım kümesini tamamladıktan sonra, meta veri deposu 'nda kaydedilen tablolar ve veritabanları, `show tables` veya küme içinden `show databases` yürüterek HDInsight 4,0 kümesi içinden görünür olacaktır. HDInsight 4,0 kümelerinde sorgu yürütme hakkında bilgi için bkz. [HDInsight sürümleri arasında sorgu yürütme](#query-execution-across-hdinsight-versions) .

Ancak tablolardaki gerçek veriler, kümenin gerekli depolama hesaplarına erişimi olana kadar erişilebilir değildir. HDInsight 4,0 kümenizin eski HDInsight 3,6 kümeniz ile aynı verilere erişebildiğinizden emin olmak için aşağıdaki adımları izleyin:

1. Biçimlendirilip tanımlanmadığını kullanarak tablonuzun veya veritabanınızın Azure Depolama hesabını saptayın.
2. HDInsight 4,0 kümeniz zaten çalışıyorsa, Azure Depolama hesabını ambarı aracılığıyla kümeye ekleyin. HDInsight 4,0 kümesini henüz oluşturmadıysanız, Azure Storage hesabının birincil veya ikincil küme depolama hesabı olarak belirtildiğinden emin olun. HDInsight kümelerine depolama hesapları ekleme hakkında daha fazla bilgi için bkz. [HDInsight 'a ek depolama hesapları ekleme](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tablolar HDInsight 3,6 ve HDInsight 4,0 ' de farklı olarak değerlendirilir. Bu nedenle, farklı sürümlerin kümeleri için aynı tabloları paylaşamazsınız. HDInsight 3,6 ' i HDInsight 4,0 ile aynı anda kullanmak istiyorsanız, her sürüm için verilerin ayrı bir kopyasına sahip olmanız gerekir.

Hive iş yükünüz, ACID ve ACID olmayan tabloların bir karışımını içerebilir. HDInsight 3,6 (Hive 2) ve HDInsight 4,0 (Hive 3) üzerindeki Hive arasındaki bir temel fark, tablolar için ACID-uyumluluk ' dir. HDInsight 3,6 ' de, Hive ACID-Uyumluluk özelliğinin etkinleştirilmesi ek yapılandırma gerektirir, ancak HDInsight 4,0 tablolarında, varsayılan olarak ACID uyumludur. Geçişten önce gereken tek eylem, 3,6 kümesindeki ACID tablosuna göre büyük bir sıkıştırma çalıştırmak içindir. Hive görünümünden veya Beeline 'dan aşağıdaki sorguyu çalıştırın:

```bash
alter table myacidtable compact 'major';
```

HDInsight 3,6 ve HDInsight 4,0 ACID tabloları ACID değişimleri 'ı farklı şekilde anladığından bu sıkıştırma gereklidir. Düzenleme, tutarlılığı güvence altına alan temiz bir tablet uygular. [Hive geçiş belgelerinin](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) 4. bölümü, HDInsight 3,6 ACID tablolarının toplu sıkıştırmasına yönelik kılavuz içerir.

Meta veri geçişini ve düzenleme adımlarını tamamladıktan sonra, gerçek ambarı geçirebilirsiniz. Hive ambarı geçişini tamamladıktan sonra, HDInsight 4,0 ambarı aşağıdaki özelliklere sahip olur:

* HDInsight 3,6 ' deki dış tablolar HDInsight 4,0 ' de dış tablolar olacaktır
* HDInsight 3,6 ' de işlemsel olmayan yönetilen tablolar HDInsight 4,0 ' de dış tablolar olacaktır
* HDInsight 3,6 ' de işlem temelli yönetilen tablolar HDInsight 4,0 ' de yönetilecek tablolar olacaktır

Geçişi yürütmeden önce ambarınızın özelliklerini ayarlamanız gerekebilir. Örneğin, bazı tabloya üçüncü bir taraf (örneğin, HDInsight 3,6 kümesi) erişildiğini düşünüyorsanız, geçiş işlemi tamamlandıktan sonra bu tablo dış olmalıdır. HDInsight 4,0 ' de, tüm yönetilen tablolar işlem. Bu nedenle, HDInsight 4,0 ' deki yönetilen tablolara yalnızca HDInsight 4,0 kümeleri tarafından erişilmelidir.

Tablo özelliklerinizi doğru şekilde ayarladıktan sonra, SSH kabuğu 'nu kullanarak küme yayın düğümlerinden birindeki Hive ambarı geçiş aracını yürütün:

1. SSH kullanarak küme baş düğümüne 'a bağlanın. Yönergeler için bkz. [SSH kullanarak HDInsight 'A bağlanma](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. `sudo su - hive` çalıştırarak Hive kullanıcısı olarak bir oturum açma kabuğu açın
1. `ls /usr/hdp`yürüterek veri platformu yığın sürümünü belirleme. Bu, sonraki komutta kullanmanız gereken bir sürüm dizesi görüntüler.
1. Kabuktan aşağıdaki komutu yürütün. `${{STACK_VERSION}}` önceki adımdaki sürüm dizesiyle değiştirin:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Geçiş aracı tamamlandıktan sonra, Hive ambarınız HDInsight 4,0 ' de hazırlanacaktır. 

> [!Important]
> HDInsight 4,0 ' deki yönetilen tablolara (3,6 ' den geçirilen tablolar dahil) HDInsight 3,6 kümeleri de dahil olmak üzere diğer hizmetler veya uygulamalar tarafından erişilmemelidir.

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight sürümleri arasında güvenli Hive

HDInsight 3,6 ' den itibaren HDInsight, HDInsight Kurumsal Güvenlik Paketi (ESP) kullanarak Azure Active Directory tümleştirilir. ESP, küme içindeki belirli kaynakların izinlerini yönetmek için Kerberos ve Apache Ranger kullanır. HDInsight 3,6 ' de Hive 'e karşı dağıtılan Ranger ilkeleri, aşağıdaki adımlarla HDInsight 4,0 ' e geçirilebilir:

1. HDInsight 3,6 kümenizdeki Ranger Service Manager paneline gidin.
2. **HIVE** adlı ilkeye gidin ve ilkeyi bir JSON dosyasına dışarı aktarın.
3. Yeni kümede, verilen ilke JSON öğesinde başvurulan tüm kullanıcıların var olduğundan emin olun. Bir kullanıcıya, ilke JSON ' de başvurulmazsa, ancak yeni kümede yoksa, kullanıcıyı yeni kümeye ekleyin veya ilkeden başvuruyu kaldırın.
4. HDInsight 4,0 kümenizdeki **Ranger Service Manager** paneline gidin.
5. **HIVE** adlı ilkeye gidin ve adım 2 ' den Ranger ilkesi JSON 'sini içeri aktarın.

## <a name="query-execution-across-hdinsight-versions"></a>HDInsight sürümleri arasında sorgu yürütme

HDInsight 3,6 kümesi içinde Hive/LLAP sorgularını yürütmenin ve hata ayıklamanın iki yolu vardır. HiveCLI, bir komut satırı deneyimi sağlar ve tez görünümü/Hive görünümü GUI tabanlı bir iş akışı sağlar.

HDInsight 4,0 ' de HiveCLI, Beeline ile değiştirilmiştir. HiveCLI, Hiveserver 1 için bir Thrift istemcsahiptir ve Beeline, Hiveserver 2 ' ye erişim sağlayan bir JDBC istemcidedir. Diğer bir JDBC uyumlu veritabanı uç noktasına bağlanmak için Beeline de kullanılabilir. Beline, herhangi bir yükleme gerekmeden HDInsight 4,0 ' de kullanıma sunulmuştur.

HDInsight 3,6 ' de Hive sunucusu ile etkileşim için GUI istemcisi, ambarı Hive görünümüdür. HDInsight 4,0, Hive görünümünü Hortonçalışmadata Analytics Studio (DAS) ile değiştirir. DAS, HDInsight kümeleriyle teslim etmez ve resmi olarak desteklenen bir paket değildir. Bununla birlikte, aşağıdaki gibi DAS 'ler kümeye yüklenebilir:

Yürütme için düğüm türü olarak "baş düğümler" ile kümenize karşı bir betik eylemi başlatın. Aşağıdaki URI 'yi "Bash betiği URI 'SI" olarak işaretlenen metin kutusuna yapıştırın: https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

5 ila 10 dakika bekleyin, sonra şu URL 'YI kullanarak Data Analytics Studio 'Yu başlatın: https://\<clustername >. azurehdinsight. net/das/

DAS yüklendikten sonra, sorgular görüntüleyicisinde çalıştırdığınız sorguları görmüyorsanız, aşağıdaki adımları uygulayın:

1. [Das yüklemesinde sorun giderme için bu kılavuzda](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)açıklanan Hive, tez ve das için yapılandırma ayarlayın.
2. Aşağıdaki Azure depolama dizini yapılandırması ' nın sayfa Blobları olduğundan ve `fs.azure.page.blob.dirs`altında listelendiğinden emin olun:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Her iki headnode üzerinde de IV, Hive, tez ve DAS 'i yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 4,0 duyurusu](../hdinsight-version-release.md)
* [HDInsight 4,0 derin bakış](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID tabloları](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
