---
title: Azure Notification Hubs şablonları
description: Azure Notification Hubs için şablonları kullanma hakkında bilgi edinin.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635651"
---
# <a name="notification-hubs-templates"></a>Notification Hubs şablonları

Şablonlar, bir istemci uygulamanın almak istediği bildirimlerin tam biçimini belirtmesini sağlar. Bir uygulama, şablonları kullanarak aşağıdakiler de dahil olmak üzere birkaç farklı avantajı fark edebilir:

- Platformdan bağımsız arka uç
- Kişiselleştirilmiş bildirimler
- İstemci sürümü bağımsızlık
- Kolay yerelleştirme

Bu bölümde, tüm cihazlarınızı platformlar arasında hedefleme ve her bir cihaza yayın bildirimlerini kişiselleştirmek için şablonların nasıl kullanılacağına ilişkin iki derinlemesine örnek verilmiştir.

## <a name="using-templates-cross-platform"></a>Şablonları platformlar arası kullanma

Anında iletme bildirimleri göndermenin standart yolu, gönderilecek her bildirim için, platform bildirim hizmetleri 'ne (WNS, APNS) belirli bir yük göndermek içindir. Örneğin, APNS 'ye bir uyarı göndermek için, yük aşağıdaki biçimdeki bir JSON nesnesidir:

```json
{"aps": {"alert" : "Hello!" }}
```

Bir Windows Mağazası uygulamasında benzer bir bildirim iletisi göndermek için, XML yükü aşağıdaki gibidir:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

MPNS (Windows Phone) ve FCM (Android) platformları için benzer yükleri oluşturabilirsiniz.

Bu gereksinim, uygulama arka ucunu her platform için farklı yük oluşturacak şekilde zorlar ve uygulamanın sunum katmanının bir parçası olan arka ucu etkili bir şekilde gerçekleştirir. Bazı kaygılar, yerelleştirme ve grafik düzenlerini (özellikle de çeşitli döşeme türleri için bildirimleri içeren Windows Mağazası uygulamaları için) içerir.

Notification Hubs şablonu özelliği, bir istemci uygulamanın, Etiketler kümesine ek olarak, şablon kayıtları olarak adlandırılan özel kayıtlar oluşturmalarına olanak sağlar. Notification Hubs şablonu özelliği, Yüklemeler (tercih edilen) veya kayıtlarla çalışırken, bir istemci uygulamanın cihazları şablonlar ile ilişkilendirmesine olanak sağlar. Önceki yük örnekleri söz konusu olduğunda, tek platformla bağımsız bilgiler gerçek uyarı iletisidir (**Merhaba!**). Şablon, ilgili istemci uygulamasının kaydı için platforma bağımsız bir iletiyi biçimlendirme hakkında bildirim hub 'ı için bir yönergeler kümesidir. Yukarıdaki örnekte, platformdan bağımsız ileti tek bir özelliktir: `message = Hello!` .

Aşağıdaki şekilde işlem gösterilmektedir:

![Şablonları platformlar arası kullanma sürecini gösteren diyagram](./media/notification-hubs-templates/notification-hubs-hello.png)

İOS istemci uygulaması kaydına yönelik şablon aşağıdaki gibidir:

```json
{"aps": {"alert": "$(message)"}}
```

Windows Mağazası istemci uygulaması için karşılık gelen şablon:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Gerçek iletinin ifade için yerine konduğuna dikkat edin `$(message)` . Bu ifade, Bildirim Hub 'ına bu belirli kayda bir ileti gönderdiğinde, izleyen bir ileti oluşturmak ve ortak değeri eklemek için yönlendirir.

Yükleme modeliyle çalışıyorsanız, yükleme "Şablonlar" anahtarı birden çok şablon içeren bir JSON içerir. Kayıt modeliyle çalışıyorsanız, istemci uygulaması birden çok şablon kullanmak için birden çok kayıt oluşturabilir; Örneğin, uyarı iletileri için bir şablon ve kutucuk güncelleştirmeleri için bir şablon. İstemci uygulamaları, yerel kayıtları da (şablon içermeyen kayıtlar) ve şablon kayıtlarını karıştırabilir.

Bildirim Hub 'ı, aynı istemci uygulamasına ait olup olmadığını düşünmeksizin her bir şablon için bir bildirim gönderir. Bu davranış, platformdan bağımsız bildirimleri daha fazla bildirimlere dönüştürmek için kullanılabilir. Örneğin, Bildirim Hub 'ına platformdan bağımsız aynı ileti, arka ucun farkında olması gerekmeden bir bildirim uyarısıyla ve kutucuk güncelleştirmesine sorunsuz bir şekilde çevrilebilir. Bazı platformlar (örneğin, iOS), kısa bir süre içinde gönderildiklerinde aynı cihaza birden çok bildirimi daraltabilir.

