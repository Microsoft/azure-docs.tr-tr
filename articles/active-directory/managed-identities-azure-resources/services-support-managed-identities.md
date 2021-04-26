---
title: Yönetilen kimlikleri destekleyen Azure hizmetleri-Azure AD
description: Azure kaynakları ve Azure AD kimlik doğrulaması için yönetilen kimlikleri destekleyen hizmetlerin listesi
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: c4cd9140d03bba1f9d95ed64c3628da4fe32ecd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771488"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Yönetilen bir kimlik kullanarak, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapabilirsiniz. Azure kaynakları ve Azure AD kimlik doğrulaması için yönetilen kimlikleri Azure üzerinde tümleştirme sürecimiz vardır. Güncelleştirmeler için sık sık kontrol edin.

> [!NOTE]
> Azure kaynakları için yönetilen kimlikler, Yönetilen Hizmet Kimliği (MSI) olarak bilinen hizmetin yeni adıdır.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikleri destekleyen Azure hizmetleri

Aşağıdaki Azure hizmetleri Azure kaynakları için yönetilen kimlikleri destekler:


### <a name="azure-api-management"></a>Azure API Management

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | Önizleme | Önizleme | Kullanılamaz | Önizleme |

Azure API Management yönetilen kimliğini (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure Resource Manager şablonu](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure Uygulama Yapılandırması

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check]  | Kullanılamaz  | ![Kullanılabilir][check] |

Azure Uygulama yapılandırması için yönetilen kimliği (varsa) yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check]  | ![Kullanılabilir][check]  | ![Kullanılabilir][check] |

