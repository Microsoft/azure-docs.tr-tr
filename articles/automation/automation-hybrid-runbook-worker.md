---
title: Azure Otomasyonu karma Runbook Worker genel bakış
description: Bu makalede, yerel veri merkezinizdeki veya bulut sağlayıcınızdaki makinelerde runbook 'ları çalıştırmak için kullanabileceğiniz karma Runbook Worker 'a genel bakış sunulmaktadır.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2bb178302d399805eb84b233060d5717e2dba8b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830565"
---
# <a name="hybrid-runbook-worker-overview"></a>Karma Runbook Çalışanına genel bakış

Azure Otomasyonu 'ndaki runbook 'lar, Azure bulut platformunda çalıştıkları için diğer bulutlardaki veya şirket içi ortamınızdaki kaynaklara erişime sahip olmayabilir. Runbook 'u doğrudan rolü barındıran makinede ve bu yerel kaynakları yönetmek için ortamdaki kaynaklara karşı çalıştırmak için Azure Automation 'ın karma Runbook Worker özelliğini kullanabilirsiniz. Runbook 'lar Azure Otomasyonu 'nda depolanır ve yönetilir ve ardından bir veya daha fazla atanmış makineye dağıtılır.

## <a name="runbook-worker-types"></a>Runbook Worker türleri

İki tür runbook çalışanı vardır-sistem ve Kullanıcı. Aşağıdaki tabloda aralarındaki fark açıklanmaktadır.

|Tür | Description |
|-----|-------------|
|**Sistem** |, Windows ve Linux makinelerine Kullanıcı tarafından belirtilen güncelleştirmeleri yüklemek için tasarlanan Güncelleştirme Yönetimi özelliği tarafından kullanılan bir gizli runbook kümesini destekler.<br> Bu karma runbook çalışanı türü karma Runbook Worker grubunun bir üyesi değildir ve bu nedenle bir runbook worker grubunu hedefleyen runbook 'ları çalıştırmaz. |
|**Kullanıcı** |, Bir veya daha fazla Runbook Worker grubunun üyesi olan Windows ve Linux makinesinde doğrudan çalıştırılması amaçlanan Kullanıcı tanımlı runbook 'ları destekler. |

Karma Runbook Worker, Windows veya Linux işletim sistemi üzerinde çalışabilir ve bu rol, Azure Izleyici [Log Analytics çalışma alanına](../azure-monitor/logs/design-logs-deployment.md)raporlama [Log Analytics aracısına](../azure-monitor/agents/log-analytics-agent.md) bağımlıdır. Çalışma alanı yalnızca desteklenen işletim sistemi için makineyi izlemek için değil, karma Runbook Worker 'ı yüklemek için gereken bileşenleri de indiriyordu.

Azure Otomasyonu [güncelleştirme yönetimi](./update-management/overview.md) etkinleştirildiğinde, Log Analytics çalışma alanınıza bağlı tüm makineler otomatik olarak bir sistem karma runbook çalışanı olarak yapılandırılır. Bir Kullanıcı Windows karma Runbook Worker olarak yapılandırmak için bkz. [Windows karma runbook çalışanı dağıtma](automation-windows-hrw-install.md) ve Linux için bkz. [Linux karma Runbook Worker dağıtımı](automation-linux-hrw-install.md).

## <a name="how-does-it-work"></a>Nasıl çalışır?

![Karma Runbook Çalışanına genel bakış](media/automation-hybrid-runbook-worker/automation.png)

Her Kullanıcı karma runbook çalışanı, çalışanı yüklerken belirttiğiniz bir karma Runbook Worker grubunun üyesidir. Bir grup tek bir çalışan içerebilir, ancak yüksek kullanılabilirlik için bir gruba birden fazla çalışan dahil edebilirsiniz. Her makine, tek bir Otomasyon hesabına tek bir karma Runbook Worker raporlaması barındırabilir; Karma çalışanı birden çok Otomasyon hesabına kaydedemezsiniz. Karma çalışanı yalnızca tek bir Otomasyon hesabından işleri dinleyebilirler. Güncelleştirme Yönetimi tarafından yönetilen sistem karma Runbook Worker 'ı barındıran makinelerde, karma Runbook Worker grubuna eklenebilirler. Ancak hem Güncelleştirme Yönetimi hem de karma runbook çalışanı grup üyeliği için aynı Otomasyon hesabını kullanmanız gerekir.

