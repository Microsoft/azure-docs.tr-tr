---
title: Azure geçişi ile makine bağımlılıklarını kullanan makineleri gruplandırın | Microsoft Docs
description: Azure geçişi hizmeti ile makine bağımlılıkları kullanılarak nasıl değerlendirme oluşturulacağını açıklar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: 1cd1ff83fd706e3474426f6cc2ac99d39e74dc22
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177942"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Değerlendirme için bağımlılık görselleştirmesini ayarlama

Bu makalede, Azure geçişi: Sunucu değerlendirmesi ' nde bağımlılık eşlemesinin nasıl ayarlanacağı açıklanır.

Bağımlılık eşleme, değerlendirmek ve geçirmek istediğiniz makineler arasında bağımlılıkları görselleştirmenize yardımcı olur.

- Azure geçişi: Sunucu değerlendirmesi ' nde, makineleri değerlendirme için birlikte toplayın. Genellikle birlikte geçirmek istediğiniz makineler.
- Daha yüksek düzeyde güvenle grupları değerlendirmek istediğinizde genellikle bağımlılık eşlemesini kullanırsınız.
- Bağımlılık eşleme, bir değerlendirme ve geçiş çalıştırmadan önce makine bağımlılıklarını çapraz denetlemenize yardımcı olur.
- Bağımlılıkları eşleme ve görselleştirme, Azure 'a geçişinizi etkin bir şekilde planlamaya yardımcı olur. Geçiş sırasında beklenmedik kesintilerden kaçınmak için hiçbir şeyin gerisinde olmadığından emin olmaya yardımcı olur.
- Eşleme kullanarak, birlikte geçirilmesi gereken bağımlı sistemleri bulabilirsiniz. Ayrıca, çalışan bir sistemin hala kullanıcılara hizmet verip vermediğini veya geçiş yerine yetki alma için bir aday olduğunu belirleyebilirsiniz.