Azure App Service yönetilen kimliği (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure portalı](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager şablonu](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc özellikli Kubernetes

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Önizleme | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Arc etkin Kubernetes Şu anda [sistem tarafından atanan kimliği desteklemektedir](../../azure-arc/kubernetes/quickstart-connect-cluster.md). Yönetilen hizmet kimlik sertifikası, Azure ile iletişim kurmak için tüm Azure Arc etkinleştirilmiş Kubernetes aracıları tarafından kullanılır.

### <a name="azure-arc-enabled-servers"></a>Azure Arc özellikli sunucular

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Tüm Azure Arc etkin sunucularının sistem tarafından atanmış bir kimliği vardır. Azure Arc etkin bir sunucusunda sistem tarafından atanan kimliği devre dışı bırakıp değiştiremezsiniz. Azure Arc etkin sunucularında yönetilen kimliklerin nasıl kullanılacağı hakkında daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

- [Yay etkin sunucularla Azure kaynaklarında kimlik doğrulama](../../azure-arc/servers/managed-identity-authentication.md)
- [Bir yönetilen kimliği, Arc etkin sunucularla kullanma](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Otomatik Yönetimi

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Önizleme | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Aboneliğinizi yeni bir kiracıya taşıdıysanız yönetilen bir kimliği yeniden yapılandırmak için aşağıdaki belgeye başvurun:
* [Bozuk bir oto Yönet hesabını onarın](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz |

[Azure şemaları](../../governance/blueprints/overview.md)ile yönetilen bir kimlik kullanmak için aşağıdaki listeye bakın:

- [Azure portal şeması atama](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API şeması atama](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Bilişsel Arama

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

### <a name="azure-cognitive-services"></a>Azure Bilişsel Hizmetler

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |


### <a name="azure-communication-services"></a>Azure İletişim Hizmetleri

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz | Kullanılamaz |


### <a name="azure-container-instances"></a>Azure Container Instances

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Linux: Önizleme<br>Windows: kullanılamıyor | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Linux: Önizleme<br>Windows: kullanılamıyor | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Container Instances yönetilen kimliği (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager şablonu](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Görevleri

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Önizleme | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Container Registry görevler için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın (kullanılabilir yerlerde olan bölgelerde):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Veri Gezgini

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Data Factory v2 için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın (varsa):

- [Azure portalı](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- ['SıNıN](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure dijital TWINS için yönetilen kimliği (varsa) yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure portalı](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Yönetilen kimlik türü |Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Önizleme | Önizleme | Kullanılamaz | Önizleme |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz  | Kullanılamaz  | Kullanılamaz |

### <a name="azure-firewall-policy"></a>Azure Güvenlik Duvarı Ilkesi

Yönetilen kimlik türü |Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Önizleme | Kullanılamaz  | Kullanılamaz  | Kullanılamaz |

### <a name="azure-functions"></a>Azure İşlevleri

Yönetilen kimlik türü |Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check]  | ![Kullanılabilir][check]  | ![Kullanılabilir][check]  |

Azure Işlevleri için yönetilen kimliği (varsa) yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure portalı](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager şablonu](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure IoT Hub yönetilen kimliğini (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure portalı](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure İçeri/Dışarı Aktarma

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem tarafından atanan | Azure Içeri aktarma verme hizmeti 'nin kullanılabildiği bölgede kullanılabilir | Önizleme | Kullanılabilir | Kullanılabilir |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Önizleme | Kullanılamaz | Kullanılamaz | Kullanılamaz |


Daha fazla bilgi için bkz. [Azure Kubernetes hizmetinde Yönetilen kimlikler kullanma](../../aks/use-managed-identity.md).

### <a name="azure-log-analytics-cluster"></a>Azure Log Analytics kümesi

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |

Daha fazla bilgi için bkz. [Azure izleyici 'de kimliğin nasıl çalıştığı](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | Kullanılamaz | ![Kullanılabilir][check] |


Azure Logic Apps yönetilen kimliği (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure portalı](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager şablonu](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Önizleme | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Önizleme | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Daha fazla bilgi için bkz. [Azure Machine Learning ile yönetilen kimlikler kullanma](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure İlkesi

|Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Ilkesi için yönetilen kimliği (varsa) yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure portalı](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Azure Resource Manager şablonları](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Service Fabric uygulamalar Için yönetilen kimlik](../../service-fabric/concepts-managed-identity.md) tüm bölgelerde kullanılabilir.

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz | Kullanılamıyor |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz |Kullanılamaz |

Tüm bölgelerde Azure Service Fabric uygulamaları için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure Resource Manager şablonu](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | Kullanılamaz | Kullanılamaz | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |


Daha fazla bilgi için bkz. [Azure Spring Cloud uygulaması için sistem tarafından atanan yönetilen kimliği etkinleştirme](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem tarafından atanan | Azure Stack Edge hizmetinin kullanılabildiği bölgede kullanılabilir | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

### <a name="azure-virtual-machine-scale-sets"></a>Azure Sanal Makine Ölçek Kümeleri

|Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] |

Azure sanal makine ölçek kümeleri (varsa) için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure portalı](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager şablonları](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Sanal Makineler

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] |
| Kullanıcı tarafından atanan | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] | ![Kullanılabilir][check] |

Azure sanal makineleri için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın (kullanılabilir yerlerde):

- [Azure portalı](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager şablonları](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure SDK’ları](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM görüntü Oluşturucusu

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | [Desteklenen bölgelerde kullanılabilir](../../virtual-machines/image-builder-overview.md#regions) | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure VM görüntü Oluşturucu için yönetilen kimliği yapılandırma hakkında bilgi edinmek için (varsa) bkz. [görüntü oluşturucuya genel bakış](../../virtual-machines/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Azure SignalR Hizmeti

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Önizleme | Önizleme | Kullanılamaz | Önizleme |
| Kullanıcı tarafından atanan | Önizleme | Önizleme | Kullanılamaz | Önizleme |

Azure SignalR hizmeti için yönetilen kimliği (varsa) yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure Resource Manager şablonu](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Kaynak Taşıyıcı

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Sistem tarafından atanan | Azure Kaynak taşıyıcısı hizmetinin kullanılabildiği bölgelerde kullanılabilir | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı tarafından atanan | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Kaynak taşıyıcısı 'ni kullanmak için aşağıdaki belgeye başvurun:

- [Azure Kaynak Taşıyıcı](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri

Aşağıdaki hizmetler Azure AD kimlik doğrulamasını destekler ve Azure kaynakları için Yönetilen kimlikler kullanan istemci hizmetleriyle test edilmiştir.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager erişimi yapılandırmak için aşağıdaki listeye bakın:

- [Azure portal aracılığıyla erişim atama](howto-assign-access-portal.md)
- [PowerShell aracılığıyla erişim atama](howto-assign-access-powershell.md)
- [Azure CLı aracılığıyla erişim atama](howto-assign-access-CLI.md)
- [Azure Resource Manager şablonu aracılığıyla erişim atama](../../role-based-access-control/role-assignments-template.md)

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://management.azure.com/`| ![Kullanılabilir][check] |
| Azure Kamu | `https://management.usgovcloudapi.net/` | ![Kullanılabilir][check] |
| Azure Almanya | `https://management.microsoftazure.de/` | ![Kullanılabilir][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Kullanılabilir][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://vault.azure.net`| ![Kullanılabilir][check] |
| Azure Kamu | `https://vault.usgovcloudapi.net` | ![Kullanılabilir][check] |
| Azure Almanya |  `https://vault.microsoftazure.de` | ![Kullanılabilir][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Kullanılabilir][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://datalake.azure.net/` | ![Kullanılabilir][check] |
| Azure Kamu |  | Kullanılamaz |
| Azure Almanya |   | Kullanılamaz |
| Azure China 21Vianet |  | Kullanılamaz |

### <a name="azure-sql"></a>Azure SQL

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://database.windows.net/` | ![Kullanılabilir][check] |
| Azure Kamu | `https://database.usgovcloudapi.net/` | ![Kullanılabilir][check] |
| Azure Almanya | `https://database.cloudapi.de/` | ![Kullanılabilir][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Kullanılabilir][check] |

### <a name="azure-data-explorer"></a>Azure Veri Gezgini

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://<account>.<region>.kusto.windows.net` | ![Kullanılabilir][check] |
| Azure Kamu | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Kullanılabilir][check] |
| Azure Almanya | `https://<account>.<region>.kusto.cloudapi.de` | ![Kullanılabilir][check] |
| Azure China 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Kullanılabilir][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://eventhubs.azure.net` | ![Kullanılabilir][check] |
| Azure Kamu |  | Kullanılamaz |
| Azure Almanya |   | Kullanılamaz |
| Azure China 21Vianet |  | Kullanılamaz |

### <a name="azure-service-bus"></a>Azure Service Bus

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://servicebus.azure.net`  | ![Kullanılabilir][check] |
| Azure Kamu |  | ![Kullanılabilir][check] |
| Azure Almanya |   | Kullanılamaz |
| Azure China 21Vianet |  | Kullanılamaz |









### <a name="azure-storage-blobs-and-queues"></a>Azure depolama Blobları ve kuyrukları

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Kullanılabilir][check] |
| Azure Kamu | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Kullanılabilir][check] |
| Azure Almanya | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Kullanılabilir][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Kullanılabilir][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Bulut | Kaynak kimliği | Durum |
|--------|------------|:-:|
| Azure genel | `https://*.asazure.windows.net` | ![Kullanılabilir][check] |
| Azure Kamu | `https://*.asazure.usgovcloudapi.net` | ![Kullanılabilir][check] |
| Azure Almanya | `https://*.asazure.cloudapi.de` | ![Kullanılabilir][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Kullanılabilir][check] |

> [!Note]
> Microsoft Power BI, [yönetilen kimlikleri de destekler](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Kullanılabileceğini"
