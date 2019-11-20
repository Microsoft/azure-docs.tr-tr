---
title: Azure Lab Services sınıf laboratuvarlarında kullanım ayarlarını yapılandırma | Microsoft Docs
description: Laboratuvar için Kullanıcı sayısını yapılandırmayı, laboratuvara kaydolmalarını ve VM 'yi kullanbilecekleri saat sayısını denetlemeyi ve daha fazlasını öğrenin.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 0ab8d8688c7856eeae7d75527620c2b77ae78029
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584087"
---
# <a name="add-and-manage-lab-users"></a>Laboratuvar kullanıcılarını ekleme ve yönetme
Bu makalede, laboratuvara Kullanıcı ekleme, laboratuvarda nasıl kayıt yapılacağı, VM 'nin kullanabileceği saat sayısını denetleme ve daha fazla bilgi edinebilirsiniz. 


## <a name="add-users-to-the-lab"></a>Laboratuvara Kullanıcı ekleme

1. Sol menüdeki **Kullanıcılar** ' ı seçin. Varsayılan olarak, **erişimi kısıtla** seçeneği etkinleştirilmiştir. Bu ayar açık olduğunda, Kullanıcı kullanıcılar listesinde olmadığı takdirde Kullanıcı kayıt bağlantısına sahip olsa bile, Kullanıcı laboratuvara kayıt yapamıyor. Yalnızca listedeki kullanıcılar, göndereceğiniz kayıt bağlantısını kullanarak laboratuvara kayıt yapabilir. Bu yordamda, kullanıcıları listeye eklersiniz. Alternatif olarak, kayıt bağlantısı olduğu sürece kullanıcıların laboratuvara kaydolmaları için **erişimi kısıtla**' yı devre dışı bırakabilirsiniz. 
2. Araç çubuğundan **Kullanıcı Ekle** ' yi seçin ve ardından **e-posta adreslerine göre Ekle**' yi seçin. 

    ![Kullanıcı Ekle düğmesi](../media/how-to-configure-student-usage/add-users-button.png)
