---
title: Linux VM 'lerine bağlanmak için SSH anahtarlarını kullanma
description: Azure 'da Linux sanal makinesine bağlanmak için bir Windows bilgisayarından SSH anahtarları oluşturmayı ve kullanmayı öğrenin.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 0199a47b2306d7d461ba61057c7ab1015015df08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835569"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Azure 'da Windows ile SSH anahtarlarını kullanma

Bu makale, Azure 'da Linux sanal makinelerine (VM 'Ler) [bağlanmak](#connect-to-your-vm) için *Güvenli Kabuk* (SSH) anahtarları [oluşturup](#create-an-ssh-key-pair) kullanmak isteyen Windows kullanıcılarına yöneliktir. Ayrıca, portalda VM oluştururken kullanmak üzere [Azure Portal SSH anahtarları oluşturup saklayabilirsiniz](../ssh-keys-portal.md) .


Bir Linux veya macOS istemcisinden SSH anahtarları kullanmak için [hızlı adımlara](mac-create-ssh-keys.md)bakın. SSH hakkında daha ayrıntılı bir genel bakış için bkz. [ayrıntılı adımlar: Azure 'da bir LINUX sanal makinesine kimlik doğrulaması IÇIN SSH anahtarları oluşturma ve yönetme](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>SSH ve anahtarlara genel bakış

[SSH](https://www.ssh.com/ssh/) , güvenli olmayan bağlantılarda güvenli oturum açma işlemlerinin yapılmasına izin veren şifreli bir bağlantı protokolüdür. SSH, Azure 'da barındırılan Linux VM 'Leri için varsayılan bağlantı protokolüdür. SSH 'in kendisi şifreli bir bağlantı sağlamasına karşın, SSH ile parolaların kullanılması, VM 'yi deneme yanılma saldırılarına karşı savunmasız bırakır. *SSH anahtarları* olarak da bilinen ortak özel anahtar ÇIFTINI kullanarak SSH ÜZERINDEN bir VM 'ye bağlanmanızı öneririz. 

Ortak özel anahtar çifti, ön kapılarınızın kilidi gibidir. Kilit **herkese** açıktır, doğru anahtara sahip herkes kapıyı açabilir. Anahtar **özeldir** ve yalnızca güvendiğiniz kişilere verilir ve kapısının kilidini açmak için kullanılabilir. 

- *Ortak anahtar* , VM 'Yi oluştururken Linux sanal makinenize yerleştirilir. 

- *Özel anahtar* yerel sisteminizde kalır. Bu özel anahtarı koruyun. Özel anahtarı paylaşmayın.

Linux sanal makinenize bağlandığınızda, VM, doğru özel anahtara sahip olduğundan emin olmak için SSH istemcisini sınar. İstemci özel anahtara sahipse, VM 'ye erişim izni verilir. 

Kuruluşunuzun güvenlik ilkelerine bağlı olarak, birden fazla Azure VM ve hizmetine erişmek için tek bir anahtar çiftini yeniden kullanabilirsiniz. Her VM için ayrı bir anahtar çifti gerekmez. 

Ortak anahtarınız herkese paylaşılabilir, ancak yalnızca sizin (veya yerel güvenlik altyapınız) özel anahtarınıza erişebilmelidir.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH istemcileri

Windows 10 ' un son sürümleri, SSH anahtarları oluşturup kullanmak ve PowerShell 'den veya bir komut isteminden SSH bağlantısı yapmak için [OpenSSH istemci komutlarını](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) içerir. Bu, bir Windows bilgisayarından Linux sanal makinenize SSH bağlantısı oluşturmanın en kolay yoludur. 

VM 'nize bağlanmak için [Azure Cloud Shell](../../cloud-shell/overview.md) Bash 'i de kullanabilirsiniz. Cloud Shell bir [Web tarayıcısında](https://shell.azure.com/bash), [Azure Portal](https://portal.azure.com)veya Visual Studio Code [Azure hesap uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)kullanarak bir Terminal olarak kullanabilirsiniz.

Ayrıca, [Linux Için Windows alt sistemini](/windows/wsl/about) kullanarak sanal makinenize SSH üzerinden bağlanabilir ve bash kabuğu içindeki diğer yerel Linux araçlarını kullanabilirsiniz.

## <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma

Komutunu kullanarak bir SSH anahtar çifti oluşturun `ssh-keygen` . Bir dosya adı girin veya parantez içinde gösterilen varsayılanı kullanın (örneğin `C:\Users\username/.ssh/id_rsa` ).  Dosya için bir parola girin veya parola kullanmak istemiyorsanız parolayı boş bırakın. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Anahtarınızı kullanarak VM oluşturma

Kimlik doğrulaması için SSH anahtarları kullanan bir Linux VM oluşturmak için VM oluştururken SSH ortak anahtarınızı sağlayın.

Azure CLı kullanarak, ve parametresini kullanarak ortak anahtar için yolu ve dosya adını belirtirsiniz `az vm create` `--ssh-key-value` .

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

PowerShell ile, `New-AzVM` ' i kullanarak VM YAPıLANDıRMASıNA SSH anahtarını kullanın ve ekleyin. Bir örnek için bkz. [hızlı başlangıç: Azure 'Da PowerShell Ile Linux sanal makinesi oluşturma](quick-create-powershell.md).

Portalı kullanarak çok fazla dağıtım yaparsanız, portaldan bir VM oluştururken kolayca seçilebileceğiniz ortak anahtarınızı Azure 'a yüklemek isteyebilirsiniz. Daha fazla bilgi için bkz. [SSH anahtarını karşıya yükleme](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Sanal makinenize bağlanma

Azure sanal makinenize dağıtılan ortak anahtar ve yerel sisteminizdeki özel anahtar ile sanal makinenizin IP adresini veya DNS adını kullanarak sanal makinenize SSH 'yi kullanın. Aşağıdaki komutta *azureuser* ve *10.111.12.123* değerini yönetıcı Kullanıcı adı, IP adresi (veya tam etki alanı adı) ve özel anahtarınızın yolu ile değiştirin:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Anahtar çiftinizi oluştururken bir parola yapılandırdıysanız, istendiğinde parolayı girin.

VM tam zamanında erişim ilkesi kullanıyorsa, VM 'ye bağlanabilmeniz için önce erişim istemeniz gerekir. Tam zamanında ilkesi hakkında daha fazla bilgi için, bkz. [tam zamanında ilkesini kullanarak sanal makine erişimini yönetme](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Sonraki adımlar

- Azure portal SSH anahtarları hakkında daha fazla bilgi için bkz. portalda VM oluştururken kullanılacak [Azure Portal SSH anahtarları oluşturma ve depolama](../ssh-keys-portal.md) .

- SSH anahtarlarıyla çalışmaya yönelik ayrıntılı adımlar, Seçenekler ve gelişmiş örnekler için bkz. [SSH anahtar çiftleri oluşturmaya yönelik ayrıntılı adımlar](create-ssh-keys-detailed.md).

- SSH anahtarları oluşturmak ve Linux VM 'lerine SSH bağlantısı yapmak için Azure Cloud Shell 'de PowerShell de kullanabilirsiniz. Bkz. [PowerShell hızlı](../../cloud-shell/quickstart-powershell.md#ssh)başlangıcı.

- Linux sanal makinelerinize bağlanmak için SSH kullanma konusunda zorluk yaşıyorsanız bkz. [Azure LINUX VM Ile SSH bağlantılarında sorun giderme](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
