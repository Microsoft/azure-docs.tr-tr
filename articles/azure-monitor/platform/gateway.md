---
title: Log Analytics ağ geçidini kullanarak bilgisayarları bağlama | Microsoft Docs
description: İnternet erişimine sahip olmadıkları zaman Azure Otomasyonu ve Log Analytics hizmetine veri göndermek için Log Analytics ağ geçidini kullanarak cihazlarınızı ve Operations Manager izlenen bilgisayarlarınızı bağlayın.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/30/2019
ms.openlocfilehash: 7574f5c17c1b4598336b8db3108946164dc203f2
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847279"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Azure Izleyici 'de Log Analytics ağ geçidini kullanarak internet erişimi olmadan bilgisayarları bağlama

>[!NOTE]
>Microsoft Azure Izleyicisine Microsoft Operations Management Suite (OMS) geçişleri, terminoloji değişiyor. Bu makale, Azure Log Analytics ağ geçidi olarak OMS ağ geçidine başvurur. 
>

Bu makalede, doğrudan bağlı olan veya Operations Manager tarafından izlenen bilgisayarların internet erişimi olmadığında Log Analytics ağ geçidini kullanarak Azure Otomasyonu ve Azure Izleyici ile iletişimin nasıl yapılandırılacağı açıklanır. 

Log Analytics ağ geçidi, HTTP CONNECT komutunu kullanarak HTTP tünelini destekleyen bir HTTP iletme ara sunucusu olur. Bu ağ geçidi, verileri Azure Otomasyonu 'na ve Azure Izleyici 'de internet 'e doğrudan bağlanamayabilecekleri bilgisayarlar adına bir Log Analytics çalışma alanına gönderir. 

Log Analytics ağ geçidi şunları destekler:

* Onun arkasındaki ve Azure Otomasyonu karma runbook çalışanları ile yapılandırılmış her aracıda yapılandırılan Log Analytics çalışma alanlarına kadar raporlama yapın.  
* Microsoft Monitoring Agent Azure Izleyici 'de bir Log Analytics çalışma alanına doğrudan bağlı olduğu Windows bilgisayarları.
* Linux için Log Analytics aracısının, Azure Izleyici 'deki bir Log Analytics çalışma alanına doğrudan bağlandığı Linux bilgisayarları.  
* UR7 SÜRÜMLERIYLE ile 2012 SP1, UR3 ile Operations Manager 2012 R2 veya Log Analytics ile tümleştirilmiş Operations Manager 2016 veya sonraki bir yönetim grubu System Center Operations Manager.  

Bazı BT güvenlik ilkeleri, ağ bilgisayarları için internet bağlantısına izin vermez. Bu bağlanmayan bilgisayarlar, örneğin, satış noktası (POS) cihazları veya BT hizmetlerini destekleyen sunucular olabilir. Bu cihazları Azure Otomasyonu 'na veya bir Log Analytics çalışma alanına bağlamak için bunları yönetebilir ve izleyebilirsiniz, bunları doğrudan Log Analytics ağ geçidiyle iletişim kuracak şekilde yapılandırın. Log Analytics ağ geçidi, yapılandırma bilgilerini alabilir ve verileri adına iletebilir. Bilgisayarlar, bir Log Analytics çalışma alanına doğrudan bağlanmak için Log Analytics aracısıyla yapılandırılmışsa, bu bilgisayarlar Log Analytics ağ geçidiyle iletişim kurar.  

Log Analytics ağ geçidi, aracılardan hizmete doğrudan veri aktarır. Bu, aktarımda verilerin hiçbirini çözümlemez ve ağ geçidi, hizmetle bağlantı kesildiğinde verileri önbelleğe almaz. Ağ Geçidi hizmet ile iletişim kuramadığından, aracı çalışmaya devam eder ve toplanan verileri izlenen bilgisayarın diskinde sıralar. Bağlantı geri yüklendiğinde, aracı Azure Izleyici 'ye toplanan önbelleğe alınmış verileri gönderir.

Bir Operations Manager yönetim grubu Log Analytics tümleştirildiğinde, yönetim sunucuları, etkinleştirdiğiniz çözüme bağlı olarak, yapılandırma bilgilerini almak ve toplanan verileri göndermek için Log Analytics ağ geçidine bağlanacak şekilde yapılandırılabilir .  Operations Manager aracılar yönetim sunucusuna bazı veriler gönderir. Örneğin, aracılar Operations Manager uyarılar, yapılandırma değerlendirmesi verileri, örnek alanı verileri ve kapasite verileri gönderebilir. Internet Information Services (IIS) günlükleri, performans verileri ve güvenlik olayları gibi diğer yüksek hacimli veriler doğrudan Log Analytics ağ geçidine gönderilir. 

