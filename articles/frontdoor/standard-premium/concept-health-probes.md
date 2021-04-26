---
title: Azure ön kapı Standart/Premium (Önizleme) sistem durumu araştırma izlemesi
description: Bu makale, Azure ön kapısının arka ucunuzun durumunu nasıl izlediğini anlamanıza yardımcı olur.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100534"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Azure ön kapı Standart/Premium (Önizleme) sistem durumu araştırma izlemesi

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Azure ön kapısı, arka ucunuzun her birine düzenli aralıklarla bir HTTP veya HTTPS isteği gönderir. Bu istekler, Azure ön kapısının arka uç havuzundaki her bir uç noktanın sistem durumunu belirlemesine izin verir. Ön kapı daha sonra istemci isteklerinizi yönlendirmek üzere "en iyi" arka uç kaynaklarını tespit etmek için bu yanıtları araştırmayı kullanır. 

> [!WARNING]
> Ön kapıda genel olarak çok uç ortamları olduğundan, arka uçlarınız için sistem durumu araştırma birimi, yapılandırılan durum araştırma sıklığı uyarınca dakikada her dakikada en fazla 1200 istek kadar yüksek olabilir. Varsayılan araştırma sıklığı 30 saniye ile, arka ucunuzdaki araştırma hacmi, dakikada yaklaşık 200 istek olmalıdır.

## <a name="supported-protocols"></a>Desteklenen protokoller

Ön kapı, HTTP veya HTTPS protokolleri üzerinden araştırmaları göndermeyi destekler. Bu araştırmalar istemci isteklerini yönlendirmek için yapılandırılmış olan TCP bağlantı noktaları üzerinden gönderilir ve bu ayar geçersiz kılınamaz.

## <a name="supported-http-methods-for-health-probes"></a>Durum araştırmaları için desteklenen HTTP yöntemleri

Ön kapı, sistem durumu araştırmalarını göndermek için aşağıdaki HTTP yöntemlerini destekler:

* **Al:** GET yöntemi, Istek URI 'SI tarafından tanımlanan bilgilerin (bir varlık biçiminde) alınması anlamına gelir.
* **Baş:** HEAD yöntemi GET ile aynıdır, ancak sunucu yanıtta bir ileti gövdesi döndürmemelidir. Yeni ön kapı profilleri için, varsayılan olarak araştırma yöntemi baş olarak ayarlanır.

> [!NOTE]
> Ön kapı, arka uçlarınızın daha düşük yük ve maliyet için sistem durumu araştırmalarının baş isteklerini kullanmanızı önerir.

## <a name="health-probe-responses"></a>Durum araştırma yanıtları

| Yanıtlar  | Description | 
| ------------- | ------------- |
| Sistem durumunu belirleme  |  200 OK durum kodu arka ucun sağlıklı olduğunu gösterir. Diğer her şey hata olarak kabul edilir. Herhangi bir nedenle (ağ arızası dahil) bir yoklama için geçerli bir HTTP yanıtı alınmadıysa, araştırma bir hata olarak sayılır.|
| Ölçüm gecikmesi  | Gecikme süresi, yanıtın son baytını elde ettiğimiz zaman araştırma isteğini gönderdiğimiz zamandan hemen önce ölçülen duvar saati zamanı. Her istek için yeni bir TCP bağlantısı kullanıyoruz ve bu ölçüm, mevcut bir sıcak bağlantıyla birlikte arka uçlara doğru değil.  |

## <a name="how-front-door-determines-backend-health"></a>Ön kapı, arka uç durumunu belirler

Azure ön kapısı, sistem durumunu öğrenmek için tüm algoritmalarda aşağıdaki üç adımlı işlemi kullanır.

1. Devre dışı bırakılan arka uçları hariç tut.

1. Sistem durumu araştırmaları hataları olan arka uçları hariç tut:

    * Bu seçim, son _n_ durum araştırma yanıtlarına bakarak yapılır. En az _x_ sağlıklı ise, arka uç sağlıklı olarak değerlendirilir.

    * _n_ , Yük Dengeleme ayarlarındaki SampleSize özelliği değiştirilerek yapılandırılır.

    * _x_ , Yük Dengeleme ayarlarında başarılı bir şekilde, gerekli özelliği değiştirilerek yapılandırılır.

1. Arka uç havuzundaki sağlıklı arka uçların kümeleri için, ön kapı ek olarak her arka uç için gecikme süresini (gidiş dönüş süresi) ölçer ve korur.


## <a name="complete-health-probe-failure"></a>Tüm sistem durumu araştırma hatası

Bir arka uç havuzundaki her arka uç için sistem durumu araştırmaları başarısız olursa, ön kapı tüm arka uçların sağlıklı olduğunu varsayar ve trafiği tüm bunlar arasında hepsini bir kez deneme dağıtımında yönlendirir.

Arka uç, sağlıklı duruma döndüğünde, ön kapı normal yük dengeleme algoritmasını sürdürür.

## <a name="disabling-health-probes"></a>Durum araştırmalarını devre dışı bırakma

Arka uç havuzunuzdaki tek bir arka uç varsa veya arka uç havuzunda yalnızca bir arka uç etkinse, sistem durumu araştırmalarını devre dışı bırakmayı seçebilirsiniz. Bunu yaptığınızda, uygulamanızın arka ucunuzun yükünü azaltacaksınız.

## <a name="next-steps"></a>Sonraki adımlar

[Ön kapı Standart/Premium oluşturmayı](create-front-door-portal.md)öğrenin.
