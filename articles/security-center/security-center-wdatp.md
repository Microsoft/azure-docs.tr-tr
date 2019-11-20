---
title: Microsoft Defender Gelişmiş tehdit koruması-Azure Güvenlik Merkezi
description: Bu belge, Azure Güvenlik Merkezi ile Microsoft Defender Gelişmiş tehdit koruması arasındaki tümleştirmeyi tanıtır.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: 46b9fe5c6a038aa98cf8df64c40bf8ea1747efec
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663604"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Azure Güvenlik Merkezi ile Microsoft Defender Gelişmiş tehdit koruması

Azure Güvenlik Merkezi, [Microsoft Defender Gelişmiş tehdit koruması](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP) ile tümleştirerek bulut Iş yükü koruma platformlarını sunmaktadır.
Bu değişiklik, kapsamlı Uç Nokta Koruma ve Yanıt (EDR) özelliklerini de beraberinde getirmektedir. Microsoft Defender ATP tümleştirmesi ile, normalleştirimler oluşturabilirsiniz. Ayrıca, Azure Güvenlik Merkezi tarafından izlenen sunucu uç noktalarında gelişmiş saldırıları algılayabilir ve bunlara yanıt verebilirsiniz.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Güvenlik Merkezi 'ndeki Microsoft Defender ATP özellikleri

Microsoft Defender ATP kullandığınızda şunları alırsınız:

- **Gelişmiş ihlal sonrası algılama algılayıcı**: Windows Server Için MICROSOFT Defender ATP sensörleri çok sayıda davranış sinyali toplar.

- **Analitik tabanlı, bulut destekli gönderi ihlali algılama**: MICROSOFT Defender ATP, değişen tehditlere hızlı bir şekilde uyum sağlar. Gelişmiş analiz ve büyük veri kullanır. Microsoft Defender ATP, bilinmeyen tehditleri algılamak için Windows, Azure ve Office 'teki sinyallerle Intelligent Security Graph güçlerinin korunmasını sağlar. İşlem yapılabilir uyarılar sağlar ve hızla yanıt vermenize olanak tanır.

- **Tehdit bilgileri**: MICROSOFT Defender ATP, saldırgan araçlarını, tekniklerini ve yordamlarını belirlediğinde uyarı oluşturur. Microsoft tehdit arayıcılar ve güvenlik ekipleri tarafından oluşturulan ve iş ortakları tarafından sunulan zeka tarafından düzenlenen verileri kullanır.

Aşağıdaki yetenekler artık Azure Güvenlik Merkezi 'nde sunulmaktadır:

- **Otomatik ekleme**: MICROSOFT Defender ATP algılayıcısı, Azure Güvenlik Merkezi 'Nde eklendi Windows Server 'lar için otomatik olarak etkinleştirilir.

- **Tek bir cam bölmesi**: Azure Güvenlik Merkezi konsolunda MICROSOFT Defender ATP uyarıları görüntülenir.

- **Ayrıntılı makine araştırması**: Azure Güvenlik Merkezi müşterileri, bir ihlalin kapsamını açmaya yönelik ayrıntılı bir araştırma yapmak Için MICROSOFT Defender ATP konsolu 'nu kullanabilir.

![Azure Güvenlik Merkezi, uyarıların bir listesini ve her uyarıyla ilgili genel bilgileri görüntüleme](media/security-center-wdatp/image1.png)

Daha fazla araştırmak için Microsoft Defender ATP 'yi kullanın. Microsoft Defender ATP, uyarı işlem ağacı ve olay grafiği gibi ek bilgiler sağlar. Ayrıca, altı aya kadar geçmiş bir dönem için her davranışı gösteren ayrıntılı bir makine zaman çizelgesi görebilirsiniz.

