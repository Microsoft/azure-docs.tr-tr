---
title: Kullanıcı geçişi yaklaşımları
titleSuffix: Azure AD B2C
description: Geçiş öncesi veya sorunsuz geçiş yöntemlerini kullanarak, başka bir kimlik sağlayıcısından Kullanıcı hesaplarını Azure AD B2C 'e geçirin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e60b80a4ebeaef7d31d4c0c1d9d4bfc41ec3a56
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256217"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Kullanıcıları Azure AD B2C'ye geçirme

Başka bir kimlik sağlayıcısından Azure Active Directory B2C (Azure AD B2C) uygulamasına geçiş, mevcut kullanıcı hesaplarının geçirilmesini de gerektirebilir. Burada iki geçiş yöntemi ele alınmıştır, *geçiş öncesi* ve *sorunsuz geçiş*. Her iki yaklaşım da, Azure AD B2C içinde Kullanıcı hesapları oluşturmak için [MICROSOFT Graph API](microsoft-graph-operations.md) 'sini kullanan bir uygulama veya betik yazmanız gerekir.

## <a name="pre-migration"></a>Geçiş öncesi

Geçiş öncesi akışta, geçiş uygulamanız her kullanıcı hesabı için bu adımları gerçekleştirir:

1. Geçerli kimlik bilgileri (Kullanıcı adı ve parola) dahil olmak üzere eski kimlik sağlayıcısından Kullanıcı hesabını okuyun.
1. Azure AD B2C dizininizde geçerli kimlik bilgileriyle ilgili bir hesap oluşturun.

Geçiş öncesi akışını şu iki durumda kullanın:

- Kullanıcının düz metin kimlik bilgilerine (Kullanıcı adı ve parola) erişiminiz var.
- Kimlik bilgileri şifrelenir, ancak şifrelerini çözebilirsiniz.

Programlı olarak Kullanıcı hesapları oluşturma hakkında bilgi için bkz. [Microsoft Graph Azure AD B2C Kullanıcı hesaplarını yönetme](microsoft-graph-operations.md).

## <a name="seamless-migration"></a>Kesintisiz geçiş

Eski kimlik sağlayıcıdaki düz metin parolalara erişilemezse sorunsuz geçiş akışını kullanın. Örneğin:

- Parola, karma işlevi gibi tek yönlü şifrelenmiş biçimde depolanır.
- Parola, eski kimlik sağlayıcısı tarafından erişim için bir şekilde depolanır. Örneğin, kimlik sağlayıcısı bir Web hizmetini çağırarak kimlik bilgilerini doğrular.

Sorunsuz geçiş akışı hala Kullanıcı hesaplarının geçirilmesini gerektirir, ancak ardından ilk oturum açma sırasında her bir kullanıcının parolasını ayarlamak için bir [REST API](custom-policy-rest-api-intro.md) (oluşturduğunuz) sorgulamak için [özel bir ilke](user-flow-overview.md) kullanır.

Bu nedenle kesintisiz geçiş akışı iki aşamaya sahiptir: *geçiş öncesi* ve *kimlik bilgilerini ayarla*.

### <a name="phase-1-pre-migration"></a>1. Aşama: geçiş öncesi

1. Geçiş uygulamanız, eski kimlik sağlayıcısından Kullanıcı hesaplarını okur.
1. Geçiş uygulaması Azure AD B2C dizininizde karşılık gelen Kullanıcı hesaplarını oluşturur, ancak oluşturduğunuz *rastgele parolaları ayarlar* .

### <a name="phase-2-set-credentials"></a>2. Aşama: kimlik bilgilerini ayarlama

Hesapların geçişi tamamlandıktan sonra, özel ilkeniz ve REST API bir Kullanıcı oturum açtığında aşağıdakileri gerçekleştirin:

1. Girilen e-posta adresine karşılık gelen Azure AD B2C Kullanıcı hesabını okuyun.
1. Boole uzantı özniteliği hesaplanarak hesabın geçiş için işaretlenip işaretlenmediğini kontrol edin.
    - Uzantı özniteliği döndürürse `True` , eski kimlik sağlayıcısına karşı parolayı doğrulamak için REST API çağırın.
      - REST API parolanın yanlış olduğunu belirlerse, kullanıcıya kolay bir hata döndürün.
      - REST API parolanın doğru olduğunu belirlerse, parolayı Azure AD B2C hesaba yazın ve Boole uzantısı özniteliğini olarak değiştirin `False` .
    - Boole uzantısı özniteliği döndürürse `False` , oturum açma işlemine normal olarak devam edin.

