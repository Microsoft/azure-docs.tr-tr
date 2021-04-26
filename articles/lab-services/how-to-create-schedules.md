---
title: Azure Lab Services 'de Labs için zamanlama oluştur | Microsoft Docs
description: Laboratuvardaki VM 'Lerin belirli bir zamanda başlamasını ve kapatılmasını sağlamak üzere Azure Lab Services laboratuvarlara yönelik zamanlamalar oluşturmayı öğrenin.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2a827c3d9f3022cb7d27ee43c9c95227c44f97e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434045"
---
# <a name="create-and-manage-schedules-for-labs-in-azure-lab-services"></a>Azure Lab Services Labs için zamanlamalar oluşturma ve yönetme 
Zamanlamalar, laboratuvardaki VM 'Lerin otomatik olarak başlamasını ve belirli bir zamanda kapatılmasını sağlamak için bir derslik Laboratuvarı yapılandırmanıza olanak tanır. Tek seferlik bir zamanlama veya yinelenen bir zamanlama tanımlayabilirsiniz. Aşağıdaki yordamlar, bir derslik Laboratuvarı için zamanlama oluşturma ve yönetme adımları sunar: 

> [!IMPORTANT]
> VM 'lerin zamanlanan çalışma süresi, [bir kullanıcıya ayrılan kotaya](how-to-configure-student-usage.md#set-quotas-for-users)göre sayılmaz. Kota, bir öğrencinin VM 'lerde harcadığı zamanlama saatleri dışında bir süredir. 

## <a name="set-a-schedule-for-the-lab"></a>Laboratuvar için zamanlama ayarlama
Laboratuvardaki VM 'Lerin belirli zamanlarda otomatik olarak başlatılması/durdurulması için laboratuvar için zamanlanmış bir olay oluşturun. Daha önce belirttiğiniz Kullanıcı kotası, bu zamanlanan sürenin dışında her bir kullanıcıya atanan ek süredir. 

> [!NOTE]
> Çalışmaya başlamadan önce, zamanlamaların laboratuvar sanal makinelerini nasıl etkilediği aşağıda verilmiştir: 
>- Şablon sanal makinesi zamanlamalara dahil değildir. 
>- Yalnızca atanan sanal makineler başlatılır. Bu, bir makinenin Son Kullanıcı (öğrenci) tarafından istenmediği anlamına gelir, makinenin zamanlanan saatlerde başlamamasını sağlayacaktır. 
>- Tüm sanal makineler (bir kullanıcı tarafından talep edilen veya değil) laboratuvar zamanlaması temelinde durdurulur. 

1. **Zamanlamalar** sayfasına geçin ve araç çubuğunda **Zamanlanmış olay Ekle** ' yi seçin. 

    ![Zamanlamayı Ekle düğmesi seçili olan Azure Lab Services "Schedule" sayfasını gösteren ekran görüntüsü.](./media/how-to-create-schedules/add-schedule-button.png)
2. **Standart** **olay türünün** seçili olduğunu onaylayın. Yalnızca VM 'Lerin başlangıç saatini belirtmek için **Başlat** ' ı seçin. Yalnızca VM 'Ler için durma saatini belirtmek üzere **Durdur** ' u seçin. 
7. **Yinele** bölümünde geçerli zamanlamayı seçin. 

    ![Zamanlamalar sayfasında zamanlama Ekle düğmesi](./media/how-to-create-schedules/select-current-schedule.png)
5. **Yinele** iletişim kutusunda, aşağıdaki adımları uygulayın:
    1. **Yineleme** alanı için **Her haftanın** ayarlandığını onaylayın. 
    3. **Başlangıç tarihini** belirtin.
    4. VM 'Lerin **başlamasını istediğiniz başlangıç saatini** belirtin.
    5. VM 'Lerin kapatıldığı **bitiş saatini** belirtin. 
    6. Belirttiğiniz başlangıç ve durdurma zamanları için **saat dilimini** belirtin. 
    2. Zamanlamanın etkili olmasını istediğiniz günleri seçin. Aşağıdaki örnekte Monday-Thursday seçilidir. 
    8. **Kaydet**’i seçin. 

        ![Yineleme zamanlaması ayarla](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Şimdi, **zamanlanan olay Ekle** sayfasında, **notlar için (isteğe bağlı)**, zamanlama için herhangi bir açıklama veya Not girin. 
4. **Zamanlanmış olay Ekle** sayfasında **Kaydet**' i seçin. 

    ![Haftalık zamanlama](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Takvim 'de zamanlamaları görüntüleme
Aşağıdaki görüntüde gösterildiği gibi, Takvim görünümünde vurgulanmış olan zamanlanmış tarihleri ve zamanları görebilirsiniz:

![Takvim görünümündeki zamanlamalar](./media/how-to-create-schedules/schedules-calendar.png)

Takvimdeki geçerli tarihe geçmek için sağ üst köşedeki **bugün** düğmesini seçin. Önceki haftaya ve **sağ oka** geçiş yapmak için **sol ok** ' i seçerek takvimdeki bir sonraki haftaya geçiş yapın. 

## <a name="edit-a-schedule"></a>Zamanlamayı Düzenle
Takvimde vurgulanan bir zamanlamayı seçtiğinizde, zamanlamayı **düzenleme** veya **silme** düğmelerini görürsünüz. 

![Zamanlamayı Düzenle sayfası](./media/how-to-create-schedules/schedule-edit-button.png)

**Zamanlanmış olayı Düzenle** sayfasında, zamanlamayı güncelleştirebilir ve **Kaydet**' i seçebilirsiniz. 

## <a name="delete-a-schedule"></a>Zamanlamayı silme

1. Bir zamanlamayı silmek için takvimde vurgulanan bir zamanlamayı seçin ve çöp kutusu simgesini (Sil) seçin:

    ![Araç çubuğundaki Sil düğmesi](./media/how-to-create-schedules/schedule-delete-button.png)
2. **Zamanlanmış olayı Sil** iletişim kutusunda, silme işlemini onaylamak için **Evet** ' i seçin. 



## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Yönetici olarak, laboratuvar hesapları oluşturun ve yönetin](how-to-manage-lab-accounts.md)
- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, bir laboratuvarın kullanımını yapılandırma ve denetleme](how-to-configure-student-usage.md)
- [Laboratuvar kullanıcısı olarak, erişim laboratuvarları](how-to-use-classroom-lab.md)
