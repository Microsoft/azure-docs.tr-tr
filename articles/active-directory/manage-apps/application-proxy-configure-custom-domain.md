---
title: Azure AD Uygulama Ara Sunucusu özel etki alanları | Microsoft Docs
description: Azure AD Uygulama Ara Sunucusu 'de özel etki alanlarını yapılandırın ve yönetin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 189b8666adde0eedcb451655657a4a82dc5e4fec
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062515"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu özel etki alanlarını yapılandırma

Azure Active Directory Uygulama Ara Sunucusu aracılığıyla bir uygulama yayımladığınızda kullanıcılarınız için bir dış URL oluşturursunuz. Bu URL varsayılan etki alanını alır *yourtenant.msappproxy.net*. Örneğin, kiracınızda *contoso* *adlı bir* uygulama yayımladığınızda dış URL *https: \//Expenses-contoso.msappproxy.net*. *Msappproxy.net*yerine kendi etki alanı adınızı kullanmak istiyorsanız, uygulamanız için özel bir etki alanı yapılandırabilirsiniz. 

## <a name="benefits-of-custom-domains"></a>Özel etki alanlarının avantajları

Mümkün olduğunda uygulamalarınız için özel etki alanları ayarlamanız iyi bir fikirdir. Özel etki alanlarını kullanmanın bazı nedenleri şunlardır:

- Uygulamalar arasındaki bağlantılar, şirket ağı dışında da çalışır. Özel etki alanı olmadan, uygulamanız uygulama proxy 'Si dışındaki hedeflere sabit kodlanmış iç bağlantılar içeriyorsa ve bağlantılar dışarıdan çözümlenememişse, bunlar kesilir. İç ve dış URL 'niz aynı olduğunda, bu sorundan kaçının. Özel etki alanlarını kullanmıyorsanız, bu sorunu gidermek için [Azure AD uygulama ara sunucusu ile yayımlanan uygulamalar için bkz. yeniden kodlanmış bağlantıları yeniden yönlendirme](../application-proxy-link-translation.md) . 
  
- Kullanıcılarınız, ağınızın içinden veya dışından aynı URL ile uygulamaya gidebildiğinden daha kolay bir deneyimle karşılaşacaktır. Farklı iç ve dış URL 'Ler öğrenmeleri veya geçerli konumlarını izlemesi gerekmez. 

- Markanızı denetleyebilir ve istediğiniz URL 'Leri oluşturabilirsiniz. Kullanıcılar, *msappproxy.net*yerine tanıdık bir ad görüp kullandığından, özel bir etki alanı kullanıcılarınızın güvenini oluşturmaya yardımcı olabilir.

- Bazı yapılandırmalara yalnızca özel etki alanları ile çalışacaksınız. Örneğin, Active Directory Federasyon Hizmetleri (AD FS) (AD FS) kullandığınızda, ancak WS-Federation ' i kullanırken Security Assertion Markup Language (SAML) kullanan uygulamalar için özel etki alanlarına ihtiyacınız vardır. Daha fazla bilgi için bkz. [uygulama proxy 'sinde talep kullanan uygulamalarla çalışma](application-proxy-configure-for-claims-aware-applications.md). 

İç ve dış URL 'Leri eşleşmeyebilirsiniz, özel etki alanları kullanmak önemli değildir, ancak yine de diğer avantajlardan faydalanabilirsiniz. 

## <a name="dns-configuration-options"></a>DNS yapılandırma seçenekleri

Gereksinimlerinize bağlı olarak DNS yapılandırmanızı ayarlamaya yönelik çeşitli seçenekler vardır:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Aynı dahili ve dış URL, farklı iç ve dış davranış 

İç kullanıcılarınızın uygulama proxy 'Si aracılığıyla yönlendirilmesini istemiyorsanız, *bölünmüş beyinli BIR DNS*ayarlayabilirsiniz. Bölünmüş bir DNS altyapısı, iç Konakları iç etki alanı ad sunucusuna ve dış ana bilgisayarları, ad çözümlemesi için bir dış etki alanı ad sunucusuna yönlendirir. 