Bağımlılık görselleştirmesi hakkında [daha fazla bilgi edinin](concepts-dependency-visualization.md#how-does-it-work) .

## <a name="before-you-start"></a>Başlamadan önce

- Bir Azure geçişi projesi [oluşturduğunuzdan](how-to-add-tool-first-time.md) emin olun.
- Zaten bir proje oluşturduysanız Azure geçişi: Sunucu değerlendirmesi [aracını eklediğinizden emin](how-to-assess.md) olun.
- Azure geçişi 'nde makinelerinizi keşfetdiğinizden emin olun; Bunu, [VMware](how-to-set-up-appliance-vmware.md) veya [Hyper-V](how-to-set-up-appliance-hyper-v.md)için bir Azure geçiş gereci ayarlayarak yapabilirsiniz. Gereç, şirket içi makineleri bulur ve Azure geçişi: Sunucu değerlendirmesi ' ne meta veri ve performans verileri gönderir. [Daha fazla bilgi edinin](migrate-appliance.md).


**Özellikler** | **Not**
--- | ---
Erişilebilirlik | Bağımlılık görselleştirmesi Azure Kamu 'da kullanılamaz.
Hizmet Eşlemesi | Bağımlılık görselleştirmesi Azure Izleyici 'de Hizmet Eşlemesi çözümünü kullanır. [Hizmet eşlemesi](../azure-monitor/insights/service-map.md) , sunucular arasındaki bağlantıları otomatik olarak bulur ve gösterir.
Aracılar | Bağımlılık görselleştirmesini kullanmak için, eşlemek istediğiniz makinelere aşağıdaki aracıları yükleyebilirsiniz:<br/> - [Log Analytics aracı](../azure-monitor/platform/log-analytics-agent.md) Aracısı (daha önce Microsoft Monitoring Agent (MMA) olarak adlandırılır.<br/> - [hizmet eşlemesi bağımlılık Aracısı](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent).<br/><br/> Aracı yüklemesini otomatikleştirmek için, Azure geçişi için bir aracı dağıtım çözümü olan System Center Configuration Manager gibi bir dağıtım aracı kullanabilirsiniz.
Bağımlılık aracısı | Windows ve Linux için [bağımlılık Aracısı desteğini](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) gözden geçirin.<br/><br/> Bağımlılık aracısını yüklemek için betikleri kullanma hakkında [daha fazla bilgi edinin](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) .
Log Analytics Aracısı (MMA) | MMA yükleme yöntemleri hakkında [daha fazla bilgi edinin](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent) .<br/><br/> System Center Operations Manager 2012 R2 veya üzeri tarafından izlenen makinelerde MMA aracısını yüklemeniz gerekmez. Hizmet Eşlemesi Operations Manager tümleştirilir. [Buradaki](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)kılavuzlardan yararlanarak tümleştirmeyi etkinleştirebilirsiniz. Bununla birlikte, bağımlılık aracısının bu makinelere yüklenmesi gerekeceğini unutmayın.<br/><br/> Log Analytics Aracısı tarafından desteklenen Linux işletim sistemlerini [gözden geçirin](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) .
Değerlendirme grupları | Bağımlılıklarını görselleştirmek istediğiniz gruplar 10 ' dan fazla makine içermemelidir. 10 ' dan fazla makineniz varsa, bağımlılıkları görselleştirmek için bunları daha küçük gruplara ayırın.

## <a name="associate-a-log-analytics-workspace"></a>Log Analytics çalışma alanını ilişkilendir

Bağımlılık görselleştirmesini kullanmak için bir [Log Analytics çalışma alanını](../azure-monitor/platform/manage-access.md) Azure geçişi projesiyle ilişkilendirmeniz gerekir.

- Yalnızca Azure geçişi proje aboneliğine bir çalışma alanı ekleyebilirsiniz.
- Var olan bir çalışma alanını ekleyebilir veya yenisini oluşturabilirsiniz.
- Bir makine için bağımlılık görselleştirmesini ilk kez ayarladığınızda çalışma alanını iliştirebilirsiniz.
- Yalnızca Azure geçişi projesindeki makineler bulduktan sonra bir çalışma alanı ekleyebilirsiniz. Bunu, [VMware](how-to-set-up-appliance-vmware.md) veya [Hyper-V](how-to-set-up-appliance-hyper-v.md)için bir Azure geçiş gereci ayarlayarak yapabilirsiniz. Gereç, şirket içi makineleri bulur ve Azure geçişi: Sunucu değerlendirmesi ' ne meta veri ve performans verileri gönderir. [Daha fazla bilgi edinin](migrate-appliance.md).

Çalışma alanını aşağıdaki şekilde ekleyin:

1. **Azure geçişi: Sunucu değerlendirmesi**' nde **Genel Bakış ' a**tıklayın. Henüz sunucu değerlendirmesi aracını eklemediyseniz, [önce bunu yapın](how-to-assess.md).
2. **Genel bakış**bölümünde, **temelleri**genişletmek için aşağı oka tıklayın.
3. **OMS çalışma alanında** **yapılandırma gerekiyor**' a tıklayın.
4. **Çalışma alanını Yapılandır**' da, yeni bir çalışma alanı oluşturmak isteyip istemediğinizi belirtin veya var olanı kullanın:

    ![Çalışma alanı Ekle](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Yeni bir çalışma alanı için bir ad belirttikten sonra, çalışma alanının oluşturulacağı [bölgeyi](https://azure.microsoft.com/global-infrastructure/regions/) seçebilirsiniz.
    - Mevcut bir çalışma alanını eklediğinizde, geçiş projesiyle aynı abonelikte bulunan tüm kullanılabilir çalışma alanlarını seçebilirsiniz.
    - Eklemek için çalışma alanına okuyucu erişimine ihtiyacınız vardır.
    - Bir projeyle ilişkili çalışma alanını iliştirildikten sonra değiştiremezsiniz.

## <a name="download-and-install-the-vm-agents"></a>Sanal makine aracılarını indirip yükleme

Aracıları, bağımlılık eşleme ile görselleştirmek istediğiniz her şirket içi makineye indirin ve yükleyin.

1. **Azure geçişi: Sunucu değerlendirmesi**' nde, **bulunan sunucular**' a tıklayın.
2. Bağımlılık görselleştirmesini kullanmak istediğiniz her makine için, **Aracı yüklemesi gerektirir öğesine**tıklayın.
3. Bir > makinenin **Bağımlılıklar** sayfasında, **MMA 'yı indirip yükleyin**, uygun aracıyı indirin ve aşağıda açıklandığı gibi yükleyin.
4. **Bağımlılık Aracısı 'Nı indir ve yükle**' de, uygun aracıyı indirin ve aşağıda açıklandığı gibi yükleyin.
5. **MMA Aracısını Yapılandır**altında, çalışma alanı kimliğini ve anahtarı kopyalayın. MMA aracısını yüklerken bunlara ihtiyacınız vardır.

### <a name="install-the-mma"></a>MMA’yı yükleme

#### <a name="install-the-agent-on-a-windows-machine"></a>Aracıyı bir Windows makinesine yükler

Aracıyı bir Windows makinesine yüklemek için:

1. İndirilen aracıya çift tıklayın.
2. **Hoş Geldiniz** sayfasında **İleri**'ye tıklayın. **Lisans Koşulları** sayfasında **Kabul Ediyorum**’a tıklayarak lisansı kabul edin.
3. **Hedef klasörde**, **sonraki**> varsayılan yükleme klasörünü tutun veya değiştirin.
4. **Aracı kurulum seçenekleri**' nde, **Azure Log Analytics**@no__t **-2 ' yi seçin**.
5. Yeni bir Log Analytics çalışma alanı eklemek için **Ekle** ' ye tıklayın. Portaldan kopyaladığınız çalışma alanı KIMLIĞINI ve anahtarını yapıştırın. **İleri**’ye tıklayın.

Aracıyı komut satırından veya System Center Configuration Manager veya [ıntigua](https://go.microsoft.com/fwlink/?linkid=2104196)gibi otomatikleştirilmiş bir yöntem kullanarak yükleyebilirsiniz. MMA aracısını yüklemek için bu yöntemleri kullanma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) . MMA aracısı bu [betik](https://go.microsoft.com/fwlink/?linkid=2104394) kullanılarak da yüklenebilir.

MMA tarafından desteklenen Windows işletim sistemleri hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) .

#### <a name="install-the-agent-on-a-linux-machine"></a>Aracıyı bir Linux makinesine yükler

Aracıyı bir Linux makinesine yüklemek için:

1. SCP/SFTP kullanarak uygun paketi (x86 veya x64) Linux bilgisayarınıza aktarın.
2. --İnstall bağımsız değişkenini kullanarak paketi yükler.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

MMA tarafından Linux işletim sistemleri desteğinin listesi hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) . 

### <a name="install-the-dependency-agent"></a>Bağımlılık aracısını yükleme
1. Bağımlılık aracısını bir Windows makinesine yüklemek için kurulum dosyasına çift tıklayın ve Sihirbazı izleyin.
2. Bir Linux makinesine bağımlılık Aracısı 'nı yüklemek için, aşağıdaki komutu kullanarak kök olarak ' yi kullanın:

    ```sh InstallDependencyAgent-Linux64.bin```

Bağımlılık aracısını yüklemek için betikleri nasıl kullanabileceğiniz hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) .

Bağımlılık Aracısı tarafından desteklenen işletim sistemleri hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) .


## <a name="create-a-group-using-dependency-visualization"></a>Bağımlılık görselleştirmesi kullanarak Grup oluşturma

1. **Azure geçişi: Sunucu değerlendirmesi**' nde, **bulunan sunucular**' a tıklayın.
2. **Bağımlılıklar** sütununda, gözden geçirmek istediğiniz her makine Için **bağımlılıkları görüntüle** ' ye tıklayın.
3. Bağımlılık eşlemesinde, aşağıdakileri görebilirsiniz:
    - Makineden gelen (istemciler) ve giden (sunucular) TCP bağlantıları.
    - Bağımlılık aracıları yüklü olmayan bağımlı makineler, bağlantı noktası numaralarına göre gruplandırılır.
    - Bağımlılık aracıları yüklü bağımlı makineler ayrı kutular olarak gösterilir.
    - Makine içinde çalışan süreçler. Her makine kutusunu genişleterek süreçlerini görüntüleyin.
    - Makine Özellikleri (FQDN, işletim sistemi, MAC adresi dahil). Ayrıntıları görüntülemek için her bir makine kutusuna tıklayın.

4. Zaman aralığı etiketindeki zaman süresine tıklayarak farklı zaman sürelerine yönelik bağımlılıklara bakabilirsiniz. Aralık varsayılan olarak bir saattir. Zaman aralığını değiştirebilir veya başlangıç ve bitiş tarihlerini ve süreyi belirtebilirsiniz.

    > [!NOTE]
    > Zaman aralığı bir saate kadar sürebilir. Daha uzun bir aralığa ihtiyacınız varsa, bağımlı verileri daha uzun bir süre sorgulamak için Azure Izleyici 'yi kullanın.

5. Gruplamak istediğiniz bağımlı makineleri tanımladıktan sonra, haritada birden fazla makine seçmek için CTRL + tıklama tuşlarını kullanın ve **Grup makineleri**' ne tıklayın.
6. Bir grup adı belirtin.
7. Bağımlı makinelerin Azure geçişi tarafından bulunduğundan emin olun.

    - Azure geçişi: Sunucu değerlendirmesi tarafından bağımlı bir makine bulunamadıysa gruba ekleyemezsiniz.
    - Bir makine eklemek için, bulmayı yeniden çalıştırın ve makinenin bulunduğunu doğrulayın.

8. Bu grup için bir değerlendirme oluşturmak istiyorsanız, grup için yeni bir değerlendirme oluşturmak üzere onay kutusunu seçin.
8. Grubu kaydetmek için **Tamam** ' ı tıklatın.

Grubu oluşturduktan sonra gruptaki tüm makinelere aracılar yüklemenizi ve sonra tüm grubun bağımlılıklarını görselleştirmenizi öneririz.

## <a name="query-dependency-data-in-azure-monitor"></a>Azure Izleyici 'de bağımlılık verilerini sorgulama

Azure geçişi projenizle ilişkili Log Analytics çalışma alanındaki Hizmet Eşlemesi tarafından yakalanan bağımlılık verilerini sorgulayabilirsiniz. Log Analytics, Azure Izleyici günlük sorgularını yazmak ve çalıştırmak için kullanılır.

- Log Analytics Hizmet Eşlemesi verileri aramanızı [öğrenin](../azure-monitor/insights/service-map.md#log-analytics-records) .
- [Log Analytics](../azure-monitor/log-query/get-started-portal.md)günlük sorgularının yazılmasına ilişkin [bir genel bakış alın](../azure-monitor/log-query/get-started-queries.md) .

Bağımlılık verileri için aşağıdaki gibi bir sorgu çalıştırın:

1. Aracıları yükledikten sonra portala gidin ve **Genel Bakış ' a**tıklayın.
2. **Azure geçişi: Sunucu değerlendirmesi**' nde **Genel Bakış ' a**tıklayın. **Temelleri**genişletmek için aşağı oka tıklayın.
3. **OMS çalışma**alanında, çalışma alanı adına tıklayın.
3. Log Analytics çalışma alanı sayfasında **genel**> **Günlükler**' e tıklayın.
4. Sorgunuzu yazın ve **Çalıştır**' a tıklayın.

### <a name="sample-queries"></a>Örnek sorgular

Bağımlılık verilerini ayıklamak için kullanabileceğiniz bir dizi örnek sorgu sağlıyoruz.

- Tercih ettiğiniz veri noktalarını ayıklamak için sorguları değiştirebilirsiniz.
- Bağımlılık verileri kayıtlarının tüm listesini [gözden geçirin](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) .
- Ek örnek sorguları [gözden geçirin](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) .

#### <a name="sample-review-inbound-connections"></a>Örnek: gelen bağlantıları gözden geçirme

Bir VM kümesi için gelen bağlantıları gözden geçirin.

- Bağlantı ölçümleri (VMConnection) tablosundaki kayıtlar tek tek fiziksel ağ bağlantılarını temsil etmez.
- Birden çok fiziksel ağ bağlantısı mantıksal bir bağlantı halinde gruplandırılır.
- Fiziksel ağ bağlantısı verilerinin VMConnection 'da nasıl toplandığından [daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) .

```
// the machines of interest
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

Bu örnek, bir makine kümesi arasındaki gelen bağlantılarda gönderilen ve alınan verilerin hacmini özetler.

```
// the machines of interest
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
