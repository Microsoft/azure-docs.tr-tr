---
title: Apache Hadoop bileşenleri ve sürümleri-Azure HDInsight
description: Azure HDInsight 'ta Apache Hadoop bileşenleri ve sürümleri hakkında bilgi edinin.
keywords: Hadoop sürümleri, Hadoop ekosistemi bileşenleri, Hadoop bileşenleri, Hadoop sürümünü denetleme
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 598f423bdb15b1d4975633db19c28b399fde484b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166794"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>HDInsight ile kullanılabilen Apache Hadoop bileşenleri ve sürümleri nelerdir?

Microsoft Azure HDInsight ve Kurumsal Güvenlik Paketi [Apache Hadoop](https://hadoop.apache.org/) ekosistem bileşenleri ve sürümleri hakkında bilgi edinin. Ayrıca, HDInsight 'ta Hadoop bileşen sürümlerini nasıl denetleyeceğinizi öğrenin.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Farklı HDInsight sürümleriyle kullanılabilen Apache Hadoop bileşenleri

Azure HDInsight, dilediğiniz zaman dağıtılabilecek birden çok Hadoop küme sürümünü destekler. 4 Nisan 2017 itibariyle, Azure HDInsight tarafından kullanılan varsayılan küme sürümü 3,6 ' dir.

HDInsight küme sürümleriyle ilişkili bileşen sürümleri aşağıdaki tabloda listelenmiştir: 

> [!NOTE]  
> HDInsight hizmeti için varsayılan sürüm bildirimde bulunulmadan değişebilir. Bir sürüm bağımlılığıdır varsa, Azure PowerShell ve Azure klasik CLı ile .NET SDK ile kümelerinizi oluştururken HDInsight sürümünü belirtin.

| Bileşen              | HDInsight 4.0 | HDInsight 3,6 (varsayılan)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop ve YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0, 1.2.1                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | desteklemez         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (aşağıdaki nota bakın) |
| Apache ambarı          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.0                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Sistem performansına ilişkin hususlar nedeniyle, 0,10 2019 Mart 'da Kafka sürüm için destek süresi sona eriyordu.

## <a name="check-for-current-hadoop-component-version-information"></a>Geçerli Hadoop bileşeni sürüm bilgilerini denetle

HDInsight küme sürümleriyle ilişkili Hadoop ekosistemi bileşen sürümleri, HDInsight güncelleştirmeleriyle değiştirilebilir. Hadoop bileşenlerini denetlemek ve bir küme için hangi sürümlerin kullanıldığını doğrulamak için, ambarı REST API kullanın. **Getcomponentınformation** komutu, hizmet bileşenleriyle ilgili bilgileri alır. Ayrıntılar için bkz. [Apache ambarı belgeleri](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Sürüm notları

HDInsight 'un en son sürümlerinde ek sürüm notları için bkz. [HDInsight sürüm notları](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>Desteklenen HDInsight sürümleri

Aşağıdaki tablolar HDInsight sürümlerini listeler. Destek süresi ve kullanımdan kaldırma tarihleri bilindiğinde de sağlanır.

### <a name="available-versions"></a>Kullanılabilir sürümler

Aşağıdaki tabloda, Azure portal bulunan HDInsight sürümlerinin yanı sıra PowerShell ve .NET SDK gibi diğer dağıtım yöntemleri listelenmiştir.

| HDInsight sürümü | VM IŞLETIM SISTEMI | Sürüm tarihi | Destek sona erme tarihi | Emeklilik tarihi | Yüksek kullanılabilirlik |  Azure portal kullanılabilirliği | 
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 Eylül 2018 | | |Yes |Yes |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4 Nisan 2017 | 30 Haziran 2020 |31 Aralık 2020 |Yes |Yes |


> [!NOTE]  
> Bir sürüm desteğinin süresi dolduktan sonra, Microsoft Azure portal aracılığıyla kullanılamayabilir. Ancak, Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) komutu ve .NET SDK 'nın sürüm kullanımdan kaldırma tarihine kadar `Version` parametresi kullanılarak küme sürümleri kullanılabilir olmaya devam eder.
>

### <a name="retired-versions"></a>Kullanımdan kaldırılan sürümler

Aşağıdaki tabloda, Azure portal mevcut **olmayan** HDInsight sürümleri listelenmektedir.

| HDInsight sürümü | HDP sürümü | VM IŞLETIM SISTEMI | Sürüm tarihi | Destek sona erme tarihi | Emeklilik tarihi | Yüksek kullanılabilirlik |  Azure portal kullanılabilirliği | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |30 Eylül 2016 |5 Eylül 2017 |28 Haziran 2018 |Yes |Hayır |
| HDInsight 3,4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 Mart 2016 |29 Aralık 2016 |9 Ocak 2018 |Yes |Hayır |
| HDInsight 3,3 |HDP 2.3 |Windows Server 2012 R2 |2 Aralık 2015 |27 Haziran 2016 |31 Temmuz 2018 |Yes |Hayır |
| HDInsight 3,3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 Aralık 2015 |27 Haziran 2016 |31 Temmuz 2017 |Yes |Hayır |
| HDInsight 3,2 |HDP 2.2 |Ubuntu 12,04 LTS veya Windows Server 2012 R2 |18 Şubat 2015 |1 Mart 2016 |1 Nisan 2017 |Yes |Hayır |
| HDInsight 3,1 |HDP 2.1 |Windows Server 2012 R2 |24 Haziran 2014 |18 Mayıs 2015 |30 Haziran 2016 |Yes |Hayır |
| HDInsight 3,0 |HDP 2.0 |Windows Server 2012 R2 |11 Şubat 2014 |17 Eylül 2014 |30 Haziran 2015 |Yes |Hayır |
| HDInsight 2,1 |HDP 1.3 |Windows Server 2012 R2 |28 Ekim 2013 |12 Mayıs 2014 |31 Mayıs 2015 |Yes |Hayır |
| HDInsight 1,6 |HDP 1.1 | |28 Ekim 2013 |26 Nisan 2014 |31 Mayıs 2015 |Hayır |Hayır |

> [!NOTE]  
> İki baş düğümü olan yüksek oranda kullanılabilir kümeler, HDInsight sürüm 2,1 ve üzeri için varsayılan olarak dağıtılır. Bunlar HDInsight sürüm 1,6 kümeleri için kullanılamaz.

## <a name="enterprise-security-package-for-hdinsight"></a>HDInsight için Kurumsal Güvenlik Paketi

Kurumsal Güvenlik, HDInsight kümenize küme iş akışı oluşturma işlemi kapsamında ekleyebileceğiniz isteğe bağlı bir pakettir. Kurumsal Güvenlik Paketi şunları destekler:

- Kimlik doğrulaması için Active Directory ile tümleştirme.

    Geçmişte, yalnızca yerel yönetici kullanıcısı ve yerel bir SSH kullanıcısı ile HDInsight kümeleri oluşturabilirsiniz. Yerel yönetici kullanıcı tüm dosyalara, klasörlere, tablolara ve sütunlara erişebilir.  Kurumsal Güvenlik Paketi ile, HDInsight kümelerini IaaS üzerinde şirket içi Active Directory, Azure Active Directory Domain Services veya Active Directory dahil olmak üzere kendi Active Directory tümleştirerek rol tabanlı erişim denetimini etkinleştirebilirsiniz sanal makine. Kümedeki etki alanı yöneticisi, kullanıcılara kümeye erişmek için kendi şirket (etki alanı) Kullanıcı adını ve parolasını kullanmalarına izin verebilir. 

    Daha fazla bilgi için bkz.

    - [Etki alanına katılmış HDInsight kümeleriyle güvenliği Apache Hadoop bir giriş](./domain-joined/hdinsight-security-overview.md)
    - [HDInsight 'ta Azure etki alanına katılmış Apache Hadoop kümeleri planlayın](./domain-joined/apache-domain-joined-architecture.md)
    - [Etki alanına katılmış korumalı alan ortamını yapılandırma](./domain-joined/apache-domain-joined-configure.md)
    - [Azure Active Directory Domain Services kullanarak etki alanına katılmış HDInsight kümelerini yapılandırma](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Veri yetkilendirmesi

  - Hive, Spark SQL ve Yarn kuyrukları için yetkilendirme için Apache Ranger ile tümleştirme.
  - Dosya ve klasörlerde erişim denetimi ayarlayabilirsiniz.

    Daha fazla bilgi için bkz.

  - [Etki alanına katılmış HDInsight 'ta Apache Hive ilkeleri yapılandırma](./domain-joined/apache-domain-joined-run-hive.md)

- Erişimleri ve yapılandırılan ilkeleri izlemek için Denetim günlüklerini görüntüleyin. 

### <a name="supported-cluster-types"></a>Desteklenen küme türleri

Şu anda yalnızca aşağıdaki küme türleri Kurumsal Güvenlik Paketi destekler:

- Hadoop (yalnızca HDInsight 3,6)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Azure Data Lake Storage için destek

Kurumsal Güvenlik Paketi, hem birincil depolama hem de eklenti depolama alanı olarak Azure Data Lake Storage kullanımını destekler.

### <a name="pricing-and-service-level-agreement"></a>Fiyatlandırma ve hizmet düzeyi sözleşmesi

Kurumsal Güvenlik Paketi için fiyatlandırma ve SLA hakkında daha fazla bilgi için bkz. [HDInsight fiyatlandırması](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight küme sürümleri için hizmet düzeyi sözleşmesi

Hizmet düzeyi sözleşmesi (SLA), _destek penceresi_bakımından tanımlanmıştır. Destek penceresi, bir HDInsight küme sürümünün Microsoft Müşteri Hizmetleri ve desteği tarafından desteklendiği süredir. Sürümün süresi geçen bir _destek sona erme tarihi_ varsa, HDInsight kümesi Destek penceresinin dışındadır. Belirtilen HDInsight sürüm X için destek sona erme tarihi (daha yeni bir X + 1 sürümü kullanılabilir olduktan sonra), daha sonra şu şekilde hesaplanır:  

* Formül 1: HDInsight kümesi sürüm X 'in yayımlandığı tarihe 180 gün ekleyin.
* Formül 2: HDInsight kümesi sürüm X + 1 ' de Azure portal tarihi ' nde kullanılabilir hale geldiğinde tarihe 90 gün ekleyin.

_Emeklilik tarihi_ , HDInsight 'ta küme sürümünün oluşturuoluşturuoluşturulamadığından tarih olur. 31 Temmuz 2017 ' den itibaren, bir HDInsight kümesini kullanımdan kaldırma tarihinden sonra yeniden boyutlandıramazsınız. 

> [!NOTE]  
> HDInsight Windows kümeleri (2,1, 3,0, 3,1, 3,2 ve 3,3 sürümleri dahil), Windows Server 64 R2 'nin 2012 bit sürümünü kullanan Azure Konuk işletim sistemi ailesi sürüm 4 ' te çalışır. Azure Konuk işletim sistemi ailesi sürüm 4, 4,0, 4,5, 4.5.1 ve 4.5.2 sürümlerinin .NET Framework destekler.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>, HDInsight sürümleriyle ilişkili hortonçalışmalar sürüm notları

Bu bölümde, HDInsight ile kullanılan Hortonçalışmalar veri platformu dağıtımları ve Apache bileşenleri için sürüm notlarına bağlantılar sağlanır.
* HDInsight küme sürümü 4,0, [Hortonçalışmalar Data Platform 3,0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) ' i temel alan bir Hadoop dağıtımı kullanır
* HDInsight küme sürümü 3,6, [Hortonçalışmalar veri platformu 2,6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html)' ni temel alan bir Hadoop dağıtımı kullanır.
* HDInsight küme sürümü 3,5, [Hortonçalışmalar veri platformu 2,5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html)' ni temel alan bir Hadoop dağıtımı kullanır. HDInsight kümesi sürüm 3,5, Azure portal oluşturulan _varsayılan_ Hadoop kümesidir.
* HDInsight küme sürümü 3,4, [Hortonçalışmalar veri platformu 2,4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)' ni temel alan bir Hadoop dağıtımı kullanır.
* HDInsight küme sürümü 3,3, [Hortonçalışmalar veri platformu 2,3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)' ni temel alan bir Hadoop dağıtımı kullanır.

  * [Apache Storm sürüm notları](https://storm.apache.org/2015/11/05/storm0100-released.html) Apache Web sitesinde bulunabilir.
  * [Apache Hive sürüm notları](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) Apache Web sitesinde bulunabilir.
* HDInsight küme sürümü 3,2, [Hortonçalışmalar veri platformu 2,2][hdp-2-2]' ni temel alan bir Hadoop dağıtımı kullanır.

  * Belirli Apache bileşenleri için sürüm notları şu şekilde kullanılabilir: [Hive 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), t, [Yarn 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [ortak](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [ambarı 2,0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [fırtınası](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)0.9.3 ve [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight küme sürümü 3,1, [Hortonçalışmalar veri platformu 2.1.7][hdp-2-1-7]tabanlı bir Hadoop dağıtımı kullanır. Kasım 7, 2014 tarihinden önce oluşturulan HDInsight 3,1 kümeleri, [Hortonçalışmalar veri platformu 2.1.1][hdp-2-1-1]tabanlıdır.
* HDInsight küme sürümü 3,0, [Hortonçalışmalar veri platformu 2,0][hdp-2-0-8]' ni temel alan bir Hadoop dağıtımı kullanır.
* HDInsight küme sürümü 2,1, [Hortonçalışmalar veri platformu 1,3][hdp-1-3-0]' ni temel alan bir Hadoop dağıtımı kullanır.
* HDInsight küme sürümü 1,6, [Hortonçalışmalar veri platformu 1,1][hdp-1-1-0]' ni temel alan bir Hadoop dağıtımı kullanır.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Kümeler için varsayılan düğüm yapılandırması ve sanal makine boyutları

Kümeniz için seçilecek sanal makine SKU 'Larının hakkında daha fazla bilgi için bkz. [Azure HDInsight küme yapılandırması ayrıntıları](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Sonraki adımlar
- [HDInsight üzerinde Apache Hadoop, Spark ve daha fazlası için küme kurulumu](hdinsight-hadoop-provision-linux-clusters.md)
- [Windows BILGISAYARDAN HDInsight üzerinde Apache Hadoop çalışma](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
