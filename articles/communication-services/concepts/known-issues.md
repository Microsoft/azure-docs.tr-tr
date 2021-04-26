---
title: Azure Iletişim Hizmetleri-bilinen sorunlar
description: Azure Iletişim Hizmetleri hakkında daha fazla bilgi edinin
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: b9ed71a8fc9346ecd454eba98dcbb3b13186eba2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276051"
---
# <a name="known-issues-azure-communication-services-calling-sdks"></a>Bilinen sorunlar: SDK 'Ları çağıran Azure Iletişim Hizmetleri
Bu makale, SDK 'Ları çağıran Azure Iletişim hizmetleriyle ilgili sınırlamalar ve bilinen sorunlar hakkında bilgi sağlar.

> [!IMPORTANT]
> Çağırma deneyiminizin kalitesini etkileyebilecek birden çok etken vardır. Iletişim Hizmetleri ağ yapılandırması ve test en iyi uygulamaları hakkında daha fazla bilgi edinmek için **[ağ gereksinimleri](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** belgelerine bakın.


## <a name="javascript-sdk"></a>JavaScript SDK'sı

Bu bölümde, Azure Communication Services JavaScript ses ve video çağırma SDK 'leriyle ilişkili bilinen sorunlar hakkında bilgi verilmektedir.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>Bir sayfanın yenilenmesi, kullanıcıyı doğrudan çağrılarından kaldırmaz

Bir Kullanıcı çağralıyorsa ve sayfayı yenilemeye karar verirse, Iletişim Hizmetleri medya hizmeti bu kullanıcıyı doğrudan çağrıdan kaldırmaz. Kullanıcının yeniden katıllanmasını bekler. Medya hizmeti zaman aşımına uğraydıktan sonra Kullanıcı çağrıdan silinir.

Son kullanıcıların, çağrıdayken uygulamanızın sayfasını yenilemesini gerektirmeyen kullanıcı deneyimleri oluşturmak en iyisidir. Bir Kullanıcı sayfayı yenilediğinde, uygulamaya geri döntiklerinde aynı Iletişim Hizmetleri kullanıcı KIMLIĞINI yeniden kullanın.

Çağrısındaki diğer katılımcıların perspektifinden, Kullanıcı zaman dilimi (1-2 dakika) çağrısında kalır. Kullanıcı aynı Iletişim Hizmetleri Kullanıcı KIMLIĞIYLE yeniden katılıyordu, koleksiyonda var olan bir nesne olarak temsil edilir `remoteParticipants` .

Kullanıcı yenilemeden önce video gönderolduysa, `videoStreams` koleksiyon, hizmet zaman aşımına uğrayana kadar önceki akış bilgilerini tutar ve bu işlemi kaldırır. Bu senaryoda, uygulama koleksiyona eklenen yeni akışları gözlemlemeye karar verebilir ve en yüksek olanı işleyebilir `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Web 'de birden fazla cihazdan birden çok önizleme işlemek mümkün değildir
Bu bilinen bir sınırlamadır. Daha fazla bilgi için, [ÇAĞıRAN SDK 'ya genel bakış](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features)bölümüne bakın.

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>Uygulama iOS veya ıpados üzerinde çalıştırıldığında Safari 'de cihazların numaralandırılması mümkün değildir

Uygulamalar Safari iOS/iPad üzerinde mik/hoparlör cihazlarını (Bluetooth gibi) numaralandıramaz/seçemezsiniz. Bu, bilinen bir işletim sistemi sınırlamasıdır.

MacOS 'ta Safari kullanıyorsanız, uygulamanız Iletişim Hizmetleri Device Manager aracılığıyla hoparlörleri numaralandıramaz/seçemeyecektir. Bu senaryoda, cihazların işletim sistemi aracılığıyla seçilmesi gerekir. MacOS üzerinde Chrome kullanıyorsanız, uygulama Iletişim Hizmetleri Device Manager aracılığıyla cihazları numaralandırabilir/seçebilir.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Devam eden bir VoIP çağrısı sırasında SMS iletileri veya çağrıları alınırken ses bağlantısı kayboldu
Bu sorun, birden çok nedenden dolayı oluşabilir:

- Bazı mobil tarayıcılar, arka plan durumundayken bağlantı tutmayın. Bu, VoIP çağrısı, uygulamanızı arka plana gönderen bir olay tarafından kesintiye uğrarsa, düzeyi düşürülmüş bir çağrı deneyimine yol açabilir. 
- Bazen, SMS veya PSTN çağrısı ses sesini yakalar ve VoIP çağrısına ses serbest bırakmaz. Apple bu sorunu iOS 14.4.1 + sürümlerinde düzeltti. 

<br/>İstemci kitaplığı: çağırma (JavaScript)
<br/>Tarayıcılar: Safari, Chrome
<br/>İşletim sistemi: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Video cihazlarının sürekli olarak değiştirilmesi video akışının geçici olarak durdurulmasına neden olabilir

Video cihazları arasında geçiş yapmak, akış seçili cihazdan elde edilirken video akışının duraklatılmasına neden olabilir.

#### <a name="possible-causes"></a>Olası nedenler
Cihazlar arasında sık geçiş yapmak, performansın düşmesine neden olabilir. Geliştiricilerin bir cihaz akışını başka bir başlatmadan önce durdurması önerilir.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Bluetooth kulaklık mikrofonu algılanamadı; bu nedenle, iOS üzerinde Safari çağrısı sırasında duyulabilir değildir
Bluetooth kulaklıklar, iOS üzerinde Safari tarafından desteklenmez. Bluetooth cihazınız, kullanılabilir mikrofon seçeneklerinde listelenmez ve Safari 'yi Safari üzerinden kullanmayı denerseniz diğer katılımcılar sizi duymayacak.

#### <a name="possible-causes"></a>Olası nedenler
Bu bilinen bir macOS/iOS/ıpados işletim sistemi sınırlamasıdır. 

**MacOS** ve **IOS/ıpados** üzerinde Safari ile, hoparlörler numaralandırması/seçimi Safari tarafından desteklenmediğinden, iletişim hizmetleri Device Manager konuşmacı cihazlarını listelemek/seçmek mümkün değildir. Bu senaryoda, cihazınızın seçiminiz işletim sistemi ile güncelleştirilmeleri gerekir.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>Bir cihazın dönmesi kötü video kalitesi oluşturabilir
Kullanıcılar, cihazlar döndürüldüğünde azaltılmış video kalitesiyle karşılaşabilir.

<br/>Etkilenen cihazlar: Google pixel 5, Google pixel 3A, Apple iPad 8 ve Apple iPad X
<br/>İstemci kitaplığı: çağırma (JavaScript)
<br/>Tarayıcılar: Safari, Chrome
<br/>İşletim sistemi: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>Kamera değişimi, ekranın donmasına neden olur 
Bir Iletişim Hizmetleri kullanıcısı, JavaScript 'ı çağıran SDK 'Yı kullanarak bir çağrıya katıldığında ve ardından kamera anahtar düğmesini ziyaret edildiğinde, uygulama yenilenene veya tarayıcı kullanıcı tarafından arka plana itilene kadar kullanıcı ARABIRIMI yanıt vermemeye başlayabilir.

<br/>Etkilenen cihazlar: Google pixel 4A
<br/>İstemci kitaplığı: çağırma (JavaScript)
<br/>Tarayıcılar: Chrome
<br/>İşletim sistemi: iOS, Android


#### <a name="possible-causes"></a>Olası nedenler
İnceleme altında.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Çağrı "bağlanıyor" durumundayken video sinyali durdurulmuşsa, bu işlem, çağrı başladıktan sonra gönderilmez 
Kullanıcılar, arama durumundayken videoyu hızlı bir şekilde açmaya/kapatmaya karar verirse, `Connecting` Bu durum çağrı için alınan akışta soruna neden olabilir. Geliştiricilerin uygulamalarını, arama durumundayken bir videonun açık/kapalı olmasını gerektirmeyen bir şekilde oluşturmasını öneririz `Connecting` . Bu sorun, aşağıdaki senaryolarda ekran performansının düşmesine neden olabilir:

 - Kullanıcı sesle başlıyorsa ve ardından, arama durumundayken videoyu başlatabilir ve durdurabilirsiniz `Connecting` .
 - Kullanıcı sesle başlıyorsa ve ardından, arama durumundayken videoyu başlatabilir ve durdurabilirsiniz `Lobby` .

#### <a name="possible-causes"></a>Olası nedenler
İnceleme altında.

### <a name="enumeratingaccessing-devices-for-safari-on-macos-and-ios"></a>MacOS ve iOS 'ta Safari için cihazları numaralandırma/bunlara erişme 
Cihazlara erişim izni verildiyse, bir süre sonra cihaz izinleri sıfırlanır. MacOS ve iOS üzerinde Safari, alınmış bir akış olmadıkça çok uzun süre boyunca izin vermez. Bu sorunu geçici olarak çözmek için en basit yol, Cihaz Yöneticisi 'nin cihaz numaralandırma API 'Lerini (DeviceManager. Getkameralar (), DeviceManager. Gethoparlörler () ve DeviceManager. Getmikrofonlar ()) çağırmadan önce DeviceManager. askDevicePermission () API 'sini çağırmadır. İzinler varsa, Kullanıcı hiçbir şey görmez, aksi takdirde, yeniden istenir.

<br/>Etkilenen cihazlar: iPhone
<br/>İstemci kitaplığı: çağırma (JavaScript)
<br/>Tarayıcılar: Safari
<br/>İşletim sistemi: iOS

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Bazen uzak katılımcı videolarını işlemek uzun zaman alır
Devam eden bir grup çağrısı sırasında, _A kullanıcısı bir_ video gönderir ve ardından _B kullanıcısı_ çağrıyı birleştirir. Bazen, B kullanıcısı A kullanıcısının videosunu göremez veya Kullanıcı A 'nın videosu uzun bir gecikmeden sonra işlemeye başlar. Bu sorun, daha fazla yapılandırma gerektiren bir ağ ortamından kaynaklanıyor olabilir. Ağ Yapılandırma Kılavuzu için [ağ gereksinimleri](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) belgelerine bakın.