## <a name="using-templates-for-personalization"></a>Kişiselleştirme için şablonları kullanma

Şablon kullanmanın başka bir avantajı da, bildirimlerin kayıt başına kişiselleştirilmesini gerçekleştirmek için Notification Hubs kullanma olanağıdır. Örneğin, belirli bir konumdaki Hava durumu koşullarına sahip bir kutucuk görüntüleyen bir hava durumu uygulaması düşünün. Bir Kullanıcı santigrat veya Fahrenhayt derece, tek veya beş günlük bir tahmin arasında seçim yapabilir. Şablonlar kullanılarak, her bir istemci uygulaması yüklemesi gereken biçime (1 günlük Santi, 1 günlük Fahrenhay, 5 gün santigrat, 5-gün Fahrenhayya) kaydedebilir ve arka uca bu şablonları doldurmakta gerekli tüm bilgileri içeren tek bir ileti gönderebilir (örneğin, santigrat ve Fahrenhayt derece ile beş günlük bir tahmin).

Santigrat sıcaklıklar ile tek günlük tahmine yönelik şablon aşağıdaki gibidir:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Bildirim Hub 'ına gönderilen ileti aşağıdaki özellikleri içerir:

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

Bu model kullanılarak, arka uç yalnızca uygulama kullanıcıları için belirli kişiselleştirme seçeneklerini depolamak zorunda kalmadan tek bir ileti gönderir. Aşağıdaki şekilde bu senaryo gösterilmektedir:

![Arka ucun her platforma yalnızca tek bir ileti gönderdiğini gösteren diyagram.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Şablonları kaydetme

Yükleme modelini (tercih edilen) veya kayıt modelini kullanarak şablonlara kaydolmak için bkz. [kayıt yönetimi](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Şablon ifadesi dili

Şablonlar XML veya JSON belge biçimleriyle sınırlıdır. Ayrıca, yalnızca belirli yerlere ifade yerleştirebilirsiniz; Örneğin, düğüm öznitelikleri veya XML için değerler, JSON için dize özelliği değerleri.

Aşağıdaki tabloda şablonlarda izin verilen dil gösterilmektedir:

| İfade       | Açıklama |
| ---------------- | --- |
| $ (Prop)          | Verilen ada sahip bir olay özelliğine başvuru. Özellik adları büyük/küçük harfe duyarlı değildir. Bu ifade özelliğin metin değerine veya özelliği yoksa boş bir dizeye çözümleniyor. |
|$ (prop, n)       | Yukarıdaki gibi, ancak metin n karakter olarak açıkça kırpıldı, örneğin $ (başlık, 20), title özelliğinin içeriğini 20 karakter olarak kırpar. |
| . (prop, n)      | Yukarıdaki gibi, ancak metin, kırpıldığı için üç noktayla sondakini sabitlenmiştir. Kırpılan dizenin ve sonekin toplam boyutu n karakteri aşamaz. (başlık, 20) bir giriş özelliği olan "This başlık çizgisi", **Bu durum başlıktır...** |
| % (Prop)          | Çıktının URI kodlamalı olması dışında $ (Name) ile benzerdir. |
| # (Prop)          | JSON şablonlarında kullanılır (örneğin, iOS ve Android şablonları için).<br><br>Bu işlev, JSON şablonlarında kullanılması dışında (örneğin, Apple şablonları), daha önce belirtilen ' $ (Prop) ' ile tamamen aynı şekilde çalışır. Bu durumda, bu işlev "{', '}" (örneğin, ' myJsonProperty ': ' # (Name) ') ile çevreleniyorsa ve JavaScript biçiminde bir sayı olarak değerlendirilir (örneğin, RegExp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, ardından JSON çıktısı bir sayıdır.<br><br>Örneğin, ' # (Name) ', ' rozet ': 40 (' 40 ' değil) olur. |
| ' text ' veya "Text" | Değişmez değer. Değişmez değerler, tek veya çift tırnak içine alınmış rastgele metni içerir. |
| expr1 + expr2    | Birleştirme işleci iki ifadeye tek bir dizeye katılıyor. |

İfadeler, önceki formlardan herhangi biri olabilir.

Birleştirme kullanılırken, tüm ifadenin birlikte kullanılması gerekir `{}` . Örneğin, `{$(prop) + ‘ - ’ + $(prop2)}`.

Örneğin, şu şablon geçerli bir XML şablonu değildir:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Daha önce açıklandığı gibi, birleştirme kullanılırken ifadeler süslü ayraç içine alınmalıdır. Örnek:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Sonraki adımlar

[Azure Notification Hubs hakkında bilgi edinin](notification-hubs-push-notification-overview.md)