Bir runbook 'u Kullanıcı karma Runbook Worker üzerinde başlattığınızda, üzerinde çalıştığı grubu belirtirsiniz. Gruptaki her çalışan, kullanılabilir işlerin olup olmadığını görmek için Azure Otomasyonu ' nu yoklar. Bir iş varsa, işi almak için ilk çalışan onu alır. İşler sırasının işlem süresi karma çalışan donanım profiline ve yüküne bağlıdır. Belirli bir çalışanı belirtemezsiniz. Karma çalışan bir yoklama mekanizmasına (30 saniyede bir) ve ilk-gelir, ilk-sahip bir sıraya uyar. Bir işin ne zaman itiltiğine bağlı olarak, Otomasyon Hizmeti tarafından hangi karma çalışan ping işlemleri işi alır. Tek bir karma çalışan genellikle her ping için dört iş (yani, 30 saniyede) alabilir. İş hızınız, 30 saniyeden daha yüksekse, karma Runbook Worker grubundaki bir başka karma çalışan da işi çekmiş olur.

Karma Runbook Worker, disk alanı, bellek veya ağ yuvaları üzerinde [Azure Sandbox](automation-runbook-execution.md#runbook-execution-environment) kaynak [sınırlarına](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) çok fazla sahip değildir. Karma çalışanındaki sınırlar yalnızca çalışan kaynakları ile ilgilidir ve Azure korumalı alanı 'nın sahip olduğu [dengeli](automation-runbook-execution.md#fair-share) çalışma süresi sınırı ile kısıtlanır.

Karma runbook çalışanlarındaki runbook 'ların dağıtımını denetlemek için ve işlerin ne zaman veya nasıl tetiklendiği, karma çalışanı Otomasyon hesabınızdaki farklı karma Runbook Worker gruplarına karşı kaydedebilirsiniz. Yürütme düzenlemelerinizi desteklemek için işleri Belirli Grup veya gruplara göre hedefleyin.

## <a name="hybrid-runbook-worker-installation"></a>Karma Runbook Worker yüklemesi

Bir Kullanıcı karma Runbook Worker 'ı yüklemek için işlem işletim sistemine bağlıdır. Aşağıdaki tabloda dağıtım türleri tanımlanmaktadır.

|Operating System  |Dağıtım türleri  |
|---------|---------|
|Windows     | [Otomatik](automation-windows-hrw-install.md#automated-deployment)<br>[El ile](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [El ile](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Bir Windows makinesi için önerilen yükleme yöntemi, bir Azure Otomasyonu runbook 'unu, yapılandırma sürecini tamamen otomatikleştirecek şekilde kullanmaktır. Bu uygun değilse, rolü el ile yüklemek ve yapılandırmak için adım adım prosedürü izleyebilirsiniz. Linux makinelerinde aracıyı makineye yüklemek için bir Python betiği çalıştırırsınız.

## <a name="network-planning"></a><a name="network-planning"></a>Ağ planlama

Bağlantı noktaları, URL 'Ler ve karma Runbook Worker için gereken diğer ağ ayrıntıları hakkında ayrıntılı bilgi için [Azure Otomasyonu ağ yapılandırması](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) 'nı denetleyin.

### <a name="proxy-server-use"></a>Proxy sunucusu kullanımı

Azure Otomasyonu ve Log Analytics aracısını çalıştıran makineler arasında iletişim için bir ara sunucu kullanıyorsanız, uygun kaynakların erişilebilir olduğundan emin olun. Karma runbook worker ve Automation hizmetlerinden gelen isteklerin zaman aşımı 30 saniyedir. Üç denemeden sonra bir istek başarısız olur.

### <a name="firewall-use"></a>Güvenlik duvarı kullanımı

Internet 'e erişimi kısıtlamak için güvenlik duvarı kullanıyorsanız, güvenlik duvarını erişime izin verecek şekilde yapılandırmanız gerekir. Log Analytics ağ geçidini ara sunucu olarak kullanıyorsanız, karma runbook çalışanları için yapılandırıldığından emin olun. Bkz. [Otomasyon karma runbook çalışanları için Log Analytics ağ geçidini yapılandırma](../azure-monitor/agents/gateway.md).

### <a name="service-tags"></a>Hizmet etiketleri

Azure Otomasyonu, Azure sanal ağ hizmeti etiketlerini destekleyerek [Guestandhybridmanagement](../virtual-network/service-tags-overview.md)hizmet etiketiyle başlar. [Ağ güvenlik gruplarında](../virtual-network/network-security-groups-overview.md#security-rules) veya [Azure Güvenlik duvarında](../firewall/service-tags.md)ağ erişim denetimleri tanımlamak için hizmet etiketlerini kullanabilirsiniz. Hizmet etiketleri, güvenlik kuralları oluştururken belirli IP adreslerinin yerine kullanılabilir. Bir kuralın uygun kaynak veya hedef alanında hizmet etiketi adı **Guestandhybridmanagement**  ' i belirterek, Otomasyon Hizmeti için trafiğe izin verebilir veya bu trafiği reddedebilirsiniz. Bu hizmet etiketi, IP aralıklarını belirli bir bölgeyle kısıtlayarak daha ayrıntılı denetim yapılmasına izin vermeyi desteklemez.

Azure Otomasyonu hizmetinin hizmet etiketi yalnızca aşağıdaki senaryolar için kullanılan IP 'Leri sağlar:

* Sanal ağınızın içinden Web kancalarını tetikleme
* VNet 'teki karma runbook çalışanları veya durum yapılandırması aracılarının otomasyon hizmetiyle iletişim kurmasına izin verin

>[!NOTE]
>**Guestandhybridmanagement** hizmet etiketi şu anda yalnızca bir karma runbook çalışanında bulunan bir Azure korumalı alanında runbook işi yürütmeyi desteklememektedir.

## <a name="support-for-impact-level-5-il5"></a>Etki düzeyi 5 için destek (IL5)

Azure Otomasyonu karma Runbook Worker, Azure Kamu 'da aşağıdaki iki yapılandırmanın herhangi birindeki 5 iş yüklerini etkileyebilir:

* [Yalıtılmış sanal makine](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Dağıtıldığında, IL5 iş yüklerini desteklemek için gereken yalıtım düzeyini sağlayan bu makine için tüm fiziksel ana bilgisayarı tüketir.

* Tek bir Azure aboneliğine ayrılmış bir veya daha fazla sanal makineyi barındırabilen fiziksel sunucular sağlayan [Azure ayrılmış Konakları](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host).

>[!NOTE]
>Karma runbook çalışanı rolü aracılığıyla işlem yalıtımı, Azure ticari ve ABD kamu bulutları için kullanılabilir.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Karma Runbook Worker için Güncelleştirme Yönetimi adresleri

Karma Runbook Worker için gereken standart adreslere ve bağlantı noktalarına ek olarak, Güncelleştirme Yönetimi [ağ planlama](./update-management/overview.md#ports) bölümünde açıklanan diğer ağ yapılandırma gereksinimleri vardır.

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Karma Runbook Worker üzerinde Azure Otomasyonu durum yapılandırması

[Azure Otomasyonu durum yapılandırmasını](automation-dsc-overview.md) karma Runbook Worker üzerinde çalıştırabilirsiniz. Karma runbook çalışanını destekleyen sunucuların yapılandırmasını yönetmek için sunucuları DSC düğümleri olarak eklemeniz gerekir. Bkz. [Azure Otomasyonu durum yapılandırması ile yönetimi için makineleri etkinleştirme](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Runbook Worker sınırları

Otomasyon hesabı başına en fazla karma çalışan grubu sayısı 4000 ' dir ve hem System & Kullanıcı karma çalışanları için geçerlidir. Yönetmek için 4.000 ' den fazla makineniz varsa, başka bir Otomasyon hesabı oluşturmanızı öneririz.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Karma Runbook Worker 'daki runbook 'lar

Yerel makinedeki kaynakları yöneten veya bir Kullanıcı karma Runbook Worker 'ın dağıtıldığı Yerel ortamdaki kaynaklara karşı çalışan runbook 'larınız olabilir. Bu durumda, runbook 'larınızı bir Otomasyon hesabı yerine karma çalışan üzerinde çalıştırmayı seçebilirsiniz. Karma Runbook Worker üzerinde çalışan runbook 'lar, Otomasyon hesabında çalıştıranlarla aynı yapıyla aynıdır. Bkz. [bir karma runbook çalışanında runbook 'Ları çalıştırma](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Karma Runbook Worker işleri

Karma Runbook Worker işleri Windows üzerinde yerel **sistem** hesabı veya Linux üzerinde [nxautomation hesabı](automation-runbook-execution.md#log-analytics-agent-for-linux) altında çalışır. Azure Otomasyonu, karma runbook çalışanları üzerindeki işleri, Azure korumalı alanında çalıştırılan işlerden farklı işler. Bkz. [runbook yürütme ortamı](automation-runbook-execution.md#runbook-execution-environment).

Karma Runbook Worker konak makinesi yeniden başlatılırsa, çalışan runbook işi başlangıçtan itibaren veya PowerShell Iş akışı runbook 'ları için son denetim noktasından başlatılır. Bir runbook işi üç kez yeniden başlatıldıktan sonra askıya alınır.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Karma Runbook Worker için Runbook izinleri

Azure dışı kaynaklara erişirken, bir Kullanıcı karma runbook çalışanı üzerinde çalışan runbook 'lar, genellikle Azure kaynaklarında kimlik doğrulaması yapan runbook 'lar tarafından kullanılan kimlik doğrulama mekanizmasını kullanamaz. Bir runbook yerel kaynaklara kendi kimlik doğrulamasını sağlar veya [Azure kaynakları için yönetilen kimlikleri](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)kullanarak kimlik doğrulamasını yapılandırır. Ayrıca, tüm runbook 'lar için bir kullanıcı bağlamı sağlamak üzere bir farklı çalıştır hesabı belirtebilirsiniz.

## <a name="view-system-hybrid-runbook-workers"></a>Sistem karma runbook çalışanlarını görüntüleme

Güncelleştirme Yönetimi özelliği Windows veya Linux makinelerde etkinleştirildikten sonra, Azure portal sistem karma runbook çalışanları grubunun listesini envantere kaydedebilirsiniz. Seçilen Otomasyon hesabı için sol bölmeden **karma çalışanlar grubu** seçeneğini belirleyerek, portalda en  fazla 2.000 çalışanı görüntüleyebilirsiniz ve ardından,

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Otomasyon hesabı sistemi karma çalışanı grupları sayfası" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

2.000 ' den fazla karma çalışanı varsa, tümünün listesini almak için aşağıdaki PowerShell betiğini çalıştırabilirsiniz:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Sonraki adımlar

* Runbook 'larınızı şirket içi veri merkezinizde veya diğer bulut ortamınızda otomatik hale getirmek üzere nasıl yapılandıracağınızı öğrenmek için bkz. [runbook 'Ları karma Runbook Worker üzerinde çalıştırma](automation-hrw-run-runbooks.md).

* Karma runbook çalışanlarınızın sorunlarını giderme hakkında bilgi edinmek için bkz. [karma Runbook Worker sorunlarını giderme](troubleshoot/hybrid-runbook-worker.md#general).
