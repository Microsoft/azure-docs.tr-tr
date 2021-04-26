---
title: Azure AD Connect Sync ile parola karması eşitleme sorunlarını giderme | Microsoft Docs
description: Bu makalede, Parola karması eşitleme sorunlarını giderme hakkında bilgi sağlanır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c6ec162ceb51c3bf19be42219b054d8371ff221
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98247361"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect eşitlemesi ile parola karması eşitleme sorunlarını giderme

Bu konuda, Parola karması eşitlemeyle ilgili sorunları gidermeye yönelik adımlar sağlanmaktadır. Parolalar beklenildiği gibi eşitlenmiyorsa, kullanıcılar veya tüm kullanıcılar için bir alt küme olabilir.

Sürüm 1.1.614.0 veya sonra dağıtım dağıtımı Azure Active Directory (Azure AD) için, Parola karması eşitleme sorunlarını gidermek için sihirbazda sorun giderme görevini kullanın:

* Parolaların eşitlenmediği bir sorununuz varsa, [hiçbir parola eşitlenmedi: sorun giderme görevini kullanarak sorun giderme](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) bölümüne bakın.

* Ayrı nesneler ile ilgili bir sorununuz varsa, [bir nesne parolaları eşitlememe: sorun giderme görevi ile ilgili sorun giderme](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) bölümüne bakın.

Sürüm 1.1.524.0 veya üzeri bir dağıtım için, Parola karması eşitleme sorunlarını gidermek için kullanabileceğiniz bir tanılama cmdlet 'i vardır:

