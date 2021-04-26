---
title: Azure AD Connect için seçmeli Parola karması eşitlemesi
description: Bu makalede, Azure AD Connect ile kullanmak üzere seçmeli Parola karması eşitlemesini ayarlama ve yapılandırma açıklanmaktadır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a73f4eba9581965470b95111e6dda1d8014e4cb
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167507"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Azure AD Connect için seçmeli Parola karması eşitleme yapılandırması

[Parola karması eşitleme](whatis-phs.md) , karma kimlik gerçekleştirmek için kullanılan oturum açma yöntemlerinden biridir. Azure AD Connect, kullanıcının bir Kullanıcı parolasının karmasını, şirket içi Active Directory örneğinden bulut tabanlı bir Azure AD örneğine eşitler.  Varsayılan olarak, bir kez kurulduktan sonra, eşitlemekte olduğunuz tüm kullanıcılara parola karması eşitlemesi gerçekleşir.

Parola karmasını Azure AD 'ye Eşitlemeden dışlanan kullanıcıların bir alt kümesine sahip olmak istiyorsanız, bu makalede belirtilen Kılavuzlu adımları kullanarak Seçmeli Parola karması eşitlemesini yapılandırabilirsiniz.

>[!Important]
> Microsoft, resmi olarak belgelenen yapılandırmaların veya eylemlerin dışında değiştirme veya çalışma Azure AD Connect eşitlemeyi desteklemez. Bu yapılandırmaların veya eylemlerin herhangi biri, tutarsız veya desteklenmeyen Azure AD Connect eşitleme durumuyla sonuçlanabilir. Sonuç olarak, Microsoft bu tür dağıtımlar için verimli teknik destek sağlayabileceğinizin garantisi vermez. 


