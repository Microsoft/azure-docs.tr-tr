---
title: Azure Veri Paylaşımı'nda desteklenen veri depoları
description: Azure veri paylaşımında kullanım için desteklenen veri depoları hakkında bilgi edinin.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: def73d137f3cc2c79ae8417995ec6bdf6c519b7d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812633"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Veri Paylaşımı'nda desteklenen veri depoları

Azure veri paylaşımı, ve farklı veri depolarından paylaşma özelliği de dahil olmak üzere açık ve esnek veri paylaşımı sağlar. Veri sağlayıcıları verileri bir veri deposu türünden paylaşabilir ve veri tüketicileri verileri alacak bir veri deposu seçebilir. 

Bu makalede, Azure veri paylaşımının desteklediği zengin Azure veri depoları kümesi hakkında bilgi edineceksiniz. Ayrıca, veri sağlayıcılarının ve veri tüketicilerinin farklı veri depolarını nasıl birleştirebileceği hakkında bilgi edineceksiniz. 

## <a name="supported-data-stores"></a>Desteklenen veri depoları 

Aşağıdaki tabloda, Azure veri paylaşımının desteklediği veri depoları açıklanmaktadır. 

| Veri deposu | Tam anlık görüntülere göre paylaşım | Artımlı anlık görüntülere dayalı paylaşım | Yerinde paylaşma 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob Depolama |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| Azure SQL Veritabanı |✓ | | |
| Azure Synapse Analytics (eski adı Azure SQL Veri Ambarı) |✓ | | |
| Azure SYNAPSE Analytics (çalışma alanı) adanmış SQL havuzu |✓ | | |
| Azure Veri Gezgini | | |✓ |

## <a name="data-store-support-matrix"></a>Veri deposu destek matrisi

Azure veri paylaşma, veri tüketicilerinin verileri kabul etmesi için bir veri deposu seçmesini sağlar. Örneğin, Azure SQL veritabanı 'ndan paylaşılan veriler Azure Data Lake Storage 2., Azure SQL veritabanı veya Azure SYNAPSE Analytics 'e alınabilir. Müşteriler bir alan veri paylaşımının ayarlandığı zaman, verileri alacak biçimi seçebilirler. 

Aşağıdaki tabloda, veri tüketicilerinin bir veri paylaşımının kabul etmesi ve yapılandırılması sırasında seçebilecekleri birleşimler ve seçenekler açıklanmaktadır. Daha fazla bilgi için bkz. [veri kümesi eşlemesi yapılandırma](how-to-configure-mapping.md).

| Veri deposu | Blob Depolama | Data Lake Storage 1. Nesil | Data Lake Storage 2. Nesil | SQL Veritabanı | SYNAPSE Analytics (eski adıyla SQL veri ambarı) | SYNAPSE Analytics (çalışma alanı) adanmış SQL havuzu | Veri Gezgini
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Blob Depolama | ✓ || ✓ |||
| Data Lake Storage 1. Nesil | ✓ | | ✓ |||
| Data Lake Storage 2. Nesil | ✓ | | ✓ |||
| SQL Veritabanı | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| SYNAPSE Analytics (eski adıyla SQL veri ambarı) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| SYNAPSE Analytics (çalışma alanı) adanmış SQL havuzu | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Veri Gezgini ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Depolama hesabından paylaşma
Azure veri paylaşımı, Azure Data Lake Storage 1. ve Azure Data Lake Storage 2. dosya, klasör ve dosya sistemlerinin paylaşılmasını destekler. Ayrıca, Azure Blob depolamadan blob, klasör ve kapsayıcı paylaşımını da destekler. Blok, ekleme veya sayfa bloblarını paylaşabilir ve blok Blobları olarak alınır.

Dosya sistemleri, kapsayıcılar veya klasörler anlık görüntü tabanlı paylaşımda paylaşıldığında, veri tüketicileri, paylaşılan verilerin tam bir kopyasını oluşturmak için seçim yapabilir. Ya da yalnızca yeni dosyaları veya güncelleştirilmiş dosyaları kopyalamak için Artımlı anlık görüntü özelliğini kullanabilirler. 

