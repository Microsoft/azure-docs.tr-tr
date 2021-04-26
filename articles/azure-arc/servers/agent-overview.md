---
title: Bağlı makine aracısına genel bakış
description: Bu makalede, karma ortamlarda barındırılan sanal makinelerin izlenmesini destekleyen Azure Arc etkin sunucu aracısına sunulan ayrıntılı bir genel bakış sunulmaktadır.
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd58997a8af86a3789895bfb29bfd5803826fa6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832968"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Azure Arc etkin sunucular aracısına genel bakış

Azure Arc etkin sunucular bağlı makine Aracısı, kurumsal ağınızda veya diğer bulut sağlayıcıınızda Azure dışında barındırılan Windows ve Linux makinelerinizi yönetmenizi sağlar. Bu makalede Aracı, sistem ve ağ gereksinimleri ve farklı dağıtım yöntemlerine ilişkin ayrıntılı bir genel bakış sunulmaktadır.

>[!NOTE]
>Eylül 2020 ' de Azure Arc etkin sunucularının genel sürümünden itibaren, Azure bağlı makine aracısının tüm yayın öncesi sürümleri (1,0 ' den küçük olan aracılar) **2 şubat 2021** tarihinde **kullanımdan kaldırılmıştır** .  Bu zaman dilimi, önceden yayınlanan aracıların Azure Arc etkin sunucular hizmeti ile iletişim kurabilmesi için sürüm 1,0 veya sonraki bir sürüme yükseltmenizi sağlar.

## <a name="agent-component-details"></a>Aracı bileşeni ayrıntıları

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Yay etkin sunucular aracısına genel bakış." border="false":::

Azure bağlı makine Aracısı paketi, birlikte paketlenmiş çeşitli mantıksal bileşenler içerir.

* Karma örnek meta veri hizmeti (HıMDS), Azure ve bağlı makinenin Azure kimliğiyle bağlantıyı yönetir.

* Konuk yapılandırma Aracısı, makinenin gerekli ilkelerle uyumlu olup olmadığını değerlendirmek gibi In-Guest Ilke ve konuk yapılandırma işlevlerini sağlar.

    Bağlantısı kesilen bir makine için Azure Ilkesi [Konuk yapılandırması](../../governance/policy/concepts/guest-configuration.md) ile ilgili aşağıdaki davranışa göz önünde edin:

    * Bağlantısı kesilen makineleri hedefleyen bir konuk yapılandırma ilkesi ataması bundan etkilenmez.
    * Konuk atama, 14 gün boyunca yerel olarak depolanır. 14 günlük süre içinde, bağlı makine Aracısı hizmete yeniden bağlanırsa, ilke atamaları yeniden uygulanır.
    * Atamalar 14 gün sonra silinir ve 14 günlük dönemden sonra makineye yeniden atanmaz.