1. **Kullanıcı Ekle** sayfasında, kullanıcıların e-posta adreslerini ayrı satırlarda veya noktalı virgülle ayrılmış tek bir satıra girin. 

    ![Kullanıcı e-posta adresleri Ekle](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. **Kaydet**’i seçin. Kullanıcıların e-posta adreslerini ve bunların durumlarını (kayıtlı veya değil) listede görürsünüz. 

    ![Kullanıcı listesi](../media/how-to-configure-student-usage/users-list-new.png)

    > [!NOTE]
    > Laboratuvar 'e kaydolduktan sonra listedeki kullanıcıların adlarını görürsünüz. Listede gösterilen ad, Azure Active Directory kullanıcının ilk ve son adları kullanılarak oluşturulur. 

### <a name="add-users-by-uploading-a-csv-file"></a>CSV dosyası karşıya yükleyerek Kullanıcı ekleme
Kullanıcıları, kullanıcıların e-posta adreslerine sahip bir CSV dosyası karşıya yükleyerek da ekleyebilirsiniz.

1. Tek bir sütundaki kullanıcıların e-posta adreslerini içeren bir CSV dosyası oluşturun.

    ![Kullanıcılar ile CSV dosyası](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Laboratuvarın **Kullanıcılar** sayfasında, araç çubuğunda **Kullanıcı Ekle** ' yı seçin ve ardından **CSV 'yi karşıya yükle**' yi seçin.

    ![CSV 'yi Karşıya Yükle düğmesi](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Kullanıcı e-posta adreslerini içeren CSV dosyasını seçin. CSV dosyasını seçtikten sonra **Aç** ' ı seçtiğinizde, aşağıdaki **Kullanıcı Ekle** penceresini görürsünüz. E-posta adresi listesi CSV dosyasındaki e-posta adresleriyle doldurulur. 

    ![CSV dosyasından e-posta adresleriyle doldurulmuş Kullanıcı ekleme penceresi](../media/how-to-configure-student-usage/add-users-window.png)
4. **Kullanıcı Ekle** penceresinde **Kaydet** ' i seçin. 
5. Kullanıcılar listesinde kullanıcıları görtığınızdan emin olun. 

    ![Eklenen kullanıcıların listesi](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Kullanıcılara davet gönder
Öğrenciye kayıt bağlantısını göndermek için aşağıdaki yöntemlerden birini kullanın. İlk yöntem, öğrenciler için kayıt bağlantısı ve isteğe bağlı bir ileti ile e-posta gönderme işlemlerinin nasıl yapılacağını gösterir. İkinci yöntemde, başkalarıyla dilediğiniz şekilde paylaşabileceğiniz kayıt bağlantısını nasıl alacağınız gösterilmektedir. 

Laboratuvar için **erişimi kısıtla** etkinleştirilirse, yalnızca Kullanıcı listesindeki kullanıcılar, laboratuvara kaydolmak için kayıt bağlantısını kullanabilir. Bu seçenek varsayılan olarak etkindir. 

### <a name="invite-all-users"></a>Tüm kullanıcıları davet et
1. Zaten sayfada değilseniz **Kullanıcılar** görünümüne geçin ve araç çubuğunda **Tümünü davet et** ' i seçin. 

    ![Öğrencileri seçin](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. **E-postaya davet gönder** sayfasında, isteğe bağlı bir ileti girin ve ardından **Gönder**' i seçin. E-posta otomatik olarak kayıt bağlantısını içerir. Bu kayıt bağlantısını,... öğesini seçerek alabilirsiniz **. (üç nokta)** araç çubuğunda ve **kayıt bağlantısı**. 

    ![Kayıt bağlantısını e-posta ile gönder](../media/tutorial-setup-classroom-lab/send-email.png)
4. **Kullanıcı** listesindeki **davetin** durumunu görürsünüz. Durum, **\<tarih >** **gönderilmesi ve ardından** gönderilmek üzere değiştirilmelidir. 

    Bir sınıfa öğrenci ekleme ve laboratuvarın kullanımını yönetme hakkında daha fazla bilgi için bkz. [öğrenci kullanımını yapılandırma](how-to-configure-student-usage.md).

### <a name="invite-selected-users"></a>Seçili kullanıcıları davet et

1. Listeden bir kullanıcı veya birden çok kullanıcı seçin. 
2. Ardından, seçili satırda gördüğünüz **zarf** simgesini seçin (veya) araç çubuğunda **davet** ' i seçin. 

    ![Seçili kullanıcıları davet et](../media/how-to-configure-student-usage/invite-selected-users.png)
3. **E-posta ile davet gönder** penceresinde, isteğe bağlı bir **ileti**girin ve ardından **Gönder**' i seçin. 

    ![Seçili kullanıcılara e-posta gönder](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Bu işlemin durumunu **Kullanıcılar** listesinin **davet** sütununda görürsünüz. Davet e-postası, kullanıcıların laboratuvara kaydolmak için kullanabileceği kayıt bağlantısını içerir.

1. Zaten sayfada değilseniz, **Kullanıcılar** görünümüne geçin. 

## <a name="get-registration-link"></a>Kayıt bağlantısını al
Ayrıca, portaldan kayıt bağlantısını alabilir ve kendi e-posta istemci uygulamanızı kullanarak gönderebilirsiniz. 

1. Sol menüdeki **Kullanıcılar** ' a tıklayarak **Kullanıcılar** görünümüne geçin. 
2. Seç **... (üç nokta)** araç çubuğunda **kayıt bağlantısı**' nı seçin.

    ![Öğrenci kaydı bağlantısı](../media/how-to-configure-student-usage/registration-link-button.png)
1. **Kullanıcı kaydı** Iletişim kutusunda **Kopyala** düğmesini seçin. Bağlantı, panoya kopyalanır. Bağlantıyı bir e-posta düzenleyiciye yapıştırın ve öğrenciye e-posta gönderin. 

    ![Öğrenci kaydı bağlantısı](../media/how-to-configure-student-usage/registration-link.png)
2. **Kullanıcı kaydı** Iletişim kutusunda **bitti**' yi seçin. 
4. Öğrencinin sınıfa kaydolabilmesi için **kayıt bağlantısını** bir öğrenciye gönderin. 

## <a name="view-users-registered-with-the-lab"></a>Laboratuvara kayıtlı kullanıcıları görüntüleme

Laboratuvara kayıtlı kullanıcıların listesini görmek için sol menüdeki **Kullanıcılar** ' ı seçin. 

![Laboratuvara kayıtlı kullanıcıların listesi](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Kullanıcılar için kota ayarlama
Aşağıdaki adımları kullanarak, Kullanıcı başına kota ayarlayabilirsiniz: 

1. Sayfa zaten etkin değilse Sol menüdeki **Kullanıcılar** ' ı seçin. 
2. **Kullanıcı başına kota seçin: araç çubuğunda \<sayı > saat** . 
3. **Kullanıcı başına kota** sayfasında, zamanlanan sınıf süresi dışında her kullanıcıya (öğrenci) vermek istediğiniz saat sayısını belirtin ve ardından **Kaydet**' i seçin.

    ![Kullanıcı başına kota](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Değiştirilen değerleri şimdi araç çubuğunda görürsünüz: **Kullanıcı başına kota: \<saat sayısı >** . 

    ![Kullanıcı başına kota (sonrasında)](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [VM 'lerin zamanlanan çalışma süresi](how-to-create-schedules.md) , bir kullanıcıya ayrılan kotaya göre sayılmaz. Kota, bir öğrencinin VM 'lerde harcadığı zamanlama saatleri dışında bir süredir. 

## <a name="set-additional-quota-for-a-specific-user"></a>Belirli bir kullanıcı için ek kota ayarlama
Bir kullanıcı için ek kota belirtebilirsiniz. Bu kota, önceki bölümde yer aldığı tüm kullanıcılar için genel kota kümesine ek olarak yapılır. Örneğin, (bir eğitmen olarak) tüm kullanıcılar için kotayı 10 saate ayarlarsanız ve belirli bir kullanıcı için 5 saatlik ek kota ayarlarsanız, kullanıcılar 15 (10 + 5) saatlik kotayı alır. Ortak kotayı daha sonra olarak değiştirirseniz, 15, daha sonra Kullanıcı 20 (15 + 5) saatlik kotayı alır. Bu genel kotanın zamanlanan sürenin dışında olduğunu unutmayın. Zamanlama sırasında öğrencinin bir laboratuvar VM 'sinde harcadığı süre bu kotaya göre sayılmaz. 

Bunu yapmak için şu adımları izleyin:

1. **Kullanıcılar** sayfasındaki kullanıcılar listesinden bir Kullanıcı (öğrenci) seçin.
2. Ardından araç çubuğundan **kotayı ayarla** ' yı seçin. 

    ![Kotayı ayarla düğmesi](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Seçili kullanıcı veya kullanıcılar için **ek saat** sayısını girin ve ardından **Uygula**' yı seçin. 

    ![Bir kullanıcı için ek kota](../media/how-to-configure-student-usage/additional-quota.png)
4. **Kullanım** sütununda kullanıcının güncelleştirilmiş kullanımını görürsünüz. 

    ![Kullanıcı için yeni kullanım](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Öğrenci hesapları
Bir sınıf laboratuvarına öğrenci eklemek için e-posta hesaplarını kullanın. Aşağıdaki e-posta hesabı türleri kullanılabilir:

- University in Office 365 Azure Active Directory (AAD) tarafından sunulan bir öğrenci e-posta hesabı. 
- `@outlook.com`, `@hotmail.com`, `@msn.com`veya `@live.com`gibi bir Microsoft e-posta hesabı.
- Microsoft olmayan bir e-posta hesabı, örneğin, Yahoo veya Google tarafından sağlanmamıştır. Ancak, bu tür hesapların bir Microsoft hesabı bağlantılı olması gerekir.
- GitHub hesabı. Bu hesabın bir Microsoft hesabı ile bağlantılı olması gerekir.

### <a name="using-a-non-microsoft-email-account"></a>Microsoft olmayan bir e-posta hesabı kullanma
Öğrenciler, bir sınıf laboratuvarına kaydolmak ve oturum açmak için Microsoft olmayan e-posta hesaplarını kullanabilir.  Ancak kayıt, öğrencilerin öncelikle Microsoft olmayan e-posta adreslerine bağlı bir Microsoft hesabı oluşturmasını gerektirir.

Birçok öğrenciye, Microsoft olmayan e-posta adreslerine bağlı bir Microsoft hesabı zaten sahip olabilir. Örneğin, öğrenciler zaten Microsoft 'un e-posta adreslerini Office, Skype, OneDrive veya Windows gibi diğer ürün veya hizmetleriyle kullandıklarında Microsoft hesabı sahiptir.  

Bir öğrenci bir sınıfta oturum açmak için kayıt URL 'sine tıkladığında, kendilerine ait e-posta adresi ve parolası sorulur. Öğrenci, Microsoft hesabı bağlantılı olmayan bir Microsoft hesabı oturum açmaya çalışırsa, öğrenci aşağıdaki hata iletisini alır: 

![Hata iletisi](../media/how-to-configure-student-usage/cant-find-account.png)

Microsoft hesabı kaydolmak için öğrenciler [http://signup.live.com](http://signup.live.com)gitmelidir.  

> [!IMPORTANT]
> Öğrenciler bir derslik laboratuvarında oturum açtıklarında, bunlara Microsoft hesabı oluşturma seçeneği verilmez. Bu nedenle, bu kayıt bağlantısını Microsoft dışı hesapları kullanan öğrencilerle göndereceğiniz sınıf Laboratuvarı kayıt e-postasına dahil etmenizi öneririz.

### <a name="using-a-github-account"></a>GitHub hesabı kullanma
Öğrenciler, bir sınıf laboratuvarına kaydolmak ve oturum açmak için mevcut bir GitHub hesabı da kullanabilir. Öğrencinin zaten GitHub hesabına bağlı bir Microsoft hesabı varsa, bu kullanıcılar oturum açabilir ve önceki bölümde gösterildiği gibi parolasını sağlayabilir. Henüz GitHub hesabını bir Microsoft hesabı bağlamamış olmaları durumunda, **oturum açma seçeneklerini**seçmeleri gerekir:

![Oturum açma seçenekleri bağlantısı](../media/how-to-configure-student-usage/signin-options.png)

**Oturum açma seçenekleri** sayfasında **GitHub ile oturum aç**' ı seçin.

![GitHub bağlantısıyla oturum açın](../media/how-to-configure-student-usage/signin-github.png)

Son olarak, bunlara GitHub hesaplarına bağlı bir Microsoft hesabı oluşturması istenir. Öğrenci bir **Sonrakini**seçtiğinde otomatik olarak gerçekleşir.  Daha sonra öğrenci hemen oturum açtı ve derslik laboratuvarına bağlanır.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Yönetici olarak, laboratuvar hesapları oluşturun ve yönetin](how-to-manage-lab-accounts.md)
- [Laboratuvar sahibi olarak, Labs oluşturma ve yönetme](how-to-manage-classroom-labs.md)
- [Laboratuvar sahibi olarak, şablonları ayarlama ve yayımlama](how-to-create-manage-template.md)
- [Laboratuvar kullanıcısı olarak, sınıf laboratuvarlarına erişin](how-to-use-classroom-lab.md)
