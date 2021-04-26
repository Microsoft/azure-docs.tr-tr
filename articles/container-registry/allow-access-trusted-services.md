---
title: Güvenilen Azure hizmetini kullanarak ağa kısıtlanmış kayıt defterine erişin
description: Bir güvenilir Azure hizmeti örneğini, görüntü çekmek veya göndermek için ağ kısıtlamalı bir kapsayıcı kayıt defterine güvenli bir şekilde erişmek üzere etkinleştirin
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 4b0d7feb223bcfcec4e8b2c786b211f4e3c3c3eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785879"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Güvenilen hizmetlerin ağ kısıtlamalı bir kapsayıcı kayıt defterine güvenli bir şekilde erişmesine izin ver (Önizleme)

Azure Container Registry, güvenilen Azure hizmetlerinin ağ erişim kuralları ile yapılandırılmış bir kayıt defterine erişmesine izin verebilir. Güvenilen hizmetlere izin verildiğinde, güvenilir bir hizmet örneği kayıt defterinin ağ kurallarını güvenli bir şekilde atlayabilir ve çekme veya anında iletme görüntüleri gibi işlemleri gerçekleştirebilir. Hizmet örneğinin yönetilen kimliği erişim için kullanılır ve bir Azure rolü atanması ve kayıt defteriyle kimlik doğrulaması yapılmalıdır.

Bu makaledeki komut örneklerini çalıştırmak için Azure CLı 'nın Azure Cloud Shell veya yerel bir yüklemesini kullanın. Yerel olarak kullanmak isterseniz, 2,18 veya üzeri bir sürüm gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

Güvenilen Azure hizmetleri tarafından kayıt defteri erişiminin yapılmasına izin verilmesi bir **Önizleme** özelliğidir.

## <a name="limitations"></a>Sınırlamalar

