---
title: include dosyası
description: include dosyası
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 0f55f3ce4856e7b6eccf665faaa1b1fa45b84b58
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72302358"
---
**Veri toplama birimi ve bekletme** 

| Katman | Gün başına sınır | Veri saklama | Açıklama |
|:---|:---|:---|:---|
| Geçerli GB başına Fiyatlandırma Katmanı<br>(2018 Nisan 'da tanıtılan) | Sınırsız | 30-730 gün | 31 günden daha fazla veri saklama, ek ücretler için kullanılabilir. Azure Izleyici fiyatlandırması hakkında daha fazla bilgi edinin. |
| Eski ücretsiz Katmanlar<br>(2016 Nisan 'da tanıtılan) | 500 MB | 7 gün | Çalışma alanınız günde 500 MB sınırına ulaştığında, veri alımı durduruluyor ve sonraki günün başlangıcında devam eder. Bir gün için UTC temel alınır. Azure Güvenlik Merkezi tarafından toplanan verilerin bu 500 MB/gün sınırına dahil edilmediğini ve bu sınırın üzerine toplanmaya devam edileceğini unutmayın.  |
| GB katmanı başına eski tek başına<br>(2016 Nisan 'da tanıtılan) | Sınırsız | 30 ila 730 gün | 31 günden daha fazla veri saklama, ek ücretler için kullanılabilir. Azure Izleyici fiyatlandırması hakkında daha fazla bilgi edinin. |
| Düğüm başına eski (OMS)<br>(2016 Nisan 'da tanıtılan) | Sınırsız | 30 ila 730 gün | 31 günden daha fazla veri saklama, ek ücretler için kullanılabilir. Azure Izleyici fiyatlandırması hakkında daha fazla bilgi edinin. |
| Eski Standart katman | Sınırsız | 30 gün  | Saklama ayarlanamıyor |
| Eski Premium katmanı | Sınırsız | 365 gün  | Saklama ayarlanamıyor |

**Abonelik başına çalışma alanı sayısı.**

| Fiyatlandırma katmanı    | Çalışma alanı sınırı | Yorumlar
|:---|:---|:---|
| Ücretsiz katmanı  | 10 | Bu sınır arttırılamıyor. |
| Diğer tüm katmanlar | Sınırsız | Bir kaynak grubu içindeki kaynak sayısıyla ve abonelik başına kaynak grubu sayısına göre sınırlandırılırsınız. |

**Azure portalda**

| Kategori | Sınırlar | Yorumlar |
|:---|:---|:---|
| Günlük sorgusu tarafından döndürülen en fazla kayıt sayısı | 10,000 | Sorgu kapsamını, zaman aralığını ve sorgudaki filtreleri kullanarak sonuçları küçültün. |


**Veri Toplayıcı API 'SI**

| Kategori | Sınırlar | Yorumlar |
|:---|:---|:---|
| Tek gönderi için en büyük boyut | 30 MB | Daha büyük birimleri birden çok gönderime ayırın. |
| Alan değerleri için en büyük boyut  | 32 KB | 32 KB'den daha uzun alanlar kesilir. |

**Arama API 'SI**

| Kategori | Sınırlar | Yorumlar |
|:---|:---|:---|
| Tek bir sorguda döndürülen en fazla kayıt sayısı | 500.000 | |
| Döndürülen en fazla veri boyutu | 64.000.000 bayt (~ 61 MIB)| |
| En fazla sorgu çalışma süresi | 10 dakika | Ayrıntılar için bkz. [zaman aşımları](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| En yüksek istek hızı | AAD kullanıcısı veya istemci IP adresi başına 30 saniye başına 200 istek | Ayrıntılar için [Hız sınırlarına](https://dev.loganalytics.io/documentation/Using-the-API/Limits) bakın. |

**Genel çalışma alanı sınırları**

| Kategori | Sınırlar | Yorumlar |
|:---|:---|:---|
| Tablodaki en fazla sütun         | 500 | |
| Sütun adı için en fazla karakter | 500 | |
| Kapasiteden bölgeler | Orta Batı ABD | Geçici kapasite sınırında olduğundan bu bölgede şu anda yeni bir çalışma alanı oluşturamazsınız. Bu sınır Kasım 2019 sonuna kadar planlanarak yapılır. |
| Veri dışarı aktarma | Şu anda kullanılamıyor | Verileri toplamak ve dışarı aktarmak için Azure Işlevini veya mantıksal uygulamayı kullanın. | 

**Veri alımı birim oranı**


Azure Izleyici, her ay büyüyen bir hızda çok sayıda müşteriye hizmet veren binlerce müşteriyi sunan yüksek ölçekli bir veri hizmetidir. [Tanılama ayarları](../articles/azure-monitor/platform/diagnostic-settings.md) kullanılarak Azure kaynaklarından gönderilen veriler için varsayılan Alım birimi hız sınırı, çalışma alanı başına YAKLAŞıK **6 GB/dak** . Bu, gerçek boyutun günlük uzunluğuna ve sıkıştırma oranına bağlı olarak veri türleri arasında değişiklik gösterebileceğinden yaklaşık bir değerdir. Bu sınır, aracılardan veya [veri TOPLAYıCı API](../articles/azure-monitor/platform/data-collector-api.md)'sinden gönderilen verilere uygulanmaz.

Verileri daha yüksek bir fiyata tek bir çalışma alanına gönderirseniz, bazı veriler bırakılır ve eşik aşılmaya devam edilirken her 6 saatte bir olay, çalışma alanınızda *işlem* tablosuna gönderilir. Alım biriminiz, hız sınırını aşmaya devam ediyorsa veya bir süre önce bu sınıra ulaşmayı bekliyorsanız, bir destek isteği açarak çalışma alanınıza bir artış isteğinde bulunabilir.
 
Çalışma alanınızdaki bu tür bir olay hakkında bildirim almak için, sıfırdan farklı sonuç sayısına sahip uyarı mantığı temeli ile aşağıdaki sorguyu kullanarak bir [günlük uyarı kuralı](../articles/azure-monitor/platform/alerts-log.md) oluşturun.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Log Analytics kullanmaya ne kadar süre kullandığınıza bağlı olarak eski fiyatlandırma katmanlarına erişiminiz olabilir. [Eski Log Analytics fiyatlandırma katmanları](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)hakkında daha fazla bilgi edinin. 