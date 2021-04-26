---
title: Kendi aboneliklerinizi getir Azure görüntülerini Red Hat Enterprise Linux | Microsoft Docs
description: Azure 'da Red Hat Enterprise Linux için kendi abonelik görüntülerini getir hakkında bilgi edinin.
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 06/10/2020
ms.author: alsin
ms.openlocfilehash: af4f9dac30c1e443e9647a5b752cdabf9c094a6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676079"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Azure 'da kendi aboneliğinizi getir Gold görüntülerini Red Hat Enterprise Linux

Red Hat Enterprise Linux (RHEL) görüntüleri, Kullandıkça Öde veya kendi abonelik (KCG) (Red Hat Gold görüntü) modeli aracılığıyla Azure 'da bulabilirsiniz. Bu makalede, Azure 'daki Red Hat Gold görüntülerine genel bakış sunulmaktadır.

>[!NOTE]
> RHEL BYOS Gold görüntüleri Azure genel (ticari) ve Azure Kamu bulutlarında kullanılabilir. Azure Çin veya Azure Kara Orman bulutlarında mevcut değildir.

## <a name="important-points-to-consider"></a>Dikkat edilmesi gereken önemli noktalar

- Bu programda sunulan Red Hat Gold görüntüleri, Azure Marketi 'ndeki RHEL Kullandıkça Öde görüntülerine benzer üretime yönelik RHEL görüntüleridir.
- Görüntüler, [Azure 'da Red Hat Enterprise Linux görüntülerde](./redhat-images.md)açıklanan geçerli ilkeleri izler.
- Bu görüntülerden oluşturulan VM 'lere standart destek ilkeleri uygulanır.
- Red Hat Gold görüntülerinden sağlanan VM 'Ler, RHEL Kullandıkça Öde görüntüleriyle ilişkili RHEL ücretleri taşımaz.
- Görüntülerin yetkileri yok. Red Hat Subscription-Manager kullanarak doğrudan Red Hat 'ten güncelleştirmeleri almak için bu VM 'Leri kaydedebilir ve abone olabilirsiniz.
- Kullandıkça Öde görüntülerinin [Azure hibrit avantajı](../../linux/azure-hybrid-benefit-linux.md)kullanarak KCG 'ye geçmesi mümkündür. Ancak, başlangıçta dağıtılan bir KCG 'nin Linux görüntüleri için Kullandıkça Öde faturalandırma modellerine geçiş yapmak mümkün değildir. Faturalandırma modelini KCG 'den Kullandıkça Öde 'ye geçirmek için, sanal makineyi ilgili görüntüden yeniden dağıtmanız gerekir.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Red Hat Gold görüntülerine erişmek için gereksinimler ve koşullar

1. [Red Hat bulut erişimi program](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) koşulları hakkında bilgi edinin. Red hat [abonelik-Manager](https://access.redhat.com/management/cloud)' da Red Hat aboneliklerinizi bulut erişimi için etkinleştirin. Bulut erişimi için kaydedilecek Azure aboneliklerine sahip olmanız gerekir.

1. Bulut erişimi için etkinleştirdiğiniz Red Hat abonelikleri uygunluk gereksinimlerini karşılıyorsa, Azure abonelikleriniz Gold görüntü erişimi için otomatik olarak etkinleştirilir.

### <a name="expected-time-for-image-access"></a>Görüntü erişimi için beklenen süre

Bulut erişimi etkinleştirme adımlarını tamamladıktan sonra Red hat, Red Hat altın görüntülerine uygunluk görüntülerini doğrular. Doğrulama başarılı olursa, üç saat içinde altın görüntülere erişim elde edersiniz.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Azure portal kırmızı hat altın görüntülerini kullanın

1. Azure aboneliğiniz Red Hat altın görüntülerine erişimi aldıktan sonra, bunları [Azure Portal](https://portal.azure.com)bulabilirsiniz. **Kaynak oluştur ' a** gidin,  >  **Tümünü gör**.

1. Sayfanın üst kısmında özel tekliflerle ilgili olduğunu görürsünüz.

    ![Market özel teklifleri](./media/rhel-byos-privateoffers.png)

1. Özel tekliflerinizi görmek için mor bağlantısını seçin veya sayfanın en altına gidin.

1. Kullanıcı arabirimindeki sağlamanın geri kalanı var olan herhangi bir Red Hat görüntüsüne ait değildir. RHEL sürümünüzü seçin ve VM 'nizi sağlamak için istemleri izleyin. Bu işlem, son adımda resmin şartlarını kabul etmenizi sağlar.

>[!NOTE]
>Bu adımlar, şu ana kadar Red Hat Gold görüntünüzü programsal dağıtım için etkinleştirmeyecek. "Ek bilgi" bölümünde açıklandığı gibi ek bir adım gereklidir.

Bu belgenin geri kalanı, görüntü üzerinde hüküm sağlamak ve kabul etmek için CLı yöntemine odaklanır. Kullanıcı arabirimi ve CLı, son sonuç (sağlanan RHEL Gold Image VM) ile oldukça tamamen değiştirilebilir.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Azure CLı 'deki Red Hat Gold görüntülerini kullanma

Aşağıdaki yönergeler, Azure CLı kullanarak bir RHEL VM için ilk dağıtım sürecinde size yol gösterir. Bu yönergelerde, [Azure CLI 'nin yüklü](/cli/azure/install-azure-cli)olduğu varsayılır.

>[!IMPORTANT]
>Aşağıdaki tüm komutlar için yayımcı, teklif, plan ve görüntü başvurularında tüm küçük harfleri kullandığınızdan emin olun.

1. İstediğiniz abonelikte olduğunuzdan emin olun.

    ```azurecli
    az account show -o=json
    ```

1. Red Hat Gold Image VM 'niz için bir kaynak grubu oluşturun.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Görüntü koşullarını kabul edin.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Bu koşulların *her bir Azure aboneliği için, görüntü SKU 'su başına bir kez* kabul edilmesi gerekir.

1. Seçim Aşağıdaki komutla VM dağıtımınızı doğrulayın:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. Önceki örnekte gösterilen aynı komutu bağımsız değişken olmadan çalıştırarak VM 'nizi sağlayın `--validate` .

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. Sanal makinenize SSH ekleyin ve ilgili olmayan bir görüntünüz olduğunu doğrulayın. Bu adımı yapmak için öğesini çalıştırın `sudo yum repolist` . RHEL 8 için kullanın `sudo dnf repolist` . Çıktı, sanal makineyi Red Hat ile kaydetmek için Subscription-Manager kullanmanızı ister.

>[!NOTE]
>, RHEL 8 üzerinde `dnf` ve bu `yum` değiştirilebilir. Daha fazla bilgi için bkz. [RHEL 8 Yönetici Kılavuzu](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>PowerShell 'den Red Hat Gold görüntülerini kullanma

Aşağıdaki betik bir örnektir. Kaynak grubu, konum, VM adı, oturum açma bilgileri ve diğer değişkenleri tercih ettiğiniz yapılandırmayla değiştirin. Yayımcı ve plan bilgileri küçük harfle yazılmalıdır.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Kendi aboneliğinizi getir altın görüntülerini şifreleyin Red Hat Enterprise Linux

Red Hat Enterprise Linux KCG Gold görüntülerinin [Azure disk şifrelemesi](../../linux/disk-encryption-overview.md)kullanılarak güvenliği sağlanmış olabilir. Şifrelemeyi etkinleştirebilmeniz için aboneliğin kayıtlı olması *gerekir* . RHEL BYOS Gold görüntüsünü kaydetme hakkında daha fazla bilgi için bkz. Red hat [abonelik-Manager kullanarak bir sistemi kaydetme ve Red Hat müşteri portalına abone olma](https://access.redhat.com/solutions/253273). Etkin bir Red Hat aboneliğiniz varsa, [Red Hat müşteri portalı etkinleştirme anahtarları oluşturmayı](https://access.redhat.com/articles/1378093)da okuyabilirsiniz.

Azure disk şifrelemesi, [Red hat özel görüntülerinde](../../linux/redhat-create-upload-vhd.md)desteklenmez. Ek Azure disk şifrelemesi gereksinimleri ve önkoşulları, [Linux sanal makineleri Için Azure disk şifrelemesi](../../linux/disk-encryption-overview.md#additional-vm-requirements)bölümünde belgelenmiştir.

Azure disk şifrelemesi uygulama adımları için bkz. [Linux VM 'Lerinde Azure disk şifrelemesi senaryoları](../../linux/disk-encryption-linux.md) ve ilgili makaleler.

## <a name="additional-information"></a>Ek bilgiler

- Bu teklif için etkinleştirilmemiş bir abonelikte VM sağlamaya çalışırsanız şu iletiyi alırsınız:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Bu durumda, aboneliğinizi etkinleştirmek için Microsoft veya Red Hat ile iletişim kurun.

- Bir RHEL BYOS görüntüsünden bir anlık görüntüyü değiştirir ve bu özel görüntüyü [paylaşılan görüntü galerisine](../../shared-image-galleries.md)yayımlamayı denerseniz, anlık görüntünün özgün kaynağıyla eşleşen plan bilgilerini sağlamanız gerekir. Örneğin, komut şöyle görünebilir:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Son satırdaki plan parametrelerine göz önünde edin.

    [Azure disk şifrelemesi](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) özel görüntülerde desteklenmez.

- RHEL BYOS görüntülerinden sanal makineler sağlamak için Otomasyon kullanırsanız, örnek komutlarda gösterildiğine benzer plan parametreleri sağlamanız gerekir. Örneğin, Terrayform kullanıyorsanız plan bilgilerini bir [plan bloğunda](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)sağlarsınız.

## <a name="next-steps"></a>Sonraki adımlar

- Red Hat bulut erişimi hakkında daha fazla ayrıntı, [Red Hat genel bulut belgelerinde](https://access.redhat.com/public-cloud) bulunabilir.
- Bulut erişimi için adım adım kılavuzlar ve program ayrıntıları için bkz. [Red Hat bulut erişimi belgeleri](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Red Hat güncelleştirme altyapısı hakkında daha fazla bilgi edinmek için bkz. [Azure Red Hat güncelleştirme altyapısı](./redhat-rhui.md).
- Azure 'daki tüm Red Hat görüntüleri hakkında daha fazla bilgi edinmek için [Belgeler sayfasına](./redhat-images.md)bakın.
- Tüm RHEL sürümleri için Red Hat destek ilkeleri hakkında daha fazla bilgi için [Red Hat Enterprise Linux yaşam döngüsü](https://access.redhat.com/support/policy/updates/errata) sayfasına bakın.
- RHEL Gold görüntüleri hakkında ek belgeler için [Red Hat belgelerine](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access)bakın.
