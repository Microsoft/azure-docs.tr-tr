---
title: Kavramlar-Azure Kubernetes Hizmetleri 'nde (AKS) erişim ve kimlik
description: Azure Active Directory tümleştirme, Kubernetes rol tabanlı erişim denetimi (RBAC) ve roller ve bağlamalar dahil olmak üzere Azure Kubernetes Service (AKS) içindeki erişim ve kimlik hakkında bilgi edinin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: a1ed1eccd7a10d78cd503559469654e5562cde0c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "67615858"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) için erişim ve kimlik seçenekleri

Kubernetes kümeleriyle kimlik doğrulamak ve güvenli hale getirmek için farklı yollar vardır. Rol tabanlı erişim denetimlerini (RBAC) kullanarak kullanıcılara veya gruplara yalnızca ihtiyaç duydukları kaynaklara erişim izni verebilirsiniz. Azure Kubernetes hizmeti (AKS) ile Azure Active Directory kullanarak güvenlik ve izin yapısını daha da geliştirebilirsiniz. Bu yaklaşımlar, uygulama iş yüklerinizin ve müşteri verilerinin güvenliğini sağlamanıza yardımcı olur.

Bu makalede, AKS 'de izinleri kimlik doğrulamasından ve atamaya yardımcı olan temel kavramlar tanıtılmaktadır:

