---
title: Ekiplerden Azure Lab Services bir VM havuzunu yönetme
description: Ekiplerden Azure Lab Services bir VM havuzunu yönetmeyi öğrenin.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946856"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Ekiplerden laboratuvar hizmetlerinde bir VM havuzunu yönetme

Sanal makine (VM) oluşturma, şablon VM ilk kez yayımlandıktan sonra başlar. Laboratuvar Kullanıcı listesindeki Kullanıcı sayısını eşitleyen VM 'Ler oluşturulacaktır. VM 'Ler Azure Lab Services ilk oturum açmaları üzerine otomatik olarak öğrencilerine atanır. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Şablon yayımlama ve VM havuzunu yönetme

Şablonu yayımlamak için takımlar Laboratuvar Hizmetleri penceresine gidin, **şablon** sekmesini seçin-> **...**  ->  **Yayımlayın**.

Şablon VM yapılandırıldıktan ve eğitimci şablonu yayımlamayı seçtiğinde, laboratuvarın Kullanıcı listesindeki Kullanıcı sayısına denk gelen VM sayısı oluşturulur. Laboratuvar yayımlandıktan ve VM 'Ler oluşturulduktan sonra, kullanıcılar laboratuvara otomatik olarak kaydedilir ve VM 'Leri, **Azure Lab Services** uygulamasına sahip olan sekmeye ilk kez erişirken Azure Lab Services ilk oturum açtıklarında bunlara atanır. 

Bir kullanıcı listesi eşitleme tetiklendiğinde, laboratuvar kapasitesi (laboratuvardaki VM sayısı) takım üyeliğindeki değişikliklere göre otomatik olarak güncelleştirilir. Yeni Kullanıcı eklendikçe ve takımdan çıkarılan kullanıcılara atanan VM 'Ler de silinecek yeni VM 'Ler oluşturulacaktır. Daha fazla bilgi için bkz. [ekipler içindeki kullanıcıları yönetme](how-to-manage-user-lists-within-teams.md). 

Eğitimciler, öğrenci VM 'lerine doğrudan VM havuzu sekmesinden erişmeye devam edebilir. Ve eğitimciler, **sanal makine havuzu** sekmesinden ya da **sanal makinelerim** düğmesine (ekranın üst/sağ köşesi) tıklayarak kendilerine atanan VM 'lere erişebilir. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="VM havuzu":::

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelere bakın:

- [Takımlara genel bakış içinde Azure Lab Services kullanma](lab-services-within-teams-overview.md)
- [Çalışmaya başlayın ve ekiplerden Laboratuvar Hizmetleri Laboratuvarı oluşturun](how-to-get-started-create-lab-within-teams.md)
- [Ekiplerden Laboratuvar Hizmetleri Kullanıcı listelerini yönetme](how-to-manage-user-lists-within-teams.md)
- [Ekiplerden Laboratuvar Hizmetleri zamanlamaları oluşturma](how-to-create-schedules-within-teams.md)
- [Ekiplerden laboratuvar hizmetlerinde bir VM 'ye (öğrenci görünümü) erişme](how-to-access-vm-for-students-within-teams.md)


