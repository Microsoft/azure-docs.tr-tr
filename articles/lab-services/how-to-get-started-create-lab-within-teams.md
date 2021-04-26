---
title: Kullanmaya başlayın ve takımlar içinde Azure Lab Services Laboratuvarı oluşturun
description: Nasıl başlacağınızı ve takımlar içinde Azure Lab Services Laboratuvarı oluşturmayı öğrenin.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433980"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>Kullanmaya başlayın ve takımlar içinde Laboratuvar Hizmetleri Laboratuvarı oluşturun

Bu makalede, **Azure Lab Services** uygulamasının bir takıma nasıl ekleneceği ve ardından MS ekipleri ortamında nasıl laboratuvar oluşturulacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, takımınız için sanal makinelerle bir laboratuvar ayarlarsınız. Laboratuvar hesabında laboratuvar ayarlamak için laboratuvar hesabındaki şu rollerden birinin üyesi olmanız gerekir: Owner, Lab Creator veya katkıda bulunan. Laboratuvar hesabı oluşturmak için kullandığınız hesap, sahip rolüne otomatik olarak eklenir. Bu nedenle, bir laboratuvar oluşturmak için laboratuvar hesabı oluşturmak için kullandığınız kullanıcı hesabını kullanabilirsiniz.

Ekipler içinde Azure Lab Services kullanırken tipik iş akışı burada verilmiştir

1. Kullanıcı Azure portal [bir laboratuvar hesabı oluşturur](tutorial-setup-lab-account.md#create-a-lab-account) .
1. [Laboratuvar hesabı Oluşturucusu, diğer kullanıcıları](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) **Laboratuvar Oluşturucu** rolüne ekler. Örneğin, laboratuvar hesabı oluşturan/yönetici laboratuvar **Oluşturucu** rolüne eğitimciler ekler; böylece, sınıfları için laboratuvarları oluşturabilirler.
1. Daha sonra, eğitimciler Create Labs, şablon VM 'yi önceden yapılandırır ve ekipteki herkese VM 'yi sağlamak üzere Laboratuvarı yayımlar.
1. Laboratuvar yayımlandıktan sonra, Azure Lab Services için ilk oturum açmada ekip üyeliği listesinde herkese bir sanal makine atanır (SSO) veya [Labs Web sitesine](https://labs.azure.com)erişerek **Azure Lab Services** uygulama içeren sekmeye tıklayın. Böylece kullanıcılar VM 'yi kullanarak iş ve ev ödevini yapabilir.

> [!IMPORTANT]
> Azure Lab Services ekipler yalnızca laboratuvar hesapları ekiplerle aynı kiracıda oluşturulmuşsa kullanılabilir.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Takıma bir sekme olarak Azure Lab Services uygulaması ekleme

Ekip sahibi olarak, doğrudan takımlar kanallarınızda **Azure Lab Services** uygulama ekleyebilir ve ardından bu uygulama ekipteki herkes tarafından kullanılabilir. Aşağıdaki üç adımı izleyin:

1. Uygulamayı eklemek istediğiniz takımlar kanalına gidin ve **+** sekme eklemeyi seçin. 
1. Sekme seçeneklerinde **Azure Lab Services** arayın ve bu uygulamayı ekleyin. 

    > [!NOTE]
    > Yalnızca takım **sahipleri** takım için laboratuvarları oluşturabilir.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="Sekme ekle":::
1. Bu takımda Labs oluşturmak için kullanmak istediğiniz bir laboratuvar hizmetleri hesabı seçin. 

    Azure Lab Services, [Azure Lab Services Web sitesinde](https://labs.azure.com) çoklu oturum açma kullanır ve erişiminiz olan tüm Laboratuvar hesaplarını çeker. 

    Ekiplerle aynı kiracıda bulunan ve **sahibi**, **katılımcısı** veya **oluşturan** erişimi olan hesaplar görüntülenir. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="ALS 'e hoş geldiniz":::
1. **Kaydet** ' e basın ve sekme kanala eklenir.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="ALS sekmesi oluşturuldu":::

    Artık, kanalınızdan **Azure Lab Services** sekmesini seçebilir ve aşağıdaki makalelerde açıklandığı gibi laboratuvarları yönetmeye başlayabilirsiniz.

Laboratuvar hesabı seçildikten sonra, takım sahipleri takım için Labs oluşturabilir. Laboratuvar oluşturma işleminin tamamı ve laboratuar düzeyindeki tüm görevler takımlar içinde gerçekleştirilebilir. Kullanıcılar aynı takım ve takım sahibi içinde birden çok Laboratuvar oluşturma seçeneğine sahip olacak ve laboratuvar hesabı düzeyinde uygun erişimle, yalnızca belirli bir takımla ilişkili laboratuvarları görür.

## <a name="next-steps"></a>Sonraki adımlar

Takımlar içinde bir laboratuvar oluşturulduğunda, laboratuvar Kullanıcı Listesi otomatik olarak doldurulur ve takım üyeliğiyle eşitlenir. Ekip üzerinde sahipler, Üyeler ve konuklar dahil herkes otomatik olarak laboratuvar Kullanıcı listesine eklenecektir. Azure Laboratuvar Hizmetleri, takım üyeliğiyle bir eşitleme koruyacak ve 24 saatte bir otomatik eşitleme tetiklenecektir. Ayrıntılar için bkz.

[Takımlar içindeki Laboratuvar Hizmetleri Kullanıcı listelerini yönetme](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Ayrıca bkz.

Ayrıca aşağıdaki makalelere bakın:

- [Takımlara genel bakış içinde Azure Lab Services kullanma](lab-services-within-teams-overview.md)
- [Laboratuvarın takımlar içindeki VM havuzunu yönetme](how-to-manage-vm-pool-within-teams.md)
- [Takımlar içinde laboratuvar zamanlamaları oluşturma ve yönetme](how-to-create-schedules-within-teams.md)
- [Takımlar içindeki bir VM 'ye erişme – öğrenci görünümü](how-to-access-vm-for-students-within-teams.md)
- [Takımlar içindeki laboratuvarları silme](how-to-delete-lab-within-teams.md)
