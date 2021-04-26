---
title: Azure Lab Services bir sınıf laboratuvarının şablonunu yönetme | Microsoft Docs
description: Azure Lab Services bir derslik laboratuvar şablonu oluşturmayı ve yönetmeyi öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be84a05fd8b278f5176cd768191d10a20fc719e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434286"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Azure Lab Services bir sınıf şablonu oluşturma ve yönetme
Laboratuvardaki şablon, tüm kullanıcıların sanal makinelerinin oluşturulduğu bir temel sanal makine görüntüsüdür. Şablon sanal makinesini, tam olarak laboratuvar kullanıcılarına sağlamak istediklerinizle yapılandırılacak şekilde ayarlayın. Laboratuvar kullanıcılarının görebileceği bir ad ve açıklama belirtebilirsiniz. Daha sonra, şablon VM 'nin örneklerini laboratuvar kullanıcılarınız için kullanılabilir hale getirmek üzere şablonu yayımlayabilirsiniz. Bir şablonu yayımladığınızda Azure Lab Services, şablonu kullanarak laboratuvarda sanal makineler oluşturur. Bu işlemde oluşturulan sanal makine sayısı, laboratuvarda izin verilen maksimum kullanıcı sayısıyla aynıdır. Laboratuvarın kullanım ilkesinde bu maksimum değeri ayarlayabilirsiniz. Tüm sanal makineler, şablonla aynı yapılandırmaya sahiptir.

Bu makalede, bir Azure Lab Services sınıf laboratuvarında şablon sanal makinesinin nasıl oluşturulacağı ve yönetileceği açıklanmaktadır. 

> [!NOTE]
> Laboratuvar oluşturduğunuzda, şablon VM oluşturulur ancak başlatılmaz. Bunu başlatabilir, ona bağlanabilir ve laboratuvar için önkoşul olan herhangi bir yazılımı yükleyebilir ve sonra yayımlayabilirsiniz. Şablon VM 'yi yayımladığınızda, bunu yapmadıysanız, sizin için otomatik olarak kapatılır. 
> 
> Şablon VM 'Leri çalışırken **Maliyet** doğurur, bu nedenle çalışıyor olması GEREKMIYORSA şablon VM 'nin kapatıldığından emin olun. 


## <a name="set-or-update-template-title-and-description"></a>Şablon başlığını ve açıklamasını ayarlama veya güncelleştirme
İlk kez başlık ve açıklama ayarlamak ve daha sonra güncelleştirmek için aşağıdaki adımları kullanın. 

1. **Şablon** sayfasında, laboratuvarın yeni **başlığını** girin.  
2. Şablonun yeni **açıklamasını** girin. Odağı metin kutusundan taşıdığınızda, otomatik olarak kaydedilir. 

    ![Şablon adı ve açıklaması](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Şablon VM 'yi güncelleştirme
Bir şablon VM 'sini güncelleştirmek için aşağıdaki adımları kullanın.  

1. Şablon VM 'si başlatılana kadar bekleyin ve ardından araç çubuğundan **şablona Bağlan** ' ı seçerek şablon VM 'sine bağlanın ve yönergeleri izleyin. Bu bir Windows makinedir, RDP dosyasını indirme seçeneği görüntülenir. 
1. Şablona bağlandıktan ve değişiklik yaptıktan sonra, artık kullanıcılarınız için en son yayımlanan sanal makinelerle aynı kuruluma sahip olmaz. Şablon değişiklikleri, yeniden yayımladıktan sonra kullanıcılarınızın var olan sanal makinelerine yansıtılmaz.

    ![Şablon VM'ye bağlanma](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. Öğrencilerinizin laboratuvarda ihtiyaç duyacağı uygulamaları (Visual Studio, Azure Depolama Gezgini gibi) yükleyin. 
1. Şablon VM bağlantısını kesin (uzak masaüstü oturumunuzu kapatın). 
1. Şablonu **Durdur** seçeneğini belirleyerek şablon VM 'yi **durdurun** . 
1. Güncelleştirilmiş şablon VM 'sini **yayımlamak** için sonraki bölümdeki adımları izleyin. 

## <a name="publish-the-template-vm"></a>Şablon VM'yi yayımlama  
Bu adımda, şablon VM 'yi yayımlarsınız. Şablon sanal makinesini yayımladığınızda, Azure Lab Services şablonu kullanarak laboratuvarda VM 'Ler oluşturur. Tüm sanal makineler, şablonla aynı yapılandırmaya sahiptir.


1. **Şablon** sayfasında, araç çubuğunda **Yayımla** ' yı seçin. 

    ![Şablon Yayımla düğmesi](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Yayımlama işlemini geri alamazsınız. 
2. **Şablonu Yayımla** sayfasında, laboratuvarda oluşturmak istediğiniz sanal makine sayısını girin ve ardından **Yayımla**' yı seçin. 

    ![Şablon yayımlama-sanal makine sayısı](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Şablonu **Yayımlama durumunu** sayfada görürsünüz. Bu işlemin tamamlanması bir saat sürebilir. 

    ![Şablonu yayımlama - ilerleme](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Yayımlama tamamlanana kadar bekleyin ve ardından sol menüdeki **sanal** makineler ' i seçerek veya **sanal makineler** Kutucuğu ' nı seçerek **sanal makine havuzu** sayfasına geçiş yapın. **Atanmamış** durumda olan sanal makineleri görtığınızdan emin olun. Bu VM’ler henüz bir öğrenciye atanmamıştır. Bu makinelerin durumu **Durduruldu** olmalıdır. Bu sayfadan bir öğrenci VM'sini başlatabilir, VM'ye bağlanabilir, VM'yi durdurabilir ve VM'yi silebilirsiniz. VM'leri bu sayfadan başlatabilir veya öğrencilerinizin başlatmasını sağlayabilirsiniz. 

    ![Durdurulmuş durumdaki sanal makineler](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Yönetici olarak, laboratuvar hesapları oluşturun ve yönetin](how-to-manage-lab-accounts.md)
- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
- [Laboratuvar kullanıcısı olarak, erişim laboratuvarları](how-to-use-classroom-lab.md)