## <a name="consider-your-implementation"></a>Uygulamanızı göz önünde bulundurun  
Yapılandırma yönetim çabalarını azaltmak için, önce parola karması eşitlemeden dışlamak istediğiniz kullanıcı nesnesi sayısını göz önünde bulundurmanız gerekir. Aşağıdaki senaryolara göre birbirini dışlayan, sizin için doğru yapılandırma seçeneğini belirlemek için gereksinimlerinize göre hizalandığını doğrulayın.
- **Dışlanacak** Kullanıcı sayısı Kullanıcı sayısından **daha küçükse** **, bu** [bölümdeki](#excluded-users-is-smaller-than-included-users)adımları izleyin.
- **Dışlanacak** Kullanıcı sayısı, **eklenecek** Kullanıcı sayısından **büyükse** , bu [bölümdeki](#excluded-users-is-larger-than-included-users)adımları izleyin.

> [!Important]
> Her iki yapılandırma seçeneği de, değişiklikleri uygulamak için gereken bir ilk eşitleme (tam eşitleme), sonraki eşitleme döngüsünün üzerinde otomatik olarak gerçekleştirilir.

> [!Important]
> Seçmeli parola karma eşitlemesini yapılandırmak, parola geri yazmayı doğrudan etkiler. Azure Active Directory içinde başlatılan parola değişiklikleri veya parola sıfırlamaları, yalnızca kullanıcı parola karması eşitleme kapsamınlarsa şirket içi Active Directory geri yazılır. 

### <a name="the-admindescription-attribute"></a>AdminDescription özniteliği
Her iki senaryo da kullanıcıların adminDescription özniteliğini belirli bir değere ayarlamayı kullanır.  Bu kuralların uygulanmasını sağlar ve seçmeli PHS 'yi işler hale getirir.

|Senaryo|adminDescription değeri|
|-----|-----|
|Dışlanan kullanıcılar eklenen kullanıcılardan daha küçüktür|Phsfıltered|
|Dışlanan kullanıcılar dahil edilen kullanıcılardan daha büyük|Phsdahil|

Bu öznitelik şu şekilde ayarlanabilir:

- Active Directory Kullanıcıları ve bilgisayarları kullanıcı arabirimini kullanma
- `Set-ADUser`PowerShell cmdlet 'i kullanılıyor.  Daha fazla bilgi için bkz. [set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser).

 


### <a name="disable-the-synchronization-scheduler"></a>Eşitleme zamanlayıcısını devre dışı bırak:
Herhangi bir senaryoya başlamadan önce, eşitleme kurallarında değişiklik yaparken eşitleme zamanlayıcısını devre dışı bırakmanız gerekir.
 1. Windows PowerShell 'i başlatın.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  Aşağıdaki cmdlet 'i çalıştırarak Scheduler 'ın devre dışı bırakıldığını onaylayın:
     
    ```Get-ADSyncScheduler```

Zamanlayıcı hakkında daha fazla bilgi için bkz. [Azure AD Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).




## <a name="excluded-users-is-smaller-than-included-users"></a>Dışlanan kullanıcılar eklenen kullanıcılardan daha küçüktür
Aşağıdaki bölümde, **dışlanacak** Kullanıcı sayısı Kullanıcı sayısından **daha az** olduğunda seçmeli parola karma eşitlemesinin nasıl etkinleştirileceği **açıklanmaktadır.**

>[!Important]
> Devam etmeden önce, yukarıdaki ana hatlarıyla belirtilen eşitleme zamanlayıcının devre dışı bırakıldığından emin olun.

- **Ad – User AccountEnabled Içinden '** ın düzenlenebilir bir kopyasını, **Parola karması eşitlemesini etkinleştirme seçeneğinin işaretini kaldırın** ve kapsam filtresini tanımlayın 
- **Ad – User AccountEnabled öğesinden** , **Parola karması eşitlemesini etkinleştirme** seçeneğini belirleyin ve kapsam filtresini tanımlayın 
- Eşitleme zamanlayıcısını yeniden etkinleştirin 
- Active Directory 'de, Parola karması eşitlemede izin vermek istediğiniz kullanıcılarda kapsam özniteliği olarak tanımlanan öznitelik değerini ayarlayın. 

>[!Important]
>Seçmeli Parola karması eşitlemesini yapılandırmak için belirtilen adımlar, yalnızca bir **adminDescription** özniteliği Active Directory, yalnızca **phsfıltered** değeriyle doldurulmuş Kullanıcı nesnelerini etkiler.
Bu öznitelik doldurulmamışsa veya değer **Phsfılm** dışında bir öğe ise, bu kurallar Kullanıcı nesnelerine uygulanmaz.


### <a name="configure-the-necessary-synchronization-rules"></a>Gerekli eşitleme kurallarını yapılandırın:

 1. Eşitleme kuralları düzenleyicisini başlatın ve filtreler **parola eşitlemesini** ve **kural türünü** **Standart** **olarak ayarlayın** .
     ![Eşitleme kuralları düzenleyicisini Başlat](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. Active Directory orman Bağlayıcısı için **ad – Kullanıcı AccountEnabled konumundan içindeki** kuralı seçin ve seçmeli parolanın karma eşitlemesine sahip olduğunu yapılandırmak istediğiniz **düzenleme**' ye tıklayın. Özgün kuralın düzenlenebilir bir kopyasını oluşturmak için ileri iletişim kutusunda **Evet** ' i seçin.
     ![Kural seçin](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. İlk kural, Parola karması eşitlemesini devre dışı bırakacak. Yeni özel kurala şu adı verin: **IÇINDEN ad-Kullanıcı AccountEnabled-kullanıcıları PHS 'Den filtreleyin**.
 Öncelik değerini 100 ' dan daha düşük bir sayı olarak değiştirin (örneğin, **90** veya ortamınızda kullanılabilir en düşük değer olan).
 Onay kutularının **parola eşitlemesini** ve **devre dışı bırakıldığından** emin olun.
 **İleri**’ye tıklayın.
  ![Geleni Düzenle](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. **Kapsam filtresi**' nde, **yan tümce Ekle**' ye tıklayın.
 Öznitelik sütununda, Işleç sütununa **eşit** olan **adminDescription** öğesini seçin ve değer olarak **phsfıltered** girin.
     ![Kapsam filtresi](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. Başka değişiklik yapılması gerekmez. **Ekleme kuralları** ve **dönüştürmeleri** , varsayılan kopyalanmış ayarlarla ayrılmalıdır, böylece şimdi **Kaydet** ' e tıklayabilirsiniz.
 Bağlayıcının bir sonraki eşitleme döngüsünün tamamına tam eşitleme çalıştırılacağını bildiren uyarı iletişim kutusunda **Tamam** ' a tıklayın.
     ![Kuralı Kaydet](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. Ardından, Parola karması eşitlemesi etkinken başka bir özel kural oluşturun. Seçmeli parolayı yapılandırmak istediğiniz Active Directory orman için **ad – Kullanıcı AccountEnabled ' daki** varsayılan kuralı yeniden seçin ve **Düzenle**' ye tıklayın. Özgün kuralın düzenlenebilir bir kopyasını oluşturmak için ileri iletişim kutusunda **Evet** ' i seçin.
     ![Özel kural](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. Yeni özel kurala şu adı verin: **içinde, ad-Kullanıcı muhasebeci için, ve PHS için dahil edilen kullanıcılar**.
 Öncelik değerini önceden oluşturulan kuraldan daha düşük bir sayı olarak değiştirin (Bu örnekte **89** olacaktır).
 **Parola eşitlemesini etkinleştir** onay kutusunun işaretli olduğundan ve **devre dışı** onay kutusunun işaretinin kaldırıldığından emin olun.
 **İleri**’ye tıklayın.  
     ![Yeni kuralı Düzenle](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. **Kapsam filtresi**' nde, **yan tümce Ekle**' ye tıklayın.
 Öznitelik sütununda **adminDescription** ' u seçin, Işleç sütununa **Not** edin ve değer olarak **phsfılm** yazın.
     ![Kapsam kuralı](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. Başka değişiklik yapılması gerekmez. **Ekleme kuralları** ve **dönüştürmeleri** , varsayılan kopyalanmış ayarlarla ayrılmalıdır, böylece şimdi **Kaydet** ' e tıklayabilirsiniz.
 Bağlayıcının bir sonraki eşitleme döngüsünün tamamına tam eşitleme çalıştırılacağını bildiren uyarı iletişim kutusunda **Tamam** ' a tıklayın.
     ![Ekleme kuralları](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. Kuralların oluşturulmasını onaylayın. **Parola eşitlemesini** **ve** **kural türü** **standardını** filtreler. Ayrıca yeni oluşturduğunuz kuralların her ikisini de görmeniz gerekir.
     ![Kuralları Onayla](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>Eşitleme zamanlayıcısını yeniden etkinleştirin:  
Gerekli eşitleme kurallarını yapılandırma adımlarını tamamladıktan sonra, aşağıdaki adımlarla eşitleme zamanlayıcısını yeniden etkinleştirin:
 1. Windows PowerShell 'de Çalıştır:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Ardından, şu işlemi çalıştırarak başarıyla etkinleştirildiğini onaylayın:

     ```Get-ADSyncScheduler```

Zamanlayıcı hakkında daha fazla bilgi için bkz. [Azure AD Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Kullanıcıları Düzenle **adminDescription** özniteliği:
Tüm yapılandırma işlemleri tamamlandıktan sonra, Active Directory ' deki Parola karması eşitlemesine **dışlamak** istediğiniz tüm kullanıcılar Için **adminDescription** öznitelik özelliğini düzenlemeniz gerekir ve kapsam filtresinde kullanılan dizeyi ekleyin: **phsfıltered**.
   
  ![Özniteliği düzenleme](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)

Bir kullanıcının **adminDescription** özniteliğini düzenlemek Için aşağıdaki PowerShell komutunu da kullanabilirsiniz:

```Set-ADUser myuser -Replace @{adminDescription="PHSFiltered"}```

## <a name="excluded-users-is-larger-than-included-users"></a>Dışlanan kullanıcılar dahil edilen kullanıcılardan daha büyük
Aşağıdaki bölümde, **dışlanacak** Kullanıcı sayısı Kullanıcı sayısından **daha büyük** olduğunda seçmeli Parola karması eşitlemesinin nasıl etkinleştirileceği **açıklanmaktadır.**

>[!Important]
> Devam etmeden önce, yukarıdaki ana hatlarıyla belirtilen eşitleme zamanlayıcının devre dışı bırakıldığından emin olun.

Aşağıdaki adımlarda gerçekleştirilecek eylemlerin özeti aşağıda verilmiştir:

- **Ad – User AccountEnabled Içinden '** ın düzenlenebilir bir kopyasını, **Parola karması eşitlemesini etkinleştirme seçeneğinin işaretini kaldırın** ve kapsam filtresini tanımlayın 
- **Ad – User AccountEnabled öğesinden** , **Parola karması eşitlemesini etkinleştirme** seçeneğini belirleyin ve kapsam filtresini tanımlayın 
- Eşitleme zamanlayıcısını yeniden etkinleştirin 
- Active Directory 'de, Parola karması eşitlemede izin vermek istediğiniz kullanıcılarda kapsam özniteliği olarak tanımlanan öznitelik değerini ayarlayın. 

>[!Important]
>Seçmeli Parola karması eşitlemesini yapılandırmak için sağlanan adımlar yalnızca, Active Directory ile birlikte girilen ve yalnızca **Phsin** değeri olan **adminDescription** özniteliği olan kullanıcı nesnelerini etkiler.
Bu öznitelik doldurulmamışsa veya değer **Phsdahil** dışında bir öğe ise, bu kurallar Kullanıcı nesnelerine uygulanmaz.


### <a name="configure-the-necessary-synchronization-rules"></a>Gerekli eşitleme kurallarını yapılandırın:

 1. Eşitleme kuralları düzenleyicisini başlatın ve **parola eşitlemesini** ve **kural türü** **standardını** filtre **olarak** ayarlayın.
     ![Kural türü](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. Seçmeli parolayı yapılandırmak istediğiniz Active Directory orman için **ad – User AccountEnabled konumundan içindeki** kuralı seçin ve **Düzenle**' ye tıklayın. Özgün kuralın düzenlenebilir bir kopyasını oluşturmak için ileri iletişim kutusunda **Evet** ' i seçin.
     ![AD 'den](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. İlk kural, Parola karması eşitlemesini devre dışı bırakacak. Yeni özel kurala şu adı verin: **IÇINDEN ad-Kullanıcı AccountEnabled-kullanıcıları PHS 'Den filtreleyin**.
 Öncelik değerini 100 ' dan daha düşük bir sayı olarak değiştirin (örneğin, **90** veya ortamınızda kullanılabilir en düşük değer olan).
 Onay kutularının **parola eşitlemesini** ve **devre dışı bırakıldığından** emin olun.
 **İleri**’ye tıklayın.
  ![Öncelik ayarla](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. **Kapsam filtresi**' nde, **yan tümce Ekle**' ye tıklayın.
Öznitelik sütununda **adminDescription** ' ı seçin, Işleç sütununa **Not** edin ve değer olarak, **phsdahil** yazın.
     ![Yan tümce Ekle](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. Başka değişiklik yapılması gerekmez. **Ekleme kuralları** ve **dönüştürmeleri** , varsayılan kopyalanmış ayarlarla ayrılmalıdır, böylece şimdi **Kaydet** ' e tıklayabilirsiniz.
 Bağlayıcının bir sonraki eşitleme döngüsünün tamamına tam eşitleme çalıştırılacağını bildiren uyarı iletişim kutusunda **Tamam** ' a tıklayın.
     ![Dönüşüm](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. Ardından, Parola karması eşitlemesi etkinken başka bir özel kural oluşturun. Seçmeli parolayı yapılandırmak istediğiniz Active Directory orman için **ad – Kullanıcı AccountEnabled ' daki** varsayılan kuralı yeniden seçin ve **Düzenle**' ye tıklayın. Özgün kuralın düzenlenebilir bir kopyasını oluşturmak için ileri iletişim kutusunda **Evet** ' i seçin.
     ![Kullanıcı AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. Yeni özel kurala şu adı verin: **içinde, ad-Kullanıcı muhasebeci için, ve PHS için dahil edilen kullanıcılar**.
 Öncelik değerini önceden oluşturulan kuraldan daha düşük bir sayı olarak değiştirin (Bu örnekte **89** olacaktır).
 **Parola eşitlemesini etkinleştir** onay kutusunun işaretli olduğundan ve **devre dışı** onay kutusunun işaretinin kaldırıldığından emin olun.
 **İleri**’ye tıklayın.
     ![Parola eşitlemeyi etkinleştir](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. **Kapsam filtresi**' nde, **yan tümce Ekle**' ye tıklayın.
 Öznitelik sütununda, Işleç sütununa **eşit** olan **adminDescription** öğesini seçin ve değer olarak **phseklenen** ' i girin.
     ![Phsdahil](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. Başka değişiklik yapılması gerekmez. **Ekleme kuralları** ve **dönüştürmeleri** , varsayılan kopyalanmış ayarlarla ayrılmalıdır, böylece şimdi **Kaydet** ' e tıklayabilirsiniz.
 Bağlayıcının bir sonraki eşitleme döngüsünün tamamına tam eşitleme çalıştırılacağını bildiren uyarı iletişim kutusunda **Tamam** ' a tıklayın.
     ![Şimdi Kaydet](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    Kuralların oluşturulmasını onaylayın. **Parola eşitlemesini** **ve** **kural türü** **standardını** filtreler. Ayrıca yeni oluşturduğunuz kuralların her ikisini de görmeniz gerekir.
     ![Eşitleme tarihi](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>Eşitleme zamanlayıcısını yeniden etkinleştirin:  
Gerekli eşitleme kurallarını yapılandırma adımlarını tamamladıktan sonra, aşağıdaki adımlarla eşitleme zamanlayıcısını yeniden etkinleştirin:
 1. Windows PowerShell 'de Çalıştır:

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. Ardından, şu işlemi çalıştırarak başarıyla etkinleştirildiğini onaylayın:

     ```Get-ADSyncScheduler```

Zamanlayıcı hakkında daha fazla bilgi için bkz. [Azure AD Connect Sync Scheduler](how-to-connect-sync-feature-scheduler.md).

### <a name="edit-users-admindescription-attribute"></a>Kullanıcıları Düzenle **adminDescription** özniteliği:
Tüm yapılandırma işlemleri tamamlandıktan sonra, Active Directory içinde parola karması eşitlemesi için **eklemek** istediğiniz tüm kullanıcılar Için **adminDescription** öznitelik özelliğini düzenlemeniz gerekir ve kapsam filtresinde kullanılan dizeyi ekleyin: **phsdahildir**.

  ![Öznitelikleri Düzenle](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 Bir kullanıcının **adminDescription** özniteliğini düzenlemek Için aşağıdaki PowerShell komutunu da kullanabilirsiniz:

 ```Set-ADUser myuser -Replace @{adminDescription="PHSIncluded"}``` 

## <a name="next-steps"></a>Sonraki Adımlar
- [Parola karması eşitleme nedir?](whatis-phs.md)
- [Parola karması eşitlemesi nasıl çalışacaktır?](how-to-connect-password-hash-synchronization.md)
