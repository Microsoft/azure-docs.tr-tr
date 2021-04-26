---
title: PowerShell-Azure ile RDP özelliklerini özelleştirme
description: PowerShell cmdlet 'leriyle Windows sanal masaüstü için RDP özelliklerini özelleştirme.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ff3661a7e092fd20207fe2e973afc316b2c244ef
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447106"
---
# <a name="customize-remote-desktop-protocol-rdp-properties-for-a-host-pool"></a>Bir konak havuzu için Uzak Masaüstü Protokolü (RDP) özelliklerini özelleştirme

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/customize-rdp-properties-2019.md)bakın.

Çoklu izleyici deneyimi ve ses yeniden yönlendirme gibi bir konak havuzunun Uzak Masaüstü Protokolü (RDP) özelliklerini özelleştirmek, kullanıcılarınız için gereksinimlerinize göre en iyi deneyimi sunmanızı sağlar. Windows sanal masaüstündeki RDP özelliklerini, Azure portal kullanarak veya **Update-AzWvdHostPool** cmdlet 'inde *-customrdpproperty* parametresini kullanarak özelleştirebilirsiniz.

Desteklenen özelliklerin tam listesi ve bunların varsayılan değerleri için bkz. [desteklenen RDP dosyası ayarları](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext) .

## <a name="default-rdp-file-properties"></a>Varsayılan RDP dosyası özellikleri

RDP dosyaları varsayılan olarak aşağıdaki özelliklere sahiptir:

|RDP özelliği|Masaüstünde|RemoteApp olarak|
|---|---|---|
|Çoklu monitör modu|Devre dışı|Etkin|
|Sürücü yeniden yönlendirmeleri etkin|Sürücüler, pano, yazıcılar, COM bağlantı noktaları ve akıllı kartlar|Sürücüler, pano ve Yazıcılar|
|Uzak ses modu|Yerel olarak oynat|Yerel olarak oynat|

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, PowerShell modülünüzü ayarlamak ve Azure 'da oturum açmak için [Windows sanal masaüstü PowerShell modülünü ayarlama](powershell-module.md) bölümündeki yönergeleri izleyin.

## <a name="configure-rdp-properties-in-the-azure-portal"></a>Azure portal RDP özelliklerini yapılandırın

Azure portal RDP özelliklerini yapılandırmak için:

1. Adresinden Azure 'da oturum açın <https://portal.azure.com> .
2. Arama çubuğuna **Windows sanal masaüstü 'nü** girin.
3. Hizmetler altında **Windows sanal masaüstü**' nü seçin.
4. Windows sanal masaüstü sayfasında, ekranın sol tarafındaki menüden **konak havuzları** ' nı seçin.
5. Güncelleştirmek istediğiniz **konak havuzunun adını** seçin.
6. Ekranın sol tarafındaki menüde **RDP özellikleri** ' ni seçin.
7. İstediğiniz özelliği ayarlayın.
   - Alternatif olarak, **Gelişmiş** sekmesini AÇABILIR ve RDP özelliklerinizi aşağıdaki bölümlerdeki PowerShell örnekleri gibi noktalı virgülle ayrılmış bir biçimde ekleyebilirsiniz.
8. İşiniz bittiğinde, değişikliklerinizi kaydetmek için **Kaydet** ' i seçin.

Sonraki bölümlerde, PowerShell 'de özel RDP özelliklerinin el ile nasıl düzenleneceği açıklanır.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Tek bir özel RDP özelliği ekleme veya düzenleme

Tek bir özel RDP özelliği eklemek veya düzenlemek için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty <property>
```

Az önce çalıştırdığınız cmdlet 'in özelliği güncelleştirdiğinden emin olmak için şu cmdlet 'i çalıştırın:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Örneğin, 0301HP adlı bir konak havuzunda "audiocapturemode" özelliğini kontrol ediyorsanız şu cmdlet 'i girersiniz:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;
```

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Birden çok özel RDP özelliği ekleme veya düzenleme

Birden çok özel RDP özelliği eklemek veya düzenlemek için, Özel RDP özelliklerini noktalı virgülle ayrılmış bir dize olarak sağlayarak aşağıdaki PowerShell cmdlet 'lerini çalıştırın:

```powershell
$properties="<property1>;<property2>;<property3>"
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty $properties
```

Aşağıdaki cmdlet 'i çalıştırarak RDP özelliğinin eklendiğinden emin olmak için kontrol edebilirsiniz:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <customRDPpropertystring>
```

Önceki cmdlet örnekimize göre 0301HP ana bilgisayar havuzunda birden çok RDP özelliği ayarlarsanız, cmdlet 'niz şöyle görünür:

```powershell
Get-AzWvdHostPool -ResourceGroupName 0301rg -Name 0301hp | format-list Name, CustomRdpProperty

Name              : 0301HP
CustomRdpProperty : audiocapturemode:i:1;audiomode:i:0;
```

## <a name="reset-all-custom-rdp-properties"></a>Tüm özel RDP özelliklerini Sıfırla

[Tek bir özel RDP özelliği ekleme veya düzenleme](#add-or-edit-a-single-custom-rdp-property)bölümündeki yönergeleri IZLEYEREK özel RDP özelliklerini varsayılan değerlerine sıfırlayabilirsiniz veya aşağıdaki PowerShell cmdlet 'ini çalıştırarak bir konak havuzu için tüm özel RDP özelliklerini sıfırlayabilirsiniz:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -CustomRdpProperty ""
```

Ayarı başarıyla kaldırmış olduğunuzdan emin olmak için şu cmdlet 'i girin:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, CustomRdpProperty

Name              : <hostpoolname>
CustomRdpProperty : <CustomRDPpropertystring>
```

## <a name="next-steps"></a>Sonraki adımlar

Artık belirli bir konak havuzu için RDP özelliklerini özelleştirdiğinize göre, bir kullanıcı oturumunun parçası olarak test etmek için bir Windows sanal masaüstü istemcisinde oturum açabilirsiniz. Bu sonraki nasıl yapılır kılavuzlarında, tercih ettiğiniz istemciyi kullanarak bir oturuma nasıl bağlanacağınızı öğreneceksiniz:

- [Windows Masaüstü istemcisine bağlanma](connect-windows-7-10.md)
- [Web istemcisiyle bağlanma](connect-web.md)
- [Android istemcisiyle bağlanma](connect-android.md)
- [macOS istemcisiyle bağlanma](connect-macos.md)
- [iOS istemcisiyle bağlanma](connect-ios.md)