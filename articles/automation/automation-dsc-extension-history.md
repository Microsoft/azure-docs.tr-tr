---
title: Azure Istenen durum yapılandırma uzantısı sürüm geçmişi ile çalışma
description: Bu makale, Azure 'daki Istenen durum yapılandırması (DSC) uzantısı için sürüm geçmişi bilgilerini paylaşır.
ms.date: 02/17/2021
keywords: DSC, PowerShell, Azure, uzantı
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: b272993cdb1b89e63190d2c8c9ef80db2327ccc1
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167524"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Azure Istenen durum yapılandırma uzantısı sürüm geçmişi ile çalışma

Azure Istenen durum yapılandırması (DSC) VM [Uzantısı](../virtual-machines/extensions/dsc-overview.md) , Azure, Windows Server ve Windows PowerShell 'ı Içeren Windows Management Framework (WMF) tarafından sunulan geliştirmeleri ve yeni özellikleri desteklemek için gereken şekilde güncelleştirilir.

Bu makalede, Azure DSC VM uzantısının her sürümü, desteklediği ortamlar ve yeni özellikler ya da değişikliklerle ilgili açıklamalar ve açıklamalar hakkında bilgi sağlanır.

## <a name="latest-version"></a>En son sürüm

### <a name="version-283"></a>Sürüm 2,83

- **Yayın tarihi:**
  - Şubat 2021
- **İşletim sistemi desteği:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Istemcisi 7/8.1/10
  - Nano Sunucu
- **WMF desteği:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0 güncelleştirmesi
  - WMF 4,0
- **Ortamınızın**
  - Azure
  - Azure Çin Vianet 21
  - Azure Kamu
- **Açıklamalar:** Bu sürümde, Windows VM uzantısına sahip imzasız ikililer için bir çözüm bulunur.

### <a name="version-280"></a>Sürüm 2,80

- **Yayın tarihi:**
  - 26 Eylül, Eyl-2019 (Azure) | 6 Temmuz 2020 (Azure Çin Vianet 21) | 20 Temmuz 2020 (Azure Kamu)
- **İşletim sistemi desteği:**
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Istemcisi 7/8.1/10
  - Nano Sunucu
- **WMF desteği:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0 güncelleştirmesi
  - WMF 4,0
- **Ortamınızın**
  - Azure
  - Azure Çin Vianet 21
  - Azure Kamu
- **Açıklamalar:** Bu yayına yeni özellik dahil değildir.

### <a name="version-276"></a>Sürüm 2,76

- **Yayın tarihi:**
  - 9 Mayıs 2018 (Azure) | 21 Haziran 2018 (Azure Çin Vianet 21, Azure Kamu)
- **İşletim sistemi desteği:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows Istemcisi 7/8.1/10
  - Nano Sunucu
- **WMF desteği:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0 güncelleştirmesi
  - WMF 4,0
