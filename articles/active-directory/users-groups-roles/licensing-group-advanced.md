---
title: Grup tabanlı lisanslama ek senaryolar-Azure AD | Microsoft Docs
description: Azure Active Directory grup tabanlı lisanslama için daha fazla senaryo
services: active-directory
keywords: Azure AD lisanslama
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 139d7e0cf2b57cc466dc97370b90a599257ce755
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025957"
---
# <a name="scenarios-limitations-and-known-issues-using-groups-to-manage-licensing-in-azure-active-directory"></a>Azure Active Directory 'de lisanslamayı yönetmek için grupları kullanarak senaryolar, sınırlamalar ve bilinen sorunlar

Azure Active Directory (Azure AD) grup tabanlı lisanslama hakkında daha gelişmiş bir bilgi edinmek için aşağıdaki bilgileri ve örnekleri kullanın.

## <a name="usage-location"></a>Kullanım konumu

Bazı Microsoft hizmetleri tüm konumlarda kullanılamaz. Bir lisansın bir kullanıcıya atanabilmesi için önce yönetici kullanıcı üzerinde **Kullanım konumu** özelliğini belirtmelidir. [Azure Portal](https://portal.azure.com), **kullanıcı** &gt; **profil** &gt; **ayarları**' nda kullanım konumu belirtebilirsiniz.

Grup lisansı ataması için, kullanım konumu belirtilmemiş tüm kullanıcılar dizinin konumunu alır. Birden çok konumdaki kullanıcılarınız varsa, lisanslarına Kullanıcı eklemeden önce Kullanıcı kaynaklarınıza doğru şekilde yansıtıdığınızdan emin olun.

> [!NOTE]
> Grup lisans ataması, bir kullanıcının mevcut kullanım konumu değerini hiçbir şekilde değiştirmez. Kullanım konumunu, Azure AD 'de Kullanıcı oluşturma akışınız kapsamında (ör. AAD Connect yapılandırması aracılığıyla) ayarlamanızı öneririz. Bu, lisans atamasının sonucunun her zaman doğru olmasını ve kullanıcıların izin verilmeyen konumlarda hizmet almamalarını güvence altına alır.

## <a name="use-group-based-licensing-with-dynamic-groups"></a>Dinamik gruplarla grup tabanlı lisanslama kullanma

Grup tabanlı lisanslamayı herhangi bir güvenlik grubuyla birlikte kullanabilirsiniz. Bu, Azure AD dinamik gruplarıyla birleştirilebilecek anlamına gelir. Dinamik Gruplar, kullanıcıları gruplara otomatik olarak eklemek ve gruplara kaldırmak için Kullanıcı kaynak özniteliklerine karşı kuralları çalıştırır.

Örneğin, kullanıcılara atamak istediğiniz bazı ürün kümesi için dinamik bir grup oluşturabilirsiniz. Her grup, öznitelikleri tarafından Kullanıcı ekleyen bir kuralla doldurulur ve her gruba, almasını istediğiniz lisanslar atanır. Özniteliği şirket içinde atayabilir ve Azure AD ile eşitleyebilir ya da özniteliği doğrudan bulutta yönetebilirsiniz.

Lisanslar, gruba eklendikten sonra kullanıcıya atanır. Öznitelik değiştirildiğinde, Kullanıcı gruplardan kalır ve lisanslar kaldırılır.

### <a name="example"></a>Örnek

Hangi kullanıcıların Microsoft Web Hizmetleri 'ne erişiminin olması gerektiğine karar veren bir şirket içi kimlik yönetimi çözümü örneğini göz önünde bulundurun. Kullanıcının sahip olması gereken lisansları temsil eden bir dize değeri depolamak için **extensionAttribute1** kullanır. Azure AD Connect Azure AD ile eşitler.

Kullanıcılar bir lisansa sahip olsa da bir lisansa gerek duyar veya her ikisine de ihtiyaç duyar. Burada, Office 365 Kurumsal E5 ve Enterprise Mobility + Security (EMS) lisanslarını gruplardaki kullanıcılara dağıttığınız bir örnek verilmiştir:

#### <a name="office-365-enterprise-e5-base-services"></a>Office 365 Kurumsal E5: temel hizmetler

![Office 365 Enterprise E5 temel hizmetlerinin ekran görüntüsü](./media/licensing-group-advanced/o365-e5-base-services.png)

#### <a name="enterprise-mobility--security-licensed-users"></a>Enterprise Mobility + Security: lisanslı kullanıcılar

![Enterprise Mobility + Security lisanslı kullanıcıların ekran görüntüsü](./media/licensing-group-advanced/o365-e5-licensed-users.png)

Bu örnekte, kullanıcının her iki lisansa de sahip olmasını istiyorsanız, bir kullanıcıyı değiştirin ve extensionAttribute1 değerini `EMS;E5_baseservices;` değerine ayarlayın. Bu değişikliği şirket içinde yapabilirsiniz. Değişiklik bulutla eşitlendikten sonra, Kullanıcı her iki gruba da otomatik olarak eklenir ve lisanslar atanır.

![Kullanıcının extensionAttribute1 nasıl ayarlanacağını gösteren ekran görüntüsü](./media/licensing-group-advanced/user-set-extensionAttribute1.png)

> [!WARNING]
> Mevcut bir grubun üyelik kuralını değiştirirken dikkatli olun. Bir kural değiştirildiğinde, grubun üyeliği yeniden değerlendirilir ve yeni kuralla eşleşmeyen kullanıcılar kaldırılır (Bu işlem sırasında yeni kuralla eşleşen kullanıcılar bundan etkilenmez). Bu kullanıcıların lisansları, hizmet kaybına veya bazı durumlarda veri kaybına neden olabilen işlem sırasında kaldırılır.
> 
> Lisans atamasına bağlı olan büyük bir dinamik grubunuz varsa, bunları ana gruba uygulamadan önce daha küçük bir test grubundaki önemli değişiklikleri doğrulamayı düşünün.

## <a name="multiple-groups-and-multiple-licenses"></a>Birden çok grup ve birden çok lisans

Kullanıcı, lisanslarla birden çok grubun üyesi olabilir. Göz önünde bulundurmanız gereken bazı noktalar şunlardır:

- Aynı ürün için birden fazla lisans çakışabilir ve bu kişiler, kullanıcıya tüm etkin hizmetlerin uygulanmasına neden olabilir. Aşağıdaki örnekte iki lisans grubu gösterilmektedir: *E3 Base Services* , önce dağıtılacak olan Foundation hizmetlerini tüm kullanıcılara içerir. Ve *E3 Extended Services* yalnızca bazı kullanıcılara dağıtılacak ek hizmetler (Sway ve Planner) içerir. Bu örnekte, Kullanıcı her iki gruba de eklenmiştir:

  ![Etkin hizmetlerin ekran görüntüsü](./media/licensing-group-advanced/view-enabled-services.png)

  Sonuç olarak, Kullanıcı bu ürün için yalnızca bir lisans kullanırken, ürünün etkin olduğu 12 hizmetten 7 ' ye sahiptir.

- *E3* lisansının seçilmesi, Kullanıcı için hangi hizmetlerin etkin olduğunu grup lisans ataması ile ilgili bilgiler de dahil olmak üzere daha fazla ayrıntı gösterir.

## <a name="direct-licenses-coexist-with-group-licenses"></a>Doğrudan lisanslar grup lisanslarıyla birlikte

Bir Kullanıcı bir gruptan lisans devralırsa, bu lisans atamasını Kullanıcı özelliklerinde doğrudan kaldıramaz veya değiştiremezsiniz. Değişikliklerin grupta yapılması ve sonra tüm kullanıcılara yayılması gerekir.

Ancak, devralınan lisansın yanı sıra aynı ürün lisansını doğrudan kullanıcıya atamak mümkündür. Diğer kullanıcıları etkilemeden, üründen yalnızca bir kullanıcı için ek hizmetler etkinleştirebilirsiniz.

Doğrudan atanan lisanslar kaldırılabilir ve devralınmış lisansları etkilemez. Bir gruptan Office 365 Kurumsal E3 lisansı devralan kullanıcıyı göz önünde bulundurun.

Başlangıçta, kullanıcı lisansı yalnızca dört hizmet planını sağlayan *E3 temel hizmetler* grubundan devralır.

1. Kullanıcıya doğrudan bir E3 lisansı atamak için **ata** ' yı seçin. Bu durumda, Yammer Enterprise dışında tüm hizmet planlarını devre dışı bırakacağız.

    Sonuç olarak, Kullanıcı E3 ürününün yalnızca bir lisansını kullanır. Ancak doğrudan atama, yalnızca bu kullanıcı için Yammer Kurumsal hizmetini mümkün bir şekilde sunar. Grup üyeliği tarafından hangi hizmetlerin etkinleştirildiğini doğrudan atamaya karşı görebilirsiniz.

1. Doğrudan atamayı kullandığınızda, aşağıdaki işlemlere izin verilir:

   - Yammer Enterprise, Kullanıcı kaynağında doğrudan kapatılabilir. Çizimdeki **açık/kapalı** geçişi, diğer hizmetin aksine bu hizmet için etkinleştirildi. Hizmet doğrudan kullanıcı üzerinde etkinleştirildiğinden, değiştirilebilir.
   - Ek hizmetler, doğrudan atanan lisansın parçası olarak da etkinleştirilebilir.
   - **Kaldır** düğmesi, kullanıcıdan doğrudan lisansı kaldırmak için kullanılabilir. Kullanıcının şimdi yalnızca devralınan grup lisansına sahip olduğunu ve yalnızca özgün hizmetlerin etkin kalacağını görebilirsiniz:

## <a name="managing-new-services-added-to-products"></a>Ürünlere eklenen yeni hizmetleri yönetme

Microsoft bir ürün lisans planına yeni bir hizmet eklediğinde, ürün lisansını atadığınız tüm gruplarda varsayılan olarak etkinleştirilir. Kiracınızdaki ürün değişiklikleriyle ilgili bildirimlere abone olan kullanıcılar, yaklaşan hizmet eklemeleri hakkında bilgilendirmeye devam eden e-postaları alacaktır.

Yönetici olarak, her grupta yeni hizmeti devre dışı bırakmak gibi değişiklik ve eylem eylemini etkileyen tüm grupları gözden geçirebilirsiniz. Örneğin, yalnızca dağıtım için belirli hizmetleri hedefleyen gruplar oluşturduysanız, bu grupları yeniden ziyaret edebilir ve yeni eklenen hizmetlerin devre dışı bırakıldığından emin olabilirsiniz.

Bu işlemin nasıl görünebileceklerini aşağıda görebilirsiniz:

1. İlk olarak, *Office 365 Enterprise E5* ürününü birkaç gruba atamış olursunuz. *O365 E5-Exchange* olarak adlandırılan gruplardan biri yalnızca üyeleri Için *Exchange Online (plan 2)* hizmetini etkinleştirmek üzere tasarlanmıştır.

2. Microsoft 'un, E5 ürününün yeni bir hizmet *Microsoft Stream*uzatılmayacak bir bildirim aldınız. Hizmet kiracınızda kullanılabilir hale geldiğinde, şunları yapabilirsiniz:

3. [**Tüm ürünler dikey penceresinde Azure Active Directory > > lisanslar**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) ' a gidin ve *Office 365 Kurumsal E5*' i seçin, sonra da bu ürüne sahip tüm grupların bir listesini görüntülemek için **lisanslı gruplar** ' ı seçin.

4. Gözden geçirmek istediğiniz gruba (Bu durumda *O365 E5-Exchange*) tıklayın. Bu, **lisanslar** sekmesini açar. E5 Lisansı ' na tıkladığınızda, tüm etkin hizmetler listelenerek bir dikey pencere açılır.
   > [!NOTE]
   > *Microsoft Stream* hizmeti, *Exchange Online* hizmetine ek olarak bu gruba otomatik olarak eklendi ve etkinleştirildi:

   ![Bir grup lisansına eklenen yeni hizmetin ekran görüntüsü](./media/licensing-group-advanced/manage-new-services.png)

5. Bu gruptaki yeni hizmeti devre dışı bırakmak istiyorsanız, hizmetin yanındaki Aç **/Kapat** düğmesine tıklayın ve değişikliği onaylamak için **Kaydet** düğmesine tıklayın. Artık Azure AD, değişikliği uygulamak için gruptaki tüm kullanıcıları işleyecek; gruba eklenen tüm yeni kullanıcılar *Microsoft Stream* hizmeti etkin olmaz.

   > [!NOTE]
   > Kullanıcılar yine de hizmetin başka bir lisans ataması (üyesi oldukları başka bir grup veya bir doğrudan lisans ataması) aracılığıyla etkinleştirilmeye devam edebilir.

6. Gerekirse, bu ürünün atandığı diğer gruplar için aynı adımları gerçekleştirin.

## <a name="use-powershell-to-see-who-has-inherited-and-direct-licenses"></a>Kimin devralınan ve doğrudan lisanslarına sahip olduğunu görmek için PowerShell 'i kullanın
Kullanıcıların doğrudan atanmış veya gruptan devralınan bir lisansa sahip olup olmadığını denetlemek için bir PowerShell betiği kullanabilirsiniz.

1. Kimlik doğrulaması yapmak ve kiracınızda bağlantı kurmak için `connect-msolservice` cmdlet 'ini çalıştırın.

2. `Get-MsolAccountSku`, Kiracıdaki tüm sağlanan Ürün lisanslarını saptamak için kullanılabilir.

   ![Get-Msolaccountsku cmdlet 'inin ekran görüntüsü](./media/licensing-group-advanced/get-msolaccountsku-cmdlet.png)

3. [Bu PowerShell betiği](licensing-ps-examples.md#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)ile ilgilendiğiniz lisans Için *accountskuid* değerini kullanın. Bu, lisansın atanma şekli hakkında bilgi ile bu lisansa sahip kullanıcıların bir listesini oluşturur.

## <a name="use-audit-logs-to-monitor-group-based-licensing-activity"></a>Grup tabanlı lisanslama etkinliğini izlemek için Denetim günlüklerini kullanma

Aşağıdakiler de dahil olmak üzere, grup tabanlı lisanslamayla ilgili tüm etkinlikleri görmek için [Azure AD denetim günlüklerini](../reports-monitoring/concept-audit-logs.md#audit-logs) kullanabilirsiniz:
- gruplardaki lisansları kim değiştirdi
- Sistem bir grup lisansı değişikliğini işlemeye başladığında ve tamamlandığında
- bir grup lisansı atamasının sonucu olarak bir kullanıcıya yapılan lisans değişiklikleri.

>[!NOTE]
> Denetim günlükleri, portalın Azure Active Directory bölümünde çoğu Blade üzerinde kullanılabilir. Bunlara nereden eriştiğinize bağlı olarak, filtreler yalnızca dikey pencerenin bağlamı ile ilgili etkinliği göstermek için önceden uygulanmış olabilir. Tahmin ettiğiniz sonuçları görmüyorsanız, [filtreleme seçeneklerini](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) inceleyin veya [**Azure Active Directory > etkinlik > Denetim günlükleri**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit)altındaki filtrelenmemiş denetim günlüklerine erişin.

### <a name="find-out-who-modified-a-group-license"></a>Bir grup lisansını kimin değiştirildiğini öğrenin

1. **Etkinlik** filtresini *Grup lisansı ayarlamak* için ayarlayın ve **Uygula**' ya tıklayın.
2. Sonuçlar, bazı lisansların grup üzerinde ayarlanan veya değiştirilen tüm durumlarını içerir.
   >[!TIP]
   > Sonuçların kapsamını atamak için *hedef* filtreye grubun adını da yazabilirsiniz.

3. Nelerin değiştiğini görmek için listeden bir öğe seçin. *Değiştirilen özellikler* altında, lisans atamasının hem eski hem de yeni değerleri listelenir.

Ayrıntıları içeren son grup lisansı değişikliklerinin bir örneği aşağıda verilmiştir:

![Ekran görüntüsü grup lisansı değişiklikleri](./media/licensing-group-advanced/audit-group-license-change.png)

### <a name="find-out-when-group-changes-started-and-finished-processing"></a>Grup değişikliklerinin ne zaman başlatıldığını ve işlemin tamamlandığını öğrenin

Bir grup üzerinde bir lisans değiştiğinde Azure AD, değişiklikleri tüm kullanıcılara uygulamaya başlayacaktır.

1. Grupların işlemeye ne zaman başlatıldığını görmek için, **etkinlik** filtresini *kullanıcılara grup tabanlı lisans uygulamaya başlayacak*şekilde ayarlayın. İşlemin aktör, Grup *tabanlı lisanslama Microsoft Azure AD* olduğunu ve tüm grup lisansı değişikliklerini yürütmek için kullanılan bir sistem hesabı olduğunu unutmayın.
   >[!TIP]
   > *Değiştirilen özellikler* alanını görmek için listedeki bir öğeye tıklayın-işlenmek üzere çekilen lisans değişikliklerini gösterir. Bu, bir grupta birden fazla değişiklik yaptıysanız ve hangisinin işlendiğinden emin değilseniz yararlıdır.

2. Benzer şekilde, grupların işlemenin ne zaman tamamlandığını görmek için, *kullanıcılara grup tabanlı lisans uygulamayı tamamlama*filtre değerini kullanın.
   > [!TIP]
   > Bu durumda, *değiştirilen özellikler* alanı sonuçların bir özetini içerir. işlemin hatalara neden olup olmadığını hızlı bir şekilde denetlemek için kullanışlıdır. Örnek çıktı:
   > ```
   > Modified Properties
   > ...
   > Name : Result
   > Old Value : []
   > New Value : [Users successfully assigned licenses: 6, Users for whom license assignment failed: 0.];
   > ```

3. Tüm Kullanıcı değişiklikleri dahil olmak üzere bir grubun nasıl işlendiği hakkındaki günlüğün tamamını görmek için aşağıdaki filtreleri ayarlayın:
   - **Başlatan (aktör)** : "grup tabanlı lisanslama Microsoft Azure AD"
   - **Tarih aralığı** (isteğe bağlı): belirli bir grubun başlatıldığını ve tamamlandığını bilmeniz için özel Aralık

Bu örnek çıktı, işleme başlangıcını, tüm ortaya çıkan Kullanıcı değişikliklerini ve işleme bitişini gösterir.

![Ekran görüntüsü grup lisansı değişiklikleri](./media/licensing-group-advanced/audit-group-processing-log.png)

>[!TIP]
> *Kullanıcı lisansıyla* ilgili öğelere tıkladığınızda, her kullanıcıya uygulanan lisans değişikliklerinin ayrıntıları gösterilir.

## <a name="deleting-a-group-with-an-assigned-license"></a>Atanmış lisansa sahip bir grubu silme

Etkin bir lisansın atandığı bir grubu silmek mümkün değildir. Bir yönetici, lisansların kullanıcılardan kaldırılmasına neden olacağını belirten bir grubu silebilir. bu nedenle, silinmeden önce herhangi bir lisansın önce gruptan kaldırılmasını gerektiririz.

Azure portal bir grubu silmeye çalışırken şu şekilde bir hata bildirimi görebilirsiniz: ![ekran görüntüsü grubu silme başarısız](./media/licensing-group-advanced/groupdeletionfailed.png)

Gruptaki **lisanslar** sekmesine gidin ve atanmış herhangi bir lisans olup olmadığını görün. Yanıt Evet ise, bu lisansları kaldırın ve grubu silmeyi yeniden deneyin.

Grubu PowerShell veya Graph API ile silmeye çalışırken benzer hatalar görebilirsiniz. Şirket içinden eşitlenmiş bir grup kullanıyorsanız Azure AD Connect, Azure AD 'de grubu silmemek durumunda da hataları bildirebilir. Bu gibi durumlarda, gruba atanmış bir lisans olup olmadığını kontrol ettiğinizden emin olun ve önce bunları kaldırın.

## <a name="limitations-and-known-issues"></a>Sınırlamalar ve bilinen sorunlar

Grup tabanlı lisanslama kullanıyorsanız, aşağıdaki sınırlamalar ve bilinen sorunlar listesini öğrenmek iyi bir fikirdir.

- Grup tabanlı lisanslama Şu anda diğer grupları (iç içe gruplar) içeren grupları desteklemez. İçine yerleştirilmiş başka bir grup olan bir gruba lisans uyguladığınızda yalnızca grubun birinci düzeyindeki üyelerine lisans atanır.

- Özelliği yalnızca güvenlik grupları ve securityEnabled = TRUE olan Office 365 grupları ile birlikte kullanılabilir.

- [Microsoft 365 Yönetim Merkezi](https://admin.microsoft.com) Şu anda grup tabanlı lisanslamayı desteklemez. Bir Kullanıcı bir gruptan lisans devralırsa, bu lisans Office Yönetim Portalı 'nda normal bir kullanıcı lisansı olarak görüntülenir. Bu lisansı değiştirmeye veya lisansı kaldırmaya çalışırsanız, Portal bir hata mesajı döndürür. Devralınan grup lisansları doğrudan bir kullanıcı üzerinde değiştirilemez.

- Lisanslar büyük bir grup (örneğin, 100.000 Kullanıcı) için atandığında veya değiştirildiğinde performansı etkileyebilir. Özellikle, Azure AD Otomasyonu tarafından oluşturulan değişiklikler hacmi, Azure AD ile şirket içi sistemler arasında Dizin eşitlemesinin performansını olumsuz yönde etkileyebilir.

- Kullanıcılarınızın üyeliklerini yönetmek için dinamik grupları kullanıyorsanız kullanıcının grubun bir parçası olduğunu doğrulayın. Bu durum lisans atama için gereklidir. Aksi takdirde dinamik grubun [üyelik kuralı işleme durumunu denetleyin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule).

- Bazı yüksek yük durumlarında, mevcut lisanslarla gruplar veya üyelik değişiklikleri için lisans değişikliklerinin işlenmesi uzun zaman alabilir. Değişikliklerinizin, 60K kullanıcıları veya daha az grup boyutunu işlemek için 24 saatten uzun sürmek istiyorsanız lütfen araştırmamızı sağlamak için [bir destek bileti açın](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/supportRequest) . 

- Lisans Yönetimi Otomasyonu, ortamdaki tüm değişiklik türlerine otomatik olarak tepki vermez. Örneğin, lisanslarınızın tükenmesine ve bazı kullanıcıların hata durumunda olmasına neden olabilirsiniz. Kullanılabilir bilgisayar sayısını boşaltmak için, diğer kullanıcılardan doğrudan atanmış bazı lisansları kaldırabilirsiniz. Ancak, sistem bu değişikliğe otomatik olarak tepki vermez ve bu hata durumundaki kullanıcıları düzeltir.

  Bu tür sınırlamalar için geçici bir çözüm olarak, Azure AD 'de **Grup** dikey penceresine gidebilir ve **yeniden işle**' ye tıklayabilirsiniz. Bu komut, bu gruptaki tüm kullanıcıları işler ve mümkünse hata durumlarını çözer.

## <a name="next-steps"></a>Sonraki adımlar

Grup tabanlı lisanslama aracılığıyla lisans yönetimine yönelik diğer senaryolar hakkında daha fazla bilgi edinmek için bkz:

* [Azure Active Directory 'de grup tabanlı lisanslama nedir?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory'de gruba lisans atama](licensing-groups-assign.md)
* [Azure Active Directory'de grubun lisans sorunlarını tanımlama ve çözme](licensing-groups-resolve-problems.md)
* [Azure Active Directory'de tek tek lisanslı kullanıcıları grup tabanlı lisanslamaya geçirme](licensing-groups-migrate-users.md)
* [Kullanıcılar Azure Active Directory'de Grup tabanlı lisanslama kullanarak ürün lisansları arasında geçirme](../users-groups-roles/licensing-groups-change-licenses.md)
* [Azure Active Directory'de Grup tabanlı lisanslama için PowerShell örnekleri](../users-groups-roles/licensing-ps-examples.md)
