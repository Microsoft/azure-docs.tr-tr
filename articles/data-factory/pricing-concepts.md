---
title: Örnekler aracılığıyla Azure Data Factory fiyatlandırmasını anlama
description: Bu makalede, ayrıntılı örneklerle Azure Data Factory fiyatlandırma modeli açıklanmaktadır ve gösterilmektedir
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/25/2018
ms.openlocfilehash: c42946733ee49ed6acf2c8deadf850208e003339
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684529"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Örnekler aracılığıyla Data Factory fiyatlandırmasını anlama

Bu makalede, ayrıntılı örneklerle Azure Data Factory fiyatlandırma modeli açıklanmaktadır ve gösterilmektedir.

> [!NOTE]
> Aşağıdaki örneklerde kullanılan fiyatlar kuramsal değildir ve gerçek fiyatlandırmaya yönelik değildir.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>AWS S3 'ten Azure Blob depolama alanına veri kopyalama

Bu senaryoda, bir saatlik zamanlamaya göre AWS S3 ' den Azure Blob depolamaya veri kopyalamak istersiniz.

Senaryoyu başarmak için aşağıdaki öğelerle bir işlem hattı oluşturmanız gerekir:

1. AWS S3 öğesinden kopyalanacak veriler için giriş veri kümesi olan bir kopyalama etkinliği.

2. Azure depolama 'daki veriler için çıkış veri kümesi.

3. İşlem hattını her saat yürütmek için bir zamanlama tetikleyicisi.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **İşlemler** | **Türler ve birimler** |
| --- | --- |
| Bağlı hizmet oluştur | 2 varlık okuma/yazma  |
| Veri kümeleri oluştur | 4 okuma/yazma varlıkları (veri kümesi oluşturma için 2, bağlantılı hizmet başvuruları için 2) |
| İşlem hattı oluşturma | 3 okuma/yazma varlıkları (işlem hattı oluşturmak için 1, veri kümesi başvuruları için 2) |
| İşlem hattı al | 1 okuma/yazma varlığı |
| İşlem hattını Çalıştır | 2 etkinlik çalıştırmaları (tetikleyici çalışması için 1, etkinlik çalıştırmaları için 1) |
| Veri Kopyalama varsayım: yürütme süresi = 10 dk | 10 \* 4 Azure Integration Runtime (varsayılan DIU ayarı = 4) veri tümleştirme birimleri ve kopyalama performansını iyileştirme hakkında daha fazla bilgi Için [Bu makaleye](copy-activity-performance.md) bakın |
| İzleme işlem hattı varsayımı: yalnızca 1 çalıştırma gerçekleşti | 2 izleme çalıştırması kayıtları yeniden denendi (işlem hattı çalıştırması için 1, etkinlik çalıştırması için 1) |

**Toplam senaryo fiyatlandırması: $0,16811**

- Data Factory Işlemler = **$0,0001**
  - Okuma/yazma = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - İzleme = 2\*000005 = $0,00001 [1 Izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme &amp; yürütme = **$0,168**
  - Etkinlik çalıştırmaları = 001\*2 = 0,002 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri taşıma etkinlikleri = $0,166 (10 dakikalık yürütme süresi için günlere eşit olarak dağıtılır. Azure Integration Runtime) $0,25/saat)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Veri kopyalama ve Azure Databricks saatlik olarak dönüştürme

Bu senaryoda, AWS S3 ' den Azure Blob depolama alanına veri kopyalamak ve saatlik bir zamanlamaya göre verileri Azure Databricks dönüştürmek istiyorsunuz.

Senaryoyu başarmak için aşağıdaki öğelerle bir işlem hattı oluşturmanız gerekir:

1. AWS S3 öğesinden kopyalanacak veriler için bir giriş veri kümesi ve Azure Storage 'daki veriler için bir çıkış veri kümesi içeren bir kopyalama etkinliği.
2. Veri dönüştürme için bir Azure Databricks etkinliği.
3. Her saat ardışık düzeni yürütmek için bir zamanlama tetikleyicisi.

![Scenario2](media/pricing-concepts/scenario2.png)

