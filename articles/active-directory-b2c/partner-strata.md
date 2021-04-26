---
title: Azure Active Directory B2C Strata ile yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Kullanıcı doğrulaması için Azure AD B2C kimlik doğrulamasını Whoıam ile tümleştirmeyi öğrenin
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 49def79c467aa8f2b3fda9688dc77fcb003a34a5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256574"
---
# <a name="tutorial-for-extending-azure-ad-b2c-to-protect-on-premises-applications-using-strata"></a>Strata kullanarak şirket içi uygulamaları korumak için Azure AD B2C genişletme öğreticisi

Bu örnek öğreticide, Azure Active Directory (AD) B2C 'yi Strata 'nun [Maverics kimlik Orchestrator](https://www.strata.io/maverics-identity-orchestrator/)ile tümleştirmeyi öğrenin.
Maverics Identity Orchestrator, şirket içi uygulamaları korumak için Azure AD B2C genişletir. Herhangi bir kimlik sistemine bağlanır, kullanıcıları ve kimlik bilgilerini saydam biçimde geçirir, ilkeleri ve konfigürasyonları eşitler, kimlik doğrulama ve oturum yönetimini soyutlar. Strata kurumların kullanılması, uygulamaları yeniden yazmadan eski 'dan Azure AD B2C hızla geçiş yapabilir. Çözümün şöyle avantajları vardır:

- **Müşteri tek Sign-On (SSO) Şirket içi karma uygulamalar**: Azure AD B2C Maverics Identity Orchestrator ile müşteri SSO 'su destekler. Kullanıcılar, Azure AD B2C veya sosyal kimlik sağlayıcısı (IDP) ' de barındırılan hesaplarıyla oturum açabilirler. Maverics SSO 'yu, Symantec Sitedefteri gibi eski kimlik sistemleri tarafından geçmişte güvenliği sağlanmış olan uygulamalara genişletir.

- **Standartlara DAYALı SSO 'yu uygulamalara yeniden yazmadan Genişlet**: Kullanıcı erişimini yönetmek ve Maverics ıDENTITY Orchestrator SAML veya OIDC BAĞLAYıCıLARıYLA SSO 'yu etkinleştirmek için Azure AD B2C kullanın.

- **Kolay yapılandırma**: Azure AD B2C Maverics KIMLIK Orchestrator SAML veya OIDC bağlayıcılarını Azure AD B2C bağlamak için basit bir adım adım kullanıcı arabirimi sağlar.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- Azure aboneliğinize bağlı bir [Azure AD B2C kiracısı](./tutorial-create-tenant.md) .

- Maverics kimlik Orchestrator tarafından kullanılan gizli dizileri depolamak için bir [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) örneği. Azure AD B2C veya Basit Dizin Erişim Protokolü (LDAP) dizini veya veritabanı gibi diğer öznitelik sağlayıcılarına bağlanmak için kullanılır.

- Bir Azure sanal makinesinde yüklü ve çalışan bir [Maverics Identity Orchestrator](https://www.strata.io/maverics-identity-orchestrator/) örneği veya tercih ettiğiniz şirket içi sunucu. Yazılım alma ve yükleme ve yapılandırma belgelerine erişme hakkında daha fazla bilgi için, [Strata](https://www.strata.io/contact/) ile iletişim kurun

- Eski bir kimlik sisteminden Azure AD B2C geçiş yaptığınız şirket içi bir uygulama.

## <a name="scenario-description"></a>Senaryo açıklaması

Strata 'nın Maverics tümleştirmesi aşağıdaki bileşenleri içerir:

- **Azure AD B2C**: kullanıcının kimlik bilgilerini doğrulamaktan sorumlu olan yetkilendirme sunucusu. Kimliği doğrulanmış kullanıcılar Azure AD B2C dizininde depolanan yerel bir hesabı kullanarak şirket içi uygulamalara erişebilir.

- **Dış sosyal veya kurumsal IDP**: herhangi bir OpenID Connect sağlayıcısı, Facebook, Google veya GitHub olabilir. Azure AD B2C ile [dış IDPs](./technical-overview.md#external-identity-providers) kullanma hakkında bilgi için bkz..  

- **Strata 'Nın Maverics kimlik Orchestrator**: Kullanıcı oturum açma bilgilerini düzenleyen ve http üstbilgileri aracılığıyla uygulamaları saydam olarak bir şekilde geçiren hizmet.

Aşağıdaki mimari diyagram uygulamayı gösterir.

![Görüntü, karma uygulamalara erişimi etkinleştirmek için Strata Maverics ile Azure AD B2C tümleştirme mimarisini gösterir](./media/partner-strata/strata-architecture-diagram.png)

| Adımlar | Description |
|:-------|:---------------|
| 1. | Kullanıcı, şirket içi barındırılan uygulamaya erişmek için bir istek yapar. Maverics kimlik Orchestrator, Kullanıcı tarafından uygulamaya yapılan isteği bir proxy 'ye sağlar.|
| 2. | Orchestrator kullanıcının kimlik doğrulama durumunu denetler. Oturum belirteci almazsa veya sağlanan oturum belirteci geçersizse, kimlik doğrulaması için kullanıcıyı Azure AD B2C gönderir.|
| 3. | Azure AD B2C, kimlik doğrulama isteğini yapılandırılan sosyal IDP 'ye gönderir.|
| 4. | IDP, kullanıcıdan kimlik bilgileri için zorluk sorunlarını. IDP 'ye bağlı olarak, kullanıcının Multi-Factor Authentication (MFA) yapması gerekebilir.|
| 5. | IDP, kimlik doğrulama yanıtını Azure AD B2C geri gönderir. İsteğe bağlı olarak, Kullanıcı bu adım sırasında Azure AD B2C dizininde yerel bir hesap oluşturabilir.|
| 6. | Azure AD B2C, Kullanıcı isteğini Orchestrator uygulamasının Azure AD B2C kiracısındaki kaydı sırasında belirtilen uç noktaya gönderir.|
| 7. | Orchestrator, erişim ilkelerini değerlendirir ve uygulamaya iletilen HTTP üst bilgilerine dahil edilecek öznitelik değerlerini hesaplar. Bu adım sırasında Orchestrator, üstbilgi değerlerini doğru şekilde ayarlamak için gereken bilgileri almak üzere ek öznitelik sağlayıcılarına çağrı gösterebilir. Orchestrator, üst bilgi değerlerini ayarlar ve isteği uygulamaya gönderir.|
| 8. | Kullanıcının kimliği doğrulanır ve uygulamaya erişimi vardır.|

## <a name="get-maverics-identity-orchestrator"></a>Maverics kimlik Orchestrator 'ı al
Eski şirket içi uygulamanızı Azure AD B2C tümleştirmek için kullanacağınız yazılımı almak için, [Strata](https://www.strata.io/contact/)ile iletişim kurun. Yazılımı aldıktan sonra, Orchestrator 'a özgü önkoşulları öğrenmek ve gerekli yükleme ve yapılandırma adımlarını gerçekleştirmek için aşağıdaki adımları izleyin.

## <a name="configure-your-azure-ad-b2c-tenant"></a>Azure AD B2C kiracınızı yapılandırma

1. **Uygulamanızı kaydetme**

   a. Orchestrator 'ı Azure AD B2C kiracısına [uygulama olarak kaydedin](./tutorial-register-applications.md?tabs=app-reg-ga) .
   >[!Note]
   >Orchestrator örneğinizi yapılandırdığınızda kiracı adı ve tanımlayıcı, istemci KIMLIĞI, istemci gizli anahtarı, yapılandırılmış talepler ve yeniden yönlendirme URI 'SI gerekir.

   b. Uygulamalarınıza Microsoft MS Graph API izinleri verin. Uygulamanız aşağıdaki izinlere sahip olacak: `offline_access` , `openid` .

   c. Uygulamanız için bir yeniden yönlendirme URI 'SI ekleyin. Bu URI, `oauthRedirectURL` Orchestrator Azure AD B2C bağlayıcı yapılandırmanızın parametresiyle eşleşir, örneğin, `https://example.com/oidc-endpoint` .

2. **Kullanıcı akışı oluşturma**: [kaydolma ve oturum açma Kullanıcı akışı](./tutorial-create-user-flows.md)oluşturma.

3. **IDP ekleme**: Kullanıcı hesabını yerel bir hesap veya sosyal ya da kurumsal [IDP](./add-identity-provider.md)ile oturum açmak için seçin.

4. **Kullanıcı özniteliklerini tanımlayın**: kaydolma sırasında toplanacak öznitelikleri tanımlayın.

5. **Uygulama taleplerini belirtin**: Orchestrator örneğiniz aracılığıyla uygulamaya döndürülecek öznitelikleri belirtin. Orchestrator, Azure AD B2C tarafından döndürülen taleplerden öznitelikleri kullanır ve LDAP dizinleri ve veritabanları gibi diğer bağlı kimlik sistemlerinden ek öznitelikler alabilir. Bu öznitelikler HTTP üst bilgilerinde ayarlanır ve şirket içi şirket içi uygulamaya gönderilir.

## <a name="configure-maverics-identity-orchestrator"></a>Maverics kimlik Orchestrator 'ı yapılandırma

Aşağıdaki bölümlerde, Orchestrator örneğinizi yapılandırmak için gereken adımlarda size yol göstereceğiz. Ek destek ve belgeler için [Strata](https://www.strata.io/contact/)ile iletişim kurun.

### <a name="maverics-identity-orchestrator-server-requirements"></a>Maverics Identity Orchestrator sunucusu gereksinimleri

Orchestrator örneğinizi, şirket içinde ya da Azure, AWS veya GCP gibi sağlayıcıya göre ortak bir bulut altyapısında çalıştırabilirsiniz.

- OS: REHL 7,7 veya üzeri, CentOS 7 +

- Disk: 10 GB (küçük)

- Bellek: 16 GB

- Bağlantı noktaları: 22 (SSH/SCP), 443, 80

- Yükler/yönetim görevleri için kök erişimi

- Maverics Identity Orchestrator, altında Kullanıcı olarak çalışır `maverics``systemd`

- Azure AD kiracınıza ulaşabilme olanağı sunan Maverics kimlik Orchestrator 'ı barındıran sunucudan ağ çıkış.

### <a name="install-maverics-identity-orchestrator"></a>Maverics kimlik Orchestrator 'ı yükler

1. En son Maverics RPM paketini edinin. Paketi, Maverics yüklemek istediğiniz sisteme yerleştirin. Dosyayı uzak bir konağa kopyalıyorsanız, [SCP](https://www.ssh.com/ssh/scp/) kullanışlı bir araçtır.

2. Maverics paketini yüklemek için, yerine dosya adınızı değiştirerek aşağıdaki komutu çalıştırın `maverics.rpm` .

   `sudo rpm -Uvf maverics.rpm`

   Varsayılan olarak, Maverics `/usr/local/bin` dizine yüklenir.

3. Maverics yükledikten sonra, altında bir hizmet olarak çalışacaktır `systemd` .  Maverics hizmetinin çalıştığını doğrulamak için şu komutu çalıştırın:

   `sudo service maverics status`

  Orchestrator yüklemesi başarılı olduysa şuna benzer bir ileti görmeniz gerekir:

```
Redirecting to /bin/systemctl status maverics.service
  maverics.service - Maverics
  Loaded: loaded (/etc/systemd/system/maverics.service; enabled; vendor preset: disabled)
  Active: active (running) since Thu 2020-08-13 16:48:01 UTC; 24h ago
  Main PID: 330772 (maverics)
  Tasks: 5 (limit: 11389)
  Memory: 14.0M
  CGroup: /system.slice/maverics.service
          └─330772 /usr/local/bin/maverics --config /etc/maverics/maverics.yaml
  ```

4. Maverics hizmeti başlatılamazsa, sorunu araştırmak için aşağıdaki komutu yürütün:

   `journalctl --unit=maverics.service --reverse`

   En son günlük girdisi çıktının başlangıcında görüntülenir.

Maverics yükledikten sonra, varsayılan `maverics.yaml` Dosya `/etc/maverics` dizininde oluşturulur.

Uygulamanızı korumak için Orchestrator 'ı yapılandırın. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/?OCID=AID2100131_SEM_bf7bdd52c7b91367064882c1ce4d83a9:G:s&ef_id=bf7bdd52c7b91367064882c1ce4d83a9:G:s&msclkid=bf7bdd52c7b91367064882c1ce4d83a9)Azure AD B2C, depola ve gizli dizileri alma ile tümleştirin. Orchestrator 'ın yapılandırmasını okuması gereken konumu tanımlayın.

### <a name="supply-configuration-using-environment-variables"></a>Ortam değişkenlerini kullanarak yapılandırma sağlama

Ortam değişkenleri aracılığıyla Orchestrator örnekleriniz için yapılandırma sağlayın.

`MAVERICS_CONFIG`

Bu ortam değişkeni, YAML yapılandırma dosyalarının ne olduğunu ve başlangıç veya yeniden başlatma sırasında nerede bulunacağını bildiren Orchestrator örneğine bildirir. Ortam değişkenini ' de ayarlayın `/etc/maverics/maverics.env` .

### <a name="create-the-orchestrators-tls-configuration"></a>Orchestrator TLS yapılandırmasını oluşturma

`tls`İçindeki alan, `maverics.yaml` Orchestrator örneğinizin kullanacağı Aktarım Katmanı Güvenlik yapılandırmalarının bildirimini yapar. Bağlayıcılar TLS nesnelerini ve Orchestrator sunucusunu kullanabilir.

`maverics`Anahtar, Orchestrator sunucusu için ayrılmıştır. Diğer tüm anahtarlar kullanılabilir ve belirli bir bağlayıcıya bir TLS nesnesi eklemek için kullanılabilir.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```

### <a name="configure-the-azure-ad-b2c-connector"></a>Azure AD B2C bağlayıcısını yapılandırma

Düzenleyiciler, kimlik doğrulama ve öznitelik sağlayıcılarıyla tümleştirilebilen Bağlayıcılar kullanır. Bu durumda, bu uygulama ağ geçidi, Azure AD B2C bağlayıcısını hem kimlik doğrulaması hem de öznitelik sağlayıcısı olarak kullanır. Azure AD B2C, kimlik doğrulaması için sosyal IDP 'yi kullanır ve ardından Orchestrator 'a bir öznitelik sağlayıcısı görevi görür ve HTTP başlıklarında talepler kümesi özniteliklerini ileterek.  

Bu bağlayıcının yapılandırması, Azure AD B2C kiracısında kayıtlı olan uygulamaya karşılık gelir.

1. Kiracınızdaki uygulama yapılandırmadan istemci KIMLIĞI, gizli anahtar ve yeniden yönlendirme URI 'sini kopyalayın.

2. Bağlayıcınıza, burada gösterildiği gibi bir ad verin `azureADB2C` ve bağlayıcıyı olarak ayarlayın `type` `azure` . Bu değer aşağıdaki diğer yapılandırma parametrelerinde kullanıldığı için bağlayıcı adını buraya göz atın.

3. Bu tümleştirme için, `authType` olarak ayarlanmalıdır `oidc` .

4. Adım 1 ' de kopyaladığınız istemci KIMLIĞINI parametre değeri olarak ayarlayın `oauthClientID` .

5. Adım 1 ' de kopyaladığınız istemci gizliliğini parametrenin değeri olarak ayarlayın `oauthClientSecret` .

6. Adım 1 ' de kopyaladığınız yeniden yönlendirme URI 'sini parametre değeri olarak ayarlayın `oauthRedirectURL` .

7. Azure AD B2C OıDC Bağlayıcısı, URL 'Ler ve imzalama anahtarları dahil olmak üzere meta verileri saptamak için iyi bilinen OıDC uç noktasını kullanır. Değerini `oidcWellKnownURL` kiracınızın uç noktasına ayarlayın.

```yaml
connectors:
  name: azureADB2C
  type: azure
  oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
  oauthRedirectURL: https://example.com/oidc-endpoint
  oauthClientID: <azureADB2CClientID>
  oauthClientSecret: <azureADB2CClientSecret>
  authType: oidc
```

### <a name="define-azure-ad-b2c-as-your-authentication-provider"></a>Kimlik doğrulama sağlayıcınız olarak Azure AD B2C tanımlama

Bir kimlik doğrulama sağlayıcısı, uygulama kaynak isteği kapsamında geçerli bir oturum sunulmayan bir kullanıcı için kimlik doğrulamanın nasıl yapılacağını belirler. Azure AD B2C kiracınızdaki yapılandırma, bir kullanıcının kimlik bilgilerini nasıl zorluk kullandığını ve ek kimlik doğrulama ilkeleri nasıl uygulanacağını belirler. Örneğin, kimlik doğrulama işlemini tamamlamaya yönelik ikinci bir faktör gerektirmek ve kimlik doğrulama başarılı olduktan sonra Orchestrator App Gateway 'e hangi taleplerin döndürüleceğine karar vermek için.

Öğesinin değeri, `authProvider` bağlayıcının `name` değeriyle eşleşmelidir.

```yaml
authProvider: azureADB2C
```

### <a name="protect-your-on-premises-app-with-an-orchestrator-app-gateway"></a>Şirket içi uygulamanızı Orchestrator App Gateway ile koruma

Orchestrator App Gateway yapılandırması, Azure AD B2C uygulamanızı nasıl koruduğumuzu ve kullanıcıların uygulamaya nasıl erişmeli olduğunu bildirir.

1. Uygulama ağ geçidiniz için bir ad oluşturun. Uygulamanız için bir tanımlayıcı olarak kolay bir ad veya tam nitelikli ana bilgisayar adı kullanabilirsiniz.

2. Öğesini ayarlayın `location` . Burada örnek, uygulamanın kökünü kullanır, `/` ancak uygulamanızın herhangi BIR URL yolu olabilir.

3. `upstream`Konak: bağlantı noktası kuralı: ' nı kullanarak korumalı uygulamayı tanımlayın `https://example.com:8080` .

4. Hata ve yetkisiz sayfalar için değerleri ayarlayın.

5. Uygulamaya kimlik doğrulaması kurmak ve uygulamaya erişimi denetlemek için sağlanması gereken HTTP üst bilgi adlarını ve öznitelik değerlerini tanımlayın. Üst bilgi adları rasgele ve genellikle uygulamanın yapılandırmasına karşılık gelir. Öznitelik değerleri, bunları sağlayan bağlayıcı tarafından gösterilemez. Aşağıdaki örnekte, Azure AD B2C döndürülen değerler, `azureADB2C` sonekin, örneğin gerekli değeri içeren özniteliğin adı olduğu bağlayıcı adı ile öneklidir `given_name` .

6. Değerlendirilecek ve Zorlanmış ilkeleri ayarlayın. Üç eylem tanımlanmıştır: `allowUnauthenticated` , `allowAnyAuthenticated` , ve `allowIfAny` . Her eylem bir ile ilişkilendirilir `resource` ve ilke bu şekilde değerlendirilir `resource` .

>[!NOTE]
>Her ikisi de `headers` `policies` varsayılan özellikleri önemli ölçüde geliştiren rastgele mantık uygulamak için JavaScript veya GoLang hizmet uzantıları kullanın.

```yaml
appgateways:
  - name: Sonar
    location: /
    upstream: https://example.com:8080
    errorPage: https://example.com:8080/sonar/error
    unauthorizedPage: https://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

### <a name="use-azure-key-vault-as-your-secrets-provider"></a>Gizli dizi sağlayıcınız olarak Azure Key Vault kullanın

Orchestrator 'ın Azure AD B2C ve diğer tüm kimlik sistemine bağlanmak için kullandığı gizli dizileri güvence altına almak önemlidir. Maverics, gizli dizileri varsayılan olarak düz metin olarak yüklemeyi sağlar `maverics.yaml` , ancak bu öğreticide gizli dizi sağlayıcısı olarak Azure Key Vault kullanacaksınız.

Orchestrator örneğinizin gizli bir sağlayıcı olarak kullanacağı [Yeni bir Key Vault oluşturmak](../key-vault/secrets/quick-create-portal.md) için yönergeleri izleyin. Kasanıza sırlarınızı ekleyin ve her gizli dizi için verilen ' i buraya göz atın `SECRET NAME` . Örneğin, `AzureADB2CClientSecret`.

Bir değeri bir yapılandırma dosyasında gizli olarak bildirmek için `maverics.yaml` , parolayı açılı parantezle sarın:

```yaml
connectors:
  - name: AzureADB2C
    type: azure
    oauthClientID: <AzureADB2CClientID>
    oauthClientSecret: <AzureADB2CClientSecret>
```

Açılı ayraç içinde belirtilen değer, `SECRET NAME` Azure Key Vault verilen parolaya karşılık gelmelidir.

Azure Key Vault parolaları yüklemek için, dosyadaki ortam değişkenini `MAVERICS_SECRET_PROVIDER` `/etc/maverics/maverics.env` aşağıdaki model kullanılarak azure-credentials.jsüzerinde bulunan kimlik bilgileriyle ayarlayın:

`MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'`

### <a name="put-everything-together"></a>Her şeyi birlikte yerleştir

Yukarıda özetlenen yapılandırmaları tamamladığınızda Orchestrator yapılandırmasının nasıl görüneceği aşağıda gösterilmiştir.

```yaml
version: 0.4.2
listenAddress: ":443"
tls:
  maverics:
    certFile: certs/maverics.crt
    keyFile: certs/maverics.key

authProvider: azureADB2C

connectors:
  - name: azureADB2C
    type: azure
    oidcWellKnownURL: https://<tenant name>.b2clogin.com/<tenant name>.onmicrosoft.com/B2C_1_login/v2.0/.well-known/openid-configuration
    oauthRedirectURL: https://example.com/oidc-endpoint
    oauthClientID: <azureADB2CClientID>
    oauthClientSecret: <azureADB2CClientSecret>
    authType: oidc

appgateways:
  - name: Sonar
    location: /
    upstream: http://example.com:8080
    errorPage: http://example.com:8080/sonar/accessdenied
    unauthorizedPage: http://example.com:8080/sonar/accessdenied

    headers:
      SM_USER: azureADB2C.sub
      firstname: azureADB2C.given_name
      lastname: azureADB2C.family_name

    policies:
      - resource: ~ \.(jpg|png|ico|svg)
        allowUnauthenticated: true
      - resource: /
        allowAnyAuthenticated: true
      - resource: /sonar/daily_deals
        allowIfAny:
          azureADB2C.customAttribute: Rewards Member
```

## <a name="test-the-flow"></a>Akışı test edin

1. Şirket içi uygulama URL 'sine gidin `https://example.com/sonar/dashboard` .

2. Orchestrator, Kullanıcı akışınız içinde yapılandırdığınız sayfaya yeniden yönlendirmelidir.

3. Sayfadaki listeden IDP 'yi seçin.

4. IDP 'ye yeniden yönlendirildikten sonra, bu IDP için gerekliyse MFA belirteci dahil olmak üzere kimlik bilgilerinizi istenen şekilde sağlayın.

5. Başarıyla kimlik doğrulamasından geçtikten sonra, uygulama isteğini Orchestrator yeniden yönlendirme URI 'sine ileten Azure AD B2C 'a yönlendirilmelisiniz.

6. Orchestrator ilkeleri değerlendirir, üstbilgileri hesaplar ve kullanıcıyı yukarı akış uygulamasına gönderir.  

7. İstenen uygulamayı görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
