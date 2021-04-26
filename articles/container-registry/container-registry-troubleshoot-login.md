---
title: Kayıt defterinde oturum açma sorunlarını giderme
description: Azure Container Registry 'de oturum açarken karşılaşılan sorunların belirtileri, nedenleri ve çözümleri
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 47186cc8256836e5367ecee520787b67662eb42f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780740"
---
# <a name="troubleshoot-registry-login"></a>Kayıt defteri oturum açma sorunlarını giderme

Bu makale, bir Azure Container Registry 'de oturum açarken karşılaşabileceğiniz sorunları gidermenize yardımcı olur. 

## <a name="symptoms"></a>Belirtiler

Aşağıdakilerden birini veya daha fazlasını içerebilir:

* `docker login`, `az acr login` Veya her ikisini kullanarak kayıt defterine oturum açılamıyor
* Kayıt defterine oturum açılamıyor ve hata alıyorsunuz ya da `unauthorized: authentication required``unauthorized: Application not registered with AAD`
* Kayıt defterine oturum açılamıyor ve Azure CLı hatası alıyorsunuz `Could not connect to the registry login server`
* Görüntüler gönderilemiyor veya çekilemiyor ve Docker hatası alıyorsunuz `unauthorized: authentication required`
* Azure Kubernetes hizmeti, Azure DevOps veya başka bir Azure hizmeti ile kayıt defterine erişilemiyor
* Kayıt defterine erişilemiyor ve hata alıyorsunuz `Error response from daemon: login attempt failed with status: 403 Forbidden` -bkz. [kayıt defteri ile ağ sorunlarını giderme](container-registry-troubleshoot-access.md)
* Azure portal kayıt defteri ayarlarına erişilemiyor veya Azure CLı kullanarak kayıt defterini yönetme

## <a name="causes"></a>Nedenler

