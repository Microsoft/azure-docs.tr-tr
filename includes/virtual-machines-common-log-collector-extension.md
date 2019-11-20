---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09c4420647043fccc408631fec75854667923721
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085284"
---
Microsoft Azure bulut hizmeti ile ilgili sorunları tanılamak, sorunlar oluşması durumunda hizmetin günlük dosyalarının sanal makinelerde toplanmasını gerektirir. Bir veya daha fazla bulut hizmeti sanal makinelerinden (her iki web rolünden ve çalışan rolünden) tek seferlik bir günlük koleksiyonu gerçekleştirmek ve toplanan dosyaları bir Azure depolama hesabına aktarmak için isteğe bağlı AzureLogCollector uzantısını kullanabilirsiniz; Bu, herhangi bir uzaktan oturum açmadan , VM 'Ler.

> [!NOTE]
> Günlüğe kaydedilen bilgilerin çoğunun açıklamaları https://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/ adresinde bulunabilir
> 
> 

Toplanacak dosya türlerine bağlı iki koleksiyon modu vardır.

* **Yalnızca Azure Konuk Aracısı günlükleri (GA)** . Bu koleksiyon modu, Azure Konuk aracıları ve diğer Azure bileşenleriyle ilgili tüm günlükleri içerir.
* **Tüm Günlükler (tam)** . Bu koleksiyon modu, tüm dosyaları GA modunda toplar ve:
  
  * Sistem ve uygulama olay günlükleri
  * HTTP hata günlükleri
  * IIS Günlükleri
  * Kurulum günlükleri
  * diğer sistem günlükleri

Her iki koleksiyon modunda, aşağıdaki yapının bir koleksiyonu kullanılarak ek veri toplama klasörleri belirtilebilir:

* **Ad**: koleksiyonun adı, toplanan dosyalarla ZIP dosyasının içindeki alt klasörün adı olarak kullanılır.
* **Konum**: sanal makinede toplanacak dosyaların bulunduğu klasörün yolu.
* **Searchmodel**: toplanacak dosyaların adlarının kalıbı. Varsayılan değer "\*"
* **Özyinelemeli**: toplanacak dosyalar belirtilen konumun altına özyinelemeli olarak konumlandırıldı.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Oluşturulmuş ZIP dosyalarını kaydetmek için uzantı için bir depolama hesabına sahip olmanız gerekir.
* Azure PowerShell. Bkz. Install [Azure PowerShell](/powershell/azure/install-az-ps)] for Install yönergeleri.

