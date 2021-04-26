---
title: Azure Izleyici 'de Log Analytics çalışma alanının sistem durumunu izleme
description: Işlem tablosundaki verileri kullanarak Log Analytics çalışma alanınızın sistem durumunun nasıl izleneceğini açıklar.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/20/2020
ms.openlocfilehash: 6f1a23170d84e39e5d531ae4e3a64b59d29bd677
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538844"
---
# <a name="monitor-health-of-log-analytics-workspace-in-azure-monitor"></a>Azure Izleyici 'de Log Analytics çalışma alanının sistem durumunu izleme
Azure Izleyici 'de Log Analytics çalışma alanınızın performansını ve kullanılabilirliğini sürdürmek için, ortaya çıkan sorunları önceden tespit etmeniz gerekir. Bu makalede, [işlem](/azure/azure-monitor/reference/tables/operation) tablosundaki verileri kullanarak Log Analytics çalışma alanınızın sistem durumunun nasıl izleneceği açıklanır. Bu tablo her Log Analytics çalışma alanına dahildir ve çalışma alanınızda oluşan hata ve uyarıları içerir. Çalışma alanınızda önemli olaylar olduğunda, bu verileri düzenli olarak gözden geçirmeniz ve uyarı oluşturmanız gerekir.

## <a name="_logoperation-function"></a>_LogOperation işlevi

Azure Izleyici günlükleri, sorunun oluştuğu çalışma alanındaki [işlem](/azure/azure-monitor/reference/tables/operation) tablosuna herhangi bir sorun hakkındaki ayrıntıları gönderir. **_LogOperation** sistem Işlevi, **işlem** tablosuna dayalıdır ve analiz ve uyarı için basitleştirilmiş bir bilgi kümesi sağlar.

## <a name="columns"></a>Sütunlar

**_LogOperation** işlevi aşağıdaki tablodaki sütunları döndürür.

| Sütun | Açıklama |
|:---|:---|
| TimeGenerated | Olayın UTC olarak gerçekleştiği zaman. |
| Kategori  | İşlem kategori grubu. İşlem türlerini filtrelemek ve daha kesin sistem denetimi ve uyarılar oluşturmaya yardımcı olmak için kullanılabilir. Kategorilerin listesi için aşağıdaki bölüme bakın. |
| İşlem  | İşlem türünün açıklaması. Bu, Log Analytics limitlerden birini, işlem türünü veya bir işlemin parçasını gösterebilir. |
| Level | Sorunun önem düzeyi:<br>-INFO: belirli bir ilgilenilmesi gerekli değil.<br>-Warning: Işlem beklenen şekilde tamamlanmadı ve ilgilenilmesi gerekiyor.<br>-Hata: Işlem başarısız oldu ve acil dikkat gerekiyor. 
| Ayrıntı | İşlemin ayrıntılı açıklaması, varsa belirli bir hata iletisi içerir. |
| _ResourceId | İşlemle ilgili Azure kaynağının kaynak KIMLIĞI.  |
| Bilgisayar | İşlem bir Azure Izleyici aracısıyla ilişkiliyse bilgisayar adı. |
| CorrelationId | Ardışık ilişkili işlemleri gruplandırmak için kullanılır. |


## <a name="categories"></a>Kategoriler

Aşağıdaki tabloda _LogOperation işlevindeki Kategoriler açıklanmaktadır. 

| Kategori | Açıklama |
|:---|:---|
| Alma           | Veri alma işleminin parçası olan işlemler. Daha fazla ayrıntı için aşağıya bakın. |
| Aracı               | Aracı yüklemesinde bir sorun olduğunu gösterir. |
| Veri toplama     | Veri koleksiyonları işlemleriyle ilgili işlemler. |
| Çözüm hedefleme  | *Configurationscope* türü işlem işlendi. |
| Değerlendirme çözümü | Bir değerlendirme işlemi yürütüldü. |


