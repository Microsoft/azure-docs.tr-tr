---
title: İşleç en iyi uygulamalar - Azure Kubernetes Hizmetleri (AKS) kimlik
description: Kimlik doğrulamasını yönetmek nasıl küme işleci en iyi yöntemler ve kümeler Azure Kubernetes Service (AKS) için yetkilendirme öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mlearned
ms.openlocfilehash: 82bf59dddeecab0addf00a935f55be8d1d7952d3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614788"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Kimlik doğrulama ve yetkilendirme Azure Kubernetes Service (AKS) için en iyi uygulamalar

Dağıtma ve bakımını kümeleri Azure Kubernetes Service (AKS) gibi kaynak ve hizmetlere erişimi yönetmenin yolları yapması gerekir. Bu denetimler ve bunlar gerekmez hizmetlerindeki kaynaklara erişim hesapları olabilir. Ayrıca hangi kimlik bilgileri kümesi, değişiklik yapmak için kullanıldığını izlenmesi zor olabilir.

Bu en iyi yöntemler makalesi, nasıl bir küme işleci erişim ve kimlik için AKS kümeleri yönetebilirsiniz üzerinde odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * AKS kümesi kullanıcılar Azure Active Directory ile kimlik doğrulaması
> * Rol tabanlı erişim denetimlerine (RBAC) ile kaynaklara erişimi denetlemek için
> * Diğer hizmetlerle doğrulatmak için yönetilen bir kimlik kullanın

## <a name="use-azure-active-directory"></a>Azure Active Directory kullanın

**En iyi uygulama kılavuzunu** -Azure AD Tümleştirmesi ile AKS dağıtma kümeleri. Azure AD kullanarak, kimlik yönetimi bileşeni merkeze alır. Herhangi bir kullanıcı hesabı veya grup durumu değişikliği AKS kümesi erişimi otomatik olarak güncelleştirilir. Rolleri veya ClusterRoles ve bağlamaları, kapsam kullanıcılar veya gruplar için gereken izinleri az miktarda için sonraki bölümde açıklandığı gibi kullanın.

Kubernetes kümenizin uygulama sahipleri ve geliştiriciler, farklı kaynaklara erişimi gerekir. Kubernetes, hangi kullanıcıların hangi kaynaklarla etkileşim denetlemek için bir kimlik yönetimi çözümü sağlamaz. Bunun yerine, genellikle kümeniz var olan bir kimlik çözümü ile tümleştirin. Azure Active Directory (AD), bir kurumsal kullanıma hazır kimlik yönetimi çözümü sağlar ve AKS küme ile tümleştirilebilir.

Azure AD ile tümleşik kümeleriyle aks'deki, oluşturduğunuz *rolleri* veya *ClusterRoles* kaynaklara erişim izinleri tanımlayın. Daha sonra *bağlama* kullanıcılar veya gruplar için Azure ad rolleri. Bu Kubernetes rol tabanlı erişim denetimlerine (RBAC), sonraki bölümde ele alınmıştır. Azure ad tümleştirmesi ve kaynaklarına erişimi nasıl denetleyebileceğiniz Aşağıdaki diyagramda görülebilir:

