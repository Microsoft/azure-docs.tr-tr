---
title: Azure VM 'ye SSH bağlantısı sorunlarını giderme | Microsoft Docs
description: Linux çalıştıran bir Azure VM için ' SSH bağlantısı başarısız ' veya ' SSH bağlantısı reddedildi ' gibi sorunları giderme.
keywords: SSH bağlantısı reddedildi, SSH hatası, Azure SSH, SSH bağlantısı başarısız oldu
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 006dbbe1b7472982a894691d019eb88ef2041dac
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088257"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Başarısız olan, hatalı veya reddedilen bir Azure Linux VM 'sine yönelik SSH bağlantıları sorunlarını giderme
Bu makale, bir Linux sanal makinesine (VM) bağlanmaya çalıştığınızda Secure Shell (SSH) hatalarından, SSH bağlantı hatalarından veya SSH 'nin reddetmesi nedeniyle oluşan sorunları bulmanıza ve düzeltmenize yardımcı olur. Bağlantı sorunlarını gidermek ve çözmek için Linux için Azure portal, Azure CLı veya VM erişim uzantısı 'nı kullanabilirsiniz.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayına dosya. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Hızlı sorun giderme adımları
Her bir sorun giderme adımından sonra sanal makineye yeniden bağlanmayı deneyin.

