---
title: Windows için Azure özel betik uzantısı | Microsoft Docs
description: Özel betik uzantısı'nı kullanarak Windows VM yapılandırma görevlerini otomatikleştirme
services: virtual-machines-windows
manager: carmonm
author: georgewallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: gwallace
ms.openlocfilehash: b71ba69bcf4965ea607e097c392573e77aab6865
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65408284"
---
# <a name="custom-script-extension-for-windows"></a>Windows için özel betik uzantısı

Özel betik uzantısı indirir ve Azure sanal makinelerinde betikleri çalıştırır. Bu uzantı dağıtım sonrası yapılandırma, yazılım yükleme veya tüm diğer yapılandırma veya yönetim görevleri için kullanışlıdır. Betikler Azure depolama veya GitHub konumlarından indirilebilir ya da Azure portalına uzantı çalışma zamanında iletilebilir. Özel betik uzantısı, Azure Resource Manager şablonları ile tümleşir ve Azure CLI, PowerShell, Azure portal veya Azure sanal makine REST API kullanılarak çalıştırılabilir.

Bu belge, Azure PowerShell modülü, Azure Resource Manager şablonları ve sorun giderme adımları Windows sistemlerinde ayrıntıları kullanarak özel betik uzantısı kullanma işlemi açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

> [!NOTE]  
> Özel betik uzantısı, bu yana kendisine bekler, parametre olarak aynı VM ile Update-AzVM çalıştırmak için kullanmayın.  

### <a name="operating-system"></a>İşletim sistemi

