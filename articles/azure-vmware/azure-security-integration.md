---
title: Azure Güvenlik Merkezi tümleştirmesiyle Azure VMware Çözüm sanal makinelerinizi koruyun
description: Azure VMware Çözüm VM 'lerinizi Azure Güvenlik Merkezi panosundan Azure 'un yerel güvenlik araçlarıyla koruyun.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: d04f0ac3e3934442ce5b6d5fbf4b53e18b3dff18
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877525"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Azure Güvenlik Merkezi tümleştirmesiyle Azure VMware Çözüm sanal makinelerinizi koruyun

Azure yerel güvenlik araçları, Azure, Azure VMware çözümünün ve şirket içi sanal makinelerin (VM) karma ortamı için koruma sağlar. Bu makalede, karma ortam güvenliği için Azure Araçları ayarlama gösterilmektedir. Bu araçları çeşitli tehditleri tanımlamak ve gidermek için kullanacaksınız.

## <a name="azure-native-services"></a>Azure yerel hizmetleri

Azure yerel hizmetlerine yönelik hızlı bir özet aşağıda verilmiştir:

- **Log Analytics çalışma alanı:** Log Analytics çalışma alanı, günlük verilerinin depolanması için benzersiz bir ortamdır. Her çalışma alanının kendi veri deposu ve yapılandırması vardır. Veri kaynakları ve çözümler, verilerini belirli bir çalışma alanında depolamak üzere yapılandırılmıştır.
- **Azure Güvenlik Merkezi:** Azure Güvenlik Merkezi, Birleşik bir altyapı güvenliği yönetim sistemidir. Veri merkezlerinin güvenliğini güçlendirir ve bulutta veya şirket içinde karma iş yükleri genelinde gelişmiş tehdit koruması sağlar.
- **Azure Sentinel:** Azure Sentinel, bulutta yerel bir güvenlik bilgileri olay yönetimi (SıEM) çözümüdür. Bir ortam genelinde güvenlik analizi, uyarı algılama ve otomatikleştirilmiş tehdit yanıtı sağlar.

## <a name="topology"></a>Topoloji

![Azure tümleşik güvenlik mimarisini gösteren bir diyagram.](media/azure-security-integration/azure-integrated-security-architecture.png)

Log Analytics Aracısı, Azure, Azure VMware çözümü ve şirket içi VM 'lerden günlük verilerinin toplanmasını mümkün bir şekilde sunar. Günlük verileri Azure Izleyici günlüklerine gönderilir ve bir Log Analytics çalışma alanında depolanır. Yeni ve mevcut VM 'Ler için Arc etkin sunucular [VM uzantıları desteğini](../azure-arc/servers/manage-vm-extensions.md) kullanarak Log Analytics aracısını dağıtabilirsiniz. 

Günlükler Log Analytics çalışma alanı tarafından toplandıktan sonra, Log Analytics çalışma alanını Azure Güvenlik Merkezi ile yapılandırabilirsiniz. Azure Güvenlik Merkezi, Azure VMware Çözüm VM 'lerinin güvenlik açığı durumunu değerlendirir ve kritik güvenlik açıkları için bir uyarı yükseltir. Örneğin, değerlendirir eksik işletim sistemi düzeltme ekleri, güvenlik yapılandırması ve [uç nokta koruması](../security-center/security-center-services.md).

Uyarı algılama, tehdit görünürlüğü, arama ve tehdit yanıtı için Log Analytics çalışma alanını Azure Sentinel ile yapılandırabilirsiniz. Yukarıdaki diyagramda Azure Güvenlik Merkezi, Azure Güvenlik Merkezi bağlayıcısı kullanılarak Azure Sentinel 'e bağlanır. Azure Güvenlik Merkezi, bir olay oluşturmak ve diğer tehditlere eşlemek için ortam güvenlik açığını Azure Sentinel 'e iletir. Ayrıca, istenmeyen etkinlikleri algılamak ve olaylara dönüştürmek için zamanlanmış kurallar sorgusunu da oluşturabilirsiniz.

## <a name="benefits"></a>Avantajlar

