---
title: Azure kanıtlama 'nı Azure PowerShell ile ayarlama
description: Azure PowerShell kullanarak bir kanıtlama sağlayıcısı ayarlama ve yapılandırma.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eca74ffe7b62cc5071d8ebaeefab52e5e59409d4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832239"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell ile Azure kanıtlama ayarlama

Azure PowerShell kullanarak bir kanıtlama sağlayıcısı oluşturmak ve yapılandırmak için aşağıdaki adımları izleyin. Azure PowerShell nasıl yükleneceği ve çalıştırılacağı hakkında bilgi için bkz. [Azure PowerShell genel bakış](/powershell/azure/) .

PowerShell Galerisi, kullanım dışı Aktarım Katmanı Güvenliği (TLS) 1,0 ve 1,1 sürümlerini içerir. TLS 1,2 veya sonraki bir sürüm önerilir. Bu nedenle, aşağıdaki hataları alabilirsiniz:

- Uyarı: ' ' paket kaynağı çözümlenemedi https://www.powershellgallery.com/api/v2
- PackageManagement\Install-Package: belirtilen arama ölçütleri ve modül adı için eşleşme bulunamadı 

PowerShell Galerisi etkileşim kurmaya devam etmek için Install-Module komutlarından önce aşağıdaki komutu çalıştırın

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Install az. kanıtlama PowerShell Module

Azure PowerShell olan makinede, Azure kanıtlama için cmdlet 'leri içeren az. kanıtlama PowerShell modülünü yükler.  

### <a name="initial-installation"></a>İlk yükleme

Tüm mevcut PowerShell pencerelerini sonlandırın.

"Geçerli kullanıcı" yüklemek için, yükseltilmiş olmayan bir PowerShell penceresi başlatın ve şunu çalıştırın:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

"Tüm kullanıcılar" ı yüklemek için yükseltilmiş bir PowerShell penceresi başlatın ve şunu çalıştırın:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Yükseltilmiş PowerShell konsolunu kapatın.

### <a name="update-the-installation"></a>Yüklemeyi güncelleştirme

Tüm mevcut PowerShell pencerelerini sonlandırın.

"Geçerli kullanıcı" öğesini güncelleştirmek için, yükseltilmiş olmayan bir PowerShell penceresi başlatın ve şunu çalıştırın:

```powershell
Update-Module -Name Az.Attestation
```

"Tüm kullanıcılar" ı güncelleştirmek için yükseltilmiş bir PowerShell penceresi başlatın ve şunu çalıştırın:

```powershell
Update-Module -Name Az.Attestation
```

Yükseltilmiş PowerShell konsolunu kapatın.

### <a name="get-installed-modules"></a>Yüklü modülleri al

Kanıtlama işlemlerini desteklemek için gereken az modüllerin en düşük sürümü:
- Az 4.5.0
- Az. Accounts 1.9.2
- Az. kanıtlama 0.1.8

Tüm az modüllerin yüklü sürümünü doğrulamak için aşağıdaki komutu çalıştırın 

```powershell
Get-InstalledModule
```
Sürümler en düşük gereksinimle eşleşmez Update-Module komutları çalıştırın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

PowerShell konsolunda Azure 'da oturum açın (yükseltilmiş erişim ayrıcalıkları olmadan).

```powershell
Connect-AzAccount
```

Gerekirse, Azure kanıtlama için kullanılacak aboneliğe geçiş yapın.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Microsoft. kanıtlama kaynak sağlayıcısını Kaydet

Microsoft. kanıtlama kaynak sağlayıcısını abonelikte kaydettirin. Azure kaynak sağlayıcıları ve kaynak sağlayıcılarının nasıl yapılandırılacağı ve yönetileceği hakkında daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md). Bir kaynak sağlayıcısı kaydetmenin bir abonelik için yalnızca bir kez gerekli olduğunu unutmayın.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Azure kanıtlama 'nın bölgesel kullanılabilirliği

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Azure Kaynak grubu oluşturma

Kanıtlama sağlayıcısı için bir kaynak grubu oluşturun. Diğer Azure kaynaklarının (istemci uygulama örneğini içeren bir sanal makine dahil) aynı kaynak grubuna yerleştirileceğini unutmayın.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

 > [!NOTE]
   > Bu kaynak grubunda bir kanıtlama sağlayıcısı oluşturulduktan sonra, bir Azure AD kullanıcısının ilke yapılandırma/ilke imzalayan sertifika yönetimi gibi işlemleri gerçekleştirmek için sağlayıcı üzerinde **kanıtlama katılımcısı** rolüne sahip olması gerekir. Bu izinler, abonelik/kaynak grubundaki **sahip** (joker karakter izinleri)/ **katkıda bulunan** (joker karakter izinleri) gibi rollerle de devralınabilir.  


## <a name="create-and-manage-an-attestation-provider"></a>Kanıtlama sağlayıcısı oluşturma ve yönetme

