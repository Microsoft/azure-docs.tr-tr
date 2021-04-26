---
title: Ping kimliğiyle Azure Active Directory B2C yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Azure AD B2C kimlik doğrulamasını ping kimliğiyle tümleştirme hakkında bilgi edinin
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 066fd6f91b19da211a73ac12fb6dca94085399ac
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256642"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Öğretici: güvenli karma erişim için Azure Active Directory B2C ile ping kimliğini yapılandırma

Bu örnek öğreticide, güvenli karma erişimi etkinleştirmek için Azure Active Directory (AD) B2C 'yi  [Pingaccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) ve [pingfederate](https://www.pingidentity.com/en/software/pingfederate.html) ile genişletmeyi öğrenin.

Internet 'e açık olan eCommerce siteleri ve Web uygulamaları gibi mevcut birçok web özelliği, bazı durumlarda ters proxy sistemi olarak adlandırılan bir proxy sisteminin arkasında dağıtılır. Bu proxy sistemleri, ön kimlik doğrulama, ilke zorlaması ve trafik yönlendirme gibi çeşitli işlevler sağlar. Örnek kullanım örnekleri, Web uygulamasının gelen Web trafiğinden korunmasını ve dağıtılmış sunucu dağıtımları arasında Tekdüzen oturum yönetimi sağlamayı içerir.

Çoğu durumda bu yapılandırma, Web uygulamasından kimlik doğrulaması yapan bir kimlik doğrulama çevirisi katmanını içerir. Sırasıyla ters proxy 'ler, Web uygulamalarına kimliği doğrulanmış kullanıcıların bağlamını, şifresiz veya Özet biçiminde üstbilgi değeri gibi daha basit bir biçimde sağlar. Bu tür bir yapılandırmada, uygulamalar, kimlik doğrulama bağlamını sağlamak ve oturumu koruma ya da yerel uygulama gibi Son Kullanıcı aracısından korumak için proxy 'ye bağlı olarak, Security Assertion Markup Language (SAML), OAuth veya Open ID Connect (OıDC) gibi endüstri standardı belirteçleri kullanmıyor. "Ortadaki adam" içinde çalışan bir hizmet olarak, proxy 'ler en son oturum denetimini sağlayabilir. Bu ayrıca Proxy hizmetinin yüksek düzeyde etkili ve ölçeklenebilir olması, ara sunucu hizmetinin arkasındaki uygulamalar için bir performans sorunu veya tek bir başarısızlık noktası olmaması anlamına gelir. Diyagram, tipik bir ters ara sunucu uygulamasının ve iletişimin akışının bir gösterimi.

![görüntü, ters proxy uygulamasını gösterir](./media/partner-ping/reverse-proxy.png)

Bu tür yapılandırmalarda kimlik platformunu modernleştirin istediğiniz durumlarda, kaygıları aşağıda verilmiştir.

- Uygulama modernleştirme çabası kimlik platformu modernleştirmesinin nasıl ayrıştırılır?

- Modern ve eski kimlik doğrulamasıyla birlikte bulunma ortamı, modernlanmış kimlik hizmeti sağlayıcısı aracılığıyla nasıl oluşturulabilir?

  a. Son Kullanıcı deneyimi tutarlılığı nasıl sürücüsüdür?

  b. Birlikte var olan uygulamalarda çoklu oturum açma deneyimi nasıl sağlanır?

