---
title: Dağıtım Azure Dosya Eşitleme | Microsoft Docs
description: Baştan sona Azure Dosya Eşitleme dağıtmayı öğrenin.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: de0eb685e212b59705d8d659cbe9627338697e9d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854518"
---
# <a name="deploy-azure-file-sync"></a>Azure Dosya Eşitleme’yi dağıtma
Şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu koruyarak kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirmek için Azure Dosya Eşitleme kullanın. Azure Dosya Eşitleme, Windows Server 'ı Azure dosya paylaşımınızın hızlı önbelleğine dönüştürür. SMB, NFS ve FTPS dahil olmak üzere verilerinize yerel olarak erişmek için Windows Server 'da bulunan herhangi bir protokolü kullanabilirsiniz. Dünyanın dört bir yanında ihtiyacınız olan sayıda önbellekler olabilir.

Bu makalede açıklanan adımları tamamlamadan önce bir [Azure dosyaları dağıtımı Için planlamayı](storage-files-planning.md) ve [bir Azure dosya eşitleme dağıtımı planlamayı](storage-sync-files-planning.md) kesinlikle öneririz.

## <a name="prerequisites"></a>Önkoşullar
* Azure Dosya Eşitleme dağıtmak istediğiniz bölgedeki bir Azure dosya paylaşımıdır. Daha fazla bilgi için bkz.
    - Azure Dosya Eşitleme için [bölge kullanılabilirliği](storage-sync-files-planning.md#region-availability) .
    - Dosya paylaşımının nasıl oluşturulacağı hakkında adım adım bir açıklama için [dosya paylaşma oluşturun](storage-how-to-create-file-share.md) .
* Azure Dosya Eşitleme eşitlenecek Windows Server veya Windows Server kümesinin en az bir desteklenen örneği. Desteklenen Windows Server sürümleri hakkında daha fazla bilgi için bkz. [Windows Server Ile birlikte çalışabilirlik](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Az PowerShell modülü, PowerShell 5,1 ya da PowerShell 6 + ile birlikte kullanılabilir. Windows olmayan sistemler dahil olmak üzere desteklenen herhangi bir sistemde Azure Dosya Eşitleme için az PowerShell modülünü kullanabilirsiniz, ancak sunucu kayıt cmdlet 'i her zaman kaydolduğunuz Windows Server örneğinde çalıştırılmalıdır (Bu işlem doğrudan veya PowerShell aracılığıyla yapılabilir) Uzaktan iletişim). Windows Server 2012 R2 'de, en az PowerShell 5,1 kullandığınızı doğrulayabilirsiniz. $PSVersionTable nesnesinin **psversion** özelliğinin değerine bakarak: \*

    ```powershell
    $PSVersionTable.PSVersion
    ```

    PSVersion değeri 5,1 ' den küçükse. , Windows Server 2012 R2 'nin en yeni yüklemelerinde olduğu gibi, [Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616)' i indirerek ve yükleyerek kolayca yükseltebilirsiniz. \* Windows Server 2012 R2 için indirmek ve yüklemek üzere uygun paket **Win\*8.1 andw2k12r2-KB\*\*\*\*\*\*-x64. msu**' dir. 

    PowerShell 6 + desteklenen herhangi bir sistemle birlikte kullanılabilir ve [GitHub sayfası](https://github.com/PowerShell/PowerShell#get-powershell)aracılığıyla indirilebilir. 

    > [!Important]  
    > Doğrudan PowerShell 'den kaydolmak yerine sunucu kaydı kullanıcı arabirimini kullanmayı planlıyorsanız, PowerShell 5,1 ' i kullanmanız gerekir.

* PowerShell 5,1 kullanmayı tercih ettiyseniz, en az .NET 4.7.2 'ın yüklü olduğundan emin olun. Sisteminizde [.NET Framework sürümleri ve bağımlılıklar](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) hakkında daha fazla bilgi edinin.

    > [!Important]  
    > Windows Server Core üzerinde .NET 4.7.2 + yüklüyorsanız, `quiet` ve `norestart` bayraklarıyla yüklemeniz gerekir, aksi takdirde yükleme başarısız olur. Örneğin, .NET 4,8 ' i yüklüyorsanız, komut aşağıdaki gibi görünür:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Buradaki yönergeleri izleyerek yüklenebilen az PowerShell modülü: [Azure PowerShell yükleyip yapılandırın](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Az. Storagessync modülü artık az PowerShell modülünü yüklediğinizde otomatik olarak yüklenir.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Windows Server’ı Azure Dosya Eşitleme ile kullanmaya hazırlama
Azure Dosya Eşitleme ile kullanmayı düşündüğünüz her sunucu için, bir yük devretme kümesindeki her sunucu düğümü dahil, **Internet Explorer Artırılmış güvenlik yapılandırmasını**devre dışı bırakın. Bu yalnızca ilk sunucu kaydı için gereklidir. Sunucu kaydedildikten sonra özelliği yeniden etkinleştirebilirsiniz.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Windows Server Core üzerinde Azure Dosya Eşitleme dağıtıyorsanız, bu adımı atlayabilirsiniz.

1. Sunucu Yöneticisi açın.
2. **Yerel sunucu**' ya tıklayın:  
    ![Sunucu Yöneticisi Kullanıcı arabiriminin sol tarafındaki "yerel sunucu"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. **Özellikler** alt bölmesinde **IE Artırılmış Güvenlik Yapılandırması** bağlantısını seçin.  
    ![Sunucu Yöneticisi Kullanıcı arabirimindeki "IE artırılmış güvenlik yapılandırması" bölmesi](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. **Internet Explorer Artırılmış Güvenlik Yapılandırması** Iletişim kutusunda **Yöneticiler** ve **Kullanıcılar**için **kapalı** ' yı seçin:  
    !["Kapalı" seçiliyken Internet Explorer Artırılmış Güvenlik Yapılandırması açılır penceresi](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
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

---

## <a name="deploy-the-storage-sync-service"></a>Depolama Eşitleme Hizmetini Dağıtma 
Azure Dosya Eşitleme dağıtımı, **depolama eşitleme hizmeti** kaynağı seçtiğiniz aboneliğin bir kaynak grubuna yerleştirilerek başlar. Gerektiğinde sağlamamız önerilir. Sunucularınız ile bu kaynak arasında bir güven ilişkisi oluşturacaksınız ve bir sunucu yalnızca bir depolama Eşitleme hizmetine kaydedilebilir. Sonuç olarak, sunucu gruplarını ayırmak için gereken sayıda depolama eşitleme hizmeti dağıtmanız önerilir. Farklı depolama eşitleme hizmetlerinden sunucuların birbirleriyle eşitlenemeyeceğini aklınızda bulundurun.

> [!Note]
> Depolama eşitleme hizmeti, dağıtılan abonelik ve kaynak grubundan erişim izinlerini devralır. Kimin erişimi olduğunu dikkatle denetlemeniz önerilir. Yazma erişimi olan varlıklar, bu depolama Eşitleme hizmetine kaydedilen sunuculardan yeni dosya kümelerini eşitlemeye başlayabilir ve verilerin bunlara erişebilen Azure Storage 'a akmasını sağlar.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Bir depolama eşitleme hizmeti dağıtmak için, [Azure Portal](https://portal.azure.com/)gidin, *kaynak oluştur ' a* tıklayın ve ardından Azure dosya eşitleme aratın. Arama sonuçlarında **Azure dosya eşitleme**' yi seçin ve ardından **Oluştur** ' u seçerek **depolama eşitlemesini dağıt** sekmesini açın.

Açılan bölmeye aşağıdaki bilgileri girin:

- **Ad**: Depolama Eşitleme Hizmeti için benzersiz bir ad (abonelik başına).
- **Abonelik**: Depolama eşitleme hizmetini oluşturmak istediğiniz abonelik. Kuruluşunuzun yapılandırma stratejisine bağlı olarak, bir veya daha fazla aboneliğe erişiminiz olabilir. Azure aboneliği, her bir bulut hizmeti için (Azure dosyaları gibi) Faturalandırma için en temel kapsayıcıdır.
- **Kaynak grubu**: Kaynak grubu, depolama hesabı veya depolama eşitleme hizmeti gibi bir Azure kaynakları mantıksal grubudur. Azure Dosya Eşitleme için yeni bir kaynak grubu oluşturabilir veya var olan bir kaynak grubunu kullanabilirsiniz. (Belirli bir proje için HR kaynaklarını veya kaynaklarını gruplama gibi kaynak gruplarını kuruluşunuz için mantıksal olarak yalıtmak üzere kapsayıcı olarak kullanmanızı öneririz.)
- **Konum**: Azure Dosya Eşitleme dağıtmak istediğiniz bölge. Bu listede yalnızca desteklenen bölgeler bulunur.

İşiniz bittiğinde, depolama eşitleme hizmeti 'ni dağıtmak için **Oluştur** ' u seçin.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
**< Az_Region >** , **< RG_Name >** ve **< my_storage_sync_service >** değerlerini kendi değerlerinizle değiştirin, ardından bir depolama eşitleme hizmeti oluşturmak ve dağıtmak için aşağıdaki Cmds kullanın:

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

---

## <a name="install-the-azure-file-sync-agent"></a>Azure Dosya Eşitleme aracısını yükleme
Azure Dosya Eşitleme aracısı, Windows Server’ın bir Azure dosya paylaşımı ile eşitlenmesini sağlayan indirilebilir bir pakettir. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Aracıyı [Microsoft Indirme merkezi](https://go.microsoft.com/fwlink/?linkid=858257)' nden indirebilirsiniz. İndirme tamamlandığında, Azure Dosya Eşitleme Aracısı yüklemesini başlatmak için MSI paketine çift tıklayın.

> [!Important]  
> Azure Dosya Eşitleme bir yük devretme kümesiyle kullanmak istiyorsanız, Azure Dosya Eşitleme aracısının kümedeki her düğüme yüklenmesi gerekir. Azure Dosya Eşitleme çalışmak için kümedeki her düğümün kayıtlı olması gerekir.

Aşağıdakileri yapmanızı öneririz:
- Sorun gidermeyi ve sunucu bakımını kolaylaştırmak için varsayılan yükleme yolunu (C:\Program Files\Azure\StorageSyncAgent) bırakın.
- Azure Dosya Eşitleme güncel tutmak için Microsoft Update etkinleştirin. Azure Dosya Eşitleme aracısına yönelik, özellik güncelleştirmeleri ve düzeltmeler de dahil olmak üzere tüm güncelleştirmeler Microsoft Update oluşur. Azure Dosya Eşitleme için en son güncelleştirmeyi yüklemenizi öneririz. Daha fazla bilgi için [Azure dosya eşitleme güncelleştirme ilkesi](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)bölümüne bakın.

Azure Dosya Eşitleme Aracısı yüklemesi tamamlandığında, sunucu kaydı kullanıcı arabirimi otomatik olarak açılır. Kaydolmadan önce bir depolama Eşitleme hizmetine sahip olmanız gerekir; Depolama eşitleme hizmeti oluşturma hakkında sonraki bölüme bakın.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
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

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server’ı Depolama Eşitleme Hizmetine kaydetme
Windows Server’ı bir Depolama Eşitleme Hizmeti’ne kaydetmek, sunucunuz (veya kümeniz) ile Depolama Eşitleme Hizmeti arasında bir güven ilişkisi kurar. Bir sunucu yalnızca bir Depolama Eşitleme Hizmeti’ne kaydedilebilir ve aynı Depolama Eşitleme Hizmeti ile ilişkili diğer sunucular ve Azure dosya paylaşımları ile eşitlenebilir.

> [!Note]
> Sunucu kaydı, depolama eşitleme hizmeti ile Windows Server arasında bir güven ilişkisi oluşturmak için Azure kimlik bilgilerinizi kullanır, ancak bundan sonra sunucu kayıtlı olduğu sürece geçerli olan kendi kimliğini oluşturur ve kullanır geçerli paylaşılan erişim Imza belirteci (Storage SAS) geçerli. Sunucunun kaydı kaldırıldıktan sonra sunucuya yeni bir SAS belirteci verilemez, böylece sunucunun Azure dosya paylaşımınızla erişme özelliği kaldırılarak eşitleme durdurulur.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Sunucu kayıt kullanıcı arabirimi, Azure Dosya Eşitleme aracısının yüklenmesinden sonra otomatik olarak açılmalıdır. Değilse, dosya konumundan el ile açabilirsiniz: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Sunucu kaydı kullanıcı arabirimi açıldığında başlamak için **oturum aç** ' ı seçin.

Oturum açtıktan sonra, sizden aşağıdaki bilgiler istenir:

![Sunucu Kaydı kullanıcı arabiriminin ekran görüntüsü](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure aboneliği**: Depolama eşitleme hizmetini içeren abonelik (bkz. [depolama eşitleme hizmetini dağıtma](#deploy-the-storage-sync-service)). 
- **Kaynak grubu**: Depolama eşitleme hizmetini içeren kaynak grubu.
- **Depolama eşitleme hizmeti**: Kaydetmek istediğiniz depolama eşitleme hizmeti 'nin adı.

Uygun bilgileri seçtikten sonra, sunucu kaydını gerçekleştirmek için **Kaydet** ' i seçin. Kayıt işleminin bir parçası olarak bir kez daha oturum açmanız istenir.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Bir eşitleme grubu ve bir bulut uç noktası oluşturma
Eşitleme grubu, bir dosya kümesi için eşitleme topolojisini tanımlar. Bir eşitleme grubu içindeki uç noktalar, birbiriyle eşitlenmiş durumda tutulur. Eşitleme grubu, bir Azure dosya paylaşımı ve en az bir sunucu uç noktasını temsil eden bir bulut uç noktası içermelidir. Sunucu uç noktası, kayıtlı bir sunucudaki yolu temsil eder. Bir sunucu birden çok eşitleme grubunda sunucu uç noktalarına sahip olabilir. İstediğiniz eşitleme topolojinizi uygun şekilde açıklamanız için ihtiyacınız olan sayıda eşitleme grubu oluşturabilirsiniz.

Bulut uç noktası, Azure dosya paylaşımının bir işaretçisidir. Tüm sunucu uç noktaları bulut uç noktasıyla eşitlenir ve bu da bulut uç noktası hub 'ı yapar. Azure dosya paylaşımının depolama hesabı, depolama eşitleme hizmeti ile aynı bölgede bulunmalıdır. Azure dosya paylaşımının tamamı eşitlenir ve tek bir özel durum oluşur: Bir NTFS biriminde gizli "sistem birimi bilgileri" klasörüyle karşılaştırılabilen özel bir klasör sağlanacak. Bu dizin "olarak adlandırılır. Systemshareınformation ". Diğer uç noktalarla eşitlenmeyecek önemli eşitleme meta verileri içerir. Kullanma veya silme!

> [!Important]  
> Eşitleme grubundaki herhangi bir bulut uç noktasında veya sunucu uç noktasında değişiklik yapabilir ve dosyalarınızı eşitleme grubundaki diğer uç noktalarla eşitler. Bulut uç noktasına (Azure dosya paylaşımında) doğrudan değişiklik yaparsanız, önce değişikliklerin Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Bir değişiklik algılama işi, her 24 saatte bir bulut uç noktası için başlatılır. Daha fazla bilgi için bkz. [Azure dosyaları hakkında sık sorulan sorular](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Bir eşitleme grubu oluşturmak için, [Azure Portal](https://portal.azure.com/)depolama eşitleme hizmetinize gidin ve **+ eşitleme grubu**' nu seçin:

![Azure portalda yeni bir eşitleme grubu oluşturma](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Açılan bölmede, bulut uç noktası olan bir eşitleme grubu oluşturmak için aşağıdaki bilgileri girin:

- **Eşitleme grubu adı**: Oluşturulacak eşitleme grubunun adı. Bu ad Depolama Eşitleme Hizmetinde benzersiz olmalıdır, ancak size mantıklı gelen herhangi bir ad olabilir.
- **Abonelik**: Depolama eşitleme hizmeti 'ni [dağıtırken](#deploy-the-storage-sync-service)depolama eşitleme hizmeti 'ni dağıttığınız abonelik.
- **Depolama hesabı**: **Depolama hesabı seç**' i seçerseniz, eşitlemek istediğiniz Azure dosya paylaşımının bulunduğu depolama hesabını seçebileceğiniz başka bir bölmesi görüntülenir.
- **Azure dosya paylaşma**: Eşitlemek istediğiniz Azure dosya paylaşımının adı.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Eşitleme grubunu oluşturmak için aşağıdaki PowerShell 'i yürütün. Eşitleme grubunun istenen `<my-sync-group>` adıyla değiştirmeyi unutmayın.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Eşitleme grubu başarıyla oluşturulduktan sonra, bulut uç noktanızı oluşturabilirsiniz. `<my-storage-account>` Ve`<my-file-share>` değerlerini beklenen değerlerle değiştirdiğinizden emin olun.

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

---

## <a name="create-a-server-endpoint"></a>Sunucu uç noktası oluşturma
Sunucu uç noktası, bir sunucu birimi üzerindeki klasör gibi kayıtlı bir sunucu üzerindeki belirli bir noktayı temsil eder. Sunucu uç noktası kayıtlı bir sunucuda (bağlı bir paylaşıma değil) bir yol olmalıdır ve bulut katmanlaması kullanmak için, yolun sistem dışı bir birimde olması gerekir. Ağa bağlı depolama (NAS) desteklenmiyor.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Sunucu uç noktası eklemek için yeni oluşturulan eşitleme grubuna gidin ve **sunucu uç noktası Ekle**' yi seçin.

![Eşitleme grubu bölmesine yeni bir sunucu uç noktası ekleme](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

**Sunucu uç noktası ekle** bölmesinde bir sunucu uç noktası oluşturmak için aşağıdaki bilgileri girin:

- **Kayıtlı sunucu**: Sunucu uç noktasını oluşturmak istediğiniz sunucunun veya kümenin adı.
- **Yol**: Eşitleme grubunun bir parçası olarak eşitlenecek Windows Server yolu.
- **Bulut katmanlama**: Bulut katmanlamayı etkinleştirmek veya devre dışı bırakmak için bir anahtar. Bulut katmanlaması sayesinde, sık kullanılmayan veya erişilen dosyalar Azure dosyaları için katmanlı olabilir.
- **Birim boş alanı**: Sunucu uç noktasının bulunduğu birimde ayrılacak boş alan miktarı. Örneğin, birim boş alanı tek bir sunucu uç noktasına sahip bir birimde% 50 olarak ayarlandıysa, yaklaşık olarak veri miktarı Azure dosyalarına katmanlı olur. Bulut katmanlama özelliğinin etkinleştirilip etkinleştirilmediği bağımsız olarak, Azure dosya paylaşımınızda her zaman eşitleme grubundaki verilerin tamamen bir kopyası bulunur.

Sunucu uç noktasını eklemek için **Oluştur**' u seçin. Dosyalarınız artık Azure dosya paylaşımınızda ve Windows Server 'da eşitlenmiş durumda tutulur. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Sunucu uç noktasını oluşturmak için aşağıdaki PowerShell komutlarını yürütün ve ve `<your-server-endpoint-path>` `<your-volume-free-space>` değerlerini istenen değerlerle değiştirdiğinizden emin olun.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

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
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Güvenlik duvarını ve sanal ağ ayarlarını yapılandırma

### <a name="portal"></a>Portal
Azure dosya eşitlemesini güvenlik duvarı ve sanal ağ ayarlarıyla çalışacak şekilde yapılandırmak istiyorsanız aşağıdakileri yapın:

1. Azure portal, güvenli hale getirmek istediğiniz depolama hesabına gidin.
1. Sol menüdeki **güvenlik duvarları ve sanal ağlar** düğmesini seçin.
1. **Erişime Izin ver**altında **Seçili ağlar** ' ı seçin.
1. Sunucularınızın IP veya sanal ağınızın uygun bölümde listelendiğinden emin olun.
1. **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver** ' in işaretli olduğundan emin olun.
1. Ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

![Güvenlik Duvarı ve sanal ağ ayarlarını Azure dosya eşitleme ile çalışacak şekilde yapılandırma](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Azure Dosya Eşitleme ile ekleme
Tam dosya uygunluk ve erişim denetimi listesi (ACL) korunarak, ilk için Azure Dosya Eşitleme sıfır kapalı kalma süresiyle birlikte eklemek için önerilen adımlar şunlardır:
 
1. Depolama eşitleme hizmeti dağıtın.
2. Eşitleme grubu oluşturun.
3. Tam veri kümesiyle sunucuya Azure Dosya Eşitleme Aracısı 'nı yükler.
4. Bu sunucuyu kaydedin ve paylaşımda bir sunucu uç noktası oluşturun. 
5. Azure dosya paylaşımında (bulut uç noktası) tam karşıya yükleme işleminin eşitlenmesine izin verin.  
6. İlk karşıya yükleme tamamlandıktan sonra, kalan sunucuların her birine Azure Dosya Eşitleme aracısını yükleyin.
7. Kalan sunucuların her birinde yeni dosya paylaşımları oluşturun.
8. İsterseniz, bulut katmanlama ilkesiyle yeni dosya paylaşımlarında sunucu uç noktaları oluşturun. (Bu adım, ilk kurulumda kullanılabilir ek depolama alanı gerektirir.)
9. Azure Dosya Eşitleme aracısının gerçek veri aktarımı olmadan tam ad alanını hızlı bir şekilde geri yüklemesini sağlar. Tam ad alanı eşitledikten sonra, eşitleme altyapısı, sunucu uç noktası için bulut katmanlaması ilkesine bağlı olarak yerel disk alanını doldurur. 
10. Eşitlemenin tamamlandığından emin olun ve topolojinizi istenen şekilde test edin. 
11. Kullanıcıları ve uygulamaları bu yeni paylaşıma yeniden yönlendirin.
12. İsteğe bağlı olarak sunuculardaki yinelenen paylaşımları silebilirsiniz.
 
İlk ekleme için ek depolama alanı yoksa ve var olan paylaşımlara eklemek istiyorsanız, verileri Azure dosya paylaşımlarında ön temel alabilirsiniz. Bu yaklaşım, ve yalnızca kapalı kalma süresini kabul edebiliyorsanız ve ilk ekleme işlemi sırasında sunucu paylaşımlarında hiçbir veri değişikliği olmaması durumunda önerilir. 
 
1. Ekleme işlemi sırasında sunuculardan herhangi bir veri üzerinde değişiklik olmamasını sağlayın.
2. SMB üzerinde herhangi bir veri aktarım aracı kullanarak sunucu verileriyle ön çekirdek ön eki olan Azure dosya paylaşımları; Örneğin, Robocopy, doğrudan SMB kopyası. AzCopy, SMB üzerinden verileri karşıya yüklemediğinden, ön dağıtım için kullanılamaz.
3. Mevcut paylaşımları işaret eden istenen sunucu uç noktaları ile Azure Dosya Eşitleme topolojisi oluşturun.
4. Tüm uç noktalarında eşitleme bitiş mutabakatı işlemine izin verin. 
5. Mutabakat tamamlandıktan sonra, değişiklikler için paylaşımlar açabilirsiniz.
 
Şu anda, önceden dağıtım öncesi yaklaşımın bazı sınırlamaları vardır- 
- Dosyalarda tam doğruluk korunmaz. Örneğin, dosyalar ACL 'Leri ve zaman damgalarını kaybeder.
- Eşitleme topolojisi tamamen çalışır duruma gelmeden önce sunucudaki veri değişiklikleri, sunucu uç noktalarında çakışmalara neden olabilir.  
- Bulut uç noktası oluşturulduktan sonra, Azure Dosya Eşitleme ilk eşitlemeyi başlatmadan önce buluttaki dosyaları algılamaya yönelik bir işlem çalıştırır. Bu işlemi gerçekleştirmek için geçen süre, ağ hızı, kullanılabilir bant genişliği ve dosya ve klasör sayısı gibi çeşitli faktörlere bağlı olarak değişir. Önizleme sürümündeki kaba tahmin için algılama işlemi yaklaşık 10 dosya/sn ile çalıştırılır.  Bu nedenle, ön dengeli dağıtım hızlı çalışıyor olsa bile, verilerin bulutta önceden hazırlanması durumunda, tam olarak çalışan bir sistemi almak için genel süre önemli ölçüde uzun sürebilir.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>DFS Çoğaltma (DFS-R) dağıtımını Azure Dosya Eşitleme geçirme
Bir DFS-R dağıtımını Azure Dosya Eşitleme 'e geçirmek için:

1. Değiştirdiğiniz DFS-R topolojisini temsil eden bir eşitleme grubu oluşturun.
2. Geçirmek için, DFS-R topolojinizde tam veri kümesine sahip olan sunucuyu başlatın. Azure Dosya Eşitleme bu sunucuya yükler.
3. Bu sunucuyu kaydedin ve geçirilecek ilk sunucu için bir sunucu uç noktası oluşturun. Bulut katmanlamayı etkinleştirmeyin.
4. Tüm verilerin Azure dosya paylaşımınızla (bulut uç noktası) eşitlenmesine izin verin.
5. Azure Dosya Eşitleme aracısını, kalan DFS-R sunucularının her birine yükleyip kaydedin.
6. DFS-R 'yi devre dışı bırak. 
7. Her DFS-R sunucusunda bir sunucu uç noktası oluşturun. Bulut katmanlamayı etkinleştirmeyin.
8. Eşitlemenin tamamlandığından emin olun ve topolojinizi istenen şekilde test edin.
9. DFS-R 'yi devre dışı bırakma.
10. Bulut katmanlaması, artık istediğiniz gibi herhangi bir sunucu uç noktasında etkinleştirilebilir.

Daha fazla bilgi için bkz. [Dağıtılmış dosya sistemi (DFS) ile Azure dosya eşitleme birlikte çalışabilirliği](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Dosya Eşitleme sunucusu uç noktası ekleme veya kaldırma](storage-sync-files-server-endpoint.md)
- [Azure Dosya Eşitleme bir sunucuyu kaydetme veya kaydını silme](storage-sync-files-server-registration.md)
- [Azure Dosya Eşitleme’yi izleme](storage-sync-files-monitoring.md)