* Parolaların eşitlenmediği bir sorununuz varsa, [hiçbir parola eşitlenmedi: Tanılama cmdlet 'ini kullanarak sorun giderme](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) bölümüne bakın.

* Ayrı nesneler ile ilgili bir sorununuz varsa, [bir nesne parolaları eşitlememe: Tanılama cmdlet 'i kullanarak sorun giderme](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) bölümüne bakın.

Azure AD Connect dağıtımının eski sürümleri için:

* Parolaların eşitlenmediği bir sorununuz varsa, [hiçbir parola eşitlenmedi: el ile sorun giderme adımları](#no-passwords-are-synchronized-manual-troubleshooting-steps) bölümüne bakın.

* Ayrı nesneler ile ilgili bir sorununuz varsa, [bir nesne parolaları eşitlememe: el ile sorun giderme adımları](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) bölümüne bakın.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Eşitlenen parola yok: sorun giderme görevini kullanarak sorun giderme

Hiçbir parolanın nasıl eşitlenmediğini anlamak için sorun giderme görevini kullanabilirsiniz.

> [!NOTE]
> Sorun giderme görevi yalnızca Azure AD Connect Version 1.1.614.0 veya üzeri için kullanılabilir.

### <a name="run-the-troubleshooting-task"></a>Sorun giderme görevini çalıştırma

Hiçbir parolanın eşitlenmediği sorunları gidermek için:

1. **Yönetici olarak çalıştır** seçeneğiyle Azure AD Connect sunucunuzda yeni bir Windows PowerShell oturumu açın.

2. `Set-ExecutionPolicy RemoteSigned`Veya çalıştırın `Set-ExecutionPolicy Unrestricted` .

3. Azure AD Connect Sihirbazı 'nı başlatın.

4. **Ek görevler** sayfasına gidin, **sorun gider**' i seçin ve **İleri**' ye tıklayın.

5. Sorun giderme sayfasında, PowerShell 'de sorun giderme menüsünü başlatmak için **Başlat** ' a tıklayın.

6. Ana menüde, **Parola karması eşitlemesini sorun gider**' i seçin.

7. Alt menüde **Parola karması eşitleme**' yi seçin.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Sorun giderme görevinin sonuçlarını anlayın

Sorun giderme görevi aşağıdaki denetimleri gerçekleştirir:

* Azure AD kiracınız için Parola karması eşitleme özelliğinin etkinleştirildiğini doğrular.

* Azure AD Connect sunucusunun hazırlama modunda olmadığı doğrular.

* Mevcut olan her bir şirket içi Active Directory Bağlayıcısı için (mevcut bir Active Directory ormanına karşılık gelen):

   * Parola karması eşitleme özelliğinin etkinleştirildiğini doğrular.
   
   * Windows uygulama olay günlüklerinde Parola karması eşitleme sinyal olaylarını arar.

   * Şirket içi Active Directory Bağlayıcısı altındaki her bir Active Directory etki alanı için:

      * Azure AD Connect sunucusundan etki alanına ulaşılabildiğini doğrular.

      * Şirket içi Active Directory Bağlayıcısı tarafından kullanılan Active Directory Domain Services (AD DS) hesaplarının parola karması eşitlemesi için gereken doğru Kullanıcı adı, parola ve izinlere sahip olduğunu doğrular.

Aşağıdaki diyagramda tek etki alanı, şirket içi Active Directory topolojisi için cmdlet 'inin sonuçları gösterilmektedir:

![Parola karması eşitleme için tanılama çıktısı](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

Bu bölümün geri kalanında, görev ve ilgili sorunlar tarafından döndürülen belirli sonuçlar açıklanmaktadır.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>Parola karması eşitleme özelliği etkin değil

Parola karma eşitlemesini Azure AD Connect Sihirbazı 'nı kullanarak etkinleştirmediyseniz, aşağıdaki hata döndürülür:

![Parola karması eşitleme etkin değil](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect sunucusu hazırlama modunda

Azure AD Connect sunucusu hazırlama modundaysa, Parola karması eşitlemesi geçici olarak devre dışı bırakılır ve aşağıdaki hata döndürülür:

![Azure AD Connect sunucusu hazırlama modunda](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Parola karması eşitleme sinyal olayı yok

Her şirket içi Active Directory bağlayıcısının kendi parola karması eşitleme kanalı vardır. Parola karması eşitleme kanalı oluşturulduğunda ve eşitlenecek herhangi bir parola değişikliği olmadığında, Windows uygulama olay günlüğü altında 30 dakikada bir sinyal olayı (EventID 654) oluşturulur. Her şirket içi Active Directory Bağlayıcısı için, cmdlet son üç saat içinde ilgili sinyal olaylarını arar. Hiçbir sinyal olayı bulunmazsa, aşağıdaki hata döndürülür:

![Parola karması eşitleme sinyal olayı yok](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS hesabın doğru izinleri yok

Parola karmalarını eşitlemeye yönelik şirket içi Active Directory Bağlayıcısı tarafından kullanılan AD DS hesabı uygun izinlere sahip değilse, aşağıdaki hata döndürülür:

![AD DS hesabı yanlış Kullanıcı adı veya parolaya sahip olduğunda döndürülen hatayı gösteren ekran görüntüsü.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Hesap Kullanıcı adı veya parola yanlış AD DS

Parola karmalarını eşitlemeye yönelik şirket içi Active Directory Bağlayıcısı tarafından kullanılan AD DS hesabının Kullanıcı adı veya parolası yanlış olursa aşağıdaki hata döndürülür:

![Yanlış kimlik bilgisi](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Bir nesne parolaları eşitlemiyor: sorun giderme görevini kullanarak sorun giderme

Bir nesnenin neden parolaları eşitleyeceğini anlamak için sorun giderme görevini kullanabilirsiniz.

> [!NOTE]
> Sorun giderme görevi yalnızca Azure AD Connect Version 1.1.614.0 veya üzeri için kullanılabilir.

### <a name="run-the-diagnostics-cmdlet"></a>Tanılama cmdlet 'ini çalıştırma

Belirli bir kullanıcı nesnesi ile ilgili sorunları gidermek için:

1. **Yönetici olarak çalıştır** seçeneğiyle Azure AD Connect sunucunuzda yeni bir Windows PowerShell oturumu açın.

2. `Set-ExecutionPolicy RemoteSigned`Veya çalıştırın `Set-ExecutionPolicy Unrestricted` .

3. Azure AD Connect Sihirbazı 'nı başlatın.

4. **Ek görevler** sayfasına gidin, **sorun gider**' i seçin ve **İleri**' ye tıklayın.

5. Sorun giderme sayfasında, PowerShell 'de sorun giderme menüsünü başlatmak için **Başlat** ' a tıklayın.

6. Ana menüde, **Parola karması eşitlemesini sorun gider**' i seçin.

7. Alt menüde, **belirli bir kullanıcı hesabı Için parola eşitlenmez**' ı seçin.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Sorun giderme görevinin sonuçlarını anlayın

Sorun giderme görevi aşağıdaki denetimleri gerçekleştirir:

* Active Directory bağlayıcı alanı, Metadize ve Azure AD bağlayıcı alanındaki Active Directory nesnesinin durumunu inceler.

* Parola karması eşitlemesi etkinleştirilmiş ve Active Directory nesnesine uygulanan eşitleme kuralları bulunduğunu doğrular.

* Nesnenin parolasını eşitlemeye yönelik son girişim sonuçlarını almaya ve görüntülemeye çalışır.

Aşağıdaki diyagramda tek bir nesne için Parola karması eşitlemeyle ilgili sorun giderirken cmdlet 'in sonuçları gösterilmektedir:

![Parola karması eşitlemesi için tanılama çıktısı-tek nesne](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

Bu bölümün geri kalanında cmdlet tarafından döndürülen belirli sonuçlar ve ilgili sorunlar açıklanmaktadır.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory nesnesi Azure AD 'ye aktarılmaz

Azure AD kiracısında karşılık gelen bir nesne olmadığından, bu şirket içi Active Directory hesabı için Parola karması eşitlemesi başarısız oldu. Aşağıdaki hata döndürülür:

![Azure AD nesnesi eksik](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Kullanıcının geçici bir parolası vardır

Şu anda Azure AD Connect Azure AD ile geçici parolaların eşitlenmesini desteklemez. Şirket içi Active Directory Kullanıcı üzerinde bir **sonraki oturum açışında parolayı Değiştir** seçeneği ayarlandıysa parolanın geçici olduğu kabul edilir. Aşağıdaki hata döndürülür:

![Geçici parola dışarıya aktarılmamış](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Parola eşitlemeye yönelik son deneme sonuçları kullanılamıyor

Varsayılan olarak, Azure AD Connect yedi gün boyunca Parola karması eşitleme denemelerinin sonuçlarını depolar. Seçili Active Directory nesnesi için kullanılabilir sonuç yoksa, aşağıdaki uyarı döndürülür:

![Tek nesne için tanılama çıktısı-parola eşitleme geçmişi yok](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Eşitlenen parola yok: Tanılama cmdlet 'ini kullanarak sorun giderme

Bir `Invoke-ADSyncDiagnostics` parolanın neden eşitlenmemiş olduğunu anlamak için cmdlet 'ini kullanabilirsiniz.

> [!NOTE]
> `Invoke-ADSyncDiagnostics`Cmdlet 'i yalnızca Azure AD Connect Version 1.1.524.0 veya üzeri için kullanılabilir.

### <a name="run-the-diagnostics-cmdlet"></a>Tanılama cmdlet 'ini çalıştırma

Hiçbir parolanın eşitlenmediği sorunları gidermek için:

1. **Yönetici olarak çalıştır** seçeneğiyle Azure AD Connect sunucunuzda yeni bir Windows PowerShell oturumu açın.

2. `Set-ExecutionPolicy RemoteSigned`Veya çalıştırın `Set-ExecutionPolicy Unrestricted` .

3. `Import-Module ADSyncDiagnostics` öğesini çalıştırın.

4. `Invoke-ADSyncDiagnostics -PasswordSync` öğesini çalıştırın.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Bir nesne parolaları eşitlemiyor: Tanılama cmdlet 'ini kullanarak sorun giderme

`Invoke-ADSyncDiagnostics`Bir nesnenin neden parolaları eşitleyeceğini anlamak için cmdlet 'ini kullanabilirsiniz.

> [!NOTE]
> `Invoke-ADSyncDiagnostics`Cmdlet 'i yalnızca Azure AD Connect Version 1.1.524.0 veya üzeri için kullanılabilir.

### <a name="run-the-diagnostics-cmdlet"></a>Tanılama cmdlet 'ini çalıştırma

Bir kullanıcı için hiçbir parolanın eşitlenmediği sorunları gidermek için:

1. **Yönetici olarak çalıştır** seçeneğiyle Azure AD Connect sunucunuzda yeni bir Windows PowerShell oturumu açın.

2. `Set-ExecutionPolicy RemoteSigned`Veya çalıştırın `Set-ExecutionPolicy Unrestricted` .

3. `Import-Module ADSyncDiagnostics` öğesini çalıştırın.

4. Aşağıdaki cmdlet'i çalıştırın:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Örnek:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Eşitlenen parola yok: el ile sorun giderme adımları

Parolaların neden eşitlenmediğini öğrenmek için şu adımları izleyin:

1. Sunucu, [hazırlama modunda](how-to-connect-sync-staging-server.md)mı? Hazırlama modundaki bir sunucu herhangi bir parolayı eşitlemez.

2. Komut dosyasını [parola eşitleme ayarlarının durumunu Al](#get-the-status-of-password-sync-settings) bölümünde çalıştırın. Parola eşitleme yapılandırmasına genel bir bakış sunar.  

    ![Parola eşitleme ayarlarından gelen PowerShell betiği çıkışı](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Özellik Azure AD 'de etkinleştirilmemişse veya eşitleme kanalı durumu etkinleştirilmemişse, yükleme Yükleme Sihirbazı 'nı çalıştırın. **Eşitleme seçeneklerini Özelleştir**' i seçin ve parola eşitleme seçimini kaldırın. Bu değişiklik, özelliği geçici olarak devre dışı bırakır. Sonra Sihirbazı yeniden çalıştırın ve parola eşitlemesini yeniden etkinleştirin. Yapılandırmanın doğru olduğundan emin olmak için betiği yeniden çalıştırın.

4. Hatalara yönelik olay günlüğüne bakın. Bir sorunu belirten aşağıdaki olayları arayın:
    * Kaynak: "Dizin eşitleme" KIMLIĞI: 0, 611, 652, 655 bu olayları görürseniz bir bağlantı sorununuz vardır. Olay günlüğü iletisi, bir sorun yaşadığınız orman bilgilerini içerir. Daha fazla bilgi için bkz. [bağlantı sorunu](#connectivity problem).

5. Hiçbir sinyal görmüyorsanız veya başka bir şey çalışmışsa, [tüm parolaların tam eşitlemesini tetikleyin](#trigger-a-full-sync-of-all-passwords). Betiği yalnızca bir kez çalıştırın.

6. Parolaları eşitlenmeyen bir nesneyle Ilgili sorun giderme bölümüne bakın.

### <a name="connectivity-problems"></a>Bağlantı sorunları

Azure AD ile bağlantınız var mı?

Hesap, tüm etki alanlarındaki parola karmalarını okumak için gerekli izinlere sahip mi? Hızlı ayarları kullanarak bağlan ' ı yüklediyseniz, izinlerin zaten doğru olması gerekir. 

Özel yükleme kullandıysanız, aşağıdakileri yaparak izinleri el ile ayarlayın:
    
1. Active Directory Bağlayıcısı tarafından kullanılan hesabı bulmak için **Synchronization Service Manager** başlatın. 
 
2. **Bağlayıcılar**' a gidin ve sorun giderirken şirket içi Active Directory ormanını arayın. 
 
3. Bağlayıcıyı seçin ve ardından **Özellikler**' e tıklayın. 
 
4. **Active Directory ormana Bağlan**' a gidin.  
    
    ![Active Directory Bağlayıcısı tarafından kullanılan hesap](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Kullanıcı adını ve hesabın bulunduğu etki alanını aklınızda bulundurun.
    
5. **Active Directory Kullanıcıları ve bilgisayarları** başlatın ve ardından, daha önce bulduğunuz hesabın ormanınızdaki tüm etki alanlarının kökünde ayarlanan izleme izinlerine sahip olduğunu doğrulayın:
    * Dizin Değişikliklerini Çoğalt
    * Dizin değişikliklerini çoğaltma

6. Etki alanı denetleyicileri Azure AD Connect tarafından erişilebilir mi? Connect sunucusu tüm etki alanı denetleyicilerine bağlanamazsa, **yalnızca tercih edilen etki alanı denetleyicisini kullan** seçeneğini yapılandırın.  
    
    ![Active Directory Bağlayıcısı tarafından kullanılan etki alanı denetleyicisi](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. **Synchronization Service Manager** ve **dizin bölümünü yapılandırmak** için geri dönün. 
 
8. **Dizin bölümlerini Seç**' de etki alanınızı seçin, **yalnızca tercih edilen etki alanı denetleyicilerini kullan** onay kutusunu işaretleyin ve ardından **Yapılandır**' a tıklayın. 

9. Listede, parola eşitleme için bağlantı için kullanması gereken etki alanı denetleyicilerini girin. Aynı liste içeri ve dışarı aktarma için de kullanılır. Tüm etki alanlarınız için bu adımları uygulayın.

> [!NOTE]
> Bu değişiklikleri uygulamak için **Microsoft Azure AD eşitleme** (ADSync) hizmetini yeniden başlatın.

10. Betik, sinyal bulunmadığını gösteriyorsa, [tüm parolaların tam eşitlemesini tetiklemek](#trigger-a-full-sync-of-all-passwords)için betiği çalıştırın.

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Bir nesne parolaları eşitlemiyor: el ile sorun giderme adımları

Bir nesnenin durumunu inceleyerek Parola karması eşitleme sorunlarını kolayca giderebilirsiniz.

1. **Active Directory Kullanıcıları ve bilgisayarları**, kullanıcıyı arayın ve sonra **kullanıcının bir sonraki oturum açışında parolayı değiştirmesi gerektiğini** doğrulayın onay kutusunun işaretini kaldırın.  

    ![Active Directory üretken parolalar](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Onay kutusu işaretliyse kullanıcıdan oturum açmasını ve parolayı değiştirmesini isteyin. Geçici parolalar Azure AD ile eşitlenmez.

2. Parola Active Directory doğru görünüyorsa, eşitleme altyapısındaki kullanıcıyı izleyin. Şirket içi Active Directory kullanıcıyı Azure AD 'den izleyerek, nesne üzerinde açıklayıcı bir hata olup olmadığını görebilirsiniz.

    a. [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md)başlatın.

    b. **Bağlayıcılar**' a tıklayın.

    c. Kullanıcının bulunduğu **Active Directory bağlayıcısını** seçin.

    d. **Arama Bağlayıcısı alanı**' nı seçin.

    e. **Kapsam** kutusunda **DN veya bağlayıcı**' yı seçin ve ardından sorun gidermekte olduğunuz kullanıcının tam DN 'sini girin.

    ![DN ile bağlayıcı alanında Kullanıcı arayın](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Aradığınız kullanıcıyı bulun ve ardından **Özellikler** ' e tıklayarak tüm öznitelikleri görüntüleyin. Kullanıcı arama sonucu değilse, [filtreleme kurallarınızı](how-to-connect-sync-configure-filtering.md) doğrulayın ve uygulamanın Connect 'te görünmesi için [Uygula ve değişiklikleri doğrula](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) ' yı çalıştırdığınızdan emin olun.

    örneğin: Son hafta için nesnenin parola eşitleme ayrıntılarını görmek için **günlük**' e tıklayın.  

    ![Nesne günlüğü ayrıntıları](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Nesne günlüğü boşsa, Azure AD Connect Active Directory parola karmasını okuyamadı. Bağlantı hatalarıyla sorun gidermeye devam edin. **Başarıya** ait başka bir değer görürseniz, [parola eşitleme günlüğündeki](#password-sync-log)tabloya bakın.

    h. **Kökenini** sekmesini seçin ve **PasswordSync** sütununda en az bir eşitleme kuralının **doğru** olduğundan emin olun. Varsayılan yapılandırmada, eşitleme kuralının adı **ad-User AccountEnabled konumunda** bulunur.  

    ![Bir kullanıcı hakkında kökenini bilgileri](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Kullanıcı özniteliklerinin listesini göstermek için **Metadize nesne özellikleri ' ne** tıklayın.  

    ![Meta veri deposu nesne özellikleri için kullanıcı özniteliklerinin listesini gösteren ekran görüntüsü.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Hiçbir **Cloudfıltered** özniteliği bulunmadığını doğrulayın. Etki alanı özniteliklerinin (domainFQDN ve Domainnetbıos) beklenen değerlere sahip olduğundan emin olun.

    j. **Bağlayıcılar** sekmesine tıklayın. Hem şirket içi Active Directory hem de Azure AD 'de bağlayıcılar görtığınızdan emin olun.

    ![Meta veri deposu bilgileri](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Azure AD 'yi temsil eden satırı seçin, **Özellikler**' e ve ardından **kökenini** sekmesine tıklayın. Bağlayıcı alanı nesnesinin, **PasswordSync** sütununda **doğru** olarak ayarlanmış bir giden kuralı olmalıdır. Varsayılan yapılandırmada, eşitleme kuralının adı **AAD-User JOIN ' e** gönderilir.  

    ![Bağlayıcı alanı nesne özellikleri iletişim kutusu](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Parola eşitleme günlüğü

Durum sütunu aşağıdaki değerlere sahip olabilir:

| Durum | Açıklama |
| --- | --- |
| Başarılı |Parola başarıyla eşitlendi. |
| FilteredByTarget |Parola, kullanıcının bir **sonraki oturum açışında parolayı değiştirmesi gerekir**. Parola eşitlenmedi. |
| NoTargetConnection |Meta veri deposunda veya Azure AD bağlayıcı alanında hiç nesne yok. |
| Sourceconnectornotsun |Şirket içi Active Directory bağlayıcı alanında hiç nesne bulunamadı. |
| TargetNotExportedToDirectory |Azure AD bağlayıcı alanındaki nesne henüz verilmedi. |
| MigratedCheckDetailsForMoreInfo |Derleme 1.0.9125.0 önce günlük girdisi oluşturuldu ve eski durumunda gösteriliyor. |
| Hata |Hizmet bilinmeyen bir hata döndürdü. |
| Bilinmiyor |Bir grup Parola karması işlenmeye çalışılırken bir hata oluştu.  |
| MissingAttribute |Azure AD Domain Services için gereken belirli öznitelikler (örneğin, Kerberos karması) kullanılamıyor. |
| RetryRequestedByTarget |Azure AD Domain Services için gereken belirli öznitelikler (örneğin, Kerberos karması) daha önce kullanılabilir değil. Kullanıcının parola karmasını yeniden eşitleme girişimi yapılır. |

## <a name="scripts-to-help-troubleshooting"></a>Sorun gidermeye yardımcı olan betikler

### <a name="get-the-status-of-password-sync-settings"></a>Parola eşitleme ayarlarının durumunu al

```powershell
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Tüm parolaların tam eşitlemesini Tetikle

> [!NOTE]
> Bu betiği yalnızca bir kez çalıştırın. Birden çok kez çalıştırmanız gerekiyorsa, sorun başka bir şeydir. Sorunu gidermek için Microsoft destek 'e başvurun.

Aşağıdaki betiği kullanarak tüm parolaların tam eşitlemesini tetikleyebilirsiniz:

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Connect Sync ile parola karması eşitlemesi uygulama](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect eşitleme: eşitleme seçeneklerini özelleştirme](how-to-connect-sync-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
