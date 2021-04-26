---
title: Azure CDN 'de HTTP/2 desteği | Microsoft Docs
description: Azure Content Delivery Network, http/1 ' den faydalanan, eş zamanlı & eşzamanlılık, üstbilgi sıkıştırması ve akış bağımlılıklarını içeren HTTP/2 ' yi destekler.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: d4ed04185da251d9042f6b54a192d0e49ef20ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778533"
---
# <a name="http2-support-in-azure-cdn"></a>Azure CDN 'de HTTP/2 desteği

HTTP/2, HTTP/1.1 \ için önemli bir düzeltme. Daha hızlı Web performansı, daha az yanıt süresi ve geliştirilmiş Kullanıcı deneyimi sağlar, ancak tanıdık HTTP yöntemlerini, durum kodlarını ve semantiğini koruyun. Http/2 HTTP ve HTTPS ile çalışmak üzere tasarlanmış olsa da, birçok istemci Web tarayıcısı yalnızca TLS üzerinden HTTP/2 ' yi destekler.

### <a name="http2-benefits"></a>HTTP/2 avantajları

HTTP/2 avantajları şunları içerir:

*   **Çoğullama ve eşzamanlılık**

    HTTP 1,1 kullanarak birden çok kaynak isteğinin birden çok TCP bağlantısı olması ve her bağlantının onunla ilişkili performans yükü vardır. HTTP/2, tek bir TCP bağlantısında birden fazla kaynağın istenme izin verir.

*   **Üstbilgi sıkıştırması**

    Sunulan kaynakların HTTP üst bilgilerini sıkıştırarak, iletişimdeki zaman önemli ölçüde azaltılır.

*   **Akış bağımlılıkları**

    Akış bağımlılıkları, istemcinin önceliğe sahip olduğu sunucuya işaret ediyor olmasını sağlar.


## <a name="http2-browser-support"></a>HTTP/2 tarayıcı desteği

Tüm ana tarayıcılar geçerli sürümlerinde HTTP/2 desteği uyguladık. Desteklenmeyen tarayıcılar, otomatik olarak HTTP/1.1 'e geri dönüş.

|Tarayıcı|En Düşük Sürüm|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Azure CDN 'de HTTP/2 desteğini etkinleştirme

Şu anda HTTP/2 desteği tüm Azure CDN profilleri için etkindir. Müşterilerden başka bir eylem yapılması gerekmez.

## <a name="next-steps"></a>Sonraki Adımlar

HTTP/2 ' nin bu avantajları hakkında daha fazla bilgi için, [Akamai adresinden bu tanıtımı](https://http2.akamai.com/demo)inceleyin.

HTTP/2 hakkında daha fazla bilgi edinmek için aşağıdaki kaynakları ziyaret edin:

*   [HTTP/2 belirtim giriş sayfası](https://http2.github.io/)
*   [Resmi HTTP/2 SSS](https://http2.github.io/faq/)
*   [Akamai HTTP/2 bilgileri](https://http2.akamai.com/)

Azure CDN 'nin kullanılabilir özellikleri hakkında daha fazla bilgi edinmek için bkz. [Azure CDN genel bakış](./cdn-overview.md).