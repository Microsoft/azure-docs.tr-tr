---
title: Azure Stack Edge Pro GPU, Pro R, Mini R üzerinde VM 'Leri Azure portal aracılığıyla yeniden boyutlandırın
description: Azure Stack Edge Pro GPU, Azure Stack Edge Pro R Azure Stack Edge Mini R 'de çalışan sanal makinelerin (VM) Azure portal aracılığıyla nasıl yeniden boyutlandırılacağını öğrenin.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080639"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 'unuzdaki VM 'Leri yeniden boyutlandırmak için Azure portal kullanın

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, Azure Stack Edge Pro GPU cihazınızda dağıtılan sanal makinelerin (VM 'Ler) yeniden boyutlandırılması açıklanmaktadır.

       
## <a name="about-vm-sizing"></a>VM boyutlandırma hakkında

VM boyutu, sanal makine için kullanılabilir hale getirilen işlem kaynaklarının (CPU, GPU ve bellek gibi) miktarını belirler. Uygulama iş yükünüz için uygun bir VM boyutu kullanarak sanal makineler oluşturmalısınız. 

Tüm makineler aynı donanımda çalışmaya rağmen, makine boyutları disk erişimi için farklı sınırlara sahip olur. Bu, VM 'leriniz genelinde genel disk erişimini yönetmenize yardımcı olabilir. Bir iş yükü arttıkça, var olan bir sanal makineyi de yeniden boyutlandırabilirsiniz.

Daha fazla bilgi için bkz. [cihazınız Için desteklenen VM boyutları](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Önkoşullar

Azure portal aracılığıyla cihazınızda çalışan bir VM 'yi yeniden boyutlandırmadan önce şunları yaptığınızdan emin olun:

1. Cihazınızda dağıtılmış en az bir sanal makine var. Bu sanal makineyi oluşturmak için [Azure Portal aracılığıyla Azure Stack Edge Pro 'unuzda VM dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)' daki yönergelere bakın.

1. SANAL makinenizin **durdurulmuş** durumda olması gerekir. VM 'nizi durdurmak için **sanal makineler > genel bakış ' a** gidin ve durdurmak istediğiniz VM 'yi seçin. Genel Bakış sayfasında **Durdur** ' u seçin ve onay istendiğinde **Evet** ' i seçin. VM 'nizi yeniden boyutlandırmadan önce VM 'yi durdurmanız gerekir.

    ![VM 'yi genel bakış sayfasından durdur](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>VM’yi yeniden boyutlandırma

Cihazınızda dağıtılan bir sanal makineyi yeniden boyutlandırmak için bu adımları izleyin. 

1. Durdurduğu sanal makineye gidin ve **genel bakış** sayfasına gidin. **VM boyutunu seçin (değiştirme)**.
    
    ![Genel Bakış sayfasında VM boyutu değişikliğini seçin](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. **VM boyutunu değiştir** dikey penceresinde, komut çubuğundan **VM boyutunu** seçin ve ardından **Değiştir**' i seçin.

    ![Yeni VM boyutu Seç](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Sanal makinenin güncelleştirildiğini belirten bir bildirim görürsünüz. Sanal makine başarıyla güncelleştirildikten sonra, **genel bakış** sayfası YENIDEN boyutlandırılan VM 'yi görüntüleyecek şekilde yenilenir.

    ![Yeniden boyutlandırılmış VM ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Sonraki adımlar

Azure Stack Edge Pro cihazınızda sanal makinelerin nasıl dağıtılacağını öğrenmek için, bkz. [Azure Portal aracılığıyla sanal makineleri dağıtma](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
