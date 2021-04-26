---
title: Azure Lab Services’teki bir sınıf laboratuvarına erişme | Microsoft Docs
description: Bir sınıf laboratuvarına kaydolma, erişebileceğiniz tüm laboratuvarları görüntüleme, laboratuvardaki bir VM 'yi başlatma/durdurma ve VM 'ye bağlanma hakkında bilgi edinin.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6d5585082473a363df5f25f331475536544ae828
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433147"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services’teki bir sınıf laboratuvarına erişme
Bu makalede bir derslik laboratuvarına kaydolma, erişebileceğiniz tüm laboratuvarları görüntüleme, laboratuvardaki bir VM 'yi başlatma/durdurma ve VM 'ye bağlanma işlemlerinin nasıl yapılacağı açıklanır. 

## <a name="register-to-the-lab"></a>Laboratuvara kaydolun

1. Eğitimci tarafından aldığınız **kayıt URL** 'sine gidin. Kayıt tamamlandıktan sonra kayıt URL 'sini kullanmanız gerekmez. Bunun yerine, URL 'YI kullanın: [https://labs.azure.com](https://labs.azure.com) . Internet Explorer 11 henüz desteklenmiyor. 

    ![Laboratuvara kaydolun](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. Kaydı tamamlamak için okul hesabınızı kullanarak hizmette oturum açın. 

    > [!NOTE]
    > Azure Lab Services kullanımı için Microsoft hesabı gereklidir. Portalda oturum açmak için Yahoo veya Google hesapları gibi Microsoft hesabı ' yi kullanmaya çalışıyorsanız, Microsoft hesabı olmayan bir Microsoft hesabı oluşturmak için yönergeleri izleyin. Ardından, kayıt işlemini tamamlamaya yönelik adımları izleyin. 
1. Kaydolduktan sonra, erişimine sahip olduğunuz laboratuvarlar için sanal makineleri gördüğünüzü onaylayın. 

    ![Erişilebilir VM 'Ler](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. Sanal makine hazırlanana kadar bekleyin. VM kutucuğunda aşağıdaki alanlara dikkat edin:
    1. Kutucuğun en üstünde, **laboratuvarın adını** görürsünüz.
    1. Sağ tarafta, VM 'nin **işletim sistemini (OS)** temsil eden simgeyi görürsünüz. Bu örnekte, Windows işletim sistemi. 
    1. Sanal makineyi başlatmak/durdurmak ve sanal makineye bağlanmak için kutucuğun altında simgeler/düğmeler görürsünüz. 
    1. Düğmelerin sağında VM 'nin durumunu görürsünüz. VM 'nin durumunun **durdurulmuş** olduğunu görtığınızdan emin olun.

        ![Durdurulmuş durumda VM](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>VM 'yi başlatma veya durdurma
1. Aşağıdaki görüntüde gösterildiği gibi ilk düğmeyi seçerek VM 'yi **başlatın** . Bu işlem biraz zaman alır.  

    ![VM’yi başlatma](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. VM durumunun **çalışıyor** olarak ayarlandığını onaylayın. 

    ![Çalışır durumda VM](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    **Durdur** işlemini göstermek için ilk düğmenin simgesinin değiştirildiğini unutmayın. VM 'yi durdurmak için bu düğmeyi seçebilirsiniz. 

## <a name="connect-to-the-vm"></a>VM’ye bağlanma

1. Laboratuvarın VM 'sine **bağlanmak** için aşağıdaki görüntüde gösterildiği gibi ikinci düğmeyi seçin. 

    ![VM’ye bağlanma](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Aşağıdaki adımlardan birini yapın: 
    1. **Windows** sanal makineleri için **RDP** dosyasını sabit diske kaydedin. Sanal makineye bağlanmak için RDP dosyasını açın. Makine üzerinde oturum açmak için eğitimcinizden aldığınız **Kullanıcı adı** ve **parolayı** kullanın. 
    3. **Linux** sanal makinelerinde, bunlara bağlanmak için **SSH** veya **RDP** (etkinse) kullanabilirsiniz. Daha fazla bilgi için bkz. [Linux makineleri için Uzak Masaüstü bağlantısını etkinleştirme](how-to-enable-remote-desktop-linux.md). 
    1. Laboratuvar VM 'sine bağlanmak için bir **Mac** kullanıyorsanız, sonraki bölümdeki yönergeleri izleyin. 

## <a name="progress-bar"></a>İlerleme çubuğu 
Kutucukta ilerleme çubuğu, size atanan [Kota saati](how-to-configure-student-usage.md#set-quotas-for-users) sayısına göre kullanılan saat sayısını gösterir. Bu süre, laboratuvarın zamanlanan saatine ek olarak size ayrılan ek süredir. İlerleme çubuğunun rengi ve ilerleme çubuğunun altındaki metin, aşağıdaki senaryolara göre farklılık gösterir:

- Bir sınıf devam ediyorsa (sınıfının zamanlaması dahilinde), kota saatlerini göstermek için ilerleme çubuğu gri olur. 

    ![Gri renkte ilerleme çubuğu](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Bir kota atanmamışsa (sıfır saat), **sınıflar sırasında kullanılabilen metin yalnızca** ilerleme çubuğunun yerine gösterilir. 
    
    ![Hiçbir kota ayarlanmamışsa durum](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- **Kotanın** tükendiğinden, ilerleme çubuğunun rengi **kırmızı** olur. 

    ![Kırmızı renkte ilerleme çubuğu](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- İlerleme çubuğunun rengi, laboratuvar için zamanlanan sürenin dışındaysa **mavi** , bazı kota süresi kullanılır. 

    ![Mavi renkte ilerleme çubuğu](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-labs"></a>Tüm laboratuvarları görüntüleyin
Laboratuvarlara kaydolduktan sonra, aşağıdaki adımları uygulayarak tüm laboratuvarları görüntüleyebilirsiniz: 

1. Öğesine gidin [https://labs.azure.com](https://labs.azure.com) . Internet Explorer 11 henüz desteklenmiyor. 
2. Laboratuvara kaydolmak için kullandığınız kullanıcı hesabını kullanarak hizmette oturum açın. 
3. Erişiminiz olan tüm laboratuvarları görtığınızdan emin olun. 

    ![Tüm laboratuvarları görüntüleme](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Yönetici olarak, laboratuvar hesapları oluşturun ve yönetin](how-to-manage-lab-accounts.md)
- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, şablonları ayarlama ve yayımlama](how-to-create-manage-template.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
 