| **İşlemler** | **Türler ve birimler** |
| --- | --- |
| Bağlı hizmet oluştur | 3 okuma/yazma varlığı  |
| Veri kümeleri oluştur | 4 okuma/yazma varlıkları (veri kümesi oluşturma için 2, bağlantılı hizmet başvuruları için 2) |
| İşlem hattı oluşturma | 3 okuma/yazma varlıkları (işlem hattı oluşturmak için 1, veri kümesi başvuruları için 2) |
| İşlem hattı al | 1 okuma/yazma varlığı |
| İşlem hattını Çalıştır | 3 etkinlik çalıştırmaları (tetikleyici çalışması için 1, etkinlik çalıştırmaları için 2) |
| Veri Kopyalama varsayım: yürütme süresi = 10 dk | 10 \* 4 Azure Integration Runtime (varsayılan DIU ayarı = 4) veri tümleştirme birimleri ve kopyalama performansını iyileştirme hakkında daha fazla bilgi Için [Bu makaleye](copy-activity-performance.md) bakın |
| İzleme işlem hattı varsayımı: yalnızca 1 çalıştırma gerçekleşti | 3 izleme çalıştırması kayıtları yeniden denendi (işlem hattı çalıştırması için 1, etkinlik çalıştırması için 2) |
| Databricks etkinlik varsayımını Yürüt: yürütme süresi = 10 dk | 10 dakikalık dış işlem hattı etkinliği yürütme |

**Toplam senaryo fiyatlandırması: $0,16916**

- Data Factory Işlemler = **$0,00012**
  - Okuma/yazma = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 3\*000005 = $0,00001 [1 Izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme &amp; yürütme = **$0,16904**
  - Etkinlik çalıştırmaları = 001\*3 = 0,003 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri taşıma etkinlikleri = $0,166 (10 dakikalık yürütme süresi için günlere eşit olarak dağıtılır. Azure Integration Runtime) $0,25/saat)
  - Dış ardışık düzen etkinliği = $0,000041 (10 dakikalık yürütme süresi için eşit olarak dağıtılır. Azure Integration Runtime için $0.00025/saat)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Saatlik dinamik parametrelerle veri kopyalama ve dönüştürme

Bu senaryoda, AWS S3 ' den Azure Blob depolama alanına veri kopyalamak ve bir saatlik zamanlamaya göre Azure Databricks (betikteki dinamik parametrelerle birlikte) ile dönüştürmek istiyorsunuz.

Senaryoyu başarmak için aşağıdaki öğelerle bir işlem hattı oluşturmanız gerekir:

1. Azure depolama 'daki veriler için bir çıkış veri kümesi olan AWS S3 'den kopyalanacak veriler için bir giriş veri kümesi içeren bir kopyalama etkinliği.
2. Parametreleri dönüştürme betiğine dinamik olarak geçirmek için bir arama etkinliği.
3. Veri dönüştürme için bir Azure Databricks etkinliği.
4. Her saat ardışık düzeni yürütmek için bir zamanlama tetikleyicisi.

![Scenario3](media/pricing-concepts/scenario3.png)

| **İşlemler** | **Türler ve birimler** |
| --- | --- |
| Bağlı hizmet oluştur | 3 okuma/yazma varlığı  |
| Veri kümeleri oluştur | 4 okuma/yazma varlıkları (veri kümesi oluşturma için 2, bağlantılı hizmet başvuruları için 2) |
| İşlem hattı oluşturma | 3 okuma/yazma varlıkları (işlem hattı oluşturmak için 1, veri kümesi başvuruları için 2) |
| İşlem hattı al | 1 okuma/yazma varlığı |
| İşlem hattını Çalıştır | 4 etkinlik çalıştırmaları (tetikleyici çalışması için 1, etkinlik çalıştırmaları için 3) |
| Veri Kopyalama varsayım: yürütme süresi = 10 dk | 10 \* 4 Azure Integration Runtime (varsayılan DIU ayarı = 4) veri tümleştirme birimleri ve kopyalama performansını iyileştirme hakkında daha fazla bilgi Için [Bu makaleye](copy-activity-performance.md) bakın |
| İzleme işlem hattı varsayımı: yalnızca 1 çalıştırma gerçekleşti | 4 izleme çalıştırması kayıtları yeniden denendi (işlem hattı çalıştırması için 1, etkinlik çalıştırması için 3) |
| Arama etkinlik varsayımını Yürüt: yürütme süresi = 1 dk | 1 dakikalık işlem hattı etkinliği yürütme |
| Databricks etkinlik varsayımını Yürüt: yürütme süresi = 10 dk | 10 dakikalık dış işlem hattı etkinliği yürütme |

