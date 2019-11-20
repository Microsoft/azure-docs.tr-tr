---
title: Azure VM sorunlarını gidermek için uzak araçları kullanma | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: fab1e0b6f3b01446baed974b4be9b7295af4f837
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749729"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Azure VM sorunlarını gidermek için uzak araçları kullanma

Bir Azure sanal makinesinde (VM) sorunları giderirken, Uzak Masaüstü Protokolü (RDP) kullanmak yerine bu makalede ele alınan uzak araçları kullanarak VM 'ye bağlanabilirsiniz.

## <a name="serial-console"></a>Seri Konsol

Uzak Azure VM 'de komutları çalıştırmak için [sanal makine seri konsolu 'nu](serial-console-windows.md) kullanın.

## <a name="remote-cmd"></a>Uzak CMD

[PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec)'yi indirin. Aşağıdaki komutu çalıştırarak VM 'ye bağlanın:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Komutun aynı VNET 'teki bir bilgisayarda çalıştırılması gerekir.
>* DIP veya HostName, \<bilgisayar > değiştirmek için kullanılabilir.
>* -S parametresi, komutun sistem hesabı (yönetici izni) kullanılarak çağrıldığından emin olur.
>* PsExec 135 ve 445 TCP bağlantı noktalarını kullanır. Bu nedenle, iki bağlantı noktasının güvenlik duvarında açık olması gerekir.

## <a name="run-commands"></a>Komutları Çalıştır

VM 'de betikleri çalıştırmak için komutları Çalıştır özelliğinin nasıl kullanılacağı hakkında daha fazla bilgi için bkz. Run [komutuyla PowerShell betiklerini](../windows/run-command.md) çalıştırma

## <a name="customer-script-extension"></a>Müşteri betiği uzantısı

Özel Betik uzantısı özelliğini kullanarak hedef VM 'de özel bir komut dosyası çalıştırabilirsiniz. Bu özelliği kullanmak için aşağıdaki koşulların karşılanması gerekir:

* VM 'nin bağlantısı vardır.

* Azure Aracısı yüklü ve sanal makinede beklendiği şekilde çalışıyor.

* Uzantı, VM 'ye daha önce yüklenmedi.
 
  Uzantı, yalnızca ilk kez kullanıldığında betiği ekleyecektir. Bu özelliği daha sonra kullanırsanız, uzantı zaten kullanıldığını algılar ve yeni betiği karşıya yüklemecektir.

Komut dosyanızı bir depolama hesabına yüklemeniz ve kendi kapsayıcısını oluşturmanız gerekir. Ardından, VM bağlantısı olan bir bilgisayarda Azure PowerShell aşağıdaki betiği çalıştırın.

### <a name="for-v1-vms"></a>V1 VM 'Leri için

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>V2 VM 'Leri için

 

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>Uzak PowerShell

>[!Note]
>Bu seçeneği kullanabilmeniz için TCP bağlantı noktası 5986 (HTTPS) açık olmalıdır.
>
>ARM VM 'Leri için, ağ güvenlik grubu (NSG) üzerinde 5986 numaralı bağlantı noktasını açmanız gerekir. Daha fazla bilgi için bkz. güvenlik grupları. 
>
>RDFE VM 'Leri için özel bağlantı noktası (5986) ve genel bağlantı noktası olan bir uç noktaya sahip olmanız gerekir. Ardından, NSG 'de genel kullanıma açık bağlantı noktasını da açmanız gerekir.

### <a name="set-up-the-client-computer"></a>İstemci bilgisayarı ayarlama

VM 'yi uzaktan bağlamak üzere PowerShell 'i kullanmak için önce istemci bilgisayarı bağlantıya izin verecek şekilde ayarlamanız gerekir. Bunu yapmak için, aşağıdaki komutu uygun şekilde çalıştırarak VM 'yi PowerShell güvenilir ana bilgisayarlar listesine ekleyin.

Güvenilen konaklar listesine bir VM eklemek için:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Güvenilir konaklar listesine birden çok VM eklemek için:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Tüm bilgisayarları güvenilir konaklar listesine eklemek için:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>VM 'de RemotePS 'yi etkinleştirme