1. [SSH yapılandırmasını sıfırlayın](#reset-config).
2. Kullanıcının [kimlik bilgilerini sıfırlayın](#reset-credentials) .
3. [Ağ güvenlik grubu](../../virtual-network/security-overview.md) kurallarının SSH trafiğine izin verdiğinden emin olun.
   * SSH trafiğine izin vermek için bir [ağ güvenlik grubu kuralı](#security-rules) olduğundan emin olun (varsayılan olarak, TCP bağlantı noktası 22).
   * Azure yük dengeleyici kullanmadan bağlantı noktası yeniden yönlendirme/eşleme kullanamazsınız.
4. [VM kaynak durumunu](../../resource-health/resource-health-overview.md)denetleyin.
   * VM 'nin sağlıklı olduğundan emin olun.
   * [Önyükleme tanılaması etkinse](boot-diagnostics.md), VM 'nin günlüklerde önyükleme hataları bildirmediğinden emin olun.
5. [VM 'Yi yeniden başlatın](#restart-vm).
6. [VM 'yi yeniden dağıtın](#redeploy-vm).

Daha ayrıntılı sorun giderme adımları ve açıklamaları için okumaya devam edin.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>SSH bağlantı sorunlarını gidermek için kullanılabilecek yöntemler
Aşağıdaki yöntemlerden birini kullanarak kimlik bilgilerini veya SSH yapılandırmasını sıfırlayabilirsiniz:

* [Azure Portal](#use-the-azure-portal) -SSH YAPıLANDıRMASıNı veya SSH anahtarını hızlıca sıfırlamanız gerekiyorsa ve Azure Araçları yüklü değilse harika.
* [Azure VM seri konsolu](https://aka.ms/serialconsolelinux) -VM seri konsolu, SSH yapılandırmasına bakılmaksızın çalışır ve sanal makinenize etkileşimli bir konsol sağlar. Aslında, "SSH olamaz" durumları özellikle, seri konsolunun çözmeye yardımcı olmak üzere tasarlanma biçiminde değildir. Daha fazla ayrıntı için aşağıya bakın.
* [Azure CLI](#use-the-azure-cli) -zaten komut SATıRLARALıYORSA, SSH yapılandırmasını veya kimlik bilgilerini hızlıca sıfırlayın. Klasik bir VM ile çalışıyorsanız, [Klasik Azure CLI](#use-the-azure-classic-cli)'yi kullanabilirsiniz.
* [Azure VMAccessForLinux uzantısı](#use-the-vmaccess-extension) -SSH yapılandırmasını veya Kullanıcı kimlik bilgilerini sıfırlamak için JSON tanım dosyalarını oluşturun ve yeniden kullanın.

Her bir sorun giderme adımından sonra, sanal makinenize yeniden bağlanmayı deneyin. Hala bağlanamıyorsanız, bir sonraki adımı deneyin.

## <a name="use-the-azure-portal"></a>Azure portalı kullanma
Azure portal, yerel bilgisayarınıza herhangi bir araç yüklemeden SSH yapılandırmasını veya Kullanıcı kimlik bilgilerini sıfırlamanın hızlı bir yolunu sağlar.

Başlamak için Azure portal VM 'nizi seçin. Aşağıdaki örnekte, **destek + sorun giderme** bölümüne gidin ve **Parolayı Sıfırla** ' yı seçin:

![Azure portal SSH yapılandırmasını veya kimlik bilgilerini sıfırlayın](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />SSH yapılandırmasını sıfırlama
SSH yapılandırmasını sıfırlamak için, önceki ekran `Reset configuration only` görüntüsünde bulunan **mod** bölümünde ' ı seçin ve ardından **Güncelleştir**' i seçin. Bu eylem tamamlandığında, sanal makinenize erişmeyi yeniden deneyin.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Kullanıcı için SSH kimlik bilgilerini sıfırlama
Mevcut bir kullanıcının kimlik bilgilerini sıfırlamak için, önceki ekran görüntüsündeki `Reset SSH public key` gibi `Reset password` **mod** bölümünde veya ' ı seçin. Kullanıcı adını ve bir SSH anahtarını veya yeni parolayı belirtip **Güncelleştir**' i seçin.

Bu menüden, sanal makinede sudo ayrıcalıklarına sahip bir kullanıcı da oluşturabilirsiniz. Yeni bir Kullanıcı adı ve ilişkili parola veya SSH anahtarı girip **Güncelleştir**' i seçin.

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />Güvenlik kurallarını denetle

Bir ağ güvenlik grubundaki bir kuralın bir sanal makineden gelen veya giden trafiği engelleyip engellemediğini onaylamak için [IP akışı doğrulama](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) kullanın. Ayrıca, gelen "Izin ver" NSG kuralının mevcut olduğundan ve SSH bağlantı noktası (varsayılan 22) için önceliklendirildiğinden emin olmak için etkin güvenlik grubu kurallarını gözden geçirebilirsiniz. Daha fazla bilgi için bkz. [sanal makine trafiği akışı sorunlarını gidermek için etkin güvenlik kurallarını kullanma](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Yönlendirmeyi denetle

Bir yolun bir sanal makineye veya bir sanal makineye yönlendirilmesini engellemediğini doğrulamak için ağ Izleyicisi 'nin [sonraki atlama](../../network-watcher/network-watcher-check-next-hop-portal.md) özelliğini kullanın. Ayrıca, bir ağ arabirimi için tüm etkin yolları görmek üzere geçerli yolları gözden geçirebilirsiniz. Daha fazla bilgi için bkz. [VM trafik akışı sorunlarını gidermek için geçerli yolları kullanma](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Azure VM seri konsolu 'Nu kullanma
[Azure VM seri konsolu](./serial-console-linux.md) , Linux sanal makineleri için metin tabanlı bir konsola erişim sağlar. Etkileşimli bir kabukta SSH bağlantınızın sorunlarını gidermek için konsolunu kullanabilirsiniz. Seri konsol kullanma [önkoşullarını](./serial-console-linux.md#prerequisites) karşılatığınızdan emin olun ve SSH bağlantınızın sorunlarını gidermek için aşağıdaki komutları deneyin.

### <a name="check-that-ssh-is-running"></a>SSH 'nin çalıştığını denetleyin
SANAL makinenizde SSH 'nin çalışıp çalışmadığını doğrulamak için aşağıdaki komutu kullanabilirsiniz:
```
$ ps -aux | grep ssh
```
Herhangi bir çıkış varsa, SSH çalışır duruma ayarlanır.

### <a name="check-which-port-ssh-is-running-on"></a>SSH 'nin hangi bağlantı noktası üzerinde çalıştığını denetleyin
Hangi bağlantı noktası SSH 'nin çalıştığını denetlemek için aşağıdaki komutu kullanabilirsiniz:
```
$ sudo grep Port /etc/ssh/sshd_config
```
Çıktılarınız şöyle görünür:
```
Port 22
```

## <a name="use-the-azure-cli"></a>Azure CLI kullanma
Henüz yapmadıysanız, en son [Azure CLI](/cli/azure/install-az-cli2) 'yı yükleyip [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açın.

Özel bir Linux disk görüntüsü oluşturup karşıya yüklüyorsanız, [Microsoft Azure Linux Aracısı](../extensions/agent-linux.md) sürümü 2.0.5 veya sonraki bir sürümün yüklü olduğundan emin olun. Galeri görüntüleri kullanılarak oluşturulan VM 'Ler için, bu erişim uzantısı zaten yüklenmiş ve sizin için yapılandırılmış.

### <a name="reset-ssh-configuration"></a>SSH yapılandırmasını Sıfırla
Başlangıçta SSH yapılandırmasını varsayılan değerlere sıfırlamayı ve SSH sunucusunu VM 'de yeniden başlatmayı deneyebilirsiniz. Bu, Kullanıcı hesabı adını, parolayı veya SSH anahtarlarını değiştirmez.
Aşağıdaki örnek, içinde `myResourceGroup`adlı `myVM` sanal makinede SSH yapılandırmasını sıfırlamak için [az VM User Reset-SSH](/cli/azure/vm/user) kullanır. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Kullanıcı için SSH kimlik bilgilerini sıfırlama
Aşağıdaki örnek, ' de adlı `myVM` `myUsername` sanalmakinede`myPassword`' de belirtilen değere kimlik bilgilerini sıfırlamak için [az VM User Update](/cli/azure/vm/user) kullanır. `myResourceGroup` Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

SSH anahtarı kimlik doğrulamasını kullanıyorsanız, belirli bir kullanıcı için SSH anahtarını sıfırlayabilirsiniz. Aşağıdaki örnek, `~/.ssh/id_rsa.pub` `myUsername`içinde adlı`myResourceGroup` `myVM` VM 'de adlı Kullanıcı için ' de depolanan SSH anahtarını güncelleştirmek için az VM Access set-Linux-User kullanır. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>VMAccess uzantısını kullanma
Linux için VM erişimi uzantısı, gerçekleştirilecek eylemleri tanımlayan bir JSON dosyasında okur. Bu eylemler, SSHD 'yi sıfırlamayı, bir SSH anahtarını sıfırlamayı veya bir Kullanıcı eklemeyi içerir. VMAccess uzantısını çağırmak için yine de Azure CLı kullanıyorsunuz, ancak isterseniz JSON dosyalarını birden çok VM genelinde yeniden kullanabilirsiniz. Bu yaklaşım, daha sonra verilen senaryolar için çağrılabilecek bir JSON dosyaları deposu oluşturmanızı sağlar.

### <a name="reset-sshd"></a>SSHD 'yi Sıfırla
Aşağıdaki içerikle adlı `settings.json` bir dosya oluşturun:

```json
{
    "reset_ssh":"True"
}
```

Azure CLI 'yı kullanarak JSON dosyanızı belirterek SSHD `VMAccessForLinux` bağlantınızı sıfırlamak için uzantıyı çağırabilirsiniz. Aşağıdaki örnek, içinde `myResourceGroup`adlı `myVM` sanal makinede SSHD 'yi sıfırlamak için [az VM Extension set](/cli/azure/vm/extension) kullanır. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Kullanıcı için SSH kimlik bilgilerini sıfırlama
SSHD doğru şekilde işlev içeriyorsa, bir Giver kullanıcısına ait kimlik bilgilerini sıfırlayabilirsiniz. Bir kullanıcının parolasını sıfırlamak için adlı `settings.json`bir dosya oluşturun. Aşağıdaki örnek, için `myUsername` kimlik bilgilerini içinde `myPassword`belirtilen değere sıfırlar. Aşağıdaki satırları `settings.json` dosyanıza, kendi değerlerinizi kullanarak girin:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Ya da bir kullanıcının SSH anahtarını sıfırlamak için, önce adlı `settings.json`bir dosya oluşturun. Aşağıdaki örnek `myUsername` , içinde `myVM` `myPassword` adlı`myResourceGroup`sanal makinede ' de belirtilen değere kimlik bilgilerini sıfırlar. Aşağıdaki satırları `settings.json` dosyanıza, kendi değerlerinizi kullanarak girin:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

JSON dosyanızı oluşturduktan sonra, JSON dosyanızı belirterek SSH kullanıcı kimlik bilgilerinizi sıfırlama `VMAccessForLinux` uzantısını çağırmak için Azure CLI ' yi kullanın. Aşağıdaki örnek, içinde `myVM` `myResourceGroup`adlı sanal makinede bulunan kimlik bilgilerini sıfırlar. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Klasik Azure CLı 'yı kullanma
Henüz yapmadıysanız, [Azure klasık CLI 'yı yükleyip Azure aboneliğinize bağlanın](../../cli-install-nodejs.md). Kaynak Yöneticisi modunu şu şekilde kullandığınızdan emin olun:

```azurecli
azure config mode arm
```

Özel bir Linux disk görüntüsü oluşturup karşıya yüklüyorsanız, [Microsoft Azure Linux Aracısı](../extensions/agent-linux.md) sürümü 2.0.5 veya sonraki bir sürümün yüklü olduğundan emin olun. Galeri görüntüleri kullanılarak oluşturulan VM 'Ler için, bu erişim uzantısı zaten yüklenmiş ve sizin için yapılandırılmış.

### <a name="reset-ssh-configuration"></a>SSH yapılandırmasını Sıfırla
SSHD yapılandırmasının kendisi yanlış yapılandırılmış olabilir veya hizmet bir hatayla karşılaştı. SSH yapılandırmasının geçerli olduğundan emin olmak için SSHD 'yi sıfırlayabilirsiniz. SSHD 'yi sıfırlamak, uygulamanız gereken ilk sorun giderme adımı olmalıdır.

Aşağıdaki örnek, adlı `myVM` `myResourceGroup`kaynak grubunda adlı bir VM 'deki SSHD 'yi sıfırlar. Kendi sanal makine ve kaynak grubu adlarınızı aşağıdaki gibi kullanın:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Kullanıcı için SSH kimlik bilgilerini sıfırlama
SSHD doğru şekilde işlev içeriyorsa, Giver kullanıcısına ait parolayı sıfırlayabilirsiniz. Aşağıdaki örnek `myUsername` , içinde `myVM` `myPassword` adlı`myResourceGroup`sanal makinede ' de belirtilen değere kimlik bilgilerini sıfırlar. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

SSH anahtarı kimlik doğrulamasını kullanıyorsanız, belirli bir kullanıcı için SSH anahtarını sıfırlayabilirsiniz. Aşağıdaki örnek `~/.ssh/id_rsa.pub` , içinde `myVM` `myUsername` adlı`myResourceGroup`sanal makinede adlı Kullanıcı için ' de depolanan SSH anahtarını güncelleştirir. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />VM 'yi yeniden başlatma
SSH yapılandırmasını ve Kullanıcı kimlik bilgilerini sıfırladıysanız veya bunu yaparken bir hatayla karşılaşırsanız, arka plandaki işlem sorunlarını gidermek için sanal makineyi yeniden başlatmayı deneyebilirsiniz.

### <a name="azure-portal"></a>Azure portal
Azure portal kullanarak bir VM 'yi yeniden başlatmak için VM 'nizi seçin ve ardından aşağıdaki örnekte olduğu gibi **Yeniden Başlat** ' ı seçin:

![Azure portal VM 'yi yeniden başlatma](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Aşağıdaki örnek, adlı `myResourceGroup`kaynak grubunda adlı `myVM` VM 'yi yeniden başlatmak için [az VM restart](/cli/azure/vm) kullanır. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasik CLI
Aşağıdaki örnek, adlı `myVM` `myResourceGroup`kaynak grubunda adlı VM 'yi yeniden başlatır. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />VM 'yi yeniden dağıtma
Bir VM 'yi Azure 'daki başka bir düğüme yeniden dağıtabilirsiniz ve bu da temeldeki ağ sorunlarını düzeltebilir. Bir VM 'yi yeniden dağıtma hakkında daha fazla bilgi için bkz. [sanal makineyi yeni Azure düğümüne](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)yeniden dağıtma.

> [!NOTE]
> Bu işlem tamamlandıktan sonra, kısa ömürlü disk verileri kaybedilir ve sanal makineyle ilişkili dinamik IP adresleri güncellenir.
>
>

### <a name="azure-portal"></a>Azure portal
Azure portal kullanarak bir VM 'yi yeniden dağıtmak için VM 'nizi seçin ve **destek + sorun giderme** bölümüne gidin. Aşağıdaki örnekte yeniden **Dağıt** ' ı seçin:

![Azure portal bir VM 'yi yeniden dağıtma](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
Aşağıdaki örnek, adlı `myResourceGroup`kaynak grubunda adlı `myVM` VM 'yi yeniden dağıtmak için [az VM yeniden dağıtma](/cli/azure/vm) kullanır. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure klasik CLI
Aşağıdaki örnek adlı `myVM` `myResourceGroup`kaynak grubunda adlı sanal makineyi yeniden dağıtır. Kendi değerlerinizi aşağıdaki gibi kullanın:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Klasik dağıtım modeli kullanılarak oluşturulan VM 'Ler
Klasik dağıtım modeli kullanılarak oluşturulan VM 'Ler için en yaygın SSH bağlantısı başarısızlıklarını çözümlemek için aşağıdaki adımları deneyin. Her adımdan sonra sanal makineye yeniden bağlanmayı deneyin.

* [Azure Portal](https://portal.azure.com)uzaktan erişimi sıfırlayın. Azure portal VM 'nizi seçin ve ardından **Uzaktan Sıfırla...** seçeneğini belirleyin.
* VM’yi yeniden başlatın. [Azure Portal](https://portal.azure.com)VM 'nizi seçin ve **Yeniden Başlat**' ı seçin.

* VM 'yi yeni bir Azure düğümüne yeniden dağıtın. Bir VM 'yi yeniden dağıtma hakkında daha fazla bilgi için bkz. [sanal makineyi yeni Azure düğümüne yeniden dağıtma](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    Bu işlem tamamlandıktan sonra, kısa ömürlü disk verileri kaybedilir ve sanal makineyle ilişkili dinamik IP adresleri güncelleştirilir.
* [Linux tabanlı sanal makineler için parola veya SSH 'yi sıfırlama](../linux/classic/reset-access-classic.md) bölümündeki yönergeleri izleyin:

  * Parolayı veya SSH anahtarını sıfırlayın.
  * Bir *sudo* Kullanıcı hesabı oluşturun.
  * SSH yapılandırmasını sıfırlayın.
* Tüm platform sorunları için VM 'nin kaynak sistem durumunu kontrol edin.<br>
     VM 'nizi seçin ve aşağı kaydırarak **Ayarlar** > **sistem durumunu denetleyin**.

## <a name="additional-resources"></a>Ek kaynaklar
* Sonraki adımlardan sonra sanal makinenize hala SSH ile erişemiyorsanız, sorununuzu çözmek için ek adımları gözden geçirmek üzere [daha ayrıntılı sorun giderme adımları](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) bölümüne bakın.
* Uygulama erişiminin sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure sanal makinesinde çalışan bir uygulamaya erişim sorunlarını giderme](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Klasik dağıtım modeli kullanılarak oluşturulan sanal makinelerin sorunlarını giderme hakkında daha fazla bilgi için bkz. [Linux tabanlı sanal makineler için parola veya SSH sıfırlama](../linux/classic/reset-access-classic.md).