![Bölünmüş beyinli DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Farklı iç ve dış URL 'Ler 

İç ve dış URL 'Ler farklıysa, Kullanıcı yönlendirmesi URL tarafından belirlendiği için, bölünmüş beyinli davranışı yapılandırmanız gerekmez. Bu durumda, yalnızca dış DNS 'i değiştirirsiniz ve dış URL 'YI uygulama proxy uç noktasına yönlendirin. 

Dış URL için özel bir etki alanı seçtiğinizde, bir bilgi çubuğu dış DNS sağlayıcısına eklemeniz gereken CNAME girişini gösterir. Uygulamanın **uygulama proxy 'si** sayfasına giderek bu bilgileri her zaman görebilirsiniz.

## <a name="set-up-and-use-custom-domains"></a>Özel etki alanlarını ayarlama ve kullanma

Şirket içi bir uygulamayı özel etki alanı kullanacak şekilde yapılandırmak için, bir özel etki alanı, özel etki alanı için bir PFX sertifikası ve yapılandırılacak şirket içi bir uygulama için onaylanmış bir Azure Active Directory gerekir. 

### <a name="create-and-verify-a-custom-domain"></a>Özel etki alanı oluşturma ve doğrulama

Özel etki alanı oluşturmak ve doğrulamak için:

1. Azure Active Directory, sol gezinti bölmesinde **özel etki alanı adları** ' nı seçin ve ardından **özel etki alanı Ekle**' yi seçin. 
1. Özel etki alanı adınızı girip **etki alanı Ekle**' yi seçin. 
1. Etki alanı sayfasında, etki alanınız için TXT kayıt bilgilerini kopyalayın. 
1. Etki alanı Kaydedicinizin üzerine gidip, kopyalanmış DNS bilgilerinizi temel alarak etki alanınız için yeni bir TXT kaydı oluşturun.
1. Etki alanını kaydettikten sonra, Azure Active Directory etki alanının sayfasında **Doğrula**' yı seçin. Etki alanı durumu **doğrulandıktan**sonra, uygulama proxy 'si de dahil olmak üzere tüm Azure AD yapılandırmalarında etki alanını kullanabilirsiniz. 

Daha ayrıntılı yönergeler için [Azure Active Directory portalını kullanarak özel etki alanı adınızı ekleme](../fundamentals/add-custom-domain.md)bölümüne bakın.

### <a name="configure-an-app-to-use-a-custom-domain"></a>Bir uygulamayı özel etki alanı kullanacak şekilde yapılandırma

Uygulamanızı özel bir etki alanı ile uygulama proxy 'Si aracılığıyla yayımlamak için:

1. Yeni bir uygulama için, Azure Active Directory, sol gezinti bölmesinde **Kurumsal uygulamalar** ' ı seçin. **Yeni uygulama**’yı seçin. Şirket **içi uygulamalar** bölümünde Şirket **içi uygulama ekle**' yi seçin. 
   
   Zaten **Kurumsal uygulamalarda**bulunan bir uygulama için listeden seçin ve sol gezinti bölmesinde **uygulama proxy 'si** ' ni seçin. 

2. Uygulama proxy 'Si ayarları sayfasında, kendi şirket içi uygulamanızı ekliyorsanız bir **ad** girin.

3.  **Iç URL** alanına UYGULAMANıZıN iç URL 'sini girin.
   
4. **Dış URL** alanında, listeyi aşağı açılır ve kullanmak istediğiniz özel etki alanını seçin.
   
5. **Add (Ekle)** seçeneğini belirleyin.
   
   ![Özel etki alanı seç](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Etki alanının zaten bir sertifikası varsa, **sertifika** alanı sertifika bilgilerini görüntüler. Aksi takdirde, **sertifika** alanını seçin. 
   
   ![Sertifikayı karşıya yüklemek için tıklayın](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. **SSL sertifikası** SAYFASıNDA, PFX Sertifika dosyanıza gidin ve seçin. Sertifika için parola girin ve **sertifikayı karşıya yükle**' yi seçin. Sertifikalar hakkında daha fazla bilgi için bkz. [özel etki alanları Için sertifikalar](#certificates-for-custom-domains) bölümü.
   
   ![Sertifikayı karşıya yükle](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Özel bir etki alanı yalnızca sertifikasını bir kez karşıya yüklenmesini gerektirir. Bundan sonra, diğer uygulamalar için özel etki alanını kullandığınızda karşıya yüklenen sertifika otomatik olarak uygulanır.
   
8. Bir sertifika eklediyseniz, **uygulama proxy 'si** sayfasında **Kaydet**' i seçin. 
   
9. **Uygulama proxy 'si** sayfasındaki bilgi çubuğunda, DNS bölgenize eklemenız gereken CNAME girişini aklınızda edin. 
   
   ![CNAME DNS girdisi Ekle](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Yeni dış URL 'YI *msappproxy.net* etki alanına YÖNLENDIREN bir DNS kaydı eklemek için [Azure Portal kullanarak DNS kayıtlarını ve kayıt kümelerini yönetme](../../dns/dns-operations-recordsets-portal.md) konusundaki yönergeleri izleyin.
   
11. DNS kaydının doğru şekilde yapılandırıldığından emin olmak için, dış URL 'nizin erişilebilir olduğunu ve *msapproxy.net* etki alanının bir diğer ad olarak göründüğünü onaylamak için [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) komutunu kullanın.

Uygulamanız artık özel etki alanını kullanacak şekilde ayarlanmıştır. Test etmeden veya bırakmadan önce kullanıcıları uygulamanıza atadığınızdan emin olun. 

Bir uygulamanın etki alanını değiştirmek için uygulamanın **uygulama proxy 'si** SAYFASıNDAKI **dış URL** 'deki açılan listeden farklı bir etki alanı seçin. Gerekirse güncelleştirilmiş etki alanı için bir sertifika yükleyin ve DNS kaydını güncelleştirin. **Dış URL**'deki açılan listede istediğiniz özel etki alanını görmüyorsanız, bu durum doğrulanmaz.

Uygulama proxy 'Si hakkında daha ayrıntılı yönergeler için bkz. [öğretici: Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Özel etki alanları için sertifikalar

Sertifika, özel etki alanınız için güvenli SSL bağlantısı oluşturur. 

### <a name="certificate-formats"></a>Sertifika biçimleri

Tüm gerekli ara sertifikaların dahil edildiğinden emin olmak için bir PFX sertifikası kullanmanız gerekir. Sertifika özel anahtarı içermelidir.

Sertifika imzası yöntemlerinde kısıtlama yoktur. Eliptik Eğri Şifreleme (ECC), konu diğer adı (SAN) ve diğer ortak sertifika türleri desteklenir. 

Joker karakter, dış URL ile eşleştiği sürece joker sertifikaları kullanabilirsiniz. [Joker uygulamalar](application-proxy-wildcard.md)için joker karakter sertifikaları kullanmanız gerekir. Sertifikayı da alt etki alanlarına erişmek üzere kullanmak istiyorsanız, alt etki alanı joker karakterlerini aynı sertifikaya konu alternatif adları olarak eklemeniz gerekir. Örneğin, konu diğer adı olarak *\*. Apps.Adventure-Works.com* eklemediğiniz sürece *\*. Adventure-Works.com* için bir sertifika *\*. Apps.Adventure-Works.com* için çalışmaz. 

Sertifika zinciri istemci cihazlarınızda yüklüyse, kendi ortak anahtar altyapınız (PKI) tarafından verilen sertifikaları kullanabilirsiniz. Intune, bu sertifikaları yönetilen cihazlara dağıtabilir. Yönetilmeyen cihazlar için, bu sertifikaları el ile yüklemelisiniz.

Özel bir kök CA kullanmak iyi bir fikir değildir. Özel kök CA 'nın Ayrıca birçok zorluk sunan istemci makinelere itilmesi gerekir. 

### <a name="certificate-management"></a>Sertifika yönetimi

Tüm sertifika yönetimi tek tek uygulama sayfalarıdır. **Sertifika** alanına erişmek Için uygulamanın **uygulama proxy 'si** sayfasına gidin.

Birden çok uygulama için aynı sertifikayı kullanabilirsiniz. Karşıya yüklenen bir sertifika başka bir uygulamayla çalışıyorsa, otomatik olarak uygulanır. Uygulamayı eklediğinizde veya yapılandırdığınızda yeniden yüklemeniz istenmez. 

Bir sertifikanın süresi dolmuşsa, başka bir sertifikayı karşıya yüklemeyi söyleyen bir uyarı alırsınız. Sertifika iptal edildiğinde, kullanıcılarınız uygulamaya erişirken bir güvenlik uyarısı görebilirler. Bir uygulama için sertifikayı güncelleştirmek için uygulama **ara sunucusu** sayfasına gidin, **sertifika**' ı seçin ve yeni bir sertifika yükleyin. Eski sertifika diğer uygulamalar tarafından kullanılmıyorsa, otomatik olarak silinir. 

## <a name="next-steps"></a>Sonraki adımlar
* Azure AD kimlik doğrulamasıyla yayımlanmış uygulamalarınızda [Çoklu oturum açmayı etkinleştirin](application-proxy-configure-single-sign-on-with-kcd.md) .
* Yayınlanan uygulamalarınıza [koşullu erişimi etkinleştirin](../conditional-access/technical-reference.md#cloud-apps-assignments) .

