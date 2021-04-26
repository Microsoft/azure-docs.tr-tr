---
title: Azure Otomasyonu Güncelleştirme Yönetimi sorunlarını giderme
description: Bu makalede, Azure Otomasyonu Güncelleştirme Yönetimi sorunları nasıl giderebileceğiniz ve giderebileceğiniz açıklanır.
services: automation
ms.subservice: update-management
ms.date: 04/16/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 36bfd2185cb7a192ce0113ee0722395c8a4ee928
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830313"
---
# <a name="troubleshoot-update-management-issues"></a>Güncelleştirme Yönetimi sorunlarını giderme

Bu makalede, makinelerinizde Güncelleştirme Yönetimi özelliğini dağıttığınızda çalıştırabileceğiniz sorunlar ele alınmaktadır. Temeldeki sorunu belirlemede karma Runbook Worker Aracısı için bir aracı sorun giderici vardır. Sorun giderici hakkında daha fazla bilgi edinmek için bkz. [Windows Update Aracısı sorunlarını giderme](update-agent-issues.md) ve [Linux Güncelleştirme Aracısı sorunlarını giderme](update-agent-issues-linux.md). Diğer özellik dağıtım sorunları için bkz. [özellik dağıtım sorunlarını giderme](onboarding.md).

>[!NOTE]
>Bir Windows makinesinde Güncelleştirme Yönetimi dağıttığınızda sorunlarla karşılaşırsanız, Windows Olay Görüntüleyicisi açın ve yerel makinedeki **uygulama ve hizmet günlükleri** altında **Operations Manager** olay günlüğünü kontrol edin. Olay KIMLIĞI 4502 ve olay ayrıntıları içeren olayları arayın `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` .

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Senaryo: bekleyen ve yüklü olarak gösterilen Linux güncelleştirmeleri

### <a name="issue"></a>Sorun

Linux makineniz için Güncelleştirme Yönetimi sınıflandırma **güvenliği** ve **diğerleri** altında bulunan belirli güncelleştirmeleri gösterir. Ancak makinede bir güncelleştirme zamanlaması çalıştırıldığında, örneğin yalnızca **güvenlik** sınıflandırmasıyla eşleşen güncelleştirmeleri yüklemek için, yüklenen güncelleştirmeler veya daha önce sınıflandırmayla eşleşen güncelleştirmelerin bir alt kümesinden farklıdır.

### <a name="cause"></a>Nedeni

Linux makineniz için bekleyen bir işletim sistemi güncelleştirmeleri değerlendirmesi tamamlandığında, Linux 'Taki satıcı tarafından sunulan [açık güvenlik açığı ve değerlendirme dili](https://oval.mitre.org/) (oval) dosyaları sınıflandırma için güncelleştirme yönetimi tarafından kullanılır. Sınıflandırma, güvenlik sorunları veya güvenlik açıklarını gideren OVAL dosyalarına bağlı olarak, Linux güncelleştirmeleri için **güvenlik** veya **diğerleri** olarak yapılır. Ancak güncelleştirme zamanlaması çalıştırıldığında, bu, yüklemek için tum, APT veya ZYPPER gibi uygun paket yöneticisini kullanarak Linux makinesinde yürütülür. Linux 'un Paket Yöneticisi, güncelleştirmeleri sınıflandırmak için farklı bir mekanizmaya sahip olabilir. burada sonuçlar, Güncelleştirme Yönetimi, OVAL dosyalarından alındıklarından farklı olabilir.

### <a name="resolution"></a>Çözüm

Linux makinesini, ilgili güncelleştirmeleri ve sınıflandırıldıkları Paket Yöneticisi başına sınıflandırmasını el ile denetleyebilirsiniz. Paket yöneticiniz tarafından hangi güncelleştirmelerin **güvenlik** olarak sınıflandırıldığını anlamak için aşağıdaki komutları çalıştırın.

YıUM için aşağıdaki komut Red Hat tarafından **güvenlik** olarak kategorilere ayrılmamış bir güncelleştirme listesi döndürür. CentOS durumunda her zaman boş bir liste döndürdüğünü ve güvenlik sınıflandırması gerçekleşmediğini unutmayın.

```bash
sudo yum -q --security check-update
```

ZYPPER için aşağıdaki komut, SUSE tarafından **güvenlik** olarak sınıflandırılan, sıfır olmayan bir güncelleştirme listesini döndürür.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

APT için aşağıdaki komut, Ubuntu Linux distros için kurallı olarak **güvenlik** halinde kategorilere ayrılmamış bir güncelleştirme listesi döndürür.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

Bu listeden, `grep ^Inst` bekleyen tüm güvenlik güncelleştirmelerini almak için komutunu çalıştırın.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Senaryo: "güncelleştirme çözümü etkinleştirilemedi" hatasını alıyorsunuz

### <a name="issue"></a>Sorun

Otomasyon hesabınızda Güncelleştirme Yönetimi etkinleştirmeye çalıştığınızda şu hatayı alırsınız:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Nedeni

Bu hata, aşağıdaki nedenlerden dolayı oluşabilir:

* Log Analytics aracısına yönelik ağ güvenlik duvarı gereksinimleri doğru şekilde yapılandırılmamış olabilir. Bu durum, DNS URL 'Leri çözümlenirken aracının başarısız olmasına neden olabilir.

