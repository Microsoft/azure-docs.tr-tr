---
title: 'Azure AD Connect eşitleme: filtrelemeyi yapılandırma | Microsoft Docs'
description: Azure AD Connect eşitlemede filtrelemenin nasıl yapılandırılacağını açıklar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595cf2c1dbc105634d33b426c67e5123b9751e6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996551"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect Eşitleme: Filtrelemeyi yapılandırma
Filtreleme kullanarak, şirket içi dizininizden Azure Active Directory (Azure AD) içinde hangi nesnelerin göründüğünü denetleyebilirsiniz. Varsayılan yapılandırma, yapılandırılan ormanlardaki tüm etki alanlarındaki tüm nesneleri alır. Genel olarak, önerilen yapılandırmadır. Exchange Online ve Skype Kurumsal gibi Microsoft 365 iş yüklerini kullanan kullanıcılar, e-posta gönderebilmeleri ve herkes arayabilmesi için tüm genel adres listesinden faydalanır. Varsayılan yapılandırmayla, Exchange veya Lync 'in şirket içi uygulamasıyla aynı deneyim yaşar.

Ancak bazı durumlarda, varsayılan yapılandırmada bazı değişiklikler yapmanız gerekir. İşte bazı örnekler:

* [Çoklu Azure AD dizin topolojisini](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant)kullanmayı planlıyorsunuz. Ardından belirli bir Azure AD dizini ile eşitlenecek nesneleri denetlemek için bir filtre uygulamanız gerekir.
* Azure veya Microsoft 365 için bir pilot çalıştırırsınız ve yalnızca Azure AD 'deki bir kullanıcı alt kümesini istiyorsunuz. Küçük pilot sürümünde, işlevselliği göstermek için bir genel adres listesinin tamamen olması önemli değildir.
* Azure AD 'de istemediğiniz birçok hizmet hesabınız ve diğer kişisel hesabınız vardır.
* Uyumluluk nedenleriyle, şirket içi kullanıcı hesaplarını silmeyin. Yalnızca bunları devre dışı bırakabilirsiniz. Ancak Azure AD 'de yalnızca etkin hesapların bulunmasını istiyorsunuz.

Bu makalede, farklı filtreleme yöntemlerinin nasıl yapılandırılacağı ele alınmaktadır.

> [!IMPORTANT]
> Microsoft, resmi olarak belgelenen eylemler dışında Azure AD Connect eşitlemesinin değiştirilmesini veya çalıştırılmasını desteklemez. Bu eylemlerden herhangi biri tutarsız veya Azure AD Connect eşitlemeye neden olabilecek bir durum oluşabilir. Sonuç olarak, Microsoft bu tür dağıtımlar için teknik destek sağlayamaz.

## <a name="basics-and-important-notes"></a>Temel bilgiler ve önemli notlar
Azure AD Connect eşitleme ' de istediğiniz zaman filtrelemeyi etkinleştirebilirsiniz. Dizin eşitleme 'nin varsayılan yapılandırmasıyla başlayıp sonra filtrelemeyi yapılandırırsanız, filtrelenen nesneler artık Azure AD ile eşitlenmez. Bu değişiklik nedeniyle, Azure AD 'de daha önce eşitlenmiş ancak daha sonra filtrelenen tüm nesneler Azure AD 'de silinir.

