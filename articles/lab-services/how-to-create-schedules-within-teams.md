---
title: Takımlar içinde Azure Lab Services zamanlamaları oluşturma
description: Takımlar içinde Laboratuvar Hizmetleri zamanlamaları oluşturmayı öğrenin.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92042346"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Takımlar içinde Laboratuvar Hizmetleri zamanlamaları oluşturma ve yönetme

Zamanlamalar, laboratuvardaki VM 'Lerin otomatik olarak başlamasını ve belirli bir zamanda kapatılmasını sağlamak için bir derslik Laboratuvarı yapılandırmanıza olanak tanır. Tek seferlik bir zamanlama veya yinelenen bir zamanlama tanımlayabilirsiniz. Aşağıdaki yordamlar, bir derslik Laboratuvarı için zamanlama oluşturma ve yönetme adımları sunar: 

Zamanlamaların laboratuvar sanal makinelerini nasıl etkilediği aşağıda verilmiştir: 

- Şablon sanal makinesi zamanlamalara dahil değildir. 
- Yalnızca atanan sanal makineler başlatılır. Bu, bir makinenin Son Kullanıcı (öğrenci) tarafından istenmediği anlamına gelir, makinenin zamanlanan saatlerde başlamamasını sağlayacaktır. 
- Tüm sanal makineler (bir kullanıcı tarafından talep edilen veya değil) laboratuvar zamanlaması temelinde durdurulur. 

> [!IMPORTANT]
> VM 'lerin zamanlanan çalışma süresi, bir kullanıcıya ayrılan kotaya göre sayılmaz. Kota, bir öğrencinin VM 'lerde harcadığı zamanlama saatleri dışında bir süredir. 

Kullanıcılar, laboratuvar [Web sitesinde](https://labs.azure.com)olduğu gibi ekipler içinde laboratuvar zamanlamaları oluşturabilir, düzenleyebilir ve silebilir. [Zamanlama oluşturma ve yönetme](how-to-create-schedules-within-teams.md)hakkındaki makaleye bakın.

## <a name="automatic-shutdown-and-disconnect-settings"></a>Otomatik olarak kapalı ve bağlantı kesme ayarları

Sanal makineler etkin bir şekilde kullanılmadığınızda ek maliyetleri önceden engellemek için çeşitli oto kapatma maliyeti denetim özelliklerini etkinleştirebilirsiniz. Aşağıdaki üç otomatik kapatılma ve bağlantı kesme özelliğinin birleşimi, kullanıcıların sanal makinelerini yanlışlıkla bırakması için gereken durumların çoğunu yakalar:
 
- İşletim sisteminin boşta kaldığı sanal makinelerle kullanıcıların bağlantısını otomatik olarak keser.
- Kullanıcıların bağlantısı kesildiğinde sanal makineleri otomatik olarak kapat.
- Başlatılan ancak kullanıcılar bağlanmadığında sanal makineleri otomatik olarak kapat.

Daha fazla ayrıntı için [Laboratuvar için otomatik kapatılma ayarlarını yapılandırma](how-to-enable-shutdown-disconnect.md)başlıklı makaleye bakın.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalelere bakın:

- [Takımlara genel bakış içinde Azure Lab Services kullanma](lab-services-within-teams-overview.md)
- [Çalışmaya başlayın ve takımlar içinde laboratuvar oluşturun](how-to-get-started-create-lab-within-teams.md)
- [Takımlar içindeki laboratuvar kullanıcı listelerini yönetme](how-to-manage-user-lists-within-teams.md)
- [Laboratuvarın takımlar içindeki VM havuzunu yönetme](how-to-manage-vm-pool-within-teams.md)
- [Takımlar içindeki bir VM 'ye erişme – öğrenci görünümü](how-to-access-vm-for-students-within-teams.md)
