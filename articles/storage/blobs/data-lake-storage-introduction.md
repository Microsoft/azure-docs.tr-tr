---
title: Azure Data Lake Storage 2. giriş
description: Azure Data Lake Storage 2. bir genel bakış sağlar
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 99863f68c20a2f95dfc744e13a977bf3ccbbf639
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580438"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. giriş

Azure Data Lake Storage 2., [Azure Blob depolama](storage-blobs-introduction.md)üzerinde oluşturulmuş, büyük veri analizi için adanmış bir yetenekler kümesidir. Data Lake Storage 2., mevcut iki depolama hizmeti, Azure Blob depolama ve Azure Data Lake Storage 1. yeteneklerini yakınsama sonucudur. Dosya sistemi semantiği, dizin ve dosya düzeyi güvenliği ve ölçeği gibi [Azure Data Lake Storage 1.](https://docs.microsoft.com/azure/data-lake-store/index)özellikler, [Azure Blob depolama](storage-blobs-introduction.md)'dan düşük maliyetli, katmanlı depolama, yüksek kullanılabilirlik/olağanüstü durum kurtarma özellikleriyle birleştirilir.

## <a name="designed-for-enterprise-big-data-analytics"></a>Kurumsal büyük veri analizi için tasarlandı

Data Lake Storage 2. Azure Storage 'ı Azure 'da kurumsal veri oluşturma için temel oluşturur. Başlangıçtan itibaren yüzlerce Gigabit işleme petabaytlarca, çok büyük miktarlarda veriyi kolay bir şekilde yönetmenize olanak tanılarken, Data Lake Storage 2. birden fazla bilgi için başlangıç sürümünden tasarlanan.

Data Lake Storage 2. temel bir parçası, blob depolamaya [hiyerarşik bir ad alanının](data-lake-storage-namespace.md) eklenmesinin bir parçasıdır. Hiyerarşik ad alanı, etkili veri erişimi için nesneleri/dosyaları bir dizin hiyerarşisi halinde düzenler. Ortak bir nesne deposu adlandırma kuralı, hiyerarşik bir dizin yapısını taklit etmek için adında eğik çizgiler kullanır. Bu yapı Data Lake Storage 2. ile gerçek hale gelir. Dizini yeniden adlandırma veya silme gibi işlemler, dizinin ad önekini paylaşan tüm nesneleri listelemek ve işlemek yerine dizinde tek Atomik meta veri işlemleri haline gelir.

Geçmişte, bulut tabanlı analizler performans, yönetim ve güvenlik alanlarında tehlikeye atabilir. Data Lake Storage 2. aşağıdaki yollarla bu yönlerinin her birini ele alınmaktadır:

-   Verileri Analize bir önkoşul olarak kopyalamanız veya dönüştürmeniz gerekmiyorsa **performans** iyileştirilir. Hiyerarşik ad alanı, genel iş performansını artıran dizin yönetimi işlemlerinin performansını önemli ölçüde artırır.

-   Dosyaları dizinler ve alt dizinler aracılığıyla düzenleyebileceğiniz ve işleyebileceğiniz için **Yönetim** daha kolay hale getirir.

-   Dizinler veya tek dosyalarda POSIX izinleri tanımlayabilmeniz için **güvenlik güvenliği** uygulanabilir.

-   Data Lake Storage 2. düşük maliyetli [Azure Blob depolama alanı](storage-blobs-introduction.md)üzerinde oluşturulduğu için **maliyet verimliliği** mümkündür. Ek özellikler, Azure 'da büyük veri analizlerini çalıştırmaya yönelik toplam sahip olma maliyetini daha düşüktür.

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage 2. temel özellikleri

-   **Hadoop uyumlu erişim**: Data Lake Storage 2., verileri [Hadoop Dağıtılmış dosya sistemi (bir)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)ile yaptığınız gibi yönetmenizi ve erişmenize olanak tanır. Yeni [ABFS sürücüsü](data-lake-storage-abfs-driver.md) , Data Lake Storage 2. depolanan verilere erişmek için [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)ve [SQL veri ambarı](https://docs.microsoft.com/azure/sql-data-warehouse/) dahil olmak üzere tüm Apache Hadoop ortamlarında kullanılabilir.

-   **POSIX Izinlerinin bir üst kümesi**: Data Lake Gen2 için güvenlik modeli, Data Lake Storage 2. özgü bazı ayrıntı düzeyi Ile birlikte ACL 'LERI ve POSIX izinlerini destekler. Ayarlar, Depolama Gezgini veya Hive ve Spark gibi çerçeveler aracılığıyla yapılandırılabilir.

-   Uygun **maliyetli**: Data Lake Storage 2. düşük maliyetli depolama kapasitesi ve işlemler sunar. Tam yaşam döngüsü boyunca veri geçişleri olarak faturalandırma ücretleri, [Azure Blob depolama yaşam döngüsü](storage-lifecycle-management-concepts.md)gibi yerleşik özellikler aracılığıyla maliyetleri en düşük düzeyde tutmaya göre değişir.

-   **İyileştirilmiş sürücü**: ABFS sürücüsü özellikle büyük veri analizi için [iyileştirilmiştir](data-lake-storage-abfs-driver.md) . Karşılık gelen REST API 'Leri, uç nokta `dfs.core.windows.net`üzerinden ortaya çıkmış.

### <a name="scalability"></a>Ölçeklenebilirlik

Azure depolama, Data Lake Storage 2. veya blob depolama arabirimlerine erişip erişemeyeceğini tasarlayarak ölçeklenebilir. *Birçok eksabaytlarca veri*depolayıp sunabilir. Bu depolama alanı miktarı, saniye başına giriş/çıkış işlemi (ıOPS) düzeyinde Gigabit/saniye (Gbps) cinsinden ölçülen aktarım hızı ile kullanılabilir. Yalnızca kalıcılığın ötesinde, işleme hizmet, hesap ve dosya düzeylerinde ölçülen istek başına gecikme süreleriyle yürütülür.

### <a name="cost-effectiveness"></a>Maliyet verimliliği

Azure Blob depolama alanının en üstünde Data Lake Storage 2. oluşturmanın avantajlarından biri, depolama kapasitesi ve işlemlerinin düşük maliyetlidir. Diğer bulut depolama hizmetlerinden farklı olarak, Data Lake Storage 2. ' de depolanan verilerin analiz gerçekleştirilmeden önce taşınması veya dönüştürülmesi gerekmez. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage).

Ayrıca, [hiyerarşik ad alanı](data-lake-storage-namespace.md) gibi özellikler birçok analiz işinin genel performansını önemli ölçüde artırır. Bu performans artışı, aynı miktarda veriyi işlemek için daha az işlem gücü yapmanız gerektiği anlamına gelir ve bu, uçtan uca analiz işi için daha düşük toplam sahip olma maliyeti (TCO) ile sonuçlanır.

### <a name="one-service-multiple-concepts"></a>Tek hizmet, birden çok kavram

Data Lake Storage 2., Azure Blob depolama alanı üzerinde oluşturulmuş, büyük veri analizi için ek bir özelliktir. Analiz için veri Lakes oluşturmak ve çalıştırmak üzere Blobların mevcut platform bileşenlerinin avantajlarından yararlanan birçok avantaj olsa da, bu, aynı paylaşılan şeyleri açıklayan birden çok kavram oluşmasına neden olur.

Aşağıdakiler, farklı kavramlarla açıklandığı gibi eşdeğer varlıklardır. Aksi belirtilmedikçe, bu varlıklar doğrudan eş anlamlı değildir:

| Kavram                                | Üst düzey kuruluş | Alt düzey kuruluş                                            | Veri kapsayıcısı |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blob 'lar – genel amaçlı nesne depolaması | Kapsayıcı              | Sanal dizin (yalnızca SDK – atomik düzenleme sağlamaz) | Blob           |
| Azure Data Lake Storage 2. – analiz depolaması          | Kapsayıcı            | Dizin                                                           | Dosya           |

## <a name="supported-open-source-platforms"></a>Desteklenen açık kaynaklı platformlar

Birçok açık kaynak platformu Data Lake Storage 2. destekler. Bu platformlar aşağıdaki tabloda görüntülenir.

> [!NOTE]
> Yalnızca bu tabloda görünen sürümler desteklenir.

| Platform |  Desteklenen sürüm (ler) | Daha Fazla Bilgi |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Apache Hadoop bileşenleri ve sürümleri HDInsight ile kullanılabilen nelerdir?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2 + | [Apache Hadoop yayınları Arşivi](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Cloudera Enterprise 6. x sürüm notları](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 + | [Databricks Runtime sürümleri](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1. x + + | [Bulut veri erişimini yapılandırma](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="supported-azure-services"></a>Desteklenen Azure hizmetleri

Data Lake Storage Gen2, verileri almak, analiz yapmak ve görsel gösterimler oluşturmak için kullanabileceğiniz çeşitli Azure hizmetlerini destekler. Desteklenen Azure hizmetlerinin bir listesi için bkz. [Azure hizmetleriyle Azure Data Lake Storage tümleştirme](data-lake-storage-integrate-with-azure-services.md).

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelerde Data Lake Storage 2. ana kavramlarından bazıları açıklanır ve verilerinizden bilgi alma, erişme, yönetme ve bu bilgilerden bilgi elde etme hakkında bilgi edinin:

- [Hiyerarşik ad alanı](data-lake-storage-namespace.md)
- [Depolama hesabı oluşturma](data-lake-storage-quickstart-create-account.md)
- [Azure Data Lake Storage çoklu protokol erişimi](data-lake-storage-multi-protocol-access.md)
- [Azure hizmetleriyle Azure Data Lake Storage tümleştirin](data-lake-storage-integrate-with-azure-services.md);
