---
title: Azure SQL Edge için sürüm notları
description: Azure SQL Edge görüntülerinde nelerin yeni olduğunu veya değiştiğini açıklayan sürüm notları.
keywords: sürüm notları SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: 6218715878ec40fecee79f1c93bf2ca1820af007
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277020"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge sürüm notları 

Bu makalede, yenilikler ve her yeni Azure SQL Edge derlemesi ile nelerin değiştiğini açıklanmaktadır.

## <a name="azure-sql-edge-103"></a>Azure SQL Edge 1.0.3

SQL Engine derleme 15.0.2000.1554

### <a name="fixes"></a>Düzeltmeler

- ONNX çalışma zamanını 1.5.3 'e yükselt
- Microsoft. SqlServer. DACFx 150.5084.2 sürümüne Güncelleştir
- Çeşitli hata düzeltmeleri  
   
## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

SQL Engine derleme 15.0.2000.1554

### <a name="fixes"></a>Düzeltmeler

- T-SQL akışı  
   - Akış nesneleri için sahiplik ve izinleri çözme
   - Günlük döndürme ve günlük önek olarak günlüğe kaydetme geliştirmeleri
   - Azure Stream Analytics: günlük iyileştirmeler, bağdaştırıcılarda hata kodu/hata iletilerini geliştirme 

- ONNX
    - Paralel sorgu senaryosu ve model Temizleme hataları için hata düzeltmeleri
    - ONNX çalışma zamanı 1.5.1 'e yükseltildi

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

SQL Engine derleme 15.0.2000.1553

### <a name="whats-new"></a>Yenilikler

- Hesaplanan sütunlarda tanımlanmış Date_Bucket ifadelerine izin verin.

### <a name="fixes"></a>Düzeltmeler

- Sonsuz bir zaman aşımı ile etkin bir bekletme ilkesi olan bir tabloyu bırakma için bekletme ilkesi düzeltilmesi
- Akış özellikleri ve bekletme-ilke özellikleri için DacFx dağıtım desteği 
- Bir SAS URL 'sindeki iç içe bir klasörden dağıtımı etkinleştirmek için DacFx dağıtım onarımı 
- Hata iletilerinde uzun sütun adlarını desteklemek için düzeltilmesi tahmın eder

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

SQL Engine derleme 15.0.2000.1552

### <a name="whats-new"></a>Yenilikler
- Ubuntu 18,04 tabanlı kapsayıcı görüntüleri 
- Ve `IGNORE NULL` işlevleri ile ilgili ve `RESPECT NULL` sözdizimi desteği `LAST_VALUE()` `FIRST_VALUE()` 
- ONNX ile tahmın için güvenilirlik iyileştirmeleri
- Veri bekletme ilkesine göre temizleme desteği:
   - Sorun giderme için bir bekletme temizleme görevi için halka arabelleği desteği
- Yeni özellik desteği: 
   - Hızlı kurtarma
   - Sorguları otomatik olarak ayarlama
   - Paralel yürütme senaryoları
- Düşük güç modu için güç tasarrufu iyileştirmeleri
- Akış yeni özellik desteği: 
   - [Anlık görüntü pencereleri](/stream-analytics-query/snapshot-window-azure-stream-analytics): yeni bir pencere türü, aynı anda gelen olaylara göre gruplandırma yapmanıza olanak sağlar.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) ve [collecttop](/stream-analytics-query/collecttop-azure-stream-analytics) , analitik işlevler olarak etkinleştirilebilir. Seçtiğiniz sütuna göre sıralanmış kayıtları döndürebilirsiniz. Pencerenin bir parçası olması gerekmez. 
   - [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)geliştirmeleri. 

### <a name="fixes"></a>Düzeltmeler
- T-SQL akış işlemlerinde sorun gidermeye yönelik ek hata iletileri ve ayrıntıları 
- Boşta modunda pil ömrünü koruma geliştirmeleri 
- T-SQL streaming Engine düzeltmeleri: 
   - Durdurulan akış işleri için Temizleme 
   - Yerelleştirme düzeltmeleri 
   - Gelişmiş Unicode işleme 
   - SQL Edge T-SQL akışı için geliştirilmiş hata ayıklama, kullanıcıların get_streaming_job iş hatası hatalarını sorgulayabilmesine izin verir
- Veri bekletme ilkesine göre Temizleme: 
    - Bekletme-ilke oluşturma ve Temizleme senaryoları için düzeltmeler
- Düşük güç modundaki güç tasarrufunu artırmak için arka plan Zamanlayıcı görevlerine yönelik düzeltmeler

### <a name="known-issues"></a>Bilinen sorunlar 
- Date_Bucket T-SQL işlevi, hesaplanan bir sütunda kullanılamaz.