### <a name="ingestion"></a>Alma
Alma işlemleri, Azure Log Analytics çalışma alanı sınırlarına ulaşma hakkında bildirim de dahil olmak üzere veri alımı sırasında oluşan sorunlardır. Bu kategorideki hata koşulları veri kaybı önerebilir, bu nedenle izlenmesi özellikle önemlidir. Aşağıdaki tabloda bu işlemler hakkında ayrıntılı bilgi verilmektedir. Log Analytics çalışma alanları için hizmet sınırları için bkz. [Azure izleyici hizmet limitleri](../service-limits.md#log-analytics-workspaces) .


| İşlem | Level | Ayrıntı | İlgili makale |
|:---|:---|:---|:---|
| Özel günlük | Hata   | Özel alanlar sütun sınırına ulaşıldı. | [Azure İzleyici hizmet sınırları](../service-limits.md#log-analytics-workspaces) |
| Özel günlük | Hata   | Özel Günlükler alınamadı. | |
| Veriyi. | Hata | Yapılandırma hatası algılandı. | |
| Veri toplama | Hata   | İstek, ayarlanan gün sayısından daha önce oluşturulduğundan veriler bırakıldı. | [Azure İzleyici Günlükleri ile kullanımı ve maliyetleri yönetme](./manage-cost-storage.md#alert-when-daily-cap-reached)
| Veri toplama | Bilgi    | Koleksiyon makinesi yapılandırması algılandı.| |
| Veri toplama | Bilgi    | Yeni gün nedeniyle veri toplama başlatıldı. | [Azure İzleyici Günlükleri ile kullanımı ve maliyetleri yönetme](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Veri toplama | Uyarı | Günlük sınıra ulaşıldığından veri toplama durdu.| [Azure İzleyici Günlükleri ile kullanımı ve maliyetleri yönetme](./manage-cost-storage.md#alert-when-daily-cap-reached) |
| Veri işleme | Hata   | JSON biçimi geçersiz. | [HTTP Veri Toplayıcı API 'SI ile günlük verilerini Azure Izleyici 'ye gönderme (Genel Önizleme)](../logs/data-collector-api.md#request-body) | 
| Veri işleme | Uyarı | Değer izin verilen en büyük boyuta kırpılmıştır. | [Azure İzleyici hizmet sınırları](../service-limits.md#log-analytics-workspaces) |
| Veri işleme | Uyarı | Boyut sınırına ulaşıldığından alan değeri kırpılmış. | [Azure İzleyici hizmet sınırları](../service-limits.md#log-analytics-workspaces) | 
| Alım oranı | Bilgi | Alım oranı limiti %70 yaklaşılıyor. | [Azure İzleyici hizmet sınırları](../service-limits.md#log-analytics-workspaces) |
| Alım oranı | Uyarı | Sınıra yaklaştığı alım oranı limiti. | [Azure İzleyici hizmet sınırları](../service-limits.md#log-analytics-workspaces) |
| Alım oranı | Hata   | Hız sınırına ulaşıldı. | [Azure İzleyici hizmet sınırları](../service-limits.md#log-analytics-workspaces) |
| Depolama | Hata   | Kullanılan kimlik bilgileri geçersiz olduğundan depolama hesabına erişilemiyor.  |



   

## <a name="alert-rules"></a>Uyarı kuralları
Log Analytics çalışma alanınızda bir sorun algılandığında kullanım dışı olarak bildirilmesi için Azure Izleyici 'de [günlük sorgu uyarılarını](../alerts/alerts-log-query.md) kullanın. Maliyetlerinizi en aza indirerek sorunları zamanında cevaplamanıza izin veren bir strateji kullanmanız gerekir. Aboneliğiniz, değerlendirilme sıklığına bağlı olarak her bir uyarı kuralı için ücretlendirilir.

Önerilen strateji, sorunun düzeyine bağlı olarak iki uyarı kuralıyla başlamasıdır. Hatalar için 5 dakikada bir kısa bir sıklık ve uyarılar için 24 saat gibi daha uzun bir sıklık kullanın. Hatalar olası veri kaybını gösterdiğinden, herhangi bir kaybı en aza indirmek için bunlara hızlıca yanıt vermek istersiniz. Uyarılar genellikle hemen ilgilenilmesi gerekmeyen bir sorunu gösterir, böylece bunları her gün gözden geçirebilirsiniz.

Günlük uyarı kuralları oluşturmak için [Azure izleyici 'yi kullanarak günlük uyarılarını oluşturma, görüntüleme ve yönetme](../alerts/alerts-log.md) içindeki işlemi kullanın. Aşağıdaki bölümlerde her kurala ilişkin ayrıntılar açıklanır.


| Sorgu | Eşik değeri | Dönem | Sıklık |
|:---|:---|:---|:---|
| `_LogOperation | where Level == "Error"`   | 0 | 5 | 5 |
| `_LogOperation | where Level == "Warning"` | 0 | 1440 | 1440 |

Bu uyarı kuralları hata veya uyarı ile tüm işlemlerle aynı yanıtı verir. Uyarı üreten işlemlere daha aşina olduğunuzda, belirli işlemler için farklı yanıt vermek isteyebilirsiniz. Örneğin, belirli işlemler için farklı kişilere bildirim göndermek isteyebilirsiniz. 

Belirli bir işlem için bir uyarı kuralı oluşturmak için, **Kategori** ve **işlem** sütunlarını içeren bir sorgu kullanın. 

Aşağıdaki örnek, alma birimi oranı sınırın %80 ' una ulaştığında bir uyarı uyarısı oluşturur.

- Hedef: Log Analytics çalışma alanınızı seçin
- Ölçütlere
  - Sinyal adı: özel günlük araması
  - Arama sorgusu: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
  - Temel alan: sonuç sayısı
  - Koşul: büyüktür
  - Eşik: 0
  - Süre: 5 (dakika)
  - Sıklık: 5 (dakika)
- Uyarı kuralı adı: günlük veri sınırına ulaşıldı
- Önem derecesi: uyarı (sev 1)


Aşağıdaki örnek, veri toplama günlük sınıra ulaştığında bir uyarı uyarısı oluşturur. 

- Hedef: Log Analytics çalışma alanınızı seçin
- Ölçütlere
  - Sinyal adı: özel günlük araması
  - Arama sorgusu: `_LogOperation | where Category == "Ingestion" | where Operation == "Data collection Status" | where Level == "Warning"`
  - Temel alan: sonuç sayısı
  - Koşul: büyüktür
  - Eşik: 0
  - Süre: 5 (dakika)
  - Sıklık: 5 (dakika)
- Uyarı kuralı adı: günlük veri sınırına ulaşıldı
- Önem derecesi: uyarı (sev 1)



## <a name="next-steps"></a>Sonraki adımlar

- [Günlük uyarıları](../alerts/alerts-log.md)hakkında daha fazla bilgi edinin.
- Çalışma alanınız için [sorgu denetim verilerini toplayın](./query-audit.md) .
