---
title: Kapsayıcılar için Azure Izleyicisini ayarlama Canlı veriler (Önizleme) | Microsoft Docs
description: Bu makalede, kapsayıcılar için Azure Izleyici ile kubectl kullanmadan kapsayıcı günlüklerinin (stdout/stderr) ve olayların gerçek zamanlı görünümünü ayarlama açıklanmaktadır.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2019
ms.author: magoedte
ms.openlocfilehash: 596c5ad378d471c6c98616a48f44e96c365ee0bb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514374"
---
# <a name="how-to-setup-the-live-data-preview-feature"></a>Canlı veri (Önizleme) özelliğini ayarlama

Azure Kubernetes Service (AKS) kümelerinden kapsayıcılar için Azure Izleyici ile canlı verileri (Önizleme) görüntülemek için, Kubernetes verilerinize erişim izni vermek üzere kimlik doğrulamasını yapılandırmanız gerekir. Bu güvenlik yapılandırması, doğrudan Azure portal, Kubernetes API 'SI aracılığıyla verilerinize gerçek zamanlı erişim sağlar.

Bu özellik günlüklere, olaylara ve ölçümlere erişimi denetlemek için üç farklı yöntemi destekler:

- Kubernetes RBAC yetkilendirmesi etkin olmayan AKS
- Kubernetes RBAC yetkilendirmesi ile AKS etkin
- Azure Active Directory (AD) SAML tabanlı çoklu oturum açma ile AKS etkin

Bu yönergeler, Kubernetes kümenize yönetim erişiminin yanı sıra Kullanıcı kimlik doğrulaması için Azure Active Directory (AD) kullanmak üzere yapılandırılıyorsa, Azure AD 'ye yönetici erişimi gerektirir.  

Bu makalede, kümeden canlı veriler (Önizleme) özelliğinin erişimini denetlemek için kimlik doğrulamanın nasıl yapılandırılacağı açıklanmaktadır:

- Rol tabanlı erişim denetimi (RBAC) etkin AKS kümesi
- Tümleşik AKS kümesi Azure Active Directory. 

