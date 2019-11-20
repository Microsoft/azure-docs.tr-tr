---
title: Azure Premium depolamayı SQL Server kullanma | Microsoft Docs
description: Bu makale, klasik dağıtım modeliyle oluşturulan kaynakları kullanır ve Azure sanal makinelerinde çalışan SQL Server Azure Premium Storage kullanma hakkında rehberlik sağlar.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f40b479b66f2fa9a60e084fc0e29f40cef052e99
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162536"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Sanal Makineler’de SQL Server ile Azure Premium Depolama kullanma

## <a name="overview"></a>Genel Bakış

[Azure Premium SSD 'ler](../disks-types.md) , düşük gecikme süresi ve yüksek aktarım hızı GÇ sağlayan yeni nesil depodur. IaaS [sanal makinelerinde](https://azure.microsoft.com/services/virtual-machines/)SQL Server gibi, önemli GÇ yoğun iş yükleri için en iyi şekilde kullanılır.

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, klasik dağıtım modelinin kullanımı ele alınmaktadır. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir.

Bu makalede, SQL Server çalıştıran bir sanal makineyi Premium Depolama kullanmak üzere geçirmeye yönelik planlama ve kılavuz sağlanmaktadır. Buna Azure altyapısı (ağ, depolama) ve Konuk Windows VM adımları dahildir. [Ek](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) içindeki örnekte, PowerShell ile IYILEŞTIRILMIŞ yerel SSD depolamadan yararlanmak üzere daha büyük VM 'lerin nasıl taşınacağı hakkında tam kapsamlı bir uçtan uca geçiş gösterilmektedir.

IAAS VM 'lerinde SQL Server Azure Premium Storage 'ın kullanılmasıyla ilgili uçtan uca işlemi anlamak önemlidir. Buna aşağıdakiler dahildir:

* Premium depolamayı kullanma ön koşullarını tanımlama.
* Yeni dağıtımlar için IaaS üzerinde SQL Server dağıtımı örnekleri Premium Depolama.
* SQL Always on kullanılabilirlik grupları kullanan tek başına sunucular ve dağıtımlar gibi mevcut dağıtımları geçirmeye yönelik örnekler.
* Olası geçiş yaklaşımları.
* Mevcut her zaman açık uygulamanın geçişine yönelik Azure, Windows ve SQL Server adımlarını gösteren tam uçtan uca örnek.

Azure sanal makineler 'de SQL Server ilgili daha fazla arka plan bilgisi için bkz. [Azure sanal makineler 'de SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Yazar:** Daniel Nuevo **Teknik gözden geçirenler:** Lusıs Carlos Vargaz Herhalka, Sanjay Mishra, Pravin mcar, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Premium Depolama önkoşulları

Premium Depolama kullanmak için birkaç önkoşul vardır.

### <a name="machine-size"></a>Makine boyutu

Premium Depolama kullanmak için DS serisi sanal makineleri (VM) kullanmanız gerekir. Bulut hizmetinizde daha önce DS serisi makineler kullanmadıysanız, mevcut VM 'yi silmeniz, eklenen diskleri silmeniz ve ardından VM 'yi DS * rol boyutu olarak yeniden oluşturmadan önce yeni bir bulut hizmeti oluşturmanız gerekir. Sanal makine boyutları hakkında daha fazla bilgi için bkz. [Azure Için sanal makine ve bulut hizmeti boyutları](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Bulut hizmetleri

DS * VM 'Leri, yeni bir bulut hizmetinde oluşturulduklarında Premium Depolama ile birlikte kullanabilirsiniz. Her zaman Azure 'da SQL Server kullanıyorsanız, her zaman açık dinleyici, bir bulut hizmetiyle ilişkili Azure Iç veya dış Load Balancer IP adresine başvurur. Bu makalede, bu senaryoda kullanılabilirliği koruyarak geçiş yapma konusu ele alınmaktadır.

> [!NOTE]
> DS * serisi, yeni bulut hizmetine dağıtılan ilk sanal makine olmalıdır.
>
>

### <a name="regional-vnets"></a>Bölgesel sanal ağlar

DS * VM 'leri için VM 'lerinizi barındıran sanal ağı (VNET) bölgesel olarak yapılandırmanız gerekir. Bu "widens" sanal ağı, daha büyük VM 'Lerin diğer kümelerde sağlanması ve aralarında iletişime izin vermek için kullanılır. Aşağıdaki ekran görüntüsünde, vurgulanan konum bölgesel sanal ağları gösterir, ancak ilk sonuç bir "dar" VNET gösterir.

![RegionalVNET][1]

Bölgesel VNET 'e geçiş yapmak için bir Microsoft destek bileti yükseltebilirsiniz. Microsoft daha sonra bir değişiklik yapar. Bölgesel sanal ağlara geçişi gerçekleştirmek için ağ yapılandırmasındaki AffinityGroup özelliğini değiştirin. Önce PowerShell 'deki ağ yapılandırmasını dışarı aktarın ve **Virtualnetworksite** öğesindeki **affinitygroup** özelliğini bir **Location** özelliği ile değiştirin. `XXXX` bir Azure bölgesi olan `Location = XXXX` belirtin. Ardından Yeni yapılandırmayı içeri aktarın.

Örneğin, aşağıdaki VNET yapılandırmasını göz önünde bulundurarak:

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

Bunu Batı Avrupa bir bölgesel VNET 'e taşımak için, yapılandırmayı aşağıdaki şekilde değiştirin:
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Depolama hesapları

Premium Depolama için yapılandırılmış yeni bir depolama hesabı oluşturmanız gerekir. Premium depolamanın kullanılması, tek tek VHD 'lerde değil, depolama hesabında ayarlanır, ancak DS * serisi VM kullanırken VHD 'leri Premium ve standart depolama hesaplarından iliştirebilirsiniz. İşletim sistemi VHD 'sini Premium depolama hesabına eklemek istemiyorsanız bunu düşünebilirsiniz.

"Premium_LRS" **türüne** sahip aşağıdaki **New-Azurestokıgeaccountpowershell** komutu bir Premium depolama hesabı oluşturur:

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>VHD önbellek ayarları

Bir Premium depolama hesabının parçası olan diskler oluşturma arasındaki temel fark, disk önbelleği ayarıdır. SQL Server veri birimi disklerinde '**Read Caching**' kullanmanız önerilir. Işlem günlüğü birimleri için disk önbelleği ayarı '**none**' olarak ayarlanmalıdır. Bu, standart depolama hesapları önerilerinden farklıdır.

VHD 'ler iliştirildikten sonra önbellek ayarı değiştirilemez. VHD 'YI, güncelleştirilmiş bir önbellek ayarıyla ayırıp yeniden eklemeniz gerekir.

### <a name="windows-storage-spaces"></a>Windows depolama alanları

Önceki standart depolamayla yaptığınız gibi [Windows depolama alanlarını](https://technet.microsoft.com/library/hh831739.aspx) kullanabilirsiniz. Bu, zaten depolama alanları kullanan bir VM 'yi geçirmenize olanak sağlar. [Ek](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) içindeki örnek (9. adım ve ileri), birden çok bağlı VHD 'ye sahip bir VM 'yi ayıklamak ve içeri aktarmak için PowerShell kodunu gösterir.

Depolama havuzları, standart Azure depolama hesabıyla üretilen iş üretimini geliştirmek ve gecikmeyi azaltmak için kullanılır. Yeni dağıtımlar için Premium Depolama ile depolama havuzlarını test etme bölümünde değeri bulabilirsiniz, ancak depolama kurulumuna ek karmaşıklık ekler.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Hangi Azure sanal disklerinin depolama havuzlarına eşlendiğini bulma

Ekli VHD 'ler için farklı önbellek ayarı önerileri olduğundan, VHD 'leri bir Premium depolama hesabına kopyalamaya karar verebilirsiniz. Ancak, bunları yeni DS serisi VM 'sine yeniden iliştirmeniz durumunda önbellek ayarlarını değiştirmeniz gerekebilir. SQL veri dosyaları ve günlük dosyaları için ayrı VHD 'ler (her ikisini de içeren tek bir VHD yerine) varsa, Premium Depolama önerilen önbellek ayarlarını uygulamak daha basittir.

> [!NOTE]
> Aynı birimde SQL Server veri ve günlük dosyalarınız varsa, seçtiğiniz önbelleğe alma seçeneği, veritabanı iş yükleriniz için GÇ erişim düzenlerine bağlıdır. Yalnızca test, bu senaryo için hangi önbelleğe alma seçeneğinin en iyi olduğunu gösterir.
>
>

Ancak, birden çok VHD 'den oluşan Windows depolama alanları kullanıyorsanız, hangi bağlı VHD 'Lerin belirli bir havuzda olduğunu belirlemek için özgün betiklerinize bakmanız gerekir, böylece önbellek ayarlarını her bir disk için uygun şekilde ayarlayabilirsiniz.

Depolama havuzuna hangi VHD 'Lerin eşlendiğini göstermek için kullanılabilir özgün komut dosyası yoksa, disk/depolama havuzu eşlemesini öğrenmek için aşağıdaki adımları kullanabilirsiniz.

Her disk için aşağıdaki adımları kullanın:

1. **Get-AzureVM** komutuyla VM 'ye bağlı disklerin listesini alın:

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. DiskName ve LUN 'A göz önünde edin.

    ![DisknameAndLUN][2]
1. SANAL makineye Uzak Masaüstü. Daha sonra | **disk sürücülerine** **Aygıt Yöneticisi** **Bilgisayar Yönetimi** | gidin. ' Microsoft sanal disklerinin ' her birinin özelliklerine bakın

    ![VirtualDiskProperties][3]
1. Burada LUN numarası, VHD 'yi VM 'ye eklerken belirttiğiniz LUN numarasına bir başvurudur.
1. ' Microsoft sanal disk ' için **Ayrıntılar** sekmesine gidin ve ardından **özellik** listesinde **Sürücü anahtarı**' na gidin. **Değerde**, aşağıdaki ekran görüntüsünde 0,0002 olan **sapmayı**aklınızda bulunur. 0,0002, depolama havuzunun başvurduğu PhysicalDisk2 öğesini gösterir.

    ![VirtualDiskPropertyDetails][4]
1. Her depolama havuzu için, ilişkili disklerin dökümünü alın:

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

Artık bu bilgileri, bağlı VHD 'leri depolama havuzlarındaki fiziksel disklerle ilişkilendirmek için kullanabilirsiniz.

VHD 'leri depolama havuzlarında fiziksel disklere eşleştirdikten sonra, bunları ayırabilir ve bir Premium depolama hesabına kopyalayabilir ve ardından doğru önbellek ayarıyla iliştirebilirsiniz. [Ek](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)içindeki örneğe bakın, 8 ile 12 arasındaki adımları izleyin. Bu adımlarda, VM 'ye bağlı bir VHD disk yapılandırmasını bir CSV dosyasına ayıklama, VHD 'leri kopyalama, disk yapılandırma önbelleği ayarlarını değiştirme ve son olarak VM 'yi tüm ekli disklere sahip bir DS serisi VM olarak yeniden dağıtma gösterilmektedir.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM depolama bant genişliği ve VHD depolama verimlilik

Depolama performansı miktarı, belirtilen DS * VM boyutuna ve VHD boyutlarına bağlıdır. VM 'lerin iliştirilebilecek VHD sayısı ve destekledikleri maksimum bant genişliği (MB/s) için farklı izinleri vardır. Belirli bant genişliği numaraları için bkz. [Azure Için sanal makine ve bulut hizmeti boyutları](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Daha büyük disk boyutları ile artan ıOPS elde edilir. Geçiş yolunuzu düşünürken bunu dikkate almalısınız. Ayrıntılar için, [IOPS ve disk türleri tablosuna bakın](../disks-types.md#premium-ssd).

Son olarak, VM 'Lerin, eklenen tüm diskler için destekledikleri farklı disk bant genişliğine sahip olduğunu göz önünde bulundurun. Yüksek yük altında, bu VM rolü boyutu için kullanılabilir maksimum disk bant genişliğini gri bir şekilde görüntüleyebilirsiniz. Örneğin, bir Standard_DS14 en fazla 512MB/sn destekler; Bu nedenle, üç P30 disk ile sanal makinenin disk bant genişliğini doygunluğu izleyebilirsiniz. Ancak bu örnekte, okuma ve yazma IOs karışımına bağlı olarak aktarım hızı sınırı aşılamaz.

## <a name="new-deployments"></a>Yeni dağıtımlar

Sonraki iki bölüm, Premium depolamaya SQL Server VM 'Leri nasıl dağıtabileceğinizi göstermektedir. Daha önce bahsedildiği gibi, işletim sistemi diskini Premium depolamaya yerleştirmeniz gerekmez. İşletim sistemi VHD 'sine yoğun GÇ iş yüklerini eklemek istiyorsanız bunu yapabilirsiniz.

İlk örnek, mevcut Azure Galeri görüntülerinin nasıl kullanıldığını gösterir. İkinci örnekte, mevcut bir standart depolama hesabında bulunan özel bir sanal makine görüntüsünün nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
> Bu örneklerde, zaten bölgesel VNET oluşturmuş olduğunuz varsayılmaktadır.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Galeri görüntüsünde Premium Depolama ile yeni bir VM oluşturma

Aşağıdaki örnekte, işletim sistemi VHD 'sinin Premium Depolama 'ya nasıl yerleştirileceği ve Premium Depolama VHD 'lerinin nasıl ekleneceği gösterilmektedir. Ancak, işletim sistemi diskini standart bir depolama hesabına yerleştirebilir ve ardından bir Premium Depolama hesabında bulunan VHD 'leri ekleyebilirsiniz. Her iki senaryo da gösterilmiştir.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>1\. Adım: Premium depolama hesabı oluşturma

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>2\. Adım: yeni bir bulut hizmeti oluşturma

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>3\. Adım: bulut hizmeti VIP ayırma (Isteğe bağlı)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = "sqlcloudVIP"
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>4\. Adım: VM kapsayıcısı oluşturma

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>5\. Adım: işletim sistemi VHD 'sini standart veya Premium depolamaya yerleştirme

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>6\. Adım: VM oluşturma

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Özel bir görüntüyle Premium Depolama kullanmak için yeni bir VM oluşturma

Bu senaryo, standart bir depolama hesabında bulunan özelleştirilmiş görüntülerin nerede olduğunu gösterir. İşletim sistemi VHD 'sini Premium depolamaya eklemek istiyorsanız, standart depolama hesabında bulunan görüntüyü kopyalamanız ve kullanılmadan önce bunları bir Premium depolamaya aktarmanız gerekir. Şirket içi bir görüntünüz varsa, bu yöntemi doğrudan Premium depolama hesabına kopyalamak için de kullanabilirsiniz.

#### <a name="step-1-create-storage-account"></a>1\. Adım: depolama hesabı oluşturma

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>2\. adım bulut hizmeti oluşturma

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>3\. Adım: var olan görüntüyü kullanma

Var olan bir görüntü kullanabilirsiniz. Ya da [var olan bir makinenin görüntüsünü](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)alabilirsiniz. Görüntü yaptığınız makinenin DS * makine olması gerekmediğini unutmayın. Görüntüye sahip olduktan sonra aşağıdaki adımlarda, **Start-Azurestokıgeblobcopy** PowerShell Komutlet Ile Premium depolama hesabına nasıl kopyalanacağı gösterilmektedir.

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>4\. Adım: depolama hesapları arasında blobu kopyalama

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>5\. Adım: düzenli olarak kopyalama durumunu kontrol edin:

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>6\. Adım: abonelik içindeki Azure disk deposuna görüntü diski ekleme

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> Durum başarılı olarak raporlamasa da, yine de disk kira hatası alabilirsiniz. Bu durumda, yaklaşık 10 dakika bekleyin.

#### <a name="step-7--build-the-vm"></a>7\. Adım: VM oluşturma

Burada sanal makineyi yansıınızdan oluşturuyor ve iki Premium Depolama VHD 'si iliştiriliyor:

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!"


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Always on kullanılabilirlik grupları kullanmayan mevcut dağıtımlar

> [!NOTE]
> Mevcut dağıtımlar için öncelikle bu makalenin [Önkoşullar](#prerequisites-for-premium-storage) bölümüne bakın.

Her zaman açık kullanılabilirlik grupları ve bunları kullanmayan SQL Server dağıtımlar için farklı hususlar vardır. Always on 'U kullanmıyorsanız ve mevcut bir tek başına SQL Server sahipseniz, yeni bir bulut hizmeti ve depolama hesabı kullanarak Premium depolamaya yükseltebilirsiniz. Aşağıdaki seçenekleri göz önünde bulundurun:

* **Yeni bir SQL Server VM oluşturun**. Yeni dağıtımlarda belgelendiği gibi, Premium depolama hesabı kullanan yeni bir SQL Server VM oluşturabilirsiniz. Ardından SQL Server yapılandırmanızı ve Kullanıcı veritabanlarınızı yedekleyin ve geri yükleyin. Uygulamanın dahili veya harici olarak erişildiği yeni SQL Server başvuracak şekilde güncelleştirilmesi gerekir. Bir yan yana (SxS) SQL Server geçişi yapıyor gibi tüm ' veritabanı dışı ' nesnelerini kopyalamanız gerekir. Bu, oturum açmalar, sertifikalar ve bağlı sunucular gibi nesneleri içerir.
* **Mevcut bir SQL Server VM geçirin**. Bu, SQL Server VM çevrimdışına alınması ve sonra tüm bağlı VHD 'Lerin Premium depolama hesabına kopyalanmasını içeren yeni bir bulut hizmetine aktarılmasının gerekli olmasını gerektirir. VM çevrimiçi olduğunda, uygulama daha önce olduğu gibi sunucu ana bilgisayar adına başvurur. Mevcut diskin boyutunun performans özelliklerini etkilediğine dikkat edin. Örneğin, 400 GB bir disk bir P20 yukarı yuvarlanır. Disk performansının gerekli olmadığını biliyorsanız, VM 'yi bir DS serisi VM olarak yeniden oluşturabilir ve ihtiyacınız olan boyut/performans belirtiminin Premium Depolama VHD 'lerini iliştirebilirsiniz. Ardından SQL DB dosyalarını ayırabilir ve yeniden iliştirebilirsiniz.

> [!NOTE]
> VHD disklerini kopyalarken, boyutuna bağlı olarak boyutu bilmeniz gerekir. Bu, disk performans belirtimini belirler. Azure en yakın disk boyutuna yuvarlar, bu nedenle bir 400 diskiniz varsa, bu bir P20 yukarı yuvarlanır. İşletim sistemi VHD 'sinin mevcut GÇ gereksinimlerine bağlı olarak, bunu bir Premium depolama hesabına geçirmeniz gerekebilir.

SQL Server dışarıdan erişiliyorsa, bulut hizmeti VIP 'si değişir. Ayrıca bitiş noktaları, ACL 'Ler ve DNS ayarlarını da güncelleştirmeniz gerekir.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Always on kullanılabilirlik grupları kullanan mevcut dağıtımlar

> [!NOTE]
> Mevcut dağıtımlar için öncelikle bu makalenin [Önkoşullar](#prerequisites-for-premium-storage) bölümüne bakın.

Başlangıçta bu bölümde Azure ağ ile her zaman nasıl etkileşime gireceğini inceleyeceğiz. Daha sonra geçişleri iki senaryoya göre ayırdık: bazı kapalı kalma süresinin toleranslı olduğu ve en az kapalı kalma süresine ulaşmanız gereken geçişlerde geçiş yapılabilir.

Şirket içi SQL Server Always on kullanılabilirlik grupları, bir sanal DNS adını bir veya daha fazla SQL sunucusu arasında paylaşılan bir IP adresi ile birlikte kaydeden şirket içinde bir dinleyici kullanır. İstemciler bağlandığında, dinleyici IP 'si aracılığıyla birincil SQL Server yönlendirilir. Bu, o anda her zaman açık IP kaynağına sahip olan sunucusudur.

![DeploymentsUseAlways on][6]

Microsoft Azure, VM 'deki bir NIC 'ye atanmış tek bir IP adresiniz olabilir, bu nedenle şirket içi olarak aynı soyutlama katmanını elde etmek için Azure Iç/dış yük dengeleyicileri (ıLB/ELB) atanan IP adresini kullanır. Sunucular arasında paylaşılan IP kaynağı ıLB/ELB ile aynı IP 'ye ayarlanır. Bu, DNS 'de yayımlanır ve istemci trafiği ıLB/ELB aracılığıyla birincil SQL Server çoğaltmaya geçirilir. ILB/ELB, her zaman açık IP kaynağını yoklamaları için araştırmaları kullandığından SQL Server birincil olduğunu bilir. Önceki örnekte, ELB/ıLB tarafından başvurulan bir uç nokta olan her düğümü yoklamıştır ve bu, yanıt veren birincil SQL Server.

> [!NOTE]
> ILB ve ELB 'nin her ikisi de belirli bir Azure bulut hizmetine atanır, bu nedenle Azure 'daki tüm bulut geçişi Load Balancer IP 'nin değiştiği anlamına gelir.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Bazı kapalı kalma süresine izin veren her zaman dağıtımda geçiş

Her zaman kapalı kalma süresine izin veren dağıtımlara geçiş yapmak için iki strateji vardır:

1. **Mevcut her zaman açık bir kümeye daha fazla ikincil çoğaltma ekleyin**
2. **Yeni bir Always on kümesine geçirme**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. var olan her zaman kümesine daha fazla Ikincil çoğaltma ekleyin

Tek strateji, her zaman açık kullanılabilirlik grubuna daha fazla ikincil eklemektir. Bunları yeni bir bulut hizmetine eklemeniz ve dinleyiciyi yeni yük dengeleyici IP 'si ile güncelleştirmeniz gerekir.

##### <a name="points-of-downtime"></a>Kesinti süresi noktaları:

* Küme doğrulaması.
* Yeni Ikincil öğeler için her zaman yük devretme testi.

VM 'de daha yüksek GÇ işleme için Windows depolama havuzları kullanıyorsanız, bunlar tam küme doğrulaması sırasında çevrimdışına alınır. Kümeye düğüm eklediğinizde doğrulama testi gerekir. Testin çalıştırılması için gereken süre farklılık gösterebilir. bu nedenle, bunun ne kadar süreceği hakkında yaklaşık bir zaman almak için bunu temsilci test ortamınızda test etmeniz gerekir.

Her zaman yüksek kullanılabilirlik işlevlerinin beklendiğinden emin olmak için yeni eklenen düğümlerde el ile yük devretme ve Chaos testi gerçekleştirebileceğiniz zamanı sağlamanız gerekir.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Doğrulama çalışmadan önce depolama havuzlarının kullanıldığı tüm SQL Server örneklerini durdurmanız gerekir.
>
> ##### <a name="high-level-steps"></a>Üst düzey adımlar
>

1. Yeni bulut hizmetinde, ekli Premium Depolama ile iki yeni SQL sunucusu oluşturun.
2. TAM yedeklemeler üzerine kopyalayın ve **norecovery**ile geri yükleyin.
3. ' Kullanıcı dışı ' VERITABANıNA bağımlı nesneleri (örneğin, oturum açma vb.) kopyalayın.
4. Yeni bir Iç Load Balancer (ıLB) oluşturun veya bir dış Load Balancer (ELB) kullanın ve ardından hem yeni düğümlerde yük dengeli uç noktaları ayarlayın.

   > [!NOTE]
   > Devam etmeden önce tüm düğümlerin doğru uç nokta yapılandırmasına sahip olup olmadığını denetleyin
   >
   >
5. SQL Server Kullanıcı/uygulama erişimini durdurun (depolama havuzları kullanılıyorsa).
6. Tüm düğümlerde SQL Server Engine hizmetlerini durdurun (depolama havuzları kullanılıyorsa).
7. Kümeye yeni düğümler ekleyin ve tam doğrulamayı çalıştırın.
8. Doğrulama başarılı olduktan sonra tüm SQL Server hizmetlerini başlatın.
9. Işlem günlüklerini yedekleme ve Kullanıcı veritabanlarını geri yükleme.
10. Always on kullanılabilirlik grubuna yeni düğümler ekleyin ve çoğaltmayı **zaman uyumlu**olarak yerleştirin.
11. [Ek](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)içindeki çoklu site örneğine göre her zaman Için yeni bulut hizmeti ILB/elb 'nin IP adresi kaynağını her zaman açık öğesine ekleyin. Windows Kümeleme 'de, **IP adresi** kaynağının **olası sahiplerini** yeni düğümleri eski olacak şekilde ayarlayın. [Ek konusunun](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)' aynı alt ağda IP adresi kaynağı ekleme ' bölümüne bakın.
12. Yeni düğümlerden birine yük devretme.
13. Yeni düğümleri otomatik yük devretme ortakları yapın ve yük devretmeyi test edin.
14. Özgün düğümleri kullanılabilirlik grubundan kaldırın.

##### <a name="advantages"></a>Üstünlü

* Yeni SQL Server 'Lar, her zaman açık öğesine eklenmeden önce test edilebilir (SQL Server ve uygulama).
* VM boyutunu değiştirebilir ve depolama alanını tam gereksinimlerinize göre özelleştirebilirsiniz. Ancak, tüm SQL dosya yollarını aynı tutmak yararlı olacaktır.
* VERITABANı yedeklemelerinin Ikincil çoğaltmalara aktarımını ne zaman başlatıldığını denetleyebilirsiniz. Bu, zaman uyumsuz bir kopya olduğundan, VHD 'leri kopyalamak için Azure **Start-Azurestokgeblobcopy** komutunu kullanmaktan farklıdır.

##### <a name="disadvantages"></a>Olumsuz

* Windows Storage havuzlarını kullanırken, yeni ek düğümlerin tam küme doğrulaması sırasında küme kapalı kalma süresi vardır.
* SQL Server sürümüne ve var olan ikincil çoğaltmalara bağlı olarak, var olan ikincilleri kaldırmadan daha fazla ikincil çoğaltma ekleyemeyebilirsiniz.
* İkincilleri ayarlarken uzun SQL veri aktarım süresi olabilir.
* Paralel olarak çalışan yeni makinelere sahip olduğunuzda geçiş sırasında ek maliyet vardır.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. yeni bir Always on kümesine geçirin

Başka bir strateji, yeni bulut hizmetinde yepyeni yeni düğümlerle yeni, her zaman yeni bir küme oluşturmak ve ardından istemcileri kullanmak için yeniden yönlendirmelidir.

##### <a name="points-of-downtime"></a>Kesinti süresi noktaları

Uygulamaları ve kullanıcıları yeni her zaman açık dinleyiciye aktardığınızda kapalı kalma süresi vardır. Kapalı kalma süresi şunlara bağlıdır:

* Yeni sunuculardaki veritabanlarına son işlem günlüğü yedeklemelerini geri yüklemek için geçen süre.
* İstemci uygulamalarının yeni Always on dinleyicisi kullanması için güncelleştirilmesi için geçen süre.

##### <a name="advantages"></a>Üstünlü

* Gerçek üretim ortamını, SQL Server ve işletim sistemi yapı değişikliklerini test edebilirsiniz.
* Depolama alanını özelleştirme ve VM boyutunu potansiyel olarak azaltma seçeneğiniz vardır. Bu, maliyet azalmasına neden olabilir.
* Bu işlem sırasında SQL Server yapınızı veya sürümünüzü güncelleştirebilirsiniz. Işletim sistemini de yükseltebilirsiniz.
* Önceki her zaman açık küme, bir katı geri alma hedefi işlevi görebilir.

##### <a name="disadvantages"></a>Olumsuz

* Her ikisinin de her zaman aynı anda çalışmasını istiyorsanız, dinleyicinin DNS adını değiştirmeniz gerekir. Bu, geçiş sırasında yönetim yükünü ekleyerek istemci uygulama dizelerinin yeni dinleyici adını yansıtması gerekir.
* Geçiş işleminden önce son eşitleme gereksinimlerini en aza indirmek için, iki ortam arasında bir eşitleme mekanizması uygulamanız gerekir.
* Yeni ortamınız çalışırken geçiş sırasında bir maliyet eklenmiştir.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>En az kapalı kalma süresi için her zaman dağıtımları geçirme

En az kapalı kalma süresi için her zaman dağıtımda geçiş yapmak üzere iki strateji vardır:

1. **Mevcut bir Ikincil siteyi kullanın: tek siteli**
2. **Mevcut Ikincil çoğaltmaları kullanın: çok siteli**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. var olan bir ikincil siteyi kullanın: tek siteli

En az kapalı kalma süresine yönelik bir strateji, var olan bir bulut ikincisinden çıkar ve bunu geçerli bulut hizmetinden kaldırır. Ardından VHD 'leri yeni Premium depolama hesabına kopyalayın ve yeni bulut hizmetinde VM 'yi oluşturun. Ardından, kümeleme ve yük devretme bölümünde dinleyiciyi güncelleştirin.

##### <a name="points-of-downtime"></a>Kesinti süresi noktaları

* Son düğümü yük dengeli bitiş noktasıyla güncelleştirdiğinizde kapalı kalma süresi vardır.
* İstemci yeniden bağlanma, istemci/DNS yapılandırmanıza bağlı olarak geciktirilebilir.
* IP adreslerini değiştirmek için her zaman açık küme grubunu çevrimdışı duruma getirme seçeneğini belirlerseniz ek kapalı kalma süresi vardır. Bu durumu, eklenen IP adresi kaynağı için bir veya bağımlılığı ve olası sahipleri kullanarak önleyebilirsiniz. [Ek konusunun](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)' aynı alt ağda IP adresi kaynağı ekleme ' bölümüne bakın.

> [!NOTE]
> Eklenen düğümün, her zaman açık yük devretme ortağı olarak içinde olmasını istediğinizde, yük dengeli kümeye yönelik bir başvuruya sahip bir Azure Uç Noktası eklemeniz gerekir. Bunu yapmak için **Add-AzureEndpoint** komutunu çalıştırdığınızda geçerli bağlantılar açık kalır, ancak yük dengeleyici güncelleştirilene kadar dinleyiciye yeni bağlantılar kurulayamaz. Bu test sırasında son 90-120saniyeye görüldü, bu test edilmelidir.

##### <a name="advantages"></a>Üstünlü

* Geçiş sırasında hiçbir ek maliyet tahakkuk etilmedi.
* Bire bir geçiş.
* Azaltılan karmaşıklık.
* Premium Depolama SKU 'larından daha fazla ıOPS sağlar. Diskler VM 'den ayrıldıktan ve yeni bulut hizmetine kopyalandıklarında, daha yüksek bir yük sağlayan VHD boyutunu artırmak için 3. taraf bir araç kullanılabilir. VHD boyutlarını artırmak için bu [Forum tartışmasına](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)bakın.

##### <a name="disadvantages"></a>Olumsuz

* Geçiş sırasında bir HA ve DR geçici kaybı vardır.
* Bu bir 1:1 geçişi olduğundan, sanal makinelerinizi destekleyen en az bir VM boyutu kullanmanız gerekir, bu nedenle VM 'lerinizi daha düşük bir boyuta geçiremeyebilirsiniz.
* Bu senaryo, zaman uyumsuz olan Azure **Start-Azurestokgeblobcopy** commandlet 'i kullanır. Kopyalama tamamlandığında SLA yoktur. Kopyaların zaman içindeki verileri farklılık gösterir, ancak bu, sıradaki beklemeye bağlı olsa da aktarılacak veri miktarına bağlıdır. Aktarım, başka bir bölgedeki Premium depolamayı destekleyen başka bir Azure veri merkezine gidiyor ise kopyalama zamanı artar. Yalnızca 2 düğümünüz varsa, kopyanın test ediden daha uzun sürmemesi durumunda olası bir hafifletme düşünün. Bu, aşağıdaki fikirleri içerebilir.
  * Üzerinde anlaşılan kapalı kalma süresi ile geçişten önce HA için geçici bir 3. SQL Server düğümü ekleyin.
  * Geçişi Azure zamanlanmış bakım dışında çalıştırın.
  * Küme çekirdeğini doğru yapılandırdığınızdan emin olun.  

##### <a name="high-level-steps"></a>Üst düzey adımlar

Bu belge, tamamlanmış bir uçtan uca örneği göstermez, ancak [ek](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) , bunu gerçekleştirmek için yararlanılabilir olabilecek ayrıntılar sağlar.

![En düşük kapalı kalma süresi][8]

* Disk yapılandırması toplayın ve düğümü kaldırın (ekli VHD 'leri silmeyin).
* Premium depolama hesabı oluşturma ve VHD 'leri standart depolama hesabından kopyalama
* Yeni bulut hizmeti oluşturun ve bu bulut hizmetinde SQL2 sanal makinesini yeniden dağıtın. Kopyalanmış orijinal işletim sistemi VHD 'sini kullanarak ve kopyalanmış VHD 'leri ekleyerek VM 'yi oluşturun.
* ILB/ELB 'yi yapılandırın ve uç noktaları ekleyin.
* Dinleyiciyi aşağıdakilerden biriyle güncelleştir:
  * Her zaman açık grubunu çevrimdışı duruma getirme ve her zaman açık dinleyiciyi yeni ıLB/ELB IP adresiyle güncelleştirme.
  * Ya da yeni bulut hizmeti ve ELB 'nin IP adresi kaynağını Windows kümelemesine ekleme. Ardından IP adresi kaynağının olası sahiplerini geçirilmiş düğüm, SQL2 olarak ayarlayın ve bunu ağ adında veya bağımlılığı olarak ayarlayın. [Ek konusunun](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)' aynı alt ağda IP adresi kaynağı ekleme ' bölümüne bakın.
* İstemcilere DNS yapılandırmasını/yaymayı kontrol edin.
* SQL1 VM 'yi geçirin ve 2 – 4. adımları uygulayın.
* 5 II adımları kullanılıyorsa, eklenen IP adresi kaynağı için olası bir sahip olarak SQL1 ekleyin
* Yük devretme sınamasını yapın.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. var olan ikincil çoğaltmaları kullanın: çoklu site

Birden fazla Azure veri merkezinde (DC) düğümünüz varsa veya karma ortamınız varsa, kapalı kalma süresini en aza indirmek için bu ortamdaki her zaman açık yapılandırma kullanabilirsiniz.

Bu yaklaşım, şirket içi veya ikincil Azure DC için Always on eşitlemesini zaman uyumlu olarak değiştirmek ve sonra bu SQL Server yük devretmeye yöneliktir. Ardından VHD 'leri bir Premium depolama hesabına kopyalayın ve makineyi yeni bir bulut hizmetine yeniden dağıtın. Dinleyiciyi güncelleştirin ve ardından yeniden devreder.

##### <a name="points-of-downtime"></a>Kesinti süresi noktaları

Kapalı kalma süresi, alternatif DC ve geri yük devretme süresinden oluşur. Ayrıca, istemci/DNS yapılandırmanıza ve istemci yeniden bağlanınıza göre geciktirilebilir.
Hibrit her zaman açık yapılandırma örneğini göz önünde bulundurun:

![MultiSite1][9]

##### <a name="advantages"></a>Üstünlü

* Mevcut altyapıya yararlanabilirsiniz.
* Önce DR Azure DC 'de Azure Storage 'ı yükseltme seçeneğiniz vardır.
* DR Azure DC depolaması yeniden yapılandırılabilir.
* Geçiş sırasında yük devretme testi hariç en az iki yük devretme işlemi vardır.
* SQL Server verilerini yedekleme ve geri yükleme ile taşımanız gerekmez.

##### <a name="disadvantages"></a>Olumsuz

* SQL Server yönelik istemci erişimine bağlı olarak, SQL Server uygulamaya alternatif bir DC 'de çalışırken daha fazla gecikme olabilir.
* VHD 'lerin kopyalama süresi Premium depolamaya uzun olabilir. Bu, düğümün kullanılabilirlik grubunda tutulup tutulmayacağını etkileyebilecek kararlarınızı etkileyebilir. Birincil düğümün, çoğaltılan işlemleri işlem günlüğünde tutması gerektiğinden, geçiş sırasında oturum yoğunluğu yoğun iş yüklerinin ne zaman çalıştığını göz önünde bulundurun. Bu nedenle bu, önemli ölçüde büyüyebilir.
* Bu senaryo, zaman uyumsuz olan Azure **Start-Azurestokgeblobcopy** commandlet 'i kullanır. Tamamlanmayı tamamlamak için SLA yoktur. Kopyaların zaman içinde değişiklik gösterdiği için, bu, sıradaki beklemeye bağlı olsa da, aktarılacak veri miktarına bağlıdır. Bu nedenle, 2. veri merkezinizde yalnızca bir düğümünüz olması, kopyalamanın test ediden daha uzun sürmemesi durumunda risk azaltma adımlarını uygulamanız gerekir. Bu risk azaltma adımları aşağıdaki fikirleri içerir:
  * Üzerinde anlaşılan kapalı kalma süresi ile geçişten önce HA için geçici bir 2. SQL düğümü ekleyin.
  * Geçişi Azure zamanlanmış bakım dışında çalıştırın.
  * Küme çekirdeğini doğru yapılandırdığınızdan emin olun.

Bu senaryoda, yüklemenizi belgelendiğinizi ve en iyi disk önbelleği ayarları için değişiklik yapmak üzere depolamanın nasıl eşlendiğini bildiğiniz varsayılır.

##### <a name="high-level-steps"></a>Üst düzey adımlar

![Multisite2][10]

* Şirket içi/alternatif Azure DC 'yi SQL Server birincil yapın ve diğer otomatik yük devretme ortağı (AFP) yapın.
* SQL2 adresinden disk yapılandırma bilgilerini toplayın ve düğümü kaldırın (ekli VHD 'leri silmeyin).
* Bir Premium depolama hesabı oluşturun ve VHD 'leri standart depolama hesabından kopyalayın.
* Yeni bir bulut hizmeti oluşturun ve SQL2 VM 'sini, prim depolama disklerinin eklendiği şekilde oluşturun.
* ILB/ELB 'yi yapılandırın ve uç noktaları ekleyin.
* Yeni ıLB/ELB IP adresi ve test yük devretmesi ile her zaman açık dinleyiciyi güncelleştirin.
* DNS yapılandırmasını denetleyin.
* AFP öğesini SQL2 olarak değiştirin ve ardından SQL1 geçirin ve adım 2 – 5 ' e geçin.
* Yük devretme sınamasını yapın.
* AFP geri SQL1 ve SQL2 olarak değiştirme

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Ek: çoklu site her zaman kümesini Premium depolamaya geçirme

Bu makalenin geri kalanı, çok siteli her zaman açık kümeyi Premium depolamaya dönüştürmeye ilişkin ayrıntılı bir örnek sağlar. Ayrıca, bir dış yük dengeleyici (ELB) kullanarak bir iç yük dengeleyiciye (ıLB) da dinleyiciyi dönüştürür.

### <a name="environment"></a>Ortam

* Windows 2k12/SQL 2k12
* SP üzerinde 1 DB dosyası
* Düğüm başına 2 x depolama havuzu

![Appendix1][11]

### <a name="vm"></a>'Nın

Bu örnekte, bir ELB 'den ıLB 'ye geçmeyi inceleyeceğiz. ELB ıLB 'den önce kullanılabilir, bu nedenle geçiş sırasında ıLB 'ye nasıl geçiş yapılacağını gösterir.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Önceki adımlar: aboneliğe bağlanın

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>1\. Adım: yeni depolama hesabı ve bulut hizmeti oluşturma

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>2\. Adım: Isteğe bağlı \<kaynaklar üzerinde izin verilen başarısızlıklarını artırın >

Her zaman açık kullanılabilirlik grubunuza ait olan belirli kaynaklarda, küme hizmetinin kaynak grubunu yeniden başlatmaya çalıştığı, bir dönemde oluşabilecek hataların sayısı sınırlar vardır. Bu yordamda ilerlemeniz önerilir. bu şekilde, makineyi kapatarak yük devretmeleri el ile gerçekleştirerek ve tetikleyemezseniz bu sınıra kadar kapatabilirsiniz.

Hata indirimini ikiye katmaya devam ediyor, bunu Yük Devretme Kümesi Yöneticisi yapmak için Always on kaynak grubunun özelliklerine gidin:

![Appendix3][13]

En yüksek başarısızlık sayısını 6 olarak değiştirin.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>3\. Adım: küme grubu için IP adresi kaynağı ekleme \<Isteğe bağlı >

Küme grubu için yalnızca bir IP adresiniz varsa ve bu bulut alt ağına hizalanmışsa, bu ağ üzerinde bulutta tüm küme düğümlerini yanlışlıkla çevrimdışı duruma getirdiğinizde, küme IP kaynağı ve küme ağı adı çevrimiçi duruma gelmeyecektir. Bu durumda, diğer küme kaynaklarının güncelleştirilmesini engeller.

#### <a name="step-4-dns-configuration"></a>4\. Adım: DNS yapılandırması

Kesintisiz geçiş uygulamak, DNS 'nin kullanılma ve güncelleştirilme yöntemine bağlıdır.
Her zaman açık olduğunda, bir Windows Cluster kaynak grubu oluşturur, Yük Devretme Kümesi Yöneticisi açarsanız, en az üç kaynağa sahip olduğunu ve belgenin başvurduğu iki kaynağın olduğunu görürsünüz:

* Sanal ağ adı (VNN): istemcilerin, her zaman açık aracılığıyla SQL Server 'a bağlanmak için bağlanacağı DNS adı.
* IP adresi kaynağı: VNN ile ilişkili IP adresi, birden fazla sahip olabilir ve bir çoklu site yapılandırmasında site/alt ağ başına bir IP adresiniz vardır.

SQL Server bağlanırken, SQL Server Istemci sürücü, dinleyiciyle ilişkili DNS kayıtlarını alır ve her zaman ilişkili her bir IP adresine bağlanmaya çalışır. Bundan sonra, bunu etkileyebilecek bazı faktörleri tartıştık.

Dinleyici adıyla ilişkili eş zamanlı DNS kayıtlarının sayısı, yalnızca ilişkili IP adresi sayısına değil, her zaman açık VNN kaynağı için Yük Devretme Kümelemesi ' Registeralpprovider's Setting 'e bağlıdır.

Her zaman Azure 'da dağıtırken, dinleyici ve IP adreslerini oluşturmak için farklı adımlar vardır, ' Registeralpproviders ' değerini 1 olarak el ile yapılandırmanız gerekir. Bu, zaten 1 olarak ayarlanmış olan şirket içi her zaman dağıtımı için farklıdır.

' Registeralpproviders ' 0 ise, yalnızca dinleyiciyle ilişkili DNS 'de bir DNS kaydı görürsünüz:

![Appendix4][14]

' Registeralpproviders ' 1 ise:

![Appendix5][15]

Aşağıdaki kod, VNN ayarlarının dökümünü yapar ve bunu sizin için ayarlar. Değişikliğin etkili olması için, VNN 'yi çevrimdışı duruma getirmeniz ve yeniden çevrimiçi açmanız gerekir. Bu, dinleyiciyi çevrimdışı olarak istemci bağlantısı kesintiye neden olur.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

Sonraki bir geçiş adımında, her zaman açık dinleyiciyi bir yük dengeleyiciye başvuran güncelleştirilmiş bir IP adresi ile güncelleştirmeniz gerekir. Bu, IP adresi kaynağı kaldırma ve ekleme işlemini içerir. IP güncelleştirmesinden sonra, DNS bölgesinde yeni IP adresinin güncelleştirildiğinden ve istemcilerin yerel DNS önbelleğini güncelleştirdiğinden emin olmanız gerekir.

İstemcileriniz farklı bir ağ kesimindeyse ve farklı bir DNS sunucusuna başvursa, geçiş sırasında DNS bölge aktarımı hakkında ne olduğunu göz önünde bulundurmanız gerekir, çünkü uygulama yeniden bağlanma süresi en az yeni bir IP 'nin bölge aktarma zamanına göre sınırlandırılır dinleyicinin adresleri. Burada zaman kısıtlaması ' nı kullanıyorsanız, Windows ekipleriniz ile artımlı bir bölge aktarımını zorlamanız ve test etmeniz ve ayrıca DNS ana bilgisayar kaydını daha düşük bir zamana (TTL) koyarak istemcilerin güncelleştirilmesi gerekir. Daha fazla bilgi için bkz. [artımlı bölge aktarımları](https://technet.microsoft.com/library/cc958973.aspx) ve [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer).

Varsayılan olarak, Azure 'da her zaman açık olan dinleyiciyle ilişkilendirilen DNS kaydı için TTL 1200 saniyedir. İstemci DNS 'in DNS 'yi dinleyici için güncelleştirilmiş IP adresiyle güncelleştirdiğinden emin olmak için geçişiniz sırasında zaman kısıtlaması altındaysa bunu azaltmak isteyebilirsiniz. VNN yapılandırması dökümünü yaparak yapılandırmayı görebilir ve değiştirebilirsiniz:

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> Daha düşük olan ' HostRecordTTL ', daha yüksek miktarda DNS trafiği oluşur.

##### <a name="client-application-settings"></a>İstemci uygulaması ayarları

SQL istemci uygulamanız .NET 4,5 SQLClient 'yi destekliyorsa, ' MULTISUBNETFAILOVER = TRUE ' anahtar sözcüğünü kullanabilirsiniz. Bu anahtar sözcük, yük devretme sırasında SQL Always on kullanılabilirlik grubuna daha hızlı bağlanmayı sağladığından uygulanmalıdır. Her zaman açık dinleyiciyle ilişkili tüm IP adreslerini paralel olarak sıralar ve yük devretme sırasında daha ısrarlı bir TCP bağlantısı yeniden deneme hızı gerçekleştirir.

Önceki ayarlar hakkında daha fazla bilgi için bkz. [MultiSubnetFailover anahtar sözcüğü ve Ilişkili Özellikler](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Ayrıca bkz. [yüksek kullanılabilirlik, olağanüstü durum kurtarma Için SqlClient desteği](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>5\. Adım: küme çekirdek ayarları

Tek seferde en az bir SQL Server aşağı doğru duruma getirmenin yanı sıra, iki düğümle dosya paylaşma tanığı (FSW) kullanıyorsanız, çekirdeği düğüm çoğunluğuna izin verecek ve dinamik oylama 'yi kullanabilmeniz için küme çekirdek ayarını değiştirmelisiniz. , tek bir düğümün devam ettirmeye izin verir.

```powershell
Set-ClusterQuorum -NodeMajority  
```

Küme çekirdeğini yönetme ve yapılandırma hakkında daha fazla bilgi için bkz. [Windows Server 2012 yük devretme kümesinde çekirdeği yapılandırma ve yönetme](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>6\. Adım: mevcut uç noktaları ve ACL 'Leri ayıklama

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

Bu metni bir dosyaya kaydedin.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>7\. Adım: yük devretme ortaklarını ve çoğaltma modlarını değiştirme

İkiden fazla SQL sunucunuz varsa, başka bir DC 'de veya şirket içinde bulunan başka bir ikincil öğenin yük devretmesini ' Synchronous ' olarak değiştirmeniz ve bunu otomatik yük devretme ortağı (AFP) yapmanız gerekir, bu sayede değişiklik yaptığınız için HA 'yı korumanız gerekir. Bunu, SSMS gibi TSQL for Modify aracılığıyla yapabilirsiniz:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>8\. Adım: Ikincil VM 'yi bulut hizmetinden kaldırma

Önce bir bulut ikincil düğümü geçirmeyi planlıyorsunuz. Bu düğüm Şu anda birincil ise, el ile yük devretme başlatmanız gerekir.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>9\. Adım: CSV dosyasında disk önbelleğe alma ayarlarını değiştirin ve kaydedin

Veri birimlerinde bu, READONLY olarak ayarlanmalıdır.

TLOG birimlerinde, bunlar NONE olarak ayarlanmalıdır.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>10. Adım: VHD 'leri kopyalama

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


VHD 'lerin kopyalama durumunu Premium depolama hesabına bakabilirsiniz:

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix8][18]

Tüm bunlar başarılı olarak kaydedilene kadar bekleyin.

Ayrı Bloblar hakkında bilgi için:

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>11. Adım: işletim sistemi diskini kaydetme

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>12. Adım: ikincili yeni bulut hizmetine aktarma

Aşağıdaki kod ayrıca, burada eklenen seçeneği kullanır ve makineyi içeri aktarabilir ve retainable VIP 'yi kullanabilirsiniz.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>13. Adım: yeni bulut svc üzerinde ıLB oluşturma, yük dengeli uç noktalar ve ACL 'Ler ekleme

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>14. Adım: güncelleştirme her zaman açık

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Appendix9][19]

Şimdi eski bulut hizmeti IP adresini kaldırın.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>15. Adım: DNS güncelleştirme denetimi

Artık SQL Server istemci ağlarınızda DNS sunucularını kontrol etmeniz ve kümelemenin eklenen IP adresi için ek konak kaydını eklediğinizden emin olmanız gerekir. Bu DNS sunucuları güncelleştirmemişse, bir DNS bölge aktarımını zorlayarak, alt ağdaki istemcilerin her ikisi de her zaman açık IP adreslerine çözümleyebilmesini sağlar, bu sayede otomatik DNS çoğaltmasını beklemeniz gerekmez.

#### <a name="step-16-reconfigure-always-on"></a>16. Adım: her zaman yeniden yapılandırma

Bu noktada, geçirilen ikincil düğümü şirket içi düğümle tamamen yeniden eşitlemek ve zaman uyumlu çoğaltma düğümüne geçiş yapmak ve bunu AFP hale getirmek için bekleytiniz.  

#### <a name="step-17-migrate-second-node"></a>17. Adım: ikinci düğümü geçirme

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file "lun, vhdname, hostcaching, disklabel, diskName"
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>18. Adım: CSV dosyasında disk önbelleğe alma ayarlarını değiştirin ve kaydedin

Veri birimleri için önbellek ayarları READONLY olarak ayarlanmalıdır.

TLOG birimlerinde önbellek ayarları NONE olarak ayarlanmalıdır.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>19. Adım: Ikincil düğüm için yeni bağımsız depolama hesabı oluşturma

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>20. Adım: VHD 'leri kopyalama

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

Tüm VHD 'ler için VHD kopyalama durumunu kontrol edebilirsiniz:

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix12][22]

Tüm bunlar başarılı olarak kaydedilene kadar bekleyin.

Ayrı Bloblar hakkında bilgi için:

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>21. Adım: işletim sistemi diskini kaydetme

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>22. Adım: yük dengeli uç noktalar ve ACL 'Ler ekleme

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>23. Adım: yük devretme testi

Geçirilen düğümün şirket içi Always on düğümü ile eşitlenmesini bekleyin. Zaman uyumlu çoğaltma moduna yerleştirip eşitleme tamamlanana kadar bekleyin. Ardından, şirket içi sunucudan yük devretme, bu, AFP olan ilk düğüme geçirilir. Çalışma çalıştıktan sonra, son geçirilen düğümü AFP olarak değiştirin.

Yük devretme işlemleri beklenen şekilde ve zamanında çalışır durumda çalıştığından emin olmak için tüm düğümler arasında yük devretme testi yapmanız ve Chaos testlerini de çalıştırmanız gerekir.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>24. Adım: kümeyi geri yerleştirme çekirdek ayarları/DNS TTL/yük devretme Pntrs/eşitleme ayarları

##### <a name="adding-ip-address-resource-on-same-subnet"></a>Aynı alt ağa IP adresi kaynağı ekleniyor

Yalnızca iki SQL sunucunuz varsa ve bunları yeni bir bulut hizmetine geçirmek istiyorsanız, ancak bunları aynı alt ağda tutmak istiyorsanız, özgün Always on IP adresini silmek ve yeni IP adresini eklemek için dinleyiciyi çevrimdışı duruma getirmeden kaçınabilirsiniz. VM 'Leri başka bir alt ağa geçiriyorsanız, bu alt ağa başvuran ek bir küme ağı olduğu için bunu yapmanız gerekmez.

Mevcut birincili yük devretmeden önce, geçirilen ikincili ve yeni bulut hizmeti için yeni IP adresi kaynağına eklendikten sonra, bu adımları küme Yük Devretme Yöneticisi içinde gerçekleştirmeniz gerekir:

IP adresini eklemek için bkz. ek, 14. adım.

1. Geçerli IP adresi kaynağı için olası sahibini ' var olan birincil SQL Server ' olarak değiştirin, örneğin, ' dansqlams4 ':

    ![Appendix13][23]
2. Yeni IP adresi kaynağı için olası sahibini ' geçirilmiş ikincil SQL Server ' olarak değiştirin, örneğin, ' dansqlams5 ':

    ![Appendix14][24]
3. Bu ayarlandıktan sonra, yük devretmeye devam edebilirsiniz ve son düğüm geçirildiğinde, düğümün olası bir sahip olarak eklenmesi için olası sahipler düzenlenmelidir:

    ![Appendix15][25]

## <a name="additional-resources"></a>Ek kaynaklar

* [Azure Premium Depolama](../disks-types.md)
* [Sanal Makineler](https://azure.microsoft.com/services/virtual-machines/)
* [Azure sanal makineler 'de SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