- Azure yerel hizmetleri, Azure 'da, Azure VMware çözümünde ve şirket içi hizmetlerde karma ortam güvenliği için kullanılabilir.
- Log Analytics çalışma alanı kullanarak, verileri veya günlükleri tek bir noktaya toplayabilir ve aynı verileri farklı Azure yerel hizmetlerine sunabilirsiniz.
- Azure Güvenlik Merkezi, aşağıdakiler dahil olmak üzere birçok özellik sunar:
    - Dosya bütünlüğünü izleme
    - Dosya daha az saldırı algılama
    - İşletim sistemi düzeltme eki değerlendirmesi 
    - Güvenlik yapılandırması hataları değerlendirmesi
    - Endpoint Protection değerlendirmesi
- Azure Sentinel şunları yapmanıza olanak sağlar:
    - Hem şirket içinde hem de birden çok bulutta, tüm kullanıcılar, cihazlar, uygulamalar ve altyapıda bulut ölçeğinde veri toplayın.
    - Daha önce algılanmayan tehditleri algıla.
    - Ölçekteki şüpheli etkinlikler için yapay zeka ve Hunt ile tehditleri araştırın.
    - Ortak görevlerin yerleşik düzenlemesi ve otomasyonu ile olaylara hızla yanıt verin.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics çalışma alanı oluşturma

Çeşitli kaynaklardan veri toplamak için bir Log Analytics çalışma alanı gerekir. Daha fazla bilgi için [Azure portal Log Analytics çalışma alanı oluşturma](../azure-monitor/logs/quick-create-workspace.md)bölümüne bakın. 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Güvenlik Merkezi 'Ni dağıtma ve Azure VMware Çözüm VM 'lerini yapılandırma

Azure Güvenlik Merkezi, dağıtım gerektirmeyen önceden yapılandırılmış bir araçtır. Azure portal, **Güvenlik Merkezi** ' ni arayıp seçin.

### <a name="enable-azure-defender"></a>Azure Defender’ı etkinleştirme

Azure Defender, Azure Güvenlik Merkezi 'nin Gelişmiş tehdit korumasını hem şirket içinde hem de bulutta karma iş yükleriniz genelinde genişletir. Bu nedenle Azure VMware Çözüm sanal makinelerinizi korumak için Azure Defender 'ı etkinleştirmeniz gerekir. 

1. Güvenlik Merkezi 'nde **Başlarken**' i seçin.

2. **Yükselt** sekmesini seçin ve ardından aboneliğinizi veya çalışma alanınızı seçin. 

3. Azure Defender 'ı etkinleştirmek için **Yükselt** ' i seçin.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Azure VMware Çözüm VM 'lerini Güvenlik Merkezi 'ne ekleme

1. Azure portal **Azure yay** üzerinde arama yapın ve bunu seçin.

2. Kaynaklar altında **sunucular** ' ı ve ardından **+ Ekle**' yi seçin.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Azure 'a bir Azure VMware Çözüm VM 'si eklemek için Azure Arc sunucuları sayfasını gösteren ekran görüntüsü.":::

3. **Betik oluştur**' u seçin.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Etkileşimli betik kullanarak sunucu ekleme seçeneğini gösteren Azure yay sayfası ekran görüntüsü."::: 
 
4. **Önkoşullar** sekmesinde **İleri**' yi seçin.

5. **Kaynak ayrıntıları** sekmesinde, aşağıdaki ayrıntıları girin: 
    - Abonelik
    - Kaynak grubu
    - Region 
    - İşletim sistemi
    - Proxy sunucusu ayrıntıları
    
    Ardından **İleri: Etiketler**' i seçin.

6. **Etiketler** sekmesinde **İleri**' yi seçin.

7. **Betiği indir ve Çalıştır** sekmesinde **İndir**' i seçin.

8. İşletim sisteminizi belirtin ve betiği Azure VMware Çözüm VM 'niz üzerinde çalıştırın.

## <a name="view-recommendations-and-passed-assessments"></a>Önerileri ve geçmiş değerlendirmeleri görüntüleme