Artımlı bir anlık görüntü, dosyaların son değiştirilme saatini temel alır. Alınan verilerdeki dosyalarla aynı ada sahip mevcut dosyaların anlık görüntüde üzerine yazılır. Kaynaktan silinen dosyalar hedefte silinmez. 

Daha fazla bilgi için bkz. [Azure Blob depolama ve Azure Data Lake Storage verileri paylaşma ve alma](how-to-share-from-storage.md).

## <a name="share-from-a-sql-based-source"></a>SQL tabanlı bir kaynaktan paylaşma
Azure veri paylaşımı, Azure SQL veritabanı ve Azure SYNAPSE Analytics (eskiden Azure SQL veri ambarı) ile hem tabloların hem de görünümlerin paylaşılmasını destekler. Azure SYNAPSE Analytics (çalışma alanı) adanmış SQL havuzundan tablo paylaşımını destekler. Azure SYNAPSE Analytics (çalışma alanı) sunucusuz SQL havuzunun paylaşılması Şu anda desteklenmiyor. 

Veri tüketicileri, verileri Azure Data Lake Storage 2. veya Azure Blob depolama alanına CSV dosyası veya Parquet dosyası olarak kabul edebilir. Ayrıca, verileri Azure SQL veritabanı ve Azure SYNAPSE Analytics 'e tablo olarak kabul edebilirler.

Tüketiciler Azure Data Lake Storage 2. veya Azure Blob depolama alanına veri kabul ettiğinde, dosya zaten mevcutsa tam anlık görüntüler hedef dosyanın içeriğinin üzerine yazar. Veriler bir tabloya alındığında ve hedef tablo henüz yoksa, Azure veri paylaşımında kaynak şemayı kullanarak bir SQL tablosu oluşturulur. Bir hedef tablo zaten varsa ve aynı ada sahipse, en son tam anlık görüntüyle bırakılır ve üzerine yazılır. Artımlı anlık görüntüler şu anda desteklenmiyor.

Daha fazla bilgi için bkz. [Azure SQL veritabanı ve Azure SYNAPSE Analytics 'ten verileri paylaşma ve alma](how-to-share-from-sql.md).

## <a name="share-from-data-explorer"></a>Veri Gezgini paylaşma
Azure veri paylaşımında, Azure Veri Gezgini kümelerinden veritabanlarını yerinde paylaşma özelliği de desteklenir. Veri sağlayıcısı veritabanı veya küme düzeyinde paylaşabilir. 

Veriler veritabanı düzeyinde paylaşıldığında, veri tüketicileri yalnızca veri sağlayıcının paylaştığı veritabanlarına erişebilir. Bir sağlayıcı, küme düzeyinde veri paylaşıyorsa veri tüketicileri, veri sağlayıcının oluşturduğu gelecekteki veritabanları dahil olmak üzere sağlayıcının kümesinden tüm veritabanlarına erişebilir.

Paylaşılan veritabanlarına erişmek için veri tüketicilerinin kendi Azure Veri Gezgini kümesine ihtiyacı vardır. Kümeleri, veri sağlayıcının Azure Veri Gezgini kümesiyle aynı Azure veri merkezinde olmalıdır. 

Bir paylaşım ilişkisi oluşturulduğunda, Azure veri paylaşımı, sağlayıcının kümesi ve tüketicinin kümesi arasında sembolik bir bağlantı oluşturur. Toplu işlem modu kullanılarak kaynak kümeye alınan veriler, hedef kümede birkaç dakika içinde görünür.

Daha fazla bilgi için bkz. [Azure Veri Gezgini verileri paylaşma ve alma](/azure/data-explorer/data-share). 

## <a name="next-steps"></a>Sonraki adımlar

Verileri paylaşmaya nasıl başlayacağınızı öğrenmek için [verilerinizi paylaşma](share-your-data.md) öğreticiye geçin.
