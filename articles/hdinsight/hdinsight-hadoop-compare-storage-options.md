---
title: Depolama seçeneklerini Azure HDInsight kümeleriyle kullanım için karşılaştırın
description: Depolama türlerine genel bir bakış ve bunların Azure HDInsight ile nasıl çalıştıkları hakkında bilgi sağlar.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: b6dd0fd95280a65615d38ab11a2f9814f58586f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945848"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Depolama seçeneklerini Azure HDInsight kümeleriyle kullanım için karşılaştırın

HDInsight kümeleri oluştururken birkaç farklı Azure depolama hizmeti arasında seçim yapabilirsiniz:

* [HDInsight ile Azure Blob depolama](./overview-azure-storage.md)
* [HDInsight ile Azure Data Lake Storage 2.](./overview-data-lake-storage-gen2.md)
* [HDInsight ile Azure Data Lake Storage 1.](./overview-data-lake-storage-gen1.md)

Bu makale, bu depolama türlerine ve bunların benzersiz özelliklerine genel bir bakış sağlar.

## <a name="storage-types-and-features"></a>Depolama türleri ve özellikleri

Aşağıdaki tabloda farklı HDInsight sürümleriyle desteklenen Azure depolama hizmetleri özetlenmektedir:

| Depolama hizmeti | Hesap türü | Ad alanı türü | Desteklenen hizmetler | Desteklenen performans katmanları | Desteklenen erişim katmanları | HDInsight sürümü | Küme türü |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Genel amaçlı v2 | Hiyerarşik (dosya sistemi) | Blob | Standart | Sık erişimli, seyrek erişimli Arşiv | 3.6 + | Spark 2,1 ve 2,2 dışında tümü|
|Azure Depolama| Genel amaçlı v2 | Nesne | Blob | Standart | Sık erişimli, seyrek erişimli Arşiv | 3.6 + | Tümü |
|Azure Depolama| Genel amaçlı v1 | Nesne | Blob | Standart | Yok | Tümü | Tümü |
|Azure Depolama| BLOB depolama * * | Nesne | Blok Blobu | Standart | Sık erişimli, seyrek erişimli Arşiv | Tümü | Tümü |
|Azure Data Lake Storage Gen1| Yok | Hiyerarşik (dosya sistemi) | Yok | Yok | Yok | yalnızca 3,6 | HBase dışında tümü |
|Azure Depolama| Blok Blobu| Nesne | Blok Blobu | Premium | Yok| 3.6 + | Yalnızca hızlandırılmış yazmaları olan HBase|
|Azure Data Lake Storage Gen2| Blok Blobu| Hiyerarşik (dosya sistemi) | Blok Blobu | Premium | Yok| 3.6 + | Yalnızca hızlandırılmış yazmaları olan HBase|

* * HDInsight kümeleri için yalnızca ikincil depolama hesapları BlobStorage türünde olabilir ve Sayfa Blobu desteklenen bir depolama seçeneği değildir.

Azure depolama hesabı türleri hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../storage/common/storage-account-overview.md)

Azure depolama erişim katmanları hakkında daha fazla bilgi için bkz [. Azure Blob depolama: Premium (Önizleme), sık erişimli, seyrek erişimli ve arşiv depolama katmanları](../storage/blobs/storage-blob-storage-tiers.md)

Birincil ve isteğe bağlı ikincil depolama için hizmet birleşimlerini kullanarak kümeler oluşturabilirsiniz. Aşağıdaki tabloda, HDInsight 'ta Şu anda desteklenen küme depolama yapılandırmalarının özeti verilmiştir:

| HDInsight sürümü | Birincil depolama alanı | İkincil depolama | Desteklenir |
|---|---|---|---|
| 3,6 & 4,0 | Genel Amaçlı v1, Genel Amaçlı v2 | Genel Amaçlı v1, Genel Amaçlı v2, BlobStorage (blok Blobları) | Yes |
| 3,6 & 4,0 | Genel Amaçlı v1, Genel Amaçlı v2 | Data Lake Storage 2. Nesil | No |
| 3,6 & 4,0 | Data Lake Storage 2. * | Data Lake Storage 2. Nesil | Yes |
| 3,6 & 4,0 | Data Lake Storage 2. * | Genel Amaçlı v1, Genel Amaçlı v2, BlobStorage (blok Blobları) | Yes |
| 3,6 & 4,0 | Data Lake Storage 2. Nesil | Data Lake Storage 1. Nesil | No |
| 3.6 | Data Lake Storage 1. Nesil | Data Lake Storage 1. Nesil | Yes |
| 3.6 | Data Lake Storage 1. Nesil | Genel Amaçlı v1, Genel Amaçlı v2, BlobStorage (blok Blobları) | Yes |
| 3.6 | Data Lake Storage 1. Nesil | Data Lake Storage 2. Nesil | No |
| 4.0 | Data Lake Storage 1. Nesil | Herhangi biri | No |
| 4.0 | Genel Amaçlı v1, Genel Amaçlı v2 | Data Lake Storage 1. Nesil | No |

* = Bu, bir veya birden çok Data Lake Storage 2. olabilir, çünkü tüm kurulum, küme erişimi için aynı yönetilen kimliği kullanır.

> [!NOTE]
> Spark 2,1 veya 2,2 kümelerinde birincil depolama Data Lake Storage 2. desteklenmez.

## <a name="data-replication"></a>Veri çoğaltma

Azure HDInsight, müşteri verilerini depolamaz. Bir küme için depolamanın birincil yolu, ilişkili depolama hesaplarıdır. Kümenizi mevcut bir depolama hesabına ekleyebilir veya küme oluşturma işlemi sırasında yeni bir depolama hesabı oluşturabilirsiniz. Yeni bir hesap oluşturulursa, yerel olarak yedekli depolama (LRS) hesabı olarak oluşturulur ve [Güven Merkezi](https://azuredatacentermap.azurewebsites.net)'nde belirtilenler dahil olmak üzere bölge içi veri yerleşimi gereksinimlerini karşılar.

HDInsight ile ilişkili depolama hesabının LRS veya [Güven Merkezi](https://azuredatacentermap.azurewebsites.net)'nde belirtilen başka bir depolama seçeneği olduğundan emin olarak HDInsight 'ın verileri tek bir bölgede depolamak için düzgün yapılandırıldığını doğrulayabilirsiniz.
 
## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Azure depolama 'ya genel bakış](./overview-azure-storage.md)
* [HDInsight 'ta Azure Data Lake Storage 1. genel bakış](./overview-data-lake-storage-gen1.md)
* [HDInsight 'ta Azure Data Lake Storage 2. genel bakış](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage 2. Nesil'e giriş](../storage/blobs/data-lake-storage-introduction.md)
* [Azure Depolama’ya giriş](../storage/common/storage-introduction.md)
