---
title: Azure ayrılmış IP adreslerini yönetme (klasik) | Microsoft Docs
description: Ayrılmış IP adreslerini (klasik) ve Azure PowerShell ve Azure CLı kullanarak nasıl yönetileceğini anlayın.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 675e7a7b557a3f19ea4d8d4960316c3859cbb9c1
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058485"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Ayrılmış IP adresler (klasik dağıtım)

 Azure 'daki IP adresleri iki kategoriye ayrılır: dinamik ve ayrılmış. Azure tarafından yönetilen genel IP adresleri varsayılan olarak dinamiktir. Diğer bir deyişle, belirli bir bulut hizmeti için kullanılan IP adresinin (VIP) veya doğrudan bir VM 'ye veya rol örneğine (ıLPıP) erişmek, kaynaklar kapatıldığında veya durdurulduğunda (serbest bırakıldığında) zaman zaman değiştirilebilir.

IP adreslerinin değiştirilmesini engellemek için bir IP adresi ayırabilirsiniz. Ayrılmış IP 'Ler yalnızca VIP olarak kullanılabilir, ancak kaynaklar kapatılsa veya durdurulmuş (serbest bırakıldığında) bulut hizmeti için IP adresinin aynı kalmasını sağlar. Ayrıca, VIP olarak kullanılan mevcut dinamik IP 'Leri ayrılmış bir IP adresine de dönüştürebilirsiniz.

> [!IMPORTANT]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır:  [Kaynak Yöneticisi ve klasik](../azure-resource-manager/resource-manager-deployment-model.md). Bu makale klasik dağıtım modelini incelemektedir. Microsoft, yeni dağıtımların çoğunun Resource Manager modelini kullanmasını önerir. [Kaynak Yöneticisi dağıtım modelini](virtual-network-ip-addresses-overview-arm.md)kullanarak STATIK genel IP adresini nasıl ayıracağınızı öğrenin.

Azure 'daki IP adresleri hakkında daha fazla bilgi edinmek için [IP adresleri](virtual-network-ip-addresses-overview-classic.md) makalesini okuyun.

## <a name="when-do-i-need-a-reserved-ip"></a>Ayrılmış bir IP 'ye ne zaman ihtiyacım var?
* **IP 'nin aboneliğinizde ayrılmış olduğundan emin olmak istiyorsunuz**. Herhangi bir koşullarda aboneliğinizden yayınlanmayan bir IP adresi ayırmak istiyorsanız, ayrılmış bir genel IP kullanmanız gerekir.  
* **IP 'Nizin, durdurulmuş veya serbest bırakılmış durumunda (VM 'ler) bile bulut hizmetinizdeki kalmasını istiyorsunuz**. Hizmetin, bulut hizmetindeki sanal makineler kapatılırken veya durdurulduğunda (serbest bırakıldığında) değişmeyen bir IP adresi kullanarak erişmesini istiyorsanız.
* **Azure 'daki giden trafiğin öngörülebilir BIR IP adresi kullandığından emin olmak istiyorsunuz**. Şirket içi güvenlik duvarınız yalnızca belirli IP adreslerinden gelen trafiğe izin verecek şekilde yapılandırılmış olabilir. Bir IP 'yi ayırarak, kaynak IP adresini bilir ve bir IP değişikliği nedeniyle güvenlik duvarı kurallarınızı güncelleştirmeniz gerekmez.

## <a name="faqs"></a>SSS
- Tüm Azure hizmetleri için ayrılmış bir IP kullanabilir miyim?
    Hayır. Ayrılmış IP 'Ler, yalnızca bir VIP aracılığıyla kullanıma sunulan VM 'Ler ve bulut hizmeti örneği rolleri için kullanılabilir.
