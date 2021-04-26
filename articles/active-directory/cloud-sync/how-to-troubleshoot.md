---
title: Azure AD Connect bulut eşitleme sorunlarını giderme
description: Bu makalede, bulut sağlama aracısında ortaya çıkabilecek sorunları nasıl giderebileceğiniz açıklanır.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 65022d98c7ee7e90d8f1fe5b6854605c841ad05b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530313"
---
# <a name="cloud-sync-troubleshooting"></a>Bulut eşitleme sorunlarını giderme

Bulut eşitleme birçok farklı öğeyi etkiler ve birçok farklı bağımlılığı vardır. Kapsamın genişliği çeşitli sorunlara yol açabilir. Bu makale, bu sorunları gidermenize yardımcı olur. Normal olarak odaklanmanız gereken alanları, ek bilgilerin nasıl toplanacağını ve sorunları izlerken kullanabileceğiniz çeşitli teknikleri tanıtır.


## <a name="common-troubleshooting-areas"></a>Ortak sorun giderme alanı

|Ad|Açıklama|
|-----|-----|
|[Aracı sorunları](#agent-problems)|Aracının doğru şekilde yüklendiğini ve Azure Active Directory (Azure AD) ile iletişim kuracağını doğrulayın.|
|[Nesne eşitleme sorunları](#object-synchronization-problems)|Nesne eşitleme sorunlarını gidermek için sağlama günlüklerini kullanın.|
|[Karantinaya alınan sorunları sağlama](#provisioning-quarantined-problems)|Karantina sorunlarını sağlamayı ve bunları nasıl düzelteceğinizi anlayın.|


## <a name="agent-problems"></a>Aracı sorunları
Aracıda doğrulamak istediğiniz ilk şey şunlardır:

-  Mi yüklendi?
-  Aracı yerel olarak çalışıyor mu?
-  Aracı portalda mı?
-  Aracı sağlıklı olarak işaretlendi mi?

Bu öğeler Azure portal ve aracıyı çalıştıran yerel sunucu üzerinde doğrulanabilir.

### <a name="azure-portal-agent-verification"></a>Aracı doğrulama Azure portal

Aracının Azure tarafından görüleceği ve sağlıklı olduğundan emin olmak için aşağıdaki adımları izleyin.

1. Azure portalında oturum açın.
1. Sol tarafta **Azure Active Directory**  >  **Azure AD Connect**' yı seçin. Ortasında, **Eşitlemeyi Yönet**' i seçin.
1. **Azure AD Connect bulut eşitleme** ekranında **tüm aracıları gözden geçir**' i seçin.

   ![Tüm aracıları gözden geçir](media/how-to-install/install-7.png)</br>
 
1. **Şirket içi sağlama aracıları** ekranında, yüklediğiniz aracıları görürsünüz. Söz konusu aracının orada olduğunu ve *sağlıklı* olarak işaretlendiğinden emin olun.

   ![Şirket içi sağlama aracıları ekranı](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Bağlantı noktasını doğrulama

Azure 'un 443 numaralı bağlantı noktasını dinlediğini ve aracınızın onunla iletişim kurabildiğini doğrulayın. 

Bu test, aracılarınızın 443 numaralı bağlantı noktası üzerinden Azure ile iletişim kurabildiğini doğrular. Bir tarayıcı açın ve aracının yüklendiği sunucudan önceki URL 'ye gidin.

![Bağlantı noktası erişilebilirliği doğrulaması](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Yerel sunucuda

Aracının çalıştığını doğrulamak için aşağıdaki adımları izleyin.

1. Aracının yüklü olduğu sunucuda, bu hizmete giderek veya    >    >  **Hizmetleri Çalıştır. msc**' yi Başlat ' a giderek hizmetler ' i açın.
1. **Hizmetler**' in altında **Microsoft Azure AD aracı güncelleştiricisi bağlama** ve **Microsoft Azure AD Connect sağlama aracısının** aynı olduğundan ve durumlarının *çalıştığından* emin olun.

   ![Hizmetler ekranı](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Ortak aracı yükleme sorunları

Aşağıdaki bölümlerde bazı yaygın aracı yükleme sorunları ve tipik çözümler açıklanır.

#### <a name="agent-failed-to-start"></a>Aracı başlatılamadı

Aşağıdakileri belirten bir hata iletisi alabilirsiniz:

**' Microsoft Azure AD Connect sağlama Aracısı ' hizmeti başlatılamadı. Sistem hizmetlerini başlatmak için yeterli ayrıcalıklara sahip olduğunuzu doğrulayın.** 

Bu sorun genellikle izinlerin yükleyicinin (NT SERVICE\AADConnectProvisioningAgent) tarafından oluşturulan yerel NT hizmeti oturum açma hesabına uygulanmasını önleyen bir grup ilkesi nedeniyle oluşur. Hizmeti başlatmak için bu izinler gereklidir.

Bu sorunu çözmek için aşağıdaki adımları izleyin.

1. Sunucuda yönetici hesabıyla oturum açın.
1. **Hizmetlere** giderek veya Hizmetleri   >  **Çalıştır**  >  **. msc**' ye giderek Hizmetleri açın.
1. **Hizmetler**' in altında, **Microsoft Azure AD sağlama Aracısı**' na çift tıklayın.
1. **Oturum aç** sekmesinde **Bu hesabı** bir etki alanı yöneticisi olarak değiştirin. Ardından hizmeti yeniden başlatın. 

   ![Oturum açma sekmesi](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Aracı zaman aşımı veya sertifika geçersiz

Aracıyı kaydetmeye çalıştığınızda aşağıdaki hata iletisini alabilirsiniz.

![Zaman aşımı hata iletisi](media/how-to-troubleshoot/troubleshoot-4.png)

Bu sorun genellikle aracının Karma Kimlik Hizmetine bağlanamamasından kaynaklanır ve bir HTTP ara sunucusu yapılandırmanız gerekir. Bu sorunu çözmek için giden bir ara sunucu yapılandırın. 

Sağlama Aracısı bir giden proxy 'nin kullanımını destekler. Bunu, *C:\Program Files\Microsoft Azure AD Connect sağlama Agent\AADConnectProvisioningAgent.exe.config* aracı yapılandırma dosyasını düzenleyerek yapılandırabilirsiniz. Aşağıdaki satırları, kapanış etiketinden hemen öncesine dosyanın sonuna doğru ekleyin `</configuration>` .
Değişkenleri `[proxy-server]` ve `[proxy-port]` proxy sunucu adı ve bağlantı noktası değerlerinizle değiştirin.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Aracı kaydı güvenlik hatasıyla başarısız oluyor

Bulut sağlama aracısını yüklerken bir hata iletisi alabilirsiniz.

Bu sorun genellikle aracının yerel PowerShell yürütme ilkeleri nedeniyle PowerShell kayıt betikleri yürütmesine neden olur.

Bu sorunu çözmek için sunucudaki PowerShell yürütme ilkelerini değiştirin. Makine ve kullanıcı ilkelerinin *tanımsız* veya *RemoteSigned* olarak ayarlanmış olması gerekir. *Kısıtlamasız* olarak ayarlandıklarında bu hatayı görürsünüz. Daha fazla bilgi için bkz. [PowerShell yürütme ilkeleri](/powershell/module/microsoft.powershell.core/about/about_execution_policies). 

### <a name="log-files"></a>Günlük dosyaları

Varsayılan olarak aracı mümkün olduğunca az hata iletisi ve yığın izleme bilgisi yarar. Bu izleme günlüklerini **C:\programdata\microsoft\azure AD Connect sağlama programı \ Trace** klasöründe bulabilirsiniz.

Aracıyla ilgili sorunları gidermeye yönelik ek ayrıntılar toplamak için aşağıdaki adımları izleyin.

1.  [Burada](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)açıklandığı gibi AADCloudSyncTools PowerShell modülünü yükler.
2. `Export-AADCloudSyncToolsLogs`Bilgileri yakalamak için PowerShell cmdlet 'ini kullanın.  Veri koleksiyonunuzu ince ayar yapmak için aşağıdaki anahtarları kullanabilirsiniz.
      - Yalnızca ayrıntılı günlükleri yakalamadan geçerli günlükleri dışarı aktarmak için SkipVerboseTrace (varsayılan = false)
      - Farklı bir yakalama süresi belirtmek için TracingDurationMins (varsayılan = 3 dakika)
      - Farklı bir çıkış yolu belirtmek için OutputPath (varsayılan = kullanıcının belgeleri)


## <a name="object-synchronization-problems"></a>Nesne eşitleme sorunları

Aşağıdaki bölümde, nesne eşitlemeyle ilgili sorun giderme hakkında bilgiler yer almaktadır.

### <a name="provisioning-logs"></a>Sağlama günlükleri

Azure portal, sağlama günlükleri, nesne eşitleme sorunlarını izlemeye ve sorun gidermenize yardımcı olmak için kullanılabilir. Günlükleri görüntülemek için **Günlükler**' i seçin.

![Günlükler düğmesi](media/how-to-troubleshoot/log-1.png)

Sağlama günlükleri, şirket içi Active Directory ortamınız ile Azure arasında eşitlenen nesnelerin durumu hakkında çok fazla bilgi sağlar.

![Sağlama günlükleri ekranı](media/how-to-troubleshoot/log-2.png)

Sayfanın en üstündeki açılan kutuları, tarihler gibi belirli sorunlarda görünümü sıfıra filtrelemek için kullanabilirsiniz. Ek bilgi görmek için tek bir olaya çift tıklayın.

![Sağlama günlükleri açılan kutusu bilgileri](media/how-to-troubleshoot/log-3.png)

Bu bilgiler, ayrıntılı adımlar ve eşitleme sorununun nerede oluştuğunu sağlar. Bu şekilde, sorunun tam olarak bulunduğu noktayı bir arada kullanabilirsiniz.


## <a name="provisioning-quarantined-problems"></a>Karantinaya alınan sorunları sağlama

Bulut eşitleme, yapılandırmanızın sistem durumunu izler ve sağlıksız nesneleri karantina durumuna koyar. Hedef sisteme karşı yapılan çağrıların çoğu veya hepsi sürekli olarak hata nedeniyle başarısız olursa, örneğin, geçersiz yönetici kimlik bilgileri, eşitleme işi karantinaya alındı olarak işaretlenir.

![Karantina durumu](media/how-to-troubleshoot/quarantine-1.png)

Durumu seçerek, karantinaya alma hakkında daha fazla bilgi görebilirsiniz. Hata kodunu ve iletiyi de elde edebilirsiniz.

![Karantinayla ilgili ek bilgileri gösteren ekran görüntüsü.](media/how-to-troubleshoot/quarantine-2.png)

Duruma sağ tıklamak, ek seçenekleri getirir:
    
   - sağlama günlüklerini görüntüle
   - Aracıyı görüntüle
   - karantinayı temizle

![Sağ tıklama menü seçeneklerini gösteren ekran görüntüsü.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Karantinayı çözümle
Bir karantinayı çözmek için iki farklı yol vardır.  Bunlar:

  - Karantina silme-filigranı temizler ve bir Delta eşitlemesi çalıştırır
  - sağlama işini yeniden başlatın-filigranı temizler ve bir ilk eşitleme çalıştırır

#### <a name="clear-quarantine"></a>Karantinayı temizle
Bu filigranı temizlemek ve doğrulama işi doğrulandıktan sonra bir Delta eşitlemesi çalıştırmak için, duruma sağ tıklayıp **karantinaya al**' ı seçmeniz yeterlidir.

Karantinanın temizlendiğine ilişkin bir bildirim görmeniz gerekir.

![Karantinanın temizlendiğine ilişkin bildirimi gösteren ekran görüntüsü.](media/how-to-troubleshoot/quarantine-5.png)

Ardından, aracıdaki durumu sağlıklı olarak görmeniz gerekir.

![Karantina durumu bilgileri](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Sağlama işini yeniden başlatın
Sağlama işini yeniden başlatmak için Azure portal kullanın. Aracı yapılandırması sayfasında, **sağlamayı yeniden Başlat**' ı seçin.

  ![Sağlamayı yeniden Başlat](media/how-to-troubleshoot/quarantine-3.png)

- [Sağlama işini yeniden başlatmak](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)için Microsoft Graph kullanın. Yeniden başlatdıklarınız üzerinde tam denetime sahip olacaksınız. Şunları temizleyebilirsiniz:
  - Et, karantina durumuna doğru tahakkuk eden Emanet sayacını yeniden başlatacak.
  - Karantina, uygulamayı karantinadan kaldırma.
  - Fli. 
  
  Aşağıdaki isteği kullanın:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Bulut eşitleme hizmeti hesabını onarma
Bulut eşitleme hizmeti hesabını onarmanız gerekiyorsa, kullanabilirsiniz `Repair-AADCloudSyncToolsAccount` .  


   1.  Başlamak için [burada](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) özetlenen yükleme adımlarını kullanın ve ardından kalan adımlara devam edin.
   2.  Yönetici ayrıcalıklarına sahip bir Windows PowerShell oturumunda aşağıdakileri yazın veya kopyalayıp yapıştırın: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Azure AD Genel yönetici kimlik bilgilerinizi girin
   4. Aşağıdakileri yazın veya kopyalayıp yapıştırın: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Bu işlem tamamlandıktan sonra hesabın başarıyla onarıldığı bildirilmelidir.

## <a name="next-steps"></a>Sonraki adımlar 

- [Bilinen sınırlamalar](how-to-prerequisites.md#known-limitations)
- [Hata kodları](reference-error-codes.md)
