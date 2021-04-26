---
title: Kavramlar-Azure Kubernetes Hizmetleri 'nde (AKS) erişim ve kimlik
description: Azure Active Directory tümleştirme, Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) ve roller ve bağlamalar dahil olmak üzere Azure Kubernetes Service (AKS) içindeki erişim ve kimlik hakkında bilgi edinin.
services: container-service
ms.topic: conceptual
ms.date: 03/24/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: b10d31cf069bc4f28a1597ec12160fa6ed98b8ce
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789564"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) için erişim ve kimlik seçenekleri

Kubernetes kümelerine çok çeşitli yollarla kimlik doğrulaması, yetkilendirme, güvenli hale getirme ve denetim erişimi sağlayabilirsiniz. 
* Kubernetes rol tabanlı erişim denetimini (Kubernetes RBAC) kullanarak kullanıcılara, gruplara ve hizmet hesaplarına yalnızca ihtiyaç duydukları kaynaklara erişim izni verebilirsiniz. 
* Azure Kubernetes Service (AKS) ile Azure Active Directory ve Azure RBAC aracılığıyla güvenlik ve izin yapısını daha da geliştirebilirsiniz. 

Kubernetes RBAC ve AKS, küme erişiminizi güvenli hale getirmenize ve yalnızca geliştiricilere ve işleçlere gereken en düşük izinleri sağlamanıza yardımcı olur.

Bu makalede, AKS 'de izinleri kimlik doğrulamasından ve atamanıza yardımcı olan temel kavramlar tanıtılmaktadır.

## <a name="aks-service-permissions"></a>AKS hizmeti izinleri

Bir küme oluştururken, AKS, Kullanıcı adına kümeyi oluşturmak ve çalıştırmak için ihtiyaç duyacağı kaynakları (VM 'Ler ve NIC 'Ler gibi) oluşturur veya değiştirir. Bu kimlik, küme oluşturma sırasında oluşturulan kümenin kimlik izninden farklıdır.

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>Küme izinlerini oluşturma ve çalıştırma kimliği

Kümeyi oluşturma ve çalıştırma kimliği için aşağıdaki izinler gereklidir.

| İzin | Nedeni |
|---|---|
| `Microsoft.Compute/diskEncryptionSets/read` | Disk şifreleme kümesi KIMLIĞINI okumak için gereklidir. |
| `Microsoft.Compute/proximityPlacementGroups/write` | Yakınlık yerleşimi gruplarını güncelleştirmek için gereklidir. |
| `Microsoft.Network/applicationGateways/read` <br/> `Microsoft.Network/applicationGateways/write` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Uygulama ağ geçitlerini yapılandırmak ve alt ağa katmak için gereklidir. |
| `Microsoft.Network/virtualNetworks/subnets/join/action` | Özel VNET kullanılırken alt ağ için ağ güvenlik grubunu yapılandırmak için gereklidir.|
| `Microsoft.Network/publicIPAddresses/join/action` <br/> `Microsoft.Network/publicIPPrefixes/join/action` | Standart Load Balancer giden genel IP 'Leri yapılandırmak için gereklidir. |
| `Microsoft.OperationalInsights/workspaces/sharedkeys/read` <br/> `Microsoft.OperationalInsights/workspaces/read` <br/> `Microsoft.OperationsManagement/solutions/write` <br/> `Microsoft.OperationsManagement/solutions/read` <br/> `Microsoft.ManagedIdentity/userAssignedIdentities/assign/action` | Log Analytics çalışma alanları ve kapsayıcılar için Azure izleme oluşturma ve güncelleştirme için gereklidir. |

### <a name="aks-cluster-identity-permissions"></a>AKS kümesi kimlik izinleri

Aşağıdaki izinler, aks kümesiyle oluşturulup ilişkili olan AKS kümesi kimliği tarafından kullanılır. Her izin aşağıdaki nedenlerle kullanılır:

| İzin | Nedeni |
|---|---|
| `Microsoft.ContainerService/managedClusters/*`  <br/> | Kullanıcı oluşturmak ve kümeyi çalıştırmak için gereklidir
| `Microsoft.Network/loadBalancers/delete` <br/> `Microsoft.Network/loadBalancers/read` <br/> `Microsoft.Network/loadBalancers/write` | Bir LoadBalancer hizmeti için yük dengeleyiciyi yapılandırmak için gereklidir. |
| `Microsoft.Network/publicIPAddresses/delete` <br/> `Microsoft.Network/publicIPAddresses/read` <br/> `Microsoft.Network/publicIPAddresses/write` | Bir LoadBalancer hizmeti için genel IP 'Leri bulmak ve yapılandırmak için gereklidir. |
| `Microsoft.Network/publicIPAddresses/join/action` | Bir LoadBalancer hizmeti için genel IP 'Leri yapılandırmak için gereklidir. |
| `Microsoft.Network/networkSecurityGroups/read` <br/> `Microsoft.Network/networkSecurityGroups/write` | Bir LoadBalancer hizmeti için güvenlik kuralları oluşturmak veya silmek için gereklidir. |
| `Microsoft.Compute/disks/delete` <br/> `Microsoft.Compute/disks/read` <br/> `Microsoft.Compute/disks/write` <br/> `Microsoft.Compute/locations/DiskOperations/read` | AzureDisks 'yi yapılandırmak için gereklidir. |
| `Microsoft.Storage/storageAccounts/delete` <br/> `Microsoft.Storage/storageAccounts/listKeys/action` <br/> `Microsoft.Storage/storageAccounts/read` <br/> `Microsoft.Storage/storageAccounts/write` <br/> `Microsoft.Storage/operations/read` | AzureFile veya AzureDisk için depolama hesaplarını yapılandırmak için gereklidir. |
| `Microsoft.Network/routeTables/read` <br/> `Microsoft.Network/routeTables/routes/delete` <br/> `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` <br/> `Microsoft.Network/routeTables/write` | Düğümler için rota tabloları ve rotalar yapılandırmak için gereklidir. |
| `Microsoft.Compute/virtualMachines/read` | Bölgeler, hata etki alanı, boyut ve veri diskleri gibi sanal makinelere yönelik bilgileri bulmak için gereklidir. |
| `Microsoft.Compute/virtualMachines/write` | Bir sanal makineye AzureDisks eklemek için gerekir. |
| `Microsoft.Compute/virtualMachineScaleSets/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read` <br/> `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read` | Bölgeler, hata etki alanı, boyut ve veri diskleri gibi bir sanal makine ölçek kümesindeki sanal makinelere ilişkin bilgileri bulmak için gereklidir. |
| `Microsoft.Network/networkInterfaces/write` | Bir sanal makineyi bir yük dengeleyici arka uç adres havuzuna bir VMAS 'e eklemek için gereklidir. |
| `Microsoft.Compute/virtualMachineScaleSets/write` | Bir sanal makine ölçek kümesini yük dengeleyici arka uç adres havuzlarına eklemek ve bir sanal makine ölçek kümesindeki düğümleri ölçeklendirmek için gereklidir. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write` | AzureDisks iliştirmek ve sanal makine ölçek kümesinden yük dengeleyiciye bir sanal makine eklemek için gereklidir. |
| `Microsoft.Network/networkInterfaces/read` | Sanal makineler için iç IP 'Leri ve yük dengeleyici arka uç adres havuzlarını bir VMALAR içinde aramak için gereklidir. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read` | Sanal makine ölçek kümesindeki bir sanal makine için iç IP 'Leri ve yük dengeleyici arka uç adres havuzlarını aramak için gereklidir. |
| `Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipconfigurations/publicipaddresses/read` | Bir sanal makine ölçek kümesindeki bir sanal makine için genel IP 'Leri bulmak için gereklidir. |
| `Microsoft.Network/virtualNetworks/read` <br/> `Microsoft.Network/virtualNetworks/subnets/read` | Başka bir kaynak grubundaki iç yük dengeleyici için bir alt ağın mevcut olup olmadığını doğrulamak için gereklidir. |
| `Microsoft.Compute/snapshots/delete` <br/> `Microsoft.Compute/snapshots/read` <br/> `Microsoft.Compute/snapshots/write` | AzureDisk için anlık görüntüleri yapılandırmak için gereklidir. |
| `Microsoft.Compute/locations/vmSizes/read` <br/> `Microsoft.Compute/locations/operations/read` | AzureDisk Birim sınırlarını bulmak için sanal makine boyutlarını bulmak için gereklidir. |

