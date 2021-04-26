---
title: Azure ön kapılı HTTP2 desteği | Microsoft Docs
description: Bu makale Azure ön kapılarında HTTP/2 desteği hakkında bilgi edinmenize yardımcı olur
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
ms.openlocfilehash: 20d45f5966aca3df89e17e03aa6120a4ddf5c5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91448707"
---
# <a name="http2-support-in-azure-front-door"></a>Azure ön kapıda HTTP/2 desteği

Şu anda HTTP/2 desteği tüm Azure ön kapı yapılandırmalarında etkindir. Müşterilerden başka bir eylem yapılması gerekmez.

HTTP/2, yanıt süresini azaltarak daha hızlı Web performansı sağlayan HTTP/1.1 için önemli bir düzeltme. HTTP/2, Kullanıcı deneyimini geliştirmek için tanıdık HTTP yöntemlerinin, durum kodlarının ve semantiklerinden korunarak yapılır. Http/2, HTTP ve HTTPS ile çalışacak şekilde tasarlandığından, çoğu istemci Web tarayıcısı yalnızca Aktarım Katmanı Güvenliği (TLS) üzerinden HTTP/2 ' yi destekler.

> [!NOTE]
> HTTP/2 protokol desteği yalnızca istemcilerden ön kapıya yapılan istekler için kullanılabilir. Arka uç havuzunda ön kapıdan geri uca iletişim HTTP/1.1 üzerinden yapılır. 

### <a name="http2-benefits"></a>HTTP/2 avantajları

HTTP/2 avantajları şunları içerir:

*   **Çoğullama ve eşzamanlılık**

    HTTP 1,1 kullanarak birden çok kaynak isteğinin birden çok TCP bağlantısı olması ve her bağlantının onunla ilişkili performans yükü vardır. HTTP/2, tek bir TCP bağlantısında birden fazla kaynağın istenme izin verir.

*   **Üstbilgi sıkıştırması**

    Sunulan kaynakların HTTP üst bilgilerini sıkıştırarak, iletişimdeki zaman önemli ölçüde azaltılır.

*   **Akış bağımlılıkları**

    Akış bağımlılıkları, istemcinin önceliğe sahip olduğu sunucuya işaret ediyor olmasını sağlar.


## <a name="http2-browser-support"></a>HTTP/2 tarayıcı desteği

Tüm ana tarayıcılar geçerli sürümlerinde HTTP/2 desteği uyguladık. Desteklenmeyen tarayıcılar otomatik olarak HTTP/1.1 'e geri döner.

|Tarayıcı|En Düşük Sürüm|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Sonraki Adımlar

HTTP/2 hakkında daha fazla bilgi edinmek için aşağıdaki kaynakları ziyaret edin:

- [HTTP/2 belirtim giriş sayfası](https://http2.github.io/)
- [Resmi HTTP/2 SSS](https://http2.github.io/faq/)
- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
