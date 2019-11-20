---
title: Kullanarak bir Azure sanal makinesi oluşturma ve yönetmeC#
description: Bir C# sanal makineyi ve tüm destekleyici kaynaklarını dağıtmak için ve Azure Resource Manager kullanın.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: fe838ade7a65161efaeb60b9e4d8de17a7080da1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032780"
---
# <a name="create-and-manage-windows-vms-in-azure-using-c"></a>Kullanarak Azure 'da Windows VM 'Leri oluşturma ve yönetmeC# #

[Azure sanal makinesi](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) birkaç destekleyici Azure kaynağı gerektirir. Bu makalede, kullanarak C#VM kaynaklarını oluşturma, yönetme ve silme konuları ele alınmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Visual Studio projesi oluşturma
> * Paketi yükler
> * Kimlik bilgileri oluştur
> * Kaynak oluşturma
> * Yönetim görevlerini gerçekleştirme
> * Kaynakları silme
> * Uygulamayı çalıştırma

Bu adımların uygulanması yaklaşık 20 dakika sürer.

## <a name="create-a-visual-studio-project"></a>Visual Studio projesi oluşturma

1. Henüz yapmadıysanız, [Visual Studio 'yu](https://docs.microsoft.com/visualstudio/install/install-visual-studio)yükleyemezsiniz. Iş yükleri sayfasında **.net masaüstü geliştirme** ' yi seçin ve ardından **yükler**' i tıklatın. Özet içinde, **.NET Framework 4-4,6 geliştirme araçlarının** sizin için otomatik olarak seçili olduğunu görebilirsiniz. Visual Studio 'Yu önceden yüklediyseniz, Visual Studio başlatıcısı 'nı kullanarak .NET iş yükünü ekleyebilirsiniz.
2. Visual Studio’da, **Dosya** > **Yeni** > **Proje**’ye tıklayın.
3. **Visual C#**  > **Şablonlar** ' da **konsol uygulaması (.NET Framework)** seçeneğini belirleyin, projenin adı için *myDotnetProject* girin, projenin konumunu seçin ve ardından **Tamam**' a tıklayın.

## <a name="install-the-package"></a>Paketi yükler

NuGet paketleri, bu adımları tamamlaması için gereken kitaplıkları yüklemenin en kolay yoludur. Visual Studio 'da ihtiyacınız olan kitaplıkları almak için şu adımları uygulayın:

1. **Araçlar** > **NuGet Paket Yöneticisi**' ne ve ardından **Paket Yöneticisi konsolu**' na tıklayın.
2. Konsola bu komutu yazın:

    ```
    Install-Package Microsoft.Azure.Management.Fluent
    ```

## <a name="create-credentials"></a>Kimlik bilgileri oluştur

Bu adıma başlamadan önce, bir [Active Directory Hizmet sorumlusuna](../../active-directory/develop/howto-create-service-principal-portal.md)erişiminizin olduğundan emin olun. Ayrıca, daha sonraki bir adımda ihtiyacınız olan uygulama KIMLIĞI, kimlik doğrulama anahtarı ve kiracı KIMLIĞINI de kaydetmeniz gerekir.

### <a name="create-the-authorization-file"></a>Yetkilendirme dosyasını oluşturma

1. Çözüm Gezgini ' de, > *myDotnetProject* ' ye sağ tıklayıp **Yeni > öğe** **Ekle** ' ye sağ tıklayın ve *ardından C# görsel öğelerde* **metin dosyası** seçin. Dosyayı *azureauth. Properties*olarak adlandırın ve **Ekle**' ye tıklayın.
2. Şu yetkilendirme özelliklerini ekleyin:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    **&lt;abonelik kimliği&gt;** abonelik tanımınızla değiştirin, **uygulama kimliği&gt;** Active Directory uygulama tanımlayıcısı ile&lt;,&lt;**kimlik doğrulama anahtarı** ile uygulama anahtarı ve&gt;**Kiracı kimliği**&lt;kiracı tanımlayıcısı ile&gt;.

3. Azureauth. Properties dosyasını kaydedin. 
4. Windows adlandırılmış AZURE_AUTH_LOCATION içinde, oluşturduğunuz yetkilendirme dosyasının tam yolu ile bir ortam değişkeni ayarlayın. Örneğin, aşağıdaki PowerShell komutu kullanılabilir:

    ```
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2019\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

### <a name="create-the-management-client"></a>Yönetim istemcisi oluşturma

1. Oluşturduğunuz proje için Program.cs dosyasını açın. Ardından, bu using deyimlerini dosyanın en üstündeki mevcut deyimlere ekleyin:

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    ```

2. Yönetim istemcisini oluşturmak için bu kodu Main yöntemine ekleyin:

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-resources"></a>Kaynak oluşturma

### <a name="create-the-resource-group"></a>Kaynak grubunu oluşturma

Tüm kaynaklar bir [kaynak grubunda](../../azure-resource-manager/resource-group-overview.md)bulunmalıdır.

Uygulamanın değerlerini belirtmek ve kaynak grubunu oluşturmak için, bu kodu Main yöntemine ekleyin:

```csharp
var groupName = "myResourceGroup";
var vmName = "myVM";
var location = Region.USWest;
    
Console.WriteLine("Creating resource group...");
var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

### <a name="create-the-availability-set"></a>Kullanılabilirlik kümesi oluşturma

[Kullanılabilirlik kümeleri](tutorial-availability-sets.md) , uygulamanız tarafından kullanılan sanal makineleri korumanıza daha kolay hale getirir.

Kullanılabilirlik kümesi oluşturmak için, bu kodu Main yöntemine ekleyin:

```csharp
Console.WriteLine("Creating availability set...");
var availabilitySet = azure.AvailabilitySets.Define("myAVSet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithSku(AvailabilitySetSkuTypes.Managed)
    .Create();
```

### <a name="create-the-public-ip-address"></a>Genel IP adresini oluşturma

Sanal makineyle iletişim kurmak için [Genel BIR IP adresi](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) gerekir.

Sanal makine için genel IP adresi oluşturmak için, bu kodu Main yöntemine ekleyin:
   
```csharp
Console.WriteLine("Creating public IP address...");
var publicIPAddress = azure.PublicIPAddresses.Define("myPublicIP")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithDynamicIP()
    .Create();
```

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

Bir sanal makinenin bir [sanal ağın](../../virtual-network/virtual-networks-overview.md)alt ağında olması gerekir.

Bir alt ağ ve sanal ağ oluşturmak için bu kodu Main yöntemine ekleyin:

```csharp
Console.WriteLine("Creating virtual network...");
var network = azure.Networks.Define("myVNet")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithAddressSpace("10.0.0.0/16")
    .WithSubnet("mySubnet", "10.0.0.0/24")
    .Create();
```

### <a name="create-the-network-interface"></a>Ağ arabirimini oluşturma

Sanal bir makine, sanal ağ üzerinde iletişim kurmak için bir ağ arabirimine ihtiyaç duyuyor.

Bir ağ arabirimi oluşturmak için, bu kodu Main yöntemine ekleyin:

```csharp
Console.WriteLine("Creating network interface...");
var networkInterface = azure.NetworkInterfaces.Define("myNIC")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetwork(network)
    .WithSubnet("mySubnet")
    .WithPrimaryPrivateIPAddressDynamic()
    .WithExistingPrimaryPublicIPAddress(publicIPAddress)
    .Create();
 ```

### <a name="create-the-virtual-machine"></a>Sanal makineyi oluşturma

Tüm destekleyici kaynakları oluşturduğunuza göre, bir sanal makine oluşturabilirsiniz.

Sanal makineyi oluşturmak için bu kodu Main yöntemine ekleyin:

```csharp
Console.WriteLine("Creating virtual machine...");
azure.VirtualMachines.Define(vmName)
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .WithAdminUsername("azureuser")
    .WithAdminPassword("Azure12345678")
    .WithComputerName(vmName)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

> [!NOTE]
> Bu öğretici, Windows Server işletim sisteminin bir sürümünü çalıştıran bir sanal makine oluşturur. Diğer görüntüleri seçme hakkında daha fazla bilgi edinmek için bkz. [Windows PowerShell ve Azure CLI Ile Azure sanal makine görüntülerini gezinme ve seçme](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Market görüntüsü yerine var olan bir diski kullanmak istiyorsanız, şu kodu kullanın:

```csharp
var managedDisk = azure.Disks.Define("myosdisk")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .WithSizeInGB(128)
    .WithSku(DiskSkuTypes.PremiumLRS)
    .Create();

azure.VirtualMachines.Define("myVM")
    .WithRegion(location)
    .WithExistingResourceGroup(groupName)
    .WithExistingPrimaryNetworkInterface(networkInterface)
    .WithSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .WithExistingAvailabilitySet(availabilitySet)
    .WithSize(VirtualMachineSizeTypes.StandardDS1)
    .Create();
```

## <a name="perform-management-tasks"></a>Yönetim görevlerini gerçekleştirme

Bir sanal makinenin yaşam döngüsü boyunca, sanal makineyi başlatmak, durdurmak veya silmek gibi yönetim görevleri gerçekleştirmek isteyebilirsiniz. Ayrıca, yinelenen veya karmaşık görevleri otomatikleştirmek için kod oluşturmak isteyebilirsiniz.

VM ile herhangi bir şey yapmanız gerektiğinde, bir örneğini almanız gerekir:

```csharp
var vm = azure.VirtualMachines.GetByResourceGroup(groupName, vmName);
```

### <a name="get-information-about-the-vm"></a>VM hakkında bilgi edinme

Sanal makine hakkında bilgi almak için bu kodu Main yöntemine ekleyin:

```csharp
Console.WriteLine("Getting information about the virtual machine...");
Console.WriteLine("hardwareProfile");
Console.WriteLine("   vmSize: " + vm.Size);
Console.WriteLine("storageProfile");
Console.WriteLine("  imageReference");
Console.WriteLine("    publisher: " + vm.StorageProfile.ImageReference.Publisher);
Console.WriteLine("    offer: " + vm.StorageProfile.ImageReference.Offer);
Console.WriteLine("    sku: " + vm.StorageProfile.ImageReference.Sku);
Console.WriteLine("    version: " + vm.StorageProfile.ImageReference.Version);
Console.WriteLine("  osDisk");
Console.WriteLine("    osType: " + vm.StorageProfile.OsDisk.OsType);
Console.WriteLine("    name: " + vm.StorageProfile.OsDisk.Name);
Console.WriteLine("    createOption: " + vm.StorageProfile.OsDisk.CreateOption);
Console.WriteLine("    caching: " + vm.StorageProfile.OsDisk.Caching);
Console.WriteLine("osProfile");
Console.WriteLine("  computerName: " + vm.OSProfile.ComputerName);
Console.WriteLine("  adminUsername: " + vm.OSProfile.AdminUsername);
Console.WriteLine("  provisionVMAgent: " + vm.OSProfile.WindowsConfiguration.ProvisionVMAgent.Value);
Console.WriteLine("  enableAutomaticUpdates: " + vm.OSProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);
Console.WriteLine("networkProfile");
foreach (string nicId in vm.NetworkInterfaceIds)
{
    Console.WriteLine("  networkInterface id: " + nicId);
}
Console.WriteLine("vmAgent");
Console.WriteLine("  vmAgentVersion" + vm.InstanceView.VmAgent.VmAgentVersion);
Console.WriteLine("    statuses");
foreach (InstanceViewStatus stat in vm.InstanceView.VmAgent.Statuses)
{
    Console.WriteLine("    code: " + stat.Code);
    Console.WriteLine("    level: " + stat.Level);
    Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
    Console.WriteLine("    message: " + stat.Message);
    Console.WriteLine("    time: " + stat.Time);
}
Console.WriteLine("disks");
foreach (DiskInstanceView disk in vm.InstanceView.Disks)
{
    Console.WriteLine("  name: " + disk.Name);
    Console.WriteLine("  statuses");
    foreach (InstanceViewStatus stat in disk.Statuses)
    {
        Console.WriteLine("    code: " + stat.Code);
        Console.WriteLine("    level: " + stat.Level);
        Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
        Console.WriteLine("    time: " + stat.Time);
    }
}
Console.WriteLine("VM general status");
Console.WriteLine("  provisioningStatus: " + vm.ProvisioningState);
Console.WriteLine("  id: " + vm.Id);
Console.WriteLine("  name: " + vm.Name);
Console.WriteLine("  type: " + vm.Type);
Console.WriteLine("  location: " + vm.Region);
Console.WriteLine("VM instance status");
foreach (InstanceViewStatus stat in vm.InstanceView.Statuses)
{
    Console.WriteLine("  code: " + stat.Code);
    Console.WriteLine("  level: " + stat.Level);
    Console.WriteLine("  displayStatus: " + stat.DisplayStatus);
}
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="stop-the-vm"></a>VM’yi durdurma

Bir sanal makineyi durdurabilir ve tüm ayarlarını tutabilir, ancak ücretlendirmeye devam edebilir veya bir sanal makineyi durdurup serbest bırakabilirsiniz. Bir sanal makine serbest bırakıldığında, onunla ilişkili tüm kaynaklar da serbest bırakılır ve faturalandırılır.

Sanal makineyi ayırmayı kaldırmadan durdurmak için, bu kodu Main yöntemine ekleyin:

```csharp
Console.WriteLine("Stopping vm...");
vm.PowerOff();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

Sanal makineyi serbest bırakmak istiyorsanız, bu koda yönelik kapatma çağrısını değiştirin:

```csharp
vm.Deallocate();
```

### <a name="start-the-vm"></a>VM’yi başlatma

Sanal makineyi başlatmak için bu kodu Main yöntemine ekleyin:

```csharp
Console.WriteLine("Starting vm...");
vm.Start();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="resize-the-vm"></a>VM 'yi yeniden boyutlandırma

Dağıtımın birçok yönü, sanal makineniz için bir boyuta karar verirken göz önünde bulundurulmalıdır. Daha fazla bilgi için bkz. [VM boyutları](sizes.md).  

Sanal makinenin boyutunu değiştirmek için bu kodu Main yöntemine ekleyin:

```csharp
Console.WriteLine("Resizing vm...");
vm.Update()
    .WithSize(VirtualMachineSizeTypes.StandardDS2) 
    .Apply();
Console.WriteLine("Press enter to continue...");
Console.ReadLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>VM 'ye veri diski ekleme

Sanal makineye bir veri diski eklemek için bu kodu Main yöntemine ekleyin. Bu örnek, boyutu 2 GB olan bir veri diski ekler.

```csharp
Console.WriteLine("Adding data disk to vm...");
vm.Update()
    .WithNewDataDisk(2, 0, CachingTypes.ReadWrite) 
    .Apply();
Console.WriteLine("Press enter to delete resources...");
Console.ReadLine();
```

## <a name="delete-resources"></a>Kaynakları silme

Azure 'da kullanılan kaynaklar için ücretlendirildiğiniz için, artık gerekli olmayan kaynakları silmek her zaman iyi bir uygulamadır. Sanal makineleri ve tüm destekleyici kaynakları silmek istiyorsanız, tüm yapmanız gerekirse kaynak grubunu silmez.

Kaynak grubunu silmek için, bu kodu Main yöntemine ekleyin:

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Bu konsol uygulamasının başlangıçtan sonuna kadar tam olarak çalıştırılması yaklaşık beş dakika sürer. 

1. Konsol uygulamasını çalıştırmak için **Başlat**' a tıklayın.

2. Kaynakları silmeye başlamak üzere **ENTER** tuşuna basmadan önce Azure Portal kaynakların oluşturulmasını doğrulamak birkaç dakika sürebilir. Dağıtım hakkındaki bilgileri görmek için dağıtım durumuna tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
* Bir [Azure sanal makinesini kullanarak C# ](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)bir sanal makine oluşturmak için bir şablon kullanmanın avantajlarından yararlanın ve bir kaynak yöneticisi şablonu kullanın.
* [.Net Için Azure kitaplıklarını](https://docs.microsoft.com/dotnet/azure/?view=azure-dotnet)kullanma hakkında daha fazla bilgi edinin.
