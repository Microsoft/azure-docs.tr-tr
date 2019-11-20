---
title: Azure 'da bir Linux VM 'de kabuk betikleri çalıştırma
description: Bu konu, komut Çalıştır özelliğini kullanarak bir Azure Linux sanal makinesi içinde betikleri çalıştırmayı açıklamaktadır.
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 21787854590d3ca0be2cbd6e9d167de33482c787
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597890"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Run komutunu kullanarak Linux sanal makinenizde kabuk betikleri çalıştırma

Çalıştır komutu özelliği, bir Azure Linux VM 'de kabuk betikleri çalıştırmak için sanal makine (VM) aracısını kullanır. Bu betikleri, genel makine veya uygulama yönetimi için kullanabilirsiniz. VM erişimi ve ağ sorunlarını hızla tanılamanıza ve düzeltmenize ve VM 'yi iyi bir duruma almanıza yardımcı olabilirler.

## <a name="benefits"></a>Avantajlar

Sanal makinelerinize birden çok şekilde erişebilirsiniz. Çalıştır komutu, sanal makinelerinizde VM aracısını kullanarak uzaktan betikleri çalıştırabilir. Linux VM 'Leri için Azure portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)veya [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) aracılığıyla Çalıştır komutunu kullanın.

Bu özellik, bir sanal makine içinde bir komut dosyası çalıştırmak istediğiniz tüm senaryolarda yararlıdır. Hatalı ağ veya yönetici kullanıcı yapılandırması nedeniyle RDP veya SSH bağlantı noktası açık olmayan bir sanal makineyi düzeltme ve düzeltme yöntemlerinden biridir.

## <a name="restrictions"></a>Kısıtlamalar

Çalıştır komutunu kullanırken aşağıdaki kısıtlamalar geçerlidir:

* Çıktı, son 4.096 bayt ile sınırlıdır.
* Bir komut dosyasının çalıştırılacağı en kısa süre yaklaşık 20 saniyedir.
* Betikler, Linux üzerinde yükseltilmiş bir kullanıcı olarak varsayılan olarak çalışır.
* Tek seferde bir komut dosyası çalıştırabilirsiniz.
* Bilgi isteyen betikler (etkileşimli mod) desteklenmez.
* Çalışan bir betiği iptal edemezsiniz.
* Bir betiğin çalışacağı en uzun süre 90 dakikadır. Bundan sonra betik zaman aşımına uğrar.
* Betiğin sonuçlarını döndürmek için VM 'den giden bağlantı gereklidir.

> [!NOTE]
> Doğru çalışması için, komutu çalıştırın (bağlantı noktası 443) Azure genel IP adreslerine bağlantı gerektirir. Uzantının bu uç noktalara erişimi yoksa, betikler başarıyla çalıştırılabilir, ancak sonuçları döndürmeyebilir. Sanal makinede trafiği engelliyorsanız, `AzureCloud` etiketini kullanarak Azure genel IP adreslerine giden trafiğe izin vermek için [hizmet etiketlerini](../../virtual-network/security-overview.md#service-tags) kullanabilirsiniz.

## <a name="available-commands"></a>Kullanılabilir komutlar

Bu tabloda, Linux VM 'Leri için kullanılabilen komutların listesi gösterilmektedir. İstediğiniz özel bir betiği çalıştırmak için **Runshellscript** komutunu kullanabilirsiniz. Bir komutu çalıştırmak için Azure CLı veya PowerShell kullanırken, `--command-id` veya `-CommandId` parametresi için sağladığınız değer aşağıdaki listelenen değerlerden biri olmalıdır. Kullanılabilir bir komut olmayan bir değer belirttiğinizde şu hatayı alırsınız:

```error
The entity was not found in this Azure location
```

|**Adı**|**Açıklama**|
|---|---|
|**RunShellScript**|Bir Linux kabuğu betiği çalıştırır.|
|**ifconfig**| Tüm ağ arabirimlerinin yapılandırmasını alır.|

## <a name="azure-cli"></a>Azure CLI

Aşağıdaki örnek, bir Azure Linux sanal makinesinde kabuk betiği çalıştırmak için [az VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) komutunu kullanır.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Komutları farklı bir kullanıcı olarak çalıştırmak için, bir kullanıcı hesabı belirtmek üzere `sudo -u` girin.

## <a name="azure-portal"></a>Azure portalı

[Azure Portal](https://portal.azure.com) bir VM 'ye gidin ve **Işlemler**altında **komutu Çalıştır** ' ı seçin. VM 'de çalıştırılacak kullanılabilir komutların listesini görürsünüz.

![Komutların listesi](./media/run-command/run-command-list.png)

Çalıştırmak için bir komut seçin. Bazı komutlardan isteğe bağlı veya gerekli giriş parametreleri olabilir. Bu komutlar için parametreler, giriş değerlerini sağlamanız için metin alanları olarak sunulur. Her komut için, **Görünüm betiği**genişleterek çalıştırılmakta olan betiği görüntüleyebilirsiniz. **Runshellscript** , kendi özel betiğinizi sağlamanıza olanak sağladığından diğer komutlardan farklıdır.

> [!NOTE]
> Yerleşik komutlar düzenlenemez.

Komutu seçtikten sonra, betiği çalıştırmak için **Çalıştır** ' ı seçin. Betik tamamlandıktan sonra çıkış penceresindeki çıktıyı ve hataları döndürür. Aşağıdaki ekran görüntüsünde **ifconfig** komutunu çalıştırmanın örnek bir çıkışı gösterilmektedir.

![Komut betiği çıkışını Çalıştır](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Aşağıdaki örnek, bir Azure VM üzerinde PowerShell betiğini çalıştırmak için [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet 'ini kullanır. Cmdlet 'i, `-ScriptPath` parametresinde başvurulan betiğin, cmdlet 'in çalıştırıldığı yere yerelde olmasını bekler.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Çalıştır komutuna erişimi sınırlandırma

Çalıştırma komutlarının listelenmesi veya bir komutun ayrıntılarını göstermek, abonelik düzeyinde `Microsoft.Compute/locations/runCommands/read` iznini gerektirir. Yerleşik [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü ve daha yüksek düzeyler bu izne sahiptir.

Bir komutun çalıştırılması, abonelik düzeyinde `Microsoft.Compute/virtualMachines/runCommand/action` iznini gerektirir. [Sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ve daha yüksek düzeyler bu izne sahiptir.

Çalıştır komutunu kullanmak için [yerleşik rollerden](../../role-based-access-control/built-in-roles.md) birini kullanabilir veya [özel bir rol](../../role-based-access-control/custom-roles.md) oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

VM 'lerinizde betikleri ve komutları uzaktan çalıştırmanın diğer yolları hakkında bilgi edinmek için bkz. [LINUX sanal makinenizde betikleri çalıştırma](run-scripts-in-vm.md).