* Ağ kısıtlamalı bir kapsayıcı kayıt defterine erişmek için [güvenilir bir hizmette](#trusted-services) etkinleştirilmiş sistem tarafından atanan yönetilen kimlik kullanmanız gerekir. Kullanıcı tarafından atanan Yönetilen kimlikler Şu anda desteklenmiyor.
* Güvenilen hizmetlerin bir [hizmet uç noktasıyla](container-registry-vnet.md)yapılandırılmış bir kapsayıcı kayıt defterine uygulanmalarına izin verme. Özelliği yalnızca [özel bir uç nokta](container-registry-private-link.md) ile kısıtlanan veya [genel IP erişim kuralları](container-registry-access-selected-networks.md) uygulanmış olan kayıt defterlerini etkiler. 

## <a name="about-trusted-services"></a>Güvenilen hizmetler hakkında

Azure Container Registry, bir kayıt defterine erişimi kısıtlayan birden çok ağ yapılandırmasını destekleyen katmanlı bir güvenlik modeline sahiptir ve aşağıdakiler de dahildir:

* [Azure özel bağlantısı olan özel uç nokta](container-registry-private-link.md). Yapılandırıldığında, bir kayıt defterinin özel uç noktasına yalnızca sanal ağ içindeki kaynaklar için özel IP adresleri kullanılarak erişilebilir.  
* Kayıt defterinin genel uç noktasına yalnızca belirli ortak IP adreslerinden veya adres aralıklarından erişime izin veren [kayıt defteri güvenlik duvarı kuralları](container-registry-access-selected-networks.md). Ayrıca, Özel uç noktaları kullanırken güvenlik duvarını genel uç noktaya erişimi engelleyecek şekilde yapılandırabilirsiniz.

Bir sanal ağda dağıtıldığında veya güvenlik duvarı kurallarıyla yapılandırıldığında, kayıt defteri varsayılan olarak bu kaynakların dışından kullanıcılara veya hizmetlere erişim izni vermez. 

Birçok çok kiracılı Azure hizmeti, bu kayıt defteri ağ ayarlarına dahil olmayan ağlardan çalışır ve bu da görüntüleri kayıt defterine çekmesini veya onları itmelerini önler. Belirli hizmet örneklerini "güvenilir" olarak tanımlayarak, kayıt defteri sahibi, görüntü çekmek veya göndermek için kayıt defterinin ağ ayarlarını güvenli bir şekilde atlamak üzere Azure kaynakları seçme izni verebilir. 

### <a name="trusted-services"></a>Güvenilen hizmetler

Kayıt defterinin **Güvenilen hizmetlere izin ver** ayarı etkinse, aşağıdaki hizmetlerin örnekleri, ağ kısıtlı kapsayıcı kayıt defterine erişebilir (varsayılan). Zamana göre daha fazla hizmet eklenecektir.

|Güvenilen hizmet  |Desteklenen kullanım senaryoları  |
|---------|---------|
|ACR Görevleri     | [ACR görevinden farklı bir kayıt defterine erişme](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | Özel bir Docker kapsayıcı görüntüsü kullanarak bir Machine Learning çalışma alanında model [dağıtma](../machine-learning/how-to-deploy-custom-docker-image.md) veya [eğitme](../machine-learning/how-to-train-with-custom-image.md) |
|Azure Container Registry | [Başka bir Azure Container Registry 'den görüntü içeri aktarma](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Curbir, güvenilen hizmetlere izin ver ayarının etkinleştirilmesi App Service, Azure Container Instances ve Azure Güvenlik Merkezi de dahil olmak üzere diğer yönetilen Azure hizmetleri örneklerinin, ağ kısıtlı kapsayıcı kayıt defterine erişmesine izin vermez.

## <a name="allow-trusted-services---cli"></a>Güvenilen hizmetlere izin ver-CLı

Varsayılan olarak, güvenilen hizmetlere izin ver ayarı yeni bir Azure Container Registry 'de etkin olur. [Az ACR Update](/cli/azure/acr#az_acr_update) komutunu çalıştırarak ayarı devre dışı bırakın veya etkinleştirin.

Devre dışı bırakmak için:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Mevcut bir kayıt defterinde veya zaten devre dışı bırakılmış bir kayıt defterinde ayarı etkinleştirmek için:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Güvenilen hizmetlere izin ver-Portal

Varsayılan olarak, güvenilen hizmetlere izin ver ayarı yeni bir Azure Container Registry 'de etkin olur. 

Portalda ayarı devre dışı bırakmak veya yeniden etkinleştirmek için:

1. Portalda kapsayıcı Kayıt defterinize gidin.
1. **Ayarlar** altında **ağ**' ı seçin. 
1. **Ortak ağ erişimine Izin ver**' de **Seçili ağlar** veya **devre dışı** seçeneğini belirleyin.
1. Aşağıdakilerden birini yapın:
    * Güvenilen hizmetlere erişimi devre dışı bırakmak için **Güvenlik Duvarı özel durumu** altında **Güvenilen Microsoft hizmetlerinin bu kapsayıcı kayıt defterine erişmesine izin ver** onay kutusunu temizleyin. 
    * Güvenilen hizmetlere izin vermek için **Güvenlik Duvarı özel durumu** altında **Güvenilen Microsoft hizmetlerinin bu kapsayıcı kayıt defterine erişmesine izin ver**' i işaretleyin.
1. **Kaydet**’i seçin.

## <a name="trusted-services-workflow"></a>Güvenilen hizmetler iş akışı

Güvenilen bir hizmetin bir örneğinin ağ kısıtlamalı bir kapsayıcı kayıt defterine erişmesini sağlamak için tipik bir iş akışı aşağıda verilmiştir.

1. Azure Container Registry için [Güvenilen hizmetlerden](#trusted-services) birinin bir örneğindeki [Azure kaynakları için](../active-directory/managed-identities-azure-resources/overview.md) sistem tarafından atanan yönetilen kimliği etkinleştirin.
1. [Azure rolü](container-registry-roles.md) kimliğini Kayıt defterinize atayın. Örneğin, kapsayıcı görüntülerini çekmek için ACRPull rolünü atayın.
1. Ağ kısıtlamalı kayıt defterinde, ayarı güvenilen hizmetler tarafından erişime izin verecek şekilde yapılandırın.
1. Ağ ile kısıtlanan kayıt defteriyle kimlik doğrulaması yapmak için kimliğin kimlik bilgilerini kullanın. 
1. Kayıt defterinden görüntüleri çekin veya rol tarafından izin verilen başka işlemler gerçekleştirin.

### <a name="example-acr-tasks"></a>Örnek: ACR görevleri

Aşağıdaki örnekte, ACR görevlerinin güvenilir bir hizmet olarak kullanılması gösterilmektedir. Görev ayrıntıları için [Azure tarafından yönetilen bir kimlik kullanarak ACR görevinde çapraz kayıt defteri kimlik doğrulaması](container-registry-tasks-cross-registry-authentication.md) konusuna bakın.

1. Bir Azure Container Registry oluşturun veya güncelleştirin ve kayıt defterine [örnek bir temel görüntü gönderin](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) . Bu kayıt defteri, senaryonun *temel kayıt* defteridir.
1. İkinci bir Azure Container Registry 'de, temel kayıt defterinden görüntü çekmek için bir ACR görevi [tanımlayın](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) ve [oluşturun](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) . Görevi oluştururken sistem tarafından atanan yönetilen kimliği etkinleştirin.
1. [Temel kayıt defterine erişmek için görev kimliğini bir Azure rolü](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources)atayın. Örneğin, görüntüleri çekme için izinleri olan AcrPull rolünü atayın.
1. Göreve [yönetilen kimlik kimlik bilgilerini ekleyin](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) .
1. Görevin ağ kısıtlamalarını atladığını doğrulamak için, temel kayıt defterinde [genel erişimi devre dışı bırakın](container-registry-access-selected-networks.md#disable-public-network-access) .
1. Görevi çalıştırın. Temel kayıt defteri ve görev düzgün şekilde yapılandırıldıysa, temel kayıt defteri erişime izin verdiğinden görev başarıyla çalıştırılır.

Güvenilen hizmetlere erişimi devre dışı bırakma sınamasını yapmak için:

1. Temel kayıt defterinde, güvenilen hizmetlere erişime izin ver ayarını devre dışı bırakın.
1. Görevi yeniden çalıştırın. Bu durumda, görev başarısız olur, çünkü temel kayıt defteri artık görev tarafından erişime izin vermez.

## <a name="next-steps"></a>Sonraki adımlar

* Bir sanal ağda özel bir uç nokta kullanarak bir kayıt defterine erişimi kısıtlamak için bkz. Azure [Container kayıt defteri Için Azure özel bağlantısını yapılandırma](container-registry-private-link.md).
* Kayıt defteri güvenlik duvarı kurallarını ayarlamak için bkz. [genel IP ağ kurallarını yapılandırma](container-registry-access-selected-networks.md).
