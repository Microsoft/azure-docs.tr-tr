---
title: Verizon özel raporları | Microsoft Docs
description: 'Aşağıdaki raporlar kullanarak, CDN kullanım biçimlerini görüntüleyebilirsiniz: Bant genişliğini, aktarılan veriler, isabetleri, önbellek durumları, isabetli önbellek okuması oranı, IPv4/IPv6 veri aktarılır.'
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 15f17ac6556c4ff731372dc7f738d0f58bdc3e31
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593309"
---
# <a name="custom-reports-from-verizon"></a>Verizon özel raporları

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Verizon özel raporları Yönet portal Verizon profilleri için kullanarak, edge CNAME'ler raporlar için toplanacak veri türü tanımlayabilirsiniz.


## <a name="accessing-verizon-custom-reports"></a>Verizon özel raporları erişme
1. CDN profili dikey penceresinden tıklayın **Yönet** düğmesi.
   
    ![CDN profili Yönet düğmesi](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN yönetim portalına açılır.
2. Üzerine **Analytics** sekmesine ve ardından üzerine **özel raporlar** açılır öğesi. Tıklayın **kenar CNAME'ler**.
   
    ![CDN yönetim portalına - özel menü bildirir.](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Edge CNAME'ler özel rapor
Edge CNAME'ler özel rapor için özel rapor günlük etkinleştirildiği edge CNAME'ler isabet sayısı ve aktarılan veriler istatistikler sağlar. Azure CDN uç noktası ana bilgisayar adları ve tüm ilişkili özel etki alanı ana bilgisayar adları Edge CNAME'ler oluşur. 

Bir edge CNAME'ın özel raporlama özelliği etkinleştirdikten sonra bir saat özel rapor veri günlük kaydı başlar. Rapor verileri, belirli bir platform için veya tüm platformlar için bir kenar CNAME'ler raporu oluşturarak görüntüleyebilirsiniz. Bu rapor için kapsamı özel rapor verilerini belirtilen süre içinde toplanan kenar CNAME'ler sınırlıdır. Ölçümleri seçeneğinde tanımlı ölçüm göre bir grafik ve veri tablosu için 10 üst kenarı CNAME'ler CNAME'ler rapor edge oluşur. 

Özel bir rapor, aşağıdaki rapor seçeneklerini tanımlayarak oluştur:

- Ölçümleri: Aşağıdaki seçenekler desteklenir:

   - İsabet sayısı: Özel raporlama özelliği etkin olduğu bir kenar CNAME yönlendirilen isteklerin toplam sayısını gösterir. Bu ölçüm, istemciye döndürülen durum kodu içermez.

   - Aktarılan veriler: Özel raporlama özelliği etkin olduğu bir kenar CNAME yönlendirilen istekler için toplam HTTP istemciler (örneğin, web tarayıcıları) için edge sunuculardan aktarılan veri miktarını gösterir. Yanıt gövdesi için HTTP yanıt üst bilgilerini ekleyerek, aktarılan veri miktarı hesaplanır. Sonuç olarak, her varlık için aktarılan veri miktarı, gerçek dosya boyutundan büyük.

- Gruplandırmaları: Çubuk grafik gösterilen istatistikleri türünü belirler. Aşağıdaki seçenekler desteklenir:

   - HTTP yanıt kodları: İstatistikleri HTTP yanıt koduna göre düzenler (örneğin, 200, 403, istemciye döndürülen vb.). 

   - Önbellek durumu: İstatistikleri önbellek durumuna göre düzenler.


Rapor için tarih aralığını ayarlamak için ya da bir önceden tanımlanmış tarih aralığı gibi seçebileceğiniz **Bugün** veya **bu hafta**, aşağı açılan listesinden veya, seçebilirsiniz **özel** ve el ile Takvim simgelere tıklayarak bir tarih aralığı girin. 

Tarih aralığı seçtikten sonra tıklayın **Git** raporu oluşturmak için.

Excel simgesine sağ tıklayarak verileri Excel biçiminde dışarı aktarabilirsiniz **Git** düğmesi.

![CNAME'ler raporu](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Edge CNAME'ler özel rapor alanları

| Alan                     | Açıklama   |
|---------------------------|---------------|
| 2xx                       | İstekler veya aktarılan veri (MB) 2xx HTTP durum kodunda sonuçlanan CNAME edge için toplam sayıyı belirtir (örneğin, 200 Tamam). |
| 3xx                       | İstekler veya aktarılan veri (MB) 3xx HTTP durum kodunda sonuçlanan CNAME edge için toplam sayıyı belirtir (örneğin, 302 bulunamadı veya 304 değiştirilmedi. |
| 4xx                       | İstekler veya aktarılan veri (MB) HTTP 4xx durum kodunda sonuçlanan CNAME edge için toplam sayıyı belirtir (örneğin, 400 Hatalı istek, 403 Yasak veya 404 bulunamadı). |
| 5XX                       | Edge 5xx HTTP durum koduna (örneğin, 500 İç sunucu hatası veya 502 hatalı ağ geçidi) sonuçları CNAME için istekleri ya da aktarılan veri (MB) toplam sayısını gösterir. |
| Önbellek isabet yüzdesi               | Önbellekten doğrudan istemciye sunulduğunu önbelleğe istek yüzdelerini gösterir. |
| İsabetli Önbellek okuma sayısı                | İstekleri ya da aktarılan veri (MB) önbellek isabet (örneğin, TCP_EXPIRED_HIT, TCP_HIT veya TCP_PARTIAL_HIT) içinde sonuçları CNAME edge için toplam sayısını gösterir. İstenen içeriği önbelleğe alınmış bir sürümü bulunan bir önbellek isabet gerçekleşir. |
| Aktarılan veri (MB)     | Toplam aktarılan veri miktarı (MB) uç sunuculardan CNAME edge için HTTP istemcilere (web tarayıcıları) gösterir. Yanıt gövdesi için HTTP yanıt üst bilgilerini ekleyerek, aktarılan veri miktarı hesaplanır. Sonuç olarak, her varlık için aktarılan veri miktarı, gerçek dosya boyutundan büyük. |
| Açıklama               | Kendi ana bilgisayar adı bir çizgiyle CNAME tanımlar |
| İsabet sayısı                      | CNAME edge istekleri toplam sayısını gösterir |
| İsabetsiz                    | İstekleri ya da aktarılan veri (MB) önbellek isabetsizliği içinde (örneğin, TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS veya TCP_MISS) sonuçları CNAME edge için toplam sayısını gösterir. Önbellek isabetsizliği talep edilen içeriği istek kabul uç sunucuda önbelleğe alınmamış oluşur. | 
| Önbellek yok                  | İstekleri ya da aktarılan veri (MB) için edge CONFIG_NOCACHE önbellek durum koduna sonuçları CNAME toplam sayısını gösterir.  |
| Diğer                     | İsteklerini veya 2xx - 5xx aralığında dışında kalan bir HTTP durum kodu ile sonuçlanır, edge CNAME için aktarılan (MB) belirtilen veri toplam sayısını gösterir. |
| Platform                  | Edge CNAME'ın trafiği işleme platformu belirtir. |
| Atanmamış               | Toplam istekler veya aktarılan veri (MB) için edge CNAME bilgi değil hangi önbellek durum kodu veya HTTP durum kodunu günlüğe gösterir.  |
| Uncacheable               | İstekleri ya da aktarılan veri (MB) bir UNCACHEABLE önbellek durum kodunda sonuçlanan CNAME edge için toplam sayısını gösterir.  |


## <a name="considerations"></a>Dikkat edilmesi gerekenler
Raporlar yalnızca son 18 ay içinde oluşturulabilir.

