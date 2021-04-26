---
title: Arama için fiyatlandırma senaryoları (ses/video) ve sohbet
titleSuffix: An Azure Communication Services concept document
description: Iletişim Hizmetleri fiyatlandırma modeli hakkında bilgi edinin.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5f7b1e6d600f5d3652ce6a66a72cbfbf33b336c4
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106091880"
---
# <a name="pricing-scenarios"></a>Fiyatlandırma senaryoları

Azure Iletişim Hizmetleri için fiyatlara genellikle Kullandıkça Öde modeli temel alınır. Aşağıdaki örneklerde bulunan fiyatlar tanım amaçlıdır ve en son Azure fiyatlandırmasını yansıtmayabilir.

## <a name="voicevideo-calling-and-screen-sharing"></a>Ses/video çağırma ve ekran paylaşımı

Azure Iletişim Hizmetleri, uygulamalarınıza ses/video çağırma ve ekran paylaşımı ekleme için izin verir. JavaScript, amaç-C (Apple), Java (Android) veya .NET SDK 'Ları kullanarak deneyimlerinizi uygulamalarınıza ekleyebilirsiniz. [Kullanılabilir SDK 'ların tam listesini](./sdk-options.md)inceleyin.

### <a name="pricing"></a>Fiyatlandırma

Çağrı ve ekran paylaşım hizmetleri, her katılımcı için dakikada $0,004, grup çağrıları için dakikada üzerinden ücretlendirilir. Olası çeşitli çağrı akışlarını anlamak için [Bu sayfaya](./call-flows.md)bakın.

Her çağrının katılımcısı, çağrıya bağlı oldukları her dakika için faturalandırmaya göre sayılır. Bu, kullanıcının video çağırma, sesli arama veya ekran paylaşımı olmasına bakılmaksızın doğru bir şekilde geçerlidir.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-sdks"></a>Fiyatlandırma örneği: JS ve iOS SDK 'larını kullanarak ses/video çağrısını gruplayın

Çiğdem, iş arkadaşları, Bob ve Charlie ile bir grup çağrısı yaptı. Gamze ve Bob, JS SDK 'Ları, Charlie iOS SDK 'Ları kullandı.

- Çağrı toplam 60 dakika sürer.
- Çiğdem ve Bob tüm çağrı için katılmış. Çiğdem, videosunu beş dakika boyunca açmıştır ve ekranı 23 dakika boyunca paylaşıyordu. Bob, tüm çağrı (60 dakika) için videoyu ve 12 dakika boyunca paylaşılan ekranını içeriyordu.
- Çağrıyı 43 dakika sonra bıraktı. Charlie, katıldık süre boyunca ses ve video kullandı (43 dakika).

**Maliyet hesaplamaları**

- 2 katılımcılar x 60 dakikada katılımcı başına dakikada x $0,004 = $0,48 [hem video hem de ses aynı hızda ücretlendirilir]
- 1 katılımcı x 43 dakika başına katılımcı başına x $0,004 dakikası = $0,172 [hem video hem de ses aynı hızda ücretlendirilir]

**Grup çağrısı Için toplam maliyet**: $0,48 + $0,172 = $0,652


### <a name="pricing-example-outbound-call-from-app-using-js-sdk-to-a-pstn-number"></a>Fiyatlandırma örneği: JS SDK 'sını bir PSTN numarasına kullanan uygulamadan giden çağrı

Çiğdem, bir uygulamadan, ile başlayan ABD telefon numarası üzerinden emre 'ye bir PSTN çağrısı yapar `+1-425` .

- Çiğdem uygulamayı derlemek için JS SDK 'sını kullandı.
- Çağrı toplam 5 dakika sürer.

**Maliyet hesaplamaları**

- VoIP bacağı (Gamze), uygulamadan Iletişim Hizmetleri sunucularına, $0,04 dakikada günde bir katılımcı başına $0,004 x 10 dakika x katılımcı
- PSTN giden bacağı (Charlie), Iletişim Hizmetleri sunucularından bir ABD telefon numarası x 10 dakika x $0,013, $0,13 dakikada bir katılımcı için haftada bir.

Note: ABD $0,013 ' e kadar karışık ücretler `+1-425` . Ayrıntılar için aşağıdaki bağlantıya başvurun: https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**Grup çağrısı Için toplam maliyet**: $0,04 + $0,13 = $0,17


### <a name="pricing-example-group-audio-call-using-js-sdk-and-1-pstn-leg"></a>Fiyatlandırma örneği: JS SDK ve 1 PSTN bayı kullanarak ses çağrısını gruplayın

