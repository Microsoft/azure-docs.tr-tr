---
title: Azure dosyaları için paylaşılan anlık görüntülere genel bakış | Microsoft Docs
description: Paylaşma anlık görüntüsü, bir Azure dosya paylaşımının, bir zamanda, paylaşımın yedeklenme yöntemi olarak bir noktada alınmış bir salt okuma sürümüdür.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c05b79d2f1da8076b507ca9ee7a06504de21d5ea
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333172"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Azure dosyaları için paylaşılan anlık görüntülere genel bakış 
Azure dosyaları, dosya paylaşımlarının paylaşım anlık görüntülerini alma özelliğini sağlar. Anlık görüntü paylaşma, zaman içinde bu noktada paylaşma durumunu yakalar. Bu makalede, paylaşma anlık görüntülerinin sağladığı özellikleri ve özel kullanım kasaınızda bunlardan nasıl yararlanacağınızı anladık.

## <a name="when-to-use-share-snapshots"></a>Paylaşma anlık görüntülerini ne zaman kullanacağınızı

### <a name="protection-against-application-error-and-data-corruption"></a>Uygulama hatasına ve veri bozulmasına karşı koruma
Dosya paylaşımları kullanan uygulamalar yazma, okuma, depolama, iletim ve işleme gibi işlemleri gerçekleştirir. Bir uygulama yanlış yapılandırılmış veya istemeden oluşan bir hata ortaya geliyorsa, yanlışlıkla üzerine yazma veya hasar verme birkaç blok olabilir. Bu senaryolara karşı korumaya yardımcı olmak için, yeni uygulama kodu dağıtmadan önce bir paylaşma anlık görüntüsü alabilirsiniz. Yeni dağıtımla bir hata veya uygulama hatası varsa, bu dosya paylaşımında verilerinizin önceki bir sürümüne geri dönebilirsiniz. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Yanlışlıkla silinmelere veya istenmeyen değişikliklere karşı koruma
Bir dosya paylaşımındaki metin dosyası üzerinde çalıştığınızı düşünün. Metin dosyası kapatıldıktan sonra değişikliklerinizi geri alma imkanını kaybedersiniz. Bu gibi durumlarda, dosyanın önceki bir sürümünü kurtarmanız gerekir. Yanlışlıkla yeniden adlandırılırsa veya silinirse dosyanın önceki sürümlerini kurtarmak için paylaşma anlık görüntülerini kullanabilirsiniz.

### <a name="general-backup-purposes"></a>Genel yedekleme amaçları
Bir dosya paylaşma oluşturduktan sonra, veri yedekleme için kullanmak üzere dosya paylaşımının paylaşılan anlık görüntüsünü düzenli aralıklarla oluşturabilirsiniz. Paylaşılan bir anlık görüntü, düzenli aralıklarla çalıştırıldığında, gelecekteki denetim gereksinimleri veya olağanüstü durum kurtarma için kullanılabilecek önceki veri sürümlerinin korunmasını sağlar.

## <a name="capabilities"></a>Yetenekler
Paylaşılan anlık görüntü, verilerinizin zaman içindeki bir noktadaki kopyasıdır. REST API kullanarak anlık görüntü oluşturabilir, silebilir ve yönetebilirsiniz. Aynı yetenekler istemci kitaplığı, Azure CLı ve Azure portal de mevcuttur. 