* Docker, ortamınızda düzgün yapılandırılmamış- [çözümünüz](#check-docker-configuration)
* Kayıt defteri yok veya ad yanlış- [çözüm](#specify-correct-registry-name)
* Kayıt defteri kimlik bilgileri geçerli değil- [çözüm](#confirm-credentials-to-access-registry)
* Kimlik bilgileri gönderim, çekme veya Azure Resource Manager işlemler için yetkilendirilmemiş- [çözüm](#confirm-credentials-are-authorized-to-access-registry)
* Kimlik bilgileri zaman aşımına uğradı- [çözüm](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Daha fazla tanılama 

Kayıt defteri ortamının sistem durumu ve isteğe bağlı olarak bir hedef kayıt defterine erişim hakkında daha fazla bilgi almak için [az ACR Check-Health](/cli/azure/acr#az_acr_check_health) komutunu çalıştırın. Örneğin, Docker yapılandırma hatalarını veya Azure Active Directory oturum açma sorunlarını tanılayın. 

Bkz. komut örnekleri için [Azure Container Registry 'nin sistem durumunu denetleme](container-registry-check-health.md) . Hatalar raporlandıysanız, önerilen çözümler için [hata başvurusunu](container-registry-health-error-reference.md) ve aşağıdaki bölümleri gözden geçirin.

Wih Azure Kubernetes hizmetini kullanarak sorun yaşıyorsanız, kayıt defterinin AKS kümesinden erişilebilir olduğunu doğrulamak için [az aks Check-ACR](/cli/azure/aks#az_aks_check_acr) komutunu çalıştırın.

> [!NOTE]
> Bazı kimlik doğrulama veya yetkilendirme hataları, kayıt defteri erişimini önleyen güvenlik duvarı veya ağ yapılandırması varsa da oluşabilir. Bkz. [kayıt defteri ile ağ sorunlarını giderme](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Olası çözümler

### <a name="check-docker-configuration"></a>Docker yapılandırmasını denetle

Çoğu Azure Container Registry kimlik doğrulama akışı, görüntüleri gönderme ve çekme gibi işlemler için kayıt defterinizde kimlik doğrulayabilmeniz için yerel bir Docker yüklemesi gerektirir. Docker CLı istemcisinin ve Daemon 'ın (Docker Engine) ortamınızda çalıştığını doğrulayın. Docker Client sürüm 18,03 veya sonraki bir sürümü gereklidir.

İlgili bağlantılar:

* [Kimlik doğrulamasına genel bakış](container-registry-authentication.md#authentication-options)
* [Kapsayıcı kayıt defteri SSS](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Doğru kayıt defteri adını belirtin

Kullanırken `docker login` , kayıt defterinin *myregistry.azurecr.io* gibi tam oturum açma sunucusu adını sağlayın. Yalnızca küçük harfler kullandığınızdan emin olun. Örnek:

```console
docker login myregistry.azurecr.io
```

Azure Active Directory kimlikle [az ACR oturum açma](/cli/azure/acr#az_acr_login) kullanırken önce [Azure CLI 'de oturum açın](/cli/azure/authenticate-azure-cli)ve kayıt defterinin Azure Kaynak adını belirtin. Kaynak adı, *kayıt defteri oluşturulduğunda (bir* etki alanı soneki olmadan) girilen addır. Örnek:

```azurecli
az acr login --name myregistry
```

İlgili bağlantılar:

* [az ACR oturum açma işlemi başarılı ancak Docker şu hatayla başarısız oldu: kimlik doğrulaması gerekli](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)

### <a name="confirm-credentials-to-access-registry"></a>Kayıt defterine erişmek için kimlik bilgilerini onaylayın

Senaryonuz için kullandığınız kimlik bilgilerinin geçerliliğini denetleyin veya size bir kayıt defteri sahibi tarafından sağlanmış olursunuz. Olası bazı sorunlar:

* Active Directory hizmet sorumlusu kullanıyorsanız, Active Directory kiracısında doğru kimlik bilgilerini kullandığınızdan emin olun:
  * Kullanıcı adı-hizmet sorumlusu uygulama KIMLIĞI ( *ISTEMCI kimliği* olarak da bilinir)
  * Parola-hizmet sorumlusu parolası ( *istemci gizli anahtarı* da denir)
* Kayıt defterine erişmek için Azure Kubernetes hizmeti veya Azure DevOps gibi bir Azure hizmeti kullanıyorsanız, hizmetinizin kayıt defteri yapılandırmasını onaylayın. 
* `az acr login` `--expose-token` Docker Daemon kullanılmadan kayıt defteri oturum açma imkanı sağlayan seçeneğiyle çalıştırdıysanız, Kullanıcı adıyla kimlik doğrulaması gerçekleştirdiğinizden emin olun `00000000-0000-0000-0000-000000000000` .
* Kayıt defteriniz [anonim çekme erişimi](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)için yapılandırıldıysa, önceki bir Docker oturumundan depolanan mevcut Docker kimlik bilgileri anonim erişimi engelleyebilir. `docker logout`Kayıt defterinde anonim çekme işlemini denemeden önce ' i çalıştırın.

İlgili bağlantılar:

* [Kimlik doğrulamasına genel bakış](container-registry-authentication.md#authentication-options)
* [Azure AD ile bireysel oturum açma](container-registry-authentication.md#individual-login-with-azure-ad)
* [Hizmet sorumlusu ile oturum açma](container-registry-auth-service-principal.md)
* [Yönetilen kimlikle oturum açma](container-registry-authentication-managed-identity.md)
* [Depo kapsamlı belirteç ile oturum açma](container-registry-repository-scoped-permissions.md)
* [Yönetici hesabıyla oturum açın](container-registry-authentication.md#admin-account)
* [Azure AD kimlik doğrulaması ve yetkilendirme hatası kodları](../active-directory/develop/reference-aadsts-error-codes.md)
* [az ACR oturum açma](/cli/azure/acr#az_acr_login) başvurusu

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Kimlik bilgilerinin kayıt defterine erişim yetkisi olduğunu onaylayın

`AcrPull`Kayıt defterinden görüntüleri çekmek için Azure rolü veya `AcrPush` görüntüleri göndermek için rol gibi kimlik bilgileriyle ilişkili kayıt defteri izinlerini onaylayın. 

Azure CLı kullanarak portaldaki bir kayıt defterine veya kayıt defteri yönetimine erişim, `Reader` Azure Resource Manager işlemleri gerçekleştirmek için en azından rol ya da eşdeğer izinleri gerektirir.

İzinleriniz kısa süre önce portala kayıt defterine erişime izin verecek şekilde değiştirilirse, eski tarayıcı önbelleğinden veya tanımlama bilgilerinden kaçınmak için tarayıcınızda bir ınbilito veya özel oturum denemeniz gerekebilir.

Rol atamaları eklemek veya kaldırmak için, sizin veya bir kayıt defteri sahibinin abonelikte yeterli ayrıcalıklara sahip olması gerekir.

İlgili bağlantılar:

* [Azure rolleri ve izinleri-Azure Container Registry](container-registry-roles.md)
* [Depo kapsamlı belirteç ile oturum açma](container-registry-repository-scoped-permissions.md)
* [Azure portalını kullanarak Azure rol ataması ekleme veya kaldırma](../role-based-access-control/role-assignments-portal.md)
* [Kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md)
* [Yeni uygulama gizli dizisi oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD kimlik doğrulaması ve yetkilendirme kodları](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Kimlik bilgilerinin dolmadığından emin olun

Belirteçler ve Active Directory kimlik bilgileri, tanımlı dönemlerden sonra zaman alabilir ve kayıt defteri erişimini önler. Erişimi etkinleştirmek için kimlik bilgilerinin sıfırlanması veya yeniden oluşturulması gerekebilir.

* Kayıt defteri oturum açma için tek bir AD kimliği, yönetilen bir kimlik veya hizmet sorumlusu kullanıyorsanız, AD belirtecinin süresi 3 saat sonra dolar. Kayıt defterine tekrar oturum açın.  
* Bir AD hizmet sorumlusu, zaman aşımına uğradı bir istemci gizli anahtarı ile kullanılıyorsa, abonelik sahibi veya hesap yöneticisinin kimlik bilgilerini sıfırlaması veya yeni bir hizmet sorumlusu oluşturması gerekir.
* [Depo kapsamlı bir belirteç](container-registry-repository-scoped-permissions.md)kullanılıyorsa, bir kayıt defteri sahibinin bir parolayı sıfırlaması veya yeni bir belirteç oluşturması gerekebilir.

İlgili bağlantılar:

* [Hizmet sorumlusu kimlik bilgilerini Sıfırla](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset)
* [Belirteç parolalarını yeniden oluştur](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Azure AD ile bireysel oturum açma](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Gelişmiş sorun giderme

Kayıt defterinde [kaynak günlüklerinin toplanması](container-registry-diagnostics-audit-logs.md) etkinse, Kirınterregistryloginevents günlüğünü gözden geçirin. Bu günlük, gelen kimlik ve IP adresi dahil olmak üzere kimlik doğrulama olaylarını ve durumunu depolar. [Kayıt defteri kimlik doğrulama hatalarıyla](container-registry-diagnostics-audit-logs.md#registry-authentication-failures)ilgili günlüğü sorgulayın. 

İlgili bağlantılar:

* [Tanılama değerlendirmesi ve denetimi günlükleri](container-registry-diagnostics-audit-logs.md)
* [Kapsayıcı kayıt defteri SSS](container-registry-faq.md)
* [Azure Container Registry için en iyi yöntemler](container-registry-best-practices.md)

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada çözemezseniz, aşağıdaki seçeneklere bakın.

* Diğer kayıt defteri sorunlarını giderme konuları şunları içerir:
  * [Kayıt defteri ile ağ sorunlarını giderme](container-registry-troubleshoot-access.md)
  * [Kayıt defteri performansının sorunlarını giderme](container-registry-troubleshoot-performance.md)
* [Topluluk desteği](https://azure.microsoft.com/support/community/) seçenekleri
* [Microsoft Soru-Cevap](/answers/products/)
* Sağladığınız bilgilere bağlı olarak [bir destek bileti açın](https://azure.microsoft.com/support/create-ticket/) . kayıt defterinizde kimlik doğrulama hatalarıyla ilgili hızlı bir tanılama çalıştırılabilir