Çiğdem ve Bob, bir VOıP çağrısında. Bob, Charlie 'in, ile başlayan ABD telefon numarası olan, Charlie 'ın PSTN numarasına yönelik çağrısını ilerletildi `+1-425` .

- Çiğdem uygulamayı derlemek için JS SDK 'sını kullandı. PSTN numarası üzerinde Charlie çağrılmadan önce 10 dakika boyunca yer alır.
- Bob, PSTN numarası üzerinde Charlie çağrısını ilerledikçe, bu üç bir 10 dakika boyunca bağlı değildir.

**Maliyet hesaplamaları**

- VoIP bacağı (Gamze ve Bob), uygulamadan Iletişim Hizmetleri sunucularına, $0,16 dakikada her katılımcı için $0,004 x 20 dakika x katılımcı
- PSTN giden bacağı (Charlie), Iletişim Hizmetleri sunucularından ABD telefon numarası günde 5 10 dakika x $0,013, dakikada bir katılımcı için aylık olarak 1 saat $0,13

Note: ABD $0,013 ' e kadar karışık ücretler `+1-425` . Ayrıntılar için aşağıdaki bağlantıya başvurun: https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv)

**VoIP + yükseltme çağrısının toplam maliyeti**: $0,16 + $0,13 = $. 29


### <a name="pricing-example-a-user-of-the-communication-services-javascript-sdk-joins-a-scheduled-microsoft-teams-meeting"></a>Fiyatlandırma örneği: Iletişim Hizmetleri JavaScript SDK 'sının bir kullanıcısı, zamanlanmış bir Microsoft ekipleri toplantısı 'nı birleştirir

Çiğdem, hasta, Bob ile bir doktor toplantısı. Çiğdem, takımlar masaüstü uygulamasından ziyaret eden öğesine katılacaktır. Bob, Iletişim Hizmetleri JavaScript SDK 'sını kullanarak toplantıya bağlanan sağlık sağlayıcısı Web sitesini kullanarak katılması için bir bağlantı gönderilir. Bob, bir Web tarayıcısı (Safari ile iPhone) kullanarak toplantıyı girmek için cep telefonuyla birlikte kullanır. Sohbet, sanal ziyaret sırasında kullanılabilir olacaktır.

- Çağrı toplam 30 dakika sürer.
- Çiğdem ve Bob tüm çağrıya katılın. Çiğdem, çağrı başladıktan beş dakika sonra videoyu etkinleştirir ve ekranı 13 dakika boyunca paylaşır. Bob 'un tüm çağrı için videosunu sunun.
- Çiğdem, beş ileti gönderir, Bob üç iletiyle yanıt verir.


**Maliyet hesaplamaları**

- 1 katılımcı (Bob) x 30 dakika başına katılımcı başına x $0,004 = $0,12 [video ve ses aynı hızda ücretlendirilir]
- 1 katılımcı (çiğdem) x 30 dakikalık x $0,000/dakikada katılımcılar/$0,0 *.
- 1 katılımcı (Bob) x 3 Sohbet iletisi x $0,0008 = $0,0024.
- 1 katılımcı (çiğdem) x 5 sohbet iletisi x $0,000 = $0,0 *.

* Çiğdem 'in katılımı takımlar lisanslarından alınmıştır. Azure faturanızda, ekip kullanıcılarının Iletişim Hizmetleri kullanıcılarına sahip olduğu dakika ve sohbet iletileri, ancak takımlar istemcisinden kaynaklanan bu dakikalar ve mesajlar ücretlendirilecektir.

**Ziyaret Için toplam maliyet**:
- Iletişim Hizmetleri JavaScript SDK 'Sı kullanılarak katılan Kullanıcı: $0,12 + $0,0024 = $0,1224
- Ekipler için Kullanıcı katılım masaüstü uygulaması: $0 (takımlar lisansı kapsamında)


## <a name="chat"></a>Sohbet

Iletişim hizmetleriyle, iki veya daha fazla kullanıcı arasında sohbet iletileri gönderme ve alma özelliğiyle uygulamanızı geliştirebilirsiniz. Sohbet SDK 'Ları JavaScript, .NET, Python ve Java için kullanılabilir. SDK 'lar [hakkında bilgi edinmek için bu sayfaya](./sdk-options.md) bakın

### <a name="price"></a>Fiyat

Gönderilen her sohbet iletisi için $0,0008 ücretlendirilirsiniz.

### <a name="pricing-example-chat-between-two-users"></a>Fiyatlandırma örneği: iki kullanıcı arasında sohbet

