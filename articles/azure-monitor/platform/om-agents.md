---
title: Azure Izleyici 'ye Operations Manager bağlanma | Microsoft Docs
description: System Center Operations Manager'a yaptığınız mevcut yatırımı korumak ve Log Analytics'le sağlanan genişletilmiş özellikleri kullanmak için, Operations Manager'ı çalışma alanınızla tümleştirebilirsiniz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 08/13/2019
ms.openlocfilehash: 79fcbb6f972eb022ce4d0e47a608e6f0d053a9ad
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162246"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Operations Manager Azure Izleyici 'ye bağlama

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) ' de mevcut yatırımlarınızı sürdürmek ve Azure izleyici ile genişletilmiş özellikleri kullanmak için, Operations Manager Log Analytics çalışma alanınızla tümleştirebilirsiniz. Bu, Operations Manager kullanmaya devam ederken Azure Izleyici 'de günlüklerin fırsatlarından yararlanmanızı sağlar:

* Operations Manager ile BT hizmetlerinizin durumunu izleyebilirsiniz.
* Olay ve sorun yönetimini destekleyen ITSM çözümlerinizle tümleştirmeyi koruyabilirsiniz.
* Operations Manager ile izlediğiniz şirket içi ve genel bulut IaaS sanal makinelerine dağıtılmış aracıların yaşam döngüsünü yönetebilirsiniz

System Center Operations Manager tümleştirme, Operations Manager günlük verilerini toplama, depolama ve çözümleme konusunda Azure Izleyici 'nin hızını ve verimliliğini kullanarak hizmet işlemleri stratejinize değer ekler. Azure Izleyici günlük sorguları, sorunların hatalarını tanımlamaya ve mevcut sorun yönetimi sürecinizi desteklemeye yönelik yinelenmeleri belirlemeye yardımcı olur. Bu verileri anlamlı yollarla göstermek için zengin panolar ve raporlama özellikleri ile performans, olay ve uyarı verilerini incelemek üzere sorgu altyapısının esnekliği, Azure Izleyici 'nin Operations Manager karmaşık olduğunu gösterir.

Operations Manager yönetim grubuna rapor veren aracılar, çalışma alanınızda etkinleştirdiğiniz [Log Analytics veri kaynaklarına](agent-data-sources.md) ve çözümlere göre sunucularınızdan veri toplar. Etkin çözümlere bağlı olarak, verileri doğrudan bir Operations Manager yönetim sunucusundan hizmete gönderilir ya da aracıyla yönetilen sistemde toplanan verilerin hacmi nedeniyle doğrudan aracıdan bir Log Analytics çalışma alanına gönderilir. Yönetim sunucusu verileri doğrudan hizmete iletir; bunlar hiçbir zaman işlem veya veri ambarı veritabanına yazılmaz. Bir yönetim sunucusu Azure Izleyici ile bağlantıyı kaybettiğinde, iletişim yeniden kuruluncaya kadar verileri yerel olarak önbelleğe alır. Yönetim sunucusu planlı bakım veya plansız kesinti nedeniyle çevrimdışıysa, yönetim grubundaki başka bir yönetim sunucusu Azure Izleyici ile bağlantıyı sürdürür.  

Aşağıdaki diyagramda, yön ve bağlantı noktaları da dahil olmak üzere bir System Center Operations Manager yönetim grubundaki ve Azure Izleyici 'deki yönetim sunucuları ve aracıları arasındaki bağlantı gösterilmektedir.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

