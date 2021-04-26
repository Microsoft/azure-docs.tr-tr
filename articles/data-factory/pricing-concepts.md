---
title: Örnekler aracılığıyla Azure Data Factory fiyatlandırmasını anlama
description: Bu makalede, ayrıntılı örneklerle Azure Data Factory fiyatlandırma modeli açıklanmaktadır ve gösterilmektedir
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 3bb9574c74aaa3c2589d0ca93fb906168ca99095
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783379"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Örneklerle Data Factory fiyatlandırmasını anlama

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, ayrıntılı örneklerle Azure Data Factory fiyatlandırma modeli açıklanmaktadır ve gösterilmektedir.

> [!NOTE]
> Aşağıdaki örneklerde kullanılan fiyatlar kuramsal değildir ve gerçek fiyatlandırmaya yönelik değildir.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>AWS S3 'ten Azure Blob depolama alanına veri kopyalama

Bu senaryoda, bir saatlik zamanlamaya göre AWS S3 ' den Azure Blob depolamaya veri kopyalamak istersiniz.

Senaryoyu başarmak için aşağıdaki öğelerle bir işlem hattı oluşturmanız gerekir:

1. AWS S3 öğesinden kopyalanacak veriler için giriş veri kümesi olan bir kopyalama etkinliği.

2. Azure depolama 'daki veriler için çıkış veri kümesi.

3. İşlem hattını her saat yürütmek için bir zamanlama tetikleyicisi.

   ![Diyagram, bir zamanlama tetikleyicisi ile bir işlem hattı gösterir. İşlem hattında, bir W S S3 bağlantılı hizmetine ve kopyalama etkinliğine akan bir giriş veri kümesine etkinlik akışlarını kopyalayın, ayrıca bir Azure depolama bağlı hizmetine akan bir çıkış veri kümesine akar.](media/pricing-concepts/scenario1.png)

| **İşlemler** | **Türler ve birimler** |
| --- | --- |
| Bağlı hizmet oluştur | 2 varlık okuma/yazma  |
| Veri kümeleri oluştur | 4 okuma/yazma varlıkları (veri kümesi oluşturma için 2, bağlantılı hizmet başvuruları için 2) |
| İşlem hattı oluşturma | 3 okuma/yazma varlıkları (işlem hattı oluşturmak için 1, veri kümesi başvuruları için 2) |
| İşlem hattı al | 1 okuma/yazma varlığı |
| İşlem hattını Çalıştır | 2 etkinlik çalıştırmaları (tetikleyici çalışması için 1, etkinlik çalıştırmaları için 1) |
| Veri Kopyalama varsayım: yürütme süresi = 10 dk | 10 \* 4 Azure Integration Runtime (varsayılan DIU ayarı = 4) veri tümleştirme birimleri ve kopyalama performansını iyileştirme hakkında daha fazla bilgi için [Bu makaleye](copy-activity-performance.md) bakın |
| İzleme işlem hattı varsayımı: yalnızca 1 çalıştırma gerçekleşti | 2 izleme çalıştırması kayıtları alındı (işlem hattı çalıştırması için 1, etkinlik çalıştırması için 1) |

**Toplam senaryo fiyatlandırması: $0,16811**

- Data Factory Işlemler = **$0,0001**
  - Okuma/yazma = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - İzleme = 2 \* 000005 = $0,00001 [1 izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme &amp; yürütmesi = **$0,168**
  - Etkinlik çalıştırmaları = 001 \* 2 = 0,002 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri taşıma etkinlikleri = $0,166 (10 dakikalık yürütme süresi için günlere eşit olarak dağıtılır. Azure Integration Runtime) $0,25/saat)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Veri kopyalama ve Azure Databricks saatlik olarak dönüştürme

Bu senaryoda, AWS S3 ' den Azure Blob depolama alanına veri kopyalamak ve saatlik bir zamanlamaya göre verileri Azure Databricks dönüştürmek istiyorsunuz.