>[!NOTE]
>Bu özellikle [özel kümeler](https://azure.microsoft.com/updates/aks-private-cluster/) olarak etkinleştirilen aks kümeleri desteklenmez. Bu özellik, tarayıcınızdan bir ara sunucu aracılığıyla Kubernetes API 'sine doğrudan erişim sağlar. Bu proxy 'den Kubernetes API 'sini engellemek için ağ güvenliğinin etkinleştirilmesi, bu trafiği engeller. 

>[!NOTE]
>Bu özellik, Azure Çin dahil olmak üzere tüm Azure bölgelerinde kullanılabilir. Azure ABD kamu 'da Şu anda kullanılamıyor.

## <a name="authentication-model"></a>Kimlik doğrulama modeli

Canlı veriler (Önizleme) özellikleri, `kubectl` komut satırı aracıyla aynı şekilde Kubernetes API 'sini kullanır. Kubernetes API uç noktaları, tarayıcınızın doğrulayamayacak olan otomatik olarak imzalanan bir sertifika kullanır. Bu özellik, trafiğin güvenilir olduğundan emin olmak için, AKS hizmetiyle sertifikayı doğrulamak üzere bir iç proxy kullanır.

Azure portal, bir Azure Active Directory kümesi için oturum açma kimlik bilgilerinizi doğrulamanızı ve küme oluşturma sırasında sizi istemci kayıt kurulumuna yönlendirmenizi ister (Bu makalede yeniden yapılandırılır). Bu davranış, `kubectl`gereken kimlik doğrulama sürecine benzerdir. 

>[!NOTE]
>Kümenizin yetkilendirmesi, Kubernetes tarafından ve ile yapılandırılan güvenlik modeliyle yönetilir. Bu özelliğe erişen kullanıcılar, `az aks get-credentials -n {your cluster name} -g {your resource group}`çalıştırmaya benzer Kubernetes yapılandırmasını (*kubeconfig*) indirme izni gerektirir. Bu yapılandırma dosyası, RBAC yetkilendirmesi etkin olmayan Azure RBAC özellikli ve AKS kümelerinde **Azure Kubernetes hizmet kümesi Kullanıcı rolü**için yetkilendirme ve kimlik doğrulama belirtecini içerir. AKS Azure Active Directory (AD) SAML tabanlı çoklu oturum açma özelliği etkinken Azure AD ve istemci kayıt ayrıntıları hakkında bilgi içerir.

>[!IMPORTANT]
>Bu özelliklerin kullanıcıları, `kubeconfig` indirmek ve bu özelliği kullanmak için [Azure Kubernetes küme kullanıcı rolünü](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions) kümeye gerektirir. Kullanıcılar bu özelliği kullanmak için kümeye katılımcı **erişimi gerektirmez.** 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>RBAC etkin olmayan Kubernetes kümesi

Kubernetes RBAC yetkilendirmesi ile yapılandırılmayan veya Azure AD çoklu oturum açma ile tümleştirilmiş bir Kubernetes kümeniz varsa, bu adımları izlemeniz gerekmez. Bunun nedeni, RBAC olmayan bir yapılandırmada varsayılan olarak yönetici izinlerinizin olması olabilir.

## <a name="configure-kubernetes-rbac-authentication"></a>Kubernetes RBAC kimlik doğrulamasını yapılandırma

Kubernetes RBAC yetkilendirmesini etkinleştirdiğinizde, iki kullanıcı kullanılır: Kubernetes API 'sine erişmek için **Clusteruser** ve **clusteradmin** . Bu, yönetici seçeneği olmadan `az aks get-credentials -n {cluster_name} -g {rg_name}` çalıştırmaya benzerdir. Bu, **Clusteruser** 'ın Kubernetes API 'sindeki bitiş noktalarına erişim verilmesi gerektiği anlamına gelir.

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

2. Yapılandırmanızı güncelleştirmek için şu komutu çalıştırın: `kubectl apply -f LogReaderRBAC.yaml`.

>[!NOTE] 
> `LogReaderRBAC.yaml` dosyanın önceki bir sürümünü kümenize uyguladıysanız, yukarıdaki 1. adımda gösterilen yeni kodu kopyalayıp yapıştırarak güncelleştirin ve ardından 2. adımda gösterilen komutu çalıştırarak kümenize uygulayın.

## <a name="configure-ad-integrated-authentication"></a>AD Tümleşik kimlik doğrulamasını yapılandırma 

Kullanıcı kimlik doğrulaması için Azure Active Directory (AD) kullanmak üzere yapılandırılmış bir AKS kümesi, bu özelliğe erişen kişinin oturum açma kimlik bilgilerini kullanır. Bu yapılandırmada, Azure AD kimlik doğrulama belirtecinizi kullanarak bir AKS kümesinde oturum açabilirsiniz.

Azure AD istemci kaydı, Azure portal yetkilendirme sayfalarını güvenilir bir yeniden yönlendirme URL 'SI olarak yeniden yönlendirmesine izin verecek şekilde yeniden yapılandırılmalıdır. Daha sonra, Azure AD kullanıcıları, **kümerolleri** ve **clusterrolebindings**aracılığıyla aynı Kubernetes API uç noktalarına doğrudan erişim izni verilir. 

Kubernetes 'te gelişmiş güvenlik kurulumu hakkında daha fazla bilgi için [Kubernetes belgelerini](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)gözden geçirin. 

>[!NOTE]
>Yeni bir RBAC etkin küme oluşturuyorsanız, bkz. [Azure Kubernetes hizmeti ile Azure Active Directory tümleştirme](../../aks/azure-ad-integration.md) ve Azure AD kimlik doğrulamasını yapılandırma adımlarını izleyin. İstemci uygulamasını oluşturma adımları sırasında, bu bölümdeki bir notta, kapsayıcılar için Azure Izleyici için oluşturmanız gereken iki yeniden yönlendirme URL 'Si vurgulanmaktadır. 

### <a name="client-registration-reconfiguration"></a>İstemci kaydı yeniden yapılandırması

1. Azure AD 'deki Kubernetes kümeniz için istemci kaydını Azure portal **Azure Active Directory > uygulama kayıtları** altına yerleştirin.

2. Sol bölmedeki **kimlik doğrulaması** ' nı seçin. 

3. Bu listeye **Web** uygulaması türleri olarak iki yeniden yönlendirme URL 'si ekleyin. İlk temel URL değeri `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` olmalıdır ve ikinci taban URL değeri `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`olmalıdır.

    >[!NOTE]
    >Bu özelliği Azure Çin 'de kullanıyorsanız, ilk temel URL değeri `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` olmalıdır ve ikinci temel URL değeri `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`olmalıdır. 
    
4. Yeniden yönlendirme URL 'Lerini kaydettikten sonra, **Gelişmiş ayarlar**' ın altında, Seçenekler **erişim belirteçleri** ve **Kimlik belirteçleri** ' ni seçin ve ardından değişikliklerinizi kaydedin.

>[!NOTE]
>Tek oturum açma için Azure Active Directory kimlik doğrulamasını yapılandırma, yalnızca yeni bir AKS kümesinin ilk dağıtımı sırasında gerçekleştirilebilir. Zaten dağıtılmış bir AKS kümesi için çoklu oturum açma yapılandıramazsınız.
  
>[!IMPORTANT]
>Güncelleştirilmiş URI 'yi kullanarak Kullanıcı kimlik doğrulaması için Azure AD 'yi yeniden yapılandırdıysanız, güncelleştirilmiş kimlik doğrulama belirtecinin indirilip uygulandığından emin olmak için tarayıcınızın önbelleğini temizleyin.

## <a name="grant-permission"></a>İzin ver

Canlı veriler (Önizleme) özelliğine erişebilmek için her Azure AD hesabına, Kubernetes içindeki uygun API 'lere izin verilmelidir. Azure Active Directory hesabı verme adımları, [Kubernetes RBAC kimlik doğrulaması](#configure-kubernetes-rbac-authentication) bölümünde açıklanan adımlara benzerdir. YAML yapılandırma şablonunu kümenize uygulamadan önce, **kümeuser** 'ın altındaki **clusterrolebinding** öğesini istenen kullanıcıyla değiştirin. 

>[!IMPORTANT]
>RBAC bağlamasını verdiğiniz kullanıcı aynı Azure AD kiracısında ise, userPrincipalName öğesine göre izinler atayın. Kullanıcı farklı bir Azure AD kiracısında ise, için sorgulama yapın ve objectID özelliğini kullanın.

AKS küme **Kümerolü bağlamalarınızı**yapılandırma hakkında daha fazla yardım için bkz. [RBAC bağlaması oluşturma](../../aks/azure-ad-integration-cli.md#create-rbac-binding).

## <a name="next-steps"></a>Sonraki adımlar

Kimlik doğrulama ayarı yaptığınızda [ölçümleri](container-insights-livedata-metrics.md), [dağıtımları](container-insights-livedata-deployments.md)ve [olayları ve günlükleri](container-insights-livedata-overview.md) kümenizdeki gerçek zamanlı olarak görüntüleyebilirsiniz.