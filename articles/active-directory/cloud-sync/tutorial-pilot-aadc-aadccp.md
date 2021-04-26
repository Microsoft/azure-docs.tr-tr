---
title: Öğretici-mevcut eşitlenmiş bir AD Ormanı için pilot Azure AD Connect bulut eşitlemesi
description: Azure Active Directory (Azure AD) Connect Sync kullanılarak zaten eşitlenmiş olan bir test Active Directory ormanı için nasıl pilot kullanacağınızı öğrenin.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5053bd0c3d63b13b1021476a09dca39dd08f581
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108766"
---
# <a name="pilot-cloud-sync-for-an-existing-synced-ad-forest"></a>Mevcut eşitlenmiş bir AD Ormanı için pilot bulut eşitlemesi 

Bu öğreticide, zaten Azure Active Directory (Azure AD) Connect Sync kullanılarak eşitlenmiş bir test Active Directory ormanı için bulut eşitlemesini adım adım görebilirsiniz.

![Oluştur](media/tutorial-migrate-aadc-aadccp/diagram-2.png)

## <a name="considerations"></a>Dikkat edilmesi gerekenler
Bu öğreticiyi denemeden önce aşağıdaki öğeleri göz önünde bulundurun:
1. Bulut eşitleme hakkında bilgi sahibi olduğunuzdan emin olun. 
2. Azure AD Connect Sync sürüm 1.4.32.0 veya üstünü kullandığınızdan ve eşitleme kurallarını belgelenen şekilde yapılandırdığınızdan emin olun. Ne zaman bir test OU 'su veya grubu Azure AD Connect eşitleme kapsamından kaldıracaksınız. Nesneleri kapsam dışına taşımak, Azure AD 'de bu nesnelerin silinmesine yol açar. Kullanıcı nesneleri söz konusu olduğunda, Azure AD 'deki nesneler geçici olarak silinir ve geri yüklenebilir. Grup nesneleri söz konusu olduğunda, Azure AD 'deki nesneler sabit olarak silinir ve geri yüklenemez. Azure AD Connect eşitlemede yeni bir bağlantı türü tanıtılmıştır, bu, bir pilot senaryo durumunda silmeyi engeller. 
3. Pilot kapsamdaki nesnelerin, bulut eşitlemesi sabit nesneleriyle eşleşen ms-DS-ımı 

   > [!NOTE]
   > Azure AD Connect eşitleme, Grup nesneleri için varsayılan olarak *MS-DS-ımuz GUID 'yi* doldurmaz.

4. Bu gelişmiş bir senaryodur. Bu öğreticide açıklanan adımları tam olarak izlediğinizden emin olun.

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticiyi tamamlamak için gerekli Önkoşullar aşağıda verilmiştir
- Azure AD Connect Sync sürüm 1.4.32.0 veya üzeri bir test ortamı
- Eşitleme kapsamındaki bir OU veya grup ve pilot kullanılabilir. Küçük bir nesne kümesiyle başlamasını öneririz.
- Sağlama aracısını barındıracak Windows Server 2012 R2 veya üstünü çalıştıran bir sunucu.
- Azure AD Connect eşitleme için kaynak bağlantısı *Objectguıd* veya *MS-DS-ımosnguıd* olmalıdır

## <a name="update-azure-ad-connect"></a>Güncelleştirme Azure AD Connect

