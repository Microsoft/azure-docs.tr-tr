---
title: include dosyası
description: include dosyası
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 01512f0e37761915fcb6eeba8c162e1a3db62c48
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491702"
---
```console
Robocopy /MT:16 /R:5 /W:5 /B /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Anahtar                | Anlamı |
|-----------------------|---------|
| `/MT:n`               | RoboCopy 'nin çoklu iş parçacıklı çalıştırmasına izin verir. N = 8 için varsayılan ve maksimum 128 iş parçacığı. Bu değeri, çekirdek başına işlemci çekirdek sayısı ve iş parçacığı sayısı ile eşleştirin. Bir üretim sunucusunun sahip olabileceği diğer görevler için çekirdekler ayrılmalıdır ' i göz önünde bulundurun. |
| `/R:n`                | Bir RoboCopy çalıştırmasının hızı, ilk denemede kopyalayabaşaracak bir dosya için en fazla yeniden deneme sayısı belirtilerek artırılabilir. n = bu RoboCopy çalıştırmasında dosyanın kalıcı olarak kopyalanması için en fazla yeniden deneme sayısı. Bu anahtar, daha fazla RoboCopy çalışmasının daha açık olduğu senaryolarda en iyi şekilde çalışır. Dosya bu çalıştırmada kopyalayamazsa, sonraki RoboCopy işi yeniden denenecek. Genellikle, kullanımda olduklarından veya zaman aşımı sorunları nedeniyle başarısız olan dosyalar, bu yaklaşım sonunda başarıyla kopyalanabilir. |
| `/W:n`                | Bu anahtar, RoboCopy 'nin son denemede başarıyla kopyalanamaz bir dosyayı kopyalamaya çalışmadan önce bekleyeceği süreyi belirtir. n = yeniden denemeler arasında beklenecek saniye sayısı. `/W:n` genellikle ile birlikte kullanılır `/R:n` . |
| `/B`                  | RoboCopy, bir yedekleme uygulamasının kullanacağı modda çalıştırır. RoboCopy 'nin geçerli kullanıcının izinleri olmayan dosyaları taşımasına izin verir. |
| `/MIR`                | *Kaynak yansıtma hedefi* , Robocopy 'nin yalnızca kaynak ve hedef arasında değişimleri kopyalamasını sağlar. Boş alt dizinler kopyalanacak. Hedefte değiştirilen veya mevcut olmayan öğeler (dosyalar veya klasörler) kopyalanacak. Hedefte bulunan ancak kaynakta yer almayan öğeler hedeften temizlenir (silinir). Bu anahtarı kullanırken, kaynak ve hedef klasör yapısını tam olarak eşleştirmek zorunludur. "Eşleşme", doğru kaynak ve klasör düzeyinden hedefteki eşleşen klasör düzeyine kopyaladığınız anlamına gelir. Yalnızca "yakala" kopyalama işlemi başarılı olabilir. Kaynak ve hedef `/MIR` uyuştudığında, kullanımı büyük ölçekli silme ve yeniden kopyalamaların oluşmasına neden olur. |
| `/IT`                 | Belirli yansıtma senaryolarında uygunluk korunmasını sağlar. </br>*Örnek* : iki Robocopy arasında çalışan bir dosya ÇALıŞıYORSA bir ACL değişikliği ve bir öznitelik güncelleştirmesi: Örneğin, *gizli* olarak işaretlenir. /IT olmadan, ACL değişikliği RoboCopy tarafından kaçırılarak hedef konuma aktarılmaz. |
|`/COPY:[copyflags]`    | Dosya kopyasının doğruluğu (belirtilmezse varsayılan `/COPY:DAT` ), Copy Flags: `D` = Data, `A` = Attributes, `T` = damgas, `S` = Security = NTFS ACL 'leri, `O` = Owner Information, `U` = bir<u>u</u>dime bilgisi. Denetim bilgileri bir Azure dosya paylaşımında depolanamaz. |
| `/DCOPY:[copyflags]`  | Dizinlerin kopyası için uygunluk (belirtilmezse varsayılan `/DCOPY:DA` ), Copy Flags: `D` = Data, `A` = öznitelikler, `T` = zaman damgaları. |
| `/NP`                 | Her dosya ve klasör için kopyanın ilerlemesi gösterilmez. İlerlemeyi görüntülemek, kopyalama performansını önemli ölçüde düşürür. |
| `/NFL`                | Dosya adlarının günlüğe kaydedilmeyeceğini belirtir. Kopyalama performansını geliştirir. |
| `/NDL`                | Dizin adlarının günlüğe kaydedilmeyeceğini belirtir. Kopyalama performansını geliştirir. |
| `/UNILOG:<file name>` | GÜNLÜK dosyasının durumunu UNICODE olarak çıkış (varolan günlüğün üzerine yaz). |
| `/LFSM`               | **yalnızca katmanlı depolamaya sahip hedefler için** </br>/LFSE isteklerini kullanarak ' düşük boş alan modunda ' bir işlem yapın. Bu anahtar, yalnızca katmanlı depolamaya sahip olan hedefler için yararlıdır ve bu, RoboCopy tamamlanmadan önce yerel kapasitenin tükenmesine sahip olabilir. Bu anahtar, bir Azure Dosya Eşitleme bulut katmanlama etkinleştirilmiş hedefle kullanılmak üzere özel olarak eklenmiştir. Azure Dosya Eşitleme bağımsız olarak kullanılabilir. Bu modda, RoboCopy bir dosya kopyası hedef birimin boş alanının ' Floor ' değerinin altına geçmesine neden olduğunda duraklatılır. Bu değer `/LFSM:n` , bayrak biçimi ile belirtilebilir. Parametre `n` taban 2: `nKB` ,, veya olarak belirtilir `nMB` `nGB` . `/LFSM`Açık taban değeri olmadan belirtilmişse, taban, hedef birimin boyutunun yüzde 10 ' una ayarlanır. Düşük boş alan modu/MT,/EFSRAW,/B ve/ZBILE uyumsuzdur. |
| `/Z`                  | **dikkatli kullanım** </br>Dosyaları yeniden başlatma modunda kopyalar, kullanımı yalnızca kararsız bir ağ ortamında önerilir. Bu seçenek, ek günlük kaydı nedeniyle kopyalama performansını önemli ölçüde azaltır. |
| `/ZB`                 | **dikkatli kullanım** </br>Yeniden başlatma modunu kullanır. Erişim reddedilirse, bu seçenek yedekleme modunu kullanır. Bu seçenek, denetim noktası gösterme nedeniyle kopyalama performansını önemli ölçüde azaltır. |
   