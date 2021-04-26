---
title: Azure 'da Linux VM 'Ler için sık sorulan sorular
description: Kaynak Yöneticisi modeliyle oluşturulan Linux sanal makineleri hakkında bazı yaygın soruların yanıtlarını sağlar.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: d510fa35163a4c4a79352f5f35af9c384bf85304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596323"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Linux Sanal Makineleri hakkında sık sorulan sorular
Bu makalede, Azure 'da Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan Linux sanal makineleri hakkında bazı yaygın soruların ele alınmaktadır. Bu konunun Windows sürümü için bkz. [hakkında sık sorulan sorular Windows sanal makineleri](../windows/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Azure sanal makinesinde ne çalıştırabilirim?
Tüm aboneler bir Azure sanal makinesinde sunucu yazılımı çalıştırabilir. Daha fazla bilgi için bkz. [Azure-Endorsed dağıtımlarında Linux](endorsed-distros.md)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Bir sanal makineyle birlikte ne kadar depolama alanı kullanabilirim?
Her veri diski 32.767 GiB 'a kadar olabilir. Kullanabileceğiniz veri diski sayısı, sanal makinenin boyutuna bağlıdır. Ayrıntılar için bkz. [Virtual Machines boyutları](../sizes.md).

Azure yönetilen diskler, verilerin kalıcı olarak depolanması için Azure sanal makineler ile kullanılmak üzere önerilen disk depolama tekliflerdir. Her bir sanal makine ile birden fazla yönetilen disk kullanabilirsiniz. Yönetilen diskler iki tür dayanıklı depolama seçeneği sunar: Premium ve standart yönetilen diskler. Fiyatlandırma bilgileri için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks).

Azure depolama hesapları, işletim sistemi diski ve tüm veri diskleri için depolama alanı da sağlayabilir. Her disk bir sayfa blobu olarak depolanan bir .vhd dosyasıdır. Fiyatlandırma ayrıntıları için bkz. [Depolama Fiyatlandırma Ayrıntıları](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Sanal makinime nasıl erişebilirim?
Secure Shell (SSH) kullanarak sanal makinede oturum açmak için bir uzak bağlantı kurun. [Windows 'dan](ssh-from-windows.md) veya [Linux ve Mac 'ten](mac-create-ssh-keys.md)nasıl bağlanacağınız hakkında yönergeler için bkz.. Varsayılan olarak, SSH en fazla 10 eş zamanlı bağlantıya izin verir. Yapılandırma dosyasını düzenleyerek bu sayıyı artırabilirsiniz.

Sorun yaşıyorsanız [Secure Shell (SSH) bağlantılarında sorun giderme](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)' yi inceleyin.

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Verileri depolamak için geçici diski (/dev/sdb1) kullanabilir miyim?
Verileri depolamak için geçici diski (/dev/sdb1) kullanmayın. Yalnızca geçici depolama alanı için geçerlidir. Kurtarılamamış verileri kaybetme riskini alırsınız.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Mevcut bir Azure VM 'yi kopyalayabilir veya kopyalayabilir miyim?
Evet. Yönergeler için, bkz. [Kaynak Yöneticisi dağıtım modelinde Linux sanal makinesinin kopyasını oluşturma](copy-vm.md).

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

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>VM oluştururken parola gereksinimleri nelerdir?

Kullanmakta olduğunuz araca bağlı olarak, farklı parola uzunluğu gereksinimleri vardır:
 - Portal-12-72 karakter arası
 - PowerShell-8-123 karakter arası
 - CLı-12-123 karakter arası
 - Azure Resource Manager (ARM) şablonları-12-72 karakter ve denetim karakterlerine izin verilmez
 

Parolaların Ayrıca aşağıdaki 4 karmaşıklık gereksinimlerinden 3 ' ü karşılaması gerekir:

* Küçük harfli karakter içermeli
* Büyük harfli karakter içermeli
* Rakam içermeli
* Özel karakter içermeli (Regex eşleşmesi [\W_])

Aşağıdaki parolalara izin verilmiyor:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@ $ $w 0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA $ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Parolayı!</td>
        <td style="text-align:center">Parola1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">o kadar!</td>
    </tr>
</table>