Filtrelemeye yönelik değişiklikler yapmaya başlamadan önce, doğru bir şekilde doğrulanmadığınız değişiklikleri yanlışlıkla dışarı aktarmadığınız için, [Zamanlanmış görevi devre dışı bıraktığınızdan](#disable-the-scheduled-task) emin olun.

Filtreleme birçok nesneyi aynı anda kaldırabildiğinden, Azure AD 'de herhangi bir değişikliği dışarı aktarmaya başlamadan önce yeni filtrelerinizin doğru olduğundan emin olmak istersiniz. Yapılandırma adımlarını tamamladıktan sonra, Azure AD 'ye dışarı ve değişiklik yapmadan önce [doğrulama adımlarını](#apply-and-verify-changes) izlemenizi önemle öneririz.

Yanlışlıkla birçok nesneyi silmenizi engellemek için, "[yanlışlıkla Silmeleri engelle](how-to-connect-sync-feature-prevent-accidental-deletes.md)" özelliği varsayılan olarak açık olur. Filtreleme nedeniyle birçok nesneyi silerseniz (varsayılan olarak 500), silmenin Azure AD 'ye gitmesini sağlamak için bu makaledeki adımları izlemeniz gerekir.

Kasım 2015 ' den ([1.0.9125](reference-connect-version-history.md)) önce bir yapı kullanırsanız, filtre yapılandırmasında bir değişiklik yapın ve parola karma eşitlemesini kullanın, yapılandırmayı tamamladıktan sonra tüm parolaların tam eşitlemesini tetiklemeniz gerekir. Parola tam eşitlemesini tetiklemenin adımları için, bkz. [tüm parolaların tam eşitlemesini tetikleme](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Build 1.0.9125 veya üzeri bir sürümü kullanıyorsanız, normal **tam eşitleme** eylemi, parolaların eşitlenip eşitlenmeyeceğini ve bu ek adımın artık gerekli olmadığını da hesaplar.

Bir filtreleme hatası nedeniyle **Kullanıcı** nesneleri YANLıŞLıKLA Azure AD 'de silinirse, filtreleme yapılandırmalarınızı KALDıRARAK Azure AD 'de Kullanıcı nesnelerini yeniden oluşturabilirsiniz. Ardından dizinlerinizi yeniden eşitlemeniz gerekir. Bu eylem, kullanıcıları Azure AD 'deki geri dönüşüm kutusu 'ndan geri yükler. Ancak, diğer nesne türlerini geri alamazsınız. Örneğin, yanlışlıkla bir güvenlik grubunu silerseniz ve bir kaynak ACL 'SI için kullanıldıysa, Grup ve ACL 'Leri kurtarılamaz.

Azure AD Connect, yalnızca kapsam içinde olduğu düşünülen nesneleri siler. Azure AD 'de başka bir eşitleme altyapısı tarafından oluşturulan nesneler varsa ve bu nesneler kapsamda değilse, filtreleme eklemek bunları kaldırmaz. Örneğin, Azure AD 'de tüm dizininizin tam bir kopyasını oluşturan bir DirSync sunucusuyla başlayabilir ve yeni bir Azure AD Connect eşitleme sunucusunu, baştan başlayarak filtreleme etkinken paralel olarak yüklerseniz, Azure AD Connect DirSync tarafından oluşturulan ek nesneleri kaldırmaz.

Azure AD Connect daha yeni bir sürümünü yüklediğinizde veya yükselttiğinizde filtreleme yapılandırması korunur. İlk eşitleme döngüsünü çalıştırmadan önce daha yeni bir sürüme yükselttikten sonra yapılandırmanın yanlışlıkla değiştirilmediğini doğrulamak her zaman en iyi uygulamadır.

Birden fazla ormanınız varsa, bu konuda açıklanan filtreleme yapılandırmalarını her ormana uygulamanız gerekir (bunların hepsi için aynı yapılandırmayı istediğinizi varsayarak).

### <a name="disable-the-scheduled-task"></a>Zamanlanan görevi devre dışı bırak
Her 30 dakikada bir eşitleme döngüsünü tetikleyen yerleşik zamanlayıcıyı devre dışı bırakmak için şu adımları izleyin:

1. Bir PowerShell istemine gidin.
2. `Set-ADSyncScheduler -SyncCycleEnabled $False`Zamanlayıcıyı devre dışı bırakmak için ' i çalıştırın.
3. Bu makalede belgelenen değişiklikleri yapın.
4. `Set-ADSyncScheduler -SyncCycleEnabled $True`Zamanlayıcıyı yeniden etkinleştirmek için ' i çalıştırın.

**1.1.105.0 önce bir Azure AD Connect derlemesi kullanıyorsanız**  
Her üç saatte bir eşitleme döngüsünü tetikleyen zamanlanmış görevi devre dışı bırakmak için şu adımları izleyin:

1. **Başlangıç** menüsünden **Görev Zamanlayıcı** başlatın.
2. Doğrudan **Görev Zamanlayıcı Kitaplığı** altında, **Azure AD eşitleme Zamanlayıcı** adlı görevi bulun, sağ tıklayın ve **devre dışı bırak**' ı seçin.  
   ![Görev Zamanlayıcı](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Artık yapılandırma değişiklikleri yapabilir ve eşitleme altyapısını **Synchronization Service Manager** konsolundan el ile çalıştırabilirsiniz.

Tüm filtreleme Değişikliklerinizi tamamladıktan sonra, geri dönüp görevi yeniden **etkinleştirmeyi** unutmayın.

## <a name="filtering-options"></a>Filtreleme seçenekleri
Dizin eşitleme aracına aşağıdaki filtreleme yapılandırma türlerini uygulayabilirsiniz:

* [**Grup tabanlı**](#group-based-filtering): tek bir gruba göre filtreleme, yalnızca Yükleme Sihirbazı kullanılarak ilk yüklemede yapılandırılabilir.
* [**Etki alanı tabanlı**](#domain-based-filtering): Bu seçeneği kullanarak, Azure AD 'ye hangi etki alanlarının eşitleneceğini seçebilirsiniz. Ayrıca, Azure AD Connect eşitleme ' i yükledikten sonra şirket içi altyapınızda değişiklik yaptığınızda, eşitleme altyapısı yapılandırmasına etki alanları ekleyebilir ve kaldırabilirsiniz.
* [**Kuruluş birimi (OU) – tabanlı**](#organizational-unitbased-filtering): Bu seçeneği kullanarak, hangi OU 'LARıN Azure AD ile eşitleneceğini seçebilirsiniz. Bu seçenek, seçilen OU 'Larda tüm nesne türlerine yöneliktir.
* [**Öznitelik tabanlı**](#attribute-based-filtering): Bu seçeneği kullanarak nesneleri nesnelerdeki öznitelik değerlerine göre filtreleyebilirsiniz. Farklı nesne türleri için farklı filtrelere de sahip olabilirsiniz.

Birden çok filtreleme seçeneğini aynı anda kullanabilirsiniz. Örneğin, yalnızca bir OU 'daki nesneleri dahil etmek için OU tabanlı filtrelemeyi kullanabilirsiniz. Aynı zamanda, nesneleri daha fazla filtrelemek için öznitelik tabanlı filtrelemeyi de kullanabilirsiniz. Birden çok filtreleme yöntemi kullandığınızda, filtreler filtreler arasında bir mantıksal "ve" kullanır.

## <a name="domain-based-filtering"></a>Etki alanı tabanlı filtreleme
Bu bölümde, etki alanı Filtrenizi yapılandırma adımları sağlanmaktadır. Azure AD Connect yükledikten sonra ormanınızdaki etki alanlarını eklediyseniz veya kaldırdıysanız, filtreleme yapılandırmasını da güncelleştirmeniz gerekir.

Etki alanı tabanlı filtrelemeyi değiştirmenin tercih edilen yolu, Yükleme Sihirbazı 'nı çalıştırıp [etki alanı ve OU filtrelemesini](how-to-connect-install-custom.md#domain-and-ou-filtering)değiştirirken olur. Yükleme Sihirbazı, bu konuda belgelenen tüm görevleri otomatikleştirir.

Yalnızca bir nedenden dolayı Yükleme Sihirbazı 'nı çalıştıramanızın ardından bu adımları izlemeniz gerekir.

Etki alanı tabanlı filtreleme yapılandırması aşağıdaki adımlardan oluşur:

1. Eşitlemeye eklemek istediğiniz etki alanlarını seçin.
2. Eklenen ve kaldırılan her etki alanı için çalıştırma profillerini ayarlayın.
3. [Değişiklikleri uygulayın ve doğrulayın](#apply-and-verify-changes).

### <a name="select-the-domains-to-be-synchronized"></a>Eşitlenecek etki alanlarını seçin
Eşitlenecek etki alanlarını seçmek için iki yol vardır:
    - Eşitleme hizmetini kullanma
    - Azure AD Connect Sihirbazı 'nı kullanma.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Eşitleme hizmeti kullanılarak eşitlenecek etki alanlarını seçin
Etki alanı filtresini ayarlamak için aşağıdaki adımları uygulayın:

1. Azure AD Connect çalıştıran sunucuda, **Adsyncadmins** güvenlik grubunun üyesi olan bir hesap kullanarak oturum açın.
2. **Başlangıç** menüsünden **eşitleme hizmetini** başlatın.
3. **Bağlayıcılar**' ı seçin ve **Bağlayıcılar** listesinden **Active Directory Domain Services** türü ile bağlayıcıyı seçin. **Eylemler**' de **Özellikler**' i seçin.  
   ![Bağlayıcı Özellikleri](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. **Dizin bölümlerini Yapılandır** öğesine tıklayın.
5. **Dizin bölümlerini Seç** listesinde, gerektiğinde etki alanlarını seçin ve seçimini kaldırın. Yalnızca eşitlenmesini istediğiniz bölümlerin seçildiğini doğrulayın.  
   !["Özellikler" penceresindeki dizin bölümlerini gösteren ekran görüntüsü.](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Şirket içi Active Directory altyapınızı değiştirdiyseniz ve etki alanlarını ormandan eklediyseniz veya kaldırdıysanız, güncelleştirilmiş bir liste almak için **Yenile** düğmesine tıklayın. Yenileme yaptığınızda kimlik bilgileriniz istenir. Windows Server Active Directory için okuma erişimi olan tüm kimlik bilgilerini sağlayın. İletişim kutusunda önceden doldurulmuş Kullanıcı olması gerekmez.  
   ![Yenileme gerekiyor](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. İşiniz bittiğinde, **Tamam**' a tıklayarak **Özellikler** iletişim kutusunu kapatın. Etki alanlarını ormandan kaldırdıysanız, bir ileti açılır penceresi bir etki alanının kaldırıldığını ve yapılandırmanın temizlendiğini söyler.
7. Çalıştırma profillerini ayarlamaya devam edin.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Azure AD Connect Sihirbazı 'nı kullanarak eşitlenecek etki alanlarını seçin
Etki alanı filtresini ayarlamak için aşağıdaki adımları uygulayın:

1.  Azure AD Connect Sihirbazı 'nı başlatın
2.  **Yapılandır**'a tıklayın.
3.  **Eşitleme seçeneklerini Özelleştir** ' i seçin ve **İleri**' ye tıklayın.
4.  Azure AD kimlik bilgilerinizi girin
5.  **Bağlı dizinler** ekranında **İleri**' ye tıklayın.
6.  **Etki alanı ve OU filtreleme sayfasında** **Yenile**' ye tıklayın.  Yeni etki alanları görüntülenir ve silinen etki alanları kaybolacaktır.
   ![Bölümler](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Çalıştırma profillerini güncelleştirme
Etki alanı filtrenizi güncelleştirdiyseniz, çalıştırma profillerini de güncelleştirmeniz gerekir.

1. **Bağlayıcılar** listesinde, önceki adımda değiştirdiğiniz bağlayıcının seçili olduğundan emin olun. **Eylemler**' de, **çalıştırma profillerini Yapılandır**' ı seçin.  
   ![Bağlayıcı çalıştırma profilleri 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Aşağıdaki profilleri bulun ve tanımla:
    * Tam İçeri Aktarma
    * Tam Eşitleme
    * Delta İçeri Aktarma
    * Delta Eşitleme
    * Dışarı Aktarma
3. Her profil için **eklenen** ve **kaldırılan** etki alanlarını ayarlayın.
    1. Her beş profilin her biri için, **eklenen** her etki alanı için aşağıdaki adımları uygulayın:
        1. Çalıştır profilini seçin ve **yeni adım**' a tıklayın.
        2. **Adım Yapılandır** sayfasındaki **tür** açılan menüsünde, yapılandırmakta olduğunuz profille aynı ada sahip adım türünü seçin. Ardından **İleri**'ye tıklayın.  
        ![Bağlayıcı çalıştırma profilleri 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. **Bağlayıcı yapılandırma** sayfasında, **bölüm** açılan menüsünde, etki alanı Filtrenizle eklemiş olduğunuz etki alanının adını seçin.  
        ![Bağlayıcı çalıştırma profilleri 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. **Çalıştırma profilini Yapılandır** iletişim kutusunu kapatmak için **son**' a tıklayın.
    2. Her beş profilin her biri için, **kaldırılan** her etki alanı için aşağıdaki adımları uygulayın:
        1. Çalıştırma profilini seçin.
        2. **Bölüm** özniteliğinin **değeri** bir GUID ise, Çalıştır adımını seçin ve **adımı sil**' e tıklayın.  
        ![Bağlayıcı çalıştırma profilleri 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Değişiklerinizi doğrulayın. Eşitlenmesini istediğiniz her etki alanı, her çalıştırma profilinde bir adım olarak listelenmelidir.
4. **Çalıştırma profillerini Yapılandır** iletişim kutusunu kapatmak için **Tamam**' ı tıklatın.
5.  Yapılandırmayı gerçekleştirmek için bir **tam içeri aktarma** ve bir **Delta eşitleme** çalıştırmanız gerekir. Bölüm uygulama ' yı okumaya devam edin [ve değişiklikleri doğrulayın](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Kuruluş birimi tabanlı filtreleme
OU tabanlı filtrelemeyi değiştirmenin tercih edilen yolu, Yükleme Sihirbazı 'nı çalıştırıp [etki alanı ve OU filtrelemesini](how-to-connect-install-custom.md#domain-and-ou-filtering)değiştirirken olur. Yükleme Sihirbazı, bu konuda belgelenen tüm görevleri otomatikleştirir.

Yalnızca bir nedenden dolayı Yükleme Sihirbazı 'nı çalıştıramanızın ardından bu adımları izlemeniz gerekir.

Kuruluş birimi tabanlı filtreleme yapılandırmak için aşağıdaki adımları uygulayın:

1. Azure AD Connect çalıştıran sunucuda, **Adsyncadmins** güvenlik grubunun üyesi olan bir hesap kullanarak oturum açın.
2. **Başlangıç** menüsünden **eşitleme hizmetini** başlatın.
3. **Bağlayıcılar**' ı seçin ve **Bağlayıcılar** listesinden **Active Directory Domain Services** türü ile bağlayıcıyı seçin. **Eylemler**' de **Özellikler**' i seçin.  
   ![Bağlayıcı Özellikleri](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. **Dizin bölümlerini Yapılandır**' a tıklayın, yapılandırmak istediğiniz etki alanını seçin ve ardından **kapsayıcılar**' a tıklayın.
5. İstendiğinde, şirket içi Active Directory okuma erişimi olan tüm kimlik bilgilerini sağlayın. İletişim kutusunda önceden doldurulmuş Kullanıcı olması gerekmez.
6. **Kapsayıcıları Seç** iletişim kutusunda, bulut diziniyle eşitlenmesini Istemediğiniz OU 'ları temizleyin ve ardından **Tamam**' a tıklayın.  
   ![Kapsayıcılar Seç iletişim kutusunda OU 'Lar](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * Windows 10 bilgisayarlarınızın Azure AD ile başarıyla eşitlenmesi için **bilgisayarlar** kapsayıcısı seçilmelidir. Etki alanına katılmış bilgisayarlarınız başka OU 'Larda bulunuyorsa, bunların seçili olduğundan emin olun.
   * Güvenler içeren birden çok ormanınız varsa, **ForeignSecurityPrincipals** kapsayıcısı seçili olmalıdır. Bu kapsayıcı, ormanlar arası güvenlik grubu üyeliğinin çözümlenebilmesine olanak tanır.
   * Cihaz geri yazma özelliğini etkinleştirdiyseniz, **RegisteredDevices** OU seçilmelidir. Grup geri yazma gibi başka bir geri yazma özelliği kullanıyorsanız, bu konumların seçili olduğundan emin olun.
   * Kullanıcılar, iNetOrgPersons, gruplar, kişiler ve bilgisayarların bulunduğu diğer bir OU 'yu seçin. Resimde, tüm bu OU 'Lar ManagedObjects OU 'da bulunur.
   * Grup tabanlı filtreleme kullanıyorsanız, grubun bulunduğu OU 'nun dahil olması gerekir.
   * Filtreleme yapılandırması tamamlandıktan sonra eklenen yeni OU 'Ların eşitlenip eşitlenmemesini veya eşitlenmemesini yapılandırabileceğinizi unutmayın. Ayrıntılar için sonraki bölüme bakın.
7. İşiniz bittiğinde, **Tamam**' a tıklayarak **Özellikler** iletişim kutusunu kapatın.
8. Yapılandırmayı gerçekleştirmek için bir **tam içeri aktarma** ve bir **Delta eşitleme** çalıştırmanız gerekir. Bölüm uygulama ' yı okumaya devam edin [ve değişiklikleri doğrulayın](#apply-and-verify-changes).

### <a name="synchronize-new-ous"></a>Yeni OU 'Ları eşitler
Filtrelemeden sonra oluşturulan yeni OU 'Lar varsayılan olarak eşitlenir. Bu durum seçili bir onay kutusuyla belirtilir. Ayrıca bazı alt OU 'Ların seçimini kaldırabilirsiniz. Bu davranışı almak için, mavi onay işaretiyle beyaz olana kadar kutuya tıklayın (varsayılan durumu). Ardından, eşitlenmesini istemediğiniz tüm alt OU 'Ların seçimini kaldırın.

Tüm alt OU 'Lar eşitlenirse, kutu mavi onay işaretiyle beyaz olur.  
![Tüm kutuları seçili olan OU](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Bazı alt OU 'Lar seçilmemiş ise, kutu beyaz onay işaretiyle gri olur.  
![Bazı alt OU 'Lar seçili olmayan OU](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Bu yapılandırmayla, ManagedObjects altında oluşturulan yeni bir OU eşitlenir.

Azure AD Connect Yükleme Sihirbazı her zaman bu yapılandırmayı oluşturur.

### <a name="dont-synchronize-new-ous"></a>Yeni OU 'Ları eşitlememe
Eşitleme altyapısını, filtreleme yapılandırması tamamlandıktan sonra yeni OU 'Ları eşitlenmeyecek şekilde yapılandırabilirsiniz. Bu durum, Kullanıcı arabiriminde, hiçbir onay işareti olmadan düz gri görüntülenen Box tarafından gösterilir. Bu davranışı almak için onay işareti olmadan beyaz olana kadar kutuya tıklayın. Ardından, eşitlenmesini istediğiniz alt OU 'Ları seçin.

![Kökü seçili olmayan OU](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Bu yapılandırmayla, ManagedObjects altında oluşturulan yeni bir OU eşitlenmez.

## <a name="attribute-based-filtering"></a>Öznitelik tabanlı filtreleme
Bu adımların çalışması için Kasım 2015 ([1.0.9125](reference-connect-version-history.md)) veya sonraki bir derlemeyi kullandığınızdan emin olun.

> [!IMPORTANT]
>Microsoft, **Azure AD Connect** tarafından oluşturulan varsayılan kuralları değiştirmemesini önerir. Kuralı değiştirmek istiyorsanız klonlayın ve özgün kuralı devre dışı bırakın. Kopyalanmış kuralda herhangi bir değişiklik yapın. Lütfen bunu yaparak (özgün kuralı devre dışı bırakarak), bu kural aracılığıyla etkinleştirilen tüm hata düzeltmeleri veya özellikleri kaçırılacaksınız.

Öznitelik tabanlı filtreleme, nesneleri filtrelemek için en esnek yoldur. Bir nesne Azure AD ile eşitlendiğinde neredeyse her yönüyle denetlemek için [bildirim temelli sağlama](concept-azure-ad-connect-sync-declarative-provisioning.md) gücünden yararlanabilirsiniz.

Active Directory meta veri deposuna [gelen](#inbound-filtering) filtreleme ve meta veri deposundaki [giden](#outbound-filtering) filtreleme işlemlerini Azure AD 'ye uygulayabilirsiniz. Bakım en kolay olduğu için gelen filtrelemeleri uygulamanızı öneririz. Yalnızca değerlendirme gerçekleşmeden önce birden fazla ormandan nesne katılması gerekiyorsa giden filtrelemeyi kullanmanız gerekir.

### <a name="inbound-filtering"></a>Gelen filtreleme
Gelen filtreleme varsayılan yapılandırmayı kullanır, burada Azure AD 'ye giden nesnelerde Cloudfıltered meta veri deposu özniteliği, eşitlenecek bir değer olarak ayarlanmamış olmalıdır. Bu özniteliğin değeri **true** olarak ayarlandıysa, nesne eşitlenmez. Tasarıma göre **false** olarak ayarlanmamalıdır. Diğer kuralların bir değer katkıda bulunma yeteneğine sahip olduğundan emin olmak için, bu özniteliğin yalnızca **true** veya **null** (yok) değerlerinin olması gerekir.

Gelen filtrelemede, hangi nesnelerin eşitleneceğini veya eşitleneceğini öğrenmek için **kapsam** gücünü kullanırsınız. Bu, kendi kuruluşunuzun gereksinimlerini karşılayacak şekilde ayarlamalar yaparsınız. Kapsam modülünde bir eşitleme kuralının kapsam içinde olduğu zaman saptanıp bir **grubu** ve **yan tümcesi** vardır. Bir grup bir veya daha fazla yan tümce içerir. Birden çok yan tümce arasında mantıksal bir "ve", birden çok grup arasında mantıksal bir "veya" vardır.

Bir örneğe bakmamıza izin verin:  
![Kapsam filtresi ekleme örneği gösteren ekran görüntüsü.](./media/how-to-connect-sync-configure-filtering/scope.png)  
Bu, **(departman = It) veya (departman = Sales ve c = US)** olarak okunmalıdır.

Aşağıdaki örneklerde ve adımlarda, Kullanıcı nesnesini bir örnek olarak kullanırsınız, ancak bunu tüm nesne türleri için kullanabilirsiniz.

Aşağıdaki örneklerde, öncelik değeri 50 ile başlar. Bu, kullanılmayan herhangi bir sayı olabilir, ancak 100 ' den düşük olmalıdır.

#### <a name="negative-filtering-do-not-sync-these"></a>Negatif filtreleme: "bunları eşitleme"
Aşağıdaki örnekte, **extensionAttribute15** ' nin **NoSync** değerine sahip olduğu tüm kullanıcıları filtreleyerek (eşitlememez) filtreleyebilirsiniz.

1. Azure AD Connect çalıştıran sunucuda, **Adsyncadmins** güvenlik grubunun üyesi olan bir hesap kullanarak oturum açın.
2. **Başlangıç** menüsünden **eşitleme kuralları düzenleyicisini** başlatın.
3. **Gelen** ' ın seçili olduğundan emin olun ve **Yeni kural ekle**' ye tıklayın.
4. Kurala "*ad – Kullanıcı DoNotSyncFilter*" gibi açıklayıcı bir ad verin. Doğru ormanı seçin, **CS nesne türü** olarak **Kullanıcı** ' yı seçin ve **MV nesne türü** olarak **kişi** ' yi seçin. **Bağlantı türü**' nde, **Birleştir**' i seçin. **Öncelik** alanına, şu anda başka bir eşitleme kuralı tarafından kullanılmayan bir değer yazın (örneğin, 50) ve ardından **İleri**' ye tıklayın.  
   ![Gelen 1 açıklaması](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. **Kapsam filtresi** bölümünde **Grup Ekle**' ye tıklayın ve **yan tümce Ekle**' ye tıklayın. **Özniteliğinde**, **ExtensionAttribute15** öğesini seçin. **Işlecin** **eşit** olarak ayarlandığından emin olun ve **değer** kutusuna **NoSync** değerini yazın. **İleri**’ye tıklayın.  
   ![Gelen 2 kapsamı](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. **JOIN** kurallarını boş bırakın ve **İleri**' ye tıklayın.
7. **Dönüşüm Ekle**' ye tıklayın, **akış türünü** **sabit** olarak seçin ve **hedef öznitelik** olarak **cloudfıltered** ' i seçin. **Kaynak** metin kutusuna **true** yazın. Kuralı kaydetmek için **Ekle** ' ye tıklayın.  
   ![Gelen 3 dönüşümü](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Yapılandırmayı gerçekleştirmek için **tam eşitleme** çalıştırmanız gerekir. Bölüm uygulama ' yı okumaya devam edin [ve değişiklikleri doğrulayın](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Pozitif filtreleme: "yalnızca bunları Eşitle"
Ayrıca, konferans odaları gibi daha belirgin olmayan nesneleri göz önünde bulundurmanız gerektiğinden, pozitif filtrelemeyi ifade etmek daha zor olabilir. Ayrıca, **ad-Kullanıcı birleşimden** gelen kutudan çıkar kuralında varsayılan filtreyi geçersiz kılacağız. Özel filtrenizi oluştururken, kritik sistem nesneleri, çoğaltma çakışma nesneleri, özel posta kutuları ve Azure AD Connect için hizmet hesaplarını eklediğinizden emin olun.

Pozitif filtreleme seçeneği iki eşitleme kuralı gerektirir. Eşitlenmesi gereken nesnelerin doğru kapsamına sahip bir kurala (veya birkaç) sahip olmanız gerekir. Ayrıca, henüz eşitlenmesi gereken bir nesne olarak tanımlanmayan tüm nesneleri filtreleyen ikinci bir catch-all eşitleme kuralına ihtiyacınız vardır.

Aşağıdaki örnekte, yalnızca bölüm özniteliğinin **Sales** değerine sahip olduğu kullanıcı nesnelerini eşitlemelisiniz.

1. Azure AD Connect çalıştıran sunucuda, **Adsyncadmins** güvenlik grubunun üyesi olan bir hesap kullanarak oturum açın.
2. **Başlangıç** menüsünden **eşitleme kuralları düzenleyicisini** başlatın.
3. **Gelen** ' ın seçili olduğundan emin olun ve **Yeni kural ekle**' ye tıklayın.
4. Kurala "*ad – Kullanıcı satışları eşitlemesi*" gibi açıklayıcı bir ad verin. Doğru ormanı seçin, **CS nesne türü** olarak **Kullanıcı** ' yı seçin ve **MV nesne türü** olarak **kişi** ' yi seçin. **Bağlantı türü**' nde, **Birleştir**' i seçin. **Öncelik** alanına, şu anda başka bir eşitleme kuralı tarafından kullanılmayan bir değer yazın (örneğin, 51) ve ardından **İleri**' ye tıklayın.  
   ![Gelen 4 açıklaması](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. **Kapsam filtresi** bölümünde **Grup Ekle**' ye tıklayın ve **yan tümce Ekle**' ye tıklayın. **Öznitelikte** **Departman**' ı seçin. Işlecin **eşit** olarak ayarlandığından emin olun ve **değer** kutusuna **Sales** değerini yazın. **İleri**’ye tıklayın.  
   ![Gelen 5 kapsam](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. **JOIN** kurallarını boş bırakın ve **İleri**' ye tıklayın.
7. **Dönüştürme Ekle**' ye tıklayın, **FlowType** olarak **sabit** ' i seçin ve **hedef öznitelik** olarak **filtrelenmiş cloudselect** öğesini seçin. **Kaynak** kutusuna **false** yazın. Kuralı kaydetmek için **Ekle** ' ye tıklayın.  
   ![Gelen 6 dönüşümü](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Bu, Cloudfiltrelenmiş öğesini **yanlış** olarak ayarlamış olduğunuz özel bir durumdur.
8. Şimdi catch-all eşitleme kuralını oluşturacağız. Kurala "*ad – Kullanıcı catch-all Filter*" gibi açıklayıcı bir ad verin. Doğru ormanı seçin, **CS nesne türü** olarak **Kullanıcı** ' yı seçin ve **MV nesne türü** olarak **kişi** ' yi seçin. **Bağlantı türü**' nde, **Birleştir**' i seçin. **Öncelik**' ın, şu anda başka bir eşitleme kuralı tarafından kullanılmayan bir değer yazın (örneğin, 99). Önceki eşitleme kuralına göre daha yüksek (düşük öncelik) bir öncelik değeri seçtiniz. Ancak daha sonra ek departmanları eşitlemeye başlamak istediğinizde daha fazla filtreleme eşitleme kuralı ekleyebilmeniz için bazı odalar da bıraktınız. **İleri**’ye tıklayın.  
   ![Gelen 7 açıklaması](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. **Kapsam filtresini** boş bırakın ve **İleri**' ye tıklayın. Boş bir filtre kuralın tüm nesnelere uygulanacağını gösterir.
10. **JOIN** kurallarını boş bırakın ve **İleri**' ye tıklayın.
11. **Dönüştürme Ekle**' ye tıklayın, **FlowType** olarak **sabit** ' i seçin ve **hedef öznitelik** olarak **cloudfıltered** ' i seçin. **Kaynak** kutusuna **doğru** yazın. Kuralı kaydetmek için **Ekle** ' ye tıklayın.  
    ![Gelen 3 dönüşümü](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Yapılandırmayı gerçekleştirmek için **tam eşitleme** çalıştırmanız gerekir. Bölüm uygulama ' yı okumaya devam edin [ve değişiklikleri doğrulayın](#apply-and-verify-changes).

Gerekirse, eşitlemeye daha fazla nesne dahil ettiğiniz ilk tür için daha fazla kural oluşturabilirsiniz.

### <a name="outbound-filtering"></a>Giden filtreleme
Bazı durumlarda filtrelemeyi yalnızca, nesneler meta veri deposuna katıldıktan sonra yapmanız gerekir. Örneğin, bir nesnenin eşitlenip eşitlenmeyeceğini anlamak için kaynak ormanından posta özniteliğine ve hesap ormanındaki userPrincipalName özniteliğine bakmak gerekebilir. Bu durumlarda, giden kuralında filtrelemeyi oluşturursunuz.

Bu örnekte, yalnızca hem e-postası hem de userPrincipalName 'i olan kullanıcıların eşitlenmesi için filtrelemeyi değiştirirsiniz @contoso.com :

1. Azure AD Connect çalıştıran sunucuda, **Adsyncadmins** güvenlik grubunun üyesi olan bir hesap kullanarak oturum açın.
2. **Başlangıç** menüsünden **eşitleme kuralları düzenleyicisini** başlatın.
3. **Kurallar türü** altında **giden**' e tıklayın.
4. Kullandığınız bağlantı sürümüne bağlı olarak, **Azure AD 'ye giden Kullanıcı katılımı** veya **giden Azure AD-User JOIN soainad** adlı kuralı bulun ve **Düzenle**' ye tıklayın.
5. Açılır pencerede, kuralın bir kopyasını oluşturmak için **Evet** yanıtını verin.
6. **Açıklama** sayfasında, **önceliği** 50 gibi kullanılmayan bir değerle değiştirin.
7. Sol taraftaki **gezinmede kapsam filtresi** ' ne tıklayın ve ardından **yan tümce Ekle**' ye tıklayın. **Öznitelikte**, **posta**' yı seçin. **İşleç** Içinde, **EndsWith** öğesini seçin. **Değer** alanına **\@ contoso.com** yazın ve ardından **yan tümce Ekle**' ye tıklayın. **Özniteliğinde** **userPrincipalName**' i seçin. **İşleç** Içinde, **EndsWith** öğesini seçin. **Değer** alanına **\@ contoso.com** yazın.
8. **Kaydet**’e tıklayın.
9. Yapılandırmayı gerçekleştirmek için **tam eşitleme** çalıştırmanız gerekir. Bölüm uygulama ' yı okumaya devam edin [ve değişiklikleri doğrulayın](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Değişiklikleri Uygula ve Doğrula
Yapılandırma değişikliklerinizi yaptıktan sonra, bunları sistemde zaten mevcut olan nesnelere uygulamanız gerekir. Ayrıca, şu anda eşitleme altyapısında olmayan nesnelerin işlenmesi (ve eşitleme altyapısının içeriğini doğrulamak için kaynak sistemi yeniden okuması gerekir) olabilir.

**Etki alanı** veya **kuruluş birimi** filtrelemeyi kullanarak yapılandırmayı değiştirdiyseniz, **tam Içeri aktarma** yapmanız ve ardından **Delta eşitlemesi** yapmanız gerekir.

Yapılandırmayı **öznitelik** filtrelemesini kullanarak değiştirdiyseniz, **tam eşitleme** yapmanız gerekir.

Aşağıdaki adımları uygulayın:

1. **Başlangıç** menüsünden **eşitleme hizmetini** başlatın.
2. **Bağlayıcılar**' ı seçin. **Bağlayıcılar** listesinde, daha önce bir yapılandırma değişikliği yaptığınız bağlayıcıyı seçin. **Eylemler**' de **Çalıştır**' ı seçin.  
   ![Bağlayıcı çalıştırması](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. **Çalıştırma profilleri**' nde, önceki bölümde belirtilen işlemi seçin. İki eylemi çalıştırmanız gerekiyorsa, birincisinin tamamlanmasının ardından ikinci birini çalıştırın. ( **Durum** sütunu seçili bağlayıcı için **Boşta** .)

Eşitlemeden sonra tüm değişiklikler verilmek üzere hazırlanır. Azure AD 'de değişiklikleri gerçekten yapmadan önce, tüm bu değişikliklerin doğru olduğunu doğrulamak istiyorsunuz.

1. Bir komut istemi başlatın ve adresine gidin `%ProgramFiles%\Microsoft Azure AD Sync\bin` .
2. `csexport "Name of Connector" %temp%\export.xml /f:x` öğesini çalıştırın.  
   Bağlayıcının adı, eşitleme hizmetidir. Azure AD için "contoso.com – Azure AD" benzeri bir ada sahiptir.
3. `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` öğesini çalıştırın.
4. Artık% TEMP% adlı, Microsoft Excel 'de incelenebilir export.csv adlı bir dosyanız var. Bu dosya, verilmek üzere olan tüm değişiklikleri içerir.
5. Veri veya yapılandırmada gerekli değişiklikleri yapın ve dışarı aktarılacak değişiklikler beklediğiniz şeydir, bu adımları yeniden çalıştırın (Içeri aktar, eşitlendiğinde ve Doğrula).

Memnun kaldığınızda, değişiklikleri Azure AD 'ye aktarın.

1. **Bağlayıcılar**' ı seçin. **Bağlayıcılar** LISTESINDEN Azure AD bağlayıcısını seçin. **Eylemler**' de **Çalıştır**' ı seçin.
2. **Çalıştırma profilleri**' nde, **dışarı aktar**' ı seçin.
3. Yapılandırma değişirse çok sayıda nesne silerseniz, sayı yapılandırılan eşikten (varsayılan olarak 500) daha fazla olduğunda dışarı aktarma sırasında bir hata görürsünüz. Bu hatayı görürseniz, "[yanlışlıkla Silmeleri engelle](how-to-connect-sync-feature-prevent-accidental-deletes.md)" özelliğini geçici olarak devre dışı bırakmanız gerekir.

Şimdi Scheduler 'ı yeniden etkinleştirmeniz zaman alabilir.

1. **Başlangıç** menüsünden **Görev Zamanlayıcı** başlatın.
2. Doğrudan **Görev Zamanlayıcı Kitaplığı** altında, **Azure AD eşitleme Zamanlayıcı** adlı görevi bulun, sağ tıklayın ve **Etkinleştir**' i seçin.

## <a name="group-based-filtering"></a>Grup tabanlı filtreleme
[Özel yükleme](how-to-connect-install-custom.md#sync-filtering-based-on-groups)kullanarak Azure AD Connect ilk kez yüklediğinizde grup tabanlı filtreleme yapılandırabilirsiniz. Yalnızca küçük bir nesne kümesinin eşitlenmesini istediğiniz bir pilot dağıtıma yöneliktir. Grup tabanlı filtrelemeyi devre dışı bıraktığınızda, yeniden etkinleştirilemez. Özel bir yapılandırmada grup tabanlı filtreleme kullanılması *desteklenmez* . Bu özelliği yalnızca Yükleme Sihirbazı kullanılarak yapılandırmak desteklenir. Pilot hesabınızı tamamladıktan sonra, bu konudaki diğer filtreleme seçeneklerinden birini kullanın. Grup tabanlı filtreleme ile birlikte OU tabanlı filtreleme kullanılırken, grubun ve üyelerinin bulunduğu OU 'lar dahil olmalıdır.

Birden çok AD ormanını eşitlerken, her AD Bağlayıcısı için farklı bir grup belirterek grup tabanlı filtreleme yapılandırabilirsiniz. Bir kullanıcıyı bir AD ormanında ve aynı kullanıcının diğer AD ormanlarında bir veya daha fazla karşılık gelen nesne ile eşitlenmesi isterseniz, Kullanıcı nesnesinin ve ilgili tüm nesnelerinin grup tabanlı filtreleme kapsamında olduğundan emin olmanız gerekir. Örnekler için:

* Bir ormanda, başka bir ormandaki ilgili FSP (yabancı güvenlik sorumlusu) nesnesine sahip bir kullanıcı var. Her iki nesne de grup tabanlı filtreleme kapsamı içinde olmalıdır. Aksi takdirde, Kullanıcı Azure AD ile eşitlenmez.

* Bir ormanda, başka bir ormanda karşılık gelen kaynak hesabı (örn. bağlı posta kutusu) olan bir kullanıcı var. Ayrıca, kullanıcıyı kaynak hesabıyla bağlamak için Azure AD Connect yapılandırdınız. Her iki nesne de grup tabanlı filtreleme kapsamı içinde olmalıdır. Aksi takdirde, Kullanıcı Azure AD ile eşitlenmez.

* Bir ormanda, başka bir ormanda ilgili posta kişisi olan bir kullanıcıya sahipsiniz. Ayrıca, kullanıcıyı posta kişisi ile bağlamak için Azure AD Connect yapılandırdınız. Her iki nesne de grup tabanlı filtreleme kapsamı içinde olmalıdır. Aksi takdirde, Kullanıcı Azure AD ile eşitlenmez.


## <a name="next-steps"></a>Sonraki adımlar
- [Azure AD Connect eşitleme](how-to-connect-sync-whatis.md) yapılandırması hakkında daha fazla bilgi edinin.
- [Şirket içi kimliklerinizi Azure AD ile tümleştirme](whatis-hybrid-identity.md)hakkında daha fazla bilgi edinin.
