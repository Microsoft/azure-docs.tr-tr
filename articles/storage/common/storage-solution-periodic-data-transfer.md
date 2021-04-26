---
title: Düzenli veri aktarımı için bir Azure çözümü seçin | Microsoft Docs
description: Verileri düzenli aralıklarla aktarırken veri aktarımı için bir Azure çözümü seçme hakkında bilgi edinin.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a15ebd43861e2116ddbb2d9055b289645962e203
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96573927"
---
# <a name="solutions-for-periodic-data-transfer"></a>Düzenli aralıklarla veri aktarımına yönelik çözümler
 
Bu makalede, verileri düzenli aralıklarla aktarırken veri aktarımı çözümlerine ilişkin bir genel bakış sunulmaktadır. Ağ üzerinden düzenli veri aktarımı, düzenli aralıklarla veya sürekli veri hareketine göre yinelenebilir olarak kategorilere ayrılır. Makalede ayrıca önerilen veri aktarımı seçenekleri ve bu senaryonun ilgili anahtar özelliği matrisi de açıklanmaktadır.

Tüm kullanılabilir veri aktarımı seçeneklerine genel bir bakış için bkz. [Azure veri aktarımı çözümü seçme](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Önerilen seçenekler

Düzenli aralıklarla veri aktarımı için önerilen seçenekler, aktarımın düzenli veya sürekli olmasına bağlı olarak iki kategoriye ayrılır.

- **Komut dosyalı/programlı araçlar** – düzenli aralıklarla oluşan veri aktarımı Için AzCopy ve Azure Storage REST API 'leri gibi betikleştirilmiş ve programlı araçları kullanın. Bu araçlar, BT uzmanları ve geliştiriciler için tasarlanmıştır.

    - **AzCopy** -bu komut satırı aracını, En Iyi performansla Azure Blob 'Larına, dosyalarına ve tablo depolamasına verileri kolayca kopyalamak için kullanın. AzCopy eşzamanlılık ve paralellik desteğine ek olarak kesintiye uğrayan kopyalama işlemlerini sürdürme olanağı sunar.
    - **Azure depolama REST API 'leri/SDK 'lar** – bir uygulama oluştururken, uygulamayı Azure Storage REST API 'lerinde geliştirebilir ve birden çok dilde sunulan Azure SDK 'larını kullanabilirsiniz. REST API 'Leri, verilerin Azure 'a ve Azure 'a yüksek performanslı olarak kopyalanması için özellikle tasarlanan Azure Storage veri taşıma kitaplığından da yararlanabilir.

- **Sürekli veri alma araçları** : sürekli ve devam eden veri alımı için Data Box çevrimiçi aktarım cihazından birini veya Azure Data Factory seçebilirsiniz. Bu araçlar BT uzmanları tarafından kurulur ve saydam bir şekilde veri aktarımını otomatikleştirebilir.

    - **Azure Data Factory** – Data Factory bir aktarım işleminin ölçeğini genişletmek için ve düzenleme ve kurumsal sınıf izleme özelliklerine ihtiyaç duyuyorsanız kullanılmalıdır. Azure Data Factory'yi kullanarak birden çok Azure hizmeti, şirket içi ortam veya ikisinin birleşiminin bulunduğu bir ortamda düzenli dosya aktarımı gerçekleştiren bir bulut işlem hattı ayarlayabilirsiniz. Azure Data Factory, farklı veri depolarında bulunan verileri alan veri temelli iş akışlarını düzenlemenize ve veri taşıma ile veri dönüşüm süreçlerini otomatikleştirmenize olanak tanır.
    - **Çevrimiçi aktarımlar için Azure Data Box ailesi** -Data Box Edge ve Data Box Gateway, verileri Azure 'a ve dışına taşıyabileceği çevrimiçi ağ cihazlarıdır. Data Box Edge, verileri karşıya yüklemeden önce işlemek için yapay zeka destekli uç işlemini kullanır. Data Box Gateway, cihazın sanal bir sürümüdür ancak aynı veri aktarımı yeteneklerine sahiptir.


## <a name="comparison-of-key-capabilities"></a>Anahtar özellikleri karşılaştırması

Aşağıdaki tabloda, temel özellikler arasındaki farklar özetlenmiştir.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Betikleştirilmiş/programlı ağ veri aktarımı

| Özellik                  | AzCopy                                 | Azure Depolama REST API'leri       |
|-----------------------------|----------------------------------------|-------------------------------|
| Form faktörü                 | Microsoft 'tan komut satırı aracı       | Müşterilere depolamaya yönelik geliştirme <br> Azure istemci kitaplıklarını kullanarak REST API 'Leri |
| İlk bir kerelik kurulum     | En az                                | Orta, değişken geliştirme çabaları    |
| Veri biçimi                 | Azure Blobları, Azure dosyaları, Azure tabloları | Azure Blobları, Azure dosyaları, Azure tabloları   |
| Performans                 | Zaten iyileştirilmiş                      | Geliştirme sırasında iyileştirin                  |
| Fiyatlandırma                     | Ücretsiz, veri çıkış ücretleri geçerlidir      | Ücretsiz, veri çıkış ücretleri geçerlidir        |

### <a name="continuous-data-ingestion-over-network"></a>Ağ üzerinden sürekli veri alımı

| Özellik                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Form faktörü                                   | Sanal cihaz             | Fiziksel cihaz          | Azure portal hizmeti, şirket içi aracıda                                                            |
| Donanım                                      | Hiper yöneticinizi            | Microsoft tarafından sağlandı    | NA                                                            |
| İlk kurulum çabası                          | Düşük (<30 dakika)            | Orta (~ birkaç saat) | Büyük (~ gün)                                                 |
| Veri biçimi                                   | Azure Blobları, Azure dosyaları   | Azure Blobları, Azure dosyaları | [Veri depoları ve biçimleri için 70 ' ten fazla veri Bağlayıcısı destekler](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Verileri önceden işleme                           | No                         | Evet, uç işlem ile    | Yes                                                           |
| Yerel önbellek<br>(Şirket içi verileri depolamak için)    | Yes                        | Yes                      | Hayır                                                            |
| Diğer bulutlardan aktar                    | Hayır                         | Hayır                       | Yes                                                           |
| Fiyatlandırma                                       | [Fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Fiyatlandırma](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Fiyatlandırma](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Sonraki adımlar

- [AzCopy ile veri aktarma](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Depolama REST API 'leri ile veri aktarımı hakkında daha fazla bilgi](/dotnet/api/overview/azure/storage).
- Nasıl yapılacağını anlayın:
    - [Data Box Gateway verileri aktarın](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
    - [Azure 'a göndermeden önce Data Box Edge verileri dönüştürün](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Azure Data Factory ile veri aktarmayı öğrenin](../../data-factory/tutorial-bulk-copy-portal.md).