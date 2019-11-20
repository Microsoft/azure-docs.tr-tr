---
title: Linux VM’leri için Windows ile SSH anahtarlarını kullanma
description: Azure 'da Linux sanal makinesine bağlanmak için bir Windows bilgisayarda SSH anahtarları oluşturma ve kullanma hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: e01fb23bbf1720f7d8df9c269373c1b8dc3ec75c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034800"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure 'da Windows ile SSH anahtarlarını kullanma

Bu makalede, Azure 'da bir Linux sanal makinesi (VM) oluşturmak ve buna bağlanmak için bir Windows bilgisayarda *Secure Shell* (SSH) anahtarları oluşturma ve kullanma yolları açıklanmaktadır. Bir Linux veya macOS istemcisinden SSH anahtarları kullanmak için bkz. [hızlı](mac-create-ssh-keys.md) veya [ayrıntılı](create-ssh-keys-detailed.md) kılavuz.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows paketleri ve SSH istemcileri
Bir *SSH istemcisi*kullanarak Azure 'Da Linux VM 'lerine bağlanır ve bunları yönetebilirsiniz. Linux veya macOS çalıştıran bilgisayarlar genellikle SSH anahtarları oluşturup yönetmek ve SSH bağlantıları oluşturmak için bir SSH komutları paketine sahiptir. 

Windows bilgisayarlarda her zaman karşılaştırılabilir SSH komutları yüklü değildir. Windows 10 ' un son sürümleri, SSH anahtarları oluşturup yönetmek ve bir komut isteminden SSH bağlantısı yapmak için [OpenSSH istemci komutları](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) sağlar. Son Windows 10 sürümleri, [Linux Için Windows alt sistemi](https://docs.microsoft.com/windows/wsl/about) 'ni, bir bash kabuğu içinde yerel olarak bir SSH istemcisi gibi çalıştırmak ve erişmek için de içerir. 

