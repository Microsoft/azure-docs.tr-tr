---
title: Azure ön kapı hizmeti-arka uç sistem durumu izleme | Microsoft Docs
description: Bu makale, Azure ön kapı hizmeti 'nin arka uçlarınızın sistem durumunu nasıl izlediğini anlamanıza yardımcı olur
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 289b05a2c50a2b4af50eb2114515a49bb653cf1a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742401"
---
# <a name="health-probes"></a>Durum araştırmaları

Her bir arka ucun sistem durumunu belirleyebilmek için, her bir ön kapı ortamı, her bir otomatik olarak yapılandırılmış arka uçlarınızın her birine yapay bir HTTP/HTTPS isteği gönderir. Front Door bu araştırmalardan gelen yanıtları kullanarak gerçek istemci isteklerini yönlendireceği "en iyi" arka uçları belirler. Ön kapıda genel olarak çok uç ortamları olduğundan, arka uçlarınıza yönelik durum araştırma istekleri birimi, saniye başına birden fazla istek yapılandırılan durum araştırma sıklığına bağlı olarak yüksek olabilir. 



## <a name="supported-protocols"></a>Desteklenen protokoller

Ön kapı, HTTP veya HTTPS protokolleri üzerinden araştırmaları göndermeyi destekler. Bu araştırmalar istemci isteklerini yönlendirmek için yapılandırılmış olan TCP bağlantı noktaları üzerinden gönderilir ve bu ayar geçersiz kılınamaz.

## <a name="health-probe-responses"></a>Durum araştırma yanıtları

| Responses  | Açıklama | 
| ------------- | ------------- |
| Sistem durumunu belirleme  |  200 OK durum kodu arka ucun sağlıklı olduğunu gösterir. Diğer her şey hata olarak kabul edilir. Herhangi bir nedenle (ağ arızası dahil) bir araştırma için geçerli bir HTTP yanıtı alınmıyorsa, araştırma bir hata olarak sayılır.|
| Ölçüm gecikmesi  | Gecikme süresi, yanıtın son baytını elde ettiğimiz zaman araştırma isteğini gönderdiğimiz zamandan hemen önce ölçülen duvar saati zamanı. Her istek için yeni bir TCP bağlantısı kullanıyoruz ve bu ölçüm, mevcut bir sıcak bağlantıyla arka uçlara doğru değil.  |

## <a name="how-front-door-determines-backend-health"></a>Ön kapı, arka uç durumunu belirler

Azure ön kapı hizmeti, sistem durumunu öğrenmek için tüm algoritmalarda aşağıdaki üç adımlı işlemi kullanır.

1. Devre dışı bırakılan arka uçları hariç tut.

2. Sistem durumu araştırmaları hataları olan arka uçları hariç tut:
    * Bu seçim, son _n_ durum araştırma yanıtlarına bakarak yapılır. En az _x_ sağlıklı ise, arka uç sağlıklı olarak değerlendirilir.

    * _n_ , Yük Dengeleme ayarlarındaki SampleSize özelliği değiştirilerek yapılandırılır.

    * _x_ , Yük Dengeleme ayarları 'ndaki başarıyla, gerekli özelliği değiştirilerek yapılandırılır.

3. Arka uç havuzundaki sağlıklı arka uçların kümesinden, ön kapı ek olarak her arka uç için gecikme süresini (gidiş dönüş süresi) ölçer ve korur.


## <a name="complete-health-probe-failure"></a>Tüm sistem durumu araştırma hatası

Bir arka uç havuzundaki her arka uç için sistem durumu araştırmaları başarısız olursa, ön kapı tüm arka uçların sağlıklı olduğunu varsayar ve trafiği tüm bunlar arasında hepsini bir kez deneme dağıtımında yönlendirir.

Arka uç, sağlıklı duruma döndüğünde, ön kapı normal yük dengeleme algoritmasını sürdürür.

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
