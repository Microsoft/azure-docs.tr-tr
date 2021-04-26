---
title: Özel etki alanlarını Azure AD B2C etkinleştir
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C için yeniden yönlendirme URL 'Lerinde özel etki alanlarını nasıl etkinleştirebileceğinizi öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2de419885938b27ebce4a934db5ef966965b3dbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580173"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Azure Active Directory B2C için özel etki alanlarını etkinleştir

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Bu makalede, Azure Active Directory B2C (Azure AD B2C) için yeniden yönlendirme URL 'Lerinde özel etki alanlarının nasıl etkinleştirileceği açıklanır. Uygulamanızla özel bir etki alanı kullanmak, daha sorunsuz bir kullanıcı deneyimi sağlar. Kullanıcının perspektifinden, *kiracı adı>. b2clogin.com<* Azure AD B2C varsayılan etki alanına yönlendirmek yerine, oturum açma işlemi sırasında etki alanında kalırlar.

![Özel etki alanı kullanıcı deneyimi](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Özel etki alanına genel bakış

[Azure ön kapısını](https://azure.microsoft.com/services/frontdoor/)kullanarak Azure AD B2C için özel etki alanları etkinleştirebilirsiniz. Azure ön kapı, hızlı, güvenli ve yaygın olarak ölçeklenebilir Web uygulamaları oluşturmak için Microsoft Global Edge ağını kullanan küresel, ölçeklenebilir bir giriş noktasıdır. Azure ön kapısının arkasında Azure AD B2C içeriği işleyebilir ve sonra Azure ön kapıda, içeriği uygulamanızın URL 'sindeki özel bir etki alanı aracılığıyla teslim etmek için bir seçenek yapılandırabilirsiniz.

Aşağıdaki diyagramda Azure ön kapı tümleştirmesi gösterilmektedir:

1. Bir uygulama, bir Kullanıcı oturum açma düğmesine tıklamasını sağlar ve bunları Azure AD B2C oturum açma sayfasına götürür. Bu sayfa özel bir etki alanı adı belirtir.
1. Web tarayıcısı, özel etki alanı adını Azure ön kapısının IP adresine çözümler. DNS çözümlemesi sırasında, özel etki alanı adı olan bir kurallı ad (CNAME) kaydı, ön kapı varsayılan ön uç ana bilgisayarınızı (örneğin,) işaret eder `contoso.azurefd.net` . 
1. Özel etki alanına (örneğin, `login.contoso.com` ) yönlendirilen trafik belirtilen ön kapı varsayılan ön uç konağına ( `contoso.azurefd.net` ) yönlendirilir.
1. Azure ön kapısı, Azure AD B2C varsayılan etki alanını kullanarak içerik Azure AD B2C çağırır `<tenant-name>.b2clogin.com` . Azure AD B2C uç noktasına yapılan istek, özgün özel etki alanı adını içeren özel bir HTTP üst bilgisi içerir.
1. Azure AD B2C, ilgili içeriği ve özgün özel etki alanını görüntüleyerek isteğe yanıt verir.

![Özel etki alanı ağ diyagramı](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> Tarayıcıdan Azure ön kapısına olan bağlantı IPv6 yerine her zaman IPv4 kullanmalıdır.

Özel etki alanları kullanırken şunları göz önünde bulundurun:

- Birden çok özel etki alanı ayarlayabilirsiniz. Desteklenen en fazla özel etki alanı sayısı için bkz. Azure [ad hizmet sınırları ve kısıtlamaları](../active-directory/enterprise-users/directory-service-limits-restrictions.md) Azure AD B2C ve [Azure aboneliği ve hizmet limitleri, Kotalar](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) ve Azure ön kapılarına yönelik kısıtlamalar.
- Azure ön kapısı ayrı bir Azure hizmetidir, bu nedenle ek ücretler uygulanır. Daha fazla bilgi için bkz. [ön kapı fiyatlandırması](https://azure.microsoft.com/pricing/details/frontdoor).
- Azure ön kapısının [Web uygulaması güvenlik duvarını](../web-application-firewall/afds/afds-overview.md)kullanmak için, güvenlik duvarı yapılandırmanızın ve kurallarınızın Azure AD B2C Kullanıcı akışlarınızla doğru şekilde çalıştığını onaylamanız gerekir.
- Özel etki alanlarını yapılandırdıktan sonra, kullanıcılar *<kiracı adı>. b2clogin.com* Azure AD B2C varsayılan etki alanı adına erişebilir (özel bir ilke kullanmıyorsanız ve [erişimi engellemeniz](#block-access-to-the-default-domain-name)mümkün değilse).
- Birden çok uygulamanız varsa, tarayıcı Azure AD B2C oturumu şu anda kullanılmakta olan etki alanı adının altında depoladığından, tümünü özel etki alanına geçirin.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="add-a-custom-domain-name-to-your-tenant"></a>Kiracınıza özel bir etki alanı adı ekleyin

[Azure AD 'de özel etki alanınızı ekleme ve doğrulama](../active-directory/fundamentals/add-custom-domain.md)kılavuzunu izleyin. Etki alanı doğrulandıktan sonra oluşturduğunuz DNS TXT kaydını silin.

> [!IMPORTANT]
> Bu adımlar için **Azure AD B2C** kiracınızda oturum açıp **Azure Active Directory** hizmetini seçtiğinizden emin olun.

Kullanmayı planladığınız her alt etki alanını doğrulayın. Yalnızca üst düzey etki alanının yeterli olmadığı doğrulanıyor. Örneğin, *login.contoso.com* ve *account.contoso.com* ile oturum açabilmeniz için, yalnızca üst düzey etki alanı *contoso.com* değil, her iki alt etki alanını da doğrulamanız gerekir.  

## <a name="create-a-new-azure-front-door-instance"></a>Yeni bir Azure ön kapısı örneği oluşturma

Ön uç konak ve yönlendirme kuralları için varsayılan ayarları kullanarak [uygulamanız Için ön kapı oluşturma](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application) adımlarını izleyin. 

> [!IMPORTANT]
> Bu adımlar için, 1. adımdaki Azure portal oturum açtıktan sonra **Dizin + abonelik** ' ı seçin ve Azure ön kapısı için kullanmak istediğiniz Azure aboneliğini içeren dizini seçin. Bu, Azure AD B2C kiracınızı içeren *Dizin olmamalıdır.* 

**Arka uç ekleme** adımında aşağıdaki ayarları kullanın:

* **Arka uç ana bilgisayar türü** için **özel ana bilgisayar**' ı seçin.  
* **Arka uç ana bilgisayar adı** için, Azure AD B2C uç noktanız için konak adını seçin, <kiracı adı>. b2clogin.com. Örneğin, contoso.b2clogin.com. 
* **Arka uç ana bilgisayar üst bilgisi** Için, **arka uç ana bilgisayar adı** için seçtiğiniz değeri seçin.

![Arka uç Ekle](./media/custom-domain/add-a-backend.png)

**Arka ucunu** **arka uç havuzuna** ekledikten sonra, **sistem durumu araştırmalarını** devre dışı bırakın.

![Arka uç havuzu ekleme](./media/custom-domain/add-a-backend-pool.png)

## <a name="set-up-your-custom-domain-on-azure-front-door"></a>Azure ön kapıda özel etki alanınızı ayarlama

[Ön kapıya özel bir etki alanı eklemek](../frontdoor/front-door-custom-domain.md)için adımları izleyin. `CNAME`Özel etki alanınız için kayıt oluştururken, daha önce [Azure AD 'ye özel etki alanı adı ekleme](#add-a-custom-domain-name-to-your-tenant) adımındaki doğrulamış olduğunuz özel etki alanı adını kullanın. 

Özel etki alanı adı doğrulandıktan sonra, **özel etki alanı adı https**' yi seçin. Ardından **sertifika yönetimi türü** altında, [ön kapı yönetimi](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door)' ni seçin veya [kendi sertifikamı kullanın](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate). 

Aşağıdaki ekran görüntüsünde, bir Azure ön kapı sertifikası kullanarak nasıl özel etki alanı ekleneceği ve HTTPS 'nin etkinleştirileceği gösterilmektedir.

![Azure ön kapısının özel etki alanını ayarlama](./media/custom-domain/azure-front-door-add-custom-domain.png) 

## <a name="configure-cors"></a>CORS Yapılandırma

[Azure AD B2C Kullanıcı arabirimini](customize-ui-with-html.md) bir HTML şablonuyla ÖZELLEŞTIRIRSENIZ, CORS 'yi özel etki alanınız ile [yapılandırmanız](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) gerekir.

Aşağıdaki adımlarla, çıkış noktaları arası kaynak paylaşımı için Azure Blob Storage 'ı yapılandırın:

1. [Azure portalında](https://portal.azure.com) depolama hesabınıza gidin.
1. Menüsünde **CORS**' yi seçin.
1. **Izin verilen çıkış noktaları** için girin `https://your-domain-name` . `your-domain-name`Etki alanı adınızla değiştirin. Örneğin, `https://login.contoso.com`. Kiracı adınızı girerken tüm küçük harfleri kullanın.
1. **Izin verilen Yöntemler** için hem hem de öğesini seçin `GET` `OPTIONS` .
1. **Izin verilen üst bilgiler** için bir yıldız işareti (*) girin.
1. **Gösterilen üstbilgiler** için bir yıldız işareti (*) girin.
1. **Maksimum yaş** için 200 girin.
1. **Kaydet**’i seçin.

## <a name="configure-your-identity-provider"></a>Kimlik sağlayıcınızı yapılandırma

Bir Kullanıcı sosyal kimlik sağlayıcısı ile oturum açmayı seçtiğinde Azure AD B2C bir yetkilendirme isteği başlatır ve oturum açma işlemini tamamlaması için kullanıcıyı seçili kimlik sağlayıcısına alır. Yetkilendirme isteği, `redirect_uri` Azure AD B2C varsayılan etki alanı adını belirtir: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

İlkenizi bir dış kimlik sağlayıcısıyla oturum açmaya izin verecek şekilde yapılandırdıysanız, OAuth yeniden yönlendirme URI 'Lerini özel etki alanı ile güncelleştirin. Çoğu kimlik sağlayıcısı, birden çok yeniden yönlendirme URI 'si kaydetmenizi sağlar. Özel ilkenizi, Azure AD B2C varsayılan etki alanı adını kullanan uygulamaları etkilemeden test edebilmeniz için, yeniden yönlendirme URI 'Leri eklemeniz önerilir. 

Aşağıdaki yeniden yönlendirme URI 'SI:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- **<özel etki alanı adı>** özel etki alanı adınızla değiştirin.
- **<kiracı adı>** , kiracınızın adı veya kiracı Kimliğiniz ile değiştirin.

Aşağıdaki örnekte geçerli bir OAuth yeniden yönlendirme URI 'SI gösterilmektedir:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

[KIRACı kimliğini](#optional-use-tenant-id)kullanmayı seçerseniz, geçerli bir OAuth yeniden yönlendirme URI 'si aşağıdaki gibi görünür:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML kimlik sağlayıcıları](saml-identity-provider-technical-profile.md) meta verileri aşağıdaki gibi görünür:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="test-your-custom-domain"></a>Özel etki alanınızı test etme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **Kullanıcı akışları ' nı (ilkeler)** seçin.
1. Bir Kullanıcı akışı seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Panoya Kopyala**' ya tıklayın.

    ![Yetkilendirme isteği URI 'sini kopyalayın](./media/custom-domain/user-flow-run-now.png)

1. **Kullanıcı akış uç noktası** URL 'sini Çalıştır ' da Azure AD B2C etki alanını (<kiracı-adı>. b2clogin.com) özel etki alanınız ile değiştirin.  
    Örneğin, yerine:

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    kullanırsınız

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```
1. **Kullanıcı akışını Çalıştır**' ı seçin. Azure AD B2C ilkenizi yüklemeniz gerekir.
1. Hem yerel hem de sosyal hesaplar ile oturum açın.
1. Testi ilkelerinizin geri kalanı ile tekrarlayın.

## <a name="configure-your-application"></a>Uygulamanızı yapılandırma 

Özel etki alanını yapılandırdıktan ve test ettikten sonra, uygulamalarınızı, Azure AD B2C etki alanı yerine ana bilgisayar adı olarak belirten URL 'YI yükleyecek şekilde güncelleştirebilirsiniz. 

Özel etki alanı tümleştirmesi, kullanıcıların kimliğini doğrulamak için Azure AD B2C ilkeleri (Kullanıcı akışları veya özel ilkeler) kullanan kimlik doğrulama uç noktaları için geçerlidir. Bu uç noktalar aşağıdaki gibi görünebilir:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Değiştir:
- Özel etki alanı ile **özel etki alanı**
- Kiracı adınızla **kiracı adı veya** Kiracı kimliği
- ilke adını ilke adınızla **adlandırın** . [Azure AD B2C ilkeleri hakkında daha fazla bilgi edinin](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


[SAML hizmeti sağlayıcısı](./saml-service-provider.md) meta verileri aşağıdaki gibi görünebilir: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>Seçim Kiracı KIMLIĞINI kullan

URL 'deki "B2C" tüm başvurularını kaldırmak için, URL 'deki B2C kiracı adınızı kiracı KIMLIĞI GUID 'niz ile değiştirebilirsiniz. Kiracı KIMLIĞI GUID 'nizi, Azure portal B2C Genel Bakış sayfasında bulabilirsiniz.
Örneğin, `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` öğesini `https://account.contosobank.co.uk/<tenant ID GUID>/` olarak değiştirin

Kiracı adı yerine kiracı KIMLIĞI kullanmayı tercih ederseniz, kimlik sağlayıcısı **OAuth yeniden yönlendirme URI 'lerini** uygun şekilde güncelleştirdiğinizden emin olun. Daha fazla bilgi için bkz. [kimlik sağlayıcınızı yapılandırma](#configure-your-identity-provider).

### <a name="token-issuance"></a>Belirteç verme

Belirteç Verenin adı (ISS) talebi, kullanılan özel etki alanına göre değişir. Örnek:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Varsayılan etki alanı adına erişimi engelle

Özel etki alanını ekledikten ve uygulamanızı yapılandırdıktan sonra, kullanıcılar hala <kiracı adı>. b2clogin.com etki alanına erişebilecek. Erişimi engellemek için ilkeyi, "konak adı" yetkilendirme isteğini izin verilen etki alanı listesine göre denetlemek üzere yapılandırabilirsiniz. Ana bilgisayar adı URL 'de görünen etki alanı adıdır. Ana bilgisayar adı `{Context:HostName}` [talep çözücüler](claim-resolver-overview.md)aracılığıyla kullanılabilir. Ardından özel bir hata iletisi sağlayabilirsiniz. 

1. [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name)'dan ana bilgisayar adını denetleyen koşullu erişim ilkesi örneğini alın.
1. Her dosyada, dizeyi `yourtenant` Azure AD B2C kiracınızın adıyla değiştirin. Örneğin, B2C kiracınızın adı *contosob2c* ise, tüm örnekleri `yourtenant.onmicrosoft.com` olur `contosob2c.onmicrosoft.com` .
1. İlke dosyalarını şu sırayla karşıya yükleyin: `B2C_1A_TrustFrameworkExtensions_HostName.xml` ve sonra `B2C_1A_signup_signin_HostName.xml` .

::: zone-end

## <a name="troubleshooting"></a>Sorun giderme

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C bir sayfa bulunamadı hatası döndürüyor

- **Belirti** -özel etki alanını yapılandırdıktan sonra, özel etki alanı ile oturum açmaya ÇALıŞTıĞıNıZDA bir HTTP 404 hata iletisi alırsınız.
- **Olası nedenler** -bu sorun DNS yapılandırmasıyla veya Azure ön kapısı arka uç yapılandırmasıyla ilgili olabilir. 
- **Çözüm**:  
    1. Özel etki alanının kayıtlı olduğundan ve Azure AD B2C kiracınızda [başarıyla doğrulandığından](#add-a-custom-domain-name-to-your-tenant) emin olun.
    1. [Özel etki alanının](../frontdoor/front-door-custom-domain.md) düzgün şekilde yapılandırıldığından emin olun. `CNAME`Özel etki alanınız için kayıt, Azure ön kapısının varsayılan ön uç konağına işaret etmelidir (örneğin, contoso.azurefd.net).
    1. [Azure ön kapısı arka uç havuzu yapılandırmasının](#set-up-your-custom-domain-on-azure-front-door) , özel etki alanı adını ayarladığınız kiracıya işaret ettiğini ve Kullanıcı akışınız veya özel ilkelerinizin nerede depolandığını doğrulayın.

### <a name="identify-provider-returns-an-error"></a>Sağlayıcıyı tanımla bir hata döndürüyor

- **Belirti** -özel bir etki alanını yapılandırdıktan sonra yerel hesaplarla oturum açabiliyor olabilirsiniz. Ancak dış [sosyal veya kurumsal kimlik sağlayıcılarından](add-identity-provider.md)kimlik bilgileriyle oturum açtığınızda kimlik sağlayıcıları bir hata iletisi gösterir.
- **Olası nedenler** -Azure AD B2C kullanıcıyı bir Federasyon kimlik sağlayıcısıyla oturum açtığında, YENIDEN yönlendirme URI 'sini belirtir. Yeniden yönlendirme URI 'SI, kimlik sağlayıcısının belirteci döndürdüğü uç noktadır. Yeniden yönlendirme URI 'SI, uygulamanızın yetkilendirme isteğiyle kullandığı etki alanıdır. Yeniden yönlendirme URI 'SI henüz kimlik sağlayıcısına kayıtlı değilse, yeni yeniden yönlendirme URI 'sine güvenmeyebilir ve bu da bir hata mesajı oluşur. 
- **Çözüm** -yeni YENIDEN yönlendirme URI 'sini eklemek için [kimlik sağlayıcınızı yapılandırma](#configure-your-identity-provider) bölümündeki adımları izleyin. 


## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>*Web uygulaması güvenlik duvarı kuralları* gibi Azure ön kapısı gelişmiş yapılandırmasını kullanabilir miyim? 
  
Azure ön kapısının gelişmiş yapılandırma ayarları resmi olarak desteklenirken, bunları kendi riskinizdeki bir şekilde kullanabilirsiniz. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>İlkemi çalıştırmayı denemek için Şimdi Çalıştır 'ı kullandığım zaman özel etki alanını göremiyorum.

URL 'YI kopyalayın, etki alanı adını el ile değiştirin ve ardından tarayıcınıza geri yapıştırın.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Azure AD B2C hangi IP adresi sunuluyor? Kullanıcının IP adresi veya Azure ön kapısının IP adresi?

Azure ön kapısı kullanıcının özgün IP adresini geçirir. Bu, denetim raporlama veya özel ilkenizde göreceğiniz IP adresidir.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>B2C ile bir üçüncü taraf Web uygulaması güvenlik duvarını (WAF) kullanabilir miyim?

Azure ön kapısının önünde kendi Web uygulaması güvenlik duvarını kullanmak için, her şeyin Azure AD B2C Kullanıcı akışlarınızla doğru şekilde çalıştığından emin olmanız ve doğrulamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

[OAuth yetkilendirme istekleri](protocols-overview.md)hakkında bilgi edinin.