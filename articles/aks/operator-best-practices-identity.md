---
title: Kimlik yönetimi için en iyi uygulamalar
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) içindeki kümeler için kimlik doğrulama ve yetkilendirmeyi yönetmek üzere küme operatörü en iyi uygulamalarını öğrenin
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: jpalma
author: palma21
ms.openlocfilehash: de84e3e2a8da3e1b5195978a8a2204fdfa2108d7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105111"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmetinde (AKS) kimlik doğrulama ve yetkilendirme için en iyi yöntemler

Azure Kubernetes Service (AKS) içinde kümeler dağıtıp bakımını yaparken, kaynak ve hizmetlere erişimi yönetmek için yollar uygulamalısınız. Bu denetimler olmadan:
* Hesapların gereksiz kaynak ve hizmetlere erişimi olabilir. 
* Değişiklik yapmak için hangi kimlik bilgileri kümesinin kullanıldığını izleme zor olabilir.

Bu en iyi yöntemler makalesi, bir küme işlecinin AKS kümelerinin erişimini ve kimliğini nasıl yönetebileceğini ele alır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure Active Directory ile AKS kümesi kullanıcılarının kimliğini doğrulayın.
> * Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) ile kaynaklara erişimi denetleme.
> * AKS kaynağına, ölçekte Kubernetes API 'sine ve ' a yönelik erişim erişimi sağlamak için Azure RBAC kullanın `kubeconfig` .
> * Diğer hizmetlerle birlikte dizin kimliklerini doğrulamak için yönetilen bir kimlik kullanın.

## <a name="use-azure-active-directory-azure-ad"></a>Azure Active Directory kullanma (Azure AD)

> **En iyi yöntemler kılavuzu** 
> 
> AKS kümelerini Azure AD tümleştirmesi ile dağıtın. Azure AD 'yi kullanarak kimlik yönetimi bileşenini merkezileştirin. Kullanıcı hesabı veya grup durumundaki herhangi bir değişiklik, AKS kümesine erişim için otomatik olarak güncelleştirilir. [Rolleri, Kümerollerinizi veya bağlamaları](#use-kubernetes-role-based-access-control-kubernetes-rbac)kullanarak kullanıcıları veya grupları en düşük izin miktarına göre kapsam.

Kubernetes kümesi geliştiricileriniz ve uygulama sahiplerinin farklı kaynaklara erişmesi gerekir. Kubernetes, kullanıcıların etkileşime girebileceği kaynakları denetleyebilmeniz için bir kimlik yönetimi çözümü yoktur. Bunun yerine, genellikle kümenizi mevcut bir kimlik çözümüyle tümleştirmeniz gerekir. Azure AD 'yi girin: AKS kümeleriyle tümleştirilen kurumsal özellikli bir kimlik yönetimi çözümüdür.

