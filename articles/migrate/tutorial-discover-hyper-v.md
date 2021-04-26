---
title: Azure geçişi bulma ve değerlendirme ile Hyper-V üzerindeki sunucuları bulma
description: Azure geçişi bulma ve değerlendirme aracı ile Hyper-V ' d e şirket içi sunucuları bulmayı öğrenin.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: f461778f988fafeacc480e100b00be7d4c165dfb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612526"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Öğretici: Azure geçişi: bulma ve değerlendirme ile Hyper-V üzerinde çalışan sunucuları bulma

Azure 'a geçiş sürecinizin bir parçası olarak şirket içi envanterinizi ve iş yüklerinizi keşfedeceksiniz.

Bu öğreticide, basit bir Azure geçişi gereci kullanarak Azure geçişi: bulma ve değerlendirme aracı ile Hyper-V konaklarındaki şirket içi sunucuları nasıl keşfedebileceğiniz gösterilmektedir. Cihazı, makine ve performans meta verilerini sürekli olarak bulacak şekilde Hyper-V konağına bir sunucu olarak dağıtırsınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure hesabı ayarlama
> * Hyper-V ortamını bulma için hazırlayın.
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
**Hyper-V konağı** | Sunucuların bulunduğu Hyper-V konakları tek başına veya bir kümede olabilir.<br/><br/> Konağın Windows Server 2019, Windows Server 2016 veya Windows Server 2012 R2 çalıştırması gerekir.<br/><br/> BT 'nin Genel Bilgi Modeli (CıM) oturumu kullanarak çekme sunucusu meta verileri ve performans verilerine bağlanabilmesi için WinRM bağlantı noktası 5985 ' de (HTTP) gelen bağlantılara izin verildiğini doğrulayın.
**Gereç dağıtımı** | Hyper-V ana bilgisayarı, Gereç için bir sunucu ayırmak üzere kaynaklara ihtiyaç duyuyor:<br/><br/> -16 GB RAM, 8 vCPU ve yaklaşık 80 GB disk depolaması.<br/><br/> -Bir dış sanal anahtar ve doğrudan ya da bir proxy aracılığıyla gereç üzerinde internet erişimi.
**Sunucular** | Sunucular herhangi bir Windows veya Linux işletim sistemi çalıştırıyor olabilir.

## <a name="prepare-an-azure-user-account"></a>Azure Kullanıcı hesabı hazırlama

Bir proje oluşturmak ve Azure geçişi gerecini kaydettirmek için, şu bir hesaba sahip olmanız gerekir:
- Azure aboneliğinde katkıda bulunan veya sahip izinleri.
- Azure Active Directory (AAD) uygulamalarını kaydetme izinleri.

Ücretsiz Azure hesabı oluşturduysanız aboneliğinizin sahibi siz olursunuz. Abonelik sahibi değilseniz, izinleri aşağıdaki şekilde atamak için sahibiyle birlikte çalışın:

1. Azure portal, "abonelikler" araması yapın ve **Hizmetler** altında **abonelikler**' i seçin.

    ![Azure aboneliğini aramak için arama kutusu](./media/tutorial-discover-hyper-v/search-subscription.png)

2. **Abonelikler** sayfasında, bir proje oluşturmak istediğiniz aboneliği seçin.
3. Abonelikte **erişim denetimi (IAM)**  >  **erişimi denetle**' yi seçin.
4. **Erişimi denetle**' de ilgili Kullanıcı hesabını arayın.
5. **Rol ataması Ekle**' de, **Ekle**' ye tıklayın.

    ![Erişimi denetlemek ve rol atamak için bir kullanıcı hesabı arayın](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. **Rol ataması Ekle**' de, katkıda bulunan veya sahip rolünü seçin ve hesabı (örneğimizde azmigrateuser) seçin. Daha sonra **Kaydet**'e tıklayın.

    ![Hesaba rol atamak için rol ataması Ekle sayfasını açar](./media/tutorial-discover-hyper-v/assign-role.png)

1. Gereci kaydettirmek için, Azure hesabınızın **AAD uygulamalarını kaydetme izinleri** olması gerekir.
1. Azure Portal ' de, **Azure Active Directory**  >  **kullanıcıları**  >  **Kullanıcı ayarları**' na gidin.
1. **Kullanıcı ayarları**' nda, Azure AD kullanıcılarının uygulamaları kaydedebildiğini doğrulayın (varsayılan olarak **Evet** ' e ayarlanır).

    ![Kullanıcıların Active Directory uygulamalar kaydedebildiğini Kullanıcı ayarlarında doğrula](./media/tutorial-discover-hyper-v/register-apps.png)

