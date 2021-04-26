---
title: Kurumsal güvenlik ve idare
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning güvenli bir şekilde kullanın: kimlik doğrulama, yetkilendirme, ağ güvenliği, veri şifreleme ve izleme.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94992038"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning için kurumsal güvenlik ve idare

Bu makalede, Azure Machine Learning için kullanılabilen güvenlik ve idare özellikleri hakkında bilgi edineceksiniz. Bu özellikler, şirketler ilkelerinizle uyumlu güvenli bir yapılandırma oluşturmak isteyen yöneticiler, DevOps ve MLOps için yararlıdır. Azure Machine Learning ve Azure platformunda şunları yapabilirsiniz:

* Kullanıcı hesabı veya gruplarına göre kaynaklara ve işlemlere erişimi kısıtla
* Gelen ve giden ağ iletişimlerini kısıtla
* Yoldaki ve bekleyen verileri şifreleme
* Güvenlik açıklarını Tara
* Yapılandırma ilkelerini uygula ve denetle

## <a name="restrict-access-to-resources-and-operations"></a>Kaynaklara ve işlemlere erişimi kısıtla

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) , Azure Machine Learning için kimlik hizmeti sağlayıcıdır. Azure kaynaklarında _kimlik doğrulaması_ yapmak için kullanılan güvenlik nesneleri (Kullanıcı, Grup, hizmet sorumlusu ve yönetilen kimlik) oluşturmanıza ve yönetmenize olanak sağlar. Azure AD bunu kullanacak şekilde yapılandırıldıysa Multi-Factor Authentication desteklenir.

Azure AD 'de Multi-Factor Authentication kullanarak Azure Machine Learning için kimlik doğrulama işlemi aşağıda verilmiştir:

1. İstemci Azure AD 'de oturum açar ve bir Azure Resource Manager belirteci alır.
1. İstemci belirteci Azure Resource Manager ve tüm Azure Machine Learning gösterir.
1. Azure Machine Learning, Kullanıcı işlem hedefine bir Machine Learning hizmet belirteci sağlar (örneğin, Azure Machine Learning işlem kümesi). Bu belirteç, Kullanıcı işlem hedefi tarafından, çalıştırma tamamlandıktan sonra Machine Learning hizmetine geri çağrı yapmak için kullanılır. Kapsam, çalışma alanıyla sınırlıdır.