Yerel olarak yükleyebileceğiniz diğer yaygın Windows SSH istemcileri aşağıdaki paketlere dahildir:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Windows Için git](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Ayrıca, [Azure Cloud Shell](../../cloud-shell/overview.md)Bash 'de bulunan SSH yardımcı programlarını da kullanabilirsiniz. 

* [https://shell.azure.com](https://shell.azure.com) veya [Azure portal](https://portal.azure.com)Web tarayıcınıza Cloud Shell erişin. 
* [Azure hesap uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)yükleyerek Visual Studio Code içinden terminal olarak Cloud Shell erişin.

## <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma
Aşağıdaki bölümlerde Windows üzerinde bir SSH anahtar çifti oluşturmak için iki seçenek açıklanır. Kabuk komutu (`ssh-keygen`) veya GUI aracı (PuTTYgen) kullanabilirsiniz. Ayrıca, PowerShell kullanarak bir anahtar oluşturmak için ortak anahtarı SSH. com (SECSH) biçiminde karşıya yükleyin. CLı kullanırken, yüklemeden önce anahtarı OpenSSH biçimine dönüştürün. 

### <a name="create-ssh-keys-with-ssh-keygen"></a>SSH-keygen ile SSH anahtarları oluşturma

Windows üzerinde SSH istemci araçları 'nı destekleyen bir komut kabuğu çalıştırırsanız (veya Azure Cloud Shell kullanıyorsanız), `ssh-keygen` komutunu kullanarak bir SSH anahtar çifti oluşturun. Aşağıdaki komutu yazın ve istemleri yanıtlayın. Seçilen konumda bir SSH anahtar çifti varsa, bu dosyaların üzerine yazılır. 

```bash
ssh-keygen -t rsa -b 2048
```

Daha fazla arka plan ve bilgi için `ssh-keygen`kullanarak SSH anahtarları oluşturma hakkında [hızlı](mac-create-ssh-keys.md) veya [ayrıntılı](create-ssh-keys-detailed.md) adımlara bakın.

### <a name="create-ssh-keys-with-puttygen"></a>PuTTYgen ile SSH anahtarları oluşturma

SSH anahtarları oluşturmak için GUI tabanlı bir araç kullanmayı tercih ediyorsanız, [Putty indirme paketine](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)dahil edilen PuTTYgen anahtar oluşturucusunu kullanabilirsiniz. 

PuTTYgen ile SSH RSA anahtar çifti oluşturmak için:

1. PuTTYgen başlatın.

2. **Oluştur**' a tıklayın. Varsayılan olarak PuTTYgen, 2048 bitlik bir SSH-2 RSA anahtarı oluşturur.

4. Anahtar için rastgele bir açıklık sağlamak üzere fareyi boş alana taşıyın.

5. Ortak anahtar oluşturulduktan sonra, isteğe bağlı olarak bir parola girin ve onaylayın. Özel SSH anahtarınızla VM 'de kimlik doğrulaması yaptığınızda parola istenir. Bir parola olmadan, birisi özel anahtarınızı alırsa, bu anahtarı kullanan herhangi bir VM veya hizmette oturum açabilir. Bir parola oluşturmanızı öneririz. Ancak, parolayı unutursanız, bunu kurtarma yolu yoktur.

6. Ortak anahtar pencerenin üst kısmında görüntülenir. Bu tüm ortak anahtarı kopyalayabilir ve ardından bir Linux sanal makinesi oluştururken Azure portal veya bir Azure Resource Manager şablonuna yapıştırabilirsiniz. Ayrıca, bilgisayarınıza bir kopyasını kaydetmek için **ortak anahtarı kaydet** ' i de seçebilirsiniz:

    ![PuTTY ortak anahtar dosyasını Kaydet](./media/ssh-from-windows/save-public-key.png)

7. İsteğe bağlı olarak, özel anahtarı PuTTy özel anahtar biçiminde (. PPK dosyası) kaydetmek için **özel anahtarı kaydet**' i seçin. VM 'ye SSH bağlantısı oluşturmak için PuTTY kullanmak üzere, daha sonra. PPK dosyasına ihtiyacınız olacak.

    ![PuTTY özel anahtar dosyasını Kaydet](./media/ssh-from-windows/save-ppk-file.png)

    Özel anahtarı, çok sayıda SSH istemcisi tarafından kullanılan özel anahtar biçimi olan OpenSSH biçiminde kaydetmek istiyorsanız, > **dönüştürme** ' yi seçin ve **OpenSSH anahtarını dışarı aktarın**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>VM dağıtımında SSH ortak anahtarı sağlama

Kimlik doğrulaması için SSH anahtarları kullanan bir Linux sanal makinesi oluşturmak için, Azure portal veya diğer yöntemleri kullanarak VM oluştururken SSH ortak anahtarınızı sağlayın.

Aşağıdaki örnek, bir Linux sanal makinesi oluştururken bu ortak anahtarı Azure portal nasıl kopyalayıp yapıştırabileceğinizi gösterir. Ortak anahtar genellikle yeni VM 'nizin ~/. SSH/authorized_key dizininde depolanır.

   ![Azure portal bir VM oluşturduğunuzda ortak anahtarı kullanın](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Sanal makinenize bağlanma

Windows 'dan Linux sanal makinenize SSH bağlantısı yapmanın bir yolu SSH istemcisi kullanmaktır. Bu, Windows sisteminizde yüklü bir SSH istemciniz varsa veya Azure Cloud Shell Bash 'de SSH araçlarını kullanıyorsanız tercih edilen yöntemdir. GUI tabanlı bir aracı tercih ediyorsanız, PuTTY ile bağlanabilirsiniz.  

### <a name="use-an-ssh-client"></a>SSH istemcisi kullanma
Azure sanal makinenize dağıtılan ortak anahtar ve yerel sisteminizdeki özel anahtar ile sanal makinenizin IP adresini veya DNS adını kullanarak sanal makinenize SSH 'yi kullanın. Aşağıdaki komutta *azureuser* ve *myvm.westus.cloudapp.Azure.com* değerini Yönetici Kullanıcı adı ve tam etki alanı adı (veya IP adresi) ile değiştirin:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Anahtar çiftinizi oluştururken bir parola yapılandırdıysanız, oturum açma işlemi sırasında istendiğinde parolayı girin.

VM tam zamanında erişim ilkesi kullanıyorsa, VM 'ye bağlanabilmeniz için önce erişim istemeniz gerekir. Tam zamanında ilkesi hakkında daha fazla bilgi için, bkz. [tam zamanında ilkesini kullanarak sanal makine erişimini yönetme](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>PuTTY ile bağlanma

[Putty indirme paketini](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) yüklediyseniz ve daha önce bir Putty özel anahtar (. PPK) dosyası oluşturduysanız, Putty Ile BIR Linux VM 'ye bağlanabilirsiniz.

1. PuTTy başlatın.

2. Azure portal sanal makinenizin ana bilgisayar adını veya IP adresini girin:

    ![Yeni PuTTY bağlantısı aç](./media/ssh-from-windows/putty-new-connection.png)

3. **SSH** > **AUTH** kategorisini > **bağlantı** ' nu seçin. PuTTY özel anahtarınızı (. PPK dosyası) bulup seçin:

    ![Kimlik doğrulaması için PuTTY özel anahtarınızı seçin](./media/ssh-from-windows/putty-auth-dialog.png)

4. VM 'nize bağlanmak için **Aç** ' a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* SSH anahtarlarıyla çalışmaya yönelik ayrıntılı adımlar, Seçenekler ve gelişmiş örnekler için bkz. [SSH anahtar çiftleri oluşturmaya yönelik ayrıntılı adımlar](create-ssh-keys-detailed.md).

* SSH anahtarları oluşturmak ve Linux VM 'lerine SSH bağlantısı yapmak için Azure Cloud Shell 'de PowerShell de kullanabilirsiniz. Bkz. [PowerShell hızlı](../../cloud-shell/quickstart-powershell.md#ssh)başlangıcı.

* Linux sanal makinelerinize bağlanmak için SSH kullanma konusunda zorluk yaşıyorsanız bkz. [Azure LINUX VM Ile SSH bağlantılarında sorun giderme](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
