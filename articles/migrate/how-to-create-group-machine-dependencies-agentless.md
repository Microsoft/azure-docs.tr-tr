---
title: Azure geçişi 'nde aracısız bağımlılık analizini ayarlama
description: Azure geçişi 'nde aracısız bağımlılık analizini ayarlayın.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 7966750d7c3e0f12bb9404a4d78bbc27e4075c52
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786592"
---
# <a name="analyze-server-dependencies-agentless"></a>Sunucu bağımlılıklarını çözümleme (aracısız)

Bu makalede, Azure geçişi: bulma ve değerlendirme aracı kullanılarak aracısız bağımlılık analizinin nasıl ayarlanacağı açıklanır. [Bağımlılık Analizi](concepts-dependency-visualization.md) , Azure 'a değerlendirme ve geçiş için sunucular genelinde bağımlılıkları belirlemenize ve anlamanıza yardımcı olur.

> [!IMPORTANT]
> Aracısız bağımlılık analizi, Azure geçişi: bulma ve değerlendirme aracı ile keşfedilen, VMware ortamınızda çalışan sunucular için şu anda önizlemededir.
> Bu önizleme müşteri desteği kapsamında ele alınmıştır ve üretim iş yükleri için kullanılabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Geçerli sınırlamalar

- Bağımlılık analizi görünümünde, şu anda bir gruba bir sunucu ekleyemez veya gruptan sunucu kaldıramazsınız.
- Bir sunucu grubu için bağımlılık eşlemesi Şu anda kullanılamıyor.
- Bir Azure geçişi projesinde, bağımlılık verileri koleksiyonu 1000 sunucuları için eşzamanlı olarak etkinleştirilebilir. 1000 sunucu toplu işleri halinde sıralama yaparak daha yüksek sayıda sunucu çözümleyebilirsiniz.

## <a name="before-you-start"></a>Başlamadan önce