Örnek özel bir ilke ve REST API görmek için GitHub 'daki [sorunsuz Kullanıcı geçişi örneğine](https://aka.ms/b2c-account-seamless-migration) bakın.

![Kullanıcı geçişine sorunsuz geçiş yaklaşımının akış çizelgesi diyagramı](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diyagram: sorunsuz geçiş akışı*

## <a name="best-practices"></a>En iyi uygulamalar

### <a name="security"></a>Güvenlik

Sorunsuz geçiş yaklaşımı, kullanıcının kimlik bilgilerini eski kimlik sağlayıcısına karşı doğrulamak için kendi özel REST API kullanır.

**REST API deneme yanılma saldırılarına karşı korumanız gerekir.** Bir saldırgan, en sonunda bir kullanıcının kimlik bilgilerini tahmin etmek için birkaç parola gönderebilir. Bu tür saldırıları erteetmenize yardımcı olmak için, oturum açma girişimlerinin sayısı belirli bir eşiği geçtiğinde istekleri REST API sunma. Ayrıca, Azure AD B2C ve REST API arasındaki iletişimin güvenliğini sağlayın. Üretim için yeniden yapılan API 'lerinizi güvenli hale getirme hakkında bilgi edinmek için bkz. [güvenli restsize API](secure-rest-api.md).

### <a name="user-attributes"></a>Kullanıcı öznitelikleri

Eski kimlik sağlayıcısındaki bilgilerin tümünün Azure AD B2C dizinine geçirilmesi gerekmez. Geçirmeden önce Azure AD B2C içinde depolanacak uygun kullanıcı özniteliği kümesini belirleyin.

- Azure AD B2C 'de mağaza **yapın**
  - Kullanıcı adı, parola, e-posta adresleri, telefon numaraları, üyelik numaraları/tanımlayıcıları.
  - Gizlilik ilkesi ve son kullanıcı lisans sözleşmeleri için onay işaretçileri.
- Azure AD B2C **içinde depolamayın**
  - Kredi kartı numaraları, sosyal güvenlik numaraları (SSN), tıbbi kayıtlar veya kamu ya da sektör uyumluluk gövdeleri tarafından düzenlenen diğer veriler gibi hassas veriler.
  - Pazarlama veya iletişim tercihleri, kullanıcı davranışları ve Öngörüler.

### <a name="directory-clean-up"></a>Dizin Temizleme

Geçiş işlemine başlamadan önce dizininizi Temizleme fırsatına sahip olursunuz.

- Azure AD B2C depolanacak kullanıcı özniteliklerinin kümesini belirleyin ve yalnızca ihtiyacınız olanları geçirin. Gerekirse, bir kullanıcı hakkında daha fazla veri depolamak için [özel öznitelikler](user-flow-custom-attributes.md) oluşturabilirsiniz.
- Birden çok kimlik doğrulama kaynağına sahip bir ortamdan geçiş yapıyorsanız (örneğin, her uygulama kendi kullanıcı dizinine sahipse) Azure AD B2C Birleşik bir hesaba geçirin.
- Birden çok uygulamanın farklı Kullanıcı adları varsa, kimlikler koleksiyonunu kullanarak bunları bir Azure AD B2C Kullanıcı hesabında saklayabilirsiniz. Parolayla ilgili olarak, kullanıcının bir tane seçmesini ve dizinde ayarlayabilmesine izin verin. Örneğin, sorunsuz geçişle yalnızca seçilen parolanın Azure AD B2C hesapta depolanması gerekir.
- Geçişten önce kullanılmayan kullanıcı hesaplarını kaldırın veya eski hesapları geçirmeyin.

### <a name="password-policy"></a>Parola ilkesi

Geçirdiğiniz hesapların Azure AD B2C tarafından zorlanan [güçlü parola kuvvetinden](../active-directory/authentication/concept-sspr-policy.md) daha zayıf olması durumunda, güçlü parola gereksinimini devre dışı bırakabilirsiniz. Daha fazla bilgi için bkz. [parola ilkesi özelliği](user-profile-attributes.md#password-policy-attribute).

## <a name="next-steps"></a>Sonraki adımlar

GitHub 'daki [Azure-AD-B2C/User-geçiş](https://github.com/azure-ad-b2c/user-migration) deposu, sorunsuz bir geçiş özel ilke örneği ve REST API kod örneği içerir:

[Sorunsuz Kullanıcı geçişi özel ilkesi & REST API kod örneği](https://aka.ms/b2c-account-seamless-migration)
