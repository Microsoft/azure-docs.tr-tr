---
title: Azure Güvenlik Merkezi 'ne dahil edilen Endpoint License için Microsoft Defender 'ı kullanma
description: Uç nokta için Microsoft Defender ve Azure Güvenlik Merkezi 'nden dağıtma hakkında bilgi edinin.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: a9997fac66dd49af04f4ed78737118d605e27072
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829899"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Güvenlik Merkezi 'nin tümleşik EDR çözümü ile uç noktalarınızı koruyun: uç nokta için Microsoft Defender

Uç nokta için Microsoft Defender, bütünsel, bulut tarafından sunulan bir uç nokta güvenlik çözümüdür. Ana özellikleri şunlardır:

- Risk tabanlı güvenlik açığı yönetimi ve değerlendirmesi 
- Saldırı yüzeyini azaltma
- Davranış tabanlı ve bulut destekli koruma
- Uç nokta algılama ve yanıt (EDR)
- Otomatik araştırma ve düzeltme
- Yönetilen arama hizmetleri

> [!TIP]
> Başlangıçta **Windows Defender ATP** olarak başlatılan bu uç nokta algılama ve yanıt (EDR) ürünü, **Microsoft Defender ATP** olarak 2019 ' de yeniden adlandırıldı.
>
> Ignite 2020, [Microsoft Defender XDR paketini](https://www.microsoft.com/security/business/threat-protection) başlattık ve bu EDR bileşeni **uç nokta için Microsoft Defender** olarak yeniden adlandırıldı.


## <a name="availability"></a>Kullanılabilirlik

| Görünüş                          | Ayrıntılar                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Yayın durumu:                  | Genel olarak kullanılabilir (GA)                                                                                                                                                                                                                                                                                      |
| Fiyat                        | [Sunucular Için Azure Defender](defender-for-servers-introduction.md) gerekir                                                                                                                                                                                                                                             |
| Desteklenen platformlar:            |  • Windows çalıştıran Azure makineleri<br> • Windows çalıştıran Azure Arc makineleri|
| Algılama için desteklenen Windows sürümleri:  |  • Windows Server 2019, 2016, 2012 R2 ve 2008 R2 SP1<br> • [Windows sanal masaüstü (WVD)](../virtual-desktop/overview.md)<br> • [Windows 10 Enterprise çoklu oturum](../virtual-desktop/windows-10-multisession-faq.yml) (eski adıyla sanal masaüstleri için Enterprise (EVD)|
| Desteklenmeyen işletim sistemleri:  |  • Windows 10 (EVD veya WVD dışında)<br> • Linux|
| Gerekli roller ve izinler: | Tümleştirmeyi etkinleştirmek/devre dışı bırakmak için: **Güvenlik Yöneticisi** veya **sahibi**<br>Güvenlik Merkezi 'nde MDADTP uyarılarını görüntülemek için: **güvenlik okuyucu**, **okuyucu**, **kaynak grubu katılımcısı**, **kaynak grubu sahibi**, **Güvenlik Yöneticisi**, **abonelik sahibi** veya **abonelik katılımcısı**|
| Larının                         | ![Yes](./media/icons/yes-icon.png) Ticari bulutlar<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Çin gov, diğer gov                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Güvenlik Merkezi 'nde uç nokta özellikleri için Microsoft Defender

Uç nokta için Microsoft Defender şunları sağlar:

- **Gelişmiş ihlal sonrası algılama sensörleri**. Windows makineler için uç noktanın algılayıcılar için Defender, çok sayıda davranış sinyali toplar.

- **Analitik tabanlı, bulut destekli, ihlal sonrası algılama**. Endpoint için Defender, değişen tehditlere hızlı bir şekilde uyum sağlar. Gelişmiş analiz ve büyük veri kullanır. Bilinmeyen tehditleri algılamak için Windows, Azure ve Office 'teki sinyallerle Intelligent Security Graph güçlü olarak dağıtılır. İşlem yapılabilir uyarılar sağlar ve hızla yanıt vermenize olanak tanır.

- **Tehdit bilgisi**. For Endpoint için Defender, saldırgan araçlarını, tekniklerini ve yordamlarını belirlediğinde uyarılar oluşturur. Microsoft tehdit arayıcılar ve güvenlik ekipleri tarafından oluşturulan ve iş ortakları tarafından sunulan zeka tarafından düzenlenen verileri kullanır.

Defender for Endpoint for Security Center ile tümleştirerek aşağıdaki ek yetenekler de avantajdan yararlanabilirsiniz:

- **Otomatik ekleme**. Güvenlik Merkezi, Güvenlik Merkezi tarafından izlenen tüm Windows Server 'lar için Endpoint algılayıcı için Microsoft Defender 'ı otomatik olarak sunar.

- **Tek bir cam bölmesi**. Güvenlik Merkezi Konsolu, uç nokta uyarıları için Microsoft Defender 'ı görüntüler. Daha fazla araştırmak için, uyarı işlem ağacı ve olay grafiği gibi ek bilgileri görebileceğiniz uç noktanın kendi portal sayfaları için Microsoft Defender 'ı kullanın. Ayrıca, altı aya kadar geçmiş bir dönem için her davranışı gösteren ayrıntılı bir makine zaman çizelgesi görebilirsiniz.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Uç noktanın kendi güvenlik merkezi için Microsoft Defender" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>Endpoint Tenant için Microsoft Defender gereksinimleri nelerdir?

Sunucularınızı izlemek için Azure Güvenlik Merkezi 'ni kullandığınızda, bir uç nokta kiracısı için Microsoft Defender otomatik olarak oluşturulur. 

- **Konum:** Endpoint için Defender tarafından toplanan veriler, sağlama sırasında tanımlandığı şekilde kiracının coğrafi konumunda depolanır. Müşteri verileri-sahte bir biçimde, Birleşik Devletler merkezi depolama ve işleme sistemlerinde de depolanabilir. Konumu yapılandırdıktan sonra değiştiremezsiniz. Uç nokta için Microsoft Defender lisansınız varsa ve verilerinizi başka bir konuma taşımanız gerekiyorsa, kiracıyı sıfırlamak için Microsoft Desteği başvurun.
- **Abonelikler taşınıyor:** Azure aboneliğinizi Azure kiracılar arasında taşıdıysanız, güvenlik merkezi 'nin uç nokta için Defender 'ı dağıtması için bazı el ile hazırlık adımları gereklidir. Tüm ayrıntılar için [Microsoft destek 'e başvurun](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>Endpoint Integration için Microsoft Defender 'ı etkinleştirin

### <a name="prerequisites"></a>Önkoşullar

Makinenizin Endpoint için Defender için gereken gereksinimleri karşıladığından emin olun:

1. Makinenin Azure 'a gerektiği şekilde bağlı olduğundan emin olun:

    - **Windows** sunucuları için, [cihaz ara sunucusunu ve Internet bağlantısı ayarlarını yapılandırma](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet) bölümünde açıklanan ağ ayarlarını yapılandırın
    - **Şirket içi** makineler Için, [Azure Arc etkin sunucularıyla karma makinelere bağlanma](../azure-arc/servers/learn/quick-enable-hybrid-vm.md) bölümünde açıklandığı gibi Azure yaya bağlayın
    - **Windows Server 2019** ve [Windows sanal masaüstü (WVD)](../virtual-desktop/overview.md) makineleri için makinelerinizin Log Analytics Aracısı çalıştığını ve microsoftmonitoringagent uzantısının olduğunu doğrulayın.
    
1. **Sunucular Için Azure Defender 'ı** etkinleştirin. Bkz. [hızlı başlangıç: Azure Defender 'ı etkinleştirme](enable-azure-defender.md).
1. Sunucularınızdaki uç noktalar için Microsoft Defender 'ı zaten lisanslandırdıysanız ve dağıttıysanız, yerleşik [Windows sunucularında](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)açıklanan yordamı kullanarak kaldırın.
1. Aboneliğinizi Azure kiracılar arasında taşıdıysanız, bazı el ile hazırlık adımları da gereklidir. Tüm ayrıntılar için [Microsoft destek 'e başvurun](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).


### <a name="enable-the-integration"></a>Tümleştirmeyi etkinleştir
1. Güvenlik Merkezi 'nin menüsünde, **fiyatlandırma & ayarları** ' nı seçin ve değiştirmek istediğiniz aboneliği seçin.
1. **Tehdit algılamayı** seçin.
1. **Endpoint Için Microsoft Defender 'ın verilerinize erişmesine Izin ver**' i seçin ve **Kaydet**' i seçin.

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Azure Güvenlik Merkezi ile Microsoft 'un EDR çözümü, uç nokta için Microsoft Defender arasındaki tümleştirmeyi etkinleştirin":::

    Azure Güvenlik Merkezi, uç nokta için sunucularınızı otomatik olarak Microsoft Defender 'a eklenecektir. Ekleme, 24 saate kadar sürebilir.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Endpoint Portal için Microsoft Defender 'a erişme

1. Kullanıcı hesabının gerekli izinlere sahip olduğundan emin olun. [Microsoft Defender Güvenlik Merkezi 'ne Kullanıcı erişimi atama](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)' da daha fazla bilgi edinin.

1. Anonim trafiği engelleyen bir ara sunucuya veya güvenlik duvarına sahip olup olmadığınızı denetleyin. Endpoint algılayıcı için Defender, sistem bağlamından bağlanır, bu nedenle anonim trafiğe izin verilmelidir. Endpoint Portal için Defender 'a yönelik kaldırma erişimini sağlamak için [proxy sunucusundaki hizmet URL 'lerine erişimi etkinleştirme](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)konusundaki yönergeleri izleyin.

1. [Microsoft Defender Güvenlik Merkezi portalını](https://securitycenter.windows.com/)açın. Portalın Özellikler ve simgeler hakkında daha fazla bilgi için bkz. [Microsoft Defender Güvenlik Merkezi portalına genel bakış](/windows/security/threat-protection/microsoft-defender-atp/portal-overview). 

## <a name="send-a-test-alert"></a>Test uyarısı gönder

Uç nokta sınama uyarısı için bir zararsız Microsoft Defender oluşturmak için:

1. ' C:\Test-mbatp-test ' klasörünü oluşturun.
1. Makinenize erişmek için Uzak Masaüstü 'Nü kullanın.
1. Komut satırı penceresi açın.
1. Komut isteminde aşağıdaki komutu kopyalayın ve çalıştırın. Komut Istemi penceresi otomatik olarak kapatılacak.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Bir test uyarısı oluşturmak için komutuyla birlikte bir komut istemi penceresi.":::

1. Komut başarılı olursa, Azure Güvenlik Merkezi panosunda ve Endpoint Portal için Microsoft Defender 'da yeni bir uyarı görürsünüz. Bu uyarının görünmesi birkaç dakika sürebilir.
1. Güvenlik Merkezi 'nde uyarıyı gözden geçirmek için **güvenlik uyarıları**  >  **şüpheli PowerShell komut satırı** bölümüne gidin.
1. Araştırma penceresinde, uç nokta portalı için Microsoft Defender 'a gitmek üzere bağlantıyı seçin.

    > [!TIP]
    > Uyarı, **bilgilendirme** önem derecesine sahip olarak tetiklenir.

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Güvenlik Merkezi 'nin uç nokta için tümleşik Microsoft Defender hakkında SSS

- [Uç nokta için Microsoft Defender lisans gereksinimleri nelerdir?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Uç nokta için bir Microsoft Defender lisansınız zaten varsa Azure Defender için bir indirim alabilir miyim?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Üçüncü taraf bir EDR aracından geçiş Nasıl yaparım? misiniz?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Uç nokta için Microsoft Defender lisans gereksinimleri nelerdir?
Uç nokta için Defender, **sunucular Için Azure Defender** ile ek bir ücret ödemeden dahil edilmiştir. Alternatif olarak, 50 makineler veya daha fazlası için ayrı olarak satın alınabilir.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Uç nokta için bir Microsoft Defender lisansınız zaten varsa Azure Defender için bir indirim alabilir miyim?
Uç nokta için bir Microsoft Defender lisansınız zaten varsa, Azure Defender lisansınızın bu bölümü için ödeme yapmak zorunda kalmazsınız.

İndirimi onaylamak için, güvenlik merkezi 'nin destek ekibine başvurun ve ilgili her lisans için ilgili çalışma alanı KIMLIĞI, bölge ve lisans bilgilerini belirtin.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Üçüncü taraf bir EDR aracından geçiş Nasıl yaparım? misiniz?
Microsoft 'a ait olmayan bir uç nokta çözümüyle geçiş için tam yönergeler, uç nokta için Microsoft Defender belgelerinde bulunabilir: [geçişe genel bakış](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration).
  


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Merkezi tarafından desteklenen platformlar ve özellikler](security-center-os-coverage.md)
- [Azure Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md): önerilerin Azure kaynaklarınızı korumanıza nasıl yardımcı olduğunu öğrenin.