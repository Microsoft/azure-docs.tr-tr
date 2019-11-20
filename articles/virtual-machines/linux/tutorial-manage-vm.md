---
title: Öğretici-Azure CLı ile Linux VM 'Leri oluşturma ve yönetme
description: Bu öğreticide, Azure CLI kullanarak Azure’da Linux VM’leri oluşturup yönetmeyi öğrenirsiniz
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b7660d2bcb6f2bb8b738ed92401937c0b988fef2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034409"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>Öğretici: Azure CLI ile Linux VM’leri Oluşturma ve Yönetme

Azure sanal makineleri tam olarak yapılandırılabilir ve esnek bir bilgi işlem ortamı sağlar. Bu öğretici VM boyutu seçme, VM görüntüsü seçme ve VM dağıtma gibi temel Azure sanal makine dağıtımı öğelerini kapsar. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * VM oluşturma ve bir VM’ye bağlanma
> * VM görüntülerini seçme ve kullanma
> * Belirli VM boyutlarını görüntüleme ve kullanma
> * VM’yi yeniden boyutlandırma
> * VM durumunu görüntüleme ve anlama

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Kaynak grubu oluşturma

[az group create](https://docs.microsoft.com/cli/azure/group) komutuyla bir kaynak grubu oluşturun. 

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Bir sanal makineden önce bir kaynak grubu oluşturulmalıdır. Bu örnekte, *eastus* bölgesinde *myResourceGroupVM* adlı bir kaynak grubu oluşturulur. 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

Kaynak grubu, bu öğretici boyunca görülebileceği gibi bir VM oluşturulurken veya değiştirilirken belirtilir.

## <a name="create-virtual-machine"></a>Sanal makine oluşturma

[az vm create](https://docs.microsoft.com/cli/azure/vm) komutuyla bir sanal makine oluşturun. 

Bir sanal makine oluştururken, işletim sistemi görüntüsü, disk boyutlandırma ve yönetici kimlik bilgileri gibi çeşitli seçenekler bulunur. Aşağıdaki örnekte *myVM* adlı Ubuntu Server çalıştıran bir VM oluşturulur. VM’de *azureuser* adlı bir kullanıcı hesabı ve varsayılan anahtar konumunda ( *~/.ssh*) mevcut değilse SSH anahtarları oluşturulur:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM’nin oluşturulması birkaç dakika sürebilir. VM oluşturulduktan sonra, Azure CLI VM hakkında bilgi çıkışı sağlar. `publicIpAddress` öğesini not edin, bu adres sanal makineye erişmek için kullanılabilir. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>VM’ye bağlanma

Artık Azure Cloud Shell’de SSH ile veya yerel bilgisayarınızdan VM’ye bağlanabilirsiniz. Örnek IP adresini önceki adımda not ettiğiniz `publicIpAddress` ile değiştirin.

```bash
ssh azureuser@52.174.34.95
```

VM’de oturum açtıktan sonra, uygulamaları yükleyebilir ve yapılandırabilirsiniz. İşiniz bittiğinde, normal olarak SSH oturumunu kapatın:

```bash
exit
```

## <a name="understand-vm-images"></a>VM görüntülerini anlama

Azure market, VM oluşturmak için kullanılabilecek çok sayıda görüntü içerir. Önceki adımlarda, bir Ubuntu görüntüsünü kullanarak bir sanal makine oluşturduk. Bu adımda, markette bir CentOS görüntüsü aramak ve ikinci bir sanal makineyi dağıtmak üzere kullanmak için Azure CLI’si kullanılır. 

Yaygın olarak kullanılan görüntülerin bir listesini görmek için, [az vm image list](/cli/azure/vm/image) komutunu kullanın.

```azurecli-interactive 
az vm image list --output table
```

Komut çıkışı Azure’daki en popüler VM görüntülerini döndürür.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Tam listeyi görmek için `--all` bağımsız değişkenini ekleyin. Görüntü listesi `--publisher` veya `–-offer` kullanılarak da filtrelenebilir. Bu örnekte, liste *CentOS* ile eşleşen teklife sahip tüm görüntüler için filtrelenmiştir. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Kısmi çıkış:

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Belirli bir görüntü kullanarak bir sanal makineyi dağıtmak için, görüntüyü *tanımlamak* amacıyla [Urn](cli-ps-findimage.md#terminology) sütunundaki yayımcı, teklif, SKU ve isteğe bağlı olarak sürüm numarasından oluşan değeri not edin. Görüntüyü belirtirken, görüntü sürümü sayısı “en yeni” ile değiştirilerek dağıtımın en yeni sürümü seçilebilir. Bu örnekte, bir CentOS 6.5 görüntüsünün son sürümünü belirtmek için `--image` bağımsız değişkeni kullanılmıştır.  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>VM boyutlarını anlama

Bir sanal makinenin boyutu sanal makine tarafından kullanılabilen CPU, GPU ve bellek gibi kaynakların miktarını belirler. Sanal makinelerin beklenen iş yüküne uygun olarak boyutlandırılması gerekir. İş yükü artarsa, mevcut bir sanal makine yeniden boyutlandırılabilir.

### <a name="vm-sizes"></a>VM Boyutları

Aşağıdaki tabloda boyutlar kullanım durumlarına göre kategorilere ayrılmaktadır.  

| Tür                     | Ortak boyutlar           |    Açıklama       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Genel amaçlı](sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Dengeli CPU/bellek. Küçük ve orta ölçekli uygulama ve veri çözümlerini geliştirmek/test etmek için idealdir.  |
| [İşlem için iyileştirilmiş](sizes-compute.md)   | Fsv2          | Yüksek CPU/bellek. Orta düzey trafiğe sahip uygulamalar, ağ gereçleri ve toplu işlemler için idealdir.        |
| [Bellek için iyileştirilmiş](sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Yüksek bellek/çekirdek. İlişkisel veritabanı, orta veya büyük boyutlu önbellekler ve bellek içi analiz için idealdir.                 |
| [Depolama için iyileştirilmiş](sizes-storage.md)      | Lsv2, ls              | Yüksek disk aktarım hızı ve GÇ. Büyük Veri, SQL ve NoSQL veritabanları için ideal.                                                         |
| [GPU](sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Ağır grafik işlemleri ile video düzenleme işlemleri için özel olarak hedeflenen VM’ler.       |
| [Yüksek performans](sizes-hpc.md) | H        | İşleme düzeyi yüksek olan isteğe bağlı ağ arabirimleri (RDMA) içeren VM’lerimiz, şimdiye kadarki en güçlü CPU ile sunuluyor. |


### <a name="find-available-vm-sizes"></a>Kullanılabilir VM boyutlarını bulma

Belirli bir bölgede kullanılabilen VM boyutlarının listesini görmek için, [az vm list-sizes](/cli/azure/vm) komutunu kullanın. 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Kısmi çıkış:

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Belirli bir boyutta VM oluşturma

Önceki VM oluşturma örneğinde, bir boyut sağlanmamış ve varsayılan boyut kullanılmıştı. [az vm create](/cli/azure/vm) komutu ve `--size` bağımsız değişkenini kullanarak, oluşturma sırasında VM boyutu seçilebilir. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>VM’yi yeniden boyutlandırma

VM dağıtıldıktan sonra, kaynak ayırmayı artırmak veya azaltmak için yeniden boyutlandırılabilir. Bir VM’nin geçerli boyutunu [az vm show](/cli/azure/vm) komutuyla görüntüleyebilirsiniz:

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Bir VM’yi yeniden boyutlandırmadan önce, istenen boyutun Azure kümesinde kullanılabilir olup olmadığını denetleyin. [az vm list-vm-resize-options](/cli/azure/vm) komutu boyut listesini döndürür. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
İstenen boyut kullanılabilirse, VM açık durumdayken yeniden boyutlandırılabilir ancak işlem sırasında yeniden başlatılır. Yeniden boyutlandırmayı gerçekleştirmek için [az vm resize]( /cli/azure/vm) komutunu kullanın.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

İstenen boyut geçerli kümede değilse, yeniden boyutlandırma işlemi gerçekleştirilmeden önce VM’nin serbest bırakılması gerekir. VM’yi durdurup serbest bırakmak için [az vm deallocate]( /cli/azure/vm) komutunu kullanın. VM yeniden açıldıktan sonra geçici diskteki verilerin silinebileceğine dikkat edin. Statik IP kullanılmadığı sürece ortak IP adresi de değiştirilir. 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

VM serbest bırakıldıktan sonra yeniden boyutlandırma işlemi gerçekleştirilebilir. 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Yeniden boyutlandırmadan sonra, VM yeniden başlatılabilir.

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>VM güç durumları

Bir Azure VM’si birçok güç durumuna sahip olabilir. Bu durum VM’nin hiper yönetici açısından bulunduğu geçerli durumu temsil eder. 

### <a name="power-states"></a>Güç durumları

| Güç Durumu | Açıklama
|----|----|
| Başlatılıyor | Sanal makinenin başlatıldığını gösterir. |
| Çalışıyor | Sanal makinenin çalıştığını gösterir. |
| Durduruluyor | Sanal makinenin durdurulmakta olduğunu gösterir. | 
| Durduruldu | Sanal makinenin durdurulduğunu gösterir. Durduruldu durumundaki sanal makinelere bilgi işlem ücretleri uygulanmaya devam eder.  |
| Serbest bırakılıyor | Sanal makinenin serbest bırakılmakta olduğunu gösterir. |
| Serbest bırakıldı | Sanal makinenin hiper yöneticiden kaldırıldığını ancak denetim masasında hala kullanılabilir olduğunu gösterir. Serbest bırakıldı durumundaki sanal makinelere bilgi işlem ücretleri uygulanmaz. |
| - | Sanal makinenin güç durumunun bilinmediğini gösterir. |

### <a name="find-the-power-state"></a>Güç durumunu bulma

Belirli bir VM’nin durumunu almak için, [az vm get-instance-view](/cli/azure/vm) komutunu kullanın. Sanal makine ve kaynak grubu için geçerli bir ad belirttiğinizden emin olun. 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Çıktı:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Yönetim görevleri

Bir sanal makinenin yaşam döngüsü boyunca, sanal makineyi başlatmak, durdurmak veya silmek gibi yönetim görevleri gerçekleştirmek isteyebilirsiniz. Ayrıca, yinelenen veya karmaşık görevleri otomatikleştirmek için betikler oluşturmak isteyebilirsiniz. Azure CLI kullanarak, birçok ortak yönetim görevi komut satırından veya betikler içinde çalıştırılabilir. 

### <a name="get-ip-address"></a>IP adresini alma

Bu komut bir sanal makinenin özel ve ortak IP adreslerini döndürür.  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Sanal makineyi durdurma

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Sanal makine başlatma

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Kaynak grubunu silme

Bir kaynak grubunu silmek ayrıca grubun içindeki VM, sanal ağ ve disk gibi tüm kaynakları da siler. `--no-wait` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür. `--yes` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar.

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, aşağıdakiler gibi temel VM oluşturma ve yönetim görevlerini öğrendiniz:

> [!div class="checklist"]
> * VM oluşturma ve bir VM’ye bağlanma
> * VM görüntülerini seçme ve kullanma
> * Belirli VM boyutlarını görüntüleme ve kullanma
> * VM’yi yeniden boyutlandırma
> * VM durumunu görüntüleme ve anlama

VM diskleri hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.  

> [!div class="nextstepaction"]
> [VM diskleri oluşturma ve yönetme](./tutorial-manage-disks.md)
