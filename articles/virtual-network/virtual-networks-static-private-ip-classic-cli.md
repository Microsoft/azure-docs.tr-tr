---
title: VM 'Ler için özel IP adresleri yapılandırma (klasik)-Azure klasik CLı
titlesuffix: Azure Virtual Network
description: Klasik Azure komut satırı arabirimi (CLI) kullanarak sanal makineler (Klasik) için özel IP adresleri yapılandırmayı öğrenin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 64342c6dcf4c9a052442a6f4b0afb7a56994f496
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059230"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Klasik Azure CLI kullanarak sanal makine (Klasik) için özel IP adreslerini yapılandırın

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Bu makale, klasik dağıtım modelini kapsamaktadır. Ayrıca [Resource Manager dağıtım modelinde statik özel IP adresi yönetme](virtual-networks-static-private-ip-arm-cli.md).

Önceden oluşturulmuş basit bir ortam izleyen beklediğiniz Klasik Azure CLI komutları örneği. Bu belgede gösterildiği komutları çalıştırmak istiyorsanız, önce açıklanan test ortamında yapı [vnet oluşturma](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Bir VM oluşturulurken özel bir statik IP adresi belirtme
Adlı yeni bir VM oluşturmak için *DNS01* adlı yeni bir bulut hizmetinde *TestService* yukarıdaki senaryo temel alınarak, şu adımları izleyin:

1. Hiç Azure CLI kullanmadıysanız bkz. [Azure CLI’yi Yükleme ve Yapılandırma](/cli/azure/install-cli-version-1.0); sonra da, Azure hesabınızı ve aboneliğinizi seçtiğiniz noktaya kadar yönergeleri uygulayın.
2. Çalıştırma **azure hizmeti oluşturma** bulut hizmetini oluşturmak için komutu.
   
        azure service create TestService --location uscentral
   
    Beklenen çıktı:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Çalıştırma **azure VM'yi oluşturma** VM oluşturmak için komutu. Statik özel IP adresi değeri dikkat edin. Çıktıdan sonra gösterilen listede kullanılan parametreler açıklanmaktadır.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Beklenen çıktı:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (veya --konum)** . VM'nin oluşturulacağı azure bölgesi. Bizim senaryomuz için bu *centralus* ’tur.
   * **-n (veya--vm-adı)** . Oluşturulacak VM adı.
   * **-w (veya--sanal-ağ-adı)** . VM'nin oluşturulacağı Vnet'in adı. 
   * **-S (veya--statik IP)** . Statik özel IP adresi VM için.
   * **TestService**. VM'nin oluşturulacağı bulut hizmeti adı.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. VM oluşturmak için kullanılan görüntü.
   * **AdminUser**. Windows VM için Yönetici'yi tıklatın.
   * <strong>AdminP@ssw0rd</strong>. Windows VM yerel yönetici parolası.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Bir sanal makine için statik özel IP adresi bilgilerini alma
Komut dosyası yukarıdaki oluşturulan VM için statik özel IP adresi bilgilerini görüntülemek için aşağıdaki Azure CLI komutunu çalıştırın ve değeri gözlemleyin *ağ StaticIP*:

    azure vm static-ip show DNS01

Beklenen çıktı:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>VM'den özel statik IP adresi kaldırma
Statik özel IP adresini kaldırmak için yukarıdaki komut VM'ye aşağıdaki Azure CLI komutunu çalıştırın eklendi:

    azure vm static-ip remove DNS01

Beklenen çıktı:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Mevcut bir VM'ye statik özel IP ekleme
Yukarıdaki betiği kullanarak oluşturulan VM 'ye statik bir özel IP adresi eklemek için aşağıdaki komutu çalıştırın:

    azure vm static-ip set DNS01 192.168.1.101

Beklenen çıktı:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>İşletim sistemi içinde IP adreslerini ayarlayın

Statik olarak bir sanal makinenin işletim sistemi içinde Azure sanal makinesine atanmış özel IP sürece atamayın, önerilen gerekli. İşletim sistemi içinde özel IP adresini el ile ayarlamanız durumunda Azure VM'sine atanan özel IP adresiyle aynı adresi olduğundan emin olun veya sanal makineye bağlantı kaybedebilir. Sanal makinenin işletim sistemi içindeki bir Azure sanal makinesine atanan genel IP adresini el ile atamayın.

## <a name="next-steps"></a>Sonraki adımlar
* Hakkında bilgi edinin [ayrılmış genel IP](virtual-networks-reserved-public-ip.md) adresleri.
* Hakkında bilgi edinin [örnek düzeyi genel IP (ILPIP)](virtual-networks-instance-level-public-ip.md) adresleri.
* Başvurun [ayrılmış IP REST API'leri](https://msdn.microsoft.com/library/azure/dn722420.aspx).