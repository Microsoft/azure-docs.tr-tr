---
title: RDP özelliklerini PowerShell Windows sanal masaüstü (klasik) ile özelleştirme-Azure
description: PowerShell cmdlet 'leriyle Windows sanal masaüstü (klasik) için RDP özelliklerini özelleştirme.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: db7d44cfdcb0f91a3633373c12f00c6eb8c94d52
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445083"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Windows sanal masaüstü (klasik) konak havuzu için Uzak Masaüstü Protokolü özelliklerini özelleştirme

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../customize-rdp-properties.md)bakın.

Çoklu izleyici deneyimi ve ses yeniden yönlendirme gibi bir konak havuzunun Uzak Masaüstü Protokolü (RDP) özelliklerini özelleştirmek, kullanıcılarınız için gereksinimlerinize göre en iyi deneyimi sunmanızı sağlar. **Set-RdsHostPool** cmdlet 'inde **-customrdpproperty** parametresini kullanarak Windows sanal masaüstündeki RDP özelliklerini özelleştirebilirsiniz.

Desteklenen özelliklerin tam listesi ve bunların varsayılan değerleri için bkz. [desteklenen RDP dosyası ayarları](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) .

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](/powershell/windows-virtual-desktop/overview/) . Bundan sonra hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Varsayılan RDP özellikleri

Varsayılan olarak, yayımlanan RDP dosyaları aşağıdaki özellikleri içerir:

|RDP özellikleri | Masaüstü Bilgisayarlar | RemoteApp uygulamaları |
|---|---| --- |
| Çoklu monitör modu | Etkin | Yok |
| Sürücü yeniden yönlendirmeleri etkin | Sürücüler, pano, yazıcılar, COM bağlantı noktaları, USB cihazları ve akıllı kartlar| Sürücüler, pano ve Yazıcılar |
| Uzak ses modu | Yerel olarak oynat | Yerel olarak oynat |

Konak havuzu için tanımladığınız tüm özel özellikler, bu Varsayılanları geçersiz kılar.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Tek bir özel RDP özelliği ekleme veya düzenleme

Tek bir özel RDP özelliği eklemek veya düzenlemek için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![Özel bir R D P özelliğini düzenlemek için, Name ve FriendlyName ile birlikte Get-RDSRemoteApp PowerShell cmdlet 'inin ekran görüntüsü.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Birden çok özel RDP özelliği ekleme veya düzenleme

Birden çok özel RDP özelliği eklemek veya düzenlemek için, Özel RDP özelliklerini noktalı virgülle ayrılmış bir dize olarak sağlayarak aşağıdaki PowerShell cmdlet 'lerini çalıştırın:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![Özel bir R D P özelliğini düzenlemek için, Name ve FriendlyName ile birlikte Set-RDSRemoteApp PowerShell cmdlet 'inin ekran görüntüsü.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Tüm özel RDP özelliklerini Sıfırla

[Tek bir özel RDP özelliği ekleme veya düzenleme](#add-or-edit-a-single-custom-rdp-property)bölümündeki yönergeleri IZLEYEREK özel RDP özelliklerini varsayılan değerlerine sıfırlayabilirsiniz veya aşağıdaki PowerShell cmdlet 'ini çalıştırarak bir konak havuzu için tüm özel RDP özelliklerini sıfırlayabilirsiniz:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![PowerShell cmdlet 'inin adı ve FriendlyName vurgulanmış Get-RDSRemoteApp ekran görüntüsü.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık belirli bir konak havuzu için RDP özelliklerini özelleştirdiğinize göre, bir kullanıcı oturumunun parçası olarak test etmek için bir Windows sanal masaüstü istemcisinde oturum açabilirsiniz. Bu sonraki iki nasıl-TOS, tercih ettiğiniz istemciyi kullanarak bir oturuma nasıl bağlanacağınızı bildirir:

- [Windows Masaüstü istemcisine bağlanma](connect-windows-7-10-2019.md)
- [Web istemcisiyle bağlanma](connect-web-2019.md)