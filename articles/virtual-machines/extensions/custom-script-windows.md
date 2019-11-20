---
title: Windows için Azure Özel Betik uzantısı
description: Özel Betik uzantısını kullanarak Windows VM yapılandırma görevlerini otomatikleştirme
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: b3c355219fcbebc5fda38c33d6eb7f9126b3b2b8
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073819"
---
# <a name="custom-script-extension-for-windows"></a>Windows için özel Betik uzantısı

Özel Betik uzantısı, Azure sanal makinelerinde betikleri indirir ve yürütür. Bu uzantı, dağıtım sonrası yapılandırma, yazılım yükleme veya başka herhangi bir yapılandırma ya da yönetim görevi için yararlıdır. Betikler Azure depolama veya GitHub konumlarından indirilebilir ya da Azure portalına uzantı çalışma zamanında iletilebilir. Özel Betik uzantısı Azure Resource Manager şablonlarıyla tümleştirilir ve Azure CLı, PowerShell, Azure portal veya Azure sanal makine REST API kullanılarak çalıştırılabilir.

Bu belgede, Windows sistemlerinde Azure PowerShell modülü, Azure Resource Manager şablonları ve Ayrıntılar sorunlarını giderme adımları kullanılarak özel betik uzantısının nasıl kullanılacağı ayrıntılı olarak açıklanır.

## <a name="prerequisites"></a>Önkoşullar

> [!NOTE]  
> Kendi kendine beklemesi gerektiğinden, Update-AzVM öğesini parametresiyle aynı VM ile çalıştırmak için özel Betik uzantısı kullanmayın.  

### <a name="operating-system"></a>İşletim Sistemi

Windows için özel Betik uzantısı, uzantı tarafından desteklenen dosya uzantısının üzerinde çalışacak. daha fazla bilgi için, bu [Azure uzantısının desteklenen işletim sistemlerine](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)bakın.

### <a name="script-location"></a>Betik konumu

Uzantıyı Azure Blob depolamaya erişmek için Azure Blob depolama kimlik bilgilerinizi kullanacak şekilde yapılandırabilirsiniz. Betik konumu, sanal makinenin GitHub veya dahili bir dosya sunucusu gibi bir uç noktaya yol göstermesi koşuluyla her yerde olabilir.

### <a name="internet-connectivity"></a>Internet bağlantısı

GitHub veya Azure Storage gibi dışarıdan bir betiği indirmeniz gerekiyorsa, ek güvenlik duvarı ve ağ güvenlik grubu bağlantı noktalarının açılması gerekir. Örneğin, betiğinizin Azure Storage 'da bulunması halinde [depolama](../../virtual-network/security-overview.md#service-tags)için Azure NSG hizmet etiketlerini kullanarak erişime izin verebilirsiniz.

Betiğinizin yerel bir sunucu üzerinde olması, ek güvenlik duvarı ve ağ güvenlik grubu bağlantı noktalarının açık olması gerekebilir.

### <a name="tips-and-tricks"></a>İpuçları ve Püf Noktaları

* Bu uzantı için en yüksek hata oranı betikteki sözdizimi hataları nedeniyle, komut dosyasını hatasız olarak test edin ve ayrıca, başarısız olduğunda bulmayı kolaylaştırmak için betiğe ek günlüğe kaydetme işlemi gerçekleştirin.
* Idempotent olan betikler yazın. Bu, yanlışlıkla yeniden çalıştıklarında sistem değişikliklerine neden olmaz.
* Betiklerin çalıştırma sırasında kullanıcı girişi gerektirmediğinden emin olun.
* Betiğin çalışması için 90 dakikalık bir süre ayrılmıştır. Betiklerin daha uzun sürmesi durumunda uzantı sağlama işlemi başarısız olur.
* Betik içine yeniden başlatma eylemi eklemeyin. Aksi halde yüklenmekte olan diğer uzantılarla ilgili sorun yaşanabilir. Yeniden başlatma gerçekleştirildiğinde önyükleme sonrasında uzantı, kaldığı yerden devam etmeyecektir.
* Yeniden başlatmaya neden olacak bir betiğe sahipseniz, uygulamaları yükleyip komut dosyalarını çalıştırırsanız, yeniden başlatmayı Windows zamanlanmış görevi kullanarak zamanlayabilir ya da DSC, Chef veya Pupevcil hayvan uzantıları gibi araçları kullanabilirsiniz.
* Uzantı, betiği yalnızca bir kez çalıştıracaktır. Betiğin her önyükleme sırasında çalıştırılmasını istiyorsanız uzantıyı kullanarak bir Windows Zamanlanmış Görevi oluşturmanız gerekir.
* Betiğin çalıştırılacağı zamanı belirlemek istiyorsanız uzantıyı kullanarak bir Windows Zamanlanmış Görevi oluşturmanız gerekir.
* Betik çalışırken Azure portalı veya CLI üzerinden uzantı durumunu yalnızca "geçiş durumunda" şeklinde görürsünüz. Çalışan bir betikle ilgili daha sık durum güncelleştirmesi almak isterseniz kendi çözümünüzü oluşturmanız gerekir.
* Özel Betik uzantısı, proxy sunucularını yerel olarak desteklemez, ancak, komut dosyanız içinde, *kıvrımlı* gibi ara sunucuları destekleyen bir dosya aktarım aracı kullanabilirsiniz.
* Betiğinizin veya komutlarınızın kullandığı varsayılan olmayan dizin konumlarına dikkat edin ve bu durumu işlemek için bir mantık oluşturun.
* Özel Betik uzantısı LocalSystem hesabı altında çalışır

