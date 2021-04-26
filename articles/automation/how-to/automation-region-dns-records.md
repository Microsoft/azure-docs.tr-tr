---
title: Azure Otomasyonu tarafından kullanılan Azure veri merkezi DNS kayıtları | Microsoft Docs
description: Bu makalede, Azure Otomasyonu özelliklerinin gerektirdiği DNS kayıtları, bu Otomasyon hesabını barındıran belirli bir Azure bölgesiyle iletişim kısıtlaması sağlar.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: d41d825c7bc33e05815c7528b436c85873859928
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168935"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Azure Otomasyonu tarafından kullanılan Azure bölgelerinin DNS kayıtları

[Azure Otomasyonu](../automation-intro.md) hizmeti, hizmete bağlanacak özellikler için BIR dizi DNS kaydı kullanır. Belirli bir bölge için tanımlanan bir Otomasyon hesabınız varsa, bu bölgesel veri merkezi ile iletişimi kısıtlayabilirsiniz. Aşağıdaki Otomasyon özelliklerinin bir güvenlik duvarının arkasında barındırıldığı zaman çalışmasına izin vermek için bu kayıtları bilmeniz gerekebilir:

* Karma Runbook Çalışanı
* Durum Yapılandırması
* Web Kancaları

>[!NOTE]
>Linux karma Runbook Worker kaydı, sürüm 1.6.10.2 veya üzeri olmadığı takdirde yeni kayıtlarla başarısız olur. Makinenin çalışan rolünün güncelleştirilmiş bir sürümünü alabilmesi ve bu yeni kayıtları kullanabilmesi için [Linux için Log Analytics aracısının](../../azure-monitor/agents/agent-linux.md) daha yeni bir sürümüne yükseltmeniz gerekir. Mevcut makineler herhangi bir sorun olmadan çalışmaya devam edecektir.  

## <a name="dns-records-per-region"></a>Bölge başına DNS kayıtları

Aşağıdaki tabloda her bölge için DNS kaydı sağlanmaktadır.

>[!NOTE]
>Burada verilen Automation DNS kayıtları listesi devre dışı bırakılmış olsa da, [özel bağlantı desteği](#support-for-private-link) altında listelenen yeni kayıtlara geçiş yapmanız ve otomasyon işlemlerinizle ilgili hatalardan kaçınmak için zaman da çalışır durumda kalmaya devam eder.

| **Bölge** | **DNS kaydı** |
| --- | --- |
| Orta Avustralya |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Doğu Avustralya |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Avustralya Güneydoğu |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Orta Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Orta Hindistan |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Doğu ABD 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Doğu Japonya |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Kuzey Avrupa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Orta Güney ABD |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Güneydoğu Asya |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Güney Birleşik Krallık | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| Orta Batı ABD | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| West Europe |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Batı ABD 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Özel bağlantı desteği

Azure Otomasyonu 'nda [özel bağlantıyı](../../private-link/private-link-overview.md) desteklemek için, desteklenen her VERI merkezinin DNS kayıtları güncelleştirilmiştir. Bölgeye özgü URL 'ler yerine, URL 'Ler Otomasyon hesabına özeldir.

| **Bölge** | **DNS kaydı** |
| --- | --- |
| Orta Batı ABD |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| Batı ABD |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| Batı ABD 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| Central US |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| Orta Güney ABD |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| Orta Kuzey ABD |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| Doğu ABD |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| Doğu ABD 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Orta Kanada |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| West Europe |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Kuzey Avrupa |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Güneydoğu Asya |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Doğu Asya |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| Orta Hindistan |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Doğu Japonya |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Güney Kore - Orta |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Avustralya Güneydoğu |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Doğu Avustralya |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Orta Avustralya |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| Güney Birleşik Krallık |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| Orta Fransa |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Güney Afrika - Kuzey |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Güney Brezilya |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| Kuzey Çin |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| Çin Kuzey 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| Çin Doğu 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| US Gov Virginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

`<accountId>`DNS kaydındaki değerini, değer **URL**'SINDEN Otomasyon hesabı KIMLIĞINIZI temsil eden GUID ile değiştirin. Azure portal **Hesap ayarları** altındaki **anahtarlardan** gereken kimliği alabilirsiniz.

![Otomasyon hesabı birincil anahtar sayfası](./media/automation-region-dns-records/automation-account-keys.png)

*Hesaptan* sonra değeri, **URL** alanından kopyalayın-`https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`

> [!NOTE]
> Web kancası ve agentservice DNS kayıtları, özel bağlantıyı desteklemek için yeni stil DNS kayıtlarına güncelleştirildi. JRDS DNS kayıtları için hem eski hem de yeni stil DNS kayıtları desteklenir. Özel bağlantı kullanmıyorsanız, eski stil DNS kayıtları görürsünüz, ancak özel bağlantı kullanırken, DNS kayıtlarının yeni stili görüntülenir.

[Özel durumları](../automation-runbook-execution.md#exceptions)tanımlarken listelenen adresleri kullanmanızı öneririz. Bölge adları yerine bölge IP adresleri listesi için, aşağıdaki bulut ortamları için Microsoft Indirme Merkezi ' nden JSON dosyasını indirin:

* [Azure IP aralıkları ve hizmet etiketleri-Azure genel](https://www.microsoft.com/download/details.aspx?id=56519)
* [Azure IP aralıkları ve hizmet etiketleri-Azure Kamu](https://www.microsoft.com/download/details.aspx?id=57063)
* [Azure IP aralıkları ve hizmet etiketleri-Azure Almanya](https://www.microsoft.com/download/details.aspx?id=57064)
* [Azure IP aralıkları ve hizmet etiketleri-Azure Çin Vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

IP adresi dosyası Microsoft Azure veri merkezlerinde kullanılan IP adresi aralıklarını listeler. İşlem, SQL ve depolama aralıklarını içerir ve şu anda dağıtılmış aralıkları ve IP aralıklarında yaklaşan değişiklikleri yansıtır. Dosyada görüntülenen yeni aralıklar, en az bir hafta için veri merkezlerinde kullanılmıyor.

Yeni IP adres dosyasını her hafta indirmek iyi bir fikirdir. Daha sonra, sitenizi Azure 'da çalışan hizmetleri doğru şekilde belirlemek için güncelleştirin.

> [!NOTE]
> Azure ExpressRoute kullanıyorsanız, her ayın ilk haftasında Azure Space Sınır Ağ Geçidi Protokolü (BGP) tanıtımını güncelleştirmek için IP adresi dosyasının kullanıldığını unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

* Karma runbook çalışanlarınızın sorunlarını giderme hakkında bilgi edinmek için bkz. [karma Runbook Worker sorunlarını giderme](../troubleshoot/hybrid-runbook-worker.md#general).

* Durum yapılandırmasıyla ilgili sorunları nasıl giderebileceğinizi öğrenmek için bkz. [durum yapılandırma sorunlarını giderme](../troubleshoot/desired-state-configuration.md).