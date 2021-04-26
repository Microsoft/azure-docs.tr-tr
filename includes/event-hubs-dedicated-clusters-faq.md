---
title: include dosyası
description: include dosyası
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92517012"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>Bir küme ile neler elde edebilirim?

Event Hubs kümesi için, alma ve akış alma işlemleri, üreticileri, Tüketicileriniz, geri alma ve işleme alma ücreti ve çok daha fazlası gibi çeşitli faktörlere bağlıdır. 

Aşağıdaki tabloda, sınamamız sırasında elde ettiğimiz kıyaslama sonuçları gösterilmektedir:

| Yük şekli | Alıcıların | Giriş bant genişliği| Giriş iletileri | Çıkış bant genişliği | Çıkış iletileri | Toplam DTU | CU başına DTU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB toplu işleri | 2 | 400 MB/sn | 400k İleti/sn | 800 MB/sn | 800k İleti/sn | 400 tüs | 100 tüs | 
| 10x10KB toplu işlem | 2 | 666 MB/sn | 66.6 k iletisi/sn | 1,33 GB/sn | 133k İleti/sn | 666 tüs | 166 tüs |
| 6x32KB toplu işleri | 1 | 1,05 GB/sn | 34k İleti/sn | 1,05 GB/sn | 34k İleti/sn | 1000 tüs | 250 tüs |

Sınamada, aşağıdaki ölçütler kullanılmıştır:

- Dört Kapasite birimi (cu düzeyinde kapsanır) içeren bir adanmış katman Event Hubs kümesi kullanıldı. 
- Alma işlemi için kullanılan olay hub 'ının 200 bölümü vardı. 
- Alınan veriler, tüm bölümlerden alınan iki alıcı uygulaması tarafından alındı.

### <a name="can-i-scale-updown-my-cluster"></a>Kümemin ölçeğini artırma/azaltma yapabilir miyim?

Oluşturulduktan sonra, kümeler en az 4 saatlik kullanım için faturalandırılır. Self Servis deneyiminin önizleme sürümünde, adanmış kümeyi ölçeğini genişletmek veya ölçeklendirmek için Event Hubs ekibine teknik kota isteği kapsamında bir [destek isteği](https://ms.portal.azure.com/#create/Microsoft.Support) gönderebilirsiniz  >    >   . Kümenizi ölçeklendirmek için isteğin tamamlanması 7 güne kadar sürebilir. 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>Coğrafi DR, kümeme nasıl çalışır?

Ayrılmış katman kümesi altında başka bir ad alanı ile ayrılmış katman kümesi altında bir ad alanını coğrafi olarak eşleştirin. Standart Teklifimizde bir ad alanı ile ayrılmış bir ad alanı eşleştirmeyi teşvik ettik, çünkü aktarım hızı sınırı uyumsuz ve hatalara neden olur. 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Standart ad alanlarımı adanmış katman kümesine ait olacak şekilde geçirebilir miyim?
Şu anda, Olay Hub 'larınızın verilerini standart bir ad alanından adanmış bir şekilde geçirmek için otomatik bir geçiş işlemini desteklemiyoruz. 