AKS 'de Azure AD ile tümleşik kümeler sayesinde, kaynaklara erişim izinleri tanımlayan *Roller* veya *kümerolleri* oluşturursunuz. Daha sonra rolleri Azure AD 'den kullanıcılara veya gruplara *bağlarsınız* . [Sonraki bölümde](#use-kubernetes-role-based-access-control-kubernetes-rbac)bu Kubernetes RBAC hakkında daha fazla bilgi edinin. Azure AD tümleştirmesi ve kaynaklara erişimi denetleme, aşağıdaki diyagramda görülebilir:

![AKS ile Azure Active Directory tümleştirme için küme düzeyinde kimlik doğrulaması](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Geliştirici Azure AD ile kimlik doğrulaması yapar.
1. Azure AD belirteç verme uç noktası erişim belirtecini yayınlar.
1. Geliştirici, gibi Azure AD belirtecini kullanarak bir eylem gerçekleştirir `kubectl create pod` .
1. Kubernetes, belirteci Azure AD ile doğrular ve geliştiricinin grup üyeliklerini getirir.
1. Kubernetes RBAC ve küme ilkeleri uygulanır.
1. Geliştirici isteği, Azure AD grup üyeliği ve Kubernetes RBAC ve ilkelerine yönelik önceki doğrulamaya göre başarılı olur.

Azure AD kullanan bir AKS kümesi oluşturmak için bkz. [Azure ACTIVE DIRECTORY aks Ile tümleştirme][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes rol tabanlı erişim denetimini kullanma (Kubernetes RBAC)

> **En iyi yöntemler kılavuzu**
> 
> Kubernetes RBAC ile kaynak kümelemek için Kullanıcı veya grup izinleri tanımlayın. Gerekli en az izin miktarını atayan roller ve bağlamalar oluşturun. Kullanıcı durumu veya grup üyeliği değişikliğini otomatik olarak güncelleştirmek ve küme kaynaklarına erişimi güncel tutmak için Azure AD ile tümleştirin.

Kubernetes 'te küme kaynaklarına ayrıntılı erişim denetimi sağlarsınız. İzinleri küme düzeyinde veya belirli ad alanlarına tanımlarsınız. Hangi kaynakların yönetilebilecek ve hangi izinlere sahip olduğunu belirlersiniz. Daha sonra bu rolleri Kullanıcı veya gruplara bağlama ile uygularsınız. *Roller*, *Kümerolleri* ve *bağlamalar* hakkında daha fazla bilgi Için bkz. [Azure KUBERNETES hizmeti (aks) için erişim ve kimlik seçenekleri][aks-concepts-identity].

Örnek olarak, aşağıdaki örnekteki YAML bildiriminde gösterildiği gibi *finans-App* adlı ad alanındaki kaynaklara tam erişimi olan bir rol oluşturursunuz:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Ardından, aşağıdaki YAML bildiriminde gösterildiği gibi, bir RoleBinding oluşturup Azure AD user *developer1 \@ contoso.com* öğesini rolebinding 'e bağlarsınız:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

AKS kümesinde *developer1 \@ contoso.com* kimlik doğrulaması yapıldığında, *finans-uygulama* ad alanındaki kaynaklar üzerinde tam izinlere sahip olurlar. Bu şekilde, kaynaklara erişimi mantıksal olarak ayırabilirsiniz ve kontrol edersiniz. Azure AD ile tümleştirmeyle birlikte Kubernetes RBAC kullanın.

Kubernetes RBAC kullanarak Kubernetes kaynaklarına erişimi denetlemek için Azure AD gruplarını nasıl kullanacağınızı görmek için bkz. [rol tabanlı erişim denetimi ve AKS 'teki Azure Active Directory kimliklerini kullanarak küme kaynaklarına erişimi denetleme][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Azure RBAC kullanma 
> **En iyi yöntemler kılavuzu** 
> 
> Bir veya daha fazla abonelikteki kaynak için gereken en düşük Kullanıcı ve grup izinlerini tanımlamak için Azure RBAC kullanın.

Bir AKS kümesini tam olarak çalıştırmak için iki erişim düzeyi gereklidir: 
1. Azure aboneliğinizdeki AKS kaynağına erişin. 

    Bu erişim düzeyi şunları yapmanıza olanak sağlar:
      * AKS API 'Lerini kullanarak kümenizi ölçeklendirmeye veya yükseltmeye yönelik denetim
      * Çekin `kubeconfig` .

    AKS kaynağına erişimin nasıl yapıldığını görmek için, `kubeconfig` bkz. [küme yapılandırma dosyasına erişimi sınırlandırma](control-kubeconfig-access.md).

2. Kubernetes API 'sine erişim. 
    
    Bu erişim düzeyi şu şekilde denetlenir:
    * [Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (Geleneksel) veya 
    * Azure RBAC 'i Kubernetes yetkilendirmesi için AKS ile tümleştirerek.

    Azure RBAC kullanarak Kubernetes API 'sine izin verme hakkında bilgi için bkz. [Kubernetes yetkilendirmesi Için Azure RBAC kullanma](manage-azure-rbac.md).

## <a name="use-pod-managed-identities"></a>Pod tarafından yönetilen kimlikleri kullanma

> **En iyi yöntemler kılavuzu** 
> 
> Pozlama veya kötüye kullanım riski altında olduklarından, Pod veya kapsayıcı görüntüleri içinde sabit kimlik bilgilerini kullanmayın. Bunun yerine, merkezi bir Azure AD kimlik çözümü kullanarak otomatik olarak erişim istemek için *Pod kimliklerini* kullanın. 

> [!NOTE]
> Pod kimlikleri yalnızca Linux Pod ve kapsayıcı görüntüleri ile kullanılmaya yöneliktir. Windows kapsayıcıları için pod tarafından yönetilen kimlikler desteği yakında kullanıma sunulacak.

Cosmos DB, Key Vault veya blob depolama gibi diğer Azure hizmetlerine erişmek için pod 'ın erişim kimlik bilgilerine ihtiyacı vardır. Kapsayıcı görüntüsü ile erişim kimlik bilgilerini tanımlayabilir veya onları bir Kubernetes gizli anahtarı olarak ekleyebilirsiniz. Her iki durumda da bunları el ile oluşturmanız ve atamanız gerekir. Genellikle bu kimlik bilgileri, Pod 'lerde yeniden kullanılır ve düzenli olarak döndürülemez.

Azure kaynakları için pod tarafından yönetilen kimlikler sayesinde, Azure AD aracılığıyla hizmetlere otomatik olarak erişim isteğinde bulabilirsiniz. Pod tarafından yönetilen kimlikler Şu anda AKS için önizleme aşamasındadır. Lütfen bkz. Azure Kubernetes hizmeti (Önizleme) içindeki [Azure Active Directory Pod tarafından yönetilen kimlikler kullanma (Önizleme) [(başlamak https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity) için belgeler. 

Pod 'lar için kimlik bilgilerini el ile tanımlamak yerine, Pod tarafından yönetilen kimlikler, bir erişim belirtecini gerçek zamanlı olarak ister, bu da yalnızca atanan hizmetlerine erişmek için kullanın. AKS 'de, Dizin 'lerin yönetilen kimlikleri kullanmasına izin veren işlemleri işleyen iki bileşen vardır:

* **Düğüm yönetimi kimliği (NMI) sunucusu** , aks kümesindeki her bir düğümde DaemonSet olarak çalışan bir pod 'dir. NMI sunucusu, Azure hizmetlerine Pod isteklerini dinler.
* **Azure Kaynak sağlayıcısı** , Kubernetes API sunucusunu sorgular ve pod 'a karşılık gelen bir Azure kimlik eşlemesini denetler.

Pod bir Azure hizmetine erişim isteğinde bulunduğunda, ağ kuralları trafiği NMI sunucusuna yönlendirir. 
1. NMI sunucusu:
    * Azure hizmetlerine erişim isteyen Pod 'leri uzak adreslerine göre belirler.
    * Azure Kaynak sağlayıcısı 'nı sorgular. 
1. Azure Kaynak sağlayıcısı, AKS kümesindeki Azure kimlik eşlemelerini denetler.
1. NMI sunucusu, Pod 'un kimlik eşlemesine göre Azure AD 'den bir erişim belirteci ister. 
1. Azure AD, Pod 'a döndürülen NMI sunucusuna erişim sağlar. 
    * Bu erişim belirteci, Pod tarafından Azure 'daki hizmetlere erişim istemek için kullanılabilir.

Aşağıdaki örnekte, bir geliştirici Azure SQL veritabanı 'na erişim istemek için yönetilen kimlik kullanan bir pod oluşturuyor:

![Pod kimlikleri, Pod 'ın diğer hizmetlere otomatik olarak erişim istemesine izin verir](media/operator-best-practices-identity/pod-identities.png)

1. Küme operatörü, bir pod hizmetlere erişim isteğinde bulunduğunda kimlikleri eşlemek için bir hizmet hesabı oluşturur.
1. Azure AD 'ye erişim belirteçleri için Azure Kaynak sağlayıcısı ile birlikte, NMI sunucusu, Pod isteklerini geçirmek üzere dağıtılır.
1. Bir geliştirici, NMI sunucusu aracılığıyla erişim belirteci isteyen yönetilen bir kimlikle Pod dağıtır.
1. Belirteç Pod 'a döndürülür ve Azure SQL veritabanına erişmek için kullanılır

> [!NOTE]
> Pod tarafından yönetilen kimlikler Şu anda önizleme durumundadır.

Pod tarafından yönetilen kimlikleri kullanmak için bkz. [Azure Kubernetes hizmetinde Azure Active Directory Pod tarafından yönetilen kimlikleri kullanma (Önizleme)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity).

## <a name="next-steps"></a>Sonraki adımlar

Bu en iyi yöntemler, kümenizin ve kaynaklarınızın kimlik doğrulama ve yetkilendirilmesine odaklanan makaledir. Bu en iyi uygulamalardan bazılarını uygulamak için aşağıdaki makalelere bakın:

* [Azure Active Directory AKS ile tümleştirme][aks-aad]
* [Azure Kubernetes hizmetinde Azure Active Directory Pod tarafından yönetilen kimlikler kullanma (Önizleme)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

AKS 'deki küme işlemleri hakkında daha fazla bilgi için aşağıdaki en iyi yöntemlere bakın:

* [Çok kiracılılık ve küme yalıtımı][aks-best-practices-cluster-isolation]
* [Temel Kubernetes Zamanlayıcı özellikleri][aks-best-practices-scheduler]
* [Gelişmiş Kubernetes Zamanlayıcı özellikleri][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