- [Kubernetes hizmet hesapları](#kubernetes-service-accounts)
- [Azure Active Directory tümleştirme](#azure-active-directory-integration)
- [Rol tabanlı erişim denetimleri (RBAC)](#role-based-access-controls-rbac)
- [Roller ve Kümerolleri](#roles-and-clusterroles)
- [RoleBindings ve ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes hizmet hesapları

Kubernetes 'deki birincil kullanıcı türlerinden biri bir *hizmet hesabıdır*. ' De bir hizmet hesabı bulunur ve, Kubernetes API 'SI tarafından yönetilir. Hizmet hesapları için kimlik bilgileri Kubernetes gizli dizileri olarak depolanır, bu da API sunucusuyla iletişim kurmak için yetkili Pod tarafından kullanılmasına olanak tanır. Çoğu API isteği, bir hizmet hesabı veya normal bir kullanıcı hesabı için bir kimlik doğrulama belirteci sağlar.

Normal Kullanıcı hesapları yalnızca hizmetler ve süreçler değil insan yöneticileri veya geliştiriciler için daha geleneksel erişime izin verir. Kubernetes, normal kullanıcı hesaplarının ve parolaların depolandığı bir kimlik yönetimi çözümü sağlamıyor. Bunun yerine, dış kimlik çözümleri Kubernetes ile tümleştirilebilir. AKS kümelerinde, bu tümleşik kimlik çözümü Azure Active Directory.

Kubernetes 'in kimlik seçenekleri hakkında daha fazla bilgi için bkz. [Kubernetes kimlik doğrulaması][kubernetes-authentication].

## <a name="azure-active-directory-integration"></a>Azure Active Directory tümleştirmesi

AKS kümelerinin güvenliği Azure Active Directory (AD) Tümleştirmesi ile geliştirilebilir. Kurumsal kimlik yönetimi 'nde geliştirilen Azure AD, temel dizin hizmetlerini, uygulama erişim yönetimini ve kimlik korumasını birleştiren çok kiracılı, bulut tabanlı bir dizin ve kimlik yönetimi hizmetidir. Azure AD ile, hesap yönetimi ve güvenlik için tek bir kaynak sağlamak üzere şirket içi kimlikleri AKS kümeleriyle tümleştirebilirsiniz.

![AKS kümeleriyle Azure Active Directory tümleştirme](media/concepts-identity/aad-integration.png)

Azure AD ile tümleşik AKS kümeleri sayesinde, kullanıcılara veya gruplara bir ad alanı veya küme genelinde Kubernetes kaynaklarına erişim izni verebilirsiniz. Bir `kubectl` yapılandırma bağlamı elde etmek için, Kullanıcı [az aks Get-Credentials][az-aks-get-credentials] komutunu çalıştırabilir. Bir Kullanıcı daha sonra aks kümesiyle `kubectl`etkileşime geçtiğinde Azure AD kimlik bilgileriyle oturum açması istenir. Bu yaklaşım Kullanıcı hesabı yönetimi ve parola kimlik bilgileri için tek bir kaynak sağlar. Kullanıcı yalnızca küme yöneticisi tarafından tanımlanan kaynaklara erişebilir.

AKS kümelerinde Azure AD kimlik doğrulaması, OAuth 2,0 protokolünün üstünde oluşturulmuş bir kimlik katmanı olan OpenID Connect 'i kullanır. OAuth 2,0, korumalı kaynaklara erişmek için erişim belirteçleri alma ve kullanma mekanizmalarını tanımlar ve OpenID Connect, kimlik doğrulamasını OAuth 2,0 yetkilendirme işlemine bir uzantı olarak uygular. OpenID Connect hakkında daha fazla bilgi için bkz. [Açık kimlik bağlantısı belgeleri][openid-connect]. OpenID Connect aracılığıyla Azure AD 'den alınan kimlik doğrulama belirteçlerini doğrulamak için, AKS kümeleri Kubernetes Web kancası belirteci kimlik doğrulamasını kullanır. Daha fazla bilgi için [Web kancası belirteci kimlik doğrulama belgelerine][webhook-token-docs]bakın.

## <a name="role-based-access-controls-rbac"></a>Rol tabanlı erişim denetimleri (RBAC)

Kullanıcıların gerçekleştirebileceği eylemlerin parçalı filtrelemesini sağlamak için Kubernetes rol tabanlı erişim denetimleri (RBAC) kullanır. Bu denetim mekanizması, kullanıcıları veya Kullanıcı gruplarını atamanıza izin verir, kaynak oluşturma veya değiştirme gibi işlemleri yapma veya çalışan uygulama iş yüklerinden günlükleri görüntüleme izni verir. Bu izinler tek bir ad alanı kapsamında olabilir veya tüm AKS kümesi genelinde verilebilir. Kubernetes RBAC ile, izinleri tanımlamak için *Roller* oluşturun ve ardından bu rolleri *rol bağlamalarıyla*kullanıcılara atayın.

Daha fazla bilgi için bkz. [RBAC yetkilendirmesi kullanma][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Azure rol tabanlı erişim denetimleri (RBAC)
Kaynaklara erişimi denetlemek için ek bir mekanizma, Azure rol tabanlı erişim denetimleri (RBAC). Kubernetes RBAC, AKS kümenizdeki kaynaklarla çalışmak üzere tasarlanmıştır ve Azure RBAC, Azure aboneliğinizdeki kaynaklar üzerinde çalışmak üzere tasarlanmıştır. Azure RBAC ile uygulanacak izinleri özetleyen bir *rol tanımı* oluşturursunuz. Daha sonra bir kullanıcı veya gruba, tek bir kaynak, bir kaynak grubu veya abonelik genelinde belirli bir *kapsam*için bu rol tanımı atanır.

Daha fazla bilgi için bkz. [Azure RBAC nedir?][azure-rbac]

## <a name="roles-and-clusterroles"></a>Roller ve Kümerolleri

Kubernetes RBAC ile kullanıcılara izin atamadan önce bu izinleri bir *rol*olarak tanımlarsınız. Kubernetes rolleri izinleri *verir* . *Reddetme* izni kavramı yoktur.

Roller bir ad alanı içinde izinler vermek için kullanılır. Tüm küme genelinde izin vermeniz veya belirli bir ad alanı dışında küme kaynakları sağlamanız gerekiyorsa, bunun yerine *Clusterroles*kullanabilirsiniz.

Kümerolü, kaynaklara izin vermek için aynı şekilde çalışarak, ancak belirli bir ad alanı değil tüm kümedeki kaynaklara uygulanabilir.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings ve ClusterRoleBindings

Roller, kaynaklara izinler vermek üzere tanımlandıktan sonra, bu Kubernetes RBAC izinlerini bir *Rolebinding*ile atarsınız. AKS kümeniz Azure Active Directory ile tümleşiyorsa, bağlamalar bu Azure AD kullanıcılarına küme içinde eylem gerçekleştirme izinleri verilme iznleridir.

Rol bağlamaları, belirli bir ad alanı için roller atamak üzere kullanılır. Bu yaklaşım, tek bir AKS kümesini mantıksal olarak ayırt etmenizi sağlar, böylece kullanıcılar yalnızca atanan ad alanındaki uygulama kaynaklarına erişebilir. Rolleri tüm küme genelinde veya belirli bir ad alanı dışındaki küme kaynaklarına bağlamanız gerekiyorsa, *Clusterrolebindings*kullanabilirsiniz.

ClusterRoleBinding, rolleri kullanıcılara bağlamanın aynı şekilde çalışacaktır, ancak belirli bir ad alanı değil tüm küme genelinde kaynaklara uygulanabilir. Bu yaklaşım, yöneticilerin veya Destek mühendislerinin AKS kümesindeki tüm kaynaklara erişmesine izin vermenizi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD ve Kubernetes RBAC ile çalışmaya başlamak için bkz. [Azure Active Directory AKS Ile tümleştirme][aks-aad].

İlişkili en iyi uygulamalar için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][operator-best-practices-identity].

Temel Kubernetes ve AKS kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes/AKS kümeleri ve iş yükleri][aks-concepts-clusters-workloads]
- [Kubernetes/AKS güvenliği][aks-concepts-security]
- [Kubernetes/AKS sanal ağları][aks-concepts-network]
- [Kubernetes/AKS depolaması][aks-concepts-storage]
- [Kubernetes/AKS ölçeği][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v1-protocols-openid-connect-code.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
