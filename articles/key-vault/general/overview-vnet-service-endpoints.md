---
title: Azure Key Vault için sanal ağ hizmet uç noktaları
description: Azure Key Vault için sanal ağ hizmet uç noktalarının, kullanım senaryoları da dahil olmak üzere belirli bir sanal ağa erişimi kısıtlamanıza nasıl izin sağladığını öğrenin.
services: key-vault
author: amitbapat
ms.author: ambapat
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 8d3b88841f03b0c5bdb9b21ea66d9a67ba795546
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814255"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault için sanal ağ hizmet uç noktaları

Azure Key Vault için sanal ağ hizmet uç noktaları, belirtilen sanal ağa erişimi kısıtlayabilmeniz için izin verir. Uç noktalar Ayrıca bir IPv4 (Internet Protokolü sürüm 4) adres aralığı listesine erişimi sınırlamanıza olanak tanır. Anahtar kasanıza bu kaynakların dışından bağlanan herhangi bir kullanıcının erişimi reddedilir.

Bu kısıtlamanın önemli bir istisnası vardır. Bir kullanıcı güvenilen Microsoft hizmetlerine izin vermeyi kabul ettiğinde, bu hizmetlerden gelen bağlantılara güvenlik duvarı üzerinden izin verilir. Örneğin, bu hizmetler Office 365 Exchange Online, Office 365 SharePoint Online, Azure işlem, Azure Resource Manager ve Azure Backup içerir. Bu tür kullanıcıların yine de geçerli bir Azure Active Directory belirteci sunması gerekir ve istenen işlemi gerçekleştirmek için izinleri (erişim ilkeleri olarak yapılandırılmış) olmalıdır. Daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Kullanım senaryoları

[Key Vault güvenlik duvarlarını ve sanal ağları](network-security.md) , varsayılan olarak tüm ağlardan gelen trafiğe (internet trafiği dahil) erişimi reddedecek şekilde yapılandırabilirsiniz. Belirli Azure sanal ağlarından ve genel İnternet IP adresi aralıklarından trafiğe erişim izni vererek uygulamalarınız için güvenli bir ağ sınırı oluşturabilirsiniz.

> [!NOTE]
> Key Vault güvenlik duvarları ve sanal ağ kuralları yalnızca Key Vault [veri düzlemine](security-features.md#privileged-access) uygulanır. Key Vault denetim düzlemi işlemleri (oluşturma, silme ve değiştirme işlemleri, erişim ilkelerini ayarlama, güvenlik duvarları ayarlama ve sanal ağ kuralları ile gizli dizileri veya anahtarların ARM şablonları aracılığıyla dağıtılması), güvenlik duvarları ve sanal ağ kurallarından etkilenmez.

Hizmet uç noktalarını nasıl kullanabileceğinizi gösteren bazı örnekler şunlardır:

* Şifreleme anahtarlarını, uygulama gizli dizilerini ve sertifikaları depolamak için Key Vault kullanıyorsunuz ve genel İnternet 'ten anahtar kasanıza erişimi engellemek istiyorsunuz.
* Anahtar kasanıza yalnızca uygulamanızın veya kısa bir ana bilgisayar listesinin bir listesini, anahtar kasanıza bağlanabilmesi için, anahtar kasanıza erişimi kilitlemek istiyorsunuz.
* Azure sanal ağınızda çalışan bir uygulamanız var ve tüm gelen ve giden trafik için bu sanal ağ kilitli. Uygulamanızın parolaları veya sertifikaları getirmek veya şifreleme anahtarlarını kullanmak için Key Vault bağlanması gerekir.

## <a name="trusted-services"></a>Güvenilen hizmetler

**Güvenilen hizmetlere Izin ver** seçeneği etkinse, anahtar kasasına erişmesine izin verilen güvenilir hizmetlerin bir listesi aşağıda verilmiştir.

|Güvenilen hizmet|Desteklenen kullanım senaryoları|
| --- | --- |
|Azure sanal makineler dağıtım hizmeti|[Sertifikaları, müşteri tarafından yönetilen Key Vault sanal makinelere dağıtın](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Azure Resource Manager şablonu dağıtım hizmeti|[Dağıtım sırasında güvenli değerler geçirin](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure disk şifreleme birimi şifreleme hizmeti|Sanal makine dağıtımı sırasında BitLocker anahtarına (Windows VM) veya DM parolasının (Linux VM) ve anahtar şifreleme anahtarına erişime izin verin. Bu, [Azure disk şifrelemesini](../../security/fundamentals/encryption-overview.md)mümkün bir şekilde sunar.|
|Azure Backup|[Azure Backup](../../backup/backup-overview.md)kullanarak Azure sanal makineler yedeklemesi sırasında ilgili anahtarların ve parolaların yedeklenme ve geri yüklenmesine izin verin.|
|Exchange Online & SharePoint Online|[Müşteri anahtarına](/microsoft-365/compliance/customer-key-overview)sahip Azure depolama hizmeti şifrelemesi müşteri anahtarına erişime izin verin.|
|Azure Information Protection|Azure Information Protection için kiracı anahtarına erişime izin ver [.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Key Vault aracılığıyla Azure Web uygulaması sertifikasını dağıtın](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Veritabanı|[Azure SQL veritabanı ve Azure SYNAPSE Analytics için kendi anahtarını getir desteğiyle saydam veri şifrelemesi](../../azure-sql/database/transparent-data-encryption-byok-overview.md).|
|Azure Depolama|[Depolama Hizmeti Şifrelemesi Azure Key Vault müşteri tarafından yönetilen anahtarları kullanma](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|Müşteri tarafından yönetilen bir anahtarla [Azure Data Lake Store verilerin şifrelenmesi](../../data-lake-store/data-lake-store-encryption.md) .|
|Azure Synapse Analytics|[Azure Key Vault 'de müşteri tarafından yönetilen anahtarlar kullanılarak verilerin şifrelenmesi](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Hızlı, kolay ve işbirliğine dayalı Apache Spark tabanlı analiz hizmeti](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[MSI kullanarak Key Vault özel etki alanı için sertifika dağıtma](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Data Factory Key Vault veri deposu kimlik bilgilerini getir](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Müşteri tarafından yönetilen anahtarlar senaryosu için anahtar kasasına erişime izin ver](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Müşteri tarafından yönetilen anahtarlar senaryosu için anahtar kasasına erişime izin ver](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure İçeri/Dışarı Aktarma| [Içeri/dışarı aktarma hizmeti için Azure Key Vault 'de müşteri tarafından yönetilen anahtarları kullanın](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Müşteri tarafından yönetilen anahtarları kullanarak kayıt defteri şifreleme](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[HTTPS etkin dinleyiciler için Key Vault sertifikaları kullanma](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> İlgili hizmetlerin Key Vault erişimi almasını sağlamak için ilgili Key Vault erişim ilkelerini ayarlamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Adım adım yönergeler için bkz. [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](network-security.md)
- bkz. [Azure Key Vault güvenliğe genel bakış](security-features.md)