* Uzantı Aracısı, yükleme, kaldırma ve yükseltme dahil olmak üzere VM uzantılarını yönetir. Uzantılar Azure 'dan indirilir ve `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` Windows üzerindeki klasöre kopyalanır ve Linux için `/opt/GC_Ext/downloads` . Windows 'da, uzantı aşağıdaki yola yüklenir `%SystemDrive%\Packages\Plugins\<extension>` ve Linux üzerinde uzantı yüklenir `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Örnek meta verileri

Bağlı makine ile ilgili meta veri bilgileri, bağlı makine Aracısı yay etkin sunucularla kaydedildikten sonra toplanır. Özellikle:

* İşletim sistemi adı, türü ve sürümü
* Bilgisayar adı
* Bilgisayar tam etki alanı adı (FQDN)
* Bağlı makine Aracısı sürümü
* Active Directory ve DNS tam etki alanı adı (FQDN)
* UUıD (BıOS KIMLIĞI)
* Bağlı makine Aracısı sinyali
* Bağlı makine Aracısı sürümü
* Yönetilen kimlik için ortak anahtar
* İlke uyumluluk durumu ve ayrıntıları (Azure Ilke Konuk yapılandırma ilkelerini kullanıyorsanız)

Aşağıdaki meta veri bilgileri, Azure 'dan aracı tarafından istendi:

* Kaynak konumu (bölge)
* Sanal makine KIMLIĞI
* Etiketler
* Azure Active Directory yönetilen kimlik sertifikası
* Konuk yapılandırma ilkesi atamaları
* Uzantı istekleri-Install, Update ve delete.

## <a name="download-agents"></a>Aracıları indir

Windows ve Linux için Azure bağlı makine Aracısı paketini aşağıda listelenen konumlardan indirebilirsiniz.

* Microsoft Indirme merkezi 'nden [Windows agent Windows Installer paketi](https://aka.ms/AzureConnectedMachineAgent) .

* Linux aracı paketi, dağıtım için tercih edilen paket biçimi kullanılarak Microsoft 'un [paket deposundan](https://packages.microsoft.com/) dağıtılır (. RPM veya. DEB).

Windows ve Linux için Azure bağlı makine Aracısı, gereksinimlerinize bağlı olarak, en son sürüme el ile veya otomatik olarak yükseltilebilir. Daha fazla bilgi için [buraya](manage-agent.md)bakın.

## <a name="prerequisites"></a>Önkoşullar

### <a name="supported-environments"></a>Desteklenen ortamlar

Yay özellikli sunucular, bağlı makine aracısının Azure *dışında* barındırılan herhangi bir fiziksel sunucuya ve sanal makineye yüklenmesini destekler. Buna VMware, Azure Stack HI ve diğer bulut ortamları gibi platformlarda çalışan sanal makineler dahildir. Yay özellikli sunucular, Azure 'da çalışan sanal makinelere veya Azure Stack hub 'ında veya Azure Stack kenarda çalışan sanal makinelere, Azure VM 'Leri zaten modellendiği için yüklemeyi desteklemez.

### <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Windows ve Linux işletim sisteminin aşağıdaki sürümleri resmi olarak Azure bağlı makine Aracısı için desteklenir:

- Windows Server 2008 R2, Windows Server 2012 R2 ve üzeri (sunucu çekirdeği dahil)
- Ubuntu 16,04 ve 18,04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> Linux hostname veya Windows bilgisayar adı, ad içindeki ayrılmış sözcüklerden veya ticari markalara ait olamaz, aksi halde bağlı makineyi Azure 'a kaydetmeye çalışmak başarısız olur. Ayrılmış sözcüklerin listesi için bkz. [ayrılmış kaynak adı hatalarını çözümleme](../../azure-resource-manager/templates/error-reserved-resource-name.md) .

### <a name="required-permissions"></a>Gerekli izinler

* Makineleri eklemek için kaynak grubundaki **Azure bağlı makine ekleme** veya [katkıda bulunan](../../role-based-access-control/built-in-roles.md#contributor) rolü üyesi olursunuz.

* Bir makineyi okumak, değiştirmek ve silmek için kaynak grubundaki **Azure bağlı makine kaynak yöneticisi** rolünün bir üyesi olursunuz.

* **Betik oluştur** metodunu kullanırken aşağı açılan listeden bir kaynak grubu seçmek için, en azından söz konusu kaynak grubu için [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolünün bir üyesi olursunuz.

### <a name="azure-subscription-and-service-limits"></a>Azure aboneliği ve hizmet limitleri

Makinelerinizi Azure Arc etkin sunucularıyla yapılandırmadan önce, bağlanacak makine sayısını planlamak için Azure Resource Manager [abonelik sınırlarını](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) ve [kaynak grubu sınırlarını](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) gözden geçirin.

Azure Arc etkin sunucuları bir kaynak grubundaki en fazla 5.000 makine örneğini destekler.

### <a name="transport-layer-security-12-protocol"></a>Aktarım Katmanı Güvenliği 1,2 Protokolü

Azure 'a aktarılan verilerin güvenliğini sağlamak için, makineyi Aktarım Katmanı Güvenliği (TLS) 1,2 kullanacak şekilde yapılandırmanızı kesinlikle öneririz. TLS/Güvenli Yuva Katmanı (SSL) uygulamasının güvenlik açığı olduğu ve geriye dönük uyumlulukla hala çalışmaya devam eden daha eski sürümlerinin **kullanılması önerilmez**.

|Platform/dil | Destek | Daha Fazla Bilgi |
| --- | --- | --- |
|Linux | Linux dağıtımları, TLS 1,2 desteği için [OpenSSL](https://www.openssl.org) 'yi kullanır. | OpenSSL sürümünüzü doğrulamak için [OpenSSL changelog](https://www.openssl.org/news/changelog.html) ' yı denetleyin.|
| Windows Server 2012 R2 ve üzeri | Desteklenir ve varsayılan olarak etkindir. | Hala [varsayılan ayarları](/windows-server/security/tls/tls-registry-settings)kullandığınızdan emin olun.|

### <a name="networking-configuration"></a>Ağ yapılandırması

Linux ve Windows için bağlı makine Aracısı, TCP bağlantı noktası 443 üzerinden Azure Arc ile güvenli bir şekilde iletişim kurar. Makine Internet üzerinden iletişim kurmak için bir güvenlik duvarı veya ara sunucu üzerinden bağlanıyorsa, ağ yapılandırma gereksinimlerini anlamak için aşağıdakileri gözden geçirin.

> [!NOTE]
> Yay özellikli sunucular, bağlı makine Aracısı için proxy olarak [Log Analytics ağ geçidi](../../azure-monitor/agents/gateway.md) kullanmayı desteklemez.
>

Giden bağlantı, güvenlik duvarınız veya ara sunucunuz tarafından kısıtlanıyorsa, aşağıda listelenen URL 'Lerin engellenmediğinden emin olun. Aracının hizmetle iletişim kurması için gereken IP aralıklarının veya etki alanı adlarının yalnızca, aşağıdaki hizmet etiketlerine ve URL 'Lere erişim izni vermeniz gerekir.

Hizmet Etiketleri:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

Adresleri

| Aracı kaynağı | Description |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Konuk Yapılandırması|
|`*.his.arc.azure.com`|Karma kimlik hizmeti|
|`www.office.com`|Office 365|

Önizleme aracıları (sürüm 0,11 ve daha düşük) aşağıdaki URL 'Lere da erişim gerektirir:

| Aracı kaynağı | Description |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Konuk Yapılandırması|
|`*-agentservice-prod-1.azure-automation.net`|Konuk Yapılandırması|

Her bir hizmet etiketi/bölgesinin IP adresleri listesi için bkz. JSON dosyası- [Azure IP aralıkları ve hizmet etiketleri – genel bulut](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft, her bir Azure hizmetini ve kullandığı IP aralıklarını içeren haftalık güncelleştirmeler yayımlar. JSON dosyasındaki bu bilgiler, her bir hizmet etiketine karşılık gelen IP aralıklarının geçerli zaman noktası listesidir. IP adresleri değişikliğe tabidir. Güvenlik Duvarı yapılandırmanız için IP adresi aralıkları gerekliyse, tüm Azure hizmetlerine erişime izin vermek için **Azurecyüksek** hizmet etiketinin kullanılması gerekir. Bu URL 'Lerin güvenlik izlemesini veya denetimini devre dışı bırakmayın, diğer Internet trafiğinden yaptığınız gibi bunlara izin verin.

Daha fazla bilgi için [hizmet etiketlerine genel bakış](../../virtual-network/service-tags-overview.md)konusunu inceleyin.

### <a name="register-azure-resource-providers"></a>Azure Kaynak sağlayıcılarını kaydetme

Azure Arc etkin sunucuları, bu hizmeti kullanabilmeniz için aboneliğinizde aşağıdaki Azure Kaynak sağlayıcılarına bağımlıdır:

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Bunlar kayıtlı değilse, aşağıdaki komutları kullanarak bunları kaydedebilirsiniz:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLı:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Ayrıca, [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)altındaki adımları izleyerek Azure Portal kaynak sağlayıcılarını kaydedebilirsiniz.

## <a name="installation-and-configuration"></a>Yükleme ve yapılandırma

Karma ortamınızdaki makineleri doğrudan Azure ile bağlamak, gereksinimlerinize bağlı olarak farklı yöntemler kullanılarak gerçekleştirilebilir. Aşağıdaki tabloda, kuruluşunuz için en uygun olanı belirleyen her bir yöntem vurgulanmaktadır.

> [!IMPORTANT]
> Bağlı makine Aracısı bir Azure Windows sanal makinesine yüklenemez. Denerseniz, yükleme bunu algılar ve geri kaydolur.

| Yöntem | Açıklama |
|--------|-------------|
| Biriyle | [Azure Portal makinelerinden gelen bağlantı](onboard-portal.md)adımlarını izleyerek aracıyı tek veya az sayıda makineye el ile yükleyebilirsiniz.<br> Azure portal, aracının yüklenmesi ve yapılandırma adımlarını otomatik hale getirmek için bir betik oluşturup makinede çalıştırabilirsiniz.|
| Ölçekte | [Hizmet sorumlusu kullanarak, Connect makinelerini](onboard-service-principal.md)izleyen birden çok makine için aracıyı yükleyip yapılandırın.<br> Bu yöntem, makineleri etkileşimli olmayan bir şekilde bağlamak için bir hizmet sorumlusu oluşturur.|
| Ölçekte | [Windows POWERSHELL DSC 'Yi kullanarak](onboard-dsc.md)yöntemi izleyerek birden çok makine için aracıyı yükleyip yapılandırın.<br> Bu yöntem, PowerShell DSC ile etkileşimli olmayan makinelere bağlanmak için bir hizmet sorumlusu kullanır. |

## <a name="connected-machine-agent-technical-overview"></a>Bağlı makine aracısına teknik genel bakış

### <a name="windows-agent-installation-details"></a>Windows Aracısı yükleme ayrıntıları

Windows için bağlı makine Aracısı, aşağıdaki üç yöntemden biri kullanılarak yüklenebilir:

* Dosyaya çift tıklayın `AzureConnectedMachineAgent.msi` .
* Komut kabuğundan Windows Installer paketini çalıştırarak el ile `AzureConnectedMachineAgent.msi` .
* Komut dosyalı bir yöntemi kullanarak bir PowerShell oturumundan.

Windows için bağlı makine aracısını yükledikten sonra, sistem genelinde aşağıdaki yapılandırma değişiklikleri uygulanır.

* Aşağıdaki yükleme klasörleri kurulum sırasında oluşturulur.

    |Klasör |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Aracı destek dosyalarını içeren varsayılan yükleme yolu.|
    |%ProgramData%\AzureConnectedMachineAgent |Aracı yapılandırma dosyalarını içerir.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Alınan belirteçleri içerir.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |`agentconfig.json`Hizmet ile kayıt bilgilerini kaydeden aracı yapılandırma dosyasını içerir.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Konuk yapılandırma Aracısı dosyalarını içeren yükleme yolu. |
    |%ProgramData%\GuestConfig |Azure 'daki (uygulanan) ilkeleri içerir.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | Uzantılar Azure 'dan indirilir ve buradan kopyalanır.|

* Aşağıdaki Windows Hizmetleri, aracının yüklenmesi sırasında hedef makinede oluşturulur.

    |Hizmet adı |Görünen ad |İşlem adı |Description |
    |-------------|-------------|-------------|------------|
    |hımds |Azure hibrit Instance Metadata Service |hımds |Bu hizmet, Azure ve bağlı makinenin Azure kimliğiyle bağlantıyı yönetmek için Azure örnek meta veri hizmeti 'ni (ıMDS) uygular.|
    |GCArcService |Konuk yapılandırması yay hizmeti |gc_service |Makinenin istenen durum yapılandırmasını izler.|
    |ExtensionService |Konuk yapılandırma uzantısı hizmeti | gc_service |Makineyi hedefleyen gerekli uzantıları yüklenir.|

* Aşağıdaki çevresel değişkenler aracı yüklemesi sırasında oluşturulur.

    |Name |Varsayılan değer |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Sorun giderme için kullanılabilen çeşitli günlük dosyaları vardır. Bunlar aşağıdaki tabloda açıklanmıştır.

    |Günlük |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Aracıların (HıMDS) hizmetinin ayrıntılarını ve Azure ile etkileşimini kaydeder.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Verbose (-v) bağımsız değişkeni kullanıldığında azcmagent aracı komutlarının çıktısını içerir.|
    |%ProgramData%\GuestConfig\ gc_agent_logs \ gc_agent. log |DSC hizmeti etkinliğinin ayrıntılarını kaydeder,<br> belirli bir deyişle, HıMDS hizmeti ile Azure Ilkesi arasındaki bağlantı.|
    |% ProgramData% \GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |DSC hizmeti telemetrisi ve ayrıntılı günlük kaydı hakkındaki ayrıntıları kaydeder.|
    |%ProgramData%\GuestConfig\ ext_mgr_logs|Uzantı Aracısı bileşeniyle ilgili ayrıntıları kaydeder.|
    |%ProgramData%\GuestConfig\ extension_logs\<Extension>|Yüklü uzantının ayrıntılarını kaydeder.|

* Yerel güvenlik grubu **karma Aracısı uzantısı uygulamaları** oluşturulur.

* Aracının kaldırılması sırasında, aşağıdaki yapıtlar kaldırılmaz.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent ve alt dizinler
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux Aracısı yükleme ayrıntıları

Linux için bağlı makine Aracısı, dağıtım için tercih edilen paket biçiminde sağlanır (. RPM veya. DEB) Microsoft [paket deposunda](https://packages.microsoft.com/)barındırılır. Aracı yüklenir ve [Install_linux_azcmagent. sh](https://aka.ms/azcmagent)kabuk betik paketi ile yapılandırılır.

Linux için bağlı makine aracısını yükledikten sonra, sistem genelinde aşağıdaki yapılandırma değişiklikleri uygulanır.

* Aşağıdaki yükleme klasörleri kurulum sırasında oluşturulur.

    |Klasör |Description |
    |-------|------------|
    |/var/seçenek/azcmagent/ |Aracı destek dosyalarını içeren varsayılan yükleme yolu.|
    |/seçenek/azcmagent/ |
    |/seçenek/GC_Ext | Konuk yapılandırma Aracısı dosyalarını içeren yükleme yolu.|
    |/Seçenek/DSC/ |
    |/var/seçenek/azcmagent/Tokens |Alınan belirteçleri içerir.|
    |/var/lib/GuestConfig |Azure 'daki (uygulanan) ilkeleri içerir.|
    |/seçenek/GC_Ext/downloads|Uzantılar Azure 'dan indirilir ve buradan kopyalanır.|

* Aşağıdaki Daemon 'ları, aracının yüklenmesi sırasında hedef makinede oluşturulur.

    |Hizmet adı |Görünen ad |İşlem adı |Description |
    |-------------|-------------|-------------|------------|
    |hımdsd. hizmeti |Azure bağlı makine Aracısı hizmeti |hımds |Bu hizmet, Azure ve bağlı makinenin Azure kimliğiyle bağlantıyı yönetmek için Azure örnek meta veri hizmeti 'ni (ıMDS) uygular.|
    |gcad. servce |GC yay hizmeti |gc_linux_service |Makinenin istenen durum yapılandırmasını izler. |
    |extd. Service |Uzantı hizmeti |gc_linux_service | Makineyi hedefleyen gerekli uzantıları yüklenir.|

* Sorun giderme için kullanılabilen çeşitli günlük dosyaları vardır. Bunlar aşağıdaki tabloda açıklanmıştır.

    |Günlük |Description |
    |----|------------|
    |/var/seçenek/azcmagent/log/hımds.log |Aracıların (HıMDS) hizmetinin ayrıntılarını ve Azure ile etkileşimini kaydeder.|
    |/var/seçenek/azcmagent/log/azcmagent.log |Verbose (-v) bağımsız değişkeni kullanıldığında azcmagent aracı komutlarının çıktısını içerir.|
    |/seçenek/logs/DSC.log |DSC hizmeti etkinliğinin ayrıntılarını kaydeder,<br> belirli bir deyişle, hımds hizmeti ile Azure Ilkesi arasındaki bağlantı.|
    |/seçenek/logs/dsc.telemetry.txt |DSC hizmeti telemetrisi ve ayrıntılı günlük kaydı hakkındaki ayrıntıları kaydeder.|
    |/var/lib/GuestConfig/ext_mgr_logs |Uzantı Aracısı bileşeniyle ilgili ayrıntıları kaydeder.|
    |/var/lib/GuestConfig/extension_logs|Yüklü uzantının ayrıntılarını kaydeder.|

* Aşağıdaki çevresel değişkenler aracı yüklemesi sırasında oluşturulur. Bu değişkenler ' de ayarlanır `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Name |Varsayılan değer |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Aracının kaldırılması sırasında, aşağıdaki yapıtlar kaldırılmaz.

    * /var/opt/azcmagent
    * /seçenek/logs

## <a name="next-steps"></a>Sonraki adımlar

* Azure Arc etkin sunucularını değerlendirmeye başlamak için, [Azure Portal aracılığıyla karma makineleri Azure 'A bağlama](onboard-portal.md)makalesini izleyin.

* Sorun giderme bilgileri, [bağlı makine Aracısı sorunlarını giderme kılavuzunda](troubleshoot-agent-onboard.md)bulunabilir.