### <a name="additional-cluster-identity-permissions"></a>Ek küme kimliği izinleri

Belirli özniteliklere sahip bir küme oluştururken, küme kimliği için aşağıdaki ek izinlere sahip olmanız gerekir. Bu izinler otomatik olarak atanmadığından, oluşturulduktan sonra bunları küme kimliğine eklemeniz gerekir.

| İzin | Nedeni |
|---|---|
| `Microsoft.Network/networkSecurityGroups/write` <br/> `Microsoft.Network/networkSecurityGroups/read` | Başka bir kaynak grubunda ağ güvenlik grubu kullanılıyorsa gereklidir. Bir LoadBalancer hizmeti için güvenlik kurallarını yapılandırmak için gereklidir. |
| `Microsoft.Network/virtualNetworks/subnets/read` <br/> `Microsoft.Network/virtualNetworks/subnets/join/action` | Özel VNET gibi başka bir kaynak grubunda bir alt ağ kullanılıyorsa gereklidir. |
| `Microsoft.Network/routeTables/routes/read` <br/> `Microsoft.Network/routeTables/routes/write` | Özel bir yol tablosu olan özel VNET gibi başka bir kaynak grubundaki yol tablosuyla ilişkili bir alt ağ kullanılıyorsa gereklidir. Diğer kaynak grubundaki alt ağ için bir alt ağın zaten mevcut olup olmadığını doğrulamak için gereklidir. |
| `Microsoft.Network/virtualNetworks/subnets/read` | Başka bir kaynak grubunda iç yük dengeleyici kullanılıyorsa gereklidir. Kaynak grubundaki iç yük dengeleyici için bir alt ağın zaten mevcut olup olmadığını doğrulamak için gereklidir. |
| `Microsoft.Network/privatednszones/*` | Özel bir privateDNSZone gibi başka bir kaynak grubunda özel bir DNS bölgesi kullanılıyorsa gereklidir. |

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

Kubernetes RBAC, Kullanıcı eylemlerinin parçalı filtrelemesini sağlar. Bu denetim mekanizmasıyla:
* Kullanıcıları veya Kullanıcı gruplarını, kaynakları oluşturma ve değiştirme veya çalışan uygulama iş yüklerinden günlükleri görüntüleme izni atayabilirsiniz. 
* İzinleri tek bir ad alanı veya tüm AKS kümesi genelinde kapsamını belirleyebilirsiniz. 
* İzinleri tanımlamak için *Roller* oluşturun ve ardından bu rolleri *rol bağlamalarıyla* kullanıcılara atayın.

Daha fazla bilgi için bkz. [Kubernetes RBAC yetkilendirmesini kullanma][kubernetes-rbac].

### <a name="roles-and-clusterroles"></a>Roller ve Kümerolleri

#### <a name="roles"></a>Roller
Kubernetes RBAC ile kullanıcılara izin atamadan önce, Kullanıcı izinlerini *rol* olarak tanımlayacaksınız. Rolleri kullanarak bir ad alanı içinde izin verin. 

> [!NOTE]
> Kubernetes rolleri izin *verir* ; İzinler *reddedilmez* .

Tüm küme genelinde veya belirli bir ad alanı dışındaki küme kaynakları için izin vermek üzere *Clusterroles* kullanabilirsiniz.

#### <a name="clusterroles"></a>ClusterRoles

Bir ClusterRole, belirli bir ad alanı değil tüm küme genelinde kaynaklara izin verir ve izinleri uygular.

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings ve ClusterRoleBindings

