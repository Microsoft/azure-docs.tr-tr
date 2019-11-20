---
title: Azure Lab Services’teki bir sınıf laboratuvarına erişme | Microsoft Docs
description: Bu öğreticide, bir profesör tarafından ayarlanan sınıf laboratuvarındaki sanal makinelere erişirsiniz.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 30983d141f087a46e420f7ea457fba181956c28e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577764"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Öğretici: Azure Lab Services’teki bir sınıf laboratuvarına erişme
Bu öğreticide, öğrenci olarak bir sınıf laboratuvarındaki sanal makineye (VM) bağlanırsınız. 

Bu öğreticide, aşağıdaki eylemleri gerçekleştireceksiniz:

> [!div class="checklist"]
> * Laboratuvara kaydolun
> * VM’yi başlatma
> * VM’ye bağlanma

## <a name="register-to-the-lab"></a>Laboratuvara kaydolun

1. Profesörden/eğitimciden aldığınız **kayıt URL’sine** gidin. Kayıt tamamlandıktan sonra kayıt URL 'sini kullanmanız gerekmez. Bunun yerine, URL 'YI kullanın: [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 henüz desteklenmiyor. 
1. Kaydı tamamlamak için okul hesabınızı kullanarak hizmette oturum açın. 

    > [!NOTE]
    > Azure Lab Services kullanımı için Microsoft hesabı gereklidir. Portalda oturum açmak için Yahoo veya Google hesapları gibi Microsoft hesabı ' yi kullanmaya çalışıyorsanız, Microsoft hesabı olmayan bir Microsoft hesabı oluşturmak için yönergeleri izleyin. Ardından, kayıt işlemini tamamlamaya yönelik adımları izleyin. 
1. Kaydolduktan sonra, erişimine sahip olduğunuz laboratuvarlar için sanal makineleri gördüğünüzü onaylayın. 
1. Sanal makine hazırlanana kadar bekleyin. VM kutucuğunda aşağıdaki alanlara dikkat edin:
    1. Kutucuğun en üstünde, **laboratuvarın adını**görürsünüz.
    1. Sağ tarafta, VM 'nin **işletim sistemini (OS)** temsil eden simgeyi görürsünüz. Bu örnekte, Windows işletim sistemi. 
    1. Kutucukta ilerleme çubuğu, size atanan [Kota saati](how-to-configure-student-usage.md#set-quotas-for-users) sayısına göre kullanılan saat sayısını gösterir. Bu süre, laboratuvarın zamanlanan saatine ek olarak size ayrılan ek süredir. 
    1. Sanal makineyi başlatmak/durdurmak ve sanal makineye bağlanmak için kutucuğun altında simgeler/düğmeler görürsünüz. 
    1. Düğmelerin sağında VM 'nin durumunu görürsünüz. VM 'nin durumunun **durdurulmuş**olduğunu görtığınızdan emin olun. 

        ![Durdurulmuş durumda VM](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>VM’yi başlatma
1. Aşağıdaki görüntüde gösterildiği gibi ilk düğmeyi seçerek VM 'yi **başlatın** . Bu işlem biraz zaman alır.  

    ![VM’yi başlatma](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. VM durumunun **çalışıyor**olarak ayarlandığını onaylayın. 

    ![Çalışır durumda VM](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    **Durdur** işlemini göstermek için ilk düğmenin simgesinin değiştirildiğini unutmayın. VM 'yi durdurmak için bu düğmeyi seçebilirsiniz. 

## <a name="connect-to-the-vm"></a>VM’ye bağlanma

1. Laboratuvarın VM 'sine **bağlanmak** için aşağıdaki görüntüde gösterildiği gibi ikinci düğmeyi seçin. 

    ![VM’ye bağlanma](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Aşağıdaki adımlardan birini yapın: 
    1. **Windows** sanal makineleri için **RDP** dosyasını sabit diske kaydedin. Sanal makineye bağlanmak için RDP dosyasını açın. Makine üzerinde oturum açmak için eğitimci/Mesleme makinenizden aldığınız **Kullanıcı adı** ve **parolayı** kullanın. 
    3. **Linux** sanal makinelerinde, bunlara bağlanmak için **SSH** veya **RDP** (etkinse) kullanabilirsiniz. Daha fazla bilgi için bkz. [Linux makineleri için Uzak Masaüstü bağlantısını etkinleştirme](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, eğitimcinizden/profesörünüzden aldığınız kayıt bağlantısını kullanarak bir sınıf laboratuvarına eriştiniz.

Laboratuvar sahibi olarak, laboratuvarınızda kimin kaydoltuını görüntülemek ve VM 'lerin kullanımını izlemek istersiniz. Laboratuvarın kullanımını izleme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Laboratuvar kullanımını izleme](tutorial-track-usage.md) 