1. Azure Güvenlik Merkezi 'nde sol bölmeden **Envanter** ' ı seçin.

2. Kaynak türü için **sunucular-Azure Arc**' ı seçin.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Kaynak türü altında seçili olan sunucuları gösteren Azure Güvenlik Merkezi envanter sayfasının ekran görüntüsü.":::

3. Kaynağınızın adını seçin. Kaynağınızın güvenlik durumu ayrıntılarını gösteren bir sayfa açılır.

4. **Öneri listesi** altında bu ayrıntıları görüntülemek **için öneriler**, **geçilen değerlendirmeler** ve **kullanım dışı değerlendirmeler** sekmelerini seçin.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Güvenlik önerilerini ve değerlendirmeleri gösteren Azure Güvenlik Merkezi 'nin ekran görüntüsü.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Azure Sentinel çalışma alanını dağıtma

Azure Sentinel, Log Analytics çalışma alanının üzerine kurulmuştur. Azure Sentinel 'i ekleme bölümündeki ilk adımınız, bu amaçla kullanmak istediğiniz Log Analytics çalışma alanını seçmenizdir.

1. Azure portal **Azure Sentinel**' i arayın ve seçin.

2. Azure Sentinel çalışma alanları sayfasında **+ Ekle**' yi seçin.

3. Log Analytics çalışma alanını seçin ve **Ekle**' yi seçin.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Azure VMware Çözüm VM 'lerinde güvenlik olayları için veri toplayıcıyı etkinleştirme

Artık Azure Sentinel 'i veri kaynaklarınızla (Bu durumda güvenlik olayları) bağlamaya hazırsınız.

1. Azure Sentinel çalışma alanları sayfasında, yapılandırılan çalışma alanını seçin.

2. Yapılandırma altında **veri bağlayıcıları**' nı seçin.

3. Bağlayıcı adı sütununun altında, listeden **güvenlik olayları** ' nı seçin ve ardından **bağlayıcı sayfasını aç**' ı seçin.

4. Bağlayıcı sayfasında, akışa almak istediğiniz olayları seçin ve sonra **Değişiklikleri Uygula**' yı seçin.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Azure Sentinel 'de, hangi olayların akışa alınacağı seçebileceğiniz güvenlik olayları sayfasının ekran görüntüsü.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Azure Güvenlik Merkezi ile Azure Sentinel 'e bağlanma  

1. Azure Sentinel çalışma alanı sayfasında, yapılandırılan çalışma alanını seçin.

2. Yapılandırma altında **veri bağlayıcıları**' nı seçin.

3. Listeden **Azure Güvenlik Merkezi** ' ni seçin ve ardından **bağlayıcı sayfasını aç**' ı seçin.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Azure Sentinel 'de Azure Güvenlik Merkezi 'ne bağlanmak için seçimi gösteren Azure Sentinel 'deki veri bağlayıcıları sayfasının ekran görüntüsü.":::

4. Azure Güvenlik Merkezi 'ni Azure Sentinel 'e bağlamak için **Bağlan** ' ı seçin.

5. Azure Güvenlik Merkezi için bir olay oluşturmak üzere **olay oluşturmayı** etkinleştirin.

## <a name="create-rules-to-identify-security-threats"></a>Güvenlik tehditlerini tanımlamak için kurallar oluşturma

Veri kaynaklarını Azure Sentinel 'e bağladıktan sonra, algılanan tehditler için uyarı oluşturmak üzere kurallar oluşturabilirsiniz. Aşağıdaki örnekte, Windows Server 'da yanlış parolayla oturum açma girişimleri için bir kural oluşturacağız.

1. Azure Sentinel Genel Bakış sayfasında, Konfigürasyonlar ' ın altında **analiz**' ı seçin.

2. Konfigürasyonlar ' ın altında **analiz**' ı seçin.

3. **+ Oluştur** ' u seçin ve açılan kutuda **zamanlanan sorgu kuralı**' nı seçin.

