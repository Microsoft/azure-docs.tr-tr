---
title: Orchestrator’dan Azure Otomasyonu’na (Beta) geçiş
description: Bu makalede runbook 'ları ve Tümleştirme paketlerini Orchestrator 'dan Azure Otomasyonu 'na nasıl geçirebileceğiniz açıklanır.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c929781772b510639e1e5e47eed19927f408d16a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830511"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Orchestrator’dan Azure Otomasyonu’na (Beta) geçiş

[System Center 2012-Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) içindeki runbook 'Lar, Azure Otomasyonu 'ndaki runbook 'Lar Windows PowerShell 'i temel alırken Orchestrator için özel olarak yazılmış tümleştirme paketlerindeki etkinliklere dayanır. Azure Otomasyonu 'ndaki [grafik runbook](automation-runbook-types.md#graphical-runbooks) 'larında, runbook 'ları PowerShell cmdlet 'lerini, alt runbook 'ları ve varlıkları temsil eden etkinlikleri içeren Orchestrator Runbook 'larına benzer bir görünüm vardır. Runbook 'ların kendilerini dönüştürmesinin yanı sıra, Tümleştirme paketlerini runbook 'ların Windows PowerShell cmdlet 'leriyle tümleştirmede kullandığı etkinliklerle dönüştürmeniz gerekir. 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA), runbook 'ları Orchestrator gibi yerel veri merkezinizde depolar ve çalıştırır ve Azure Otomasyonu ile aynı tümleştirme modüllerini kullanır. Runbook Dönüştürücüsü, Orchestrator Runbook 'larını SMA içinde desteklenmeyen grafik runbook 'larına dönüştürür. Standart etkinlikler modülünü ve System Center Orchestrator tümleştirme modüllerini SMA 'e yüklemeye devam edebilirsiniz, ancak [runbook 'larınızı el ile yeniden yazmanız](/system-center/sma/authoring-automation-runbooks)gerekir.

## <a name="download-the-orchestrator-migration-toolkit"></a>Orchestrator geçiş araç setini indirin

