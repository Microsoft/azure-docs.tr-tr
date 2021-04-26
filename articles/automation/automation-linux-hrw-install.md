---
title: Azure Otomasyonu 'nda Linux karma runbook çalışanı dağıtma
description: Bu makalede, yerel veri merkezinizdeki veya bulut ortamınızdaki Linux tabanlı makinelerde runbook 'ları çalıştırmak için bir Azure Otomasyonu karma Runbook Worker nasıl yükleneceği açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 04/06/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24dc0d2b243eb6c13e5670a1438876132c5e429e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833661"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Linux karma runbook çalışanı dağıtma

Azure 'un [etkin](../azure-arc/servers/overview.md)olduğu sunucular da dahil olmak üzere runbook 'Ları doğrudan Azure 'Da veya Azure dışı makinede çalıştırmak Için Azure Otomasyonu 'Nun Kullanıcı karma Runbook Worker özelliğini kullanabilirsiniz. Rolü barındıran makineden veya sunucudan, bu yerel kaynakları yönetmek için Runbook 'ları doğrudan bu ve ortamdaki kaynaklara karşı çalıştırabilirsiniz.

Linux hibrit Runbook Worker, runbook 'ları, yükseltme gerektiren komutları çalıştırmak için yükseltilebilir özel bir kullanıcı olarak yürütür. Azure Otomasyonu runbook 'ları depolar ve yönetir ve ardından bunları bir veya daha fazla belirlenen makineye gönderir. Bu makalede karma Runbook Worker 'ın bir Linux makinesine nasıl yükleneceği, çalışanın nasıl kaldırılacağı ve karma Runbook Worker grubunun nasıl kaldırılacağı açıklanır.

Runbook Worker 'ı başarıyla dağıttıktan sonra, runbook 'larınızı şirket içi veri merkezinizde veya diğer bulut ortamınızda otomatikleştirmek üzere nasıl yapılandıracağınızı öğrenmek için [karma Runbook Worker 'daki runbook 'Ları Çalıştır](automation-hrw-run-runbooks.md) ' ı inceleyin.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, aşağıdakilere sahip olduğunuzdan emin olun.

### <a name="a-log-analytics-workspace"></a>Log Analytics çalışma alanı

Karma Runbook Worker rolü, rolü yüklemek ve yapılandırmak için bir Azure Izleyici Log Analytics çalışma alanına bağlıdır. [Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)aracılığıyla veya [Azure Portal](../azure-monitor/logs/quick-create-workspace.md)aracılığıyla oluşturabilirsiniz.

Azure Izleyici Log Analytics çalışma alanınız yoksa, çalışma alanını oluşturmadan önce [Azure Izleyici günlüğü tasarım kılavuzunu](../azure-monitor/logs/design-logs-deployment.md) gözden geçirin.

### <a name="log-analytics-agent"></a>Log Analytics aracısı

Karma Runbook Worker rolü, desteklenen Linux işletim sistemi için [Log Analytics aracısına](../azure-monitor/agents/log-analytics-agent.md) gerek duyar. Azure dışında barındırılan sunucular veya makineler için, [Azure Arc etkin sunucularını](../azure-arc/servers/overview.md)kullanarak Log Analytics aracısını yükleyebilirsiniz.

>[!NOTE]
>Linux için Log Analytics aracısını yükledikten sonra, `sudoers.d` klasörün veya sahip olduğu izinleri değiştirmemelisiniz. Sudo izni, karma Runbook Worker 'ın altında çalıştığı kullanıcı bağlamı olan **nxautomation** hesabı için gereklidir. İzinler kaldırılmamalıdır. Bunu belirli klasörler veya komutlarla kısıtlamak, bir değişikliğe neden olabilir.
>

### <a name="supported-linux-operating-systems"></a>Desteklenen Linux işletim sistemleri

Karma Runbook Worker özelliği aşağıdaki dağıtımları destekler. Tüm işletim sistemleri x64 olarak kabul edilir. x86, herhangi bir işletim sistemi için desteklenmez.

