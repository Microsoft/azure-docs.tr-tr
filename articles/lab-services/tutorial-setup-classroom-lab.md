---
title: Azure Lab Services kullanarak sınıf laboratuvarı ayarlama | Microsoft Docs
description: Bu öğreticide, sınıfınızda öğrenciler tarafından kullanılan sanal makinelerle bir derslik Laboratuvarı kurmak için Azure Lab Services kullanırsınız.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 8093a1fd270cdba8bdccaf48737bf6737bdd394d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98787426"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Öğretici: Bir sınıf laboratuvarı ayarlama 
Bu öğreticide, sınıftaki öğrenciler tarafından kullanılan sanal makinelerle bir sınıf laboratuvarı ayarlayacaksınız.  

Bu öğreticide, aşağıdaki eylemleri gerçekleştireceksiniz:

> [!div class="checklist"]
> * Sınıf laboratuvarı oluşturma
> * Laboratuvara Kullanıcı ekleme
> * Laboratuvar için zamanlamayı ayarla
> * Öğrenciye davetiye e-postası gönder

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticide, sınıfınız için sanal makinelerle bir laboratuvar ayarlarsınız. Laboratuvar hesabında bir derslik Laboratuvarı ayarlamak için laboratuvar hesabındaki şu rollerden birine üye olmanız gerekir: sahip, laboratuvar Oluşturucusu veya katkıda bulunan. Laboratuvar hesabı oluşturmak için kullandığınız hesap, sahip rolüne otomatik olarak eklenir. Bu nedenle, bir sınıf laboratuvarı oluşturmak için laboratuvar hesabı oluşturmak için kullandığınız kullanıcı hesabını kullanabilirsiniz. 

Azure Lab Services kullanırken tipik iş akışı aşağıda verilmiştir:

1. Laboratuvar hesabı Oluşturucusu, diğer kullanıcıları **Laboratuvar Oluşturucu** rolüne ekler. Örneğin, laboratuvar hesabı oluşturan/yönetici laboratuvar **Oluşturucu** rolüne eğitimciler ekler; böylece, sınıfları için laboratuvarları oluşturabilirler. 
2. Daha sonra eğitimciler, kendi sınıfları için VM 'Ler ile Labs oluşturma ve sınıftaki öğrencilerle kayıt bağlantıları gönderme. 
3. Öğrenciler, eğitimciler 'tan, laboratuvara kaydolmak için aldıkları kayıt bağlantısını kullanır. Kaydolduktan sonra, iş ve ev işlerini yapmak için laboratuvarlarda VM 'Leri kullanabilirler. 

## <a name="create-a-classroom-lab"></a>Sınıf laboratuvarı oluşturma
Bu adımda, Azure 'da sınıfınız için bir laboratuvar oluşturacaksınız. 

