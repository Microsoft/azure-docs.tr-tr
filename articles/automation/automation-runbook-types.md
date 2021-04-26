---
title: Azure Otomasyonu runbook türleri
description: Bu makalede, Azure Otomasyonu 'nda kullanabileceğiniz runbook 'ların türleri ve hangi türün kullanılacağını belirleme konuları açıklanmaktadır.
services: automation
ms.subservice: process-automation
ms.date: 02/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3e4f90372c2da22e8df3430ce340477352e5033b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830457"
---
# <a name="azure-automation-runbook-types"></a>Azure Otomasyonu runbook türleri

Azure Otomasyonu Işlem Otomasyonu özelliği, aşağıdaki tabloda tanımlandığı gibi çeşitli runbook türlerini destekler. İşlem otomasyonu ortamı hakkında bilgi edinmek için bkz. [Azure Otomasyonu 'Nda runbook yürütme](automation-runbook-execution.md).

| Tür | Description |
|:--- |:--- |
| [Grafik](#graphical-runbooks)|Windows PowerShell 'i temel alan ve Azure portal grafik düzenleyicisinde tamamen oluşturulan ve düzenlenen grafik runbook 'u. |
| [Grafik PowerShell Iş akışı](#graphical-runbooks)|Windows PowerShell Iş akışına dayalı grafik runbook 'u ve Azure portal içindeki grafik düzenleyicide tamamen oluşturulmuş ve düzenlenmiş. |
| [PowerShell](#powershell-runbooks) |Windows PowerShell komut dosyası tabanlı metin runbook 'u. |
| [PowerShell İş Akışı](#powershell-workflow-runbooks)|Windows PowerShell Iş akışı komut dosyası tabanlı metin runbook 'u. |
| [Python](#python-runbooks) |Python betik temelli metin runbook 'u. |

Belirli bir runbook için kullanılacak türü belirlerken aşağıdaki noktaları dikkate alın.

* Runbook 'ları grafik 'ten metin türüne veya başka bir şekilde dönüştüremezsiniz.
* Farklı türlerde runbook 'ların alt runbook 'lar olarak kullanılması için sınırlamalar vardır. Daha fazla bilgi için bkz. [Azure Otomasyonu 'Nda alt runbook 'lar](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafik runbook 'ları

Azure portal grafik düzenleyicisini kullanarak grafik ve grafik PowerShell Iş akışı runbook 'ları oluşturup düzenleyebilirsiniz. Ancak, başka bir araçla bu tür bir runbook oluşturamaz veya düzenleyemezsiniz. Grafik runbook 'ların ana özellikleri:

* Otomasyon hesabınızdaki dosyalara aktarılır ve sonra başka bir Otomasyon hesabına içeri aktarılır.
* PowerShell kodu oluştur.
* İçeri aktarma sırasında grafik PowerShell Iş akışı runbook 'larına veya sürümünden dönüştürüldü.

### <a name="advantages"></a>Avantajlar

* Visual Insert-Link-configure Authoring model kullanın.
* Verilerin süreç boyunca nasıl akacağını odaklayın.
* Yönetim süreçlerini görsel olarak temsil eder.
* Üst düzey iş akışları oluşturmak için diğer runbook 'ları alt runbook 'lar olarak ekleyin.
* Modüler programlamayı teşvik edin.

### <a name="limitations"></a>Sınırlamalar

* Azure portal dışında oluşturulamıyor veya düzenlenemiyor.
* Karmaşık mantık yürütmek için PowerShell kodu içeren bir kod etkinliği gerekebilir.
* [Metin biçimlerinden](automation-runbook-types.md)birine dönüştürülemez, ya da bir metin runbook 'unu grafik biçimine dönüştürebilirsiniz. 
* Grafik iş akışının oluşturduğu PowerShell kodunu görüntüleyemez veya doğrudan düzenleyemezsiniz. Oluşturduğunuz kodu herhangi bir kod etkinliği içinde görüntüleyebilirsiniz.
* Bir Linux karma Runbook Worker üzerinde runbook 'lar çalıştırılamıyor. Bkz. [karma runbook çalışanı kullanarak veri merkezinizdeki veya buluttaki kaynakları otomatikleştirme](automation-hybrid-runbook-worker.md).

## <a name="powershell-runbooks"></a>PowerShell runbook 'ları

PowerShell runbook 'ları Windows PowerShell 'i temel alır. Azure portal metin düzenleyicisini kullanarak runbook 'un kodunu doğrudan düzenleyebilirsiniz.  Ayrıca herhangi bir çevrimdışı metin düzenleyicisini kullanabilir ve [runbook 'U](manage-runbooks.md) Azure Otomasyonu 'na aktarabilirsiniz.

### <a name="advantages"></a>Avantajlar

* PowerShell Iş akışı ile ilgili diğer karmaşıklıklar olmadan tüm karmaşık mantığı PowerShell kodu ile uygulayın.
* Çalıştırmadan önce derlenmesi gerekmediğinden, PowerShell Iş akışı runbook 'lardan daha hızlı bir başlangıç yapın.
* Hem Windows hem de Linux için Azure 'da ve karma runbook çalışanlarında çalıştırın.

### <a name="limitations"></a>Sınırlamalar

* PowerShell betiği hakkında bilgi sahibi olmanız gerekir.
* Runbook 'lar paralel olarak birden çok eylem yürütmek için [paralel işleme](automation-powershell-workflow.md#use-parallel-processing) kullanamaz.
* Runbook 'lar bir hata oluşursa runbook 'u yeniden başlatmak için [denetim noktaları](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) kullanamaz.
* Yeni bir iş oluşturan [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) cmdlet 'ini kullanarak yalnızca PowerShell iş akışı runbook 'larını ve grafik runbook 'larını alt runbook 'lar olarak ekleyebilirsiniz.

### <a name="known-issues"></a>Bilinen sorunlar

PowerShell runbook 'larında şu andaki bilinen sorunlar şunlardır:

* PowerShell runbook 'ları null değeri olan şifrelenmemiş bir [değişken varlığı](./shared-resources/variables.md) alamıyor.
* PowerShell runbook 'ları, adında bir değişken varlık alamıyor `*~*` .
* Bir PowerShell runbook 'unda döngüdeki bir [Get-Process](/powershell/module/microsoft.powershell.management/get-process) işlemi, 80 yinelemeden sonra çökebilir.
* Bir PowerShell runbook 'u aynı anda çıkış akışına büyük miktarda veri yazmayı denediğinde başarısız olabilir. Genellikle bu soruna geçici bir çözüm olarak, runbook 'un büyük nesnelerle çalışması için gereken bilgileri vermesini sağlayabilirsiniz. Örneğin, hiçbir kısıtlama olmadan kullanmak yerine `Get-Process` cmdlet 'in yalnızca ' de olduğu gibi gerekli parametreleri çıkışına sahip olabilirsiniz `Get-Process | Select ProcessName, CPU` .

## <a name="powershell-workflow-runbooks"></a>PowerShell Iş akışı runbook 'ları

PowerShell Iş akışı runbook 'ları [Windows PowerShell Iş akışını](automation-powershell-workflow.md)temel alan metin runbook 'larıdır. Azure portal metin düzenleyicisini kullanarak runbook 'un kodunu doğrudan düzenleyebilirsiniz. Ayrıca herhangi bir çevrimdışı metin düzenleyicisini kullanabilir ve [runbook 'U](manage-runbooks.md) Azure Otomasyonu 'na aktarabilirsiniz.

### <a name="advantages"></a>Avantajlar

* PowerShell Iş akışı kodu ile tüm karmaşık mantığı uygulayın.
* Bir hata oluşursa, işlemi sürdürmeye yönelik [denetim noktaları](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) kullanın.
* Paralel olarak birden çok eylem yapmak için [paralel işleme](automation-powershell-workflow.md#use-parallel-processing) kullanın.
* , Üst düzey iş akışları oluşturmak için alt runbook 'lar olarak diğer grafik runbook 'larını ve PowerShell Iş akışı runbook 'larını içerebilir.

### <a name="limitations"></a>Sınırlamalar

* PowerShell Iş akışı hakkında bilgi sahibi olmanız gerekir.
* Runbook 'lar, [Serisi kaldırılan nesneler](automation-powershell-workflow.md#deserialized-objects)gibi PowerShell iş akışının ek karmaşıklığı ile uğraşmalıdır.
* Runbook 'lar çalıştırılmadan önce derlenmesi gerektiğinden PowerShell runbook 'lardan daha uzun sürer.
* PowerShell runbook 'larını yalnızca cmdlet 'ini kullanarak alt runbook 'lar olarak ekleyebilirsiniz `Start-AzAutomationRunbook` .
* Runbook 'lar Linux karma Runbook Worker üzerinde çalıştırılamaz.

## <a name="python-runbooks"></a>Python runbook 'ları

Python runbook 'ları Python 2 ve Python 3 altında derlenir. Python 3 runbook 'ları Şu anda önizleme aşamasındadır. Azure portal metin düzenleyicisini kullanarak runbook 'un kodunu doğrudan düzenleyebilirsiniz. Ayrıca, çevrimdışı bir metin Düzenleyicisi kullanabilir ve [runbook 'U](manage-runbooks.md) Azure Otomasyonu 'na aktarabilirsiniz.

Python 3 runbook 'ları aşağıdaki Azure genel altyapılarında desteklenir:

* Azure genel
* Azure Kamu

### <a name="advantages"></a>Avantajlar

* Sağlam Python kitaplıklarını kullanın.
* , Azure 'da veya karma runbook çalışanları üzerinde çalışabilir.
* Python 2 için Windows karma runbook çalışanları, [python 2,7](https://www.python.org/downloads/release/latest/python2) yüklü olarak desteklenir.
* Python 3 bulut Işleri için Python 3,8 sürümü desteklenir. Kod farklı sürümler arasında uyumluysa 3. x sürümünden oluşan betikler ve paketler çalışabilir.  
* Windows makinelerde Python 3 karma işleri için kullanmak isteyebileceğiniz herhangi bir 3. x sürümünü yüklemeyi tercih edebilirsiniz.  
* Linux makinelerinde Python 3 karma işleri için, DSC OMSConfig ve Linux hibrit Worker 'ı çalıştırmak için makinede yüklü Python 3 sürümüne bağımlıdır. Linux makinelerine 3,6 yüklemenizi öneririz. Ancak, Python 3 ' ün sürümleri arasındaki yöntem imzalarında veya sözleşmelerinde hiçbir değişiklik yoksa farklı sürümler de çalışır.

### <a name="limitations"></a>Sınırlamalar

* Python betiği hakkında bilgi sahibi olmanız gerekir.
* Üçüncü taraf kitaplıklarını kullanmak için paketleri Otomasyon hesabına [aktarmanız](python-packages.md) gerekir.
*    Bir Python 3 runbook 'unu (Önizleme) başlatmak Için PowerShell/PowerShell iş akışında start-automationrunbook cmdlet 'ini kullanma çalışmaz. Bu sınırlamaya geçici bir çözüm için Azurerd. Automation modülünden az. Automation modülünden veya **Start-AzureRmAutomationRunbook** cmdlet 'inden **Start-azautomationrunbook** cmdlet 'ini kullanabilirsiniz.  
* Python 3 runbook 'ları (Önizleme) ve paketleri PowerShell ile çalışmaz.
* Azure Otomasyonu, **sys. stderr** desteklemez.

### <a name="known-issues"></a>Bilinen sorunlar

Python 3 işleri bazen özel durum iletisiyle başarısız oldu *yorumlayıcı yürütülebilir yolu*. Bir iş gecikirse, 10 dakikadan uzun bir süre sonra veya **Start-automationrunbook** kullanarak Python 3 runbook 'ları başlatmak için bu özel durumu görebilirsiniz. İş gecikirse, runbook 'un yeniden başlatılması yeterli olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

* PowerShell runbook 'ları hakkında bilgi edinmek için bkz. [öğretici: PowerShell runbook 'U oluşturma](learn/automation-tutorial-runbook-textual-powershell.md).
* PowerShell Iş akışı runbook 'ları hakkında bilgi edinmek için bkz. [öğretici: PowerShell Iş akışı runbook 'U oluşturma](learn/automation-tutorial-runbook-textual.md).
* Grafik runbook 'ları hakkında bilgi edinmek için bkz. [öğretici: grafik runbook 'U oluşturma](learn/automation-tutorial-runbook-graphical.md).
* Python runbook 'ları hakkında bilgi edinmek için bkz. [öğretici: Python runbook oluşturma](learn/automation-tutorial-runbook-textual-python2.md).
