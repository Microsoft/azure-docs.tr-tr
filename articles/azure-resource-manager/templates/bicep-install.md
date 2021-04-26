---
title: Bıcep geliştirme ve dağıtım ortamlarını ayarlama
description: Bıcep geliştirme ve dağıtım ortamlarını yapılandırma
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2c905a3885fcfc5c9eb9d9db4004126882798611
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313564"
---
# <a name="install-bicep-preview"></a>Bıcep 'yi (Önizleme) yükler

Bıcep geliştirme ve dağıtım ortamlarını ayarlamayı öğrenin.

## <a name="development-environment"></a>Geliştirme ortamı

En iyi bicep yazma deneyimini almak için iki bileşene ihtiyacınız vardır:

- **Visual Studio Code Için Bıcep uzantısı**. Bicep dosyaları oluşturmak için, iyi bir Bıcep Düzenleyicisi gerekir. [Bıcep uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)ile [Visual Studio Code](https://code.visualstudio.com/) önerilir. Bu araçlar, dil desteği ve kaynak otomatik tamamlama sağlar. Bicep dosyalarını oluşturma ve doğrulamaya yardımcı olurlar. Visual Studio Code ve Bıcep uzantısını kullanma hakkında daha fazla bilgi için bkz. [hızlı başlangıç: Visual Studio Code Ile bıcep dosyaları oluşturma](./quickstart-create-bicep-use-visual-studio-code.md).
- **Bıcep CLI**. Bıcep CLı kullanarak bicep dosyalarını ARM JSON şablonlarına derleyin ve ARM JSON şablonlarını Bıcep dosyalarına derlemeyi bozun. Yükleme yönergeleri için bkz. [Bıcep CLI yükleme](#install-manually).

## <a name="deployment-environment"></a>Dağıtım ortamı

Yerel bicep dosyalarını dağıtmak için iki bileşene ihtiyacınız vardır:

- **Azure CLI sürüm 2.20.0 veya üzeri veya Azure PowerShell sürüm 5.6.0 veya üzeri**. Yükleme yönergeleri için bkz.:

  - [Azure PowerShell’i yükleme](/powershell/azure/install-az-ps)
  - [Windows'da Azure CLI'yi yükleme](/cli/azure/install-azure-cli-windows)
  - [Linux 'ta Azure CLı 'yı yükler](/cli/azure/install-azure-cli-linux)
  - [macOS’ta Azure CLI'yi yükleme](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > Şu anda hem Azure CLı hem de Azure PowerShell yalnızca yerel bicep dosyalarını dağıtabilir. Azure CLı kullanarak Bıcep dosyalarını dağıtma hakkında daha fazla bilgi için bkz. [Deploy-CLI](./deploy-cli.md#deploy-remote-template). Azure PowerShell kullanarak Bıcep dosyalarını dağıtma hakkında daha fazla bilgi için bkz. [Deploy-PowerShell]( ./deploy-powershell.md#deploy-remote-template).

- **Bıcep CLI**. Bıcep CLı, dağıtımdan önce Bıcep dosyalarını JSON şablonlarına derlemek için gereklidir. Yükleme yönergeleri için bkz. [Bıcep CLI yükleme](#install-bicep-cli).

Bileşenler yüklendikten sonra, ile bir Bıcep dosyası dağıtabilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Bıcep CLı 'yı yükler

- Bıcep dosyalarını derlemek ve derlemek için bicep CLı 'yi kullanmak için bkz. [El Ile Install](#install-manually).
- Bıcep dosyalarını dağıtmak için Azure CLı 'yi kullanmak için bkz. [Azure CLI Ile kullanma](#use-with-azure-cli).
- Bicep dosyalarını dağıtmak üzere Azure PowerShell kullanmak için, bkz. [Azure PowerShell Ile kullanma](#use-with-azure-powershell).

### <a name="use-with-azure-cli"></a>Azure CLı ile kullanma

Azure CLı sürüm 2.20.0 veya üzeri yüklü olduğunda, kendisine bağımlı bir komut yürütüldüğünde Bıcep CLı otomatik olarak yüklenir. Örnek:

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

veya

```azurecli
az bicep ...
```

Ayrıca, yerleşik komutları kullanarak CLı 'yı el ile yükleyebilirsiniz:

```bash
az bicep install
```

En son sürüme yükseltmek için:

```bash
az bicep upgrade
```

Belirli bir sürümü yüklemek için:

```bash
az bicep install --version v0.3.255
```

> [!IMPORTANT]
> Azure CLı, Bıcep CLı 'nın sahip olduğunuz herhangi bir bicep yüklemesi ile çakışmadan ayrı bir sürümünü yüklüyor ve Azure CLı, yolunuza Bıcep CLı eklemez. Bıcep dosyalarını derlemek/derlemek veya Bıcep dosyalarını dağıtmak üzere Azure PowerShell kullanmak için bicep CLı 'yi kullanmak için, bkz. [el ile](#install-manually) veya [Azure PowerShell ile kullanma](#use-with-azure-powershell).

Bıcep CLı 'nın tüm kullanılabilir sürümlerini listelemek için:

```bash
az bicep list-versions
```

Yüklü sürümleri göstermek için:

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Azure PowerShell ile kullanma

Azure PowerShell, Bıcep CLı 'yı henüz yükleyeme yeteneğine sahip değil. Azure PowerShell (v 5.6.0 veya üzeri), Bıcep CLı 'nın yolunda zaten yüklü ve kullanılabilir olmasını bekler. [El ile yüklenen yöntemlerden](#install-manually)birini izleyin.

Bıcep dosyalarını dağıtmak için Bıcep CLı sürüm 0.3.1 veya üzeri gereklidir. Bicep CLı sürümünü denetlemek için:

```cmd
bicep --version
```

> [!IMPORTANT]
> Azure CLı, Bıcep CLı 'nın kendi kendine içerilen bir sürümünü yüklüyor. Azure CLı için gerekli sürümler yüklü olsa bile Azure PowerShell dağıtım başarısız olur.

Bıcep CLı yüklendikten sonra, bir dağıtım cmdlet 'i için gerektiğinde Bıcep CLı çağırılır. Örnek:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>El ile yükleme

Aşağıdaki yöntemler Bıcep CLı 'yı yükler ve yolunuza ekler.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>Homebrew aracılığıyla

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

##### <a name="macos-manual-install"></a>macOS el ile yüklemesi

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

[En son Windows Installer](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)sürümünü indirip çalıştırın. Yükleyici, yönetici ayrıcalıkları gerektirmez. Yüklemeden sonra, Bıcep CLı Kullanıcı yolunuza eklenir. YOL değişikliğinin etkili olması için tüm açık komut kabuğu pencerelerini kapatın ve yeniden açın.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>PowerShell ile el ile

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Gecelik derlemelerini yükler

Kullanıma sunulmadan önce Bıcep 'nin en son yayın öncesi bitlerini denemek istiyorsanız, bkz. [gecelik derlemelerini Install](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> Bu yayın öncesi derlemeler bilinen veya bilinmeyen hatalara sahip olma olasılığını çok daha yüksektir.

## <a name="next-steps"></a>Sonraki adımlar

[Bıcep hızlı](./quickstart-create-bicep-use-visual-studio-code.md)başlangıcı ile çalışmaya başlayın.
