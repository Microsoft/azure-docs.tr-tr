---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 43e3463a3284f57825073888146b38fa14cbf5d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109054"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Dağıtılan bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Iletişim Hizmetleri kaynağında alınan telefon numarası. [telefon numarası alma](../../telephony-sms/get-phone-number.md).
- Bir `User Access Token` çağrı istemcisini etkinleştirmek için. [Nasıl yapılır `User Access Token` ](../../access-tokens.md) hakkında daha fazla bilgi edinmek için
- [Uygulamanıza çağrı ekleme ile çalışmaya](../getting-started-with-calling.md) başlama için hızlı başlangıcı doldurun

### <a name="prerequisite-check"></a>Önkoşul denetimi

- Iletişim Hizmetleri kaynağınız ile ilişkili telefon numaralarını görüntülemek için [Azure Portal](https://portal.azure.com/)oturum açın, iletişim hizmetleri kaynağınızı bulun ve sol gezinti bölmesinden **telefon numaraları** sekmesini açın.
- İOS için SDK 'Yı çağıran Azure Iletişim Hizmetleri ile uygulamanızı oluşturabilir ve çalıştırabilirsiniz:

## <a name="setting-up"></a>Ayarlanıyor

## <a name="start-a-call-to-phone"></a>Telefon çağrısı Başlat

Iletişim Hizmetleri kaynağında edindiğiniz telefon numarasını belirtin; Bu, çağrıyı başlatmak için kullanılır:
> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör.: + 12223334444)

`startCall` *Çağrıya başla* düğmesine dokunduğunda gerçekleştirilecek olay işleyicisini değiştirin:

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.startCall([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>Kodu çalıştırma

**Ürün**  >  **çalıştırması** ' nı seçerek veya (&#8984;-R) klavye kısayolunu kullanarak uygulamanızı iOS simülatörü üzerinde oluşturabilir ve çalıştırabilirsiniz.

![Hızlı başlangıç uygulamasına yönelik nihai görünüm](../media/ios/quick-start-make-call.png)

Eklenen metin alanına bir telefon numarası girerek ve **çağrıya başla** düğmesine tıklayarak telefon araması yapabilirsiniz.
> [!WARNING]
> Telefon numaralarının E. 164 uluslararası standart biçiminde sağlanması gerektiğini unutmayın. (ör.: + 12223334444)

> [!NOTE]
> İlk kez bir çağrı yaptığınızda sistem mikrofona erişim ister. Bir üretim uygulamasında API 'yi kullanarak `AVAudioSession` [izin durumunu denetleyin](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) ve izin verilmediği zaman uygulamanızın davranışını dikkatlice güncelleştirebilirsiniz.
