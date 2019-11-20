---
title: Java kullanarak bir Azure sanal makinesi oluşturma ve yönetme
description: Bir sanal makineyi ve tüm destekleyici kaynaklarını dağıtmak için Java ve Azure Resource Manager kullanın.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: bf0f2928bd7cbac08a2a887481f19b3acb99f6fa
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039714"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Java kullanarak Azure 'da Windows VM 'Leri oluşturma ve yönetme

[Azure sanal makinesi](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) birkaç destekleyici Azure kaynağı gerektirir. Bu makalede, Java kullanarak VM kaynaklarını oluşturma, yönetme ve silme konuları ele alınmaktadır. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Maven projesi oluşturma
> * Bağımlılık Ekle
> * Kimlik bilgileri oluştur
> * Kaynak oluşturma
> * Yönetim görevlerini gerçekleştirme
> * Kaynakları silme
> * Uygulamayı çalıştırma

Bu adımların uygulanması yaklaşık 20 dakika sürer.

## <a name="create-a-maven-project"></a>Maven projesi oluşturma

1. Henüz yapmadıysanız, [Java 'yı](https://aka.ms/azure-jdks)yükleyebilirsiniz.
2. [Maven](https://maven.apache.org/download.cgi)'i yükler.
3. Yeni bir klasör ve proje oluşturun:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Bağımlılık Ekle

1. `testAzureApp` klasörü altında `pom.xml` dosyasını açın ve uygulamanızın oluşturulmasına olanak tanımak için derleme yapılandırmasını &lt;proje&gt; ekleyin:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Azure Java SDK 'sına erişmek için gereken bağımlılıkları ekleyin.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency>
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Dosyayı kaydedin.

## <a name="create-credentials"></a>Kimlik bilgileri oluştur

Bu adıma başlamadan önce, bir [Active Directory Hizmet sorumlusuna](../../active-directory/develop/howto-create-service-principal-portal.md)erişiminizin olduğundan emin olun. Ayrıca, daha sonraki bir adımda ihtiyacınız olan uygulama KIMLIĞI, kimlik doğrulama anahtarı ve kiracı KIMLIĞINI de kaydetmeniz gerekir.

### <a name="create-the-authorization-file"></a>Yetkilendirme dosyasını oluşturma

1. `azureauth.properties` adlı bir dosya oluşturun ve bu özellikleri buna ekleyin:

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

2. Dosyayı kaydedin.
3. Kimlik doğrulama dosyasının tam yolu ile kabukta AZURE_AUTH_LOCATION adlı bir ortam değişkeni ayarlayın.

### <a name="create-the-management-client"></a>Yönetim istemcisi oluşturma

1. `src\main\java\com\fabrikam` altında `App.java` dosyasını açın ve bu Package ifadesinin en üstte olduğundan emin olun:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. Paket deyimi altında bu içeri aktarma deyimlerini ekleyin:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. İstek yapmak için ihtiyaç duyduğunuz Active Directory kimlik bilgilerini oluşturmak için, bu kodu uygulama sınıfının Main yöntemine ekleyin:
   
    ```java
    try {
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Kaynak oluşturma

### <a name="create-the-resource-group"></a>Kaynak grubunu oluşturma

Tüm kaynaklar bir [kaynak grubunda](../../azure-resource-manager/resource-group-overview.md)bulunmalıdır.

Uygulamanın değerlerini belirtmek ve kaynak grubunu oluşturmak için, bu kodu Main yöntemindeki try bloğuna ekleyin:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Kullanılabilirlik kümesi oluşturma

[Kullanılabilirlik kümeleri](tutorial-availability-sets.md) , uygulamanız tarafından kullanılan sanal makineleri korumanıza daha kolay hale getirir.

Kullanılabilirlik kümesi oluşturmak için, bu kodu Main yöntemindeki try bloğuna ekleyin:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Genel IP adresini oluşturma

Sanal makineyle iletişim kurmak için [Genel BIR IP adresi](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) gerekir.

Sanal makinenin genel IP adresini oluşturmak için, bu kodu ana yöntemdeki try bloğuna ekleyin:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

Bir sanal makinenin bir [sanal ağın](../../virtual-network/virtual-networks-overview.md)alt ağında olması gerekir.

Bir alt ağ ve sanal ağ oluşturmak için, bu kodu ana yöntemdeki try bloğuna ekleyin:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>Ağ arabirimini oluşturma

Sanal bir makine, sanal ağ üzerinde iletişim kurmak için bir ağ arabirimine ihtiyaç duyuyor.

Bir ağ arabirimi oluşturmak için, bu kodu Main yöntemindeki try bloğuna ekleyin:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>Sanal makineyi oluşturma

Tüm destekleyici kaynakları oluşturduğunuza göre, bir sanal makine oluşturabilirsiniz.

Sanal makineyi oluşturmak için, bu kodu Main yöntemindeki try bloğuna ekleyin:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Bu öğretici, Windows Server işletim sisteminin bir sürümünü çalıştıran bir sanal makine oluşturur. Diğer görüntüleri seçme hakkında daha fazla bilgi edinmek için bkz. [Windows PowerShell ve Azure CLI Ile Azure sanal makine görüntülerini gezinme ve seçme](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Market görüntüsü yerine var olan bir diski kullanmak istiyorsanız, şu kodu kullanın: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PremiumLRS)
    .create();

azure.virtualMachines.define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.StandardDS1)
    .create();
```

## <a name="perform-management-tasks"></a>Yönetim görevlerini gerçekleştirme

Bir sanal makinenin yaşam döngüsü boyunca, sanal makineyi başlatmak, durdurmak veya silmek gibi yönetim görevleri gerçekleştirmek isteyebilirsiniz. Ayrıca, yinelenen veya karmaşık görevleri otomatikleştirmek için kod oluşturmak isteyebilirsiniz.

VM ile herhangi bir şey yapmanız gerektiğinde, bunun bir örneğini almanız gerekir. Bu kodu Main yönteminin try bloğuna ekleyin:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>VM hakkında bilgi edinme

Sanal makine hakkında bilgi almak için, bu kodu ana yöntemdeki try bloğuna ekleyin:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>VM’yi durdurma

Bir sanal makineyi durdurabilir ve tüm ayarlarını tutabilir, ancak ücretlendirmeye devam edebilir veya bir sanal makineyi durdurup serbest bırakabilirsiniz. Bir sanal makine serbest bırakıldığında, onunla ilişkili tüm kaynaklar da serbest bırakılır ve faturalandırılır.

Sanal makineyi ayırmayı kaldırmadan durdurmak için, bu kodu ana yöntemdeki try bloğuna ekleyin:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Sanal makineyi serbest bırakmak istiyorsanız, bu koda yönelik kapatma çağrısını değiştirin:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>VM’yi başlatma

Sanal makineyi başlatmak için, bu kodu Main yöntemindeki try bloğuna ekleyin:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>VM 'yi yeniden boyutlandırma

Dağıtımın birçok yönü, sanal makineniz için bir boyuta karar verirken göz önünde bulundurulmalıdır. Daha fazla bilgi için bkz. [VM boyutları](sizes.md).  

Sanal makinenin boyutunu değiştirmek için, bu kodu Main yöntemindeki try bloğuna ekleyin:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>VM 'ye veri diski ekleme

2 GB boyutundaki sanal makineye bir veri diski eklemek için, 0 LUN 'unu ve bir tür okumayı önbelleğe alma türünü, bu kodu Main yöntemindeki try bloğuna ekleyin:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Kaynakları silme

Azure 'da kullanılan kaynaklar için ücretlendirildiğiniz için, artık gerekli olmayan kaynakları silmek her zaman iyi bir uygulamadır. Sanal makineleri ve tüm destekleyici kaynakları silmek istiyorsanız, tüm yapmanız gerekirse kaynak grubunu silmez.

1. Kaynak grubunu silmek için, bu kodu Main yöntemindeki try bloğuna ekleyin:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. App. Java dosyasını kaydedin.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Bu konsol uygulamasının başlangıçtan sonuna kadar tam olarak çalıştırılması yaklaşık beş dakika sürer.

1. Uygulamayı çalıştırmak için bu Maven komutunu kullanın:

    ```
    mvn compile exec:java
    ```

2. Kaynakları silmeye başlamak üzere **ENTER** tuşuna basmadan önce Azure Portal kaynakların oluşturulmasını doğrulamak birkaç dakika sürebilir. Dağıtım hakkındaki bilgileri görmek için dağıtım durumuna tıklayın.


## <a name="next-steps"></a>Sonraki adımlar
* [Java Için Azure kitaplıklarını](https://docs.microsoft.com/java/azure/java-sdk-azure-overview)kullanma hakkında daha fazla bilgi edinin.

