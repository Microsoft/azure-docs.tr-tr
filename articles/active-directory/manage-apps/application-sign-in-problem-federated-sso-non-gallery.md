---
title: Galeri dışı Federasyon çoklu oturum açma uygulamasında oturum açma sorunları | Microsoft Docs
description: Azure AD ile SAML tabanlı federe çoklu oturum açma için yapılandırılmış bir uygulamada oturum açarken yüz yüze olabilecek belirli sorunlar için rehberlik
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: d26f52db394877cff13bf4b0b476a9603c1ddba8
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381385"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Federasyon çoklu oturum açma için yapılandırılmış Galeri olmayan bir uygulamada oturum açma sorunları

Aşağıdaki oturum açma sorunlarını gidermek için aşağıdaki öneriyi izleyerek daha iyi tanılama ve çözüm adımlarını otomatikleştirmeniz önerilir:

- Azure portal Test deneyimini kullanırken daha iyi tanılama ve çözümler sağlamak üzere Azure Active Directory (Azure AD) yardımcı olmak için [uygulamalarım güvenli tarayıcı uzantısını](access-panel-extension-problem-installing.md) yüklemek.
- Azure portal, uygulama yapılandırma sayfasında Test deneyimini kullanarak hatayı yeniden oluşturun. [SAML tabanlı çoklu oturum açma uygulamalarında hata ayıklama](../develop/howto-v1-debug-saml-sso-issues.md) hakkında daha fazla bilgi edinin

## <a name="application-not-found-in-directory"></a>Uygulama dizinde bulunamadı

*Hata AADSTS70001: Tanımlayıcılı `https://contoso.com` uygulama dizinde*bulunamadı.

**Olası nedeni**

Yayımlayan özniteliği, bir uygulamadan Azure AD 'ye Gönderen, Azure AD 'de yapılandırılan tanımlayıcı değeriyle eşleşmiyor.

**Çözümleme**

SAML isteğindeki özniteliğinin Azure AD 'de yapılandırılan tanımlayıcı değeri ile eşleştiğinden emin olun. `Issuer` Uygulamalarım güvenli tarayıcı uzantısı ile Azure portal [Test deneyimini](../develop/howto-v1-debug-saml-sso-issues.md) kullanıyorsanız, bu adımları el ile izlemeniz gerekmez.

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. Uygulama yüklendikten sonra **Temel SAML yapılandırması**'nı açın. Tanımlayıcı metin kutusundaki değerin hatada görüntülenen tanımlayıcı değeri için olan değerle eşleştiğini doğrulayın.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Yanıt adresi, uygulama için yapılandırılan yanıt adresleriyle eşleşmiyor. 

*Hata AADSTS50011: Yanıt adresi `https://contoso.com` , uygulama için yapılandırılan yanıt adresleriyle eşleşmiyor* 

**Olası nedeni** 

SAML isteğindeki AssertionConsumerServiceURL değeri, Azure AD 'de yapılandırılan yanıt URL 'Si değeri veya düzeniyle eşleşmiyor. SAML isteğindeki AssertionConsumerServiceURL değeri, hatada gördüğünüz URL 'dir. 

**Çözümleme** 

SAML isteğindeki özniteliğinin Azure AD 'de yapılandırılan tanımlayıcı değeri ile eşleştiğinden emin olun. `Issuer` Uygulamalarım güvenli tarayıcı uzantısı ile Azure portal [Test deneyimini](../develop/howto-v1-debug-saml-sso-issues.md) kullanıyorsanız, bu adımları el ile izlemeniz gerekmez.
 
1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın. 

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın. 

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi. 

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın. 

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için. 

   * Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar** olarak ayarlayın.
  
6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. Uygulama yüklendikten sonra **Temel SAML yapılandırması**'nı açın. Yanıt URL 'si metin kutusundaki değeri SAML isteğindeki `AssertionConsumerServiceURL` değerle eşleşecek şekilde doğrulayın veya güncelleştirin.    
    
Azure AD 'de yanıt URL 'SI değerini güncelleştirdikten ve SAML isteğindeki uygulama tarafından gönderilen değeri eşleştirdikten sonra, uygulamada oturum açabiliyor olmanız gerekir.

## <a name="user-not-assigned-a-role"></a>Kullanıcıya bir rol atanmamış

*Hata AADSTS50105: Oturum açmış olan Kullanıcı `brian\@contoso.com` , uygulama için bir role atanmamış*

**Olası nedeni**

Kullanıcıya Azure AD 'de uygulamaya erişim izni verilmedi.

**Çözümleme**

Bir uygulamaya doğrudan bir veya daha fazla kullanıcı atamak için aşağıdaki adımları izleyin. Uygulamalarım güvenli tarayıcı uzantısı ile Azure portal [Test deneyimini](../develop/howto-v1-debug-saml-sso-issues.md) kullanıyorsanız, bu adımları el ile izlemeniz gerekmez.

