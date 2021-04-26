---
title: Azure ön kapısı-arka uç sistem durumu izleme | Microsoft Docs
description: Bu makale, Azure ön kapısının arka uçlarınızın sistem durumunu nasıl izlediğini anlamanıza yardımcı olur
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: dd56740b7153cdbafdfa847a22d34b57f862cdf3
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550753"
---
# <a name="health-probes"></a>Durum araştırmaları

Belirli bir ön kapılı ortamda her bir arka ucun durumunu ve yakınlığını öğrenmek için, her bir ön kapı ortamı, yapılandırılmış her bir arka uçınızdan düzenli olarak yapay bir HTTP/HTTPS isteği gönderir. Ön kapı daha sonra istemci isteklerinizi yönlendirmek üzere "en iyi" arka uç kaynaklarını tespit etmek için bu yanıtları araştırmayı kullanır. 

> [!WARNING]
> Ön kapıda genel olarak çok uç ortamları olduğundan, arka uçlarınız için sistem durumu araştırma birimi, yapılandırılan durum araştırma sıklığı uyarınca dakikada her dakikada en fazla 1200 istek kadar yüksek olabilir. Varsayılan araştırma sıklığı 30 saniye ile, arka ucunuzdaki araştırma hacmi, dakikada yaklaşık 200 istek olmalıdır.

> [!NOTE]
> Ön kapı HTTP/HTTPS araştırmaları, şu `User-Agent` değere sahip üst bilgi kümesiyle gönderilir: `Edge Health Probes` . 

## <a name="supported-protocols"></a>Desteklenen protokoller

Ön kapı, HTTP veya HTTPS protokolleri üzerinden araştırmaları göndermeyi destekler. Bu araştırmalar istemci isteklerini yönlendirmek için yapılandırılmış olan TCP bağlantı noktaları üzerinden gönderilir ve bu ayar geçersiz kılınamaz.

## <a name="supported-http-methods-for-health-probes"></a>Durum araştırmaları için desteklenen HTTP yöntemleri

Ön kapı, sistem durumu araştırmalarını göndermek için aşağıdaki HTTP yöntemlerini destekler:

1. **Al:** GET yöntemi, Istek URI 'SI tarafından tanımlanan bilgilerin (bir varlık biçiminde) alınması anlamına gelir.
2. **Baş:** HEAD yöntemi GET ile aynıdır, ancak sunucu yanıtta bir ileti gövdesi döndürmemelidir. Yeni ön kapı profilleri için, varsayılan olarak araştırma yöntemi baş olarak ayarlanır.

> [!NOTE]
> Ön kapı, arka uçlarınızın daha düşük yük ve maliyet için sistem durumu araştırmalarının baş isteklerini kullanmanızı önerir.

## <a name="health-probe-responses"></a>Durum araştırma yanıtları

| Yanıtlar  | Açıklama | 
| ------------- | ------------- |
| Sistem durumunu belirleme  |  200 OK durum kodu arka ucun sağlıklı olduğunu gösterir. Diğer her şey hata olarak kabul edilir. Herhangi bir nedenle (ağ arızası dahil) bir yoklama için geçerli bir HTTP yanıtı alınmadıysa, araştırma bir hata olarak sayılır.|
| Ölçüm gecikmesi  | Gecikme süresi, yanıtın son baytını elde ettiğimiz zaman araştırma isteğini gönderdiğimiz zamandan hemen önce ölçülen duvar saati zamanı. Her istek için yeni bir TCP bağlantısı kullanıyoruz ve bu ölçüm, mevcut bir sıcak bağlantıyla birlikte arka uçlara doğru değil.  |

## <a name="how-front-door-determines-backend-health"></a>Ön kapı, arka uç durumunu belirler

Azure ön kapısı, sistem durumunu öğrenmek için tüm algoritmalarda aşağıdaki üç adımlı işlemi kullanır.

1. Devre dışı bırakılan arka uçları hariç tut.

2. Sistem durumu araştırmaları hataları olan arka uçları hariç tut:
    * Bu seçim, son _n_ durum araştırma yanıtlarına bakarak yapılır. En az _x_ sağlıklı ise, arka uç sağlıklı olarak değerlendirilir.

    * _n_ , Yük Dengeleme ayarlarındaki SampleSize özelliği değiştirilerek yapılandırılır.

    * _x_ , Yük Dengeleme ayarlarında başarılı bir şekilde, gerekli özelliği değiştirilerek yapılandırılır.

3. Arka uç havuzundaki sağlıklı arka uçların kümeleri için, ön kapı ek olarak her arka uç için gecikme süresini (gidiş dönüş süresi) ölçer ve korur.


## <a name="complete-health-probe-failure"></a>Tüm sistem durumu araştırma hatası

Bir arka uç havuzundaki her arka uç için sistem durumu araştırmaları başarısız olursa, ön kapı tüm arka uçların sağlıklı olduğunu varsayar ve trafiği tüm bunlar arasında hepsini bir kez deneme dağıtımında yönlendirir.

Arka uç, sağlıklı duruma döndüğünde, ön kapı normal yük dengeleme algoritmasını sürdürür.

## <a name="disabling-health-probes"></a>Durum araştırmalarını devre dışı bırakma

Arka uç havuzunuzdaki tek bir arka uç varsa, uygulamanızın arka ucunuzdaki yükünü azaltan sistem durumu araştırmalarını devre dışı bırakmayı seçebilirsiniz. Arka uç havuzunda birden fazla arka uç olsa bile, ancak bunlardan yalnızca biri etkin durumdaysa, sistem durumu araştırmalarını devre dışı bırakabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