Senaryoyu başarmak için aşağıdaki öğelerle bir işlem hattı oluşturmanız gerekir:

1. AWS S3 öğesinden kopyalanacak veriler için bir giriş veri kümesi ve Azure Storage 'daki veriler için bir çıkış veri kümesi içeren bir kopyalama etkinliği.
2. Veri dönüştürme için bir Azure Databricks etkinliği.
3. Her saat ardışık düzeni yürütmek için bir zamanlama tetikleyicisi.

![Diyagram, bir zamanlama tetikleyicisi ile bir işlem hattı gösterir. İşlem hattında, etkinlik akışlarını bir giriş veri kümesine, bir çıkış veri kümesine ve Azure Databricks üzerinde çalışan bir DataBricks etkinliğine kopyalayın. Giriş veri kümesi, bir W S S3 bağlantılı hizmetine akar. Çıkış veri kümesi, Azure depolama bağlı hizmetine akar.](media/pricing-concepts/scenario2.png)

| **İşlemler** | **Türler ve birimler** |
| --- | --- |
| Bağlı hizmet oluştur | 3 okuma/yazma varlığı  |
| Veri kümeleri oluştur | 4 okuma/yazma varlıkları (veri kümesi oluşturma için 2, bağlantılı hizmet başvuruları için 2) |
| İşlem hattı oluşturma | 3 okuma/yazma varlıkları (işlem hattı oluşturmak için 1, veri kümesi başvuruları için 2) |
| İşlem hattı al | 1 okuma/yazma varlığı |
| İşlem hattını Çalıştır | 3 etkinlik çalıştırmaları (tetikleyici çalışması için 1, etkinlik çalıştırmaları için 2) |
| Veri Kopyalama varsayım: yürütme süresi = 10 dk | 10 \* 4 Azure Integration Runtime (varsayılan DIU ayarı = 4) veri tümleştirme birimleri ve kopyalama performansını iyileştirme hakkında daha fazla bilgi için [Bu makaleye](copy-activity-performance.md) bakın |
| İzleme işlem hattı varsayımı: yalnızca 1 çalıştırma gerçekleşti | 3 izleme çalıştırması kayıtları alındı (işlem hattı çalıştırması için 1, etkinlik çalıştırması için 2) |
| Databricks etkinlik varsayımını Yürüt: yürütme süresi = 10 dk | 10 dakikalık dış işlem hattı etkinliği yürütme |

**Toplam senaryo fiyatlandırması: $0,16916**

- Data Factory Işlemler = **$0,00012**
  - Okuma/yazma = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 3 \* 000005 = $0,00001 [1 izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme &amp; yürütmesi = **$0,16904**
  - Etkinlik çalıştırmaları = 001 \* 3 = 0,003 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri taşıma etkinlikleri = $0,166 (10 dakikalık yürütme süresi için günlere eşit olarak dağıtılır. Azure Integration Runtime) $0,25/saat)
  - Dış ardışık düzen etkinliği = $0,000041 (10 dakikalık yürütme süresi için eşit olarak dağıtılır. Azure Integration Runtime için $0.00025/saat)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Saatlik dinamik parametrelerle veri kopyalama ve dönüştürme

Bu senaryoda, AWS S3 ' den Azure Blob depolama alanına veri kopyalamak ve bir saatlik zamanlamaya göre Azure Databricks (betikteki dinamik parametrelerle birlikte) ile dönüştürmek istiyorsunuz.

Senaryoyu başarmak için aşağıdaki öğelerle bir işlem hattı oluşturmanız gerekir:

1. Azure depolama 'daki veriler için bir çıkış veri kümesi olan AWS S3 'den kopyalanacak veriler için bir giriş veri kümesi içeren bir kopyalama etkinliği.
2. Parametreleri dönüştürme betiğine dinamik olarak geçirmek için bir arama etkinliği.
3. Veri dönüştürme için bir Azure Databricks etkinliği.
4. Her saat ardışık düzeni yürütmek için bir zamanlama tetikleyicisi.

