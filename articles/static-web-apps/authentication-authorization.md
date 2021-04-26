---
title: Azure statik Web Apps kimlik doğrulaması ve yetkilendirme
description: Statik uygulamanızı güvenli hale getirmek için farklı yetkilendirme sağlayıcıları kullanmayı öğrenin.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.openlocfilehash: 29821733b73717634aa8f0ab72270f058ffd3ddc
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309399"
---
# <a name="authentication-and-authorization-for-azure-static-web-apps-preview"></a>Azure Static Web Apps Önizlemesi için kimlik doğrulaması ve yetkilendirme

Azure statik Web Apps, kimlik doğrulamasını aşağıdaki sağlayıcılarla yöneterek kimlik doğrulama deneyimini kolaylaştırır:

- Azure Active Directory
- GitHub
- Facebook
- Google<sup>1</sup>
- Twitter

Sağlayıcıya özgü [davetler](#invitations) kullanıcıları rollerle ilişkilendirir ve yetkili kullanıcılara, _staticwebapp.config.js_ dosyasında tanımlanan kurallara göre [yollara](routes.md) erişim verilir.

Tüm kimlik doğrulama sağlayıcıları varsayılan olarak etkindir. Bir kimlik doğrulama sağlayıcısını kısıtlamak için özel bir yol kuralıyla [erişimi engelleyin](#block-an-authorization-provider) .

Kimlik doğrulama ve yetkilendirme konuları, yönlendirme kavramlarıyla önemli ölçüde örtüşüyor. Bu makaleyle birlikte [yönlendirme kılavuzunu](routes.md) okuduğunuzdan emin olun.

## <a name="roles"></a>Roller

Statik bir Web uygulamasına erişen her Kullanıcı bir veya daha fazla role aittir. Kullanıcıların ait olduğu iki yerleşik rol vardır:

- **anonim**: tüm kullanıcılar _anonim_ role otomatik olarak aittir.
- **kimliği doğrulanan**: oturum açan tüm kullanıcılar _kimliği doğrulanmış_ role aittir.

Yerleşik rollerin ötesinde, yeni roller oluşturabilir, bunları davet aracılığıyla kullanıcılara atayabilir ve dosyadaki _staticwebapp.config.js_ bunlara başvurabilirsiniz.

## <a name="role-management"></a>Rol yönetimi

### <a name="add-a-user-to-a-role"></a>Bir role Kullanıcı ekleme

Web sitenize Kullanıcı eklemek için, kullanıcıları belirli rollerle ilişkilendirmenizi sağlayan davetler oluşturursunuz. Roller dosyada _staticwebapp.config.js_ tanımlanır ve saklanır.

<a name="invitations" id="invitations"></a>

#### <a name="create-an-invitation"></a>Davet oluşturma

Davetler, bireysel yetkilendirme sağlayıcılarına özgüdür, bu nedenle destekedilecek sağlayıcıları seçerken uygulamanızın ihtiyaçlarını göz önünde bulundurun. Bazı sağlayıcılar kullanıcının e-posta adresini açığa çıkarır, diğerleri ise yalnızca sitenin Kullanıcı adını sağlar.

<a name="provider-user-details" id="provider-user-details"></a>

| Yetkilendirme sağlayıcısı | Bir kullanıcının |
| ---------------------- | ---------------- |
| Azure Active Directory | e-posta adresi    |
| Facebook               | e-posta adresi    |
| GitHub                 | username         |
| Google<sup>1</sup>     | e-posta adresi    |
| Twitter                | username         |

1. [Azure Portal](https://portal.azure.com)bir statik Web Apps kaynağına gidin.
1. _Ayarlar_ altında **rol yönetimi**' ne tıklayın.
1. **Davet et** düğmesine tıklayın.
1. Seçenekler listesinden bir _Yetkilendirme sağlayıcısı_ seçin.
1. _Davetli Ayrıntılar_ kutusuna alıcının Kullanıcı adı veya e-posta adresini ekleyin.
   - GitHub ve Twitter için Kullanıcı adını girersiniz. Tüm diğerleri için alıcının e-posta adresini girin.
1. _Etki alanı_ açılır listesinden statik sitenizin etki alanını seçin.
   - Seçtiğiniz etki alanı, davette görüntülenen etki alanıdır. Siteniz ile ilişkili özel bir etki alanınız varsa, muhtemelen özel etki alanını seçmek isteyeceksiniz.
1. _Rol_ kutusuna rol adlarının virgülle ayrılmış bir listesini ekleyin.
1. Davetin geçerli kalmasını istediğiniz en fazla saat sayısını girin.
   - Olası en yüksek sınır 168 saattir. Bu değer 7 gündür.
1. **Oluştur** düğmesine tıklayın.
1. Bağlantıyı _davet et bağlantı_ kutusundan kopyalayın.
1. Uygulamanıza erişim verdiğiniz kişiye davet bağlantısını e-posta ile gönderin.

Kullanıcı davetteki bağlantıya tıkladığında, bunlara karşılık gelen hesabıyla oturum açması istenir. Başarıyla oturum açtıktan sonra, Kullanıcı seçili rollerle ilişkilendirilir.

> [!CAUTION]
> Yol kurallarınızın seçtiğiniz kimlik doğrulama sağlayıcılarıyla çakışmadığından emin olun. Bir sağlayıcının yol kuralıyla engellenmesi, kullanıcıların davetleri kabul etmesini engeller.

### <a name="update-role-assignments"></a>Rol atamalarını Güncelleştir

1. [Azure Portal](https://portal.azure.com)bir statik Web Apps kaynağına gidin.
1. _Ayarlar_ altında **rol yönetimi**' ne tıklayın.
1. Listede kullanıcıya tıklayın.
1. _Rol_ kutusundaki rol listesini düzenleyin.
1. **Güncelleştir** düğmesine tıklayın.

### <a name="remove-user"></a>Kullanıcı kaldırma

1. [Azure Portal](https://portal.azure.com)bir statik Web Apps kaynağına gidin.
1. _Ayarlar_ altında **rol yönetimi**' ne tıklayın.
1. Listeden kullanıcıyı bulun.
1. Kullanıcının satırındaki onay kutusunu işaretleyin.
1. **Sil** düğmesine tıklayın.

Bir kullanıcıyı kaldırdığınızda, aşağıdaki öğeleri aklınızda bulundurun:

1. Bir kullanıcının kaldırılması, izinlerini geçersiz kılar.
1. Dünya genelindeki yayma birkaç dakika sürebilir.
1. Kullanıcı uygulamaya geri eklendiyse, [ `userId` değişiklikler](user-information.md).

## <a name="remove-personal-identifying-information"></a>Kişisel tanımlama bilgilerini Kaldır

Bir uygulamaya son kullanıcı olarak izin verdiğinizde uygulamanın, kimlik sağlayıcısına bağlı olarak e-posta adresinize veya Kullanıcı adınızla erişimi vardır. Bu bilgiler sağlandığında, uygulamanın sahibi kişisel olarak tanımlama bilgilerinin nasıl yönetileceğine karar verir.

Son kullanıcıların, bu bilgileri sistemlerinden iptal etmek için ayrı Web uygulamalarının yöneticileriyle iletişim kurabilmesi gerekir.

Azure statik Web Apps platformundan kişisel tanımlama bilgilerini kaldırmak ve platformun gelecekteki isteklerde bu bilgileri sağlamamasını engellemek için, URL 'YI kullanarak bir istek gönderebilirsiniz:

```url
https://identity.azurestaticapps.net/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

Platformun bu bilgileri tek tek uygulamalara gelecek isteklerde sağlamamasını engellemek için aşağıdaki URL 'ye bir istek gönder:

```url
https://<WEB_APP_DOMAIN_NAME>/.auth/purge/<AUTHENTICATION_PROVIDER_NAME>
```

## <a name="system-folder"></a>Sistem klasörü

Azure statik Web Apps, `/.auth` yetkilendirmeyle Ilgili API 'lere erişim sağlamak için sistem klasörünü kullanır. Klasörü altındaki yolların herhangi birini `/.auth` doğrudan son kullanıcılara sunmak yerine, kolay URL 'ler oluşturmak için [yönlendirme kuralları](routes.md) oluşturmayı düşünün.

## <a name="login"></a>Oturum aç

Sağlayıcıya özgü oturum açma yolunu bulmak için aşağıdaki tabloyu kullanın.

| Yetkilendirme sağlayıcısı | Oturum açma yolu             |
| ---------------------- | ----------------------- |
| Azure Active Directory | `/.auth/login/aad`      |
| Facebook               | `/.auth/login/facebook` |
| GitHub                 | `/.auth/login/github`   |
| Google<sup>1</sup>     | `/.auth/login/google`   |
| Twitter                | `/.auth/login/twitter`  |

Örneğin, GitHub ile oturum açmak için aşağıdaki kod parçacığına benzer bir oturum açma bağlantısı ekleyebilirsiniz:

```html
<a href="/.auth/login/github">Login</a>
```

Birden fazla sağlayıcıyı desteklemeyi seçtiyseniz, Web sitenizde her biri için sağlayıcıya özgü bir bağlantı oluşturmanız gerekir.

Varsayılan bir sağlayıcıyı _/login_ gibi kolay bir yola eşlemek için bir [yol kuralı](./configuration.md#routes) kullanabilirsiniz.

```json
{
  "route": "/login",
  "redirect": "/.auth/login/github"
}
```

### <a name="post-login-redirect"></a>Oturum açma sonrası yeniden yönlendirme

Bir kullanıcının oturum açtıktan sonra belirli bir sayfaya dönmesini istiyorsanız sorgu dizesi parametresinde tam nitelikli bir URL sağlayın `post_login_redirect_uri` .

Örnek:

```html
<a href="/.auth/login/github?post_login_redirect_uri=https://zealous-water.azurestaticapps.net/success">Login</a>
```

## <a name="logout"></a>Oturumu Kapat

`/.auth/logout`Rota, kullanıcıları Web sitesinden dışarı kaydeder. Aşağıdaki örnekte gösterildiği gibi, kullanıcının oturum açmasını sağlamak için site gezintisine bir bağlantı ekleyebilirsiniz.

```html
<a href="/.auth/logout">Log out</a>
```

_/Logout_ gibi kolay bir yol eşlemek için bir [yol kuralı](./configuration.md#routes) kullanabilirsiniz.

```json
{
  "route": "/logout",
  "redirect": "/.auth/logout"
}
```

### <a name="post-logout-redirect"></a>Kapatma sonrası yeniden yönlendirme

Bir kullanıcının, oturum kapatıldıktan sonra belirli bir sayfaya dönmesini istiyorsanız `post_logout_redirect_uri` sorgu dizesi parametresinde BIR URL sağlayın.

## <a name="block-an-authorization-provider"></a>Yetkilendirme sağlayıcısını engelle

Uygulamanızı bir yetkilendirme sağlayıcısı kullanarak kısıtlamak isteyebilirsiniz. Örneğin, uygulamanız yalnızca [e-posta adreslerini açığa çıkaran sağlayıcılar](#provider-user-details)için standartlaştırmak isteyebilir.

Sağlayıcıyı engellemek için, engellenen sağlayıcıya özgü rotaya yönelik istekler için 404 döndürecek [yol kuralları](routes.md) oluşturabilirsiniz. Örneğin, Twitter 'ı sağlayıcı olarak kısıtlamak için aşağıdaki yol kuralını ekleyin.

```json
{
  "route": "/.auth/login/twitter",
  "statusCode": "404"
}
```

## <a name="restrictions"></a>Kısıtlamalar

Genel kısıtlamalar ve sınırlamalar için [Kotalar makalesine](quotas.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kullanıcı kimlik doğrulaması ve yetkilendirme verilerine erişme](user-information.md)

<sup>1</sup> bekleyen dış sertifika.
