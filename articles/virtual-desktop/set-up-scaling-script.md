---
title: Dinamik ölçek Windows sanal masaüstü oturumu Konakları-Azure
description: Windows sanal masaüstü oturumu konakları için otomatik ölçeklendirme betiği ayarlamayı açıklar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: helohr
ms.openlocfilehash: 744f7d5c191180757620e87d926422c9f1e0baba
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607450"
---
# <a name="scale-session-hosts-dynamically"></a>Oturum konaklarını dinamik olarak ölçeklendirme

Azure 'daki birçok Windows sanal masaüstü dağıtımında, sanal makine maliyetleri toplam Windows sanal masaüstü dağıtım maliyetinin önemli bir kısmını temsil eder. Maliyetleri azaltmak için, en yoğun kullanım saatlerinde oturum ana bilgisayarı sanal makinelerini (VM 'Ler) kapatıp serbest bırakmak, sonra da en yüksek kullanım saatlerinde yeniden başlatmanız gerekir.

Bu makalede, Windows sanal masaüstü ortamınızdaki oturum ana bilgisayarı sanal makinelerini otomatik olarak ölçeklendirmek için basit bir ölçeklendirme betiği kullanılmaktadır. Ölçeklendirme betiği nasıl çalıştığı hakkında daha fazla bilgi edinmek için [ölçeklendirme betiği nasıl kullanılır](#how-the-scaling-script-works) bölümüne bakın.

## <a name="prerequisites"></a>Ön koşullar

Komut dosyasını çalıştırdığınız ortam aşağıdaki şeylere sahip olmalıdır:

- Bu kiracıyı sorgulama izinleri olan bir Windows sanal masaüstü kiracısı ve hesabı veya hizmet sorumlusu (örneğin, RDS katılımcısı).
- Windows sanal masaüstü hizmetinde yapılandırılmış ve kayıtlı oturum ana bilgisayar havuzu VM 'Leri.
- Görev Zamanlayıcı aracılığıyla zamanlanmış görevi çalıştıran ve oturum konaklarına ağ erişimi olan ek bir sanal makine. Bu, daha sonra belge içinde Scaler VM olarak adlandırılır.
- Zamanlanmış görevi çalıştıran VM 'de yüklü [Microsoft Azure Kaynak Yöneticisi PowerShell modülü](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) .
- Zamanlanan görevi çalıştıran VM 'de yüklü olan [Windows sanal masaüstü PowerShell modülü](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) .

## <a name="recommendations-and-limitations"></a>Öneriler ve sınırlamalar

Ölçeklendirme betiği çalışırken şunları göz önünde bulundurun:

- Bu ölçeklendirme betiği, ölçek betiğini çalıştıran zamanlanmış görevin örneği başına yalnızca bir konak havuzunu işleyebilir.
- Ölçek betikleri çalıştıran zamanlanmış görevler, her zaman açık olan bir VM üzerinde olmalıdır.
- Ölçek betiği ve yapılandırması için her bir örnek için ayrı bir klasör oluşturun.
- Bu betik, çok faktörlü kimlik doğrulaması gerektiren Azure AD Kullanıcı hesaplarıyla Windows sanal masaüstü 'nde oturum açmayı desteklemez. Windows sanal masaüstü hizmetine ve Azure 'a erişmek için hizmet sorumlularını kullanmanızı öneririz. PowerShell ile bir hizmet sorumlusu ve rol ataması oluşturmak için [Bu öğreticiyi](create-service-principal-role-powershell.md) izleyin.
- Azure 'un SLA garantisi yalnızca bir kullanılabilirlik kümesindeki VM 'Ler için geçerlidir. Belgenin geçerli sürümü, ölçeklendirmeyi yapan tek bir VM 'ye sahip bir ortamı açıklar ve bu da kullanılabilirlik gereksinimlerini karşılamayabilir.

## <a name="deploy-the-scaling-script"></a>Ölçeklendirme betiğini dağıtma

Aşağıdaki yordamlarda, ölçeklendirme betiğinin nasıl dağıtılacağı açıklanır.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Ortamınızı ölçeklendirme betiği için hazırlama

İlk olarak, ortamınızı ölçeklendirme betiği için hazırlayın:

1. Bir etki alanı yönetici hesabı ile zamanlanmış görevi çalıştıracak VM 'de (Scaler VM) oturum açın.
2. Scaler VM üzerinde ölçeklendirme betiğini ve yapılandırmasını tutacak bir klasör oluşturun (örneğin, **C:\\ölçeklendirme-HostPool1**).
3. **Basicscale. ps1**, **config. xml**ve **Functions-PSStoredCredentials. ps1** dosyalarını ve **powershellmodules** klasörünü, [ölçeklendirme betiği deposundan](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) indirin ve adım 2 ' de oluşturduğunuz klasöre kopyalayın. Dosyaları Scaler VM 'sine kopyalamadan önce almanın iki birincil yolu vardır:
    - Git deposunu yerel makinenize kopyalayın.
    - Her bir dosyanın **Ham** sürümünü görüntüleyin, her dosyanın içeriğini kopyalayıp bir metin düzenleyicisine yapıştırın, ardından dosyaları karşılık gelen dosya adı ve dosya türü ile kaydedin. 

### <a name="create-securely-stored-credentials"></a>Güvenli şekilde depolanan kimlik bilgileri oluştur

Daha sonra, güvenli şekilde depolanan kimlik bilgilerini oluşturmanız gerekir:

1. PowerShell ıSE 'yi yönetici olarak açın.
2. Aşağıdaki cmdlet 'i çalıştırarak RDS PowerShell modülünü içeri aktarın:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Düzenle bölmesini açın ve **Function-PSStoredCredentials. ps1** dosyasını yükleyin, sonra tüm betiği çalıştırın (F5)
4. Aşağıdaki cmdlet'i çalıştırın:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Örneğin, **set-değişken-adı KeyPath-Scope genel-değer "c:\\ölçeklendirme-HostPool1"**
5. **New-StoredCredential-keypath \$keyPath** cmdlet 'ini çalıştırın. İstendiğinde, ana bilgisayar havuzunu sorgulama izinleri ile Windows sanal masaüstü kimlik bilgilerinizi girin (konak havuzu **config. xml**dosyasında belirtilir).
    - Farklı hizmet sorumlularını veya standart hesabı kullanırsanız, her hesap için **Yeni-StoredCredential-keypath \$keyPath** cmdlet 'ini çalıştırarak yerel depolanan kimlik bilgilerini oluşturun.
6. Kimlik bilgilerinin başarıyla oluşturulduğunu onaylamak için **Get-StoredCredential-List** ' i çalıştırın.

### <a name="configure-the-configxml-file"></a>Config. xml dosyasını yapılandırma

Config. xml dosyasındaki ölçeklendirme betiği ayarlarını güncelleştirmek için aşağıdaki alanlara ilgili değerleri girin:

| Alan                     | Açıklama                    |
|-------------------------------|------------------------------------|
| Aadtenantıd                   | Oturum Ana bilgisayar VM 'lerinin çalıştırıldığı aboneliği ilişkilendiren Azure AD kiracı KIMLIĞI     |
| Aadapplicationıd              | Hizmet sorumlusu uygulama KIMLIĞI                                                       |
| Aadservicesprincipalsecret     | Bu, test aşamasında girilebilir, ancak **Functions-PSStoredCredentials. ps1** ile kimlik bilgileri oluşturduğunuzda boş tutulur    |
| currentAzureSubscriptionId    | Oturum Ana bilgisayar VM 'lerinin çalıştırıldığı Azure aboneliğinin KIMLIĞI                        |
| tenantName                    | Windows sanal masaüstü kiracı adı                                                    |
| hostPoolName                  | Windows sanal masaüstü konak havuzu adı                                                 |
| RDBroker                      | WVD Service URL 'SI, varsayılan https değeri:\//rdbroker.wvd.microsoft.com             |
| Kullanıcı adı                      | Hizmet sorumlusu uygulama KIMLIĞI (Aadapplicationıd ile aynı hizmet sorumlusuna sahip olmak mümkündür) veya Multi-Factor Authentication olmadan standart Kullanıcı |
| isServicePrincipal            | Kabul edilen değerler **true** veya **false**şeklindedir. Kullanılan ikinci kimlik bilgileri kümesinin bir hizmet sorumlusu veya standart bir hesap olup olmadığını gösterir. |
| BeginPeakTime                 | Yoğun kullanım süresi başladığında                                                            |
| EndPeakTime                   | Yoğun kullanım süresi sona erdiğinde                                                              |
| Timedifferenceınhours         | Saat cinsinden yerel saat ve UTC arasındaki zaman farkı                                   |
| SessionThresholdPerCPU        | Yeni bir oturum ana bilgisayar VM 'sinin yoğun saatlerde ne zaman başlatılması gerektiğini belirlemede kullanılan, CPU eşiği başına en fazla oturum sayısı.  |
| MinimumNumberOfRDSH           | Yoğun kullanım süresi boyunca çalışmayı sürdürmek için en düşük konak havuzu VM sayısı             |
| LimitSecondsToForceLogOffUser | Kullanıcıları oturumu kapatmaya zorlamadan önce beklenecek saniye sayısı. 0 olarak ayarlanırsa, kullanıcılar oturumu kapanmaya zorlanmaz.  |
| LogOffMessageTitle            | Oturum açmaya zorlanmadan önce kullanıcıya gönderilen iletinin başlığı                  |
| LogOffMessageBody             | Oturum kapatmadan önce kullanıcılara gönderilen uyarı iletisinin gövdesi. Örneğin, "Bu makine X dakika içinde kapatılacak. Lütfen çalışmanızı kaydedin ve oturumunuzu kapatın. " |

### <a name="configure-the-task-scheduler"></a>Görev Zamanlayıcı yapılandırma

Configuration. xml dosyasını yapılandırdıktan sonra, Görev Zamanlayıcı normal bir aralıkta basicScaler. ps1 dosyasını çalıştıracak şekilde yapılandırmanız gerekir.

1. **Görev Zamanlayıcı**başlatın.
2. **Görev Zamanlayıcı** penceresinde, **görev oluştur...** seçeneğini belirleyin.
3. **Görev oluştur** iletişim kutusunda, **genel** sekmesini seçin, bir **ad** gırın (örneğin, "dinamik RDSH"), **kullanıcının oturum açıp açmamadığını Çalıştır** ' ı seçin ve **en yüksek ayrıcalıklarla çalıştırın**.
4. **Tetikleyiciler** sekmesine gidin ve ardından **yeni...** seçeneğini belirleyin.
5. **Yeni tetikleyici** Iletişim kutusunda **Gelişmiş ayarlar**altında, **görevi her zaman Yinele** ' yi işaretleyin ve uygun süreyi ve süreyi (örneğin, **15 dakika** veya **süresiz**) seçin.
6. **Eylemler** sekmesini ve **yeni..** . seçeneğini belirleyin.
7. **Yeni eylem** iletişim kutusunda, **PowerShell. exe** ' yi **Program/betik** alanına girin ve sonra da **bağımsız değişken Ekle (isteğe bağlı)** alanına **C:\\ölçeklendirme\\basicscale. ps1** yazın.
8. **Koşullar** ve **Ayarlar** sekmelerine gidin ve her biri için varsayılan ayarları kabul etmek üzere **Tamam** ' ı seçin.
9. Ölçek betiğini çalıştırmayı planladığınız yönetim hesabının parolasını girin.

## <a name="how-the-scaling-script-works"></a>Ölçeklendirme betiği nasıl kullanılır?

Bu ölçeklendirme betiği, gün içinde en yoğun kullanım süresinin başlangıcı ve sonu dahil olmak üzere bir config. xml dosyasından ayarları okur.

En yüksek kullanım süresi boyunca, komut dosyası her bir konak havuzu için geçerli oturum sayısını ve geçerli çalışan RDSH kapasitesini denetler. Çalışan oturum ana bilgisayar VM 'lerinin, config. xml dosyasında tanımlanan SessionThresholdPerCPU parametresine göre var olan oturumları desteklemek için yeterli kapasiteye sahip olup olmadığını hesaplar. Aksi takdirde, betik konak havuzunda ek oturum ana bilgisayarı VM 'Leri başlatır.

En yoğun kullanım süresi boyunca, betik, config. xml dosyasındaki MinimumNumberOfRDSH parametresine bağlı olarak hangi oturum ana bilgisayar VM 'lerinin kapanması gerektiğini belirler. Komut dosyası, konaklara bağlanan yeni oturumları engellemek için oturum Konağı VM 'lerini boşaltma moduna ayarlayacaktır. Config. xml dosyasında **Limitsecondstoforcelogoffuser** parametresini sıfır olmayan pozitif bir değere ayarlarsanız betik, şu anda oturum açmış olan tüm kullanıcılara, işi kaydetmesi, yapılandırılan süreyi beklemesi ve ardından kullanıcıları oturumu kapatmaya zorlayacaktır. Tüm Kullanıcı oturumları bir oturum ana bilgisayar VM 'sinde kapatıldıktan sonra, komut dosyası sunucuyu kapatır.

Config. xml dosyasında **Limitsecondstoforcelogoffuser** parametresini sıfır olarak ayarlarsanız betik, ana bilgisayar havuzu özelliklerindeki oturum yapılandırma ayarının kullanıcı oturumlarını kapatmayı işlemesine izin verir. Bir oturum ana bilgisayar VM 'sinde herhangi bir oturum varsa, oturum ana bilgisayarı VM çalışır durumda kalır. Herhangi bir oturum yoksa, betik oturum ana bilgisayarı sanal makinesini kapatır.

Betik, Görev Zamanlayıcı kullanarak Scaler VM sunucusunda düzenli olarak çalışacak şekilde tasarlanmıştır. Uzak Masaüstü Hizmetleri ortamınızın boyutuna bağlı olarak uygun zaman aralığını seçin ve sanal makinelerin başlatılmasının ve kapanmasının biraz zaman alabilir olduğunu unutmayın. Ölçek betiğini 15 dakikada bir çalıştırmayı öneririz.

## <a name="log-files"></a>Günlük dosyaları

Ölçeklendirme betiği, **WVDTenantScale. log** ve **WVDTenantUsage. log**olmak üzere iki günlük dosyası oluşturur. **WVDTenantScale. log** dosyası, ölçekleme betiğinin her yürütülmesi sırasında olayları ve hataları (varsa) günlüğe kaydeder.

**WVDTenantUsage. log** dosyası, ölçek betiğini her yürüttüğünüzde etkin çekirdek sayısını ve etkin sanal makine sayısını kaydeder. Bu bilgileri, Microsoft Azure VM 'lerin gerçek kullanımını ve maliyetini tahmin etmek için kullanabilirsiniz. Dosya, aşağıdaki bilgileri içeren her öğe ile virgülle ayrılmış değerler olarak biçimlendirilir:

>zaman, konak havuzu, çekirdek, VM 'Ler

Dosya adı aynı zamanda bir. csv uzantısına sahip olacak şekilde değiştirilebilir, Microsoft Excel 'e yüklenir ve analiz edilebilir.
