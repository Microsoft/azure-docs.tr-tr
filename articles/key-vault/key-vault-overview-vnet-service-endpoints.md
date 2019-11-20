---
title: Azure Key Vault için sanal ağ hizmet uç noktaları Azure Key Vault | Microsoft Docs
description: Key Vault için sanal ağ hizmeti uç noktalarına genel bakış
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: 6e85e33cf0488cf688303c590c2bbaf5d671d9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467115"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault için sanal ağ hizmeti uç noktaları

Azure Key Vault için sanal ağ hizmet uç noktaları, belirtilen sanal ağa erişimi kısıtlayabilmeniz için izin verir. Uç noktalar Ayrıca bir IPv4 (Internet Protokolü sürüm 4) adres aralığı listesine erişimi sınırlamanıza olanak tanır. Anahtar kasanıza bu kaynakların dışından bağlanan herhangi bir kullanıcının erişimi reddedilir.

Bu kısıtlamanın önemli bir istisnası vardır. Bir kullanıcı güvenilen Microsoft hizmetlerine izin vermeyi kabul ettiğinde, bu hizmetlerden gelen bağlantılara güvenlik duvarı üzerinden izin verilir. Örneğin, bu hizmetler Office 365 Exchange Online, Office 365 SharePoint Online, Azure işlem, Azure Resource Manager ve Azure Backup içerir. Bu tür kullanıcıların yine de geçerli bir Azure Active Directory belirteci sunması gerekir ve istenen işlemi gerçekleştirmek için izinleri (erişim ilkeleri olarak yapılandırılmış) olmalıdır. Daha fazla bilgi için bkz. [sanal ağ hizmeti uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Kullanım senaryoları

[Key Vault güvenlik duvarlarını ve sanal ağları](key-vault-network-security.md) , varsayılan olarak tüm ağlardan gelen trafiğe (internet trafiği dahil) erişimi reddedecek şekilde yapılandırabilirsiniz. Belirli Azure sanal ağlarından ve genel İnternet IP adresi aralıklarından trafiğe erişim izni vererek uygulamalarınız için güvenli bir ağ sınırı oluşturabilirsiniz.

> [!NOTE]
> Key Vault güvenlik duvarları ve sanal ağ kuralları yalnızca Key Vault [veri düzlemine](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) uygulanır. Key Vault denetim düzlemi işlemleri (oluşturma, silme ve değiştirme işlemleri, erişim ilkelerini ayarlama, güvenlik duvarlarını ayarlama ve sanal ağ kuralları), güvenlik duvarları ve sanal ağ kurallarından etkilenmez.

Hizmet uç noktalarını nasıl kullanabileceğinizi gösteren bazı örnekler şunlardır:

* Şifreleme anahtarlarını, uygulama gizli dizilerini ve sertifikaları depolamak için Key Vault kullanıyorsunuz ve genel İnternet 'ten anahtar kasanıza erişimi engellemek istiyorsunuz.
* Anahtar kasanıza yalnızca uygulamanızın veya kısa bir ana bilgisayar listesinin bir listesini, anahtar kasanıza bağlanabilmesi için, anahtar kasanıza erişimi kilitlemek istiyorsunuz.
* Azure sanal ağınızda çalışan bir uygulamanız var ve tüm gelen ve giden trafik için bu sanal ağ kilitli. Uygulamanızın parolaları veya sertifikaları getirmek veya şifreleme anahtarlarını kullanmak için Key Vault bağlanması gerekir.

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma

Güvenlik duvarlarını ve sanal ağları yapılandırmak için gereken adımlar aşağıda verilmiştir. Bu adımlar, PowerShell, Azure CLı veya Azure portal kullanıp kullanmayacağınızı uygular.

1. Ayrıntılı erişim günlüklerini görmek için [Key Vault günlüğe kaydetmeyi](key-vault-logging.md) etkinleştirin. Bu, güvenlik duvarları ve sanal ağ kuralları bir anahtar kasasının erişimini engelliyorsa tanılamada yardımcı olur. (Bu adım isteğe bağlıdır, ancak önemle önerilir.)
2. Hedef sanal ağlar ve alt ağlar için **Anahtar Kasası için hizmet uç noktalarını** etkinleştirin.
3. Belirli sanal ağlardan, alt ağlardan ve IPv4 adres aralıklarından ilgili anahtar kasasına erişimi kısıtlamak için bir Anahtar Kasası için güvenlik duvarlarını ve sanal ağ kurallarını ayarlayın.
4. Bu anahtar kasasının herhangi bir güvenilir Microsoft hizmeti tarafından erişilebilir olması gerekiyorsa, **Güvenilen Azure hizmetlerinin** Key Vault bağlanmasına izin ver seçeneğini etkinleştirin.

Daha fazla bilgi için bkz. [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](key-vault-network-security.md).

> [!IMPORTANT]
> Güvenlik duvarı kuralları etkin olduktan sonra, kullanıcılar, istekleri izin verilen sanal ağlardan veya IPv4 adres aralıklarından başlatıldığında yalnızca Key Vault [veri düzlemi](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control) işlemleri gerçekleştirebilir. Bu, Azure portal Key Vault erişmek için de geçerlidir. Kullanıcılar Azure portal bir anahtar kasasına gözatabilse de, istemci makineleri izin verilenler listesinde yoksa anahtarları, parolaları veya sertifikaları listelemeyebilir. Bu, diğer Azure hizmetleri tarafından Key Vault seçiciyi de etkiler. Kullanıcılar, güvenlik duvarı kuralları istemci makinesini engelliyorsa, anahtar kasalarının listesini görebilirler, ancak liste anahtarlarını göremez.


> [!NOTE]
> Aşağıdaki yapılandırma sınırlamalarından haberdar olun:
> * En fazla 127 sanal ağ kuralına ve 127 IPv4 kuralına izin verilir. 
> * "/31" veya "/32" ön ek boyutlarını kullanan küçük adres aralıkları desteklenmez. Bunun yerine, tek tek IP adresi kurallarını kullanarak bu aralıkları yapılandırın.
> * IP ağ kurallarına yalnızca genel IP adresleri için izin verilir. Özel ağlar için ayrılan IP adresi aralıklarına (RFC 1918 ' de tanımlandığı gibi) IP kurallarında izin verilmez. Özel ağlarda **10.** , **172.16-31**ve 192,168 ile başlayan adresler bulunur **.** 
> * Şu anda yalnızca IPv4 adresleri destekleniyor.

## <a name="trusted-services"></a>Güvenilen hizmetler

**Güvenilen hizmetlere Izin ver** seçeneği etkinse, anahtar kasasına erişmesine izin verilen güvenilir hizmetlerin bir listesi aşağıda verilmiştir.

|Güvenilen hizmet|Desteklenen kullanım senaryoları|
| --- | --- |
|Azure sanal makineler dağıtım hizmeti|[Sertifikaları, müşteri tarafından yönetilen Key Vault sanal makinelere dağıtın](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/).|
|Azure Resource Manager şablonu dağıtım hizmeti|[Dağıtım sırasında güvenli değerler geçirin](../azure-resource-manager/resource-manager-keyvault-parameter.md).|
|Azure disk şifreleme birimi şifreleme hizmeti|Sanal makine dağıtımı sırasında BitLocker anahtarına (Windows VM) veya DM parolasının (Linux VM) ve anahtar şifreleme anahtarına erişime izin verin. Bu, [Azure disk şifrelemesini](../security/fundamentals/encryption-overview.md)mümkün bir şekilde sunar.|
|Azure Backup|[Azure Backup](../backup/backup-introduction-to-azure-backup.md)kullanarak Azure sanal makineler yedeklemesi sırasında ilgili anahtarların ve parolaların yedeklenme ve geri yüklenmesine izin verin.|
|Exchange Online & SharePoint Online|[Müşteri anahtarına](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697)sahip Azure depolama hizmeti şifrelemesi müşteri anahtarına erişime izin verin.|
|Azure Information Protection|Azure Information Protection için kiracı anahtarına erişime izin ver [.](https://docs.microsoft.com/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Key Vault aracılığıyla Azure Web uygulaması sertifikasını dağıtın](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Veritabanı|[Azure SQL veritabanı ve veri ambarı için kendi anahtarını getir desteğiyle saydam veri şifrelemesi](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current).|
|Azure Depolama|[Depolama Hizmeti Şifrelemesi Azure Key Vault müşteri tarafından yönetilen anahtarları kullanma](../storage/common/storage-service-encryption-customer-managed-keys.md).|
|Azure Data Lake Store|Müşteri tarafından yönetilen bir anahtarla [Azure Data Lake Store verilerin şifrelenmesi](../data-lake-store/data-lake-store-encryption.md) .|
|Azure Databricks|[Hızlı, kolay ve işbirliğine dayalı Apache Spark tabanlı analiz hizmeti](../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[MSI kullanarak Key Vault özel etki alanı için sertifika dağıtma](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|
|Azure Data Factory|[Data Factory Key Vault veri deposu kimlik bilgilerini getir](https://go.microsoft.com/fwlink/?linkid=2109491)|



> [!NOTE]
> İlgili hizmetlerin Key Vault erişimi almasını sağlamak için ilgili Key Vault erişim ilkelerini ayarlamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* [Anahtar kasanızın güvenliğini sağlama](key-vault-secure-your-key-vault.md)
* [Azure Key Vault güvenlik duvarlarını ve sanal ağları yapılandırma](key-vault-network-security.md)