Azure AD B2C [Pingaccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) ve [pingfederate](https://www.pingidentity.com/en/software/pingfederate.html) teknolojileriyle tümleştirerek söz konusu kaygıları çözme yaklaşımını tartıştık.

## <a name="coexistence-environment"></a>Birlikte bulunma ortamı

Aynı zamanda uygun maliyetli bir basit çözüm, kimlik doğrulamasının yetkisini alarak, doğru proxy sistemini modernlanmış kimlik sistemini kullanacak şekilde yapılandırmaktır.  
Bu durumda proxy 'ler, modern kimlik doğrulama protokollerini destekleyecektir ve yeni kimlik sağlayıcısına (IDP) Kullanıcı gönderecek yeniden yönlendirme tabanlı (pasif) kimlik doğrulamasını kullanır.

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Kimlik sağlayıcısı olarak Azure AD B2C

Azure AD B2C, farklı kullanıcı deneyimlerini ve ayrıca sunucu sonlandırmalarından genişletilmiş şekilde **Kullanıcı** olarak da adlandırılan davranışları yönlendiren **ilkeler** tanımlayabilir. Bu tür bir ilke, kimlik doğrulamasını bir IDP gibi gerçekleştirebilen bir protokol uç noktası sunar. Belirli ilkelerin uygulama tarafında özel bir işlem yapılması gerekmez. Uygulama, ilgili ilkenin açığa çıkarılan protokolüne özgü kimlik doğrulama uç noktasına bir sektör standart kimlik doğrulama isteği oluşturur.  
Azure AD B2C, her ilke için birden çok ilke veya benzersiz veren için aynı verenler paylaşacak şekilde yapılandırılabilir. Her uygulama, protokol yerel kimlik doğrulama isteği yaparak ve oturum açma, kaydolma ve profil düzenlemeleri gibi istenen kullanıcı davranışlarını sürücüleyerek bu ilkelerin bir veya daha çoğuna işaret edebilir. Diyagramda OıDC ve SAML uygulama iş akışları gösterilmektedir.

![görüntü OıDC ve SAML uygulamasını gösterir](./media/partner-ping/azure-ad-identity-provider.png)

Söz konusu senaryo, modernleştirilmiş uygulamalar için iyi işlem yaptığı sürece, uygulamalara erişim isteği Kullanıcı deneyimi için bağlam içeremese de, eski uygulamaların kullanıcıyı uygun şekilde yeniden yönlendirmek zor olabilir. Çoğu durumda, Web uygulamasındaki proxy katmanı veya tümleşik bir aracı erişim isteğini karşılar.

### <a name="pingaccess-as-a-reverse-proxy"></a>Ters proxy olarak PingAccess

Birçok müşteri, bu makalede daha önce belirtildiği gibi bir veya daha fazla rol çalmak için ters proxy olarak PingAccess 'i dağıttı. PingAccess, Web uygulaması sunucusu üzerinde çalışan bir aracıdan gelen adam veya bir yeniden yönlendirme olarak bir doğrudan isteği ele geçirebilir.

PingAccess, bir yukarı akış kimlik doğrulama sağlayıcısında kimlik doğrulaması gerçekleştirmek için OıDC, OAuth2 veya SAML ile yapılandırılabilir. PingAccess sunucusunda bu amaçla tek bir yukarı akış IOP yapılandırılabilir. Aşağıdaki diyagramda bu yapılandırma gösterilmektedir.

![görüntü, OıDC uygulamasıyla PingAccess 'i gösterir](./media/partner-ping/authorization-flow.png)

Birden çok ilkenin birden çok **IDP** sergiletiği tipik bir Azure AD B2C dağıtımında, bir zorluk sergiler. PingAccess yalnızca tek bir yukarı akış IDP ile yapılandırılmış olabilir.  

### <a name="pingfederate-as-a-federation-proxy"></a>Federasyon proxy 'si olarak PingFederate

PingFederate, gerekirse bir kimlik doğrulama sağlayıcısı veya bir ara sunucu olarak tam olarak yapılandırılabilen bir kurumsal kimlik köprüsüyle kullanılabilir. Aşağıdaki diyagramda bu yetenek gösterilmektedir.

![görüntü PingFederate uygulamasını gösterir](./media/partner-ping/pingfederate.png)

Bu özellik, bir gelen isteği belirli bir Azure AD B2C ilkesine bağlamsal olarak/dinamik olarak veya bildirimli olarak değiştirmek için kullanılabilir. Bu yapılandırma için protokol sırası akışının bir gösterimi aşağıda verilmiştir.

![görüntü PingAccess ve PingFederate iş akışını gösterir](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Azure aboneliği. Bir [hesabınız yoksa ücretsiz bir hesap](https://azure.microsoft.com/free/)alın.

- Azure aboneliğinize bağlı bir [Azure AD B2C kiracısı](./tutorial-create-tenant.md) .

- PingAccess ve PingFederate, Docker kapsayıcılarında veya doğrudan Azure VM 'lerinde dağıtılır.

## <a name="connectivity"></a>Bağlantı

Aşağıdakilerin bağlı olduğundan emin olun.

- **Pingaccess sunucusu** : Azure AD B2C hizmeti ve pingfederate sunucusu tarafından yayımlanan PingFederate sunucusu, istemci tarayıcısı, OIDC, OAuth iyi bilinen ve anahtar keşfi ile iletişim kurabilir.

- **Pingfederate sunucusu** : Azure AD B2C hizmeti tarafından yayımlanan pingaccess sunucusu, istemci tarayıcısı, OIDC, OAuth iyi bilinen ve anahtar keşfi ile iletişim kurabilir.

- **Eski veya üst bilgi tabanlı AuthN uygulaması** – pingaccess Server ile ve arasında iletişim kurabilir.

- **SAML bağlı olan taraf uygulaması** -istemciden tarayıcı trafiğine erişebiliyor. Azure AD B2C hizmeti tarafından yayımlanan SAML Federasyonu meta verilerine erişim sağlayabiliyor.

- **Modern uygulama** : istemciden tarayıcı trafiğine ulaşabilir. Azure AD B2C hizmeti tarafından yayımlanan OıDC, OAuth iyi bilinen ve anahtar bulmaya erişebilir.

- **REST API** – bir yerel veya Web istemcisinden trafiğe ulaşabiliyor. Azure AD B2C hizmeti tarafından yayımlanan OıDC, OAuth iyi bilinen ve anahtar bulmaya erişebilir.

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C Yapılandır

Bu amaçla temel Kullanıcı akışlarını veya Gelişmiş kimlik kurumsal çerçevesi (ıEF) ilkelerini kullanabilirsiniz. PingAccess, [WebFinger](https://tools.ietf.org/html/rfc7033) tabanlı bulma kuralını kullanarak **veren** değerine göre meta veri uç noktası oluşturur.
Bu kuralı izlemek için, Kullanıcı akışlarında ilke özelliklerini kullanarak Azure AD B2C veren güncelleştirmesini güncelleştirin.

![görüntü, belirteç ayarlarını gösterir](./media/partner-ping/token-setting.png)

Gelişmiş ilkelerde, bu, [JWT belirteci verenin teknik profilindeki](./jwt-issuer-technical-profile.md) **ıssuwithtfp** değeri Ile **ıssuanceclaımpattern** meta veri öğesi kullanılarak yapılandırılabilir.

## <a name="configure-pingaccesspingfederate"></a>PingAccess/PingFederate yapılandırma

Aşağıdaki bölümde gerekli yapılandırma ele alınmaktadır.
Diyagramda, tümleştirme için genel Kullanıcı akışı gösterilmektedir.

![görüntüde PingAccess ve PingFederate tümleştirmesi gösterilir](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>PingFederate 'i belirteç sağlayıcısı olarak yapılandırma

PingFederate ' i PingAccess için belirteç sağlayıcısı olarak yapılandırmak için, PingFederate 'ten PingAccess 'e bağlantıyı ve PingFederate 'e PingAccess 'ten bağlantı kurulması gerektiğini doğrulayın.  
Yapılandırma adımları için [Bu makaleye](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) bakın.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Üst bilgi tabanlı kimlik doğrulaması için bir PingAccess uygulaması yapılandırma

Üst bilgi tabanlı kimlik doğrulaması için hedef Web uygulaması için bir PingAccess uygulamasının oluşturulması gerekir. Şu adımları izleyin.

#### <a name="step-1--create-a-virtual-host"></a>1. adım – sanal konak oluşturma

>[!IMPORTANT]
>Bu çözüme yönelik olarak yapılandırmak için, her uygulama için sanal konağın oluşturulması gerekir. Yapılandırma konuları ve etkileri hakkında daha fazla bilgi için bkz. [önemli noktalar](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

Sanal konak oluşturmak için aşağıdaki adımları izleyin:

1. **Ayarlar**  >    >  **sanal konaklarına** git

2. **Sanal konak Ekle** 'yi seçin

3. Ana bilgisayar alanına uygulama URL 'sinin FQDN kısmını girin

4. Bağlantı noktası alanına **443** girin.

5. **Kaydet**’i seçin

#### <a name="step-2--create-a-web-session"></a>2. adım – bir Web oturumu oluşturun

Bir Web oturumu oluşturmak için aşağıdaki adımları izleyin:

1. **Ayarlar**  >  **erişim**  >  **Web oturumları** 'na gidin

2. **Web oturumu Ekle** 'yi seçin

3. Web oturumu için bir **ad** sağlayın.

4. **IŞARETLI JWT** veya **şifreli JWT** **tanımlama bilgisi türünü** seçin

5. **Hedef kitle** için benzersiz bir değer sağlayın

6. **ISTEMCI kimliği** ALANıNA **Azure AD uygulama kimliği** ' ni girin.

7. **Istemci parolası** alanına, Azure AD 'de uygulama Için oluşturduğunuz **anahtarı** girin.

8. İsteğe bağlı-Microsoft Graph API ile özel talepler oluşturup kullanabilirsiniz. Bunu seçerseniz, **Gelişmiş** ' i seçin ve **Istek profili** ve **Kullanıcı özniteliklerini Yenile** seçeneklerini kaldırın. Özel talepler kullanma hakkında daha fazla bilgi için bkz. [özel talep kullanma](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

9. **Kaydet**’i seçin

#### <a name="step-3--create-identity-mapping"></a>3. Adım – kimlik eşlemesi oluşturma

>[!NOTE]
>Birden fazla uygulama üst bilgide aynı verileri bekliyorsanız, kimlik eşleme birden fazla uygulamayla birlikte kullanılabilir.

Kimlik eşlemesi oluşturmak için aşağıdaki adımları izleyin:

1. **Ayarlar**  >  **erişim**  >  **kimliği eşlemeleri** 'ne git

2. **Kimlik eşlemesi Ekle** ' yi seçin

3. Bir **ad** belirtin

4. **Üst bilgi kimliği eşlemesinin** kimlik eşleme türünü seçin

5. **Öznitelik eşleme** tablosunda, gerekli eşlemeleri belirtin. Örneğin,

   Öznitelik adı | Üst bilgi adı |
   |-------|--------|
   |'le | x-userprentaname |
   |e-posta   |    x-e-posta  |
   |id   | x-OID  |
   |'yi   |     x kapsamı |
   |AMR    |    x-AMR    |

6. **Kaydet**’i seçin

#### <a name="step-4--create-a-site"></a>4. adım – site oluşturma

>[!NOTE]
>Bazı yapılandırmalarda, bir sitede birden fazla uygulama bulunabilir. Bir site, uygun yerlerde birden çok uygulamayla birlikte kullanılabilir.

Bir site oluşturmak için aşağıdaki adımları izleyin:

1. **Ana**  >  **sitelere** git

2. **Site Ekle** 'yi seçin

3. Site için bir **ad** belirtin

4. Site **hedefini** girin. Hedef, uygulamayı barındıran sunucunun ana bilgisayar adı: bağlantı noktası çiftidir. Bu alana uygulamanın yolunu girmeyin. Örneğin, üzerindeki bir uygulama, https://mysite:9999/AppName sitem 'in hedef değerine sahip olacaktır: 9999

5. Hedefin güvenli bağlantılar isteyip beklenmediğini belirtin.

6. Hedef, güvenli bağlantılar bekliyorsanız, güvenilen sertifika grubunu **herhangi bir güvenecek** şekilde ayarlayın.

7. **Kaydet**’i seçin

#### <a name="step-5--create-an-application"></a>5. adım – uygulama oluşturma

Azure 'da korumak istediğiniz her uygulama için PingAccess 'te bir uygulama oluşturmak için bu adımları izleyin.

1. **Ana**  >  **uygulamalara** git

2. **Uygulama Ekle** ' yi seçin

3. Uygulama için bir **ad** belirtin

4. İsteğe bağlı olarak, uygulama için bir **Açıklama** girin

5. Uygulamanın **bağlam kökünü** belirtin. Örneğin, üzerindeki bir uygulama https://mysite:9999/AppName /Appnamein bağlam köküne sahip olur. Bağlam kökü eğik çizgi (/) ile başlamalıdır, eğik çizgiyle (/) bitmemelidir ve birden fazla katman derinliğinden (örneğin,/Apps/MyApp) daha fazla olabilir.

6. Oluşturduğunuz **sanal konağı** seçin

   >[!NOTE]
   >Sanal konak ve bağlam kökünün birleşimi PingAccess içinde benzersiz olmalıdır.

7. Oluşturduğunuz **Web oturumunu** seçin

8. Uygulamayı içeren oluşturduğunuz **siteyi** seçin

9. Oluşturduğunuz **kimlik eşlemesini** seçin

10. Kaydettiğinizde siteyi etkinleştirmek için **etkin** ' i seçin

11. **Kaydet**’i seçin

### <a name="configure-the-pingfederate-authentication-policy"></a>PingFederate kimlik doğrulama ilkesini yapılandırma

PingFederate kimlik doğrulama ilkesini, Azure AD B2C kiracılar tarafından belirtilen birden çok IDPs 'ye federasyona eklemek için yapılandırın

1. IDPs ve SP arasındaki öznitelikleri bağlamak için bir sözleşme oluşturun. Daha fazla bilgi için bkz. [Federasyon hub ve kimlik doğrulama ilkesi sözleşmeleri](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html). SP, her IDP 'den farklı bir öznitelik kümesi gerektirmediği takdirde muhtemelen yalnızca bir sözleşmeye ihtiyaç duyarsınız.

2. Her IDP için, IDP ile PingFederate, SP olarak Federasyon merkezi arasında bir IDP bağlantısı oluşturun.

3. **Hedef oturum eşleme** penceresinde, IDP bağlantısına geçerli kimlik doğrulama ilkesi sözleşmeleri ekleyin.

4. **Seçiciler** penceresinde bir kimlik doğrulama Seçicisi yapılandırın. Örneğin, her IDP 'yi bir kimlik doğrulama ilkesinde karşılık gelen IDP bağlantısına eşlemek için **tanımlayıcı Ilk bağdaştırıcısının** bir örneğine bakın.

5. PingFederate, IDP olarak Federasyon merkezi ve SP arasında bir SP bağlantısı oluşturun.

6. Karşılık gelen kimlik doğrulama ilkesi sözleşmesini, **kimlik doğrulama kaynağı eşleme** penceresinde SP bağlantısı 'na ekleyin.

7. Her IDP ile çalışarak, Federasyon hub 'ı olan PingFederate 'e bağlanın.

8. IDP olarak Federasyon hub 'ı PingFederate 'e bağlanmak için SP ile birlikte çalışın.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