**Toplam senaryo fiyatlandırması: $0,17020**

- Data Factory Işlemler = **$0,00013**
  - Okuma/yazma = 11\*00001 = $0,00011 [1 R/W = $0,50/50000 = 0,00001]
  - Monitoring = 4\*000005 = $0,00002 [1 Izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme &amp; yürütme = **$0,17007**
  - Etkinlik çalıştırmaları = 001\*4 = 0,004 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri taşıma etkinlikleri = $0,166 (10 dakikalık yürütme süresi için günlere eşit olarak dağıtılır. Azure Integration Runtime) $0,25/saat)
  - İşlem hattı etkinliği = $0,00003 (1 dakikalık yürütme süresi için eşit olarak dağıtılır. Azure Integration Runtime için $0.002/saat)
  - Dış ardışık düzen etkinliği = $0,000041 (10 dakikalık yürütme süresi için eşit olarak dağıtılır. Azure Integration Runtime için $0.00025/saat)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>Normal bir Workday için eşleme veri akışı hata ayıklamasını kullanma (Önizleme fiyatlandırması)

Veri mühendisi olarak her gün veri akışlarını tasarlama, oluşturma ve test etme konusunda siz sorumlusunuz. Sabah ADF Kullanıcı arabiriminde oturum açın ve veri akışları için hata ayıklama modunu etkinleştirin. Hata ayıklama oturumları için varsayılan TTL 60 dakikadır. Günde 10 saat boyunca çalışırsınız, bu nedenle hata ayıklama oturumunuzun süresi dolmaz. Bu nedenle, günün ücreti şu şekilde olacaktır:

**10 (saat) x 8 (çekirdek) x $0,112 = $8,96**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>Eşleme verisi akışlarıyla blob deposundaki verileri dönüştürme (Önizleme fiyatlandırması)

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
| Veri akışı varsayımları: yürütme süresi = 10 dk + 10 dakikalık TTL | 10 \*, 10 TTL ile genel Işlem için 8 çekirdek |
| İzleme işlem hattı varsayımı: yalnızca 1 çalıştırma gerçekleşti | 2 izleme çalıştırması kayıtları yeniden denendi (işlem hattı çalıştırması için 1, etkinlik çalıştırması için 1) |

**Toplam senaryo fiyatlandırması: $0,3011**

- Data Factory Işlemler = **$0,0001**
  - Okuma/yazma = 10\*00001 = $0,0001 [1 R/W = $0,50/50000 = 0,00001]
  - İzleme = 2\*000005 = $0,00001 [1 Izleme = $0,25/50000 = 0,000005]
- İşlem hattı düzenleme &amp; yürütme = **$0,301**
  - Etkinlik çalıştırmaları = 001\*2 = 0,002 [1 Çalıştırma = $1/1000 = 0,001]
  - Veri akışı etkinlikleri = $0,299 20 dakika (10 dakikalık yürütme süresi + 10 dakikalık TTL) için eşit olarak dağıtılır. 8 çekirdek genel işlem ile Azure Integration Runtime $0.112/saat

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory için fiyatlandırmayı anladığınıza göre, şimdi kullanmaya başlayın!

- [Azure Data Factory Kullanıcı arabirimini kullanarak bir veri fabrikası oluşturma](quickstart-create-data-factory-portal.md)

- [Azure Data Factory'ye giriş](introduction.md)

- [Azure Data Factory 'de görsel yazma](author-visually.md)