Klasik VM 'Ler için, Özel Betik uzantısı 'nı kullanarak aşağıdaki betiği çalıştırın:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

ARM VM 'Leri için, EnableRemotePS betiğini çalıştırmak için portaldan Çalıştır komutlarını kullanın:

![Çalıştır Komutu](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>VM’ye bağlanma

İstemci bilgisayar konumuna bağlı olarak aşağıdaki komutu çalıştırın:

* VNET veya dağıtım dışında

  * Klasik bir VM için aşağıdaki komutu çalıştırın:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * ARM VM 'si için, önce genel IP adresine bir DNS adı ekleyin. Ayrıntılı adımlar için bkz. [WINDOWS VM için Azure Portal tam etki alanı adı oluşturma](../windows/portal-create-fqdn.md). Ardından şu komutu çalıştırın:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* VNET veya dağıtım içinde aşağıdaki komutu çalıştırın:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>SkipCaCheck bayrağını ayarlamak, oturumu başlattığınızda VM 'ye bir sertifikayı aktarma gereksinimini atlar.

Ayrıca, VM 'de uzaktan betik çalıştırmak için Invoke-Command cmdlet 'ini de kullanabilirsiniz:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Uzak kayıt defteri

>[!Note]
>Bu seçeneği kullanabilmek için TCP bağlantı noktası 135 veya 445 açık olmalıdır.
>
>ARM VM 'Leri için NSG 'de 5986 numaralı bağlantı noktasını açmanız gerekir. Daha fazla bilgi için bkz. güvenlik grupları. 
>
>RDFE VM 'Leri için, özel bağlantı noktası 5986 ve genel bağlantı noktası olan bir uç noktaya sahip olmanız gerekir. Ayrıca, NSG 'de genel kullanıma yönelik bağlantı noktasını da açmanız gerekir.

1. Aynı sanal ağ üzerindeki başka bir VM 'den, kayıt defteri Düzenleyicisi 'ni (Regedit. exe) açın.

2. **Dosya** >**ağ kayıt defterine bağla**' yı seçin.

   ![Uzak seçenek](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Hedef VM 'yi, **ana bilgisayar** adı veya **dinamik IP** (tercih edilecek nesne adını girin) kutusuna girerek bulun.

   ![Uzak seçenek](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Hedef VM 'nin kimlik bilgilerini girin.

5. Tüm gerekli kayıt defteri değişikliklerini yapın.

## <a name="remote-services-console"></a>Uzak hizmetler konsolu

>[!Note]
>Bu seçeneği kullanabilmek için 135 veya 445 TCP bağlantı noktaları açık olmalıdır.
>
>ARM VM 'Leri için NSG 'de 5986 numaralı bağlantı noktasını açmanız gerekir. Daha fazla bilgi için bkz. güvenlik grupları. 
>
>RDFE VM 'Leri için, özel bağlantı noktası 5986 ve genel bağlantı noktası olan bir uç noktaya sahip olmanız gerekir. Daha sonra, NSG 'de genel kullanıma yönelik bağlantı noktasını da açmanız gerekir.

1. Aynı sanal ağ üzerindeki başka bir VM 'den **Service. msc**' nin bir örneğini açın.

2. **Hizmetler (yerel)** öğesine sağ tıklayın.

3. **Başka bir bilgisayara bağlan**' ı seçin.

   ![Uzak hizmet](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Hedef VM 'nin dinamik IP 'sini girin.

   ![Giriş DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Hizmetlerde gerekli değişiklikleri yapın.

## <a name="next-steps"></a>Sonraki Adımlar

[-PSSession girin](https://technet.microsoft.com/library/hh849707.aspx)

[Klasik dağıtım modelini kullanarak Windows için özel Betik uzantısı](../extensions/custom-script-classic.md)

PsExec, [Padstools paketinin](https://download.sysinternals.com/files/PSTools.zip)bir parçasıdır.

Padstools Suite hakkında daha fazla bilgi için bkz. [Padstools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).


