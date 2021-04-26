---
title: Istenen durum yapılandırmasını kullanarak kimlik bilgilerini Azure 'a geçirin
description: PowerShell Istenen durum yapılandırması (DSC) kullanarak Azure sanal makinelerine kimlik bilgilerini güvenli bir şekilde nasıl geçirebileceğinizi öğrenin.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 05/02/2018
ms.openlocfilehash: 6817dd6baacd835b7d433177ff18af1238ee44a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560095"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Kimlik bilgilerini Azure DSCExtension işleyicisine geçirin

Bu makalede, Azure için Istenen durum yapılandırması (DSC) uzantısı ele alınmaktadır. DSC uzantı işleyicisine genel bir bakış için bkz. [Azure Istenen durum yapılandırması uzantı Işleyicisine giriş](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Kimlik bilgilerini geçir

Yapılandırma sürecinin bir parçası olarak Kullanıcı hesaplarını ayarlamanız, hizmetlere erişmeniz veya bir programı Kullanıcı bağlamına yüklemeniz gerekebilir. Bu işlemleri yapmak için kimlik bilgilerini sağlamanız gerekir.

Parametreli yapılandırma ayarlamak için DSC 'yi kullanabilirsiniz. Parametreli bir yapılandırmada, kimlik bilgileri yapılandırmaya geçirilir ve. mof dosyalarında güvenli bir şekilde depolanır. Azure uzantı işleyicisi, sertifikaların otomatik yönetimini sağlayarak kimlik bilgisi yönetimini basitleştirir.

Aşağıdaki DSC yapılandırma betiği, belirtilen parolaya sahip bir yerel kullanıcı hesabı oluşturur:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Yapılandırmanın bir parçası olarak **node localhost** dahil edilmesi önemlidir. Uzantı işleyicisi özellikle **node localhost** bildirimine bakar. Bu ifade eksikse, aşağıdaki adımlar çalışmaz. Typecast **[PSCredential]** eklemek de önemlidir. Bu özel tür, kimlik bilgisini şifrelemek için uzantıyı tetikler.

Bu betiği Azure Blob depolama alanına yayımlamak için:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Azure DSC uzantısını ayarlamak ve kimlik bilgisini sağlamak için:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Kimlik bilgisinin güvenliği nasıl sağlanır

Bu kodu çalıştırmak bir kimlik bilgisi ister. Kimlik bilgileri sağlandıktan sonra, bu, bellekte kısaca saklanır. Kimlik bilgisi **set-AzVMDscExtension** cmdlet 'i kullanılarak yayımlandığında, KIMLIK bilgisi https üzerinden VM 'ye iletilir. VM 'de, Azure yerel VM sertifikasını kullanarak diskte şifrelenmiş kimlik bilgilerini depolar. Kimlik bilgisinin bellek içinde kısaca şifresi çözülür ve daha sonra DSC 'ye iletilmesi için yeniden şifrelenir.

Bu işlem [, uzantı işleyicisi olmayan güvenli yapılandırmaların kullanmaktan](/powershell/scripting/dsc/pull-server/securemof)farklıdır. Azure ortamı, yapılandırma verilerini sertifikalar aracılığıyla güvenli bir şekilde iletmenin bir yolunu sunar. DSC uzantı işleyicisini kullandığınızda,   /  **configurationData** içinde $CertificatePath veya $CertificateID **$Thumbprint** girdisi sağlamanız gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure DSC uzantı işleyicisine giriş](dsc-overview.md)alın.
- [DSC uzantısının Azure Resource Manager şablonunu](dsc-template.md)inceleyin.
- PowerShell DSC hakkında daha fazla bilgi için [PowerShell belge merkezine](/powershell/scripting/dsc/overview/overview)gidin.
- PowerShell DSC 'yi kullanarak yönetebileceğiniz daha fazla işlevsellik ve daha fazla DSC kaynağı için [PowerShell Galerisi](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)' ne gözatabileceğinizi unutmayın.