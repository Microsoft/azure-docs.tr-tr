---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712493"
---
Bir açık kaynak veri toplayıcı birleşik günlük kaydı için Fluentd olur. `Fluentd` Ayarlarını yönetmek için kapsayıcının bağlantı bir [Fluentd](https://www.fluentd.org) sunucusu. Kapsayıcı günlüklerini yazma izni veren bir Fluentd oturum açma sağlayıcısı kapsayıcı içerir ve isteğe bağlı olarak ölçüm verileri Fluentd sunucusuna.

Aşağıdaki tabloda altında desteklenen yapılandırma ayarları açıklanmaktadır `Fluentd` bölümü.

| Ad | Veri türü | Açıklama |
|------|-----------|-------------|
| `Host` | Dize | Fluentd sunucusunun DNS ana bilgisayar adını veya IP adresi. |
| `Port` | Tamsayı | Fluentd sunucusunun bağlantı noktası.<br/> 24224 varsayılan değerdir. |
| `HeartbeatMs` | Tamsayı | Milisaniye cinsinden sinyal aralığı. Bu aralığı süresi dolmadan önce olay trafik gönderildi, bir sinyal Fluentd sunucuya gönderilir. 60000 milisaniye (1 dakika) varsayılan değerdir. |
| `SendBufferSize` | Tamsayı | Gönderme işlemleri için ayrılan bayt cinsinden ağ arabellek alanı. 32768 bayt (32 kilobayt) varsayılan değerdir. |
| `TlsConnectionEstablishmentTimeoutMs` | Tamsayı | Zaman aşımı, Fluentd sunucusuyla bir SSL/TLS bağlantı kurmak için milisaniye cinsinden. 10000 milisaniye (10 saniye) varsayılan değerdir.<br/> Varsa `UseTLS` yanlış olarak bu değeri yok sayıldı ayarlanmış. |
| `UseTLS` | Boole | Kapsayıcı Fluentd sunucusu ile iletişim kurmak için SSL/TLS kullanıp kullanmayacağını belirtir. Varsayılan değer false'tur. |