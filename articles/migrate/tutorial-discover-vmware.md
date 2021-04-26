---
title: Azure geçişi bulma ve değerlendirmesi ile VMware ortamında çalışan sunucuları bulma
description: Azure geçişi bulma ve değerlendirme aracı ile VMware ortamında çalışan şirket içi sunucuları bulmayı öğrenin
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: 09b04c67519bfa920a3781612823c5755cbc6d2d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627804"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>Öğretici: Azure geçişi: bulma ve değerlendirme ile VMware ortamında çalışan sunucuları bulma

Azure 'a geçiş sürecinizin bir parçası olarak şirket içi envanterinizi ve iş yüklerinizi keşfedeceksiniz.

Bu öğreticide, basit bir Azure geçişi gereci kullanarak VMware ortamında çalışan sunucuları Azure geçişi: bulma ve değerlendirme aracı ile nasıl keşfedebileceğiniz gösterilmektedir. Sunucu, sunucu bağımlılıkları ve SQL Server örnekleri ve veritabanları için sürekli olarak sunucu ve performans meta verilerini, sunucular üzerinde çalışan uygulamaları, sunucu bağımlılıklarını ve örneklerini ve veritabanlarını saptamak için vCenter Server çalıştıran bir sunucu olarak gereci dağıtırsınız

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir Azure hesabı ayarlayın.
> * VMware ortamını bulma için hazırlayın.
> * Bir proje oluşturun.
> * Azure geçişi gereci ayarlayın.
> * Sürekli bulmayı başlatın.

> [!NOTE]
> Öğreticiler senaryo denemek için en hızlı yolu gösterir ve mümkün olan yerlerde varsayılan seçenekleri kullanır.  

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce, bu önkoşulların yerinde olup olmadığını kontrol edin.