BT güvenlik ilkeleriniz ağınızdaki bilgisayarların Internet 'e bağlanmasına izin vermediğinden, yönetim sunucuları yapılandırma bilgilerini almak ve bu çözümlere bağlı olarak toplanan verileri göndermek için Log Analytics ağ geçidine bağlanacak şekilde yapılandırılabilir etkinletir. Operations Manager yönetim grubunuzu Azure Izleyici 'ye bir Log Analytics ağ geçidiyle iletişim kuracak şekilde yapılandırma hakkında daha fazla bilgi ve adımlar için, bkz. [Log Analytics ağ geçidini kullanarak bilgisayarları Azure izleyici 'ye bağlama](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdaki gereksinimleri gözden geçirin.

* Azure Izleyici yalnızca System Center Operations Manager 2016 veya üstünü destekler, Operations Manager 2012 SP1 UR6 'DAKI veya üzeri ve Operations Manager 2012 R2 UR2 veya üzeri. Operations Manager 2012 SP1 UR7 ve Operations Manager 2012 R2 UR3'e ara sunucu desteği eklenmiştir.
* System Center Operations Manager 2016 ABD kamu bulutuyla tümleştirilirken güncelleştirme paketi 2 veya sonraki bir sürüme sahip güncelleştirilmiş bir danışman yönetim paketi gerekir. System Center Operations Manager 2012 R2, güncelleştirme paketi 3 veya sonraki sürümlere sahip güncelleştirilmiş bir danışman yönetim paketi gerektirir.
* Tüm Operations Manager aracılarının en düşük destek gereksinimlerini karşılaması gerekir. Aracıların en düşük güncelleştirmede olduğundan emin olun, aksi halde Windows Agent iletişimi başarısız olabilir ve Operations Manager olay günlüğünde hata oluşturabilir.
* Log Analytics çalışma alanı. Daha fazla bilgi için [Log Analytics çalışma alanına genel bakış](design-logs-deployment.md)konusunu inceleyin. 
* [Log Analytics katkıda bulunan rolünün](manage-access.md#manage-access-using-azure-permissions)bir üyesi olan bir hesapla Azure 'da kimlik doğrulaması yapabilirsiniz.

* Desteklenen bölgeler-bir Log Analytics çalışma alanına bağlanmak için System Center Operations Manager tarafından yalnızca aşağıdaki Azure bölgeleri desteklenir:
    - Orta Batı ABD
    - Avustralya Güneydoğu
    - Batı Avrupa
    - Doğu ABD
    - Güneydoğu Asya
    - Doğu Japonya
    - Birleşik Krallık, Güney
    - Orta Hindistan
    - Kanada Orta
    - Batı ABD 2

>[!NOTE]
>Azure API 'Lerinde yapılan son değişiklikler, müşterilerin yönetim grubu ve Azure Izleyici arasındaki tümleştirmeyi ilk kez başarıyla yapılandırmasını engelleyecek. Yönetim gruplarını hizmetle zaten tümleştirmiş olan müşteriler için, mevcut bağlantınızı yeniden yapılandırmanız gerekmiyorsa bu sorundan etkilenmez.  
>Aşağıdaki Operations Manager sürümleri için yeni bir yönetim paketi yayımlanmıştır:
> - System Center Operations Manager 2019 için, bu yönetim paketi kaynak medyaya dahildir ve yeni bir yönetim grubunun kurulumu sırasında veya yükseltme sırasında yüklenir.
>- Operations Manager 1801 yönetim paketi, Operations Manager 1807 için de geçerlidir.
>- System Center Operations Manager 1801 için, yönetim paketini [buradan](https://www.microsoft.com/download/details.aspx?id=57173)indirin.
>- System Center 2016-Operations Manager için, yönetim paketini [buradan](https://www.microsoft.com/download/details.aspx?id=57172)indirin.  
>- System Center Operations Manager 2012 R2 için, yönetim paketini [buradan](https://www.microsoft.com/download/details.aspx?id=57171)indirin.  


### <a name="network"></a>Ağ

Aşağıdaki bilgiler, Azure Izleyici ile iletişim kurmak için Operations Manager Aracısı, yönetim sunucuları ve Işletim konsolu için gereken proxy ve güvenlik duvarı yapılandırma bilgilerini listeler. Her bileşenden gelen trafik, ağınızdan Azure Izleyici 'ye giden bir trafiktir.

|Kaynak | Bağlantı noktası numarası| HTTP İncelemesini atlama|  
|---------|------|-----------------------|  
|**Aracı**|||  
|\*.ods.opinsights.azure.com| 443 |Yes|  
|\*.oms.opinsights.azure.com| 443|Yes|  
|\*.blob.core.windows.net| 443|Yes|  
|\*.azure-automation.net| 443|Yes|  
|**Yönetim sunucusu**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Yes|  
|\*.ods.opinsights.azure.com| 443| Yes|  
|*.azure-automation.net | 443| Yes|  
|**Konsolu Azure Izleyici 'ye Operations Manager**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 ve 443||  
|\*.microsoft.com| 80 ve 443||  
|\*.microsoftonline.com| 80 ve 443||  
|\*.mms.microsoft.com| 80 ve 443||  
|login.windows.net| 80 ve 443||  
|portal.loganalytics.io| 80 ve 443||
|api.loganalytics.io| 80 ve 443||
|docs.loganalytics.io| 80 ve 443||  

### <a name="tls-12-protocol"></a>TLS 1,2 Protokolü

Azure Izleyici 'ye geçişte verilerin güvenliğini sağlamak için aracıyı ve yönetim grubunu en az Aktarım Katmanı Güvenliği (TLS) 1,2 kullanacak şekilde yapılandırmanızı önemle öneririz. TLS/Güvenli Yuva Katmanı (SSL) uygulamasının güvenlik açığı olduğu ve geriye dönük uyumlulukla hala çalışmaya devam eden daha eski sürümlerinin **kullanılması önerilmez**. Daha fazla bilgi için [TLS 1,2 kullanarak verileri güvenli bir şekilde göndermeyi](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)inceleyin.

## <a name="connecting-operations-manager-to-azure-monitor"></a>Operations Manager Azure Izleyici 'ye bağlama

Operations Manager yönetim grubunuzu Log Analytics çalışma alanlarınızdan birine bağlanacak şekilde yapılandırmak için aşağıdaki adım serisini uygulayın.

Log Analytics çalışma alanı ile Operations Manager yönetim grubunuzun ilk kaydı sırasında, yönetim grubu için proxy yapılandırmasını belirtme seçeneği Işletim konsolunda kullanılamaz.  Bu seçeneğin sağlanması için önce yönetim grubunun hizmete başarıyla kaydedilmiş olması gerekir.  Bu sorunu geçici olarak çözmek için, Işletim konsolunu çalıştıran sistemde Netsh kullanarak sistem proxy yapılandırmasını güncelleştirmeniz gerekir ve yönetim grubundaki tüm yönetim sunucularını yapılandırın.  

1. Yükseltilmiş bir komut istemi açın.
   a. **Başlat** ' a gidin ve **cmd**yazın.
   b. **Komut istemi** ' ne sağ tıklayın ve yönetici olarak çalıştır * * seçeneğini belirleyin.
1. Aşağıdaki komutu girin ve **Enter** tuşuna basın:

    `netsh winhttp set proxy <proxy>:<port>`

Azure Izleyici ile tümleştirme için aşağıdaki adımları tamamladıktan sonra, `netsh winhttp reset proxy` çalıştırarak yapılandırmayı kaldırabilir ve sonra proxy veya Log Analytics ağ geçidi sunucusunu belirtmek için Işletim konsolundaki **proxy sunucusunu yapılandır** seçeneğini kullanabilirsiniz.

1. Operations Manager konsolunda **Yönetim** çalışma alanını seçin.
1. Operations Management Suite düğümünü genişletin ve **Bağlantı**'ya tıklayın.
1. **Operations Management Suite'e kaydolun** bağlantısına tıklayın.
1. **Operations Management Suite Ekleme Sihirbazı: Kimlik Doğrulama** sayfasında, OMS aboneliğinizle ilişkilendirilmiş yönetici hesabının e-posta adresi veya telefon numarasını ve parolasını girin ve **Oturum aç**'a tıklayın.

   >[!NOTE]
   >Operations Management Suite adı kullanımdan kaldırıldı.

1. Kimliği başarıyla doğrulandıktan sonra **Operations Management Suite Ekleme Sihirbazı: çalışma alanı sayfasını seçin** sayfasında Azure kiracınızı, aboneliğinizi ve Log Analytics çalışma alanınızı seçmeniz istenir. Birden çok çalışma alanınız varsa, açılan listeden Operations Manager yönetim grubuna kaydetmek istediğiniz çalışma alanını seçin ve ardından **İleri**'ye tıklayın.

   > [!NOTE]
   > Operations Manager bir kerede tek bir Log Analytics çalışma alanını destekler. Önceki çalışma alanıyla Azure Izleyici 'ye kayıtlı olan bağlantı ve bilgisayarlar Azure Izleyici 'den kaldırılır.
   >
   >
1. **Operations Management Suite Ekleme Sihirbazı: Özet** sayfasında ayarlarınızı onaylayın ve bunlar doğruysa **Oluştur**'a tıklayın.
1. **Operations Management Suite Ekleme Sihirbazı: Son** sayfasında **Kapat**'a tıklayın.

### <a name="add-agent-managed-computers"></a>Aracı tarafından yönetilen bilgisayarlar ekleme

Log Analytics çalışma alanınız ile tümleştirmeyi yapılandırdıktan sonra, yalnızca hizmetle bir bağlantı kurarak yönetim grubunuza raporlama aracılarından hiçbir veri toplanmaz. Bu, aracı tarafından yönetilen belirli bilgisayarların Azure Izleyici için günlük verilerini toplamasını yapılandırmadan önce gerçekleşmez. Bilgisayar nesnelerini tek tek seçebileceğiniz gibi, Windows bilgisayar nesnelerini içeren bir grup da seçebilirsiniz. Mantıksal diskler veya SQL veritabanları gibi başka bir sınıfın örneklerini içeren grupları seçemezsiniz.

1. Operations Manager konsolunu açın ve **Yönetim** çalışma alanını seçin.
1. Operations Management Suite düğümünü genişletin ve **Bağlantı**'ya tıklayın.
1. Bölmenin sağ tarafındaki Eylemler başlığı altında **Bilgisayar/Grup Ekle** bağlantısına tıklayın.
1. **Bilgisayar Araması** iletişim kutusunda Operations Manager tarafından izlenen bilgisayarları veya grupları arayabilirsiniz. Azure Izleyiciyi eklemek için Operations Manager yönetim sunucusu dahil olmak üzere bilgisayarları veya grupları seçin, **Ekle**' ye ve ardından **Tamam**' a tıklayın.

İşletim konsolunun **Yönetim** çalışma alanında Operations Manager Suite'in altındaki Yönetilen Bilgisayarlar düğümünden, veri toplamak için yapılandırılmış bilgisayarları ve grupları görüntüleyebilirsiniz. Burada, gerekirse bilgisayarları ve grupları ekleyebilir veya kaldırabilirsiniz.

### <a name="configure-proxy-settings-in-the-operations-console"></a>İşletim konsolunda ara sunucu ayarlarını yapılandırma

Yönetim grubu ve Azure Izleyici arasında bir iç proxy sunucusu varsa, aşağıdaki adımları gerçekleştirin. Bu ayarlar yönetim grubundan merkezi olarak yönetilir ve Azure Izleyici için günlük verilerini toplamak üzere kapsama dahil olan aracıyla yönetilen sistemlere dağıtılır.  Bazı çözümlerin yönetim sunucusunu atladığı ve doğrudan hizmete veri gönderdiği durumlarda, bu yararlı olur.

1. Operations Manager konsolunu açın ve **Yönetim** çalışma alanını seçin.
1. Operations Management Suite'i genişletin ve **Bağlantılar**'a tıklayın.
1. OMS Bağlantısı görünümünde, **Ara Sunucuyu Yapılandır**'a tıklayın.
1. **Operations Management Suite Sihirbazı: Ara Sunucu** sayfasında **Operations Management Suite erişimi için bir ara sunucu kullan**'ı seçin, ardından bağlantı noktası numarasını içeren URL'yi yazın (örneğin, http://corpproxy:80 ) ve **Son**'a tıklayın.

Proxy sunucunuz kimlik doğrulaması gerektiriyorsa, yönetim grubunda Azure Izleyici 'ye raporlayan yönetilen bilgisayarlara yayılması gereken kimlik bilgilerini ve ayarları yapılandırmak için aşağıdaki adımları gerçekleştirin.

1. Operations Manager konsolunu açın ve **Yönetim** çalışma alanını seçin.
1. **RunAs Yapılandırması** altında, **Profiller**'i seçin.
1. **System Center Advisor Farklı Çalıştır Profili Ara Sunucusu** profilini açın.
1. Farklı Çalıştır Profili Sihirbazı'nda, bir Farklı Çalıştır hesabı kullanmak için Ekle'ye tıklayın. [Farklı Çalıştır hesabı](https://technet.microsoft.com/library/hh321655.aspx) oluşturabilir veya mevcut bir hesabı kullanabilirsiniz. Doğrudan ara sunucuya geçiş yapmak için bu hesabın yeterli izinlere sahip olması gerekir.
1. Yönetilecek hesabı belirlemek için, **Seçilen bir sınıf, grup veya nesne**'yi seçin, **Seç...** düğmesine tıklayın ve ardından **Grup...** öğesine tıklayarak **Grup Araması** kutusunu açın.
1. **Microsoft System Center Advisor İzleme Sunucusu Grubu**'nu arayın ve ardından seçin. Grubu seçtikten sonra **Grup Araması** kutusunu kapatmak için **Tamam**'a tıklayın.
1. **Farklı Çalıştır Hesabı Ekle** kutusunu kapatmak için **Tamam**'a tıklayın.
1. Sihirbazı tamamlamak ve yaptığınız değişiklikleri kaydetmek için **Kaydet**'e tıklayın.

Bağlantı oluşturulduktan ve Azure Izleyici 'de günlük verilerini hangi aracıların toplayacağınızı ve rapor alyacağını yapılandırdıktan sonra, aşağıdaki yapılandırma sırasıyla Yönetim grubunda uygulanır:

* **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** Farklı Çalıştır Hesabı oluşturulur. Bu hesap **Microsoft System Center Advisor Farklı Çalıştır Profili Blobu** Farklı Çalıştır profiliyle ilişkilendirilmiştir ve iki sınıfı hedefler: **Koleksiyon Sunucusu** ve **Operations Manager Yönetim Grubu**.
* İki bağlayıcı oluşturulur.  İlki **Microsoft. SystemCenter. Advisor. DataConnector** olarak adlandırılmıştır ve yönetim grubundaki tüm sınıfların örneklerinden oluşturulan tüm uyarıları Azure izleyici 'ye ileten bir abonelikle otomatik olarak yapılandırılır. İkinci bağlayıcı, Azure Izleyici ve paylaşım verileriyle iletişim sağlanmasından sorumlu olan **danışman Bağlayıcısı**olur.
* Yönetim grubunda veri toplamak için seçilmiş olan aracılar ve gruplar **Microsoft System Center Advisor Sunucu İzleme Grubu**'na eklenir.

## <a name="management-pack-updates"></a>Yönetim paketi güncelleştirmeleri

Yapılandırma tamamlandıktan sonra Operations Manager yönetim grubu, Azure Izleyici ile bir bağlantı kurar. Yönetim sunucusu web hizmetiyle eşitlenir ve Operations Manager'la tümleştirilmek üzere etkinleştirmiş olduğunuz çözümler için yönetim paketleri biçiminde güncelleştirilmiş yapılandırma bilgilerini alır. Operations Manager, bu yönetim paketlerinin güncelleştirmelerini denetler ve kullanılabilir olduğunda onları otomatik olarak indirip içeri aktarır. Bu davranışı denetleyen özellikle iki kural vardır:

* **Microsoft. SystemCenter. Advisor. MPUpdate** -temel Azure izleyici yönetim paketlerini güncelleştirir. Varsayılan olarak her 12 saatte bir çalıştırılır.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - Çalışma alanınızda etkinleştirilmiş olan çözüm yönetim paketlerini güncelleştirir. Varsayılan olarak her beş (5) dakikada bir çalıştırılır.

Bu iki kuralı, otomatik indirmeyi devre dışı bırakarak veya yönetim sunucusunun Azure Izleyici ile ne sıklıkta eşitleyeceğini, yeni bir yönetim paketinin mevcut olup olmadığını ve indirilmesinin gerekip gerekmediğini belirleyerek geçersiz kılabilir. **Frequency** parametresini saniye cinsinden bir değerle değiştirip eşitleme zamanlamasında değişiklik yapmak veya **Enabled** parametresini değiştirip kuralları devre dışı bırakmak için, [Kuralı veya İzlemeyi Geçersiz Kılma](https://technet.microsoft.com/library/hh212869.aspx) altındaki adımları izleyin. Geçersiz kılmalarda, Operations Manager Yönetim Grubu sınıfındaki tüm nesneleri hedefleyin.

Üretim yönetim grubunuzdaki yönetim paketi sürümlerini denetlemek için mevcut değişiklik denetimi işleminizi takip etmeye devam etmek için, kuralları devre dışı bırakabilir ve güncelleştirmelere izin verildiğinde belirli zamanlarda etkinleştirebilirsiniz. Ortamınızda bir geliştirme veya QA yönetim grubu varsa ve İnternet'e bağlıysa, bu senaryoyu desteklemek için söz konusu yönetim grubunu Log Analytics çalışma alanıyla yapılandırabilirsiniz. Bu, Azure Izleyici yönetim paketlerinin yinelemeli sürümlerini üretim yönetim grubunuza bırakmadan önce gözden geçirmenize ve değerlendirmenize olanak tanır.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Operations Manager grubunu yeni bir Log Analytics Çalışma Alanına geçirme

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
1. Azure portalının sol alt köşesinde bulunan **Diğer hizmetler**'e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Log Analytics**'i seçin ve bir çalışma alanı oluşturun.  
1. Operations Manager Yöneticiler rolüne üye olan bir hesapla Operations Manager konsolunu açın ve **Yönetim** çalışma alanını seçin.
1. Log Analytics genişletin ve **Bağlantılar**' ı seçin.
1. Bölmenin orta kısmında **Operation Management Suite'i Yeniden Yapılandır** bağlantısını seçin.
1. **Log Analytics Ekleme sihirbazını** izleyin ve yeni Log Analytics çalışma alanınız ile ilişkili yönetici hesabının e-posta adresini veya telefon numarasını ve parolasını girin.

   > [!NOTE]
   > **Operations Management Suite Ekleme Sihirbazı: Çalışma Alanı Seçin** sayfasında kullanımda olan mevcut çalışma alanı gösterilir.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Azure Izleyici ile Operations Manager Tümleştirmesini doğrulama

Operations Manager tümleştirme için Azure Izleyici 'nin başarılı olduğunu doğrulayabilmeniz için birkaç farklı yol vardır.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Azure portalında tümleştirmeyi onaylamak için

1. Azure portalının sol alt köşesinde bulunan **Diğer hizmetler**'e tıklayın. Kaynak listesinde **Log Analytics** yazın. Yazmaya başladığınızda liste, girişinize göre filtrelenir.
1. Log Analytics çalışma alanlarınızın listesinde uygun çalışma alanını seçin.  
1. **Gelişmiş ayarlar**'ı, **Bağlı Kaynaklar**'ı ve sonra da **System Center**'ı seçin.
1. System Center Operations Manager bölümünün altındaki tabloda yönetim grubu adının listelendiğini, ayrıca aracı sayısının ve veriler son alındığındaki durumun gösterildiğini görüyor olmalısınız.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>İşletim konsolunda tümleştirmeyi onaylamak için

1. Operations Manager konsolunu açın ve **Yönetim** çalışma alanını seçin.
1. **Yönetim Paketleri**'ni seçin ve **Aranan:** metin kutusuna **Advisor** veya **Intelligence** yazın.
1. Etkinleştirdiğiniz çözümlere bağlı olarak, arama sonuçlarında ilgili yönetim paketinin listelendiğini görürsünüz.  Örneğin Uyarı Yönetimi çözümünü etkinleştirdiyseniz, listede Microsoft System Center Advisor Uyarı Yönetimi yönetim paketi yer alır.
1. **İzleme** görünümünden **Operations Management Suite\Sistem Durumu** görünümüne gidin.  **Yönetim Sunucusu Durumu** bölmesinin altında bir Yönetim sunucusu seçin ve **Ayrıntı Görünümü** bölmesinde **Kimlik doğrulama hizmeti URI'si** özelliğinin değerinin Log Analytics Çalışma Alanı Kimliği ile eşleştiğini onaylayın.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Azure Izleyici ile tümleştirmeyi kaldırma

Artık Operations Manager yönetim grubunuzda Log Analytics çalışma alanı arasında tümleştirmeye ihtiyacınız kalmadığında, yönetim grubunda bağlantıyı ve yapılandırmayı düzgün kaldırmak için izlenmesi gereken bazı adımlar vardır. Aşağıdaki yordam, yönetim grubunuzun başvurusunu silerek Log Analytics çalışma alanınızı güncellemiştir, Azure Izleyici bağlayıcılarını silin ve ardından hizmetle tümleştirmeyi destekleyen yönetim paketlerini silin.  

Operations Manager ile tümleştirilen ve Azure Izleyici ile tümleştirmeyi desteklemek için gereken yönetim paketleri için yönetim paketleri yönetim grubundan kolayca silinemez. Bunun nedeni Azure Izleyici yönetim paketlerinden bazılarının diğer ilgili yönetim paketlerine bağımlılıkları vardır. Diğer yönetim paketlerinde bağımlılıkları olan yönetim paketlerini silmek için, TechNet Betik Merkezi'nden [bağımlılıkları olan yönetim paketini kaldırma](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) betiğini indirin.  

1. Operations Manager Yöneticiler rolüne üye olan bir hesapla Operations Manager Komut Kabuğu'nu açın.

    > [!WARNING]
    > Devam etmeden önce adında Advisor veya IntelligencePack terimi bulunan hiçbir özel yönetim paketiniz olmadığını doğrulayın; aksi takdirde, aşağıdaki adımları o paketleri de yönetim grubundan siler.
    >

1. Komut kabuğu istemcisine `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue` yazın
1. Sonra `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue` yazın
1. Diğer System Center Advisor yönetim paketlerinde bağımlılığı olan kalan yönetim paketlerini kaldırmak için, daha önce TechNet Betik Merkezi'nden indirmiş olduğunuz *RecursiveRemove.ps1* betiğini kullanın.  

    > [!NOTE]
    > Advisor yönetim paketlerini PowerShell ile kaldırma adımı, Microsoft System Center Advisor veya Microsoft System Center Advisor Iç yönetim paketlerini otomatik olarak silmez.  Bunları silmeyi denemeyin.  
    >  

1. Operations Manager Yöneticiler rolüne üye olan bir hesapla Operations Manager İşletim konsolunu açın.
1. **Yönetim**'in altında **Yönetim Paketleri** düğümünü açın, **Aranan:** kutusuna **Advisor** yazın ve aşağıdaki yönetim paketlerinin yönetim grubunuza aktarılmış durumda olduğunu doğrulayın:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Azure portal **Ayarlar** kutucuğuna tıklayın.
1. **Bağlı kaynaklar**' ı seçin.
1. System Center Operations Manager bölümündeki tabloda, çalışma alanından kaldırmak istediğiniz yönetim grubunun adını görmeniz gerekir. **Son Veriler** sütununun altında **Kaldır**'a tıklayın.  

    > [!NOTE]
    > Bağlı yönetim grubundan hiçbir etkinlik algılanmazsa 14 gün geçene kadar **Kaldır** bağlantısı kullanılamaz.  
    >

1. Kaldırma işlemine devam etmek istediğinizi onaylamanızı isteyen bir pencere görüntülenir.  Devam etmek için **Evet**'e tıklayın.

İki bağlayıcıyı (Microsoft.SystemCenter.Advisor.DataConnector ve Advisor Connector) silmek için, aşağıdaki PowerShell betiğini bilgisayarınıza kaydedin ve aşağıdaki örnekleri kullanarak yürütün:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Bu betiği çalıştırdığınız bilgisayar bir yönetim sunucusu değilse, yönetim grubunuzun sürümüne bağlı olarak bu bilgisayarda Operations Manager komut kabuğunun yüklü olması gerekir.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Gelecekte, yönetim grubunuzu bir Log Analytics çalışma alanına yeniden bağlamayı planlıyorsanız, `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` yönetim paketi dosyasını yeniden içeri aktarmanız gerekir. Ortamınıza dağıtılan System Center Operations Manager sürümüne bağlı olarak bu dosyayı aşağıdaki konumda bulabilirsiniz:

* System Center 2016 - Operations Manager ve üstü için kaynak medyada `\ManagementPacks` klasörünün altında.
* Yönetim grubunuza uygulanan en son güncelleştirme dağıtımından. Operations Manager 2012 için kaynak klasör `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` ve 2012 R2 için `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`bulunur.

## <a name="next-steps"></a>Sonraki adımlar

İşlevsellik eklemek ve veri toplamak için bkz. [Çözüm Galerisi Azure izleyici çözümlerini ekleme](../../azure-monitor/insights/solutions.md).
