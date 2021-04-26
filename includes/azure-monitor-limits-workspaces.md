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
ms.openlocfilehash: ee7ccb126eb04e168ccc213f0429d41a76a8bc2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103021630"
---
**Veri toplama birimi ve bekletme** 

| Katman | Gün başına sınır | Veri saklama | Yorum |
|:---|:---|:---|:---|
| Geçerli GB başına Fiyatlandırma Katmanı<br>(2018 Nisan 'da tanıtılan) | Sınır yok | 30-730 gün | 31 günden daha fazla veri saklama, ek ücretler için kullanılabilir. Azure Izleyici fiyatlandırması hakkında daha fazla bilgi edinin. |
| Eski ücretsiz Katmanlar<br>(2016 Nisan 'da tanıtılan) | 500 MB | 7 gün | Çalışma alanınız günde 500 MB sınırına ulaştığında, veri alımı durduruluyor ve sonraki günün başlangıcında devam eder. Bir gün için UTC temel alınır. Azure Güvenlik Merkezi tarafından toplanan verilerin bu 500 MB/gün sınırına dahil edilmediğini ve bu sınırın üzerine toplanmaya devam edileceğini unutmayın.  |
| GB katmanı başına eski tek başına<br>(2016 Nisan 'da tanıtılan) | Sınır yok | 30 ila 730 gün | 31 günden daha fazla veri saklama, ek ücretler için kullanılabilir. Azure Izleyici fiyatlandırması hakkında daha fazla bilgi edinin. |
| Düğüm başına eski (OMS)<br>(2016 Nisan 'da tanıtılan) | Sınır yok | 30 ila 730 gün | 31 günden daha fazla veri saklama, ek ücretler için kullanılabilir. Azure Izleyici fiyatlandırması hakkında daha fazla bilgi edinin. |
| Eski Standart katman | Sınır yok | 30 gün  | Saklama ayarlanamıyor |
| Eski Premium katmanı | Sınır yok | 365 gün  | Saklama ayarlanamıyor |

**Abonelik başına çalışma alanı sayısı.**

| Fiyatlandırma katmanı    | Çalışma alanı sınırı | Yorumlar
|:---|:---|:---|
| Ücretsiz katmanı  | 10 | Bu sınır arttırılamıyor. |
| Diğer tüm katmanlar | Sınır yok | Bir kaynak grubu içindeki kaynak sayısıyla ve abonelik başına kaynak grubu sayısına göre sınırlandırılırsınız. |

**Azure portalı**

| Kategori | Sınır | Yorumlar |
|:---|:---|:---|
| Günlük sorgusu tarafından döndürülen en fazla kayıt sayısı | 10,000 | Sorgu kapsamını, zaman aralığını ve sorgudaki filtreleri kullanarak sonuçları küçültün. |


**Veri Toplayıcı API 'SI**

| Kategori | Sınır | Yorumlar |
|:---|:---|:---|
| Tek gönderi için en büyük boyut | 30 MB | Daha büyük birimleri birden çok gönderime ayırın. |
| Alan değerleri için en büyük boyut  | 32 KB | 32 KB'den daha uzun alanlar kesilir. |

**Arama API’si**

| Kategori | Sınır | Yorumlar |
|:---|:---|:---|
| Tek bir sorguda döndürülen en fazla kayıt sayısı | 500.000 | |
| Döndürülen en fazla veri boyutu | 64.000.000 bayt (~ 61 MIB)| |
| En fazla sorgu çalışma süresi | 10 dakika | Ayrıntılar için bkz. [zaman aşımları](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| En yüksek istek hızı | Azure AD kullanıcısı veya istemci IP adresi başına 30 saniyelik 200 istek | Ayrıntılar için [Hız sınırlarına](https://dev.loganalytics.io/documentation/Using-the-API/Limits) bakın. |

**Azure İzleyici Günlükleri bağlayıcısı**

| Kategori | Sınır | Yorumlar |
|:---|:---|:---|
| En fazla kayıt sayısı | 500.000 | |
| En fazla sorgu zaman aşımı | 110 saniye | |
| Grafikler | | Günlükler sayfasında görselleştirme ve bağlayıcı farklı grafik kitaplıkları kullanıyor ve bazı işlevler şu anda bağlayıcıda kullanılamıyor. |

**Genel çalışma alanı sınırları**

| Kategori | Sınır | Yorumlar |
|:---|:---|:---|
| Tablodaki en fazla sütun         | 500 | |
| Sütun adı için en fazla karakter | 500 | |

**<a name="data-ingestion-volume-rate">Veri alımı birim oranı</a>**

Azure Izleyici, her ay büyüyen bir hızda çok sayıda müşteriye hizmet veren binlerce müşteriyi sunan yüksek ölçekli bir veri hizmetidir. Birim hızı sınırı, çok kiracılı ortamdaki ani artış artışlarından Azure Izleyici müşterilerinin yalıtılmayı amaçlamaktadır. Çalışma alanlarında 500 MB (sıkıştırılmış) varsayılan Alım birimi hız eşiği tanımlanmıştır, bu, yaklaşık **6 GB/dak** sıkıştırılmamış değere çevrilir. gerçek boyut, günlük uzunluğuna ve sıkıştırma oranına bağlı olarak veri türleri arasında farklılık gösterebilir. Toplu hız sınırı, [Tanılama ayarları](../articles/azure-monitor/essentials/diagnostic-settings.md)aracılığıyla Azure kaynaklarından alınan verilere uygulanır. Birim hız sınırına ulaşıldığında, bir yeniden deneme mekanizması 30 dakikalık bir dönemde verileri 4 kez almak ve işlem başarısız olursa onu bırakmak üzere çalışır. [Aracılardan](../articles/azure-monitor/agents/agents-overview.md) veya [Veri Toplayıcı API](../articles/azure-monitor/logs/data-collector-api.md)'sinden alınan veriler için uygulanmaz.

Çalışma alanınıza gönderilen veriler, çalışma alanınızda yapılandırılan eşiğin %80 ' inden daha yüksek bir birim hızında olduğunda, eşik aşılmaya devam edilirken 6 saatte bir bir  olay gönderilir. Alınan birim oranı eşiğin üstünde olduğunda, bazı veriler bırakılır ve eşik aşılmaya devam edilirken her 6 saatte bir olay, çalışma alanınızda *işlem* tablosuna gönderilir. Alım hacminin oranı eşiği aşmaya devam ediyorsa veya kısa bir süre sonra bu duruma ulaşmayı bekliyorsanız, bir destek isteği açarak onu ' de artırma isteğinde bulunabilir. 

Alma sınırlarına ulaştığınızda bildirim kuralları oluşturmak üzere uyarı kuralları oluşturmak için bkz. [Azure izleyici 'de Log Analytics çalışma alanının sistem durumunu izleme](../articles/azure-monitor/logs/monitor-workspace.md) .

>[!NOTE]
>Log Analytics kullanmaya ne kadar süre kullandığınıza bağlı olarak eski fiyatlandırma katmanlarına erişiminiz olabilir. [Eski Log Analytics fiyatlandırma katmanları](../articles/azure-monitor/logs/manage-cost-storage.md#legacy-pricing-tiers)hakkında daha fazla bilgi edinin.