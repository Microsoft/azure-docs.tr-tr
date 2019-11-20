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
ms.date: 09/19/2019
ms.author: spelluru
ms.openlocfilehash: 2ac9e8b8d0635eceb7d4f85ad867b102f7d064f5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585129"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Azure Lab Services’teki bir sınıf laboratuvarına erişme
Bu makalede bir derslik laboratuvarına kaydolma, erişebileceğiniz tüm laboratuvarları görüntüleme, laboratuvardaki bir VM 'yi başlatma/durdurma ve VM 'ye bağlanma işlemlerinin nasıl yapılacağı açıklanır. 

## <a name="register-to-the-lab"></a>Laboratuvara kaydolun

1. Profesörden/eğitimciden aldığınız **kayıt URL’sine** gidin. Kayıt tamamlandıktan sonra kayıt URL 'sini kullanmanız gerekmez. Bunun yerine, URL 'YI kullanın: [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 henüz desteklenmiyor. 
1. Kaydı tamamlamak için okul hesabınızı kullanarak hizmette oturum açın. 

    > [!NOTE]
    > Azure Lab Services kullanımı için Microsoft hesabı gereklidir. Portalda oturum açmak için Yahoo veya Google hesapları gibi Microsoft hesabı ' yi kullanmaya çalışıyorsanız, Microsoft hesabı olmayan bir Microsoft hesabı oluşturmak için yönergeleri izleyin. Ardından, kayıt işlemini tamamlamaya yönelik adımları izleyin. 
1. Kaydolduktan sonra, erişimine sahip olduğunuz laboratuvarlar için sanal makineleri gördüğünüzü onaylayın. 
1. Sanal makine hazırlanana kadar bekleyin. VM kutucuğunda aşağıdaki alanlara dikkat edin:
    1. Kutucuğun en üstünde, **laboratuvarın adını**görürsünüz.
    1. Sağ tarafta, VM 'nin **işletim sistemini (OS)** temsil eden simgeyi görürsünüz. Bu örnekte, Windows işletim sistemi. 
    1. Sanal makineyi başlatmak/durdurmak ve sanal makineye bağlanmak için kutucuğun altında simgeler/düğmeler görürsünüz. 
    1. Düğmelerin sağında VM 'nin durumunu görürsünüz. VM 'nin durumunun **durdurulmuş**olduğunu görtığınızdan emin olun.

        ![Durdurulmuş durumda VM](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>VM 'yi başlatma veya durdurma
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
    1. Laboratuvar VM 'sine bağlanmak için bir **Mac** kullanıyorsanız, sonraki bölümdeki yönergeleri izleyin. 

## <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Mac üzerinde RDP kullanarak VM 'ye bağlanma
Bu bölümde, bir öğrencinin RDP kullanarak Mac 'ten bir VM 'ye nasıl bağlanabildiği gösterilmektedir.

### <a name="step-1-install-microsoft-remote-desktop-on-a-mac"></a>1\. Adım: Mac 'e Microsoft Uzak Masaüstü yüklemesi
1. Mac 'inizde App Store ' u açın ve **Microsoft Uzak Masaüstü**arayın.

    ![Microsoft Uzak Masaüstü](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. En son Microsoft Uzak Masaüstü sürümünü yükler. 

### <a name="step-2-access-the-vm-from-your-mac-using-rdp"></a>2\. Adım: RDP kullanarak Mac 'Inizden VM 'ye erişme
1. **Microsoft Uzak Masaüstü** yüklü olan bilgisayarınızda indirilen **RDP** dosyasını açın. VM 'ye bağlanmaya başlamalıdır. 

    ![VM’ye bağlanma](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Aşağıdaki uyarıyı alırsanız **devam** ' ı seçin. 

    ![Sertifika uyarısı](../media/how-to-use-classroom-lab/certificate-error.png)
1. VM 'yi görmeniz gerekir. 

    > [!NOTE]
    > Aşağıdaki örnek bir CentOS Linux sanal makinesi içindir. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>İlerleme çubuğu 
Kutucukta ilerleme çubuğu, size atanan [Kota saati](how-to-configure-student-usage.md#set-quotas-for-users) sayısına göre kullanılan saat sayısını gösterir. Bu süre, laboratuvarın zamanlanan saatine ek olarak size ayrılan ek süredir. İlerleme çubuğunun rengi ve ilerleme çubuğunun altındaki metin, aşağıdaki senaryolara göre farklılık gösterir:

- Bir sınıf devam ediyorsa (sınıfının zamanlaması dahilinde), kota saatlerini göstermek için ilerleme çubuğu gri olur. 

    ![Gri renkte ilerleme çubuğu](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Bir kota atanmamışsa (sıfır saat), **sınıflar sırasında kullanılabilen metin yalnızca** ilerleme çubuğunun yerine gösterilir. 
    
    ![Hiçbir kota ayarlanmamışsa durum](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- **Kotanın**tükendiğinden, ilerleme çubuğunun rengi **kırmızı**olur. 

    ![Kırmızı renkte ilerleme çubuğu](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- İlerleme çubuğunun rengi, laboratuvar için zamanlanan sürenin dışındaysa **mavi** , bazı kota süresi kullanılır. 

    ![Mavi renkte ilerleme çubuğu](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Tüm sınıf laboratuvarlarını görüntüleme
Laboratuvarlara kaydolduktan sonra, aşağıdaki adımları uygulayarak tüm sınıf laboratuvarlarını görüntüleyebilirsiniz: 

1. [https://labs.azure.com](https://labs.azure.com)gidin. Internet Explorer 11 henüz desteklenmiyor. 
2. Laboratuvara kaydolmak için kullandığınız kullanıcı hesabını kullanarak hizmette oturum açın. 
3. Erişiminiz olan tüm laboratuvarları görtığınızdan emin olun. 

    ![Tüm laboratuvarları görüntüleme](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Yönetici olarak, laboratuvar hesapları oluşturun ve yönetin](how-to-manage-lab-accounts.md)
- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, şablonları ayarlama ve yayımlama](how-to-create-manage-template.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
 