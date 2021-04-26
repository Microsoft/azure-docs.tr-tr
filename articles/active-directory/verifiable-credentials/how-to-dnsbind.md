---
title: Etki alanınızı, merkezi olmayan tanımlayıcılarınız (DıD) (Önizleme) ile bağlama-Azure Active Directory doğrulanabilir kimlik bilgileri
description: DNS bağlama hakkında bilgi edinin
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: ad5bb6e45479b4cccfa0b002427066439135e468
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588454"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Etki alanınızı, merkezi olmayan tanımlayıcılarınız (DıD) ile ilişkilendirin

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu makalede:
> [!div class="checklist"]
> * Neden etki alanı sitemizi bağlantımız gerekir?
> * Dıds ve etki alanlarını nasıl bağlayacağız?
> * Etki alanı bağlama işlemi nasıl çalışır?
> * Verify/doğrulanmamış etki alanı mantığı nasıl çalışır?

## <a name="prerequisites"></a>Önkoşullar

Uygulamanızı etki alanına bağlamak için aşağıdakileri tamamlamış olmanız gerekir.

- [Başlangıç ve sonraki](get-started-verifiable-credentials.md) [öğretici kümesini](enable-your-tenant-verifiable-credentials.md)doldurun.

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Neden etki alanı sitemizi bağlantımız gerekir?

Bir tanıtıcı, var olan sistemlere sabitlenmiş bir tanımlayıcı olarak başlatılır. Bir kullanıcı veya kuruluş ona sahip olabileceğinden ve bu uygulamayı denetleyebileceğinden, bir, yararlıdır. Kuruluşla etkileşim kuran bir varlık ' kim ' olduğunu bilmez, bu durumda yararlı değildir.

Bir etki alanına bir bağlantı bağlamak, tüm varlıkların şifreli olarak bir etki alanı arasındaki ilişkiyi doğrulamasına izin vererek ilk güven sorununu çözer.

## <a name="how-do-we-link-dids-and-domains"></a>Dıds ve etki alanlarını nasıl bağlayacağız?