![Uyarı hakkında ayrıntılı bilgi içeren Microsoft Defender ATP sayfası](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Platform desteği

Güvenlik Merkezi 'ndeki Microsoft Defender ATP, Windows Server 2016, 2012 R2 ve 2008 R2 SP1 'de algılamayı destekler, Azure VM 'Leri için Standart katman aboneliğine ve Azure dışı VM 'Lere ihtiyacınız varsa yalnızca çalışma alanı düzeyinde Standart katman gerekir.

> [!NOTE]
> Sunucuları izlemek için Azure Güvenlik Merkezi 'ni kullandığınızda, Microsoft Defender ATP kiracısı otomatik olarak oluşturulur ve Microsoft Defender ATP verileri varsayılan olarak Avrupa 'da depolanır. Verilerinizi başka bir konuma taşımanız gerekiyorsa, kiracıyı sıfırlamak için Microsoft Desteği başvurmanız gerekir. Bu tümleştirmenin kullanılmasıyla sunucu uç noktası izleme Office 365 GCC müşterileri için devre dışı bırakıldı.

## <a name="onboarding-servers-to-security-center"></a>Sunucuları güvenlik merkezi 'ne ekleme 

Sunucuları güvenlik merkezi 'ne eklemek için **Azure Güvenlik Merkezi 'Ne git** ' e tıklayarak sunucuları MICROSOFT Defender ATP sunucu ekleme işleminden ekleyin.

1. **Ekleme** alanında, verileri depolamak için bir çalışma alanı seçin veya oluşturun. <br>
2. Tüm çalışma alanlarınızı göremiyorsanız, bunun nedeni izinlerin bulunmaması olabilir, çalışma alanınızın Azure Güvenlik Standart katmanı olarak ayarlandığından emin olun. Daha fazla bilgi için bkz. [Gelişmiş güvenlik Için Güvenlik Merkezi 'Nin standart katmanına yükseltme](security-center-pricing.md).
    
3. Microsoft Monitoring Agent nasıl yükleneceğine ilişkin yönergeleri görüntülemek için **Sunucu Ekle** ' yi seçin. 

4. Ekleme işleminden sonra, **işlem ve uygulamalar**altında makineleri izleyebilirsiniz.

   ![Yerleşik bilgisayarlar](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Microsoft Defender ATP tümleştirmesini etkinleştir

Microsoft Defender ATP tümleştirmesi 'nin etkin olup olmadığını görmek için **güvenlik merkezi** > **fiyatlandırma & ayarları** ' nı seçin > aboneliğinize tıklayın.
Burada, şu anda etkin olan entegrasyona bakabilirsiniz.

  ![Microsoft Defender ATP tümleştirmesi etkin olan Azure Güvenlik Merkezi tehdit algılama ayarları sayfası](media/security-center-wdatp/enable-integrations.png)

- Sunucuları zaten Azure Güvenlik Merkezi Standart katmanına eklendi, başka bir işlem gerçekleştirmeniz gerekir. Azure Güvenlik Merkezi, sunucuları otomatik olarak Microsoft Defender ATP 'ye eklenecektir. Ekleme, 24 saate kadar sürebilir.

- Sunucuları hiçbir zaman Azure Güvenlik Merkezi Standart katmanına eklendi, bunları her zamanki gibi Azure Güvenlik Merkezi 'ne ekleyin.

- Sunucuları Microsoft Defender ATP aracılığıyla eklendi:
  - [Sunucu makinelerini nasıl boşaltıkılabileceğine](https://go.microsoft.com/fwlink/p/?linkid=852906)ilişkin yönergeler için belgelere bakın.
  - Bu sunucuları Azure Güvenlik Merkezi 'ne ekleyin.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Microsoft Defender ATP portalına erişim

[Portala kullanıcı erişimi atama](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access)' daki yönergeleri izleyin.

## <a name="set-the-firewall-configuration"></a>Güvenlik duvarı yapılandırmasını ayarlama

Anonim trafiği engelleyen bir ara sunucu veya güvenlik duvarınız varsa, Microsoft Defender ATP algılayıcısı sistem bağlamından bağlandığında, anonim trafiğe izin verildiğini doğrulayın. [Proxy sunucusundaki Microsoft Defender ATP hizmeti URL 'lerine erişimi etkinleştirme](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)' deki yönergeleri izleyin.

## <a name="test-the-feature"></a>Özelliği test etme

Bir zararsız Microsoft Defender ATP test uyarısı oluşturmak için:

1. Uzak Masaüstü 'Nü kullanarak bir Windows Server 2012 R2 VM 'sine veya Windows Server 2016 sanal makinesine erişin. Bir komut istemi penceresi açın.

2. Komut isteminde aşağıdaki komutu kopyalayın ve çalıştırın. Komut Istemi penceresi otomatik olarak kapatılacak.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Yukarıdaki komutla bir komut Istemi penceresi](media/security-center-wdatp/image4.jpeg)

3. Komut başarılı olursa, Azure Güvenlik Merkezi panosunda ve Microsoft Defender ATP portalında yeni bir uyarı görürsünüz. Bu uyarının görünmesi birkaç dakika sürebilir.

4. Güvenlik Merkezi 'nde uyarıyı gözden geçirmek için **güvenlik uyarıları** > **şüpheli PowerShell komut satırı**' na gidin.

5. Araştırma penceresinde, Microsoft Defender ATP portalına gitmek için bağlantıyı seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Merkezi tarafından desteklenen platformlar ve özellikler](security-center-os-coverage.md)
- [Azure Güvenlik Merkezi 'nde güvenlik Ilkelerini ayarlama](tutorial-security-policy.md): Azure abonelikleriniz ve kaynak gruplarınız için güvenlik ilkelerini yapılandırma hakkında bilgi edinin.
- [Azure Güvenlik Merkezi 'nde güvenlik önerilerini yönetme](security-center-recommendations.md): önerilerin Azure kaynaklarınızı korumanıza nasıl yardımcı olduğunu öğrenin.
- [Azure Güvenlik Merkezi'nde güvenlik durumunu izleme](security-center-monitoring.md): Azure kaynaklarınızın sistem durumunu nasıl izleyeceğiniz hakkında bilgi edinin.
