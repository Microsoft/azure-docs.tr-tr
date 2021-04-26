---
title: Azure DevTest Labs'de laboratuvara erişme | Microsoft Docs
description: Bu öğreticide, Azure DevTest Labs kullanılarak oluşturulan laboratuvara erişir, sanal makineler talep eder, sanal makineleri kullanır ve sanal makine talebini geri alırsınız.
ms.topic: tutorial
ms.date: 06/26/2020
ms.author: spelluru
ms.openlocfilehash: 96d41b74b3c143feb64fc9c602de68182787c08b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379477"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Öğretici: Azure DevTest Labs’de bir laboratuvara erişme
Bu öğreticide, [Öğretici: Azure DevTest Labs’de laboratuvar oluşturma](tutorial-create-custom-lab.md) başlıklı öğreticide oluşturulan laboratuvarı kullanırsınız.

Bu öğreticide, aşağıdaki eylemleri gerçekleştireceksiniz:

> [!div class="checklist"]
> * Laboratuvarda sanal makine (VM) talep etme
> * VM’ye bağlanma
> * Sanal makine talebini geri alma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="access-the-lab"></a>Laboratuvara erişme

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Soldaki menüden **Tüm kaynaklar**’ı seçin. 
3. Kaynak türü için **DevTest Labs**’i seçin. 
4. Laboratuvarı seçin. 

    ![Laboratuvarı seçme](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Sanal makine talep etme

1. **Talep edilebilir sanal makineler** listesinde **...** (üç nokta) ve sonra **Makineyi talep et**’i seçin.

    ![Sanal makineyi talep etme](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. **Sanal makinelerim** listesinde sanal makineyi gördüğünüzü onaylayın.

    ![Sanal makinem](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>VM’ye bağlanma

1. Listeden sanal makinenizi seçin. Sanal makineniz için **Sanal Makine sayfasını** görürsünüz. Araç çubuğunda **Bağlan**’ı seçin.

    ![Sanal makineye bağlanma](./media/tutorial-use-custom-lab/connect-button.png)
2. İndirilen **RDP** dosyasını sabit diskinize kaydedin ve sanal makineye bağlanmak için kullanın. Önceki bölümde sanal makine oluşturulurken belirttiğiniz kullanıcı adını ve parolayı belirtin. 

    Bir Linux VM'ye bağlanmak için VM’nin SSH ve/veya RDP erişiminin etkinleştirilmesi gerekir. RDP üzerinden Linux VM’ye bağlanma adımları için bkz. [Azure’da Linux VM’ye bağlanmak için Uzak Masaüstü’nü yükleme ve yapılandırma](../virtual-machines/linux/use-remote-desktop.md). 

    > [!NOTE]
    > VM 'niz için sanal makine sayfasına ulaşmak üzere başka yollar vardır. Bunlardan bazıları şunlardır: 
    > 
    > 1. Aboneliğinizdeki tüm VM 'Leri arayın. **Sanal makine** sayfasına ulaşmak için VM 'lerin listesinden VM 'nizi seçin.
    > 2. Kaynak grubunun **kaynak grubu** sayfasına gidin. Sonra, **sanal makine** sayfasına ulaşmak için kaynak grubundaki kaynak listesinden VM 'nizi seçin. 
    >
    > Bu seçenekleri kullanarak aldığınız **sanal makine** sayfasındaki araç çubuğunda **Bağlan** düğmesini kullanmayın. Bunun yerine, bu makalede gösterildiği gibi **DevTest Labs** sayfasından **sanal makine** sayfasına gidin ve araç çubuğundaki **Bağlan** düğmesini kullanın.


## <a name="unclaim-the-vm"></a>Sanal makine talebini geri alma
Sanal makineyi kullandıktan sonra şu adımları izleyerek sanal makine talebini geri alın: 

1. Sanal makine sayfasında, araç çubuğundan **Talebi Geri Al**’ı seçin. 

    ![Sanal makine talebini geri alma](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Sanal makine talebi geri alınmadan önce sanal makine kapatılır. Bu işlemin durumunu bildirimler ' de görebilirsiniz.  
3. Üstteki içerik haritası menüsünde laboratuvar adına tıklayarak DevTest Lab sayfasına geri gidin. 
    
    ![Laboratuvara geri git](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. VM 'yi en alttaki **sanal makineler** listesi listesinden görtığınızdan emin olun.

    
## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure DevTest Labs kullanılarak oluşturulan bir laboratuvara nasıl erişeceğiniz ve bu laboratuvarı nasıl kullanacağınız gösterildi. Laboratuvardaki sanal makinelere erişme ve bu sanal makineleri kullanma hakkında daha fazla bilgi için bkz. 

> [!div class="nextstepaction"]
> [Nasıl yapılır: Laboratuvardaki sanal makineleri kullanma](devtest-lab-add-vm.md)

