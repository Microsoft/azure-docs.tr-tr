---
title: Container Insights canlı verilerini ayarlama (Önizleme) | Microsoft Docs
description: Bu makalede, kapsayıcı öngörüleri ile kubectl kullanılmadan kapsayıcı günlüklerinin (stdout/stderr) ve olayların gerçek zamanlı görünümünü ayarlama açıklanmaktadır.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: references_regions
ms.openlocfilehash: 4302bdbb3d71c890f7fb0cfb82ab5f8d5aecbd43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713788"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>Canlı veriler (Önizleme) özelliğini ayarlama

Azure Kubernetes Service (AKS) kümelerinden gelen kapsayıcı öngörüleri ile canlı verileri (Önizleme) görüntülemek için, Kubernetes verilerinize erişim izni vermek üzere kimlik doğrulamasını yapılandırmanız gerekir. Bu güvenlik yapılandırması, doğrudan Azure portal, Kubernetes API 'SI aracılığıyla verilerinize gerçek zamanlı erişim sağlar.

Bu özellik günlüklere, olaylara ve ölçümlere erişimi denetlemek için aşağıdaki yöntemleri destekler:

- Kubernetes RBAC yetkilendirmesi etkin olmayan AKS
- Kubernetes RBAC yetkilendirmesi ile AKS etkin
    - Küme rolü bağlama **[Clustermonitoringuser](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) ile yapılandırılan aks 'ler**
- Azure Active Directory (AD) SAML tabanlı çoklu oturum açma ile AKS etkin

Bu yönergeler, Kubernetes kümenize yönetim erişiminin yanı sıra Kullanıcı kimlik doğrulaması için Azure Active Directory (AD) kullanmak üzere yapılandırılıyorsa, Azure AD 'ye yönetici erişimi gerektirir.

Bu makalede, kümeden canlı veriler (Önizleme) özelliğinin erişimini denetlemek için kimlik doğrulamanın nasıl yapılandırılacağı açıklanmaktadır:

- Kubernetes rol tabanlı erişim denetimi (Kubernetes RBAC) etkin AKS kümesi
- Tümleşik AKS kümesi Azure Active Directory.


## <a name="authentication-model"></a>Kimlik doğrulaması modeli

Canlı veriler (Önizleme) özellikleri, komut satırı aracıyla aynı şekilde Kubernetes API 'sini kullanır `kubectl` . Kubernetes API uç noktaları, tarayıcınızın doğrulayamayacak olan otomatik olarak imzalanan bir sertifika kullanır. Bu özellik, trafiğin güvenilir olduğundan emin olmak için, AKS hizmetiyle sertifikayı doğrulamak üzere bir iç proxy kullanır.

Azure portal, bir Azure Active Directory kümesi için oturum açma kimlik bilgilerinizi doğrulamanızı ve küme oluşturma sırasında sizi istemci kayıt kurulumuna yönlendirmenizi ister (Bu makalede yeniden yapılandırılır). Bu davranış, tarafından gereken kimlik doğrulama sürecine benzerdir `kubectl` .

>[!NOTE]
>Kümenizin yetkilendirmesi, Kubernetes tarafından ve ile yapılandırılan güvenlik modeliyle yönetilir. Bu özelliğe erişen kullanıcılar, çalıştırmaya benzer Kubernetes yapılandırmasını (*kubeconfig*) indirme izni gerektirir `az aks get-credentials -n {your cluster name} -g {your resource group}` . Bu yapılandırma dosyası, Kubernetes RBAC yetkilendirmesi etkin olmayan Azure RBAC özellikli ve AKS kümelerinde **Azure Kubernetes hizmet kümesi Kullanıcı rolü** için yetkilendirme ve kimlik doğrulama belirtecini içerir. AKS Azure Active Directory (AD) SAML tabanlı çoklu oturum açma özelliği etkinken Azure AD ve istemci kayıt ayrıntıları hakkında bilgi içerir.

>[!IMPORTANT]
>Bu özelliklerin kullanıcıları indirmek ve bu özelliği kullanmak için [Azure Kubernetes küme kullanıcı rolünü](../../role-based-access-control/built-in-roles.md) kümeye gerektirir `kubeconfig` . Kullanıcılar bu özelliği kullanmak için kümeye katılımcı **erişimi gerektirmez.**

## <a name="using-clustermonitoringuser-with-kubernetes-rbac-enabled-clusters"></a>Kubernetes RBAC özellikli kümeler ile clusterMonitoringUser kullanma

