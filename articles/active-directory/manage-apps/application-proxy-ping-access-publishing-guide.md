---
title: Azure AD Uygulama Ara Sunucusu için PingAccess ile üst bilgi tabanlı kimlik doğrulaması
description: Üst bilgi tabanlı kimlik doğrulamasını desteklemek için, PingAccess ve uygulama proxy 'Si ile uygulama yayımlayın.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 23008d785c27b901f3487d3eddff7cb8e7529f6e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600089"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Uygulama Ara Sunucusu ve PingAccess ile çoklu oturum açmak için üst bilgi tabanlı kimlik doğrulaması

Azure Active Directory (Azure AD) uygulama proxy 'Si, Azure AD müşterilerinizin uygulamalarınıza daha fazlasına erişebilmeleri için PingAccess ile işbirliği yaptı. PingAccess, tümleşik [üst bilgi tabanlı çoklu oturum açma](application-proxy-configure-single-sign-on-with-headers.md)ötesinde başka bir seçenek sağlar.

## <a name="whats-pingaccess-for-azure-ad"></a>Azure AD için PingAccess nedir?

Azure AD için PingAccess sayesinde kullanıcılara kimlik doğrulaması için üst bilgi kullanan uygulamalara erişim ve çoklu oturum açma (SSO) izni verebilirsiniz. Uygulama proxy 'Si, erişimi doğrulamak ve bağlayıcı hizmeti üzerinden trafiği geçirmek için Azure AD kullanarak bu uygulamaları başka bir şekilde değerlendirir. PingAccess uygulamaların önünde bulunur ve erişim belirtecini Azure AD 'den bir üstbilgiye dönüştürür. Daha sonra uygulama, kimlik doğrulamasını okuyabilirler biçiminde alır.

Kullanıcılarınız şirket uygulamalarınızı kullanmak için oturum açtıklarında farklı hiçbir şey yapmaz. Herhangi bir cihazdaki herhangi bir yerden çalışmaya devam edebilirler. Uygulama proxy bağlayıcıları, uzak trafiği kimlik doğrulama türlerine bakılmaksızın tüm uygulamalara yönlendirdiklerinden, yükleri otomatik olarak dengelemeye devam eder.

## <a name="how-do-i-get-access"></a>Nasıl yaparım? erişim mi?

Bu senaryo Azure Active Directory ve PingAccess arasındaki bir iş ortaklığından geldiğinden, her iki hizmet için de lisansa sahip olmanız gerekir. Ancak Azure Active Directory Premium abonelikler, 20 ' ye kadar uygulamayı kapsayan temel bir PingAccess lisansını içerir. 20 ' den fazla üst bilgi tabanlı uygulama yayımlamanız gerekiyorsa, PingAccess 'ten ek bir lisans satın alabilirsiniz.