1. Açık [ **Azure portalında** ](https://portal.azure.com/) ve oturum açma bir **genel yönetici.**

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Listeden bir kullanıcıya atamak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Kullanıcılar ve gruplar** ' a tıklayın.

8. Tıklayın **Ekle** üstünde düğme **kullanıcılar ve gruplar** listesini açmak için **atama Ekle** bölmesi.

9. tıklayın **kullanıcılar ve gruplar** seçiciden **atama Ekle** bölmesi.

10. Yazın **tam adı** veya **e-posta adresi** içine atama isteyen kullanıcının **adına veya e-posta adresine göre arama** arama kutusu.

11. Üzerine **kullanıcı** göstermek için listedeki bir **onay kutusu**. Kullanıcının profil fotoğrafı veya kullanıcı için eklenecek logosu yanındaki onay kutusuna tıklayın **seçili** listesi.

12. **Seçim** Birden **fazla kullanıcı eklemek**istiyorsanız **ada veya e-posta adresine göre ara** kutusuna başka bir **tam ad** veya **e-posta adresi** yazın ve bu kullanıcıyı **Seçili** listeye eklemek için onay kutusuna tıklayın.

13. Kullanıcı seçme işlemini tamamladığınızda, tıklayın **seçin** uygulamaya atanan kullanıcıların ve grupların listesi eklemek için düğme.

14. **İsteğe bağlı:** tıklayın **rolü Seç** seçicide **atama Ekle** bölmesinde seçtiğiniz kullanıcılara atamak için bir rol seçin.

15. Tıklayın **atama** düğmesi Seçilen kullanıcılara uygulamayı atamak için.

Bir kısa süre sonra seçtiğiniz kullanıcıların çözüm Açıklama bölümünde açıklanan yöntemleri kullanarak bu uygulamaları başlatması mümkün.

## <a name="not-a-valid-saml-request"></a>Geçerli bir SAML Isteği değil

*Hata AADSTS75005: İstek geçerli bir SAML2 Protocol iletisi değil.*

**Olası nedeni**

Azure AD, uygulama tarafından Çoklu oturum açma için gönderilen SAML İsteğini desteklemiyor. Bazı yaygın sorunlar şunlardır:

-   SAML isteğinde gerekli alanlar eksik

-   SAML isteği kodlama yöntemi

**Çözümleme**

1.  SAML isteğini yakala. SAML isteğini nasıl yakalayacağınızı öğrenmek için [Azure AD 'de uygulamalar IÇIN SAML tabanlı çoklu oturum açma hatalarını ayıklama](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) öğreticisini izleyin.

2.  Uygulama satıcısına ve paylaşımıyla iletişim kurun:

    -   SAML isteği

    -   [Azure AD çoklu oturum açma SAML protokolü gereksinimleri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Uygulama satıcısı, çoklu oturum açma için Azure AD SAML uygulamasını destekledikleri doğrulaması gerekir.

## <a name="misconfigured-application"></a>Yanlış yapılandırılmış uygulama

*Hata AADSTS650056: Yanlış yapılandırılmış uygulama. Bunun nedeni, aşağıdakilerden biri olabilir: İstemci, istemcinin uygulama kaydında istenen izinlerde ' AAD Graph ' için herhangi bir izin listemedi. Ya da yönetici kiracıya onaylamadı. Ya da, yapılandırılan istemci uygulama tanımlayıcısıyla eşleştiğinden emin olmak için istekteki uygulama tanımlayıcısını kontrol edin. Kiracı adına yapılandırmayı veya onayı onarmak için lütfen yöneticinize başvurun.* .

**Olası nedeni**

Uygulamanın SAML isteğindeki Azure AD 'ye gönderdiği öznitelik,AzureAD'deuygulamaiçinyapılandırılantanımlayıcıdeğeriyleeşleşmiyor.`Issuer`

**Çözümleme**

SAML isteğindeki özniteliğinin Azure AD 'de yapılandırılan tanımlayıcı değeri ile eşleştiğinden emin olun. `Issuer` Uygulamalarım güvenli tarayıcı uzantısı ile Azure portal [Test deneyimini](../develop/howto-v1-debug-saml-sso-issues.md) kullanıyorsanız, aşağıdaki adımları el ile izlemeniz gerekmez:

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici**olarak oturum açın.

1.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

1.  Filtre arama kutusuna **"Azure Active Directory"** yazın ve **Azure Active Directory** öğesini seçin.

1.  Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.

1.  Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

    Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar**olarak ayarlayın.

1.  Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

1.  Uygulama yüklendikten sonra **Temel SAML yapılandırması**'nı açın. Tanımlayıcı metin kutusundaki değerin hatada görüntülenen tanımlayıcı değeri için olan değerle eşleştiğini doğrulayın.

## <a name="certificate-or-key-not-configured"></a>Sertifika veya anahtar yapılandırılmadı

Hata AADSTS50003: İmza anahtarı yapılandırılmadı.

**Olası nedeni**

Uygulama nesnesi bozuk ve Azure AD uygulama için yapılandırılan sertifikayı tanımıyor.

**Çözümleme**

Yeni bir sertifika silmek ve oluşturmak için aşağıdaki adımları izleyin:

1. [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici** olarak oturum açın.

2. Sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i tıklatarak **Azure Active Directory uzantısını** açın.

3. Yazın **"Azure Active Directory**" filtre arama kutusunu seçip **Azure Active Directory** öğesi.

4. Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' a tıklayın.

5. tıklayın **tüm uygulamaları** tüm uygulamaların bir listesini görüntülemek için.

   * Burada show istediğiniz uygulamayı göremiyorsanız kullanın **filtre** üst kısmındaki denetim **tüm uygulamalar listesini** ayarlayıp **Göster** seçeneğini **tüm Uygulamalar.**

6. Çoklu oturum açmayı yapılandırmak istediğiniz uygulamayı seçin.

7. Uygulama yüklendikten sonra, uygulamanın sol taraftaki gezinti menüsünden **Çoklu oturum açma** seçeneğine tıklayın.

8. **SAML Imzalama sertifikası** bölümünde **Yeni sertifika oluştur** ' a tıklayın.

9. Sona erme tarihi seçin. Ardından Kaydet ' e tıklayın **.**

10. Etkin sertifikayı geçersiz kılmak için **Yeni sertifika etkin hale getirin** ' i işaretleyin. Ardından bölmenin üstündeki **Kaydet**’e tıklayın ve geçiş sertifikasını etkinleştirmeyi kabul edin.

11. **Kullanılmayan** sertifikayı kaldırmak Için **SAML imzalama sertifikası** bölümünde **Kaldır** ' ı tıklatın.

## <a name="saml-request-not-present-in-the-request"></a>İstekte SAML Isteği yok

*Hata AADSTS750054: SAML yeniden yönlendirme bağlaması için HTTP isteğinde bir sorgu dizesi parametreleri olarak SAMLRequest veya SAMLResponse mevcut olmalıdır.*

**Olası nedeni**

Azure AD, HTTP isteğindeki URL parametreleri içindeki SAML isteğini tanımlayamadı. Bu durum, uygulamanın Azure AD 'ye SAML isteği gönderilirken HTTP yeniden yönlendirme bağlamasını kullanmadığı durumlarda meydana gelebilir.

**Çözümleme**

Uygulamanın, HTTP yeniden yönlendirme bağlamasını kullanarak konum başlığına kodlanmış SAML isteğini gönderebilmesi gerekir. Bunun nasıl gerçekleştirileceği hakkında daha fazla bilgi için [SAML protokolü belirtimi belgesinde](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf) HTTP Yeniden Yönlendirme Bağlaması bölümünü okuyun.

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD, belirteci yanlış bir uç noktaya gönderiyor

**Olası nedeni**

Çoklu oturum açma sırasında, oturum açma isteği açık bir yanıt URL 'SI içermiyorsa (onaylama tüketici hizmeti URL 'SI), Azure AD bu uygulama için yapılandırılmış tüm özel URL 'Lerden birini seçer. Uygulamanın açık bir yanıt URL 'SI yapılandırılmış olsa bile, Kullanıcı yeniden yönlendirilebilir https://127.0.0.1:444. 

Uygulama galeriden olmayan bir uygulama olarak eklendiğinde, Azure Active Directory bu yanıt URL'sini bir varsayılan değer olarak oluşturuldu. Bu davranış değiştirildi ve Azure Active Directory artık varsayılan olarak bu URL'yi eklemez. 

**Çözümleme**

Uygulama için yapılandırılmış kullanılmayan yanıt URL 'Lerini silin.

1.  [**Azure Portal**](https://portal.azure.com/) açın ve **genel yönetici** veya **ortak yönetici**olarak oturum açın.

2.  Ana sol taraftaki Gezinti menüsünün en üstündeki **tüm hizmetler** ' i seçerek **Azure Active Directory uzantısını** açın.

3.  Filtre arama kutusuna **"Azure Active Directory"** yazın ve **Azure Active Directory** öğesini seçin.

4.  Azure Active Directory sol taraftaki gezinti menüsünden **Kurumsal uygulamalar** ' ı seçin.

5.  Tüm uygulamalarınızın listesini görüntülemek için **tüm uygulamalar** ' ı seçin.

    Burada görünmesini istediğiniz uygulamayı görmüyorsanız, **tüm uygulamalar listesinin** en üstündeki **filtre** denetimini kullanın ve **göster** seçeneğini **tüm uygulamalar**olarak ayarlayın.

6.  Çoklu oturum açma için yapılandırmak istediğiniz uygulamayı seçin.

7.  Uygulama yüklendikten sonra **Temel SAML yapılandırması**'nı açın. **Yanıt URL 'si (onaylama tüketici hizmeti URL 'si)** içinde, sistem tarafından oluşturulan kullanılmayan veya varsayılan yanıt URL 'lerini silin. Örneğin: `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Bir uygulamaya gönderilen SAML taleplerini özelleştirirken sorun oluştu

Uygulamanıza gönderilen SAML öznitelik taleplerini özelleştirmeyi öğrenmek için, daha fazla bilgi için [Azure Active Directory 'Da talep eşleme](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) konusuna bakın.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD çoklu oturum açma SAML protokolü gereksinimleri](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
