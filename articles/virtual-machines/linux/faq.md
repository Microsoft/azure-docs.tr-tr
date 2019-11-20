---
title: Azure 'da Linux VM 'Ler için sık sorulan sorular
description: Kaynak Yöneticisi modeliyle oluşturulan Linux sanal makineleri hakkında bazı yaygın soruların yanıtlarını sağlar.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 107f2376df7eb767953b32b3447937e24869894c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036162"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Linux Sanal Makineleri hakkında sık sorulan sorular
Bu makalede, Azure 'da Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan Linux sanal makineleri hakkında bazı yaygın soruların ele alınmaktadır. Bu konunun Windows sürümü için bkz. [hakkında sık sorulan sorular Windows sanal makineleri](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure sanal makinesinde ne çalıştırabilirim?
Tüm aboneler bir Azure sanal makinesinde sunucu yazılımı çalıştırabilir. Daha fazla bilgi için bkz. [Azure onaylı dağıtımlara Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Bir sanal makineyle birlikte ne kadar depolama alanı kullanabilirim?
Her veri diski 32.767 GiB 'a kadar olabilir. Kullanabileceğiniz veri diski sayısı, sanal makinenin boyutuna bağlıdır. Ayrıntılar için bkz. [Virtual Machines boyutları](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure yönetilen diskler, verilerin kalıcı olarak depolanması için Azure sanal makineler ile kullanılmak üzere önerilen disk depolama tekliflerdir. Her bir Sanal Makine ile birden fazla Yönetilen Disk kullanabilirsiniz. Yönetilen Diskler iki tür dayanıklı depolama seçeneği sunar: Premium ve Standart Yönetilen Diskler. Fiyatlandırma bilgileri için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks).

Azure depolama hesapları, işletim sistemi diski ve tüm veri diskleri için depolama alanı da sağlayabilir. Her disk bir sayfa blobu olarak depolanan bir .vhd dosyasıdır. Fiyatlandırma ayrıntıları için bkz. [Depolama Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Sanal makinime nasıl erişebilirim?
Secure Shell (SSH) kullanarak sanal makinede oturum açmak için bir uzak bağlantı kurun. [Windows 'dan](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya [Linux ve Mac 'ten](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)nasıl bağlanacağınız hakkında yönergeler için bkz. Varsayılan olarak, SSH en fazla 10 eş zamanlı bağlantıya izin verir. Yapılandırma dosyasını düzenleyerek bu sayıyı artırabilirsiniz.

Sorun yaşıyorsanız [Secure Shell (SSH) bağlantılarında sorun giderme](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)' yi inceleyin.

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Verileri depolamak için geçici diski (/dev/sdb1) kullanabilir miyim?
Verileri depolamak için geçici diski (/dev/sdb1) kullanmayın. Yalnızca geçici depolama alanı için geçerlidir. Kurtarılamamış verileri kaybetme riskini alırsınız.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Mevcut bir Azure VM 'yi kopyalayabilir veya kopyalayabilir miyim?
Evet. Yönergeler için, bkz. [Kaynak Yöneticisi dağıtım modelinde Linux sanal makinesinin kopyasını oluşturma](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Neden Kanada Orta ve Kanada Doğu bölgelerini Azure Resource Manager göremiyorum?
Kanada Orta ve Kanada Doğu iki yeni bölgesi, mevcut Azure abonelikleri için sanal makine oluşturma için otomatik olarak kaydedilmemiş. Bu kayıt, bir sanal makine Azure Resource Manager kullanılarak başka bir bölgeye Azure portal aracılığıyla dağıtıldığında otomatik olarak yapılır. Bir sanal makine başka bir Azure bölgesine dağıtıldıktan sonra, yeni bölgeler sonraki sanal makineler için kullanılabilir olmalıdır.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Oluşturulduktan sonra VM 'ye bir NIC ekleyebilir miyim?
Evet, bu artık mümkündür. Önce VM 'nin serbest bırakılmasının durdurulması gerekir. Ardından, bir NIC ekleyebilir veya kaldırabilirsiniz (VM 'deki son NIC olmadığı müddetçe). 

## <a name="are-there-any-computer-name-requirements"></a>Herhangi bir bilgisayar adı gereksinimi var mı?
Evet. Bilgisayar adı en fazla 64 karakter uzunluğunda olabilir. Kaynaklarınızı adlandırma hakkında daha fazla bilgi için bkz. [adlandırma kuralları kuralları ve kısıtlamaları](/azure/architecture/best-practices/resource-naming) .

## <a name="are-there-any-resource-group-name-requirements"></a>Herhangi bir kaynak grubu adı gereksinimi var mı?
Evet. Kaynak grubu adı en fazla 90 karakter uzunluğunda olabilir. Kaynak grupları hakkında daha fazla bilgi için bkz. [adlandırma kuralları kuralları ve kısıtlamaları](/azure/architecture/best-practices/resource-naming) .

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>VM oluştururken Kullanıcı adı gereksinimleri nelerdir?

Kullanıcı adları 1-32 karakter uzunluğunda olmalıdır.

Şu kullanıcı adlarıyla izin verilmiyor:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>VM oluştururken parola gereksinimleri nelerdir?

Kullanmakta olduğunuz araca bağlı olarak, farklı parola uzunluğu gereksinimleri vardır:
 - Portal-12-72 karakter arası
 - PowerShell-8-123 karakter arası
 - CLı-12-123 arasında
 

Parolaların Ayrıca aşağıdaki 4 karmaşıklık gereksinimlerinden 3 ' ü karşılaması gerekir:

* Küçük karakterler var
* Büyük karakterlere sahip
* Bir rakam
* Özel bir karakter (Regex Match [\ W_])

Aşağıdaki parolalara izin verilmiyor:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Parolayı!</td>
        <td style="text-align:center">Parola1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">o kadar!</td>
    </tr>
</table>
