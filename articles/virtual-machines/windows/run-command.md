---
title: Azure 'da Windows VM 'de PowerShell betikleri çalıştırma
description: Bu konuda, komut Çalıştır özelliğini kullanarak bir Azure Windows sanal makinesi içinde PowerShell betikleri çalıştırma açıklanmaktadır
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fa7f72989d47499127714eddfa6b5e98aa80178c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749234"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Çalıştır komutunu kullanarak PowerShell betiklerini Windows sanal makinenizde çalıştırma

Çalıştır komutu özelliği, bir Azure Windows VM içinde PowerShell betikleri çalıştırmak için sanal makine (VM) aracısını kullanır. Bu betikleri, genel makine veya uygulama yönetimi için kullanabilirsiniz. VM erişimi ve ağ sorunlarını hızla tanılamanıza ve düzeltmenize ve VM 'yi iyi bir duruma almanıza yardımcı olabilirler.

 

## <a name="benefits"></a>Avantajlar

Sanal makinelerinize birden çok şekilde erişebilirsiniz. Çalıştır komutu, sanal makinelerinizde VM aracısını kullanarak uzaktan betikleri çalıştırabilir. Windows VM 'Leri için Azure portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)veya [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) aracılığıyla Run komutunu kullanın.

Bu özellik, bir sanal makine içinde bir komut dosyası çalıştırmak istediğiniz tüm senaryolarda yararlıdır. Hatalı ağ veya yönetici kullanıcı yapılandırması nedeniyle RDP veya SSH bağlantı noktası açık olmayan bir sanal makineyi düzeltme ve düzeltme yöntemlerinden biridir.

## <a name="restrictions"></a>Kısıtlamalar

Çalıştır komutunu kullanırken aşağıdaki kısıtlamalar geçerlidir:

* Çıktı, son 4.096 bayt ile sınırlıdır.
* Bir komut dosyasının çalıştırılacağı en kısa süre yaklaşık 20 saniyedir.
* Betikler Windows üzerinde sistem olarak çalışır.
* Tek seferde bir komut dosyası çalıştırılabilir.
* Bilgi isteyen betikler (etkileşimli mod) desteklenmez.
* Çalışan bir betiği iptal edemezsiniz.
* Bir betiğin çalışacağı en uzun süre 90 dakikadır. Bundan sonra zaman aşımına uğrar.
* Betiğin sonuçlarını döndürmek için VM 'den giden bağlantı gereklidir.