![Diyagram, bir zamanlama tetikleyicisi ile bir işlem hattı gösterir. İşlem hattında, etkinlik akışlarını bir giriş veri kümesine, bir çıkış veri kümesine ve Azure Databricks üzerinde çalışan bir DataBricks etkinliğine akan arama etkinliğine kopyalayın. Giriş veri kümesi, bir W S S3 bağlantılı hizmetine akar. Çıkış veri kümesi, Azure depolama bağlı hizmetine akar.](media/pricing-concepts/scenario3.png)

| **İşlemler** | **Türler ve birimler** |
| --- | --- |
| Bağlı hizmet oluştur | 3 okuma/yazma varlığı  |
| Veri kümeleri oluştur | 4 okuma/yazma varlıkları (veri kümesi oluşturma için 2, bağlantılı hizmet başvuruları için 2) |
| İşlem hattı oluşturma | 3 okuma/yazma varlıkları (işlem hattı oluşturmak için 1, veri kümesi başvuruları için 2) |
| İşlem hattı al | 1 okuma/yazma varlığı |
| İşlem hattını Çalıştır | 4 etkinlik çalıştırmaları (tetikleyici çalışması için 1, etkinlik çalıştırmaları için 3) |
| Veri Kopyalama varsayım: yürütme süresi = 10 dk | 10 \* 4 Azure Integration Runtime (varsayılan DIU ayarı = 4) veri tümleştirme birimleri ve kopyalama performansını iyileştirme hakkında daha fazla bilgi için [Bu makaleye](copy-activity-performance.md) bakın |
| İzleme işlem hattı varsayımı: yalnızca 1 çalıştırma gerçekleşti | 4 izleme çalıştırması kayıtları alındı (işlem hattı çalıştırması için 1, etkinlik çalıştırması için 3) |
| Arama etkinlik varsayımını Yürüt: yürütme süresi = 1 dk | 1 dakikalık işlem hattı etkinliği yürütme |
| Databricks etkinlik varsayımını Yürüt: yürütme süresi = 10 dk | 10 dakikalık dış işlem hattı etkinliği yürütme |

**Toplam senaryo fiyatlandırması: $0,17020**

- Data Factory Işlemler = **$0,00013**
  - Okuma/yazma = 11 \* 00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 4 \* 000005 = $0,00002 [1 izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme &amp; yürütmesi = **$0,17007**
  - Etkinlik çalıştırmaları = 001 \* 4 = 0,004 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri taşıma etkinlikleri = $0,166 (10 dakikalık yürütme süresi için günlere eşit olarak dağıtılır. Azure Integration Runtime) $0,25/saat)
  - İşlem hattı etkinliği = $0,00003 (1 dakikalık yürütme süresi için eşit olarak dağıtılır. Azure Integration Runtime için $0.002/saat)
  - Dış ardışık düzen etkinliği = $0,000041 (10 dakikalık yürütme süresi için eşit olarak dağıtılır. Azure Integration Runtime için $0.00025/saat)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday"></a>Normal bir Workday için eşleme veri akışı hata ayıklamasını kullanma

Veri mühendisi olarak, Sam her gün veri akışlarını tasarlamada, oluşturmadan ve test etmekten sorumludur. Sam, sabah ADF Kullanıcı arabirimine oturum açar ve veri akışları için hata ayıklama moduna izin vermez. Hata ayıklama oturumları için varsayılan TTL 60 dakikadır. Sam, günde 8 saat boyunca çalışarak hata ayıklama oturumunun süresi dolmaz. Bu nedenle, Sam günün ücreti şu şekilde olacaktır:

**8 (saat) x 8 (işlem için iyileştirilmiş çekirdek) x $0,193 = $12,35**

