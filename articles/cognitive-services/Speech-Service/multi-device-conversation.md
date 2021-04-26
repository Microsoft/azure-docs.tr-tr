---
title: Çok cihazlı konuşma (Önizleme)-konuşma hizmeti
titleSuffix: Azure Cognitive Services
description: Çoklu cihaz konuşması, birden fazla istemci arasında konuşma veya metin konuşması oluşturmayı ve aralarında gönderilen iletileri koordine etmelerini kolaylaştırır.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7b38bc390b28788003c2de757f45e8ecdfd5c89a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932423"
---
# <a name="what-is-multi-device-conversation-preview"></a>Çok cihazlı konuşma (Önizleme) nedir?

**Çoklu cihaz konuşması** , birden fazla istemci arasında konuşma veya metin konuşması oluşturmayı ve aralarında gönderilen iletileri koordine etmelerini kolaylaştırır.

**Çok cihazlı konuşmadan** şunları yapabilirsiniz:

- Birden fazla istemciyi aynı konuşmaya bağlayın ve bunların aralarında ileti gönderme ve alma yönetimini yönetin.
- Her bir istemciden kolayca ses aktarabilir ve isteğe bağlı çeviri ile dökümü diğer kullanıcılara gönderin.
- İsteğe bağlı çeviri ile istemciler arasında kolayca metin iletileri gönderin.

Bir cihaz dizisi genelinde çalışacak bir özellik veya çözüm oluşturabilirsiniz. Her cihaz, iletileri (ses veya anlık iletilerin dökümünü) diğer tüm cihazlara bağımsız olarak gönderebilir.

[**Konuşma dökümü**](conversation-transcription.md) , çok kanallı bir mikrofon dizisine sahip tek bir cihazda çalışıyorsa, çok **cihazlı görüşme** , her biri tek bir mikrofonla birden çok cihazla senaryolar için uygundur.

>[!IMPORTANT]
> Çok cihazlı konuşma, istemciler arasında ses dosyalarının **gönderilmesini desteklemez:** yalnızca döküm ve/veya çeviri.

## <a name="key-features"></a>Önemli özellikler