New-AzAttestation bir kanıtlama sağlayıcısı oluşturur.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile, bir güvenilen İmzalama anahtarları kümesini belirten bir dosyadır. PolicySignerCertificateFile parametresi için bir dosya adı belirtilmişse, kanıtlama sağlayıcısı yalnızca imzalı JWT biçimindeki ilkelerle yapılandırılabilir. Else ilkesi, metin veya işaretsiz JWT biçiminde yapılandırılabilir.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

PolicySignersCertificateFile örnek için bkz. [ilke imzalayan sertifika örnekleri](policy-signer-examples.md).

Get-AzAttestation durum ve AttestURI gibi kanıtlama sağlayıcısı özelliklerini alır. AttestURI ' yi daha sonra gerekli olacağı için bir yere göz atın.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

Yukarıdaki komut aşağıdaki gibi bir çıktı üretmelidir: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Kanıtlama sağlayıcıları Remove-AzAttestation cmdlet 'i kullanılarak silinebilir.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>İlke yönetimi

İlkeleri yönetmek için, bir Azure AD kullanıcısı "eylemler" için aşağıdaki izinleri gerektirir:
- Microsoft. kanıtlama/attestationProviders/kanıtlama/okuma
- Microsoft. kanıtlama/attestationProviders/kanıtlama/yazma
- Microsoft. kanıtlama/attestationProviders/kanıtlama/silme

 Bu eylemleri gerçekleştirmek için bir Azure AD kullanıcısının kanıtlama sağlayıcısında **kanıtlama katılımcısı** rolü olmalıdır. Bu izinler, abonelik/kaynak grubundaki **sahip** (joker karakter izinleri)/ **katkıda bulunan** (joker karakter izinleri) gibi rollerle de devralınabilir.  

İlkeleri okumak için, bir Azure AD kullanıcısı "eylemler" için aşağıdaki izinleri gerektirir:
- Microsoft. kanıtlama/attestationProviders/kanıtlama/okuma

 Bu eylemi gerçekleştirmek için bir Azure AD kullanıcısının kanıtlama sağlayıcısında **kanıtlama okuyucusu** rolü olmalıdır. Okuma izni Ayrıca, abonelik/kaynak grubundaki **okuyucu** (joker karakter izinleri) gibi rollerle devralınabilir.  

Aşağıdaki PowerShell cmdlet 'leri bir kanıtlama sağlayıcısı için ilke yönetimi sağlar (bir seferde bir t).

Get-AzAttestationPolicy belirtilen t için geçerli ilkeyi döndürür. Cmdlet, ilkeyi ilkenin hem metin hem de JWT biçiminde görüntüler.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

Desteklenen t türleri şunlardır "SgxEnclave", "OpenEnclave" ve "VbsEnclave".

Set-AttestationPolicy, belirtilen t için yeni bir ilke ayarlar. Cmdlet, ilkeyi metin veya JWT biçiminde kabul eder ve PolicyFormat parametresi tarafından denetlenir. PolicyFormat için varsayılan değer "Text" değeridir. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Bir kanıtlama sağlayıcısının oluşturulması sırasında PolicySignerCertificateFile sağlanırsa, ilkeler yalnızca imzalı JWT biçiminde yapılandırılabilir. Else ilkesi, metin veya işaretsiz JWT biçiminde yapılandırılabilir.

JWT biçimindeki kanıtlama ilkesi, "AttestationPolicy" adlı bir talep içermelidir. İmzalı ilke için, JWT 'nin mevcut ilke imzalayan sertifikalara karşılık gelen özel anahtarla imzalanmış olması gerekir.

İlke örnekleri için bkz. [kanıtlama ilkesi örnekleri](policy-examples.md).

Reset-AzAttestationPolicy, belirtilen t için ilkeyi varsayılan olarak sıfırlar.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>İlke imzalayan sertifikaları yönetimi

PowerShell cmdlet 'lerinin aşağıdaki bir kanıtlama sağlayıcısı için ilke imzalayan sertifikaları yönetimi sağlar:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

İlke imzalayan sertifikası, "Maa-policyCertificate" adlı talebe sahip imzalı bir JWT. Talebin değeri, eklenecek güvenilir imzalama anahtarını içeren bir JWK değeridir. JWT, mevcut ilke imzalayan sertifikalarına karşılık gelen özel anahtarla imzalanmalıdır.

İlke imzalayan sertifikasının tüm anlam düzenlemesi 'nin PowerShell dışında yapılması gerektiğini unutmayın. PowerShell 'e göre düşünüldüğünde, basit bir dizedir.

İlke imzalayan sertifika örneği için bkz. [ilke imzalayan sertifika örnekleri](policy-signer-examples.md).

Cmdlet 'ler ve parametreleri hakkında daha fazla bilgi için bkz. [Azure kanıtlama PowerShell cmdlet 'leri](/powershell/module/az.attestation/#attestation) 

## <a name="next-steps"></a>Sonraki adımlar

- [Kanıtlama ilkesi yazma ve imzalama](author-sign-policy.md)
- [Kod örneklerini kullanarak bir SGX kuşatma atturuntest](/samples/browse/?expanded=azure&terms=attestation)
