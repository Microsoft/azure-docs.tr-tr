---
title: Azure 'da Windows sanal makine sorunlarını dağıtma sorunlarını giderme | Microsoft Docs
description: Azure Resource Manager dağıtım modelinde Windows sanal makine sorunlarını dağıtma sorunlarını giderin.
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
ms.openlocfilehash: 2baa82bda1f92fe81bb0db69b84e6865b2709e42
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058045"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Azure 'da Windows sanal makine sorunlarını dağıtma sorunlarını giderme

Azure 'da sanal makine (VM) dağıtımı sorunlarını gidermek için sık karşılaşılan hataların ve çözümlerin [en önemli sorunlarını](#top-issues) gözden geçirin.

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayına dosya. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.

## <a name="top-issues"></a>En önemli sorunlar
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Windows istemci görüntüsünü nasıl kullanabilirim ve Azure 'a nasıl dağıtırım?

Uygun bir Visual Studio (eski adıyla MSDN) aboneliğiniz varsa geliştirme ve test senaryoları için Windows 7, Windows 8 veya Windows 10 ' u Azure 'da kullanabilirsiniz. Bu [makalede](../windows/client-images.md) , Azure 'da Windows istemcisini çalıştırmaya yönelik uygunluk gereksinimleri ve Azure Galeri görüntülerinin kullanımları özetlenmektedir.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hibrit kullanım avantajı 'nı (HUB) kullanarak bir sanal makineyi nasıl dağıtırım?

Azure hibrit kullanım teklifi ile Windows sanal makinelerini dağıtmanın birkaç farklı yolu vardır.

Kurumsal Anlaşma abonelik için:

• Azure hibrit kullanım teklifi ile önceden yapılandırılmış belirli Market görüntülerinden VM 'Ler dağıtın.

Kurumsal Anlaşma için:

• Bir Kaynak Yöneticisi şablonu veya Azure PowerShell kullanarak özel bir VM yükleyin ve dağıtın.

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

 - [Azure hibrit kullanım avantajı genel bakış](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [İndirilebilir SSS](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Windows Server ve Windows istemcisi Için Azure hibrit kullanım avantajı](../windows/hybrid-use-benefit-licensing.md).

 - [Azure 'da hibrit kullanım teklifi 'ni nasıl kullanabilirim?](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Visual Studio Enterprise (BizSpark) için aylık kredimi etkinleştirmek Nasıl yaparım?

Aylık kredinizi etkinleştirmek için bu [makaleye](https://azure.microsoft.com/offers/ms-azr-0064p/)bakın.

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Pencere istemci görüntülerine erişim sağlamak için Kurumsal Anlaşma (EA) Kurumsal Geliştirme ve Test nasıl eklenir?

Yalnızca, Kurumsal Yönetici tarafından Kurumsal Geliştirme ve Test teklifi kapsamında abonelik oluşturma izni verilen Hesap Sahipleri bu işlemi yapabilir. Hesap sahibi Azure hesap portalı aracılığıyla abonelikler oluşturur ve ardından etkin Visual Studio abonelerini ortak yönetici olarak eklememelidir. Geliştirme ve test için gereken kaynakları yönetebilmeleri ve kullanabilmesi için. Daha fazla bilgi için bkz. [Kurumsal Geliştirme ve test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Windows N serisi VM 'imde sürücüm eksik

Windows tabanlı VM 'Ler için sürücüler [burada](../windows/n-series-driver-setup.md)bulunur.

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>N serisi VM My içinde bir GPU örneği bulamıyorum

Windows Server 2016 veya Windows Server 2012 R2 çalıştıran Azure N serisi VM 'lerinin GPU yetilerinden yararlanmak için, her bir VM 'ye, her bir sanal makinede NVıDıA grafik sürücülerini yüklemelisiniz. [Windows VM](../windows/n-series-driver-setup.md) 'Leri ve [Linux VM 'leri](../linux/n-series-driver-setup.md)için sürücü kurulum bilgileri kullanılabilir.

## <a name="is-n-series-vms-available-in-my-region"></a>N serisi VM 'Ler bölgemde kullanılabilir mi?

[Bölge tablosuna göre kullanılabilir ürünlerden](https://azure.microsoft.com/regions/services)kullanılabilirliği denetleyebilir ve [burada](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)fiyatlandırma yapabilirsiniz.

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Azure 'da hangi istemci görüntülerini kullanabilirim ve dağıtırım ve bunları nasıl edinebilirim?

Windows 7, Windows 8 veya Windows 10 ' u Azure 'da geliştirme/test senaryoları için uygun bir Visual Studio (eski adıyla MSDN) aboneliğine sahip olursunuz. 

- Windows 10 görüntüleri, [uygun geliştirme/test tekliflerindeki](../windows/client-images.md#eligible-offers)Azure galerisinden kullanılabilir. 
- Herhangi bir teklif türündeki Visual Studio aboneleri, 64 bitlik bir Windows 7, Windows 8 veya Windows 10 görüntüsünü [yeterince hazırlayabilir ve oluşturabilir](../windows/prepare-for-upload-vhd-image.md) , sonra da [Azure 'a yükleyebilir](../windows/upload-generalized-managed.md). Kullanım, etkin Visual Studio aboneleri tarafından geliştirme ve test ile sınırlı kalır.

Bu [makalede](../windows/client-images.md) , Azure 'da Windows istemcisini çalıştırmaya yönelik uygunluk gereksinimleri ve Azure Galeri görüntülerinin kullanımı özetlenmektedir.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>VM 'imi yeniden boyutlandırırken istediğim VM boyut ailesini göremiyorum.

Bir VM çalışırken bir fiziksel sunucuya dağıtılır. Azure bölgelerindeki fiziksel sunucular, ortak fiziksel donanım kümelerinde gruplandırılır. VM 'nin farklı donanım kümelerine taşınmasını gerektiren bir VM 'nin yeniden boyutlandırılması, VM 'yi dağıtmak için kullanılan dağıtım modeline bağlı olarak farklılık gösteren bir değer.

- Klasik dağıtım modelinde dağıtılan sanal makineler, bulut hizmeti dağıtımının kaldırılması ve yeniden dağıtılması gerekir ve bu da VM 'Leri başka bir boyuttaki ailedeki bir boyutla değiştirin.

- Kaynak Yöneticisi dağıtım modelinde dağıtılan VM 'Ler, kullanılabilirlik kümesindeki herhangi bir sanal makinenin boyutunu değiştirmeden önce kullanılabilirlik kümesindeki tüm VM 'Leri durdurmanız gerekir.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Listelenen VM boyutu, kullanılabilirlik kümesinde dağıtıldığında desteklenmez.

Kullanılabilirlik kümesinin kümesinde desteklenen bir boyut seçin. İhtiyaç duyduğunuz en büyük VM boyutunu seçmek için bir kullanılabilirlik kümesi oluşturulması ve kullanılabilirlik kümesine ilk dağıtımınız olması önerilir.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Mevcut bir klasik VM 'yi bir kullanılabilirlik kümesine ekleyebilir miyim?

Evet. Mevcut bir klasik VM 'yi yeni veya var olan bir kullanılabilirlik kümesine ekleyebilirsiniz. Daha fazla bilgi için bkz. [var olan bir sanal makineyi bir kullanılabilirlik kümesine ekleme](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Sonraki adımlar
Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun.

Alternatif olarak, bir Azure destek olayına dosya. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
