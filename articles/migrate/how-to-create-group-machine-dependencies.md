---
title: Azure geçişi 'nde aracı tabanlı bağımlılık analizini ayarlama
description: Bu makalede, Azure geçişi 'nde aracı tabanlı bağımlılık analizinin nasıl ayarlanacağı açıklanır.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6ded5d4ed8c2a55939bba908a05adbd2dea2ccbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714785"
---
# <a name="set-up-dependency-visualization"></a>Bağımlılık görselleştirmesini ayarlama

Bu makalede, Azure geçişi: bulma ve değerlendirme 'da aracı tabanlı bağımlılık analizinin nasıl ayarlanacağı açıklanır. [Bağımlılık Analizi](concepts-dependency-visualization.md) , değerlendirmek ve Azure 'a geçirmek istediğiniz sunucular genelinde bağımlılıkları belirlemenize ve anlamanıza yardımcı olur.

## <a name="before-you-start"></a>Başlamadan önce

- İçin aracı tabanlı bağımlılık analizine yönelik destek ve dağıtım gereksinimlerini gözden geçirin:
    - [VMware ortamındaki sunucular](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Fiziksel sunucular](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Hyper-V ortamındaki sunucular](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Şunları yaptığınızdan emin olun:
    - Bir Azure geçişi projesi vardır. Bunu yapmazsanız, şimdi bir tane [oluşturun](./create-manage-projects.md) .
    - Projeye Azure geçişi: bulma ve değerlendirme aracını [eklemişseniz](how-to-assess.md) .
    - Şirket içi sunucuları bulmaya yönelik bir [Azure geçiş](migrate-appliance.md) gereci ayarlayın. Gereç, şirket içi sunucuları bulur ve Azure geçişi: bulma ve değerlendirme için meta verileri ve performans verilerini gönderir. İçin bir gereç ayarlayın:
        - [VMware ortamındaki sunucular](how-to-set-up-appliance-vmware.md)
        - [Hyper-V ortamındaki sunucular](how-to-set-up-appliance-hyper-v.md)
        - [Fiziksel sunucular](how-to-set-up-appliance-physical.md)
- Bağımlılık görselleştirmesini kullanmak için bir [Log Analytics çalışma alanını](../azure-monitor/logs/manage-access.md) Azure geçişi projesiyle ilişkilendirirsiniz:
    - Bir çalışma alanını yalnızca Azure geçiş gereci ayarladıktan ve Azure geçişi projesindeki sunucuları bulduktan sonra ekleyebilirsiniz.
    - Abonelikte Azure geçişi projesini içeren bir çalışma alanı olduğundan emin olun.
    - Çalışma alanı Doğu ABD, Güneydoğu Asya veya Batı Avrupa bölgelerinde bulunmalıdır. Diğer bölgelerdeki çalışma alanları bir projeyle ilişkilendirilemez.
    - Çalışma alanının [hizmet eşlemesi desteklendiği](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)bir bölgede olması gerekir.
    - Yeni veya mevcut bir Log Analytics çalışma alanını Azure geçişi projesiyle ilişkilendirebilirsiniz.
    - Bir sunucu için bağımlılık görselleştirmesini ilk kez ayarladığınızda çalışma alanını iliştirebilirsiniz. Bir Azure geçişi projesi çalışma alanı eklendikten sonra değiştirilemez.
    - Log Analytics, Azure geçişi ile ilişkili çalışma alanı, geçiş projesi anahtarıyla ve proje adıyla etiketlenir.

## <a name="associate-a-workspace"></a>Çalışma alanını ilişkilendirme

1. Sunucuları değerlendirme için bulduktan sonra,   >  **Azure geçişi: bulma ve değerlendirme** sunucularında **Genel Bakış ' a** tıklayın.  
2. **Azure geçişi: bulma ve değerlendirme** bölümünde **temel** bileşenler ' e tıklayın.
3. **OMS çalışma alanında** **yapılandırma gerekiyor**' a tıklayın.

     ![Log Analytics çalışma alanını yapılandırma](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. **OMS çalışma alanını Yapılandır**' da, yeni bir çalışma alanı oluşturmak isteyip istemediğinizi belirtin veya var olanı kullanın.
    - Proje aboneliğindeki tüm çalışma alanlarından var olan bir çalışma alanı seçebilirsiniz.
    - İlişkilendirmek için çalışma alanına okuyucu erişimine ihtiyacınız vardır.
5. Yeni bir çalışma alanı oluşturursanız, onun için bir konum seçin.

    ![Yeni bir çalışma alanı Ekle](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> Özel uç nokta bağlantısı için OMS çalışma alanını yapılandırmayı [öğrenin](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) .  

## <a name="download-and-install-the-vm-agents"></a>Sanal makine aracılarını indirip yükleme

Çözümlemek istediğiniz her bir sunucuda aracıları yükleyebilirsiniz.

> [!NOTE]
> System Center Operations Manager 2012 R2 veya üzeri tarafından izlenen sunucular için MMA aracısını yüklemeniz gerekmez. Hizmet Eşlemesi Operations Manager tümleştirilir. Tümleştirme kılavuzunu [izleyin](../azure-monitor/vm/service-map-scom.md#prerequisites) .

1. **Azure geçişi: bulma ve değerlendirme** bölümünde **bulunan sunucular**' a tıklayın.
2. Bağımlılık görselleştirmesi ile çözümlemek istediğiniz her sunucu için, **Bağımlılıklar** sütununda, **Aracı yüklemesi gerektirir**' a tıklayın.
3. **Bağımlılıklar** sayfasında, Windows veya Linux için MMA ve bağımlılık aracısını indirin.
4. **MMA Aracısını Yapılandır** altında, çalışma alanı kimliğini ve anahtarı kopyalayın. MMA aracısını yüklerken bunlara ihtiyacınız vardır.

    ![Aracıları yükler](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>MMA’yı yükleme

Çözümlemek istediğiniz her Windows veya Linux sunucusuna MMA 'yı yükleyebilirsiniz.

### <a name="install-mma-on-a-windows-server"></a>Windows Server 'da MMA 'yı yükler

Aracıyı bir Windows sunucusuna yüklemek için:

1. İndirilen aracıya çift tıklayın.
2. **Hoş Geldiniz** sayfasında **İleri**'ye tıklayın. **Lisans Koşulları** sayfasında **Kabul Ediyorum**’a tıklayarak lisansı kabul edin.
3. **Hedef klasörde**, **sonraki**> varsayılan yükleme klasörünü tutun veya değiştirin.
4. **Aracı kurulum seçenekleri**' nde, **Azure Log Analytics**  >  **İleri**' yi seçin.
5. Yeni bir Log Analytics çalışma alanı eklemek için **Ekle** ' ye tıklayın. Portaldan kopyaladığınız çalışma alanı KIMLIĞINI ve anahtarını yapıştırın. **İleri**’ye tıklayın.

Aracıyı komut satırından veya Configuration Manager veya [ıntigua](https://www.intigua.com/intigua-for-azure-migration)gibi otomatikleştirilmiş bir yöntem kullanarak yükleyebilirsiniz.
- MMA aracısını yüklemek için bu yöntemleri kullanma hakkında [daha fazla bilgi edinin](../azure-monitor/agents/log-analytics-agent.md#installation-options) .
- MMA aracısı bu [betik](https://github.com/brianbar-MSFT/Install-MMA) kullanılarak da yüklenebilir.
- MMA tarafından desteklenen Windows işletim sistemleri hakkında [daha fazla bilgi edinin](../azure-monitor/agents/agents-overview.md#supported-operating-systems) .

### <a name="install-mma-on-a-linux-server"></a>Linux sunucusuna MMA 'yı yükler

MMA 'yı bir Linux sunucusuna yüklemek için:

1. SCP/SFTP kullanarak uygun paketi (x86 veya x64) Linux bilgisayarınıza aktarın.
2. --İnstall bağımsız değişkenini kullanarak paketi yükler.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA tarafından Linux işletim sistemleri desteğinin listesi hakkında [daha fazla bilgi edinin](../azure-monitor/agents/agents-overview.md#supported-operating-systems) . 

## <a name="install-the-dependency-agent"></a>Bağımlılık aracısını yükleme

1. Bağımlılık aracısını bir Windows sunucusuna yüklemek için kurulum dosyasına çift tıklayın ve Sihirbazı izleyin.
2. Bir Linux sunucusuna bağımlılık Aracısı 'nı yüklemek için, aşağıdaki komutu kullanarak kök olarak ' yi kullanın:

    ```sh InstallDependencyAgent-Linux64.bin```

- Bağımlılık aracısını yüklemek için betikleri nasıl kullanabileceğiniz hakkında [daha fazla bilgi edinin](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) .
- Bağımlılık Aracısı tarafından desteklenen işletim sistemleri hakkında [daha fazla bilgi edinin](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) .


## <a name="create-a-group-using-dependency-visualization"></a>Bağımlılık görselleştirmesi kullanarak Grup oluşturma

Şimdi değerlendirme için bir grup oluşturun. 


> [!NOTE]
> Bağımlılıklarını görselleştirmek istediğiniz gruplar 10 ' dan fazla sunucu içermemelidir. 10 ' dan fazla sunucunuz varsa, bunları daha küçük gruplara ayırın.

1. **Azure geçişi: bulma ve değerlendirme** bölümünde **bulunan sunucular**' a tıklayın.
2. **Bağımlılıklar** sütununda, gözden geçirmek istediğiniz her sunucu Için **bağımlılıkları görüntüle** ' ye tıklayın.
3. Bağımlılık eşlemesinde, aşağıdakileri görebilirsiniz:
    - Sunucudan gelen (istemciler) ve giden (sunucular) TCP bağlantıları.
    - Bağımlılık aracıları yüklü olmayan bağımlı sunucular, bağlantı noktası numaralarına göre gruplandırılır.
    - Bağımlılık aracıları yüklü bağımlı sunucular ayrı kutular olarak gösterilir.
    - Sunucu içinde çalışan süreçler. İşlem görüntülemek için her bir sunucu kutusunu genişletin.
    - Sunucu Özellikleri (FQDN, işletim sistemi, MAC adresi dahil). Ayrıntıları görüntülemek için her bir sunucu kutusuna tıklayın.

4. Zaman aralığı etiketindeki zaman süresine tıklayarak farklı zaman sürelerine yönelik bağımlılıklara bakabilirsiniz.
    - Aralık varsayılan olarak bir saattir. 
    - Zaman aralığını değiştirebilir veya başlangıç ve bitiş tarihlerini ve süreyi belirtebilirsiniz.
    - Zaman aralığı bir saate kadar sürebilir. Daha uzun bir aralığa ihtiyacınız varsa, bağımlı verileri daha uzun bir süre sorgulamak için Azure Izleyici 'yi kullanın.

5. Gruplamak istediğiniz bağımlı sunucuları tanımladıktan sonra, haritada birden çok sunucu seçmek için CTRL + tıklama tuşlarını kullanın ve **Grup makineleri**' ne tıklayın.
6. Bir grup adı belirtin.
7. Bağımlı sunucuların Azure geçişi tarafından keşfedildiğini doğrulayın.

    - Azure geçişi: bulma ve değerlendirme tarafından bağımlı bir sunucu bulunamadıysa gruba ekleyemezsiniz.
    - Bir sunucu eklemek için bulma işlemini yeniden çalıştırın ve sunucunun bulunduğunu doğrulayın.

8. Bu grup için bir değerlendirme oluşturmak istiyorsanız, grup için yeni bir değerlendirme oluşturmak üzere onay kutusunu seçin.
8. Grubu kaydetmek için **Tamam** ' ı tıklatın.

Grubu oluşturduktan sonra gruptaki tüm sunuculara aracılar yüklemenizi ve sonra tüm grubun bağımlılıklarını görselleştirmenizi öneririz.

## <a name="query-dependency-data-in-azure-monitor"></a>Azure Izleyici 'de bağımlılık verilerini sorgulama

Azure geçişi projesiyle ilişkili Log Analytics çalışma alanındaki Hizmet Eşlemesi tarafından yakalanan bağımlılık verilerini sorgulayabilirsiniz. Log Analytics, Azure Izleyici günlük sorgularını yazmak ve çalıştırmak için kullanılır.

- Log Analytics Hizmet Eşlemesi verileri aramanızı [öğrenin](../azure-monitor/vm/service-map.md#log-analytics-records) .
- [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)günlük sorgularının yazılmasına ilişkin [bir genel bakış alın](../azure-monitor/logs/get-started-queries.md) .

Bağımlılık verileri için aşağıdaki gibi bir sorgu çalıştırın:

1. Aracıları yükledikten sonra portala gidin ve **Genel Bakış ' a** tıklayın.
2. **Azure geçişi: bulma ve değerlendirme** bölümünde **Genel Bakış ' a** tıklayın. **Temelleri** genişletmek için aşağı oka tıklayın.
3. **OMS çalışma** alanında, çalışma alanı adına tıklayın.
3. Log Analytics çalışma alanı sayfasında **genel**> **Günlükler**' e tıklayın.
4. Sorgunuzu yazın ve **Çalıştır**' a tıklayın.

### <a name="sample-queries"></a>Örnek sorgular

Bağımlılık verilerini ayıklamak için kullanabileceğiniz birkaç örnek sorgu aşağıda verilmiştir.

- Tercih ettiğiniz veri noktalarını ayıklamak için sorguları değiştirebilirsiniz.
- Bağımlılık verileri kayıtlarının tüm listesini [gözden geçirin](../azure-monitor/vm/service-map.md#log-analytics-records) .
- Ek örnek sorguları [gözden geçirin](../azure-monitor/vm/service-map.md#sample-log-searches) .

#### <a name="sample-review-inbound-connections"></a>Örnek: gelen bağlantıları gözden geçirme

Bir sunucu kümesi için gelen bağlantıları gözden geçirin.

- Bağlantı ölçümleri (VMConnection) tablosundaki kayıtlar tek tek fiziksel ağ bağlantılarını temsil etmez.
- Birden çok fiziksel ağ bağlantısı mantıksal bir bağlantı halinde gruplandırılır.
- Fiziksel ağ bağlantısı verilerinin VMConnection 'da nasıl toplandığından [daha fazla bilgi edinin](../azure-monitor/vm/service-map.md#connections) .

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Örnek: gönderilen ve alınan verileri özetleme

Bu örnek, bir sunucular kümesi arasındaki gelen bağlantılarda gönderilen ve alınan verilerin hacmini özetler.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Sonraki adımlar

Bir grup için [değerlendirme oluşturun](how-to-create-assessment.md) .