Daha fazla bilgi için bkz. [Azure Active Directory sürümleri](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Uygulamanızı Azure 'da yayımlayın

Bu makale, kullanıcıların bu senaryoya ilk kez bir uygulama yayımlamasına yöneliktir. Yayımlama adımlarının açıklanmasının yanı sıra, uygulama proxy 'Si ve PingAccess ile çalışmaya başlama konusunda size rehberlik eder. Her iki hizmeti de zaten yapılandırdıysanız, ancak yayımlama adımlarında bir yenileyici isterseniz, uygulama [proxy 'si ile uygulamanızı Azure AD 'ye ekleme](#add-your-application-to-azure-ad-with-application-proxy) bölümüne atlayın.

> [!NOTE]
> Bu senaryo Azure AD ve PingAccess arasında bir ortaklık olduğundan, bazı yönergeler ping kimlik sitesinde bulunur.

### <a name="install-an-application-proxy-connector"></a>Uygulama proxy bağlayıcısını yükler

Uygulama proxy 'Si etkinse ve bir bağlayıcıyı zaten yüklediyseniz, bu bölümü atlayabilir ve uygulama [proxy 'si ile uygulamanızı Azure AD 'ye ekleyebilirsiniz](#add-your-application-to-azure-ad-with-application-proxy).

Uygulama proxy Bağlayıcısı, uzak çalışanlarınızdan gelen trafiği yayımlanan uygulamalarınıza yönlendiren bir Windows Server hizmetidir. Daha ayrıntılı yükleme yönergeleri için bkz. [öğretici: Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md).

1. [Azure Active Directory portalında](https://aad.portal.azure.com/) uygulama Yöneticisi olarak oturum açın. **Azure Active Directory Yönetim Merkezi** sayfası görüntülenir.
1. **Azure Active Directory**  >  **uygulama proxy 'si**  >  **indirme Bağlayıcısı hizmeti**' ni seçin. **Uygulama proxy Bağlayıcısı indirme** sayfası açılır.

   ![Uygulama proxy Bağlayıcısı indirme](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-connector-download.png)

1. Yükleme yönergelerini izleyin.

Bağlayıcının indirilmesi, dizininiz için otomatik olarak uygulama proxy 'Si sağlamalıdır, ancak yoksa, **uygulama proxy 'Sini etkinleştir** seçeneğini belirleyebilirsiniz.

### <a name="add-your-application-to-azure-ad-with-application-proxy"></a>Uygulamanızı Azure AD 'ye uygulama proxy 'Si ile ekleme

Azure portal gerçekleştirmeniz gereken iki eylem vardır. İlk olarak, uygulamanızı uygulama proxy 'Si ile yayımlamanız gerekir. Ardından, PingAccess adımları sırasında kullanabileceğiniz uygulama hakkında bazı bilgiler toplamanız gerekir.

#### <a name="publish-your-application"></a>Uygulamanızı yayımlama

Önce uygulamanızı yayımlamanız gerekir. Bu eylem şunları içerir:

- Şirket içi uygulamanızı Azure AD 'ye ekleme
- Uygulamayı test etmek ve üst bilgi tabanlı SSO 'yu seçmek için kullanıcı atama
- Uygulamanın yeniden yönlendirme URL 'sini ayarlama
- Şirket içi uygulamanızı kullanmak için kullanıcılar ve diğer uygulamalar için izin verme

Kendi şirket içi uygulamanızı yayımlamak için:

1. Son bölümde yapmadıysanız, uygulama Yöneticisi olarak [Azure Active Directory portalında](https://aad.portal.azure.com/) oturum açın.
1. **Kurumsal uygulamalar**' ı seçin  >  **Yeni uygulama**  >  **Şirket içi uygulama ekleme**. **Kendi şirket içi uygulamanızı ekleyin** sayfası görüntülenir.

   ![Kendi şirket içi uygulamanızı ekleyin](./media/application-proxy-configure-single-sign-on-with-ping-access/add-your-own-on-premises-application.png)
1. Gerekli alanları yeni uygulamanızla ilgili bilgilerle doldurun. Ayarlar için aşağıdaki kılavuzu kullanın.

   > [!NOTE]
   > Bu adım hakkında daha ayrıntılı yönergeler için bkz. [Azure AD 'ye şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

   1. **Iç URL**: Normalde, şirket ağı üzerinde olduğunuzda uygulamanın oturum açma sayfasına götüren URL 'yi sağlarsınız. Bu senaryoda, bağlayıcının PingAccess proxy 'sini uygulamanın ön sayfası olarak işlemesi gerekir. Şu biçimi kullanın: `https://<host name of your PingAccess server>:<port>` . Bağlantı noktası varsayılan olarak 3000 ' dir, ancak bunu PingAccess 'te yapılandırabilirsiniz.

      > [!WARNING]
      > Bu çoklu oturum açma türü için iç URL 'nin kullanılması `https` ve kullanması gerekir `http` . Ayrıca, uygulama proxy 'sinin uygulamalar arasında ayrım yapmasına olanak tanıyan iki uygulamanın aynı iç URL 'ye sahip olması gerektiği bir uygulama yapılandırılırken kısıtlama vardır.

   1. **Ön kimlik doğrulama yöntemi**: **Azure Active Directory** seçin.
   1. **Üst bilgilerdeki URL 'Yi çevir**: **Hayır**' ı seçin.

   > [!NOTE]
   > İlk uygulamanız bu ise, bağlantı noktası 3000 ' u kullanarak başlangıç yapın ve PingAccess yapılandırmanızı değiştirirseniz bu ayarı güncelleştirmek için geri dönün. Sonraki uygulamalarda, bağlantı noktasının PingAccess 'te yapılandırdığınız dinleyiciyle eşleşmesi gerekir. [PingAccess 'teki dinleyiciler](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=reference/ui/pa_c_Listeners.html)hakkında daha fazla bilgi edinin.

1. **Add (Ekle)** seçeneğini belirleyin. Yeni uygulama için genel bakış sayfası görüntülenir.

Şimdi uygulama testi için bir Kullanıcı atayın ve üst bilgi tabanlı çoklu oturum açma seçeneğini belirleyin:

1. Uygulama kenar çubuğundan **Kullanıcılar ve gruplar**  >  **Kullanıcı**  >  **kullanıcıları ve grupları Ekle ( \<Number> Seçili)** seçeneğini belirleyin. Aralarından seçim yapabileceğiniz kullanıcılar ve grupların listesi görüntülenir.

   ![Kullanıcıların ve grupların listesini gösterir](./media/application-proxy-configure-single-sign-on-with-ping-access/users-and-groups.png)

1. Uygulama testi için bir kullanıcı seçin ve **Seç**' i seçin. Bu sınama hesabının şirket içi uygulamaya erişimi olduğundan emin olun.
1. **Ata**’yı seçin.
1. Uygulama kenar çubuğundan **Çoklu oturum açma**  >  **üst bilgi tabanlı**' yı seçin.

   > [!TIP]
   > Üst bilgi tabanlı çoklu oturum açma 'yı ilk kez kullanıyorsanız PingAccess 'i yüklemeniz gerekir. Azure aboneliğinizin PingAccess yüklemenizin otomatik olarak ilişkilendirildiğinden emin olmak için bu çoklu oturum açma sayfasındaki bağlantıyı kullanarak PingAccess 'i indirin. İndirme sitesini şimdi açabilir veya daha sonra bu sayfaya geri dönebilirsiniz.

   ![Üst bilgi tabanlı oturum açma ekranını ve PingAccess 'i gösterir](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.png)

1. **Kaydet**’i seçin.

Yeniden yönlendirme URL 'nizin dış URL 'niz olarak ayarlandığından emin olun:

1. **Azure Active Directory Yönetim Merkezi** kenar çubuğundan **Azure Active Directory**  >  **uygulama kayıtları**' yı seçin. Uygulamaların listesi görüntülenir.
1. Uygulamanızı seçin.
1. Web ve genel istemciler için ayarlanan yeniden yönlendirme URI 'lerinin sayısını göstererek **yeniden yönlendirme URI**'lerinin yanındaki bağlantıyı seçin. **\<application name> -Kimlik doğrulama** sayfası görüntülenir.
1. Daha önce uygulamanıza atadığınız dış URL 'nin **yeniden yönlendirme URI 'leri** listesinde olup olmadığını denetleyin. Değilse, bir **Web** yeniden yönlendirme URI 'si türü kullanarak dış URL 'yi hemen ekleyin ve **Kaydet**' i seçin.

Dış URL 'nin yanı sıra, dış URL üzerindeki Azure Active Directory yetkilendirme uç noktası, yeniden yönlendirme URI 'Leri listesine eklenmelidir.

`https://*.msappproxy.net/pa/oidc/cb`
`https://*.msappproxy.net/`

Son olarak, kullanıcıların okuma erişiminin olması ve diğer uygulamaların okuma/yazma erişimine sahip olması için şirket içi uygulamanızı ayarlayın:

1. Uygulamanızın **uygulama kayıtları** kenar çubuğundan **API izinleri**  >    >  **Microsoft API 'leri**  >  **Microsoft Graph** izin Ekle ' yi seçin. Windows Azure Active Directory için API 'Leri içeren **Microsoft Graph** için **istek API 'si izinleri** sayfası görüntülenir.

   ![API izinleri ıste sayfasını gösterir](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

1. **Temsilci izinleri**  >  **Kullanıcı**  >  **kullanıcısı. Read** seçeneğini belirleyin.
1. **Uygulama izinleri**  >  **uygulama**  >  **uygulaması. ReadWrite. All**' ı seçin.
1. **Izin Ekle**' yi seçin.
1. **API izinleri** sayfasında, **Için \<your directory name> yönetici onayı ver**' i seçin.

#### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess adımları için bilgi toplayın

Uygulamanızı PingAccess ile ayarlamak için bu üç bilgi parçasını (tüm GUID 'Leri) toplamanız gerekir:

| Azure AD alanının adı | PingAccess alanının adı | Veri biçimi |
| --- | --- | --- |
| **Uygulama (istemci) kimliği** | **İstemci Kimliği** | GUID |
| **Dizin (kiracı) kimliği** | **Veren** | GUID |
| `PingAccess key` | **İstemci parolası** | Rastgele dize |

Bu bilgileri toplamak için:

1. **Azure Active Directory Yönetim Merkezi** kenar çubuğundan **Azure Active Directory**  >  **uygulama kayıtları**' yı seçin. Uygulamaların listesi görüntülenir.
1. Uygulamanızı seçin. Uygulamanızın **uygulama kayıtları** sayfası görüntülenir.

   ![Bir uygulama için kayda genel bakış](./media/application-proxy-configure-single-sign-on-with-ping-access/registration-overview-for-an-application.png)

1. **Uygulama (istemci) kimliği** değerinin yanındaki **Panoya Kopyala** simgesini seçin, sonra kopyalayıp kaydedin. Bu değeri daha sonra PingAccess 'in istemci KIMLIĞI olarak belirtirsiniz.
1. **Dizin (kiracı) kimliği** değeri ' nin ardından, **Panoya Kopyala**' yı seçin, sonra da kopyalayın ve kaydedin. Bu değeri daha sonra PingAccess 'in veren olarak belirtirsiniz.
1. Uygulamanızın **uygulama kayıtları** kenar çubuğundan Sertifikalar ' ı **ve gizli** dizileri  >  **yeni istemci parolası**' nı seçin. **İstemci parolası Ekle** sayfası görüntülenir.

   ![İstemci parolası ekleme sayfasını gösterir](./media/application-proxy-configure-single-sign-on-with-ping-access/add-a-client-secret.png)

1. **Açıklama**' da yazın `PingAccess key` .
1. **Süre sonu** altında, pingaccess tuşunun nasıl ayarlanacağını seçin: **1 yılda**, **2 yıl içinde** veya **hiçbir zaman**.
1. **Add (Ekle)** seçeneğini belirleyin. PingAccess tuşu, istemci gizli dizileri tablosunda, **değer** alanı ' nı tekrar dolduran rastgele bir dize ile görünür.
1. PingAccess tuşunun **değer** alanının yanındaki **Panoya Kopyala** simgesini seçin, sonra kopyalayıp kaydedin. Bu değeri daha sonra PingAccess 'in istemci gizli anahtarı olarak belirtirsiniz.

**Alanı güncelleştirin `acceptMappedClaims` :**

1. [Azure Active Directory portalında](https://aad.portal.azure.com/) uygulama Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **uygulama kayıtları** seçin. Uygulamaların listesi görüntülenir.
1. Uygulamanızı seçin.
1. Uygulamanızın **uygulama kayıtları** sayfasının kenar çubuğundan **bildirim**' ı seçin. Uygulamanızın kaydı için bildirim JSON kodu görüntülenir.
1. `acceptMappedClaims`Alanı arayın ve değerini olarak değiştirin `True` .
1. **Kaydet**’i seçin.

### <a name="use-of-optional-claims-optional"></a>İsteğe bağlı talepler kullanımı (isteğe bağlı)

İsteğe bağlı talepler, her kullanıcı ve kiracının sahip olduğu standart, ancak dahil olmayan talepler eklemenize olanak tanır. Uygulama bildirimini değiştirerek uygulamanız için isteğe bağlı talepler yapılandırabilirsiniz. Daha fazla bilgi için bkz [. Azure AD uygulama bildirimini anlama makalesi](../develop/reference-app-manifest.md)

PingAccess 'in kullanacağı access_token e-posta adresini eklemek için örnek:

```json
    "optionalClaims": {
        "idToken": [],
        "accessToken": [
            {
                "name": "email",
                "source": null,
                "essential": false,
                "additionalProperties": []
            }
        ],
        "saml2Token": []
    },
```

### <a name="use-of-claims-mapping-policy-optional"></a>Talep eşleme ilkesi kullanımı (isteğe bağlı)

AzureAD içinde mevcut olmayan öznitelikler için [talep eşleme ilkesi (Önizleme)](../develop/reference-claims-mapping-policy-type.md#claims-mapping-policy-properties) . Talep eşleme, ADFS veya Kullanıcı nesneleriniz tarafından desteklenen ek özel talepler ekleyerek eski şirket içi uygulamaları buluta geçirmenize olanak sağlar

Uygulamanızın özel bir talep kullanmasını ve ek alanlar içermesini sağlamak için [özel bir talep eşleme ilkesi oluşturduğunuzdan ve uygulamaya atandığından](../develop/active-directory-claims-mapping.md)emin olun.

> [!NOTE]
> Özel bir talep kullanmak için, tanımlanmış ve uygulamaya atanmış özel bir ilkeniz olması gerekir. Bu ilke tüm gerekli özel öznitelikleri içermelidir.
>
> PowerShell veya Microsoft Graph aracılığıyla ilke tanımı ve atama yapabilirsiniz. PowerShell 'de bunu yapıyorsanız, önce kullanmanız `New-AzureADPolicy` ve ardından uygulamaya atamanız gerekebilir `Add-AzureADServicePrincipalPolicy` . Daha fazla bilgi için bkz. [talep eşleme ilkesi ataması](../develop/active-directory-claims-mapping.md).

Örnek:
```powershell
$pol = New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","JwtClaimType":"employeeid"}]}}') -DisplayName "AdditionalClaims" -Type "ClaimsMappingPolicy"

Add-AzureADServicePrincipalPolicy -Id "<<The object Id of the Enterprise Application you published in the previous step, which requires this claim>>" -RefObjectId $pol.Id
```

### <a name="enable-pingaccess-to-use-custom-claims"></a>Özel talepler kullanmak için PingAccess 'i etkinleştir

Özel talepler kullanmak için PingAccess 'in etkinleştirilmesi isteğe bağlıdır, ancak uygulamanın ek talepler kullanmasını beklemeniz durumunda gereklidir.

Aşağıdaki adımda PingAccess 'i yapılandırırken, oluşturduğunuz Web oturumunun (ayarlar->erişim->Web oturumları) **Istek profili** seçiminin seçili olması ve **Kullanıcı özniteliklerinin yenilemeyecek** **şekilde** yenilenmesi gerekir

## <a name="download-pingaccess-and-configure-your-application"></a>PingAccess 'i indirme ve uygulamanızı yapılandırma

Tüm Azure Active Directory kurulum adımlarını tamamladığınıza göre, PingAccess 'i yapılandırmak için üzerinde geçiş yapabilirsiniz.

Bu senaryonun PingAccess bölümüne ilişkin ayrıntılı adımlar, ping kimliği belgelerinde devam eder. Ping kimliği Web sitesindeki [Microsoft Azure AD uygulama proxy 'si kullanılarak yayınlanan uygulamaları korumak](https://support.pingidentity.com/s/document-item?bundleId=pingaccess-52&topicId=agents/azure/pa_c_PAAzureSolutionOverview.html) ve [pingaccess 'in en son sürümünü](https://www.pingidentity.com/en/lp/azure-download.html?)indirmek Için Azure AD için pingaccess yapılandırma bölümündeki yönergeleri izleyin.

Bu adımlar PingAccess 'i yüklemenize ve bir PingAccess hesabı ayarlamanıza yardımcı olur (henüz bir hesabınız yoksa). Ardından, bir Azure AD OpenID Connect (OıDC) bağlantısı oluşturmak için Azure AD portalından kopyaladığınız **Dizin (kiracı) kimliği** değeri ile bir belirteç sağlayıcısı ayarlarsınız. Ardından, PingAccess üzerinde bir Web oturumu oluşturmak için, **uygulama (istemci) kimliğini** ve `PingAccess key` değerlerini kullanırsınız. Bundan sonra kimlik eşlemeyi ayarlayabilir ve sanal bir konak, site ve uygulama oluşturabilirsiniz.

### <a name="test-your-application"></a>Uygulamanızı test etme

Tüm bu adımları tamamladığınızda uygulamanız çalışır duruma gelmelidir. Test etmek için bir tarayıcı açın ve uygulamayı Azure 'da yayımladığınızda oluşturduğunuz dış URL 'ye gidin. Uygulamaya atadığınız test hesabıyla oturum açın.

## <a name="next-steps"></a>Sonraki adımlar

- [Microsoft Azure AD uygulama proxy 'Si kullanılarak yayımlanan uygulamaları korumak için Azure AD için PingAccess 'i yapılandırma](https://docs.pingidentity.com/bundle/pingaccess-60/page/jep1564006742933.html)
- [Azure Active Directory uygulamalarda çoklu oturum açma](what-is-single-sign-on.md)
- [Uygulama Proxy'si sorunlarını ve hata iletilerini giderme](application-proxy-troubleshoot.md)