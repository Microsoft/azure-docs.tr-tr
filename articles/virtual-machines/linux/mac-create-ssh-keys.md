---
title: Azure 'da Linux VM 'Ler için SSH anahtar çifti oluşturma ve kullanma
description: Azure 'da Linux VM 'Ler için SSH genel-özel anahtar çifti oluşturma ve kullanma, kimlik doğrulama işleminin güvenliğini artırmak için.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c5e683e1f5af42a69fac45c20f52169834967649
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788142"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Hızlı adımlar: Azure 'da Linux VM 'Ler için SSH genel-özel anahtar çifti oluşturma ve kullanma

Güvenli Kabuk (SSH) anahtar çifti ile Azure 'da kimlik doğrulaması için SSH anahtarları kullanan sanal makineler (VM 'Ler) oluşturabilirsiniz. Bu makalede, Linux VM 'Ler için SSH ortak özel anahtar dosya çiftinin hızlı bir şekilde nasıl oluşturulacağı ve kullanılacağı gösterilmektedir. Bu adımları Azure Cloud Shell, macOS veya Linux ana bilgisayarı ile tamamlayabilirsiniz. 

> [!NOTE]
> SSH anahtarları kullanılarak oluşturulan VM 'Ler, varsayılan olarak, bir deneme yanılma saldırısı zorluğunu büyük ölçüde artıran parolalar devre dışı olarak yapılandırılır. 

Daha fazla arka plan ve örnek için bkz. [SSH anahtar çiftleri oluşturmaya yönelik ayrıntılı adımlar](create-ssh-keys-detailed.md).

Windows bilgisayarda SSH anahtarları oluşturma ve kullanma hakkında ek yollar için bkz. [Azure 'Da Windows Ile SSH anahtarlarını kullanma](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma

`ssh-keygen`SSH ortak ve özel anahtar dosyaları oluşturmak için komutunu kullanın. Varsayılan olarak, bu dosyalar ~/PST SSH dizininde oluşturulur. Özel anahtar dosyasına erişmek için farklı bir konum ve isteğe bağlı bir parola (*parola*) belirtebilirsiniz. Verilen konumda aynı ada sahip bir SSH anahtar çifti varsa, bu dosyaların üzerine yazılır.

Aşağıdaki komut RSA şifrelemesini ve 4096 bit uzunluğunu kullanarak bir SSH anahtar çifti oluşturur:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

[Az VM Create](/cli/azure/vm#az_vm_create) komutuyla VM 'nizi oluşturmak IÇIN [Azure CLI](/cli/azure) kullanıyorsanız, isteğe bağlı olarak, seçeneğini kullanarak SSH ortak ve özel anahtar dosyaları oluşturabilirsiniz `--generate-ssh-keys` . Anahtar dosyaları, seçeneğiyle, aksi belirtilmedikçe ~/PST SSH dizininde depolanır `--ssh-dest-key-path` . Bir SSH anahtar çifti zaten varsa ve  `--generate-ssh-keys` seçenek kullanılırsa, yeni bir anahtar çifti oluşturulmaz, bunun yerine var olan anahtar çifti kullanılacaktır. Aşağıdaki komutta, *VMName* ve *RgName* değerlerini kendi değerlerinizle değiştirin:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>VM dağıtımında SSH ortak anahtarı sağlama

Kimlik doğrulaması için SSH anahtarları kullanan bir Linux sanal makinesi oluşturmak için Azure portal, Azure CLı, Azure Resource Manager şablonları veya diğer yöntemleri kullanarak VM oluştururken SSH ortak anahtarınızı belirtin:

* [Azure portalı ile Linux sanal makinesi oluşturma](quick-create-portal.md)
* [Azure CLI ile Linux sanal makinesi oluşturma](quick-create-cli.md)
* [Bir Azure şablonu kullanarak bir Linux VM oluşturma](create-ssh-secured-vm-from-template.md)

SSH ortak anahtarının biçimiyle ilgili bilgi sahibi değilseniz, ortak anahtarınızı aşağıdaki komutla görüntüleyebilirsiniz ve `cat` `~/.ssh/id_rsa.pub` gerekirse kendi ortak anahtar dosyanızın yolu ve dosya adı ile değiştirin:

```bash
cat ~/.ssh/id_rsa.pub
```

Tipik bir ortak anahtar değeri şu örneğe benzer şekilde görünür:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Azure portal veya Kaynak Yöneticisi şablonunda kullanmak üzere ortak anahtar dosyasının içeriğini kopyalayıp yapıştırırsanız, sondaki boşluğu kopyalamadığınızdan emin olun. MacOS 'ta ortak anahtar kopyalamak için ortak anahtar dosyasını ' a kanal oluşturarak aktarabilirsiniz `pbcopy` . Linux 'ta benzer şekilde, ortak anahtar dosyasını gibi programlara kanal oluşturarak aktarabilirsiniz `xclip` .

Azure 'daki Linux sanal makinenize yerleştirdiğiniz ortak anahtar, anahtar çiftini oluştururken farklı bir konum belirtmediğiniz müddetçe varsayılan olarak ~/. ssh/id_rsa. pub ' da depolanır. VM 'nizi var olan bir ortak anahtarla oluşturmak için [Azure clı 2,0](/cli/azure) 'yi kullanmak için, seçeneği ile [az VM Create](/cli/azure/vm#az_vm_create) komutunu kullanarak bu ortak anahtarın değerini ve isteğe bağlı olarak konumunu belirtin `--ssh-key-values` . Aşağıdaki komutta *Myvm*, *myresourcegroup*, *ubuntults*, *azureuser* ve *hayal shkey. pub* değerlerini kendi değerlerinizle değiştirin:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

VM 'niz ile birden çok SSH anahtarı kullanmak istiyorsanız, bunları buna benzer bir yere ayrılmış listeye girebilirsiniz `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` .


## <a name="ssh-into-your-vm"></a>VM’ye SSH uygulama

Azure sanal makinenize dağıtılan ortak anahtar ve yerel sisteminizdeki özel anahtar, sanal makinenizin IP adresini veya DNS adını kullanarak sanal makinenize SSH. Aşağıdaki komutta, *azureuser* ve *myvm.westus.cloudapp.Azure.com* değerini Yönetici Kullanıcı adı ve tam etki alanı adı (veya IP adresi) ile değiştirin:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Anahtar çiftinizi oluştururken bir parola belirttiyseniz, oturum açma işlemi sırasında istendiğinde bu parolayı girin. VM, ~/. SSH/known_hosts dosyanıza eklenir ve Azure VM 'nizin ortak anahtarı değişene veya sunucu adı ~/. SSH/known_hosts öğesinden kaldırılana kadar yeniden bağlanmanız istenmez.

VM tam zamanında erişim ilkesi kullanıyorsa, VM 'ye bağlanabilmeniz için önce erişim istemeniz gerekir. Tam zamanında ilkesi hakkında daha fazla bilgi için, bkz. [tam zamanında ilkesini kullanarak sanal makine erişimini yönetme](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Sonraki adımlar

* SSH anahtar çiftleri ile çalışma hakkında daha fazla bilgi için bkz. [SSH anahtar çiftleri oluşturma ve yönetme hakkında ayrıntılı adımlar](create-ssh-keys-detailed.md).

* Azure VM 'lerine SSH bağlantılarıyla ilgili güçlükleriniz varsa bkz. [Azure LINUX VM Ile SSH bağlantılarında sorun giderme](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).