## <a name="ctp-23"></a>CTP 2,3
SQL Engine derleme 15.0.2000.1549
### <a name="whats-new"></a>Yenilikler
- Date_Bucket () işlevindeki özel kaynakları destekleme 
- SQL dağıtımının bir parçası olarak BACPAC dosyaları desteği
- Veri bekletme ilkesine göre temizleme desteği:      
   - Bekletme ilkesini etkinleştirmek için DDL desteği 
   - Saklı yordamlar ve arka plan temizleme görevi için Temizleme
   - Temizleme görevlerini izlemek için genişletilmiş olaylar

### <a name="fixes"></a>Düzeltmeler
- T-SQL akış işlemlerinde sorun gidermeye yönelik ek hata iletileri ve ayrıntıları 
- Boşta modunda pil ömrünü koruma geliştirmeleri 
- T-SQL streaming Engine: 
   - Alt akışlı atlamalı penceresinde takılı filigran için çözüm 
   - Kullanıcı tarafından işlem yapılabilir bir hata olarak toplandığından emin olmak için Framework özel durum işleme için çözüm


## <a name="ctp-22"></a>CTP 2,2
SQL Engine derleme 15.0.2000.1546
### <a name="whats-new"></a>Yenilikler
- Kök olmayan kapsayıcılar için destek 
- Kullanım ve tanılama veri toplama desteği 
- T-SQL akış güncelleştirmeleri:
   - Stream nesne adları için Unicode karakterler desteği

### <a name="fixes"></a>Düzeltmeler
- T-SQL akış güncelleştirmeleri:
   - İşlem Temizleme iyileştirmeleri
   - Günlüğe kaydetme ve tanılama geliştirmeleri
- Veri alımı için performans iyileştirmesi

## <a name="ctp-21"></a>CTP 2,1 
SQL Engine derleme 15.0.2000.1545
### <a name="fixes"></a>Düzeltmeler
- ARM 'de bir CPUıD sorununu işlemek için önceden yapılan-ONNX modelleriyle izin verildi 
- T-SQL akışı başladığında hata yolunun işlenmesi geliştirildi
- Veri olmadığında iş ölçümlerinde eşik gecikmesi değeri düzeltildi.
- Bağdaştırıcının toplu işler arasında bir değişken şeması olduğunda çıkış bağdaştırıcısında sorun giderme  

## <a name="ctp-20"></a>CTP 2,0 
SQL Engine derleme 15.0.2000.1401
### <a name="whats-new"></a>Yenilikler
-   Ürün adı *Azure SQL Edge* 'e güncelleştirildi
-  Date_Bucket işlevi:
    - Tarih, saat ve tarih saat türleri için destek
- ONNX ile tahmın edin:
    - ÇALıŞMA zamanı parametresi için ONNX gereksinimi  
- T-SQL akış desteği (sınırlı Önizleme) 
 
### <a name="known-issues"></a>Bilinen sorunlar

- Sorun: bir zamanlama sorunu nedeniyle başlatma sırasında DACPAC uygulama ile ilgili olası sorunlar.
- Geçici çözüm: SQL Server yeniden başlatın. Aksi takdirde, kapsayıcı DACPAC 'yi uygulamayı yeniden dener.

### <a name="request-support"></a>Destek iste
Destek [sayfasında](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)destek isteyebilirsiniz. Aşağıdaki alanları seçin: 
- **Sorun türü**: *Teknik* 
- **Hizmet**: *IoT Edge*
- **Sorun türü**: *sorunum IoT Edge modülüyle ilişkili*
- **Sorun alt türü**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Örnek bir destek bileti gösteren ekran görüntüsü.":::

## <a name="ctp-15"></a>CTP 1,5
SQL Engine derleme 15.0.2000.1331
### <a name="whats-new"></a>Yenilikler
- Date_Bucket işlevi:
    - DateTimeOffset türü için destek
- ONNX modelleriyle tahmın edin:
  - NVARCHAR desteği
 
## <a name="ctp-14"></a>CTP 1,4
SQL Engine derleme 15.0.2000.1247
### <a name="whats-new"></a>Yenilikler
-   ONNX modelleriyle tahmın edin:
    - VARCHAR desteği
    - ONNX çalışma zamanı 1,0 sürümüne geçiş 

- Aşağıdaki özellikler etkinleştirilmiştir:
  - CDC desteği
  - Sıkıştırma içeren geçmiş tablosu
  - Günlük okuma için daha yüksek ölçekli bir faktör
  - Batch modu, aşağı doğru filtrele
  - İleriye okuma iyileştirmeleri
 
## <a name="ctp-13"></a>CTP 1,3
SQL Engine derleme 15.0.2000.1147
### <a name="whats-new"></a>Yenilikler
- Azure IoT Portal dağıtımı: 
    - AMD64 ve ARM görüntülerini dağıtma desteği
    - Akış işi oluşturma desteği
    - DACPAC dağıtımı
- ONNX modelleriyle tahmın edin:
    - Sayısal tür desteği
- Aşağıdaki özellikler etkinleştirilmiştir:
    - Toplam sütun deposu taramasına aşağı itme
    - Mutlu-go-yuvarlak taramalar
- Ayak izi ve bellek-tüketim azaltma işi
