---
title: Azure Otomasyonu karma Runbook Worker
description: Bu makalede, yerel veri merkezinizdeki veya bulut sağlayıcınızdaki makinelerde runbook 'ları çalıştırmak için kullanabileceğiniz bir Azure Otomasyonu özelliği olan karma Runbook Worker 'ı yükleme ve kullanma hakkında bilgi sağlanır.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c10905c283619e6008dbe6ab8c4e721888b8b786
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743796"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Karma runbook çalışanı kullanarak veri merkezinizdeki veya buluttaki kaynakları otomatikleştirin

Azure Otomasyonu 'ndaki runbook 'lar, Azure bulut platformunda çalıştıkları için diğer bulutlardaki veya şirket içi ortamınızdaki kaynaklara erişime sahip olmayabilir. Runbook 'u doğrudan rolü barındıran bilgisayarda ve bu yerel kaynakları yönetmek için ortamdaki kaynaklara karşı çalıştırmak için Azure Otomasyonu 'nun karma Runbook Worker özelliğini kullanabilirsiniz. Runbook 'lar Azure Otomasyonu 'nda depolanır ve yönetilir ve ardından bir veya daha fazla atanmış bilgisayara gönderilir.

Aşağıdaki görüntüde Bu işlevsellik gösterilmektedir:

![Karma Runbook Çalışanına genel bakış](media/automation-hybrid-runbook-worker/automation.png)

Her karma runbook çalışanı, aracıyı yüklerken belirttiğiniz bir karma Runbook Worker grubunun üyesidir. Bir grup tek bir aracı içerebilir, ancak yüksek kullanılabilirlik için bir gruba birden çok aracı yükleyebilirsiniz. Her makine, tek bir Otomasyon hesabına tek bir karma çalışanı raporlama barındırabilir.