Kubernetes [RBAC yetkilendirmesini etkinleştirdikten](#configure-kubernetes-rbac-authorization) sonra Kubernetes Kullanıcı rolünün **Kümekullanıcısına** canlı veriler (Önizleme) özelliğine bağlanmasına izin vermek için ek yapılandırma değişiklikleri uygulama gereksinimini ortadan kaldırmak için, aks, **clustermonitoringuser** adlı yeni bir Kubernetes kümesi rol bağlaması eklemiştir. Bu küme rolü bağlamasında, Kubernetes API 'sine ve Canlı veriler (Önizleme) özelliğinin kullanılmasıyla ilgili uç noktalara erişmek için gerekli tüm izinler bulunur.

Bu yeni kullanıcıyla canlı veri (Önizleme) özelliğini kullanabilmeniz için AKS küme kaynağında [Azure Kubernetes hizmet kümesi kullanıcısı](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) veya [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) rolünün bir üyesi olmanız gerekir. Kapsayıcı öngörüleri etkinleştirildiğinde, varsayılan olarak clusterMonitoringUser kullanılarak kimlik doğrulaması yapacak şekilde yapılandırılır. ClusterMonitoringUser rolü bağlama bir kümede yoksa, **clusteruser** bunun yerine kimlik doğrulaması için kullanılır. Katkıda bulunan, clusterMonitoringUser 'a (varsa) erişmenizi sağlar ve Azure Kuberenetes hizmet kümesi kullanıcısı, Kümekullanıcısına erişmenizi sağlar. Bu iki rolden herhangi biri, bu özelliği kullanmak için yeterli erişim sağlar.

AKS, bu yeni rol bağlamasını Ocak 2020 ' de yayımlamıştır, bu yüzden Ocak 2020 ' den önce oluşturulan kümeler buna sahip değildir. Ocak 2020 tarihinden önce oluşturulmuş bir kümeniz varsa, yeni **Clustermonitoringuser** , küme ÜZERINDE bir put işlemi gerçekleştirerek veya kümede, küme sürümünü güncelleştirme gıbı bir put işlemi gerçekleştiren başka bir işlem gerçekleştirerek mevcut bir kümeye eklenebilir.

## <a name="kubernetes-cluster-without-kubernetes-rbac-enabled"></a>Kubernetes RBAC etkin olmayan Kubernetes kümesi

Kubernetes RBAC yetkilendirmesi ile yapılandırılmayan veya Azure AD çoklu oturum açma ile tümleştirilmiş bir Kubernetes kümeniz varsa, bu adımları izlemeniz gerekmez. Bunun nedeni, RBAC olmayan bir yapılandırmada varsayılan olarak yönetici izinlerinizin olması olabilir.

## <a name="configure-kubernetes-rbac-authorization"></a>Kubernetes RBAC yetkilendirmesini yapılandırma

Kubernetes RBAC yetkilendirmesini etkinleştirdiğinizde, iki kullanıcı kullanılır: Kubernetes API 'sine erişmek için **Clusteruser** ve **clusteradmin** . Bu, `az aks get-credentials -n {cluster_name} -g {rg_name}` Yönetim seçeneği olmadan çalışmaya benzer. Bu, **Clusteruser** 'ın Kubernetes API 'sindeki bitiş noktalarına erişim verilmesi gerektiği anlamına gelir.

Aşağıdaki örnek adımlarda, bu YAML yapılandırma şablonundan küme rolü bağlamasının nasıl yapılandırılacağı gösterilmektedir.

1. YAML dosyasını kopyalayıp yapıştırın ve Logreaderrtza. YAML olarak kaydedin.

    ```
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRole
    metadata:
       name: containerHealth-log-reader
    rules:
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"]
          resources:
             - "pods/log"
             - "events"
             - "nodes"
             - "pods"
             - "deployments"
             - "replicasets"
          verbs: ["get", "list"]
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: ClusterRoleBinding
    metadata:
       name: containerHealth-read-logs-global
    roleRef:
       kind: ClusterRole
       name: containerHealth-log-reader
       apiGroup: rbac.authorization.k8s.io
    subjects:
    - kind: User
      name: clusterUser
      apiGroup: rbac.authorization.k8s.io
    ```

2. Yapılandırmanızı güncelleştirmek için şu komutu çalıştırın: `kubectl apply -f LogReaderRBAC.yaml` .

>[!NOTE]
> Dosyanın önceki bir sürümünü `LogReaderRBAC.yaml` kümenize uyguladıysanız, yukarıdaki 1. adımda gösterilen yeni kodu kopyalayıp yapıştırarak güncelleştirin ve ardından 2. adımda gösterilen komutu çalıştırarak kümenize uygulayın.

## <a name="configure-ad-integrated-authentication"></a>AD ile tümleşik kimlik doğrulamasını yapılandırma

Kullanıcı kimlik doğrulaması için Azure Active Directory (AD) kullanmak üzere yapılandırılmış bir AKS kümesi, bu özelliğe erişen kişinin oturum açma kimlik bilgilerini kullanır. Bu yapılandırmada, Azure AD kimlik doğrulama belirtecinizi kullanarak bir AKS kümesinde oturum açabilirsiniz.

Azure AD istemci kaydı, Azure portal yetkilendirme sayfalarını güvenilir bir yeniden yönlendirme URL 'SI olarak yeniden yönlendirmesine izin verecek şekilde yeniden yapılandırılmalıdır. Daha sonra, Azure AD kullanıcıları, **kümerolleri** ve **clusterrolebindings** aracılığıyla aynı Kubernetes API uç noktalarına doğrudan erişim izni verilir.

Kubernetes 'te gelişmiş güvenlik kurulumu hakkında daha fazla bilgi için [Kubernetes belgelerini](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)gözden geçirin.

>[!NOTE]
>Yeni bir Kubernetes RBAC özellikli küme oluşturuyorsanız, bkz. [Azure Kubernetes hizmeti ile Azure Active Directory tümleştirme](../../aks/azure-ad-integration-cli.md) ve Azure AD kimlik doğrulamasını yapılandırma adımlarını izleyin. İstemci uygulamasını oluşturma adımları sırasında, aşağıdaki adım 3 ' te belirtilenlerle eşleşen kapsayıcı öngörüleri için oluşturmanız gereken iki yeniden yönlendirme URL 'sini vurgular.

### <a name="client-registration-reconfiguration"></a>İstemci kaydı yeniden yapılandırması

1. Azure AD 'deki Kubernetes kümeniz için istemci kaydını Azure portal **Azure Active Directory > uygulama kayıtları** altına yerleştirin.

2. Sol bölmedeki **kimlik doğrulaması** ' nı seçin.

3. Bu listeye **Web** uygulaması türleri olarak iki yeniden yönlendirme URL 'si ekleyin. İlk temel URL değeri olmalıdır `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` ve ikinci taban URL değeri olmalıdır `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

    >[!NOTE]
    >Bu özelliği Azure Çin 'de kullanıyorsanız ilk temel URL değeri olmalıdır `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` ve ikinci taban URL değeri olmalıdır `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` .

4. Yeniden yönlendirme URL 'Lerini kaydettikten sonra, **örtük izin**' ın altında, Seçenekler **erişim belirteçleri** ve **Kimlik belirteçleri** ' ni seçin ve ardından değişikliklerinizi kaydedin.

>[!NOTE]
>Tek oturum açma için Azure Active Directory kimlik doğrulamasını yapılandırma, yalnızca yeni bir AKS kümesinin ilk dağıtımı sırasında gerçekleştirilebilir. Zaten dağıtılmış bir AKS kümesi için çoklu oturum açma yapılandıramazsınız.

>[!IMPORTANT]
>Güncelleştirilmiş URI 'yi kullanarak Kullanıcı kimlik doğrulaması için Azure AD 'yi yeniden yapılandırdıysanız, güncelleştirilmiş kimlik doğrulama belirtecinin indirilip uygulandığından emin olmak için tarayıcınızın önbelleğini temizleyin.

## <a name="grant-permission"></a>İzin ver

Canlı veriler (Önizleme) özelliğine erişebilmek için her Azure AD hesabına, Kubernetes içindeki uygun API 'lere izin verilmelidir. Azure Active Directory hesabı verme adımları, [Kubernetes RBAC kimlik doğrulaması](#configure-kubernetes-rbac-authorization) bölümünde açıklanan adımlara benzerdir. YAML yapılandırma şablonunu kümenize uygulamadan önce, **kümeuser** 'ın altındaki **clusterrolebinding** öğesini istenen kullanıcıyla değiştirin.

>[!IMPORTANT]
>İçin Kubernetes RBAC bağlamaya verdiğiniz kullanıcı aynı Azure AD kiracısında ise, userPrincipalName temelinde izinleri atayın. Kullanıcı farklı bir Azure AD kiracısında ise, için sorgulama yapın ve objectID özelliğini kullanın.

AKS küme **Kümerolü bağlamalarınızı** yapılandırma hakkında daha fazla yardım için bkz. [Kubernetes RBAC bağlaması oluşturma](../../aks/azure-ad-integration-cli.md#create-kubernetes-rbac-binding).

## <a name="next-steps"></a>Sonraki adımlar

Kimlik doğrulama ayarı yaptığınızda [ölçümleri](container-insights-livedata-metrics.md), [dağıtımları](container-insights-livedata-deployments.md)ve [olayları ve günlükleri](container-insights-livedata-overview.md) kümenizdeki gerçek zamanlı olarak görüntüleyebilirsiniz.
