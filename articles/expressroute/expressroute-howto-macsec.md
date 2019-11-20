---
title: 'Azure ExpressRoute: MACsec yapılandırma'
description: Bu makale, kenar yönlendiricileriniz ile Microsoft 'un uç yönlendiricileri arasındaki bağlantıları güvenli hale getirmek için MACsec 'i yapılandırmanıza yardımcı olur.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083541"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>ExpressRoute doğrudan bağlantı noktalarında MACsec yapılandırma

Bu makale, PowerShell kullanarak Edge yönlendiricileriniz ile Microsoft 'un uç yönlendiricileri arasındaki bağlantıları güvenli hale getirmek için MACsec 'i yapılandırmanıza yardımcı olur.

## <a name="before-you-begin"></a>Başlamadan önce

Yapılandırmaya başlamadan önce, aşağıdakileri onaylayın:

* [ExpressRoute doğrudan sağlama iş akışlarını](expressroute-erdirect-about.md)anlamış olursunuz.
* [ExpressRoute doğrudan bağlantı noktası kaynağı](expressroute-howto-erdirect.md)oluşturdunuz.
* PowerShell 'i yerel olarak çalıştırmak istiyorsanız, Azure PowerShell en son sürümünün bilgisayarınızda yüklü olduğunu doğrulayın.

### <a name="working-with-azure-powershell"></a>Azure PowerShell ile çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Oturum açın ve doğru aboneliği seçin

Yapılandırmayı başlatmak için Azure hesabınızda oturum açın ve kullanmak istediğiniz aboneliği seçin.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Azure Key Vault, MACsec gizli dizileri ve Kullanıcı Kimliği oluşturun

1. MACsec gizli dizilerini yeni bir kaynak grubunda depolamak için bir Key Vault örneği oluşturun.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Zaten bir Anahtar Kasası veya kaynak grubunuz varsa, bunları yeniden kullanabilirsiniz. Ancak, var olan anahtar kasanızda [ **geçici silme** özelliğini](../key-vault/key-vault-ovw-soft-delete.md) etkinleştirmeniz önemlidir. Geçici silme etkinleştirilmemişse, etkinleştirmek için aşağıdaki komutları kullanabilirsiniz:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Kullanıcı Kimliği oluşturun.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    New-Azuseratandıdentity geçerli bir PowerShell cmdlet 'i olarak tanınmazsa, aşağıdaki modülü (Yönetici modunda) yükleyip yukarıdaki komutu yeniden çalıştırın.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Bir bağlantı ilişki anahtarı (CAK) ve bir bağlantı ilişkisi anahtar adı (CKN) oluşturun ve bunları anahtar kasasında depolayın.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Kullanıcı kimliğine Al iznini atayın.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Artık bu kimlik, anahtar kasasından, örneğin, ve CKN gizli dizilerini alabilir.
5. Bu Kullanıcı kimliğini ExpressRoute tarafından kullanılacak şekilde ayarlayın.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. ExpressRoute doğrudan bağlantı noktalarında MACsec yapılandırma

### <a name="to-enable-macsec"></a>MACsec 'i etkinleştirmek için

Her ExpressRoute doğrudan örneğinde iki fiziksel bağlantı noktası bulunur. Her iki bağlantı noktasında aynı anda MACsec 'i etkinleştirmeyi veya aynı anda bir bağlantı noktasında MACsec 'i etkinleştirmeyi seçebilirsiniz. Tek seferde bir bağlantı noktası oluşturma (diğer bağlantı noktasına hizmet verirken trafiği etkin bir bağlantı noktasına geçirerek), ExpressRoute Direct 'niz zaten hizmette olduğunda kesintiye en aza indirmenize yardımcı olabilir.

1. MACsec gizli dizilerini ve şifre ayarlama ve Kullanıcı kimliğini bağlantı noktasıyla ilişkilendirin ve bu sayede ExpressRoute yönetim kodunun gerekirse MACsec gizli anahtarlara erişebilmesi gerekir.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Seçim Bağlantı noktaları yönetimsel bir durumdaysa, bağlantı noktalarını getirmek için aşağıdaki komutları çalıştırabilirsiniz.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Bu noktada MACsec, Microsoft tarafında bulunan ExpressRoute doğrudan bağlantı noktalarında etkinleştirilir. Bu uygulamayı Edge cihazlarınızda yapılandırmadıysanız, aynı MACsec gizli dizileri ve şifrelemeniz ile yapılandırmayı da deneyebilirsiniz.

### <a name="to-disable-macsec"></a>MACsec 'i devre dışı bırakmak için

Eğer ExpressRoute doğrudan Örneğinizde MACsec artık istenmiyorsa, devre dışı bırakmak için aşağıdaki komutları çalıştırabilirsiniz.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Bu noktada, MACsec, Microsoft tarafındaki ExpressRoute doğrudan bağlantı noktalarında devre dışıdır.

### <a name="test-connectivity"></a>Bağlantıyı test etme
ExpressRoute doğrudan bağlantı noktalarında MACsec 'i (MACsec anahtar güncelleştirmesi dahil) yapılandırdıktan sonra, devrelerin BGP oturumlarının çalışır duruma olup olmadığını [kontrol edin](expressroute-troubleshooting-expressroute-overview.md) . Henüz bağlantı noktaları üzerinde herhangi bir devreniz yoksa, lütfen önce bir tane oluşturun ve Azure özel eşlemesini veya devresinin Microsoft eşlemesini ayarlayın. MACsec 'in, MACsec anahtar uyumsuzluğu da dahil olmak üzere yanlış yapılandırılmış ise, ağ cihazlarınızla Microsoft 'un ağ cihazları arasında, katman 2 ve BGP kurulumu 'nda, katman 3 ' te ARP çözünürlüğü görmezsiniz. Her şey düzgün şekilde yapılandırıldıysa BGP yollarının her iki yönde de doğru tanıtıldığı ve uygulama veri akışının ExpressRoute üzerinden uygun şekilde olduğunu görmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar
1. [ExpressRoute doğrudan üzerinde bir ExpressRoute devresi oluşturma](expressroute-howto-erdirect.md)
2. [Azure sanal ağına bir ExpressRoute bağlantı hattı bağlama](expressroute-howto-linkvnet-arm.md)
3. [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
