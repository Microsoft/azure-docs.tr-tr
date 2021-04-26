---
title: Kayıt defteri rolleri ve izinleri
description: Azure Container Registry 'deki kaynaklara yönelik ayrıntılı izinler sağlamak için Azure rol tabanlı erişim denetimi (Azure RBAC) ve kimlik ve erişim yönetimi (ıAM) kullanın.
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 097ccf89caf63d2a504d072cf04c2b534a57a031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207963"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roller ve izinler

Azure Container Registry hizmeti, bir Azure Container Registry 'ye farklı düzeylerde izinler sağlayan [yerleşik bir Azure rolleri](../role-based-access-control/built-in-roles.md) kümesini destekler. Kullanıcılara, hizmet sorumlularına veya bir kayıt defteriyle etkileşim kurması gereken diğer kimliklere, örneğin kapsayıcı görüntülerini çekme veya gönderme gibi belirli izinler atamak için [Azure rol tabanlı erişim denetimi 'ni (Azure RBAC)](../role-based-access-control/index.yml) kullanın. Ayrıca, farklı işlemler için bir kayıt defterine ayrıntılı izinlerle [özel roller](#custom-roles) de tanımlayabilirsiniz.

| Rol/Izin       | [Erişim Kaynak Yöneticisi](#access-resource-manager) | [Kayıt Defteri Oluştur/Sil](#create-and-delete-registry) | [Görüntü gönder](#push-image) | [Çekme resmi](#pull-image) | [Görüntü verilerini sil](#delete-image-data) | [İlkeleri Değiştir](#change-policies) |   [Görüntüleri imzala](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Sahip | X | X | X | X | X | X |  |  
| Katılımcı | X | X | X |  X | X | X |  |  
| Okuyucu | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| Acrimageimzalayan |  |  |  |  |  |  | X |

## <a name="assign-roles"></a>Rolleri atama

Mevcut bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliğe rol ataması eklemek için üst düzey adımlar için [rol ataması ekleme adımlarına](../role-based-access-control/role-assignments-steps.md) bakın. Azure portal, Azure CLı veya diğer Azure araçlarını kullanabilirsiniz.

Hizmet sorumlusu oluştururken, erişim ve izinlerini kapsayıcı kayıt defteri gibi Azure kaynakları için de yapılandırırsınız. Azure CLı kullanan örnek bir betik için bkz. [hizmet sorumluları ile Azure Container Registry kimlik doğrulaması](container-registry-auth-service-principal.md#create-a-service-principal).

## <a name="differentiate-users-and-services"></a>Kullanıcıları ve Hizmetleri ayırt etme

Her zaman izin uygulandığında en iyi yöntem, bir kişiye veya hizmete bir görevi gerçekleştirmek için en sınırlı sayıda izin sağlamaktır. Aşağıdaki izin kümeleri, insanların ve gözetimsiz hizmetler tarafından kullanılabilen bir özellik kümesini temsil eder.

### <a name="cicd-solutions"></a>CI/CD çözümleri

`docker build`CI/CD çözümlerindeki komutları otomatikleştirmede `docker push` yetenekler gerekir. Bu gözetimsiz hizmet senaryolarında **Acrpush** rolünü atamayı öneririz. Bu rol, daha geniş **katkıda bulunan** rolünün aksine, hesabın diğer kayıt defteri işlemlerini gerçekleştirmesini veya Azure Resource Manager erişmesini önler.

### <a name="container-host-nodes"></a>Kapsayıcı konak düğümleri

Benzer şekilde, Kapsayıcılarınızı çalıştıran düğümlerin **Acrpull** rolü olması gerekir, ancak **okuyucu** becerileri gerektirmemelidir.

### <a name="visual-studio-code-docker-extension"></a>Docker uzantısını Visual Studio Code

Visual Studio Code [Docker uzantısı](https://code.visualstudio.com/docs/azure/docker)gibi araçlar için, kullanılabilir Azure Container kayıt defterlerini listelemek üzere ek kaynak sağlayıcısı erişimi gerekir. Bu durumda, kullanıcılarınıza **okuyucu** veya **katkıda bulunan** rolü erişimi sağlayın. Bu roller `docker pull` ,, `docker push` , `az acr list` `az acr build` ve diğer yeteneklere izin verir. 

## <a name="access-resource-manager"></a>Erişim Kaynak Yöneticisi

[Azure CLI](/cli/azure/)ile Azure Portal ve kayıt defteri yönetimi için Azure Resource Manager erişim gerekir. Örneğin, komutunu kullanarak kayıt defterlerinin listesini almak için `az acr list` Bu izin kümesine ihtiyacınız vardır. 

## <a name="create-and-delete-registry"></a>Kayıt defteri oluştur ve Sil

Azure Container Registry oluşturma ve silme özelliği.

## <a name="push-image"></a>Görüntü gönder

Bir görüntü özelliği `docker push` veya bir Helu grafiği gibi [desteklenen başka bir yapıtı](container-registry-image-formats.md) bir kayıt defterine gönderme. Yetkili kimliği kullanarak kayıt defteriyle [kimlik doğrulaması](container-registry-authentication.md) gerektirir. 

## <a name="pull-image"></a>Çekme resmi

`docker pull`Bir kayıt defterinden, karantinaya alınmamış bir görüntü veya HELI grafiği gibi başka bir [desteklenen yapıt](container-registry-image-formats.md) çekme özelliği. Yetkili kimliği kullanarak kayıt defteriyle [kimlik doğrulaması](container-registry-authentication.md) gerektirir.

## <a name="delete-image-data"></a>Görüntü verilerini sil

[Kapsayıcı görüntülerini silme](container-registry-delete.md)veya HELI grafikleri gibi desteklenen diğer [yapıtları](container-registry-image-formats.md) bir kayıt defterinden silme özelliği.

## <a name="change-policies"></a>İlkeleri Değiştir

Bir kayıt defterinde ilkeleri yapılandırma özelliği. , Görüntü Temizleme, karantina etkinleştirme ve görüntü imzalama dahil olmak üzere ilkeler.

## <a name="sign-images"></a>Görüntüleri imzala

Genellikle bir hizmet sorumlusu kullanan otomatik bir işleme atanan görüntüleri imzalama özelliği. Bu izin, bir kayıt defterine güvenilir bir görüntü göndermeye olanak tanımak için genellikle [anında iletme görüntüsüyle](#push-image) birleştirilir. Ayrıntılar için bkz. [Azure Container Registry içerik güveni](container-registry-content-trust.md).

## <a name="custom-roles"></a>Özel roller

Diğer Azure kaynaklarında olduğu gibi, Azure Container Registry için ayrıntılı izinlerle [özel roller](../role-based-access-control/custom-roles.md) de oluşturabilirsiniz. Ardından, özel rolleri kullanıcılara, hizmet sorumlularına veya bir kayıt defteriyle etkileşimde bulunmak için gereken diğer kimliklere atayın. 

Özel bir role hangi izinlerin uygulanacağını öğrenmek için, Microsoft. ContainerRegistry [eylemlerinin](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)listesine bakın, [yerleşik ACR rollerinin](../role-based-access-control/built-in-roles.md)izin verilen eylemlerini inceleyin veya şu komutu çalıştırın:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Özel bir rol tanımlamak için bkz. [özel rol oluşturma adımları](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> Özel bir rolde Azure Container Registry Şu anda `Microsoft.ContainerRegistry/*` `Microsoft.ContainerRegistry/registries/*` tüm eşleşen eylemlere erişim veren veya gibi joker karakterleri desteklememektedir. Rol içinde her türlü gerekli eylemi belirtin.

### <a name="example-custom-role-to-import-images"></a>Örnek: görüntüleri içeri aktarmak için özel rol

Örneğin, aşağıdaki JSON bir özel rol için bir kayıt defterine [görüntü aktarmaya](container-registry-import-images.md) izin veren en düşük eylemleri tanımlar.

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

JSON açıklamasını kullanarak özel bir rol oluşturmak veya güncelleştirmek için [Azure CLI](../role-based-access-control/custom-roles-cli.md), [Azure Resource Manager şablonu](../role-based-access-control/custom-roles-template.md), [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)veya diğer Azure araçlarını kullanın. Özel bir rol için rol atamalarını, yerleşik Azure rolleri için rol atamalarını yönettiğiniz şekilde ekleyin veya kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

* Azure rollerini [Azure Portal](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md)veya diğer Azure araçlarını kullanarak Azure Identity 'a atama hakkında daha fazla bilgi edinin.

* Azure Container Registry için [kimlik doğrulama seçenekleri](container-registry-authentication.md) hakkında bilgi edinin.

* Bir kapsayıcı kayıt defterinde [Depo kapsamındaki izinleri](container-registry-repository-scoped-permissions.md) (Önizleme) etkinleştirme hakkında bilgi edinin.