- **Gerçek** zamanlı döküm: herkes, konuşmanın bir dökümünü alacak ve böylece metni gerçek zamanlı olarak takip edebilir veya daha sonra için kaydedebilir.
- **Gerçek zamanlı çeviri** – metin çevirisi için 70 ' den fazla [dil](language-support.md#text-languages) ile kullanıcılar, görüşmeyi tercih edilen dillere çevirebilir.
- **Okunabilir** döküm-noktalama ve çeviri, noktalama ve tümce sonları ile kolayca takip edilir.
- **Ses veya metin girişi** – her bir Kullanıcı, katılımcının seçili dili için etkinleştirilen dil desteği özelliklerine bağlı olarak kendi cihazını konuşarak veya yazabilir. Lütfen [dil desteğine](language-support.md#speech-to-text)başvurun.
- **İleti geçişi** -çok cihazlı görüşme hizmeti, bir istemci tarafından gönderilen iletileri kendi seçtikleri dillerde, diğer tüm kullanıcılara dağıtır.
- **İleti tanımlama** : kullanıcıların konuşmada aldığı her ileti, kendisini gönderen kullanıcının takma adıyla etiketlenecek.

## <a name="use-cases"></a>Uygulama alanları

### <a name="lightweight-conversations"></a>Hafif konuşmalar

Bir konuşmayı oluşturmak ve katılmak kolaydır. Bir Kullanıcı ' ana bilgisayar ' olarak davranır ve rastgele beş harfli bir konuşma kodu ve QR kodu üreten bir konuşma oluşturur. Tüm diğer kullanıcılar konuşma kodunu yazarak veya QR kodunu tarayarak konuşmaya katılabilir. 

Kullanıcılar konuşma kodu aracılığıyla birleşdiğinden ve iletişim bilgilerini paylaşmak için gerekli olmadığından, hızlı, noktalı konuşmalar oluşturmak kolaydır.

### <a name="inclusive-meetings"></a>Kapsamlı toplantılar

Gerçek zamanlı döküm ve çeviri, konuşmaları farklı diller konuştan ve/veya işitme güçlüğü çeken kişiler için erişilebilir hale getirmenize yardımcı olabilir. Her kişi, tercih edilen dillerini konuşarak veya anlık iletiler göndererek konuşmaya etkin bir şekilde katılabilir.

### <a name="presentations"></a>Sunular

Ayrıca, hem ekranda hem de hedef kitle üyelerinin kendi cihazlarındaki sunular ve seminerler için açıklamalı alt yazılar sağlayabilirsiniz. Hedef kitle konuşma koduyla birleştikten sonra, kendi cihazındaki bu dökümü kendi tercih ettiği dilde görebilirler.

## <a name="how-it-works"></a>Nasıl çalışır?

Tüm istemciler konuşma oluşturmak veya konuşmayı birleştirmek için konuşma SDK 'sını kullanır. Konuşma SDK 'Sı, katılımcıların listesi, her istemcinin seçtiği dil (ler) ve gönderilen iletiler dahil olmak üzere bir konuşmanın ömrünü yöneten çok cihazlı konuşma hizmetiyle etkileşime girer.  

Her istemci, ses veya anlık ileti gönderebilir. Hizmet, sesi metne dönüştürmek ve olduğu gibi anlık iletiler göndermek için konuşma tanımayı kullanır. İstemciler farklı diller seçince, hizmet tüm iletileri her bir istemcinin belirtilen diline çevirir.

![Çok cihazlı konuşmaya genel bakış Diyagramı](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Konuşmaya, konağa ve katılımcıya genel bakış

**Konuşma** , bir kullanıcının diğer katılan kullanıcıların katılması için başlattığı bir oturumdur. Tüm istemciler, beş harfli **konuşma kodunu** kullanarak konuşmaya bağlanır.

Her konuşma aşağıdakileri içeren meta veriler oluşturur:
-    Görüşmenin başladığı ve sona erdiği zaman damgaları
-    Her kullanıcının seçtiği takma ad ve konuşma veya metin girişi için birincil dil içeren, konuşmadaki tüm katılımcıların listesi.


Bir konuşmada iki tür Kullanıcı vardır:  **ana bilgisayar** ve **katılımcı**.

**Ana bilgisayar** bir konuşmayı başlatan ve bu görüşmenin Yöneticisi olarak davranan Kullanıcı olur.
- Her konuşmada yalnızca bir konak olabilir
- Konuşma süresince ana bilgisayarın konuşmaya bağlı olması gerekir. Ana bilgisayar konuşmayı bırakırsa, konuşma diğer tüm katılımcılar için sona acaktır.
- Ana bilgisayarda konuşmayı yönetmek için birkaç ek denetim bulunur: 
    - Konuşmayı kilitle-ek katılımcıların katılmasını önleyin
    - Tüm katılımcıların sesini kapat-diğer katılımcıların konuşmaya veya anlık mesajlardan ayrılıp konuşmadan ileti göndermesini önleyin
    - Bireysel katılımcıları susturma
    - Tüm katılımcıları aç
    - Bireysel katılımcıları aç

**Katılımcı** bir konuşmaya katılan bir kullanıcı.
- Katılımcı, diğer katılımcılar için konuşmayı sonlandırmadan, her zaman aynı konuşmayı bırakabilir ve yeniden katılabilir.
- Katılımcılar konuşmayı kilitleyemez veya diğerlerinin kilidini açma/kapatma

> [!NOTE]
> Her konuşmada en fazla 100 katılımcı olabilir ve bu, belirli bir zamanda 10 ' un aynı anda konuşmasına olanak sağlayabilir.

## <a name="language-support"></a>Dil desteği

Bir konuşma oluştururken veya katılırken, her kullanıcının bir **birincil dil** seçmesi gerekir: ' de anlık iletileri konuşmaları ve gönderebilecekleri dil ve ayrıca diğer kullanıcıların iletilerini göreceği dil.

İki tür dil vardır: **konuşmadan metne** ve **salt metin**:
- Kullanıcı, birincil dili olarak bir **konuşmaya metin** dili seçerse, konuşmada hem konuşma hem de metin girişi kullanılabilir.

- Kullanıcı **yalnızca metin** dilini seçerse, yalnızca metin girişi kullanabilir ve konuşmada anında ileti gönderebilecektir. Yalnızca metin dilleri, metin çevirisi için desteklenen dillerdir, ancak metinden konuşmaya değildir. [Dil desteği](./language-support.md) sayfasında kullanılabilir dilleri görebilirsiniz.

Birincil dilinden ayrı olarak her katılımcı, görüşmeyi çevirmek için ek diller de belirtebilir.

Aşağıda, kullanıcının seçtiğiniz birincil dile bağlı olarak çok cihazlı bir konuşmada yapabilecekleri bir Özet verilmiştir.


| Kullanıcının konuşmada neler yapabilecekleri | Konuşmayı metne dönüştürme | Yalnızca metin |
|-----------------------------------|----------------|------|
| Konuşma girişini kullanma | ✔️ | ❌ |
| Anlık iletiler gönder | ✔️ | ✔️ |
| Konuşmayı çevir | ✔️ | ✔️ |

> [!NOTE]
> Konuşmayı metne dönüştürme ve metin çevirisi dillerinin listesi için bkz. [desteklenen diller](./language-support.md).



## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Konuşmaları gerçek zamanlı olarak çevir](quickstarts/multi-device-conversation.md)