---
title: Sık karşılaşılan hataları giderme
description: İlke tanımları, çeşitli SDK 'lar ve Kubernetes için eklenti oluşturma sorunlarını giderme hakkında bilgi edinin.
ms.date: 04/19/2021
ms.topic: troubleshooting
ms.openlocfilehash: c4feae11c6d8d78a43bae9882405e292a18e90bd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725071"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Azure Ilkesini kullanmayla ilgili sorunları giderme

İlke tanımları oluştururken, SDK 'lar ile çalışırsınız veya [Kubernetes eklentisi Için Azure ilkesi](../concepts/policy-for-kubernetes.md) 'ni ayarlarken hatalarla karşılaşabilirsiniz. Bu makalede oluşabilecek çeşitli genel hatalar açıklanmakta ve bunları çözmek için yollar önerilmektedir.

## <a name="find-error-details"></a>Hata ayrıntılarını bul

Hata ayrıntılarının konumu, üzerinde çalıştığınız Azure Ilkesinin en yönüne bağlıdır.

- Özel bir ilkeyle çalışıyorsanız, şema hakkında geri bildirim almak için Azure portal gidin veya kaynakların nasıl değerlendirildiğini görmek için sonuç [uyumluluk verilerini](../how-to/get-compliance-data.md) gözden geçirin.
- Çeşitli SDK 'lardan biriyle çalışıyorsanız, SDK, işlevin neden başarısız olduğuna ilişkin ayrıntıları sağlar.
- Kubernetes eklentisi ile çalışıyorsanız, kümede [oturum açma](../concepts/policy-for-kubernetes.md#logging) ile başlayın.

## <a name="general-errors"></a>Genel hatalar

### <a name="scenario-alias-not-found"></a>Senaryo: diğer ad bulunamadı

#### <a name="issue"></a>Sorun

İlke tanımında yanlış veya varolmayan diğer ad kullanılıyor. Azure Ilkesi Azure Resource Manager özellikleriyle eşlemek için [takma adlar](../concepts/definition-structure.md#aliases) kullanır.

#### <a name="cause"></a>Nedeni

İlke tanımında yanlış veya varolmayan diğer ad kullanılıyor.

#### <a name="resolution"></a>Çözüm

İlk olarak, Kaynak Yöneticisi özelliğinin bir diğer adı olduğunu doğrulayın. Kullanılabilir diğer adları aramak için Visual Studio Code veya SDK [Için Azure ilke uzantısı](../how-to/extension-for-vscode.md) ' na gidin.
Bir Kaynak Yöneticisi özelliği için diğer ad yoksa, bir destek bileti oluşturun.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Senaryo: değerlendirme ayrıntıları güncel değil

#### <a name="issue"></a>Sorun

Kaynak _başlatılmamış_ durumda veya uyumluluk ayrıntıları güncel değil.

#### <a name="cause"></a>Nedeni

Yeni bir ilke veya girişim atamasının uygulanması yaklaşık 30 dakika sürer. Mevcut bir atamanın kapsamındaki yeni veya güncelleştirilmiş kaynaklar yaklaşık 15 dakika içinde kullanılabilir hale gelir. Her 24 saatte bir standart uyumluluk taraması oluşur. Daha fazla bilgi için bkz. [değerlendirme Tetikleyicileri](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Çözüm

İlk olarak, bir değerlendirmenin tamamlanmasını ve uyumluluk sonuçlarının Azure portal ya da SDK 'da kullanılabilir hale gelmesi için uygun bir süre bekleyin. Azure PowerShell veya REST API ile yeni bir değerlendirme taraması başlatmak için bkz. [isteğe bağlı değerlendirme taraması](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-isnt-as-expected"></a>Senaryo: uyumluluk beklendiği gibi değil

#### <a name="issue"></a>Sorun

Kaynak, kaynak için beklenen _uyumlu_ veya _uyumsuz_ değerlendirme durumunda değil.

#### <a name="cause"></a>Nedeni

Kaynak, ilke atamasının doğru kapsamında değil veya ilke tanımı istenen şekilde çalışmıyor.

#### <a name="resolution"></a>Çözüm

İlke tanımınızda sorun gidermek için şunları yapın:

1. İlk olarak, bir değerlendirmenin tamamlanmasını ve uyumluluk sonuçlarının Azure portal veya SDK 'da kullanılabilir hale gelmesi için uygun süreyi bekleyin. 

1. Azure PowerShell veya REST API ile yeni bir değerlendirme taraması başlatmak için bkz. [isteğe bağlı değerlendirme taraması](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Atama parametrelerinin ve atama kapsamının doğru ayarlandığından emin olun.
1. [İlke tanımı modunu](../concepts/definition-structure.md#mode) denetleyin:
   - Mod `all` tüm kaynak türleri için olmalıdır.
   - `indexed`İlke tanımı, etiketleri veya konumu denetlediğinde mod olmalıdır.
1. Kaynak kapsamının [dışlandığından](../concepts/assignment-structure.md#excluded-scopes) veya [muaf](../concepts/exemption-structure.md)olmadığından emin olun.
1. Bir ilke atamasının uyumluluğu `0/0` kaynakları gösteriyorsa, atama kapsamında geçerli olacak hiçbir kaynak belirlenmemiştir. Hem ilke tanımını hem de atama kapsamını denetleyin.
1. Uyumlu olması beklenen uyumsuz bir kaynak için bkz. [uyumsuzluk nedenlerini belirleme](../how-to/determine-non-compliance.md). Tanımın değerlendirilen Özellik değeri ile karşılaştırılması, kaynağın neden uyumsuz olduğunu gösterir.
   - **Hedef değer** yanlış ise, ilke tanımını gözden geçirin.
   - **Geçerli değer** yanlışsa kaynak yükünü ile doğrulayın `resources.azure.com` .
1. Diğer yaygın sorunlar ve çözümleri için bkz. [sorun giderme: zorunlu değildir](#scenario-enforcement-not-as-expected).

Yinelenen ve özelleştirilmiş yerleşik ilke tanımı veya özel tanımınızda sorun yaşıyorsanız, sorunu doğru bir şekilde yönlendirmek için **Ilke yazma** altında bir destek bileti oluşturun.

### <a name="scenario-enforcement-not-as-expected"></a>Senaryo: zorlama beklendiği gibi değil

#### <a name="issue"></a>Sorun

Azure Ilkesinin üzerinde çalışmasını beklediğinizi belirten bir kaynak üzerinde işlem yapılmaz ve [Azure etkinlik günlüğünde](../../../azure-monitor/essentials/platform-logs-overview.md)giriş yok.

#### <a name="cause"></a>Nedeni

İlke ataması, _devre dışı bırakılmış_ bir [**Enforcementmode**](../concepts/assignment-structure.md#enforcement-mode) ayarı için yapılandırıldı.
**Enforcementmode** devre dışı olsa da, ilke efekti zorlanmaz ve etkinlik günlüğünde giriş yoktur.

#### <a name="resolution"></a>Çözüm

Aşağıdakileri yaparak ilke atamasının zorlanmasıyla ilgili sorunları giderin:

1. İlk olarak, bir değerlendirmenin tamamlanmasını ve uyumluluk sonuçlarının Azure portal veya SDK 'da kullanılabilir hale gelmesi için uygun süreyi bekleyin. 

1. Azure PowerShell veya REST API ile yeni bir değerlendirme taraması başlatmak için bkz. [isteğe bağlı değerlendirme taraması](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Atama parametrelerinin ve atama kapsamının doğru ayarlandığından ve **Enforcementmode** 'un _etkinleştirildiğinden_ emin olun.
1. [İlke tanımı modunu](../concepts/definition-structure.md#mode) denetleyin:
   - Mod `all` tüm kaynak türleri için olmalıdır.
   - `indexed`İlke tanımı, etiketleri veya konumu denetlediğinde mod olmalıdır.
1. Kaynak kapsamının [dışlandığından](../concepts/assignment-structure.md#excluded-scopes) veya [muaf](../concepts/exemption-structure.md)olmadığından emin olun.
1. Kaynak yükünün ilke mantığını eşleştirdiğini doğrulayın. Bu, [BIR http Arşivi (Har) izlemesi yakalanarak](../../../azure-portal/capture-browser-trace.md) veya Azure Resource Manager şablonu (ARM şablonu) özelliklerini inceleyerek yapılabilir.
1. Diğer yaygın sorunlar ve çözümleri için bkz. [sorun giderme: beklenen şekilde uyumluluk](#scenario-compliance-isnt-as-expected).

Yinelenen ve özelleştirilmiş yerleşik ilke tanımı veya özel tanımınızda sorun yaşıyorsanız, sorunu doğru bir şekilde yönlendirmek için **Ilke yazma** altında bir destek bileti oluşturun.

### <a name="scenario-denied-by-azure-policy"></a>Senaryo: Azure Ilkesi tarafından reddedildi

#### <a name="issue"></a>Sorun

Bir kaynağın oluşturulması veya güncelleştirilmesi reddedildi.

#### <a name="cause"></a>Nedeni

Yeni veya güncelleştirilmiş kaynağınızın kapsamına yönelik bir ilke ataması, bir ilke tanımının bir [reddetme](../concepts/effects.md#deny) etkisi ile ölçütlerine uygun olduğunu karşılar. Bu tanımları karşılayan kaynakların oluşturulması veya güncellenmesi engellenir.

#### <a name="resolution"></a>Çözüm

Bir reddetme ilke atamasından alınan hata iletisi, ilke tanımı ve ilke atama kimliklerini içerir. İletideki hata bilgileri kaçırıldığında, [etkinlik günlüğünde](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log)da kullanılabilir. Bu bilgileri, kaynak kısıtlamalarını anlamak ve izin verilen değerleri eşlemek için istekteki kaynak özelliklerini ayarlamak üzere daha fazla bilgi almak için kullanın.

### <a name="scenario-definition-targets-multiple-resource-types"></a>Senaryo: tanım birden çok kaynak türünü hedefliyor

#### <a name="issue"></a>Sorun

Oluşturma veya güncelleştirme sırasında birden çok kaynak türü içeren bir ilke tanımı, aşağıdaki hatayla başarısız olur:

```error
The policy definition '{0}' targets multiple resource types, but the policy rule is authored in a way that makes the policy not applicable to the target resource types '{1}'.
```

#### <a name="cause"></a>Nedeni

İlke tanımı kuralında, hedef kaynak türleri tarafından değerlendirilmeyecek bir veya daha fazla koşul vardır.

#### <a name="resolution"></a>Çözüm

Bir diğer ad kullanılırsa, diğer adın, öncesinde bir tür koşulu ekleyerek yalnızca ait olduğu kaynak türüne karşı değerlendirildiğinden emin olun. Bir alternatif, birden çok kaynak türünü hedeflemenin önüne geçmek için, ilke tanımını birden çok tanıma bölmaktır.

## <a name="template-errors"></a>Şablon hataları

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Senaryo: şablon tarafından işlenen Ilke tarafından desteklenen işlevler

#### <a name="issue"></a>Sorun

Azure Ilkesi, yalnızca bir ilke tanımında kullanılabilen birçok ARM şablonu işlevini ve işlevini destekler. Kaynak Yöneticisi, bu işlevleri bir ilke tanımının bir parçası yerine bir dağıtımın parçası olarak işler.

#### <a name="cause"></a>Nedeni

Veya gibi desteklenen işlevleri kullanarak `parameter()` `resourceGroup()` , ilke tanımı ve Azure ilke altyapısının çalışmasına izin vermek yerine, işlevin, dağıtım sırasında işlenen sonucuna neden olur.

#### <a name="resolution"></a>Çözüm

Bir işlevi bir ilke tanımının parçası olarak geçirmek için, tüm dizeyi, özelliği gibi görünen bir ile kaçış `[` `[[resourceGroup().tags.myTag]` . Kaçış karakteri, şablonu işlerken Kaynak Yöneticisi değeri bir dize olarak görmesini sağlar. Daha sonra Azure Ilkesi, işlevi ilke tanımına koyar, bu da beklenen şekilde dinamik olmasına olanak sağlar. Daha fazla bilgi için bkz. [Azure Resource Manager şablonlarındaki sözdizimi ve ifadeler](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Kubernetes yükleme hataları için eklenti

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Senaryo: bir parola hatası nedeniyle bir Held grafiği kullanılarak yükleme başarısız oluyor

#### <a name="issue"></a>Sorun

`helm install azure-policy-addon`Komut başarısız olur ve aşağıdaki hatalardan birini döndürür:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Nedeni

Oluşturulan parola, `,` HELI grafiğinin bölünmesi bir virgül () içerir.

#### <a name="resolution"></a>Çözüm

Çalıştırdığınızda `helm install azure-policy-addon` , parola değerindeki virgül () karakterini `,` ters eğik çizgiyle () kaçış `\` .

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Senaryo: ad zaten mevcut olduğu için bir Held grafiği kullanılarak yükleme başarısız oluyor

#### <a name="issue"></a>Sorun

`helm install azure-policy-addon`Komut başarısız olur ve aşağıdaki hatayı döndürür:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Nedeni

Ada sahip Held grafiği `azure-policy-addon` zaten yüklenmiş veya kısmen yüklenmiş.

#### <a name="resolution"></a>Çözüm

[Kubernetes eklentisinin Azure ilkesini kaldırmak](../concepts/policy-for-kubernetes.md#remove-the-add-on)için yönergeleri izleyin, sonra komutu yeniden çalıştırın `helm install azure-policy-addon` .

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Senaryo: Azure sanal makinesi Kullanıcı tarafından atanan kimlikler, sistem tarafından atanan Yönetilen kimlikler ile değiştirilmiştir

#### <a name="issue"></a>Sorun

Konuk yapılandırma ilkesi girişimlerini bir makine içindeki denetim ayarlarına atadıktan sonra, makineye atanan kullanıcı tarafından atanan Yönetilen kimlikler artık atanmaz. Yalnızca sistem tarafından atanan yönetilen kimlik atanır.

#### <a name="cause"></a>Nedeni

Konuk yapılandırma DeployIfNotExists tanımlarında daha önce kullanılan ilke tanımları, sistem tarafından atanan bir kimliğin makineye atandığını, ancak kullanıcı tarafından atanan kimlik atamalarını da kaldırdığını kabul ediyor.

#### <a name="resolution"></a>Çözüm

Daha önce Bu soruna neden olan tanımlar _\[ kullanım dışı \]_ olarak görünür ve Kullanıcı tarafından atanan yönetilen kimlikleri kaldırmadan önkoşulları yöneten ilke tanımlarına göre değiştirilirler. El ile bir adım gereklidir. _\[ Kullanım dışı \]_ olarak işaretlenen tüm mevcut ilke atamalarını silin ve bunları, orijinalle aynı ada sahip olan güncelleştirilmiş önkoşul ilkesi girişimi ve ilke tanımlarıyla değiştirin.

Ayrıntılı bir anlatım için, [Konuk yapılandırma denetim ilkeleri Için önemli değişiklik yayımlanan](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)blog gönderisine bakın.

## <a name="add-on-for-kubernetes-general-errors"></a>Kubernetes genel hataları için eklenti

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Senaryo: eklenti, çıkış kısıtlamaları nedeniyle Azure Ilke hizmeti uç noktasına ulaşamadığından

#### <a name="issue"></a>Sorun

Eklenti Azure Ilke hizmeti uç noktasına ulaşamıyor ve aşağıdaki hatalardan birini döndürür:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Nedeni

Bu sorun, bir küme çıkışı kilitlendiğinde oluşur.

#### <a name="resolution"></a>Çözüm

Aşağıdaki makalelerde belirtilen etki alanlarının ve bağlantı noktalarının açık olduğundan emin olun:

- [AKS kümeleri için gerekli giden ağ kuralları ve tam etki alanı adları (FQDN)](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Azure 'da yay özellikli Kubernetes için Azure Ilkesi eklentisini kurma (Önizleme)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Senaryo: eklenti, AAD-Pod kimlik yapılandırması nedeniyle Azure Ilke hizmeti uç noktasına ulaşamıyor

#### <a name="issue"></a>Sorun

Eklenti Azure Ilke hizmeti uç noktasına ulaşamıyor ve aşağıdaki hatalardan birini döndürür:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Nedeni

Bu hata, kümeye _Add-Pod kimliği_ yüklendiğinde ve _Kuto-System_ 'ların _AAD-Pod kimliği_'nde dışlanmadığı durumlarda oluşur.

_AAD-Pod kimliği_ bileşen düğümü yönetilen kimliği (NMI) Pod, Azure örnek meta veri uç noktası çağrılarını ele almak için düğümlerin Iptables 'larını değiştirir. Bu kurulum, Pod 'ın _AAD-Pod kimliği_ kullanmıyor olsa bile meta veri uç noktasına yapılan tüm istekler nmi tarafından yakalanmasıdır.
_AzurePodIdentityException_ customresourcedefinition (CRD), _AAD_ 'de tanımlanan etiketlerle eşleşen bir pod 'dan kaynaklanan bir meta veri uç noktasına yapılan isteklerin NMI 'da herhangi bir işlem yapılmadan proxy olması gerektiğini bildirmek üzere yapılandırılabilir.

#### <a name="resolution"></a>Çözüm

`kubernetes.azure.com/managedby: aks` _AzurePodIdentityException_ CRD 'yi yapılandırarak _AAD-Pod-identity_ içindeki _Kuto-System_ ad alanında etiketi olan sistem yığınlarını hariç tutun.

Daha fazla bilgi için bkz. [belirli bir pod/uygulama için Azure Active Directory (Azure AD) Pod kimliğini devre dışı bırakma](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Bir özel durum yapılandırmak için şu örneği izleyin:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Senaryo: kaynak sağlayıcı kayıtlı değil

#### <a name="issue"></a>Sorun

Eklenti Azure Ilke hizmeti uç noktasına ulaşabilir, ancak eklenti günlükleri aşağıdaki hatalardan birini görüntüler:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Nedeni

' Microsoft. Poliyghts ' kaynak sağlayıcısı kayıtlı değil. İlke tanımlarını almak ve uyumluluk verilerini döndürmek için eklentinin kayıtlı olması gerekir.

#### <a name="resolution"></a>Çözüm

Küme aboneliğine ' Microsoft. Policınghts ' kaynak sağlayıcısını kaydedin. Yönergeler için bkz. [kaynak sağlayıcısını kaydetme](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Senaryo: abonelik devre dışı

#### <a name="issue"></a>Sorun

Eklenti Azure Ilke hizmeti uç noktasına ulaşabilir, ancak aşağıdaki hata görüntülenir:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Nedeni

Bu hata, aboneliğin sorunlu olduğu belirlenen ve aboneliği engelleyecek Özellik bayrağının eklendiği anlamına gelir `Microsoft.PolicyInsights/DataPlaneBlocked` .

#### <a name="resolution"></a>Çözüm

Bu sorunu araştırmak ve çözmek için, [özellik ekibine başvurun](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>Sonraki adımlar

Sorununuz bu makalede listelenmiyorsa veya çözemezseniz, aşağıdaki kanallardan birini ziyaret ederek destek alın:

- [Microsoft Q&A](/answers/topics/azure-policy.html)aracılığıyla uzmanlardan yanıt alın.
- İle bağlanın [@AzureSupport](https://twitter.com/azuresupport) . Twitter 'daki bu resmi Microsoft Azure Kaynak, Azure Community 'yi doğru yanıtlara, desteğe ve uzmanlara bağlayarak müşteri deneyimini iyileştirmenize yardımcı olur.
- Hala yardıma ihtiyacınız varsa [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **destek isteği gönder**' i seçin.
