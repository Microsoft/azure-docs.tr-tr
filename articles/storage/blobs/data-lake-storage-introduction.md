---
title: Azure Data Lake Storage 2. giriş
description: Azure Data Lake Storage 2. bir giriş okuyun. Temel özellikleri öğrenin. Desteklenen BLOB depolama özelliklerini, Azure hizmet tümleştirmelerini ve platformları inceleyin.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 1c4d04e25bf8f7d981c998baafb468f04b66eaf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879907"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2. Nesil'e giriş

Azure Data Lake Storage 2., [Azure Blob depolama](storage-blobs-introduction.md)üzerinde oluşturulmuş, büyük veri analizi için adanmış bir yetenekler kümesidir. 

Data Lake Storage 2., Azure Blob depolama ile [Azure Data Lake Storage 1.](../../data-lake-store/index.yml) yeteneklerini söyleyecektir. Örneğin, Data Lake Storage 2. dosya sistemi semantiğini, dosya düzeyi güvenliği ve ölçeği sağlar. Bu yetenekler BLOB depolama üzerinde oluşturulduğundan, yüksek kullanılabilirlik/olağanüstü durum kurtarma özellikleri sayesinde düşük maliyetli, katmanlı depolama alanı da alacaksınız.

## <a name="designed-for-enterprise-big-data-analytics"></a>Kurumsal büyük veri analizi için tasarlandı

Data Lake Storage 2. Azure Storage 'ı Azure 'da kurumsal veri oluşturma için temel oluşturur. Başlangıçtan itibaren yüzlerce Gigabit işleme petabaytlarca, çok büyük miktarlarda veriyi kolay bir şekilde yönetmenize olanak tanılarken, Data Lake Storage 2. birden fazla bilgi için başlangıç sürümünden tasarlanan.

Data Lake Storage 2. temel bir parçası, blob depolamaya [hiyerarşik bir ad alanının](data-lake-storage-namespace.md) eklenmesinin bir parçasıdır. Hiyerarşik ad alanı, etkili veri erişimi için nesneleri/dosyaları bir dizin hiyerarşisi halinde düzenler. Ortak bir nesne deposu adlandırma kuralı, hiyerarşik bir dizin yapısını taklit etmek için adında eğik çizgiler kullanır. Bu yapı Data Lake Storage 2. ile gerçek hale gelir. Dizini yeniden adlandırma veya silme gibi işlemler, dizinde tek Atomik meta veri işlemleri haline gelir. Dizinin ad önekini paylaşan tüm nesneleri listelemeye ve işlemeye gerek yoktur.

Data Lake Storage 2. blob Storage üzerinde yapılar ve aşağıdaki yollarla performansı, yönetimi ve güvenliği geliştirir:

-   Verileri Analize bir önkoşul olarak kopyalamanız veya dönüştürmeniz gerekmiyorsa **performans** iyileştirilir. Blob depolamada düz ad alanı ile karşılaştırıldığında, hiyerarşik ad alanı, genel iş performansını artıran dizin yönetimi işlemlerinin performansını önemli ölçüde artırır.

-   Dosyaları dizinler ve alt dizinler aracılığıyla düzenleyebileceğiniz ve işleyebileceğiniz için **Yönetim** daha kolay hale getirir.

-   Dizinler veya tek dosyalarda POSIX izinleri tanımlayabilmeniz için **güvenlik güvenliği** uygulanabilir.

Ayrıca, düşük maliyetli [Azure Blob depolama](storage-blobs-introduction.md)alanının üzerine inşa edildiği için Data Lake Storage 2. çok maliyetli hale gelir. Ek özellikler, Azure 'da büyük veri analizlerini çalıştırmaya yönelik toplam sahip olma maliyetini daha düşüktür.

## <a name="key-features-of-data-lake-storage-gen2"></a>Data Lake Storage 2. temel özellikleri

-   **Hadoop uyumlu erişim**: Data Lake Storage 2., verileri [Hadoop Dağıtılmış dosya sistemi (bir)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)ile yaptığınız gibi yönetmenizi ve erişmenize olanak tanır. Yeni [ABFS sürücüsü](data-lake-storage-abfs-driver.md) (verilere erişmek için kullanılır) tüm Apache Hadoop ortamlarında kullanılabilir. Bu ortamlar [Azure HDInsight](../../hdinsight/index.yml)*,* [Azure Databricks](/azure/databricks/)ve [Azure SYNAPSE analizlerini](../../synapse-analytics/index.yml)içerir.

