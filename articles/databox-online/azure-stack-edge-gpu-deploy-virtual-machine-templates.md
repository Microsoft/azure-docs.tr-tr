---
title: Şablonlar aracılığıyla Azure Stack Edge Pro cihazınızda sanal makineler dağıtma
description: Şablonları kullanarak bir Azure Stack Edge Pro cihazında sanal makinelerin (VM 'Ler) nasıl oluşturulacağını ve yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: cf236396f080af9676f211c42178ddda6a794420
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568350"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Şablonlar aracılığıyla Azure Stack Edge Pro GPU cihazınızda sanal makineler dağıtma

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu öğreticide, şablonları kullanarak Azure Stack Edge Pro cihazınızda bir sanal makinenin nasıl oluşturulacağı ve yönetileceği açıklanmaktadır. Bu şablonlar, VM 'niz için altyapıyı ve yapılandırmayı tanımlayan JavaScript Nesne Gösterimi (JSON) dosyalarıdır. Bu şablonlarda, dağıtılacak kaynakları ve bu kaynakların özelliklerini belirtirsiniz.

Çalışma zamanında parametreleri bir dosyadan giriş olarak götürebilecekleri için şablonlar farklı ortamlarda esnektir. Standart adlandırma yapısı `TemplateName.json` şablon ve `TemplateName.parameters.json` Parametreler dosyası içindir. ARM şablonları hakkında daha fazla bilgi için [Azure Resource Manager şablonlar nelerdir?](../azure-resource-manager/templates/overview.md)bölümüne bakın.

Bu öğreticide, kaynak oluşturmak için önceden yazılmış örnek şablonlar kullanacağız. Şablon dosyasını düzenlemeniz gerekmez ve yalnızca `.parameters.json` dosyaları değiştirerek makinenize dağıtımı özelleştirin. 

## <a name="vm-deployment-workflow"></a>VM dağıtımı iş akışı

Birçok cihaza Azure Stack Edge Pro VM 'Leri dağıtmak için, tam Fleet için tek bir Sysprep uygulanmış VHD 'yi, dağıtım için aynı şablonu ve yalnızca her dağıtım konumu için söz konusu şablona yönelik parametrelere küçük değişiklikler yapmanız gerekir (Bu değişiklikler, burada yaptığımız veya programlı bir şekilde gerçekleştiğimiz için bu değişiklikleri el ile yapabilirsiniz.) 

Şablonları kullanarak dağıtım iş akışının üst düzey özeti aşağıdaki gibidir:

1. **Önkoşulları yapılandırma** -üç tür önkoşul vardır: cihaz, istemci ve sanal makine için.

    1. **Cihaz önkoşulları**

        1. Cihazda Azure Resource Manager bağlayın.
        2. Yerel Kullanıcı arabirimi aracılığıyla işlem etkinleştirin.

    2. **İstemci önkoşulları**

        1. İstemci üzerindeki VM şablonlarını ve ilişkili dosyaları indirin.
        1. İsteğe bağlı olarak, istemcide TLS 1,2 ' i yapılandırın.
        1. [Microsoft Azure Depolama Gezgini indirip yükleyin](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) .

    3. **VM önkoşulları**

        1. Cihaz konumunda tüm VM kaynaklarını içerecek bir kaynak grubu oluşturun.
        1. VM görüntüsü oluşturmak için kullanılan VHD 'YI karşıya yüklemek için bir depolama hesabı oluşturun.
        1. Cihazınıza erişen istemcideki DNS veya hosts dosyasına yerel depolama hesabı URI 'SI ekleyin.
        1. Cihaza ve cihazınıza erişen yerel istemciye BLOB depolama sertifikasını yükler. İsteğe bağlı olarak Depolama Gezgini blob Storage sertifikasını yükler.
        1. Daha önce oluşturulan depolama hesabına bir VHD oluşturun ve yükleyin.

2. **Şablonlardan VM oluşturma**

    1. `CreateImage.parameters.json`Parametre dosyası ve dağıtım şablonu kullanarak BIR VM görüntüsü oluşturun `CreateImage.json` .
    1. `CreateVM.parameters.json`Parametreleri dosya ve dağıtım şablonu kullanarak önceden oluşturulmuş kaynaklarla BIR VM oluşturun `CreateVM.json` .

## <a name="device-prerequisites"></a>Cihaz önkoşulları

Azure Stack Edge Pro cihazınızda bu önkoşulları yapılandırın.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>İstemci önkoşulları

İstemcinizdeki Azure Stack Edge Pro cihazına erişmek için kullanılacak önkoşulları yapılandırın.

1. Bir VHD 'YI karşıya yüklemek için kullanıyorsanız [Depolama Gezgini indirin](https://azure.microsoft.com/features/storage-explorer/) . Alternatif olarak, bir VHD 'yi karşıya yüklemek için AzCopy 'i de indirebilirsiniz. AzCopy 'in eski sürümlerini çalıştırıyorsanız istemci makinenizde TLS 1,2 ' i yapılandırmanız gerekebilir. 
1. [VM şablonlarını ve parametreleri dosyalarını](https://aka.ms/ase-vm-templates) Istemci makinenize indirin. Bu dosyayı, çalışma dizini olarak kullanacağınız bir dizine ayıklayın.


## <a name="vm-prerequisites"></a>VM önkoşulları

VM oluşturma için gereken kaynakları oluşturmak üzere bu önkoşulları yapılandırın. 

    
### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup) ile yeni bir Azure kaynak grubu oluşturun. Kaynak grubu, depolama hesabı, disk, yönetilen disk gibi Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

> [!IMPORTANT]
> Tüm kaynaklar cihazla aynı konumda oluşturulur ve konum **Dbelocal** olarak ayarlanır.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Örnek çıktı aşağıda gösterilmiştir.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Önceki adımda oluşturulan kaynak grubunu kullanarak yeni bir depolama hesabı oluşturun. Bu hesap, sanal makıne için sanal disk görüntüsünü karşıya yüklemek üzere kullanılacak **yerel bir depolama hesabıdır** .

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Yalnızca yerel olarak yedekli depolama (Standard_LRS veya Premium_LRS) gibi yerel depolama hesapları Azure Resource Manager aracılığıyla oluşturulabilir. Katmanlı depolama hesapları oluşturmak için, [Azure Stack Edge Pro 'unuzdaki depolama hesaplarına bağlanma,](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)bkz. ekleme adımları.

Örnek çıktı aşağıda gösterilmiştir.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Depolama hesabı anahtarını almak için `Get-AzureRmStorageAccountKey` komutunu çalıştırın. Bu komutun örnek bir çıktısı burada gösterilmiştir.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Ana bilgisayar dosyasına blob URI Ekle

BLOB depolama alanına bağlanmak için kullandığınız istemcinin Hosts dosyasına blob URI 'sini zaten eklemiş olduğunuzdan emin olun. **Not defteri 'ni yönetici olarak çalıştırın** ve içindeki blob URI 'si için aşağıdaki girişi ekleyin `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

Tipik bir ortamda, DNS 'niz, tüm depolama hesaplarının bir girdiyle Azure Stack Edge Pro cihazına işaret edebilmesi için yapılandırılır `*.blob.devicename.domainname.com` .

### <a name="optional-install-certificates"></a>Seçim Sertifikaları yükler

*Http* kullanarak Depolama Gezgini aracılığıyla bağlanacaksınız bu adımı atlayın. *Https* kullanıyorsanız, Depolama Gezgini uygun sertifikaları yüklemeniz gerekir. Bu durumda, blob uç noktası sertifikasını yükler. Daha fazla bilgi için bkz. [sertifikaları yönetme](azure-stack-edge-gpu-manage-certificates.md)bölümünde sertifika oluşturma ve karşıya yükleme. 

### <a name="create-and-upload-a-vhd"></a>VHD oluşturma ve yükleme

Sonraki adımda karşıya yüklemek için kullanabileceğiniz bir sanal disk yansımanıza sahip olduğunuzdan emin olun. [VM görüntüsü oluşturma](azure-stack-edge-gpu-create-virtual-machine-image.md)bölümündeki adımları izleyin. 

Önceki adımlarda oluşturduğunuz yerel depolama hesabındaki sayfa Bloblarında kullanılacak tüm disk görüntülerini kopyalayın. VHD 'YI önceki adımlarda oluşturduğunuz [depolama hesabına yüklemek için](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) [Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) veya AzCopy gibi bir araç kullanabilirsiniz. 

### <a name="use-storage-explorer-for-upload"></a>Karşıya yüklemek için Depolama Gezgini kullan

1. Depolama Gezgini'ni açın. **Düzenle** ' ye gidin ve uygulamanın **Azure Stack API 'leri hedef** olarak ayarlandığından emin olun.

    ![Hedefi Azure Stack API 'Ler olarak ayarla](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. İstemci sertifikasını ped biçiminde yükler. **Sertifikaları Içeri aktarma > SSL sertifikalarını düzenle >** gidin. İstemci sertifikasını işaret edin.

    ![BLOB depolama uç noktası sertifikasını içeri aktarma](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Cihaz tarafından üretilen sertifikalar kullanıyorsanız, BLOB depolama uç noktası `.cer` sertifikasını indirip bir `.pem` biçime dönüştürün. Aşağıdaki komutu çalıştırın. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Kendi sertifikanızı getirirken imzalama zinciri kök sertifikasını `.pem` biçiminde kullanın.

3. Sertifikayı içeri aktardıktan sonra değişikliklerin etkili olması için Depolama Gezgini yeniden başlatın.

    ![Depolama Gezgini yeniden Başlat](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. Sol bölmede **depolama hesapları** ' na sağ tıklayın ve **Azure depolama 'ya Bağlan**' ı seçin. 

    ![Azure depolama 'ya bağlanma 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. **Depolama hesabı adını ve anahtarını kullan**’ı seçin. **İleri**’yi seçin.

    ![Azure depolama 'ya bağlanma 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. **Ad ve anahtar Ile Bağlan** bölümünde, **görünen ad**, **depolama hesabı adı**, Azure depolama **hesabı anahtarı**' nı belirtin. **Diğer** depolama etki alanını seçin ve sonra `<device name>.<DNS domain>` bağlantı dizesini belirtin. Depolama Gezgini bir sertifika yüklemediğiniz takdirde, **http kullan** seçeneğini işaretleyin. **İleri**’yi seçin.

    ![Ad ve anahtarla Bağlan](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. **Bağlantı özetini** gözden geçirin ve **Bağlan**' ı seçin.

8. Depolama hesabı sol bölmede görüntülenir. Depolama hesabını seçin ve genişletin. **BLOB kapsayıcıları**' nı seçin, sağ tıklayın ve **BLOB kapsayıcısı oluştur**' u seçin. Blob Kapsayıcınız için bir ad sağlayın.

9. Yeni oluşturduğunuz kapsayıcıyı seçin ve sağ bölmede **karşıya yükle > dosyaları** karşıya yükle ' yi seçin. 

    ![VHD dosyasını karşıya yükle 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. **Seçilen dosyalara** yüklemek istediğiniz VHD 'yi inceleyin ve üzerine gelin. **BLOB türünü** **Sayfa Blobu** olarak seçin ve **karşıya yükle**' yi seçin.

    ![VHD dosyasını karşıya yükle 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. VHD blob kapsayıcısına yüklendikten sonra, VHD 'yi seçin, sağ tıklayın ve ardından **Özellikler**' i seçin. 

    ![VHD dosyasını karşıya yükle 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Sonraki adımlarda kullanacağınız **URI**'yi kopyalayın ve kaydedin.

    ![URI 'yi Kopyala](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>VM 'niz için görüntü oluşturma

SANAL makinenizin görüntüsünü oluşturmak için, `CreateImage.parameters.json` Parametreler dosyasını düzenleyin ve `CreateImage.json` Bu parametre dosyasını kullanan şablonu dağıtın.


### <a name="edit-parameters-file"></a>Parametre dosyasını Düzenle

Dosya `CreateImage.parameters.json` aşağıdaki parametreleri alır: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
    }
```

`CreateImage.parameters.json`Azure Stack Edge Pro cihazınız için aşağıdaki değerleri içerecek şekilde dosyayı düzenleyin:

1. Karşıya yükleyeceğiniz VHD 'ye karşılık gelen işletim sistemi türünü sağlayın. İşletim sistemi türü Windows veya Linux olabilir.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Görüntü URI 'sini önceki adımda karşıya yüklediğiniz görüntünün URI 'siyle değiştirin:

   ```json
   "imageUri": {
       "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
       },
   ```

   Depolama Gezgini ile *http* KULLANıYORSANıZ, URI 'yi bir *http* URI 'si olarak değiştirin.

3. Benzersiz bir görüntü adı belirtin. Bu görüntü, sonraki adımlarda VM oluşturmak için kullanılır. 

   Bu makalede kullanılan örnek bir JSON aşağıda verilmiştir.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. Parametreler dosyasını kaydedin.


### <a name="deploy-template"></a>Şablon dağıtma 

Şablonu dağıtın `CreateImage.json` . Bu şablon, sonraki adımda VM oluşturmak için kullanılacak görüntü kaynaklarını dağıtır.

> [!NOTE]
> Bir kimlik doğrulama hatası alırsanız şablonu dağıttığınızda, bu oturum için Azure kimlik bilgilerinizin geçerliliği geçmiş olabilir. `login-AzureRM`Azure Stack Edge Pro cihazınızda Azure Resource Manager bağlanmak için komutu yeniden çalıştırın.

1. Şu komutu çalıştırın: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    Bu komut bir görüntü kaynağı dağıtır. Kaynağı sorgulamak için aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    İşte başarıyla oluşturulmuş bir görüntünün örnek çıktısı aşağıda verilmiştir.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
    ```
    
## <a name="create-vm"></a>VM oluşturma

### <a name="edit-parameters-file-to-create-vm"></a>VM oluşturmak için parametreleri dosyasını Düzenle
 
Sanal makine oluşturmak için `CreateVM.parameters.json` parametre dosyasını kullanın. Aşağıdaki parametreleri alır.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

`CreateVM.parameters.json`Azure Stack Edge Pro cihazınız için ' de uygun parametreleri atayın.

1. Benzersiz bir ad, ağ arabirimi adı ve ipconfig adı sağlayın. 
1. Bir Kullanıcı adı, parola ve desteklenen bir VM boyutu girin.
1. İşlem için ağ arabirimini etkinleştirdiğinizde, bu ağ arabiriminde sanal bir anahtar ve sanal ağ otomatik olarak oluşturulur. VNET adını, alt ağ adını ve VNET kaynak grubu adını almak için mevcut sanal ağı sorgulayabilirsiniz.

    Şu komutu çalıştırın:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    Örnek çıktı aşağıdaki gibidir:
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    VNET için ASEVNET adı, alt ağ adı için ASEVNETsubNet ve VNET kaynak grubu adı için ASERG kullanın.
    
1. Artık yukarıda tanımlanan alt ağ ağında bulunan VM 'ye atamak için bir statik IP adresi gerekir. **Privateıpaddress** öğesini parametre dosyasındaki bu adresle değiştirin. VM 'nin yerel DCHP sunucusundan bir IP adresi almasını sağlamak için `privateIPAddress` değeri boş bırakın.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Parametreler dosyasını kaydedin.

    Bu makalede kullanılan örnek bir JSON aşağıda verilmiştir.
    
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "vmName": {
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
        }
      }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>VM oluşturmak için şablon dağıtma

VM oluşturma şablonunu dağıtın `CreateVM.json` . Bu şablon, mevcut VNet 'ten bir ağ arabirimi oluşturur ve dağıtılan görüntüden VM oluşturur.

1. Şu komutu çalıştırın: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    VM oluşturma 15-20 dakika sürer. İşte başarıyla oluşturulmuş bir sanal makinenin örnek çıktısı aşağıda verilmiştir.
    
    ```powershell
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
    ```   

    `New-AzureRmResourceGroupDeployment`Komutunu parametresiyle zaman uyumsuz olarak da çalıştırabilirsiniz `–AsJob` . Cmdlet arka planda çalıştırıldığında örnek bir çıktı aşağıda verilmiştir. Ardından cmdlet 'ini kullanarak oluşturulan işin durumunu sorgulayabilirsiniz `Get-Job` .

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. VM 'nin başarıyla sağlanıp sağlanmadığını denetleyin. Şu komutu çalıştırın:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Sanal makineye bağlanma

Windows veya Linux VM oluşturup oluşturdığınıza bağlı olarak, bağlanma adımları farklı olabilir.

### <a name="connect-to-windows-vm"></a>Windows VM 'ye bağlanma

Windows VM 'ye bağlanmak için aşağıdaki adımları izleyin.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Linux VM 'ye bağlanma

Bir Linux sanal makinesine bağlanmak için bu adımları izleyin.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]



## <a name="next-steps"></a>Sonraki adımlar

[Azure Resource Manager cmdlet 'leri](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)