* Güncelleştirme Yönetimi hedefleme yanlış yapılandırılmış ve makine, güncelleştirmeleri beklendiği gibi almıyor.

* Ayrıca, makinenin uyumluluk altında durumunun olduğunu da fark edebilirsiniz `Non-compliant` .  Aynı zamanda, **aracı masaüstü Analizi** aracıyı olarak bildirir `Disconnected` .

### <a name="resolution"></a>Çözüm

* İşletim sistemine bağlı olarak [Windows](update-agent-issues.md#troubleshoot-offline) veya [Linux](update-agent-issues-linux.md#troubleshoot-offline)için sorun gidericiyi çalıştırın.

* Güncelleştirme Yönetimi çalışması için hangi adreslere ve bağlantı noktalarına izin verileceğini öğrenmek için [ağ yapılandırması](../automation-hybrid-runbook-worker.md#network-planning) ' na gidin.  

* Kapsam yapılandırma sorunlarını denetleyin. [Kapsam yapılandırması](../update-management/scope-configuration.md) , hangi makinelerin güncelleştirme yönetimi yapılandırıldığını belirler. Makinenizin çalışma alanınızda gösterilmesi, ancak Güncelleştirme Yönetimi istemiyorsanız, kapsam yapılandırmasını makineleri hedefleyecek şekilde ayarlamanız gerekir. Kapsam yapılandırması hakkında bilgi edinmek için bkz. [çalışma alanındaki makineleri etkinleştirme](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

* [Karma runbook çalışanını şirket içi bir Windows bilgisayarından kaldırma](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) bölümündeki adımları Izleyerek veya [karma Runbook Worker 'ı şirket içi bir Linux bilgisayarından kaldırarak](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)çalışan yapılandırmasını kaldırın.

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Senaryo: yenisiyle değiştirilen güncelleştirme Güncelleştirme Yönetimi eksik olarak belirtildi

### <a name="issue"></a>Sorun

Eski güncelleştirmeler, yenisiyle değiştirilseler bile bir Otomasyon hesabı için eksik olarak görünür. Aynı güvenlik açığını düzelten sonraki bir güncelleştirme mevcut olduğundan, yenisiyle değiştirilen bir güncelleştirme, yüklemenize gerek kalmaz. Güncelleştirme Yönetimi, yenisiyle değiştirilen güncelleştirmeyi yoksayar ve yerine geçen güncelleştirmenin yerine uygulanabilir değildir. İlgili bir sorun hakkında daha fazla bilgi için bkz. [Update 'in yerini almıştır](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Nedeni

Yenisiyle değiştirilen güncelleştirmeler, geçerli kabul edilebilmesi için Windows Server Update Services 'de (WSUS) reddedilmez.

### <a name="resolution"></a>Çözüm

Yenisiyle değiştirilen bir güncelleştirme %100 ' a uygulanamıyorsa, bu güncelleştirmenin onay durumunu `Declined` WSUS olarak değiştirmelisiniz. Tüm güncelleştirmelerinizin onay durumunu değiştirmek için:

1. Otomasyon hesabında, makine durumunu görüntülemek için **güncelleştirme yönetimi** ' yi seçin. Bkz. [güncelleştirme değerlendirmelerini görüntüleme](../update-management/view-update-assessments.md).

2. %100 olduğundan emin olmak için yenisiyle değiştirilen güncelleştirmeyi denetleyin.

3. Makinelerin rapor aldığı WSUS sunucusunda, [güncelleştirmeyi reddedin](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates).

4. **Bilgisayarlar** ' ı seçin ve **Uyumluluk** sütununda uyumluluk için yeniden taramaya zorlayın. Bkz. [VM güncelleştirmelerini yönetme](../update-management/manage-updates-for-vm.md).

5. Yenisiyle değiştirilen diğer güncelleştirmeler için yukarıdaki adımları tekrarlayın.

6. Windows Server Update Services (WSUS) için, WSUS [sunucusu Temizleme Sihirbazı 'nı](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)kullanarak altyapıyı yenilemek üzere yenisiyle değiştirilen tüm güncelleştirmeleri temizleyin.

7. Görüntüleme sorununu düzeltmek ve güncelleştirme yönetimi için kullanılan disk alanı miktarını en aza indirmek için bu yordamı düzenli olarak tekrarlayın.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Senaryo: makineler altında portalda gösterilmez Güncelleştirme Yönetimi

### <a name="issue"></a>Sorun

Makineleriniz aşağıdaki belirtilerle sahiptir:

* Makineniz `Not configured` , BIR VM 'nin güncelleştirme yönetimi görünümünden gösterilir.

* Azure Otomasyonu hesabınızın Güncelleştirme Yönetimi görünümünde makineleriniz eksik.

* Uyumluluk altında olarak görünen makineleriniz `Not assessed` vardır . Ancak, karma Runbook Worker için Azure Izleyici günlüklerinde sinyal verilerini görürsünüz, ancak Güncelleştirme Yönetimi için değildir.

### <a name="cause"></a>Nedeni

Bu soruna yerel yapılandırma sorunları veya yanlış yapılandırılmış kapsam yapılandırması neden olabilir. Olası belirli nedenler şunlardır:

* Karma runbook çalışanını yeniden kaydetmeniz ve yeniden yüklemeniz gerekebilir.

* Çalışma alanınızda ulaşılan ve daha fazla veri depolamayı engelleyen bir kota tanımlamış olabilirsiniz.

### <a name="resolution"></a>Çözüm

1. İşletim sistemine bağlı olarak [Windows](update-agent-issues.md#troubleshoot-offline) veya [Linux](update-agent-issues-linux.md#troubleshoot-offline)için sorun gidericiyi çalıştırın.

2. Makinenizin doğru çalışma alanına bildirimde bulunduğundan emin olun. Bu yönü doğrulamaya ilişkin yönergeler için bkz. [Azure izleyici ile aracı bağlantısını doğrulama](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Ayrıca, bu çalışma alanının Azure Otomasyonu hesabınıza bağlı olduğundan emin olun. Onaylamak için Otomasyon hesabınıza gidin ve **Ilgili kaynaklar** altında **bağlantılı çalışma alanı** ' nı seçin.

3. Makinelerin Otomasyon hesabınıza bağlı Log Analytics çalışma alanında göründüğünden emin olun. Log Analytics çalışma alanında aşağıdaki sorguyu çalıştırın.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    Makinenizi sorgu sonuçlarında görmüyorsanız, son zamanlarda iade edilmedi demektir. Büyük olasılıkla yerel bir yapılandırma sorunu var ve [aracıyı yeniden yüklemeniz](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows)gerekir.

    Makineniz sorgu sonuçlarında listeleniyorsa, **güncelleştirmelerin** listelendiğini, **çözüm** özelliği altında doğrulayın. Bu, Güncelleştirme Yönetimi kaydedildiğini doğrular. Değilse, kapsam yapılandırma sorunlarını kontrol edin. [Kapsam yapılandırması](../update-management/scope-configuration.md) , hangi makinelerin güncelleştirme yönetimi yapılandırıldığını belirler. Makinenin hedefi için kapsam yapılandırmasını yapılandırmak üzere, bkz. çalışma alanındaki [makineleri etkinleştirme](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

4. Çalışma alanınızda bu sorguyu çalıştırın.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   Bir `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` sonuç alırsanız, çalışma alanınızda tanımlanan kotaya ulaşıldı ve bu da verilerin kaydedilmesini durdurdu. Çalışma alanınızda, **kullanım ve tahmini maliyetler** altında **veri hacmi yönetimi** ' ne gidin ve kotayı değiştirin veya kaldırın.

5. Sorununuz hala çözülmedi ise, Windows için karma çalışanı yeniden yüklemek üzere [Windows karma Runbook Worker dağıtma](../automation-windows-hrw-install.md) bölümündeki adımları izleyin. Linux için, [Linux karma runbook çalışanı dağıtma](../automation-linux-hrw-install.md)' daki adımları izleyin.

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Senaryo: abonelikler için Otomasyon kaynak sağlayıcısı kaydedilemiyor

### <a name="issue"></a>Sorun

Otomasyon hesabınızda Özellik dağıtımlarıyla çalışırken aşağıdaki hata oluşur:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Nedeni

Otomasyon kaynak sağlayıcısı abonelikte kayıtlı değil.

### <a name="resolution"></a>Çözüm

Otomasyon kaynak sağlayıcısını kaydetmek için Azure portal aşağıdaki adımları izleyin.

1. Portalın altındaki Azure hizmeti listesinde, **tüm hizmetler**' i seçin ve ardından Genel hizmet grubundaki **abonelikler** ' i seçin.

2. Aboneliğinizi seçin.

3. **Ayarlar** altında **kaynak sağlayıcıları**' nı seçin.

4. Kaynak sağlayıcıları listesinden, Microsoft. Automation kaynak sağlayıcısının kayıtlı olduğunu doğrulayın.

5. Listede yoksa, [kaynak sağlayıcısı kaydı hatalarını giderme](../../azure-resource-manager/templates/error-register-resource-provider.md)adımlarını izleyerek Microsoft. Automation sağlayıcısını kaydedin.

## <a name="scenario-scheduled-update-did-not-patch-some-machines"></a><a name="scheduled-update-missed-machines"></a>Senaryo: zamanlanan güncelleştirme bazı makinelere düzeltme eki sağlamadı

### <a name="issue"></a>Sorun

Güncelleştirme önizlemesindeki makineler, zamanlanmış bir çalışma sırasında düzeltme eki eklenen makineler listesinde görünmez veya dinamik bir grubun seçili kapsamlarının VM 'Leri, portalda güncelleştirme önizlemesi listesinde gösterilmez.

Güncelleştirme önizlemesi listesi, seçilen kapsamlar için bir [Azure Kaynak Grafiği](../../governance/resource-graph/overview.md) sorgusunun aldığı tüm makinelerden oluşur. Kapsamlar, bir sistem karma Runbook Worker 'ın yüklü olduğu ve erişim izinlerine sahip olduğu makineler için filtrelenir.

### <a name="cause"></a>Nedeni

Bu sorunun nedeni aşağıdakilerden biri olabilir:

* Dinamik bir sorgudaki kapsamda tanımlanan abonelikler, kayıtlı Otomasyon kaynak sağlayıcısı için yapılandırılmamış.

* Zamanlama yürütüldüğünde makineler kullanılabilir değil veya uygun etiketlere sahip değil.

* Seçili kapsamlar üzerinde doğru erişiminiz yok.

* Azure Kaynak Grafiği sorgusu beklenen makineleri almaz.

* Sistem karma Runbook Worker makinelerde yüklü değil.

### <a name="resolution"></a>Çözüm

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Kayıtlı Otomasyon kaynak sağlayıcısı için abonelikler yapılandırılmadı

Aboneliğiniz Otomasyon kaynak sağlayıcısı için yapılandırılmamışsa, Bu abonelikteki makinelere ilişkin bilgileri sorgulayabilir veya alamazsınız. Abonelik kaydını doğrulamak için aşağıdaki adımları kullanın.

1. [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)Azure hizmet listesine erişin.

2. **Tüm hizmetler**' i seçin ve ardından Genel hizmet grubundaki **abonelikler** ' i seçin.

3. Dağıtımınızın kapsamında tanımlanan aboneliği bulun.

4. **Ayarlar** altında **kaynak sağlayıcıları**' nı seçin.

5. Microsoft. Automation kaynak sağlayıcısının kayıtlı olduğunu doğrulayın.

6. Listede yoksa, [kaynak sağlayıcısı kaydı hatalarını giderme](../../azure-resource-manager/templates/error-register-resource-provider.md)adımlarını izleyerek Microsoft. Automation sağlayıcısını kaydedin.

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Zamanlama yürütüldüğünde makineler kullanılamıyor veya doğru şekilde etiketlenmedi

Aboneliğiniz Otomasyon kaynak sağlayıcısı için yapılandırılmışsa, ancak belirli [dinamik gruplarla](../update-management/configure-groups.md) güncelleştirme zamanlaması çalıştırıldığında bazı makineler kaçırıldığında aşağıdaki yordamı kullanın.

1. Azure portal Otomasyon hesabını açın ve **güncelleştirme yönetimi**' ı seçin.

2. Güncelleştirme dağıtımının çalıştırıldığı zamanı tam olarak öğrenmek için [güncelleştirme yönetimi geçmişi](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) kontrol edin.

3. Güncelleştirme Yönetimi tarafından kaçırıldığınıza şüphelendiğiniz makinelerde, [makine değişikliklerini bulmak](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)Için Azure Kaynak Grafiği 'NI (arg) kullanın.

4. Güncelleştirme dağıtımının çalıştırılmasından önce, bir gün gibi önemli bir süre içinde değişiklikler arayın.

5. Değişiklik silme veya güncelleştirme gibi tüm systemik değişikliklerinin bu dönemdeki makinelere yönelik arama sonuçlarını denetleyin. Bu değişiklikler makine durumunu veya etiketlerini değiştirebilir, böylece Güncelleştirmeler dağıtıldığında makinelerin makine listesinde seçilmemesi sağlanır.

6. Makine durumu veya etiket sorunlarını düzeltmek için gereken makineleri ve kaynak ayarlarını ayarlayın.

7. Belirtilen dinamik gruplara sahip dağıtımın tüm makineleri içerdiğinden emin olmak için güncelleştirme zamanlamasını yeniden çalıştırın.

#### <a name="incorrect-access-on-selected-scopes"></a>Seçili kapsamlar üzerinde yanlış erişim

Azure portal yalnızca belirli bir kapsamda yazma erişiminizin bulunduğu makineleri görüntüler. Bir kapsam için doğru erişiminiz yoksa, bkz. [öğretici: Azure Portal kullanarak Kullanıcı erişimini Azure kaynaklarına verme](../../role-based-access-control/quickstart-assign-role-user-portal.md).

#### <a name="resource-graph-query-doesnt-return-expected-machines"></a>Kaynak Grafiği sorgusu beklenen makineleri döndürmüyor

Sorgularınızın doğru şekilde çalışıp çalışmadığını öğrenmek için aşağıdaki adımları izleyin.

1. Azure portal ' deki kaynak grafik Gezgini dikey penceresinde aşağıda gösterildiği gibi biçimlendirilen bir Azure Kaynak Grafiği sorgusu çalıştırın. Azure Kaynak Graph ' i kullanmaya yeni çalışıyorsanız, kaynak grafik Gezgini ile nasıl çalışacağınızı öğrenmek için bu [hızlı](../../governance/resource-graph/first-query-portal.md) başlangıca bakın. Bu sorgu, Güncelleştirme Yönetimi içinde dinamik grubu oluştururken seçtiğiniz filtreleri taklit eder. Bkz. [güncelleştirme yönetimi dinamik grupları kullanma](../update-management/configure-groups.md).

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Aşağıda bir örnek verilmiştir:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. Aradığınız makinelerin sorgu sonuçlarında listelenip listelenmediğini denetleyin.

3. Makineler listede yoksa, dinamik grupta Seçili filtre ile ilgili bir sorun olabilir. Grup yapılandırmasını gereken şekilde ayarlayın.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Karma Runbook Worker makinelerde yüklü değil

Makineler Azure Kaynak Grafiği sorgu sonuçlarında görünür, ancak yine de dinamik grup önizlemede gösterilmez. Bu durumda, makineler sistem karma runbook çalışanları olarak atanmayabilir ve bu nedenle Azure Otomasyonu 'Nu ve Güncelleştirme Yönetimi işleri çalıştıramayabilir. Görmeyi beklediğiniz makinelerin sistem karma runbook çalışanları olarak ayarlandığından emin olmak için:

1. Azure portal, doğru şekilde görünmeyen bir makine için Otomasyon hesabına gidin.

2. **Işlem Otomasyonu** altında **karma çalışan grupları** ' nı seçin.

3. **Sistem karma çalışanı grupları** sekmesini seçin.

4. Karma çalışanın bu makine için mevcut olduğunu doğrulayın.

5. Makine bir sistem karma Runbook Worker olarak ayarlanmamışsa, Güncelleştirme Yönetimi Genel Bakış makalesinin [güncelleştirme yönetimi etkinleştir](../update-management/overview.md#enable-update-management) bölümünde makineyi etkinleştirme yöntemlerini gözden geçirin. Etkinleştirilecek Yöntem, makinenin çalıştığı ortama bağlıdır.

6. Önizlemede görüntülemediğiniz tüm makineler için yukarıdaki adımları tekrarlayın.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Senaryo: Güncelleştirme Yönetimi bileşenleri etkin, ancak VM yapılandırılmakta olmaya devam ediyor

### <a name="issue"></a>Sorun

Dağıtım başladıktan sonra VM 15 dakika üzerinde aşağıdaki iletiyi görmeye devam edersiniz:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Nedeni

Bu hata, aşağıdaki nedenlerden dolayı oluşabilir:

* Otomasyon hesabıyla iletişim engelleniyor.

* Farklı kaynak bilgisayar kimliklerine sahip yinelenen bir bilgisayar adı vardır. Bu senaryo, belirli bir bilgisayar adına sahip bir VM farklı kaynak gruplarında oluşturulduğunda ve abonelikte aynı lojistik Aracısı çalışma alanına raporlarken oluşur.

* Dağıtılan VM görüntüsü, Windows için Log Analytics Aracısı ile sistem hazırlığı (Sysprep) ile hazırlanmamış bir kopyalanmış makineden gelebilir.

### <a name="resolution"></a>Çözüm

VM ile ilgili tam sorunu belirlemede yardımcı olması için, Otomasyon hesabınıza bağlı Log Analytics çalışma alanında aşağıdaki sorguyu çalıştırın.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Otomasyon hesabı ile iletişim engellendi

Güncelleştirme Yönetimi çalışması için hangi adreslere ve bağlantı noktalarına izin verileceğini öğrenmek için [ağ planlamasına](../update-management/overview.md#ports) gidin.

#### <a name="duplicate-computer-name"></a>Yinelenen bilgisayar adı

Ortamlarında benzersiz adlar sağlamak için sanal makinelerinizi yeniden adlandırın.

#### <a name="deployed-image-from-cloned-machine"></a>Kopyalanmış makineden dağıtılan görüntü

Kopyalanmış bir görüntü kullanıyorsanız, farklı bilgisayar adlarında aynı kaynak bilgisayar KIMLIĞI vardır. Bu durumda:

1. Log Analytics çalışma alanınızda, gösterilen kapsam yapılandırması için VM 'yi kayıtlı aramadan kaldırın `MicrosoftDefaultScopeConfig-Updates` . Kayıtlı aramalar, çalışma alanınızda **genel** altında bulunabilir.

2. Aşağıdaki cmdlet 'i çalıştırın.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. `Restart-Service HealthService`Sistem sağlığı hizmetini yeniden başlatmak için ' i çalıştırın. Bu işlem anahtarı yeniden oluşturur ve yeni bir UUID oluşturur.

4. Bu yaklaşım işe yaramazsa, önce görüntüde Sysprep 'i çalıştırın ve ardından Windows için Log Analytics aracısını yüklemeniz gerekir.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Senaryo: başka bir Azure kiracısındaki makineler için bir güncelleştirme dağıtımı oluştururken bağlantılı bir abonelik hatası alıyorsunuz

### <a name="issue"></a>Sorun

Başka bir Azure kiracısındaki makineler için bir güncelleştirme dağıtımı oluşturmaya çalıştığınızda aşağıdaki hatayla karşılaşırsınız:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Nedeni

Bu hata, bir güncelleştirme dağıtımında yer alan başka bir kiracıda Azure VM 'Leri olan bir güncelleştirme dağıtımı oluşturduğunuzda oluşur.

### <a name="resolution"></a>Çözüm

Zamanlanmış bu öğeleri almak için aşağıdaki geçici çözümü kullanın. Bir zamanlama oluşturmak için [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) cmdlet 'ini parametresiyle birlikte kullanabilirsiniz `ForUpdateConfiguration` . Ardından [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) cmdlet 'ini kullanın ve diğer Kiracıdaki makineleri `NonAzureComputer` parametresine geçirin. Aşağıdaki örnek bunun nasıl yapılacağını göstermektedir:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Senaryo: açıklanamayan yeniden başlatmalar

### <a name="issue"></a>Sorun

**Yeniden başlatma denetimi** seçeneğini **hiçbir şekilde yeniden başlatmaya** ayarlamış olsanız bile, güncelleştirmeler yüklendikten sonra makineler yeniden başlatılıyor.

### <a name="cause"></a>Nedeni

Windows Update, herhangi biri yeniden başlatma davranışını değiştirebilecek çeşitli kayıt defteri anahtarları tarafından değiştirilebilir.

### <a name="resolution"></a>Çözüm

Makinelerinizin düzgün yapılandırıldığından emin olmak için [yeniden başlatmayı yönetmek üzere kullanılan](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kayıt defteri ve kayıt defteri anahtarlarını [düzenleyerek otomatik güncelleştirmeleri yapılandırma](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) altında listelenen kayıt defteri anahtarlarını gözden geçirin.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Senaryo: bir güncelleştirme dağıtımında makine "başlatılamadı" olarak gösteriliyor

### <a name="issue"></a>Sorun

Bir makine bir `Failed to start` durumu gösterir. Makinenin belirli ayrıntılarını görüntülediğinizde, aşağıdaki hatayı görürsünüz:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Nedeni

Bu hatanın oluşmasının nedeni aşağıdakilerden biri olabilir:

* Makine artık yok.
* Makine kapalı ve ulaşılamaz durumda.
* Makine bir ağ bağlantısı sorununa sahiptir ve bu nedenle makinedeki karma çalışana ulaşılamıyor.
* Log Analytics aracısına, kaynak bilgisayar KIMLIĞINI değiştiren bir güncelleştirme vardı.
* Bir Otomasyon hesabında 200 eşzamanlı iş sınırına ulaşırsanız güncelleştirme çalışması kısıtlanıyor. Her dağıtım bir iş olarak değerlendirilir ve bir güncelleştirme dağıtımındaki her makine iş olarak sayılır. Otomasyon hesabınızda çalışmakta olan diğer herhangi bir Otomasyon işi veya güncelleştirme dağıtımı, eşzamanlı iş sınırına doğru sayılır.

### <a name="resolution"></a>Çözüm

Uygun olduğunda, güncelleştirme dağıtımlarınız için [dinamik grupları](../update-management/configure-groups.md) kullanın. Ayrıca, aşağıdaki adımları gerçekleştirebilirsiniz.

1. Makinenizin veya sunucunuzun [gereksinimleri](../update-management/overview.md#system-requirements)karşıladığını doğrulayın.
2. Karma Runbook Worker Aracısı sorun gidericisini kullanarak karma Runbook Worker bağlantısını doğrulayın. Sorun giderici hakkında daha fazla bilgi için bkz. [Güncelleştirme Aracısı sorunlarını giderme](update-agent-issues.md).

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Senaryo: güncelleştirmeler dağıtım olmadan yüklenir

### <a name="issue"></a>Sorun

Bir Windows makinesini Güncelleştirme Yönetimi kaydettiğinizde, bir dağıtım olmadan yüklenen güncelleştirmeleri görürsünüz.

### <a name="cause"></a>Nedeni

Windows 'da, güncelleştirmeler kullanılabilir duruma geldiğinde otomatik olarak yüklenir. Bu davranış, makineye dağıtılacak bir güncelleştirme zamanalmadıysanız karışıklığına neden olabilir.

### <a name="resolution"></a>Çözüm

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU`Kayıt defteri anahtarı varsayılan olarak 4: `auto download and install` .

Güncelleştirme Yönetimi istemciler için, bu anahtarı 3: olarak ayarlamayı öneririz `auto download but do not auto install` .

Daha fazla bilgi için bkz. [otomatik güncelleştirmeleri yapılandırma](/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Senaryo: makine farklı bir hesaba zaten kayıtlı

### <a name="issue"></a>Sorun

Aşağıdaki hata iletisini alırsınız:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Nedeni

Makine Güncelleştirme Yönetimi için zaten başka bir çalışma alanına dağıtıldı.

### <a name="resolution"></a>Çözüm

1. Makinenin doğru çalışma alanına rapor olduğundan emin olmak için, makineler bölümündeki adımları izleyin [güncelleştirme yönetimi altında portalda görünmez](#nologs) .
2. [Karma runbook grubunu silerek](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)makinedeki yapıtları temizleyin ve sonra yeniden deneyin.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Senaryo: makine hizmetle iletişim kuramıyor

### <a name="issue"></a>Sorun

Aşağıdaki hata iletilerinden birini alırsınız:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Nedeni

Bir proxy, ağ geçidi veya güvenlik duvarı ağ iletişimini engelliyor olabilir.

### <a name="resolution"></a>Çözüm

Ağınızı gözden geçirin ve uygun bağlantı noktalarına ve adreslere izin verildiğinden emin olun. Güncelleştirme Yönetimi ve karma runbook çalışanları için gereken bağlantı noktalarının ve adreslerin listesi için bkz. [ağ gereksinimleri](../automation-hybrid-runbook-worker.md#network-planning) .

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Senaryo: otomatik olarak imzalanan sertifika oluşturulamıyor

### <a name="issue"></a>Sorun

Aşağıdaki hata iletilerinden birini alırsınız:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Nedeni

Karma Runbook Worker otomatik olarak imzalanan bir sertifika oluşturamadı.

### <a name="resolution"></a>Çözüm

Sistem hesabının **C:\programdata\microsoft\crypto\rsa** klasörüne okuma erişimi olduğunu doğrulayın ve yeniden deneyin.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Senaryo: zamanlanan güncelleştirme bir MaintenanceWindowExceeded hatasıyla başarısız oldu

### <a name="issue"></a>Sorun

Güncelleştirmeler için varsayılan bakım penceresi 120 dakikadır. Bakım penceresini en fazla 6 saat veya 360 dakika olarak artırabilirsiniz.

### <a name="resolution"></a>Çözüm

Başarılı bir şekilde başladıktan sonra bir güncelleştirme çalıştırması sırasında neden oluştuğunu anlamak için, çalıştırmada etkilenen makinenin [iş çıktısını kontrol edin](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) . Çözebileceğinizi ve üzerinde işlem yapmanız gereken makinelerinizden belirli hata iletileri bulabilirsiniz.  

Başarısız olan zamanlanmış güncelleştirme dağıtımlarını düzenleyin ve bakım penceresini arttırın.

Bakım pencereleri hakkında daha fazla bilgi için bkz. [Install Updates](../update-management/deploy-updates.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Senaryo: makine "değerlendirilmedi" olarak gösterilir ve bir HRESULT özel durumu gösterir

### <a name="issue"></a>Sorun

* Uyumluluk altında olarak görünen makineleriniz `Not assessed` vardır ve bunların altında bir özel durum iletisi görürsünüz.
* Portalda bir HRESULT hata kodu görürsünüz.

### <a name="cause"></a>Nedeni

Güncelleştirme Aracısı (Windows üzerindeki Windows Update Aracısı; bir Linux dağıtımı için Paket Yöneticisi) doğru yapılandırılmamış. Güncelleştirme Yönetimi, gereken güncelleştirmeleri, düzeltme ekinin durumunu ve dağıtılan düzeltme eklerinin sonuçlarını sağlamak için makinenin güncelleştirme aracısına bağımlıdır. Bu bilgiler olmadan, Güncelleştirme Yönetimi gereken veya yüklenen düzeltme eklerine düzgün şekilde bildiremiyor.

### <a name="resolution"></a>Çözüm

Güncelleştirmeleri makinede yerel olarak gerçekleştirmeyi deneyin. Bu işlem başarısız olursa, genellikle bir güncelleştirme Aracısı yapılandırma hatası olduğu anlamına gelir.

Bu sorun genellikle ağ yapılandırması ve güvenlik duvarı sorunlarından kaynaklanır. Sorunu gidermek için aşağıdaki denetimleri kullanın.

* Linux için, paket deponuzdaki ağ uç noktasına ulaşabildiğinizden emin olmak için uygun belgeleri denetleyin.

* Windows için, [güncelleştirmeler intranet uç noktasından (WSUS/SCCM) indirmeyen güncelleştirmeler](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)bölümünde listelenen aracı yapılandırmanızı denetleyin.

  * Makineler Windows Update için yapılandırılmışsa, [http/proxy ile Ilgili sorunlar](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)bölümünde açıklanan uç noktalara ulaşabildiğinizden emin olun.
  * Makineler Windows Server Update Services (WSUS) için yapılandırılmışsa, [WUServer kayıt defteri anahtarı](/windows/deployment/update/waas-wu-settings)tarafından yapılandırılan WSUS sunucusuna erişebildiğinizden emin olun.

Bir HRESULT görürseniz, tüm özel durum iletisini görmek için kırmızı renkte görüntülenecek özel duruma çift tıklayın. Olası çözümler veya önerilen eylemler için aşağıdaki tabloyu gözden geçirin.

|Özel durum  |Çözüm veya eylem  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Özel durumun nedeni hakkında ek ayrıntılar bulmak için [Windows Update hata kodu listesinde](https://support.microsoft.com/help/938205/windows-update-error-code-list) ilgili hata kodunu arayın.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Bunlar ağ bağlantısı sorunlarını gösterir. Makinenizin Güncelleştirme Yönetimi için ağ bağlantısı olduğundan emin olun. Gerekli bağlantı noktalarının ve adreslerin listesi için [ağ planlama](../update-management/overview.md#ports) bölümüne bakın.        |
|`0x8024001E`| Hizmet veya sistem kapatıldığından güncelleştirme işlemi tamamlanmadı.|
|`0x8024002E`| Windows Update hizmeti devre dışı bırakıldı.|
|`0x8024402C`     | Bir WSUS sunucusu kullanıyorsanız, kayıt defteri anahtarı için kayıt defteri değerlerinin `WUServer` `WUStatusServer`  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` doğru WSUS sunucusunu belirtdiğinizden emin olun.        |
|`0x80072EE2`|Bir ağ bağlantısı sorunu veya yapılandırılmış bir WSUS sunucusuyla iletişim kurulurken bir sorun var. WSUS ayarlarını denetleyin ve hizmetin istemciden erişilebilir olduğundan emin olun.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Windows Update hizmeti 'nin (wuauserv) çalıştığından ve devre dışı bırakılmadığından emin olun.        |
|`0x80070005`| Bir erişim reddedildi hatası, aşağıdakilerden biri nedeniyle oluşabilir:<br> Virüslü bilgisayar<br> Windows Update ayarları doğru yapılandırılmadı<br> %WinDir%\SoftwareDistribution klasörüyle dosya izni hatası<br> Sistem sürücüsünde yetersiz disk alanı (C:).
|Diğer tüm genel durumlar     | Olası çözümler için internet 'te bir arama çalıştırın ve yerel BT desteğiniz ile çalışın.         |

**%Windir%\WindowsUpdate.log** dosyasını gözden geçirmek olası nedenleri belirlemenize de yardımcı olabilir. Günlüğü okuma hakkında daha fazla bilgi için bkz. [windowsupdate. log dosyasını okuma](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Ayrıca, makinede Windows Update sorunları denetlemek için [Windows Update sorun gidericiyi](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indirebilir ve çalıştırabilirsiniz.

> [!NOTE]
> [Windows Update sorun giderici](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) belgeleri Windows istemcilerinde kullanılması gerektiğini gösterir, ancak Windows Server 'da da kullanılabilir.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Senaryo: güncelleştirme çalıştırması geri dönüş başarısız durumu (Linux)

### <a name="issue"></a>Sorun

Bir güncelleştirme çalıştırması başlatılır, ancak çalıştırma sırasında hatalarla karşılaşır.

### <a name="cause"></a>Nedeni

Olası nedenler:

* Paket Yöneticisi sağlam değil.
* Güncelleştirme Aracısı (Windows için WUA, Linux için özel paket yöneticisi) yanlış yapılandırılmış.
* Belirli paketler, bulut tabanlı düzeltme eki uygulama ile müdahale ediyor.
* Makineye ulaşılamıyor.
* Güncelleştirmeler çözümlenmeyen bağımlılıklara sahipti.

### <a name="resolution"></a>Çözüm

Bir güncelleştirme çalıştırması sırasında başarılı olduktan sonra bir başarısızlık oluşursa, çalıştırmada etkilenen makinenin [iş çıktısını kontrol](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) edin. Çözebileceğinizi ve üzerinde işlem yapmanız gereken makinelerinizden belirli hata iletileri bulabilirsiniz. Güncelleştirme Yönetimi, paket yöneticisinin başarılı güncelleştirme dağıtımları için sağlıklı olmasını gerektirir.

İş başarısız olmadan hemen önce belirli düzeltme ekleri, paketler veya güncelleştirmeler görülemezse, bu öğelerin bir sonraki güncelleştirme dağıtımından [dışlanmasını](../update-management/deploy-updates.md#schedule-an-update-deployment) deneyebilirsiniz. Windows Update günlük bilgilerini toplamak için bkz. [Windows Update günlük dosyaları](/windows/deployment/update/windows-update-logs).

Düzeltme eki uygulama sorununu çözemezseniz, **/var/opt/Microsoft/omsagent/Run/automationworker/omsupdatemgmt.log** dosyasının bir kopyasını oluşturun ve bir sonraki güncelleştirme dağıtımı başlamadan önce sorun giderme amacıyla koruyun.

## <a name="patches-arent-installed"></a>Düzeltme ekleri yüklenmedi

### <a name="machines-dont-install-updates"></a>Makineler güncelleştirmeleri yüklemiyor

Güncelleştirmeleri doğrudan makinede çalıştırmayı deneyin. Makine güncelleştirmeleri uygulayamıyorum, [sorun giderme kılavuzunda olası hataların listesine](#hresult)bakın.

Güncelleştirmeler yerel olarak çalışıyorsa, [GÜNCELLEŞTIRME YÖNETIMI VM 'Yi kaldırma](../update-management/remove-vms.md)konusunda yer alan kılavuzu izleyerek aracıyı kaldırıp yeniden yüklemeyi deneyin.

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Güncelleştirmelerin kullanılabildiğini biliyorum, ancak makinelerimde kullanılabilir olarak gösterilmiyorum

Bu durum genellikle makineler WSUS veya Microsoft uç nokta Configuration Manager güncelleştirmeleri almak üzere yapılandırılmışsa, ancak WSUS ve Configuration Manager güncelleştirmeleri onaylanmamış olur.

`UseWUServer` [Bu makalenin kayıt defterini düzenleyerek otomatik güncelleştirmeleri yapılandırma bölümünde](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)kayıt defteri ANAHTARLARıNA, MAKINELERIN WSUS ve SCCM için yapılandırılıp yapılandırılmadığını kontrol edebilirsiniz.

Güncelleştirmeler WSUS 'de onaylanmamışsa, bunlar yüklenmez. Aşağıdaki sorguyu çalıştırarak Log Analytics onaylanmamış güncelleştirmeleri kontrol edebilirsiniz.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Güncelleştirmeler yüklenmiş görünüyor ancak makinemde bulamıyorum

Güncelleştirmeler genellikle başka güncelleştirmelerle değiştirilir. Daha fazla bilgi için, Windows Update sorun giderme kılavuzunda [güncelleştirme 'nin yerini almıştır](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) .

### <a name="installing-updates-by-classification-on-linux"></a>Linux'da güncelleştirmeleri sınıflandırmaya göre yükleme

Güncelleştirmeleri Linux'a sınıflandırmaya göre ("Kritik ve güvenlik güncelleştirmeleri") yüklemek, özellikle CentOS için önemli uyarılara tabidir. Bu sınırlamalar [güncelleştirme yönetimi genel bakış sayfasında](../update-management/overview.md#linux)belgelenmiştir.

### <a name="kb2267602-is-consistently-missing"></a>KB2267602, sürekli olarak eksik

KB2267602, [Windows Defender tanım güncelleştirmesidir](https://www.microsoft.com/wdsi/definitions). Günlük olarak güncelleştirilir.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu görmüyorsanız veya sorununuzu çözemezseniz, ek destek için aşağıdaki kanallardan birini deneyin.

* Azure [forumları](https://azure.microsoft.com/support/forums/)aracılığıyla Azure uzmanlarından yanıtlar alın.
* [@AzureSupport](https://twitter.com/azuresupport)Müşteri deneyimini iyileştirmek için resmi Microsoft Azure hesabıyla bağlanın.
* Azure destek olayı dosyası oluşturma. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.