Karma Runbook Worker üzerinde bir runbook başlattığınızda, üzerinde çalıştığı grubu belirtirsiniz. Gruptaki her çalışan, kullanılabilir işlerin olup olmadığını görmek için Azure Otomasyonu ' nu yoklar. Bir iş varsa, işi almak için ilk çalışan onu alır. İşler sırasının işlem süresi karma çalışan donanım profiline ve yüküne bağlıdır. Belirli bir çalışanı belirtemezsiniz. Karma runbook çalışanları, Azure sanal alanının sahip olduğu limitlerin çoğunu paylaşmaz. Disk alanı, bellek veya ağ yuvaları için aynı sınırlara sahip değildir. Karma runbook çalışanları yalnızca karma Runbook Worker 'daki kaynaklarla sınırlandırılır. Ayrıca, hibrit runbook çalışanları, Azure korumalı alan tarafından kullanılan 180 dakikalık bir [paylaşılan](automation-runbook-execution.md#fair-share) zaman sınırını paylaşmaz. Azure korumalı alanlar ve karma runbook çalışanları için hizmet limitleri hakkında daha fazla bilgi edinmek için iş [limitleri](../azure-subscription-service-limits.md#automation-limits) sayfasına bakın.

## <a name="install-a-hybrid-runbook-worker"></a>Karma Runbook Worker 'ı yükler

Karma Runbook Worker 'ı yüklemeye yönelik işlem işletim sistemine bağlıdır. Aşağıdaki tablo, yükleme için kullanabileceğiniz yöntemlerin bağlantılarını içerir.

Bir Windows karma runbook çalışanı yüklemek ve yapılandırmak için iki yöntem kullanabilirsiniz. Önerilen yöntem, bir Windows bilgisayarı yapılandırma işlemini tamamen otomatik hale getirmek için bir Otomasyon Runbook 'u kullanmaktır. İkinci yöntem, rolü el ile yüklemek ve yapılandırmak için adım adım prosedürü takip eden bir yöntemdir. Linux makinelerinde aracıyı makineye yüklemek için bir Python betiği çalıştırırsınız.

|OS  |Dağıtım türleri  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[El ile](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Istenen durum yapılandırması (DSC) ile karma runbook çalışanı rolünü destekleyen sunucularınızın yapılandırmalarını yönetmek için, bunları DSC düğümleri olarak eklemeniz gerekir. DSC ile yönetime yönelik ekleme hakkında daha fazla bilgi için bkz. [Azure Automation DSC yönetim için makineleri ekleme](automation-dsc-onboarding.md).
>
>[Güncelleştirme yönetimi çözümünü](automation-update-management.md)etkinleştirirseniz, Azure Log Analytics çalışma alanınıza bağlı tüm bilgisayarlar, bu çözüme dahil olan runbook 'ları destekleyecek şekilde otomatik olarak karma Runbook Worker olarak yapılandırılır. Ancak, bilgisayar otomasyon hesabınızda zaten tanımlanmış olan karma çalışan gruplarıyla kayıtlı değildir. Bilgisayar, hem çözüm hem de karma runbook çalışanı grup üyeliği için aynı hesabı kullandığınız sürece Otomasyon Runbook 'larını desteklemek üzere otomasyon hesabınızdaki karma Runbook Worker grubuna eklenebilir. Bu işlev karma Runbook Worker 'ın 7.2.12024.0 sürümüne eklenmiştir.

Karma Runbook Worker dağıtmaya başlamadan önce [ağınızı planlama bilgilerini](#network-planning) gözden geçirin. Çalışanı başarıyla dağıttıktan sonra, runbook 'larınızı şirket içi veri merkezinizde veya diğer bulut ortamlarınızda otomatikleştirmek üzere nasıl yapılandıracağınızı öğrenmek için [bir karma runbook çalışanındaki runbook 'ları](automation-hrw-run-runbooks.md) inceleyin.

Bilgisayar, hem çözüm hem de karma runbook çalışanı grup üyeliği için aynı hesabı kullandığınız sürece Otomasyon Runbook 'larını desteklemek üzere otomasyon hesabınızdaki karma Runbook Worker grubuna eklenebilir. Bu işlev Karma Runbook Çalışanının 7.2.12024.0 sürümüne eklenmiştir.

## <a name="remove-a-hybrid-runbook-worker"></a>Karma runbook çalışanını kaldırma

Bir gruptan bir veya daha fazla karma runbook çalışanı kaldırabilir veya gereksinimlerinize bağlı olarak grubu kaldırabilirsiniz. Bir karma runbook çalışanını şirket içi bir bilgisayardan kaldırmak için aşağıdaki adımları kullanın:

1. Azure portal Otomasyon hesabınıza gidin.
2. **Hesap ayarları**altında **anahtarlar** ' ı seçin ve **URL** ve **birincil erişim anahtarı**değerlerini aklınızda yapın. Sonraki adımla ilgili bu bilgilere ihtiyacınız vardır.

### <a name="windows"></a>Windows

Yönetici modunda bir PowerShell oturumu açın ve aşağıdaki komutu çalıştırın. Kaldırma işleminin ayrıntılı bir günlüğü için **-verbose** anahtarını kullanın.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Eski makineleri karma çalışanı grubundan kaldırmak için isteğe bağlı `machineName` parametresini kullanın.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Çalışma alanı kimliği 'ni almak `ls /var/opt/microsoft/omsagent` için karma Runbook Worker üzerinde komutunu kullanabilirsiniz. Dizinde, klasörün adının çalışma alanı kimliği olduğu bir klasör vardır.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Bu kod Microsoft Monitoring Agent bilgisayardan kaldırmaz, yalnızca karma Runbook Worker rolünün işlevleri ve yapılandırması.

## <a name="remove-a-hybrid-worker-group"></a>Karma çalışanı grubunu kaldırma

Bir grubu kaldırmak için, önce daha önce gösterilen yordamı kullanarak karma runbook çalışanını grubun üyesi olan her bilgisayardan kaldırmanız gerekir. Sonra, grubu kaldırmak için aşağıdaki adımları kullanın:

1. Azure portal Otomasyon hesabını açın.
2. **Işlem Otomasyonu**altında **karma çalışan grupları**' nı seçin. Silmek istediğiniz grubu seçin. Bu grubun Özellikler sayfası görüntülenir.

   ![Özellikler sayfası](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Seçili grubun Özellikler sayfasında **Sil**' i seçin. Bu eylemi onaylamanızı isteyen bir ileti. Devam etmek istediğinizden emin değilseniz **Evet** ' i seçin.

   ![Onay iletisi](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Bu işlemin tamamlanması birkaç saniye sürebilir. Bu işlemin ilerleme durumunu menüdeki **Bildirimler**’in altından izleyebilirsiniz.

## <a name="network-planning"></a>Ağınızı yapılandırma

### <a name="hybrid-worker-role"></a>Karma çalışan rolü

Karma Runbook Worker 'ın Azure Otomasyonu 'na bağlanması ve kaydolmasının ardından bu bölümde açıklanan bağlantı noktası numarasına ve URL 'Lere erişimi olması gerekir. Bu erişim, Azure Izleyici günlüklerine bağlanmak üzere [Microsoft Monitoring Agent için gereken bağlantı noktaları ve URL 'lere](../azure-monitor/platform/agent-windows.md) yöneliktir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Aracı ve Azure Otomasyonu hizmeti arasındaki iletişim için bir ara sunucu kullanıyorsanız, uygun kaynakların erişilebilir olduğundan emin olun. Karma runbook worker ve Automation hizmetlerinden gelen isteklerin zaman aşımı 30 saniyedir. 3 denemeden sonra istek başarısız olur. İnternet 'e erişimi kısıtlamak için güvenlik duvarı kullanıyorsanız, güvenlik duvarınızı erişime izin verecek şekilde yapılandırmanız gerekir. Log Analytics ağ geçidini ara sunucu olarak kullanıyorsanız, karma çalışanlar için yapılandırıldığından emin olun. Bunun nasıl yapılacağı hakkında yönergeler için bkz. [Otomasyon karma çalışanları için Log Analytics ağ geçidini yapılandırma](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Karma Runbook Worker rolünün Otomasyon ile iletişim kurması için aşağıdaki bağlantı noktası ve URL 'Ler gereklidir:

* Bağ Giden internet erişimi için yalnızca TCP 443 gereklidir.
* Genel URL: *. azure-automation.net
* US Gov Virginia genel URL 'SI: *. azure-automation.us
* Aracı hizmeti: https://\<Workspace ID\>. Agentsvc.Azure-Automation.net

Özel durumlar tanımlanırken listelenen adreslerin kullanılması önerilir. IP adresleri için [Microsoft Azure veri MERKEZI IP aralıklarını](https://www.microsoft.com/en-us/download/details.aspx?id=56519)indirebilirsiniz. Bu dosya haftalık olarak güncelleştirilir ve şu anda dağıtılmış aralıklar ve IP aralıklarında yaklaşan değişiklikler vardır.

Belirli bir bölge için tanımlanan bir Otomasyon hesabınız varsa, bu bölgesel veri merkezi ile iletişimi kısıtlayabilirsiniz. Aşağıdaki tabloda her bölge için DNS kaydı verilmiştir:

| **Bölge** | **DNS kaydı** |
| --- | --- |
| Batı Orta ABD | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Orta Güney ABD |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Doğu ABD 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Batı ABD 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Orta Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Batı Avrupa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Kuzey Avrupa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Güneydoğu Asya |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Orta Hindistan |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japonya Doğu |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Avustralya Doğu |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Avustralya Güneydoğu |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Birleşik Krallık Güney | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| ABD Devleti Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Bölge adları yerine bölge IP adresleri listesi için, Microsoft Indirme Merkezi ' nden [Azure veri MERKEZI IP adresi](https://www.microsoft.com/download/details.aspx?id=41653) XML dosyasını indirin.

> [!NOTE]
> Azure veri merkezi IP adresi XML dosyası Microsoft Azure veri merkezlerinde kullanılan IP adresi aralıklarını listeler. Dosya işlem, SQL ve depolama aralıklarını içerir.
>
>Güncelleştirilmiş bir dosya haftalık olarak gönderilir. Dosya, şu anda dağıtılmış aralıkları ve IP aralıklarında yaklaşan değişiklikleri yansıtır. Dosyada görüntülenen yeni aralıklar, en az bir hafta için veri merkezlerinde kullanılmıyor.
>
> Her hafta yeni XML dosyasını indirmek iyi bir fikirdir. Daha sonra, sitenizi Azure 'da çalışan hizmetleri doğru şekilde belirlemek için güncelleştirin. Azure ExpressRoute kullanıcıları bu dosyanın, her ayın ilk haftasında Azure Space Sınır Ağ Geçidi Protokolü (BGP) tanıtımını güncelleştirmek için kullanıldığını unutmayın.

### <a name="update-management"></a>Güncelleştirme Yönetimi

Karma Runbook Worker için gereken standart adreslerin ve bağlantı noktalarının üstünde, özellikle Güncelleştirme Yönetimi için aşağıdaki adresler gereklidir. Bu adreslerle iletişim bağlantı noktası 443 üzerinden yapılır.

|Azure genel  |Azure Kamu  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Sonraki adımlar

* Runbook 'larınızı şirket içi veri merkezinizde veya diğer bulut ortamınızda otomatik hale getirmek üzere nasıl yapılandıracağınızı öğrenmek için bkz. [runbook 'Ları karma Runbook Worker üzerinde çalıştırma](automation-hrw-run-runbooks.md).
* Karma runbook çalışanlarınızı nasıl giderebileceğinizi öğrenmek için bkz. [karma runbook çalışanlarına sorun giderme](troubleshoot/hybrid-runbook-worker.md#general)

