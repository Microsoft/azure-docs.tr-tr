---
title: Azure Lab Services bir sınıf laboratuvarının şablonunu yönetme | Microsoft Docs
description: Azure Lab Services bir derslik laboratuvar şablonu oluşturmayı ve yönetmeyi öğrenin.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: fd3396a98359aef966cd8bb2f984e556fc6abcc7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584838"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Azure Lab Services bir sınıf şablonu oluşturma ve yönetme
Laboratuvardaki şablon, tüm kullanıcıların sanal makinelerinin oluşturulduğu bir temel sanal makine görüntüsüdür. Şablon sanal makinesini, tam olarak laboratuvar kullanıcılarına sağlamak istediklerinizle yapılandırılacak şekilde ayarlayın. Laboratuvar kullanıcılarının görebileceği bir ad ve açıklama belirtebilirsiniz. Daha sonra, şablon VM 'nin örneklerini laboratuvar kullanıcılarınız için kullanılabilir hale getirmek üzere şablonu yayımlayabilirsiniz. Bir şablonu yayımladığınızda Azure Lab Services, şablonu kullanarak laboratuvarda sanal makineler oluşturur. Bu işlemde oluşturulan sanal makine sayısı, laboratuvarda izin verilen maksimum kullanıcı sayısıyla aynıdır. Laboratuvarın kullanım ilkesinde bu maksimum değeri ayarlayabilirsiniz. Tüm sanal makineler, şablonla aynı yapılandırmaya sahiptir.

Bu makalede, bir Azure Lab Services sınıf laboratuvarında şablon sanal makinesinin nasıl oluşturulacağı ve yönetileceği açıklanmaktadır. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Sınıf Laboratuvarı oluştururken şablon yayımlama
Sınıf Laboratuvarı oluştururken bir şablonu yayımlamayı öğrenmek için bkz. [sınıf Laboratuvarı oluşturma](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>Şablon başlığını ve açıklamasını ayarlama veya güncelleştirme
İlk kez başlık ve açıklama ayarlamak ve daha sonra güncelleştirmek için aşağıdaki adımları kullanın. 

1. **Şablon** sayfasında, laboratuvarın yeni **başlığını** girin.  
2. Şablonun yeni **açıklamasını** girin. Odağı metin kutusundan taşıdığınızda, otomatik olarak kaydedilir. 

    ![Şablon adı ve açıklaması](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Şablon VM 'yi güncelleştirme
Bir şablon VM 'sini güncelleştirmek için aşağıdaki adımları kullanın.  

1. **Şablon** sayfasında, araç çubuğunda **şablonu Özelleştir** ' i seçin. 
2. **Şablonu Özelleştir** Iletişim kutusunda **devam**' ı seçin. Şablonu başlatıp değişiklik yaptıktan sonra, artık kullanıcılarınız için en son yayımlanan sanal makinelerle aynı kuruluma sahip olmaz. Şablon değişiklikleri, yeniden yayımladıktan sonra kullanıcılarınızın var olan sanal makinelerine yansıtılmaz.

    ![Özelleştirme iletişim kutusu](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Şablon VM 'si başlatılana kadar bekleyin ve ardından araç çubuğundan **şablona Bağlan** ' ı seçerek şablon VM 'sine bağlanın ve yönergeleri izleyin. Bu bir Windows makinedir, RDP dosyasını indirme seçeneği görüntülenir. 

    ![Şablon VM'ye bağlanma](../media/how-to-create-manage-template/connect-template-vm.png)
1. Öğrencilerinizin laboratuvarda ihtiyaç duyacağı uygulamaları (Visual Studio, Azure Depolama Gezgini gibi) yükleyin. 
2. Şablon VM bağlantısını kesin (uzak masaüstü oturumunuzu kapatın). 
3. Şablonu **Durdur**seçeneğini belirleyerek şablon VM 'yi **durdurun** . 
4. Güncelleştirilmiş şablon VM 'sini **yayımlamak** için sonraki bölümdeki adımları izleyin. 

## <a name="publish-the-template-vm"></a>Şablon VM'yi yayımlama  
Laboratuvarı oluştururken şablonu yayımlamazsanız, daha sonra yayımlayabilirsiniz. Yayımlamadan önce, şablon VM 'ye bağlanmak ve tüm yazılımlarla güncelleştirmek isteyebilirsiniz. Bir şablonu yayımladığınızda Azure Lab Services, şablonu kullanarak laboratuvarda sanal makineler oluşturur. Bu işlemde oluşturulan sanal makinelerin sayısı, ilk kez yayımlandığında belirttiğiniz VM 'lerin sayısı veya sanal makine havuzu sayfasında belirttiğiniz şeydir. Tüm sanal makineler, şablonla aynı yapılandırmaya sahiptir. 

1. **Şablon** sayfasında, araç çubuğunda **Yayımla** ' yı seçin. 
1. **Şablonu Yayımla** iletisi kutusunda iletiyi gözden geçirin ve **Yayımla**' yı seçin. Bu işlem, kaç sanal makine oluşturuldığına bağlı olarak biraz zaman alabilir.

    ![Yayımla düğmesi](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Şablon yayımlama işlemi geri alınamaz. Yine de şablonu yeniden yayımlayabilirsiniz. 
4. Yayımlama işleminin durumunu şablon sayfasında görebilirsiniz. Şablonun durumunun **yayımlanmış**olarak değiştirilmesini bekleyin. 

    ![Yayımlama durumu](../media/how-to-create-manage-template/publish-status.png)
1. **Sanal makineler** sayfasına geçin ve **Atanmamış** durumundaki sanal makineleri gördüğünüzü doğrulayın. Bu VM’ler henüz bir öğrenciye atanmamıştır. VM'ler oluşturulana kadar bekleyin. Bu makinelerin durumu **Durduruldu** olmalıdır. Bu sayfadan bir öğrenci VM'sini başlatabilir, VM'ye bağlanabilir, VM'yi durdurabilir ve VM'yi silebilirsiniz. Bu sayfada başlatabilir veya öğrencilerinizin VM 'Leri başlatmasını sağlayabilirsiniz. 

    ![Durdurulmuş durumdaki sanal makineler](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Yönetici olarak, laboratuvar hesapları oluşturun ve yönetin](how-to-manage-lab-accounts.md)
- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
- [Laboratuvar kullanıcısı olarak, sınıf laboratuvarlarına erişin](how-to-use-classroom-lab.md)
