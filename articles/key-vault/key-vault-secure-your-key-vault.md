---
title: Anahtar kasasına güvenli erişim-Azure Key Vault | Microsoft Docs
description: Azure Key Vault, anahtarlar ve gizli dizileri için erişim izinlerini yönetin. Key Vault için kimlik doğrulama ve yetkilendirme modelini ve anahtar kasanızın güvenliğini sağlamayı içerir.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 04f4a71e6b54100e5a133958845cf732c2286b32
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301067"
---
# <a name="secure-access-to-a-key-vault"></a>Anahtar kasasına güvenli erişim

Azure Key Vault, şifreleme anahtarlarını ve sertifikalar, bağlantı dizeleri ve parolalar gibi gizli dizileri koruyan bir bulut hizmetidir. Bu veriler hassas ve iş açısından kritik olduğundan, yalnızca yetkili uygulamalara ve kullanıcılara izin vererek anahtar kasalarınıza güvenli bir şekilde erişmeniz gerekir. Bu makalede Key Vault erişim modeline genel bir bakış sunulmaktadır. Kimlik doğrulama ve yetkilendirmeyi açıklar ve anahtar kasalarınıza erişimin güvenliğini nasıl sağlayabileceğinizi açıklar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Erişim modeline genel bakış

Bir anahtar kasasına erişim, iki arabirim aracılığıyla denetlenir: **Yönetim düzlemi** ve **veri düzlemi**. Yönetim düzlemi Key Vault kendisini yönettiğiniz yerdir. Bu düzlemdeki işlemler, anahtar kasalarını oluşturmayı ve silmeyi, Key Vault özelliklerini almayı ve erişim ilkelerini güncelleştirmeyi içerir. Veri düzlemi, bir anahtar kasasında depolanan verilerle çalıştığınız yerdir. Anahtarlar, gizli diziler ve sertifikalar ekleyebilir, silebilir ve değiştirebilirsiniz.

Her iki düzlemde bir anahtar kasasına erişmek için, tüm çağıranların (kullanıcılar veya uygulamalar) uygun kimlik doğrulaması ve yetkilendirmesi olması gerekir. Kimlik doğrulama, arayanın kimliğini belirler. Yetkilendirme, çağıranın hangi işlemleri yürütebileceğini belirler. 

Her iki düzlem de kimlik doğrulaması için Azure Active Directory (Azure AD) kullanır. Yetkilendirme için, yönetim düzlemi rol tabanlı erişim denetimi (RBAC) kullanır ve veri düzlemi bir Key Vault erişim ilkesi kullanır.

## <a name="active-directory-authentication"></a>Active Directory kimlik doğrulaması

Bir Azure aboneliğinde bir Anahtar Kasası oluşturduğunuzda, bu, aboneliğin Azure AD kiracısı ile otomatik olarak ilişkilendirilir. Her iki düzlemdeki tüm çağıranlar bu kiracıya kaydolmalıdır ve anahtar kasasına erişmek için kimliğini doğrular. Her iki durumda da, uygulamalar Key Vault iki şekilde erişebilir:

- **Kullanıcı ve uygulama erişimi**: uygulama, oturum açmış bir kullanıcı adına Key Vault erişir. Bu tür erişimin örnekleri Azure PowerShell ve Azure portal içerir. Kullanıcı erişimi iki şekilde verilir. Kullanıcılar herhangi bir uygulamadan Key Vault erişebilir veya belirli bir uygulamayı ( _bileşik kimlik_olarak adlandırılır) kullanmaları gerekir.
- **Yalnızca uygulama erişimi**: uygulama bir Daemon hizmeti veya arka plan işi olarak çalışır. Uygulama kimliğine Anahtar Kasası erişimi verilir.

