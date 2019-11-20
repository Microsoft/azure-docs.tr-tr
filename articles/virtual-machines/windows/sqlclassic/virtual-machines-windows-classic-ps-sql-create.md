---
title: Klasik SQL Server VM oluşturma (PowerShell)
description: SQL Server sanal makine galeri görüntüleri ile Azure VM oluşturmak için adımlar ve PowerShell betikleri sağlar. Bu konu klasik dağıtım modunu kullanır.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 8757b634b76867a2d5ccce3dcfdc9d66ef25c25e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032728"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Azure PowerShell kullanarak SQL Server sanal makine sağlama (klasik)

Bu makalede, PowerShell cmdlet 'lerini kullanarak Azure 'da SQL Server sanal makine oluşturma adımları sağlanmaktadır.

> [!IMPORTANT] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir.

Bu konunun Kaynak Yöneticisi sürümü için bkz. [Azure PowerShell Kaynak Yöneticisi kullanarak SQL Server sanal makine sağlama](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>PowerShell 'i yükleyip yapılandırın:
1. Bir Azure hesabınız yoksa, [Azure ücretsiz deneme](https://azure.microsoft.com/pricing/free-trial/)yi ziyaret edin.
2. [En son Azure PowerShell komutlarını indirip yükleyin](/powershell/azure/overview).
3. Windows PowerShell 'i başlatın ve **Add-AzureAccount** komutuyla Azure aboneliğinize bağlayın.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Hedef Azure bölgenizi belirleme

SQL Server sanal makineniz, belirli bir Azure bölgesinin bulunduğu bir bulut hizmetinde barındırılır. Aşağıdaki adımlar, Bu öğreticinin geri kalanında kullanılacak bölge, depolama hesabı ve bulut hizmetinizi belirlemenize yardımcı olur.

1. SQL Server VM barındırmak için kullanmak istediğiniz veri merkezini saptayın. Aşağıdaki PowerShell komutu, kullanılabilir bölge adlarının bir listesini görüntüler.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Tercih ettiğiniz konumu tanımladıktan sonra bu bölgeye **$dcLocation** adlı bir değişken ayarlayın. Örneğin, aşağıdaki komut, bölgeyi "Doğu ABD" olarak ayarlar:

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Aboneliğinizi ve depolama hesabınızı ayarlama

1. Yeni sanal makine için kullanacağınız Azure aboneliğini belirleme.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Hedef Azure aboneliğinizi **$subscr** değişkenine atayın. Ardından bunu geçerli Azure aboneliğiniz olarak ayarlayın.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Ardından var olan depolama hesaplarını kontrol edin. Aşağıdaki betik, seçtiğiniz bölgede bulunan tüm depolama hesaplarını görüntüler:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Yeni bir depolama hesabına ihtiyacınız varsa, önce aşağıdaki örnekte olduğu gibi New-Azurestokımageaccount komutuyla bir tam-küçük harf depolama hesabı adı oluşturun: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Hedef depolama hesabı adını **$staccount**atayın. Ardından, abonelik ve geçerli depolama hesabını ayarlamak için **set-azuyeniden göndermeyi** kullanın.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>SQL Server sanal makine görüntüsü seçin

1. Galerideki kullanılabilir SQL Server sanal makine görüntülerinin listesini bulun. Bu görüntülerin hepsi, "SQL" ile başlayan bir **ımagefamily** özelliğine sahiptir. Aşağıdaki sorgu, SQL Server önceden yüklenmiş olarak kullanabileceğiniz görüntü ailesini görüntüler.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Sanal makine görüntü ailesini bulduğunuzda, bu ailede birden çok yayınlanan görüntü bulunabilir. Seçtiğiniz görüntü ailenizin en son yayımlanmış sanal makine görüntüsü adını bulmak için aşağıdaki betiği kullanın (örneğin, **Windows Server 2012 R2 'de SQL Server 2016 RTM Enterprise**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Sanal makineyi oluşturma

Son olarak, sanal makineyi PowerShell ile oluşturun:

1. Yeni VM 'yi barındırmak için bir bulut hizmeti oluşturun. Bunun yerine var olan bir bulut hizmetinin kullanılması da mümkün olduğunu unutmayın. Bulut hizmetinin kısa adı ile yeni bir değişken **$SVCNAME** oluşturun.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Sanal makine adını ve boyutunu belirtin. Sanal makine boyutları hakkında daha fazla bilgi için bkz. [Azure Için sanal makine boyutları](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Yerel yönetici hesabını ve parolayı belirtin.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Sanal makineyi oluşturmak için aşağıdaki betiği çalıştırın.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Daha fazla açıklama ve yapılandırma seçenekleri için, [Windows tabanlı sanal makineler oluşturmak ve önceden yapılandırmak üzere Azure PowerShell kullanılan](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) **komut kümesini oluşturma** bölümüne bakın.

## <a name="example-powershell-script"></a>Örnek PowerShell betiği

Aşağıdaki komut dosyası, **Windows Server 2012 R2 sanal makinesinde SQL Server 2016 RTM Enterprise** oluşturan bir komut dosyası örneği sağlar. Bu betiği kullanırsanız, bu konudaki önceki adımlara göre ilk değişkenleri özelleştirmeniz gerekir.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Uzak Masaüstü ile bağlantı kurmak

1. Kurulumu tamamlamaya yönelik bu sanal makineleri başlatmak için geçerli kullanıcının belge klasöründe RDP dosyalarını oluşturun:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. Belgeler dizininde, RDP dosyasını başlatın. Daha önce sağlanmış olan Yönetici Kullanıcı adı ve parolasıyla bağlantı sağlayın (örneğin, Kullanıcı adınız VMAdmin ise, Kullanıcı olarak "\VMAdmin" değerini belirtin ve parolayı girin).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Uzaktan erişim için SQL Server makinenin yapılandırmasını doldurun

Uzak Masaüstü ile makinede oturum açtıktan sonra, [Azure VM 'de SQL Server bağlantısını yapılandırma adımlarında](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)bulunan yönergelere göre SQL Server yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

Sanal [makineler belgelerinde](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)PowerShell ile sanal makine sağlamaya yönelik ek yönergeler bulabilirsiniz.

Birçok durumda, bir sonraki adım veritabanlarınızı bu yeni SQL Server VM geçirmeyecektir. Veritabanı geçiş kılavuzu için bkz. [Azure VM 'de bir veritabanını SQL Server geçirme](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Ayrıca, SQL sanal makineleri oluşturmak için Azure portal kullanmaya da ilgileniyorsanız, bkz. [Azure 'da SQL Server sanal makinesi sağlama](../sql/virtual-machines-windows-portal-sql-server-provision.md). Portalda size yol gösteren öğreticinin, bu PowerShell konusunda kullanılan klasik model yerine önerilen Kaynak Yöneticisi modelini kullanarak VM 'Ler oluşturduğunu unutmayın.

Bu kaynaklara ek olarak, [Azure sanal makinelerinde SQL Server çalıştırmaya ilişkin diğer konuları](../sql/virtual-machines-windows-sql-server-iaas-overview.md)incelemenizi öneririz.