**Gereksinim** | **Ayrıntılar**
--- | ---
**vCenter Server/ESXi Konağı** | 5,5, 6,0, 6,5 veya 6,7 sürümünü çalıştıran bir vCenter Server gerekir.<br/><br/> Sunucular 5,5 veya sonraki bir sürümü çalıştıran bir ESXi konağında barındırılmalıdır.<br/><br/> VCenter Server, Gereç yapılandırma ve performans meta verilerini toplayabilmesi için TCP bağlantı noktası 443 ' de gelen bağlantılara izin verin.<br/><br/> Gereç, bağlantı noktası 443 üzerindeki vCenter Server varsayılan olarak bağlanır. VCenter Server farklı bir bağlantı noktasında dinliyorsa, Gereç Yapılandırma Yöneticisi 'nde vCenter Server ayrıntılarını sağladığınızda bağlantı noktasını değiştirebilirsiniz.<br/><br/> ESXi konaklarında, TCP bağlantı noktası 443 ' de, sunucularda yüklü uygulamaların ve aracısız bağımlılık analizinin keşfedilmesi için gelen erişime izin verildiğinden emin olun.
**Elektrikli** | vCenter Server Azure geçiş gereci için bir sunucu ayırmak üzere kaynaklara ihtiyaç duyuyor:<br/><br/> -32 GB RAM, 8 vCPU ve yaklaşık 80 GB disk depolaması.<br/><br/> -Bir dış sanal anahtar ve doğrudan ya da bir proxy aracılığıyla gereç sunucusunda internet erişimi.
**Sunucular** | Tüm Windows ve Linux işletim sistemi sürümleri, yapılandırma ve performans meta verilerinin bulunması için desteklenir. <br/><br/> Sunucularda uygulama bulma işlemini gerçekleştirmek için, tüm Windows ve Linux işletim sistemi sürümleri desteklenir. Aracısız bağımlılık analizi için desteklenen işletim sistemi sürümleri için [buraya](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) bakın.<br/><br/> Yüklü uygulamalar bulmayı ve aracısız bağımlılık analizini yapmak için, VMware araçlarının (10.2.0 ' den sonraki sürümler) sunucularda yüklü ve çalışıyor olması gerekir. Windows sunucularında PowerShell sürüm 2,0 veya üzeri yüklü olmalıdır.<br/><br/> SQL Server örnekleri ve veritabanlarını öğrenmek için, desteklenen Windows işletim sistemi sürümleri ve kimlik doğrulama mekanizmaları, desteklenen SQL Server sürümleri ve sürümleri için [buraya](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) bakın.

## <a name="prepare-an-azure-user-account"></a>Azure Kullanıcı hesabı hazırlama

Bir proje oluşturmak ve Azure geçişi gerecini kaydettirmek için, şu bir hesaba sahip olmanız gerekir:

- Azure aboneliğinde katkıda bulunan veya sahip izinleri
- Azure Active Directory (AAD) uygulamalarını kaydetme izinleri
- Sahip veya katkıda bulunan, Azure aboneliğinde aracısız sunucu geçişi sırasında kullanılan bir Key Vault oluşturmak için Kullanıcı erişimi Yöneticisi izinleri

Ücretsiz Azure hesabı oluşturduysanız aboneliğinizin sahibi siz olursunuz. Abonelik sahibi değilseniz, izinleri aşağıdaki şekilde atamak için sahibiyle birlikte çalışın:

1. Azure portal, "abonelikler" araması yapın ve **Hizmetler** altında **abonelikler**' i seçin.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Azure aboneliğini aramak için arama kutusu":::


2. **Abonelikler** sayfasında, bir proje oluşturmak istediğiniz aboneliği seçin.
3. Abonelikte **erişim denetimi (IAM)**  >  **erişimi denetle**' yi seçin.
4. **Erişimi denetle**' de ilgili Kullanıcı hesabını arayın.
5. **Rol ataması Ekle**' de, **Ekle**' ye tıklayın.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Erişimi denetlemek ve rol atamak için bir kullanıcı hesabı arayın":::
    
6. **Rol ataması Ekle**' de, katkıda bulunan veya sahip rolünü seçin ve hesabı (örneğimizde azmigrateuser) seçin. Daha sonra **Kaydet**'e tıklayın.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Hesaba rol atamak için rol ataması Ekle sayfasını açar":::

1. Gereci kaydettirmek için, Azure hesabınızın **AAD uygulamalarını kaydetme izinleri** olması gerekir.
1. Azure Portal ' de, **Azure Active Directory**  >  **kullanıcıları**  >  **Kullanıcı ayarları**' na gidin.
1. **Kullanıcı ayarları**' nda, Azure AD kullanıcılarının uygulamaları kaydedebildiğini doğrulayın (varsayılan olarak **Evet** ' e ayarlanır).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Kullanıcıların Active Directory uygulamalar kaydedebildiğini Kullanıcı ayarlarında doğrula":::

