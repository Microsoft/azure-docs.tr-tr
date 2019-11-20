---
title: Azure 'da Linux VM 'Ler için SSH anahtar çifti oluşturma ve kullanma
description: Azure 'da Linux VM 'Ler için SSH genel-özel anahtar çifti oluşturma ve kullanma, kimlik doğrulama işleminin güvenliğini artırmak için.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: cb3bb6a91c25298535cfba1107b85f200031a7d6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035908"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Hızlı adımlar: Azure 'da Linux VM 'Ler için SSH genel-özel anahtar çifti oluşturma ve kullanma

Güvenli Kabuk (SSH) anahtar çifti ile Azure 'da kimlik doğrulaması için SSH anahtarları kullanan sanal makineler (VM 'Ler) oluşturabilir ve parolaların oturum açması gereksinimini ortadan kaldırabilirsiniz. Bu makalede, Linux VM 'Ler için SSH ortak özel anahtar dosya çiftinin hızlı bir şekilde nasıl oluşturulacağı ve kullanılacağı gösterilmektedir. Bu adımları Azure Cloud Shell, macOS veya Linux ana bilgisayarı, Linux için Windows alt sistemi ve OpenSSH 'yi destekleyen diğer araçları kullanarak tamamlayabilirsiniz. 

> [!NOTE]
> SSH anahtarları kullanılarak oluşturulan VM 'Ler, varsayılan olarak, bir deneme yanılma saldırısı zorluğunu büyük ölçüde artıran parolalar devre dışı olarak yapılandırılır. 

Daha fazla arka plan ve örnek için bkz. [SSH anahtar çiftleri oluşturmaya yönelik ayrıntılı adımlar](create-ssh-keys-detailed.md).

Windows bilgisayarda SSH anahtarları oluşturma ve kullanma hakkında ek yollar için bkz. [Azure 'Da Windows Ile SSH anahtarlarını kullanma](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma

SSH ortak ve özel anahtar dosyaları oluşturmak için `ssh-keygen` komutunu kullanın. Varsayılan olarak, bu dosyalar ~/PST SSH dizininde oluşturulur. Özel anahtar dosyasına erişmek için farklı bir konum ve isteğe bağlı bir parola (*parola*) belirtebilirsiniz. Verilen konumda aynı ada sahip bir SSH anahtar çifti varsa, bu dosyaların üzerine yazılır.

Aşağıdaki komut RSA şifrelemesini ve 2048 bit uzunluğunu kullanarak bir SSH anahtar çifti oluşturur:

```bash
ssh-keygen -t rsa -b 2048
```

[Az VM Create](/cli/azure/vm#az-vm-create) komutuyla VM 'nizi oluşturmak IÇIN [Azure CLI](/cli/azure) kullanıyorsanız, isteğe bağlı olarak `--generate-ssh-keys` seçeneğini kullanarak SSH ortak ve özel anahtar dosyaları oluşturabilirsiniz. Anahtar dosyalar, `--ssh-dest-key-path` seçeneği ile aksi belirtilmedikçe ~/PST dizininde depolanır. `--generate-ssh-keys` seçeneği, var olan anahtar dosyalarının üzerine yazmaz, bunun yerine bir hata döndürüyor. Aşağıdaki komutta, *VMName* ve *RgName* değerlerini kendi değerlerinizle değiştirin:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>VM dağıtımında SSH ortak anahtarı sağlama

Kimlik doğrulaması için SSH anahtarları kullanan bir Linux sanal makinesi oluşturmak için Azure portal, Azure CLı, Azure Resource Manager şablonları veya diğer yöntemleri kullanarak VM oluştururken SSH ortak anahtarınızı belirtin:

* [Azure portal bir Linux sanal makinesi oluşturun](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLı ile Linux sanal makinesi oluşturma](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure şablonu kullanarak bir Linux VM oluşturma](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

SSH ortak anahtarının biçimi hakkında bilginiz yoksa, aşağıdaki `cat` komutuyla ortak anahtarınızı görüntüleyebilir ve gerekirse `~/.ssh/id_rsa.pub` kendi ortak anahtar dosyanızın yolu ve dosya adı ile değiştirin:

```bash
cat ~/.ssh/id_rsa.pub
```

Tipik bir ortak anahtar değeri şu örneğe benzer şekilde görünür:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Azure portal veya Kaynak Yöneticisi şablonunda kullanmak üzere ortak anahtar dosyasının içeriğini kopyalayıp yapıştırırsanız, sondaki boşluğu kopyalamadığınızdan emin olun. MacOS 'ta ortak anahtar kopyalamak için ortak anahtar dosyasını **pbcopy**olarak kanal oluşturarak aktarabilirsiniz. Linux 'ta benzer şekilde, ortak anahtar dosyasını **xclip**gibi programlara kanal oluşturarak aktarabilirsiniz.

Azure 'daki Linux sanal makinenize yerleştirdiğiniz ortak anahtar, anahtar çiftini oluştururken farklı bir konum belirtmediğiniz müddetçe varsayılan olarak ~/. ssh/id_rsa. pub ' da depolanır. VM 'nizi mevcut bir ortak anahtarla oluşturmak için [Azure clı 2,0](/cli/azure) 'yi kullanmak için, `--ssh-key-value` seçeneği ile [az VM Create](/cli/azure/vm#az-vm-create) komutunu kullanarak bu ortak anahtarın değerini ve isteğe bağlı olarak konumunu belirtin. Aşağıdaki komutta, *VMName*, *RgName*ve *keyFile* değerlerini kendi değerlerinizle değiştirin:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>VM’ye SSH uygulama

Azure sanal makinenize dağıtılan ortak anahtar ve yerel sisteminizdeki özel anahtar, sanal makinenizin IP adresini veya DNS adını kullanarak sanal makinenize SSH. Aşağıdaki komutta, *azureuser* ve *myvm.westus.cloudapp.Azure.com* değerini Yönetici Kullanıcı adı ve tam etki alanı adı (veya IP adresi) ile değiştirin:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Anahtar çiftinizi oluştururken bir parola belirttiyseniz, oturum açma işlemi sırasında istendiğinde bu parolayı girin. VM, ~/. SSH/known_hosts dosyanıza eklenir ve Azure VM 'nizin ortak anahtarı değişene veya sunucu adı ~/. SSH/known_hosts öğesinden kaldırılana kadar yeniden bağlanmanız istenmez.

VM tam zamanında erişim ilkesi kullanıyorsa, VM 'ye bağlanabilmeniz için önce erişim istemeniz gerekir. Tam zamanında ilkesi hakkında daha fazla bilgi için, bkz. [tam zamanında ilkesini kullanarak sanal makine erişimini yönetme](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Sonraki adımlar

* SSH anahtar çiftleri ile çalışma hakkında daha fazla bilgi için bkz. [SSH anahtar çiftleri oluşturma ve yönetme hakkında ayrıntılı adımlar](create-ssh-keys-detailed.md).

* Azure VM 'lerine SSH bağlantılarıyla ilgili güçlükleriniz varsa bkz. [Azure LINUX VM Ile SSH bağlantılarında sorun giderme](troubleshoot-ssh-connection.md).
