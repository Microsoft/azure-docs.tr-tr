---
title: SCP ile Azure Linux VM 'lerine/dosyalarından dosya taşıma
description: SCP 'yi ve SSH anahtar çiftini kullanarak Azure 'da Linux VM 'ye ve bu sanal makineye dosyaları güvenle taşıyın.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: d78a8e59a55718048df2022cec75c7a2b56f1a6b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036593"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>SCP kullanarak bir Linux VM 'ye veya buradan dosya taşıma

Bu makalede, güvenli kopya (SCP) kullanarak iş istasyonunuzdan bir Azure Linux VM 'sine veya bir Azure Linux VM 'sinden iş istasyonunuza kadar dosya taşıma işlemlerinin nasıl yapılacağı gösterilir. Hızlı ve güvenli bir şekilde iş istasyonunuz ve Linux VM arasında dosya taşımak, Azure altyapınızı yönetmek için önemlidir. 

Bu makalede, [SSH ortak ve özel anahtar dosyaları](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)kullanılarak Azure 'da dağıtılan BIR Linux sanal makinesine ihtiyacınız vardır. Ayrıca yerel bilgisayarınız için bir SCP istemcisine ihtiyacınız vardır. SSH üzerinde oluşturulmuştur ve çoğu Linux ve Mac bilgisayarın ve bazı Windows kabukların varsayılan bash kabuğu 'nda bulunur.

## <a name="quick-commands"></a>Hızlı komutlar

Linux VM 'ye bir dosya kopyalama

```bash
scp file azureuser@azurehost:directory/targetfile
```

Linux VM 'den bir dosyayı aşağı kopyalama

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Ayrıntılı kılavuz

Örnek olarak, bir Azure yapılandırma dosyasını bir Linux sanal makinesine taşıdık ve hem SCP hem de SSH anahtarlarını kullanarak bir günlük dosyası dizini çekeceğiz.   

## <a name="ssh-key-pair-authentication"></a>SSH anahtar çifti kimlik doğrulaması

SCP, aktarım katmanı için SSH kullanır. SSH, hedef konaktaki kimlik doğrulamasını işler ve dosyayı SSH ile varsayılan olarak belirtilen şifrelenmiş bir tünelde alır. SSH kimlik doğrulaması için, Kullanıcı adları ve parolalar kullanılabilir. Ancak, SSH ortak ve özel anahtar kimlik doğrulaması, en iyi güvenlik uygulaması olarak önerilir. SSH bağlantıyı doğrulandıktan sonra, SCP dosyayı kopyalamaya başlar. Düzgün yapılandırılmış bir `~/.ssh/config` ve SSH ortak ve özel anahtarları kullanarak, yalnızca bir sunucu adı (veya IP adresi) kullanılarak SCP bağlantısı oluşturulabilir. Yalnızca bir SSH anahtarınız varsa, SCP onu `~/.ssh/` dizininde arar ve varsayılan olarak VM 'de oturum açmak için kullanır.

`~/.ssh/config` ve SSH ortak ve özel anahtarlarınızı yapılandırma hakkında daha fazla bilgi için bkz. [SSH anahtarları oluşturma](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP 'yi Linux sanal makinesine bir dosya

İlk örnekte, Otomasyonu dağıtmak için kullanılan bir Linux sanal makinesine bir Azure yapılandırma dosyası kopyalayacağız. Bu dosya gizli dizileri içeren Azure API kimlik bilgilerini içerdiğinden güvenlik önemlidir. SSH tarafından sunulan şifreli tünel, dosyanın içeriğini korur.

Aşağıdaki komut yerel *. Azure/config* dosyasını FQDN *myserver.eastus.cloudapp.Azure.com*ile bir Azure VM 'sine kopyalar. Azure VM 'deki Yönetici Kullanıcı adı *azureuser*. Dosya */Home/azureuser/* dizinine yöneliktir. Bu komutta kendi değerlerinizi yerine koyun.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP bir Linux VM 'den bir dizin

Bu örnekte, Linux VM 'sinden bir günlük dosyası dizinini iş istasyonunuza kopyalayacağız. Bir günlük dosyası gizli veya gizli veriler içermeyebilir veya içermeyebilir. Ancak SCP 'nin kullanılması, günlük dosyalarının içeriklerinin şifrelenmesini sağlar. Dosyaları aktarmak için SCP 'nin kullanılması, günlük dizinini ve dosyalarını iş istasyonunuza daha da güvenli hale getirmenin en kolay yoludur.

Aşağıdaki komut, Azure VM 'deki */Home/azureuser/logs/* dizinindeki dosyaları yerel/tmp dizinine kopyalar:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r` CLI bayrağı, SCP 'nin dosyaları ve dizinleri komutta listelenen dizin noktasından yinelemeli olarak kopyalamasını ister.  Ayrıca, komut satırı sözdiziminin `cp` Copy komutuna benzer olduğuna dikkat edin.

## <a name="next-steps"></a>Sonraki adımlar

* [VMAccess uzantısını kullanarak Azure Linux VM 'lerinde kullanıcıları, SSH 'yi yönetme ve diskleri denetleme veya onarma](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
