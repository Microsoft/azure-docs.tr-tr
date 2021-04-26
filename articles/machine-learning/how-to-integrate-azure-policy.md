---
title: İlke uyumluluğunu denetleme ve yönetme
titleSuffix: Azure Machine Learning
description: Çalışma alanlarınızın gereksinimlerinize uygun olduğundan emin olmak üzere Azure Machine Learning için yerleşik ilkeleri kullanmak üzere Azure Ilkesi 'ni nasıl kullanacağınızı öğrenin.
author: aashishb
ms.author: aashishb
ms.date: 03/25/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: f708e2181511da97ecffcd6f1636a2b232b4fbc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544375"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Azure Ilkesi kullanarak Azure Machine Learning denetleme ve yönetme

[Azure ilkesi](../governance/policy/index.yml) , Azure kaynaklarının ilkelerinizle uyumlu olduğundan emin olmanızı sağlayan bir idare aracıdır. Azure Machine Learning, aşağıdaki ilkeleri atayabilirsiniz:

| İlke | Description |
| ----- | ----- |
| **Müşteri tarafından yönetilen anahtar** | Çalışma alanlarının müşterinin yönettiği anahtar kullanması gerekip gerekmediğini denetleyin veya zorlayın. |
| **Özel bağlantı** | Çalışma alanlarının bir sanal ağla iletişim kurmak için özel uç nokta kullanıp kullanmadığını denetleyin veya zorlayın. |
| **Özel uç nokta** | Özel uç noktanın oluşturulması gereken Azure sanal ağ alt ağını yapılandırın. |
| **Özel DNS bölgesi** | Özel bağlantı için kullanılacak özel DNS bölgesini yapılandırın. |
| **Kullanıcı tarafından atanan yönetilen kimlik** | Çalışma alanlarının Kullanıcı tarafından atanan yönetilen kimlik kullanıp kullanmadığını denetleyin veya zorlayın. |

