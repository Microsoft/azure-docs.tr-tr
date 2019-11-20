---
title: Azure Lab Services çoklu VM ortamlarını etkinleştirme | Microsoft Docs
description: Azure Lab Services sınıf laboratuvarında, bir şablon sanal makinesi içinde birden çok sanal makine içeren bir ortam oluşturmayı öğrenin.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332386"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Sınıf laboratuvarının şablon VM 'si içinde birden çok VM içeren ortam oluşturma
Şu anda Azure Lab Services bir laboratuvarda bir şablon sanal makinesi ayarlamanıza ve tek bir kopyayı her bir kullanıcı için kullanılabilir hale getirmenizi sağlar. Ancak, güvenlik duvarlarını veya sunucuları ayarlama hakkında bir BT sınıfı öğretme yapıyorsanız, her öğrencinize, birden çok sanal makinenin bir ağ üzerinden konuşabilecek bir ortamla birlikte sağlamanız gerekebilir.

İç içe sanallaştırma, bir laboratuvarın şablon sanal makinesi içinde çoklu VM ortamı oluşturmanızı sağlar. Şablonu yayımlamak, her kullanıcıya, içindeki birden çok VM ile bir sanal makine oluşturacak şekilde laboratuvar sağlar.

## <a name="what-is-nested-virtualization"></a>İç içe sanallaştırma nedir?
İç içe sanallaştırma, sanal makine içinde sanal makineler oluşturmanızı sağlar. İç içe sanallaştırma Hyper-V aracılığıyla yapılır ve yalnızca Windows VM 'lerinde kullanılabilir.

İç içe sanallaştırma hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Azure 'da iç içe sanallaştırma](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Azure VM 'de iç içe sanallaştırmayı etkinleştirme](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Azure Lab Services içinde iç içe sanallaştırmayı kullanın
Önemli adımlar şunlardır:

1. Laboratuvar için **büyük** boyutlu bir **Windows** şablon makinesi oluşturun. 
2. Bu sunucuya bağlanın ve [iç içe sanallaştırmayı etkinleştirin](../../virtual-machines/windows/nested-virtualization.md).


Aşağıdaki yordam size ayrıntılı adımlar sağlar: 

1. Henüz yoksa bir laboratuvar hesabı oluşturun. Yönergeler için bkz. [öğretici: Azure Lab Services laboratuvar hesabı ayarlama](tutorial-setup-lab-account.md).
1. [Azure Lab Services web sitesine](https://labs.azure.com) gidin. Internet Explorer 11 ' in henüz desteklenmediğini unutmayın. 
2. **Oturum aç**’ı seçip kimlik bilgilerinizi girin. Azure Lab Services, kuruluş hesaplarını ve Microsoft hesaplarını destekler. 
3. **Yeni laboratuvar**' ı seçin. 
    
    ![Sınıf laboratuvarı oluşturma](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. **Yeni Laboratuvar** penceresinde aşağıdaki eylemleri gerçekleştirin: 
    1. Laboratuvarınız için bir **ad** belirtin. 
    2. **Sanal makine boyutu**için **büyük (iç içe sanallaştırma)** veya **Orta (iç içe sanallaştırma)** seçeneğini belirleyin.
    6. Kullanmak istediğiniz bir Windows **görüntüsü** seçin. İç içe sanallaştırma yalnızca Windows makinelerde kullanılabilir. 
    4. Sonra **İleri**’yi seçin. 

        ![Sınıf laboratuvarı oluşturma](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. **Sanal makine kimlik bilgileri** sayfasında, laboratuvardaki tüm VM 'ler için varsayılan kimlik bilgilerini belirtin. Kullanıcının **adını** ve **parolasını** belirtin ve ardından **İleri**' yi seçin.  

        ![Yeni laboratuvar penceresi](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Kullanıcı adını ve parolayı not edin. Bunlar tekrar gösterilmeyecektir.
    3. **Laboratuvar ilkeleri** sayfasında, her kullanıcı için ayrılan saat sayısını (**her bir kullanıcı için kota**) laboratuvar için zamanlanan süre dışında girin ve ardından **son**' u seçin. 

        ![Her Kullanıcı için kota](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Şablon VM oluşturma 'nın durumunu gösteren aşağıdaki ekranı görmeniz gerekir. Laboratuvar şablonunun oluşturulması 20 dakika sürebilir. 

    ![Şablon VM oluşturma durumu](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. **Şablon** sayfasında, araç çubuğunda **şablonu Özelleştir** ' i seçin. 

    ![Şablonu Özelleştir düğmesi](../media/how-to-create-manage-template/customize-template-button.png)
2. **Şablonu Özelleştir** Iletişim kutusunda **devam**' ı seçin. Şablonu başlatıp değişiklik yaptıktan sonra, artık kullanıcılarınız için en son yayımlanan sanal makinelerle aynı kuruluma sahip olmaz. Şablon değişiklikleri, yeniden yayımladıktan sonra kullanıcılarınızın var olan sanal makinelerine yansıtılmaz.

    ![Özelleştirme iletişim kutusu](../media/how-to-create-manage-template/customize-template-dialog.png)
1. İç içe sanallaştırmayı yapılandırmak için şablon VM 'ye bağlanmak üzere araç çubuğunda **şablona Bağlan** düğmesine tıklayın. Bu bir Windows makinedir, RDP dosyasını indirme seçeneği görüntülenir. 

    ![Şablon VM'ye bağlanma](../media/how-to-create-manage-template/connect-template-vm.png) 
9. Şablon sanal makinesi içinde, iç içe sanallaştırmayı ayarlayın ve birden çok sanal makineyle bir sanal ağ yapılandırın. Ayrıntılı adım adım yönergeler için bkz. [Azure VM 'de iç içe sanallaştırmayı etkinleştirme](../../virtual-machines/windows/nested-virtualization.md). Bu adımların hızlı bir özeti aşağıda verilmiştir: 
    1. Şablon sanal makinesindeki Hyper-V özelliğini etkinleştirin.
    2. İç içe geçmiş sanal makineler için internet bağlantısı ile iç sanal ağ ayarlama
    3. Hyper-V Yöneticisi aracılığıyla sanal makineler oluşturma
    4. Sanal makinelere bir IP adresi atayın
10. **Şablon** sayfasında, araç çubuğunda **Yayımla** ' yı seçin. 

    ![Şablon Yayımla düğmesi](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Yayımlama işlemini geri alamazsınız. 
8. **Şablonu Yayımla** sayfasında, laboratuvarda oluşturmak istediğiniz sanal makine sayısını girin ve ardından **Yayımla**' yı seçin. 

    ![Şablon yayımlama-sanal makine sayısı](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Şablonu **Yayımlama durumunu** sayfada görürsünüz. Bu işlemin tamamlanması bir saat sürebilir. 

    ![Şablonu yayımlama - ilerleme](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Sonraki adımlar

Artık, her kullanıcı içinde çoklu VM ortamı içeren tek bir sanal makine alır. Laboratuvara Kullanıcı ekleme ve bunlara kayıt bağlantısı gönderme hakkında bilgi edinmek için aşağıdaki makaleye bakın: [laboratuvara Kullanıcı ekleme](tutorial-setup-classroom-lab.md#add-users-to-the-lab).