Bir etki alanı ile bir bir bağlantı, [Iyi bilinen](https://identity.foundation/.well-known/resources/did-configuration/)bir kimlik Foundation tarafından yazılmış açık bir standart uygulama aracılığıyla bir bağlantı sağlıyoruz. Azure Active Directory (Azure AD) içindeki doğrulanabilen kimlik bilgileri hizmeti, kuruluşunuzun, yaptığınız etki alanı bilgilerini dahil ederek ve iyi bilinen yapılandırma dosyası oluşturarak, BT ile etki alanı arasındaki bağlantıyı yapmasını sağlar:

1. Azure AD, BT belgesi içinde bir hizmet uç noktası yazmak için kuruluş kurulumu sırasında sağladığınız etki alanı bilgilerini kullanır. İle etkileşimde bulunan tüm taraflar, YAPTıĞıNıZ talep ettiğiniz etki alanını görebilir.  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. Azure AD 'deki doğrulanabilir kimlik bilgisi hizmeti, etki alanınızı barındırabildiğiniz uyumlu, tanınmış bir yapılandırma kaynağı oluşturur. Yapılandırma dosyası, etki alanınızı içeren bir kaynak ile imzalanmış ' DomainLinkageCredential ' adlı bir kendi kendine verilen doğrulanabilir kimlik bilgisi içerir. Kök etki alanı URL 'sinde depolanan yapılandırma belgesi örneği aşağıda verilmiştir.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

İyi bilinen yapılandırma dosyasına sahip olduktan sonra, AAD 'nizi doğrulanabilir kimlik bilgileri için etkinleştirirken belirttiğiniz etki alanı adını kullanarak dosyayı kullanılabilir yapmanız gerekir.

* Etki alanının kökündeki iyi bilinen bir yapılandırma dosyasını barındırın.
* Yeniden yönlendirmeleri kullanmayın.
* Yapılandırma dosyasını dağıtmak için HTTPS kullanın.

>[!IMPORTANT]
>Microsoft Authenticator yeniden yönlendirmeleri kabul etmez, belirtilen URL 'nin son hedef URL 'SI olması gerekir.

## <a name="user-experience"></a>Kullanıcı deneyimi 

Bir Kullanıcı bir verme akışından geçiş yaparken veya doğrulanabilir bir kimlik bilgisi sunduklarında, kuruluş ve onun YAPTıĞıNDAKI bir şeyi bilmelidir. Doğrulanabilir kimlik bilgileri cüzdan olan etki alanı Microsoft Authenticator, bir bir bağlantının,, sonuç belgesindeki etki alanıyla ilişkisini doğrular ve sonuca bağlı olarak kullanıcılara iki farklı deneyim sunar.

## <a name="verified-domain"></a>Doğrulanan etki alanı

Microsoft Authenticator **doğrulanmış** bir simge görüntülemeden önce, birkaç şeyin doğru olması gerekir:

* Kendi kendine yayınlanan açma KIMLIĞI (SıOP) isteğinin imzalanmasının, bağlı etki alanı için bir hizmet uç noktası olması gerekir.
* Kök etki alanı bir yeniden yönlendirme kullanmaz ve HTTPS kullanır.
* DıD belgesinde listelenen etki alanı, çözümlenebilen iyi bilinen bir kaynağa sahiptir.
* İyi bilinen kaynağın doğrulanabilir kimlik bilgileri, akışı başlatmak için kullanılan Microsoft Authenticator olan SıOP 'yi imzalamak için kullanılan aynı tanıtıcıyla imzalanır.

Daha önce bahsedilen tüm değerler doğru ise, Microsoft Authenticator doğrulanmış bir sayfa görüntüler ve doğrulanan etki alanını içerir.

![Yeni izin isteği](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Doğrulanmamış etki alanı

Yukarıdakilerden herhangi biri doğru değilse, Microsoft Authenticator kullanıcının etki alanının doğrulanmamış olduğunu, kullanıcının riskli bir işlem ortasında olduğunu ve dikkatli ilerlemelidir. Şu nedenle bu yolu almak için seçtiniz:

* , Bir etki alanına sabitlenmiş değil.
* Yapılandırma düzgün ayarlanmadı.
* Kullanıcının etkileşimde bulunduğu, kötü amaçlı olduğu ve aslında bir etki alanı (Aslında olmadığı için) kanıtlayamamasından dolayı. Bu son nokta nedeniyle, uyarı iletisini yaymamak için, sizi kullanıcının alışkın olduğu etki alanına bağlamanız zorunludur.

![kimlik bilgisi ekle ekranında doğrulanmamış etki alanı uyarısı](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>İyi bilinen yapılandırmayı dağıt

1. Doğrulanabilir kimlik bilgileri ' nde ayarlar sayfasına gidin ve **Bu etki alanını doğrula** ' yı seçin.

   ![Ayarlar ' da bu etki alanını doğrula](media/how-to-dnsbind/settings-verify.png) 

2. Aşağıdaki görüntüde gösterilen dosyada did-configuration.jsindirin.

   ![İyi bilinen yapılandırmayı indir](media/how-to-dnsbind/verify-download.png) 

3. JWT 'yi kopyalayın, [JWT.MS](https://www.jwt.ms) açın ve etki alanının doğru olduğunu doğrulayın.

4. DID belgesini kopyalayın ve aynı etki alanının, DıD belgesine dahil edildiğini doğrulamak için [Iyon ağ Gezginini](https://identity.foundation/ion/explorer) açın. 

5. Belirtilen konumda iyi bilinen yapılandırma kaynağını barındırın. Örnek: `https://www.example.com/.well-known/did-configuration.json`

6. Doğrulamak için Microsoft Authenticator test etme veya sunma. Kimlik doğrulayıcısında ' güvenli olmayan uygulamalar hakkında uyar ' ayarının açık olduğundan emin olun.

>[!NOTE]
>Varsayılan olarak, ' güvenli olmayan uygulamalar hakkında uyar ' seçeneği açıktır.

Tebrikler, artık önyüklendi bir güven Web 'e sahipsiniz!

## <a name="next-steps"></a>Sonraki adımlar

Ekleme sırasında, değiştirmek istediğiniz yanlış etki alanı bilgilerini girdiğinizde, bu işlemi değiştirmeye karar vermeniz [gerekir.](how-to-opt-out.md) Şu anda, bu belgenin güncelleştirilmesini desteklemiyoruz. Kullanıma almak ve geri dönmek yeni bir yepyeni bir Işlem oluşturur.
