---
title: Azure Izleyici 'de tel veri çözümü | Microsoft Docs
description: Kablo verileri, Log Analytics aracıları olan bilgisayarlardan birleştirilmiş ağ ve performans verileri. Verilerin bağıntısını sağlamanıza yardımcı olmak için ağ verileri günlük verilerinizle birleştirilir.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/03/2018
ms.openlocfilehash: 5e19c9bd47fe253f9a416b923ec0cb1748682842
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900584"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Azure Izleyici 'de Wire Data 2.0 (Önizleme) çözümü

![Wire Data sembolü](media/wire-data/wire-data2-symbol.png)

Çevre verileri, ortamınızdaki Operations Manager izlenenler de dahil olmak üzere, Windows bağlı ve Linux bağlantılı bilgisayarlardan Log Analytics aracısıyla toplanan birleştirilmiş ağ ve performans verileri. Verilerin bağıntısını sağlamanıza yardımcı olmak için ağ verileri diğer günlük verilerinizle birleştirilir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Log Analytics aracısına ek olarak, kablo verileri çözümü BT altyapınızdaki bilgisayarlara yüklediğiniz Microsoft bağımlılık aracılarını kullanır. Bağımlılık Aracıları [OSI modelinde](https://en.wikipedia.org/wiki/OSI_model) 2-3 ağ düzeyleri için bilgisayarlarınıza ve bilgisayarlarınızdan gönderilen ağ verilerini, ayrıca kullanılan çeşitli protokollerle bağlantı noktalarını izler. Veriler daha sonra Aracılar kullanılarak Azure Izleyici 'ye gönderilir.  

>[!NOTE]
>Daha önce Hizmet Eşlemesi dağıttıysanız veya Hizmet Eşlemesi veya [VM'ler için Azure izleyici](../../azure-monitor/insights/vminsights-overview.md)düşünüyorsanız, Azure izleyici 'de toplayıp depolarlar ve depolarlar. böylece, Tel veriye karşılaştırılabilir bilgiler sağlayan yeni bir bağlantı ölçüm verileri kümesi vardır.

Azure Izleyici, varsayılan olarak, Windows ve Linux 'ta yerleşik sayaçların yanı sıra, belirtebileceğiniz diğer performans sayaçlarından CPU, bellek, disk ve ağ performansı verileri için verileri günlüğe kaydeder. Her aracı için, alt ağlar ve bilgisayar tarafından kullanılmakta olan uygulama düzeyi protokoller de dahil olmak üzere, ağ ve diğer verileri toplama işlemi gerçek zamanlı olarak yapılır.  Wire Data, alttaki TCP aktarım katmanında değil uygulama düzeyindeki ağ verilerine bakar.  Çözüm, bireysel ACK ve SYNs 'e bakmıyor.  El sıkışma tamamlandığında, canlı bağlantı kabul edilir ve bağlı olarak işaretlenir. Söz konusu bağlantı, her iki taraf da yuvanın açık olduğunu ve verilerin ileri ve geri geçiş yapabildiğini kabul ettiği sürece canlı kalır.  Her iki taraf da bağlantıyı kapattığında, bağlantısı kesik olarak işaretlenir.  Bu nedenle, yalnızca başarıyla tamamlanan paketlerin bant genişliğini sayar; yeniden göndermeleri veya başarısız paketleri raporlamaz.

[Cisco'nun NetFlow protokolüyle](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html) [sFlow](http://www.sflow.org/)'u veya başka bir yazılımı kullandıysanız, sinyal verilerinden gördüğünüz istatistikler ve veriler tanıdık gelecektir.

Yerleşik Günlük arama sorgusu türlerinden bazıları şunlardır:

- Sinyal verileri sağlayan aracılar
- Sinyal verilerini sağlayan aracıların IP adresleri
- IP adresinden giden iletişimler
- Uygulama protokolleri tarafından gönderilen bayt sayısı
- Uygulama hizmeti tarafından gönderilen bayt sayısı
- Farklı protokoller tarafından alınan bayt sayısı
- IP sürümü tarafından gönderilen ve alınan toplam bayt sayısı
- Güvenilir şekilde ölçülmüş bağlantılarda ortalama gecikme süresi
- Ağ trafiği başlatan veya alan bilgisayar işlemleri
- İşlem için ağ trafiği miktarı

Sinyal verilerini kullanarak arama yaptığınızda, en önemli aracılar ve en önemli protokoller hakkındaki bilgileri görüntülemek için verileri filtreleyebilir ve gruplandırabilirsiniz. Öte yandan bazı bilgisayarların (IP adresleri/MAC adresleri) birbiriyle ne zaman, ne kadar süreyle iletişim kurduğunu ve ne kadar veri gönderildiği de görüntüleyebilirsiniz. Temelde, ağ trafiği hakkında arama tabanlı olan meta verileri görüntülersiniz.

Bununla birlikte, meta verileri görüntülediğiniz için bunların kapsamlı bir sorun giderme işleminde kullanışlı olması şart değildir. Azure Izleyici 'deki tel veri, ağ verilerinin tam bir yakalaması değildir.  Derin paket düzeyi sorun giderme işlemlerine yönelik değildir. Diğer koleksiyon yöntemleriyle karşılaştırıldığında, aracıyı kullanmanın avantajı, gereçlerini yüklemenize, ağ anahtarlarınızı yeniden yapılandırmanıza veya karmaşık konfigürasyonlar gerçekleştirmenize gerek kalmaz. Sinyal verileri yalnızca aracı tabanlıdır; bilgisayara aracıyı yüklersiniz ve o da kendi ağ trafiğini izler. Bir diğer avantajı da, kullanıcının yapı katmanına sahip olmadığı bulut sağlayıcılarında ya da barındıran hizmet sağlayıcısında veya Microsoft Azure'da çalıştırılan iş yüklerini izlemek istediğinizde ortaya çıkar.

## <a name="connected-sources"></a>Bağlı kaynaklar

Wire Data verilerini Microsoft Bağımlılık Aracısı'ndan alır. Dependency Agent, Azure Izleyici bağlantıları için Log Analytics aracısına bağlıdır. Bu, bir sunucuda Log Analytics aracısının yüklü olması ve bağımlılık aracısıyla yapılandırılması gerektiği anlamına gelir. Aşağıdaki tabloda Wire Data çözümü tarafından desteklenen bağlı kaynaklar açıklanır:

| **Bağlı kaynak** | **Destekleniyor** | **Açıklama** |
| --- | --- | --- |
| Windows aracıları | Yes | Wire Data, Windows aracı bilgisayarlarından gelen verileri analiz eder ve toplar. <br><br> Windows Agents, [Windows için Log Analytics aracısına](../platform/agent-windows.md)ek olarak Microsoft bağımlılık Aracısı 'nı gerektirir. İşletim sistemi sürümlerinin tam listesi için bkz. [Desteklenen işletim sistemleri](vminsights-enable-overview.md#supported-operating-systems). |
| Linux aracıları | Yes | Wire Data, Linux aracı bilgisayarlarından gelen verileri analiz eder ve toplar.<br><br> Linux [için Log Analytics aracısına](../learn/quick-collect-linux-computer.md)ek olarak, Linux aracıları Microsoft bağımlılık Aracısı gerektirir. İşletim sistemi sürümlerinin tam listesi için bkz. [Desteklenen işletim sistemleri](vminsights-enable-overview.md#supported-operating-systems). |
| System Center Operations Manager yönetim grubu | Yes | Wire Data, bağlantılı bir [System Center Operations Manager yönetim grubunda](../platform/om-agents.md) Windows ve Linux aracılarından gelen verileri analiz eder ve toplar. <br><br> System Center Operations Manager Aracısı bilgisayarından Azure Izleyici 'ye doğrudan bağlantı gereklidir. |
| Azure depolama hesabı | Hayır | Wire Data verileri aracı bilgisayarlardan topladığından, Azure Depolama'dan toplayacağı veri yoktur. |

Windows 'da Microsoft Monitoring Agent (MMA), veri toplamak ve göndermek için hem System Center Operations Manager hem de Azure Izleyici tarafından kullanılır. Bağlama bağlı olarak, aracıya System Center Operations Manager Aracı, Log Analytics Aracısı, MMA veya doğrudan Aracı denir. System Center Operations Manager ve Azure Izleyici, MMA 'nın biraz farklı sürümlerini sağlar. Bu sürümler her rapor System Center Operations Manager, Azure Izleyici 'ye veya her ikisine de bağlanabilir.

Linux 'ta, Linux için Log Analytics Aracısı verileri toplayıp Azure Izleyici 'ye gönderir. Azure Izleyici 'ye doğrudan bağlı aracılarla veya System Center Operations Manager yönetim grupları aracılığıyla Azure Izleyici 'ye bağlanan sunucularda, Tel verileri kullanabilirsiniz.

Bağımlılık Aracısı herhangi bir veri iletmez ve güvenlik duvarları veya bağlantı noktalarında değişiklik gerektirmez. Bu veriler, her zaman doğrudan veya Log Analytics ağ geçidi aracılığıyla Log Analytics Aracısı tarafından Azure Izleyici 'ye iletilir.

![aracı diyagramı](./media/wire-data/agents.png)

Azure Izleyici 'ye bağlı bir yönetim grubu System Center Operations Manager bir Kullanıcı kullanıyorsanız:

- System Center Operations Manager aracılarınız Azure Izleyici 'ye bağlanmak üzere internet 'e erişebil, ek yapılandırma gerekmez.
- System Center Operations Manager aracılarınız Internet üzerinden Azure Izleyici 'ye erişegerekmiyorsa, Log Analytics ağ geçidini System Center Operations Manager ile çalışacak şekilde yapılandırmanız gerekir.

Windows veya Linux bilgisayarlarınız hizmete doğrudan bağlanamıyorsa, Log Analytics aracısını Log Analytics ağ geçidini kullanarak Azure Izleyici 'ye bağlanacak şekilde yapılandırmanız gerekir. Log Analytics ağ geçidini [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=52666)' nden indirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

- [İçgörü ve Analiz](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) çözüm teklifi gereklidir.
- Wire Data çözümünü önceki sürümünü kullanıyorsanız, önce o sürümü kaldırmalısınız. Ancak özgün Wire Data çözümüyle yakalanmış olan tüm veriler Wire Data 2.0'da ve günlük aramasında yine kullanılabilir.
- Bağımlılık aracısını yüklemek veya kaldırmak için yönetici ayrıcalıkları gerekir.
- Bağımlılık aracısının 64 bitlik bir işletim sistemine sahip bir bilgisayara yüklenmesi gerekir.

### <a name="operating-systems"></a>İşletim sistemleri

Aşağıdaki bölümlerde, bağımlılık Aracısı için desteklenen işletim sistemleri listelenmektedir. Wire Data hiçbir işletim sisteminin 32 bit mimarilerini desteklemez.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows masaüstü

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Desteklenen Linux işletim sistemleri
Aşağıdaki bölümlerde, Linux üzerinde bağımlılık Aracısı için desteklenen işletim sistemleri listelenmektedir.  

- Yalnızca varsayılan ve SMP Linux çekirdek sürümleri desteklenir.
- PAE ve Xen gibi standart dışı çekirdek sürümleri hiçbir Linux dağıtımında desteklenmez. Örneğin, "2.6.16.21-0.8-Xen" yayın dizesiyle bir sistem desteklenmez.
- Standart çekirdeklerin yeniden derlemeleri de dahil olmak üzere özel çekirdekler desteklenmez.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| İşletim sistemi sürümü | Çekirdek sürümü |
|:--|:--|
| 7,4 | 3.10.0-693 |
| 7,5 | 3.10.0-862 |
| 7,6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| İşletim sistemi sürümü | Çekirdek sürümü |
|:--|:--|
| 6,9 | 2.6.32-696 |
| 6,10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| İşletim sistemi sürümü | Çekirdek sürümü |
|:--|:--|
| 6,9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6,10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| İşletim sistemi sürümü | Çekirdek sürümü |
|:--|:--|
| Ubuntu 18.04 | Çekirdek 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | Çekirdek 4,15. * |
| 16,04 | 4,4. \*<br>4,8. \*<br>4,10. \*<br>4,11. \*<br>4,13. \* |
| 14,04 | 3,13.\*<br>4,4. \* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| İşletim sistemi sürümü | Çekirdek sürümü
|:--|:--|
| 11 SP4 | 3,0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| İşletim sistemi sürümü | Çekirdek sürümü
|:--|:--|
| 12 SP2 | 4,4. * |
| 12 SP3 | 4,4. * |

### <a name="dependency-agent-downloads"></a>Bağımlılık Aracısı İndirmeleri

| Dosya | İşletim Sistemi | Sürüm | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Yapılandırma

Çalışma alanlarınızda Wire Data çözümünü yapılandırmak için aşağıdaki adımları uygulayın.

1. Etkinlik Günlüğü Analizi çözümünü [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) 'nden veya [Çözüm Galerisi izleme çözümlerini ekleme](../../azure-monitor/insights/solutions.md)bölümünde açıklanan işlemi kullanarak etkinleştirin.
2. Veri almak istediğiniz her bilgisayara bağımlılık aracısını yükler. Bağımlılık Aracısı anında komşular için bağlantıları izleyebilir, bu nedenle her bilgisayarda bir aracıya ihtiyacınız olmayabilir.

> [!NOTE]
> Wire Data çözümünün önceki sürümünü yeni çalışma alanlarına ekleyemezsiniz. Özgün Wire Data çözümünü daha önce etkinleştirdiyseniz, bunu kullanmaya devam edebilirsiniz. Öte yandan, Wire Data 2.0'ı kullanmak için önce özgün sürümü kaldırmanız gerekir.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Windows 'a bağımlılık Aracısı 'nı yükler

Aracıyı yüklemek veya kaldırmak için yönetici ayrıcalıkları gereklidir.

Bağımlılık Aracısı, InstallDependencyAgent-Windows. exe ile Windows çalıştıran bilgisayarlara yüklenir. Bu yürütülebilir dosyayı hiçbir seçenek olmadan çalıştırırsanız, etkileşimli yükleme yapmak için izleyebileceğiniz bir sihirbaz başlatır.

Windows çalıştıran her bilgisayara bağımlılık Aracısı 'nı yüklemek için aşağıdaki adımları kullanın:

1. [Ortamınızda barındırılan Windows bilgisayarlarından veri toplama](../../azure-monitor/platform/agent-windows.md)bölümündeki adımları izleyerek Log Analytics aracısını yüklersiniz.
2. Önceki bölümdeki bağlantıyı kullanarak Windows bağımlılık Aracısı 'nı indirin ve ardından aşağıdaki komutu kullanarak çalıştırın: `InstallDependencyAgent-Windows.exe`
3. Sihirbazı izleyerek aracıyı yükleyin.
4. Bağımlılık Aracısı başlatılamazsa, ayrıntılı hata bilgileri için günlüklere bakın. Windows aracıları için günlük dizini: %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Windows komut satırı

Komut satırından yüklemek için aşağıdaki tabloda yer alan seçenekleri kullanın. Yükleme bayraklarının listesini görmek için yükleyiciyi çalıştırırken aşağıda gösterildiği gibi /? bayrağını kullanın.

InstallDependencyAgent-Windows.exe /?

| **Bayrak** | **Açıklama** |
| --- | --- |
| <code>/?</code> | Komut satırı seçeneklerinin listesini alır. |
| <code>/S</code> | Kullanıcıdan bilgi istenmeden sessiz yükleme gerçekleştirir. |

Windows bağımlılık aracısının dosyaları varsayılan olarak C:\Program Files\Microsoft Dependency Agent 'a yerleştirilir.

### <a name="install-the-dependency-agent-on-linux"></a>Linux 'a bağımlılık Aracısı 'nı yükler

Aracıyı yüklemek veya yapılandırmak için kök erişimi gerekir.

Bağımlılık Aracısı, kendi kendine ayıklanan ikiliye sahip bir kabuk betiği olan InstallDependencyAgent-Linux64. bin aracılığıyla Linux bilgisayarlara yüklenir. Dosyayı çalıştırmak için _sh_ kullanın veya dosyanın kendisine yürütme izinleri ekleyin.

Her bir Linux bilgisayara bağımlılık Aracısı 'nı yüklemek için aşağıdaki adımları kullanın:

1. [Ortamınızda barındırılan Linux bilgisayarlardan veri toplama](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)bölümündeki adımları izleyerek Log Analytics aracısını yüklersiniz.
2. Önceki bölümde bulunan bağlantıyı kullanarak Linux bağımlılık aracısını indirin ve ardından aşağıdaki komutu kullanarak onu kök olarak yükleyin: SH InstallDependencyAgent-Linux64. bin
3. Bağımlılık Aracısı başlatılamazsa, ayrıntılı hata bilgileri için günlüklere bakın. Linux aracıları için günlük dizini: /var/opt/microsoft/dependency-agent/log.

Yükleme bayraklarının listesini görmek için, yükleme programını aşağıda gösterildiği gibi `-help` bayrağıyla çalıştırın.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Bayrak** | **Açıklama** |
| --- | --- |
| <code>-help</code> | Komut satırı seçeneklerinin listesini alır. |
| <code>-s</code> | Kullanıcıdan bilgi istenmeden sessiz yükleme gerçekleştirir. |
| <code>--check</code> | İzinleri ve işletim sistemini denetleyin ama aracıyı yüklemeyin. |

Bağımlılık aracısına ait dosyalar aşağıdaki dizinlere yerleştirilir:

| **Dosyalar** | **Konum** |
| --- | --- |
| Çekirdek dosyaları | /opt/microsoft/dependency-agent |
| Günlük dosyaları | /var/opt/microsoft/dependency-agent/log |
| Yapılandırma dosyaları | /etc/opt/microsoft/dependency-agent/config |
| Hizmet yürütülebilir dosyaları | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| İkili depolama dosyaları | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Yükleme betiği örnekleri

Bağımlılık aracısını aynı anda birçok sunucuya kolayca dağıtmak için bir betik kullanılmasına yardımcı olur. Windows veya Linux 'ta bağımlılık aracısını indirmek ve yüklemek için aşağıdaki betik örneklerini kullanabilirsiniz.

#### <a name="powershell-script-for-windows"></a>Windows için PowerShell betiği

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Linux için kabuk betiği

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>İstenen Durum Yapılandırması

Bağımlılık aracısını Istenen durum yapılandırması aracılığıyla dağıtmak için xPSDesiredStateConfiguration modülünü ve aşağıdakiler gibi bir kod bitini kullanabilirsiniz:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Bağımlılık aracısını kaldırma

Bağımlılık aracısını kaldırmanıza yardımcı olması için aşağıdaki bölümleri kullanın.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Windows 'da bağımlılık aracısını kaldırma

Yönetici, Windows için bağımlılık aracısını Denetim Masası üzerinden kaldırabilir.

Yönetici ayrıca, bağımlılık aracısını kaldırmak için%Programfiles%\Microsoft Dependency Fıles\uninstall.exe komutunu da çalıştırabilir.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Linux 'ta bağımlılık aracısını kaldırma

Bağımlılık aracısını Linux 'tan tamamen kaldırmak için aracının kendisini ve aracı ile otomatik olarak yüklenen bağlayıcıyı kaldırmanız gerekir. Aşağıdaki tek komutu kullanarak ikisini de kaldırabilirsiniz:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Yönetim paketleri

Log Analytics çalışma alanında Wire Data etkinleştirildiğinde, söz konusu çalışma alanındaki tüm Windows sunucularına 300 KB'lık bir yönetim paketi gönderilir. System Center Operations Manager aracılarını bir [bağlı yönetim grubunda](../platform/om-agents.md) kullanıyorsanız, System Center Operations Manager'dan Bağımlılık İzleyicisi yönetim paketi dağıtılır. Aracılar doğrudan bağlandıysa, Azure Izleyici yönetim paketini sunar.

Yönetim paketinin adı Microsoft.IntelligencePacks.ApplicationDependencyMonitor'dır. Şu konuma yazılır: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Yönetim paketi şu veri kaynağını kullanır: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Çözümü kullanma

Çözümü yüklemek ve yapılandırmak için aşağıdaki bilgileri kullanın.

- Wire Data çözümü Windows Server 2012 R2, Windows 8.1 ve daha sonraki işletim sistemlerini çalıştıran bilgisayarlardan veri alır.
- Sinyal verilerini almak istediğiniz bilgisayarlarda Microsoft .NET Framework 4.0 veya üstü bulunmalıdır.
- [Çözüm Galerisi izleme çözümleri ekleme](solutions.md)bölümünde açıklanan işlemi kullanarak, Log Analytics çalışma alanınıza tel veri çözümünü ekleyin. Başka bir yapılandırma işlemi gerekmez.
- Belirli bir çözümle ilişkili sinyal verilerini görüntülemek istiyorsanız, çözümün çalışma alanınıza önceden eklenmiş olması gerekir.

Aracılarınız yüklendikten ve siz çözümü yükledikten sonra, çalışma alanınızda Wire Data 2.0 kutucuğu gösterilir.

![Wire Data kutucuğu](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Wire Data 2.0 çözümünü kullanma

Azure portalında Log Analytics çalışma alanınızın **Genel bakış** sayfasında **Wire Data 2.0** kutucuğuna tıklayarak Wire Data panosunu açın. Pano aşağıdaki tabloda gösterilen dikey pencereleri içerir. Her dikey pencerede, dikey pencerenin belirtilen kapsam ve zaman aralığına yönelik ölçütleriyle eşleşen en fazla 10 öğe listelenir. Dikey pencerenin altındaki **Tümünü göster**’e tıklayarak veya dikey pencere başlığına tıklayarak tüm kayıtları döndüren bir günlük araması yapabilirsiniz.

| **Dikey pencere** | **Açıklama** |
| --- | --- |
| Ağ trafiğini yakalayan aracılar | Ağ trafiğini yakalayan aracıların sayısını gösterir ve trafiği yakalayan ilk 10 bilgisayarı listeler. <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code> günlük araması çalıştırmak için sayıya tıklayın. Yakalanan toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir bilgisayara tıklayın. |
| Yerel Alt Ağlar | Aracıların keşfettiği yerel alt ağların sayısını gösterir.  Tüm alt ağları ve her birinden gönderilen bayt sayısını listeleyen bir <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> günlük araması çalıştırmak için sayıya tıklayın. Alt ağ üzerinden gönderilen toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir alt ağa tıklayın. |
| Uygulama Düzeyi Protokolleri | Aracılar tarafından keşfedilen, kullanımdaki uygulama düzeyi protokollerinin sayısını gösterir. <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code> günlük araması çalıştırmak için sayıya tıklayın. Protokol kullanılarak gönderilen toplam bayt sayısını döndüren bir günlük araması çalıştırmak için listedeki bir protokole tıklayın. |

![Wire Data panosu](./media/wire-data/wire-data-dash.png)

**Ağ trafiğini yakalayan aracılar** dikey penceresini kullanarak bilgisayarlar tarafından kullanılmakta olan ağ genişliğini miktarını belirleyebilirsiniz. Bu dikey pencere ortamınızdaki en _geveze_ bilgisayarı kolayca bulmanıza yardımcı olabilir. Bu tür bilgisayarlar aşırı yüklenmiş, anormal çalışıyor veya normalin üzerinde ağ kaynağı kullanıyor olabilir.

![günlük araması örneği](./media/wire-data/log-search-example01.png)

Benzer biçimde, **Yerel Alt Ağlar** dikey penceresini kullanarak alt ağlarınız üzerinden ne kadar ağ trafiği taşındığını belirleyebilirsiniz. Kullanıcılar alt ağları çoğunlukla uygulamalarının kritik alanları çevresinde tanımlar. Bu dikey pencere söz konusu alanların görülmesini sağlar.

![günlük araması örneği](./media/wire-data/log-search-example02.png)

**Uygulama Düzeyi Protokolleri** hangi protokollerin kullanımda olduğunu öğrenmenize yardımcı olduğundan, yararlı bir dikey penceredir. Örneğin, ağ ortamınızda SSH'nin kullanımda olmamasını bekliyor olabilirsiniz. Dikey pencerede sağlanan bilgileri görüntüleyerek bu beklentinizin doğru olup olmadığını hızla anlayabilirsiniz.

![günlük araması örneği](./media/wire-data/log-search-example03.png)

Ayrıca protokol trafiğinin zaman içinde arttığını mı yoksa azaldığını mı bilmek de yararlı olur. Örneğin, bir uygulama tarafından iletilen verilerin miktarı artıyorsa, bu farkında olmanız gereken bir durum veya dikkate değer bulduğunuz bir bilgi olabilir.

## <a name="input-data"></a>Giriş verileri

Sinyal verileri, etkinleştirilmiş olan aracıları kullanarak ağ trafiği hakkındaki meta verileri toplar. Her aracı yaklaşık her 15 saniyede bir veri gönderir.

## <a name="output-data"></a>Çıktı verileri

Her giriş verileri türü için _WireData_ türünde bir kayıt oluşturulur. Aşağıdaki tabloda WireData kayıtlarının özellikleri gösterilmiştir:

| Özellik | Açıklama |
|---|---|
| Bilgisayar | Verilerin toplandığı bilgisayarın adı |
| TimeGenerated | Kaydın zamanı |
| LocalIP | Yerel bilgisayarın IP adresi |
| SessionState | Bağlantılı veya bağlantısı kesilmiş |
| ReceivedBytes | Alınan bayt miktarı |
| ProtocolName | Kullanılan ağ protokolünün adı |
| IPVersion | IP sürümü |
| Yön | Gelen veya giden |
| MaliciousIP | Bilinen kötü amaçlı kaynağın IP adresi |
| Önem Derecesi | Kötü amaçlı olduğundan şüphe edilen yazılımın önem derecesi |
| RemoteIPCountry | Uzak IP adresinin ülkesi/bölgesi |
| ManagementGroupName | Operations Manager yönetim grubunun adı |
| SourceSystem | Verilerin toplandığı kaynak |
| SessionStartTime | Oturumun başlangıç saati |
| SessionEndTime | Oturumun bitiş saati |
| LocalSubnet | Verilerin toplandığı alt ağ |
| LocalPortNumber | Yerel bağlantı noktası numarası |
| RemoteIP | Uzak bilgisayar tarafından kullanılan uzak IP adresi |
| RemotePortNumber | Uzak IP adresi tarafından kullanılan bağlantı noktası numarası |
| SessionID | İki IP adresi arasındaki iletişim oturumunu tanımlayan benzersiz bir değer |
| SentBytes | Gönderilen bayt sayısı |
| TotalBytes | Oturum sırasında gönderilen toplam bayt sayısı |
| ApplicationProtocol | Kullanılan ağ protokolünün türü   |
| ProcessID | Windows işlem kimliği |
| ProcessName | İşlemin yıl ve dosya adı |
| RemoteIPLongitude | IP boylam değeri |
| RemoteIPLatitude | IP enlem değeri |

## <a name="next-steps"></a>Sonraki adımlar

- Ayrıntılı sinyal verileri arama kayıtlarını görüntülemek için [günlüklerde arama yapın](../../azure-monitor/log-query/log-query-overview.md).