-   **POSIX Izinlerinin bir üst kümesi**: Data Lake Gen2 için güvenlik modeli, Data Lake Storage 2. özgü bazı ayrıntı düzeyi Ile birlikte ACL 'LERI ve POSIX izinlerini destekler. Ayarlar, Depolama Gezgini veya Hive ve Spark gibi çerçeveler aracılığıyla yapılandırılabilir.

-   Uygun **maliyetli**: Data Lake Storage 2. düşük maliyetli depolama kapasitesi ve işlemler sunar. [Azure Blob depolama yaşam döngüsü](storage-lifecycle-management-concepts.md) gibi özellikler yaşam döngüsü boyunca veri geçişleri olarak maliyetleri iyileştirir.

-   **İyileştirilmiş sürücü**: ABFS sürücüsü özellikle büyük veri analizi için [iyileştirilmiştir](data-lake-storage-abfs-driver.md) . Karşılık gelen REST API 'Leri uç noktadan geliyor `dfs.core.windows.net` .

### <a name="scalability"></a>Ölçeklenebilirlik

Azure depolama, Data Lake Storage 2. veya blob depolama arabirimlerine erişip erişemeyeceğini tasarlayarak ölçeklenebilir. *Birçok eksabaytlarca veri* depolayıp sunabilir. Bu depolama alanı miktarı, saniye başına giriş/çıkış işlemi (ıOPS) düzeyinde Gigabit/saniye (Gbps) cinsinden ölçülen aktarım hızı ile kullanılabilir. İşleme, hizmet, hesap ve dosya düzeylerinde ölçülen istek temelli gecikme süreleriyle yürütülür.

### <a name="cost-effectiveness"></a>Maliyet uygunluğu

Data Lake Storage 2. Azure Blob depolama alanı üzerinde oluşturulduğundan, depolama kapasitesi ve işlem maliyetleri düşüktür. Diğer bulut depolama hizmetlerinden farklı olarak, verileri analiz etmeden önce taşımanız veya dönüştürmeniz gerekmez. Fiyatlandırma hakkında daha fazla bilgi için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage).

Ayrıca, [hiyerarşik ad alanı](data-lake-storage-namespace.md) gibi özellikler birçok analiz işinin genel performansını önemli ölçüde artırır. Bu performans artışı, aynı miktarda veriyi işlemek için daha az işlem gücü yapmanız gerektiği anlamına gelir ve bu, uçtan uca analiz işi için daha düşük toplam sahip olma maliyeti (TCO) ile sonuçlanır.

### <a name="one-service-multiple-concepts"></a>Tek hizmet, birden çok kavram

Data Lake Storage 2. Azure Blob Storage üzerinde oluşturulduğundan, birden çok kavram aynı paylaşılan şeyleri tanımlayabilir.

Aşağıdakiler, farklı kavramlarla açıklandığı gibi eşdeğer varlıklardır. Aksi belirtilmedikçe, bu varlıklar doğrudan eş anlamlı değildir:

| Konsept                                | Üst düzey kuruluş | Alt düzey kuruluş                                            | Veri kapsayıcısı |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Blob 'lar – genel amaçlı nesne depolaması | Kapsayıcı              | Sanal dizin (yalnızca SDK – atomik düzenleme sağlamaz) | Blob           |
| Azure Data Lake Storage 2. – analiz depolaması          | Kapsayıcı            | Dizin                                                           | Dosya           |

## <a name="supported-blob-storage-features"></a>Desteklenen Blob depolama özellikleri

[Tanılama günlüğü](../common/storage-analytics-logging.md), [erişim katmanları](storage-blob-storage-tiers.md)ve [BLOB depolama yaşam döngüsü yönetim ilkeleri](storage-lifecycle-management-concepts.md) gibi BLOB depolama özellikleri hesabınız için kullanılabilir. 

Desteklenen BLOB depolama özelliklerinin bir listesi için, bkz. [Azure Data Lake Storage 2. bulunan BLOB Storage özellikleri](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Desteklenen Azure hizmeti tümleştirmeleri

Data Lake Storage Gen2, çeşitli Azure hizmetlerini destekler. Bunları kullanarak verileri alabilir, analiz yapabilir ve görsel gösterimler oluşturabilirsiniz. Desteklenen Azure hizmetlerinin listesi için bkz. [Azure Data Lake Storage 2. destekleyen Azure hizmetleri](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Desteklenen açık kaynaklı platformlar

Birçok açık kaynak platformu Data Lake Storage 2. destekler. Tüm liste için, bkz. [Azure Data Lake Storage 2. destekleyen açık kaynaklı platformlar](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Ayrıca bkz.

- [Azure Data Lake Storage 2. ile ilgili bilinen sorunlar](data-lake-storage-known-issues.md)
- [Azure Data Lake Storage çoklu protokol erişimi](data-lake-storage-multi-protocol-access.md)