4. **Genel** sekmesinde, gerekli bilgileri girin.

    - Ad
    - Açıklama
    - Taktikler
    - Önem derecesi
    - Durum

    **İleri ' yi seçin: kural mantığını ayarla >**.

5. **Kural mantığını ayarla** sekmesinde, gerekli bilgileri girin.

    - Kural sorgusu (burada örnek sorgumuzu gösteriliyor)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Varlıkları eşle
    - Sorgu zamanlaması
    - Uyarı eşiği
    - Olay gruplama
    - Gizleme

    **İleri**’yi seçin.

6. **Olay ayarları** sekmesinde, **Bu analiz kuralı tarafından tetiklenen uyarılardan olay oluştur** ' u etkinleştirin ve **Ileri ' yi seçin: otomatik yanıt >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Azure Sentinel 'de yeni bir kural oluşturmak için analitik Kural Sihirbazı 'nın ekran görüntüsü. Bu kural tarafından tetiklenen uyarılardan olayları etkin olarak gösterir.":::

7. **İleri ' yi seçin: >gözden geçirin**.

8. **İnceleme ve oluşturma** sekmesinde, bilgileri gözden geçirin ve **Oluştur**' u seçin.

Windows Server 'da başarısız olan üçüncü oturum açma denemesinden sonra oluşturulan kural, her başarısız girişim için bir olay tetikler.

## <a name="view-alerts"></a>Uyarıları görüntüleme

Oluşturulan olayları Azure Sentinel ile görüntüleyebilirsiniz. Ayrıca, olayları atayıp Azure Sentinel içinden bir kez çözümlendikten sonra kapatabilirsiniz.

1. Azure Sentinel genel bakış sayfasına gidin.

2. Tehdit yönetimi altında **Olaylar**' ı seçin.

3. Bir olay seçin. Daha sonra bir çözüm için olayı bir ekibe atayabilirsiniz.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Olay seçiliyken Azure Sentinel olayları sayfasının ekran görüntüsü ve çözümleme için olayı atama seçeneği.":::

    Sorunu çözdükten sonra kapatabilirsiniz.

## <a name="hunt-security-threats-with-queries"></a>Sorgularla güvenlik tehditleri

Ortamınızdaki tehditleri belirlemek için sorgular oluşturabilir veya Azure Sentinel 'de önceden tanımlanmış kullanılabilir sorguyu kullanabilirsiniz. Aşağıdaki adımlar önceden tanımlanmış bir sorgu çalıştırır.

1. Azure Sentinel genel bakış sayfasına gidin.

2. Tehdit yönetimi altında, **Arayıcı**' ı seçin. Önceden tanımlanmış sorguların listesi görüntülenir.

3. Bir sorgu seçin ve ardından **Sorguyu Çalıştır**' ı seçin.

4. Sonuçları denetlemek için **sonuçları görüntüle** ' yi seçin.

### <a name="create-a-new-query"></a>Yeni sorgu oluşturma

1.  Tehdit yönetimi altında, ve **+ Yeni sorgu** **' yı** seçin.

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="+ Yeni sorgu vurgulanmış şekilde Azure Sentinel arama sayfasının ekran görüntüsü.":::

2. Özel bir sorgu oluşturmak için aşağıdaki bilgileri girin.

    - Ad
    - Açıklama
    - Özel sorgu
    - Eşleme girin
    - Taktikler
    
3. **Oluştur**’u seçin. Sonra oluşturulan sorguyu seçebilir, **sorgu çalıştırabilir** ve **sonuçları görüntüleyebilirsiniz**.

## <a name="next-steps"></a>Sonraki adımlar

Azure VMware Çözüm sanal makinelerinizi nasıl koruyabileceğinizi artık kapsadığınıza göre şunları öğrenmek isteyebilirsiniz:

- [Azure Defender panosunu](../security-center/azure-defender-dashboard.md) kullanma
- [Azure Sentinel 'de gelişmiş çok aşamalı saldırı algılama](../azure-monitor/logs/quick-create-workspace.md)
- [Azure VMware Çözüm VM 'lerinin yaşam döngüsü yönetimi](lifecycle-management-of-azure-vmware-solution-vms.md)
