---
title: Azure portal özel bir VHD 'den bir Windows sanal makinesi oluşturun
description: Azure portal bir VHD 'den yeni bir Windows sanal makinesi oluşturun.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: 587acd37f465dda7e514b595f8451afe3d259dea
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039138"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Azure portal kullanarak bir VHD 'den VM oluşturma

Azure 'da bir sanal makine (VM) oluşturmanın birkaç yolu vardır: 

- Kullanmak üzere zaten bir sanal sabit disk (VHD) varsa veya VHD 'yi kullanmak için mevcut bir VM 'den kopyalamak istiyorsanız, VHD 'yi yeni VM 'ye bir işletim sistemi diski olarak *ekleyerek* yenı bir VM oluşturabilirsiniz. 

- Silinen bir VM 'nin VHD 'sinden yeni bir VM oluşturabilirsiniz. Örneğin, doğru şekilde çalışmayan bir Azure VM 'si varsa, sanal makineyi silebilir ve VHD 'YI kullanarak yeni bir VM oluşturabilirsiniz. Aynı VHD 'yi yeniden kullanabilir veya bir anlık görüntü oluşturup anlık görüntüden yeni bir yönetilen disk oluşturarak VHD 'nin kopyasını oluşturabilirsiniz. Anlık görüntü oluşturmak birkaç adım daha fazla olsa da, özgün VHD 'yi korur ve size bir geri dönüş sağlar.

- Klasik bir VM alıp Kaynak Yöneticisi dağıtım modelini ve yönetilen diskleri kullanan yeni bir VM oluşturmak için VHD 'yi kullanın. En iyi sonuçlar için, anlık görüntüyü oluşturmadan önce Azure portal klasik VM 'yi **durdurun** .
 
- Şirket içi VHD 'den bir Azure VM oluşturabilir ve şirket içi VHD 'yi karşıya yükleyerek yeni bir VM 'ye ekleyebilirsiniz. VHD 'yi bir depolama hesabına yüklemek için PowerShell veya başka bir aracı kullanın ve ardından VHD 'den yönetilen bir disk oluşturursunuz. Daha fazla bilgi için bkz. [Özel BIR VHD 'Yi karşıya yükleme](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Birden çok VM oluşturmak istiyorsanız özel bir disk kullanmayın. Bunun yerine, daha büyük dağıtımlar için [bir görüntü oluşturun](capture-image-resource.md) ve ardından [Bu görüntüyü kullanarak birden çok VM oluşturun](create-vm-generalized-managed.md).

Tek bir anlık görüntü veya VHD 'den 20 VM 'ye eş zamanlı dağıtım sayısını sınırlamanızı öneririz. 

## <a name="copy-a-disk"></a>Disk kopyalama

Anlık görüntü oluşturun ve ardından anlık görüntüden bir disk oluşturun. Bu strateji, özgün VHD 'YI geri dönüş olarak tutmanızı sağlar:

1. [Azure Portal](https://portal.azure.com), sol taraftaki menüden **tüm hizmetler**' i seçin.
2. **Tüm hizmetler** arama kutusunda, **diskler** ' i girip, ardından **diskler** ' i seçerek kullanılabilir disklerin listesini görüntüleyin.
3. Kullanmak istediğiniz diski seçin. Bu diskin **disk** sayfası görüntülenir.
4. Üstteki menüden **anlık görüntü oluştur**' u seçin. 
5. Anlık görüntü için bir **ad** girin.
6. Anlık görüntü için bir **kaynak grubu** seçin. Var olan bir kaynak grubunu kullanabilir ya da yeni bir tane oluşturabilirsiniz.
7. **Hesap türü**için **Standart (HDD)** veya **Premium (SSD)** depolamayı seçin.
8. İşiniz bittiğinde, anlık görüntüyü oluşturmak için **Oluştur** ' u seçin.
9. Anlık görüntü oluşturulduktan sonra sol menüden **kaynak oluştur** ' u seçin.
10. Arama kutusuna **yönetilen disk** girin ve ardından listeden **yönetilen diskler** ' i seçin.
11. **Yönetilen diskler** sayfasında **Oluştur**' u seçin.
12. Disk için bir **ad** girin.
13. Disk için bir **kaynak grubu** seçin. Var olan bir kaynak grubunu kullanabilir ya da yeni bir tane oluşturabilirsiniz. Bu seçim, VM 'yi diskten oluşturduğunuz kaynak grubu olarak da kullanılacaktır.
14. **Hesap türü**için **Standart (HDD)** veya **Premium (SSD)** depolamayı seçin.
15. **Kaynak türü**' nde **anlık görüntünün** seçili olduğundan emin olun.
16. **Kaynak anlık görüntü** açılır penceresinde, kullanmak istediğiniz anlık görüntüyü seçin.
17. Gerektiğinde diğer ayarlamaları yapın ve sonra diski oluşturmak için **Oluştur** ' u seçin.

## <a name="create-a-vm-from-a-disk"></a>Diskten VM oluşturma

Kullanmak istediğiniz yönetilen disk VHD 'sine sahip olduktan sonra, portalda VM 'yi oluşturabilirsiniz:

1. [Azure Portal](https://portal.azure.com), sol taraftaki menüden **tüm hizmetler**' i seçin.
2. **Tüm hizmetler** arama kutusunda, **diskler** ' i girip, ardından **diskler** ' i seçerek kullanılabilir disklerin listesini görüntüleyin.
3. Kullanmak istediğiniz diski seçin. Bu diskin **disk** sayfası açılır.
4. **Genel bakış** sayfasında, **DISK durumunun** **eklenmemiş**olarak listelendiğinden emin olun. Değilse, diski VM 'den ayırmanız ya da diski boşaltmak için VM 'yi silmeniz gerekebilir.
4. Sayfanın üst kısmındaki menüde **VM oluştur**' u seçin.
5. Yeni VM 'nin **temel bilgiler** sayfasında, bir **sanal makine adı** girin ve var olan bir **kaynak grubunu** seçin ya da yeni bir tane oluşturun.
6. Boyut **için** **Boyut sayfasına erişmek üzere boyutu** **Değiştir** ' i seçin.
7. Bir VM boyutu satırı seçip **Seç**' i seçin.
8. **Ağ** sayfasında, portalın tüm yeni kaynaklar oluşturmasına izin verebilir veya var olan bir **sanal ağ** ve **ağ güvenlik grubunu**seçebilirsiniz. Portal, yeni VM için her zaman yeni bir ağ arabirimi ve genel IP adresi oluşturur. 
9. **Yönetim** sayfasında, izleme seçeneklerinde tüm değişiklikleri yapın.
10. **Konuk yapılandırması** sayfasında, gereken tüm uzantıları ekleyin.
11. İşiniz bittiğinde, **gözden geçir + oluştur**' u seçin. 
12. VM yapılandırması doğrulamayı geçerse, dağıtımı başlatmak için **Oluştur** ' u seçin.


## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, PowerShell kullanarak [BIR VHD 'Yi Azure 'a yükleyebilir ve özelleştirilmiş bir sanal makine oluşturabilirsiniz](create-vm-specialized.md).