Minimum olarak, [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0 sahip olmanız gerekir. Azure AD Connect eşitlemesini güncelleştirmek için Azure AD Connect şu adımları uygulayın [: en son sürüme yükseltme](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Zamanlayıcıyı durdur
Azure AD Connect eşitleme, bir Zamanlayıcı kullanarak şirket içi dizininizde gerçekleşen değişiklikleri eşitler. Özel kuralları değiştirmek ve eklemek için, bu Zamanlayıcı 'yı devre dışı bırakmak istiyorsunuz. bu sayede, bu işlem üzerinde çalışırken eşitlemeler çalışmaz.  Aşağıdaki adımları kullanın:

1.  Azure AD Connect çalıştıran sunucuda, açık PowerShell 'i yönetici ayrıcalıklarıyla eşitleyin.
2.  `Stop-ADSyncSyncCycle` öğesini çalıştırın.  ENTER tuşuna basın.
3.  `Set-ADSyncScheduler -SyncCycleEnabled $false` öğesini çalıştırın.

>[!NOTE] 
>Azure AD Connect eşitleme için kendi özel zamanlayıcınız çalıştırıyorsanız, lütfen Zamanlayıcıyı devre dışı bırakın. 

## <a name="create-custom-user-inbound-rule"></a>Özel Kullanıcı gelen kuralı oluştur

 1. Aşağıda gösterildiği gibi, masaüstündeki uygulama menüsünden eşitleme düzenleyicisini başlatın:</br>
 ![Eşitleme kuralı Düzenleyicisi menüsü](media/tutorial-migrate-aadc-aadccp/user-8.png)</br>
 
 2. Yön için açılan listeden **gelen** ' ı seçin ve **Yeni kural ekle**' ye tıklayın.
 !["Gelen" ve "yeni kural ekle" düğmesinin seçili olduğu "eşitleme kurallarınızı görüntüleme ve yönetme" penceresini gösteren ekran görüntüsü.](media/tutorial-migrate-aadc-aadccp/user-1.png)</br>
 
 3. **Açıklama** sayfasında, aşağıdakileri girin ve **İleri**' ye tıklayın:

    **Ad:** Kurala anlamlı bir ad verin<br>
    **Açıklama:** Anlamlı bir açıklama ekleyin<br>
    **Bağlı sistem:** Özel eşitleme kuralı yazarken AD bağlayıcısını seçin<br>
    **Bağlı sistem nesne türü:** Kullanıcısını<br>
    **Meta veri deposu nesne türü:** Kişiler<br>
    **Bağlantı türü:** Ayrılma<br>
    **Öncelik:** Sistemde benzersiz bir değer sağlayın<br>
    **Etiket:** Bunu boş bırakın<br>
    !["Gelen eşitleme kuralı-açıklama oluşturma" sayfasının girilen değerleri gösteren ekran görüntüsü.](media/tutorial-migrate-aadc-aadccp/user-2.png)</br>
 
 4. **Kapsam filtresi** sayfasında, pilot uygulamayı açmak istediğiniz OU veya güvenlik grubunu girin.  OU 'ya filtre uygulamak için, ayırt edici adın OU kısmını ekleyin. Bu kural, o OU 'da bulunan tüm kullanıcılara uygulanır.  Yani, DN "OU = CPUsers, DC = contoso, DC = com" ile sonlanıyorsa, bu filtreyi eklersiniz.  Ardından **İleri**'ye tıklayın. 

    |Kural|Öznitelik|İşleç|Değer|
    |-----|----|----|-----|
    |Kapsamı bulunan OU|DEĞERI|ENDSWITH|OU 'nun ayırt edici adı.|
    |Kapsam grubu||ISMEMBEROF|Güvenlik grubunun ayırt edici adı.|

    ![Kapsam filtresi değeri girilmiş "gelen eşitleme kuralı eşleme Filtresi Oluştur" sayfasını gösteren ekran görüntüsü.](media/tutorial-migrate-aadc-aadccp/user-3.png)</br>
 
 5. Kural **Birleştir** sayfasında, **İleri**' ye tıklayın.
 6. **Dönüşümler** sayfasında, sabit bir dönüşüm ekleyin: cloudNoFlow özniteliğine akış doğru. **Ekle**'ye tıklayın.
 !["Sabit dönüşüm" akışı eklenmiş "gelen eşitleme kuralı-dönüşümler oluşturma" sayfasını gösteren ekran görüntüsü.](media/tutorial-migrate-aadc-aadccp/user-4.png)</br>

Tüm nesne türleri için aynı adımların izlenmesi gerekir (Kullanıcı, Grup ve kişi). Yapılandırılmış AD Bağlayıcısı/AD Ormanı başına adımları yineleyin. 

## <a name="create-custom-user-outbound-rule"></a>Özel Kullanıcı giden kuralı oluştur

 1. Yön için açılan listeden **giden** ' ı seçin ve **Kural Ekle**' ye tıklayın.
 !["Giden" yönünün seçili olduğunu ve "yeni kural ekle" düğmesinin vurgulandığını gösteren ekran görüntüsü.](media/tutorial-migrate-aadc-aadccp/user-5.png)</br>
 
 2. **Açıklama** sayfasında, aşağıdakileri girin ve **İleri**' ye tıklayın:

    **Ad:** Kurala anlamlı bir ad verin<br>
    **Açıklama:** Anlamlı bir açıklama ekleyin<br>
    **Bağlı sistem:** Özel eşitleme kuralı yazarken Azure AD bağlayıcısını seçin<br>
    **Bağlı sistem nesne türü:** Kullanıcısını<br>
    **Meta veri deposu nesne türü:** Kişiler<br>
    **Bağlantı türü:** JoinNoFlow<br>
    **Öncelik:** Sistemde benzersiz bir değer sağlayın<br>
    **Etiket:** Bunu boş bırakın<br>
    
    !["Açıklama" sayfasını, girilen özellikleri gösteren ekran görüntüsü.](media/tutorial-migrate-aadc-aadccp/user-6.png)</br>
 
 3. **Kapsam filtresi** sayfasında **cloudnoflow** eşittir **true**' ı seçin. Ardından **İleri**'ye tıklayın.
 ![Özel kural](media/tutorial-migrate-aadc-aadccp/user-7.png)</br>
 
 4. Kural **Birleştir** sayfasında, **İleri**' ye tıklayın.
 5. **Dönüşümler** sayfasında, **Ekle**' ye tıklayın.

Tüm nesne türleri için aynı adımların izlenmesi gerekir (Kullanıcı, Grup ve kişi).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Azure AD Connect sağlama aracısını yükler
1. Kurumsal Yönetici izinleriyle kullanacağınız sunucuda oturum açın.  [Temel ad ve Azure ortamı](tutorial-basic-ad-azure.md) öğreticisini KULLANıYORSANıZ, CP1 olacaktır.
2. [Burada](how-to-install.md#install-the-agent)özetlenen adımları kullanarak Azure AD Connect bulutu sağlama aracısını indirin.
3. Azure AD Connect bulut eşitlemesini çalıştırın (AADConnectProvisioningAgent. Installer)
3. Giriş ekranında, lisans koşullarını **kabul edin** ve **yükler**' e tıklayın.</br>
!["D Connect sağlama aracısını Microsoft Azure" Giriş ekranını gösteren ekran görüntüsü.](media/how-to-install/install-1.png)</br>

4. Bu işlem tamamlandıktan sonra Yapılandırma Sihirbazı başlatılır.  Azure AD Genel Yönetici hesabınızla oturum açın.
5. **Bağlan Active Directory** ekranında, **Dizin Ekle** ' ye tıklayın ve Active Directory Yönetici hesabınızla oturum açın.  Bu işlem, şirket içi dizininizi ekleyecek.  **İleri**’ye tıklayın.</br>
![Bir dizin değeri girilmiş "Connect Active Directory" ekranını gösteren ekran görüntüsü.](media/how-to-install/install-3a.png)</br>

6. **Yapılandırma Tamam** ekranında **Onayla**' ya tıklayın.  Bu işlem aracıyı kaydedip yeniden başlatacak.</br>
!["Onayla" düğmesinin seçili olduğu "Yapılandırma Tamam" ekranını gösteren ekran görüntüsü.](media/how-to-install/install-4a.png)</br>

7. Bu işlem tamamlandıktan sonra, **başarıyla doğrulandığına** ilişkin bir uyarı görmeniz gerekir.  **Çıkış**' a tıklayabilirsiniz.</br>
![Hoş Geldiniz ekranı](media/how-to-install/install-5.png)</br>
8. İlk giriş ekranını hala görüyorsanız **Kapat**' a tıklayın.

## <a name="verify-agent-installation"></a>Aracı yüklemesini doğrulama
Aracı doğrulaması Azure portal ve aracıyı çalıştıran yerel sunucu üzerinde oluşur.

### <a name="azure-portal-agent-verification"></a>Aracı doğrulama Azure portal
Aracının Azure tarafından görüldüğünü doğrulamak için şu adımları izleyin:

1. Azure portalında oturum açın.
2. Sol tarafta **Azure Active Directory**' ı seçin, **Azure AD Connect** ' a tıklayın ve ardından **bulut eşitlemesini Yönet**' i seçin.</br>
![Azure portalda](media/how-to-install/install-6.png)</br>

3.  **Azure AD Connect bulut eşitleme** ekranında **tüm aracıları gözden geçir**' e tıklayın.
![Azure AD sağlama](media/how-to-install/install-7.png)</br>
 
4. **Şirket içi sağlama aracıları ekranında** , yüklediğiniz aracıları görürsünüz.  Söz konusu aracının orada olduğunu ve **devre dışı** olarak işaretlendiğinden emin olun.  Aracı varsayılan sağlama aracıları tarafından devre dışı bırakıldı ![](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Yerel sunucuda
Aracının çalıştığını doğrulamak için şu adımları izleyin:

1.  Yönetici hesabıyla sunucuda oturum açma
2.  Hizmetlere giderek veya Start/Run/Services. msc ' ye giderek **Hizmetleri** açın.
3.  **Hizmetler** ' in altında **Microsoft Azure AD aracı Güncelleştirici** ' ı bağlama ve **Microsoft Azure AD Connect sağlama aracısının** aynı ve **çalışır** durumda olduğundan emin olun.
![Hizmetler](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Azure AD Connect bulut eşitlemesini yapılandırma
Sağlamayı yapılandırmak için aşağıdaki adımları kullanın:

 1. Azure AD portalında oturum açın.
 2. **Azure Active Directory** tıklayın
 3. **Azure AD Connect** tıklayın
 4.  
  ![ "Bulut eşitlemesini Yönet" bağlantısını gösteren bulut eşitlemesini Yönet ekran görüntüsünü seçin.](media/how-to-configure/manage-1.png)</br>
 5.   
  ![ "Yeni yapılandırma" bağlantısı vurgulanmış şekilde Azure AD Connect bulut eşitleme ekranının yeni yapılandırma ekran görüntüsü ' ne tıklayın.](media/tutorial-single-forest/configure-1.png)</br>
 6.  Yapılandırma ekranında bir **bildirim e-postası** girin, seçiciyi **etkinleştirmek** için taşıyın ve **Kaydet**' e tıklayın.
 ![Bildirim e-postasına sahip ekranı Yapılandır ekranının ekran görüntüsü doldurulmuş ve seçili şekilde etkin.](media/tutorial-single-forest/configure-2.png)</br>
 7. **Yapılandır**' ın altında, yapılandırma kuralının kapsamını değiştirmek için **tüm kullanıcılar** ' ı seçin.
 !["Kapsam kullanıcıları" öğesinin yanında "tüm kullanıcılar" vurgulanmış şekilde yapılandırma ekranının ekran görüntüsü.](media/how-to-configure/scope-2.png)</br>
 8. Sağ tarafta, yeni oluşturduğunuz kuruluş birimini (OU = CPUsers, DC = contoso, DC = com ") içerecek şekilde kapsamını değiştirin.
 ![Kapsamını vurgulayan kapsam kullanıcıları ekranının ekran görüntüsü oluşturduğunuz OU 'ya değişti.](media/tutorial-existing-forest/scope-2.png)</br>
 9.  **Bitti** ve **Kaydet**' e tıklayın.
 10. Kapsam artık tek bir kuruluş birimine ayarlanmalıdır. 
 !["Kapsam kullanıcıları" yanında vurgulanan "1 kuruluş birimi" ile yapılandırma ekranının ekran görüntüsü.](media/tutorial-existing-forest/scope-3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-sync"></a>Kullanıcıların bulut eşitlemesi tarafından sağlandığını doğrulama
Artık şirket içi dizinimizde bulunan kullanıcıların eşitlenmiş olduğunu ve artık Azure AD kiracısında bulunduğunu doğrulayacaksınız.  Bu işlem işleminin tamamlanması birkaç saat sürebilir.  Kullanıcıların bulut eşitlemesine göre sağlamasını doğrulamak için şu adımları izleyin:

1. [Azure portalına](https://portal.azure.com) gidip Azure aboneliği olan bir hesapla oturum açın.
2. Sol tarafta **Azure Active Directory** ' yi seçin.
3. **Azure AD Connect** tıklayın
4. **Bulut eşitlemesini Yönet** 'e tıklayın
5. **Günlükler** düğmesine tıklayın
6. Kullanıcının bulut eşitlemesine göre sağlandığını doğrulamak için bir Kullanıcı adı arayın

Ayrıca, Azure AD 'de Kullanıcı ve grubun mevcut olduğunu doğrulayabilirsiniz.

## <a name="start-the-scheduler"></a>Zamanlayıcıyı başlatma
Azure AD Connect eşitleme, bir Zamanlayıcı kullanarak şirket içi dizininizde gerçekleşen değişiklikleri eşitler. Artık kuralları değiştirdiğimize göre, Scheduler 'ı yeniden başlatabilirsiniz.  Aşağıdaki adımları kullanın:

1.  Azure AD Connect çalıştıran sunucuda, açık PowerShell 'i yönetici ayrıcalıklarıyla eşitleyin
2.  `Set-ADSyncScheduler -SyncCycleEnabled $true` öğesini çalıştırın.
3.  `Start-ADSyncSyncCycle` öğesini çalıştırın.  ENTER tuşuna basın.  

>[!NOTE] 
>Azure AD Connect eşitleme için kendi özel zamanlayıcınız çalıştırıyorsanız lütfen zamanlayıcıyı etkinleştirin. 

Zamanlayıcı etkinleştirildikten sonra, `cloudNoFlow=true` herhangi bir başvuru özniteliği (örn.) dışında, meta veri deposundaki nesnelerde bulunan tüm değişiklikleri dışarı aktarmayı durdurur Azure AD Connect. Yönetici) güncelleştiriliyor. Nesnede herhangi bir başvuru özniteliği güncelleştirmesi varsa, Azure AD Connect `cloudNoFlow` sinyali yoksayacak ve nesnedeki tüm güncelleştirmeleri dışarı aktaracaktır.

## <a name="something-went-wrong"></a>Bir sorun oluştu
Pilot 'ın beklendiği gibi çalışmayolmaması durumunda aşağıdaki adımları izleyerek Azure AD Connect eşitleme kurulumuna geri dönebilirsiniz:
1.  Azure portal sağlama yapılandırmasını devre dışı bırakın. 
2.  Eşitleme kuralı Düzenleyicisi aracını kullanarak bulut sağlaması için oluşturulan tüm özel eşitleme kurallarını devre dışı bırakın. Devre dışı bırakma, tüm bağlayıcılarda tam eşitlemeye neden olmalıdır.



## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)