Bir veya daha fazla Operations Manager ağ geçidi sunucusu, güvenilmeyen sistemleri bir çevre ağında veya yalıtılmış bir ağda izlemek üzere dağıtılırsa, bu sunucular bir Log Analytics ağ geçidiyle iletişim kuramaz.  Operations Manager ağ geçidi sunucuları yalnızca bir yönetim sunucusuna rapor verebilir.  Bir Operations Manager yönetim grubu, Log Analytics ağ geçidiyle iletişim kuracak şekilde yapılandırıldığında, ara sunucu yapılandırma bilgileri, Azure Izleyici için günlük verilerini toplamak üzere yapılandırılan aracıyla yönetilen her bilgisayara otomatik olarak dağıtılır. ayar boş olsa bile.

Ağ Geçidi aracılığıyla bir Log Analytics çalışma alanıyla iletişim kuran doğrudan bağlı veya Operations Management grupları için yüksek kullanılabilirlik sağlamak üzere Ağ Yükü Dengeleme (NLB) kullanarak trafiği birden çok ağ geçidi sunucusu arasında yeniden yönlendirin ve dağıtabilirsiniz. Bu şekilde, bir ağ geçidi sunucusu kapatıyorsa trafik, kullanılabilir başka bir düğüme yönlendirilir.  

Log Analytics ağ geçidini çalıştıran bilgisayarın, ağ geçidinin iletişim kurması gereken hizmet uç noktalarını belirlemek için Log Analytics Windows aracısının olması gerekir. Aracının aynı zamanda ağ geçidini, ağ geçidinin arkasındaki aracıların veya Operations Manager yönetim grubunun ile yapılandırıldığı çalışma alanlarına bildirmek üzere yönlendirilmesi gerekir. Bu yapılandırma, ağ geçidinin ve aracının atanan çalışma alanıyla iletişim kurmasına izin verir.

Ağ Geçidi, en fazla dört çalışma alanına bağlanabilir. Bu, bir Windows aracısının desteklediği çalışma alanlarının toplam sayısıdır.  

Aracıların ağ geçidine otomatik olarak veri aktarabilmesi için her aracının ağ geçidine ağ bağlantısı olması gerekir. Ağ geçidini bir etki alanı denetleyicisine yüklemekten kaçının.

Aşağıdaki diyagramda, ağ geçidi aracılığıyla, Azure Otomasyonu ve Log Analytics doğrudan aracılardan alınan veri akışı gösterilmektedir. Aracı proxy yapılandırması, Log Analytics ağ geçidinin yapılandırıldığı bağlantı noktasıyla aynı olmalıdır.  

![Hizmetlerle doğrudan aracı iletişimi diyagramı](./media/gateway/oms-omsgateway-agentdirectconnect.png)

Aşağıdaki diyagramda Operations Manager yönetim grubundaki veri akışı Log Analytics gösterilmektedir.   