9. ' Uygulama kayıtları ' ayarlarının ' No ' olarak ayarlanması durumunda, gerekli izni atamak için kiracı/genel yönetici isteyin. Alternatif olarak, kiracı/genel yönetici, AAD uygulamasının kaydedilmesine izin vermek için **uygulama geliştirici** rolünü bir hesaba atayabilir. [Daha fazla bilgi edinin](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Hyper-V konakları hazırlama

Hyper-V konaklarının el ile veya bir komut dosyası kullanarak hazırlanabilirsiniz. Hazırlama adımları tabloda özetlenmiştir. Komut dosyası bu otomatik olarak hazırlar.

**Adım** | **Komut Dosyası** | **El ile**
--- | --- | ---
Konak gereksinimlerini doğrulama | Konağın desteklenen bir Hyper-V sürümü ve Hyper-V rolü çalıştığını denetler.<br/><br/>WinRM hizmetini etkinleştirilir ve konakta 5985 (HTTP) ve 5986 (HTTPS) bağlantı noktalarını (meta veri koleksiyonu için gereklidir) açar. | Konağın Windows Server 2019, Windows Server 2016 veya Windows Server 2012 R2 çalıştırması gerekir.<br/><br/> BT 'nin Genel Bilgi Modeli (CıM) oturumu kullanarak çekme sunucusu meta verileri ve performans verilerine bağlanabilmesi için WinRM bağlantı noktası 5985 ' de (HTTP) gelen bağlantılara izin verildiğini doğrulayın.<br/><br/> Betik, şu anda Ingilizce olmayan yerel ayarlara sahip konaklarda desteklenmiyor.  
PowerShell sürümünü doğrula | Betiği desteklenen bir PowerShell sürümünde çalıştırıp çalıştırdığınızı denetler. | Hyper-V konağında PowerShell sürüm 4,0 veya üstünü kullandığınızı denetleyin.
Hesap oluşturma | Hyper-V konağı üzerinde doğru izinlere sahip olduğunuzu doğrular.<br/><br/> Doğru izinlerle yerel bir kullanıcı hesabı oluşturmanıza olanak sağlar. | Seçenek 1: Hyper-V konak makinesine yönetici erişimi olan bir hesap hazırlayın.<br/><br/> 2. seçenek: bir yerel yönetici hesabı veya etki alanı yönetici hesabı hazırlayın ve hesabı şu gruplara ekleyin: uzak yönetim kullanıcıları, Hyper-V yöneticileri ve performans Izleyicisi kullanıcıları.
PowerShell uzaktan iletişimini etkinleştir | Azure geçişi gerecinin bir WinRM bağlantısı üzerinden konakta PowerShell komutları çalıştırabilmeleri için konakta PowerShell uzaktan iletişimini mümkün bir şekilde sunar. | Ayarlamak için, her bir konakta, yönetici olarak bir PowerShell konsolu açın ve şu komutu çalıştırın: ``` powershell Enable-PSRemoting -force ```
Hyper-V tümleştirme hizmetlerini ayarlama | Hyper-V tümleştirme hizmetlerinin konak tarafından yönetilen tüm sunucularda etkin olduğunu denetler. | Her sunucuda [Hyper-V tümleştirme hizmetlerini etkinleştirin](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) .<br/><br/> Windows Server 2003 çalıştırıyorsanız, [Bu yönergeleri izleyin](prepare-windows-server-2003-migration.md).
Sunucu diskleri uzak SMB paylaşımlarında bulunuyorsa kimlik bilgilerini devretmek | Temsilci kimlik bilgileri | CredSSP 'yi SMB paylaşımlarında disklerle çalıştıran konaklarda kimlik bilgilerini devretmek için bu komutu çalıştırın: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> Bu komutu, tüm Hyper-V konaklarında uzaktan çalıştırabilirsiniz.<br/><br/> Kümeye yeni konak düğümleri eklerseniz, bulma için otomatik olarak eklenir, ancak CredSSP 'yi el ile etkinleştirmeniz gerekir.<br/><br/> Gereci ayarlarken, [Bu uygulamayı gereç üzerinde etkinleştirerek](#delegate-credentials-for-smb-vhds)CredSSP ayarlamayı tamamlayacağız. 

### <a name="run-the-script"></a>Betiği çalıştırın

1. Betiği [Microsoft Indirme merkezi](https://aka.ms/migrate/script/hyperv)' nden indirin. Betik, Microsoft tarafından şifreli olarak imzalanır.
2. MD5 veya SHA256 karma dosyalarını kullanarak betik bütünlüğünü doğrulayın. Diyez etiketi değerleri aşağıda verilmiştir. Komut dosyasının karmasını oluşturmak için şu komutu çalıştırın:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Örnek kullanım: 

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Betik bütünlüğünü doğruladıktan sonra, bu PowerShell komutuyla birlikte betiği her Hyper-V konağında yükseltilmiş izinlerle çalıştırın:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Karma değerleri şunlardır:

**Karma** |  **Değer**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Proje ayarlama

Yeni bir proje ayarlayın.

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.
3. **Genel bakış** bölümünde **proje oluştur**' u seçin.
5. **Proje oluştur**' da Azure aboneliğinizi ve kaynak grubunuzu seçin. Yoksa, bir kaynak grubu oluşturun.
6. **Proje ayrıntıları**' nda projeyi oluşturmak istediğiniz proje adını ve coğrafi konumu belirtin. [Kamu](migrate-support-matrix.md#supported-geographies-public-cloud) ve [kamu bulutları](migrate-support-matrix.md#supported-geographies-azure-government)için desteklenen coğrafi lıkları gözden geçirin.

   ![Proje adı ve bölgesi için kutular](./media/tutorial-discover-hyper-v/new-project.png)

7. **Oluştur**’u seçin.
8. Projenin dağıtılması için birkaç dakika bekleyin. **Azure geçişi: bulma ve değerlendirme** aracı varsayılan olarak yeni projeye eklenir.

![Azure geçişi 'ni gösteren sayfa: bulma ve değerlendirme aracı varsayılan olarak eklendi](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Zaten bir proje oluşturduysanız, daha fazla sunucu bulmayı ve değerlendirmeyi yapmak üzere ek gereçlere kaydolmak için aynı projeyi kullanabilirsiniz. [Daha fazla bilgi](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Gereci ayarlama

Azure geçişi basit bir Azure geçiş gereci kullanır. Gereç sunucu bulma işlemini gerçekleştirir ve Azure geçişi 'ne sunucu yapılandırma ve performans meta verileri gönderir. Gereç, projeden indirilebilecek bir VHD dosyası dağıtarak ayarlanabilir.

> [!NOTE]
> Bir nedenden dolayı, şablonu kullanarak gereci ayarlayamazsınız, var olan bir Windows Server 2016 sunucusunda bir PowerShell betiği kullanarak bu ayarı yapabilirsiniz. [Daha fazla bilgi edinin](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).

Bu öğretici, Hyper-V ortamında çalışan bir sunucuda gereci aşağıda gösterildiği gibi ayarlar:

1. Portalda bir gereç adı sağlayın ve bir proje anahtarı oluşturun.
1. Azure portal sıkıştırılmış bir Hyper-V VHD 'sini indirin.
1. Gereci oluşturun ve Azure geçişi: bulma ve değerlendirme 'a bağlanıp bağlanamadığından emin olun.
1. Gereci ilk kez yapılandırın ve proje anahtarını kullanarak projeyi projeye kaydedin.

### <a name="1-generate-the-project-key"></a>1. proje anahtarı oluştur

1. **Geçiş hedefleri**  >  **sunucularında**  >  **Azure geçişi: bulma ve değerlendirme** bölümünde **bul**' u seçin.
2. Sunucuları **bul**' da  >  **sunucularınız sanallaştırılır mi?**, **Hyper-V ile Evet '** i seçin.
3. **1: proje anahtarı oluştur**' da, Azure geçiş gereci için sunucu keşfi için ayarlayabileceğiniz bir ad sağlayın. Ad 14 karakter veya daha kısa bir harf olmalıdır.
1. Gerekli Azure kaynaklarını oluşturmaya başlamak için **anahtar oluştur** ' a tıklayın. Lütfen kaynak oluşturma sırasında sunucu bulma sayfasını kapatmayın.
1. Azure kaynakları başarıyla oluşturulduktan sonra bir **Proje anahtarı** oluşturulur.
1. Yapılandırma sırasında gereç kaydını tamamlamamak için gerekli olacak şekilde anahtarı kopyalayın.

### <a name="2-download-the-vhd"></a>2. VHD 'YI indirin

**2: Azure geçişi yükleme gereci indirin**, öğesini seçin. VHD dosyası ve **İndir**' e tıklayın.

### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtmadan önce daraltılmış dosyanın güvenli olduğunu denetleyin.

1. Dosyayı indirdiğiniz makinede yönetici komut penceresi açın.

2. ZIP dosyasının karmasını oluşturmak için aşağıdaki PowerShell komutunu çalıştırın
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Örnek kullanım: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  En son gereç sürümlerini ve karma değerlerini doğrulayın:

    - Azure genel bulutu için:

        **Senaryo** | **İndir** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d61360402324dc8d8a14f47772da351b46d9127

    - Azure Kamu için:

        **Senaryo** _ | _ *İndir** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3. gereç oluşturma

İndirilen dosyayı içeri aktarın ve bir gereç oluşturun.

1. Sıkıştırılmış VHD dosyasını, gereci barındıracak Hyper-V konağındaki bir klasöre ayıklayın. Üç klasör ayıklanır.
2. Hyper-V Yöneticisi'ni açın. **Eylemler**' de **sanal makineyi içeri aktar**' a tıklayın.
2. Sanal makineyi Içeri aktarma sihirbazında > **başlamadan önce** **İleri**' ye tıklayın.
3. **Klasörü bul**' da ayıklanan VHD 'yi içeren klasörü belirtin. Ardından **İleri**'ye tıklayın.
1. **Sanal makine seç** bölümünde **İleri**' ye tıklayın.
2. **Içeri aktarma türünü seçin** bölümünde **sanal makineyi Kopyala (yenı bir benzersiz kimlik oluştur)** seçeneğine tıklayın. Ardından **İleri**'ye tıklayın.
3. **Hedef Seç** bölümünde varsayılan ayarı bırakın. **İleri**’ye tıklayın.
4. **Depolama klasörlerinde** varsayılan ayarı bırakın. **İleri**’ye tıklayın.
5. **Ağ seçin** bölümünde, gerecin kullanacağı sanal anahtarı belirtin. Anahtar, verileri Azure 'a göndermek için internet bağlantısı gerektirir.
6. **Özet** bölümünde ayarları gözden geçirin. Ardından, **Son**'a tıklayın.
7. Hyper-V Yöneticisi 'nde **sanal makineler**> gereç başlatın.

### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gerecin [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu](migrate-appliance.md#government-cloud-urls) bulutları için Azure URL 'lerine bağlanabildiğinizden emin olun.

### <a name="4-configure-the-appliance"></a>4. gereci yapılandırma

Gereci ilk kez ayarlayın.

> [!NOTE]
> Gereci indirilen VHD yerine bir [PowerShell betiği](deploy-appliance-script.md) kullanarak ayarlarsanız, bu yordamın ilk iki adımı ilgili değildir.

1. Hyper-V Yöneticisi 'nde **sanal makineler**>, **bağlantı**> gereç öğesine sağ tıklayın.
2. Gereç için dil, saat dilimi ve parola sağlayın.
3. Gereç ile bağlanabilecek herhangi bir makinede bir tarayıcı açın ve gereç Web uygulamasının URL 'sini açın: **https://*Gereç adı veya IP adresi*: 44368**.

   Alternatif olarak, uygulama kısayoluna tıklayarak uygulamayı gereç masaüstünden açabilirsiniz.
1. **Lisans koşullarını** kabul edin ve üçüncü taraf bilgilerini okuyun.
1. **Önkoşulları ayarlamak**> Web uygulamasında şunları yapın:
    - **Bağlantı**: uygulama, gerecin internet erişimi olup olmadığını denetler. Gereç bir proxy kullanıyorsa:
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
4. Günlüğe kaydetme için kullanılan Azure Kullanıcı hesabının, anahtar üretimi sırasında oluşturulan Azure kaynakları üzerinde doğru izinleri varsa, Gereç kaydı başlatılır.
1. Gereç başarıyla kaydedildikten sonra, **Ayrıntıları görüntüle**' ye tıklayarak kayıt ayrıntılarına bakabilirsiniz.

### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD 'leri için temsilci kimlik bilgileri

SMB 'lerde VHD 'ler çalıştırıyorsanız, Gereç üzerinden Hyper-V konaklarına kimlik bilgileri temsilcisini etkinleştirmeniz gerekir. Bunu gereç içinden yapmak için:

1. Gereç üzerinde bu komutu çalıştırın. HyperVHost1/HyperVHost2, örnek ana bilgisayar adlarıdır.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Alternatif olarak, bunu gereç üzerindeki Yerel Grup İlkesi Düzenleyicisi yapın:
    - **Yerel bilgisayar ilkesi**  >  **bilgisayar yapılandırması**' nda, **Yönetim Şablonları**  >  **sistem**  >  **kimlik bilgileri temsili**' ne tıklayın.
    - **Yeni kimlik bilgileri aktarımına Izin ver**' e çift tıklayın ve **etkin**' i seçin.
    - **Seçenekler**' de **göster**' e tıklayın ve bulmayı Istediğiniz her Hyper-V konağını, **WSMan/** as öneki ile listeye ekleyin.
    - **Kimlik bilgileri temsilcisi** içinde, **yalnızca NTLM sunucu kimlik doğrulamasıyla yeni kimlik bilgileri aktarmaya izin ver**' e çift tıklayın. Daha sonra, bulmayı istediğiniz her Hyper-V konağını, **WSMan/** as öneki ile listeye ekleyin.

## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

Gerecden Hyper-V konaklarına veya kümelerine bağlanın ve sunucu bulmayı başlatın.

1. **1. Adım: Hyper-V konağı kimlik bilgilerini sağlayın** bölümünde kimlik bilgileri için kolay bir ad belirtmek üzere **kimlik bilgileri ekle** ' ye tıklayın, gerecin sunucuları bulmaya yönelik kullanacağı bir Hyper-v Konağı/kümesi Için **Kullanıcı adı** ve **parola** ekleyin. **Kaydet**'e tıklayın.
1. Aynı anda birden çok kimlik bilgisi eklemek istiyorsanız, kaydetmek için **daha fazla Ekle** ' ye tıklayın ve daha fazla kimlik bilgisi ekleyin. Hyper-V ortamındaki sunucuları bulmak için birden çok kimlik bilgisi desteklenir.
1. **2. Adım: Hyper-v Konağı/kümesi ayrıntılarını sağlama** bölümünde, Hyper-v Konağı/kümesi **IP adresini/FQDN** 'yi ve konağa/kümeye bağlanacak kimlik bilgileri için kolay adı belirtmek üzere **bulma kaynağı Ekle** ' ye tıklayın.
1. **Tek seferde tek bir öğe ekleyebilir** veya tek bir go içinde **birden fazla öğe ekleyebilirsiniz** . Ayrıca Hyper-V konağı/kümesi ayrıntılarını **Içeri aktarma CSV** aracılığıyla sağlama seçeneği de vardır.

    - **Tek öğe Ekle**' yi seçerseniz, kimlik bilgileri ve Hyper-V konağı/kümesi **IP adresi/FQDN** için kolay ad belirtmeniz ve **Kaydet**' e tıklamanız gerekir.
    - **Birden çok öğe Ekle** _(varsayılan olarak seçilidir)_ seçeneğini belirlerseniz, metin kutusunda kimlik bilgileri Için kolay ad ile Hyper-V konağı/KÜMESI **IP adresi/FQDN** belirterek birden çok kayıt ekleyebilirsiniz. Eklenen kayıtları **doğrulayın** ve **Kaydet**' e tıklayın.
    - **CSV 'Yi Içeri aktar**' ı SEÇERSENIZ bir CSV şablon dosyası indirebilir, dosyayı Hyper-V konağı/kümesi **IP adresi/FQDN** ve kimlik bilgileri için kolay ad ile doldurabilirsiniz. Sonra dosyayı gereç içine aktarır, dosyadaki kayıtları **doğrulayın** ve **Kaydet**' e tıklayın.

1. Kaydet 'e tıkladığınızda gereç, eklenen Hyper-V konaklarına/kümelerine bağlantıyı doğrulamaya çalışır ve her konak/küme için tablodaki **doğrulama durumunu** gösterir.
    - Başarıyla doğrulanan konaklar/kümeler için IP adreslerine/FQDN 'ye tıklayarak daha fazla ayrıntı görüntüleyebilirsiniz.
    - Bir konakta doğrulama başarısız olursa, tablonun durum sütununda **doğrulama** ' ya tıklayarak hatayı gözden geçirin. Sorunu düzeltin ve tekrar doğrulayın.
    - Konakları veya kümeleri kaldırmak için **Sil**'e tıklayın.
    - Belirli bir konağı kümeden kaldıramazsınız. Tüm kümeyi yalnızca çıkarabilirsiniz.
    - Kümedeki belirli konaklarla ilgili sorunlar olsa bile bir küme ekleyebilirsiniz.
1. Keşfi başlatmadan önce her zaman konaklara/kümelere bağlantıyı **yeniden doğrulayabilirsiniz** .
1. Başarıyla doğrulanan konaklardan/kümelerden sunucu bulmayı **başlatmak için bulmayı Başlat**' a tıklayın. Bulma işlemi başarılı bir şekilde başlatıldıktan sonra, tablodaki her bir konağa/kümeye yönelik bulma durumunu denetleyebilirsiniz.

Bu, bulmayı başlatır. Bulunan sunucuların meta verilerinin Azure portal görünmesi için ana bilgisayar başına yaklaşık 2 dakika sürer.

## <a name="verify-servers-in-the-portal"></a>Portalda sunucuları doğrulama

Bulma işlemi tamamlandıktan sonra, sunucuların portalda göründüğünü doğrulayabilirsiniz.

1. Azure Geçişi panosunu açın.
2. **Azure geçişi-sunucular**  >  **Azure geçişi: bulma ve değerlendirme** sayfasında, **bulunan sunucuların** sayısını görüntüleyen simgeye tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure VM 'lerine geçiş için [Hyper-V ortamındaki sunucuları değerlendirin](tutorial-assess-hyper-v.md) .
- Bulma sırasında gereç tarafından toplanan [verileri gözden geçirin](migrate-appliance.md#collected-data---hyper-v) .