Windows için özel betik uzantısı daha fazla bilgi için desteklenen uzantısı uzantısında OSs çalıştırın, bkz. Bu [Azure uzantısı desteklenen işletim sistemleri](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Betik konumu

Azure Blob depolama alanına erişmek için Azure Blob Depolama kimlik bilgilerini kullanmayı uzantısını yapılandırabilirsiniz. VM, GitHub veya bir iç dosya sunucusu gibi bu uç noktasına yönlendirebilir sürece her yerden, betik konumu olabilir.

### <a name="internet-connectivity"></a>Internet bağlantısı

GitHub veya Azure depolama gibi bir komut dosyası dışarıdan yüklemek gereken ek bir güvenlik duvarı ve ağ güvenlik grubu bağlantı noktaları açılması gerekir. Örneğin, betiğinizi Azure Depolama'da yer alıyorsa, Azure NSG hizmet etiketleri kullanarak erişime izin verebilir [depolama](../../virtual-network/security-overview.md#service-tags).

Kodunuzu yerel bir sunucudaysa, ek güvenlik duvarı sonra yine de gerekebilir ve ağ güvenlik grubu bağlantı noktalarının açılması gerekir.

### <a name="tips-and-tricks"></a>İpuçları ve Püf Noktaları

* Bu uzantı için en yüksek hata oranı hata, komut dosyasını çalıştırır test komut dosyasında sözdizimi hataları nedeniyle ve başarısız olduğu bulmak daha kolay hale getirmek için komut dosyası bir günlük daha koyun de.
* Eşgüçlüdür betikleri yazın. Bu, bunlar yanlışlıkla yeniden çalıştırırsanız, sistem değişiklikleri neden olmayacağından sağlar.
* Betiklerin çalıştırma sırasında kullanıcı girişi gerektirmediğinden emin olun.
* Betiğin çalışması için 90 dakikalık bir süre ayrılmıştır. Betiklerin daha uzun sürmesi durumunda uzantı sağlama işlemi başarısız olur.
* Betik içine yeniden başlatma eylemi eklemeyin. Aksi halde yüklenmekte olan diğer uzantılarla ilgili sorun yaşanabilir. Yeniden başlatma gerçekleştirildiğinde önyükleme sonrasında uzantı, kaldığı yerden devam etmeyecektir.
* Yeniden başlatma neden, sonra uygulamalar yükleyebilir ve betikleri çalıştırma bir betiğiniz varsa, Windows zamanlanmış bir görev kullanarak yeniden zamanlamak veya DSC, Chef veya Puppet uzantıları gibi araçları kullanın.
* Uzantı, betiği yalnızca bir kez çalıştıracaktır. Betiğin her önyükleme sırasında çalıştırılmasını istiyorsanız uzantıyı kullanarak bir Windows Zamanlanmış Görevi oluşturmanız gerekir.
* Betiğin çalıştırılacağı zamanı belirlemek istiyorsanız uzantıyı kullanarak bir Windows Zamanlanmış Görevi oluşturmanız gerekir.
* Betik çalışırken Azure portalı veya CLI üzerinden uzantı durumunu yalnızca "geçiş durumunda" şeklinde görürsünüz. Çalışan bir betikle ilgili daha sık durum güncelleştirmesi almak isterseniz kendi çözümünüzü oluşturmanız gerekir.
* Özel betik uzantısı yerel olarak proxy sunucularını desteklemez, ancak komut dosyanızın içinde proxy sunucuları gibi destekleyen bir dosya aktarım aracı kullanabilir *Curl*
* Betiğinizin veya komutlarınızın kullandığı varsayılan olmayan dizin konumlarına dikkat edin ve bu durumu işlemek için bir mantık oluşturun.

## <a name="extension-schema"></a>Uzantı şeması

Betik konumu ve çalıştırılacak komutu gibi özel betik uzantısı yapılandırmasını belirtir. Bu yapılandırma, yapılandırma dosyalarında depolayın, komut satırında belirtin veya bir Azure Resource Manager şablonu belirtin.

Hassas veriler şifrelenir ve bunların şifresini yalnızca sanal makinenin içinde bir korumalı bir yapılandırma depolayabilirsiniz. Korumalı yapılandırma yürütme komutu bir parola eşdeğerindeki içerdiğinde yararlıdır.

Bu öğeler hassas verisi olarak kabul edilir ve uzantıları korumalı ayarı yapılandırmasında belirtilen. Azure VM uzantısının korumalı ayarı veriler şifrelenir ve yalnızca hedef sanal makinede şifresi.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
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
> Uzantı yalnızca bir sürümü bir noktada bir VM'de zamanında, iki kez aynı sanal makine için aynı Resource Manager şablonu, özel bir komut dosyası başarısız olur belirtme yüklenebilir.

### <a name="property-values"></a>Özellik değerleri

| Ad | Değer / örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Compute | string |
| type | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (örn.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | array |
| timestamp (örn.) | 123456789 | 32 bit tamsayı |
| commandToExecute (örn.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | string |
| storageAccountName (örn.) | examplestorageacct | string |
| storageAccountKey (örn.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Bu özellik adları büyük/küçük harfe duyarlıdır. Dağıtım sorunları önlemek için burada gösterildiği gibi adları kullanın.

#### <a name="property-value-details"></a>Özellik değeri ayrıntıları

* `commandToExecute`: (**gerekli**, string) yürütmek için giriş noktası betiği. Bu alan, bunun yerine komutunuz parolalar gibi gizli dizileri içeren ya da kendi fileUris büyük/küçük harfe duyarlıdır kullanın.
* `fileUris`: (isteğe bağlı, dize dizisi) dosyaların indirilmesi için URL.
* `timestamp` Bu alan yalnızca bu alanın değerini değiştirerek bir yeniden betiğin tetiklemek için (isteğe bağlı, 32 bit tamsayı) kullanın.  Kabul edilebilir tamsayı değerdeki; yalnızca önceki değerinden farklı olmalıdır.
* `storageAccountName`: (isteğe bağlı, dize) depolama hesabı adı. Depolama kimlik bilgileri, belirtirseniz, tüm `fileUris` URL'leri, Azure BLOB'ları için olmalıdır.
* `storageAccountKey`: (isteğe bağlı, dize) depolama hesabı erişim anahtarı

Ortak veya korumalı ayarlarında aşağıdaki değerleri ayarlayabilirsiniz Bu, uzantı hem genel hem de korumalı ayarlarında aşağıdaki değerleri ayarlandığı herhangi bir yapılandırma reddeder.

* `commandToExecute`

Hata ayıklama, ancak için genel ayarları yararlı olabilir kullanarak, korumalı ayarları kullanmanız önerilir.

Genel ayarlar, betik yürütüldüğü VM düz metin olarak gönderilir.  Korumalı ayarları, yalnızca Azure ve VM bildiği bir anahtar kullanılarak şifrelenir. Gönderildikleri olarak ayarlar VM kaydedilir, ayarları şifrelediyseniz diğer bir deyişle, bunlar şifrelenmiş VM'de kaydedilirler. Şifrelenmiş değerler şifresini çözmek için kullanılan sertifika, VM üzerinde depolanan ve çalışma zamanında ayarlarını (gerekiyorsa) şifresini çözmek için kullanılan.

## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir. Önceki bölümde ayrıntılı JSON şema dağıtımı sırasında özel betik uzantısı'nı çalıştırmak için bir Azure Resource Manager şablonu kullanılabilir. Aşağıdaki örnekler özel betik uzantısı kullanma işlemini gösterir:

* [Öğretici: Sanal makine uzantıları Azure Resource Manager şablonları ile dağıtma](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Windows ve Azure SQL DB iki katmanlı uygulama dağıtma](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell dağıtım

`Set-AzVMCustomScriptExtension` Komutu, mevcut bir sanal makine için özel betik uzantısı eklemek için kullanılabilir. Daha fazla bilgi için [kümesi AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Ek örnekler

### <a name="using-multiple-scripts"></a>Birden çok komut dosyalarını kullanma

Bu örnekte, sunucunuzu oluşturmak için kullanılan üç betik vardır. **CommandToExecute** adlı nasıl diğer seçenekleri sahip ilk komut dosyasını çağırır. Örneğin, doğru hata işleme, günlüğe kaydetme ve durum yönetimi ile yürütme denetimleri ana bir komut dosyası olabilir. Komut dosyalarını çalıştırmak için yerel makinenize indirilir. Örneğin `1_Add_Tools.ps1` çağrı yapıyordu `2_Add_Features.ps1` ekleyerek `.\2_Add_Features.ps1` betik ve yineleme için diğer komut dosyaları için bu işlem, tanımladığınız `$settings`.

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

### <a name="running-scripts-from-a-local-share"></a>Betikleri yerel bir paylaşımından çalıştırma

Bu örnekte, komut dosyası konumunuz için yerel bir SMB sunucusu kullanmak isteyebilirsiniz. Bunu yaparak, dışındaki tüm diğer ayarlar girmeniz gerekmez **commandToExecute**.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Özel betik CLI ile birden çok kez çalıştırmayı öğrenin

Özel betik uzantısı birden çok kez çalıştırmak istiyorsanız, bu koşullar altında bu eylem yalnızca yapabilirsiniz:

* Uzantı **adı** parametresi, önceki dağıtım uzantısı ile aynı.
* Komut yeniden yürütülmesi olmaz aksi yapılandırmasını güncelleştirin. Dinamik bir özellik gibi bir zaman damgası komutu içine ekleyebilirsiniz.

Alternatif olarak, ayarlayabileceğiniz [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) özelliğini **true**.

### <a name="using-invoke-webrequest"></a>Invoke-WebRequest kullanarak

Kullanıyorsanız [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) betiğinizde, parametresini belirtmeniz gerekir `-UseBasicParsing` ya da aksi takdirde ayrıntılı durumu denetlenirken aşağıdaki hatayı alırsınız:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>Klasik VM'ler

Özel betik uzantısı'Klasik sanal makinelerinde dağıtmak için Azure portalında veya Klasik Azure PowerShell cmdlet'lerini kullanabilirsiniz.

### <a name="azure-portal"></a>Azure portal

Klasik sanal makine kaynağınıza gidin. Seçin **uzantıları** altında **ayarları**.

Tıklayın **+ Ekle** ve kaynak listesinden seçin **özel betik uzantısı**.

Üzerinde **uzantı yükleme** sayfasında, yerel PowerShell dosyasını seçin ve herhangi bir bağımsız değişken doldurun ve tıklayın **Tamam**.

### <a name="powershell"></a>PowerShell

Kullanım [kümesi AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) cmdlet'i, mevcut bir sanal makine için özel betik uzantısı eklemek için kullanılabilir.

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

Uzantı dağıtım durumuyla ilgili veriler, Azure portalından ve Azure PowerShell modülü kullanılarak alınabilir. Belirli bir VM'nin için uzantıları dağıtım durumunu görmek için aşağıdaki komutu çalıştırın:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Uzantı çıkış hedef sanal makinede aşağıdaki klasörü altında bulunan dosyaları için günlüğe kaydedilir.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Belirtilen dosyalar, hedef sanal makinede aşağıdaki klasöre yüklenir.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

Burada `<n>` uzantının çalıştırmaları arasında değişebilir bir ondalık tamsayı.  `1.*` Gerçek, geçerli değerle `typeHandlerVersion` uzantısı değeri.  Örneğin, gerçek dizin olabilir `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

Yürütülürken `commandToExecute` komut uzantısı bu dizin ayarlar (örneğin, `...\Downloads\2`) geçerli çalışma dizini. Bu işlem üzerinden indirilen dosyaları bulmak için göreli yolların kullanılması tanır `fileURIs` özelliği. Örnekler için aşağıdaki tabloya bakın.

Zaman içinde mutlak indirme yolunu değişebildiğinden, göreli komut dosyası yolları için iyileştirilmiş en iyisidir `commandToExecute` dize, mümkün olduğunda. Örneğin:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

İlk URI segmenti üzerinden indirilen dosyalar için tutulur sonra yol bilgisi `fileUris` özellik listesi.  Aşağıdaki tabloda gösterildiği gibi indirilen dosyaları indirme alt yapısını yansıtmak için eşlenen `fileUris` değerleri.  

#### <a name="examples-of-downloaded-files"></a>İndirilen Dosya örnekleri

| FileUris URI | İndirilen göreli konum | Mutlak konumu indirilen <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> CustomScript uzantısı, tek bir yürütme içinde değil ancak, VM'nin ömrü boyunca mutlak dizin yolları değiştirin.

### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).