> [!NOTE]
> Doğru çalışması için, komutu çalıştırın (bağlantı noktası 443) Azure genel IP adreslerine bağlantı gerektirir. Uzantının bu uç noktalara erişimi yoksa, betikler başarıyla çalıştırılabilir, ancak sonuçları döndürmeyebilir. Sanal makinede trafiği engelliyorsanız, `AzureCloud` etiketini kullanarak Azure genel IP adreslerine giden trafiğe izin vermek için [hizmet etiketlerini](../../virtual-network/security-overview.md#service-tags) kullanabilirsiniz.

## <a name="available-commands"></a>Kullanılabilir komutlar

Bu tabloda, Windows VM 'Leri için kullanılabilen komutların listesi gösterilmektedir. İstediğiniz özel bir betiği çalıştırmak için **Runpowershellscript** komutunu kullanabilirsiniz. Bir komutu çalıştırmak için Azure CLı veya PowerShell kullanırken, `--command-id` veya `-CommandId` parametresi için sağladığınız değer aşağıdaki listelenen değerlerden biri olmalıdır. Kullanılabilir bir komut olmayan bir değer belirttiğinizde şu hatayı alırsınız:

```error
The entity was not found in this Azure location
```

|**Ad**|**Açıklama**|
|---|---|
|**RunPowerShellScript**|Bir PowerShell betiği çalıştırır.|
|**EnableRemotePS**|Makineyi uzak PowerShell 'i etkinleştirecek şekilde yapılandırır.|
|**EnableAdminAccount**|Yerel yönetici hesabının devre dışı olup olmadığını denetler ve bu durumda izin vermez.|
|**Komutunu**| TCP/IP 'ye erişen her bir bağdaştırıcı için IP adresi, alt ağ maskesi ve varsayılan ağ geçidi için ayrıntılı bilgileri gösterir.|
|**Rdpayarları**|Kayıt defteri ayarlarını ve etki alanı ilkesi ayarlarını denetler. Makine bir etki alanının parçasıysa veya ayarları varsayılan değerlere değiştirdiğinde ilke eylemlerini önerir.|
|**ResetRDPCert**|RDP dinleyicisine bağlı SSL sertifikasını kaldırır ve RDP dinleyicisi güvenliğini varsayılana geri yükler. Sertifikayla ilgili herhangi bir sorun görürseniz bu betiği kullanın.|
|**SetRDPPort**|Uzak Masaüstü bağlantıları için varsayılan veya Kullanıcı tarafından belirtilen bağlantı noktası numarasını ayarlar. Bağlantı noktasına gelen erişim için güvenlik duvarı kuralları sağlar.|

## <a name="azure-cli"></a>Azure CLI

Aşağıdaki örnek, bir Azure Windows sanal makinesinde kabuk betiği çalıştırmak için [az VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) komutunu kullanır.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com) bir VM 'ye gidin ve **Işlemler**altında **komutu Çalıştır** ' ı seçin. VM 'de çalıştırılacak kullanılabilir komutların listesini görürsünüz.

![Komutların listesi](./media/run-command/run-command-list.png)

Çalıştırmak için bir komut seçin. Bazı komutlardan isteğe bağlı veya gerekli giriş parametreleri olabilir. Bu komutlar için parametreler, giriş değerlerini sağlamanız için metin alanları olarak sunulur. Her komut için, **Görünüm betiği**genişleterek çalıştırılmakta olan betiği görüntüleyebilirsiniz. **Runpowershellscript** , kendi özel betiğinizi sağlamanıza olanak sağladığından diğer komutlardan farklıdır.

> [!NOTE]
> Yerleşik komutlar düzenlenemez.

Komutu seçtikten sonra, betiği çalıştırmak için **Çalıştır** ' ı seçin. Betik tamamlandıktan sonra çıkış penceresindeki çıktıyı ve hataları döndürür. Aşağıdaki ekran görüntüsünde, **Rdpsettings** komutunu çalıştırmanın örnek bir çıkışı gösterilmektedir.

![Komut betiği çıkışını Çalıştır](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

Aşağıdaki örnek, bir Azure VM üzerinde PowerShell betiğini çalıştırmak için [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet 'ini kullanır. Cmdlet 'i, `-ScriptPath` parametresinde başvurulan betiğin, cmdlet 'in çalıştırıldığı yere yerelde olmasını bekler.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Çalıştır komutuna erişimi sınırlandırma

Çalıştırma komutlarının listelenmesi veya bir komutun ayrıntılarını göstermek, abonelik düzeyinde `Microsoft.Compute/locations/runCommands/read` iznini gerektirir. Yerleşik [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolü ve daha yüksek düzeyler bu izne sahiptir.

Bir komutun çalıştırılması, abonelik düzeyinde `Microsoft.Compute/virtualMachines/runCommand/action` iznini gerektirir. [Sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ve daha yüksek düzeyler bu izne sahiptir.

Çalıştır komutunu kullanmak için [yerleşik rollerden](../../role-based-access-control/built-in-roles.md) birini kullanabilir veya [özel bir rol](../../role-based-access-control/custom-roles.md) oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

SANAL makinenizde komut dosyalarını ve komutları uzaktan çalıştırmanın diğer yolları hakkında bilgi edinmek için bkz. [WINDOWS sanal makinenizde betikleri çalıştırma](run-scripts-in-vm.md).