## <a name="extension-schema"></a>Uzantı şeması

Özel Betik uzantısı yapılandırması, betik konumu ve çalıştırılacak komut gibi şeyleri belirtir. Bu yapılandırmayı yapılandırma dosyalarında saklayabilir, komut satırında belirtebilir veya bir Azure Resource Manager şablonunda belirtebilirsiniz.

Gizli verileri, şifrelenen ve yalnızca sanal makine içinde şifresi çözülen korunan bir yapılandırmada saklayabilirsiniz. Korunan yapılandırma, yürütme komutu parola gibi gizli dizileri içerdiğinde yararlıdır.

Bu öğeler gizli veriler olarak değerlendirilmeli ve uzantılar korumalı ayar yapılandırmasında belirtilmelidir. Azure VM uzantısının korumalı ayarı veriler şifrelenir ve yalnızca hedef sanal makinede şifresi.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Bir sanal makinede bir uzantının yalnızca bir sürümü yüklü olabilir. aynı VM için aynı Kaynak Yöneticisi şablonunda iki kez özel betik belirtilmesi başarısız olur.

> [!NOTE]
> Bu şemayı VirtualMachine kaynağı içinde ya da tek başına bir kaynak olarak kullanabiliriz. Bu uzantı ARM şablonunda tek başına bir kaynak olarak kullanılıyorsa, kaynağın adının bu biçimde "virtualMachineName/extensionName" biçiminde olması gerekir. 

### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| Dosya URI 'leri (ör.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (örn.) | 123456789 | 32 bit tamsayı |
| commandToExecute (ör.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (ör.) | örnek storageacct | string |
| storageAccountKey (ör.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Bu özellik adları büyük/küçük harfe duyarlıdır. Dağıtım sorunlarından kaçınmak için, adları burada gösterildiği gibi kullanın.

#### <a name="property-value-details"></a>Özellik değeri ayrıntıları

* `commandToExecute`: (**gerekli**, dize) yürütülecek giriş noktası betiği. Komutunuz parolalar gibi gizli dizileri içeriyorsa veya dosya URI 'larınız hassas ise bu alanı kullanın.
* `fileUris`: (isteğe bağlı, dize dizisi) indirilecek dosya (ler) in URL 'Leri.
* `timestamp` (isteğe bağlı, 32-bit tamsayı) bu alanı yalnızca bu alanın değerini değiştirerek betiğin yeniden çalıştırılması tetiklenecek şekilde kullanın.  Herhangi bir tamsayı değeri kabul edilebilir; yalnızca önceki değerden farklı olmalıdır.
* `storageAccountName`: (isteğe bağlı, dize) depolama hesabının adı. Depolama kimlik bilgilerini belirtirseniz, tüm `fileUris` Azure Blob 'larının URL 'Si olması gerekir.
* `storageAccountKey`: (isteğe bağlı, dize) depolama hesabının erişim anahtarı

Aşağıdaki değerler ortak veya korumalı ayarlarda ayarlanabilir, uzantı, aşağıdaki değerlerin hem genel hem de korumalı ayarlarda ayarlandığı tüm yapılandırmaları reddeder.

* `commandToExecute`

Genel ayarların kullanılması hata ayıklama için yararlı olabilir, ancak korumalı ayarları kullanmanız önerilir.

Ortak ayarlar, betiğin yürütüleceği sanal makineye şifresiz metin olarak gönderilir.  Korumalı ayarlar yalnızca Azure ve VM için bilinen bir anahtar kullanılarak şifrelenir. Ayarlar, gönderilen VM 'ye kaydedilir, yani ayarlar şifrelendiyse VM 'de şifreli olarak kaydedilir. Şifrelenmiş değerlerin şifresini çözmek için kullanılan sertifika VM 'de depolanır ve çalışma zamanında ayarların (gerekliyse) şifresini çözmek için kullanılır.

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir. Önceki bölümde ayrıntılı olan JSON şeması, dağıtım sırasında özel betik uzantısını çalıştırmak için bir Azure Resource Manager şablonunda kullanılabilir. Aşağıdaki örnekler özel betik uzantısının nasıl kullanılacağını göstermektedir:

* [Öğretici: Azure Resource Manager şablonlarıyla sanal makine uzantılarını dağıtma](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Windows ve Azure SQL DB 'de Iki katmanlı uygulama dağıtma](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell dağıtımı

`Set-AzVMCustomScriptExtension` komutu, var olan bir sanal makineye özel Betik uzantısı eklemek için kullanılabilir. Daha fazla bilgi için bkz. [set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Ek örnekler

### <a name="using-multiple-scripts"></a>Birden çok komut dosyası kullanma

Bu örnekte, sunucunuzu oluşturmak için kullanılan üç betiğinin olması gerekir. **Commandtoexecute** ilk betiği çağırır, ardından diğerlerinin nasıl çağrıldığını gösteren seçenekleriniz vardır. Örneğin, doğru hata işleme, günlüğe kaydetme ve durum yönetimiyle birlikte yürütmeyi denetleyen ana bir betiğe sahip olabilirsiniz. Betikler, çalıştırmak için yerel makineye indirilir. Örneğin `1_Add_Tools.ps1`, komut dosyasına `.\2_Add_Features.ps1` ekleyerek `2_Add_Features.ps1` çağıracaksınız ve bu işlemi `$settings`' de tanımladığınız diğer betikler için tekrarlamalısınız.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Yerel bir paylaşımdan betikleri çalıştırma

Bu örnekte, komut dosyası konumunuz için yerel bir SMB sunucusu kullanmak isteyebilirsiniz. Bunu yaptığınızda, **Commandtoexecute**dışında başka bir ayar sağlamanız gerekmez.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>CLı ile özel betiği birden çok kez çalıştırma

Özel Betik uzantısını birden çok kez çalıştırmak istiyorsanız, bu eylemi yalnızca şu koşullarda yapabilirsiniz:

* Uzantı **adı** parametresi, uzantının önceki dağıtımıyla aynı olur.
* Yapılandırmayı güncelleştirin, aksi takdirde komut yeniden yürütülmez. Komutuna bir zaman damgası gibi dinamik bir özelliği ekleyebilirsiniz.

Alternatif olarak, [Forceupdatetag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) özelliğini de **true**olarak ayarlayabilirsiniz.

### <a name="using-invoke-webrequest"></a>Invoke-WebRequest kullanma

Betiğinizdeki [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) kullanıyorsanız, `-UseBasicParsing` parametresini belirtmeniz gerekir. Aksi takdirde, ayrıntılı durumu denetlerken şu hatayı alırsınız:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>Klasik VM'ler

Özel Betik uzantısını klasik VM 'lerde dağıtmak için Azure portal veya klasik Azure PowerShell cmdlet 'lerini kullanabilirsiniz.

### <a name="azure-portal"></a>Azure portalında

Klasik VM kaynağına gidin. **Ayarlar**altındaki **Uzantılar** ' ı seçin.

**+ Ekle** ' ye tıklayın ve kaynak listesinde **Özel Betik uzantısı**' nı seçin.

**Uzantıyı yükler** sayfasında, yerel PowerShell dosyasını seçin ve tüm bağımsız değişkenleri doldurun ve **Tamam**' a tıklayın.

### <a name="powershell"></a>PowerShell

Özel Betik uzantısını var olan bir sanal makineye eklemek için [set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) cmdlet 'ini kullanın.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal alabilir ve Azure PowerShell modülü kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için aşağıdaki komutu çalıştırın:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Uzantı çıkışı, hedef sanal makinede bulunan aşağıdaki klasör altında bulunan dosyalara kaydedilir.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Belirtilen dosyalar hedef sanal makinede aşağıdaki klasöre indirilir.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

Burada `<n>`, uzantının yürütmeleri arasında değişebilir bir ondalık tamsayıdır.  `1.*` değeri, uzantının gerçek, geçerli `typeHandlerVersion` değeriyle eşleşir.  Örneğin, gerçek dizin `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`olabilir.  

`commandToExecute` komutu yürütürken, uzantı bu dizini (örneğin, `...\Downloads\2`) geçerli çalışma dizini olarak ayarlar. Bu işlem, `fileURIs` özelliği aracılığıyla indirilen dosyaları bulmak için göreli yolların kullanılmasını sağlar. Örnekler için aşağıdaki tabloya bakın.

Mutlak indirme yolu zaman içinde değişebileceğinden, mümkün olduğunda `commandToExecute` dizesinde göreli betik/dosya yollarını kabul etmek daha iyidir. Örneğin:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

İlk URI segmentinden sonraki yol bilgileri, `fileUris` özellik listesi aracılığıyla indirilen dosyalar için tutulur.  Aşağıdaki tabloda gösterildiği gibi, indirilen dosyalar `fileUris` değerlerinin yapısını yansıtmak için indirme alt dizinlerinde eşleştirilir.  

#### <a name="examples-of-downloaded-files"></a>Indirilen dosya örnekleri

| FileUris 'de URI | Göreli indirilen konum | Mutlak indirilen konum <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> mutlak DIZIN yolları VM 'nin ömrü boyunca değişir, ancak CustomScript uzantısının tek bir yürütmesi içinde değildir.

### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Ayrıca bir Azure destek olayı da oluşturabilirsiniz. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).
