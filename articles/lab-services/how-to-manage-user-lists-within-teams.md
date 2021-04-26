---
title: Ekiplerden Azure Lab Services kullanıcı listelerini yönetme
description: Azure Lab Services kullanıcı listelerini ekiplerden yönetmeyi öğrenin.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91946839"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>Ekiplerden Laboratuvar Hizmetleri Kullanıcı listelerini yönetme

Takımlar içinde bir laboratuvar oluşturulduğunda (bkz. [Başlarken ve takımlar aracılığıyla Laboratuvar Hizmetleri Laboratuvarı oluşturma](how-to-get-started-create-lab-within-teams.md)), laboratuvar Kullanıcı Listesi otomatik olarak doldurulur ve takım üyeliğiyle eşitlenir. Ekip üzerinde sahipler, Üyeler ve konuklar dahil herkes otomatik olarak laboratuvar Kullanıcı listesine eklenecektir. Azure Laboratuvar Hizmetleri, takım üyeliğiyle bir eşitleme tutar ve 24 saatte bir otomatik eşitleme tetiklenir. 

## <a name="sync-users"></a>Kullanıcıları Eşitle

Eğitimciler, takım üyeliği güncelleştirildikten sonra el ile eşitleme tetikleyebilmesi için **Eşitle** düğmesini kullanabilir. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Kullanıcıları Eşitle":::

Otomatik veya el ile eşitleme tamamlandıktan sonra, laboratuvarın yayımlandığına bağlı olarak aşağıdakiler geçerlidir.

* Laboratuvar en az bir kez yayımlanmamışsa:
    * Kullanıcılar, takım üyeliğindeki değişikliklere göre laboratuvar Kullanıcı listesine eklenir veya silinir. 
* Laboratuvar en az bir kez yayımlandıysa, Kullanıcı ekleme veya silmeye ek olarak laboratuvar kapasitesi de otomatik olarak güncelleştirilir.
    * Takıma yeni eklemeler varsa yeni VM 'Ler oluşturulur.
    * Herhangi bir Kullanıcı takımdan silinmişse, ilişkili VM de silinir.

## <a name="next-steps"></a>Sonraki adımlar

Şablon VM yapılandırıldıktan ve eğitimci şablonu yayımlamayı seçtiğinde, laboratuvarın Kullanıcı listesindeki Kullanıcı sayısına denk gelen VM sayısı oluşturulur. Laboratuvar yayımlandıktan ve VM 'Ler oluşturulduktan sonra, kullanıcılar laboratuvara otomatik olarak kaydedilir ve VM 'Leri, **Azure Lab Services** uygulamasına sahip olan sekmeye ilk kez erişirken Azure Lab Services ilk oturum açtıklarında bunlara atanır. 

Şablon VM 'sini yayımlamak için takımlar Laboratuvar Hizmetleri penceresine gidin, **şablon** sekmesini seçin-> **...**  ->  **Yayımlayın**.

VM havuzlarını yönetmek için, bkz. [ekiplerin laboratuvar HIZMETLERINDEKI VM havuzunu yönetme](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>Ayrıca gözden geçirin

Aşağıdaki makalelere bakın:

- [Takımlara genel bakış içinde Azure Lab Services kullanma](lab-services-within-teams-overview.md)
- [Çalışmaya başlayın ve ekiplerden Laboratuvar Hizmetleri Laboratuvarı oluşturun](how-to-get-started-create-lab-within-teams.md)
- [Ekiplerden Laboratuvar Hizmetleri zamanlamaları oluşturma](how-to-create-schedules-within-teams.md)
- [Ekiplerden laboratuvar hizmetlerinde bir VM 'ye (öğrenci görünümü) erişme](how-to-access-vm-for-students-within-teams.md)

