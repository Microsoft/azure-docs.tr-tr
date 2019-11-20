---
title: Azure AD 'de kurumsal uygulamalar için SAML belirteci taleplerini özelleştirme
titleSuffix: Microsoft identity platform
description: Azure AD 'de kurumsal uygulamalar için SAML belirtecinde verilen talepleri özelleştirmeyi öğrenin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1490a25e69ff22fde1f5c870868f20ea6f9a1cf7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74046979"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Nasıl yapılır: kurumsal uygulamalar için SAML belirtecinde verilen talepleri özelleştirme

Günümüzde Azure Active Directory (Azure AD), Azure AD uygulama galerisinde ve özel uygulamalarda önceden tümleştirilmiş uygulamalar da dahil olmak üzere çoğu kurumsal uygulamayla çoklu oturum açmayı (SSO) destekler. Bir Kullanıcı, SAML 2,0 protokolünü kullanarak Azure AD aracılığıyla bir uygulamanın kimliğini doğruladığında, Azure AD uygulamaya bir belirteç gönderir (bir HTTP POST aracılığıyla). Sonra uygulama, Kullanıcı adı ve parola istemek yerine, kullanıcının oturum açmasını sağlamak için belirtecini doğrular ve kullanır. Bu SAML belirteçleri, *talep*olarak bilinen kullanıcı hakkında bilgi parçalarını içerir.