- Azure geçişi: bulma ve değerlendirme aracı eklenmiş [bir proje oluşturduğunuzdan](./create-manage-projects.md) emin olun.
- Bağımlılık analizi gerçekleştirmek için [VMware gereksinimlerini](migrate-support-matrix-vmware.md#vmware-requirements) gözden geçirin.
- Gereci ayarlamadan önce [gereç gereksinimlerini](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) gözden geçirin.
- Sunucularda bağımlılık analizini etkinleştirmeden önce [bağımlılık Analizi gereksinimlerini gözden geçirin](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) .

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Azure geçişi gereci dağıtma ve yapılandırma

1. Azure geçişi gerecini dağıtmaya yönelik gereksinimleri [gözden geçirin](migrate-appliance.md#appliance---vmware) .
2. Gereçlerin [ortak](migrate-appliance.md#public-cloud-urls) ve [kamu bulutlarında](migrate-appliance.md#government-cloud-urls)erişmesi gereken Azure URL 'lerini gözden geçirin.
3. Bulma ve değerlendirme sırasında gereç tarafından toplanan [verileri gözden geçirin](migrate-appliance.md#collected-data---vmware) .
4. Gereç [için bağlantı noktası](migrate-support-matrix-vmware.md#port-access-requirements) erişim gereksinimleri.
5. Bulmayı başlatmak için [Azure geçişi gereci dağıtın](how-to-set-up-appliance-vmware.md) . Gereci dağıtmak için, bir OVA şablonunu indirip vCenter Server çalıştıran bir sunucu oluşturmak için VMware 'ye içeri aktarabilirsiniz. Gereci dağıttıktan sonra projeyi projeye kaydetmeniz ve bulmayı başlatacak şekilde yapılandırmanız gerekir.
6. Gereci yapılandırırken, Gereç Yapılandırma Yöneticisi 'nde aşağıdakileri belirtmeniz gerekir:
    - Bağlanmak istediğiniz vCenter Server ayrıntıları.
    - VMware ortamınızdaki sunucuları bulmaya yönelik kapsamlı kimlik bilgileri vCenter Server.
    - Etki alanı/Windows (etki alanı olmayan)/Linux (etki alanı olmayan) kimlik bilgileri olabilen sunucu kimlik bilgileri. Kimlik bilgilerini ve bunları nasıl işleyeceğinizi [öğrenmek hakkında daha fazla bilgi edinin](add-server-credentials.md) .

## <a name="verify-permissions"></a>İzinleri doğrulama

- Bulma ve değerlendirme için [vCenter Server salt okunurdur bir hesap oluşturmanız](./tutorial-discover-vmware.md#prepare-vmware) gerekir. Bir salt okuma hesabının   >  , bağımlılık verilerini toplamak üzere sunucularla etkileşime geçmesini sağlamak için sanal makineler **Konuk işlemleri** için etkinleştirilmiş ayrıcalıklara ihtiyacı vardır.
- Azure geçişi 'nin bağımlılık verilerini toplamak üzere sunucuya erişebilmeleri için bir kullanıcı hesabına ihtiyacınız vardır. Windows ve Linux sunucuları için hesap gereksinimleri hakkında [bilgi edinin](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) .

### <a name="add-credentials-and-initiate-discovery"></a>Kimlik bilgileri ekleme ve bulmayı başlatma

1. Gereç yapılandırma yöneticisini açın, gerecin önkoşul denetimlerini ve kaydını doldurun.
2. **Kimlik bilgilerini ve bulma kaynaklarını yönetme** paneline gidin.
1.  **1. Adım: vCenter Server kimlik bilgilerini belirtin** bölümünde, gerecin vCenter Server çalıştıran sunucuları keşfetmesi için kullanacağı vCenter Server hesabı için kimlik bilgilerini sağlamak üzere **kimlik bilgileri ekle** ' ye tıklayın.
1. **2. Adım: vCenter Server ayrıntıları belirtin** bölümünde, açılan listeden kimlik bilgileri için kolay ad seçmek üzere **bulma kaynağı Ekle** ' ye tıklayın, :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server Ayrıntılar için gereç Yapılandırma Yöneticisi"::: ' nde vCenter Server örneği panel 3 ' ün **IP adresini/FQDN** 'sini belirtin
1. **3. Adım: yazılım envanterini gerçekleştirmek için sunucu kimlik bilgilerini sağlama, aracısız bağımlılık Analizi ve SQL Server örnekleri ve veritabanlarının bulunması**, yazılım envanterini başlatmak için birden çok sunucu kimlik bilgisi sağlamak üzere **kimlik bilgileri ekle** ' ye tıklayın.
1. VCenter Server bulmayı **başlatmak için bulmayı Başlat**' a tıklayın.

 VCenter Server bulma işlemi tamamlandıktan sonra, Gereç yüklü uygulamalar, roller ve Özellikler (yazılım envanteri) bulmayı başlatır. Yazılım envanteri sırasında, eklenen sunucu kimlik bilgileri sunuculara göre tekrarlanacaktır ve aracısız bağımlılık analizi için onaylanır. Sunucular için aracısız bağımlılık analizini portaldan etkinleştirebilirsiniz. Aracısız bağımlılık analizini etkinleştirmek için yalnızca doğrulamanın başarılı olduğu sunucular seçilebilir.

## <a name="start-dependency-discovery"></a>Bağımlılık bulmayı Başlat

Bağımlılık bulmayı etkinleştirmek istediğiniz sunucuları seçin.

1. **Azure geçişi: bulma ve değerlendirme** bölümünde **bulunan sunucular**' a tıklayın.
2. Bulmayı gözden geçirmek istediğiniz **gereç adını** seçin.
1. **Bağımlılıklar (aracısız)** sütununda sunucuların doğrulama durumunu görebilirsiniz.
1. **Bağımlılık Analizi** açılır düğmesine tıklayın.
1. **Sunucu Ekle**' ye tıklayın.
1. **Sunucu Ekle** sayfasında, bağımlılık analizini etkinleştirmek istediğiniz sunucuları seçin. Bağımlılık eşlemesini yalnızca doğrulamanın başarılı olduğu sunucularda etkinleştirebilirsiniz. Sonraki doğrulama döngüsünün son doğrulama zaman damgasından sonra 24 saat çalışır.
1. Sunucuları seçtikten sonra **Sunucu Ekle**' ye tıklayın.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Bağımlılık analizini Başlat":::

Sunucularda bağımlılık analizini etkinleştirdikten sonra, altı saat etrafında bağımlılıkları görselleştirebilirsiniz. Bağımlılık analizi için aynı anda birden çok sunucuyu etkinleştirmek istiyorsanız, [PowerShell](#start-or-stop-dependency-analysis-using-powershell) 'i kullanarak bunu yapabilirsiniz.

## <a name="visualize-dependencies"></a>Bağımlılıkları görselleştirin

1. **Azure geçişi: bulma ve değerlendirme** bölümünde **bulunan sunucular**' a tıklayın.
1. Bulmayı gözden geçirmek istediğiniz **gereç adını** seçin.
1. Bağımlılıklarını görmek istediğiniz sunucu için arama yapın.
1. **Bağımlılıklar (aracısız)** sütununun altında, **bağımlılıkları görüntüle** ' ye tıklayın.
1. **Zaman süresi** açılan listesini kullanarak Haritayı görüntülemek istediğiniz zaman aralığını değiştirin.
1. Seçili sunucuya bağımlılığı olan sunucuları listelemek için **istemci** grubunu genişletin.
1. Seçili sunucudan bağımlılığı olan sunucuları listelemek için **bağlantı noktası** grubunu genişletin.
1. Bağımlı sunuculardan herhangi birinin harita görünümüne gitmek için sunucu adına tıklayın > sunucu **haritasını yükle** 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="sunucu bağlantı noktası grubu ' nu genişletin ve sunucu haritasını yükleyin":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="İstemci grubunu Genişlet":::

8. Her bağımlılığın işlem düzeyi ayrıntılarını görüntülemek için seçili sunucuyu genişletin.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="İşlem göstermek için sunucuyu genişlet":::

> [!NOTE]
> Bağımlılık için işlem bilgileri her zaman kullanılabilir değildir. Kullanılabilir değilse, bağımlılık "Bilinmeyen işlem" olarak işaretlenen işlemle birlikte gösterilmiştir.

## <a name="export-dependency-data"></a>Bağımlılık verilerini dışarı aktar

1. **Azure geçişi: bulma ve değerlendirme** bölümünde **bulunan sunucular**' a tıklayın.
2. **Bağımlılık Analizi** açılır düğmesine tıklayın.
3. **Uygulama bağımlılıklarını dışarı aktar**' a tıklayın.
4. **Uygulama bağımlılıklarını dışarı aktar** sayfasında, istenen sunucuları bulan gereç adını seçin.
5. Başlangıç saatini ve bitiş saatini seçin. Verileri yalnızca son 30 güne indirebileceğinizi unutmayın.
6. **Bağımlılığı dışarı aktar** öğesine tıklayın.

Bağımlılık verileri bir CSV biçiminde verilir ve indirilir. İndirilen dosya, bağımlılık analizi için etkinleştirilen tüm sunuculardaki bağımlılık verilerini içerir. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Bağımlılıkları dışarı aktar":::

### <a name="dependency-information"></a>Bağımlılık bilgileri

İçe aktarılmış CSV içindeki her satır, belirtilen zaman yuvasında gözlemlenen bir bağımlılığa karşılık gelir.

Aşağıdaki tablo, dışarıya aktarılmış CSV içindeki alanları özetler. Sunucu adı, uygulama ve işlem alanlarının yalnızca aracısız bağımlılık Analizi etkin olan sunucular için doldurulduğunu unutmayın.

**Alan adı** | **Ayrıntılar**
--- | --- 
Timeslot | Bağımlılığın gözlemlendiği zaman lotu. <br/> Bağımlılık verileri, şu anda 6 saatlik yuva üzerinden yakalanır.
Kaynak sunucu adı | Kaynak sunucunun adı 
Kaynak uygulama | Kaynak sunucudaki uygulamanın adı  
Kaynak işlem | Kaynak sunucudaki işlemin adı  
Hedef sunucu adı | Hedef sunucunun adı
Hedef IP | Hedef sunucunun IP adresi
Hedef uygulama | Hedef sunucudaki uygulamanın adı
Hedef işlem | Hedef sunucudaki işlemin adı  
Hedef bağlantı noktası | Hedef sunucudaki bağlantı noktası numarası

## <a name="stop-dependency-discovery"></a>Bağımlılık bulmayı durdur

Bağımlılık bulmayı durdurmak istediğiniz sunucuları seçin.

1. **Azure geçişi: bulma ve değerlendirme** bölümünde **bulunan sunucular**' a tıklayın.
1. Bulmayı gözden geçirmek istediğiniz **gereç adını** seçin.
1. **Bağımlılık Analizi** açılır düğmesine tıklayın.
1. **Sunucuları kaldır**' a tıklayın.
1. **Sunucuları kaldır** sayfasında, bağımlılık analizi için durdurmak istediğiniz sunucuyu seçin.
1. Sunucuları seçtikten sonra **sunucuları kaldır**' a tıklayın.

Aynı anda birden çok sunucuda bağımlılığı durdurmak istiyorsanız, [PowerShell](#start-or-stop-dependency-analysis-using-powershell) 'i kullanarak bunu yapabilirsiniz.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>PowerShell kullanarak bağımlılık analizini başlatma veya durdurma

GitHub 'daki [Azure PowerShell örnekleri](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) deposundan PowerShell modülünü indirin.

### <a name="log-in-to-azure"></a>Azure'da oturum açma

1. Connect-AzAccount cmdlet 'ini kullanarak Azure aboneliğinizde oturum açın.

    ```PowerShell
    Connect-AzAccount
    ```
    Azure Kamu kullanıyorsanız, aşağıdaki komutu kullanın.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Projeyi oluşturduğunuz aboneliği seçin 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. İndirilen AzMig_Dependencies PowerShell modülünü içeri aktarma

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Bağımlılık veri toplamayı etkinleştir veya devre dışı bırak

1. Aşağıdaki komutları kullanarak projenizdeki bulunan sunucuların listesini alın. Aşağıdaki örnekte, proje adı FabrikamDemoProject ve ait olduğu kaynak grubu FabrikamDemoRG. Sunucu listesi FabrikamDemo_VMs.csv ' de kaydedilecek

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    Dosyasında, sunucu görünen adını, bağımlılık koleksiyonunun geçerli durumunu ve bulunan tüm sunucuların ARM KIMLIĞINI görebilirsiniz. 

2. Bağımlılıkları etkinleştirmek veya devre dışı bırakmak için bir giriş CSV dosyası oluşturun. Dosyanın "ARM KIMLIĞI" üst bilgisine sahip bir sütunu olması gerekir. CSV dosyasındaki ek üstbilgiler yok sayılır. Önceki adımda oluşturulan dosyayı kullanarak CSV oluşturabilirsiniz. Bağımlılıkları etkinleştirmek veya devre dışı bırakmak istediğiniz sunucuları koruyarak dosyanın bir kopyasını oluşturun. 

    Aşağıdaki örnekte, FabrikamDemo_VMs_Enable.csv giriş dosyasındaki sunucu listesinde bağımlılık Analizi etkinleştiriliyor.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    Aşağıdaki örnekte, FabrikamDemo_VMs_Disable.csv giriş dosyasındaki sunucu listesinde bağımlılık Analizi devre dışı bırakılıyor.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Power BI 'de ağ bağlantılarını görselleştirin

Azure geçişi, aynı anda birçok sunucunun ağ bağlantılarını görselleştirmek ve işlem ve sunucuya göre filtrelemek için kullanabileceğiniz bir Power BI şablonu sunar. Görselleştirmek için, aşağıdaki yönergelere göre Power BI bağımlılık verileriyle yükleyin.

1. GitHub 'daki [Azure PowerShell örnekleri](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) deposundan PowerShell modülünü ve Power BI şablonunu indirin.

2. Aşağıdaki yönergeleri kullanarak Azure 'da oturum açın: 
    - Connect-AzAccount cmdlet 'ini kullanarak Azure aboneliğinizde oturum açın.

        ```PowerShell
        Connect-AzAccount
        ```

    - Azure Kamu kullanıyorsanız, aşağıdaki komutu kullanın.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Projeyi oluşturduğunuz aboneliği seçin

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. İndirilen AzMig_Dependencies PowerShell modülünü içeri aktarma

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Aşağıdaki komutu çalıştırın. Bu komut, bir CSV 'deki bağımlılıklar verilerini indirir ve Power BI görselleştirme için kullanılabilecek benzersiz bağımlılıkların bir listesini oluşturmak üzere işler. Aşağıdaki örnekte, proje adı FabrikamDemoProject ve ait olduğu kaynak grubu FabrikamDemoRG. Bağımlılıklar, FabrikamAppliance tarafından bulunan sunucular için indirilir. Benzersiz bağımlılıklar FabrikamDemo_Dependencies.csv kaydedilir

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. İndirilen Power BI şablonunu açın

6. Power BI indirilen bağımlılık verilerini yükleyin.
    - Şablonu Power BI açın.
    - Araç çubuğundan **veri al** ' a tıklayın. 
    - Ortak veri kaynaklarından **metin/CSV** ' yi seçin.
    - İndirilen bağımlılıklar dosyasını seçin.
    - **Yükle**' ye tıklayın.
    - CSV dosyasının adıyla bir tablo alındığını görürsünüz. Tabloyu sağ taraftaki alanlar çubuğunda görebilirsiniz. AzMig_Dependencies olarak yeniden adlandırın
    - Araç çubuğundan Yenile ' ye tıklayın.

    Ağ bağlantıları grafiği ve kaynak sunucu adı, hedef sunucu adı, kaynak işlem adı, hedef işlem adı Dilimleyicileri içeri aktarılan verilerle birlikte çalışır olmalıdır.

7. Sunucu ve işlemlere göre filtreleme ağ bağlantılarının haritasını görselleştirin. Dosyanızı kaydedin.

## <a name="next-steps"></a>Sonraki adımlar

Değerlendirme için [sunucuları gruplandırın](how-to-create-a-group.md) .