Geçişin ilk adımı, [System Center Orchestrator geçiş araç setini](https://www.microsoft.com/download/details.aspx?id=47323)indirmenin bir adımdır. Bu araç seti, runbook 'ları Orchestrator 'dan Azure Otomasyonu 'na dönüştürmeye yardımcı olan araçları içerir.  

## <a name="import-the-standard-activities-module"></a>Standart etkinlikler modülünü içeri aktarma

[Standart etkinlikler modülünü](/system-center/orchestrator/standard-activities) Azure Otomasyonu 'na aktarın. Bu, dönüştürülen grafik runbook 'larını kullanan standart Orchestrator etkinliklerinin dönüştürülmüş sürümlerini içerir.

## <a name="import-orchestrator-integration-modules"></a>Orchestrator tümleştirme modüllerini içeri aktarma

Microsoft, System Center bileşenlerini ve diğer ürünleri otomatikleştirmek için Runbook 'lar oluşturmak üzere [tümleştirme paketleri](/previous-versions/system-center/packs/hh295851(v=technet.10)) sağlar. Bu tümleştirme paketlerinden bazıları şu anda OıT tabanlıdır ancak bilinen sorunlar nedeniyle şu anda tümleştirme modüllerine dönüştürülemez. System Center [Orchestrator tümleştirme modüllerini](https://www.microsoft.com/download/details.aspx?id=49555) , runbook 'larınız tarafından kullanılan tümleştirme paketleri Için System Center 'A erişen Azure Otomasyonu 'na aktarın. Bu paket, Azure Otomasyonu 'na aktarılabilecek ve Service Management Automation tümleştirme paketlerinin dönüştürülmüş sürümlerini içerir.  

## <a name="convert-integration-packs"></a>Tümleştirme paketlerini Dönüştür

[Orchestrator Integration Toolkit (OıT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) kullanılarak oluşturulan herhangi bir tümleştirme paketini Azure otomasyonu veya Service Management Automation içeri aktarılabilecek PowerShell tabanlı tümleştirme modüllerine dönüştürmek Için [tümleştirme paketi dönüştürücüsünü](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) kullanın. Tümleştirme paketi dönüştürücüsünü çalıştırdığınızda, size bir tümleştirme paketi (. OIP) dosyası seçmenize olanak tanıyan bir sihirbaz sunulur. Sihirbaz daha sonra bu tümleştirme paketine dahil edilen etkinlikleri listeler ve hangi etkinliklerin geçirilecek olduğunu seçmenizi sağlar. Sihirbazı tamamladığınızda, özgün Tümleştirme paketindeki etkinliklerin her biri için karşılık gelen bir cmdlet 'i içeren bir tümleştirme modülü oluşturur.

> [!NOTE]
> OıT ile oluşturulmamış Tümleştirme paketlerini dönüştürmek için tümleştirme paketi dönüştürücüsünü kullanamazsınız. Microsoft tarafından sunulan ve şu anda bu araçla dönüştürülemediği bazı tümleştirme paketleri de mevcuttur. Bu tümleştirme paketlerinin dönüştürülmüş sürümleri, Azure Otomasyonu veya Service Management Automation yüklenebilmeleri için indirme için sağlanır.

### <a name="parameters"></a>Parametreler

Tümleştirme paketindeki bir etkinliğin tüm özellikleri, tümleştirme modülündeki karşılık gelen cmdlet 'in parametrelerine dönüştürülür.  Windows PowerShell cmdlet 'leri, tüm cmdlet 'lerle kullanılabilecek bir dizi [ortak parametreye](/powershell/module/microsoft.powershell.core/about/about_commonparameters) sahiptir. Örneğin,-Verbose parametresi, bir cmdlet 'in işlem hakkındaki ayrıntılı bilgileri çıkışına neden olur.  Hiçbir cmdlet ortak parametre ile aynı ada sahip bir parametreye sahip olamaz. Bir etkinliğin ortak parametreli aynı ada sahip bir özelliği varsa, sihirbaz, parametre için başka bir ad vermenizi ister.

### <a name="monitor-activities"></a>Etkinlikleri izleme

Orchestrator 'daki runbook 'ları izleyerek [izleyici etkinliğini](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) başlatın ve belirli bir olay tarafından çağrılması için sürekli olarak bekletmeyi çalıştırın. Azure Otomasyonu runbook 'ları izlemeyi desteklemediğinden, tümleştirme paketindeki tüm izleme etkinlikleri dönüştürülmez. Bunun yerine, izleme etkinliğinin tümleştirme modülünde bir yer tutucu cmdlet 'i oluşturulur.  Bu cmdlet 'in bir işlevi yoktur, ancak onu kullanan dönüştürülmüş runbook 'un yüklenmesini sağlar. Bu runbook, Azure Otomasyonu 'nda çalıştırılamaz, ancak bunu değiştirebilmeniz için yüklenebilir.

Orchestrator, bir tümleştirme paketine dahil olmayan ancak birçok runbook tarafından kullanılan bir dizi [Standart etkinlik](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) içerir.  Standart etkinlikler modülü, bu etkinliklerin her biri için bir cmdlet eşdeğerini içeren bir tümleştirme modülüdür. Standart bir etkinlik kullanan dönüştürülmüş runbook 'ları içeri aktarmadan önce Bu tümleştirme modülünü Azure Otomasyonu 'nda yüklemelisiniz.

Dönüştürülmüş runbook 'ların desteklenmesinin yanı sıra, standart etkinlikler modülündeki cmdlet 'ler, Azure Otomasyonu 'nda yeni runbook 'lar oluşturmak için Orchestrator 'ı bilen bir kişi tarafından kullanılabilir. Tüm standart etkinliklerin işlevselliği cmdlet 'lerle gerçekleştirilese de, farklı şekilde çalışmayabilir. Dönüştürülmüş standart etkinlikler modülündeki cmdlet 'ler ilgili etkinlikleriyle aynı şekilde çalışır ve aynı parametreleri kullanır. Bu, Azure Otomasyonu runbook 'larına geçiş yapmanıza yardımcı olabilir.

## <a name="convert-orchestrator-runbooks"></a>Orchestrator Runbook 'larını dönüştürme

Orchestrator Runbook Converter, Orchestrator Runbook 'larını Azure Otomasyonu 'na aktarılabilen [grafik runbook](automation-runbook-types.md#graphical-runbooks) 'larına dönüştürür. Runbook Dönüştürücüsü, dönüşümü yapan cmdlet 'i ile bir PowerShell modülü olarak uygulanır `ConvertFrom-SCORunbook` . Dönüştürücüyü yüklediğinizde, cmdlet 'i yükleyen bir PowerShell oturumunun kısayolunu oluşturur.   

Bir runbook 'u dönüştürmeye ve Azure Otomasyonu 'na aktarmaya yönelik temel adımlar aşağıda verilmiştir. Cmdlet 'ini kullanma ayrıntıları bu bölümün ilerleyen kısımlarında verilmiştir.

1. Orchestrator 'dan bir veya daha fazla runbook dışarı aktarın.
2. Runbook 'taki tüm etkinlikler için tümleştirme modüllerini edinin.
3. İçe aktarılmış dosyadaki Orchestrator Runbook 'larını dönüştürün.
4. Değişiklikleri doğrulamak ve gerekli tüm el ile görevleri öğrenmek için günlüklerdeki bilgileri gözden geçirin.
5. Dönüştürülmüş runbook 'ları Azure Otomasyonu 'na aktarın.
6. Azure Otomasyonu 'nda gerekli varlıkları oluşturun.
7. Gerekli etkinlikleri değiştirmek için Azure Otomasyonu 'nda runbook 'u düzenleyin.

Sözdizimi şöyledir `ConvertFrom-SCORunbook` :

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath-dönüştürülecek runbook 'ları içeren dışa aktarma dosyasının yolu.
* Modül-runbook 'larda etkinlikleri içeren tümleştirme modüllerinin virgülle ayrılmış listesi.
* OutputFolder-dönüştürülen grafik runbook 'ları oluşturmak için klasörün yolu.

Aşağıdaki örnek komut, **MyRunbooks.ois_export** adlı bir dışarı aktarma dosyasındaki runbook 'ları dönüştürür.  Bu runbook 'lar Active Directory ve Data Protection Manager Tümleştirme paketlerini kullanır.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Runbook Dönüştürücüsü günlük dosyalarını kullan

Runbook Dönüştürücüsü, dönüştürülmüş runbook ile aynı konumda aşağıdaki günlük dosyalarını oluşturur.  Dosyalar zaten mevcutsa, son dönüşümdeki bilgilerle üzerine yazılır.

| Dosya | İçindekiler |
|:--- |:--- |
| Runbook Dönüştürücüsü-Ilerleme. log |Dönüştürmenin ayrıntılı adımları, her etkinlik için bilgiler ve Dönüştürülmeyen her etkinlik için uyarı olarak başarıyla dönüştürüldü. |
| Runbook Dönüştürücüsü-Özet. log |Tüm uyarılar dahil olmak üzere son dönüştürmenin Özeti ve dönüştürülen runbook için gereken bir değişken oluşturmak gibi gerçekleştirmeniz gereken görevleri takip edin. |

### <a name="export-runbooks-from-orchestrator"></a>Runbook 'ları Orchestrator 'dan dışarı aktar

Runbook Dönüştürücüsü bir veya daha fazla runbook içeren bir Orchestrator 'dan dışarı aktarma dosyasıyla birlikte kullanılabilir.  Dışarı aktarma dosyasındaki her Orchestrator Runbook 'u için karşılık gelen bir Azure Otomasyonu runbook 'u oluşturur.  

Bir runbook 'u Orchestrator 'dan dışarı aktarmak için Runbook Designer 'de runbook 'un adına sağ tıklayın ve **dışarı aktar**' ı seçin.  Bir klasördeki tüm runbook 'ları dışarı aktarmak için klasörün adına sağ tıklayın ve **dışarı aktar**' ı seçin.

### <a name="convert-runbook-activities"></a>Runbook etkinliklerini Dönüştür

Runbook Dönüştürücüsü, Orchestrator Runbook 'taki her etkinliği Azure Otomasyonu 'nda karşılık gelen bir etkinliğe dönüştürür.  Dönüştürülemeyen etkinlikler için, runbook 'ta uyarı metniyle birlikte bir yer tutucu etkinlik oluşturulur.  Dönüştürülmüş runbook 'u Azure Otomasyonu 'na aktardıktan sonra, bu etkinliklerin herhangi birini gerekli işlevselliği gerçekleştiren geçerli etkinliklerle değiştirmeniz gerekir.

Standart etkinlikler modülündeki tüm Orchestrator etkinlikleri dönüştürülür. Bu modülde olmayan ve Dönüştürülmeyen bazı standart Orchestrator etkinlikleri vardır. Örneğin, `Send Platform Event` olay Orchestrator 'a özgü olduğundan Azure Otomasyonu eşdeğeri yoktur.

Azure Otomasyonu 'nda bunlara eşdeğer olmadığından [izleyici etkinlikleri](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) dönüştürülmez. Özel durumlar, dönüştürülmüş tümleştirme paketlerinde yer tutucu etkinliğine dönüştürülen etkinlikleri izler.

İle tümleştirme modülünün yolunu sağladıysanız, dönüştürülmüş tümleştirme paketinden herhangi bir etkinlik dönüştürülür `modules` . System Center tümleştirme paketleri için System Center Orchestrator tümleştirmesi modüllerini kullanabilirsiniz.

### <a name="manage-orchestrator-resources"></a>Orchestrator kaynaklarını yönetme

Runbook dönüştürücüsü yalnızca runbook 'ları, sayaçlar, değişkenler veya bağlantılar gibi diğer Orchestrator kaynaklarını dönüştürür.  Sayaçlar Azure Otomasyonu 'nda desteklenmez.  Değişkenler ve bağlantılar desteklenir, ancak bunları el ile oluşturmanız gerekir. Runbook 'un bu tür kaynaklar gerektirip gerektirmediğini ve dönüştürülen runbook 'un düzgün çalışması için Azure Otomasyonu 'nda oluşturmanız gereken ilgili kaynakları belirtmesini sağlayan günlük dosyaları size bildirir.

Örneğin, bir runbook bir etkinlikte belirli bir değeri doldurmak için bir değişken kullanabilir.  Dönüştürülen runbook, etkinliği dönüştürür ve Azure Otomasyonu 'nda Orchestrator değişkeniyle aynı ada sahip bir değişken varlığı belirtir. Bu eylem, dönüştürmeden sonra oluşturulan **runbook Dönüştürücüsü-Summary. log** dosyasında belirtilmiştir. Runbook 'u kullanmadan önce bu değişken varlığı Azure Otomasyonu 'nda el ile oluşturmanız gerekir.

### <a name="work-with-orchestrator-input-parameters"></a>Orchestrator giriş parametreleriyle çalışma

Orchestrator 'daki runbook 'lar, etkinlik ile giriş parametrelerini kabul eder `Initialize Data` .  Dönüştürülen runbook bu etkinliği içeriyorsa, etkinliğin her bir parametresi için Azure Otomasyonu runbook 'unda bir [giriş parametresi](automation-graphical-authoring-intro.md#handle-runbook-input) oluşturulur.  Dönüştürülmüş runbook 'ta her parametreyi alan ve döndüren bir [Iş akışı betik denetim](automation-graphical-authoring-intro.md#use-activities) etkinliği oluşturulur. Runbook 'taki bir giriş parametresi kullanan etkinlikler, bu etkinliğin çıktısına başvurur.

Bu stratejinin kullanıldığı neden Orchestrator Runbook 'unda işlevselliği en iyi şekilde yansıtmasıdır.  Yeni grafik runbook 'larında etkinlik, runbook giriş veri kaynağı kullanarak doğrudan giriş parametrelerine başvurmalıdır.

### <a name="invoke-runbook-activity"></a>Runbook 'U çağır etkinliği

Orchestrator 'daki runbook 'lar etkinlikteki diğer runbook 'ları başlatır `Invoke Runbook` . Dönüştürülen runbook bu etkinliği içeriyorsa ve `Wait for completion` seçenek ayarlandıysa, dönüştürülmüş runbook 'ta kendisi için bir runbook etkinliği oluşturulur.  `Wait for completion`Seçenek ayarlanmamışsa, runbook 'u başlatmak Için [Start-azautomationrunbook](/powershell/module/az.automation/start-azautomationrunbook) kullanan bir iş akışı betiği etkinliği oluşturulur. Dönüştürülmüş runbook 'u Azure Otomasyonu 'na aktardıktan sonra, bu etkinliği etkinlikte belirtilen bilgilerle değiştirmeniz gerekir.

## <a name="create-orchestrator-assets"></a>Orchestrator varlıkları oluşturma

Runbook Dönüştürücüsü Orchestrator varlıklarını dönüştürmez. Azure Otomasyonu 'nda gerekli Orchestrator varlıklarını el ile oluşturmanız gerekir.

## <a name="configure-hybrid-runbook-worker"></a>Karma Runbook Worker 'ı yapılandırma

Orchestrator Runbook 'ları bir veritabanı sunucusunda depolar ve Runbook sunucularında her ikisi de yerel veri merkezinizde çalışır. Azure Otomasyonu 'ndaki runbook 'lar Azure bulutunda depolanır ve [karma runbook çalışanı](automation-hybrid-runbook-worker.md)kullanılarak yerel veri merkezinizde çalıştırılabilir. Yerel sunucularda çalışmak ve yerel kaynaklara erişmek üzere tasarlandıklarından, runbook 'larınızı Orchestrator 'dan dönüştürülen runbook 'ları çalıştıracak şekilde yapılandırın.

## <a name="related-articles"></a>İlgili makaleler:

* Orchestrator ayrıntıları için bkz. [System Center 2012-Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)).
* [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12))içindeki hizmetlerin yönetimini otomatikleştirme hakkında daha fazla bilgi edinin.
* Orchestrator etkinliklerinin ayrıntıları [Orchestrator Standart etkinliklerinde](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))bulunabilir.
* Orchestrator geçiş araç takımını edinmek için bkz. [System Center Orchestrator geçiş araç seti 'Ni indirme](https://www.microsoft.com/download/details.aspx?id=47323).
* Azure Otomasyonu karma Runbook Worker 'a genel bakış için bkz. [karma Runbook Worker genel bakış](automation-hybrid-runbook-worker.md).
