---
title: Azure kaynakları için yönetilen kimlikleri destekleyen Azure hizmetleri
description: Azure kaynakları ve Azure AD kimlik doğrulaması için yönetilen kimlikleri destekleyen hizmetlerin listesi
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 09/24/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0731510977c01b08d9aa557246dce3bd92b2f826
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473223"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikleri destekleyen hizmetler

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Yönetilen bir kimlik kullanarak, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapabilirsiniz. Azure kaynakları ve Azure AD kimlik doğrulaması için yönetilen kimlikleri Azure üzerinde tümleştirme sürecimiz vardır. Güncelleştirmeler için sık sık kontrol edin.

> [!NOTE]
> Azure kaynakları için yönetilen kimlikler, Yönetilen Hizmet Kimliği (MSI) olarak bilinen hizmetin yeni adıdır.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimlikleri destekleyen Azure hizmetleri

Aşağıdaki Azure hizmetleri Azure kaynakları için yönetilen kimlikleri destekler:

### <a name="azure-virtual-machines"></a>Azure Sanal Makineler

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Kullanılabilir | Önizleme | Önizleme | Önizleme | 
| Kullanıcı atandı | Kullanılabilir | Önizleme | Önizleme | Önizleme |

Azure sanal makineleri için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın (kullanılabilir yerlerde):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager şablonları](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure sanal makine ölçek kümeleri

|Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Kullanılabilir | Önizleme | Önizleme | Önizleme |
| Kullanıcı atandı | Kullanılabilir | Önizleme | Önizleme | Önizleme |

Azure sanal makine ölçek kümeleri (varsa) için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager şablonları](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Kullanılabilir | Kullanılabilir | Kullanılabilir | Kullanılabilir |
| Kullanıcı atandı | Kullanılabilir | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure App Service yönetilen kimliği (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager şablonu](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Kullanılabilir | Kullanılabilir | Kullanılamaz | Kullanılamaz |
| Kullanıcı atandı | Kullanılabilir | Kullanılabilir | Kullanılamaz | Kullanılamaz |

[Azure şemaları](../../governance/blueprints/overview.md)ile yönetilen bir kimlik kullanmak için aşağıdaki listeye bakın:

- [Azure portal şeması atama](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API şeması atama](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure İşlevleri

Yönetilen kimlik türü |Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Kullanılabilir | Kullanılabilir | Kullanılabilir | Kullanılabilir |
| Kullanıcı atandı | Kullanılabilir | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Işlevleri için yönetilen kimliği (varsa) yapılandırmak üzere aşağıdaki listeye bakın:

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager şablonu](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Önizleme | Önizleme | Kullanılamaz | Önizleme |
| Kullanıcı atandı | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Logic Apps yönetilen kimliği (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal-system-logic-app)
- [Azure Resource Manager şablonu](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Kullanılabilir | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı atandı | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Data Factory v2 için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın (varsa):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Kullanılabilir | Kullanılabilir | Kullanılamaz | Kullanılamaz |
| Kullanıcı atandı | Kullanılamaz | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure API Management yönetilen kimliğini (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure Resource Manager şablonu](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Linux: Önizleme<br>Windows: kullanılamıyor | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı atandı | Linux: Önizleme<br>Windows: kullanılamıyor | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Container Instances yönetilen kimliği (varsa) yapılandırmak için aşağıdaki listeye bakın:

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager şablonu](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Görevleri

Yönetilen kimlik türü | Tüm genel kullanıma açık<br>Küresel Azure bölgeleri | Azure Kamu | Azure Almanya | Azure Çin 21Vianet |
| --- | --- | --- | --- | --- |
| Sistem atandı | Kullanılabilir | Kullanılamaz | Kullanılamaz | Kullanılamaz |
| Kullanıcı atandı | Önizleme | Kullanılamaz | Kullanılamaz | Kullanılamaz |

Azure Container Registry görevler için yönetilen kimliği yapılandırmak üzere aşağıdaki listeye bakın (kullanılabilir yerlerde olan bölgelerde):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri

Aşağıdaki hizmetler Azure AD kimlik doğrulamasını destekler ve Azure kaynakları için Yönetilen kimlikler kullanan istemci hizmetleriyle test edilmiştir.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager erişimi yapılandırmak için aşağıdaki listeye bakın:

- [Azure portal aracılığıyla erişim atama](howto-assign-access-portal.md)
- [PowerShell aracılığıyla erişim atama](howto-assign-access-powershell.md)
- [Azure CLı aracılığıyla erişim atama](howto-assign-access-CLI.md)
- [Azure Resource Manager şablonu aracılığıyla erişim atama](../../role-based-access-control/role-assignments-template.md)

| Bulut | Kaynak kimliği | Durum |
|--------|------------|--------|
| Azure genel | `https://management.azure.com/`| Kullanılabilir |
| Azure Kamu | `https://management.usgovcloudapi.net/` | Kullanılabilir |
| Azure Almanya | `https://management.microsoftazure.de/` | Kullanılabilir |
| Azure Çin 21Vianet | `https://management.chinacloudapi.cn` | Kullanılabilir |

### <a name="azure-key-vault"></a>Azure Key Vault

| Bulut | Kaynak kimliği | Durum |
|--------|------------|--------|
| Azure genel | `https://vault.azure.net`| Kullanılabilir |
| Azure Kamu | `https://vault.usgovcloudapi.net` | Kullanılabilir |
| Azure Almanya |  `https://vault.microsoftazure.de` | Kullanılabilir |
| Azure Çin 21Vianet | `https://vault.azure.cn` | Kullanılabilir |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Bulut | Kaynak kimliği | Durum |
|--------|------------|--------|
| Azure genel | `https://datalake.azure.net/` | Kullanılabilir |
| Azure Kamu |  | Yok |
| Azure Almanya |   | Yok |
| Azure Çin 21Vianet |  | Yok |

### <a name="azure-sql"></a>Azure SQL 

| Bulut | Kaynak kimliği | Durum |
|--------|------------|--------|
| Azure genel | `https://database.windows.net/` | Kullanılabilir |
| Azure Kamu | `https://database.usgovcloudapi.net/` | Kullanılabilir |
| Azure Almanya | `https://database.cloudapi.de/` | Kullanılabilir |
| Azure Çin 21Vianet | `https://database.chinacloudapi.cn/` | Kullanılabilir |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Bulut | Kaynak kimliği | Durum |
|--------|------------|--------|
| Azure genel | `https://eventhubs.azure.net` | Kullanılabilir |
| Azure Kamu |  | Yok |
| Azure Almanya |   | Yok |
| Azure Çin 21Vianet |  | Yok |

### <a name="azure-service-bus"></a>Azure Service Bus

| Bulut | Kaynak kimliği | Durum |
|--------|------------|--------|
| Azure genel | `https://servicebus.azure.net`  | Kullanılabilir |
| Azure Kamu |  | Kullanılabilir |
| Azure Almanya |   | Yok |
| Azure Çin 21Vianet |  | Yok |









### <a name="azure-storage-blobs-and-queues"></a>Azure depolama Blobları ve kuyrukları

| Bulut | Kaynak kimliği | Durum |
|--------|------------|--------|
| Azure genel | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Kullanılabilir |
| Azure Kamu | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Kullanılabilir |
| Azure Almanya | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Kullanılabilir |
| Azure Çin 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Kullanılabilir |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Bulut | Kaynak kimliği | Durum |
|--------|------------|--------|
| Azure genel | `https://*.asazure.windows.net` | Kullanılabilir |
| Azure Kamu | `https://*.asazure.usgovcloudapi.net` | Kullanılabilir |
| Azure Almanya | `https://*.asazure.cloudapi.de` | Kullanılabilir |
| Azure Çin 21Vianet | `https://*.asazure.chinacloudapi.cn` | Kullanılabilir |