![Log Analytics ile Operations Manager iletişimin diyagramı](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Sisteminizi ayarlama

Log Analytics ağ geçidini çalıştırmak için tasarlanan bilgisayarlar aşağıdaki yapılandırmaya sahip olmalıdır:

* Windows 10, Windows 8.1 veya Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 veya Windows Server 2008
* Microsoft .NET Framework 4,5
* En az 4 çekirdekli işlemci ve 8 GB bellek 
* Windows için, ağ geçidi üzerinden iletişim kuran aracılarla aynı çalışma alanına rapor verecek şekilde yapılandırılmış bir [Log Analytics Aracısı](agent-windows.md)

### <a name="language-availability"></a>Dil kullanılabilirliği

Log Analytics ağ geçidi şu dillerde kullanılabilir:

- Çince (Basitleştirilmiş)
- Çince (Geleneksel)
- Çekçe
- Hollanda dili
- Türkçe
- Fransızca
- Almanca
- Macarca
- İtalyanca
- Japonca
- Korece
- Lehçe
- Portekizce (Brezilya)
- Portekizce (Portekiz)
- Rusça
- İspanyolca (Uluslararası)

### <a name="supported-encryption-protocols"></a>Desteklenen şifreleme protokolleri

Log Analytics ağ geçidi yalnızca Aktarım katmanı güvenliğini destekler (TLS) 1,0, 1,1 ve 1,2.  Güvenli Yuva Katmanı (SSL) desteklemez.  Log Analytics yoldaki verilerin güvenliğini sağlamak için, ağ geçidini en az TLS 1,2 kullanacak şekilde yapılandırın. TLS veya SSL 'nin eski sürümleri savunmasızdır. Bunlar şu anda geriye dönük uyumlulukla izin verse de kullanmaktan kaçının.  

Daha fazla bilgi için [TLS 1,2 kullanarak verileri güvenli bir şekilde göndermeyi](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)inceleyin. 

### <a name="supported-number-of-agent-connections"></a>Desteklenen aracı bağlantısı sayısı

Aşağıdaki tabloda, bir Ağ Geçidi sunucusuyla yaklaşık olarak kaç aracının iletişim kurabildiği gösterilmektedir. Destek, 6 saniyede bir 200 KB veri karşıya yükleyen aracıları temel alır. Sınanan her bir aracı için, veri hacmi günde yaklaşık 2,7 GB olur.

|Ağ geçidi |Desteklenen aracılar (yaklaşık)|  
|--------|----------------------------------|  
|CPU: Intel Xeon Işlemci E5-2660 v3 \@ 2,6 GHz 2 çekirdek<br> Bellek: 4 GB<br> Ağ bant genişliği: 1 Gbps| 600|  
|CPU: Intel Xeon Işlemci E5-2660 v3 \@ 2,6 GHz 4 çekirdek<br> Bellek: 8 GB<br> Ağ bant genişliği: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Log Analytics ağ geçidini indirin

Log Analytics Gateway kurulum dosyasının en son sürümünü [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=54443) 'nden veya Azure Portal alın.

Azure portal Log Analytics ağ geçidini almak için şu adımları izleyin:

1. Hizmetler listesine gidip **Log Analytics**seçin. 
1. Bir çalışma alanı seçin.
1. Çalışma alanı Dikey penceresindeki **genel**altında **hızlı başlangıç**' yi seçin. 
1. **Çalışma alanına bağlanacak bir veri kaynağı seçin**altında **bilgisayarlar**' ı seçin.
1. **Doğrudan aracı** dikey penceresinde **Log Analytics ağ geçidini indir**' i seçin.
 
   ![Log Analytics ağ geçidini indirme adımlarının ekran görüntüsü](./media/gateway/download-gateway.png)

or 

1. Çalışma alanı Dikey penceresinde **Ayarlar**altında **Gelişmiş ayarlar**' ı seçin.
1. **Windows server** > **bağlı kaynaklar** ' a gidin ve **Log Analytics ağ geçidini indir**' i seçin.

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>Kurulum sihirbazını kullanarak Log Analytics ağ geçidini yükleme

Kurulum Sihirbazı 'nı kullanarak bir ağ geçidi yüklemek için aşağıdaki adımları izleyin. 

1. Hedef klasörden, **Log Analytics Gateway. msi**' ye çift tıklayın.
1. **Hoş Geldiniz** sayfasında, **İleri**’yi seçin.

   ![Ağ Geçidi Kurulum Sihirbazı 'nda hoş geldiniz sayfasının ekran görüntüsü](./media/gateway/gateway-wizard01.png)

1. **Lisans Sözleşmesi** sayfasında, Microsoft yazılımı lisans koşulları 'nı kabul etmek Için **Lisans sözleşmesinin koşullarını kabul ediyorum** ' u seçin ve ardından **İleri**' yi seçin.
1. **Bağlantı noktası ve proxy adresi** sayfasında:

   a. Ağ Geçidi için kullanılacak TCP bağlantı noktası numarasını girin. Kurulum, Windows Güvenlik Duvarı 'nda bir gelen kuralı yapılandırmak için bu bağlantı noktası numarasını kullanır.  Varsayılan değer 8080 ' dir.
      Bağlantı noktası numarasının geçerli aralığı 1 ile 65535 arasındadır. Giriş bu aralığa girmezse bir hata iletisi görüntülenir.

   b. Ağ geçidinin yüklü olduğu sunucunun bir proxy üzerinden iletişim kurması gerekiyorsa, ağ geçidinin bağlanması gereken proxy adresini girin. Örneğin, `http://myorgname.corp.contoso.com:80` girin.  Bu alanı boş bırakırsanız, ağ geçidi internet 'e doğrudan bağlanmaya çalışır.  Proxy sunucunuz kimlik doğrulaması gerektiriyorsa, bir Kullanıcı adı ve parola girin.

   c. **İleri**’yi seçin.

   ![Ağ geçidi proxy 'sinin yapılandırmasının ekran görüntüsü](./media/gateway/gateway-wizard02.png)

1. Microsoft Update etkinleştirilmemişse, Microsoft Update sayfası görüntülenir ve etkinleştirmeyi seçebilirsiniz. Bir seçim yapın ve ardından **İleri**' yi seçin. Aksi halde, bir sonraki adımla devam edin.
1. **Hedef klasör** sayfasında, C:\Program Files\oms Gateway varsayılan klasörünü bırakın ya da ağ geçidini yüklemek istediğiniz konumu girin. Sonra **İleri**’yi seçin.
1. **Yüklemeye hazırlanma** sayfasında, **yükler**' i seçin. Kullanıcı hesabı denetimi, yüklemek için izin isterse, **Evet**' i seçin.
1. Kurulum bittikten sonra **son**' u seçin. Hizmetin çalıştığını doğrulamak için, Services. msc ek bileşenini açın ve **OMS ağ geçidinin** hizmet listesinde göründüğünü ve durumunun **çalıştığını**doğrulayın.

   ![OMS ağ geçidinin çalıştığını gösteren yerel hizmetlerin ekran görüntüsü](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>Komut satırını kullanarak Log Analytics ağ geçidini yükler
Ağ Geçidi için indirilen dosya, komut satırından veya diğer otomatikleştirilmiş yöntemden sessiz yüklemeyi destekleyen bir Windows Installer paketidir. Windows Installer için standart komut satırı seçenekleri konusunda bilgi sahibi değilseniz, bkz. [komut satırı seçenekleri](https://docs.microsoft.com/windows/desktop/Msi/command-line-options).
 
Aşağıdaki tabloda, kurulum tarafından desteklenen parametrelerin vurgulanmıştır.

|Parametreler| Notlar|
|----------|------| 
|PORTNUMBER | Dinlenecek ağ geçidi için TCP bağlantı noktası numarası |
|PROXY | Ara sunucunun IP adresi |
|INSTALLDIR | Ağ Geçidi yazılım dosyalarının install dizinini belirtmek için tam yol |
|NITELEN | Proxy sunucusu ile kimlik doğrulaması yapılacak Kullanıcı kimliği |
|PAROLAYı | Proxy ile kimlik doğrulamak için Kullanıcı kimliğinin parolası |
|Licensekabul edildi | Lisans anlaşmasını kabul etmiş olduğunuzu doğrulamak için **1** değerini belirtin |
|HASAUTH | Kullanıcı adı/parola parametreleri belirtildiğinde **1** değerini belirtin |
|HASPROXY | **Proxy** PARAMETRESI için IP adresi belirtirken **1** değerini belirtin |

Ağ geçidini sessizce yüklemek ve belirli bir ara sunucu adresiyle, bağlantı noktası numarasıyla yapılandırmak için, aşağıdakileri yazın:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

/Qn komut satırı seçeneğinin kullanılması kurulumu gizler,/QB, sessiz yükleme sırasında kurulumu gösterir.  

Proxy ile kimlik doğrulaması yapmak için kimlik bilgileri sağlamanız gerekiyorsa, şunu yazın:

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

Yükleme işleminden sonra, aşağıdaki PowerShell cmdlet 'lerini kullanarak ayarların kabul edildiğini (Kullanıcı adı ve parolayı doğrulayın) doğrulayabilirsiniz:

- **Get-OMSGatewayConfig** : ağ geçidinin dinlemesi IÇIN yapılandırıldığı TCP bağlantı noktasını döndürür.
- **Get-OMSGatewayRelayProxy** : ile iletişim kurmak için yapılandırdığınız proxy sunucusunun IP adresini döndürür.

## <a name="configure-network-load-balancing"></a>Ağ Yükü Dengelemeyi Yapılandırma 
Ağ Yükü Dengeleme [(NLB),](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing) [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)veya donanım tabanlı yük dengeleyiciler kullanarak Ağ Yük Dengelemesi 'ni (NLB) kullanarak yüksek kullanılabilirlik için ağ geçidini yapılandırabilirsiniz. Yük dengeleyici, istenen bağlantıları Log Analytics aracılarından veya Operations Manager yönetim sunucularından düğümleri arasında yönlendirerek trafiği yönetir. Bir ağ geçidi sunucusu kapatıyorsa trafik diğer düğümlere yönlendirilir.

### <a name="microsoft-network-load-balancing"></a>Microsoft Ağ Yükü Dengeleme
Windows Server 2016 Ağ Yükü Dengeleme kümesini nasıl tasarlayacağınızı ve dağıtacağınızı öğrenmek için bkz. [Ağ Yükü Dengeleme](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). Aşağıdaki adımlarda, Microsoft Ağ Yükü Dengeleme kümesinin nasıl yapılandırılacağı açıklanır.  

1. Yönetici hesabıyla NLB kümesinin bir üyesi olan Windows Server üzerinde oturum açın.  
2. Ağ Yükü Dengeleme Yöneticisi 'Ni Sunucu Yöneticisi ' de açın, **Araçlar**' a ve ardından **Ağ Yükü Dengeleme Yöneticisi**' ne tıklayın.
3. Yüklü Microsoft Monitoring Agent sahip bir Log Analytics ağ geçidi sunucusunu bağlamak için, kümenin IP adresine sağ tıklayın ve ardından **Konak kümeye ekle**' ye tıklayın. 

    ![Ağ Yükü Dengeleme Yöneticisi – konağı kümeye ekleyin](./media/gateway/nlb02.png)
 
4. Bağlanmak istediğiniz ağ geçidi sunucusunun IP adresini girin. 

    ![Ağ Yükü Dengeleme Yöneticisi – kümeye konak ekle: Bağlan](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
Bir Azure Load Balancer tasarlamayı ve dağıtmayı öğrenmek için bkz. Azure Load Balancer nedir [?](../../load-balancer/load-balancer-overview.md). Temel yük dengeleyiciyi dağıtmak için, **arka uç sunucuları oluşturma**bölümünde özetlenen adımları dışlayarak bu [hızlı](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) başlangıçta özetlenen adımları izleyin.   

> [!NOTE]
> **Temel SKU 'yu**kullanarak Azure Load Balancer yapılandırma, Azure sanal makinelerinin bir kullanılabilirlik kümesine ait olmasını gerektirir. Kullanılabilirlik kümeleri hakkında daha fazla bilgi edinmek için bkz. [Azure 'Da Windows sanal makinelerinin kullanılabilirliğini yönetme](../../virtual-machines/windows/manage-availability.md). Mevcut sanal makineleri bir kullanılabilirlik kümesine eklemek için [Azure Resource Manager VM kullanılabilirlik kümesi ayarla](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)' ya bakın.
> 

Yük dengeleyici oluşturulduktan sonra, trafiği bir veya daha fazla ağ geçidi sunucusuna dağıtan bir arka uç havuzunun oluşturulması gerekir. Hızlı başlangıç makalesi bölümünde açıklanan adımları izleyerek [Yük Dengeleyici için kaynak oluşturma](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer)bölümüne bakın.  

>[!NOTE]
>Sistem durumu araştırmasını yapılandırırken, ağ geçidi sunucusunun TCP bağlantı noktasını kullanacak şekilde yapılandırılmalıdır. Sistem durumu araştırması, ağ geçidi sunucularını, sistem durumu denetimlerine yönelik yanıtlarını temel alarak yük dengeleyici rotasyondan dinamik olarak ekler veya kaldırır. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Log Analytics aracısını ve Operations Manager yönetim grubunu yapılandırma
Bu bölümde, Azure Otomasyonu veya Log Analytics ile iletişim kurmak için Log Analytics ağ geçidiyle doğrudan bağlı Log Analytics aracılarını, bir Operations Manager yönetim grubunu veya Azure Otomasyonu karma runbook çalışanlarını nasıl yapılandıracağınızı öğreneceksiniz.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Tek başına Log Analytics Aracısı yapılandırma
Log Analytics Aracısı yapılandırılırken, proxy sunucu değerini, Log Analytics ağ geçidi sunucusunun IP adresiyle ve bağlantı noktası numarası ile değiştirin. Bir yük dengeleyicinin arkasında birden çok ağ geçidi sunucusu dağıttıysanız, Log Analytics Aracısı proxy yapılandırması, yük dengeleyicinin sanal IP adresidir.  

>[!NOTE]
>Log Analytics doğrudan bağlanan ağ geçidine ve Windows bilgisayarlarına Log Analytics aracısını yüklemek için bkz. [Azure 'Da Windows bilgisayarlarını Log Analytics hizmetine bağlama](agent-windows.md). Linux bilgisayarlarını bağlamak için bkz. [Karma ortamda Linux bilgisayarları için Log Analytics Aracısı yapılandırma](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Aracıyı ağ geçidi sunucusuna yükledikten sonra, ağ geçidi ile iletişim kuran çalışma alanı veya çalışma alanı aracılarıyla raporlamak üzere yapılandırın. Log Analytics Windows Aracısı ağ geçidinde yüklü değilse, 300 olayı OMS ağ geçidi olay günlüğüne yazılır ve bu da aracının yüklenmesi gerektiğini gösterir. Aracı yüklenir, ancak üzerinden iletişim kuran aracılarla aynı çalışma alanına rapor vermek üzere yapılandırılmamışsa, 105 olayı aynı günlüğe yazılır ve bu da ağ geçidinde aracının ortak olan aracılarla aynı çalışma alanına rapor verecek şekilde yapılandırılması gerektiğini gösterir. ağ geçidiyle mmunicate.

Yapılandırmayı tamamladıktan sonra, değişiklikleri uygulamak için OMS Ağ Geçidi hizmetini yeniden başlatın. Aksi takdirde, ağ geçidi Log Analytics ile iletişim kurmaya çalışır olan aracıları reddeder ve OMS ağ geçidi olay günlüğünde olay 105 ' i rapor eder. Bu durum, Ağ Geçidi sunucusundaki aracı yapılandırmasından bir çalışma alanı eklediğinizde veya kaldırdığınızda da olur.   

Otomasyon karma Runbook Worker ile ilgili bilgi için bkz. [karma runbook çalışanı kullanarak veri merkezinizdeki veya buluttaki kaynakları otomatikleştirme](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Tüm aracıların aynı proxy sunucusunu kullandığı Operations Manager yapılandırın

Operations Manager proxy yapılandırması, ayar boş olsa bile Operations Manager rapor veren tüm aracılara otomatik olarak uygulanır.  

Operations Manager desteklemek üzere OMS ağ geçidini kullanmak için şunları yapmanız gerekir:

* OMS ağ geçidi sunucusuna yüklenmiş Microsoft Monitoring Agent (sürüm 8.0.10900.0 veya sonrası), yönetim grubunuzun rapor verecek şekilde yapılandırıldığı aynı Log Analytics çalışma alanlarıyla yapılandırılmış.
* Internet bağlantısı. Alternatif olarak, OMS ağ geçidi, internet 'e bağlı bir ara sunucuya bağlanmalıdır.

> [!NOTE]
> Ağ Geçidi için bir değer belirtmezseniz, boş değerler tüm aracılara gönderilir.
>

Operations Manager yönetim grubunuz ilk kez bir Log Analytics çalışma alanına kaydolurken, Işletim konsolundaki yönetim grubu için proxy yapılandırmasını belirtme seçeneğini görmezsiniz. Bu seçenek yalnızca yönetim grubu hizmete kaydedilmişse kullanılabilir.  

Tümleştirmeyi yapılandırmak için, Işletim konsolunu çalıştırdığınız sistemde ve yönetim grubundaki tüm yönetim sunucularında Netsh kullanarak sistem proxy yapılandırmasını güncelleştirin. Şu adımları uygulayın:

1. Yükseltilmiş bir komut istemi açın:

   a. **Başlat** ' ı seçin ve **cmd**girin.  

   b. **Komut istemi** ' ne sağ tıklayın ve **yönetici olarak çalıştır**' ı seçin.  

1. Aşağıdaki komutu kullanın:

   `netsh winhttp set proxy <proxy>:<port>`

Log Analytics ile tümleştirmeyi tamamladıktan sonra, `netsh winhttp reset proxy`çalıştırarak değişikliği kaldırın. Ardından, Işletim konsolunda, Log Analytics ağ geçidi sunucusunu belirtmek için **proxy sunucusunu yapılandır** seçeneğini kullanın. 

1. Operations Manager konsolunda, **Operations Management Suite**altında **bağlantı**' yı seçin ve ardından **proxy sunucusunu yapılandır**' ı seçin.

   ![Operations Manager ekran görüntüsü, proxy sunucusunu yapılandırma seçimini gösterir](./media/gateway/scom01.png)

1. **Operations Management Suite 'e erişmek için bir proxy sunucusu kullan** ' ı seçin ve sonra Log Analytics ağ GEÇIDI sunucusunun IP adresini veya yük DENGELEYICININ sanal IP adresini girin. `http://`önekiyle başlamak için dikkatli olun.

   ![Proxy sunucu adresini gösteren Operations Manager ekran görüntüsü](./media/gateway/scom02.png)

1. **Son**’u seçin. Operations Manager yönetim grubunuz artık ağ geçidi sunucusu üzerinden Log Analytics hizmetine iletişim kuracak şekilde yapılandırılmıştır.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Belirli aracıların bir ara sunucu kullanacağı Operations Manager yapılandırın

Büyük veya karmaşık ortamlar için, yalnızca belirli sunucuların (veya grupların) Log Analytics ağ geçidi sunucusunu kullanmasını isteyebilirsiniz.  Bu sunucular için Operations Manager aracısını doğrudan güncelleştiremezsiniz çünkü bu değerin, yönetim grubu için genel değer ile üzerine yazılır.  Bunun yerine, bu değerleri göndermek için kullanılan kuralı geçersiz kılın.  

> [!NOTE] 
> Ortamınızda birden çok Log Analytics ağ geçidi sunucusuna izin vermek istiyorsanız bu yapılandırma tekniğini kullanın.  Örneğin, belirli Log Analytics Ağ Geçidi sunucularının bölgesel olarak belirtilmesini zorunlu kılabilirsiniz.
>

Belirli sunucuları veya grupları Log Analytics ağ geçidi sunucusunu kullanacak şekilde yapılandırmak için: 

1. Operations Manager konsolunu açın ve **yazma** çalışma alanını seçin.  
1. Yazma çalışma alanında **kurallar**' ı seçin. 
1. Operations Manager araç çubuğunda **kapsam** düğmesini seçin. Bu düğme kullanılamıyorsa, **izleme** bölmesinde bir klasör değil bir nesne seçtiğinizden emin olun. **Kapsam yönetim paketi nesneleri** iletişim kutusu, yaygın olarak hedeflenen sınıfların, grupların veya nesnelerin listesini görüntüler. 
1. **Ara** alanına **sistem sağlığı hizmeti** girin ve listeden seçin. **Tamam**’ı seçin.  
1. **Danışman proxy ayar kuralını**arayın. 
1. Operations Manager araç çubuğunda **geçersiz kılmalar** ' ı seçin ve ardından **sınıfın belirli bir nesnesi Için rule\override** ' ın üzerine gelin: sistem sağlığı hizmeti ve listeden bir nesne seçin.  Veya bu geçersiz kılmayı uygulamak istediğiniz sunucuların sistem sağlığı hizmeti nesnesini içeren özel bir grup oluşturun. Ardından, geçersiz kılmayı özel grubunuza uygulayın.
1. **Geçersiz kılma özellikleri** iletişim kutusunda, **WebProxyAddress** parametresinin yanındaki **geçersiz kılma** sütununa bir onay işareti ekleyin.  **Geçersiz kılma değeri** alanına Log Analytics ağ geçidi sunucusunun URL 'sini girin. `http://`önekiyle başlamak için dikkatli olun.  

    >[!NOTE]
    > Kuralı etkinleştirmeniz gerekmez. Microsoft System Center Advisor Izleme sunucusu grubunu hedefleyen Microsoft System Center Advisor güvenli başvuru geçersiz kılma yönetim paketinde zaten bir geçersiz kılma ile otomatik olarak yönetiliyor.
    > 

1. **Hedef yönetim paketini seçin** listesinden bir yönetim paketi seçin veya **Yeni**' yi seçerek yeni bir korumasız yönetim paketi oluşturun. 
1. İşiniz bittiğinde **Tamam**'a tıklayın. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Otomasyon karma runbook çalışanları için yapılandırma

Ortamınızda karma runbook çalışanları Otomasyonu varsa, çalışanları destekleyecek şekilde ağ geçidini yapılandırmak için aşağıdaki adımları izleyin.

Her bir bölgenin URL 'sini bulmak için Otomasyon belgelerinin [ağınızı yapılandırma](../../automation/automation-hybrid-runbook-worker.md#network-planning) bölümüne bakın.

Bilgisayarınız karma runbook çalışanı olarak otomatik olarak kaydedilmişse, örneğin Güncelleştirme Yönetimi çözümü bir veya daha fazla VM için etkinleştirilmişse, aşağıdaki adımları izleyin:

1. Iş çalışma zamanı veri hizmeti URL 'Lerini Log Analytics ağ geçidinde Izin verilen ana bilgisayar listesine ekleyin. Örneğin, `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Aşağıdaki PowerShell cmdlet 'ini kullanarak Log Analytics Ağ Geçidi hizmetini yeniden başlatın: `Restart-Service OMSGatewayService`

Bilgisayarınız, karma Runbook Worker kayıt cmdlet 'i kullanılarak Azure Otomasyonu 'na katılırsa, şu adımları izleyin:

1. Aracı hizmeti kayıt URL 'sini Log Analytics ağ geçidinde Izin verilen ana bilgisayar listesine ekleyin. Örneğin, `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Iş çalışma zamanı veri hizmeti URL 'Lerini Log Analytics ağ geçidinde Izin verilen ana bilgisayar listesine ekleyin. Örneğin, `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Log Analytics Ağ Geçidi hizmetini yeniden başlatın.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Yararlı PowerShell cmdlet 'leri

Cmdlet 'leri kullanarak Log Analytics ağ geçidinin yapılandırma ayarlarını güncelleştirme görevlerini gerçekleştirebilirsiniz. Cmdlet 'lerini kullanmadan önce şunları yaptığınızdan emin olun:

1. Log Analytics ağ geçidini (Microsoft Windows Installer) yükler.
1. Bir PowerShell konsol penceresi açın.
1. Şu komutu yazarak modülü içeri aktarın: `Import-Module OMSGateway`
1. Önceki adımda bir hata oluşmamışsa modül başarıyla içeri aktarıldı ve cmdlet 'ler kullanılabilir. `Get-Module OMSGateway` girin
1. Değişiklik yapmak için cmdlet 'lerini kullandıktan sonra OMS Ağ Geçidi hizmetini yeniden başlatın.

3\. adımdaki bir hata modülün içeri aktarılmadığı anlamına gelir. PowerShell modülü bulamadığında hata ortaya çıkabilir. Modülü OMS ağ geçidi yükleme yolunda bulabilirsiniz: *C:\Program FILES\MICROSOFT OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parametreler** | **Açıklama** | **Örnek** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Anahtar |Hizmetin yapılandırmasını alır |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Anahtar (gerekli) <br> Değer |Hizmetin yapılandırmasını değiştirir |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Geçiş (yukarı akış) proxy adresini alır |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Adres<br> Kullanıcı adı<br> Parola |Geçiş (yukarı akış) proxy 'sinin adresini (ve kimlik bilgilerini) ayarlar |1. bir geçiş proxy 'si ve kimlik bilgisi ayarlayın:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. kimlik doğrulaması gerektirmeyen bir geçiş proxy 'si ayarlayın: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. geçiş proxy ayarını temizleyin:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |İzin verilen ana bilgisayarı alır (izin verilen Konakları otomatik olarak indirilmez, yalnızca yerel olarak yapılandırılmış izin verilen ana bilgisayar) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Ana bilgisayar (gerekli) |Konağı izin verilenler listesine ekler |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Ana bilgisayar (gerekli) |Konağı izin verilenler listesinden kaldırır |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Konu (gerekli) |İstemci sertifikasını izin verilenler listesine ekler |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Konu (gerekli) |İzin verilenler listesinden istemci sertifikası konusunu kaldırır |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Şu anda izin verilen istemci sertifikası konularını alır (izin verilen konularla otomatik olarak indirilmeyen, yalnızca yerel olarak yapılandırılmış izin verilen konular) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>Sorun giderme

Ağ Geçidi tarafından günlüğe kaydedilen olayları toplamak için Log Analytics aracısının yüklü olması gerekir.

![Log Analytics ağ geçidi günlüğündeki Olay Görüntüleyicisi listesinin ekran görüntüsü](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Ağ Geçidi olay kimliklerini ve açıklamalarını Log Analytics

Aşağıdaki tabloda Log Analytics ağ geçidi günlük olaylarının olay kimlikleri ve açıklamaları gösterilmektedir.

| **ID** | **Açıklama** |
| --- | --- |
| 400 |Özel KIMLIĞI olmayan herhangi bir uygulama hatası. |
| 401 |Yanlış yapılandırma. Örneğin, listenPort = bir tamsayı yerine "metin". |
| 402 |TLS el sıkışma iletileri ayrıştırılırken özel durum. |
| 403 |Ağ hatası. Örneğin, hedef sunucuya bağlanamaz. |
| 100 |Genel bilgiler. |
| 101 |Hizmet başlatıldı. |
| 102 |Hizmet durdu. |
| 103 |İstemciden bir HTTP CONNECT komutu alındı. |
| 104 |HTTP CONNECT komutu değil. |
| 105 |Hedef sunucu izin verilenler listesinde değil veya hedef bağlantı noktası güvenli değil (443). <br> <br> OMS ağ geçidi sunucunuzdaki MMA aracısının ve OMS ağ geçidi ile iletişim kuran aracıların aynı Log Analytics çalışma alanına bağlı olduğundan emin olun. |
| 105 |TcpConnection hatası – geçersiz Istemci sertifikası: CN = Gateway. <br><br> OMS ağ geçidi sürüm 1.0.395.0 veya üstünü kullandığınızdan emin olun. Ayrıca OMS ağ geçidi sunucunuzdaki MMA aracısının ve OMS ağ geçidi ile iletişim kuran aracıların aynı Log Analytics çalışma alanına bağlı olduğundan emin olun. |
| 106 |Desteklenmeyen TLS/SSL protokolü sürümü.<br><br> Log Analytics ağ geçidi yalnızca TLS 1,0, TLS 1,1 ve 1,2 destekler. SSL 'yi desteklemez.|
| 107 |TLS oturumu doğrulandı. |

### <a name="performance-counters-to-collect"></a>Toplanacak performans sayaçları

Aşağıdaki tabloda Log Analytics ağ geçidi için kullanılabilen performans sayaçları gösterilmektedir. Sayaçları eklemek için performans Izleyicisini kullanın.

| **Ad** | **Açıklama** |
| --- | --- |
| Log Analytics ağ geçidi/etkin Istemci bağlantısı |Etkin istemci ağı (TCP) bağlantısı sayısı |
| Log Analytics ağ geçidi/hata sayısı |Hata sayısı |
| Log Analytics ağ geçidi/bağlı Istemci |Bağlı istemci sayısı |
| Log Analytics ağ geçidi/reddetme sayısı |Herhangi bir TLS doğrulama hatası nedeniyle reddetme sayısı |

![Log Analytics ağ geçidi arabiriminin ekran görüntüsü, performans sayaçlarını gösterir](./media/gateway/counters.png)

## <a name="assistance"></a>Yardım

Azure portal oturum açtığınızda, Log Analytics ağ geçidi veya başka bir Azure hizmeti veya özelliği ile ilgili yardım alabilirsiniz.
Yardım almak için portalın sağ üst köşesindeki soru işareti simgesini seçin ve **Yeni destek isteği**' ni seçin. Ardından yeni destek isteği formunu doldurun.

![Yeni destek isteğinin ekran görüntüsü](./media/gateway/support.png)

## <a name="next-steps"></a>Sonraki adımlar

Bağlı kaynaklardan veri toplamak için [veri kaynakları ekleyin](../../azure-monitor/platform/agent-data-sources.md) ve verileri Log Analytics çalışma alanınızda depolayın.
