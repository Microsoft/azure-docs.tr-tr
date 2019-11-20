---
title: Azure Container Registry ile kimlik doğrulama
description: Bir Azure Active Directory kimlikle oturum açma, hizmet sorumlularını kullanma ve isteğe bağlı yönetici kimlik bilgilerini kullanma dahil olmak üzere bir Azure Container Registry için kimlik doğrulama seçenekleri.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a55cba27c676b283a4da490f05dd6fc672e10d49
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032381"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Özel bir Docker kapsayıcı kayıt defteri ile kimlik doğrulama

Her biri bir veya daha fazla kayıt defteri kullanımı senaryosu için geçerli olan bir Azure Container Registry ile kimlik doğrulamanın birkaç yolu vardır.

Önerilen yollarla, [tek tek oturum açma](#individual-login-with-azure-ad)yoluyla bir kayıt defterine doğrudan kimlik doğrulaması dahildir veya uygulamalarınızın ve kapsayıcı düzenleyicilerinin bir Azure Active Directory (Azure AD) hizmeti kullanarak katılımsız veya "gözetimsiz" kimlik doğrulaması gerçekleştirebilir [ sorumlu](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Azure AD ile bireysel oturum açma

Kayıt defteriyle doğrudan bir geliştirme iş istasyonundan görüntü çekme ve gönderme gibi, [Azure CLI](/cli/azure/install-azure-cli)'de [az ACR Login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) komutunu kullanarak kimlik doğrulaması yapma.

```azurecli
az acr login --name <acrName>
```

İle `az acr login`oturum açtığınızda, CLI, kayıt defterinizle oturumunuzu sorunsuz bir şekilde doğrulamak için [az Login](/cli/azure/reference-index#az-login) çalıştırdığınızda oluşturulan belirteci kullanır. Bu şekilde oturum açtıktan sonra, kimlik bilgileriniz önbelleğe alınır ve oturuminizdeki sonraki `docker` komutlar Kullanıcı adı veya parola gerektirmez. 

Kayıt defteri erişimi için tarafından `az acr login` kullanılan belirteç 1 saat için geçerlidir; bu nedenle, bir `docker` komutu çalıştırmadan önce her zaman kayıt defterinde oturum açmanız önerilir. Belirtecin süresi dolarsa, yeniden kimlik doğrulaması yapmak için `az acr login` komutunu tekrar kullanarak yenileyebilirsiniz. 

Azure `az acr login` kimlikleri ile kullanılması, [rol tabanlı erişim](../role-based-access-control/role-assignments-portal.md)sağlar. Bazı senaryolarda, Azure AD 'de kendi bireysel kimliklerinizi kullanarak bir kayıt defterinde oturum açmak isteyebilirsiniz. Şirketler arası senaryolar için veya bireysel erişimi yönetmek istemediğiniz bir çalışma grubunun ihtiyaçlarını işlemek üzere [Azure kaynakları için yönetilen bir kimlikle](container-registry-authentication-managed-identity.md)da oturum açabilirsiniz.

## <a name="service-principal"></a>Hizmet sorumlusu

Kayıt defterinize bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md) atarsanız, uygulamanız veya hizmetiniz bunu gözetimsiz kimlik doğrulaması için kullanabilir. Hizmet sorumluları, bir kayıt defterine [rol tabanlı erişime](../role-based-access-control/role-assignments-portal.md) izin verir ve bir kayıt defterine birden çok hizmet sorumlusu atayabilirsiniz. Birden çok hizmet sorumlusu, farklı uygulamalar için farklı erişim tanımlamanızı sağlar.

Bir kapsayıcı kayıt defteri için kullanılabilir roller şunlardır:

* **Acrpull**: çekme

* **Acrpush**: çekme ve gönderme

* **Sahip**: diğer kullanıcılara roller çekme, gönderme ve atama

Rollerin tüm listesi için bkz. [Azure Container Registry rolleri ve izinleri](container-registry-roles.md).

CLı betikleri için bir Azure Container Registry ile kimlik doğrulaması yapmak üzere bir hizmet sorumlusu oluşturmak ve hizmet sorumlusu kullanma kılavuzu için, bkz. [hizmet sorumluları ile Azure Container Registry kimlik doğrulaması](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Yönetici hesabı

Her kapsayıcı kayıt defteri, varsayılan olarak devre dışı bırakılan bir yönetici kullanıcı hesabı içerir. Yönetici kullanıcıyı etkinleştirebilir ve Azure portal kimlik bilgilerini yönetebilir veya Azure CLı veya diğer Azure araçlarını kullanarak yapılandırabilirsiniz.

> [!IMPORTANT]
> Yönetici hesabı, genellikle test amacıyla tek bir kullanıcının kayıt defterine erişmesi için tasarlanmıştır. Yönetici hesabı kimlik bilgilerini birden çok kullanıcı arasında paylaşmayı önermiyoruz. Yönetici hesabıyla kimlik doğrulaması yapan tüm kullanıcılar, kayıt defterine gönderme ve çekme erişimi olan tek bir kullanıcı olarak görüntülenir. Bu hesabın değiştirilmesi veya devre dışı bırakılması, kimlik bilgilerini kullanan tüm kullanıcılar için kayıt defteri erişimini devre dışı bırakır. Gözetimsiz senaryolara yönelik kullanıcılar ve hizmet sorumluları için bireysel kimlik önerilir.
>

Yönetici hesabı, her ikisi de yeniden üretilbilen iki parolayla sağlanır. İki parola, diğerini yeniden oluştururken bir parola kullanarak kayıt defteriyle bağlantıyı korumanıza olanak sağlar. Yönetici hesabı etkinleştirilmişse, kayıt defterine temel kimlik doğrulaması sorulduğunda, Kullanıcı adını ve parolayı `docker login` komuta geçirebilirsiniz. Örneğin:

```
docker login myregistry.azurecr.io 
```

Oturum açma kimlik bilgilerini yönetmek için en iyi uygulamalar için [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) komut başvurusuna bakın.

Mevcut bir kayıt defteri için yönetici kullanıcıyı etkinleştirmek üzere, Azure CLI 'de `--admin-enabled` [az ACR Update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) komutunun parametresini kullanabilirsiniz:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Kayıt defterinizde gezinerek yönetici kullanıcıyı Azure portal etkinleştirebilirsiniz, **Ayarlar**altında **erişim anahtarları** ' nı seçip **Yönetici Kullanıcı**altında **etkinleştirin** .

![Azure portal Yönetici Kullanıcı Kullanıcı arabirimini etkinleştirme][auth-portal-01]

## <a name="next-steps"></a>Sonraki adımlar

* [Azure CLı kullanarak ilk görüntünüzü gönderin](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