Bir paylaşımın anlık görüntülerini hem REST API hem de SMB kullanarak görüntüleyebilirsiniz. Dizin veya dosya sürümlerinin listesini alabilir ve belirli bir sürümü doğrudan sürücü olarak bağlayabilirsiniz (yalnızca Windows-bkz. [sınırlara](#limits)ulaşılabilir). 

Bir paylaşma anlık görüntüsü oluşturulduktan sonra okunabilir, kopyalanabilir veya silinebilir, ancak değiştirilmez. Bir tam paylaşma anlık görüntüsünü başka bir depolama hesabına kopyalayamazsınız. Bu dosyayı AzCopy veya diğer kopyalama mekanizmalarını kullanarak yapmanız gerekir.

Paylaşılan anlık görüntü özelliği dosya paylaşma düzeyinde sağlanır. Tek tek dosyaları geri yüklemeye izin vermek için tek tek dosya düzeyinde alma sağlanır. SMB, REST API, Portal, istemci kitaplığı veya PowerShell/CLı araçları kullanarak bir dosya paylaşımının tamamını geri yükleyebilirsiniz.

Bir dosya paylaşımının paylaşma anlık görüntüsü, temel dosya paylaşımıyla aynıdır. Tek fark, paylaşma anlık görüntüsünün alındığı saati göstermek için Share URI 'sine bir **DateTime** değeri eklenmeiydi. Örneğin, bir dosya paylaşımının URI 'SI http://storagesample.core.file.windows.net/myshare ise, paylaşılan anlık görüntü URI 'SI şuna benzerdir:
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Ortak olarak silinene kadar paylaşma anlık görüntüleri korunur. Paylaşılan anlık görüntü, temel dosya paylaşımının içeriğini aşamaz. Geçerli anlık görüntülerinizi izlemek için taban dosya paylaşımıyla ilişkili anlık görüntüleri numaralandırabilirsiniz. 

Bir dosya paylaşımının paylaşma anlık görüntüsünü oluşturduğunuzda, paylaşımın sistem özelliklerindeki dosyaların aynı değerlerle paylaşma anlık görüntüsüne kopyalanması gerekir. Temel dosyalar ve dosya paylaşımının meta verileri, oluşturduğunuz zaman paylaşma anlık görüntüsü için ayrı meta veriler belirtmediğiniz takdirde, paylaşma anlık görüntüsüne de kopyalanır.

Önce tüm paylaşma anlık görüntülerini silmediğiniz takdirde, paylaşılan anlık görüntülere sahip bir paylaşıma silemezsiniz.

## <a name="space-usage"></a>Alan kullanımı 
Paylaşılan anlık görüntü, doğası halinde artımsal. Yalnızca en son paylaşma anlık görüntülerinizin ardından değiştirilen veriler kaydedilir. Bu, paylaşma anlık görüntüsünü oluşturmak için gereken süreyi en aza indirir ve depolama maliyetlerine kaydeder. Nesne veya özellik veya meta veri güncelleştirme işlemi için herhangi bir yazma işlemi, "değiştirilen içerik" öğesine doğru sayılır ve paylaşma anlık görüntüsünde saklanır. 

Alanı korumak için, karmaşıklığın en yüksek olduğu süre için paylaşma anlık görüntüsünü silebilirsiniz.

Paylaşma anlık görüntüleri artımlı olarak kaydedilse bile, paylaşımın geri yüklenmesi için yalnızca en son paylaşma anlık görüntüsünü saklamanız gerekir. Bir paylaşma anlık görüntüsünü sildiğinizde, yalnızca bu paylaşılan anlık görüntüye özgü veriler kaldırılır. Etkin anlık görüntüler, verilerinizi (paylaşma anlık görüntüsünün alındığı zamandan) orijinal konuma veya alternatif bir konuma gözatmanıza ve geri yüklemenize gerek duyduğunuz tüm bilgileri içerir. Öğe düzeyinde geri yükleme yapabilirsiniz.

Anlık görüntüler 5 TB 'lik paylaşma sınırınıza doğru sayılmaz. Toplamda ne kadar alan paylaşma anlık görüntüsü kaplayacağı bir sınır yoktur. Depolama hesabı sınırları hala geçerlidir.

## <a name="limits"></a>Sınırlar
Azure dosyalarının bugün izin verdiği en fazla paylaşılan anlık görüntü sayısı 200 ' dir. 200 anlık görüntüleri paylaştıktan sonra, yenilerini oluşturmak için eski paylaşılan anlık görüntüleri silmeniz gerekir. 

Paylaşılan anlık görüntü oluşturmaya yönelik eşzamanlı çağrılara yönelik bir sınır yoktur. Belirli bir dosya paylaşımının anlık görüntülerini paylaşan alan miktarına yönelik bir sınır yoktur. 

Bugün, Linux üzerinde paylaşma anlık görüntülerini bağlamak mümkün değildir. Bunun nedeni, Linux SMB istemcisinin Windows gibi anlık görüntüleri bağlamayı desteklemeleridir.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Paylaşma anlık görüntüsünden verileri bir paylaşıma geri kopyalama
Dosyaları içeren kopyalama işlemleri ve anlık görüntüleri paylaşma aşağıdaki kurallara uyar:

Bir dosya paylaşımında tek tek dosyaları, temel paylaşımıyla veya başka bir konuma kopyalayabilirsiniz. Dosyanın önceki bir sürümünü geri yükleyebilir veya dosya dosya paylaşımından paylaşma anlık görüntüsüne kopyalayarak dosya paylaşımının tamamını geri yükleyebilirsiniz. Paylaşma anlık görüntüsü temel paylaşıma yükseltilmedi. 

Paylaşma anlık görüntüsü kopyalandıktan sonra bozulmadan kalır, ancak paylaşımın anlık görüntüsünde bulunan verilerin bir kopyasıyla taban dosya paylaşımının üzerine yazılır. Geri yüklenen tüm dosyalar "değiştirilen içerik" öğesine doğru sayılır.

Share anlık görüntüsüne bir dosyayı farklı bir ada sahip başka bir hedefe kopyalayabilirsiniz. Elde edilen hedef dosya, paylaşma anlık görüntüsü değil yazılabilir bir dosyadır. Bu durumda, taban dosya paylaşımınız bozulmadan kalır.

Bir kopyası olan bir hedef dosyanın üzerine yazıldığında, özgün hedef dosyayla ilişkili tüm paylaşılan anlık görüntüler bozulmadan kalır.

## <a name="general-best-practices"></a>Genel en iyi uygulamalar 
Azure üzerinde altyapı çalıştırırken, mümkün olduğunda veri kurtarma yedeklemelerini otomatik hale getirin. Otomatik eylemler, el ile gerçekleştirilen işlemlerden daha güvenilirdir, veri korumayı ve kurtarılabilirliğe yardımcı olur. Otomasyon için REST API, Istemci SDK 'sını veya komut dosyasını kullanabilirsiniz.

Paylaşma anlık görüntüsü Zamanlayıcı 'yı dağıtmadan önce, paylaşılan anlık görüntü sıklığınızı ve bekletme ayarlarını dikkatle gözden geçirin ve gereksiz ücretleri kullanmaktan kaçının.

Paylaşılan anlık görüntüler yalnızca dosya düzeyinde koruma sağlar. Paylaşılan anlık görüntüler bir dosya paylaşımında veya depolama hesabında FAT-parmak silme işlemlerini engellemez. Bir depolama hesabının yanlışlıkla silinmekten korunmasını sağlamak için, depolama hesabını veya kaynak grubunu kilitlemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar
- Paylaşma anlık görüntüleriyle çalışma:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Anlık görüntü paylaşma SSS](storage-files-faq.md#share-snapshots)