* Amazon Linux 2012,09-2015,09
* CentOS Linux 5, 6, 7 ve 8
* Oracle Linux 5, 6 ve 7
* Red Hat Enterprise Linux Server 5, 6, 7 ve 8
* Borçlu GNU/Linux 6, 7 ve 8
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS ve 18,04 LTS
* 12 ve 15 SUSE Linux Enterprise Server (SUSE Not 13 veya 14 numaralı sürüm sürümü değil)

> [!IMPORTANT]
> Sistem karma Runbook Worker rolüne bağlı olan Güncelleştirme Yönetimi özelliğini etkinleştirmeden önce [, desteklediği dağıtımları](update-management/overview.md#supported-operating-systems)doğrulayın.

### <a name="minimum-requirements"></a>Minimum gereksinimler

Bir Linux sistemi ve Kullanıcı karma Runbook Worker için en düşük gereksinimler şunlardır:

* İki çekirdek
* 4 GB RAM
* Bağlantı noktası 443 (giden)

| **Gerekli paket** | **Açıklama** | **En düşük sürüm**|
|--------------------- | --------------------- | -------------------|
|GLIBC |GNU C Kitaplığı| 2.5-12 |
|Openssl| OpenSSL kitaplıkları | 1,0 (TLS 1,1 ve TLS 1,2 desteklenir)|
|Curl | Web istemcisini kıvır | 7.15.5|
|Python-ctypes | Python 2. x veya Python 3. x gereklidir |
|PAM | Eklenebilir Kimlik Doğrulaması Modülleri|
| **İsteğe bağlı paket** | **Açıklama** | **En düşük sürüm**|
| PowerShell Core | PowerShell runbook 'larını çalıştırmak için PowerShell Core 'un yüklenmesi gerekir. Yükleme hakkında bilgi edinmek için bkz. [Linux üzerinde PowerShell Core yükleme](/powershell/scripting/install/installing-powershell-core-on-linux) . | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Karma Runbook Worker grubuna makine ekleme

Çalışan makineyi, Otomasyon hesaplarınızdan birindeki bir karma Runbook Worker grubuna ekleyebilirsiniz. Güncelleştirme Yönetimi tarafından yönetilen sistem karma Runbook Worker 'ı barındıran makinelerde, karma Runbook Worker grubuna eklenebilirler. Ancak hem Güncelleştirme Yönetimi hem de karma runbook çalışanı grup üyeliği için aynı Otomasyon hesabını kullanmanız gerekir.

>[!NOTE]
>Azure Otomasyonu [güncelleştirme yönetimi](./update-management/overview.md) , sistem karma runbook çalışanını güncelleştirme yönetimi için etkinleştirilmiş bir Azure veya Azure dışı makineye otomatik olarak yüklüyor. Ancak, bu çalışan Otomasyon hesabınızdaki herhangi bir karma Runbook Worker grubuna kayıtlı değildir. Runbook 'larınızı bu makinelerde çalıştırmak için, onları karma Runbook Worker grubuna eklemeniz gerekir. Bir gruba eklemek için [bir Linux hibrit Runbook Worker Install](#install-a-linux-hybrid-runbook-worker) bölümünün altındaki 4. adımı izleyin.

## <a name="supported-linux-hardening"></a>Desteklenen Linux sağlamlaştırma

Aşağıdakiler henüz desteklenmemektedir:

* CI

## <a name="supported-runbook-types"></a>Desteklenen runbook türleri

Linux hibrit runbook çalışanları, Azure Otomasyonu 'nda sınırlı sayıda runbook türü kümesini destekler ve aşağıdaki tabloda açıklanmıştır.

|Runbook türü | Desteklenir |
|-------------|-----------|
|Python 3 (Önizleme)|Evet, yalnızca bu kaldırmalar için gereklidir: SUSE LES 15, RHEL 8 ve CentOS 8|
|Python 2 |Evet, Python 3<sup>1</sup> gerektirmeyen herhangi bir ayırıcı için |
|PowerShell |Evet<sup>2</sup> |
|PowerShell İş Akışı |No |
|Grafik |No |
|Grafik PowerShell Iş akışı |No |

<sup>1</sup> Bkz. [desteklenen Linux işletim sistemleri](#supported-linux-operating-systems).

<sup>2</sup> PowerShell runbook 'ları, Linux makinesinde PowerShell Core 'un yüklü olmasını gerektirir. Yükleme hakkında bilgi edinmek için bkz. [Linux üzerinde PowerShell Core yükleme](/powershell/scripting/install/installing-powershell-core-on-linux) .

### <a name="network-configuration"></a>Ağ yapılandırması

Karma Runbook Worker ağ gereksinimleri için bkz. [ağınızı yapılandırma](automation-hybrid-runbook-worker.md#network-planning).

## <a name="install-a-linux-hybrid-runbook-worker"></a>Linux karma Runbook Worker 'ı yükler

Karma Runbook Worker dağıtmak için iki yöntem vardır. Azure portal runbook galerisinden runbook 'u içeri aktarıp çalıştırabilirsiniz veya aynı görevi gerçekleştirmek için bir dizi PowerShell komutunu el ile çalıştırabilirsiniz.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Runbook galerisinden runbook içeri aktarma

İçeri aktarma yordamı, [Azure Portal GitHub 'dan runbook 'Ları Içeri aktarma](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)bölümünde ayrıntılı olarak açıklanmıştır. İçeri aktarılacak runbook 'un adı **Automation Linux HybridWorker oluştur**.

Runbook aşağıdaki parametreleri kullanır.

| Parametre | Durum | Açıklama |
| ------- | ----- | ----------- |
| `Location` | Zorunlu | Log Analytics çalışma alanının konumu. |
| `ResourceGroupName` | Zorunlu | Otomasyon hesabınızın kaynak grubu. |
| `AccountName` | Zorunlu | Karma çalıştırma çalışanının kaydedileceği Otomasyon hesabı adı. |
| `CreateLA` | Zorunlu | True ise, `WorkspaceName` bir Log Analytics çalışma alanı oluşturmak için değerini kullanır. False ise, değeri `WorkspaceName` var olan bir çalışma alanına başvurmalıdır. |
| `LAlocation` | İsteğe Bağlı | Log Analytics çalışma alanının oluşturulacağı konum veya zaten var. |
| `WorkspaceName` | İsteğe Bağlı | Oluşturulacak veya kullanılacak Log Analytics çalışma alanının adı. |
| `CreateVM` | Zorunlu | True ise, `VMName` Yeni BIR VM 'nin adı olarak değerini kullanın. Yanlışsa, `VMName` mevcut VM 'yi bulmak ve kaydetmek için kullanın. |
| `VMName` | İsteğe Bağlı | Değerine bağlı olarak, oluşturulan veya kaydedilen sanal makinenin adı `CreateVM` . |
| `VMImage` | İsteğe Bağlı | Oluşturulacak VM görüntüsünün adı. |
| `VMlocation` | İsteğe Bağlı | Oluşturulan veya kaydedilen sanal makinenin konumu. Bu konum belirtilmemişse, değeri `LAlocation` kullanılır. |
| `RegisterHW` | Zorunlu | Doğru ise, VM 'yi karma çalışan olarak kaydedin. |
| `WorkerGroupName` | Zorunlu | Karma çalışanı grubunun adı. |

### <a name="manually-run-powershell-commands"></a>PowerShell komutlarını el ile Çalıştır

Bir Linux karma runbook çalışanı yüklemek ve yapılandırmak için aşağıdaki adımları gerçekleştirin.

1. Yükseltilmiş bir PowerShell komut isteminde aşağıdaki komutu çalıştırarak veya [Azure portal](https://portal.azure.com)Cloud Shell Log Analytics çalışma alanınızda Azure Otomasyonu çözümünü etkinleştirin:

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Log Analytics aracısını hedef makineye dağıtın.

    * Azure VM 'Leri için [Linux için sanal makine uzantısını](../virtual-machines/extensions/oms-linux.md)kullanarak linux için Log Analytics aracısını yüklersiniz. Uzantı Log Analytics aracısını Azure sanal makinelerine yükleyip sanal makineleri mevcut bir Log Analytics çalışma alanına kaydeder. [ *Linux* veya *Windows* VM 'leri için dağıtım Log Analytics aracısını](../governance/policy/samples/built-in-policies.md#monitoring) atamak üzere bir Azure Resource Manager şablonu, Azure CLI veya Azure İlkesi kullanabilirsiniz. Aracı yüklendikten sonra, makine Otomasyon hesabınızdaki bir karma Runbook Worker grubuna eklenebilir.

    * Azure dışı makineler için, [Azure Arc etkin sunucularını](../azure-arc/servers/overview.md)kullanarak Log Analytics aracısını yükleyebilirsiniz. Yay özellikli sunucular, aşağıdaki yöntemleri kullanarak Log Analytics aracısının dağıtılmasını destekler:

        - VM uzantıları çerçevesini kullanma.

            Azure Arc etkin sunucuları 'ndaki bu özellik, Azure olmayan bir Windows ve/veya Linux sunucusuna Log Analytics Agent VM uzantısını dağıtmanıza olanak tanır. SANAL makine uzantıları, karma makinelerinizde veya Arc etkin sunucularla yönetilen sunucularda aşağıdaki yöntemler kullanılarak yönetilebilir:

            - [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Kaynak Yöneticisi şablonları](../azure-arc/servers/manage-vm-extensions-template.md)

        - Azure Ilkesi 'ni kullanma.

            Bu yaklaşımı kullanarak, Arc etkin sunucusunda Log Analytics aracısının yüklü olup olmadığını denetlemek için Azure Ilke [dağıtma Log Analytics aracısını Linux veya Windows Azure Arc makineler](../governance/policy/samples/built-in-policies.md#monitoring) yerleşik ilkesine kullanırsınız. Aracı yüklü değilse, bir düzeltme görevi kullanarak otomatik olarak dağıtır. Alternatif olarak, makineleri VM'ler için Azure İzleyici ile izlemeyi planlıyorsanız, bunun yerine Log Analytics aracısını yüklemek ve yapılandırmak için [VM'ler için Azure izleyici etkinleştir](../governance/policy/samples/built-in-initiatives.md#monitoring) girişim kullanın.

        Azure Ilkesini kullanarak Windows veya Linux için Log Analytics Aracısı yüklemenizi öneririz.

    > [!NOTE]
    > Istenen durum yapılandırması (DSC) ile karma Runbook Worker rolünü destekleyen makinelerin yapılandırmasını yönetmek için, makineleri DSC düğümleri olarak eklemeniz gerekir.

    > [!NOTE]
    > Linux hibrit Worker yüklemesi sırasında karşılık gelen sudo izinleri olan [nxautomation hesabı](automation-runbook-execution.md#log-analytics-agent-for-linux) bulunmalıdır. Çalışanı yüklemeye çalışırsanız ve hesap yoksa veya uygun izinlere sahip değilse, yükleme başarısız olur.

3. Aracının çalışma alanına rapor ettiğini doğrulayın.

    Linux için Log Analytics Aracısı, makineleri bir Azure Izleyici Log Analytics çalışma alanına bağlar. Aracıyı makinenize yükleyip çalışma alanınıza bağladığınızda, karma Runbook Worker için gereken bileşenleri otomatik olarak indirir.

    Aracı birkaç dakika sonra Log Analytics çalışma alanınıza başarıyla bağlandığında, çalışma alanına sinyal verisi gönderdiğini doğrulamak için aşağıdaki sorguyu çalıştırabilirsiniz.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Arama sonuçlarında, makineye bağlı olduğunu ve hizmete raporlanmasını belirten, makinenin sinyal kayıtlarını görmeniz gerekir. Varsayılan olarak, her aracı atanmış çalışma alanına bir sinyal kaydını iletir.

4. Makineyi karma Runbook Worker grubuna eklemek için aşağıdaki komutu çalıştırın,, ve parametrelerinin değerlerini belirtin `-w` `-k` `-g` `-e` .

    Parametreler için gereken bilgileri `-k` ve `-e` Otomasyon hesabınızdaki **anahtarlar** sayfasından alabilirsiniz. Sayfanın sol tarafındaki **Hesap ayarları** bölümünün altında bulunan **anahtarlar** ' ı seçin.

    ![Anahtarları Yönet sayfası](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Parametresi için `-e` , **URL** değerini kopyalayın.

    * Parametresi için `-k` , **BIRINCIL erişim anahtarı** değerini kopyalayın.

    * Parametresi için `-g` , yeni Linux hibrit Runbook Worker 'ın katılması gereken karma Runbook Worker grubunun adını belirtin. Bu grup Otomasyon hesabında zaten mevcutsa, geçerli makine buna eklenir. Bu grup yoksa, bu ad ile oluşturulur.

    * Parametresi için `-w` Log Analytics çalışma alanı kimliğinizi belirtin.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Betik tamamlandıktan sonra dağıtımı doğrulayın. Otomasyon hesabınızdaki **karma runbook çalışanı grupları** sayfasında, **Kullanıcı karma runbook çalışanları grubu** sekmesinde, yeni veya mevcut grup ve üye sayısını gösterir. Mevcut bir gruptur, üye sayısı artırılır. Sayfadaki listeden grubu seçebilirsiniz, sol taraftaki menüden **hibrit çalışanlar**' ı seçin. **Karma çalışanlar** sayfasında, grubun her bir üyesini listelendiğini görebilirsiniz.

    > [!NOTE]
    > Bir Azure VM için Linux için Log Analytics sanal makine uzantısı kullanıyorsanız, `autoUpgradeMinorVersion` `false` otomatik yükseltme sürümleri için ayarı, karma Runbook Worker ile sorun oluşmasına neden olabilir. Uzantıyı el ile yükseltme hakkında bilgi edinmek için bkz. [Azure CLI dağıtımı](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>İmza doğrulamasını kapat

Varsayılan olarak, Linux karma runbook çalışanları imza doğrulaması gerektirir. Bir çalışan için imzasız runbook çalıştırırsanız bir `Signature validation failed` hata görürsünüz. İmza doğrulamasını devre dışı bırakmak için aşağıdaki komutu çalıştırın. İkinci parametreyi Log Analytics çalışma alanı KIMLIĞINIZLE değiştirin.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Karma runbook çalışanını kaldırma

`ls /var/opt/microsoft/omsagent`Çalışma alanı kimliğini almak Için karma Runbook Worker üzerinde komutunu kullanabilirsiniz. Çalışma alanı KIMLIĞI ile adlandırılan bir klasör oluşturulur.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Bu betik, Linux için Log Analytics aracısını makineden kaldırmaz. Yalnızca karma Runbook Worker rolünün işlevselliğini ve yapılandırmasını kaldırır.

## <a name="remove-a-hybrid-worker-group"></a>Karma Çalışanı grubunu kaldırma

Linux makinelerinin karma Runbook Worker grubunu kaldırmak için, bir Windows karma çalışanı grubu ile aynı adımları kullanırsınız. Bkz. [karma çalışanı grubunu kaldırma](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Sonraki adımlar

* Runbook 'larınızı şirket içi veri merkezinizde veya diğer bulut ortamınızda otomatik hale getirmek üzere nasıl yapılandıracağınızı öğrenmek için bkz. [runbook 'Ları karma Runbook Worker üzerinde çalıştırma](automation-hrw-run-runbooks.md).

* Karma runbook çalışanlarınızın sorunlarını giderme hakkında bilgi edinmek için bkz. [karma Runbook Worker sorunlarını giderme-Linux](troubleshoot/hybrid-runbook-worker.md#linux).
