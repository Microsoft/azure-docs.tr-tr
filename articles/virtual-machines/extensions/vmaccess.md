---
title: Azure Linux VM 'ye erişimi sıfırlama
description: VMAccess uzantısı ve Azure CLı kullanarak yönetici kullanıcıları yönetme ve Linux VM 'lerinde erişimi sıfırlama
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 05/10/2018
ms.openlocfilehash: e0e63762fc151d93cb34885c04dd4d03285c29f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557055"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Azure CLı ile VMAccess uzantısını kullanarak Linux VM 'lerinde yönetim kullanıcılarını, SSH 'yi yönetme ve diskleri denetleme veya onarma
## <a name="overview"></a>Genel Bakış
Linux sanal makinenizde bulunan disk hataları gösteriyor. Linux VM 'niz için kök parolayı bir daha sıfırlayabilirsiniz veya yanlışlıkla SSH özel anahtarınızı silmiş olursunuz. Bu, veri merkezinin günlerindeki geri gerçekleştiyse, daha sonra sunucu konsoluna ulaşmak için KVM 'yi açmanız gerekir. Linux 'a erişimi sıfırlamak veya disk düzeyinde bakım gerçekleştirmek üzere konsola erişmenize izin veren bir KVM anahtarı olarak Azure VMAccess uzantısını düşünün.

Bu makalede, Azure VMAccess uzantısını kullanarak bir diski denetleme veya onarma, Kullanıcı erişimini sıfırlama, yönetici kullanıcı hesaplarını yönetme veya Azure Resource Manager sanal makineler olarak çalışırken Linux üzerinde SSH yapılandırmasını güncelleştirme işlemlerinin nasıl yapılacağı gösterilir. Klasik sanal makineleri yönetmeniz gerekiyorsa, [klasık VM belgelerinde](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic)bulunan yönergeleri izleyebilirsiniz. 
 
> [!NOTE]
> AAD oturum açma uzantısını yükledikten sonra sanal makinenizin parolasını sıfırlamak için VMAccess uzantısını kullanırsanız, makinenizde AAD oturum açmayı yeniden etkinleştirmek için AAD oturum açma uzantısını yeniden çalıştırmanız gerekir.

## <a name="prerequisites"></a>Önkoşullar
### <a name="operating-system"></a>İşletim sistemi

VM erişimi uzantısı şu Linux dağıtımlarına karşı çalıştırılabilir:

| Dağıtım | Sürüm |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS ve 12,04 LTS |
| Debian | De, 7.9 +, 8.2 + |
| Red Hat | RHEL 6.7 +, 7.1 + |
| Oracle Linux | 6.4 +, 7.0 + |
| SUSE | 11 ve 12 |
| OpenSuse | openSUSE artık 42.2 + |
| CentOS | CentOS 6.3 +, 7.0 + |
| CoreOS | 494.4.0 + |

## <a name="ways-to-use-the-vmaccess-extension"></a>VMAccess Uzantısını kullanmanın yolları
Linux sanal makinelerinizdeki VMAccess uzantısını kullanmanın iki yolu vardır:

