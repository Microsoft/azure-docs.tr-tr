---
title: Azure Otomasyonu runbook türleri
description: "Azure Otomasyonu 'nda kullanabileceğiniz farklı runbook türlerini ve hangi türün kullanılacağı belirlenirken dikkate almanız gereken önemli noktaları açıklar. "
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c29015e2fd327d74183d5fbbd6214152507e517
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886776"
---
# <a name="azure-automation-runbook-types"></a>Azure Otomasyonu runbook türleri

Azure Otomasyonu, aşağıdaki tabloda kısaca açıklanan çeşitli runbook türlerini destekler.  Aşağıdaki bölümler her bir tür hakkında, her birinin ne zaman kullanılacağı hakkında konular dahil daha fazla bilgi sağlar.

| Tür | Açıklama |
|:--- |:--- |
| [Grafik](#graphical-runbooks)|Windows PowerShell 'i temel alarak Azure portal ve tamamen grafik düzenleyicide oluşturulmuş ve düzenlenebilir. |
| [Grafik PowerShell Iş akışı](#graphical-runbooks)|Windows PowerShell Iş akışına dayalıdır ve Azure portal içindeki grafik düzenleyicide tamamen oluşturulup düzenleniyordu. |
| [PowerShell](#powershell-runbooks) |Windows PowerShell betiğini temel alan metin runbook 'u. |
| [PowerShell İş Akışı](#powershell-workflow-runbooks)|Windows PowerShell Iş akışına dayalı metin runbook 'u. |
| [Python](#python-runbooks) |Python tabanlı metin runbook 'u. |

## <a name="graphical-runbooks"></a>Grafik runbook 'ları

[Grafik](automation-runbook-types.md#graphical-runbooks) ve grafik PowerShell iş akışı runbook 'ları, Azure Portal grafik Düzenleyicisi ile oluşturulur ve düzenlenir.  Bunları bir dosyaya aktarabilir ve sonra başka bir Otomasyon hesabına aktarabilirsiniz. Ancak onları başka bir araçla oluşturamaz veya düzenleyemezsiniz. Grafik runbook 'lar PowerShell kodu oluşturur, ancak kodu doğrudan görüntüleyemez veya değiştiremezsiniz. Grafik runbook 'ları [metin biçimlerinden](automation-runbook-types.md)birine dönüştürülemez veya bir metin runbook 'unun grafik biçimine dönüştürülmesine izin verebilir. Grafik runbook 'ları, içeri aktarma sırasında grafik PowerShell Iş akışı runbook 'larına ve diğer bir yönteme dönüştürülebilir.

### <a name="advantages"></a>Üstünlü

* Görsel ekleme-bağlantı-yazma modelini yapılandırma
* Verilerin işlem boyunca nasıl akacağını odaklayın
* Yönetim süreçlerini görsel olarak temsil etme
* Üst düzey iş akışları oluşturmak için diğer runbook 'ları alt runbook 'lar olarak ekleyin
* Modüler programlamayı teşvik eder

### <a name="limitations"></a>Sınırlamalar

* Azure portal dışında runbook düzenlenemiyor.
* Karmaşık mantık yürütmek için PowerShell kodu içeren bir kod etkinliği gerektirebilir.
* Grafik iş akışı tarafından oluşturulan PowerShell kodunu görüntüleyemez veya doğrudan düzenleyemezsiniz. Oluşturduğunuz kodu herhangi bir kod etkinliği içinde görüntüleyebilirsiniz.
* Linux karma Runbook Worker üzerinde çalışamıyoruz

## <a name="powershell-runbooks"></a>PowerShell runbook 'ları

PowerShell runbook 'ları Windows PowerShell 'i temel alır.  Azure portal metin düzenleyicisini kullanarak runbook 'un kodunu doğrudan düzenleyebilirsiniz.  Ayrıca herhangi bir çevrimdışı metin düzenleyicisini kullanabilir ve [runbook 'U](manage-runbooks.md) Azure Otomasyonu 'na aktarabilirsiniz.

### <a name="advantages"></a>Üstünlü

* PowerShell Iş akışı ek karmaşıklıklarını olmadan tüm karmaşık mantığı PowerShell koduyla birlikte uygulayın.
* Runbook, çalıştırılmadan önce derlenmesi gerekmediğinden, PowerShell Iş akışı runbook 'lardan daha hızlı başlar.
* Azure 'da veya hem Linux hem de Windows karma runbook çalışanları üzerinde çalıştırılabilir

### <a name="limitations"></a>Sınırlamalar

* PowerShell betiği hakkında bilgi sahibi olmanız gerekir.
* Paralel olarak birden çok eylem yürütmek için [paralel işleme](automation-powershell-workflow.md#parallel-processing) kullanılamaz.
* Bir hata varsa runbook 'u yeniden başlatmak için [denetim noktaları](automation-powershell-workflow.md#checkpoints) kullanılamaz.
* PowerShell Iş akışı runbook 'ları ve grafik runbook 'lar yalnızca yeni bir iş oluşturan start-AzureAutomationRunbook cmdlet 'i kullanılarak alt runbook 'lar olarak dahil edilebilir.

### <a name="known-issues"></a>Bilinen Sorunlar

PowerShell runbook 'larında geçerli bilinen sorunlar aşağıda verilmiştir.

* PowerShell runbook 'ları null değeri olan şifrelenmemiş bir [değişken varlığı](automation-variables.md) alamıyor.
* PowerShell runbook 'ları ada sahip bir [değişken varlığı](automation-variables.md) alamıyor *~* .
* PowerShell runbook 'unda bir döngüde Get-Process, 80 yinelemeden sonra çökebilir.
* Bir PowerShell runbook 'u aynı anda çıkış akışına büyük miktarda veri yazmaya çalışırsa başarısız olabilir.   Genellikle, büyük nesnelerle çalışırken yalnızca ihtiyacınız olan bilgileri yazarak bu sorunu geçici olarak çözebilirsiniz.  Örneğin, *Get-Process*gibi bir şey almak yerine yalnızca gerekli alanları *Get-Process ile çıktısını alabilirsiniz | ProcessName, CPU öğesini seçin*.

## <a name="powershell-workflow-runbooks"></a>PowerShell Iş akışı runbook 'ları

PowerShell Iş akışı runbook 'ları [Windows PowerShell Iş akışını](automation-powershell-workflow.md)temel alan metin runbook 'larıdır.  Azure portal metin düzenleyicisini kullanarak runbook 'un kodunu doğrudan düzenleyebilirsiniz.  Ayrıca herhangi bir çevrimdışı metin düzenleyicisini kullanabilir ve [runbook 'U](manage-runbooks.md) Azure Otomasyonu 'na aktarabilirsiniz.

### <a name="advantages"></a>Üstünlü

* PowerShell Iş akışı kodu ile tüm karmaşık mantığı uygulayın.
* Bir hata varsa runbook 'u yeniden başlatmak için [denetim noktaları](automation-powershell-workflow.md#checkpoints) kullanın.
* Paralel olarak birden çok eylem gerçekleştirmek için [paralel işleme](automation-powershell-workflow.md#parallel-processing) kullanın.
* , Üst düzey iş akışları oluşturmak için alt runbook 'lar olarak diğer grafik runbook 'larını ve PowerShell Iş akışı runbook 'larını içerebilir.

### <a name="limitations"></a>Sınırlamalar

* Yazar PowerShell Iş akışı hakkında bilgi sahibi olmalıdır.
* Runbook, [Serisi kaldırılan nesneler](automation-powershell-workflow.md#code-changes)gibi PowerShell iş akışının ek karmaşıklığı ile uğraşmalıdır.
* Runbook, çalıştırılmadan önce derlenmesi gerektiğinden PowerShell runbook 'lardan daha uzun süre sürer.
* PowerShell runbook 'ları yalnızca, yeni bir iş oluşturan start-AzureAutomationRunbook cmdlet 'i kullanılarak alt runbook 'lar olarak dahil edilebilir.
* Linux karma Runbook Worker üzerinde çalışamıyoruz

## <a name="python-runbooks"></a>Python runbook 'ları

Python runbook 'ları Python 2 altında derlenir.  Azure portal metin düzenleyicisini kullanarak veya bir çevrimdışı metin Düzenleyicisi ile runbook kodunu doğrudan düzenleyebilir ve [runbook 'U](manage-runbooks.md) Azure Otomasyonu 'na aktarabilirsiniz.

### <a name="advantages"></a>Üstünlü

* Sağlam Python kitaplıklarını kullanın.
* Azure 'da veya her iki Linux karma runbook çalışanı üzerinde çalıştırılabilir. Windows karma runbook çalışanları, [Python 2.7](https://www.python.org/downloads/release/latest/python2) yüklü olarak desteklenir.

### <a name="limitations"></a>Sınırlamalar

* Python betiği hakkında bilgi sahibi olmanız gerekir.
* Şu anda yalnızca Python 2 desteklenir. Python 3 özel işlevlerinin başarısız olacağı anlamına gelir.
* Üçüncü taraf kitaplıklarını kullanmak için, paketin kullanılması için Otomasyon hesabına [içeri aktarmanız](python-packages.md) gerekir.

## <a name="considerations"></a>Dikkat edilmesi gerekenler

Belirli bir runbook için hangi türün kullanılacağını belirlerken aşağıdaki ek noktalara göz atın.

* Runbook 'lardan grafik olarak metin türüne veya diğer bir yönteme dönüştüremezsiniz.
* Alt runbook olarak farklı türlerde runbook 'lar kullanılarak sınırlamalar vardır. Daha fazla bilgi için bkz. [Azure Otomasyonu 'Nda alt runbook 'lar](automation-child-runbooks.md).

## <a name="next-steps"></a>Sonraki adımlar

* Grafik runbook 'u yazma hakkında daha fazla bilgi için bkz. [Azure Otomasyonu 'Nda grafik yazma](automation-graphical-authoring-intro.md)
* Runbook 'lar için PowerShell ve PowerShell iş akışları arasındaki farkları anlamak için bkz. [Windows PowerShell Iş akışını öğrenme](automation-powershell-workflow.md)
* Runbook oluşturma veya içeri aktarma hakkında daha fazla bilgi için bkz. [runbook oluşturma veya Içeri aktarma](manage-runbooks.md)
* Dil başvurusu ve öğrenme modülleri de dahil olmak üzere PowerShell hakkında daha fazla bilgi için [PowerShell belgelerine](https://docs.microsoft.com/powershell/scripting/overview)bakın.