Bir *talep* , bir kimlik sağlayıcısının bu kullanıcı için çalıştıkları belirtecin içindeki bir kullanıcı hakkında bilgi veren bir sorundur. [SAML belirtecinde](https://en.wikipedia.org/wiki/SAML_2.0), bu VERILER genellikle SAML Attribute ifadesinde bulunur. Kullanıcının benzersiz KIMLIĞI, genellikle ad tanımlayıcısı olarak da bilinen SAML konusu içinde temsil edilir.

Azure AD, varsayılan olarak, Azure AD 'de kullanıcının Kullanıcı adı (Kullanıcı asıl adı olarak da bilinir) değerine sahip `NameIdentifier` bir talep içeren bir SAML belirteci verir. Bu, kullanıcıyı benzersiz şekilde tanımlayabilirler. SAML belirteci ayrıca kullanıcının e-posta adresini, adını ve soyadını içeren ek talepler içerir.

SAML belirtecinde verilen talepleri uygulamaya görüntülemek veya düzenlemek için Azure portal içinde uygulamayı açın. Ardından **Kullanıcı öznitelikleri & talepler** bölümünü açın.

![Azure portal Kullanıcı öznitelikleri & talepler bölümünü açın](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

SAML belirtecinde verilen talepleri düzenlemeniz gerekebilecek iki nedeni vardır:

* Uygulama `NameIdentifier` veya NameID talebinin Azure AD 'de depolanan Kullanıcı adı (veya Kullanıcı asıl adı) dışında bir şey olmasını gerektirir.
* Uygulama, farklı bir talep URI 'si veya talep değerleri kümesi gerektirecek şekilde yazılmıştır.

## <a name="editing-nameid"></a>Ad kimliğini Düzenle

NameID (ad tanımlayıcı değeri) düzenlemek için:

1. **Ad tanımlayıcı değeri** sayfasını açın.
1. Özniteliğe uygulamak istediğiniz özniteliği veya dönüşümü seçin. İsteğe bağlı olarak, NameID talebinin olmasını istediğiniz biçimi belirtebilirsiniz.

   ![NameID (ad tanımlayıcı) değerini Düzenle](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID biçimi

SAML isteği belirli bir biçime sahip Nameıdpolicy öğesini içeriyorsa, Azure AD istekteki biçimi kabul eder.

SAML isteği Nameıdpolicy için bir öğe içermiyorsa Azure AD, belirttiğiniz biçimle birlikte NameID olarak verilecek. Hiçbir biçim belirtilmemişse Azure AD, seçilen talep kaynağıyla ilişkili varsayılan kaynak biçimini kullanır.

**Ad tanımlayıcı biçimi Seç** açılan menüsünde, aşağıdaki seçeneklerden birini seçebilirsiniz.

| NameID biçimi | Açıklama |
|---------------|-------------|
| **Varsayılan** | Azure AD varsayılan kaynak biçimini kullanır. |
| **RESERVATION** | Azure AD, NameID biçimi olarak kalıcı kullanacaktır. |
| **EmailAddress** | Azure AD, NameID biçimi olarak Emapostaadı kullanacaktır. |
| **Memesi** | Azure AD, NameID biçimi olarak belirtilmemiş kullanır. |
| **Windows etki alanı nitelikli adı** | Azure AD, NameID biçimi olarak WindowsDomainQualifiedName kullanır. |

Geçici NameID de desteklenir, ancak açılan listede kullanılamaz ve Azure tarafında yapılandırılamaz. Nameıdpolicy özniteliği hakkında daha fazla bilgi edinmek için bkz. [Çoklu oturum açma SAML Protokolü](single-sign-on-saml-protocol.md).

### <a name="attributes"></a>Öznitelikler

`NameIdentifier` (veya NameID) talebi için istenen kaynağı seçin. Aşağıdaki seçeneklerden seçim yapabilirsiniz.

| Ad | Açıklama |
|------|-------------|
| Email | Kullanıcının e-posta adresi |
| userprincipalName | Kullanıcının Kullanıcı asıl adı (UPN) |
| onpremisessamaccount | Şirket içi Azure AD 'den eşitlenmiş SAM hesap adı |
| uzantının | Azure AD 'de kullanıcının ObjectID |
| EmployeeID | Kullanıcının çalışan KIMLIĞI |
| Dizin genişletmeleri | [Azure AD Connect eşitleme kullanılarak şirket içi Active Directory eşitlenen](../hybrid/how-to-connect-sync-feature-directory-extensions.md) Dizin uzantıları |
| Uzantı öznitelikleri 1-15 | Azure AD şemasını genişletmek için kullanılan şirket içi uzantı öznitelikleri |

Daha fazla bilgi için bkz. [Tablo 3: kaynak başına GEÇERLI kimlik değerleri](active-directory-claims-mapping.md#table-3-valid-id-values-per-source).

Ayrıca, Azure AD 'de tanımladığınız talepler için herhangi bir sabit (statik) değer atayabilirsiniz. Sabit değer atamak için lütfen aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com/), **Kullanıcı öznitelikleri & talepler** bölümünde, talepleri düzenlemek için **Düzenle** simgesine tıklayın.

1. Değiştirmek istediğiniz gerekli talebe tıklayın.

1. **Kaynak özniteliğinde** kuruluşunuza göre sabit değeri tırnak işareti olmadan girin ve **Kaydet**' e tıklayın.

    ![Azure portal Kullanıcı öznitelikleri & talepler bölümünü açın](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. Sabit değer aşağıda gösterildiği gibi görüntülenecektir.

    ![Azure portal Kullanıcı öznitelikleri & talepler bölümünü açın](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>Özel talepler-dönüşümler

Talep dönüştürmeleri işlevlerini de kullanabilirsiniz.

| İşlev | Açıklama |
|----------|-------------|
| **ExtractMailPrefix()** | Etki alanı sonekini e-posta adresinden veya Kullanıcı asıl adından kaldırır. Bu, yalnızca Kullanıcı adının geçirildiği ilk kısmını ayıklar (örneğin, joe_smith@contoso.comyerine "joe_smith"). |
| **JOIN ()** | Doğrulanmış bir etki alanıyla bir özniteliği birleştirir. Seçilen Kullanıcı tanımlayıcı değeri bir etki alanına sahipse, seçilen doğrulanmış etki alanını eklemek için Kullanıcı adını ayıklar. Örneğin, Kullanıcı tanımlayıcı değeri olarak e-postayı (joe_smith@contoso.com) seçip doğrulanmış etki alanı olarak contoso.onmicrosoft.com ' i seçerseniz, bu, joe_smith@contoso.onmicrosoft.comsonuçlanır. |
| **ToLower ()** | Seçili özniteliğin karakterlerini küçük harfli karakterlere dönüştürür. |
| **ToUpper ()** | Seçili özniteliğin karakterlerini büyük harfli karakterlere dönüştürür. |

## <a name="adding-application-specific-claims"></a>Uygulamaya özgü talepler ekleme

Uygulamaya özel talepler eklemek için:

1. **Kullanıcı öznitelikleri & talepler**' de, **Kullanıcı taleplerini Yönet** sayfasını açmak için **yeni talep Ekle** ' yi seçin.
1. Taleplerin **adını** girin. Değer, SAML spec başına, bir URI deseninin tamamen izlenmesi gerekmez. Bir URI deseninin olması gerekiyorsa, bunu **ad alanı** alanına koyabilirsiniz.
1. Talebin değerini almak için gereken **kaynağı** seçin. Kaynak özniteliği açılan listesinden bir kullanıcı özniteliği seçebilir veya bir talep olarak yaymadan önce Kullanıcı özniteliğine bir dönüşüm uygulayabilirsiniz.

### <a name="claim-transformations"></a>Talep dönüştürmeleri

Bir Kullanıcı özniteliğine dönüşüm uygulamak için:

1. **Talebi Yönet**bölümünde, isteği kaynak olarak *dönüştürme* ' yi seçerek **Dönüştürmeyi Yönet** sayfasını açın.
2. Dönüştürme açılan listesinden işlevi seçin. Seçili işleve bağlı olarak, dönüşümde değerlendirmek için parametreler ve sabit bir değer sağlamanız gerekir. Kullanılabilir işlevler hakkında daha fazla bilgi için aşağıdaki tabloya bakın.
3. Birden çok dönüşüm uygulamak için **dönüştürme Ekle**' ye tıklayın. Bir talebe en fazla iki dönüşüm uygulayabilirsiniz. Örneğin, önce `user.mail`e-posta önekini ayıklayabilirsiniz. Ardından, dizeyi büyük harfe getirin.

   ![NameID (ad tanımlayıcı) değerini Düzenle](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

Talepleri dönüştürmek için aşağıdaki işlevleri kullanabilirsiniz.

| İşlev | Açıklama |
|----------|-------------|
| **ExtractMailPrefix()** | Etki alanı sonekini e-posta adresinden veya Kullanıcı asıl adından kaldırır. Bu, yalnızca Kullanıcı adının geçirildiği ilk kısmını ayıklar (örneğin, joe_smith@contoso.comyerine "joe_smith"). |
| **JOIN ()** | İki özniteliği birleştirerek yeni bir değer oluşturur. İsteğe bağlı olarak, iki öznitelik arasında bir ayırıcı kullanabilirsiniz. NameID talep dönüştürmesi için, JOIN doğrulanmış bir etki alanıyla kısıtlıdır. Seçilen Kullanıcı tanımlayıcı değeri bir etki alanına sahipse, seçilen doğrulanmış etki alanını eklemek için Kullanıcı adını ayıklar. Örneğin, Kullanıcı tanımlayıcı değeri olarak e-postayı (joe_smith@contoso.com) seçip doğrulanmış etki alanı olarak contoso.onmicrosoft.com ' i seçerseniz, bu, joe_smith@contoso.onmicrosoft.comsonuçlanır. |
| **ToLower ()** | Seçili özniteliğin karakterlerini küçük harfli karakterlere dönüştürür. |
| **ToUpper ()** | Seçili özniteliğin karakterlerini büyük harfli karakterlere dönüştürür. |
| **Contains ()** | Giriş belirtilen değerle eşleşiyorsa bir öznitelik veya sabit verir. Aksi takdirde, eşleşme yoksa başka bir çıktı belirleyebilirsiniz.<br/>Örneğin, "@contoso.com" etki alanını içeriyorsa değerin kullanıcının e-posta adresi olduğu bir talep oluşturmak istiyorsanız, aksi takdirde Kullanıcı asıl adını çıkarmak isteyebilirsiniz. Bunu yapmak için, aşağıdaki değerleri yapılandırırsınız:<br/>*Parametre 1 (giriş)* : User. email<br/>*Değer*: "@contoso.com"<br/>Parametre 2 (çıkış): User. email<br/>Parametre 3 (eşleşme yoksa çıkış): User. UserPrincipalName |
| **EndWith ()** | Giriş belirtilen değerle sona ererse bir öznitelik veya sabit verir. Aksi takdirde, eşleşme yoksa başka bir çıktı belirleyebilirsiniz.<br/>Örneğin, çalışan KIMLIĞI "000" ile bitiyorsa değerin kullanıcının çalışan KIMLIĞI olduğu bir talep oluşturmak istiyorsanız, aksi takdirde bir uzantı özniteliği çıktısını almak istersiniz. Bunu yapmak için, aşağıdaki değerleri yapılandırırsınız:<br/>*Parametre 1 (giriş)* : User. EmployeeID<br/>*Değer*: "000"<br/>Parametre 2 (çıkış): User. EmployeeID<br/>Parametre 3 (eşleşme yoksa çıkış): User. extensionAttribute1 |
| **StartWith ()** | Giriş belirtilen değerle başlıyorsa bir öznitelik veya sabit verir. Aksi takdirde, eşleşme yoksa başka bir çıktı belirleyebilirsiniz.<br/>Örneğin, ülke/bölge "ABD" ile başlıyorsa değerin kullanıcının çalışan KIMLIĞI olduğu bir talep oluşturmak istiyorsanız, aksi takdirde bir uzantı özniteliği çıktısını almak isteyebilirsiniz. Bunu yapmak için, aşağıdaki değerleri yapılandırırsınız:<br/>*Parametre 1 (giriş)* : User. Country<br/>*Değer*: "US"<br/>Parametre 2 (çıkış): User. EmployeeID<br/>Parametre 3 (eşleşme yoksa çıkış): User. extensionAttribute1 |
| **Ayıkla ()-sonrasında eşleme** | Belirtilen değerle eşleştirdikten sonra alt dizeyi döndürür.<br/>Örneğin, girişin değeri "Finance_BSimon" ise, eşleşen değer "Finance_" ise, talebin çıktısı "Bsıon" olur. |
| **Extract ()-öncesinde eşleme** | Belirtilen değerle eşleşene kadar alt dizeyi döndürür.<br/>Örneğin, girişin değeri "BSimon_US" ise, eşleşen değer "_US" ise, talebin çıktısı "Bsıon" olur. |
| **Ayıkla ()-eşleşen** | Belirtilen değerle eşleşene kadar alt dizeyi döndürür.<br/>Örneğin, girişin değeri "Finance_BSimon_US" ise, ilk eşleşen değer "Finance_", ikinci eşleşen değer "_US" ise, talebin çıktısı "Bsıon" olur. |
| **ExtractAlpha ()-önek** | Dizenin ön ek alfabetik bölümünü döndürür.<br/>Örneğin, girişin değeri "BSimon_123" ise, "Bsıon" döndürür. |
| **ExtractAlpha ()-sonek** | Dizenin son ek alfabetik bölümünü döndürür.<br/>Örneğin, girişin değeri "123_Simon" ise, "Simon" döndürür. |
| **ExtractNumeric ()-ön ek** | Dizenin ön ek sayısal parçasını döndürür.<br/>Örneğin, girişin değeri "123_BSimon" ise, "123" döndürür. |
| **ExtractNumeric ()-sonek** | Dizenin son ek sayısal parçasını döndürür.<br/>Örneğin, girişin değeri "BSimon_123" ise, "123" döndürür. |
| **IfEmpty ()** | Giriş null veya boşsa bir öznitelik veya sabit verir.<br/>Örneğin, belirli bir kullanıcının çalışan KIMLIĞI boşsa, bir ExtensionAttribute içinde depolanan bir özniteliğin çıktısını almak istiyorsanız. Bunu yapmak için, aşağıdaki değerleri yapılandırırsınız:<br/>Parametre 1 (giriş): User. EmployeeID<br/>Parametre 2 (çıkış): User. extensionAttribute1<br/>Parametre 3 (eşleşme yoksa çıkış): User. EmployeeID |
| **IfNotEmpty ()** | Giriş null veya boş değilse bir öznitelik veya sabit verir.<br/>Örneğin, belirli bir kullanıcının çalışan KIMLIĞI boş değilse, bir ExtensionAttribute içinde depolanan bir özniteliğin çıktısını almak istiyorsanız. Bunu yapmak için, aşağıdaki değerleri yapılandırırsınız:<br/>Parametre 1 (giriş): User. EmployeeID<br/>Parametre 2 (çıkış): User. extensionAttribute1 |

Ek Dönüştürmelere ihtiyacınız varsa, *SaaS uygulaması* kategorisi altında [Azure AD 'deki geri bildirim forumuna](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) fikir gönderin.

## <a name="emitting-claims-based-on-conditions"></a>Koşullara göre talepleri yayma

Kullanıcı türüne ve kullanıcının ait olduğu gruba göre bir talebin kaynağını belirtebilirsiniz. 

Kullanıcı türü şu olabilir:
- **Any**: tüm kullanıcıların uygulamaya erişmesine izin verilir.
- **Üyeler**: kiracının yerel üyesi
- **Tüm konuklar**: Kullanıcı, Azure AD ile veya olmayan bir dış kuruluştan alınır.
- **AAD konukları**: Konuk Kullanıcı Azure ad kullanan başka bir kuruluşa aittir.
- **Dış konuklar**: Konuk Kullanıcı Azure AD olmayan bir dış kuruluşa aittir.


Bunun yararlı olduğu bir senaryo, bir talebin kaynağı bir konuk ve bir uygulamaya erişen çalışan için farklılık gösteren bir senaryodur. Kullanıcı bir çalışan ise, NameID 'nin Kullanıcı. email kaynağı olduğunu belirtmek isteyebilirsiniz, ancak kullanıcı bir konuysanız, NameID User. extensionAttribute1 'tan kaynaklıdır.

Talep koşulu eklemek için:

1. **Talep yönetme**' de talep koşullarını genişletin.
2. Kullanıcı türünü seçin.
3. Kullanıcının ait olacağı grupları seçin. Belirli bir uygulama için tüm talepler genelinde en fazla 10 benzersiz grup seçebilirsiniz. 
4. Talebin değerini almak için gereken **kaynağı** seçin. Kaynak özniteliği açılan listesinden bir kullanıcı özniteliği seçebilir veya bir talep olarak yaymadan önce Kullanıcı özniteliğine bir dönüşüm uygulayabilirsiniz.

Koşulları eklediğiniz sıra önemlidir. Azure AD, talebe göre hangi değerin yayacağına karar vermek için koşulları yukarıdan aşağıya değerlendirir. 

Örneğin, Brita Simon, contoso kiracısındaki bir Konuk Kullanıcı. Aynı zamanda Azure AD kullanan başka bir kuruluşa aittir. Fabrikam uygulaması için aşağıdaki yapılandırma verildiğinde, Brita Fabrikam ' ta oturum açmaya çalıştığında Azure AD, koşulları takip edecek şekilde değerlendirir.

İlk olarak, Azure AD, Brita 'ın Kullanıcı türünün `All guests`olup olmadığını doğrular. Bu, doğru olduğundan Azure AD, `user.extensionattribute1`talebine yönelik kaynağı atar. İkinci olarak, Azure AD, Brita 'ın Kullanıcı türünün `AAD guests`olup olmadığını doğrular, bu da doğru olduğundan Azure AD, talebin kaynağını `user.mail`atar. Son olarak, talep, Brita için `user.email` değer ile yayılır.

![Talep koşullu yapılandırması](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD 'de uygulama yönetimi](../manage-apps/what-is-application-management.md)
* [Azure AD uygulama galerisinde olmayan uygulamalarda çoklu oturum açmayı yapılandırma](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML tabanlı çoklu oturum açma sorunlarını giderme](howto-v1-debug-saml-sso-issues.md)
