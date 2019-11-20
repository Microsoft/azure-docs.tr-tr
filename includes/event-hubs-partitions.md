---
title: include dosyası
description: include dosyası
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68481594"
---
Event Hubs her bir tüketicinin ileti akışında yalnızca belirli bir alt küme ya da bölümü okuduğu bölünmüş bir tüketici modeli aracılığıyla ileti akışı sağlar. Bu model, olay işleme için yatay ölçek sağlar ve kuyruklar ile konularda kullanılamayan diğer akış odaklı özellikleri sunar.

Bölüm bir olay hub'ında tutulan olayların sıralı dizisidir. Yeni olaylar geldikçe dizinin sonuna eklenir. Bölüm bir "yürütme günlüğü" olarak düşünülebilir.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Olay hub'ları, tüm bölümler, olay hub'ı uygulayan bir yapılandırılmış elde tutma süresi verilerini korur. Olayların süresi saat bazında dolar; bunları açıkça silemezsiniz. Bölümler birbirinden bağımsız olup kendi veri dizisini içerdiğinden genellikle farklı hızlarda büyürler.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Bölüm sayısı, oluşturma sırasında belirtilir ve 2 ile 32 arasında olmalıdır. Bölüm sayısı değiştirilemez; bu nedenle, bölüm sayısını ayarlarken uzun vadeli ölçeği dikkate almanız gerekir. Bölümler, tüketen uygulamalarda gerekli aşağı akış paralelliğiyle ilişkili bir veri düzenleme mekanizmasıdır. Bir olay hub'ındaki bölüm sayısı, sahip olmayı beklediğiniz eşzamanlı okuyucu sayısıyla doğrudan ilgilidir. Event Hubs ekibine başvurarak bölüm sayısını 32’nin üzerine çıkarabilirsiniz.

Oluşturma sırasında 32 olan mümkün olan en yüksek değer olarak ayarlamak isteyebilirsiniz. Göndericilerin, geri kalan 31 bölümden oluşan 32 ' dan yalnızca tek bir bölüme gönderilmesi için yapılandırmadığınız müddetçe, birden fazla bölüme sahip olmanın sırayı korumadan birden çok bölüme gönderilmesine neden olacağını unutmayın. Önceki durumda, tüm 32 bölümlerdeki olayları okumanız gerekir. İkinci durumda, olay Işlemcisi ana bilgisayarında yapmanız gerekek yapılandırmadan farklı bir ek maliyet yoktur.

Bölümler tanımlanabilir ve doğrudan gönderilebilir olsa da doğrudan bir bölüme göndermek önerilmez. Bunun yerine, [olay yayımcıları](../articles/event-hubs/event-hubs-features.md#event-publishers) bölümünde tanıtılan daha yüksek düzey yapıları kullanabilirsiniz. 

Bölümler, olayı, kullanıcı tanımlı bir özellik paketini ve bölümdeki uzaklığı ve akış dizisindeki sayısı gibi meta verileri gövdesi içerir olay verileri dizisi ile doldurulur.

En iyi ölçeğe ulaşmak için 1:1 üretilen iş birimi ve bölüm dengelemenize önerilir. Tek bir bölümde, bir üretilen iş birimi için garantili giriş ve çıkış vardır. Bir bölüm üzerinde daha yüksek aktarım hızı elde edebiliyor olsa da, performans garanti edilmez. Bu nedenle, bir olay hub 'ındaki bölüm sayısının üretilen iş birimi sayısına eşit veya daha büyük olması önemle önerilir.

İhtiyaç duyduğunuz toplam üretilen iş miktarı verildiğinde, ihtiyacınız olan üretilen iş birimi sayısını ve minimum bölüm sayısını, kaç bölüm olması gerektiğini öğrenirsiniz. Elde etmek istediğiniz aşağı akış paralelliğini ve gelecekteki verimlilik ihtiyaçlarınızı temel alarak bölüm sayısı ' nı seçin. Bir olay hub 'ında bulunan bölüm sayısı için ücret alınmaz.

Bölümleri ve kullanılabilirlikleri ile güvenilirlikleri arasındaki dengeleme hakkında daha fazla bilgi için, bkz: [Event Hubs programlama kılavuzu](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) ve [Event Hubs’ta kullanılabilirlik ve tutarlılık](../articles/event-hubs/event-hubs-availability-and-consistency.md) makalesi.
