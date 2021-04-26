---
title: Dağıtım Azure Dosya Eşitleme | Microsoft Docs
description: Baştan sona, Azure portal, PowerShell veya Azure CLı kullanarak Azure Dosya Eşitleme dağıtmayı öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 64b9ce78f05e1c8d14317f33f21758a86baeabd6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869194"
---
# <a name="deploy-azure-file-sync"></a>Azure Dosya Eşitleme’yi dağıtma
Şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirmek için Azure Dosya Eşitleme kullanın. Azure Dosya Eşitleme, Windows Server’ı Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürür. Verilere yerel olarak erişmek için Windows Server üzerinde kullanılabilen tüm protokolleri (SMB, NFS ve FTPS gibi) kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.

Bu makalede açıklanan adımları tamamlamadan önce bir [Azure dosyaları dağıtımı Için planlamayı](../files/storage-files-planning.md) ve [bir Azure dosya eşitleme dağıtımı planlamayı](file-sync-planning.md) kesinlikle öneririz.

## <a name="prerequisites"></a>Önkoşullar

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Azure Dosya Eşitleme dağıtmak istediğiniz bölgedeki bir Azure dosya paylaşımıdır. Daha fazla bilgi için bkz.
    - Azure Dosya Eşitleme için [bölge kullanılabilirliği](file-sync-planning.md#azure-file-sync-region-availability) .
    - Dosya paylaşımının nasıl oluşturulacağı hakkında adım adım bir açıklama için [dosya paylaşma oluşturun](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) .
1. Azure Dosya Eşitleme eşitlenecek Windows Server veya Windows Server kümesinin en az bir desteklenen örneği. Windows Server 'ın desteklenen sürümleri ve önerilen sistem kaynakları hakkında daha fazla bilgi için bkz. [Windows dosya sunucusu konuları](file-sync-planning.md#windows-file-server-considerations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Azure Dosya Eşitleme dağıtmak istediğiniz bölgedeki bir Azure dosya paylaşımıdır. Daha fazla bilgi için bkz.
    - Azure Dosya Eşitleme için [bölge kullanılabilirliği](file-sync-planning.md#azure-file-sync-region-availability) .
    - Dosya paylaşımının nasıl oluşturulacağı hakkında adım adım bir açıklama için [dosya paylaşma oluşturun](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) .
1. Azure Dosya Eşitleme eşitlenecek Windows Server veya Windows Server kümesinin en az bir desteklenen örneği. Windows Server 'ın desteklenen sürümleri ve önerilen sistem kaynakları hakkında daha fazla bilgi için bkz. [Windows dosya sunucusu konuları](file-sync-planning.md#windows-file-server-considerations).

1. Az PowerShell modülü, PowerShell 5,1 ya da PowerShell 6 + ile birlikte kullanılabilir. Windows olmayan sistemler de dahil olmak üzere desteklenen herhangi bir sistemde Azure Dosya Eşitleme için az PowerShell modülünü kullanabilirsiniz, ancak sunucu kayıt cmdlet 'i her zaman kaydolduğunuz Windows Server örneğinde çalıştırılmalıdır (Bu işlem doğrudan veya PowerShell uzaktan iletişim yoluyla yapılabilir). Windows Server 2012 R2 'de, en az PowerShell 5,1 kullandığınızı doğrulayabilirsiniz. \* **$PSVersionTable** nesnesinin **psversion** özelliğinin değerine bakarak:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    **Psversion** değeri 5,1 ' den küçükse \* , Windows Server 2012 R2 'nin en yeni yüklemelerinde olduğu gibi, [WINDOWS Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616)indirip yükleyerek kolayca yükseltebilirsiniz. Windows Server 2012 R2 için indirmek ve yüklemek üzere uygun paket **Win 8.1 andw2k12r2-KB \* \* \* \* \* \* \* -x64. msu**' dir. 

    PowerShell 6 + desteklenen herhangi bir sistemle birlikte kullanılabilir ve [GitHub sayfası](https://github.com/PowerShell/PowerShell#get-powershell)aracılığıyla indirilebilir. 

    > [!Important]  
    > Doğrudan PowerShell 'den kaydolmak yerine sunucu kaydı kullanıcı arabirimini kullanmayı planlıyorsanız, PowerShell 5,1 ' i kullanmanız gerekir.

1. PowerShell 5,1 kullanmayı tercih ettiyseniz, en az .NET 4.7.2 'ın yüklü olduğundan emin olun. Sisteminizde [.NET Framework sürümleri ve bağımlılıklar](/dotnet/framework/migration-guide/versions-and-dependencies) hakkında daha fazla bilgi edinin.

    > [!Important]  
    > Windows Server Core üzerinde .NET 4.7.2 + yüklüyorsanız, ve bayraklarıyla yüklemeniz gerekir, `quiet` `norestart` Aksi takdirde yükleme başarısız olur. Örneğin, .NET 4,8 ' i yüklüyorsanız, komut aşağıdaki gibi görünür:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Buradaki yönergeleri izleyerek yüklenebilen az PowerShell modülü: [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Az. Storagessync modülü artık az PowerShell modülünü yüklediğinizde otomatik olarak yüklenir.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure Dosya Eşitleme dağıtmak istediğiniz bölgedeki bir Azure dosya paylaşımıdır. Daha fazla bilgi için bkz.
    - Azure Dosya Eşitleme için [bölge kullanılabilirliği](file-sync-planning.md#azure-file-sync-region-availability) .
    - Dosya paylaşımının nasıl oluşturulacağı hakkında adım adım bir açıklama için [dosya paylaşma oluşturun](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) .
1. Azure Dosya Eşitleme eşitlenecek Windows Server veya Windows Server kümesinin en az bir desteklenen örneği. Windows Server 'ın desteklenen sürümleri ve önerilen sistem kaynakları hakkında daha fazla bilgi için bkz. [Windows dosya sunucusu konuları](file-sync-planning.md#windows-file-server-considerations).

1. [Azure CLI'yi yükleme](/cli/azure/install-azure-cli)

   İsterseniz, bu öğreticideki adımları tamamlayabilmeniz için Azure Cloud Shell de kullanabilirsiniz.  Azure Cloud Shell, tarayıcınız aracılığıyla kullandığınız etkileşimli bir kabuk ortamıdır.  Aşağıdaki yöntemlerden birini kullanarak Cloud Shell başlatın:

   - Kod bloğunun sağ üst köşesindeki **Deneyin**’i seçin. Azure Cloud Shell **açar,** ancak kodu Cloud Shell otomatik olarak kopyalamaz.

   - Cloud Shell giderek açın [https://shell.azure.com](https://shell.azure.com)

   - [Azure Portal](https://portal.azure.com) sağ üst köşedeki menü çubuğunda bulunan **Cloud Shell** düğmesini seçin

1. Oturum açın.

   CLI’nın yerel yüklemesini kullanıyorsanız [az login](/cli/azure/reference-index#az_login) komutunu kullanarak oturum açın.

   ```azurecli
   az login
   ```

    Terminalinizde görüntülenen adımları uygulayarak kimlik doğrulama işlemini tamamlayın.

1. [Az FileSync](/cli/azure/storagesync) Azure CLI uzantısını yükler.

   ```azurecli
   az extension add --name storagesync
   ```

   **Storagessync** uzantı başvurusunu yükledikten sonra, aşağıdaki uyarıyı alırsınız.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Windows Server’ı Azure Dosya Eşitleme ile kullanmaya hazırlama
Azure Dosya Eşitleme ile kullanmayı düşündüğünüz her sunucu için, bir yük devretme kümesindeki her sunucu düğümü dahil, **Internet Explorer Artırılmış güvenlik yapılandırmasını** devre dışı bırakın. Bu yalnızca ilk sunucu kaydı için gereklidir. Sunucu kaydedildikten sonra özelliği yeniden etkinleştirebilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Windows Server Core üzerinde Azure Dosya Eşitleme dağıtıyorsanız, bu adımı atlayabilirsiniz.

1. Sunucu Yöneticisi'ni açın.
2. **Yerel sunucu**' ya tıklayın:  
    ![Sunucu Yöneticisi kullanıcı arabiriminin sol tarafındaki "Yerel Sunucu"](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-1.png)
3. **Özellikler** alt bölmesinde **IE Artırılmış Güvenlik Yapılandırması** bağlantısını seçin.  
    ![Sunucu Yöneticisi kullanıcı arabirimindeki "IE Artırılmış Güvenlik Yapılandırması" bölmesi](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-2.png)
4. **Internet Explorer Artırılmış Güvenlik Yapılandırması** Iletişim kutusunda **Yöneticiler** ve **Kullanıcılar** için **kapalı** ' yı seçin:  
    !["Kapalı" seçeneğinin işaretli olduğu Internet Explorer Artırılmış Güvenlik Yapılandırması açılır penceresi](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Internet Explorer Artırılmış güvenlik yapılandırmasını devre dışı bırakmak için, yükseltilmiş bir PowerShell oturumundan aşağıdakileri yürütün:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal veya PowerShell için yönergeleri izleyin.

---

## <a name="deploy-the-storage-sync-service"></a>Depolama Eşitleme Hizmetini Dağıtma 
Azure Dosya Eşitleme dağıtımı, **depolama eşitleme hizmeti** kaynağı seçtiğiniz aboneliğin bir kaynak grubuna yerleştirilerek başlar. Gerektiğinde sağlamamız önerilir. Sunucularınız ile bu kaynak arasında bir güven ilişkisi oluşturacaksınız ve bir sunucu yalnızca bir depolama Eşitleme hizmetine kaydedilebilir. Sonuç olarak, sunucu gruplarını ayırmak için gereken sayıda depolama eşitleme hizmeti dağıtmanız önerilir. Farklı depolama eşitleme hizmetlerinden sunucuların birbirleriyle eşitlenemeyeceğini aklınızda bulundurun.

> [!Note]
> Depolama eşitleme hizmeti, dağıtılan abonelik ve kaynak grubundan erişim izinlerini devralır. Kimin erişimi olduğunu dikkatle denetlemeniz önerilir. Yazma erişimi olan varlıklar, bu depolama Eşitleme hizmetine kaydedilen sunuculardan yeni dosya kümelerini eşitlemeye başlayabilir ve verilerin bunlara erişebilen Azure Storage 'a akmasını sağlar.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Bir depolama eşitleme hizmeti dağıtmak için, [Azure Portal](https://portal.azure.com/)gidin, *kaynak oluştur ' a* tıklayın ve ardından Azure dosya eşitleme aratın. Arama sonuçlarında **Azure dosya eşitleme**' yi seçin ve ardından **Oluştur** ' u seçerek **depolama eşitlemesini dağıt** sekmesini açın.

Açılan bölmeye aşağıdaki bilgileri girin:

- **Ad**: depolama eşitleme hizmeti için benzersiz bir ad (bölge başına).
- **Abonelik**: depolama eşitleme hizmetini oluşturmak istediğiniz abonelik. Kuruluşunuzun yapılandırma stratejisine bağlı olarak, bir veya daha fazla aboneliğe erişiminiz olabilir. Azure aboneliği, her bir bulut hizmeti için (Azure dosyaları gibi) Faturalandırma için en temel kapsayıcıdır.
- **Kaynak grubu**: bir kaynak grubu, depolama hesabı veya depolama eşitleme hizmeti gibi bir Azure kaynakları mantıksal grubudur. Azure Dosya Eşitleme için yeni bir kaynak grubu oluşturabilir veya var olan bir kaynak grubunu kullanabilirsiniz. (Belirli bir proje için HR kaynaklarını veya kaynaklarını gruplama gibi kaynak gruplarını kuruluşunuz için mantıksal olarak yalıtmak üzere kapsayıcı olarak kullanmanızı öneririz.)
- **Konum**: Azure dosya eşitleme dağıtmak istediğiniz bölge. Bu listede yalnızca desteklenen bölgeler bulunur.

İşiniz bittiğinde, depolama eşitleme hizmeti 'ni dağıtmak için **Oluştur** ' u seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
, `<Az_Region>` `<RG_Name>` Ve `<my_storage_sync_service>` değerlerini kendi değerlerinizle değiştirin, ardından bir depolama eşitleme hizmeti oluşturmak ve dağıtmak için aşağıdaki komutları kullanın:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal veya PowerShell için yönergeleri izleyin.

---

## <a name="install-the-azure-file-sync-agent"></a>Azure Dosya Eşitleme aracısını yükleme
Azure Dosya Eşitleme aracısı, Windows Server’ın bir Azure dosya paylaşımı ile eşitlenmesini sağlayan indirilebilir bir pakettir. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aracıyı [Microsoft Indirme merkezi](https://go.microsoft.com/fwlink/?linkid=858257)' nden indirebilirsiniz. İndirme tamamlandığında, Azure Dosya Eşitleme Aracısı yüklemesini başlatmak için MSI paketine çift tıklayın.

> [!Important]  
> Azure Dosya Eşitleme bir yük devretme kümesiyle kullanmak istiyorsanız, Azure Dosya Eşitleme aracısının kümedeki her düğüme yüklenmesi gerekir. Azure Dosya Eşitleme çalışmak için kümedeki her düğümün kayıtlı olması gerekir.

Şunları yapmanızı öneririz:
- Sorun gidermeyi ve sunucu bakımını kolaylaştırmak için varsayılan yükleme yolunu (C:\Program Files\Azure\StorageSyncAgent) bırakın.
- Azure Dosya Eşitleme güncel tutmak için Microsoft Update etkinleştirin. Azure Dosya Eşitleme aracısına yönelik, özellik güncelleştirmeleri ve düzeltmeler de dahil olmak üzere tüm güncelleştirmeler Microsoft Update oluşur. Azure Dosya Eşitleme için en son güncelleştirmeyi yüklemenizi öneririz. Daha fazla bilgi için [Azure dosya eşitleme güncelleştirme ilkesi](file-sync-planning.md#azure-file-sync-agent-update-policy)bölümüne bakın.

Azure Dosya Eşitleme Aracısı yüklemesi tamamlandığında, sunucu kaydı kullanıcı arabirimi otomatik olarak açılır. Kaydolmadan önce bir depolama Eşitleme hizmetine sahip olmanız gerekir; Depolama eşitleme hizmeti oluşturma hakkında sonraki bölüme bakın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
IŞLETIM sistemine yönelik Azure Dosya Eşitleme aracısının uygun sürümünü indirmek için aşağıdaki PowerShell kodunu yürütün ve sisteminize yükleyin.

> [!Important]  
> Azure Dosya Eşitleme bir yük devretme kümesiyle kullanmak istiyorsanız, Azure Dosya Eşitleme aracısının kümedeki her düğüme yüklenmesi gerekir. Kümedeki her düğümün Azure Dosya Eşitleme çalışmak için kayıtlı olması gerekir.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal veya PowerShell için yönergeleri izleyin.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server’ı Depolama Eşitleme Hizmetine kaydetme
Windows Server’ı bir Depolama Eşitleme Hizmeti’ne kaydetmek, sunucunuz (veya kümeniz) ile Depolama Eşitleme Hizmeti arasında bir güven ilişkisi kurar. Bir sunucu yalnızca bir Depolama Eşitleme Hizmeti’ne kaydedilebilir ve aynı Depolama Eşitleme Hizmeti ile ilişkili diğer sunucular ve Azure dosya paylaşımları ile eşitlenebilir.

> [!Note]
> Sunucu kaydı, depolama eşitleme hizmeti ile Windows Server arasında bir güven ilişkisi oluşturmak için Azure kimlik bilgilerinizi kullanır, ancak bundan sonra sunucu kayıtlı olduğu ve geçerli paylaşılan erişim Imza belirteci (depolama SAS) geçerli olduğu sürece geçerli olan kendi kimliğini oluşturur ve kullanır. Sunucunun kaydı kaldırıldıktan sonra sunucuya yeni bir SAS belirteci verilemez, böylece sunucunun Azure dosya paylaşımınızla erişme özelliği kaldırılarak eşitleme durdurulur.

Sunucuyu kaydeden yönetici, belirtilen depolama eşitleme hizmeti için yönetim rolleri **sahibinin** veya **katkıda bulunan** bir üyesi olmalıdır. Bu, depolama eşitleme hizmeti için Azure portal **Access Control (IAM)** altında yapılandırılabilir.

Ayrıca, yöneticilerin bir depolama eşitleme hizmetinde eşitleme yapılandırmasına izin verilen sunuculardan sunucu kaydedebilmesini de mümkündür. Bunun için, yalnızca sunucuları kaydettirebilir ve özel rolünüze aşağıdaki izinlerle izin verilen yöneticileri listelüünüzde özel bir rol oluşturmanız gerekir:

* "Microsoft. Storagessync/storageSyncServices/registeredServers/Write"
* "Microsoft. Storagessync/storageSyncServices/Read"
* "Microsoft. Storagessync/storageSyncServices/iş akışları/okuma"
* "Microsoft. Storagessync/storageSyncServices/iş akışları/işlemler/okuma"

# <a name="portal"></a>[Portal](#tab/azure-portal)
Sunucu kayıt kullanıcı arabirimi, Azure Dosya Eşitleme aracısının yüklenmesinden sonra otomatik olarak açılmalıdır. Açılmazsa, şu dosya konumundan kendiniz açabilirsiniz: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Sunucu kaydı kullanıcı arabirimi açıldığında başlamak için **oturum aç** ' ı seçin.

Oturum açtıktan sonra, sizden aşağıdaki bilgiler istenir:

![Sunucu Kaydı kullanıcı arabiriminin ekran görüntüsü](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure aboneliği**: depolama eşitleme hizmetini içeren abonelik (bkz. [depolama eşitleme hizmetini dağıtma](#deploy-the-storage-sync-service)). 
- **Kaynak grubu**: depolama eşitleme hizmeti 'ni içeren kaynak grubu.
- **Depolama eşitleme hizmeti**: kaydetmek Istediğiniz depolama eşitleme hizmeti 'nin adı.

Uygun bilgileri seçtikten sonra, sunucu kaydını gerçekleştirmek için **Kaydet** ' i seçin. Kayıt işleminin bir parçası olarak bir kez daha oturum açmanız istenir.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure portal veya PowerShell için yönergeleri izleyin.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Bir eşitleme grubu ve bir bulut uç noktası oluşturma
Eşitleme grubu, bir dosya kümesi için eşitleme topolojisini tanımlar. Bir eşitleme grubu içindeki uç noktalar, birbiriyle eşitlenmiş durumda tutulur. Eşitleme grubu, bir Azure dosya paylaşımı ve en az bir sunucu uç noktasını temsil eden bir bulut uç noktası içermelidir. Sunucu uç noktası, kayıtlı bir sunucudaki yolu temsil eder. Bir sunucu birden çok eşitleme grubunda sunucu uç noktalarına sahip olabilir. İstediğiniz eşitleme topolojinizi uygun şekilde açıklamanız için ihtiyacınız olan sayıda eşitleme grubu oluşturabilirsiniz.

Bulut uç noktası, Azure dosya paylaşımının bir işaretçisidir. Tüm sunucu uç noktaları bulut uç noktasıyla eşitlenir ve bu da bulut uç noktası hub 'ı yapar. Azure dosya paylaşımının depolama hesabı, depolama eşitleme hizmeti ile aynı bölgede bulunmalıdır. Azure dosya paylaşımının tamamı eşitlenecek, tek bir özel durum: bir NTFS biriminde gizli "sistem birimi bilgileri" klasörüyle karşılaştırılabilen özel bir klasör sağlanacak. Bu dizin "olarak adlandırılır. Systemshareınformation ". Diğer uç noktalarla eşitlenmeyecek önemli eşitleme meta verileri içerir. Kullanma veya silme!

> [!Important]  
> Eşitleme grubundaki herhangi bir bulut uç noktasında veya sunucu uç noktasında değişiklik yapabilir ve dosyalarınızı eşitleme grubundaki diğer uç noktalarla eşitler. Bulut uç noktasına (Azure dosya paylaşımında) doğrudan değişiklik yaparsanız, önce değişikliklerin Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Bir değişiklik algılama işi, her 24 saatte bir bulut uç noktası için başlatılır. Daha fazla bilgi için bkz. [Azure dosyaları hakkında sık sorulan sorular](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection).

Bulut uç noktasını oluşturan yöneticinin, bulut uç noktasının işaret ettiği Azure dosya paylaşımının bulunduğu depolama hesabı için yönetim rolü **sahibinin** bir üyesi olması gerekir. Bu, depolama hesabının Azure portal **Access Control (IAM)** altında yapılandırılabilir.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Bir eşitleme grubu oluşturmak için, [Azure Portal](https://portal.azure.com/)depolama eşitleme hizmetinize gidin ve **+ eşitleme grubu**' nu seçin:

![Azure portalda yeni bir eşitleme grubu oluşturma](media/storage-sync-files-deployment-guide/create-sync-group-part-1.png)

Açılan bölmede, bulut uç noktası olan bir eşitleme grubu oluşturmak için aşağıdaki bilgileri girin:

- **Eşitleme grubu adı**: oluşturulacak eşitleme grubunun adı. Bu ad Depolama Eşitleme Hizmetinde benzersiz olmalıdır, ancak size mantıklı gelen herhangi bir ad olabilir.
- **Abonelik**: depolama eşitleme hizmeti 'ni [dağıtmak](#deploy-the-storage-sync-service)için depolama eşitleme hizmetini dağıttığınız abonelik.
- **Depolama hesabı**: **depolama hesabı seç**' i seçerseniz, ile eşitlemek istediğiniz Azure dosya paylaşımının bulunduğu depolama hesabını seçebileceğiniz başka bir bölmesi görüntülenir.
- **Azure dosya paylaşma**: eşitlemek istediğiniz Azure dosya paylaşımının adı.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Eşitleme grubunu oluşturmak için aşağıdaki PowerShell 'i yürütün. `<my-sync-group>`Eşitleme grubunun istenen adıyla değiştirmeyi unutmayın.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Eşitleme grubu başarıyla oluşturulduktan sonra, bulut uç noktanızı oluşturabilirsiniz. `<my-storage-account>`Ve `<my-file-share>` değerlerini beklenen değerlerle değiştirdiğinizden emin olun.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Yeni bir eşitleme grubu oluşturmak için [az storagessync Sync-Group](/cli/azure/storagesync/sync-group#az_storagesync_sync_group_create) komutunu kullanın.  Tüm CLı komutları için bir kaynak grubuna varsayılan olarak, [az configure](/cli/azure/reference-index#az_configure)komutunu kullanın.

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Yeni bir bulut uç noktası oluşturmak için [az storagessync Sync-Group Cloud-Endpoint](/cli/azure/storagesync/sync-group/cloud-endpoint#az_storagesync_sync_group_cloud_endpoint_create) komutunu kullanın.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Sunucu uç noktası oluşturma
Sunucu uç noktası, bir sunucu birimi üzerindeki klasör gibi kayıtlı bir sunucu üzerindeki belirli bir noktayı temsil eder. Sunucu uç noktası aşağıdaki koşullara tabidir:

- Sunucu uç noktası, kayıtlı bir sunucuda (bağlı bir paylaşıma değil) bir yol olmalıdır. Ağa bağlı depolama (NAS) desteklenmiyor.
- Sunucu uç noktası sistem biriminde olabilse de, sistem birimindeki sunucu uç noktaları bulut katmanlaması kullanmıyor olabilir.
- Bir birimde sunucu uç noktası oluşturulduktan sonra yolu veya sürücü harfini değiştirme desteklenmez. Kayıtlı sunucunuzda son bir yol kullandığınızdan emin olun.
- Kayıtlı bir sunucu birden çok sunucu uç noktasını destekleyebilir, ancak bir eşitleme grubu herhangi bir zamanda kayıtlı sunucu başına yalnızca bir sunucu uç noktası olabilir. Eşitleme grubu içindeki diğer sunucu uç noktaları, farklı kayıtlı sunucularda olmalıdır.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Sunucu uç noktası eklemek için yeni oluşturulan eşitleme grubuna gidin ve **sunucu uç noktası Ekle**' yi seçin.

![Eşitleme grubu bölmesine yeni bir sunucu uç noktası ekleme](media/storage-sync-files-deployment-guide/create-sync-group-part-2.png)

**Sunucu uç noktası ekle** bölmesinde bir sunucu uç noktası oluşturmak için aşağıdaki bilgileri girin:

- **Kayıtlı sunucu**: sunucu uç noktasını oluşturmak istediğiniz sunucunun veya kümenin adı.
- **Yol**: eşitleme grubunun bir parçası olarak eşitlenecek Windows Server yolu.
- **Bulut katmanlama**: bulut katmanlamayı etkinleştirmek veya devre dışı bırakmak için bir anahtar. Bulut katmanlaması sayesinde, sık kullanılmayan veya erişilen dosyalar Azure dosyaları için katmanlı olabilir.
- **Birim boş alanı**: sunucu uç noktasının bulunduğu birimde ayrılacak boş alan miktarı. Örneğin, birim boş alanı tek bir sunucu uç noktasına sahip bir birimde %50 olarak ayarlandıysa, yaklaşık olarak veri miktarı Azure dosyalarına katmanlı olur. Bulut katmanlama özelliğinin etkinleştirilip etkinleştirilmediği bağımsız olarak, Azure dosya paylaşımınızda her zaman eşitleme grubundaki verilerin tamamen bir kopyası bulunur.
- **İlk indirme modu**: Bu, Azure dosya paylaşımında dosyalar varken, ancak sunucuda olmadığında faydalı olabilecek, aracı sürümü 11 ' den başlayarak isteğe bağlı bir seçimdir. Böyle bir durum, örneğin, başka bir şube ofis sunucusunu bir eşitleme grubuna eklemek için veya hatalı bir sunucuyu olağanüstü durumdan kurtardığınızda bir sunucu uç noktası oluşturursanız olabilir. Bulut katmanlaması etkinleştirilmişse, varsayılan olarak yalnızca ad alanını geri çekmeniz ve başlangıçta dosya içeriği olmaması gerekir. Bu, Kullanıcı erişimi isteklerinin sunucuya hangi dosya içeriğinin geri çekileceğine karar vermesini düşünüyorsanız kullanışlıdır. Bulut katmanlaması devre dışıysa, varsayılan olarak ad alanı önce indirilir ve ardından dosyalar, yerel kapasiteye ulaşılana kadar son değiştirilen zaman damgasına göre geri alınacaktır. Bununla birlikte, ilk indirme modunu yalnızca ad alanı olarak değiştirebilirsiniz. Üçüncü mod yalnızca, bu sunucu uç noktası için bulut katmanlaması devre dışı bırakılmışsa kullanılabilir. Bu mod önce ad alanını geri çekmeyi önler. Dosyalar yalnızca yerel sunucuda görünür ve bu, tam olarak indirme şansı vardır. Bu mod, örneğin bir uygulamanın tam dosyaların mevcut olmasını gerektiriyorsa ve bu dosya ad alanındaki katmanlı dosyalara tolerans uygulamadıysanız yararlı olur.

Sunucu uç noktasını eklemek için **Oluştur**' u seçin. Dosyalarınız artık Azure dosya paylaşımınızda ve Windows Server 'da eşitlenmiş durumda tutulur. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Sunucu uç noktasını oluşturmak için aşağıdaki PowerShell komutlarını yürütün, ve `<your-server-endpoint-path>` ' ı `<your-volume-free-space>` ve isteğe bağlı ilk indirme ilkesi için isteğe bağlı ayarı kontrol ettiğinizden emin olun.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Yeni bir sunucu uç noktası oluşturmak için [az storagessync Sync-Group Server-Endpoint](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create) komutunu kullanın.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Güvenlik duvarı ve sanal ağ ayarlarını yapılandırma

### <a name="portal"></a>Portal
Azure dosya eşitlemesini güvenlik duvarı ve sanal ağ ayarlarıyla çalışacak şekilde yapılandırmak istiyorsanız aşağıdakileri yapın:

1. Azure portal, güvenli hale getirmek istediğiniz depolama hesabına gidin.
1. Sol taraftaki menüden **ağ** ' ı seçin.
1. **Seçilen ağlarda** **erişimine izin ver** altında.
1. Sunucularınızın IP veya sanal ağınızın **adres aralığı** bölümünün altında listelendiğinden emin olun.
1. **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver** ' in işaretli olduğundan emin olun.
1. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

    ![Güvenlik Duvarı ve sanal ağ ayarlarını Azure dosya eşitleme ile çalışacak şekilde yapılandırma](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Azure Dosya Eşitleme ile ekleme
Tam dosya uygunluk ve erişim denetimi listesi (ACL) korunarak, sıfır kesinti olmadan ilk kez Azure Dosya Eşitleme eklemek için önerilen adımlar şunlardır:
 
1. Depolama eşitleme hizmeti dağıtın.
1. Eşitleme grubu oluşturun.
1. Tam veri kümesiyle sunucuya Azure Dosya Eşitleme Aracısı 'nı yükler.
1. Bu sunucuyu kaydedin ve paylaşımda bir sunucu uç noktası oluşturun. 
1. Azure dosya paylaşımında (bulut uç noktası) tam karşıya yükleme işleminin eşitlenmesine izin verin.  
1. İlk karşıya yükleme tamamlandıktan sonra, kalan sunucuların her birine Azure Dosya Eşitleme aracısını yükleyin.
1. Kalan sunucuların her birinde yeni dosya paylaşımları oluşturun.
1. İsterseniz, bulut katmanlama ilkesiyle yeni dosya paylaşımlarında sunucu uç noktaları oluşturun. (Bu adım, ilk kurulumda kullanılabilir ek depolama alanı gerektirir.)
1. Azure Dosya Eşitleme aracısının gerçek veri aktarımı olmadan tam ad alanını hızlı bir şekilde geri yüklemesini sağlar. Tam ad alanı eşitledikten sonra, eşitleme altyapısı, sunucu uç noktası için bulut katmanlaması ilkesine bağlı olarak yerel disk alanını doldurur. 
1. Eşitlemenin tamamlandığından emin olun ve topolojinizi istenen şekilde test edin. 
1. Kullanıcıları ve uygulamaları bu yeni paylaşıma yeniden yönlendirin.
1. İsteğe bağlı olarak sunuculardaki yinelenen paylaşımları silebilirsiniz.
 
İlk ekleme için ek depolama alanı yoksa ve var olan paylaşımlara eklemek istiyorsanız, verileri Azure dosya paylaşımlarında ön temel alabilirsiniz. Bu yaklaşım, ve yalnızca kapalı kalma süresini kabul edebiliyorsanız ve ilk ekleme işlemi sırasında sunucu paylaşımlarında hiçbir veri değişikliği olmaması durumunda önerilir. 
 
1. Ekleme işlemi sırasında sunuculardan herhangi bir veri üzerinde değişiklik olmamasını sağlayın.
1. SMB üzerinde herhangi bir veri aktarım aracı kullanarak sunucu verileriyle ön çekirdek Azure dosya paylaşımları. Örneğin, Robocopy. Ayrıca REST üzerinde AzCopy kullanabilirsiniz. ACL zaman damgalarını ve özniteliklerini korumak için AzCopy komutunu uygun anahtarlarla birlikte kullandığınızdan emin olun.
1. Mevcut paylaşımları işaret eden istenen sunucu uç noktaları ile Azure Dosya Eşitleme topolojisi oluşturun.
1. Tüm uç noktalarında eşitleme bitiş mutabakatı işlemine izin verin. 
1. Mutabakat tamamlandıktan sonra, değişiklikler için paylaşımlar açabilirsiniz.
 
Şu anda, önceden dağıtım öncesi yaklaşımın bazı sınırlamaları vardır- 
- Eşitleme topolojisi tamamen çalışır duruma gelmeden önce sunucudaki veri değişiklikleri, sunucu uç noktalarında çakışmalara neden olabilir.  
- Bulut uç noktası oluşturulduktan sonra, Azure Dosya Eşitleme ilk eşitlemeyi başlatmadan önce buluttaki dosyaları algılamaya yönelik bir işlem çalıştırır. Bu işlemi gerçekleştirmek için geçen süre, ağ hızı, kullanılabilir bant genişliği ve dosya ve klasör sayısı gibi çeşitli faktörlere bağlı olarak değişir. Önizleme sürümündeki kaba tahmin için algılama işlemi yaklaşık 10 dosya/sn ile çalıştırılır.  Bu nedenle, ön dengeli dağıtım hızlı çalışıyor olsa bile, verilerin bulutta önceden hazırlanması durumunda, tam olarak çalışan bir sistemi almak için genel süre önemli ölçüde uzun sürebilir.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Önceki sürümler ve VSS üzerinden self servis geri yükleme (Birim Gölge Kopyası Hizmeti)

> [!IMPORTANT]
> Aşağıdaki bilgiler, depolama eşitleme aracısının yalnızca sürüm 9 (veya üzeri) ile kullanılabilir. 9 ' dan düşük sürümlerde StorageSyncSelfService cmdlet 'leri olmayacaktır.

Önceki sürümler, bir dosyanın geri yüklenebilen sürümlerini bir SMB istemcisine sunmak için bir birimin sunucu tarafı VSS anlık görüntülerini kullanmanızı sağlayan bir Windows özelliğidir.
Bu, genellikle self servis geri yükleme olarak adlandırılan, bir BT yöneticisinden geri yüklemeye bağlı olmak yerine doğrudan bilgi çalışanları için bir güçlü senaryo sağlar.

VSS anlık görüntüleri ve önceki sürümler Azure Dosya Eşitleme bağımsız olarak çalışır. Ancak, bulut katmanlaması uyumlu bir moda ayarlanmalıdır. Birçok Azure Dosya Eşitleme sunucu uç noktası aynı birimde bulunabilir. Bulut katmanlaması kullanmayı planladığınız veya kullandığınız bir sunucu uç noktası olan her birim için aşağıdaki PowerShell çağrısını yapmanız gerekir.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS anlık görüntüleri bir birimin tamamında alınır. Varsayılan olarak, belirli bir birim için en fazla 64 anlık görüntü bulunabilir ve anlık görüntüleri depolamak için yeterli alan yoktur. VSS bunu otomatik olarak işler. Varsayılan anlık görüntü zamanlaması günde iki anlık görüntü alır ve Pazartesi 'den Cuma 'Ya kadar. Bu zamanlama Windows zamanlanmış görevi aracılığıyla yapılandırılabilir. Yukarıdaki PowerShell cmdlet 'i iki şey yapar:
1. Belirtilen birimdeki Azure dosya eşitlemeleri bulut katmanlaması ' nı, önceki sürümlerle uyumlu olacak şekilde yapılandırır ve bir dosyanın sunucudaki buluta katmanlı olsa bile önceki sürümden geri yüklenebildiğinden emin olur. 
1. Varsayılan VSS zamanlamasını sunar. Daha sonra daha sonra değiştirmeye karar verebilirsiniz. 

> [!Note]  
> Dikkat etmeniz gereken iki önemli nokta vardır:
>- -Zorlama parametresini kullanırsanız ve VSS Şu anda etkinse, geçerli VSS anlık görüntü zamanlamasının üzerine yazar ve varsayılan zamanlamaya göre değiştirilir. Cmdlet 'ini çalıştırmadan önce özel yapılandırmanızı kaydetdiğinizden emin olun.
> - Bu cmdlet 'i bir küme düğümünde kullanıyorsanız kümedeki diğer tüm düğümlerde de çalıştırmanız gerekir! 

Self Servis geri yükleme uyumluluğun etkin olup olmadığını görmek için aşağıdaki cmdlet 'i çalıştırabilirsiniz.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Sunucu üzerindeki tüm birimleri ve her biri için bulut katmanlama uyumlu gün sayısını listeler. Bu sayı, birim başına olası en yüksek anlık görüntüye ve varsayılan anlık görüntü zamanlamaya göre otomatik olarak hesaplanır. Bu nedenle, varsayılan olarak, bir bilgi çalışanına sunulan tüm önceki sürümler ' den geri yüklemek için kullanılabilir. Varsayılan zamanlamayı daha fazla anlık görüntü alacak şekilde değiştirirseniz de aynı değer geçerlidir.
Ancak, zamanlamayı, uyumlu günler değerinden daha eski olan birimde kullanılabilir bir anlık görüntüye neden olacak şekilde değiştirirseniz, kullanıcılar geri yüklemek için bu eski anlık görüntüyü (önceki sürüm) kullanamaz.

> [!Note]
> Self Servis geri yükleme özelliğinin etkinleştirilmesi, Azure depolama tüketiminiz ve faturanızda bir etkiye sahip olabilir. Bu etki, sunucuda şu anda katmanlı olan dosyalarla sınırlıdır. Bu özelliği etkinleştirmek, bulutta bulunan ve önceki sürümler (VSS anlık görüntüsü) girişi aracılığıyla başvurulabilen bir dosya sürümü olmasını sağlar.
>
> Özelliği devre dışı bırakırsanız, uyumlu günler penceresi geçirilmeden Azure depolama alanı tüketimi yavaş bir şekilde ret edilir. Bunu hızlandırmanın bir yolu yoktur. 

Birim başına varsayılan en fazla VSS anlık görüntüsü sayısı (64) ve bu işlem için varsayılan zamanlama, bir bilgi çalışanının, biriminizde depolayabileceği VSS anlık görüntülerine göre geri yüklenebildiği en fazla 45 gün içinde sonuçlanır.

Max. 64 VSS anlık görüntüsü, birim başına doğru ayar değilse, [Bu değeri bir kayıt defteri anahtarı aracılığıyla değiştirebilirsiniz](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Yeni sınırın etkin olması için, daha önce etkinleştirildiği her birimde önceki sürüm uyumluluğunu etkinleştirmek üzere cmdlet 'i yeniden çalıştırmanız gerekir. Bu, en fazla birim başına en fazla VSS anlık görüntüsü sayısını hesaba koymak için-zorlama bayrağını kullanın. Bu, yeni hesaplanan gün sayısına neden olur. Bu değişikliğin yalnızca yeni katmanlı dosyalar üzerinde etkili olacağını ve yapmış olabileceğiniz VSS zamanlamasıyla ilgili tüm özelleştirmelerin üzerine yazılmasını lütfen unutmayın.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Yeni ve değiştirilmiş dosyaları Azure dosya paylaşımından proaktif olarak geri çek

Aracı sürümü 11 ile sunucu uç noktasında yeni bir mod kullanılabilir hale gelir. Bu mod, yerel kullanıcılar herhangi bir dosyaya erişmeden önce bile, küresel olarak dağıtılmış şirketlerin uzak bir bölgedeki sunucu önbelleğinin önceden doldurulmasına olanak tanır. Sunucu uç noktasında etkinleştirildiğinde bu mod, bu sunucunun Azure dosya paylaşımında oluşturulmuş veya değiştirilmiş dosyaları geri çekmesine neden olur.

### <a name="scenario"></a>Senaryo

Küresel olarak dağıtılan bir şirkette ABD 'de ve Hindistan 'daki şube ofisleri bulunur. Sabah (US saat) bilgi çalışanları, yeni bir proje için yeni bir klasör ve yeni dosyalar oluşturur ve tüm gün üzerinde çalışır. Azure Dosya Eşitleme, klasör ve dosyaları Azure dosya paylaşımında (bulut uç noktası) eşitler. Hindistan 'daki bilgi çalışanları proje üzerinde kendi saat dilimlerinde çalışmaya devam edecektir. Her sabah ulaşan yerel Azure Dosya Eşitleme etkin sunucu, Hindistan ekibinin yerel bir önbellekte etkin bir şekilde çalışması için bu yeni dosyaların yerel olarak kullanılabilmesini gerektirir. Bu modu etkinleştirmek, istek üzerine geri çekme nedeniyle ilk dosya erişiminin daha yavaş olmasını engeller ve sunucunun Azure dosya paylaşımında değiştirildikleri anda dosyaları önceden çekmesine olanak tanır.

> [!IMPORTANT]
> Azure dosya paylaşımında, sunucuya yakın olan değişiklikleri izlemenin, çıkış trafiğinizi ve Azure 'dan faturanızı artırabileceğini unutmayın. Sunucuya geri çekilen dosyalar gerçekten yerel olarak gerekmiyorsa, sunucuya gereksiz geri çağırma olumsuz sonuçlara sahip olabilir. Bu modu, bulutta en son değişikliklere sahip bir sunucuda önbelleğin önceden doldurulmasının, bu sunucudaki dosyaları kullanan kullanıcılar veya uygulamalar üzerinde olumlu bir etkiye sahip olduğunu bildiğiniz durumlarda kullanın.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Bir Azure dosya paylaşımında nelerin değiştiğini önceden çekmek için bir sunucu uç noktası etkinleştirin

# <a name="portal"></a>[Portal](#tab/proactive-portal)

1. [Azure Portal](https://portal.azure.com/), depolama eşitleme hizmetinize gidin, doğru eşitleme grubunu seçin ve ardından Azure dosya paylaşımında (bulut uç noktası) yapılan değişiklikleri yakından izlemek istediğiniz sunucu uç noktasını belirleyin.
1. Bulut katmanlama bölümünde "Azure dosya paylaşma indirmesi" konusunu bulun. Şu anda seçili olan modu görürsünüz ve Azure dosya paylaşma değişikliklerini daha yakından izlemek ve bunları sunucuya geri çekmek için değiştirebilir.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Şu anda etkin olan bir sunucu uç noktası için Azure dosya paylaşımının karşıdan yükleme davranışını gösteren bir resim ve bunu değiştirmeye izin veren bir menü açmak için bir düğme.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

PowerShell 'de sunucu uç noktası özelliklerini [set-AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) cmdlet 'i aracılığıyla değiştirebilirsiniz.

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>DFS Çoğaltma (DFS-R) dağıtımını Azure Dosya Eşitleme geçirme
Bir DFS-R dağıtımını Azure Dosya Eşitleme 'e geçirmek için:

1. Değiştirdiğiniz DFS-R topolojisini temsil eden bir eşitleme grubu oluşturun.
1. Geçirmek için, DFS-R topolojinizde tam veri kümesine sahip olan sunucuyu başlatın. Azure Dosya Eşitleme bu sunucuya yükler.
1. Bu sunucuyu kaydedin ve geçirilecek ilk sunucu için bir sunucu uç noktası oluşturun. Bulut katmanlamayı etkinleştirmeyin.
1. Tüm verilerin Azure dosya paylaşımınızla (bulut uç noktası) eşitlenmesine izin verin.
1. Azure Dosya Eşitleme aracısını, kalan DFS-R sunucularının her birine yükleyip kaydedin.
1. DFS-R 'yi devre dışı bırak. 
1. Her DFS-R sunucusunda bir sunucu uç noktası oluşturun. Bulut katmanlamayı etkinleştirmeyin.
1. Eşitlemenin tamamlandığından emin olun ve topolojinizi istenen şekilde test edin.
1. DFS-R 'yi devre dışı bırakma.
1. Bulut katmanlaması, artık istediğiniz gibi herhangi bir sunucu uç noktasında etkinleştirilebilir.

Daha fazla bilgi için bkz. [Dağıtılmış dosya sistemi (DFS) ile Azure dosya eşitleme birlikte çalışabilirliği](file-sync-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Dosya Eşitleme sunucusu uç noktası ekleme veya kaldırma](file-sync-server-endpoint.md)
- [Azure Dosya Eşitleme bir sunucuyu kaydetme veya kaydını silme](file-sync-server-registration.md)
- [Azure Dosya Eşitleme’yi izleme](file-sync-monitoring.md)