Aynı zamanda Chris, başka bir veri mühendisi de veri profili oluşturma ve ETL tasarım çalışması için ADF tarayıcı kullanıcı arabirimine kaydedilir. Chris, ADF gibi tüm gün içinde çalışmaz. Chris yalnızca, yukarıdaki Sam ile aynı dönemde ve aynı gün içinde 1 saat boyunca veri akışı hata ayıklayıcısını kullanmalıdır. Bunlar, hata ayıklama kullanımı için Gamze 'nin ücretlerine yöneliktir:

**1 (saat) x 8 (genel amaçlı çekirdekler) x $0,274 = $2,19**

## <a name="transform-data-in-blob-store-with-mapping-data-flows"></a>Eşleme verisi akışlarıyla blob deposundaki verileri dönüştürme

Bu senaryoda, bir saatlik zamanlamaya göre ADF eşleme veri akışları ' nda blob deposundaki verileri görsel olarak dönüştürmek istiyorsunuz.

Senaryoyu başarmak için aşağıdaki öğelerle bir işlem hattı oluşturmanız gerekir:

1. Dönüştürme mantığıyla bir veri akışı etkinliği.

2. Azure depolama 'daki veriler için bir giriş veri kümesi.

3. Azure depolama 'daki veriler için çıkış veri kümesi.

4. İşlem hattını her saat yürütmek için bir zamanlama tetikleyicisi.

| **İşlemler** | **Türler ve birimler** |
| --- | --- |
| Bağlı hizmet oluştur | 2 varlık okuma/yazma  |
| Veri kümeleri oluştur | 4 okuma/yazma varlıkları (veri kümesi oluşturma için 2, bağlantılı hizmet başvuruları için 2) |
| İşlem hattı oluşturma | 3 okuma/yazma varlıkları (işlem hattı oluşturmak için 1, veri kümesi başvuruları için 2) |
| İşlem hattı al | 1 okuma/yazma varlığı |
| İşlem hattını Çalıştır | 2 etkinlik çalıştırmaları (tetikleyici çalışması için 1, etkinlik çalıştırmaları için 1) |
| Veri akışı varsayımları: yürütme süresi = 10 dk + 10 dakikalık TTL | 10 \* TTL ile 10 16 genel işlem |
| İzleme işlem hattı varsayımı: yalnızca 1 çalıştırma gerçekleşti | 2 izleme çalıştırması kayıtları alındı (işlem hattı çalıştırması için 1, etkinlik çalıştırması için 1) |

**Toplam senaryo fiyatlandırması: $1,4631**

