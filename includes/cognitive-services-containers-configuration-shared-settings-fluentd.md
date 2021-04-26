---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001224"
---
Floentd, Birleşik günlük kaydına yönelik açık kaynaklı bir veri toplayıcıdır. `Fluentd`Ayarlar, kapsayıcının bir [Floentd](https://www.fluentd.org) sunucusuyla bağlantısını yönetir. Kapsayıcı, kapsayıcının günlükleri ve isteğe bağlı olarak ölçüm verilerini Floentd sunucusuna yazmasını sağlayan bir Floentd günlüğe kaydetme sağlayıcısı içerir.

Aşağıdaki tabloda, bölümünde desteklenen yapılandırma ayarları açıklanmaktadır `Fluentd` .

| Name | Veri türü | Açıklama |
|------|-----------|-------------|
| `Host` | Dize | Floentd sunucusunun IP adresi veya DNS ana bilgisayar adı. |
| `Port` | Tamsayı | Floentd sunucusunun bağlantı noktası.<br/> Varsayılan değer 24224 ' dir. |
| `HeartbeatMs` | Tamsayı | Sinyal aralığı (milisaniye cinsinden). Bu Aralık sona ermeden önce hiçbir olay trafiği gönderilmezse, Floentd sunucusuna bir sinyal gönderilir. Varsayılan değer 60000 milisaniyedir (1 dakika). |
| `SendBufferSize` | Tamsayı | Gönderme işlemleri için ayrılan, bayt cinsinden ağ arabelleği alanı. Varsayılan değer 32768 bayttır (32 kilobayt). |
| `TlsConnectionEstablishmentTimeoutMs` | Tamsayı | Akıcı Entd sunucusuyla SSL/TLS bağlantısı kurmak için milisaniye cinsinden zaman aşımı. Varsayılan değer 10000 milisaniyedir (10 saniye).<br/> `UseTLS`False olarak ayarlanırsa, bu değer yoksayılır. |
| `UseTLS` | Boole | Kapsayıcının, Floentd sunucusuyla iletişim kurmak için SSL/TLS kullanması gerekip gerekmediğini belirtir. Varsayılan değer false'tur. |