- Kaç tane ayrılmış IP 'den ihtiyacım olabilir?
    Ayrıntılar için bkz. [Azure Limitleri](../azure-subscription-service-limits.md#networking-limits) makalesi.
- Ayrılmış IP 'Ler için bir ücret var mı?
    Bazen. Fiyatlandırma ayrıntıları için [ayrılmış IP adresi fiyatlandırma ayrıntıları](https://go.microsoft.com/fwlink/?LinkID=398482) sayfasına bakın.
- Nasıl yaparım? bir IP adresi ayırsın mı?
    Bir Azure bölgesinde bir IP adresi ayırmak için PowerShell, [Azure yönetim REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)veya [Azure Portal](https://portal.azure.com) kullanabilirsiniz. Aboneliğiniz ile ayrılmış bir IP adresi ilişkilendirilmiş.
- Benzeşim grubu tabanlı VNET 'ler ile ayrılmış bir IP kullanabilir miyim?
    Hayır. Ayrılmış IP 'Ler yalnızca bölgesel VNET 'lerde desteklenir. Ayrılmış IP 'Ler, benzeşim gruplarıyla ilişkili sanal ağlar için desteklenmez. VNet 'i bölge veya benzeşim grubuyla ilişkilendirme hakkında daha fazla bilgi için bkz. [Bölgesel sanal ağlar ve benzeşim grupları hakkında](virtual-networks-migrate-to-regional-vnet.md) makalesi.

## <a name="manage-reserved-vips"></a>Ayrılmış VIP 'leri yönetme

### <a name="using-azure-powershell-classic"></a>Azure PowerShell kullanma (klasik)

Ayrılmış IP 'Leri kullanabilmeniz için aboneliğinize eklemeniz gerekir. *Orta ABD* konumunda bulunan genel IP adresleri havuzundan şu şekilde ayrılmış bir IP oluşturun:

> [!NOTE]
> Klasik dağıtım modeli için Azure PowerShell hizmet yönetimi sürümünü yüklemelisiniz. Daha fazla bilgi için bkz. [Azure PowerShell Hizmet Yönetimi modülünü yükleme](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Ancak, hangi IP 'nin ayrılmakta olduğunu belirtmezsiniz. Aboneliğinizde hangi IP adreslerinin ayrıldığını görüntülemek için aşağıdaki PowerShell komutunu çalıştırın ve *Rezervedipname* ve *Address*değerlerine dikkat edin:

```powershell
Get-AzureReservedIP
```

Beklenen çıktı:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>PowerShell ile ayrılmış bir IP adresi oluşturduğunuzda, ' de ayrılmış IP 'yi oluşturmak için bir kaynak grubu belirtemezsiniz. Azure, bunu *varsayılan ağ* adlı bir kaynak grubuna otomatik olarak koyar. [Azure Portal](https://portal.azure.com)kullanarak ayrılmış IP oluşturursanız, seçtiğiniz herhangi bir kaynak grubunu belirtebilirsiniz. Ayrılmış IP 'yi *varsayılan ağ* dışında bir kaynak grubunda oluşturursanız, `Get-AzureReservedIP` ve gibi komutlarla ayrılmış IP 'ye başvurduğunuzda her seferinde, ve `Remove-AzureReservedIP`gibi komutlarla ad *grubuna başvurmanız gerekir. ayrılmış-IP-adı*.  Örneğin, *Myresourcegroup*adlı bir kaynak grubunda *myreservedıp* adlı ayrılmış bir IP oluşturursanız, *Myresourcegroup MYRESERVEDıP adlı grup*olarak ayrılmış IP adına başvurmanız gerekir.   


Bir IP ayrıldıktan sonra, siz silene kadar aboneliğiniz ile ilişkili kalır. Ayrılmış bir IP 'yi şu şekilde silin:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Azure CLı 'yi kullanma (klasik)
Azure klasik CLı kullanarak *Orta ABD* konumunda bulunan genel IP adresleri havuzundan ayrılmış bir IP oluşturun:

> [!NOTE]
> Klasik dağıtım için Azure klasik CLı kullanmanız gerekir. Klasik Azure CLı yükleme hakkında daha fazla bilgi için bkz [. Azure klasık CLI yükleme](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Komutundaki
 
```azurecli
azure network reserved-ip create <name> <location>
```
Örnek:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Azure CLı kullanarak aboneliğinizde hangi IP adreslerinin ayrıldığını aşağıda gösterildiği gibi görebilirsiniz: 

Komutundaki
```azurecli
azure network reserved-ip list
```
Bir IP ayrıldıktan sonra, siz silene kadar aboneliğiniz ile ilişkili kalır. Ayrılmış bir IP 'yi şu şekilde silin:

Komutundaki

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Örnek:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Mevcut bir bulut hizmetinin IP adresini ayır
Mevcut bir bulut hizmetinin IP adresini `-ServiceName` parametresini ekleyerek ayırabilirsiniz. Bir bulut hizmeti *TestService* 'in ıp adresini *Orta ABD* konumunda aşağıdaki gibi ayırın:

- Azure PowerShell kullanma (klasik):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Azure CLı (klasik) kullanma:
  
    Komutundaki

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Örnek:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Ayrılmış IP 'yi yeni bir bulut hizmeti ile ilişkilendir
Aşağıdaki betik yeni bir ayrılmış IP oluşturur, ardından bunu *TestService*adlı yeni bir bulut hizmeti ile ilişkilendirir.

### <a name="using-azure-powershell-classic"></a>Azure PowerShell kullanma (klasik)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Bir bulut hizmeti ile kullanmak üzere ayrılmış bir IP oluşturduğunuzda, gelen iletişim için *VIP:&lt;Port numarası >* kullanarak VM 'ye başvurursunuz. IP ayırma, VM 'ye doğrudan bağlanabilmeniz anlamına gelmez. Ayrılmış IP, VM 'nin dağıtıldığı bulut hizmetine atanır. VM 'ye doğrudan IP 'ye bağlanmak istiyorsanız, örnek düzeyinde genel IP yapılandırmanız gerekir. Örnek düzeyi genel IP, doğrudan sanal makinenize atanan bir genel IP türüdür (ıLPıP olarak adlandırılır). Ayrılamaz. Daha fazla bilgi için [örnek düzeyinde ortak IP (ıLPıP)](virtual-networks-instance-level-public-ip.md) makalesini okuyun.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Çalışan bir dağıtımdan ayrılmış IP 'yi kaldırma

Yeni bir bulut hizmetine eklenen ayrılmış IP 'yi aşağıda gösterildiği gibi kaldırın: 
### <a name="using-azure-powershell-classic"></a>Azure PowerShell kullanma (klasik)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Azure CLı 'yi kullanma (klasik)
Komutundaki

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Örnek:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Ayrılmış bir IP 'yi çalışan bir dağıtımdan kaldırmak aboneliğinizden ayırmayı kaldırmaz. Yalnızca aboneliğinizdeki başka bir kaynak tarafından kullanılacak IP 'yi serbest bırakır.
> 

Ayrılmış bir IP 'yi bir abonelikten tamamen kaldırmak için aşağıdaki komutu çalıştırın:

Komutundaki

```azurecli
azure network reserved-ip delete <name>
```
Örnek:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Ayrılmış bir IP 'yi çalışan bir dağıtım ile ilişkilendir

### <a name="using-azure-powershell-classic"></a>Azure PowerShell kullanma (klasik)

Aşağıdaki komutlar, *TestVM2*adlı yenı bir VM ile *TestService2* adlı bir bulut hizmeti oluşturur. *Myreservedıp* adlı mevcut ayrılmış IP, daha sonra bulut hizmeti ile ilişkilendirilir.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Azure CLı 'yi kullanma (klasik)
Azure CLı kullanarak, yeni bir ayrılmış IP 'yi çalışan bulut hizmeti dağıtımınız ile aşağıdaki gibi ilişkilendirebilirsiniz:

Komutundaki
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Örnek:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Hizmet yapılandırma dosyası kullanarak ayrılmış IP 'yi bulut hizmetiyle ilişkilendirme
Ayrıca, bir hizmet yapılandırma (CSCFG) dosyası kullanarak ayrılmış bir IP 'yi bir bulut hizmeti ile ilişkilendirebilirsiniz. Aşağıdaki örnek XML, bir bulut hizmetinin *Myreservedıp*adlı ayrılmış bir VIP kullanmak üzere nasıl yapılandırılacağını göstermektedir:
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>Sonraki adımlar
* [IP adreslemesinin](virtual-network-ip-addresses-overview-classic.md) klasik dağıtım modelinde nasıl çalıştığını anlayın.
* [Ayrılmış özel IP adresleri](virtual-networks-reserved-private-ip.md)hakkında bilgi edinin.
* [Örnek düzeyi genel IP (ıLPıP) adresleri](virtual-networks-instance-level-public-ip.md)hakkında bilgi edinin.

