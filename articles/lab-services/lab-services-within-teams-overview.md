---
title: Microsoft ekipleri dahilinde Azure Lab Services
description: Microsoft ekipleri dahilinde Azure Lab Services kullanımına ilişkin bir genel bakış sağlar.
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f39613427ed174dfca80d4a48be9473ab7025e79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433914"
---
# <a name="azure-lab-services-within-microsoft-teams"></a>Microsoft ekipleri dahilinde Azure Lab Services

Azure Lab Services, **Azure Lab Services** takımlar uygulaması kullanılarak Microsoft ekipleri dahilinde yararlanılabilir olabilir. Laboratuvar hesaplarına sahip/katkıda bulunan/Oluşturucu erişimi olan herhangi bir takım sahibi, laboratuvarlar oluşturabilir ve ekipteki herkese sanal makineler sağlayabilir.

Bu makalede, takımlar dahilinde Azure Lab Services kullanmanın avantajları özetlenmektedir ve takımlar içinde laboratuvarların oluşturulması ve yönetilmesi ile ilgili yönergeler için diğer makalelere bağlantılar sağlanmıştır. 

> [!NOTE]
>**Azure Lab Services** Takımlar uygulaması yalnızca bir takıma eklenebilir, bireysel sohbetlere veya grup sohbetlerine eklenemez.

## <a name="benefits"></a>Avantajlar

Microsoft ekipleriyle Azure Lab Services Tümleştirme, eğitimciler bir sınıf ortamı ayarlamaya ve Team (class) içinde sanal Laboratuvar ortamları sağlamanıza yardımcı olur: 

* Eğitimciler, laboratuvarları ekiplerden çıkmadan ve [Azure Lab Services Web sitesine](https://labs.azure.com)gitmek zorunda kalmadan ekiplerine ait sanal makinelere erişebilecek şekilde ayarlayabilir.
* Ekiplerden Azure Lab Services için çoklu oturum açma (SSO).
* Takım ve laboratuvar sahipleri iki farklı sistemde sınıf rostaları bulundurmamalıdır-laboratuvar Kullanıcı listesi takım üyeliğinden otomatik olarak doldurulur ve her 24 saatte bir eşitleme gerçekleştirilir. 
* Şablon VM 'nin ilk yayımlandıktan sonra, laboratuvar kapasitesi (diğer bir deyişle, laboratuvardaki VM sayısı), takım üyeliğinden Kullanıcı ekleme/silme işlemini temel alarak otomatik olarak ayarlanır. 
* Ekip ve laboratuvar sahipleri yalnızca ekipte ilgili laboratuvarları görüntüler ve öğrenciler yalnızca belirli takım için sağlanan VM 'Leri görüntüler. 
* Kullanıcılar laboratuvara otomatik olarak kaydedilir ve sanal makineler laboratuvar yayımlandıktan sonra ilk oturum açma üzerine otomatik olarak atanır. Eğitimciler davetiye göndermek zorunda değildir ve öğrencilerin Laboratuvarı ayrı ayrı kaydetmesi gerekmez.  

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelere bakın:

- [Çalışmaya başlayın ve takımlar içinde laboratuvar oluşturun](how-to-get-started-create-lab-within-teams.md)
- [Takımlar içindeki laboratuvar kullanıcı listelerini yönetme](how-to-manage-user-lists-within-teams.md)
- [Laboratuvarın takımlar içindeki VM havuzunu yönetme](how-to-manage-vm-pool-within-teams.md)
- [Takımlar içinde laboratuvar zamanlamaları oluşturma ve yönetme](how-to-create-schedules-within-teams.md)
- [Takımlar içindeki bir VM 'ye erişme – öğrenci görünümü](how-to-access-vm-for-students-within-teams.md)