Geeta, bir güncelleştirmeyi paylaşmak ve 5 ileti göndermesi için bir sohbet iş parçacığı başlatır. Sohbet 10 dakika sürer. Geeta, her biri başka 15 ileti gönderiyor.

**Maliyet hesaplamaları**
- Gönderilen ileti sayısı (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Fiyatlandırma örneği: birden çok kullanıcı ile sohbeti gruplayın

Charlie, arkadaşlarını bir tatil planı için & Jasmine ile bir sohbet iş parçacığı başlatır. Bu kişiler, Charlie 'de, Casey & Jasmine 'nın sırasıyla 20, 30 ve 18 ileti gönderdikleri sırada bir sohbet edebilir. Bu kişilerin, arkadaşınızın yolculuğa katılması, sohbet iş parçacığına eklemesi ve tüm ileti geçmişini kendi ile paylaşması hakkında fark ederler.

Gül iletileri görür ve sohbet başlatır. Bu arada, Casey bir çağrı alır ve konuşmayı daha sonra yakalamak üzere karar verir. Charlie, Jasmine & gül, seyahat tarihlerine karar verir ve sırasıyla bir 30, 25, 35 ileti gönderir.

**Maliyet hesaplamaları**

- Gönderilen ileti sayısı (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefon ve SMS

## <a name="price"></a>Fiyat

Telefon hizmetleri dakika başına fiyatlandırılır, ancak SMS ileti başına ücretlendirilir. Fiyatlandırma, kullandığınız sayının türü ve konumu ile çağrılarınızın ve SMS iletilerinizin hedefinin yerini belirler.

### <a name="telephone-number-leasing"></a>Telefon numarası Kiralama

Telefon numarası kiralama ücretleri önde ücretlendirilir ve ayda bir ay temelinde tekrarlardır:

|Sayı türü   |Aylık ücret   |
|--------------|-----------|
|Yerel (Birleşik Devletler)     |1/ay        |
|Ücretsiz (Birleşik Devletler) |2/ay |


### <a name="telephone-calling"></a>Telefon çağırma

Geleneksel telefon çağrısı (genel anahtarlı telefon ağı üzerinden oluşan çağrı) Birleşik Devletler göre telefon numaraları için Kullandıkça Öde fiyatlandırmasıyla birlikte kullanılabilir. Fiyat, kullanılan sayı türüne ve çağrının hedefine göre dakikalık bir ücretdir. En popüler arama hedeflerine ilişkin fiyatlandırma ayrıntıları aşağıdaki tabloya dahil edilmiştir. Hedeflerin tam listesi için lütfen [ayrıntılı fiyatlandırma listesine](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) bakın.


#### <a name="united-states-calling-prices"></a>Birleşik Devletler çağrı fiyatları

Aşağıdaki fiyatlar 30 Haziran 2021 ' ye kadar gerekli iletişim vergilerini ve ücretlerini içerir:

|Sayı türü   |Çağrı yapmak için   |Çağrı almak için|
|--------------|-----------|------------|
|Yerel     |Başlangıç fiyatı: $0.013/dk       |$0.0085/dk        |
|Ücretsiz |$0.013/dk   |$0.0220/dk |

#### <a name="other-calling-destinations"></a>Diğer çağrı hedefleri

Aşağıdaki fiyatlar 30 Haziran 2021 ' ye kadar gerekli iletişim vergilerini ve ücretlerini içerir:

|Çağrısı yap   |Dakika başına fiyat|
|-----------|------------|
|Kanada     |Başlangıç fiyatı: $0.013/dk   |
|Birleşik Krallık     |Başlangıç fiyatı: $0,015/dk   |
|Almanya     |Başlangıç fiyatı: $0,015/dk   |
|Fransa     |Başlangıç fiyatı: $0,016/dk   |


### <a name="sms"></a>SMS

SMS, Kullandıkça Öde fiyatlandırması sunmaktadır. Fiyat, iletinin hedefine göre ileti başına ücretlendirilir. İletiler, Birleşik Devletler içinde bulunan telefon numaralarına ücretsiz telefon numaraları tarafından gönderilebilir. Yerel (coğrafi) telefon numaralarının SMS iletileri göndermek için kullanılamayacağını unutmayın.

Aşağıdaki fiyatlar 30 Haziran 2021 ' ye kadar gerekli iletişim vergilerini ve ücretlerini içerir:

|Ülke   |İleti gönderme|İleti alma|
|-----------|------------|------------|
|ABD (ücretsiz)    |$0.0075/msg   | $0.0075/msg |
