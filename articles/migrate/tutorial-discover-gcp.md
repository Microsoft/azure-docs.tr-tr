---
title: Azure geçişi bulma ve değerlendirmesi ile GCP örneklerinde sunucuları bulma
description: Azure geçişi bulma ve değerlendirmesi ile GCP üzerinde sunucuları bulmayı öğrenin.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/13/2021
ms.custom: mvc
ms.openlocfilehash: c5d57705ca0d49db1fb1d67e20beb609f21b1d5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771439"
---
# <a name="tutorial-discover-google-cloud-platform-gcp-instances-with-azure-migrate-discovery-and-assessment"></a>Öğretici: Azure geçişi ile Google Cloud Platform (GCP) örneklerini bulma: bulma ve değerlendirme

Azure 'a geçiş yolculuğunun bir parçası olarak, sunucularınızı değerlendirme ve geçiş için keşfedeceksiniz.

Bu öğreticide, basit bir Azure geçişi gereci kullanarak Azure geçişi: bulma ve değerlendirme aracı ile Google Cloud Platform (GCP) örneklerinin nasıl keşfedilmesi gösterilmektedir. Makine ve performans meta verilerini sürekli olarak saptamak için gereci GCP üzerinde bir sunucuya dağıtırsınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir Azure hesabı ayarlayın.
> * Sunucu, bulma için GCP üzerinde hazırlanıyor.
> * Bir proje oluşturun.
> * Azure geçişi gereci ayarlayın.
> * Sürekli bulmayı başlatın.

> [!NOTE]
> Öğreticiler, bir senaryoyu denemek için en hızlı yolu gösterir ve varsayılan seçenekleri kullanır.  

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, bu önkoşulların yerinde olup olmadığını kontrol edin.

**Gereksinim** | **Ayrıntılar**
--- | ---
**Elektrikli** | Azure geçişi gerecini çalıştırmak için GCP üzerinde bir sunucuya ihtiyacınız vardır. Makine şunları içermelidir:<br/><br/> -Windows Server 2016 yüklendi.<br/> Gereci _Windows Server 2019 ile bir makinede çalıştırmak desteklenmez_.<br/><br/> -16 GB RAM, 8 vCPU, 80 GB disk depolaması ve harici bir sanal anahtar.<br/><br/> -Doğrudan veya bir ara sunucu üzerinden internet erişimi olan statik veya dinamik bir IP adresi.
**Windows Server örnekleri** | BT 'nin yapılandırma ve performans meta verilerini çekebilmesi için WinRM bağlantı noktası 5985 (HTTP) üzerinde gelen bağlantılara izin verin.
**Linux sunucu örnekleri** | 22 (TCP) numaralı bağlantı noktasında gelen bağlantılara izin verin.

## <a name="prepare-an-azure-user-account"></a>Azure Kullanıcı hesabı hazırlama

Bir proje oluşturmak ve Azure geçişi gerecini kaydettirmek için, şu bir hesaba sahip olmanız gerekir:

* Azure aboneliğinde katkıda bulunan veya sahip izinleri.
* Azure Active Directory (AAD) uygulamalarını kaydetme izinleri.

Ücretsiz Azure hesabı oluşturduysanız aboneliğinizin sahibi siz olursunuz. Abonelik sahibi değilseniz, izinleri aşağıdaki şekilde atamak için sahibiyle birlikte çalışın:

1. Azure portal, "abonelikler" araması yapın ve **Hizmetler** altında **abonelikler**' i seçin.

    ![Azure aboneliğini aramak için arama kutusu](./media/tutorial-discover-gcp/search-subscription.png)

2. **Abonelikler** sayfasında, bir proje oluşturmak istediğiniz aboneliği seçin.
3. Abonelikte **erişim denetimi (IAM)**  >  **erişimi denetle**' yi seçin.
4. **Erişimi denetle**' de ilgili Kullanıcı hesabını arayın.
5. **Rol ataması Ekle**' de, **Ekle**' ye tıklayın.

    ![Erişimi denetlemek ve rol atamak için bir kullanıcı hesabı arayın](./media/tutorial-discover-gcp/azure-account-access.png)