9. ' Uygulama kayıtları ' ayarlarının ' No ' olarak ayarlanması durumunda, gerekli izni atamak için kiracı/genel yönetici isteyin. Alternatif olarak, kiracı/genel yönetici, AAD uygulamasının kaydedilmesine izin vermek için **uygulama geliştirici** rolünü bir hesaba atayabilir. [Daha fazla bilgi edinin](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>VMware 'yi hazırlama

VCenter Server, hesabınızın bir OVA dosyası kullanarak VM oluşturma izinlerine sahip olup olmadığını denetleyin. Bu, Azure geçişi gereci bir VMware sanal makinesi olarak bir OVA dosyası kullanarak dağıtırken gereklidir.

Azure geçişi, VMware ortamınızda çalışan sunucuların bulunması ve değerlendirilmesi için vCenter Server salt okunurdur. Ayrıca yüklü uygulamalar bulmayı ve aracısız bağımlılık analizini gerçekleştirmek istiyorsanız, **sanal makineler > Konuk işlemleri** için hesabın etkinleştirilmiş olması gerekir.

### <a name="create-an-account-to-access-vcenter"></a>VCenter 'a erişmek için bir hesap oluşturun

VSphere Web Istemcisinde bir hesabı aşağıdaki şekilde ayarlayın:

1. VSphere Web Istemcisinde yönetici ayrıcalıklarına sahip bir hesap kullanarak > **Yönetim**' i seçin.
2. **Erişim**, **SSO kullanıcıları ve grupları**' nı seçin.
3. **Kullanıcılar**' da yeni bir kullanıcı ekleyin.
4. **Yeni Kullanıcı**' da hesap ayrıntılarını yazın. Daha sonra, **Tamam**'a tıklayın.
5. **Genel izinler**' de, Kullanıcı hesabını seçin ve hesaba **salt okunurdur** rolünü atayın. Daha sonra, **Tamam**'a tıklayın.
6. Ayrıca yüklü uygulamalar bulmayı ve aracısız bağımlılık analizini yapmak istiyorsanız, **Roller** ' e gidin > **salt oku** rolünü seçin ve **ayrıcalıklar** bölümünde **Konuk işlemler**' i seçin. "Alt öğelere yay" onay kutusunu seçerek ayrıcalıkları vCenter Server altındaki tüm nesnelere yayabilirsiniz.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Salt okuma rolünde Konuk işlemlere izin vermek için onay kutusu":::

> [!NOTE]
> Bulmayı belirli vCenter Server veri merkezleri, kümeler, bir küme klasörü, konaklar, konaklar klasörü veya vCenter Server hesabını tanımlayarak tek tek sunucular ile sınırlayabilirsiniz. VCenter Server Kullanıcı hesabının kapsamını belirleme hakkında [**daha fazla bilgi edinin**](set-discovery-scope.md) .

### <a name="create-an-account-to-access-servers"></a>Sunuculara erişmek için bir hesap oluşturma

Yüklü uygulamalar bulmayı, aracısız bağımlılık analizini ve SQL Server örnekleri ve veritabanlarını bulmayı gerçekleştirmek için sunucularda gerekli ayrıcalıklara sahip bir kullanıcı hesabına ihtiyacınız vardır. Kullanıcı hesabını gereç Yapılandırma Yöneticisi ' nde sağlayabilirsiniz. Gereç, sunuculara hiçbir aracı yüklemez.

1. Windows Server 'lar için sunucularda yönetim izinlerine sahip bir hesap (yerel veya etki alanı) oluşturun. SQL Server örnekleri ve veritabanlarını öğrenmek için, Windows veya SQL Server hesabının sysadmin sunucu rolünün bir üyesi olması gerekir. Kullanıcı hesabına gerekli rolü atama hakkında [daha fazla bilgi edinin](/sql/relational-databases/security/authentication-access/server-level-roles) .
2. Linux sunucuları için kök ayrıcalıklarına sahip bir hesap oluşturun. Alternatif olarak,/bin/netstat ve/bin/ls dosyalarında şu izinlere sahip bir hesap oluşturabilirsiniz: CAP_DAC_READ_SEARCH ve CAP_SYS_PTRACE.

> [!NOTE]
> Artık yüklü uygulamalar bulmayı, aracısız bağımlılık analizini ve SQL Server örneklerinin ve veritabanlarının bulunmasını gerçekleştirmek için Configuration Manager 'da birden fazla sunucu kimlik bilgisi ekleyebilirsiniz. Birden çok etki alanı/Windows (etki alanı olmayan)/Linux (etki alanı olmayan) ve/veya SQL Server kimlik doğrulama kimlik bilgilerini ekleyebilirsiniz. [**Daha fazla bilgi edinin**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Proje ayarlama

Yeni bir proje ayarlayın.

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.
3. **Genel bakış** > geçiş hedeflerinize göre seçin: **Windows, Linux ve SQL Server** veya **SQL Server (yalnızca)** veya **daha fazla senaryo** bulun > **proje oluştur**' u seçin.
5. **Proje oluştur**' da Azure aboneliğinizi ve kaynak grubunuzu seçin. Yoksa, bir kaynak grubu oluşturun.
6. **Proje ayrıntıları**' nda projeyi oluşturmak istediğiniz proje adını ve coğrafi konumu belirtin. [Kamu](migrate-support-matrix.md#supported-geographies-public-cloud) ve [kamu bulutları](migrate-support-matrix.md#supported-geographies-azure-government)için desteklenen coğrafi lıkları gözden geçirin.

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Proje adı ve bölgesi için kutular":::

7. **Oluştur**’u seçin.
8. Projenin dağıtılması için birkaç dakika bekleyin. **Azure geçişi: bulma ve değerlendirme** aracı varsayılan olarak yeni projeye eklenir.

> [!NOTE]
> Zaten bir proje oluşturduysanız, daha fazla Hayır bulma ve değerlendirme için ek gereçlere kaydolmak üzere aynı projeyi kullanabilirsiniz. sunucu. [ **Daha fazla bilgi**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Gereci ayarlama

Azure geçişi: bulma ve değerlendirme basit bir Azure geçişi gereci kullanır. Gereç sunucu bulma işlemini gerçekleştirir ve Azure geçişi 'ne sunucu yapılandırma ve performans meta verileri gönderir. Gereç, projeden indirilebilen bir OVA şablonu dağıtarak ayarlanabilir.

> [!NOTE]
> Bir nedenden dolayı, şablonu kullanarak gereci ayarlayamazsınız, var olan bir Windows Server 2016 sunucusunda bir PowerShell betiği kullanarak bu ayarı yapabilirsiniz. [**Daha fazla bilgi edinin**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>OVA ile dağıtma

Bir OVA şablonunu kullanarak gereci ayarlamak için:

1. Portalda bir gereç adı sağlayın ve bir proje anahtarı oluşturun.
1. Bir OVA şablon dosyasını indirip vCenter Server içe aktarın. OVA 'nın güvenli olduğunu doğrulayın.
1. OVA dosyasından gereç oluşturun ve Azure geçişi 'ne bağlanıp bağlanamadığından emin olun.
1. Gereci ilk kez yapılandırın ve proje anahtarını kullanarak projeyi projeye kaydedin.

### <a name="1-generate-the-project-key"></a>1. proje anahtarı oluştur

1. **Geçiş hedefleri**  >  **Windows, Linux ve SQL Server**  >  **Azure geçişi: bulma ve değerlendirme** bölümünde **bul**' u seçin.
2. Sunucularınızdaki **sunucular**  >  **sanallaştırılmış mı?**, **VMware vSphere hiper yöneticiyle Evet '** i seçin.
3. **1: proje anahtarı oluştur**' da, VMware ortamınızda sunucu keşfi Için ayarladığınız Azure geçiş gereci için bir ad sağlayın. Ad 14 karakter veya daha kısa bir harf olmalıdır.
1. Gerekli Azure kaynaklarını oluşturmaya başlamak için **anahtar oluştur** ' a tıklayın. Kaynak oluşturma sırasında bul sayfasını kapatmayın.
1. Azure kaynakları başarıyla oluşturulduktan sonra bir **Proje anahtarı** oluşturulur.
1. Yapılandırma sırasında gereç kaydını tamamlamamak için gerekli olacak şekilde anahtarı kopyalayın.

### <a name="2-download-the-ova-template"></a>2. OVA şablonunu indirin

**2: Azure geçişi yükleme gereci indirin**, öğesini seçin. OVA dosyası ve **İndir**' e tıklayın.

### <a name="verify-security"></a>Güvenliği doğrulama

Dağıtım yapmadan önce OVA dosyasının güvenli olup olmadığını denetleyin:

1. Dosyayı indirdiğiniz sunucuda, bir yönetici komut penceresi açın.
2. OVA dosyasının karmasını oluşturmak için aşağıdaki komutu çalıştırın:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Örnek kullanım: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. En son gereç sürümlerini ve karma değerlerini doğrulayın:

    - Azure genel bulutu için:
    
        **Algoritma** | **İndir** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Azure Kamu için:
    
        **Algoritma** | **İndir** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [En son sürüm](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3. gereç sunucusunu oluşturma

İndirilen dosyayı içeri aktarın ve VMware ortamında bir sunucu oluşturun

1. VSphere istemci konsolunda,   >  **ovf şablonu dosya dağıt**' a tıklayın.
2. OVF şablonu Dağıtma Sihirbazı > **kaynak** bölümünde ova dosyasının konumunu belirtin.
3. **Ad** ve **konum** bölümünde sunucu için bir kolay ad belirtin. Sunucunun barındırılacak envanter nesnesini seçin.
5. **Konak/küme**' da, sunucunun çalıştırılacağı konağı veya kümeyi belirtin.
6. **Depolama** alanında, sunucu için depolama hedefini belirtin.
7. **Disk Biçimi**’nde disk türünü ve boyutunu belirtin.
8. **Ağ eşlemesinde**, sunucunun bağlanacağı ağı belirtin. Ağ, Azure geçişi 'ne meta veri göndermek için internet bağlantısı gerektirir.
9. Ayarları gözden geçirip onayladıktan sonra **Son**’a tıklayın.

### <a name="verify-appliance-access-to-azure"></a>Azure 'a gereç erişimini doğrulama

Gereç sunucusunun [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu](migrate-appliance.md#government-cloud-urls) bulutları için Azure URL 'lerine bağlanabildiğinizden emin olun.

### <a name="4-configure-the-appliance"></a>4. gereci yapılandırma

Gereci ilk kez ayarlayın.

> [!NOTE]
> Gereci, indirilen OVA yerine bir [**PowerShell betiği**](deploy-appliance-script.md) kullanarak ayarlarsanız, bu yordamın ilk iki adımı ilgili değildir.

1. VSphere Istemci konsolunda sunucuya sağ tıklayın ve ardından **Konsolu Aç**' ı seçin.
2. Gereç için dil, saat dilimi ve parola sağlayın.
3. Gereci bağlayabilirler ve gereç Yapılandırma Yöneticisi 'nin URL 'sini açmak için herhangi bir makinede bir tarayıcı açın: `https://appliance name or IP address: 44368` .

   Alternatif olarak, Configuration Manager kısayolunu seçerek, Gereç sunucu masaüstündeki Configuration Manager ' ı açabilirsiniz.
1. **Lisans koşullarını** kabul edin ve üçüncü taraf bilgilerini okuyun.
1. Configuration Manager > **önkoşulları ayarlama** bölümünde şunları yapın:
   - **Bağlantı**: gereç, sunucunun internet erişimi olup olmadığını denetler. Sunucu bir proxy kullanıyorsa:
     - Proxy adresini  `http://ProxyIPAddress` veya `http://ProxyFQDN` dinleme bağlantı noktasını belirtmek için kurulum Proxy 'ye tıklayın.
     - Proxy için kimlik doğrulaması gerekiyorsa kimlik bilgilerini gerekin.
     - Yalnızca HTTP proxy’si desteklenir.
     - Proxy ayrıntıları eklediyseniz veya proxy ve/veya kimlik doğrulamasını devre dışı bırakırsanız, bağlantıyı tetiklemek için **Kaydet** 'e tıklayarak bağlantı denetimini yeniden başlatın.
   - **Zaman eşitleme**: bulmanın düzgün çalışması için gereç üzerindeki zaman internet ile eşitlenmiş olmalıdır.
   - **Güncelleştirmeleri yükleme**: gereç en son güncelleştirmelerin yüklü olmasını sağlar. Denetim tamamlandıktan sonra, Gereç sunucusunda çalışan hizmetlerin durumunu ve sürümlerini görmek için **gereç hizmetlerini görüntüle** ' ye tıklayabilirsiniz.
   - **VDDK 'Yi yükleme**: gereç, VMware vSphere sanal disk geliştirme seti 'nın (VDDK) yüklü olduğunu denetler. Yüklü değilse, VMware 'den VDDK 6,7 ' i indirin ve indirilen ZIP içeriğini **yükleme yönergelerindeki** belirtilen konuma ayıklayın.

     Azure geçişi sunucu geçişi, Azure 'a geçiş sırasında sunucuları çoğaltmak için VDDK 'yi kullanır. 
1. İsterseniz, gerecin tüm önkoşulları karşılayıp karşılamadığını denetlemek için gereç yapılandırması sırasında dilediğiniz zaman **Önkoşulları yeniden çalıştırabilirsiniz** .

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Gereç Yapılandırma Yöneticisi 'nde Panel 1":::

### <a name="register-the-appliance-with-azure-migrate"></a>Gereci Azure geçişi ile kaydetme

1. Portaldan kopyalanmış **Proje anahtarını** yapıştırın. Anahtarınız yoksa **Azure geçişi: bulma ve değerlendirme> var olan gereçlerini bulmak> yönetmek** için, anahtar oluşturma sırasında verdiğiniz gereç adını seçin ve karşılık gelen anahtarı kopyalayın.
1. Azure ile kimlik doğrulaması yapmak için bir cihaz koduna ihtiyacınız olacaktır. **Oturum** açmak için tıkladığınızda, aşağıda gösterildiği gibi cihaz koduyla kalıcı olarak açılır.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Cihaz kodunu kalıcı olarak gösterme":::

1. Cihaz kodunu kopyalamak ve yeni bir tarayıcı sekmesinde bir Azure oturum açma istemi açmak için **kodu kopyala & oturum** aç ' a tıklayın. Görünmüyorsa, tarayıcıda açılır pencere engelleyicisini devre dışı bırakmış olduğunuzdan emin olun.
1. Yeni sekmede, Azure Kullanıcı adınızı ve parolanızı kullanarak cihaz kodunu yapıştırın ve oturum açın.
   
   PIN ile oturum açma desteklenmez.
3. Oturum açmadan oturum açma sekmesini yanlışlıkla kapatırsanız, oturum açma düğmesini yeniden etkinleştirmek için gereç Yapılandırma Yöneticisi 'nin tarayıcı sekmesini yenilemeniz gerekir.
1. Başarıyla oturum açtıktan sonra, Gereç Yapılandırma Yöneticisi ile önceki sekmeye geri dönün.
1. Günlüğe kaydetme için kullanılan Azure Kullanıcı hesabının, anahtar üretimi sırasında oluşturulan Azure kaynakları üzerinde doğru izinleri varsa, Gereç kaydı başlatılır.
1. Gereç başarıyla kaydedildikten sonra, **Ayrıntıları görüntüle**' ye tıklayarak kayıt ayrıntılarına bakabilirsiniz.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Gereç Yapılandırma Yöneticisi 'nde panel 2":::

## <a name="start-continuous-discovery"></a>Sürekli bulmayı Başlat

### <a name="provide-vcenter-server-details"></a>vCenter Server ayrıntılarını sağlayın

Gereçinin, sunucuların yapılandırma ve performans verilerini bulması için vCenter Server 'e bağlanması gerekir.

1. **1. Adım: kimlik bilgilerini vCenter Server sağlayın**' de kimlik bilgileri için kolay bir ad belirtmek üzere **kimlik bilgileri ekle** ' ye tıklayın, gerecin vCenter Server çalıştıran sunucuları bulmaya kullanacağı vCenter Server hesabı Için **Kullanıcı adı** ve **parola** ekleyin.
    - Yukarıdaki bu makalede ele alınan gerekli izinlere sahip bir hesap ayarlamış olmanız gerekir.
    - Bulma işlemini belirli VMware nesneleri (vCenter Server veri merkezleri, kümeler, bir küme klasörü, konaklar, konaklar klasörü veya ayrı sunucular) için kapsama eklemek istiyorsanız, Azure geçişi tarafından kullanılan hesabı kısıtlamak için [Bu makaledeki](set-discovery-scope.md) yönergeleri gözden geçirin.
1. **2. Adım: vCenter Server ayrıntıları belirtin** bölümünde, açılan listeden kimlik bilgileri için kolay ad seçmek üzere **bulma kaynağı Ekle** ' ye tıklayın, vCenter Server **IP adresini/FQDN** 'yi belirtin. **Bağlantı noktasını** varsayılan (443) olarak bırakabilir veya vCenter Server dinlediği ve **Kaydet**' e tıklayarak özel bir bağlantı noktası belirtebilirsiniz.
1. **Kaydet**'e tıklanınca, Gereç vCenter Server bağlantısını doğrulamayı dener ve tablodaki **doğrulama durumunu** vCenter Server IP adresine/FQDN 'ye göre gösterir.
1. Bulmayı başlatmadan önce vCenter Server bağlantıyı **yeniden doğrulayabilirsiniz** .

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server Ayrıntılar için gereç Yapılandırma Yöneticisi 'nde panel 3":::

### <a name="provide-server-credentials"></a>Sunucu kimlik bilgilerini belirtin

**3. Adım: yazılım envanterini gerçekleştirmek için sunucu kimlik bilgilerini sağlama, aracısız bağımlılık Analizi ve SQL Server örneklerinin ve veritabanlarının bulunması** için, birden çok sunucu kimlik bilgilerini sağlamayı seçebilirsiniz veya bu özellikleri kullanmak istemiyorsanız, adımı atlayıp vCenter Server bulmaya devam edebilirsiniz. Amacınızı daha sonra istediğiniz zaman değiştirebilirsiniz.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Sunucu ayrıntıları için gereç Yapılandırma Yöneticisi 'nde panel 3":::

Bu özellikleri kullanmak istiyorsanız, aşağıdaki adımları izleyerek sunucu kimlik bilgileri sağlayabilirsiniz. Gereç, bulma özelliklerini gerçekleştirmek için kimlik bilgilerini sunucularla otomatik olarak eşlemeye çalışır.

- **Kimlik bilgileri ekle** düğmesine tıklayarak sunucu kimlik bilgilerini ekleyebilirsiniz. Bu işlem, açılan listeden **kimlik bilgileri türünü** seçebileceğiniz kalıcı bir şekilde açılır.
- Etki alanı/Windows (etki alanı olmayan)/Linux (etki alanı olmayan)/SQL Server kimlik doğrulama kimlik bilgileri sağlayabilirsiniz. Kimlik bilgilerini ve bunları nasıl işleyeceğinizi [öğrenmek hakkında daha fazla bilgi edinin](add-server-credentials.md) .
- Her kimlik bilgileri türü için kimlik bilgileri için kolay bir ad belirtmeniz, **Kullanıcı adı** ve **parola** eklemeniz ve **Kaydet**' e tıklamanız gerekir.
- Etki alanı kimlik bilgilerini seçerseniz, etki alanının FQDN 'sini de belirtmeniz gerekir. Bu etki alanının Active Directory kimlik bilgilerinin orijinalliğini doğrulamak için FQDN gereklidir.
- Yüklenen uygulamaların bulunması, aracısız bağımlılık analizi veya SQL Server örneklerinin ve veritabanlarının bulunması için hesapta [gerekli izinleri](add-server-credentials.md#required-permissions) gözden geçirin.
- Aynı anda birden çok kimlik bilgisi eklemek istiyorsanız, kaydetmek için **daha fazla Ekle** ' ye tıklayın ve daha fazla kimlik bilgisi ekleyin.
- **Kaydet** veya **daha fazla Ekle**' ye tıkladığınızda, Gereç, etki alanı kimlik bilgilerini, etki alanının Active Directory kimliğini doğrular. Bu, Gereç kimlik bilgilerini ilgili sunucularla eşlemek için birden çok yineleme yaparken hesap kilitlenmelerini önlemek için yapılır.
- Kimlik bilgileri tablosundaki tüm etki alanı kimlik bilgileri için **doğrulama durumunu** görebilirsiniz. Yalnızca etki alanı kimlik bilgileri doğrulanacak.
- Doğrulama başarısız olursa, karşılaşılan hatayı görmek için **başarısız** durumuna tıklayabilir ve başarısız etki alanı kimlik bilgilerini yeniden doğrulamak için sorunu düzelttikten sonra **kimlik bilgilerini yeniden doğrula** ' ya tıklayabilirsiniz.

     :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Birden çok kimlik bilgisi sağlamak için gereç Yapılandırma Yöneticisi 'nde panel 3":::

### <a name="start-discovery"></a>Bulmayı başlat

1. VCenter Server bulmayı **başlatmak için bulmayı Başlat**' a tıklayın. Bulma işlemi başarılı bir şekilde başlatıldıktan sonra, kaynak tablosundaki vCenter Server IP adresine/FQDN 'ye karşı bulma durumunu kontrol edebilirsiniz.
1. Sunucu kimlik bilgilerini sağladıysanız, vCenter Server bulma işlemi tamamlandıktan sonra yazılım envanteri (yüklü uygulamaların keşfi) otomatik olarak başlatılır. Yazılım envanteri her 12 saatte bir gerçekleştirilir.
1. [Yazılım envanteri](how-to-discover-applications.md) , sunucularda çalışan SQL Server örnekleri tanımlar ve bilgileri kullanarak, Gereç üzerinde sunulan Windows kimlik doğrulaması veya SQL Server kimlik doğrulama kimlik bilgileri aracılığıyla örneklere bağlanmaya çalışır ve SQL Server veritabanlarına ve bunların özelliklerine veri toplar. SQL bulma her 24 saatte bir gerçekleştirilir.
1. Yazılım envanteri sırasında, eklenen sunucu kimlik bilgileri sunuculara göre tekrarlanacaktır ve aracısız bağımlılık analizi için onaylanır. Sunucular için aracısız bağımlılık analizini portaldan etkinleştirebilirsiniz. Aracısız bağımlılık analizini etkinleştirmek için yalnızca doğrulamanın başarılı olduğu sunucular seçilebilir.

> [!Note]
>Azure geçişi, Azure geçişi gereci ve kaynak SQL Server örnekleri (şifreleme bağlantısı özelliği TRUE olarak ayarlanır) arasındaki iletişimi şifreler. Bu bağlantılar [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) ile ŞIFRELENIR (true olarak ayarlanır); Aktarım katmanı, kanalı şifrelemek ve güveni doğrulamak üzere sertifika zincirini atlamak için SSL kullanır. Gereç sunucusunun, [**sertifikanın kök yetkilisine güvenecek**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)şekilde ayarlanması gerekir.<br/>
Sunucu başlatıldığında sunucuda bir sertifika sağlanmadıysa SQL Server, oturum açma paketlerini şifrelemek için kullanılan kendinden imzalı bir sertifika oluşturur. [**Daha fazla bilgi edinin**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Bulma işlemi aşağıdaki gibi çalışmaktadır:

- Keşfedilen sunucular envanterinin portalda görünmesi 15 dakika sürer.
- Yüklü uygulamaların bulunması biraz zaman alabilir. Süre, bulunan sunucu sayısına bağlıdır. 500 sunucularında, bulunan stokun Azure geçişi portalında görünmesi yaklaşık bir saat sürer.
- Sunucuları bulma işlemi tamamlandıktan sonra, portaldan sunucular üzerinde aracısız bağımlılık analizini etkinleştirebilirsiniz.
- SQL Server örnekleri ve veritabanı verileri, bulma başlatma işleminden itibaren 24 saat içinde portalda görünmeye başlar.

## <a name="next-steps"></a>Sonraki adımlar

- Azure VM 'lerine geçiş için [sunucuları değerlendirin](./tutorial-assess-vmware-azure-vm.md) .
- [SQL Server](./tutorial-assess-sql.md) 'ı Azure SQL 'e geçiş için değerlendirin.
- Bulma sırasında gereç tarafından toplanan [verileri gözden geçirin](migrate-appliance.md#collected-data---vmware) .