1. [Azure Lab Services web sitesine](https://labs.azure.com) gidin. Internet Explorer 11 ' in henüz desteklenmediğini unutmayın. 
2. **Oturum aç**’ı seçip kimlik bilgilerinizi girin. Azure Lab Services, kuruluş hesaplarını ve Microsoft hesaplarını destekler. 
3. **Yeni laboratuvar**' ı seçin. 
    
    !["Yeni laboratuvar" düğmesi seçiliyken "Azure Lab Services" gösteren ekran görüntüsü.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. **Yeni Laboratuvar** penceresinde aşağıdaki eylemleri gerçekleştirin: 
    1. Laboratuvarınız için bir **ad** belirtip **İleri**' yi seçin.  

        ![Sınıf laboratuvarı oluşturma](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. **Sanal makine kimlik bilgileri** sayfasında, laboratuvardaki tüm VM 'ler için varsayılan kimlik bilgilerini belirtin. Kullanıcının **adını** ve **parolasını** belirtin ve ardından **İleri**' yi seçin.  

        ![Yeni laboratuvar penceresi](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Kullanıcı adını ve parolayı not edin. Bunlar tekrar gösterilmeyecektir.
    3. **Laboratuvar ilkeleri** sayfasında **son**' u seçin. 

        ![Her Kullanıcı için kota](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Şablon VM oluşturma 'nın durumunu gösteren aşağıdaki ekranı görmeniz gerekir. Bu işlem 20 dakikaya kadar sürer. 

    ![Şablon VM oluşturma durumu](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **Şablon** sayfasında, aşağıdaki adımları uygulayın: Bu adımlar öğretici için **isteğe bağlıdır** .

    1. **Bağlan**'ı seçerek şablon VM'sine bağlanın. Bir Linux şablon sanal makinesi ise, SSH veya RDP kullanarak bağlanmak isteyip istemediğinizi seçin (RDP etkinse).
    3. Şablon VM 'de sınıfınız için gereken yazılımları yükleyip yapılandırın. 
    4. Şablon VM 'yi **durdurun** .  

    > [!NOTE]
    > Şablon VM 'Leri çalışırken **Maliyet** doğurur, bu nedenle çalışıyor olması GEREKMIYORSA şablon VM 'nin kapatıldığından emin olun. 

## <a name="publish-the-template-vm"></a>Şablon VM'yi yayımlama
Bu adımda, şablon VM 'yi yayımlarsınız. Şablon sanal makinesini yayımladığınızda, Azure Lab Services şablonu kullanarak laboratuvarda VM 'Ler oluşturur. Tüm sanal makineler, şablonla aynı yapılandırmaya sahiptir.

1. **Şablon** sayfasında, araç çubuğunda **Yayımla** ' yı seçin. 

    ![Şablon Yayımla düğmesi](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Yayımlama işlemini geri alamazsınız. 
2. **Şablonu Yayımla** sayfasında, laboratuvarda oluşturmak istediğiniz sanal makine sayısını girin ve ardından **Yayımla**' yı seçin. 

    ![Şablon yayımlama-sanal makine sayısı](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Şablonu **Yayımlama durumunu** sayfada görürsünüz. Bu işlemin tamamlanması bir saat sürebilir. 

    ![Şablonu yayımlama - ilerleme](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Yayımlama tamamlanana kadar bekleyin ve ardından sol menüdeki **sanal** makineler ' i seçerek veya **sanal makineler** Kutucuğu ' nı seçerek **sanal makine havuzu** sayfasına geçiş yapın. **Atanmamış** durumda olan sanal makineleri görtığınızdan emin olun. Bu VM’ler henüz bir öğrenciye atanmamıştır. Bu makinelerin durumu **Durduruldu** olmalıdır. Bu sayfadan bir öğrenci VM'sini başlatabilir, VM'ye bağlanabilir, VM'yi durdurabilir ve VM'yi silebilirsiniz. VM'leri bu sayfadan başlatabilir veya öğrencilerinizin başlatmasını sağlayabilirsiniz. 

    ![Durdurulmuş durumdaki sanal makineler](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Bir eğitimci bir öğrenci sanal makinesini açtığında öğrencinin kotası etkilenmez. Bir kullanıcı için kota, kullanıcının zamanlanan sınıf süresi dışında kullanabileceği laboratuvar saatleri sayısını belirtir. Kotalar hakkında daha fazla bilgi için bkz. [kullanıcılar için kotaları ayarlama](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Laboratuvar için zamanlama ayarlama
Laboratuvardaki VM 'Lerin belirli zamanlarda otomatik olarak başlatılması/durdurulması için laboratuvar için zamanlanmış bir olay oluşturun. Daha önce belirttiğiniz Kullanıcı kotası (varsayılan: 10 saat), bu zamanlanan sürenin dışında her bir kullanıcıya atanan ek süredir. 

1. **Zamanlamalar** sayfasına geçin ve araç çubuğunda **Zamanlanmış olay Ekle** ' yi seçin. 

    !["Zamanlamalar" sayfasında "zamanlanmış olay Ekle" düğmesini gösteren ekran görüntüsü.](./media/how-to-create-schedules/add-schedule-button.png)
2. **Zamanlanmış olay Ekle** sayfasında, aşağıdaki adımları uygulayın:
    1. **Standart** **olay türünün** seçili olduğunu onaylayın.  
    2. Sınıf için **başlangıç tarihini** seçin. 
    4. VM 'Lerin **başlamasını istediğiniz başlangıç saatini** seçin.
    5. VM 'Lerin kapatılmakta olduğu **durdurma saatini** seçin. 
    6. Belirttiğiniz başlangıç ve durdurma zamanları için **saat dilimini** seçin. 
3. Aynı **Zamanlanmış olay Ekle** sayfasında, **Yinele** bölümünde geçerli zamanlamayı seçin.  

    ![Zamanlamalar sayfasında zamanlama Ekle düğmesi](./media/how-to-create-schedules/select-current-schedule.png)
5. **Yinele** iletişim kutusunda, aşağıdaki adımları uygulayın:
    1. **Yineleme** alanı için **Her haftanın** ayarlandığını onaylayın. 
    2. Zamanlamanın etkili olmasını istediğiniz günleri seçin. Aşağıdaki örnekte Monday-Friday seçilidir. 
    3. Zamanlama için bir **bitiş tarihi** seçin.
    8. **Kaydet**’i seçin. 

        ![Yineleme zamanlaması ayarla](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Şimdi, **zamanlanan olay Ekle** sayfasında, **notlar için (isteğe bağlı)**, zamanlama için herhangi bir açıklama veya Not girin. 
4. **Zamanlanmış olay Ekle** sayfasında **Kaydet**' i seçin. 

    ![Haftalık zamanlama](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Zamanlamanın ayarlandığını doğrulamak için takvimdeki başlangıç tarihine gidin.
    
    ![Takvimde zamanla](./media/how-to-create-schedules/schedule-calendar.png)

    Bir sınıfa yönelik zamanlamalar oluşturma ve yönetme hakkında daha fazla bilgi için bkz. [Labs için zamanlama oluşturma ve yönetme](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Laboratuvara Kullanıcı ekleme

Kullanıcıları eklediğinizde, varsayılan olarak **erişimi kısıtla** seçeneği açıktır ve kullanıcılar listesinde olmadıkları takdirde, öğrenciler bir kayıt bağlantısı olsa bile laboratuvara kayıt yaptıramazlar. Yalnızca listelenen kullanıcılar, göndereceğiniz kayıt bağlantısını kullanarak laboratuvara kayıt yapabilir. Kayıt bağlantısına sahip oldukları sürece öğrencilerin laboratuvara kaydedilmesine izin veren **erişimi kısıtla** seçeneğini devre dışı bırakabilirsiniz. 

### <a name="add-users-from-an-azure-ad-group"></a>Azure AD grubundan Kullanıcı ekleme

Bir laboratuvar Kullanıcı listesini mevcut bir Azure Active Directory (Azure AD) grubuyla eşitleyebilirsiniz, böylece kullanıcı el ile eklemeniz veya silmeniz gerekmez. 

Kurumsal kaynaklara ve bulut tabanlı uygulamalara erişimi yönetmek için kuruluşunuzun Azure Active Directory içinde bir Azure AD grubu oluşturulabilir. Daha fazla bilgi için bkz. [Azure AD grupları](../active-directory/fundamentals/active-directory-manage-groups.md). Kuruluşunuz Microsoft Office 365 veya Azure hizmetlerini kullanıyorsa, kuruluşunuzda Azure Active Directory yönettiğiniz Yöneticiler zaten olur. 

> [!IMPORTANT]
> Kullanıcı listesinin boş olduğundan emin olun. Laboratuvar içinde el ile eklediğiniz veya bir CSV dosyası içeri aktardığınız mevcut kullanıcılar varsa, Laboratuvarı mevcut bir gruba eşitleme seçeneği görünmez. 

1. Sol bölmede **Kullanıcılar**' ı seçin. 
1. **Gruptan Eşitle**' ye tıklayın. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Bir Azure AD grubundan eşitleme yaparak Kullanıcı ekleme":::
    
1. Laboratuvarınızı ile eşitlemek için mevcut bir Azure AD grubu seçmeniz istenir. 
    
    Listede bir Azure AD grubu görmüyorsanız, aşağıdakilerden biri olabilir:

    -   Bir Azure Active Directory Konuk Kullanıcı (genellikle Azure AD 'nin sahibi olan kuruluşun dışındaysanız) varsa ve Azure AD içinde gruplar arayabileceksiniz. Bu durumda, bu durumda laboratuvara bir Azure AD grubu ekleyemeyeceksiniz. 
    -   Takımlar aracılığıyla oluşturulan Azure AD grupları bu listede gösterilmez. Laboratuvarların içinden doğrudan içinden laboratuvar oluşturmak ve yönetmek için Azure Lab Services uygulama ekleyebilirsiniz. [Bir laboratuvarın Kullanıcı listesini takımlar içinden yönetme](how-to-manage-user-lists-within-teams.md)hakkında daha fazla bilgi için bkz.. 
1. Laboratuvarınızı eşitlemek için Azure AD grubunu girdikten sonra **Ekle**' ye tıklayın.
1. Laboratuvar eşitlendikten sonra, Azure AD grubunun içindeki herkesi Kullanıcı olarak laboratuvara çeker ve kullanıcı listesinin güncelleştirildiğini görürsünüz. Yalnızca bu Azure AD grubundaki kişilerin laboratuvarınızda erişimi olur. Kullanıcı listesi, Azure AD grubunun en son üyeliğiyle eşleşecek şekilde her 24 saatte bir yenilenir. Azure AD grubundaki en son değişikliklerle el ile eşitleme yapmak için kullanıcılar sekmesindeki Eşitle düğmesine de tıklayabilirsiniz.
1. Tüm kullanıcılara, laboratuvara kayıt bağlantısı olan tüm kullanıcılara bir e-posta gönderecek olan **Tümünü davet et** düğmesine tıklayarak kullanıcıları laboratuvara davet edin. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>Kullanıcıları e-postalardan veya CSV dosyasından el ile Ekle

Bu bölümde öğrencileri el ile (e-posta adresine göre veya bir CSV dosyası karşıya yükleyerek) eklersiniz. 

#### <a name="add-users-by-email-address"></a>E-posta adresine göre Kullanıcı ekleme

1. Sol bölmede **Kullanıcılar**' ı seçin. 
1. **Kullanıcıları el Ile Ekle**' ye tıklayın. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="Kullanıcıları el ile ekleme":::
1. **E-posta adresi ekle** ' yi seçin (varsayılan), öğrenciler için e-posta adreslerini ayrı satırlara veya noktalı virgülle ayrılmış tek bir satıra girin. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="Kullanıcıların e-posta adreslerini ekleme":::
1. **Kaydet**’i seçin. 

    Bu liste, geçerli kullanıcıların e-posta adreslerini ve laboratuvarda kayıtlı olup olmadığını gösterir. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Kullanıcı listesi":::

    > [!NOTE]
    > Öğrenciler laboratuvara kaydedildikten sonra liste adlarını görüntüler. Listede gösterilen ad, Azure Active Directory öğrencilerinin ilk ve soyadlarını kullanarak oluşturulur. 

#### <a name="add-users-by-uploading-a-csv-file"></a>CSV dosyası karşıya yükleyerek Kullanıcı ekleme

Ayrıca, e-posta adreslerini içeren bir CSV dosyasını karşıya yükleyerek da kullanıcı ekleyebilirsiniz. 

Bir CSV metin dosyası, virgülle ayrılmış (CSV) tablolu verileri (sayılar ve metin) depolamak için kullanılır. Sütunları sütunlar alanlarında (örneğin, elektronik tablolarda) depolamak yerine, bir CSV dosyası virgüllerle ayrılmış bilgileri depolar. Bir CSV dosyasındaki her satır aynı sayıda virgülle ayrılmış "alan" alanına sahip olur. CSV dosyalarını kolayca oluşturmak ve düzenlemek için Excel 'i kullanabilirsiniz.

1. Microsoft Excel 'de öğrencilerin e-posta adreslerini tek bir sütunda listeleyen bir CSV dosyası oluşturun.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="Bir CSV dosyasındaki kullanıcıların listesi":::
1. **Kullanıcılar** bölmesinin en üstünde **Kullanıcı Ekle**' yı seçin ve ardından **CSV 'yi karşıya yükle**' yi seçin.
1. Öğrencilerin e-posta adreslerini içeren CSV dosyasını seçin ve sonra **Aç**' ı seçin.

    **Kullanıcı Ekle** PENCERESI, CSV dosyasındaki e-posta adresi listesini görüntüler. 
1. **Kaydet**’i seçin. 
1. **Kullanıcılar** bölmesinde, eklenen öğrencilerin listesini görüntüleyin. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Kullanıcılar bölmesinde eklenen kullanıcıların listesi"::: 

## <a name="send-invitation-emails-to-users"></a>Kullanıcılara davet e-postaları gönder

1. Zaten sayfada değilseniz **Kullanıcılar** görünümüne geçin ve araç çubuğunda **Tümünü davet et** ' i seçin. 

    ![Öğrencileri seçin](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. **E-postaya davet gönder** sayfasında, isteğe bağlı bir ileti girin ve ardından **Gönder**' i seçin. E-posta otomatik olarak kayıt bağlantısını içerir. Bu kayıt bağlantısını,... öğesini seçerek alabilirsiniz **. (üç nokta)** araç çubuğunda ve **kayıt bağlantısı**. 

    ![Kayıt bağlantısını e-posta ile gönder](./media/tutorial-setup-classroom-lab/send-email.png)
4. **Kullanıcı** listesindeki **davetin** durumunu görürsünüz. Durum, **tarihinde gönderilmek üzere ve sonrasında** **gönderilmesi &lt; &gt;** için değişmelidir. 

Bir sınıfa öğrenci ekleme ve laboratuvarın kullanımını yönetme hakkında daha fazla bilgi için bkz. [öğrenci kullanımını yapılandırma](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure 'da sınıfınız için bir laboratuvar oluşturdunuz. Bir öğrencinin, kayıt bağlantısını kullanarak laboratuvardaki bir sanal makineye nasıl erişebileceğinizi öğrenmek için sonraki öğreticiye ilerleyin:

> [!div class="nextstepaction"]
> [Sınıf laboratuvarındaki bir sanal makineye bağlanma](tutorial-connect-virtual-machine-classroom-lab.md)