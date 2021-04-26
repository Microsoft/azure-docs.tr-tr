---
title: Doğrulanabilir kimlik bilgilerini veren olarak Iptal etme-Azure Active Directory doğrulanabilir kimlik bilgileri
description: Vermiş olduğunuz bir doğrulanabilen kimlik bilgisini iptal etmeyi öğrenin
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222852"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Daha önce verilen bir doğrulanabilen kimlik bilgisini iptal etme (Önizleme)

Doğrulanabilir kimlik bilgileri (VC) ile çalışma sürecinin bir parçası olarak yalnızca kimlik bilgilerini vermek zorunda değilsiniz, ancak bazen bunları iptal etmeniz gerekir. Bu makalede, VC belirtiminin **durum** özelliği bölümüne giderek, neden kimlik bilgilerini iptal etmek istediğimiz ve bazı veriler ve gizlilik etkilerine ilişkin daha yakından bir göz atalım.

> [!IMPORTANT]
> Azure Active Directory doğrulanabilir kimlik bilgileri şu anda genel önizlemede.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Doğrulanabilir kimlik bilgileri belirtiminde Status özelliği

Doğrulanabilir kimlik bilgisini iptal etme etkilerine ilişkin etkileri anlayabilmemiz için, **durum denetiminin** ne olduğunu ve bugün nasıl çalıştığını bilmek yardımcı olabilir.