Kaynaklara izinler vermek için roller tanımladıktan sonra, bu Kubernetes RBAC izinlerini bir *Rolebinding* ile atarsınız. AKS kümeniz [Azure Active Directory (Azure AD) ile tümleşiyorsa](#azure-ad-integration), rolebindings küme içinde eylem gerçekleştirmek IÇIN Azure AD kullanıcılarına izinler verir. Bkz. [Kubernetes rol tabanlı erişim denetimi ve Azure Active Directory kimliklerini kullanarak küme kaynaklarına erişimi denetleme](azure-ad-rbac.md).

#### <a name="rolebindings"></a>RoleBindings

RoleBindings kullanarak belirli bir ad alanı için kullanıcılara roller atayın. RoleBindings ile tek bir AKS kümesini mantıksal olarak ayırabilirsiniz, ancak kullanıcıların kendilerine atanan ad alanındaki uygulama kaynaklarına erişmesini sağlayabilirsiniz. 

Rolleri tüm küme genelinde veya belirli bir ad alanı dışındaki küme kaynaklarına bağlamak için *Clusterrolebindings* kullanın.

#### <a name="clusterrolebinding"></a>ClusterRoleBinding

ClusterRoleBinding ile, rolleri kullanıcılara bağlarsınız ve belirli bir ad alanı değil tüm kümedeki kaynaklara uygularsınız. Bu yaklaşım, yöneticilerin veya Destek mühendislerinin AKS kümesindeki tüm kaynaklara erişmesine izin vermenizi sağlar.


> [!NOTE]
> Microsoft/AKS, yerleşik bir Kubernetes rolü `aks-service` ve yerleşik rol bağlama altında Kullanıcı onayı ile herhangi bir küme eylemi gerçekleştirir `aks-service-rolebinding` . 
> 
> Bu rol, AKS 'in küme sorunlarını giderme ve tanılama, ancak izinleri değiştiremeyeceği ya da roller ya da rol bağlamaları ya da diğer yüksek ayrıcalıklı eylemler oluşturmalarına olanak sağlar. Rol erişimi yalnızca, tam zamanında (JıT) erişime sahip etkin destek biletleri altında etkinleştirilir. [Aks destek ilkeleri](support-policies.md)hakkında daha fazla bilgi edinin.


### <a name="kubernetes-service-accounts"></a>Kubernetes hizmet hesapları

*Hizmet hesapları* , Kubernetes içindeki birincil kullanıcı türlerinden biridir. Kubernetes API 'SI hizmet hesaplarını tutar ve yönetir. Hizmet hesabı kimlik bilgileri, Kubernetes gizli dizileri olarak depolanır ve bu, API sunucusuyla iletişim kurmak için yetkili Pod tarafından kullanılmasına izin verir. Çoğu API isteği, bir hizmet hesabı veya normal bir kullanıcı hesabı için bir kimlik doğrulama belirteci sağlar.

Normal Kullanıcı hesapları yalnızca hizmetler ve süreçler değil insan yöneticileri veya geliştiriciler için daha geleneksel erişime izin verir. Kubernetes normal kullanıcı hesaplarını ve parolaları depolamak üzere bir kimlik yönetimi çözümü sağlamıyorsa, dış kimlik çözümlerini Kubernetes ile tümleştirebilirsiniz. AKS kümelerinde, bu tümleşik kimlik çözümü Azure AD ' dir.

Kubernetes 'in kimlik seçenekleri hakkında daha fazla bilgi için bkz. [Kubernetes kimlik doğrulaması][kubernetes-authentication].

## <a name="azure-ad-integration"></a>Azure AD tümleştirmesi

Azure AD tümleştirmesiyle AKS kümesi güvenlerinizi geliştirin. Kurumsal kimlik yönetimi 'nde geliştirilen Azure AD, temel Dizin Hizmetleri, uygulama erişimi yönetimi ve kimlik korumasını birleştiren çok kiracılı, bulut tabanlı bir dizin ve kimlik yönetimi hizmetidir. Azure AD ile, hesap yönetimi ve güvenlik için tek bir kaynak sağlamak üzere şirket içi kimlikleri AKS kümeleriyle tümleştirebilirsiniz.

![AKS kümeleriyle Azure Active Directory tümleştirme](media/concepts-identity/aad-integration.png)

Azure AD ile tümleşik AKS kümeleri sayesinde, kullanıcılara veya gruplara bir ad alanı veya küme genelinde Kubernetes kaynaklarına erişim izni verebilirsiniz. 

1. Bir `kubectl` yapılandırma bağlamı almak için, Kullanıcı [az aks Get-Credentials][az-aks-get-credentials] komutunu çalıştırır. 
1. Bir Kullanıcı AKS kümesiyle etkileşime geçtiğinde `kubectl` Azure AD kimlik bilgileriyle oturum açması istenir. 

Bu yaklaşım Kullanıcı hesabı yönetimi ve parola kimlik bilgileri için tek bir kaynak sağlar. Kullanıcı yalnızca küme yöneticisi tarafından tanımlanan kaynaklara erişebilir.

Azure AD kimlik doğrulaması, OpenID Connect ile AKS kümelerine sağlanır. OpenID Connect, OAuth 2,0 protokolünün üstünde oluşturulmuş bir kimlik katmanıdır. OpenID Connect hakkında daha fazla bilgi için bkz. [Açık kimlik bağlantısı belgeleri][openid-connect]. Kubernetes kümesinin içinden, kimlik doğrulama belirteçlerini doğrulamak için [Web kancası belirteci kimlik doğrulaması][webhook-token-docs] kullanılır. Web kancası belirteci kimlik doğrulaması, AKS kümesinin bir parçası olarak yapılandırılır ve yönetilir.

### <a name="webhook-and-api-server"></a>Web kancası ve API sunucusu

![Web kancası ve API sunucusu kimlik doğrulama akışı](media/concepts-identity/auth-flow.png)

Yukarıdaki grafikte gösterildiği gibi, API sunucusu AKS Web kancası sunucusunu çağırır ve aşağıdaki adımları gerçekleştirir:

1. `kubectl` , [OAuth 2,0 cihaz yetkilendirme verme akışı](../active-directory/develop/v2-oauth2-device-code.md)ile kullanıcıların oturum açmasını sağlamak IÇIN Azure AD istemci uygulamasını kullanır.
2. Azure AD, bir access_token, id_token ve bir refresh_token sağlar.
3. Kullanıcı bir access_token ile bir istek yapar `kubectl` `kubeconfig` .
4. `kubectl` access_token API sunucusuna gönderir.
5. API sunucusu, doğrulama gerçekleştirmek için auth Web kancası sunucusuyla yapılandırılır.
6. Kimlik doğrulama Web kancası sunucusu, Azure AD ortak imzalama anahtarını denetleyerek JSON Web Token imzasının geçerli olduğunu onaylar.
7. Sunucu uygulaması, oturum açmış kullanıcının grup üyeliklerini MS Graph API sorgulamak için Kullanıcı tarafından sağlanmış kimlik bilgilerini kullanır.
8. API sunucusuna, erişim belirtecinin Kullanıcı asıl adı (UPN) talebi ve nesne KIMLIĞINE göre kullanıcının grup üyeliği gibi Kullanıcı bilgilerini içeren bir yanıt gönderilir.
9. API, Kubernetes role/RoleBinding ' i temel alan bir yetkilendirme kararı uygular.
10. Yetkilendirildikten sonra, API sunucusu öğesine bir yanıt döndürür `kubectl` .
11. `kubectl` kullanıcıya geri bildirim sağlar.
 
[Aks tarafından yönetilen Azure AD tümleştirmesi nasıl yapılır kılavuzumuzu](managed-aad.md)kullanarak aks 'ı Azure AD ile tümleştirmeyi öğrenin.

## <a name="azure-role-based-access-control"></a>Azure rol tabanlı erişim denetimi

Azure rol tabanlı erişim denetimi (RBAC), Azure kaynakları üzerinde ayrıntılı erişim yönetimi sağlayan [Azure Resource Manager](../azure-resource-manager/management/overview.md) yerleşik bir yetkilendirme sistemidir.

| RBAC sistemi | Description |
|---|---|
| Kubernetes RBAC | AKS kümenizdeki Kubernetes kaynakları üzerinde çalışmak üzere tasarlanmıştır. |
| Azure RBAC | Azure aboneliğinizdeki kaynaklar üzerinde çalışmak üzere tasarlanmıştır. |

Azure RBAC ile uygulanacak izinleri özetleyen bir *rol tanımı* oluşturursunuz. Daha sonra bir Kullanıcı atayabilir veya bu rol tanımını belirli bir *kapsam* için *rol ataması* aracılığıyla gruplayabilirsiniz. Kapsam, tek bir kaynak, bir kaynak grubu veya abonelik genelinde olabilir.

Daha fazla bilgi için bkz. [Azure rol tabanlı erişim denetimi (Azure RBAC) nedir?][azure-rbac]

Bir AKS kümesini tam olarak çalıştırmak için iki erişim düzeyi gereklidir: 
* [Azure aboneliğinizdeki AKS kaynağına erişin](#azure-rbac-to-authorize-access-to-the-aks-resource). 
  * AKS API 'Lerini kullanarak kümenizi ölçeklendirmeye veya yükseltmeye yönelik denetim.
  * Çekin `kubeconfig` .
* Kubernetes API 'sine erişim. Bu erişim şu şekilde denetlenir:
  * [Kubernetes RBAC](#kubernetes-rbac) (Geleneksel).
  * [Azure RBAC 'ı Kubernetes yetkilendirmesi için AKS Ile tümleştirme](#azure-rbac-for-kubernetes-authorization-preview).

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>AKS kaynağına erişimi yetkilendirmek için Azure RBAC

Azure RBAC ile kullanıcılarınıza (veya kimliklere) bir veya daha fazla abonelik genelinde AKS kaynaklarına ayrıntılı erişim sağlayabilirsiniz. Örneğin, kümenizi ölçeklendirmek ve yükseltmek için [Azure Kubernetes hizmet katılımcısı rolünü](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role) kullanabilirsiniz. Bu arada, [Azure Kubernetes hizmet kümesi yönetici rolüne](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) sahip başka bir kullanıcının yalnızca yöneticiyi çekme izni vardır `kubeconfig` .

Alternatif olarak, kullanıcıya genel [katkıda bulunan](../role-based-access-control/built-in-roles.md#contributor) rolünü verebilirsiniz. Genel katkıda bulunan rolüyle, kullanıcılar, izinleri yönetme dışında, AKS kaynağında, yukarıdaki izinleri ve tüm işlemleri gerçekleştirebilir.

[AKS 'Deki Kubernetes yapılandırma dosyasına erişim tanımlamak Için Azure RBAC kullanın](control-kubeconfig-access.md).

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Kubernetes yetkilendirmesi için Azure RBAC (Önizleme)

Azure RBAC tümleştirmesiyle, Azure AD ile tümleşik Kubernetes küme kaynağı izinlerini ve atamalarını Azure rol tanımı ve rol atamaları kullanarak yönetebilmeniz için AKS bir Kubernetes yetkilendirme Web kancası sunucusunu kullanacaktır.

![Kubernetes yetkilendirme akışı için Azure RBAC](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

Yukarıdaki diyagramda gösterildiği gibi, Azure RBAC tümleştirmesi kullanılırken, Kubernetes API 'sine yapılan tüm istekler [Azure Active Directory tümleştirme bölümünde](#azure-ad-integration)açıklanacak şekilde aynı kimlik doğrulama akışını izler. 

Azure AD 'de isteği yapan kimlik varsa Azure, isteği yetkilendirmek için Kubernetes RBAC ile ekipte bulunur. Kimlik, Azure AD dışında (yani bir Kubernetes hizmet hesabı) varsa, yetkilendirme normal Kubernetes RBAC ' ye geri dönecek.

Bu senaryoda, Azure RBAC mekanizmalarını ve API 'Lerini kullanarak, Kubernetes rolleriyle yaptığınız gibi kullanıcılara yerleşik roller atayabilir veya özel roller oluşturabilirsiniz. 

Bu özellik ile, kullanıcılara yalnızca AKS kaynağı için abonelikler arasında izin vermemiş olursunuz, ancak aynı zamanda Kubernetes API erişimini denetleyen bu kümelerin her birinin içindeki rol ve izinleri de yapılandırırsınız. Örneğin, `Azure Kubernetes Service RBAC Viewer` rolü abonelik kapsamında verebilirsiniz. Rol alıcısı, tüm Kubernetes nesnelerini değiştirmeden tüm kümelerden alabilir ve alabilirler.

> [!IMPORTANT]
> Bu özelliği kullanmadan önce Kubernetes yetkilendirmesi için Azure RBAC 'i etkinleştirmeniz gerekir. Daha fazla ayrıntı ve adım adım yönergeler için, [Kubernetes yetkilendirmesi nasıl yapılır Kılavuzu Için Azure RBAC 'Yi kullanın](manage-azure-rbac.md) .

#### <a name="built-in-roles"></a>Yerleşik roller

AKS, aşağıdaki dört yerleşik rolü sağlar. Bunlar, [Kubernetes yerleşik rollerine](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles) benzerdir ve bu da crds 'yi destekleme gibi birkaç farklılık vardır. Her [Azure yerleşik rolü](../role-based-access-control/built-in-roles.md)tarafından izin verilen eylemlerin tam listesine bakın.

| Rol                                | Açıklama  |
|-------------------------------------|--------------|
| Azure Kubernetes hizmeti RBAC Görüntüleyicisi  | Bir ad alanındaki birçok nesneyi görmek için salt okuma erişimine izin verir. <br> Rollerin veya rol bağlamalarının görüntülenmesine izin vermez.<br> Görüntülemeye izin vermez `Secrets` . İçerik okuma, ad alanındaki `Secrets` `ServiceAccount` kimlik bilgilerine erişim sağlar ve bu da `ServiceAccount` ad alanında (bir ayrıcalık yükseltme), API erişimine izin verir.  |
| Azure Kubernetes hizmeti RBAC yazıcı | Bir ad alanındaki nesnelerin çoğuna okuma/yazma erişimi sağlar. <br> Rolleri veya rol bağlamalarını görüntülemeye veya değiştirmeye izin vermez. <br> Ad alanındaki herhangi bir ServiceAccount olarak bulunan bir veya daha fazla ad alanı üzerinde herhangi bir ServiceAccount `Secrets` 'ın API erişim düzeylerini kazanmak için kullanılabilir. |
| Azure Kubernetes hizmeti RBAC Yöneticisi  | Yönetici erişimine izin verir, bir ad alanı içinde verilmek üzere tasarlanmıştır. <br> Ad alanı içinde roller ve rol bağlamaları oluşturma özelliği de dahil olmak üzere bir ad alanındaki (veya küme kapsamındaki) kaynakların çoğuna okuma/yazma erişimi sağlar. <br> Kaynak kotasına veya ad alanının kendine yazma erişimine izin vermez. |
| Azure Kubernetes hizmeti RBAC kümesi Yöneticisi  | Süper Kullanıcı erişiminin herhangi bir kaynak üzerinde herhangi bir işlem gerçekleştirmesine izin verir. <br> Kümedeki her kaynak ve tüm ad alanlarında tam denetim sağlar. |


## <a name="summary"></a>Özet

Azure AD tümleştirmesi etkinleştirildiğinde kullanıcıların Kubernetes 'te kimlik doğrulaması yapabilmesinin hızlı bir özetini görmek için tabloyu görüntüleyin. Her durumda, kullanıcının komut dizisi:
1. `az login`Azure 'da kimlik doğrulaması yapmak için ' i çalıştırın.
1. `az aks get-credentials`Kümenin kimlik bilgilerini içine indirmek için ' i çalıştırın `.kube/config` .
1. `kubectl`Komutları çalıştırın. 
   * İlk komut, aşağıdaki tabloda açıklandığı gibi, kümede kimlik doğrulaması yapmak için tarayıcı tabanlı kimlik doğrulaması tetiklenebilir.

Azure portal şunları bulabilirsiniz:
* İkinci sütunda başvurulan *rol izni* (Azure RBAC rolü verme) **Access Control** sekmesinde gösterilir. 
* Küme Yöneticisi Azure AD grubu, **yapılandırma** sekmesinde gösterilir.
  * Ayrıca `--aad-admin-group-object-ids` , Azure CLI 'de parametre adıyla birlikte bulunur.


| Description        | Rol verme gerekli| Küme Yöneticisi Azure AD grupları | Kullanılması gereken durumlar |
| -------------------|------------|----------------------------|-------------|
| İstemci sertifikası kullanarak eski yönetici oturumu açma| **Azure Kubernetes yönetici rolü**. Bu rol, `az aks get-credentials` `--admin` [eski (Azure dışı ad) küme yönetici sertifikasını](control-kubeconfig-access.md) kullanıcının kullanıcısına indiren bayrağıyla birlikte kullanılmasına izin verir `.kube/config` . Bu, "Azure Kubernetes yönetici rolü" nin tek amacı değildir.|yok|Kalıcı olarak engellendiyse, kümenize erişimi olan geçerli bir Azure AD grubuna erişemez.| 
| El ile (küme) RoleBindings ile Azure AD| **Azure Kubernetes Kullanıcı rolü**. "Kullanıcı" rolü `az aks get-credentials` bayrak olmadan kullanılmasına izin verir `--admin` . (Bu, "Azure Kubernetes Kullanıcı rolü" öğesinin tek amacı olur.) Sonuç olarak, Azure AD özellikli bir kümede [boş bir girdinin](control-kubeconfig-access.md) indirilmesi, bu, `.kube/config` tarafından ilk kez kullanıldığında tarayıcı tabanlı kimlik doğrulamasını tetikler `kubectl` .| Kullanıcı bu grupların hiçbirinde değil. Kullanıcı herhangi bir Küme Yöneticisi grubunda olmadığından, hakları tamamen küme yöneticileri tarafından ayarlanan herhangi bir RoleBindings veya ClusterRoleBindings tarafından denetlenir. (Küme) RoleBindings, [Azure AD kullanıcılarını veya Azure AD gruplarını](azure-ad-rbac.md) oldukları gibi aday olarak belirler `subjects` . Böyle bir bağlama ayarlanmamışsa, Kullanıcı herhangi bir `kubectl` komutu kullanamaz.|Ayrıntılı erişim denetimi istiyorsanız ve Kubernetes yetkilendirmesi için Azure RBAC kullanmıyorsanız. Bağlamaları ayarlayan kullanıcının bu tabloda listelenen diğer yöntemlerden biriyle oturum açması gerektiğini unutmayın.|
| Yönetici grubuna üye tarafından Azure AD| Yukarıdakiyle aynı|Kullanıcı burada listelenen gruplardan birinin üyesidir. AKS, listelenen tüm grupları Kubernetes rolüne bağlayan bir ClusterRoleBinding otomatik olarak oluşturur `cluster-admin` . Bu nedenle, bu gruplardaki kullanıcılar tüm `kubectl` komutları olarak çalıştırabilir `cluster-admin` .|Kullanıcılara kolay bir şekilde yönetici hakları vermek istiyorsanız ve Kubernetes yetkilendirmesi için Azure _RBAC kullanmıyorsanız._|
| Kubernetes yetkilendirmesi için Azure RBAC ile Azure AD|İki rol: <br> İlk olarak, **Azure Kubernetes Kullanıcı rolü** (yukarıdaki gibi). <br> İkincisi, "Azure Kubernetes Service **RBAC**..." Yukarıda listelenen roller veya kendi özel alternatifi.|Kubernetes yetkilendirmesi için Azure RBAC etkinleştirildiğinde yapılandırma sekmesindeki yönetici rolleri alanı ilgisiz olur.|Kubernetes yetkilendirmesi için Azure RBAC kullanıyorsunuz. Bu yaklaşım, RoleBindings veya ClusterRoleBindings ayarlamanıza gerek kalmadan ayrıntılı denetim sağlar.|

## <a name="next-steps"></a>Sonraki adımlar

- Azure AD ve Kubernetes RBAC ile çalışmaya başlamak için bkz. [Azure Active Directory AKS Ile tümleştirme][aks-aad].
- İlişkili en iyi uygulamalar için bkz. [AKS 'de kimlik doğrulama ve yetkilendirme Için en iyi uygulamalar][operator-best-practices-identity].
- Kubernetes yetkilendirmesi için Azure RBAC ile çalışmaya başlamak için bkz. Azure [Kubernetes Service (AKS) kümesi içindeki erişimi yetkilendirmek Için Azure RBAC kullanma](manage-azure-rbac.md).
- Dosyanızı güvenli hale getirmek için `kubeconfig` bkz. [küme yapılandırma dosyasına erişimi sınırlandırma](control-kubeconfig-access.md)

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
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