- **Ortamınızın**
  - Azure
  - Azure Çin Vianet 21
  - Azure Kamu
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (WMF yükleme için bir yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - Alt durum ve diğer küçük hata düzeltmeleri için uzantı meta verilerinde geliştirme.

## <a name="supported-versions"></a>Desteklenen sürümler

> [!WARNING]
> 2,4 ile 2,13 arasındaki sürümler, imza sertifikalarının zaman aşımına erdiği, 2016 Ağustos ayında, WMF 5,0 genel önizlemeyi kullanır.
> Bu sorun hakkında daha fazla bilgi için aşağıdaki [Blog makalesine](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/)bakın.

### <a name="version-275"></a>Sürüm 2,75

- **Yayın tarihi:** 5 Mart 2018
- **Işletim sistemi desteği:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, nano sunucu
- **WMF desteği:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (WMF yükleme için bir yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - GitHub, TLS 1,2 ' i zorladıktan sonra Azure Marketi 'nde bulunan DIY Kaynak Yöneticisi şablonlarını kullanarak bir VM 'yi Azure Otomasyonu durum yapılandırmasına ekleyemez veya GitHub 'da barındırılan tüm yapılandırmaları almak için DSC Uzantısı 'nı kullanabilirsiniz. Uzantı dağıtıldığında aşağıdakine benzer bir hata döndürülür:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - Yeni uzantı sürümünde TLS 1,2 artık zorlanır. Uzantı dağıtıldığında, `AutoUpgradeMinorVersion = true` Kaynak Yöneticisi şablonunda zaten belirtilmişse, uzantı 2,75 'e yeniden yükseltilir. El ile güncelleştirmeler için `TypeHandlerVersion = 2.75` Kaynak Yöneticisi şablonunuzda belirtin.

### <a name="version-270---272"></a>Sürüm 2,70-2,72

- **Yayın tarihi:** 13 Kasım 2017
- **Işletim sistemi desteği:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, nano sunucu
- **WMF desteği:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (WMF yükleme için bir yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - Hata düzeltmeleri, portalda ve bir Kaynak Yöneticisi şablonuyla Azure Otomasyonu durum yapılandırması kullanımını kolaylaştıran iyileştirmeler &. Daha fazla bilgi için bkz. DSC Uzantısı belgelerindeki [varsayılan yapılandırma betiği](../virtual-machines/extensions/dsc-overview.md) .

### <a name="version-226"></a>Sürüm 2,26

- **Yayın tarihi:** 9 Haziran 2017
- **Işletim sistemi desteği:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, nano sunucu
- **WMF desteği:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (WMF yükleme için bir yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - Telemetri geliştirmeleri.

### <a name="version-225"></a>Sürüm 2,25

- **Yayın tarihi:** 2 Haziran 2017
- **Işletim sistemi desteği:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10, nano sunucu
- **WMF desteği:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (WMF yükleme için bir yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - Çeşitli hata düzeltmeleri ve diğer küçük geliştirmeler eklenmiştir.

### <a name="version-224"></a>Sürüm 2,24

- **Yayın tarihi:** 13 Nisan 2017
- **Işletim sistemi desteği:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano Server
- **WMF desteği:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (WMF yükleme için bir yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - Uzantı meta verileri olarak & DSC Aracısı kimliği VM UUID 'sini gösterir. Diğer küçük iyileştirmeler eklendi.

### <a name="version-223"></a>Sürüm 2,23

- **Yayın tarihi:** 15 Mart 2017
- **Işletim sistemi desteği:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano Server
- **WMF desteği:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (WMF yükleme için bir yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - Hata düzeltmeleri ve diğer geliştirmeler eklenmiştir.

### <a name="version-222"></a>Sürüm 2,22

- **Yayın tarihi:** 8 Şubat 2017
- **Işletim sistemi desteği:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano Server
- **WMF desteği:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (WMF yükleme için bir yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - DSC Uzantısı artık WMF 5,1 ' i desteklemektedir.
  - Diğer küçük geliştirmeler eklenmiştir.

### <a name="version-221"></a>Sürüm 2,21

- **Yayın tarihi:** 2 Aralık 2016
- **Işletim sistemi desteği:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano Server
- **WMF desteği:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 güncelleştirmesi, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 ' de yer alan DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) 'YI (WMF yüklemesi için yeniden başlatma gerekir) yüklenir. Nano sunucu için, sanal makinede DSC rolü yüklüdür.
- **Yeni Özellikler:**
  - DSC Uzantısı artık nano Server 'da kullanılabilir. Bu sürüm öncelikle, uzantıyı nano sunucu 'da çalıştırmaya yönelik kod değişiklikleri içerir.
  - Diğer küçük geliştirmeler eklenmiştir.

### <a name="version-220"></a>Sürüm 2,20

- **Yayın tarihi:** 2 Ağustos 2016
- **Işletim sistemi desteği:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF desteği:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 güncelleştirmesi, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 Technical Preview 'a dahil edilen DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) 'YI (WMF yüklemesi için yeniden başlatma gerekir) yüklenir.
- **Yeni Özellikler:**
  - WMF 5,1 Preview desteği. İlk yayımlandığında, bu sürüm isteğe bağlı bir yükseltiyordu ve WMF 5,1 Preview sürümünü yüklemek için Kaynak Yöneticisi şablonlarda Wmfversion = ' 5.1 PP ' belirtmeniz gerekiyordu.
    Wmfversion = ' Latest ' hala [WMF 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/)'yi yüklüyor.
    WMF 5,1 Preview hakkında daha fazla bilgi için [Bu bloga](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/)bakın.
  - Diğer küçük düzeltmeler ve geliştirmeler eklenmiştir.

### <a name="version--219"></a>Sürüm 2,19

- **Yayın tarihi:** 3 Haziran 2016
- **Işletim sistemi desteği:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF desteği:** WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Azure, Azure Çin Vianet 21, Azure Kamu
- **Açıklamalar:** Bu sürüm, Windows Server 2016 Technical Preview 'a dahil edilen DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) 'YI (WMF yüklemesi için yeniden başlatma gerekir) yüklenir.
- **Yeni Özellikler:**
  - DSC Uzantısı artık Azure Çin Vianet 21 sürümünde kullanılabilir. Bu sürüm, uzantıyı Azure Çin Vianet 21 tarihinde çalıştırmaya yönelik düzeltmeler içerir.

### <a name="version-218"></a>Sürüm 2,18

- **Yayın tarihi:** 3 Haziran 2016
- **Işletim sistemi desteği:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF desteği:** WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 Technical Preview 'a dahil edilen DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) 'YI (WMF yüklemesi için yeniden başlatma gerekir) yüklenir.
- **Yeni Özellikler:**
  - Telemetri Düzeltme İndirmesi (bilinen Azure DNS sorunu) sırasında veya yükleme sırasında bir hata oluştuğunda telemetri engellenmemiş hale getirin.
  - Yeniden başlatmadan sonra uzantının yapılandırmayı işlemeyi durdurduğu aralıklı soruna yönelik çözüm. Bu, DSC uzantısının ' geçiş ' durumunda kalmasına neden oldu.
  - Diğer küçük düzeltmeler ve geliştirmeler eklenmiştir.

### <a name="version-217"></a>Sürüm 2,17

- **Yayın tarihi:** 26 Nisan 2016
- **Işletim sistemi desteği:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF desteği:** WMF 5,0 RTM, WMF 4,0 güncelleştirme, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 Technical Preview 'a dahil edilen DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) 'YI (WMF yüklemesi için yeniden başlatma gerekir) yüklenir.
- **Yeni Özellikler:**
  - WMF 4,0 güncelleştirmesi desteği. WMF 4,0 güncelleştirmesi hakkında daha fazla bilgi için [Bu bloga](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/)bakın.
  - DSC Uzantısı yüklemesi sırasında oluşan hatalarda veya bir DSC yapılandırma sonrası yüklemesi uygulanırken oluşan hatalarda yeniden deneme mantığı. Bu değişikliğin bir parçası olarak, önceki yükleme başarısız olursa uzantı yüklemeyi yeniden dener veya daha önce başarısız olan bir DSC yapılandırmasını yeniden harekete (başarı/hata) veya yeni bir istek gelirse, en fazla üç kez daha önceden başarısız olur. Uzantı geçersiz Kullanıcı ayarları/kullanıcı girişi nedeniyle başarısız olursa, yeniden denenmez. Bu durumda, uzantının yeni bir istek ile yeniden çağrılması ve Kullanıcı ayarları doğru olması gerekir. 

  > [!NOTE]
   > DSC Uzantısı, yeniden denemeler için Azure VM aracısına bağımlıdır. Azure VM Aracısı, bir başarılı veya hata durumuna ulaşıncaya kadar uzantıyı son başarısız istekle çağırır.

### <a name="version-216"></a>Sürüm 2,16

- **Yayın tarihi:** 21 Nisan 2016
- **Işletim sistemi desteği:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF desteği:** WMF 5,0 RTM, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 Technical Preview 'a dahil edilen DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) 'YI (WMF yüklemesi için yeniden başlatma gerekir) yüklenir.
- **Yeni Özellikler:**
  - Hata işlemede ve diğer küçük hata düzeltmelerde geliştirme.
  - DSC uzantı ayarlarındaki yeni özellik. `ForcePullAndApply` Gelişmiş Seçenekler ' de, yenileme modu çekme olduğunda (varsayılan gönderim modundan farklı olarak) DSC Uzantısı 'nın DSC yapılandırmalarına olanak tanımak için bir adım adım eklenmiştir. DSC Uzantısı ayarları hakkında daha fazla bilgi için [Bu bloga](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)başvurun.

### <a name="version-215"></a>Sürüm 2,15

- **Yayın tarihi:** 14 Mart 2016
- **Işletim sistemi desteği:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF desteği:** WMF 5,0 RTM, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 Technical Preview 'a dahil edilen DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) 'YI (WMF yüklemesi için yeniden başlatma gerekir) yüklenir.
- **Yeni Özellikler:**
  - Uzantı sürümü 2,14 ' de, WMF RTM 'yi yüklemek için değişiklikler eklenmiştir. 2.13.2.0 uzantısından 2.14.0.0 sürümüne yükseltirken bazı DSC cmdlet 'lerinin başarısız olduğunu veya yapılandırmanızın bir hata ile başarısız olduğunu fark edebilirsiniz-' verilen özellik değerlerine sahip bir örnek bulunamadı '. Daha fazla bilgi için bkz. [DSC sürüm notları](/powershell/scripting/wmf/known-issues/known-issues-dsc). Bu sorunlara yönelik geçici çözümler 2,15 sürümüne eklenmiştir. 
  - Sürüm 2,14 ' ü zaten yüklediyseniz ve yukarıdaki iki sorunlardan birinde çalıştırıyorsanız, bu adımları el ile gerçekleştirmeniz gerekir. Yükseltilmiş bir PowerShell oturumunda aşağıdaki komutları çalıştırın:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Sürüm 2,14

- **Yayın tarihi:** 25 Şubat 2016
- **Işletim sistemi desteği:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF desteği:** WMF 5,0 RTM, WMF 4,0
- **Ortam:** Mavisi
- **Açıklamalar:** Bu sürüm, Windows Server 2016 Technical Preview 'a dahil edilen DSC kullanır; diğer Windows OSs için [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) 'YI (WMF yüklemesi için yeniden başlatma gerekir) yüklenir.
- **Yeni Özellikler:**
  - WMF RTM kullanır.
  - DSC uzantısının kalitesini artırmak için veri toplamayı sağlar. Daha fazla bilgi için bu [Blog makalesine](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)bakın.
  - Kaynak Yöneticisi şablonunda uzantı için güncelleştirilmiş bir ayar biçimi sağlar. Daha fazla bilgi için bu [Blog makalesine](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)bakın.
  - Hata düzeltmeleri ve diğer geliştirmeler.

## <a name="next-steps"></a>Sonraki adımlar

- PowerShell DSC hakkında daha fazla bilgi için bkz. [PowerShell belge merkezi](/powershell/scripting/dsc/overview/overview).
- [DSC uzantısının Kaynak Yöneticisi şablonunu](../virtual-machines/extensions/dsc-template.md)inceleyin.
- PowerShell DSC ile yönetebileceğiniz diğer işlevsellik ve kaynaklar için [PowerShell galerisine](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)gözatamazsınız.
- Hassas parametreleri yapılandırmalara geçirme hakkında ayrıntılı bilgi için bkz. [DSC uzantı işleyicisiyle kimlik bilgilerini güvenli bir şekilde yönetme](../virtual-machines/extensions/dsc-credentials.md).