[W3C doğrulanabilir kimlik bilgileri spec](https://www.w3.org/TR/vc-data-model/) , 4,9 bölümünde **Status** özelliğine başvurur [:](https://www.w3.org/TR/vc-data-model/#status)

"Bu belirtim, bir doğrulanabilen kimlik bilgisinin geçerli durumu hakkında bilgi bulma için aşağıdaki **Credentialstatus** özelliğini tanımlar, örneğin askıya alınma veya iptal edilip edilmeyeceğini belirtir."

Ancak, W3C belirtimi **durum denetiminin** nasıl uygulanması gerektiği üzerine bir biçim tanımlamaz.

"Durum şemaları için veri modelini, biçimlerini ve protokollerini tanımlama, bu belirtim için kapsam dışında. Doğrulanabilir kimlik bilgileri durum denetimi uygulamak isteyen uygulayıcılar için kullanılabilir durum düzenlerini içeren doğrulanabilir bir kimlik bilgisi uzantısı kayıt defteri [VC-EXTENSION-REGISTRY] var. "

>[!NOTE]
>Şimdilik Microsoft 'un durum denetimi uygulamasına özeldir, ancak standart bir şekilde hizalamak için, BT topluluğuyla etkin bir şekilde çalışıyoruz.

## <a name="how-does-the-status-property-work"></a>**Durum** özelliği nasıl çalışır?

Her Microsoft tarafından verilen doğrulanabilir kimlik bilgisi için, credentialStatus adlı bir öznitelik vardır. Bu, Microsoft 'un sizin adınıza yönettiği bir durum API 'SI ile doldurulur. Neye benzer bir örnek aşağıda verilmiştir.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

Açık kaynak doğrulanabilir kimlik bilgileri SDK 'Sı, durum API 'sini çağırmayı ve gerekli verileri sağlamayı işler.

API çağrıldıktan ve doğru bilgileri sağladıkça, API doğru veya yanlış döndürür. Doğrulanabilir kimlik bilgileri, verenle etkin olmaya devam eder ve doğrulanabilir kimlik bilgisini veren tarafından etkin bir şekilde iptal edildi.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Bir VC 'yi neden iptal etmek isteyebilirsiniz?

Her müşteri, doğrulanabilen bir kimlik bilgisini iptal etmek için kendi benzersiz nedenlerinden oluşur, ancak bu nedenle şimdiye kadar duyduğumuz ortak temalardan bazıları aşağıda verilmiştir. 

- Öğrenci KIMLIĞI: öğrenciye artık University 'de etkin bir öğrenci yok.
- Çalışan KIMLIĞI: çalışan artık etkin bir çalışan değil.
- Durum sürücüleri lisansı: sürücü artık bu durumda yer alınmaz.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Yok etme özelliği ile doğrulanabilir kimlik bilgisi ayarlama

Tüm doğrulanabilir kimlik bilgileri verileri varsayılan olarak Microsoft ile depolanmaz. Bu nedenle, belirli bir doğrulanabilir kimlik bilgisi KIMLIĞINI iptal etmek için başvuruya yönelik veri yok. Verenin, Microsoft to Index ve sonra anahtar ve karma kimlik bilgisi özniteliğinden belirli bir alanı belirtmesi gerekir.

>[!NOTE]
>Karma değeri, bir girişi kapatan ```preimage``` ve sabit uzunlukta bir karma olarak adlandırılan bir çıktı üreten tek yönlü bir şifreleme işlemidir. Bir karma işlemi tersine çevirmek için şu anda hesaplama yapılabilir değildir.

Microsoft 'un hangi doğrulanabilir kimlik bilgisi özniteliğini indekslemek istediğinizi söyleyebilirsiniz. Dizin oluşturmanın ne kadar etkili olması, dizin oluşturma değerlerinin, iptal etmek istediğiniz VCs ilgili doğrulanabilir kimlik bilgilerinizi aramak için kullanılabilir olmasını sağlar.

**Örnek:** Çiğdem, Woodgrove çalışanı. Çiğdem, contoso 'da çalışmak için Woodgrove bıraktı. Woodgrove için BT Yöneticisi olan Gamze, bu kimlik bilgileriyle gamze 'nin e-postasını arar arama sorgusunu Iptal eder. Bu örnekte, Gamze, Woodgrove doğrulanan çalışan kimlik bilgisinin e-posta alanı olarak dizinlenir. 

Kural dosyasının dizini içerecek şekilde nasıl değiştirildiği hakkında bir örnek için aşağıya bakın.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>Bir kural dosyasından yalnızca bir özniteliğin dizini oluşturulabilir.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Doğrulanabilir kimlik bilgisini iptal Nasıl yaparım?

Bir dizin talebi ayarlandıktan ve kullanıcılarınıza doğrulanabilir kimlik bilgileri verildikten sonra, VC dikey penceresinde doğrulanabilir bir kimlik bilgisini nasıl iptal edekullanabileceğinizi görmeniz zaman alabilir.

1. Azure Active Directory ' de doğrulanabilir kimlik bilgileri dikey penceresine gidin.
1. Daha önce dizin talebi ayarladığınız ve bir kullanıcıya doğrulanabilir kimlik bilgisi veren doğrulanabilir kimlik bilgisini seçin. =
1. Sol taraftaki menüden kimlik bilgisini iptal et kimlik  
    ![ bilgisini iptal et ' i seçin](media/how-to-issuer-revoke/settings-revoke.png) 
1. İptal etmek istediğiniz kullanıcının dizin özniteliğini arayın. 

   ![İptal edilecek kimlik bilgisini bul](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Yalnızca doğrulanabilen kimlik bilgilerinden dizinli talebin karmasını depoladığından, arama sonuçlarını yalnızca tam bir eşleşme dolduracaktır. Giriş, BT Yöneticisi tarafından arandığı şekilde ele aldık ve veritabanımızda bir karma eşleşmemiz olup olmadığını görmek için aynı karma algoritmayı kullanıyoruz.
    
1. Bir eşleşme bulduktan sonra, iptal etmek istediğiniz kimlik bilgisinin sağ tarafındaki **Revoke** seçeneğini belirleyin.

   ![İptal işleminden sonra kullanıcının kimlik bilgisini hala aldığına ilişkin bir uyarı verir](media/how-to-issuer-revoke/warning.png) 

1. Başarıyla İptalden sonra durum güncelleştirmesini görürsünüz ve sayfanın üst kısmında yeşil bir başlık görüntülenir. 
   ![Ayarlar ' da bu etki alanını doğrula](media/how-to-issuer-revoke/revoke-successful.png) 

Artık bağlı olan bir taraf, Microsoft 'un durum API 'SI olan bu kimlik bilgisinin durumunu denetlemek için, kiracı adına işlem yapan bir ' false ' yanıtı döndürür.

## <a name="next-steps"></a>Sonraki adımlar

Akışa yönelik bir sınama kimlik bilgisiyle kendi üzerinde işlevselliği test edin. [Öğreticilerimizi inceleyerek](get-started-verifiable-credentials.md), kiracınızı doğrulanabilir kimlik bilgileri verecek şekilde yapılandırma hakkında bilgi görebilirsiniz.