İlkeler, abonelik veya kaynak grubu düzeyinde gibi farklı kapsamlardan ayarlanabilir. Daha fazla bilgi için bkz. [Azure ilkesi belgeleri](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Yerleşik ilkeler

Azure Machine Learning, Azure Machine Learning ortak senaryolar için kullanabileceğiniz bir ilke kümesi sağlar. Bu ilke tanımlarını mevcut aboneliğinize atayabilir veya kendi özel tanımlarınızı oluşturmak için temel olarak kullanabilirsiniz. Azure Machine Learning yerleşik ilkelerinin tam bir listesi için, bkz. [Azure Machine Learning Için yerleşik ilkeler](../governance/policy/samples/built-in-policies.md#machine-learning).

Azure Machine Learning ilgili yerleşik ilke tanımlarını görüntülemek için aşağıdaki adımları kullanın:

1. [Azure Portal](https://portal.azure.com) __Azure ilkesi__ ' ne gidin.
1. __Tanımlar__' ı seçin.
1. __Tür__ için _yerleşik_' i seçin ve __Kategori__ için __Machine Learning__' yi seçin.

Buradan görüntülenecek ilke tanımlarını seçebilirsiniz. Bir tanımı görüntülerken, ilkeyi belirli bir kapsama atamak için __ata__ bağlantısını kullanabilir ve ilke için parametreleri yapılandırabilirsiniz. Daha fazla bilgi için bkz. [Ilke atama-Portal](../governance/policy/assign-policy-portal.md).

[Azure PowerShell](../governance/policy/assign-policy-powershell.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md)ve [şablonları](../governance/policy/assign-policy-template.md)kullanarak da ilke atayabilirsiniz.

## <a name="workspace-encryption-with-customer-managed-key"></a>Müşteri tarafından yönetilen anahtarla çalışma alanı şifrelemesi

Bir çalışma alanının müşteri tarafından yönetilen bir anahtarla şifrelenip şifrelenmeyeceğini veya ölçümleri ve meta verileri şifrelemek için Microsoft tarafından yönetilen bir anahtar kullanıp kullanmadığını denetler. Müşteri tarafından yönetilen anahtarı kullanma hakkında daha fazla bilgi için veri şifreleme makalesinin [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) bölümüne bakın.

Bu ilkeyi yapılandırmak için, efekt parametresini __Denetim__ veya __reddetme__ olarak ayarlayın. __Denetim__ olarak ayarlanırsa, müşteri tarafından yönetilen anahtar olmadan bir çalışma alanı oluşturabilirsiniz ve etkinlik günlüğünde bir uyarı olayı oluşturulur.

İlke __Reddet__ olarak ayarlandıysa, müşteri tarafından yönetilen bir anahtar belirtmediği takdirde bir çalışma alanı oluşturamazsınız. Müşteri tarafından yönetilen anahtar olmadan bir çalışma alanı oluşturmaya çalışmak, şuna benzer bir hatayla sonuçlanır `Resource 'clustername' was disallowed by policy` ve etkinlik günlüğünde bir hata oluşturur. İlke tanımlayıcısı bu hatanın bir parçası olarak da döndürülür.

## <a name="workspace-should-use-private-link"></a>Çalışma alanı özel bağlantı kullanmalıdır

Bir çalışma alanının Azure sanal ağı ile iletişim kurmak için Azure özel bağlantısı kullanması gerekip gerekmediğini denetler. Özel bağlantı kullanma hakkında daha fazla bilgi için bkz. [bir çalışma alanı için özel bağlantı yapılandırma](how-to-configure-private-link.md).

Bu ilkeyi yapılandırmak için, efekt parametresini __Denetim__ veya __reddetme__ olarak ayarlayın. __Denetim__ olarak ayarlanırsa, özel bağlantı kullanmadan bir çalışma alanı oluşturabilirsiniz ve etkinlik günlüğünde bir uyarı olayı oluşturulur.

İlke __Reddet__ olarak ayarlandıysa, özel bir bağlantı kullanmadığı takdirde bir çalışma alanı oluşturamazsınız. Özel bağlantı olmadan çalışma alanı oluşturma girişimi bir hatayla sonuçlanır. Hata ayrıca etkinlik günlüğüne kaydedilir. İlke tanımlayıcısı bu hatanın bir parçası olarak döndürülür.

## <a name="workspace-should-use-private-endpoint"></a>Çalışma alanı özel uç nokta kullanmalıdır

Bir Azure sanal ağının belirtilen alt ağı içinde özel bir uç nokta oluşturmak için bir çalışma alanı yapılandırır.

Bu ilkeyi yapılandırmak için, efekt parametresini __Deployifnotexists__ olarak ayarlayın. __Privateendpointsubnetıd__ öğesini alt AĞıN Azure Resource Manager kimliği olarak ayarlayın.
## <a name="workspace-should-use-private-dns-zones"></a>Çalışma alanı özel DNS bölgelerini kullanmalıdır

Özel bir uç nokta için varsayılan DNS çözümlemesini geçersiz kılarak özel bir DNS bölgesi kullanmak üzere bir çalışma alanı yapılandırır.

Bu ilkeyi yapılandırmak için, efekt parametresini __Deployifnotexists__ olarak ayarlayın. __PrivateDnsZoneId__ öğesini kullanmak IÇIN özel DNS BÖLGESININ Azure Resource Manager kimliği olarak ayarlayın. 

## <a name="workspace-should-use-user-assigned-managed-identity"></a>Çalışma alanı kullanıcı tarafından atanan yönetilen kimliği kullanmalıdır

Bir çalışma alanının, sistem tarafından atanan yönetilen kimlik (varsayılan) veya Kullanıcı tarafından atanan yönetilen kimlik kullanılarak oluşturulup oluşturulmayacağını denetler. Çalışma alanının yönetilen kimliği, Azure depolama, Azure Container Registry, Azure Key Vault ve Azure Application Insights gibi ilişkili kaynaklara erişmek için kullanılır. Daha fazla bilgi için bkz. [Azure Machine Learning ile yönetilen kimlikler kullanma](how-to-use-managed-identities.md).

Bu ilkeyi yapılandırmak için, efekt parametresini __Denetim__, __reddetme__ veya __devre dışı__ olarak ayarlayın. __Denetim__ olarak ayarlanırsa, Kullanıcı tarafından atanan yönetilen kimlik belirtmeden bir çalışma alanı oluşturabilirsiniz. Sistem tarafından atanan bir kimlik kullanılır ve etkinlik günlüğünde bir uyarı olayı oluşturulur.

İlke __Reddet__ olarak ayarlandıysa, oluşturma işlemi sırasında Kullanıcı tarafından atanan bir kimlik sağlamadığınız sürece bir çalışma alanı oluşturamazsınız. Kullanıcı tarafından atanan kimlik sağlamadan bir çalışma alanı oluşturma girişimi bir hatayla sonuçlanır. Hata ayrıca etkinlik günlüğüne kaydedilir. İlke tanımlayıcısı bu hatanın bir parçası olarak döndürülür.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure İlkesi belgeleri](../governance/policy/overview.md)
* [Azure Machine Learning için yerleşik ilkeler](policy-reference.md)
* [Azure Güvenlik Merkezi ile güvenlik ilkeleriyle çalışma](../security-center/tutorial-security-policy.md)