![AKS ile Azure Active Directory tümleştirme için küme düzeyinde kimlik doğrulaması](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Geliştirici Azure AD ile kimlik doğrulaması yapar.
1. Azure AD belirteç yayınında uç noktası, erişim belirteci verir.
1. Geliştirici gibi Azure AD belirtecini kullanarak bir eylem gerçekleştirir. `kubectl create pod`
1. Kubernetes ile Azure Active Directory belirteci doğrular ve geliştiricilere yönelik grup üyeliklerini getirir.
1. Kubernetes rol tabanlı erişim denetimi (RBAC) ve küme ilkeleri uygulanır.
1. Geliştirici istek başarılı ya da Azure AD grup üyeliği ve Kubernetes RBAC ilkelerini önceki doğrulama bağlı değildir.

Azure AD kullanan bir AKS kümesi oluşturmak için bkz [Azure Active Directory Tümleştirme ile AKS][aks-aad].

## <a name="use-role-based-access-controls-rbac"></a>Rol tabanlı erişim denetimlerine (RBAC) kullanın

**En iyi uygulama kılavuzunu** -küme içindeki kaynaklara kullanıcıları veya grupları sahip izinleri tanımlamak için kullanmak Kubernetes RBAC. Roller ve en az miktarda gerekli izinleri atamanız bağlamaları oluşturun. Kullanıcı durumu veya grup üyeliği herhangi bir değişiklik otomatik olarak güncelleştirilir ve küme kaynaklarında erişim geçerli olan Azure AD ile tümleştirin.

Kubernetes'te, küme kaynaklarında erişim ayrıntılı denetim sağlar. İzinleri küme seviyesinde veya belirli ad alanlarına tanımlanabilir. Hangi kaynakların yönetilebilir, tanımlamak ve hangi izinlere sahip. Bu roller kullanıcılara veya gruplara bir bağlama ile uygulanmış olan. Hakkında daha fazla bilgi için *rolleri*, *ClusterRoles*, ve *bağlamaları*, bkz: [erişim ve kimlik seçeneklerini Azure Kubernetes Service (AKS)][aks-concepts-identity].

Örnek olarak, kaynakları adlı ad alanındaki tam erişim veren bir rol oluşturabilirsiniz *Finans uygulama*aşağıdaki örnek YAML bildirimde gösterildiği gibi:

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

Bir RoleBinding sonra Azure AD kullanıcı bağlayan oluşturulur *developer1\@contoso.com* aşağıdaki YAML bildirimde gösterildiği RoleBinding için:

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

Zaman *developer1\@contoso.com* kimlik doğrulaması kaynakları için tam izinlere sahiptirler AKS kümesi karşı *Finans uygulama* ad alanı. Bu şekilde, mantıksal olarak ayrı ve denetim kaynaklara erişin. Kubernetes RBAC, Azure ile birlikte kullanılmalıdır AD-tümleştirmesi, önceki bölümde anlatıldığı gibidir.

RBAC kullanarak Kubernetes kaynaklarına erişimi denetlemek için Azure AD grupları kullanma hakkında bilgi için bkz: [AKS rol tabanlı erişim denetimlerine ve Azure Active Directory kimlikleri kullanarak küme kaynaklarında erişim denetimi][azure-ad-rbac].

## <a name="use-pod-identities"></a>Pod kimlikler kullanın

**En iyi uygulama kılavuzunu** -Etkilenme veya kötüye kullanımı riski olduğu gibi sabit kimlik bilgilerini pod'ların veya kapsayıcı görüntüleri kullanmayın. Bunun yerine, otomatik olarak kullanarak merkezi bir erişim istemek için pod kimlikleri kullanmak Azure AD kimlik çözümü. Pod kimlikler Linux pod'ların ve kapsayıcı görüntüleri ile kullanılmak için tasarlanmıştır.

Pod'ların Cosmos DB, anahtar kasası veya Blob Depolama gibi diğer Azure hizmetlerine erişim için gerektiğinde pod erişim kimlik bilgileri gerekir. Bu erişim kimlik bilgileri ile kapsayıcı görüntüsü tanımlanabilir veya Kubernetes gizli eklenmiş ancak el ile oluşturulan ve atanmış gerekir. Genellikle, kimlik bilgilerini pod'ları arasında yeniden ve düzenli olarak Döndürülmüş değildir.

(Şu anda ilişkili AKS açık kaynaklı proje uygulanan) Azure kaynakları için yönetilen kimlik Hizmetleri aracılığıyla Azure AD için erişim isteği otomatik olarak sağlar. Kimlik bilgilerini el ile tanımlamak yoksa, pod'ları için bunun yerine gerçek zamanlı bir erişim belirteci isteği ve bunların atanmış hizmetlere erişmek için kullanabilirsiniz. AKS, küme işleci tarafından yönetilen kimlikleri kullanmak pod'ların izin vermek için iki bileşenden dağıtılır:

* **Düğüm yönetim kimlik (NMI) sunucu** AKS kümesindeki her düğümde bir DaemonSet çalışan bir pod olduğu. NMI sunucunun Azure hizmetlerine pod isteklerini dinler.
* **Yönetilen kimlik denetleyici (MIC)** merkezi bir pod Kubernetes API sunucusu sorgulamak için gerekli izinlere sahip olduğundan ve bir Azure kimlik eşlemesini karşılık gelen bir pod için denetler.

Pod'ların bir Azure hizmetine erişim istediğinde, ağ kuralları düğümü yönetim kimlik (NMI) sunucusuna trafiği yönlendirin. Azure hizmetlerine erişim için kendi uzak adresini temel alan ve yönetilen kimlik denetleyici (MIC) sorgular isteği pod'ların NMI sunucuyu tanımlar. MIC AKS kümesi ve NMI sunucunun Azure kimlik eşlemeleri denetler, ardından gelen Azure Active Directory (AD) erişim belirteci pod'ın kimlik eşlemesini göre ister. Azure AD için pod döndürülen NMI sunucuya erişim sağlar. Bu erişim belirteci, ardından Azure hizmetlerine erişime izin istemek için pod tarafından kullanılabilir.

Aşağıdaki örnekte, bir geliştirici, Azure SQL Server örneğine erişim istemek için bir yönetilen kimlik kullanan bir pod oluşturur:

![Pod kimlikler otomatik olarak diğer hizmetlere erişimi istemek bir pod izin ver](media/operator-best-practices-identity/pod-identities.png)

1. Küme işleci, ilk pod'ların hizmetlerine erişim istediğinde, kimliklerini eşlemek için kullanılan bir hizmet hesabı oluşturur.
1. MIC ve NMI sunucudaki tüm Azure ad erişim belirteçlerini pod isteklerinde aktarma dağıtılır.
1. Bir geliştirici, bir pod NMI sunucu üzerinden bir erişim belirteci isteklerini yönetilen bir kimlikle dağıtır.
1. Belirteç pod'u döndürdü ve bir Azure SQL Server örneğine erişmek için kullanılır.

> [!NOTE]
> Yönetilen pod kimlikler, açık kaynak bir projedir ve Azure teknik destek birimi tarafından desteklenmiyor.

Pod kimlikleri kullanmak için bkz: [Kubernetes uygulamaları için Azure Active Directory kimlikleri][aad-pod-identity].

## <a name="next-steps"></a>Sonraki adımlar

Kimlik doğrulaması ve yetkilendirme küme ve kaynaklar için bu en iyi yöntemler makalesi odaklanır. Bazı bu en iyi yöntemleri uygulamak için aşağıdaki makalelere bakın:

* [AKS ile Azure Active Directory Tümleştirme][aks-aad]
* [AKS ile Azure kaynakları için yönetilen kimlikleri kullanmak][aad-pod-identity]

AKS kümesi işlemleri hakkında daha fazla bilgi için aşağıdaki en iyi bakın:

* [Çok kiracılılık ve küme ayırma][aks-best-practices-scheduler]
* [Temel Kubernetes Zamanlayıcı Özellikleri][aks-best-practices-scheduler]
* [Gelişmiş Kubernetes Zamanlayıcı Özellikleri][aks-best-practices-advanced-scheduler]

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
