---
title: Windows bilgisayarlarını Azure Izleyici 'ye bağlama | Microsoft Docs
description: Bu makalede, Windows için Log Analytics aracısıyla diğer bulutlarda veya şirket içinde barındırılan Windows bilgisayarlarının Azure Izleyici 'ye nasıl bağlanacağı açıklanır.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: abe114a989c4ec672d391a7fd7d83341d4c52638
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932783"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Windows bilgisayarlarını Azure Izleyici 'ye bağlama

Yerel veri merkezinizdeki veya Azure Izleyici ile diğer bulut ortamlarınızdaki sanal makineleri veya fiziksel bilgisayarları izlemek ve yönetmek için, Log Analytics aracısını dağıtmanız (Ayrıca Microsoft Monitoring Agent (MMA) olarak da bilinir) ve yapılandırmak gerekir bir veya daha fazla Log Analytics çalışma alanına rapor edin. Aracı Ayrıca Azure Otomasyonu için karma Runbook Worker rolünü destekler.  

İzlenen bir Windows bilgisayarında, aracı Microsoft Monitoring Agent hizmeti olarak listelenir. Microsoft Monitoring Agent hizmeti, günlük dosyalarından ve Windows olay günlüğü, performans verileri ve diğer telemetriden olayları toplar. Aracı BT raporlarının Azure Izleyici ile iletişim kuramasa bile, aracı çalışmaya devam eder ve toplanan verileri izlenen bilgisayarın diskinde sıralar. Bağlantı geri yüklendiğinde Microsoft Monitoring Agent hizmeti toplanan verileri hizmete gönderir.

Aracı aşağıdaki yöntemlerden biri kullanılarak yüklenebilir. Çoğu yükleme, uygun şekilde farklı bilgisayar kümelerini yüklemek için bu yöntemlerin bir birleşimini kullanır.  Her yöntemin kullanımıyla ilgili ayrıntılar, makalenin ilerleyen kısımlarında verilmiştir.

* El ile yükleme. Kurulum, Kurulum Sihirbazı 'nı kullanarak, komut satırından veya mevcut bir yazılım dağıtım Aracı kullanılarak dağıtıldıktan sonra, bilgisayar üzerinde el ile çalıştırılır.
* Azure Otomasyonu Istenen durum yapılandırması (DSC). Ortamınızda zaten dağıtılan Windows bilgisayarları için bir komut dosyası ile Azure Otomasyonu 'nda DSC 'yi kullanma.  
* PowerShell betiği.
* Azure Stack 'de Windows Şirket içi çalıştıran sanal makineler için şablon Kaynak Yöneticisi. 

>[!NOTE]
>Azure Güvenlik Merkezi (ASC), Microsoft Monitoring Agent bağımlıdır (Ayrıca, Log Analytics Windows Aracısı olarak da adlandırılır) ve dağıtımı bir parçası olarak bir Log Analytics çalışma alanına raporlamak üzere yükleyecek ve yapılandıracaktır. ASC, aboneliğinizdeki tüm sanal makinelere Log Analytics Windows aracısının otomatik olarak yüklenmesini sağlayan ve belirli bir çalışma alanına rapor verecek şekilde yapılandıran bir otomatik sağlama seçeneği içerir. Bu seçenek hakkında daha fazla bilgi için bkz. [Log Analytics aracısının otomatik sağlamasını etkinleştirme](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-the-log-analytics-agent-).
>

