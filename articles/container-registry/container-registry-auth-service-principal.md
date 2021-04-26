---
title: Hizmet sorumlusuyla kimlik doğrulama
description: Azure Active Directory hizmet sorumlusu kullanarak özel kapsayıcı kayıt defterinizde görüntülere erişim sağlayın.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: a32538e5fc5354427bafc5098634becdcedd1239
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285544"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Hizmet sorumluları ile kimlik doğrulamasını Azure Container Registry

Kapsayıcı Kayıt defterinize anında iletme, çekme veya başka erişim sağlamak için bir Azure Active Directory (Azure AD) hizmet sorumlusu kullanabilirsiniz. Hizmet sorumlusu kullanarak, "gözetimsiz" hizmetlere ve uygulamalarına erişim sağlayabilirsiniz.

## <a name="what-is-a-service-principal"></a>Hizmet sorumlusu nedir?

Azure AD *hizmet sorumluları* , aboneliğinizdeki Azure kaynaklarına erişim sağlar. Hizmet sorumlusunu bir hizmet için Kullanıcı kimliği olarak düşünebilirsiniz; burada "hizmet", kaynaklara erişmesi gereken herhangi bir uygulama, hizmet veya platformdur. Erişim haklarına sahip bir hizmet sorumlusunu, yalnızca belirttiğiniz kaynaklarla kapsamlı bir şekilde yapılandırabilirsiniz. Ardından, uygulamanızı veya hizmetinizi bu kaynaklara erişmek için hizmet sorumlusunun kimlik bilgilerini kullanacak şekilde yapılandırın.

Azure Container Registry bağlamında Azure 'da özel kayıt defteriniz için çekme, gönderme ve çekme veya diğer izinlerle bir Azure AD hizmet sorumlusu oluşturabilirsiniz. Tüm liste için, bkz. [Azure Container Registry roller ve izinler](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Neden hizmet sorumlusu kullanmalıyım?

Bir Azure AD hizmet sorumlusu kullanarak özel kapsayıcı Kayıt defterinize kapsamlı erişim sağlayabilirsiniz. Her biri için Kayıt defterinize özel erişim hakları olan her bir uygulama veya hizmetlerinizin farklı hizmet sorumluları oluşturun. Ve, hizmetler ve uygulamalar arasında kimlik bilgilerinin paylaşılmasını önleyebileceğiniz için kimlik bilgilerini döndürebilir veya yalnızca hizmet sorumlusu (ve dolayısıyla uygulama) için erişimi iptal edebilirsiniz.

Örneğin, Web uygulamanızı yalnızca görüntü erişimi ile birlikte sağlayan bir hizmet sorumlusu kullanacak şekilde yapılandırın `pull` . Yapı sisteminiz, hem hem de erişimiyle birlikte sağlayan bir hizmet sorumlusu kullanır `push` `pull` . Uygulamanızın geliştirilmesi uygulamalı şekilde değişirse, derleme sistemini etkilemeden hizmet sorumlusu kimlik bilgilerini döndürebilirsiniz.

## <a name="when-to-use-a-service-principal"></a>Hizmet sorumlusu ne zaman kullanılır?

**Gözetimsiz senaryolarda** kayıt defteri erişimi sağlamak için bir hizmet sorumlusu kullanmanız gerekir. Diğer bir deyişle, kapsayıcı görüntülerini otomatik olarak veya başka bir şekilde katılımsız bir şekilde iletme veya çekme gerektiren herhangi bir uygulama, hizmet veya komut dosyası. Örnek:

  * *Çekme*: bir kayıt defterinden Kubernetes, DC/OS ve Docker sısınma dahil olmak üzere düzenleme sistemlerine kapsayıcı dağıtın. Ayrıca, kapsayıcı kayıt defterlerinden [Azure Kubernetes hizmeti (AKS)](../aks/cluster-container-registry-integration.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml)ve diğerleri gibi ilgili Azure hizmetlerine de çekebilirsiniz.

  * *Gönderme*: kapsayıcı görüntüleri oluşturun ve Azure Pipelines veya Jenkins gibi sürekli tümleştirme ve dağıtım çözümlerini kullanarak bunları bir kayıt defterine gönderin.

