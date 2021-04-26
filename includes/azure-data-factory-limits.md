---
title: include dosyası
description: include dosyası
services: data-factory
author: chez-charlie
ms.service: data-factory
ms.topic: include
ms.date: 11/16/2020
ms.author: chez
ms.custom: include file
ms.openlocfilehash: 96c67327a8272a02e677cc40b46c3177e470b684
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106126082"
---
Azure Data Factory, müşteri aboneliklerinin diğer iş yüklerinden korunduğundan emin olmak için aşağıdaki varsayılan sınırlara sahip çok kiracılı bir hizmettir. Sınırlandırmaları aboneliğinizin en büyük sınırına yükseltmek için desteğe başvurun.

### <a name="version-2"></a>Sürüm 2

| Kaynak | Varsayılan limit | Üst sınır |
| -------- | ------------- | ------------- |
| Azure aboneliğindeki veri fabrikaları | 800 | 800 |
| Veri Fabrikası içinde işlem hatları, veri kümeleri, Tetikleyiciler, bağlı hizmetler, Özel uç noktalar ve tümleştirme çalışma zamanları gibi toplam varlık sayısı | 5.000 | [Desteğe başvurun](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tek bir abonelik altında Azure-SSIS tümleştirme çalışma zamanları için toplam CPU çekirdekleri | 256 | [Desteğe başvurun](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Fabrika içindeki tüm işlem hatları arasında paylaşılan, Veri Fabrikası başına eşzamanlı işlem hattı çalıştırılır | 10,000  | 10,000 |
| [Azure Integration Runtime bölgesi](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) başına abonelik başına eşzamanlı dış etkinlik çalıştırılır<br><small>Dış etkinlikler Integration Runtime üzerinde yönetilir, ancak Databricks, saklı yordam, Hdınsights, Web ve diğerleri dahil olmak üzere bağlantılı hizmetlerde yürütülür. Bu sınır, kendinden konak IR için uygulanmaz.</small> | 3.000 | 3.000 |
| [Azure Integration Runtime bölgesi](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) başına abonelik başına eşzamanlı işlem hattı etkinliği çalıştırılır <br><small>İşlem hattı etkinlikleri, arama, GetMetadata ve Sil dahil olmak üzere tümleştirme çalışma zamanı üzerinde yürütülür. Bu sınır, kendinden konak IR için uygulanmaz.</small> | 1.000 | 1.000                                                        |
| [Azure Integration Runtime bölgesi](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) başına abonelik başına eş zamanlı yazma işlemi<br><small>Test bağlantısı dahil, klasör listesi ve Tablo listesine gözatıp verileri önizleyin. Bu sınır, kendinden konak IR için uygulanmaz.</small> | 200 | 200                                                          |
| [Azure Integration Runtime bölgesi](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) başına abonelik başına eşzamanlı veri tümleştirme birimleri<sup>1</sup> tüketimi| Bölge grubu 1<sup>2</sup>: 6.000<br>Bölge grubu 2<sup>2</sup>: 3.000<br>Bölge grubu 3<sup>2</sup>: 1.500 | Bölge grubu 1<sup>2</sup>: 6.000<br/>Bölge grubu 2<sup>2</sup>: 3.000<br/>Bölge grubu 3<sup>2</sup>: 1.500 |
| Kapsayıcılar için iç etkinlikleri içeren, işlem hattı başına en fazla etkinlik | 40 | 40 |
| Tek bir şirket içinde barındırılan tümleştirme çalışma zamanına karşı oluşturulabilecek en fazla bağlantılı tümleştirme çalışma zamanı sayısı | 100 | [Desteğe başvurun](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| İşlem hattı başına en fazla parametre | 50 | 50 |
| ForEach öğeleri | 100.000 | 100.000 |
| ForEach paralellik | 20 | 50 |
| Ardışık düzen başına en fazla sıraya alınmış çalıştırma | 100 | 100 |
| İfade başına karakter | 8,192 | 8,192 |
| En az dönerek pencere tetikleme aralığı | 15 dk | 15 dk |
| İşlem hattı etkinlik çalıştırmaları için maksimum zaman aşımı | 7 gün | 7 gün |
| İşlem hattı nesneleri için nesne başına bayt sayısı<sup>3</sup> | 200 KB | 200 KB |
| Veri kümesi ve bağlı hizmet nesneleri için nesne başına bayt sayısı<sup>3</sup> | 100 KB | 2.000 KB |
| Her etkinlik çalışması için yük başına bayt sayısı<sup>4</sup> | 896 KB | 896 KB |
| Kopyalama etkinliği başına veri tümleştirme birimleri<sup>1</sup> çalışma | 256 | 256 |
| API çağrıları yazma | 1200/h | 1200/h<br/><br/> Bu sınır, Azure Data Factory değil Azure Resource Manager tarafından belirlenir. |
| API çağrılarını oku | 12500/h | 12500/h<br/><br/> Bu sınır, Azure Data Factory değil Azure Resource Manager tarafından belirlenir. |
| Dakikada izleme sorgusu sayısı | 1.000 | 1.000 |
| Veri akışı hata ayıklama oturumunun en uzun süresi | 8 saat | 8 saat |
| Tümleştirme çalışma zamanı başına eşzamanlı veri akışı sayısı | 50 | [Desteğe başvurun](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Her fabrika için Kullanıcı başına eşzamanlı veri akışı hata ayıklama oturumu sayısı | 3 | 3 |
| Veri akışı Azure IR TTL sınırı | 4 saat |  4 saat |
| Bir fabrikada meta veri varlık boyutu sınırı | 2 GB | [Desteğe başvurun](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> veri tümleştirme birimi (Diu), buluttan buluta kopyalama işleminde kullanılır, [veri tümleştirme birimlerinden (sürüm 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units)daha fazla bilgi edinin. Faturalandırma hakkında bilgi için bkz. [Azure Data Factory fiyatlandırması](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) veri uyumluluğu, verimlilik ve azaltılmış ağ çıkış maliyetlerini sağlamak için [genel kullanıma sunulmuştur](https://azure.microsoft.com/global-infrastructure/services/) . 

| Bölge grubu | Bölgeler |
| -------- | ------ |
| Bölge grubu 1 | Orta ABD, Doğu ABD, Doğu ABD 2, Kuzey Avrupa, Batı Avrupa, Batı ABD, Batı ABD 2 |
| Bölge grubu 2 | Avustralya Doğu, Avustralya Güneydoğu, Brezilya Güney, Orta Hindistan, Japonya Doğu, Orta Kuzey ABD, Orta Güney ABD, Güneydoğu Asya, Orta Batı ABD |
| Bölge grubu 3 | Diğer bölgeler |

<sup>3</sup> işlem hattı, veri kümesi ve bağlı hizmet nesneleri, iş yükünüzün mantıksal bir gruplandırmasını temsil eder. Bu nesnelere yönelik sınırlar, Azure Data Factory taşıyacağınız ve işleyebilmeniz için veri miktarıyla ilişkili değildir. Data Factory, verilerin petabaytlarca işlemek için ölçeklendirilecek şekilde tasarlanmıştır.

<sup>4</sup> her etkinlik çalışması için yük, etkinlik yapılandırmasını, ilişkili veri kümelerini ve bağlı hizmet yapılandırmalarını ve etkinlik türü başına oluşturulan sistem özelliklerinin küçük bir bölümünü içerir. Bu yük boyutu için sınır Azure Data Factory taşıyacağınız ve işleyebilmeniz için veri miktarıyla ilişkili değildir. Bu sınıra ulaşırsanız, [Belirtiler ve öneriler](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large) hakkında bilgi edinin.

### <a name="version-1"></a>Sürüm 1

| **Kaynak** | **Varsayılan sınır** | **Üst sınır** |
| --- | --- | --- |
| Veri Fabrikası içindeki işlem hatları |2,500 |[Desteğe başvurun](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Veri Fabrikası içindeki veri kümeleri |5.000 |[Desteğe başvurun](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Veri kümesi başına eşzamanlı dilimler |10 |10 |
| İşlem hattı nesneleri için nesne başına bayt sayısı<sup>1</sup> |200 KB |200 KB |
| Veri kümesi ve bağlı hizmet nesneleri için nesne başına bayt sayısı<sup>1</sup> |100 KB |2.000 KB |
| Azure HDInsight bir abonelik içinde isteğe bağlı küme çekirdekleri<sup>2</sup> |60 |[Desteğe başvurun](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Kopya etkinliği başına bulut veri taşıma birimleri<sup>3</sup> çalışma |32 |32 |
| İşlem hattı etkinlik çalıştırmaları için yeniden deneme sayısı |1.000 |MaxInt (32 bit) |

<sup>1</sup> işlem hattı, veri kümesi ve bağlı hizmet nesneleri, iş yükünüzün mantıksal bir gruplandırmasını temsil eder. Bu nesnelere yönelik sınırlar, Azure Data Factory taşıyacağınız ve işleyebilmeniz için veri miktarıyla ilişkili değildir. Data Factory, verilerin petabaytlarca işlemek için ölçeklendirilecek şekilde tasarlanmıştır.

<sup>2</sup> Isteğe bağlı HDInsight çekirdekleri, veri fabrikasını içeren abonelikten ayrılır. Sonuç olarak, önceki sınır, isteğe bağlı HDInsight çekirdekleri için Data Factory tarafından zorlanan çekirdek sınırı olur. Azure aboneliğinizle ilişkili çekirdek limitinden farklıdır.

<sup>3</sup> sürüm 1 için bulut veri taşıma birimi (DMU), buluttan buluta kopyalama işleminde kullanılır, [bulut veri taşıma birimlerinden (sürüm 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units)daha fazla bilgi edinin. Faturalandırma hakkında bilgi için bkz. [Azure Data Factory fiyatlandırması](https://azure.microsoft.com/pricing/details/data-factory/).

| **Kaynak** | **Varsayılan alt sınır** | **Minimum sınır** |
| --- | --- | --- |
| Zamanlama aralığı |15 dakika |15 dakika |
| Yeniden deneme girişimleri arasındaki Aralık |1 saniye |1 saniye |
| Yeniden deneme zaman aşımı değeri |1 saniye |1 saniye |

#### <a name="web-service-call-limits"></a>Web hizmeti çağrı limitleri
Azure Resource Manager, API çağrıları için sınırlara sahiptir. API çağrılarını [Azure Resource Manager API sınırları](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)dahilinde bir hızda yapabilirsiniz.
