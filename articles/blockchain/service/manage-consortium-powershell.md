---
title: Azure PowerShell kullanarak Azure blok zinciri hizmeti üyelerini yönetme
description: Azure PowerShell kullanarak Azure blok zinciri hizmeti Consortium üyelerini yönetmeyi öğrenin.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: c35a3bd99518825805c2f29cfdc586e1ccf5b0bb
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329172"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-by-using-powershell"></a>PowerShell kullanarak Azure blok zinciri hizmeti 'nde konsorsiyum üyelerini yönetme

PowerShell 'i kullanarak Azure blok zinciri hizmetinize yönelik blok zinciri Konsorsiyumu üyelerini yönetebilirsiniz. Yönetici ayrıcalıklarına sahip Üyeler blok zinciri Konsorsiyumu içindeki tüm katılımcılar için rolleri davet edebilir, ekleyebilir, kaldırabilir ve değiştirebilir. Kullanıcı ayrıcalıklarına sahip Üyeler blok zinciri Konsorsiyumu içindeki tüm katılımcıları görüntüleyebilir ve üye görünen adını değiştirebilir.

## <a name="prerequisites"></a>Önkoşullar

* [Azure Portal](create-member.md)kullanarak bir blok zinciri üyesi oluşturun.
* Yarışmada, Üyeler ve düğümler hakkında daha fazla bilgi için bkz. [Azure blok zinciri hizmeti Consortium](consortium.md).

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell’i açma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Ayrıca, [Shell.Azure.com/PowerShell](https://shell.azure.com/powershell)' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell açabilirsiniz. Kod bloklarını kopyalamak için **Kopyala** ' yı seçin, Cloud Shell yapıştırın ve çalıştırmak için **ENTER** ' u seçin.

## <a name="install-the-powershell-module"></a>PowerShell modülünü yükler

PowerShell Galerisi Microsoft.AzureBlockchainService.ConsortiumManagement.PS paketini yükler.

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>Bilgi tercihini ayarla

Bilgi tercih değişkenini ayarlayarak cmdlet 'leri yürütürken daha fazla bilgi edinebilirsiniz. Varsayılan olarak, *$InformationPreference* *SilentlyContinue*olarak ayarlanır.

Cmdlet 'lerden daha ayrıntılı bilgi için, PowerShell 'de tercihi aşağıdaki gibi ayarlayın:

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>Web3 bağlantısı oluşturma

Consortium üyelerini yönetmek için, blok zinciri hizmeti üye uç noktanıza bir Web3 bağlantısı kurun. Bu betiği, konsorsiyum yönetimi cmdlet 'lerini çağırmak için genel değişkenleri ayarlamak üzere kullanabilirsiniz.

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

*@No__t-1üye hesabı parolasını @ no__t-2* ' i, üyeyi oluştururken kullandığınız üye hesap parolasıyla değiştirin.

Azure portal diğer değerleri bulun:

1. [Azure Portal](https://portal.azure.com)’ında oturum açın.
1. Varsayılan blok zinciri hizmeti üyesine **genel bakış** sayfasına gidin.

    ![Üyeye genel bakış](./media/manage-consortium-powershell/member-overview.png)

    *@No__t-1Üye hesabı @ no__t-2* ve *\<rootcontract adresi @ no__t-5* ' i portaldan alınan değerlerle değiştirin.

1. Uç nokta adresi için **işlem düğümleri**' ni seçin ve ardından **varsayılan işlem düğümünü**seçin. Varsayılan düğüm, blok zinciri üyesiyle aynı ada sahiptir.
1. **Bağlantı dizelerini**seçin.

    ![Bağlantı dizeleri](./media/manage-consortium-powershell/connection-strings.png)

    *@No__t-1Endpoint Address @ no__t-2* ' i **https (erişim anahtarı 1)** veya **https (erişim anahtarı 2)** değeriyle değiştirin.

## <a name="manage-the-network-and-smart-contracts"></a>Ağ ve akıllı sözleşmeleri yönetme

Blok zinciri uç noktasının, konsorsiyumun yönetiminden sorumlu akıllı sözleşmeleri ile bağlantı kurmak için ağ ve akıllı anlaşma cmdlet 'lerini kullanın.

### <a name="import-consortiummanagementcontracts"></a>İçeri aktarma-Consortiummanagementsözleşmeleri

Bu cmdlet 'i, konsorsiyum yönetiminin akıllı sözleşmelerine bağlamak için kullanın. Bu sözleşmeler, konsorsiyumun içindeki üyeleri yönetmek ve zorlamak için kullanılır.

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| RootContractAddress | Konsorsiyum yönetimi akıllı sözleşmelerinin kök sözleşme adresi | Yes |
| Web3Client | New-Web3Connection ' den edinilen Web3Client nesnesi | Yes |

#### <a name="example"></a>Örnek

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

Bu cmdlet 'i, uzak düğümün yönetim hesabının bilgilerini tutacak bir nesne oluşturmak için kullanın.

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| ManagedAccountAddress | Blok zinciri üyesi hesap adresi | Yes |
| ManagedAccountPassword | Hesap adresi parolası | Yes |

#### <a name="example"></a>Örnek

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

İşlem düğümünün RPC uç noktasıyla bağlantı kurmak için bu cmdlet 'i kullanın.

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | Blok zinciri üye uç nokta adresi | Yes |

#### <a name="example"></a>Örnek

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>Konsorsiyum üyelerini yönetme

Konsorsiyumun içindeki üyeleri yönetmek için konsorsiyum üye yönetimi cmdlet 'lerini kullanın. Kullanılabilir eylemler, konsorsiyumun rolüne bağlıdır.

### <a name="get-blockchainmember"></a>Get-BlockchainMember

Bu cmdlet 'i kullanarak, konsorsiyumun üye ayrıntılarını veya listesini alın.

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| Adı | Ayrıntılarını almak istediğiniz blok zinciri hizmeti üyesinin adı. Bir ad girildiğinde, üyenin ayrıntılarını döndürür. Bir ad atlandığında, tüm konsorsiyum üyelerinin bir listesini döndürür. | Hayır |
| Üyeleri | Import-ConsortiumManagementContracts öğesinden edinilen Üyeler nesnesi | Yes |
| Web3Client | New-Web3Connection ' den edinilen Web3Client nesnesi | Yes |

#### <a name="example"></a>Örnek

$ContractConnection değişkenini ayarlamak için [bir Web3 bağlantısı kurun](#establish-a-web3-connection) .

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>Örnek çıkış

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

Bir blok zinciri üyesini kaldırmak için bu cmdlet 'i kullanın.

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| Adı | Kaldırılacak üye adı | Yes |
| Üyeleri | Import-ConsortiumManagementContracts öğesinden edinilen Üyeler nesnesi | Yes |
| Web3Account | Import-Web3Account öğesinden elde edilen Web3Account nesnesi | Yes |
| Web3Client | New-Web3Connection ' den edinilen Web3Client nesnesi | Yes |

#### <a name="example"></a>Örnek

$ContractConnection ve $MemberAccount değişkenlerini ayarlamak için [bir Web3 bağlantısı kurun](#establish-a-web3-connection) .

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

Bu cmdlet 'i, görünen ad ve konsorsiyum rolü de dahil olmak üzere blok zinciri üye özniteliklerini ayarlamak için kullanın.

Konsorsiyum yöneticileri, tüm Üyeler için **DisplayName** ve **role** ayarlayabilir. Kullanıcı rolüne sahip bir konsorsiyum üyesi yalnızca kendi üyesinin görünen adını değiştirebilir.

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| Adı | Blok zinciri üyesinin adı | Yes |
| DisplayName | Yeni görünen ad | Hayır |
| AccountAddress | Hesap adresi | Hayır |
| Üyeleri | Import-ConsortiumManagementContracts öğesinden edinilen Üyeler nesnesi | Yes |
| Web3Account | Import-Web3Account öğesinden elde edilen Web3Account nesnesi | Yes |
| Web3Client |  New-Web3Connection ' den edinilen Web3Client nesnesi| Yes |

#### <a name="example"></a>Örnek

$ContractConnection ve $MemberAccount değişkenlerini ayarlamak için [bir Web3 bağlantısı kurun](#establish-a-web3-connection) .

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>Konsorsiyumun üyelerinin davetlerini yönetin

Konsorsiyum üyelerinin davetlerini yönetmek için konsorsiyum üye davetiyesi yönetimi cmdlet 'lerini kullanın. Kullanılabilir eylemler, konsorsiyumun rolüne bağlıdır.

### <a name="new-blockchainmemberinvitation"></a>New-Blockchainmemberdavetiyesi

Yeni üyeleri konsorsiya davet etmek için bu cmdlet 'i kullanın.

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| kaynak grubundaki | Davet edilecek üyenin Azure abonelik KIMLIĞI | Yes |
| Rol | Konsorsiyum rolü. Değerler yönetıcı veya Kullanıcı olabilir. YÖNETICI, konsorsiyum Yöneticisi rolüdür. Kullanıcı, konsorsiyum üyesi rolüdür. | Yes |
| Üyeleri | Import-ConsortiumManagementContracts öğesinden edinilen Üyeler nesnesi | Yes |
| Web3Account | Import-Web3Account öğesinden elde edilen Web3Account nesnesi | Yes |
| Web3Client | New-Web3Connection ' den edinilen Web3Client nesnesi | Yes |

#### <a name="example"></a>Örnek

$ContractConnection ve $MemberAccount değişkenlerini ayarlamak için [bir Web3 bağlantısı kurun](#establish-a-web3-connection) .

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-Blockchainmemberdavetiyesi

Bu cmdlet 'i bir konsorsiyum üyesinin davetiye durumunu almak veya listelemek için kullanın.

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| kaynak grubundaki | Davet edilecek üyenin Azure abonelik KIMLIĞI. Abonelik KIMLIĞI sağlanmışsa, abonelik KIMLIĞININ davet ayrıntılarını döndürür. Abonelik KIMLIĞI atlanırsa, tüm üye davetlerinin bir listesini döndürür. | Hayır |
| Üyeleri | Import-ConsortiumManagementContracts öğesinden edinilen Üyeler nesnesi | Yes |
| Web3Client | New-Web3Connection ' den edinilen Web3Client nesnesi | Yes |

#### <a name="example"></a>Örnek

$ContractConnection değişkenini ayarlamak için [bir Web3 bağlantısı kurun](#establish-a-web3-connection) .

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation – SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>Örnek çıkış

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-Blockchainmemberdavetiyesi

Bir konsorsiyum üyesinin davetini iptal etmek için bu cmdlet 'i kullanın.

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| kaynak grubundaki | İptal edilecek üyenin Azure abonelik KIMLIĞI | Yes |
| Üyeleri | Import-ConsortiumManagementContracts öğesinden edinilen Üyeler nesnesi | Yes |
| Web3Account | Import-Web3Account öğesinden elde edilen Web3Account nesnesi | Yes |
| Web3Client | New-Web3Connection ' den edinilen Web3Client nesnesi | Yes |

#### <a name="example"></a>Örnek

$ContractConnection ve $MemberAccount değişkenlerini ayarlamak için [bir Web3 bağlantısı kurun](#establish-a-web3-connection) .

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-Blockchainmemberdavetiyesi

Mevcut bir davetin **rolünü** ayarlamak için bu cmdlet 'i kullanın. Yalnızca konsorsiyum yöneticileri davetleri değiştirebilir.

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| Parametre | Açıklama | Gereklidir |
|-----------|-------------|:--------:|
| kaynak grubundaki | Davet edilecek üyenin Azure abonelik KIMLIĞI | Yes |
| Rol | Davet için yeni konsorsiyum rolü. Değerler **Kullanıcı** veya **yönetici**olabilir. | Yes |
| Üyeleri |  Import-ConsortiumManagementContracts öğesinden edinilen Üyeler nesnesi | Yes |
| Web3Account | Import-Web3Account öğesinden elde edilen Web3Account nesnesi | Yes |
| Web3Client | New-Web3Connection ' den edinilen Web3Client nesnesi | Yes |

#### <a name="example"></a>Örnek

$ContractConnection ve $MemberAccount değişkenlerini ayarlamak için [bir Web3 bağlantısı kurun](#establish-a-web3-connection) .

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>Sonraki adımlar

Yarışmao, Üyeler ve düğümler hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Azure blok zinciri hizmeti Consortium](consortium.md)