6. **Rol ataması Ekle**' de, katkıda bulunan veya sahip rolünü seçin ve hesabı (örneğimizde azmigrateuser) seçin. Daha sonra **Kaydet**'e tıklayın.

    ![Hesaba rol atamak için rol ataması Ekle sayfasını açar](./media/tutorial-discover-gcp/assign-role.png)

1. Gereci kaydettirmek için, Azure hesabınızın **AAD uygulamalarını kaydetme izinleri** olması gerekir.
1. Azure Portal ' de, **Azure Active Directory**  >  **kullanıcıları**  >  **Kullanıcı ayarları**' na gidin.
1. **Kullanıcı ayarları**' nda, Azure AD kullanıcılarının uygulamaları kaydedebildiğini doğrulayın (varsayılan olarak **Evet** ' e ayarlanır).

    ![Kullanıcıların Active Directory uygulamalar kaydedebildiğini Kullanıcı ayarlarında doğrula](./media/tutorial-discover-gcp/register-apps.png)

1. ' Uygulama kayıtları ' ayarlarının ' No ' olarak ayarlanması durumunda, gerekli izni atamak için kiracı/genel yönetici isteyin. Alternatif olarak, kiracı/genel yönetici, AAD uygulamasının kaydedilmesine izin vermek için **uygulama geliştirici** rolünü bir hesaba atayabilir. [Daha fazla bilgi edinin](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-gcp-instances"></a>GCP örneklerini hazırlama

Yöneticinin GCP üzerindeki sunuculara erişmek için kullanabileceği bir hesap ayarlayın.

* **Windows sunucuları** için:
    * Etki alanına katılmış olmayan sunucularda yerel bir kullanıcı hesabı ve bulma sırasında dahil etmek istediğiniz etki alanına katılmış sunucularda bir etki alanı hesabı ayarlayın. Kullanıcı hesabını aşağıdaki gruplara ekleyin: 
        * Uzaktan Yönetim Kullanıcıları
        * Performans Izleyicisi kullanıcıları
        * Performans günlüğü kullanıcıları.
* **Linux sunucuları** için:
    * Bulunmasını istediğiniz Linux sunucularında bir kök hesabınız olması gerekir. Kök hesabı sağlayasağlamazsanız, alternatif olarak [destek matrisindeki](migrate-support-matrix-physical.md#physical-server-requirements) yönergelere bakın.
    * Azure geçişi AWS örneklerini keşfederken parola kimlik doğrulamasını kullanır. AWS örnekleri varsayılan olarak parola kimlik doğrulamasını desteklemez. Örneği keşfedebilmeniz için önce parola kimlik doğrulamasını etkinleştirmeniz gerekir.
        1. Her bir Linux makinesinde oturum açın.
        2. Sshd_config dosyasını açın: VI/etc/ssh/sshd_config
        3. Dosyasında, **Passwordaduthentication** satırını bulun ve değeri **Evet** olarak değiştirin.
        4. Dosyayı kaydedin ve kapatın. SSH hizmetini yeniden başlatın.
    * Linux sunucularınızı keşfettiğiniz bir kök Kullanıcı kullanıyorsanız, sunucularda kök oturum açmaya izin verildiğinden emin olun.
        1. Her Linux makinesinde oturum açın
        2. Sshd_config dosyasını açın: VI/etc/ssh/sshd_config
        3. Dosyasında, **PermitRootLogin** satırını bulun ve değeri **Evet** olarak değiştirin.
        4. Dosyayı kaydedin ve kapatın. SSH hizmetini yeniden başlatın.

## <a name="set-up-a-project"></a>Proje ayarlama

Yeni bir proje ayarlayın.

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.
3. **Genel bakış** bölümünde **proje oluştur**' u seçin.
4. **Proje oluştur**' da Azure aboneliğinizi ve kaynak grubunuzu seçin. Yoksa, bir kaynak grubu oluşturun.
5. **Proje ayrıntıları**' nda projeyi oluşturmak istediğiniz proje adını ve coğrafi konumu belirtin. [Kamu](migrate-support-matrix.md#supported-geographies-public-cloud) ve [kamu bulutları](migrate-support-matrix.md#supported-geographies-azure-government)için desteklenen coğrafi lıkları gözden geçirin.

   ![Proje adı ve bölgesi için kutular](./media/tutorial-discover-gcp/new-project.png)

6. **Oluştur**’u seçin.
7. Projenin dağıtılması için birkaç dakika bekleyin. **Azure geçişi: bulma ve değerlendirme** aracı varsayılan olarak yeni projeye eklenir.

![Varsayılan olarak eklenen sunucu değerlendirmesi aracını gösteren sayfa](./media/tutorial-discover-gcp/added-tool.png)

> [!NOTE]
> Zaten bir proje oluşturduysanız, daha fazla sunucu bulmayı ve değerlendirmeyi yapmak üzere ek gereçlere kaydolmak için aynı projeyi kullanabilirsiniz. [Daha fazla bilgi](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Gereci ayarlama

Azure geçişi gereci, aşağıdakileri yapmak için Azure geçişi: bulma ve değerlendirme tarafından kullanılan hafif bir gereç:

* Şirket içi sunucuları bulun.
* Bulunan sunucular için meta verileri ve performans verilerini Azure geçişi: bulma ve değerlendirme 'a gönderin.

Azure geçişi gereci hakkında [daha fazla bilgi edinin](migrate-appliance.md) .

Gereci kurmak için şunları yapın:

1. Portalda bir gereç adı sağlayın ve bir proje anahtarı oluşturun.
1. Azure geçişi yükleyicisi komut dosyasıyla Azure portal sıkıştırılmış bir dosyayı indirin.
1. Sıkıştırılmış dosyadan içerikleri ayıklayın. Yönetim ayrıcalıklarıyla PowerShell konsolunu başlatın.
1. Gereç Web uygulamasını başlatmak için PowerShell betiğini yürütün.
1. Gereci ilk kez yapılandırın ve proje anahtarını kullanarak projeyi projeye kaydedin.

### <a name="1-generate-the-project-key"></a>1. proje anahtarı oluştur

1. **Geçiş hedefleri**  >  **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme** bölümünde **bul**' u seçin.
2. Sunucuları **bul**' da  >  **sunucularınız sanallaştırılır mi?**, **fiziksel veya diğer (AWS, GCP, Xen, vb.)** öğesini seçin.
3. **1: proje anahtarı oluştur**' da, GCP sanal sunucularınızın bulunması Için ayarladığınız Azure geçiş gereci için bir ad sağlayın. Ad 14 karakter veya daha kısa bir harf olmalıdır.
4. Gerekli Azure kaynaklarını oluşturmaya başlamak için **anahtar oluştur** ' a tıklayın. Kaynak oluşturma sırasında sunucuları keşfet sayfasını kapatmayın.
5. Azure kaynakları başarıyla oluşturulduktan sonra bir **Proje anahtarı** oluşturulur.
6. Yapılandırma sırasında gereç kaydını tamamlamamak için gerekli olacak şekilde anahtarı kopyalayın.

### <a name="2-download-the-installer-script"></a>2. yükleyici betiğini indirin

**2: Azure geçişi yükleme gereci indirin** ve **İndir**' e tıklayın.

### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz makinede yönetici komut penceresi açın.
2. Daraltılmış dosyanın karmasını oluşturmak için aşağıdaki komutu çalıştırın:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Genel bulut için örnek kullanım: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Kamu Bulutu için örnek kullanım: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  En son gereç sürümlerini ve karma değerlerini doğrulayın:
    - Genel bulut için:

        **Senaryo** | **İndir** | **Karma değeri**
        --- | --- | ---
        Fiziksel (85 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140334) | ce5e6f0507936def8020eb7b3109173dad60fc51dd39c3bd23099bc9baaabe29

    - Azure Kamu için:

        **Senaryo** | **İndir** | **Karma değeri**
        --- | --- | ---
        Fiziksel (85 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140338) | ae132ebc574caf231bf41886891040ffa7abbe150c8b50436818b69e58622276
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Azure geçişi yükleyici betiğini çalıştırın
Yükleyici betiği şunları yapar:

- GCP sunucu keşfi ve değerlendirmesi için aracıları ve bir Web uygulamasını kurar.
- Windows etkinleştirme hizmeti, IIS ve PowerShell ıSE dahil Windows rollerini yükler.
- Bir IIS yeniden yazılabilir modülünü indirip yükler. [Daha fazla bilgi edinin](https://www.microsoft.com/download/details.aspx?id=7435).
- Azure geçişi için kalıcı ayar ayrıntılarıyla bir kayıt defteri anahtarını (HKLM) güncelleştirir.
- Yolun altında aşağıdaki dosyaları oluşturur:
    - **Yapılandırma dosyaları**:%ProgramData%\Microsoft Azure\Config
    - **Günlük dosyaları**:%ProgramData%\Microsoft Azure\Logs

Betiği aşağıdaki gibi çalıştırın:

1. Sıkıştırılmış dosyayı, Gereç barındıracak sunucuda bir klasöre ayıklayın.  Betiği mevcut bir Azure geçişi gereci üzerinde bir makinede çalıştırmayın emin olun.
2. Yönetim (yükseltilmiş) ayrıcalığıyla yukarıdaki sunucuda PowerShell 'i başlatın.
3. PowerShell dizinini, indirilen sıkıştırılmış dosyadan içeriğin ayıklandığı klasör olarak değiştirin.
4. Aşağıdaki komutu çalıştırarak **AzureMigrateInstaller.ps1** adlı betiği çalıştırın:

    - Genel bulut için: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Azure Kamu için: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Betik, başarıyla tamamlandığında gereç Web uygulamasını başlatacaktır.

Herhangi bir sorun yaşıyorsanız, sorun giderme için C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>timestamp</em>. log dosyasına komut dosyası günlüklerine erişebilirsiniz.

### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gerecin [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu](migrate-appliance.md#government-cloud-urls) bulutları için Azure URL 'lerine bağlanabildiğinizden emin olun.

### <a name="4-configure-the-appliance"></a>4. gereci yapılandırma

Gereci ilk kez ayarlayın.

1. Gereç ile bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Web uygulamasının URL 'sini açın: **https://*Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayoluna tıklayarak uygulamayı masaüstünden açabilirsiniz.
2. **Lisans koşullarını** kabul edin ve üçüncü taraf bilgilerini okuyun.
1. **Önkoşulları ayarlamak**> Web uygulamasında şunları yapın:
    - **Bağlantı**: uygulama, sunucunun internet erişimi olup olmadığını denetler. Sunucu bir proxy kullanıyorsa:
        - **Ara sunucu** ' ya tıklayın ve proxy adresini (form http://ProxyIPAddress veya http://ProxyFQDN) dinleme bağlantı noktası) belirtin.
        - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
        - Yalnızca HTTP proxy’si desteklenir.
        - Proxy ayrıntıları eklediyseniz veya proxy ve/veya kimlik doğrulamasını devre dışı bırakırsanız, bağlantıyı tetiklemek için **Kaydet** 'e tıklayarak bağlantı denetimini yeniden başlatın.
    - **Zaman eşitleme**: Saat doğrulandı. Sunucu bulmanın düzgün çalışması için gereç saatinin internet ile eşitlenmiş olması gerekir.
    - **Güncelleştirmeleri yükleme**: Azure geçişi: bulma ve değerlendirme, gerecin en son güncelleştirmelerin yüklü olduğunu denetler. Denetim tamamlandıktan sonra gereç **hizmetlerini görüntüle** ' ye tıklayarak gereç üzerinde çalışan bileşenlerin durumunu ve sürümlerini görebilirsiniz.

### <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. Portaldan kopyalanmış **Proje anahtarını** yapıştırın. Anahtarınız yoksa **Azure geçişi: bulma ve değerlendirme> var olan gereçlerini bulmak> yönetmek** için, anahtar oluşturma sırasında verdiğiniz gereç adını seçin ve karşılık gelen anahtarı kopyalayın.
1. Azure ile kimlik doğrulaması yapmak için bir cihaz koduna ihtiyacınız olacaktır. **Oturum** açmak için tıkladığınızda, aşağıda gösterildiği gibi cihaz koduyla kalıcı olarak açılır.

    ![Cihaz kodunu kalıcı olarak gösterme](./media/tutorial-discover-vmware/device-code.png)

1. Cihaz kodunu kopyalamak ve yeni bir tarayıcı sekmesinde bir Azure oturum açma istemi açmak için **kodu kopyala & oturum** aç ' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
1. Yeni sekmede, Azure Kullanıcı adınızı ve parolanızı kullanarak cihaz kodunu yapıştırın ve oturum açın.
   
   PIN ile oturum açma desteklenmez.
3. Oturum açmadan oturum açma sekmesini yanlışlıkla kapatırsanız, oturum açma düğmesini yeniden etkinleştirmek için gereç Yapılandırma Yöneticisi 'nin tarayıcı sekmesini yenilemeniz gerekir.
1. Başarıyla oturum açtıktan sonra, Gereç Yapılandırma Yöneticisi ile önceki sekmeye geri dönün.
4. Günlüğe kaydetme için kullanılan Azure Kullanıcı hesabının, anahtar üretimi sırasında oluşturulan Azure kaynakları üzerinde doğru [izinleri](#prepare-an-azure-user-account) varsa, Gereç kaydı başlatılır.
5. Gereç başarıyla kaydedildikten sonra, **Ayrıntıları görüntüle**' ye tıklayarak kayıt ayrıntılarına bakabilirsiniz.

## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

Şimdi, Gereç üzerinden keşfedilecek GCP sunucularına bağlanın ve bulmayı başlatın.

1. **1. Adım: Windows ve Linux fiziksel veya sanal sunucularının bulunması için kimlik bilgilerini sağlayın** bölümünde **kimlik bilgileri ekle**' ye tıklayın.
1. Windows Server için, kaynak türünü **Windows Server** olarak seçin, kimlik bilgileri için kolay bir ad belirtin, Kullanıcı adını ve parolayı ekleyin. **Kaydet**'e tıklayın.
1. Linux sunucusu için parola tabanlı kimlik doğrulaması kullanıyorsanız, Linux sunucusu olarak kaynak türünü **(parola tabanlı)** seçin, kimlik bilgileri için kolay bir ad belirtin, Kullanıcı adını ve parolayı ekleyin. **Kaydet**'e tıklayın.
1. Linux sunucusu için SSH anahtar tabanlı kimlik doğrulaması kullanıyorsanız, Linux sunucusu olarak kaynak türünü seçebilirsiniz **(SSH anahtar tabanlı)**, kimlik bilgileri için kolay bir ad belirtebilir, Kullanıcı adını ekleyebılır, SSH özel anahtar dosyasına gözatıp seçebilirsiniz. **Kaydet**'e tıklayın.

    - Azure geçişi, RSA, DSA, ECDSA ve Ed25519 algoritmalarını kullanarak SSH-keygen komutu tarafından oluşturulan SSH özel anahtarını destekler.
    - Şu anda Azure geçişi, parola tabanlı SSH anahtarını desteklemez. Parola olmadan bir SSH anahtarı kullanın.
    - Şu anda Azure geçişi, PuTTY tarafından oluşturulan SSH özel anahtar dosyasını desteklemez.
    - Azure geçişi, aşağıda gösterildiği gibi SSH özel anahtar dosyasının OpenSSH biçimini destekler:
    
    ![SSH özel anahtarının desteklediği biçim](./media/tutorial-discover-physical/key-format.png)


2. Aynı anda birden çok kimlik bilgisi eklemek istiyorsanız, kaydetmek için **daha fazla Ekle** ' ye tıklayın ve daha fazla kimlik bilgisi ekleyin. 
3. **2. Adım: fiziksel veya sanal sunucu ayrıntılarını sağlayın** sayfasında **bulma kaynağı Ekle** ' ye tıklayarak sunucu **IP adresini/FQDN** 'yi ve sunucuya bağlanacak kimlik bilgileri için kolay adı belirtin.
4. **Tek seferde tek bir öğe ekleyebilir** veya tek bir go içinde **birden fazla öğe ekleyebilirsiniz** . Ayrıca, **Içeri aktarma CSV** aracılığıyla sunucu ayrıntılarını sağlamak için bir seçenek de vardır.

    - **Tek öğe Ekle**' yi seçerseniz, işletim sistemi türünü seçebilir, kimlik bilgileri için kolay ad belirtebilir, sunucu **IP adresi/FQDN** ekleyebilir ve **Kaydet**' e tıklayabilirsiniz.
    - **Birden çok öğe Ekle**' yi seçerseniz, metin kutusunda kimlik bilgileri için kolay ada sahip sunucu **IP adresi/FQDN** belirterek, bir kerede birden çok kayıt ekleyebilirsiniz. * * Eklenen kayıtlar ' ı doğrulayın ve **Kaydet**' e tıklayın.
    - **CSV 'Yi Içeri aktar** ' ı _(varsayılan olarak seçilidir)_ seçerseniz, bir CSV şablon dosyası Indirebilir, dosyayı sunucu **IP adresi/FQDN** ve kimlik bilgileri için kolay ad ile doldurabilirsiniz. Sonra dosyayı gereç içine aktarır, dosyadaki kayıtları **doğrulayın** ve **Kaydet**' e tıklayın.

5. Kaydet ' e tıkladığınızda, Gereç eklenen sunucularla bağlantıyı doğrulamayı dener ve tablodaki **doğrulama durumunu** her bir sunucuya göre gösterir.
    - Sunucu için doğrulama başarısız olursa, tablonun durum sütununda **doğrulama** ' ya tıklayarak hatayı gözden geçirin. Sorunu düzeltin ve tekrar doğrulayın.
    - Bir sunucuyu kaldırmak için **Sil**' e tıklayın.
6. Keşfi başlatmadan önce sunuculara olan bağlantıyı **yeniden doğrulayabilirsiniz** .
7. Başarıyla doğrulanan sunucuları bulmayı **başlatmak için bulmayı Başlat**' a tıklayın. Bulma işlemi başarılı bir şekilde başlatıldıktan sonra, tablodaki her bir sunucu için bulma durumunu denetleyebilirsiniz.


Bu, bulmayı başlatır. Sunucu başına, bulunan sunucunun meta verilerinin Azure portal görünmesi yaklaşık 2 dakika sürer.

## <a name="verify-servers-in-the-portal"></a>Portalda sunucuları doğrulama

Bulma işlemi tamamlandıktan sonra, sunucuların portalda göründüğünü doğrulayabilirsiniz.

1. Azure Geçişi panosunu açın.
2. **Azure geçişi-Windows, Linux ve SQL sunucuları**  >  **Azure geçişi: bulma ve değerlendirme** sayfasında, **bulunan sunucuların** sayısını görüntüleyen simgeye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

* Azure VM 'lerine geçiş için [GCP sunucularını değerlendirin](tutorial-assess-gcp.md) .
* Bulma sırasında gereç tarafından toplanan [verileri gözden geçirin](migrate-appliance.md#collected-data---physical) .
