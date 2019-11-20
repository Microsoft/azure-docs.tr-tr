---
title: Azure Lighthouse örnekleri ve şablonları
description: Bu örnekler ve Azure Resource Manager şablonları, Azure temsilcili kaynak yönetimi için müşterilerin nasıl ekleneceğini ve Azure Lighthouse senaryolarının nasıl destekleneceğini gösterir.
ms.topic: sample
ms.date: 10/17/2019
ms.openlocfilehash: 20c1fc62c83202f97ed9cff68fd285280d94a321
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132156"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse örnekleri

Aşağıdaki tablo, Azure Lighthouse için önemli Azure Resource Manager şablonlarının bağlantılarını içerir. Bu dosyalar ve daha fazlası [Azure Lighthouse örnekleri deposunda](https://github.com/Azure/Azure-Lighthouse-samples/) da bulunabilir.

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Azure tarafından atanan temsilcinin kaynak yönetimine müşterileri ekleme

Belirli ekleme senaryolarını gerçekleştirme için farklı şablonlar sağlarız. En uygun seçeneği kullanın ve parametre dosyasını ortamınızı yansıtacak şekilde değiştirmeyi unutmayın. Bu dosyaları dağıtımınızda nasıl kullanacağınız hakkında daha fazla bilgi için bkz. [Müşteriyi Azure tarafından atanan temsilcinin kaynak yönetimine ekleme](../how-to/onboard-customer.md).

| **Şablon** | **Açıklama** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Bir müşterinin aboneliğini, Azure tarafından atanan temsilcinin kaynak yönetimine ekler. Her abonelik için ayrı dağıtım yapılmalıdır. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Bir veya daha fazla müşterinin kaynak grubunu Azure tarafından atanan temsilcinin kaynak yönetimine ekler. Tek kaynak grubu için **rgDelegatedResourceManagement** veya aynı aboneliğe birden fazla kaynak grubunu eklemek için **multipleRgDelegatedResourceManagement** kullanın. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | [Azure Market'te bir yönetilen hizmet teklifi yayımladıysanız](../how-to/publish-managed-services-offers.md), teklifi kabul eden kullanıcılara kaynakları eklemek için isterseniz bu şablonu kullanabilirsiniz. Parametre dosyasındaki market değerleri teklifinizi yayımlarken kullandığınız değerlerle eşleşmelidir. |

Normalde eklenen her abonelik için ayrı dağıtım gerekir ama birden çok abonelikte şablonları dağıtabilirsiniz.

| **Şablon** | **Açıklama** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Azure Resource Manager şablonlarını birden fazla abonelikte dağıtır. |

## <a name="azure-policy"></a>Azure İlkesi

Bu örnekler Azure İlkesinin, Azure tarafından atanan temsilcinin kaynak yönetimi için eklenen aboneliklerle nasıl kullanılacağını gösterir.

| **Şablon** | **Açıklama** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Temsilci aboneliğine etiket ekleyen veya kaldıran (değiştirme etkisini kullanarak) bir ilke atar. Daha fazla bilgi için bkz. [Temsilci aboneliği içinde düzeltilebilir bir ilke dağıtma](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Temsilci atamalarını denetleyecek bir ilke atar. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Temsilci aboneliklerindeki Azure Key Vault kaynakları üzerinde tanılamayı etkinleştiren (deployIfNotExists etkisini kullanarak) bir ilke atar. Daha fazla bilgi için bkz. [Temsilci aboneliği içinde düzeltilebilir bir ilke dağıtma](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Temsilci aboneliğinde tanılamayı etkinleştirmek için çeşitli ilkeler atar ve tüm Windows ile Linux VM'lerini ilke tarafından oluşturulan Log Analytics çalışma alanına bağlar. Daha fazla bilgi için bkz. [Temsilci aboneliği içinde düzeltilebilir bir ilke dağıtma](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Temsilci aboneliğine bir girişim (birden fazla ilgili ilke tanımı) uygular. |

## <a name="azure-monitor"></a>Azure İzleyici

Bu örnekler Azure İzleyici'nin, Azure tarafından atanan temsilcinin kaynak yönetimi için eklenen aboneliklerde uyarı oluşturmak amacıyla nasıl kullanılacağını gösterir.

| **Şablon** | **Açıklama** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Bu şablon bir Azure uyarısı oluşturur ve mevcut Eylem Grubuna bağlanır.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Kusto sorgularına dayalı birden fazla Log Analytics uyarısı oluşturur.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Kullanıcı, yöneten kiracıya bir aboneliği temsilci olarak atadığında kiracıda uyarı dağıtır.|

## <a name="additional-cross-tenant-scenarios"></a>Diğer kiracılar arası senaryolar

Bu örnekler kiracılar arası yönetim senaryolarında gerçekleştirilebilecek çeşitli görevleri gösterir.

| **Şablon** | **Açıklama** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Depolama hesaplarını iki farklı kaynak grubuna dağıtır.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Azure yönetim hizmetlerini oluşturur, bunları birbirine bağlar ve ek çözümler dağıtır. Uçtan uca dağıtım için **rgWithAzureMgmt.json** şablonunu kullanın. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Hedeflenen Azure aboneliğinde Azure Güvenlik Merkezi’ni etkinleştirir ve yapılandırır. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Temsilci aboneliğindeki mevcut Log Analytics çalışma alanında Azure Sentinel'i dağıtır ve etkinleştirir. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Bu şablonlar, bunları belirlenen Log Analytics çalışma alanına bağlayarak, Windows ve Linux VM'lerinizde Log Analytics VM uzantılarını dağıtmanıza olanak tanır |

## <a name="next-steps"></a>Sonraki adımlar

- [Azure tarafından atanan temsilcinin kaynak yönetimi](../concepts/azure-delegated-resource-management.md) hakkında bilgi edinin.
- [Azure Lighthouse örnekleri deposunu](https://github.com/Azure/Azure-Lighthouse-samples/) gözden geçirin.