Her iki erişim türü için de uygulama Azure AD ile kimlik doğrulaması yapar. Uygulama, uygulama türüne göre desteklenen herhangi bir [kimlik doğrulama yöntemini](../active-directory/develop/authentication-scenarios.md) kullanır. Uygulama, erişim izni vermek için düzlemdeki bir kaynak için bir belirteç alır. Kaynak, Azure ortamına göre yönetim veya veri düzleminde bir uç noktadır. Uygulama belirteci kullanır ve Key Vault bir REST API isteği gönderir. Daha fazla bilgi edinmek için [tüm kimlik doğrulama akışını](../active-directory/develop/v2-oauth2-auth-code-flow.md)gözden geçirin.

Her iki düzlemde kimlik doğrulama için tek bir mekanizmanın çeşitli avantajları vardır:

- Kuruluşlar, kuruluştaki tüm anahtar kasaları için merkezi olarak erişimi denetleyebilir.
- Bir Kullanıcı ayrılsa bile, kuruluştaki tüm anahtar kasalarına erişimi anında kaybeder.
- Kuruluşlar, ek güvenlik için Multi-Factor Authentication 'ı etkinleştirmek gibi Azure AD 'deki seçenekleri kullanarak kimlik doğrulamasını özelleştirebilir.

## <a name="resource-endpoints"></a>Kaynak uç noktaları

Uygulamalar, uç noktalar aracılığıyla düzlemleri erişir. İki düzlemi için erişim denetimleri bağımsız olarak çalışır. Bir uygulamaya anahtar kasasındaki anahtarları kullanma erişimi vermek için, Key Vault erişim ilkesi kullanarak veri düzlemi erişimi verirsiniz. Bir kullanıcıya Key Vault özelliklerine ve etiketlere yönelik okuma erişimi vermek, ancak verilere (anahtarlar, gizlilikler veya sertifikalar) erişmek için, RBAC ile yönetim düzlemi erişimi verirsiniz.

Aşağıdaki tabloda yönetim ve veri düzlemleri için uç noktalar gösterilmektedir.

| Erişim @ no__t-0düzlemi | Erişim uç noktaları | Operations | Access @ no__t-0control mekanizması |
| --- | --- | --- | --- |
| Yönetim düzlemi | **Genel:**<br> management.azure.com:443<br><br> **Azure Çin 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure ABD:**<br> management.usgovcloudapi.net:443<br><br> **Azure Almanya:**<br> management.microsoftazure.de:443 | Anahtar kasaları oluşturun, okuyun, güncelleştirin ve silin<br><br>Key Vault erişim ilkelerini ayarlama<br><br>Key Vault etiketlerini ayarla | RBAC Azure Resource Manager |
| Veri düzlemi | **Genel:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Çin 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure ABD:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Almanya:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Anahtarlar: şifre çözme, şifreleme,<br> sarmalama geri al, sarmala, doğrula, imzala,<br> Al, Listele, Güncelleştir, oluştur,<br> İçeri aktarma, silme, yedekleme, geri yükleme<br><br> Gizlilikler: get, List, set, Delete | Key Vault erişim ilkesi |

## <a name="management-plane-and-rbac"></a>Yönetim düzlemi ve RBAC