* Azure CLı ve gerekli parametreleri kullanın.
* [VMAccess uzantısının işlem ve ardından üzerinde işlem yapması için ham JSON dosyalarını kullanın](#use-json-files-and-the-vmaccess-extension) .

Aşağıdaki örneklerde [az VM User](/cli/azure/vm/user) komutları kullanılır. Bu adımları gerçekleştirmek için, en son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü olması ve [az oturum açma](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açmış olması gerekir.

## <a name="update-ssh-key"></a>SSH anahtarını güncelleştirme
Aşağıdaki örnek, adlı sanal makinede Kullanıcı için SSH anahtarını güncelleştirir `azureuser` `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Note:** `az vm user update` Komut, yeni ortak anahtar METNINI `~/.ssh/authorized_keys` VM 'deki yönetici kullanıcı için dosyaya ekler. Bu, var olan SSH anahtarlarını değiştirmez veya kaldırmaz. Bu, dağıtım sırasında ayarlanan önceki anahtarları veya VMAccess uzantısı aracılığıyla sonraki güncelleştirmeleri kaldırmaz.

## <a name="reset-password"></a>Parola sıfırlama
Aşağıdaki örnek, adlı sanal makinede Kullanıcı parolasını sıfırlar `azureuser` `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>SSH'yi yeniden başlatma
Aşağıdaki örnek, SSH arka plan programını yeniden başlatır ve SSH yapılandırmasını adlı bir VM 'deki varsayılan değerlere sıfırlar `myVM` :

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Yönetici/sudo kullanıcısı oluşturma
Aşağıdaki örnek, `myNewUser` **sudo** izinleri ile adlı bir kullanıcı oluşturur. Hesap, adlı sanal makinede kimlik doğrulaması için bir SSH anahtarı kullanır `myVM` . Bu yöntem, geçerli kimlik bilgilerinin kaybolması veya unutuldu durumunda bir VM 'ye erişimi yeniden elde etmenize yardımcı olmak için tasarlanmıştır. En iyi uygulama olarak, **sudo** izinleri olan hesaplar sınırlı olmalıdır.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Kullanıcı silme
Aşağıdaki örnekte adlı VM 'de adlı bir Kullanıcı silinir `myNewUser` `myVM` :

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>JSON uzantılarını ve VMAccess Uzantısını kullanma
Aşağıdaki örnekler Ham JSON dosyalarını kullanır. Daha sonra JSON dosyalarınızı çağırmak için [az VM Extension set](/cli/azure/vm/extension) kullanın. Bu JSON dosyaları, Azure şablonlarından da çağrılabilir. 

### <a name="reset-user-access"></a>Kullanıcı erişimini Sıfırla
Linux sanal makinenizde köke erişimi kaybettiyseniz, bir kullanıcının SSH anahtarını veya parolasını güncelleştirmek için bir VMAccess betiği başlatabilirsiniz.

Bir kullanıcının SSH ortak anahtarını güncelleştirmek için adlı bir dosya oluşturun `update_ssh_key.json` ve aşağıdaki biçimde ayarlar ekleyin. Ve parametreleri için kendi değerlerinizi `username` değiştirin `ssh_key` :

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

VMAccess betiğini şu şekilde yürütün:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Kullanıcı parolasını sıfırlamak için adlı bir dosya oluşturun `reset_user_password.json` ve aşağıdaki biçimde ayarlar ekleyin. Ve parametreleri için kendi değerlerinizi `username` değiştirin `password` :

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

VMAccess betiğini şu şekilde yürütün:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>SSH'yi yeniden başlatma
SSH arka plan programını yeniden başlatmak ve SSH yapılandırmasını varsayılan değerlere sıfırlamak için adlı bir dosya oluşturun `reset_sshd.json` . Aşağıdaki içeriği ekleyin:

```json
{
  "reset_ssh": true
}
```

VMAccess betiğini şu şekilde yürütün:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Yönetici kullanıcıları yönetme

Kimlik doğrulaması için SSH anahtarı kullanan **sudo** izinlerine sahip bir kullanıcı oluşturmak için, adlı bir dosya oluşturun `create_new_user.json` ve aşağıdaki biçimde ayarları ekleyin. Ve parametreleri için kendi değerlerinizi `username` değiştirin `ssh_key` . Bu yöntem, geçerli kimlik bilgilerinin kaybolması veya unutuldu durumunda bir VM 'ye erişimi yeniden elde etmenize yardımcı olmak için tasarlanmıştır. En iyi uygulama olarak, **sudo** izinleri olan hesaplar sınırlı olmalıdır.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

VMAccess betiğini şu şekilde yürütün:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Bir kullanıcıyı silmek için adlı bir dosya oluşturun `delete_user.json` ve aşağıdaki içeriği ekleyin. Parametresi için kendi değerini değiştirin `remove_user` :

```json
{
  "remove_user":"myNewUser"
}
```

VMAccess betiğini şu şekilde yürütün:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Diski denetleme veya onarma
VMAccess kullanarak, Linux VM 'ye eklediğiniz bir diski de denetleyebilir ve onarabilirsiniz.

Diski denetleyip onarmak için, adlı bir dosya oluşturun `disk_check_repair.json` ve aşağıdaki biçimde ayarları ekleyin. Adı için kendi değerini değiştirin `repair_disk` :

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

VMAccess betiğini şu şekilde yürütün:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal ve Azure CLı kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için, Azure CLı 'yı kullanarak aşağıdaki komutu çalıştırın.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