Aracıyı birden fazla çalışma alanına rapor verecek şekilde yapılandırmanız gerekiyorsa, bu, ilk kurulum sırasında yapılamaz, yalnızca [bir çalışma alanı ekleme veya kaldırma](agent-manage.md#adding-or-removing-a-workspace)bölümünde açıklandığı gibi Denetim Masası 'Ndan veya PowerShell 'den daha sonra ayarları güncelleyerek.  

Desteklenen yapılandırmayı anlamak için [desteklenen Windows işletim sistemlerini](log-analytics-agent.md#supported-windows-operating-systems) ve [ağ güvenlik duvarı yapılandırmasını](log-analytics-agent.md#network-firewall-requirements) inceleyin.

## <a name="obtain-workspace-id-and-key"></a>Çalışma alanı kimliği ve anahtarını alma
Windows için Log Analytics aracısını yüklemeden önce, Log Analytics çalışma alanınız için çalışma alanı KIMLIĞI ve anahtarı gereklidir.  Bu bilgiler, her bir yükleme yönteminden kurulum sırasında aracıyı doğru şekilde yapılandırmak ve Azure ticari ve ABD kamu bulutunda Azure Izleyici ile başarıyla iletişim kurabildiğinden emin olmak için gereklidir. 

1. Azure portalında **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics**’i seçin.
2. Log Analytics çalışma alanları listenizde, aracıyı raporlamak üzere yapılandırmayı planladığınız çalışma alanını seçin.
3. **Gelişmiş ayarlar**’ı seçin.<br><br> ![Log Analytics Gelişmiş Ayarlar](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. **Bağlı Kaynaklar**’ı seçin ve ardından **Windows Sunucuları**’nı seçin.   
5. **Çalışma alanı kimliği** ve **birincil anahtar**olan en sevdiğiniz düzenleyiciye kopyalayıp yapıştırın.    
   
## <a name="configure-agent-to-use-tls-12"></a>Aracıyı TLS 1,2 kullanacak şekilde yapılandırma
Windows Aracısı ve Log Analytics hizmeti arasındaki iletişimde [TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) protokolünün kullanımını yapılandırmak için, aracı sanal makineye yüklenmeden önce veya daha sonra etkinleştirmek üzere aşağıdaki adımları izleyebilirsiniz.

>[!NOTE]
>Windows Server 2008 SP2 x64 çalıştıran bir VM 'yi TLS 1,2 kullanmak üzere yapılandırıyorsanız, önce aşağıdaki adımları gerçekleştirmeden önce aşağıdaki [SHA-2 kod imzalama desteği güncelleştirmesini](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) yüklemeniz gerekir. 
>

1. Şu kayıt defteri alt anahtarını bulun: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. TLS 1,2 için **protokoller** altında bir alt anahtar oluşturma **Hklm\system\currentcontrolset\control\securityproviders\schannel\protocols\tls 1,2**
3. Daha önce oluşturduğunuz TLS 1,2 protokol sürümü alt anahtarı altında bir **istemci** alt anahtarı oluşturun. Örneğin, **Hklm\system\currentcontrolset\control\securityproviders\schannel\protocols\tls 1.2 \ Client**.
4. **Hklm\system\currentcontrolset\control\securityproviders\schannel\protocols\tls 1.2 \ Client**altında aşağıdaki DWORD değerlerini oluşturun:

    * **Etkin** [değer = 1]
    * **DisabledByDefault** [değer = 0]  

Varsayılan olarak devre dışı olduğu gibi, .NET Framework 4,6 veya üstünü güvenli şifrelemeyi destekleyecek şekilde yapılandırın. [Güçlü şifreleme](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) , TLS 1,2 gibi daha güvenli ağ protokolleri kullanır ve güvenli olmayan protokolleri engeller. 

1. Şu kayıt defteri alt anahtarını bulun: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v4.0.30319**.  
2. Bu alt anahtar altında **1**değeriyle **Schusestrongşifre** DWORD değeri oluşturun.  
3. Şu kayıt defteri alt anahtarını bulun: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319**.  
4. Bu alt anahtar altında **1**değeriyle **Schusestrongşifre** DWORD değeri oluşturun. 
5. Ayarların etkili olması için sistemi yeniden başlatın. 

## <a name="install-the-agent-using-setup-wizard"></a>Kurulum sihirbazını kullanarak aracı yükleme
Aşağıdaki adımlar, bilgisayarınızda aracı için Kurulum Sihirbazı 'nı kullanarak Azure ve Azure Kamu bulutundaki Log Analytics aracısını yükleyip yapılandırır. Aracıyı bir System Center Operations Manager yönetim grubuna da raporlamak üzere nasıl yapılandıracağınızı öğrenmek istiyorsanız, bkz. [Aracı Kurulum Sihirbazı ile Operations Manager aracısını dağıtma](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. Log Analytics çalışma alanınızda, daha önce gidilen **Windows sunucuları** sayfasında, Windows işletim sisteminin işlemci mimarisine bağlı olarak indirmek Için uygun **Windows aracısını indir** sürümünü seçin.   
2. Aracıyı bilgisayarınıza yüklemek için Kurulum'u çalıştırın.
2. **Hoş Geldiniz** sayfasında **İleri**'ye tıklayın.
3. **Lisans Koşulları** sayfasında, lisansı okuyun ve **Kabul Ediyorum**'a tıklayın.
4. **Hedef Klasör** sayfasında, varsayılan yükleme klasörünü değiştirin veya koruyun ve ardından **İleri**'ye tıklayın.
5. **Aracı Kurulum Seçenekleri** sayfasında, aracıyı Azure Log Analytics'e bağlamayı seçin ve ardından **İleri**'ye tıklayın.   
6. **Azure Log Analytics** sayfasında aşağıdakileri yapın:
   1. Daha önce kopyaladığınız **Çalışma Alanı Kimliği** ve **Çalışma Alanı Anahtarı (Birincil Anahtar)** değerlerini yapıştırın.  Bilgisayarın Azure Kamu bulutundaki bir Log Analytics çalışma alanına raporlaması gerekiyorsa, **Azure Cloud** açılan listesinden **Azure ABD Kamu**'yu seçin.  
   2. Bilgisayarın Log Analytics hizmetiyle bir ara sunucu üzerinden iletişim kurması gerekiyorsa, **Gelişmiş**'e tıklayın ve ara sunucunun URL'siyle bağlantı noktası numarasını sağlayın.  Ara sunucunuz kimlik doğrulaması gerektiriyorsa, ara sunucuyla kimlik doğrulaması yapmak için kullanıcı adını ve parolayı yazın, ardından **İleri**'ye tıklayın.  
7. Gerekli yapılandırma ayarlarını sağlamayı tamamladığınızda **İleri**'ye tıklayın.<br><br> ![Çalışma Alanı Kimliği ve Birincil Anahtarı'nı yapıştırın](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. **Yüklemeye Hazır** sayfasında seçimlerinizi gözden geçirin ve ardından **Yükle**'ye tıklayın.
9. **Yapılandırma başarıyla tamamlandı** sayfasında **Son**'a tıklayın.

Tamamlandığında, **Denetim Masası**'nda **Microsoft Monitoring Agent** gösterilir. Log Analytics rapor ettiğini onaylamak için, [Log Analytics için aracı bağlantısını doğrula](#verify-agent-connectivity-to-log-analytics)' yı gözden geçirin. 

## <a name="install-the-agent-using-the-command-line"></a>Komut satırını kullanarak aracıyı yükler
Aracının indirilen dosyası, kendi içinde bulunan bir yükleme paketidir.  Aracının ve destekleyici dosyaların kurulum programı pakette bulunur ve aşağıdaki örneklerde gösterilen komut satırı kullanılarak düzgün bir şekilde yüklenmek üzere ayıklanmalıdır.    

>[!NOTE]
>Bir aracıyı yükseltmek istiyorsanız, Log Analytics betik oluşturma API 'sini kullanmanız gerekir. Daha fazla bilgi için bkz. [Windows ve Linux için Log Analytics aracısını yönetme ve koruma](agent-manage.md) konusuna bakın.

Aşağıdaki tabloda, Automation DSC kullanılarak dağıtıldığında de dahil olmak üzere, aracının kurulumu tarafından desteklenen belirli parametreler vurgulanmıştır.

|MMA özgü seçenekler                   |Notlar         |
|---------------------------------------|--------------|
| NOAPM = 1                               | İsteğe bağlı parametre. Aracıyı .NET uygulama performansı Izleme olmadan yüklenir.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = aracıyı bir çalışma alanına rapor verecek şekilde yapılandırma                |
|OPINSIGHTS_WORKSPACE_ID                | Eklenecek çalışma alanı KIMLIĞI (GUID)                    |
|OPINSIGHTS_WORKSPACE_KEY               | Çalışma alanı ile ilk kimlik doğrulaması için kullanılan çalışma alanı anahtarı |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Çalışma alanının bulunduğu bulut ortamını belirtin <br> 0 = Azure ticari bulutu (varsayılan) <br> 1 = Azure Kamu |
|OPINSIGHTS_PROXY_URL               | Kullanılacak proxy için URI |
|OPINSIGHTS_PROXY_USERNAME               | Kimliği doğrulanmış bir ara sunucuya erişmek için Kullanıcı adı |
|OPINSIGHTS_PROXY_PASSWORD               | Kimliği doğrulanmış bir ara sunucuya erişmek için parola |

1. Aracı yükleme dosyalarını ayıklamak için, yükseltilmiş bir komut isteminden `MMASetup-<platform>.exe /c` ' ı çalıştırın ve dosyaları ayıklama yolunu sorar.  Alternatif olarak, `MMASetup-<platform>.exe /c /t:<Full Path>` bağımsız değişkenlerini geçirerek yolu belirtebilirsiniz.  
2. Aracıyı sessizce yüklemek ve Azure ticari bulutundaki bir çalışma alanına raporlamak üzere yapılandırmak için, kurulum dosyalarını ayıkladığınız klasörden şunu yazın: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   ya da aracıyı Azure ABD kamu bulutuna bildirilecek şekilde yapılandırmak için şunu yazın: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >*OPINSIGHTS_WORKSPACE_ID* ve *OPINSIGHTS_WORKSPACE_KEY* parametrelerinin dize değerlerinin, paket için geçerli seçenekler olarak Windows Installer söylemek üzere çift tırnaklı şekilde kapsüllenmesi gerekir. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Azure Otomasyonu 'nda DSC 'yi kullanarak aracı 'nı yükler

Aracıyı Azure Automation DSC kullanarak yüklemek için aşağıdaki betik örneğini kullanabilirsiniz.   Bir Otomasyon hesabınız yoksa, Automation DSC kullanmadan önce gerekli bir Otomasyon hesabı oluşturmaya yönelik gereksinimleri ve adımları anlamak için bkz. [Azure Otomasyonu ile çalışmaya başlama](/azure/automation/) .  Automation DSC hakkında bilgi sahibi değilseniz [Automation DSC kullanmaya](../../automation/automation-dsc-getting-started.md)başlama konusunu inceleyin.

Aşağıdaki örnek, `URI` değeri tarafından tanımlanan 64 bitlik aracıyı yüklenmektedir. Ayrıca, URI değerini değiştirerek 32 bit sürümünü de kullanabilirsiniz. Her iki sürümün URI 'Leri şunlardır:

- Windows 64-bit Aracısı- https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bit Aracısı- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Bu yordam ve betik örneği, bir Windows bilgisayarına zaten dağıtılan aracının yükseltilmesini desteklemez.

Aracı paketinin 32-bit ve 64 bit sürümleri farklı ürün kodlarına sahiptir ve yayımlanan yeni sürümler de benzersiz bir değer içermelidir.  Ürün kodu, bir uygulamanın veya ürünün asıl kimliği olan ve Windows Installer **ProductCode** özelliği tarafından temsil EDILEN bir GUID 'dir.  **Mmagent. ps1** betiğindeki `ProductId` değeri 32-bit veya 64 bit aracı yükleyicisi paketindeki ürün koduyla eşleşmelidir.

Ürün kodunu aracı yüklemesi paketinden doğrudan almak için, Windows yazılım geliştirme seti 'nin bir bileşeni olan veya PowerShell 'i kullanarak [](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) bir [bileşen olan Windows Installer geliştiriciler Için Windows SDK bileşenlerinden Orca. exe ' yi kullanabilirsiniz. örnek betik](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) , Microsoft değerli bir profesyonel (MVP) tarafından yazılmıştır.  Her iki yaklaşım için, önce MMASetup yükleme paketinden **MOMAgent. msi** dosyasını ayıklamanız gerekir.  Bu, [komut satırını kullanarak aracıyı Install](#install-the-agent-using-the-command-line)bölümünün altındaki ilk adımda gösterilmektedir.  

1. XPSDesiredStateConfiguration DSC modülünü [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) ' den Azure Otomasyonu 'na aktarın.  
2.  *OPSINSIGHTS_WS_ID* ve *OPSINSIGHTS_WS_KEY*için Azure Otomasyonu değişken varlıkları oluşturun. Log Analytics çalışma alanı KIMLIĞINIZLE *OPSINSIGHTS_WS_ID* ayarlayın ve *OPSINSIGHTS_WS_KEY* ' i çalışma alanınızın birincil anahtarına ayarlayın.
3.  Betiği kopyalayın ve MMAgent. ps1 olarak kaydedin.

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Betikteki `ProductId` değerini, daha önce önerilen yöntemleri kullanarak aracı yüklemesi paketinin en son sürümünden ayıklanan ürün koduyla güncelleştirin. 
5. [MMAgent. ps1 yapılandırma betiğini](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) Otomasyon hesabınıza aktarın. 
5. Yapılandırmaya [bir Windows bilgisayarı veya düğüm atayın](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) . 15 dakika içinde düğüm yapılandırmasını denetler ve aracı düğümüne gönderilir.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Log Analytics için aracı bağlantısını doğrula

Aracının yüklenmesi tamamlandıktan sonra, başarılı bir şekilde bağlandığının doğrulanması ve raporlama iki şekilde gerçekleştirilebilir.  

Bilgisayarın **Denetim Masası** sayfasında **Microsoft Monitoring Agent**'ı bulun.  Bunu seçin ve **Azure Log Analytics** sekmesinde aracı şöyle bir ileti görüntülemelidir: **Microsoft Monitoring Agent Microsoft Operations Management Suite hizmetine başarıyla bağlandı.**<br><br> ![MMA'nın Log Analytics'e bağlantı durumu](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Azure portal basit bir günlük sorgusu da gerçekleştirebilirsiniz.  

1. Azure portalında **Tüm hizmetler**’e tıklayın. Kaynak listesinde **Azure izleyici**yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Azure izleyici**'yi seçin.  
2. Menüde **Günlükler** ' i seçin. 
2. Günlükler bölmesinde, sorgu alanına şunu yazın:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Döndürülen arama sonuçlarında, bağlı olduğunu ve hizmete raporlanmasını belirten bilgisayar için sinyal kayıtlarını görmeniz gerekir.   

## <a name="next-steps"></a>Sonraki adımlar

- Aracıyı sanal makineden yeniden yapılandırma, yükseltme veya kaldırma hakkında bilgi edinmek için [Windows ve Linux için Log Analytics aracısının yönetimini ve güvenliğini](agent-manage.md) gözden geçirin.

- Aracıyı yüklerken veya yönetirken sorunlarla karşılaşırsanız [Windows Agent sorunlarını giderme](agent-windows-troubleshoot.md) konusunu gözden geçirin.
