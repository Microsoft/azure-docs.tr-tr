---
title: Birden çok NIC ile VM (klasik) oluşturma-Azure klasik CLı | Microsoft Docs
description: Klasik Azure komut satırı arabirimi (CLı) kullanarak birden çok NIC ile VM (klasik) oluşturmayı öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dc437b15f73866f76361da529690eac7a10af1a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058754"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Azure klasik CLı kullanarak birden çok NIC ile VM (klasik) oluşturma

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Azure 'da sanal makineler (VM) oluşturabilir ve VM 'lerinizden her birine birden çok ağ arabirimi (NIC) ekleyebilirsiniz. Birden çok NIC, NIC 'lerde trafik türlerinin ayrılmasını sağlar. Örneğin, bir NIC Internet ile iletişim kurabiliyorsa, diğeri yalnızca Internet 'e bağlı olmayan iç kaynaklarla iletişim kurar. Uygulama teslimi ve WAN iyileştirme çözümleri gibi birçok ağ sanal aygıtı için ağ trafiğini birden çok NIC arasında ayırma özelliği gereklidir.

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Kaynak Yöneticisi ve klasik](../resource-manager-deployment-model.md). Bu makale klasik dağıtım modelini incelemektedir. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir. [Kaynak Yöneticisi dağıtım modelini](../virtual-machines/linux/multiple-nics.md)kullanarak bu adımları nasıl gerçekleştireceğinizi öğrenin.

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Aşağıdaki adımlarda, WEB sunucuları için *ıaasöykü* adlı bir kaynak grubu ve db sunucuları Için *ıaasöykü arka ucu* adlı bir kaynak grubu kullanılır.

## <a name="prerequisites"></a>Önkoşullar
DB sunucularını oluşturmadan önce, bu senaryo için gerekli tüm kaynaklarla *ıaasöykü* kaynak grubunu oluşturmanız gerekir. Bu kaynakları oluşturmak için, aşağıdaki adımları tamamlayın. [Sanal ağ oluşturma](virtual-networks-create-vnet-classic-cli.md) makalesindeki adımları izleyerek bir sanal ağ oluşturun.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Arka uç VM 'Leri dağıtma
Arka uç VM 'Leri aşağıdaki kaynakların oluşturulmasına bağlıdır:

* **Veri diskleri Için depolama hesabı**. Daha iyi performans için, veritabanı sunucularındaki veri diskleri, Premium depolama hesabı gerektiren katı hal sürücüsü (SSD) teknolojisini kullanacaktır. Premium depolamayı desteklemek için dağıttığınız Azure konumunun bulunduğundan emin olun.
* **NIC**. Her VM 'nin, biri veritabanı erişimi ve diğeri yönetim için olmak üzere iki NIC 'e sahip olması gerekir.
* **Kullanılabilirlik kümesi**. Tüm veritabanı sunucuları tek bir kullanılabilirlik kümesine eklenecek ve bu VM 'lerden en az birinin bakım sırasında çalıştığından emin olmanız gerekir.

### <a name="step-1---start-your-script"></a>1\. adım-betiğinizi başlatın
[Burada](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)kullanılan tüm Bash betiğini indirebilirsiniz. Komut dosyasını ortamınızda çalışacak şekilde değiştirmek için aşağıdaki adımları izleyin:

1. [Ön](#prerequisites)koşullarda yukarıda dağıtılan mevcut kaynak grubunuza göre aşağıdaki değişkenlerin değerlerini değiştirin.

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Aşağıdaki değişkenlerin değerlerini, arka uç dağıtımınız için kullanmak istediğiniz değerlere göre değiştirin.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2\. adım-sanal makinelerinize gereken kaynakları oluşturma
1. Tüm arka uç VM 'Ler için yeni bir bulut hizmeti oluşturun. Kaynak grubu adı ve `$backendCSName` `$location` Azure bölgesi için değişken kullanımına dikkat edin.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Kendi VM 'Leri tarafından kullanılacak işletim sistemi ve veri diskleri için bir Premium depolama hesabı oluşturun.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>3\. adım-birden çok NIC ile VM oluşturma
1. `numberOfVMs` Değişkenlere göre birden çok VM oluşturmak için bir döngü başlatın.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Her VM için, her iki NIC 'in adını ve IP adresini belirtin.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. VM 'yi oluşturun. Ad, alt ağ ve `--nic-config` IP adresi ile tüm NIC 'lerin bir listesini içeren parametresinin kullanımına dikkat edin.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Her VM için iki veri diski oluşturun.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>4\. adım-betiği çalıştırma
Artık gereksinimlerinize göre betiği indirip değiştirdiğinize göre, arka uç veritabanı VM 'lerini birden çok NIC ile oluşturmak için betiği çalıştırın.

1. Komut dosyanızı kaydedin ve **Bash** terminalinizden çalıştırın. İlk çıktıyı aşağıda gösterildiği gibi göreceksiniz.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Birkaç dakika sonra yürütme sona bırakılır ve çıktının geri kalanını aşağıda gösterildiği gibi görürsünüz.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5\. adım-VM 'nin işletim sistemi içinde yönlendirmeyi yapılandırma

Azure DHCP, sanal makineye bağlı ilk (birincil) ağ arabirimine bir varsayılan ağ geçidi atar. Azure, bir sanal makineye bağlı ek (ikincil) ağ arabirimlerine varsayılan ağ geçidi atamaz. Bu nedenle varsayılan olarak, alt ağın dışında kalan ve ikincil bir ağ arabirimi içeren kaynaklarla iletişim kurulamaz. Ancak, ikincil ağ arabirimleri kendi alt ağlarının dışında kalan kaynaklarla iletişim kurabilir. İkincil ağ arabirimleri için yönlendirmeyi yapılandırmak için, bkz. [birden çok ağ arabirimi ile bir sanal makine işletim sistemi Içinde Yönlendirme](virtual-network-network-interface-vm.md).