Bir kayıt defterine bireysel erişim için, örneğin, bir kapsayıcı görüntüsünü geliştirme iş istasyonunuza el ile çektiğinizde, kayıt defteri erişimi (örneğin, [az ACR oturum açma][az-acr-login]ile) yerine kendı [Azure AD kimliğinizi](container-registry-authentication.md#individual-login-with-azure-ad) kullanmanızı öneririz.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Örnek betikler

Azure CLı için yukarıdaki örnek betikleri GitHub ' da ve Azure PowerShell sürümlerinin yanı sıra bulabilirsiniz:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Hizmet sorumlusu ile kimlik doğrulama

Kapsayıcı Kayıt defterinize erişim verdiğiniz bir hizmet sorumlusu olduktan sonra, "gözetimsiz" hizmetlere ve uygulamalarına erişim için kimlik bilgilerini yapılandırabilir veya komutunu kullanarak bunları girebilirsiniz `docker login` . Aşağıdaki değerleri kullanın:

* **Kullanıcı adı** -hizmet sorumlusu uygulama kimliği ( *istemci kimliği* olarak da bilinir)
* **Parola** -hizmet sorumlusu parolası ( *istemci gizli anahtarı* da denir)

Her değerin biçimi vardır `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . 

> [!TIP]
> [Az ad SP Reset-Credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) komutunu çalıştırarak bir hizmet sorumlusunun parolasını yeniden oluşturabilirsiniz.
>

### <a name="use-credentials-with-azure-services"></a>Azure hizmetleriyle kimlik bilgilerini kullanma

Azure Container Registry ile kimlik doğrulaması yapan herhangi bir Azure hizmetinden hizmet sorumlusu kimlik bilgilerini kullanabilirsiniz.  Çeşitli senaryolar için kayıt defterinin yönetici kimlik bilgileri yerine hizmet sorumlusu kimlik bilgilerini kullanın.

Örneğin, [Azure Container Instances](container-registry-auth-aci.md)Için bir Azure Container Registry 'den görüntü çekmek üzere kimlik bilgilerini kullanın.

### <a name="use-with-docker-login"></a>Docker oturum açma ile kullanma

`docker login`Hizmet sorumlusu kullanarak çalıştırabilirsiniz. Aşağıdaki örnekte, hizmet sorumlusu uygulama KIMLIĞI, ortam değişkenine `$SP_APP_ID` ve değişkendeki parolaya geçirilir `$SP_PASSWD` . Docker kimlik bilgilerini yönetmek için önerilen uygulamalar için [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) komut başvurusuna bakın.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Oturum açıldıktan sonra Docker kimlik bilgilerini önbelleğe alır.

### <a name="use-with-certificate"></a>Sertifikayla kullanma

Hizmet sorumlusuna bir sertifika eklediyseniz, Azure CLı 'de sertifika tabanlı kimlik doğrulamasıyla oturum açabilir ve ardından bir kayıt defterine erişmek için [az ACR Login][az-acr-login] komutunu kullanabilirsiniz. Parola yerine gizli bir sertifika kullanılması, CLı kullandığınızda ek güvenlik sağlar. 

[Bir hizmet sorumlusu oluşturduğunuzda](/cli/azure/create-an-azure-service-principal-azure-cli)otomatik olarak imzalanan bir sertifika oluşturulabilir. Veya var olan bir hizmet sorumlusuna bir veya daha fazla sertifika ekleyebilirsiniz. Örneğin, bir kayıt defterinden görüntü çekme veya gönderme haklarıyla bir hizmet sorumlusu oluşturmak veya güncelleştirmek için bu makaledeki betiklerden birini kullanırsanız, [az ad SP kimlik bilgisi sıfırlama][az-ad-sp-credential-reset] komutunu kullanarak bir sertifika ekleyin.

[Azure CLI 'da oturum açmak](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)için hizmet sorumlusu 'nı sertifikayla birlikte kullanmak için, sertifika pek biçiminde olmalı ve özel anahtarı içermelidir. Sertifikanız gerekli biçimde değilse, dönüştürmek için gibi bir araç kullanın `openssl` . Hizmet sorumlusunu kullanarak CLı 'de oturum açmak için [az Login][az-login] çalıştırdığınızda, hizmet SORUMLUSUNUN uygulama kimliği ve ACTIVE DIRECTORY Kiracı kimliği de sağlar. Aşağıdaki örnek bu değerleri ortam değişkenleri olarak göstermektedir:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Ardından, kayıt defteri ile kimlik doğrulaması yapmak için [az ACR Login][az-acr-login] çalıştırın:

```azurecli
az acr login --name myregistry
```

CLı, `az login` kayıt defteri ile oturumunuzun kimliğini doğrulamak için çalıştırdığınızda oluşturulan belirteci kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* Azure Container Registry ile kimlik doğrulaması yapmak için diğer senaryolar için [kimlik doğrulamaya genel bakış](container-registry-authentication.md) bölümüne bakın.

* Bir kapsayıcı kayıt defteri için hizmet sorumlusu kimlik bilgilerini depolamak ve almak üzere bir Azure Anahtar Kasası kullanmanın bir örneği için [ACR görevlerini kullanarak bir kapsayıcı görüntüsü oluşturma ve dağıtma](container-registry-tutorial-quick-task.md)öğreticisine bakın.

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