- Data Factory Işlemler = **$0,0001**
  - Okuma/yazma = 10 \* 00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - İzleme = 2 \* 000005 = $0,00001 [1 izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme &amp; yürütmesi = **$1,463**
  - Etkinlik çalıştırmaları = 001 \* 2 = 0,002 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri akışı etkinlikleri = $1,461 20 dakika (10 dakikalık yürütme süresi + 10 dakikalık TTL) için eşit olarak dağıtılır. 16 çekirdek genel işlem ile Azure Integration Runtime $0.274/saat

## <a name="data-integration-in-azure-data-factory-managed-vnet"></a>Azure Data Factory yönetilen VNET 'te veri tümleştirmesi
Bu senaryoda, Azure Blob depolamada orijinal dosyaları silmek ve Azure SQL veritabanından Azure Blob depolama alanına veri kopyalamak istersiniz. Bu yürütmeyi farklı işlem hatları üzerinde iki kez yapacaksınız. Bu iki işlem hattı yürütme süresi örtüşüyor.
![Scenario4 ](media/pricing-concepts/scenario-4.png) senaryoyu tamamlamak için aşağıdaki öğelerle iki işlem hattı oluşturmanız gerekir:
  - Bir işlem hattı etkinliği – etkinliği sil.
  - Azure Blob depolama alanından kopyalanacak veriler için giriş veri kümesi olan bir kopyalama etkinliği.
  - Azure SQL veritabanı 'ndaki veriler için çıkış veri kümesi.
  - İşlem hattını yürütmek için bir zamanlama tetikler.


| **İşlemler** | **Türler ve birimler** |
| --- | --- |
| Bağlı hizmet oluştur | 4 varlık okuma/yazma |
| Veri kümeleri oluştur | 8 okuma/yazma varlıkları (veri kümesi oluşturma için 4, bağlantılı hizmet başvuruları için 4) |
| İşlem hattı oluşturma | 6 okuma/yazma varlıkları (ardışık düzen oluşturma için 2, veri kümesi başvuruları için 4) |
| İşlem hattı al | 2 varlık okuma/yazma |
| İşlem hattını Çalıştır | 6 etkinlik çalıştırmaları (tetikleyici çalışması için 2, etkinlik çalıştırmaları için 4) |
| Silme etkinliğini Yürüt: her yürütme süresi = 5 dk. İlk işlem hattındaki Etkinlik yürütme yürütmesi 10:00, UTC 'den 10:05. UTC 'ye kadar olur. İkinci işlem hattında etkinlik yürütmeyi silme, 10:02 saat UTC 'den 10:07 ' e kadar UTC 'ye kadar olur.|Yönetilen VNET 'te toplam 7 dakikalık işlem hattı etkinliği yürütme. İşlem hattı etkinliği yönetilen VNET 'te en fazla 50 eşzamanlılık destekler. |
| Veri Kopyalama varsayım: her yürütme süresi = 10 dk. İlk işlem hattındaki kopya yürütme 10:06 ' dan UTC 'den 10:15 ' ye kadar UTC 'ye kadar olur. İkinci işlem hattında etkinlik yürütmeyi silme, 10:08 saat UTC 'den 10:17 ' e kadar UTC 'ye kadar olur. | 10 * 4 Azure Integration Runtime (varsayılan DIU ayarı = 4) veri tümleştirme birimleri ve kopyalama performansını iyileştirme hakkında daha fazla bilgi Için [Bu makaleye](copy-activity-performance.md) bakın |
| İzleme işlem hattı varsayımı: yalnızca 2 çalıştırma gerçekleşti | 6 izleme çalıştırma kaydı alındı (işlem hattı çalıştırması için 2, etkinlik çalıştırması için 4) |


**Toplam senaryo fiyatlandırması: $0,45523**

- Data Factory Işlemler = $0,00023
  - Okuma/yazma = 20 * 00001 = $0,0002 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 6 * 000005 = $0,00003 [1 Izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme & yürütme = $0,455
  - Etkinlik çalıştırmaları = 0,001 * 6 = 0,006 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri taşıma etkinlikleri = $0,333 (10 dakikalık yürütme süresi için günlere eşit olarak dağıtılır. Azure Integration Runtime) $0,25/saat)
  - İşlem hattı etkinliği = $0,116 (7 dakikalık yürütme süresi için günlere eşit olarak dağıtılır. Azure Integration Runtime 1/saat)

> [!NOTE]
> Bu fiyatlar yalnızca örnek amaçlıdır.

**SSS**

S: 50 'den fazla işlem hattı etkinliği çalıştırmak istersem, bu etkinlikler eşzamanlı olarak çalıştırılabilir mi?

Y: en fazla 50 eşzamanlı işlem hattı etkinliğine izin verilir.  "Ücretsiz yuva" kapatılıncaya kadar 51th ardışık düzen etkinliği sıraya alınacaktır. Dış etkinlik için aynı. Maksimum 800 eşzamanlı dış etkinliğe izin verilir.

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory için fiyatlandırmayı anladığınıza göre, şimdi kullanmaya başlayın!

- [Azure Data Factory UI kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-portal.md)

- [Azure Data Factory'ye giriş](introduction.md)

- [Azure Data Factory 'de görsel yazma](author-visually.md)
