---
title: Windows sanal masaüstü için Tanılama aracını dağıtma-Azure
description: Windows sanal masaüstü için tanılama UX aracı 'nı dağıtma.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: helohr
ms.openlocfilehash: 27231dc25604e9031f0456d787530bf2a29616f7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167440"
---
# <a name="deploy-the-diagnostics-tool"></a>Tanılama aracını dağıtma

Windows sanal masaüstü için tanılama aracı sizin için şunları yapabilir:

- Bir haftada bir dönem boyunca tek bir kullanıcı için tanılama etkinliklerini (Yönetim, bağlantı veya akış) arayın.
- Log Analytics çalışma alanınızdan bağlantı etkinlikleri için oturum ana bilgisayar bilgilerini toplayın.
- Belirli bir konak için sanal makine (VM) performans ayrıntılarını gözden geçirin.
- Hangi kullanıcıların oturum ana bilgisayarında oturum açdığını görün.
- Belirli bir oturum ana bilgisayarındaki etkin kullanıcılara ileti gönderin.
- Kullanıcıları oturum ana bilgisayarında imzalayın.

## <a name="prerequisites"></a>Önkoşullar

Araç için Azure Resource Manager şablonunu dağıtabilmeniz için önce bir Azure Active Directory uygulama kaydı ve Log Analytics çalışma alanı oluşturmanız gerekir. Bunun için yönetici bu izinlere gerek duyar:

- Azure aboneliğinin sahibi
- Azure aboneliğinizde kaynak oluşturma izni
- Azure AD uygulaması oluşturma izni
- RDS sahibi veya katkıda bulunan hakları

Başlamadan önce bu iki PowerShell modülünü de yüklemeniz gerekir:

- [Azure PowerShell modülü](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.4.0)
- [Azure AD modülü](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

Oturum açtığınızda abonelik KIMLIĞINIZ için hazırlık yaptığınızdan emin olun.

Her şeyi sipariş ettikten sonra, Azure AD uygulama kaydı oluşturabilirsiniz.

## <a name="create-an-azure-active-directory-app-registration"></a>Azure Active Directory uygulama kaydı oluşturma

Bu bölümde, bir hizmet sorumlusu ile Azure Active Directory uygulamasını oluşturmak ve API izinleri almak için PowerShell 'in nasıl kullanılacağı gösterilmektedir.

>[!NOTE]
>API izinleri Windows Sanal Masaüstü ' dir, Log Analytics ve Microsoft Graph API izinleri Azure Active Directory uygulamasına eklenir.

1. PowerShell 'i yönetici olarak açın.
2. Tanılama aracı için kullanmak istediğiniz Azure aboneliğinde sahip veya katkıda bulunan izinleri olan bir hesapla Azure 'da oturum açın:
   ```powershell
   Login-AzAccount
   ```
3. Azure AD 'de aynı hesapla oturum açın:
   ```powershell
   Connect-AzureAD
   ```
4. [RDS-Templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) deposuna gidin ve PowerShell 'de **Createuyarlanpregistrationfordiagnostics. ps1** betiğini çalıştırın.
5.  Betik sizden uygulamanızı girmenizi istediğinde, benzersiz bir uygulama adı girin.


Komut dosyası başarıyla çalıştıktan sonra, çıktıda şu işlemleri göstermelidir:

-  Uygulamanızı doğrulayan bir ileti artık hizmet sorumlusu rolü atamasına sahiptir.
-  Tanılama aracını dağıtırken ihtiyacınız olacak Istemci KIMLIĞINIZ ve Istemci gizli anahtarı.

Uygulamanızı kaydettirdiğiniz için artık Log Analytics çalışma alanınızı yapılandırma zamanı vardır.

## <a name="configure-your-log-analytics-workspace"></a>Log Analytics çalışma alanınızı yapılandırma

Olası en iyi deneyim için, Log Analytics çalışma alanınızı, uzak bir oturumda kullanıcı deneyiminin deyimlerini türetmenizi sağlayan aşağıdaki performans sayaçlarıyla yapılandırmanızı öneririz. Önerilen eşiklere sahip önerilen sayaçların bir listesi için bkz. [Windows performans sayacı eşikleri](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>PowerShell kullanarak Azure Log Analytics çalışma alanı oluşturma

Bir Log Analytics çalışma alanı oluşturmak ve Kullanıcı deneyimini ve uygulama performansını izlemek için önerilen Windows performans sayaçlarını yapılandırmak için bir PowerShell betiği çalıştırabilirsiniz.

>[!NOTE]
>Kullanmak istediğiniz PowerShell betiği olmadan yaptığınız mevcut bir Log Analytics çalışma alanınız zaten varsa, [Azure Portal betik sonuçlarını doğrulamak](#validate-the-script-results-in-the-azure-portal)için ileratlayın.

PowerShell betiğini çalıştırmak için:

1.  PowerShell 'i yönetici olarak açın.
2.  [RDS-Templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) deposuna gidin ve PowerShell 'de **Createloganalticsworkspace fordiagnostics. ps1** betiğini çalıştırın.
3. Parametreler için aşağıdaki değerleri girin:

    - **Resourcegroupname**için kaynak grubunun adını girin.
    - **Loganaliz Ticsçalışmaadı**için Log Analytics çalışma alanınız için benzersiz bir ad girin.
    - **Konum**için, kullanmakta olduğunuz Azure bölgesini girin.
    - **Abonelikler**' in altında Azure Portal bulabileceğiniz **Azure abonelik kimliğini**girin.

4. Yönetici erişimine sahip bir kullanıcının kimlik bilgilerini girin.
5. Azure portal aynı kullanıcının kimlik bilgileriyle oturum açın.
6. Daha sonra günlüğe kaydetme veya Loganaliz Ticsworkspace KIMLIĞINI yazın.
7. Log Analytics çalışma alanını PowerShell betiği ile ayarlarsanız, performans sayaçlarınız zaten yapılandırılmış olmalıdır ve [Azure Portal betik sonuçlarının doğrulanması](#validate-the-script-results-in-the-azure-portal)için ilerme atlayabilirsiniz. Aksi takdirde, sonraki bölüme geçin.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Mevcut Log Analytics çalışma alanınızda Windows performans sayaçlarını yapılandırma

Bu bölüm, önceki bölümde PowerShell betiği olmadan oluşturulan mevcut bir Azure Log Analytics çalışma alanını kullanmak isteyen kullanıcılar içindir. Betiği kullanmadıysanız, önerilen Windows performans sayaçlarını el ile yapılandırmanız gerekir.

Önerilen performans sayaçlarını el ile yapılandırmak için aşağıdaki adımları uygulayın:

1. Internet tarayıcınızı açın ve [Azure Portal](https://portal.azure.com/) Yönetici hesabınızla oturum açın.
2. Sonra, yapılandırılan Windows performans sayaçlarını gözden geçirmek için **Log Analytics çalışma alanlarına** gidin.
3. **Ayarlar** bölümünde **Gelişmiş ayarlar**' ı seçin.
4. Bundan sonra, **Windows performans sayaçları** > **veriler** ' e gidin ve aşağıdaki sayaçları ekleyin:

    -   MantıksalDisk (\*) boş alan\\
    -   MantıksalDisk (C:)\\ort. disk kuyruğu uzunluğu
    -   Bellek (\*)\\kullanılabilir MBayt
    -   İşlemci bilgileri (\*)\\Işlemci zamanı
    -   Oturum başına kullanıcı girişi gecikmesi (\*)\\en fazla giriş gecikmesi

[Azure izleyici 'de Windows ve Linux performans verileri kaynaklarında](/azure/azure-monitor/platform/data-sources-performance-counters)performans sayaçları hakkında daha fazla bilgi edinin.

>[!NOTE]
>Yapılandırdığınız tüm ek sayaçlar, Tanılama Aracı 'nda gösterilmez. Tanılama aracında görünmesini sağlamak için aracın yapılandırma dosyasını yapılandırmanız gerekir. Bunu gelişmiş yönetimle birlikte nasıl yapacağınız hakkında yönergeler, daha sonraki bir tarihte GitHub 'da kullanılabilir.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Azure portal betik sonuçlarının doğrulanması

Tanılama aracını dağıtmaya devam etmeden önce, Azure Active Directory uygulamanızın API izinlerine sahip olduğunu ve Log Analytics çalışma alanınızın önceden yapılandırılmış Windows performans sayaçlarını içerdiğini doğrulamanızı öneririz.

### <a name="review-your-app-registration"></a>Uygulama kaydınızı gözden geçirin

Uygulama kaydlarınızın API izinlerine sahip olduğundan emin olmak için:

1. Bir tarayıcı açın ve [Azure Portal](https://portal.azure.com/) Yönetici hesabınızla bağlanın.
2. **Uygulama kayıtları** gidin ve Azure AD uygulaması kaydınızı bulun.

      ![API izinleri sayfası.](media/api-permissions-page.png)


### <a name="review-your-log-analytics-workspace"></a>Log Analytics çalışma alanınızı gözden geçirin

Log Analytics çalışma alanınızın önceden yapılandırılmış Windows performans sayaçlarına sahip olduğundan emin olmak için:

1. [Azure Portal](https://portal.azure.com/), yapılandırılmış Windows performans sayaçlarını gözden geçirmek için **Log Analytics çalışma alanları** ' na gidin.
2. **Ayarlar**altında **Gelişmiş ayarlar**' ı seçin.
3. Bundan sonra, **Windows performans sayaçları** > **veri** ' ye gidin.
4. Aşağıdaki sayaçların önceden yapılandırılmış olduğundan emin olun:

   - MantıksalDisk (\*) boş alan\\: diskte toplam kullanılabilir alanın boş alan miktarını yüzde olarak görüntüler.
   - MantıksalDisk (C:)\\ort. disk kuyruğu uzunluğu: C sürücünüz için disk aktarım isteği uzunluğu. Değer, kısa bir süre için 2 ' den fazla olamaz.
   - Bellek (\*)\\kullanılabilir MBayt: sistem için megabayt cinsinden kullanılabilir bellek.
   - İşlemci bilgileri (\*)\\Işlemci zamanı: işlemcinin boş olmayan bir iş parçacığını yürütmek için harcadığı geçen sürenin yüzdesi.
   - Oturum başına kullanıcı girişi gecikmesi (\*)\\en fazla giriş gecikmesi

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Log Analytics çalışma alanınızdaki VM 'lere bağlanma

VM 'lerin sistem durumunu görüntüleyebilmek için Log Analytics bağlantısını etkinleştirmeniz gerekir. VM 'lerinizi bağlamak için şu adımları izleyin:

1. Bir tarayıcı açın ve [Azure Portal](https://portal.azure.com/) Yönetici hesabınızla oturum açın.
2. Log Analytics çalışma alanınıza gidin.
3. Sol bölmede, çalışma alanı veri kaynakları altında **sanal makineler**' i seçin.
4. Bağlanmak istediğiniz VM 'nin adını seçin.
5. **Bağlan**’ı seçin.

## <a name="deploy-the-diagnostics-tool"></a>Tanılama aracını dağıtma

Tanılama aracı için Azure Kaynak Yönetimi şablonunu dağıtmak için:

1.   [GitHub Azure RDS-templates sayfasına](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy)gidin.
2.  Şablonu Azure 'a dağıtın ve şablondaki yönergeleri izleyin. Aşağıdaki bilgilerin kullanılabilir olduğundan emin olun:

    -   İstemci kimliği
    -   İstemci parolası
    -   Log Analytics çalışma alanı kimliği

3.  Giriş parametreleri sağlandığında, hüküm ve koşulları kabul edin ve ardından **satın al**' ı seçin.

Dağıtım 2 – 3 dakika sürer. Dağıtım başarılı olduktan sonra kaynak grubuna gidin ve Web uygulaması ve App Service planı kaynaklarının orada olduğundan emin olun.

Bundan sonra yeniden yönlendirme URI 'sini ayarlamanız gerekir.

### <a name="set-the-redirect-uri"></a>Yeniden yönlendirme URI 'sini ayarla

Yeniden yönlendirme URI 'sini ayarlamak için:

1.  [Azure Portal](https://portal.azure.com/), **uygulama hizmetleri** ' ne gidin ve oluşturduğunuz uygulamayı bulun.
2.  Genel Bakış sayfasına gidin ve bulduğunuz URL 'YI kopyalayın.
3.  **Uygulama kayıtları** ' na gidin ve dağıtmak istediğiniz uygulamayı seçin.
4.  Sol bölmede, Yönet bölümünde, **kimlik doğrulaması**' nı seçin.
5.  **Yeniden YÖNLENDIRME URI** 'si metin kutusuna Istenen YENIDEN yönlendirme URI 'sini girin ve ardından menünün sol üst köşesindeki **Kaydet** ' i seçin.
6. Tür ' ün altındaki açılan menüde **Web** ' i seçin.
7. Uygulamaya genel bakış sayfasından URL 'YI girin ve sonuna **/Security/SignIn-callback** ekleyin. Örneğin: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![Yeniden yönlendirme URI 'SI sayfası](media/redirect-uri-page.png)

8. Şimdi Azure kaynaklarınıza giderek, şablonda belirttiğiniz ada sahip Azure App Services kaynağını seçin ve onunla ilişkili URL 'ye gidin. (Örneğin, şablonda kullandığınız uygulama adı `contosoapp45`, ilişkili URL 'niz <https://contosoapp45.azurewebsites.net>).
9. Uygun Azure Active Directory Kullanıcı hesabını kullanarak oturum açın.
10.   **Kabul Et**’i seçin.

## <a name="distribute-the-diagnostics-tool"></a>Tanılama aracını dağıtma

Tanılama aracını kullanıcılarınız için kullanılabilir hale gelmeden önce, aşağıdaki izinlere sahip olduklarından emin olun:

- Kullanıcıların Log Analytics için okuma erişimi olmalıdır. Daha fazla bilgi için bkz. [Azure izleyici ile roller, izinler ve güvenlik ile çalışmaya başlama](/azure/azure-monitor/platform/roles-permissions-security).
-  Kullanıcıların ayrıca Windows sanal masaüstü kiracısı (RDS okuyucu rolü) için okuma erişimine ihtiyacı vardır. Daha fazla bilgi için bkz. [Windows sanal masaüstü 'Nde temsilci erişimi](delegated-access-virtual-desktop.md).

Kullanıcılarınıza aşağıdaki bilgileri de vermeniz gerekir:

- Uygulamanın URL 'SI
- Tek tek kiracının erişebileceği kiracı grubu adları.

## <a name="use-the-diagnostics-tool"></a>Tanılama aracını kullanma

Kuruluşunuzda aldığınız bilgileri kullanarak hesabınızda oturum açtıktan sonra, için etkinlikleri sorgulamak istediğiniz kullanıcı için UPN 'yi hazırlayın. Bir arama, son hafta içinde gerçekleşen belirtilen etkinlik türü altında tüm etkinlikleri size verecektir.

### <a name="how-to-read-activity-search-results"></a>Etkinlik arama sonuçlarını okuma

Etkinlikler zaman damgasına göre sıralanır ve öncelikle en son etkinliklerdir. Sonuçlar bir hata döndürmezse, önce bir hizmet hatası olup olmadığını kontrol edin. Hizmet hataları için, sorunu ayıklamamıza yardımcı olması için etkinlik bilgileriyle bir destek bileti oluşturun. Diğer tüm hata türleri genellikle kullanıcı veya yönetici tarafından çözülebilir. En yaygın hata senaryolarının listesi ve bunları çözme hakkında bilgi için bkz. [sorunları belirleme ve tanılama](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Hizmet hataları, bağlantılı belgelerde "dış hatalar" olarak adlandırılır. Bu, PowerShell başvurusunu güncelleştirdiğimiz zaman değişecektir.

Bağlantı etkinliklerinin birden fazla hatası olabilir. Kullanıcı tarafından gelen diğer hataları görmek için etkinlik türünü genişletebilirsiniz. Hakkında daha fazla bilgi görmek için bir iletişim kutusu açmak üzere hata kodunun adını seçin.

### <a name="investigate-the-session-host"></a>Oturum konağını araştırın 

Arama sonuçlarında hakkında bilgi edinmek istediğiniz oturum konağını bulun ve seçin.

Oturum Ana bilgisayar sistem durumunu çözümleyebilirsiniz:

- Önceden tanımlanmış bir eşiğe göre, sorguları Log Analytics oturum ana bilgisayar sistem durumu bilgilerini alabilirsiniz.
- Etkinlik olmadığında veya oturum ana bilgisayarı Log Analytics bağlı olmadığında, bilgiler kullanılamaz.

Oturum ana bilgisayarındaki kullanıcılarla da etkileşim kurabilirsiniz:

- Oturumu kapatıp oturum açmış kullanıcılara bir ileti gönderebilirsiniz.
- İlk olarak aradığınız Kullanıcı varsayılan olarak seçilidir, ancak aynı zamanda ileti göndermek veya birden çok kullanıcıyı aynı anda imzalamak için ek kullanıcılar da seçebilirsiniz.

### <a name="windows-performance-counter-thresholds"></a>Windows performans sayacı eşikleri

- MantıksalDisk (\*) boş alan\\:

    - Boş olan mantıksal disk üzerindeki toplam kullanılabilir alan yüzdesini görüntüler.
    - Eşik: %20 ' den az, sağlıksız olarak işaretlendi.

- MantıksalDisk (C:)\\ortalama disk sırası uzunluğu:

    - Depolama sistemi koşullarını temsil eder.
    - Eşik: 5 ' ten yüksek, sağlıksız olarak işaretlendi.

- Bellek (\*) kullanılabilir MBayt\\:

    - Sistem için kullanılabilir bellek.
    - Eşik: 500 megabayttan az, sağlıksız olarak işaretlendi.

- İşlemci bilgileri (\*)\\Işlemci zamanı:

    - Eşik: %80 ' den yüksek, sağlıksız olarak işaretlendi.

- [Oturum başına kullanıcı girişi gecikmesi (\*)\\en fazla giriş gecikmesi](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters):

    - Eşik: 2000 MS 'den yüksek, sağlıksız olarak işaretlendi.
