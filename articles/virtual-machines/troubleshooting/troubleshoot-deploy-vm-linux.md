---
title: Azure 'da Linux sanal makine sorunlarını dağıtma sorunlarını giderme | Microsoft Docs
description: Azure Resource Manager dağıtım modelinde Linux sanal makine sorunlarını dağıtma sorunlarını giderin.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 230319471dbf62bf64ee980d84c360807687ce17
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058223"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Azure 'da Linux sanal makine sorunlarını dağıtma sorunlarını giderme

Azure 'da sanal makine (VM) dağıtımı sorunlarını gidermek için sık karşılaşılan hataların ve çözümlerin [en önemli sorunlarını](#top-issues) gözden geçirin.

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayına dosya. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.

## <a name="top-issues"></a>En önemli sorunlar
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Küme, istenen VM boyutunu desteklemez
\<Properties Supporttopicıds = "123456789" resourceTags = "Windows" Productpesıd = "1234, 5678"/>
- Daha küçük bir VM boyutu kullanarak isteği yeniden deneyin.
- İstenen VM 'nin boyutu değiştirilenemez:
    - Kullanılabilirlik kümesindeki tüm VM 'Leri durdurun. Kaynak **grupları** > kaynak grubu ' na tıklayın **> kaynak** grubunuz > sanal **makineleriniz > sanal makineler** > **Durdur**>
    - Tüm VM 'Ler durduktan sonra, istenen boyutta VM 'yi oluşturun.
    - Önce yeni VM 'yi başlatın ve sonra durdurulan sanal makinelerin her birini seçip Başlat ' a tıklayın.


## <a name="the-cluster-does-not-have-free-resources"></a>Kümede boş kaynak yok
\<Properties Supporttopicıds = "123456789" resourceTags = "Windows" Productpesıd = "1234, 5678"/>
- İsteği daha sonra yeniden deneyin.
- Yeni VM farklı bir kullanılabilirlik kümesinin parçası olabilir
    - Farklı bir kullanılabilirlik kümesinde (aynı bölgede) bir VM oluşturun.
    - Yeni VM 'yi aynı sanal ağa ekleyin.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Visual Studio Enterprise (BizSpark) için aylık kredimi etkinleştirmek Nasıl yaparım?

Aylık kredinizi etkinleştirmek için bu [makaleye](https://azure.microsoft.com/offers/ms-azr-0064p/)bakın.

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Ubuntu NV VM için GPU sürücüsünü neden yüklemem?

Şu anda Linux GPU desteği yalnızca Ubuntu Server 16,04 LTS çalıştıran Azure NC VM 'lerinde kullanılabilir. Daha fazla bilgi için bkz. [Linux çalıştıran N serisi VM 'ler IÇIN GPU sürücülerini ayarlama](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Linux N serisi VM 'imde sürücüm eksik

Linux tabanlı VM 'Ler için sürücüler [burada](../linux/n-series-driver-setup.md)bulunur. 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>N serisi VM My içinde bir GPU örneği bulamıyorum

Windows Server 2016 veya Windows Server 2012 R2 çalıştıran Azure N serisi VM 'lerinin GPU yetilerinden yararlanmak için, her bir VM 'ye, her bir sanal makinede NVıDıA grafik sürücülerini yüklemelisiniz. [Windows VM](../windows/n-series-driver-setup.md) 'Leri ve [Linux VM 'leri](../linux/n-series-driver-setup.md)için sürücü kurulum bilgileri kullanılabilir.

## <a name="is-n-series-vms-available-in-my-region"></a>N serisi VM 'Ler bölgemde kullanılabilir mi?

[Bölge tablosuna göre kullanılabilir ürünlerden](https://azure.microsoft.com/regions/services)kullanılabilirliği denetleyebilir ve [burada](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)fiyatlandırma yapabilirsiniz.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>VM 'imi yeniden boyutlandırırken istediğim VM boyut ailesini göremiyorum.

Bir VM çalışırken bir fiziksel sunucuya dağıtılır. Azure bölgelerindeki fiziksel sunucular, ortak fiziksel donanım kümelerinde gruplandırılır. VM 'nin farklı donanım kümelerine taşınmasını gerektiren bir VM 'nin yeniden boyutlandırılması, VM 'yi dağıtmak için kullanılan dağıtım modeline bağlı olarak farklılık gösteren bir değer.

- Klasik dağıtım modelinde dağıtılan sanal makineler, bulut hizmeti dağıtımının kaldırılması ve yeniden dağıtılması gerekir ve bu da VM 'Leri başka bir boyuttaki ailedeki bir boyutla değiştirin.

- Kaynak Yöneticisi dağıtım modelinde dağıtılan VM 'Ler, kullanılabilirlik kümesindeki herhangi bir sanal makinenin boyutunu değiştirmeden önce kullanılabilirlik kümesindeki tüm VM 'Leri durdurmanız gerekir.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Listelenen VM boyutu, kullanılabilirlik kümesinde dağıtıldığında desteklenmez.

Kullanılabilirlik kümesinin kümesinde desteklenen bir boyut seçin. İhtiyaç duyduğunuz en büyük VM boyutunu seçmek için bir kullanılabilirlik kümesi oluşturulması ve kullanılabilirlik kümesine ilk dağıtımınız olması önerilir.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Azure 'da hangi Linux dağıtımları/sürümleri destekleniyor?

[Azure tarafından onaylanan dağıtımların](../linux/endorsed-distros.md)listesini Linux 'ta bulabilirsiniz.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Mevcut bir klasik VM 'yi bir kullanılabilirlik kümesine ekleyebilir miyim?

Evet. Mevcut bir klasik VM 'yi yeni veya var olan bir kullanılabilirlik kümesine ekleyebilirsiniz. Daha fazla bilgi için bkz. [var olan bir sanal makineyi bir kullanılabilirlik kümesine ekleme](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Sonraki adımlar
Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun.

Alternatif olarak, bir Azure destek olayına dosya. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