[![Azure Machine Learning kimlik doğrulaması](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Her çalışma alanı, çalışma alanıyla aynı ada sahip ilişkili, sistem tarafından atanan bir [yönetilen kimliğe](../active-directory/managed-identities-azure-resources/overview.md) sahiptir. Bu yönetilen kimlik, çalışma alanı tarafından kullanılan kaynaklara güvenli bir şekilde erişmek için kullanılır. Ekli kaynaklar üzerinde aşağıdaki Azure RBAC izinlerine sahiptir:

| Kaynak | İzinler |
| ----- | ----- |
| Çalışma alanı | Katılımcı |
| Depolama hesabı | Depolama Blob Verileri Katkıda Bulunanı |
| Key Vault | Tüm anahtarlar, gizlilikler, sertifikalara erişim |
| Azure Container Registry | Katılımcı |
| Çalışma alanını içeren kaynak grubu | Katılımcı |
| Anahtar kasasını içeren kaynak grubu (çalışma alanını içeren bunlardan farklıysa) | Katılımcı |

Yöneticilerin yönetilen kimliğin önceki tabloda bahsedilen kaynaklara erişimini iptal etmemenizi önermiyoruz. Yeniden [eşitleme anahtarları işlemini](how-to-change-storage-access-key.md)kullanarak erişimi geri yükleyebilirsiniz.

Azure Machine Learning Ayrıca, `aml-` `Microsoft-AzureML-Support-App-` her çalışma alanı bölgesi için aboneliğinizde katkıda bulunan düzeyinde erişim ile ek bir uygulama (ad veya ile başlar) oluşturur. Örneğin, Doğu ABD ' de bir çalışma alanınız varsa ve bir diğeri aynı abonelikte Kuzey Avrupa, bu uygulamalardan ikisini de görürsünüz. Bu uygulamalar, işlem kaynaklarını yönetmenize yardımcı olmak için Azure Machine Learning sağlar.

Ayrıca, Azure sanal makineler ve Azure Machine Learning işlem kümesi ile birlikte kullanmak üzere kendi yönetilen kimliklerinizi de yapılandırabilirsiniz. Bir VM ile yönetilen kimlik, tek bir kullanıcının Azure AD hesabı yerine SDK 'dan çalışma alanınıza erişmek için kullanılabilir. Bir işlem kümesi ile, yönetilen kimlik, eğitim işini çalıştıran kullanıcının erişimi olmayan güvenli veri depoları gibi kaynaklara erişmek için kullanılır. Daha fazla bilgi için bkz. [Azure Machine Learning çalışma alanı Için kimlik doğrulaması](how-to-setup-authentication.md).

> [!TIP]
> Azure Machine Learning içinde Azure AD ve Azure RBAC kullanımı için bazı özel durumlar mevcuttur:
> * İsteğe bağlı olarak, Azure Machine Learning işlem örneği ve işlem kümesi gibi işlem kaynaklarına __SSH__ erişimini etkinleştirebilirsiniz. SSH erişimi, Azure AD değil ortak/özel anahtar çiftlerini temel alır. SSH erişimi, Azure RBAC tarafından yönetilmez.
> * __Anahtar__ veya __belirteç__ tabanlı kimlik doğrulaması kullanarak Web Hizmetleri (çıkarım uç noktaları) olarak dağıtılan modellerle kimlik doğrulaması yapabilirsiniz. Anahtarlar statik dizelerdir, ancak belirteçler bir Azure AD güvenlik nesnesi kullanılarak alınır. Daha fazla bilgi için bkz. [Web hizmeti olarak dağıtılan modeller için kimlik doğrulamasını yapılandırma](how-to-authenticate-web-service.md).

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:
* [Azure Machine Learning çalışma alanı için kimlik doğrulaması](how-to-setup-authentication.md)
* [Azure Machine Learning erişimini Yönet](how-to-assign-roles.md)
* [Depolama hizmetlerine bağlanma](how-to-access-data.md)
* [Eğitim sırasında gizli diziler için Azure Key Vault kullanın](how-to-use-secrets-in-runs.md)
* [Azure Machine Learning ile Azure AD yönetilen kimliğini kullanma](how-to-use-managed-identities.md)
* [Web hizmetinizdeki Azure AD yönetilen kimliğini kullanın](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Ağ güvenliği ve yalıtımı

Azure Machine Learning kaynaklara ağ erişimini kısıtlamak için [Azure sanal ağ (VNet)](../virtual-network/virtual-networks-overview.md)kullanabilirsiniz. VNET 'ler, genel İnternet 'ten kısmen veya tamamen yalıtılmış olan ağ ortamları oluşturmanıza olanak tanır. Bu, çözümünüz için saldırı yüzeyini ve veri kaybı olasılığını azaltır.

Tek tek istemcileri veya kendi ağınızı VNet 'e bağlamak için bir sanal özel ağ (VPN) ağ geçidi kullanabilirsiniz

Azure Machine Learning çalışma alanı, VNet 'in arkasında özel bir uç nokta oluşturmak için [Azure özel bağlantısını](../private-link/private-link-overview.md) kullanabilir. Bu, sanal ağ içinden çalışma alanına erişmek için kullanılabilecek özel IP adresleri kümesi sağlar. Azure Machine Learning bağlı olan hizmetlerden bazıları Azure özel bağlantısı 'nı da kullanabilir, ancak bazıları ağ güvenlik gruplarını veya Kullanıcı tanımlı yönlendirmeyi kullanır.

Daha fazla bilgi için, aşağıdaki belgelere bakın:

* [Sanal ağ yalıtımı ve gizliliği genel bakış](how-to-network-security-overview.md)
* [Güvenli çalışma alanı kaynakları](how-to-secure-workspace-vnet.md)
* [Güvenli eğitim ortamı](how-to-secure-training-vnet.md)
* [Güvenli çıkarım ortamı](how-to-secure-inferencing-vnet.md)
* [Güvenli bir sanal ağda Studio 'yu kullanma](how-to-enable-studio-virtual-network.md)
* [Özel DNS kullanma](how-to-custom-dns.md)
* [Güvenlik duvarını yapılandırma](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Veri şifrelemesi

Azure Machine Learning, Azure platformunda çeşitli işlem kaynaklarını ve veri depolarını kullanır. Her birinin, bekleyen ve aktarım sırasında veri şifrelemesini nasıl desteklediğine ilişkin daha fazla bilgi edinmek için bkz. [Azure Machine Learning Ile veri şifreleme](concept-data-encryption.md).

Modelleri Web Hizmetleri olarak dağıttığınızda, Aktarım Katmanı Güvenliği 'ni (TLS) yoldaki verileri şifrelemek için etkinleştirebilirsiniz. Daha fazla bilgi için bkz. [güvenli bir Web hizmeti yapılandırma](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Güvenlik açığı taraması

[Azure Güvenlik Merkezi](../security-center/security-center-introduction.md) hibrit bulut iş yükleri arasında birleşik güvenlik yönetimi ve gelişmiş tehdit koruması sağlar. Azure Machine Learning için [Azure Container Registry](../container-registry/container-registry-intro.md) kaynağınızın ve Azure Kubernetes hizmet kaynaklarınızın taranmasını etkinleştirmeniz gerekir. Daha fazla bilgi için bkz. Güvenlik Merkezi [Ile Azure Kubernetes hizmet tümleştirmesi ve Güvenlik Merkezi ile](../security-center/defender-for-kubernetes-introduction.md) [Azure Container Registry görüntü tarama](../security-center/defender-for-container-registries-introduction.md) .

## <a name="audit-and-manage-compliance"></a>Uyumluluğu denetleme ve yönetme

[Azure ilkesi](../governance/policy/index.yml) , Azure kaynaklarının ilkelerinizle uyumlu olduğundan emin olmanızı sağlayan bir idare aracıdır. Azure Machine Learning çalışma alanınızın özel bir uç nokta kullanıp kullanmadığını gibi belirli yapılandırmalara izin vermek veya zorlamak için ilkeler ayarlayabilirsiniz. Azure Ilkesi hakkında daha fazla bilgi için bkz. [Azure ilkesi belgeleri](../governance/policy/overview.md). Azure Machine Learning özgü ilkeler hakkında daha fazla bilgi için bkz. [Azure ilkesiyle uyumluluğu denetleme ve yönetme](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Sonraki adımlar

* [TLS ile güvenli Azure Machine Learning Web Hizmetleri](how-to-secure-web-service.md)
* [Web hizmeti olarak dağıtılan bir Machine Learning modeli kullanma](how-to-consume-web-service.md)
* [Azure Güvenlik Duvarı ile Azure Machine Learning kullanma](how-to-access-azureml-behind-firewall.md)
* [Azure sanal ağ ile Azure Machine Learning kullanma](how-to-network-security-overview.md)
* [REST ve iletim sırasında veri şifrelemesi](concept-data-encryption.md)
* [Azure 'da gerçek zamanlı bir öneri API 'SI oluşturun](/azure/architecture/reference-architectures/ai/real-time-recommendation)