## <a name="add-the-extension"></a>Uzantıyı ekleme
AzureLogCollector uzantısını eklemek için [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) cmdlet 'Lerini veya [hizmet yönetimi REST API 'lerini](https://msdn.microsoft.com/library/ee460799.aspx) kullanabilirsiniz.

Cloud Services için, var olan Azure PowerShell cmdlet 'i, **set-AzureServiceExtension**, bulut hizmeti rol örneklerinde uzantıyı etkinleştirmek için kullanılabilir. Bu cmdlet aracılığıyla bu uzantı her etkinleştirildiğinde, seçilen rollerin seçili rol örneklerinde günlük koleksiyonu tetiklenir.

Sanal makinelerde, sanal makinelerde uzantıyı etkinleştirmek için, **set-AzureVMExtension**, mevcut Azure PowerShell cmdlet 'i kullanılabilir. Bu uzantı cmdlet 'ler aracılığıyla her etkinleştirildiğinde, her bir örnek üzerinde günlük koleksiyonu tetiklenir.

Bu uzantı, dahili olarak JSON tabanlı PublicConfiguration ve PrivateConfiguration kullanır. Ortak ve özel yapılandırma için örnek bir JSON 'nin düzeni aşağıda verilmiştir.

### <a name="publicconfiguration"></a>PublicConfiguration

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> Bu uzantının **Privateconfiguration**'ı olması gerekmez. **– Privateconfiguration** bağımsız değişkeni için yalnızca boş bir yapı sağlayabilirsiniz.
> 
> 

AzureLogCollector 'i bir veya daha fazla bulut hizmeti örneğine veya seçili rollerin sanal makinesine eklemek için aşağıdaki iki adımdan birini izleyebilirsiniz. Bu, her bir VM 'deki koleksiyonları çalıştırmak ve toplanan dosyaları belirtilen Azure hesabına göndermek için tetikler.

## <a name="adding-as-a-service-extension"></a>Hizmet uzantısı olarak ekleme
1. Azure PowerShell aboneliğinize bağlamak için yönergeleri izleyin.
2. AzureLogCollector uzantısını eklemek ve etkinleştirmek istediğiniz hizmet adını, yuvasını, rolleri ve rol örneklerini belirtin.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Dosyaların toplanacağı ek veri klasörünü belirtin (Bu adım isteğe bağlıdır).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > Sabit bir sürücü kullanmasından bu yana rol kök sürücüsünü belirtmek için belirteç `%roleroot%` kullanabilirsiniz.
   > 
   > 
4. Toplanan dosyaların yükleneceği Azure depolama hesabı adını ve anahtarını belirtin.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Bir bulut hizmeti için AzureLogCollector uzantısını etkinleştirmek üzere aşağıdaki şekilde SetAzureServiceLogCollector. ps1 (makalenin sonuna dahildir) öğesini çağırın. Yürütme tamamlandıktan sonra karşıya yüklenen dosyayı `https://YourStorageAccountName.blob.core.windows.net/vmlogs` altına bulabilirsiniz.

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Aşağıda, Betiğe geçirilen parametrelerin tanımı verilmiştir. (Bu, aşağıda de kopyalanır.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **HizmetAdı**: bulut hizmeti adınız.
* **Roller**: "WebRole1" veya "WorkerRole1" gibi rollerin bir listesi.
* **Örnekler**: virgülle ayrılmış rol örneklerinin adlarının bir listesi--tüm rol örnekleri için joker ("*") dizesini kullanın.
* **Yuva**: yuva adı. "Üretim" veya "hazırlama".
* **Mode**: toplama modu. "Tam" veya "GA".
* **StorageAccountName**: toplanan verileri depolamak için Azure depolama hesabının adı.
* **Storageaccountkey**: Azure depolama hesabı anahtarının adı.
* **Additionaldatalocationlist**: aşağıdaki yapının listesi:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>VM uzantısı olarak ekleme
Azure PowerShell aboneliğinize bağlamak için yönergeleri izleyin.

1. Hizmet adını, VM 'yi ve koleksiyon modunu belirtin.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Toplanan dosyaların yükleneceği Azure depolama hesabı adını ve anahtarını belirtin.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Bir bulut hizmeti için AzureLogCollector uzantısını etkinleştirmek üzere aşağıdaki şekilde SetAzureVMLogCollector. ps1 (makalenin sonuna dahildir) öğesini çağırın. Yürütme tamamlandıktan sonra karşıya yüklenen dosyayı `https://YourStorageAccountName.blob.core.windows.net/vmlogs` altına bulabilirsiniz.

Aşağıda, Betiğe geçirilen parametrelerin tanımı verilmiştir. (Bu, aşağıda de kopyalanır.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **HizmetAdı**: bulut hizmeti adınız.
* **VMName**: sanal makinenin adı.
* **Mode**: toplama modu. "Tam" veya "GA".
* **StorageAccountName**: toplanan verileri depolamak için Azure depolama hesabının adı.
* **Storageaccountkey**: Azure depolama hesabı anahtarının adı.
* **Additionaldatalocationlist**: aşağıdaki yapının listesi:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Uzantı PowerShell betik dosyaları
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Sonraki Adımlar
Artık günlüklerinizi tek bir basit konumdan inceleyebilir veya kopyalayabilirsiniz.

