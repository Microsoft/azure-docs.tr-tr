---
title: Azure AD Kullanıcı akışlarında dil özelleştirmesi
description: Azure Active Directory Kullanıcı akışlarınızda dil deneyimini özelleştirme hakkında bilgi edinin.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: a199c207e8ea35f1471df9bfd0c4134551b9995f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653499"
---
# <a name="language-customization-in-azure-active-directory"></a>Azure Active Directory dil özelleştirmesi

Azure Active Directory (Azure AD) içindeki dil özelleştirmesi, Kullanıcı akışınızın kullanıcılarınızın ihtiyaçlarına uyacak şekilde farklı dillere uyum sağlamasına izin verir. Microsoft, [36 dil](#supported-languages)için Çeviriler sağlar. Deneyiminiz yalnızca tek bir dil için sağlanmış olsa bile öznitelik koleksiyonu sayfasında öznitelik adlarını özelleştirebilirsiniz.

## <a name="how-language-customization-works"></a>Dil özelleştirmesi nasıl kullanılır?

Varsayılan olarak, tutarlı bir kaydolma deneyimi sağlamak üzere kaydolan kullanıcılar için dil özelleştirmesi etkinleştirilmiştir. Kayıt sırasında öznitelik toplama işleminin parçası olarak kullanıcılara görüntülenecek dizeleri değiştirmek için dilleri kullanabilirsiniz.

> [!NOTE]
> Özel Kullanıcı özniteliklerini kullanıyorsanız kendi çevirilerinizi sağlamanız gerekir. Daha fazla bilgi için bkz. [dizelerinizi özelleştirme](#customize-your-strings).

## <a name="customize-your-strings"></a>Dizelerinizi özelleştirin

Dil özelleştirmesi, Kullanıcı akışındaki herhangi bir dizeyi özelleştirmenize olanak sağlar.

1. [Azure portalda](https://portal.azure.com) Azure AD yöneticisi olarak oturum açın.
2. **Azure hizmetleri** altında **Azure Active Directory**' yi seçin.
3. Sol taraftaki menüden **dış kimlikler**' i seçin.
4. **Kullanıcı akışları ' nı** seçin.
3. Çeviriler için etkinleştirmek istediğiniz kullanıcı akışını seçin.
4. **Dilleri** seçin.
5. Kullanıcı akışının **Diller** sayfasında, özelleştirmek istediğiniz dili seçin.
6. **Öznitelik koleksiyonu sayfasını** genişletin.
7. **Varsayılanları indir** ' i seçin (veya daha önce bu dili düzenlediyseniz, **geçersiz kılmaları indir** ).

Bu adımlar size Dizelerinizin düzenlenmesine başlamak için kullanabileceğiniz bir JSON dosyası sağlar.

### <a name="change-any-string-on-the-page"></a>Sayfadaki herhangi bir dizeyi değiştirme

1. JSON düzenleyicisinde önceki yönergelerden indirilen JSON dosyasını açın.
1. Değiştirmek istediğiniz öğeyi bulun. `StringId`Aradığınız dize için arama yapabilir veya `Value` değiştirmek istediğiniz özniteliği arayabilirsiniz.
1. Özniteliğini, `Value` görüntülenmesini istediğiniz şekilde güncelleştirin.
1. Değiştirmek istediğiniz her dize için `Override` olarak değiştirin `true` .
1. Dosyayı kaydedin ve değişikliklerinizi karşıya yükleyin. (Karşıya yükleme denetimini, JSON dosyasını indirdiğiniz konum ile aynı yerde bulabilirsiniz.)

> [!IMPORTANT]
> Bir dizeyi geçersiz kılmanız gerekirse değerini olarak ayarladığınızdan emin olun `Override` `true` . Değer değiştirilmemişse, giriş yok sayılır.

### <a name="change-extension-attributes"></a>Uzantı özniteliklerini Değiştir

Bir özel kullanıcı özniteliği için dizeyi değiştirmek isterseniz veya bir JSON öğesine eklemek istiyorsanız, şu biçimdedir:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

`<ExtensionAttribute>`Özel Kullanıcı özniteme adıyla değiştirin.

`<ExtensionAttributeValue>`Görüntülenecek yeni dize ile değiştirin.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections kullanarak değer listesi sağlama

Yanıtlar için bir değer listesi kümesi sağlamak istiyorsanız, bir öznitelik oluşturmanız gerekir `LocalizedCollections` . `LocalizedCollections` , `Name` ve `Value` çiftleri dizisidir. Öğelerin sırası görüntülendikleri sıra olacaktır. Eklemek için `LocalizedCollections` aşağıdaki biçimi kullanın:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` , bu `LocalizedCollections` özniteliğin yanıt olduğu Kullanıcı özniteliğidir.
* `Name` , kullanıcıya gösterilen değerdir.
* `Value` Bu seçenek belirlendiğinde talepte döndürülen değer.

### <a name="upload-your-changes"></a>Değişikliklerinizi karşıya yükleyin

1. JSON dosyanızdaki değişiklikleri tamamladıktan sonra kiracınıza geri dönün.
1. **Kullanıcı akışları** ' nı seçin ve çeviriler için etkinleştirmek istediğiniz kullanıcı akışına tıklayın.
1. **Dilleri** seçin.
1. Çevirmek istediğiniz dili seçin.
1. **Öznitelik koleksiyonu sayfası** seçin.
1. Klasör simgesini seçin ve karşıya yüklenecek JSON dosyasını seçin.

Değişiklikler Kullanıcı akışınıza otomatik olarak kaydedilir.

## <a name="additional-information"></a>Ek bilgiler

### <a name="page-ui-customization-labels-as-overrides"></a>Geçersiz kılmalar olarak sayfa Kullanıcı arabirimi özelleştirme etiketleri

Dil özelleştirmesini etkinleştirdiğinizde, sayfa UI özelleştirmesi kullanan etiketlere yönelik önceki düzenlemeleriniz Ingilizce (en) için bir JSON dosyasında kalıcıdır. Dil özelleştirmesindeki dil kaynaklarını karşıya yükleyerek etiketlerinizi ve diğer dizelerinizi değiştirmeye devam edebilirsiniz.

### <a name="up-to-date-translations"></a>Güncel Çeviriler

Microsoft, kullanım için en güncel çevirileri sağlamaya kararlıdır. Microsoft, çevirileri sürekli olarak geliştirir ve sizin için uyumluluk sağlar. Microsoft, genel terminolojilerin hatalarını ve değişikliklerini belirler ve Kullanıcı akışınızda sorunsuz bir şekilde çalışacak güncelleştirmeler yapar.

### <a name="support-for-right-to-left-languages"></a>Sağdan sola diller için destek

Microsoft şu anda sağdan sola diller için destek sağlamaz. Bu bunu, özel yerel ayarları kullanarak ve dizelerin görüntülenme biçimini değiştirmek için CSS kullanarak gerçekleştirebilirsiniz. Bu özelliğe ihtiyacınız varsa lütfen [Azure geri bildirimde](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)bu uygulamayı oylayın.

### <a name="social-identity-provider-translations"></a>Sosyal kimlik sağlayıcısı çevirileri

Microsoft, `ui_locales` sosyal oturumlar için OIDC parametresini sağlar. Ancak Facebook ve Google dahil bazı sosyal kimlik sağlayıcıları bunları dikkate almaz.

### <a name="browser-behavior"></a>Tarayıcı davranışı

Chrome ve Firefox, kendi set dilleri için istek. Desteklenen bir dilise, varsayılan değer olarak gösterilir. Microsoft Edge Şu anda bir dil isteyemez ve doğrudan varsayılan dile gider.

## <a name="supported-languages"></a>Desteklenen diller

Azure AD aşağıdaki diller için destek içerir. Kullanıcı akış dilleri Azure AD tarafından sağlanır. Multi-Factor Authentication (MFA) bildirim dilleri [Azure AD MFA](../authentication/concept-mfa-howitworks.md)tarafından sağlanır.

| Dil              | Dil kodu | Kullanıcı akışları         | MFA bildirimleri  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arapça                | Ar            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Bulgarca             | bg            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Bangla                | milyar TL            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| Katalanca               | yetkilisini            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Çekçe                 | 'ye            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Danca                | kapattığımda            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Almanca                | seçimini            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Yunanca                 | seri            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| İngilizce               | en            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Spanish               | es            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Estonya Dili              | lale            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Baskça                | yapılan            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Fince               | Fi            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Fransızca                | kesir            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Galiçya Dili              | g            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Gucerat dili              | çubuğu            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| İbranice                | LIP            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Hintçe                 | n            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Hırvatça              | sa            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Macarca             | Hu            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Endonezce            | kimlik            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| İtalyanca               | içerdiği            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Japonca              | Sofya            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Kazakça                | kk            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Kannada dili               | KN            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| Korece                | dili            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Litvanca            | lt            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Letonca               | aramasını            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Malayalam dili             | ml            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| Marathi               | Mr            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| Malayca                 | SWM            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Norveççe Bokmal      | NB            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| Felemenkçe                 | nl            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Norveççe             | hayır            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Pencap dili               | VARS            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| Lehçe                | pl            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Portekizce - Brezilya   | pt-br         | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Portekizce - Portekiz | pt-pt         | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Rumence              | ro            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Rusça               | ru            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Slovakça                | sor            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Slovence             | SL            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Sırpça (Kiril)    | SR-cryıl-CS    | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Sırpça (Latin)       | sr-Latn-CS    | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| İsveççe               | v            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Tamil dili                 | dolu            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| Telugu dili                | ot            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![No belirten X](./media/user-flow-customize-language/no.png) |
| Tayca                  | 11            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Türkçe               | tr            | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Ukraynaca             | tr            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Vietnamca            | v            | ![No belirten X](./media/user-flow-customize-language/no.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Basitleştirilmiş Çince  | zh-Hans       | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |
| Geleneksel Çince | zh-Hant       | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) | ![Yeşil onay işareti.](./media/user-flow-customize-language/yes.png) |