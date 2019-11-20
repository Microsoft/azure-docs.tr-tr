---
title: Çekirdek Verizon raporları | Microsoft Docs
description: 'Aşağıdaki raporlar kullanarak, CDN kullanım biçimlerini görüntüleyebilirsiniz: Bant genişliğini, aktarılan veriler, isabetleri, önbellek durumları, isabetli önbellek okuması oranı, IPv4/IPv6 veri aktarılır.'
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d48ddafdc1ec30ae1533b3a3101582f33e7f4b5c
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594153"
---
# <a name="core-reports-from-verizon"></a>Verizon'dan alınan çekirdek raporlar

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Verizon profillerini yönet portal Verizon'dan alınan çekirdek raporlar kullanarak, aşağıdaki raporları ile CDN için kullanım düzenlerini görüntüleyebilirsiniz:

* Bant genişliği
* Aktarılan veriler
* İsabet sayısı
* Önbellek durumları
* İsabetli Önbellek okuması oranı
* IPv4/IPv6 aktarılan veriler

## <a name="accessing-verizon-core-reports"></a>Verizon çekirdek raporlar erişme
1. CDN profili dikey penceresinden tıklayın **Yönet** düğmesi.
   
    ![CDN profili Yönet düğmesi](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN yönetim portalına açılır.
2. Üzerine **Analytics** sekmesine ve ardından üzerine **alınan çekirdek raporlar** açılır öğesi. Bir rapor menüsünde tıklayın.
   
    ![CDN yönetim portalına - alınan çekirdek Raporlar menüsü](./media/cdn-reports/cdn-core-reports.png)

3. Her bir rapor için bir tarih aralığındaki seçin **tarih aralığı** listesi. Bir önceden tanımlanmış tarih aralığı gibi seçebilirsiniz **Bugün** veya **bu hafta**, veya seçebilirsiniz **özel** ve el ile Takvim simgelere tıklayarak bir tarih aralığı girin. 

4. Bir tarih aralığı seçtikten sonra tıklayın **Git** raporu oluşturmak için. 

4. Verileri Excel biçiminde dışarı aktarmak istiyorsanız, Excel simgesini tıklayın **Git** düğmesi.

## <a name="bandwidth"></a>Bant genişliği
Bant genişliği rapor, belirli süre boyunca, MB/sn cinsinden HTTP ve HTTPS için CDN bant genişliği kullanımını gösteren bir grafik ve veri tablosu oluşur. Tüm POP boyunca veya belirli POP için bant genişliği kullanımını görüntüleyebilirsiniz. Bu rapor ani trafik değişiklikleri ve dağıtım için POP görüntülemenize olanak sağlar.

Gelen **kenar düğümleri** listesinden **tüm kenar düğümleri** tüm düğümleri gelen trafiği görmek veya belirli bir bölge seçin.

Rapor, beş dakikada bir güncelleştirilir.

![Bant genişliği raporu](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Aktarılan veriler
Bu rapor, belirli süre boyunca, GB cinsinden HTTP ve HTTPS için CDN trafik kullanımını gösteren bir grafik ve veri tablosu oluşur. Belirli POP için veya tüm POP'ları arasındaki trafiği kullanım görüntüleyebilirsiniz. Bu rapor ani trafik değişiklikleri ve dağıtım arasında POP görüntülemenize olanak sağlar.

Gelen **kenar düğümleri** listesinden **tüm kenar düğümleri** tüm düğümleri gelen trafiği görmek veya belirli bir bölge seçin.

Rapor, beş dakikada bir güncelleştirilir.

![Rapor aktarılan veriler](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>İsabet sayısı (durum kodları)
Bu rapor isteği durum kodları, içerik dağıtımını açıklar. İçerik için her istek HTTP durum kodu oluşturur. Durum kodu istek uç POP işlenme açıklar. Örneğin, bir hata oluştuğunu 4xx durum kodları gösterir ancak istek başarıyla bir istemciye sunulduğu 2xx durum kodu gösterir. HTTP durum kodları hakkında daha fazla bilgi için bkz. [listesi HTTP durum kodları](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

![İsabet sayısı raporu](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Önbellek durumları
Bu rapor, Önbelleği İsabetli ve İsabetsiz Önbellek okuma sayısı istemci istekleri için dağıtımını açıklar. En hızlı performansı Önbelleği İsabetli Okuma sonuçlandığından İsabetsiz Önbellek okuma sayısı ve süresi dolmuş önbellek isabet en aza indirerek veri teslim hızını en iyi duruma getirebilirsiniz. 

İsabetsiz önbellek okuma sayısı azaltmak için aşağıdaki kullanımını en aza indirmek için kaynak sunucunuza yapılandırın: 
 * `no-cache` Yanıt Üstbilgileri
 * Sorgu dizesi, kesinlikle gerekli sürece önbelleğe alma  
 * Olmayan önbelleğe kaydedilemeyen yanıt kodları

Süresi dolmuş önbellek isabet azaltmak için bir varlığın ayarlamak `max-age` kaynak sunucu isteklerinin sayısını en aza indirmek için uzun bir süre için.

![Önbellek durumları raporu](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Ana önbellek durumları şunlardır:
* TCP_HIT: Edge sunucudan hizmet. Nesne önbellekte olduğu ve onun max-age aştı değil.
* TCP_MISS: Kaynak sunucudan hizmet. Nesne önbellekte değil ve kaynağa geri yanıt:.
* TCP_EXPIRED _MISS: Kaynağı yeniden doğrulanırken sonra kaynak sunucudan hizmet. Nesne önbellekte oldu, ancak kendi max-age aşmıştı. Kaynaktan yeni bir yanıt değiştiriliyor önbellek nesnesi içinde bir yeniden doğrulanırken başlangıç sonuçlandı.
* TCP_EXPIRED _HIT: Kaynağı yeniden doğrulanırken sonra Edge'den sunulur. Nesne önbellekte oldu, ancak kendi max-age aşmıştı. Bir kaynak sunucuya yeniden doğrulanırken değiştirilmemiş önbellek nesnesinde sonuçlandı.

### <a name="full-list-of-cache-statuses"></a>Önbellek durumları tam listesi
* TCP_HIT - istek istemci için doğrudan POP sunulduğunda, bu durum bildirilir. İstemcinin en yakın POP önbelleğe alınır ve geçerli yaşam süresi (TTL) sahip bir varlık hemen POP sunulur. TTL şu yanıt üst bilgilerini tarafından belirlenir:
  
  * Cache-Control: s-maxage
  * Cache-Control: Maksimum yaş
  * Süre sonu
* TCP_MISS: Bu durum, istenen varlık önbelleğe alınmış bir sürümü üzerinde istemcinin en yakın POP bulunamadı gösterir. Varlık, kaynak sunucuyu veya bir kaynak kalkan sunucu istenmektedir. Bir varlık, kaynak sunucu veya kaynak kalkan sunucu döndürürse, istemciye sunulan ve hem istemci hem de uç sunucusunu önbelleğe alınır. Aksi takdirde, olmayan-200 durum kodu (örneğin, 403 Yasak veya 404 bulunamadı) döndürülür.
* TCP_EXPIRED_HIT: Süresi dolmuş bir TTL ile bir varlığı hedefleyen bir isteğin doğrudan POP istemciye sunulduğu bu durum raporlanır. Örneğin, varlık max-age ne zaman sona erdi. 
  
   Süresi dolmuş bir isteği, genellikle kaynak sunucuya yeniden doğrulama istekte sonuçlanır. Varlık daha yeni bir sürümü yok TCP_EXPIRED_HIT durumu oluşmasına kaynak sunucu belirtmeniz gerekir. Bu durum genellikle bir varlığın Cache-Control ve Expires üst bilgileri güncelleştirmede sonuçlanır.
* TCP_EXPIRED_MISS: Bu durum, süresi dolmuş bir önbelleğe alınan varlık daha yeni bir sürümü için istemci POP sunulduğunda bildirilir. Önbelleğe alınan bir varlık için TTL'nin süresi Bu durum oluşur (örneğin, süresi doldu, max-age) ve kaynak sunucu, varlık daha yeni bir sürümünü döndürür. Varlık bu yeni sürümü, önbelleğe alınmış sürümün yerine istemciye hizmet verir. Ayrıca, bu uç sunucu ve istemci önbelleğe alınır.
* CONFIG_NOCACHE: Bu durum, müşteriye özgü yapılandırma uç POP varlık önbelleğe alınmasını önledi olduğunu gösterir.
* HİÇBİRİ - bu durum, bir önbellek içerik güncellik denetimi gerçekleştirilmemiş gösterir.
* TCP_CLIENT_REFRESH_MISS: Bir edge POP yeni bir sürümü eski bir varlık kaynak sunucudan almak için bir tarayıcı gibi bir HTTP istemci zorlar, bu durum raporlanır. Varsayılan olarak, sunucular, uç sunucularda kaynak sunucudan varlık yeni bir sürümünü almak için zorlama gelen HTTP istemci engeller.
* TCP_PARTIAL_HIT: Bu durum, bir bayt aralığı isteği kısmen önbelleğe alınmış bir varlık için isabet sonuçlandığında bildirilir. İstenen bayt aralığı, istemciye POP hemen sunulur.
* UNCACHEABLE: Bu durum, bir varlık ayarlandığında bildirilir `Cache-Control` ve `Expires` üst bilgileri gösterir, POP veya HTTP istemcisi tarafından önbelleğe alınması gerektiğini değil. Bu tür istekleri kaynak sunucudan sunulur.

## <a name="cache-hit-ratio"></a>İsabetli Önbellek okuması oranı
Bu rapor, doğrudan önbellekten sunulduğunu önbelleğe alınmış istekler yüzdesini gösterir.

Raporu aşağıdaki bilgileri sağlar:

* Talep edilen içeriği, istemciye en yakın POP önbelleğe alınmadı.
* İstek, doğrudan ağımız edge'den sunulduğu.
* İstek kaynak sunucu ile yeniden doğrulama gerekli değil.

Rapor içermez:

* Ülke/filtreleme seçeneklerini bölge nedeniyle reddedildi istekler.
* Bunlar değil konumlandırılmalıdır başlıklarını belirtmek varlıklar için istekler. Örneğin, `Cache-Control: private`, `Cache-Control: no-cache`, veya `Pragma: no-cache` üstbilgileri bir varlık önbelleğe alınmasını engellemek.
* Kısmen önbelleğe alınmış içeriği için bayt aralığı isteklerini.

Formülü aşağıdaki gibidir: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![İsabetli Önbellek okuması oranı raporu](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>IPv4/IPv6 aktarılan veriler
Bu rapor, IPv4 ve IPv6 trafiği kullanım dağıtımı gösterir.

![IPv4/IPv6 aktarılan veriler](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>Dikkat edilmesi gerekenler
Raporlar yalnızca son 18 ay içinde oluşturulabilir.