Yönetim düzleminde, bir çağıranın yürütebileceği işlemleri yetkilendirmek için RBAC (rol tabanlı Access Control) kullanın. RBAC modelinde, her Azure aboneliğinin bir Azure AD örneği vardır. Bu dizinden kullanıcılara, gruplara ve uygulamalara erişim izni verirsiniz. Azure aboneliğindeki Azure Resource Manager dağıtım modelini kullanan kaynakları yönetmek için erişim izni verilir. Erişim vermek için [Azure Portal](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)veya [Azure Resource Manager REST API 'lerini](https://msdn.microsoft.com/library/azure/dn906885.aspx)kullanın.

Bir kaynak grubunda bir Anahtar Kasası oluşturup Azure AD 'yi kullanarak erişimi yönetebilirsiniz. Kullanıcılara veya gruplara bir kaynak grubundaki anahtar kasalarını yönetme yeteneği vermiş olursunuz. Uygun RBAC rolleri atayarak erişimi belirli bir kapsam düzeyinde verirsiniz. Anahtar kasalarını yönetmek üzere bir kullanıcıya erişim izni vermek için, belirli bir kapsamdaki kullanıcıya önceden tanımlı bir `key vault Contributor` rolü atarsınız. Aşağıdaki kapsamlar düzeyleri RBAC rolüne atanabilir:

- **Abonelik**: abonelik düzeyinde atanan bir RBAC rolü, bu aboneliğin içindeki tüm kaynak grupları ve kaynaklar için geçerlidir.
- **Kaynak grubu**: kaynak grubu düzeyinde atanan bir RBAC rolü, kaynak grubundaki tüm kaynaklar için geçerlidir.
- **Belirli kaynak**: belirli bir kaynak IÇIN atanan RBAC rolü bu kaynak için geçerlidir. Bu durumda, kaynak belirli bir Anahtar Kasası olur.

Önceden tanımlanmış birkaç rol vardır. Önceden tanımlanmış bir rol gereksinimlerinize uygun değilse, kendi rolünüzü tanımlayabilirsiniz. Daha fazla bilgi için bkz. [RBAC: yerleşik roller](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Bir kullanıcının bir Anahtar Kasası yönetim düzlemine `Contributor` izinleri varsa, Kullanıcı bir Key Vault erişim ilkesi ayarlayarak kendilerine veri düzlemine erişim izni verebilir. Anahtar kasalarınıza @no__t 0 rol erişimi olduğunu sıkı bir şekilde denetleyebilirsiniz. Anahtar kasalarınızı, anahtarlarınızı, sırları ve sertifikalarınızı yalnızca yetkili kişilerin erişebildiğinden ve yönetebilmesi için emin olun.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Veri düzlemi ve erişim ilkeleri

Bir Anahtar Kasası için Key Vault erişim ilkeleri ayarlayarak veri düzlemi erişimi verirsiniz. Bu erişim ilkelerini ayarlamak için, bir Kullanıcı, Grup veya uygulamanın bu Anahtar Kasası için yönetim düzlemi için `Contributor` izinlerine sahip olması gerekir.

Bir anahtar kasasındaki anahtarlar veya gizlilikler için belirli işlemleri yürütmek üzere bir Kullanıcı, Grup veya uygulama erişimi verirsiniz. Key Vault, bir Anahtar Kasası için en fazla 1.024 erişim ilkesi girişini destekler. Birkaç kullanıcıya veri düzlemi erişimi sağlamak için bir Azure AD güvenlik grubu oluşturun ve bu gruba kullanıcı ekleyin.

<a id="key-vault-access-policies"></a>Key Vault erişim ilkeleri, izinleri anahtarlar, gizlilikler ve sertifikaya ayrı olarak verir. Gizli dizileri değil yalnızca anahtarlara bir kullanıcı erişimi verebilirsiniz. Anahtarlar, gizli diziler ve sertifikalar için erişim izinleri kasa düzeyindedir. Key Vault erişim ilkeleri, belirli bir anahtar, gizli dizi ya da sertifika gibi ayrıntılı, nesne düzeyindeki izinleri desteklemez. Bir Anahtar Kasası için erişim ilkeleri ayarlamak için [Azure Portal](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)veya [Key Vault yönetimi REST API 'lerini](https://msdn.microsoft.com/library/azure/mt620024.aspx)kullanın.

> [!IMPORTANT]
> Key Vault erişim ilkeleri kasa düzeyinde geçerlidir. Bir kullanıcıya anahtar oluşturma ve silme izni verildiğinde, bu işlemleri ilgili anahtar kasasındaki tüm anahtarlar üzerinde gerçekleştirebilirler.
>

[Azure Key Vault için sanal ağ hizmet uç noktalarını](key-vault-overview-vnet-service-endpoints.md)kullanarak veri düzlemi erişimini kısıtlayabilirsiniz. Güvenlik [duvarları ve sanal ağ kurallarını](key-vault-network-security.md) ek bir güvenlik katmanı için yapılandırabilirsiniz.

## <a name="example"></a>Örnek

Bu örnekte, SSL için bir sertifika, verileri depolamak için Azure depolama ve imzalama işlemleri için RSA 2.048 bitlik bir anahtar kullanan bir uygulama geliştiriyoruz. Uygulamamız bir Azure sanal makinesinde (VM) (veya bir sanal makine ölçek kümesi) çalışır. Uygulama gizli dizileri depolamak için bir Anahtar Kasası kullanabiliriz. Azure AD ile kimlik doğrulaması yapmak için uygulama tarafından kullanılan önyükleme sertifikasını depolayabiliriz.

Aşağıdaki depolanmış anahtarlar ve gizli anahtarlara erişmeniz gerekir:
- **SSL sertifikası**: SSL için kullanılır.
- **Depolama anahtarı**: depolama hesabına erişmek için kullanılır.
- **RSA 2.048 bit anahtarı**: imzalama işlemleri için kullanılır.
- **Önyükleme sertifikası**: Azure AD kimlik doğrulaması için kullanılır. Erişim verildikten sonra, depolama anahtarını getirip imzalama için RSA anahtarını kullanabilirsiniz.

Uygulamamızı kimin yönetebileceğini, dağıtabileceğinizi ve denetleyeceğinizi belirlemek için aşağıdaki rolleri tanımlamanız gerekir:
- **Güvenlik ekibi**: CSO (Güvenlik Müdürü) veya benzer katkıda bulunanlar ofisindeki BT personeli. Güvenlik ekibi, gizli dizileri doğru bir şekilde ping işlemi yapmaktan sorumludur. Gizli dizileri SSL sertifikaları, imzalama için RSA anahtarları, bağlantı dizeleri ve depolama hesabı anahtarları içerebilir.
- **Geliştiriciler ve işleçler**: uygulamayı geliştiren ve Azure 'da dağıtan personel. Bu ekibin üyeleri güvenlik personelinin bir parçası değildir. SSL sertifikaları ve RSA anahtarları gibi hassas verilere erişimi olmamalıdır. Yalnızca dağıttıkları uygulamanın gizli verilere erişimi olmalıdır.
- **Denetçiler**: Bu rol, geliştirme veya genel BT personelinin üyesi olmayan katkıda bulunanlar içindir. Güvenlik standartlarıyla uyumluluğu sağlamak için sertifikaların, anahtarların ve parolaların kullanımını ve bakımını gözden geçirir. 

Uygulamamızın kapsamı dışında başka bir rol var: abonelik (veya kaynak grubu) Yöneticisi. Abonelik Yöneticisi güvenlik ekibi için ilk erişim izinlerini ayarlar. Uygulama için gerekli kaynaklara sahip bir kaynak grubunu kullanarak güvenlik ekibine erişim izni verir.

Rollerimiz için aşağıdaki işlemleri yetkilendirmemiz gerekir:

**Güvenlik ekibi**
- Anahtar kasaları oluşturun.
- Key Vault günlüğünü açın.
- Anahtar ve gizli dizileri ekleyin.
- Olağanüstü durum kurtarma için anahtarların yedeklerini oluşturun.
- Kullanıcılara ve uygulamalara belirli işlemler için izin vermek üzere Key Vault erişim ilkeleri ayarlayın.
- Anahtarları ve gizli dizileri düzenli olarak alın.

**Geliştiriciler ve işleçler**
- İmzalama için önyükleme ve SSL sertifikaları (parmak izleri), depolama anahtarı (gizli URI) ve RSA anahtarı (anahtar URI) için Güvenlik ekibinden başvurular alın.
- Anahtarları ve gizli dizileri programlama yoluyla erişmek için uygulamayı geliştirin ve dağıtın.

**Denetçiler**
- Anahtarların ve parolaların doğru kullanımını ve veri güvenliği standartlarıyla uyumluluğunu onaylamak için Key Vault günlüklerini gözden geçirin.

Aşağıdaki tabloda rollerimiz ve uygulamamız için erişim izinleri özetlenmektedir. 

| Rol | Yönetim düzlemi izinleri | Veri düzlemi izinleri |
| --- | --- | --- |
| Güvenlik ekibi | Katkıda bulunan Key Vault | Anahtarlar: yedekleme, oluşturma, silme, alma, içeri aktarma, listeleme, geri yükleme<br>Gizlilikler: tüm işlemler |
| Geliştiriciler ve @ no__t-0işleçleri | Key Vault dağıtma izni<br><br> **Note**: Bu izin, dağıtılan VM 'lerin bir anahtar kasasından gizli dizileri almasına izin verir. | Hiçbiri |
| Denetçiler | Hiçbiri | Anahtarlar: listeleme<br>Parolalar: listeleme<br><br> **Not**: Bu izin, denetçilerin, günlüklere yayılmayan anahtarlar ve gizli diziler için öznitelikleri (Etiketler, etkinleştirme tarihleri, sona erme tarihleri) incelemeye olanak sağlar. |
| Uygulama | Hiçbiri | Anahtarlar: imzalama<br>Parolalar: imzalama |

Üç takım rolünün, Key Vault izinlerle birlikte diğer kaynaklara erişmesi gerekir. VM 'Leri (veya Azure App Service Web Apps özelliğini) dağıtmak için, geliştiricilerin ve işleçlerin bu kaynak türlerine `Contributor` erişimi olması gerekir. Denetçilerin Key Vault günlüklerinin depolandığı depolama hesabına okuma erişimi olması gerekir.

Program aracılığıyla sertifikaların, erişim anahtarlarının ve parolaların nasıl dağıtılacağı hakkında daha fazla bilgi için şu kaynaklara bakın:
- [Müşteri tarafından yönetilen anahtar kasasından](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blog gönderisi) sanal makinelere sertifika dağıtmayı öğrenin.
- [Azure Key Vault istemci örneklerini](https://www.microsoft.com/download/details.aspx?id=45343)indirin. Bu içerik bir önyükleme sertifikasının bir anahtar kasasına erişmek için Azure AD 'de kimlik doğrulaması yapmak üzere nasıl kullanılacağını gösterir.

Azure portal kullanarak erişim izinlerinin çoğuna izin verebilirsiniz. Ayrıntılı izinler vermek için Azure PowerShell veya Azure CLı kullanabilirsiniz.

Bu bölümdeki PowerShell kod parçacıkları aşağıdaki varsayımlar ile oluşturulmuştur:
- Azure AD yöneticisi, üç rolü temsil eden güvenlik grupları oluşturdu: contoso güvenlik ekibi, contoso uygulama DevOps ve contoso uygulama denetçileri. Yönetici kullanıcıları ilgili gruplarına ekledi.
- Tüm kaynaklar **Contosoapprg** kaynak grubunda bulunur.
- Key Vault Günlükler **contosologstorage** depolama hesabında depolanır. 
- **Contosokeykasası** anahtar kasası ve **contosologstorage** Storage hesabı aynı Azure konumudur.

Abonelik Yöneticisi `key vault Contributor` ve `User Access Administrator` rollerini güvenlik ekibine atar. Bu roller güvenlik ekibinin, her ikisi de **Contosoapprg** kaynak grubundaki diğer kaynaklara ve anahtar kasalarına erişimi yönetmesine olanak tanır.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Güvenlik ekibi bir Anahtar Kasası oluşturur ve günlük ve erişim izinlerini ayarlar. Key Vault erişim ilkesi izinleri hakkında daha fazla bilgi için bkz. [Azure Key Vault anahtarlar, gizli diziler ve sertifikalar hakkında](about-keys-secrets-and-certificates.md).

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Tanımlı özel rollerimiz yalnızca **Contosoapprg** kaynak grubunun oluşturulduğu aboneliğe atanabilir. Diğer aboneliklerdeki diğer projelere özel bir rol kullanmak için, rol için kapsama diğer abonelikler ekleyin.

DevOps personeliniz için, Anahtar Kasası `deploy/action` izni için özel rol ataması kaynak grubunun kapsamına alınır. Yalnızca **Contosoapprg** kaynak grubunda oluşturulan VM 'lerin gizli DIZILERI (SSL ve önyükleme sertifikaları) erişimine izin verilir. DevOps üyesine göre diğer kaynak gruplarında oluşturulan VM 'Ler, VM 'nin gizli URI 'lerine sahip olsa bile bu gizli anahtarlara erişemez.

Örneğimizde basit bir senaryo açıklanmaktadır. Gerçek yaşam senaryoları daha karmaşık olabilir. Gereksinimlerinize göre anahtar kasanıza yönelik izinleri ayarlayabilirsiniz. Güvenlik ekibinin, uygulamalarında DevOps personeli tarafından kullanılan anahtar ve gizli başvuruları (URI 'Ler ve parmak izleri) sağladığını kabul ediyoruz. Geliştiricilere ve işleçlere herhangi bir veri düzlemi erişimi gerekmez. Anahtar kasanızın güvenliğini sağlama konusunda odaklandık. [VM 'lerinizi](https://azure.microsoft.com/services/virtual-machines/security/), [depolama hesaplarınızı](../storage/common/storage-security-guide.md)ve diğer Azure kaynaklarınızı güvenli hale getirmeye benzer bir göz kazandırın.

> [!NOTE]
> Bu örnek, Key Vault erişimin üretimde nasıl kilitli olduğunu gösterir. Geliştiricilerin, kasalarını, VM 'Leri ve uygulamayı geliştirdikleri depolama hesabını yönetmek için tam izinlerle kendi aboneliğine veya kaynak grubuna sahip olmaları gerekir.

[Key Vault güvenlik duvarlarını ve sanal ağları yapılandırarak](key-vault-network-security.md)anahtar kasanıza ek güvenli erişim ayarlamanızı öneririz.

## <a name="resources"></a>Kaynaklar

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: yerleşik roller](../role-based-access-control/built-in-roles.md)

* [Kaynak Yöneticisi dağıtımı ve klasik dağıtımı anlama](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Azure PowerShell ile RBAC 'yi yönetme](../role-based-access-control/role-assignments-powershell.md) 

* [REST API ile RBAC 'yi yönetme](../role-based-access-control/role-assignments-rest.md)

* [Microsoft Azure için RBAC](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Bu 2015 Microsoft Ignite Konferansı videosu, Azure 'daki erişim yönetimi ve raporlama özelliklerini tartışır. Azure AD 'yi kullanarak Azure aboneliklerine erişimin güvenliğini sağlamaya yönelik en iyi yöntemleri de araştırır.

* [OAuth 2,0 ve Azure AD kullanarak Web uygulamalarına erişim yetkisi verme](../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Key Vault Management REST API 'Leri](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Key Vault erişim ilkesini ayarlama da dahil olmak üzere, anahtar kasanızı programlı bir şekilde yönetmek için REST API 'Lerinin başvurusu.

* [REST API 'Leri Key Vault](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Anahtar erişim denetimi](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Gizli anahtar erişim denetimi](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* PowerShell kullanarak Key Vault erişim ilkesini [ayarlayın](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) ve [kaldırın](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) .
  
## <a name="next-steps"></a>Sonraki adımlar

[Key Vault güvenlik duvarlarını ve sanal ağları](key-vault-network-security.md)yapılandırın.

Bir yöneticiye yönelik bir başlangıç öğreticisi için bkz. Azure Key Vault nedir [?](key-vault-overview.md).

Key Vault kullanım günlüğü hakkında daha fazla bilgi için bkz. [Azure Key Vault günlüğü](key-vault-logging.md).

Azure Key Vault ile anahtarları ve gizli dizileri kullanma hakkında daha fazla bilgi için bkz. [anahtarlar ve gizli diziler hakkında](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Key Vault hakkında sorularınız varsa [forumları](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)ziyaret edin.
