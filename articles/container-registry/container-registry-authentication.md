---
title: Kayıt defteri kimlik doğrulama seçenekleri
description: Bir Azure Active Directory kimlikle oturum açma, hizmet sorumlularını kullanma ve isteğe bağlı yönetici kimlik bilgilerini kullanma dahil olmak üzere, özel bir Azure Kapsayıcı kayıt defteri için kimlik doğrulama seçenekleri.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 097a322260e4c4f55d4e0d7e3e107abdd15a3b8a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831645"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Azure Container Registry ile kimlik doğrulama

Her biri bir veya daha fazla kayıt defteri kullanımı senaryosu için geçerli olan bir Azure Container Registry ile kimlik doğrulamanın birkaç yolu vardır.

Önerilen yollarla, [tek tek oturum açma](#individual-login-with-azure-ad)yoluyla bir kayıt defterine doğrudan kimlik doğrulaması dahildir veya uygulamalarınızın ve kapsayıcı düzenleyicilerinin bir Azure Active Directory (Azure AD) [hizmet sorumlusu](#service-principal)kullanarak katılımsız veya "gözetimsiz" kimlik doğrulaması gerçekleştirebilir.

## <a name="authentication-options"></a>Kimlik doğrulaması seçenekleri

Aşağıdaki tabloda, kullanılabilir kimlik doğrulama yöntemleri ve tipik senaryolar listelenmektedir. Ayrıntılar için bkz. bağlantılı içerik.

| Yöntem                               | Kimlik doğrulaması nasıl yapılır?                                           | Senaryolar                                                            | Azure rol tabanlı erişim denetimi (Azure RBAC)                             | Sınırlamalar                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Bireysel AD kimliği](#individual-login-with-azure-ad)                | `az acr login` Azure CLı 'da                             | Geliştiricilere ve test edicilere göre etkileşimli gönderim/çekme                                    | Yes                              | AD belirtecinin her 3 saatte bir yenilenmesi gerekir     |
| [AD hizmet sorumlusu](#service-principal)                  | `docker login`<br/><br/>`az acr login` Azure CLı 'da<br/><br/> API 'lerde veya araç defterindeki kayıt defteri oturum açma ayarları<br/><br/> [Kubernetes çekme gizli dizisi](container-registry-auth-kubernetes.md)                                           | CI/CD ardışık düzeninde katılımsız gönderim<br/><br/> Azure 'a veya dış hizmetlere katılımsız çekme  | Yes                              | SP parolasının varsayılan süre sonu 1 yıldır       |                                                           
| [AKS ile tümleştirme](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | AKS kümesi oluşturulduğunda veya güncelleştirilirken kayıt defteri Ekle  | AKS kümesine katılımsız çekme                                                  | Hayır, yalnızca çekme erişimi             | Yalnızca AKS kümesi ile kullanılabilir            |
| [Azure kaynakları için yönetilen kimlik](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` Azure CLı 'da                                       | Azure CI/CD ardışık düzeninde katılımsız gönderim<br/><br/> Azure hizmetlerine katılımsız çekme<br/><br/>   | Yes                              | Yalnızca [Azure kaynakları için yönetilen kimlikleri destekleyen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) Select Azure hizmetlerinden kullanın              |
| [Yönetici kullanıcı](#admin-account)                            | `docker login`                                          | Bireysel geliştirici veya test edici tarafından etkileşimli gönderim/çekme<br/><br/>Kayıt defterinden Azure App Service veya Azure Container Instances 'ye görüntü dağıtımı                      | Hayır, her zaman çek ve anında erişim  | Kayıt defteri başına tek hesap, birden çok kullanıcı için önerilmez         |
| [Depo kapsamlı erişim belirteci](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` Azure CLı 'da   | Bireysel geliştirici veya test edici tarafından depoya etkileşimli gönderim/çekme<br/><br/> Ayrı sistem veya dış cihaz tarafından yapılan katılımsız gönderme/depoya çekme                  | Yes                              | Şu anda AD kimliğiyle tümleştirildi  |

## <a name="individual-login-with-azure-ad"></a>Azure AD ile bireysel oturum açma

Kayıt defteriyle doğrudan çalışırken, bir geliştirme iş istasyonundan görüntü çekme ve görüntüleri oluşturduğunuz bir kayıt defterine gönderme gibi, bireysel Azure kimliğinizi kullanarak kimlik doğrulaması yapın. [Az Login](/cli/azure/reference-index#az_login)komutuyla [Azure CLI](/cli/azure/install-azure-cli) 'da oturum açın ve [az ACR Login](/cli/azure/acr#az_acr_login) komutunu çalıştırın:

```azurecli
az login
az acr login --name <acrName>
```

İle oturum açtığınızda `az acr login` , CLI, `az login` kayıt defterinizle oturumunuzu sorunsuz bir şekilde doğrulamak için çalıştırdığınızda oluşturulan belirtecini kullanır. Kimlik doğrulama akışını gerçekleştirmek için, Docker CLı ve Docker Daemon 'ın ortamınızda yüklü ve çalışır durumda olması gerekir. `az acr login` , dosyasında Azure Active Directory belirteç ayarlamak için Docker istemcisini kullanır `docker.config` . Bu şekilde oturum açtıktan sonra, kimlik bilgileriniz önbelleğe alınır ve `docker` oturuminizdeki sonraki komutlar Kullanıcı adı veya parola gerektirmez.

> [!TIP]
> Ayrıca `az acr login` , Docker görüntüleri dışındaki yapıtları, Kayıt defterinize ( [OCI yapıları](container-registry-oci-artifacts.md)gibi) göndermek veya çekmek istediğinizde tek bir kimliğin kimliğini doğrulamak için kullanın.  

Kayıt defteri erişimi için tarafından kullanılan belirteç `az acr login` **3 saat** için geçerlidir; bu nedenle, bir komutu çalıştırmadan önce her zaman kayıt defterinde oturum açmanız önerilir `docker` . Belirtecin süresi dolarsa, `az acr login` yeniden kimlik doğrulaması yapmak için komutunu tekrar kullanarak yenileyebilirsiniz. 

`az acr login`Azure kimlikleri ile kullanılması, [Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/role-assignments-portal.md)sağlar. Bazı senaryolarda, Azure AD 'de kendi bireysel Kimliğiniz ile bir kayıt defterinde oturum açmak veya diğer Azure [rollerini ve izinleri](container-registry-roles.md)Ile diğer Azure kullanıcılarını yapılandırmak isteyebilirsiniz. Şirketler arası senaryolar için veya tek tek erişimi yönetmek istemediğiniz bir çalışma grubunun veya geliştirme iş akışının ihtiyaçlarını işlemek için [Azure kaynakları için yönetilen bir kimlikle](container-registry-authentication-managed-identity.md)da oturum açabilirsiniz.

### <a name="az-acr-login-with---expose-token"></a>az ACR ile oturum açma--gösterme-belirteç

Bazı durumlarda, `az acr login` Docker Daemon ortamınızda çalışmadığı zaman kimlik doğrulaması yapmanız gerekebilir. Örneğin, `az acr login` DOCKER CLI 'yı sağlayan ancak Docker Daemon çalıştırmayan Azure Cloud Shell bir betikte çalıştırmanız gerekebilir.

Bu senaryo için, `az acr login` önce `--expose-token` parametresiyle çalıştırın. Bu seçenek, Docker CLı aracılığıyla oturum açmak yerine bir erişim belirteci gösterir.

```azurecli
az acr login --name <acrName> --expose-token
```

Çıktı, burada kısaltılmış olarak erişim belirtecini görüntüler:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
Kayıt defteri kimlik doğrulaması için, belirteç kimlik bilgisini güvenli bir konumda depolamanızı ve [Docker oturum açma](https://docs.docker.com/engine/reference/commandline/login/) kimlik bilgilerini yönetmek için önerilen uygulamaları izlemeniz önerilir. Örneğin, belirteç değerini bir ortam değişkeninde depolayın:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Ardından, Çalıştır `docker login` , `00000000-0000-0000-0000-000000000000` Kullanıcı adı olarak geçer ve erişim belirtecini parola olarak kullanarak:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Hizmet sorumlusu

Kayıt defterinize bir [hizmet sorumlusu](../active-directory/develop/app-objects-and-service-principals.md) atarsanız, uygulamanız veya hizmetiniz bunu gözetimsiz kimlik doğrulaması için kullanabilir. Hizmet sorumluları, [Azure rol tabanlı erişim denetimi 'ne (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) bir kayıt defterine izin verir ve bir kayıt defterine birden çok hizmet sorumlusu atayabilirsiniz. Birden çok hizmet sorumlusu, farklı uygulamalar için farklı erişim tanımlamanızı sağlar.

Bir kapsayıcı kayıt defteri için kullanılabilir roller şunlardır:

* **Acrpull**: çekme

* **Acrpush**: çekme ve gönderme

* **Sahip**: diğer kullanıcılara roller çekme, gönderme ve atama

Rollerin tüm listesi için bkz. [Azure Container Registry rolleri ve izinleri](container-registry-roles.md).

CLı betikleri için bir Azure Container Registry ile kimlik doğrulamaya yönelik bir hizmet sorumlusu oluşturma ve daha fazla rehberlik için bkz. [hizmet sorumluları ile Azure Container Registry kimlik doğrulaması](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Yönetici hesabı

Her kapsayıcı kayıt defteri, varsayılan olarak devre dışı bırakılan bir yönetici kullanıcı hesabı içerir. Yönetici kullanıcıyı etkinleştirebilir ve Azure portal kimlik bilgilerini yönetebilir veya Azure CLı veya diğer Azure araçlarını kullanarak yapılandırabilirsiniz. Yönetici hesabının kayıt defteri için tam izinleri vardır.

Yönetici hesabı şu anda bir kapsayıcı kayıt defterinden belirli Azure hizmetlerine görüntü dağıtmaya yönelik bazı senaryolar için gereklidir. Örneğin, bir kayıt defterinden doğrudan [Azure Container Instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) veya [kapsayıcılar için Azure Web Apps](container-registry-tutorial-deploy-app.md)'e bir kapsayıcı görüntüsü dağıtmak üzere Azure Portal kullandığınızda yönetici hesabı gerekir.

> [!IMPORTANT]
> Yönetici hesabı, genellikle test amacıyla tek bir kullanıcının kayıt defterine erişmesi için tasarlanmıştır. Yönetici hesabı kimlik bilgilerini birden çok kullanıcı arasında paylaşmayı önermiyoruz. Yönetici hesabıyla kimlik doğrulaması yapan tüm kullanıcılar, kayıt defterine gönderme ve çekme erişimi olan tek bir kullanıcı olarak görüntülenir. Bu hesabın değiştirilmesi veya devre dışı bırakılması, kimlik bilgilerini kullanan tüm kullanıcılar için kayıt defteri erişimini devre dışı bırakır. Gözetimsiz senaryolara yönelik kullanıcılar ve hizmet sorumluları için bireysel kimlik önerilir.
>

Yönetici hesabı, her ikisi de yeniden üretilbilen iki parolayla sağlanır. İki parola, diğerini yeniden oluştururken bir parola kullanarak kayıt defteriyle bağlantıyı korumanıza olanak sağlar. Yönetici hesabı etkinleştirilmişse, `docker login` kayıt defterine temel kimlik doğrulaması sorulduğunda, Kullanıcı adını ve parolayı komuta geçirebilirsiniz. Örnek:

```
docker login myregistry.azurecr.io 
```

Oturum açma kimlik bilgilerini yönetmek için önerilen uygulamalar için [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) komut başvurusuna bakın.

Mevcut bir kayıt defteri için yönetici kullanıcıyı etkinleştirmek üzere, `--admin-enabled` Azure CLI 'de [az ACR Update](/cli/azure/acr#az_acr_update) komutunun parametresini kullanabilirsiniz:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Kayıt defterinizde gezinerek yönetici kullanıcıyı Azure portal etkinleştirebilirsiniz, **Ayarlar** altında **erişim anahtarları** ' nı seçip **Yönetici Kullanıcı** altında **etkinleştirin** .

![Azure portal Yönetici Kullanıcı Kullanıcı arabirimini etkinleştirme][auth-portal-01]

## <a name="next-steps"></a>Sonraki adımlar

* [Azure CLı kullanarak ilk görüntünüzü gönderin](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
